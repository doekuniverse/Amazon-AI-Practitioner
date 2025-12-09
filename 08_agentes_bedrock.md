# Capítulo 8: Agentes en Amazon Bedrock

## 8.1 ¿Qué son los Agentes de IA?

### Definición

Un **agente de IA** es un sistema autónomo que puede:
- Entender instrucciones complejas
- Tomar decisiones
- Ejecutar acciones
- Usar herramientas
- Completar tareas de principio a fin

### Diferencia con Chatbots Simples

**Chatbot tradicional:**
```
Usuario: Quiero reservar un vuelo a Madrid
Chatbot: Claro, ¿qué fecha prefieres?
Usuario: 15 de diciembre
Chatbot: ¿Clase económica o business?
[Múltiples intercambios...]
```

**Agente:**
```
Usuario: Reserva un vuelo a Madrid el 15 de diciembre, económico, ventana
Agente: [Automáticamente]
  1. Busca vuelos disponibles
  2. Compara precios
  3. Selecciona asiento ventana
  4. Procesa pago
  5. Envía confirmación
Agente: ✓ Reservado. Vuelo IB3421, asiento 12A, $450
```

### Características Clave

1. **Autonomía**: Trabaja sin intervención constante
2. **Razonamiento**: Decide qué hacer y en qué orden
3. **Uso de herramientas**: APIs, bases de datos, servicios externos
4. **Persistencia**: Mantiene contexto entre tareas
5. **Adaptabilidad**: Ajusta estrategia según resultados

## 8.2 Arquitectura de Agentes

### Componentes

```
┌──────────────────────────────────────┐
│         Usuario                      │
└────────────┬─────────────────────────┘
             │ Instrucción
             ▼
┌──────────────────────────────────────┐
│      Orquestador (LLM)               │
│  - Entiende la tarea                 │
│  - Planifica pasos                   │
│  - Decide qué herramienta usar       │
└────┬─────────────┬───────────────────┘
     │             │
     ▼             ▼
┌─────────┐   ┌─────────┐   ┌─────────┐
│ Tool 1  │   │ Tool 2  │   │ Tool 3  │
│ (API)   │   │ (DB)    │   │ (Search)│
└─────────┘   └─────────┘   └─────────┘
```

### Flujo de Trabajo

**Ejemplo: "Analiza las ventas del último trimestre"**

```
1. Orquestador recibe instrucción
   ↓
2. Identifica herramientas necesarias:
   - API de ventas
   - Herramienta de análisis
   ↓
3. Ejecuta en secuencia:
   a) Consulta API de ventas (Q3 2024)
   b) Procesa datos
   c) Genera análisis
   d) Crea visualización
   ↓
4. Devuelve resultado completo
```

## 8.3 Agentes en Amazon Bedrock

### Crear un Agente

```python
import boto3

bedrock_agent = boto3.client('bedrock-agent')

# Crear agente
response = bedrock_agent.create_agent(
    agentName='asistente-ventas',
    description='Agente para consultas de ventas y análisis',
    
    # Modelo fundacional
    foundationModel='anthropic.claude-3-sonnet-20240229-v1:0',
    
    # Instrucciones del agente
    instruction='''
    Eres un asistente de ventas experto.
    
    Tus responsabilidades:
    1. Consultar datos de ventas
    2. Analizar tendencias
    3. Responder preguntas sobre productos
    4. Generar reportes
    
    Siempre sé preciso y cita fuentes de datos.
    ''',
    
    # Configuración
    idleSessionTTLInSeconds=600,
    
    # Rol de IAM
    agentResourceRoleArn='arn:aws:iam::123456789:role/BedrockAgentRole'
)

agent_id = response['agent']['agentId']
```

### Agregar Action Groups

**Action Group** = Conjunto de acciones que el agente puede ejecutar

```python
# Definir API Schema (OpenAPI)
api_schema = {
    "openapi": "3.0.0",
    "info": {
        "title": "Sales API",
        "version": "1.0.0"
    },
    "paths": {
        "/sales": {
            "get": {
                "summary": "Get sales data",
                "parameters": [
                    {
                        "name": "start_date",
                        "in": "query",
                        "required": True,
                        "schema": {"type": "string"}
                    },
                    {
                        "name": "end_date",
                        "in": "query",
                        "required": True,
                        "schema": {"type": "string"}
                    }
                ],
                "responses": {
                    "200": {
                        "description": "Sales data",
                        "content": {
                            "application/json": {
                                "schema": {
                                    "type": "object",
                                    "properties": {
                                        "total": {"type": "number"},
                                        "transactions": {"type": "array"}
                                    }
                                }
                            }
                        }
                    }
                }
            }
        }
    }
}

# Crear Action Group
response = bedrock_agent.create_agent_action_group(
    agentId=agent_id,
    agentVersion='DRAFT',
    actionGroupName='sales-actions',
    description='Acciones para consultar ventas',
    
    # API Schema
    apiSchema={
        'payload': json.dumps(api_schema)
    },
    
    # Lambda que ejecuta las acciones
    actionGroupExecutor={
        'lambda': 'arn:aws:lambda:us-east-1:123:function:sales-api-handler'
    }
)
```

### Lambda Handler para Actions

```python
# sales-api-handler Lambda function
import json
import boto3

def lambda_handler(event, context):
    # Bedrock envía la acción a ejecutar
    action = event['actionGroup']
    api_path = event['apiPath']
    parameters = event['parameters']
    
    if api_path == '/sales':
        # Extraer parámetros
        start_date = next(p['value'] for p in parameters if p['name'] == 'start_date')
        end_date = next(p['value'] for p in parameters if p['name'] == 'end_date')
        
        # Consultar base de datos
        sales_data = get_sales_from_db(start_date, end_date)
        
        # Retornar resultado
        return {
            'messageVersion': '1.0',
            'response': {
                'actionGroup': action,
                'apiPath': api_path,
                'httpMethod': 'GET',
                'httpStatusCode': 200,
                'responseBody': {
                    'application/json': {
                        'body': json.dumps(sales_data)
                    }
                }
            }
        }

def get_sales_from_db(start, end):
    # Lógica para consultar DB
    return {
        'total': 125000,
        'transactions': 450,
        'average': 277.78
    }
```

### Agregar Knowledge Base

```python
# Asociar Knowledge Base al agente
response = bedrock_agent.associate_agent_knowledge_base(
    agentId=agent_id,
    agentVersion='DRAFT',
    knowledgeBaseId='KB123456',
    description='Documentación de productos y políticas',
    knowledgeBaseState='ENABLED'
)
```

### Preparar y Desplegar

```python
# Preparar agente (validar configuración)
response = bedrock_agent.prepare_agent(
    agentId=agent_id
)

# Crear alias para producción
response = bedrock_agent.create_agent_alias(
    agentId=agent_id,
    agentAliasName='produccion',
    description='Versión de producción'
)

alias_id = response['agentAlias']['agentAliasId']
```

### Invocar Agente

```python
bedrock_agent_runtime = boto3.client('bedrock-agent-runtime')

# Invocar agente
response = bedrock_agent_runtime.invoke_agent(
    agentId=agent_id,
    agentAliasId=alias_id,
    sessionId='session-123',  # Mantiene contexto
    inputText='Muéstrame las ventas del último mes y analiza las tendencias'
)

# Procesar respuesta (streaming)
for event in response['completion']:
    if 'chunk' in event:
        chunk = event['chunk']
        if 'bytes' in chunk:
            print(chunk['bytes'].decode())
```

## 8.4 Orquestación

### Proceso de Razonamiento

**Trace del agente:**

```
1. Usuario: "Muéstrame las ventas del último mes"

2. Orquestador (pensamiento interno):
   - Necesito obtener la fecha actual
   - Calcular inicio del mes pasado
   - Llamar a la API de ventas
   - Analizar los datos
   - Presentar resultado

3. Acción 1: Calcular fechas
   start_date = "2024-11-01"
   end_date = "2024-11-30"

4. Acción 2: Invocar API
   GET /sales?start_date=2024-11-01&end_date=2024-11-30
   
5. Resultado API:
   {
     "total": 125000,
     "transactions": 450,
     "average": 277.78
   }

6. Acción 3: Analizar y responder
   "En noviembre 2024 tuviste:
    - Ventas totales: $125,000
    - 450 transacciones
    - Ticket promedio: $277.78
    
    Esto representa un incremento del 12% vs octubre."
```

### Trazabilidad

**Bedrock proporciona trace completo:**

```python
# Habilitar trace
response = bedrock_agent_runtime.invoke_agent(
    agentId=agent_id,
    agentAliasId=alias_id,
    sessionId='session-123',
    inputText='Analiza las ventas',
    enableTrace=True  # ← Habilitar
)

# Ver trace
for event in response['completion']:
    if 'trace' in event:
        trace = event['trace']['trace']
        
        # Ver razonamiento
        if 'orchestrationTrace' in trace:
            orch = trace['orchestrationTrace']
            print(f"Pensamiento: {orch.get('rationale')}")
            print(f"Acción: {orch.get('invocationInput')}")
            print(f"Resultado: {orch.get('observation')}")
```

**Beneficios:**
- Debugging
- Entender decisiones
- Optimizar prompts
- Auditoría

## 8.5 Casos de Uso Avanzados

### 1. Asistente de E-commerce

**Capacidades:**
- Buscar productos
- Comparar precios
- Procesar pedidos
- Rastrear envíos
- Gestionar devoluciones

**Action Groups:**
```python
actions = {
    "product-search": {
        "search_products": "Buscar en catálogo",
        "get_product_details": "Detalles de producto",
        "check_inventory": "Verificar stock"
    },
    "order-management": {
        "create_order": "Crear pedido",
        "get_order_status": "Estado de pedido",
        "cancel_order": "Cancelar pedido"
    },
    "shipping": {
        "track_shipment": "Rastrear envío",
        "update_address": "Cambiar dirección"
    }
}
```

**Ejemplo de uso:**
```
Usuario: Quiero comprar una laptop gaming, presupuesto $1500

Agente:
1. Busca laptops gaming ≤ $1500
2. Compara especificaciones
3. Verifica stock
4. Presenta top 3 opciones
5. Usuario selecciona
6. Procesa pedido
7. Confirma y da tracking
```

### 2. Asistente Financiero

**Capacidades:**
- Consultar saldos
- Analizar gastos
- Detectar fraudes
- Generar reportes
- Asesorar inversiones

**Knowledge Base:**
- Políticas bancarias
- Regulaciones financieras
- Guías de inversión

**Ejemplo:**
```
Usuario: ¿Cómo van mis gastos este mes?

Agente:
1. Consulta transacciones del mes
2. Categoriza gastos
3. Compara con presupuesto
4. Identifica anomalías
5. Genera reporte:
   "Gastos totales: $3,200
    - Alimentación: $800 (↑15% vs promedio)
    - Transporte: $400
    - Entretenimiento: $600 (↑40% ⚠️)
    
    Recomendación: Reducir entretenimiento para cumplir presupuesto"
```

### 3. Asistente de Soporte Técnico

**Capacidades:**
- Diagnosticar problemas
- Buscar en base de conocimiento
- Ejecutar scripts de solución
- Escalar a humanos si necesario
- Crear tickets

**Flujo:**
```
Usuario: Mi aplicación no inicia

Agente:
1. Pregunta por detalles (SO, versión, error)
2. Busca en KB soluciones conocidas
3. Sugiere pasos de troubleshooting
4. Si no resuelve → Ejecuta diagnóstico automático
5. Si persiste → Crea ticket y escala
```

## 8.6 Mejores Prácticas

### 1. Instrucciones Claras

**Malo:**
```
Eres un asistente útil.
```

**Bueno:**
```
Eres un asistente de ventas para empresa de software B2B.

Responsabilidades:
1. Consultar datos de ventas (usar sales-api)
2. Responder preguntas sobre productos (usar knowledge-base)
3. Generar reportes (formato markdown)

Reglas:
- Siempre cita fuentes de datos
- Si no tienes información, di "No tengo esos datos"
- Para datos sensibles, requiere autenticación
- Máximo 3 intentos por acción antes de escalar

Tono: Profesional y conciso
```

### 2. Definir APIs Claramente

**OpenAPI Schema completo:**
- Descripciones detalladas
- Ejemplos de parámetros
- Respuestas esperadas
- Códigos de error

### 3. Manejo de Errores

```python
def lambda_handler(event, context):
    try:
        # Lógica principal
        result = process_action(event)
        return success_response(result)
    
    except ValidationError as e:
        return error_response(400, f"Parámetros inválidos: {e}")
    
    except DatabaseError as e:
        return error_response(500, "Error al consultar datos")
    
    except Exception as e:
        # Log para debugging
        print(f"Error inesperado: {e}")
        return error_response(500, "Error interno")
```

### 4. Seguridad

**Control de acceso:**
```python
def lambda_handler(event, context):
    # Verificar autenticación
    user_id = event.get('sessionAttributes', {}).get('userId')
    
    if not user_id:
        return error_response(401, "No autenticado")
    
    # Verificar autorización
    if not has_permission(user_id, event['apiPath']):
        return error_response(403, "No autorizado")
    
    # Procesar acción
    return process_action(event)
```

### 5. Optimización de Costos

**Caché de resultados frecuentes:**
```python
import redis

cache = redis.Redis()

def get_sales_data(start, end):
    cache_key = f"sales:{start}:{end}"
    
    # Buscar en caché
    cached = cache.get(cache_key)
    if cached:
        return json.loads(cached)
    
    # Si no está, consultar DB
    data = query_database(start, end)
    
    # Guardar en caché (1 hora)
    cache.setex(cache_key, 3600, json.dumps(data))
    
    return data
```

## 8.7 Limitaciones y Consideraciones

### Limitaciones

1. **Complejidad**: Tareas muy complejas pueden fallar
2. **Latencia**: Múltiples llamadas = mayor tiempo
3. **Costo**: Más tokens que chatbot simple
4. **Determinismo**: No siempre toma la misma ruta

### Cuándo NO Usar Agentes

- Tareas simples de Q&A → Usar chatbot con RAG
- Respuestas instantáneas requeridas → Latencia alta
- Presupuesto muy limitado → Más costoso
- Acciones críticas sin supervisión → Requiere human-in-the-loop

### Cuándo SÍ Usar Agentes

- Tareas multi-paso complejas
- Integración con múltiples sistemas
- Necesidad de razonamiento
- Automatización de workflows

## Preguntas de Repaso

1. **¿Qué diferencia a un agente de un chatbot simple?**
   - a) El costo
   - b) La capacidad de ejecutar acciones y usar herramientas autónomamente
   - c) El modelo de lenguaje usado
   - d) La velocidad de respuesta

2. **¿Qué es un Action Group en Bedrock Agents?**
   - a) Un tipo de modelo
   - b) Conjunto de acciones que el agente puede ejecutar
   - c) Un servicio de AWS
   - d) Una métrica de rendimiento

3. **¿Qué componente ejecuta las acciones del agente?**
   - a) El modelo de lenguaje
   - b) Lambda functions
   - c) S3
   - d) CloudWatch

4. **¿Para qué sirve el trace en agentes?**
   - a) Cifrar datos
   - b) Ver el razonamiento y decisiones del agente
   - c) Reducir costos
   - d) Aumentar velocidad

5. **¿Cuál NO es una buena práctica para agentes?**
   - a) Instrucciones claras y detalladas
   - b) Manejo de errores robusto
   - c) Permitir acciones críticas sin supervisión
   - d) APIs bien documentadas

## Respuestas

1. **b** - La capacidad de ejecutar acciones y usar herramientas autónomamente
2. **b** - Conjunto de acciones que el agente puede ejecutar
3. **b** - Lambda functions
4. **b** - Ver el razonamiento y decisiones del agente
5. **c** - Permitir acciones críticas sin supervisión (esto es MALO)

---

**Capítulo Anterior**: [Evaluación y Monitoreo](file:///C:/Users/doeku/Web/AI/07_evaluacion_monitoreo.md)

**Próximo Capítulo**: [Casos de Uso y Mejores Prácticas](file:///C:/Users/doeku/Web/AI/09_casos_uso_mejores_practicas.md)
