# Capítulo 3: Modelos Fundacionales y Amazon Bedrock

## 3.1 ¿Qué son los Modelos Fundacionales?

Los **Foundation Models** (Modelos Fundacionales) son modelos de IA pre-entrenados con grandes cantidades de datos que tienen conocimiento general sobre múltiples dominios.

### Características Principales

1. **Pre-entrenados**: Ya vienen entrenados por empresas especializadas
2. **Conocimiento amplio**: Múltiples dominios y tareas
3. **Transferencia de aprendizaje**: Pueden adaptarse a tareas específicas
4. **Multimodales**: Algunos procesan texto, imágenes, audio, video

### GPT: Generative Pre-trained Transformer

**GPT** significa:
- **Generative**: Genera contenido nuevo
- **Pre-trained**: Ya está entrenado
- **Transformer**: Arquitectura de red neuronal

## 3.2 Modelos Disponibles en Amazon Bedrock

### Anthropic Claude

**Familia Claude 3:**

#### Claude 3.5 Sonnet
- **Mejor balance**: Rendimiento vs costo
- **Contexto**: 200,000 tokens
- **Fortalezas**:
  - Razonamiento complejo
  - Análisis de documentos largos
  - Código de alta calidad
  - Seguimiento preciso de instrucciones

**Casos de uso:**
- Análisis de contratos extensos
- Generación de código
- Asistentes conversacionales avanzados

#### Claude 3 Opus
- **Máximo rendimiento**: El más capaz
- **Contexto**: 200,000 tokens
- **Fortalezas**:
  - Tareas complejas de razonamiento
  - Creatividad superior
  - Análisis profundo

**Casos de uso:**
- Investigación y análisis complejo
- Generación de contenido creativo de alta calidad
- Tareas que requieren máxima precisión

#### Claude 3 Haiku
- **Más rápido y económico**
- **Contexto**: 200,000 tokens
- **Fortalezas**:
  - Latencia ultra-baja
  - Costo reducido
  - Respuestas rápidas

**Casos de uso:**
- Chatbots de alto volumen
- Clasificación de texto
- Extracción de información simple

### Meta LLaMA

**Características generales:**
- **Open-source**: Código abierto
- **Multilingüe**: Soporte para múltiples idiomas
- **Eficiente**: Buena relación calidad/precio

#### LLaMA 2
**Tamaños disponibles:**
- 7B parámetros: Más rápido, menor capacidad
- 13B parámetros: Balance
- 70B parámetros: Mayor capacidad

**Fortalezas:**
- Generación de texto general
- Conversación
- Resumen de textos

#### LLaMA 3
**Tamaños disponibles:**
- 8B parámetros
- 70B parámetros

**Mejoras sobre LLaMA 2:**
- Mejor razonamiento
- Más conocimiento
- Mejor seguimiento de instrucciones

**Casos de uso:**
- Aplicaciones que requieren control total
- Investigación y desarrollo
- Implementaciones on-premise

### Amazon Titan

**Modelos propios de AWS:**

#### Titan Text
**Variantes:**
- **Titan Text Express**: Rápido y económico
- **Titan Text Lite**: Ultra-ligero
- **Titan Text Premier**: Máximo rendimiento

**Fortalezas:**
- Integración nativa con AWS
- Optimizado para casos empresariales
- Soporte completo de AWS
- Multilingüe (100+ idiomas)

**Casos de uso:**
- Resumen de textos
- Generación de contenido
- Búsqueda semántica
- Chatbots empresariales

#### Titan Embeddings
**Función**: Convertir texto en vectores

**Especificaciones:**
- Dimensiones: 1536
- Multilingüe: 25+ idiomas
- Optimizado para RAG

**Casos de uso:**
- Bases de conocimiento
- Búsqueda semántica
- Recomendaciones
- Clustering de documentos

#### Titan Image Generator
**Función**: Generación de imágenes desde texto

**Capacidades:**
- Generación desde descripción
- Edición de imágenes
- Variaciones de imagen
- Inpainting y outpainting

**Casos de uso:**
- Marketing y publicidad
- E-commerce (imágenes de productos)
- Diseño creativo
- Prototipado visual

### AI21 Labs Jurassic

**Características:**
- Especializado en texto
- Multilingüe
- Instrucciones complejas

**Modelos:**
- Jurassic-2 Mid
- Jurassic-2 Ultra

**Casos de uso:**
- Generación de contenido largo
- Resumen de documentos
- Parafraseo
- Análisis de texto

### Cohere

**Modelos:**
- Command: Generación de texto
- Embed: Embeddings multilingües

**Fortalezas:**
- Multilingüe (100+ idiomas)
- Optimizado para empresas
- Búsqueda semántica

**Casos de uso:**
- Búsqueda empresarial
- Clasificación de texto
- Generación de contenido multilingüe

### Stability AI

**Modelo**: Stable Diffusion

**Función**: Generación de imágenes

**Capacidades:**
- Text-to-image
- Image-to-image
- Inpainting
- Upscaling

**Casos de uso:**
- Arte generativo
- Diseño de productos
- Marketing visual
- Prototipado de UI/UX

## 3.3 Comparación de Modelos

### Por Capacidad

| Modelo | Razonamiento | Creatividad | Velocidad | Costo |
|--------|--------------|-------------|-----------|-------|
| Claude 3 Opus | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Claude 3.5 Sonnet | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| Claude 3 Haiku | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐ |
| LLaMA 3 70B | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ |
| Titan Text Premier | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ |

### Por Caso de Uso

**Análisis de documentos largos:**
1. Claude 3.5 Sonnet (200K contexto)
2. Claude 3 Opus
3. Titan Text Premier

**Chatbots de alto volumen:**
1. Claude 3 Haiku (velocidad + costo)
2. Titan Text Express
3. LLaMA 3 8B

**Generación de código:**
1. Claude 3.5 Sonnet
2. Claude 3 Opus
3. LLaMA 3 70B

**Multilingüe:**
1. Cohere Command
2. Titan Text
3. LLaMA 3

**Generación de imágenes:**
1. Stable Diffusion
2. Titan Image Generator

## 3.4 Playground de Bedrock

### Acceso al Playground

**Navegación:**
1. Consola de AWS
2. Amazon Bedrock
3. Playgrounds → Chat

### Funcionalidades

#### 1. Selección de Modelo

**Opciones:**
- Ver modelos disponibles
- Modelos con acceso habilitado
- Solicitar acceso a nuevos modelos

**Proceso de habilitación:**
1. Ir a "Model access"
2. Seleccionar modelos deseados
3. "Request model access"
4. Esperar aprobación (generalmente inmediata)

#### 2. Configuración de Hiperparámetros

**Temperature (Temperatura)**
```
Rango: 0.0 - 1.0

0.0 - 0.3: Determinístico
- Respuestas consistentes
- Ideal para: Clasificación, extracción de datos

0.4 - 0.7: Balanceado
- Creatividad moderada
- Ideal para: Chatbots, Q&A

0.8 - 1.0: Creativo
- Respuestas variadas
- Ideal para: Generación de contenido, brainstorming
```

**Top P (Nucleus Sampling)**
```
Rango: 0.0 - 1.0

0.9: Valor típico
- Considera el 90% de probabilidad acumulada
- Balance entre diversidad y coherencia
```

**Top K**
```
Rango: 1 - 500

40-50: Valor típico
- Número de tokens candidatos
- Menor = más conservador
- Mayor = más diverso
```

**Max Tokens (Longitud de Respuesta)**
```
Rango: 1 - límite del modelo

Consideraciones:
- Controla longitud de respuesta
- Impacta costo directamente
- Claude: hasta 4096 tokens de salida
```

#### 3. System Prompts

**Función**: Definir comportamiento del modelo

**Ejemplo:**
```
Eres un asistente experto en AWS especializado en servicios de IA.
Tus respuestas deben ser:
- Técnicas pero accesibles
- Basadas en documentación oficial
- Incluir ejemplos cuando sea posible
- Mencionar costos cuando sea relevante

Si no sabes algo, di "No tengo información suficiente".
```

**Mejores prácticas:**
- Ser específico sobre el rol
- Definir tono y estilo
- Establecer limitaciones
- Incluir formato de respuesta

#### 4. Modo Comparación

**Función**: Comparar respuestas de diferentes modelos

**Proceso:**
1. Seleccionar 2-3 modelos
2. Ingresar el mismo prompt
3. Ejecutar simultáneamente
4. Comparar:
   - Calidad de respuesta
   - Latencia (tiempo de respuesta)
   - Tokens utilizados
   - Costo estimado

**Métricas mostradas:**
- **Time to first token**: Latencia inicial
- **Total tokens**: Input + output
- **Tiempo total**: Duración completa

**Ejemplo de comparación:**
```
Prompt: "Explica qué es RAG en 100 palabras"

Claude 3.5 Sonnet:
- Latencia: 0.8s
- Tokens: 150
- Calidad: ⭐⭐⭐⭐⭐

Claude 3 Haiku:
- Latencia: 0.3s
- Tokens: 120
- Calidad: ⭐⭐⭐⭐

Conclusión: Haiku más rápido, Sonnet más completo
```

## 3.5 APIs de Bedrock

### Bedrock Runtime API

**Función**: Invocar modelos para inferencia

#### Invoke Model (Síncrono)

**Python (boto3):**
```python
import boto3
import json

bedrock = boto3.client('bedrock-runtime', region_name='us-east-1')

# Prompt
prompt = "Explica qué es Amazon Bedrock"

# Invocar Claude
response = bedrock.invoke_model(
    modelId='anthropic.claude-3-sonnet-20240229-v1:0',
    body=json.dumps({
        "anthropic_version": "bedrock-2023-05-31",
        "messages": [
            {
                "role": "user",
                "content": prompt
            }
        ],
        "max_tokens": 500,
        "temperature": 0.7
    })
)

# Procesar respuesta
result = json.loads(response['body'].read())
print(result['content'][0]['text'])
```

#### Invoke Model with Response Stream (Streaming)

**Función**: Recibir respuesta en tiempo real (palabra por palabra)

**Python:**
```python
response = bedrock.invoke_model_with_response_stream(
    modelId='anthropic.claude-3-sonnet-20240229-v1:0',
    body=json.dumps({
        "anthropic_version": "bedrock-2023-05-31",
        "messages": [{"role": "user", "content": prompt}],
        "max_tokens": 500
    })
)

# Procesar stream
stream = response['body']
for event in stream:
    chunk = json.loads(event['chunk']['bytes'])
    if chunk['type'] == 'content_block_delta':
        print(chunk['delta']['text'], end='', flush=True)
```

**Ventajas del streaming:**
- Mejor experiencia de usuario
- Percepción de menor latencia
- Respuestas visibles inmediatamente

### Bedrock API

**Función**: Gestión de modelos y configuración

**Operaciones:**
- Listar modelos disponibles
- Obtener detalles de modelo
- Gestionar acceso a modelos
- Crear/gestionar bases de conocimiento
- Crear/gestionar agentes

**Ejemplo - Listar modelos:**
```python
bedrock = boto3.client('bedrock', region_name='us-east-1')

response = bedrock.list_foundation_models()

for model in response['modelSummaries']:
    print(f"Modelo: {model['modelName']}")
    print(f"ID: {model['modelId']}")
    print(f"Provider: {model['providerName']}")
    print("---")
```

## 3.6 Notebooks de Ejemplo

### Repositorio Oficial

**GitHub**: `aws-samples/amazon-bedrock-workshop`

**Contenido:**
- Notebooks de introducción
- Ejemplos de RAG
- Fine-tuning
- Agentes
- Casos de uso específicos

### Ejemplos Básicos

#### 1. Invocación Simple
```python
# Configurar cliente
import boto3
bedrock = boto3.client('bedrock-runtime')

# Función helper
def invoke_bedrock(prompt, model_id, max_tokens=500):
    response = bedrock.invoke_model(
        modelId=model_id,
        body=json.dumps({
            "anthropic_version": "bedrock-2023-05-31",
            "messages": [{"role": "user", "content": prompt}],
            "max_tokens": max_tokens
        })
    )
    return json.loads(response['body'].read())

# Usar
result = invoke_bedrock(
    "¿Qué es Amazon Bedrock?",
    "anthropic.claude-3-sonnet-20240229-v1:0"
)
print(result['content'][0]['text'])
```

#### 2. Conversación Multi-turno
```python
conversation_history = []

def chat(user_message):
    # Agregar mensaje del usuario
    conversation_history.append({
        "role": "user",
        "content": user_message
    })
    
    # Invocar modelo
    response = bedrock.invoke_model(
        modelId='anthropic.claude-3-sonnet-20240229-v1:0',
        body=json.dumps({
            "anthropic_version": "bedrock-2023-05-31",
            "messages": conversation_history,
            "max_tokens": 500
        })
    )
    
    # Procesar respuesta
    result = json.loads(response['body'].read())
    assistant_message = result['content'][0]['text']
    
    # Agregar a historial
    conversation_history.append({
        "role": "assistant",
        "content": assistant_message
    })
    
    return assistant_message

# Usar
print(chat("Hola, ¿qué es RAG?"))
print(chat("¿Cuáles son sus ventajas?"))
print(chat("Dame un ejemplo práctico"))
```

## 3.7 Costos y Optimización

### Modelo de Precios

**Factores:**
1. **Modelo seleccionado**: Cada modelo tiene precio diferente
2. **Tokens de entrada**: Texto enviado al modelo
3. **Tokens de salida**: Texto generado por el modelo
4. **Región**: Precios varían por región

**Ejemplo de precios (us-east-1):**

| Modelo | Input (por 1K tokens) | Output (por 1K tokens) |
|--------|----------------------|------------------------|
| Claude 3.5 Sonnet | $0.003 | $0.015 |
| Claude 3 Haiku | $0.00025 | $0.00125 |
| Titan Text Express | $0.0002 | $0.0006 |
| LLaMA 3 70B | $0.00099 | $0.00099 |

### Calculadora de Costos

**Ejemplo:**
```
Escenario: Chatbot de atención al cliente

Parámetros:
- Modelo: Claude 3.5 Sonnet
- Solicitudes por minuto: 30
- Horas de operación: 8 horas/día
- Tokens de entrada promedio: 100
- Tokens de salida promedio: 100

Cálculo:
Solicitudes/día = 30 * 60 * 8 = 14,400
Tokens entrada/día = 14,400 * 100 = 1,440,000
Tokens salida/día = 14,400 * 100 = 1,440,000

Costo/día:
- Entrada: (1,440,000 / 1000) * $0.003 = $4.32
- Salida: (1,440,000 / 1000) * $0.015 = $21.60
- Total: $25.92/día = $777.60/mes
```

### Estrategias de Optimización

#### 1. Selección de Modelo Apropiado

**Pregunta**: ¿Necesito el modelo más potente?

**Estrategia:**
- Tareas simples → Haiku o Titan Express
- Tareas complejas → Sonnet
- Tareas críticas → Opus

**Ahorro potencial**: 80-90%

#### 2. Limitar Tokens de Salida

**Problema**: Respuestas innecesariamente largas

**Solución:**
```python
# Antes
max_tokens = 4096  # Máximo permitido

# Después
max_tokens = 200  # Suficiente para la mayoría de respuestas
```

**Ahorro potencial**: 50-70%

#### 3. Prompt Engineering

**Problema**: Prompts verbosos

**Solución:**
```python
# Antes (150 tokens)
prompt = """
Eres un asistente muy útil y amigable que ayuda a los usuarios...
[párrafos de instrucciones]
Por favor responde la siguiente pregunta de manera detallada...
"""

# Después (30 tokens)
system_prompt = "Asistente experto en AWS"
prompt = "¿Qué es Bedrock?"
```

**Ahorro potencial**: 20-30%

#### 4. Caché de Respuestas

**Concepto**: Guardar respuestas frecuentes

**Implementación:**
```python
import redis

cache = redis.Redis()

def get_response(prompt):
    # Buscar en caché
    cached = cache.get(prompt)
    if cached:
        return cached
    
    # Si no está, invocar modelo
    response = invoke_bedrock(prompt)
    
    # Guardar en caché
    cache.setex(prompt, 3600, response)  # 1 hora
    
    return response
```

**Ahorro potencial**: 40-60% para preguntas frecuentes

#### 5. Capacidad Reservada

**Para alto volumen:**
- Comprar capacidad reservada (throughput)
- Descuentos significativos
- Latencia garantizada

**Cuándo considerar:**
- Más de 1M tokens/día
- Aplicación crítica
- Presupuesto predecible

## Preguntas de Repaso

1. **¿Qué modelo de Bedrock ofrece el mejor balance entre rendimiento y costo?**
   - a) Claude 3 Opus
   - b) Claude 3.5 Sonnet
   - c) Claude 3 Haiku
   - d) Titan Text Lite

2. **¿Cuál es la ventaja principal del modo streaming?**
   - a) Menor costo
   - b) Mayor precisión
   - c) Mejor experiencia de usuario con respuestas en tiempo real
   - d) Más tokens disponibles

3. **¿Qué hiperparámetro controla la creatividad del modelo?**
   - a) Max Tokens
   - b) Top K
   - c) Temperature
   - d) Top P

4. **¿Cuál es el propósito de Titan Embeddings?**
   - a) Generar imágenes
   - b) Convertir texto en vectores para RAG
   - c) Traducir idiomas
   - d) Generar código

5. **¿Qué factor NO afecta el costo en Bedrock?**
   - a) Modelo seleccionado
   - b) Tokens de entrada
   - c) Tokens de salida
   - d) Número de usuarios simultáneos

## Respuestas

1. **b** - Claude 3.5 Sonnet
2. **c** - Mejor experiencia de usuario con respuestas en tiempo real
3. **c** - Temperature
4. **b** - Convertir texto en vectores para RAG
5. **d** - Número de usuarios simultáneos (se paga por tokens, no por usuarios)

---

**Capítulo Anterior**: [Arquitectura RAG](file:///C:/Users/doeku/Web/AI/02_arquitectura_rag.md)

**Próximo Capítulo**: [Personalización de Modelos](file:///C:/Users/doeku/Web/AI/04_personalizacion_modelos.md)
