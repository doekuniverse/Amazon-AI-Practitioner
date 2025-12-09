# Capítulo 2: Arquitectura RAG (Retrieval-Augmented Generation)

## 2.1 ¿Qué es RAG?

**RAG (Retrieval-Augmented Generation)** es un patrón de arquitectura que combina la recuperación de información con la generación de texto para mejorar la precisión y relevancia de las respuestas de los modelos de lenguaje.

### Problema que Resuelve

Los modelos de lenguaje pre-entrenados tienen limitaciones:

1. **Conocimiento estático**: Solo saben hasta su fecha de entrenamiento
2. **Alucinaciones**: Pueden generar información plausible pero incorrecta
3. **Falta de contexto específico**: No conocen datos privados o empresariales
4. **Información desactualizada**: No tienen acceso a datos recientes

**RAG soluciona estos problemas** al permitir que el modelo acceda a información externa en tiempo real.

### Ejemplo Práctico

**Sin RAG:**
```
Usuario: ¿Cuál es el estado de mi pedido #12345?
Modelo: No tengo acceso a información de pedidos específicos.
```

**Con RAG:**
```
Usuario: ¿Cuál es el estado de mi pedido #12345?
Sistema RAG:
  1. Busca en base de datos: pedido #12345
  2. Recupera: "Estado: En tránsito, llegada estimada: 10 de diciembre"
  3. Enriquece el prompt con esta información
Modelo: Tu pedido #12345 está en tránsito y llegará aproximadamente el 10 de diciembre.
```

## 2.2 Arquitectura de RAG

### Componentes Principales

```
┌─────────────┐
│   Usuario   │
└──────┬──────┘
       │ Query
       ▼
┌─────────────────┐
│  Orquestador    │ ◄─── Componente central
└────┬────────┬───┘
     │        │
     │        └──────────────┐
     │                       │
     ▼                       ▼
┌──────────────┐    ┌────────────────┐
│ Base de Datos│    │ Modelo de      │
│ Vectorial    │    │ Embeddings     │
└──────┬───────┘    └────────────────┘
       │
       │ Contexto recuperado
       ▼
┌──────────────────┐
│ Modelo de        │
│ Lenguaje (LLM)   │
└────────┬─────────┘
         │
         ▼
    Respuesta
```

### 1. Orquestador

**Función**: Componente central que coordina todo el proceso.

**Responsabilidades:**
- Recibir la consulta del usuario
- Hacer búsquedas en la base de datos vectorial
- Recuperar información relevante
- Enriquecer el prompt con contexto
- Enviar al modelo de lenguaje
- Devolver la respuesta al usuario

**En Amazon Bedrock:**
- Knowledge Bases actúan como orquestador
- Proceso completamente automatizado
- Auditable y trazable

### 2. Base de Datos Vectorial

**Función**: Almacenar representaciones vectoriales de documentos.

**¿Por qué vectores?**

Los modelos de lenguaje no entienden texto directamente, necesitan representaciones numéricas llamadas **embeddings** o **vectores**.

**Características:**
- Cada documento se convierte en un vector de números
- Vectores similares están cerca en el espacio vectorial
- Permite búsqueda semántica (por significado, no solo palabras clave)

**Opciones en AWS:**
- **Amazon OpenSearch Service**: Búsqueda y análisis
- **Amazon Aurora con pgvector**: Base de datos relacional con vectores
- **Amazon Neptune**: Base de datos de grafos
- **Pinecone, Weaviate**: Bases de datos vectoriales especializadas

### 3. Modelo de Embeddings

**Función**: Convertir texto en vectores numéricos.

**Proceso:**
```
Texto: "Amazon Bedrock es un servicio de IA"
       ↓
Modelo de Embeddings
       ↓
Vector: [0.23, -0.45, 0.67, ..., 0.12] (1536 dimensiones)
```

**Modelos disponibles en Bedrock:**
- **Amazon Titan Embeddings**: Optimizado para AWS
- **Cohere Embed**: Multilingüe
- Dimensiones típicas: 384, 768, 1536

**Importante**: Usar el mismo modelo de embeddings para:
- Indexar documentos
- Procesar consultas

### 4. Modelo de Lenguaje (LLM)

**Función**: Generar la respuesta final usando el contexto recuperado.

**Proceso:**
1. Recibe el prompt original del usuario
2. Recibe el contexto recuperado de la base vectorial
3. Genera una respuesta coherente y precisa

## 2.3 Flujo de Trabajo RAG

### Fase 1: Indexación (Una sola vez)

```
Documentos → Chunking → Embeddings → Base de Datos Vectorial
```

**Pasos detallados:**

1. **Preparar documentos**
   - PDFs, Word, HTML, texto plano
   - Subir a Amazon S3

2. **Chunking (División)**
   - Dividir documentos en fragmentos manejables
   - Tamaño típico: 500-1000 tokens
   - Mantener contexto coherente

3. **Generar embeddings**
   - Cada chunk se convierte en vector
   - Modelo de embeddings procesa cada fragmento

4. **Almacenar en base vectorial**
   - Vectores + metadata
   - Índices para búsqueda rápida

### Fase 2: Consulta (Cada solicitud)

```
Query → Embedding → Búsqueda Vectorial → Recuperar Chunks → 
Enriquecer Prompt → LLM → Respuesta
```

**Pasos detallados:**

1. **Usuario hace una pregunta**
   ```
   "¿Cómo configuro una base de conocimiento en Bedrock?"
   ```

2. **Convertir pregunta a vector**
   - Mismo modelo de embeddings
   - Vector de la consulta

3. **Búsqueda de similitud**
   - Comparar vector de consulta con vectores almacenados
   - Encontrar los K chunks más similares (típicamente K=3-5)
   - Métrica: similitud coseno

4. **Recuperar contexto**
   ```
   Chunk 1: "Para crear una base de conocimiento en Bedrock..."
   Chunk 2: "Los pasos son: 1) Subir documentos a S3..."
   Chunk 3: "Bedrock automáticamente procesa y vectoriza..."
   ```

5. **Enriquecer el prompt**
   ```
   System: Eres un asistente experto en AWS.
   
   Contexto:
   [Chunk 1]
   [Chunk 2]
   [Chunk 3]
   
   Pregunta del usuario: ¿Cómo configuro una base de conocimiento en Bedrock?
   
   Instrucción: Responde basándote SOLO en el contexto proporcionado.
   ```

6. **LLM genera respuesta**
   - Usa el contexto para responder
   - Cita fuentes cuando sea posible
   - Evita alucinaciones

## 2.4 Implementación de RAG en Amazon Bedrock

### Opción 1: Knowledge Bases (Sin Código)

**Ventajas:**
- No requiere programación
- Configuración en minutos
- Gestión automática de embeddings
- Integración con S3

**Pasos:**

1. **Crear bucket S3**
   ```
   Subir documentos: PDFs, TXT, HTML, etc.
   ```

2. **Crear Knowledge Base en Bedrock**
   - Nombre de la base
   - Seleccionar bucket S3
   - Elegir modelo de embeddings
   - Configurar base de datos vectorial

3. **Sincronizar**
   - Bedrock procesa documentos
   - Genera embeddings
   - Almacena en base vectorial

4. **Probar en Playground**
   - Hacer preguntas
   - Ver respuestas con citas
   - Verificar fuentes

### Opción 2: Implementación Programática

**Ventajas:**
- Control total
- Personalización avanzada
- Integración con sistemas existentes

**Ejemplo con Python (boto3):**

```python
import boto3

# Cliente de Bedrock
bedrock = boto3.client('bedrock-runtime')

# 1. Generar embedding de la consulta
def get_embedding(text):
    response = bedrock.invoke_model(
        modelId='amazon.titan-embed-text-v1',
        body=json.dumps({"inputText": text})
    )
    return json.loads(response['body'].read())['embedding']

# 2. Buscar en base vectorial (ejemplo con OpenSearch)
def search_vectors(query_embedding, k=3):
    # Búsqueda de similitud en OpenSearch
    # Retorna los K documentos más relevantes
    pass

# 3. Enriquecer prompt y generar respuesta
def rag_query(user_question):
    # Obtener embedding de la pregunta
    query_vector = get_embedding(user_question)
    
    # Buscar documentos relevantes
    relevant_docs = search_vectors(query_vector, k=3)
    
    # Construir contexto
    context = "\n\n".join([doc['text'] for doc in relevant_docs])
    
    # Prompt enriquecido
    prompt = f"""
    Contexto:
    {context}
    
    Pregunta: {user_question}
    
    Responde basándote solo en el contexto proporcionado.
    """
    
    # Invocar LLM
    response = bedrock.invoke_model(
        modelId='anthropic.claude-3-sonnet-20240229-v1:0',
        body=json.dumps({
            "anthropic_version": "bedrock-2023-05-31",
            "messages": [{"role": "user", "content": prompt}],
            "max_tokens": 500
        })
    )
    
    return response
```

## 2.5 Ventajas de RAG

### 1. Información Actualizada

- **Problema**: Modelos pre-entrenados tienen conocimiento estático
- **Solución RAG**: Acceso a información en tiempo real
- **Ejemplo**: Consultar estado de pedidos, precios actuales, inventario

### 2. Reducción de Alucinaciones

- **Problema**: Modelos pueden generar información incorrecta
- **Solución RAG**: Respuestas basadas en documentos verificados
- **Ejemplo**: Respuestas legales basadas en contratos reales

### 3. Transparencia y Trazabilidad

- **Problema**: No saber de dónde viene la información
- **Solución RAG**: Citas y referencias a fuentes
- **Ejemplo**: "Según el manual de usuario, página 15..."

### 4. Adaptabilidad

- **Problema**: Contextos cambian con el tiempo
- **Solución RAG**: Actualizar documentos sin reentrenar modelo
- **Ejemplo**: Actualizar catálogo de productos diariamente

### 5. Datos Privados

- **Problema**: Modelos públicos no conocen datos empresariales
- **Solución RAG**: Acceso a bases de conocimiento privadas
- **Ejemplo**: Políticas internas, procedimientos, contratos

## 2.6 Comparación: RAG vs Fine-tuning vs Pre-training

| Aspecto | RAG | Fine-tuning | Pre-training |
|---------|-----|-------------|--------------|
| **Costo** | Bajo | Medio | Alto |
| **Tiempo** | Minutos | Horas | Días/Semanas |
| **Infraestructura** | No requiere | Requiere GPU | Requiere cluster GPU |
| **Actualización** | Inmediata | Requiere reentrenamiento | Requiere reentrenamiento |
| **Datos necesarios** | Documentos | Miles de ejemplos | Millones de ejemplos |
| **Especialización** | Contexto específico | Tarea específica | Dominio completo |
| **Transparencia** | Alta (citas) | Media | Baja |

### Cuándo usar cada uno:

**RAG:**
- Necesitas información actualizada constantemente
- Tienes documentos/bases de datos existentes
- Quieres transparencia en las respuestas
- Presupuesto limitado

**Fine-tuning:**
- Necesitas especialización en una tarea
- Tienes datos etiquetados de calidad
- Quieres mejorar el estilo de respuesta
- Necesitas seguimiento de instrucciones específicas

**Pre-training:**
- Quieres un modelo completamente personalizado
- Tienes datos masivos de un dominio
- Necesitas diferenciación competitiva
- Presupuesto significativo

## 2.7 Mejores Prácticas para RAG

### 1. Preparación de Documentos

**Calidad sobre cantidad:**
- Documentos bien estructurados
- Información precisa y verificada
- Eliminar contenido redundante

**Formato:**
- Markdown para mejor estructura
- Encabezados claros
- Listas y tablas bien formateadas

### 2. Chunking Efectivo

**Tamaño de chunks:**
- Muy pequeños: Pierden contexto
- Muy grandes: Información irrelevante
- Óptimo: 500-1000 tokens

**Estrategias:**
- Por párrafos
- Por secciones
- Overlap (solapamiento) entre chunks

### 3. Selección de Modelo de Embeddings

**Consideraciones:**
- Idioma soportado
- Dimensionalidad (más dimensiones = más precisión, más costo)
- Velocidad vs precisión

### 4. Optimización de Búsqueda

**Número de chunks (K):**
- Muy pocos: Información insuficiente
- Muchos: Ruido y costo
- Óptimo: 3-5 chunks

**Filtros:**
- Metadata (fecha, categoría, autor)
- Umbral de similitud mínimo

### 5. Prompt Engineering para RAG

**Estructura recomendada:**
```
System: Define el rol y comportamiento

Contexto: [Chunks recuperados]

Instrucciones:
- Responde SOLO basándote en el contexto
- Si no encuentras la respuesta, di "No tengo información"
- Cita las fuentes cuando sea posible

Pregunta: [Query del usuario]
```

## 2.8 Casos de Uso de RAG

### 1. Chatbot de Atención al Cliente

**Escenario**: E-commerce con miles de productos

**Implementación:**
- Base de conocimiento: FAQs, políticas, descripciones de productos
- Actualización: Diaria (nuevos productos, cambios de precio)
- Beneficio: Respuestas precisas 24/7

### 2. Asistente Legal

**Escenario**: Firma de abogados

**Implementación:**
- Base de conocimiento: Leyes, precedentes, contratos
- Búsqueda: Por jurisdicción, fecha, tipo de caso
- Beneficio: Investigación legal rápida y precisa

### 3. Soporte Técnico

**Escenario**: Empresa de software

**Implementación:**
- Base de conocimiento: Documentación, troubleshooting, release notes
- Actualización: Con cada versión
- Beneficio: Resolución rápida de problemas

### 4. Análisis de Documentos Financieros

**Escenario**: Banco o institución financiera

**Implementación:**
- Base de conocimiento: Estados financieros, reportes, regulaciones
- Búsqueda: Por empresa, período, métrica
- Beneficio: Análisis rápido y fundamentado

## Preguntas de Repaso

1. **¿Cuál es la principal ventaja de RAG comparado con un modelo fine-tuned?**
   - a) RAG es más rápido en procesamiento
   - b) RAG permite actualizar información sin reentrenar el modelo
   - c) RAG es más barato en todos los casos
   - d) RAG mejora automáticamente las capacidades del modelo

2. **¿Qué componente administra el proceso de búsqueda en RAG?**
   - a) La base de datos vectorial
   - b) El modelo de embeddings
   - c) El orquestador
   - d) El modelo de lenguaje

3. **¿Por qué necesitamos embeddings en RAG?**
   - a) Para hacer el sistema más rápido
   - b) Porque los modelos necesitan representaciones numéricas del texto
   - c) Para reducir costos
   - d) Para mejorar la seguridad

4. **¿Cuál es el tamaño óptimo típico para chunks en RAG?**
   - a) 50-100 tokens
   - b) 500-1000 tokens
   - c) 5000-10000 tokens
   - d) Todo el documento completo

5. **¿Qué permite RAG que no permite un modelo pre-entrenado solo?**
   - a) Generar texto más creativo
   - b) Acceder a información privada y actualizada
   - c) Procesar imágenes
   - d) Reducir el costo por token

## Respuestas

1. **b** - RAG permite actualizar información sin reentrenar el modelo
2. **c** - El orquestador
3. **b** - Porque los modelos necesitan representaciones numéricas del texto
4. **b** - 500-1000 tokens
5. **b** - Acceder a información privada y actualizada

---

**Capítulo Anterior**: [Introducción a IA Generativa](file:///C:/Users/doeku/Web/AI/01_introduccion_ia_generativa.md)

**Próximo Capítulo**: [Modelos Fundacionales y Amazon Bedrock](file:///C:/Users/doeku/Web/AI/03_modelos_fundacionales_bedrock.md)
