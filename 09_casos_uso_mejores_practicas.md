# Capítulo 9: Casos de Uso y Mejores Prácticas

## 9.1 Casos de Uso Empresariales

### 1. Chatbot de Atención al Cliente

**Escenario**: E-commerce con 10,000 consultas/día

**Arquitectura:**
```
Usuario → API Gateway → Lambda → Bedrock (Claude Haiku) → RAG (Knowledge Base)
                                      ↓
                                  Guardrails
                                      ↓
                                  DynamoDB (Sesiones)
```

**Implementación:**

```python
import boto3
import json
from datetime import datetime

class CustomerSupportBot:
    def __init__(self):
        self.bedrock = boto3.client('bedrock-runtime')
        self.dynamodb = boto3.resource('dynamodb')
        self.sessions_table = self.dynamodb.Table('chat-sessions')
        
    def handle_message(self, user_id, message, session_id=None):
        # 1. Recuperar o crear sesión
        if not session_id:
            session_id = f"{user_id}-{datetime.now().timestamp()}"
            history = []
        else:
            session = self.sessions_table.get_item(Key={'session_id': session_id})
            history = session.get('Item', {}).get('history', [])
        
        # 2. Construir prompt con historial
        messages = history + [{"role": "user", "content": message}]
        
        # 3. Invocar Bedrock con RAG
        response = self.bedrock.invoke_model(
            modelId='anthropic.claude-3-haiku-20240307-v1:0',
            body=json.dumps({
                "anthropic_version": "bedrock-2023-05-31",
                "messages": messages,
                "max_tokens": 300,
                "temperature": 0.3,  # Baja para consistencia
                "system": """Eres un asistente de atención al cliente.
                Usa lenguaje cordial y profesional.
                Si no sabes algo, ofrece escalar a un humano."""
            })
        )
        
        result = json.loads(response['body'].read())
        assistant_message = result['content'][0]['text']
        
        # 4. Guardar en historial
        messages.append({"role": "assistant", "content": assistant_message})
        self.sessions_table.put_item(Item={
            'session_id': session_id,
            'user_id': user_id,
            'history': messages,
            'updated_at': datetime.now().isoformat()
        })
        
        return {
            'session_id': session_id,
            'response': assistant_message
        }
```

**Métricas de éxito:**
- Resolución en primer contacto: 75%
- Tiempo promedio de respuesta: <2s
- Satisfacción del cliente: 4.5/5
- Reducción de tickets a humanos: 60%

**Costos estimados:**
```
10,000 consultas/día
Promedio: 100 tokens input, 150 tokens output
Modelo: Claude Haiku

Costo/día: (10,000 * 100 / 1000 * $0.00025) + (10,000 * 150 / 1000 * $0.00125)
         = $0.25 + $1.875 = $2.13/día
         = $64/mes

Ahorro vs agentes humanos: ~$15,000/mes
ROI: 23,400%
```

### 2. Análisis de Documentos Legales

**Escenario**: Firma de abogados analiza contratos

**Arquitectura:**
```
PDF → S3 → Textract → Chunking → Embeddings → OpenSearch
                                                    ↓
Usuario → Bedrock (Claude Opus) ← RAG ←────────────┘
```

**Implementación:**

```python
class LegalDocumentAnalyzer:
    def __init__(self):
        self.bedrock = boto3.client('bedrock-runtime')
        self.textract = boto3.client('textract')
        self.opensearch = OpenSearchClient()
        
    def analyze_contract(self, pdf_path, questions):
        # 1. Extraer texto del PDF
        text = self.extract_text_from_pdf(pdf_path)
        
        # 2. Indexar en OpenSearch
        doc_id = self.index_document(text)
        
        # 3. Analizar con preguntas específicas
        results = {}
        for question in questions:
            answer = self.ask_question(doc_id, question)
            results[question] = answer
        
        return results
    
    def extract_text_from_pdf(self, pdf_path):
        # Usar Textract para OCR
        with open(pdf_path, 'rb') as file:
            response = self.textract.detect_document_text(
                Document={'Bytes': file.read()}
            )
        
        text = ""
        for block in response['Blocks']:
            if block['BlockType'] == 'LINE':
                text += block['Text'] + "\n"
        
        return text
    
    def ask_question(self, doc_id, question):
        # Buscar contexto relevante
        context = self.opensearch.search(doc_id, question)
        
        # Preguntar a Claude Opus (mejor para análisis complejo)
        prompt = f"""
        Analiza el siguiente fragmento de contrato y responde la pregunta.
        
        Contexto:
        {context}
        
        Pregunta: {question}
        
        Proporciona:
        1. Respuesta directa
        2. Cita textual relevante
        3. Implicaciones legales
        """
        
        response = self.bedrock.invoke_model(
            modelId='anthropic.claude-3-opus-20240229-v1:0',
            body=json.dumps({
                "messages": [{"role": "user", "content": prompt}],
                "max_tokens": 1000,
                "temperature": 0.1  # Muy baja para precisión
            })
        )
        
        return json.loads(response['body'].read())['content'][0]['text']

# Uso
analyzer = LegalDocumentAnalyzer()

questions = [
    "¿Cuál es la duración del contrato?",
    "¿Qué cláusulas de terminación existen?",
    "¿Hay cláusulas de no competencia?",
    "¿Cuáles son las penalizaciones por incumplimiento?"
]

results = analyzer.analyze_contract('contrato.pdf', questions)
```

**Beneficios:**
- Análisis en minutos vs horas
- Identificación de riesgos
- Consistencia en revisiones
- Reducción de errores humanos

### 3. Generación de Contenido de Marketing

**Escenario**: Agencia genera contenido para redes sociales

**Arquitectura:**
```
Brief → Bedrock (Claude Sonnet) → Contenido
  ↓                                    ↓
Brand Guidelines (RAG)           Validación
                                       ↓
                                  Aprobación humana
```

**Implementación:**

```python
class ContentGenerator:
    def __init__(self):
        self.bedrock = boto3.client('bedrock-runtime')
        
    def generate_social_media_post(self, product, platform, tone):
        # Características por plataforma
        platform_specs = {
            'twitter': {'max_length': 280, 'hashtags': 3},
            'instagram': {'max_length': 2200, 'hashtags': 10},
            'linkedin': {'max_length': 3000, 'hashtags': 5}
        }
        
        specs = platform_specs[platform]
        
        prompt = f"""
        Genera un post para {platform} sobre: {product}
        
        Requisitos:
        - Tono: {tone}
        - Máximo {specs['max_length']} caracteres
        - Incluir {specs['hashtags']} hashtags relevantes
        - Call-to-action claro
        - Emojis apropiados
        
        Formato:
        [Texto del post]
        
        Hashtags: [lista]
        """
        
        response = self.bedrock.invoke_model(
            modelId='anthropic.claude-3-5-sonnet-20240620-v1:0',
            body=json.dumps({
                "messages": [{"role": "user", "content": prompt}],
                "max_tokens": 500,
                "temperature": 0.8  # Alta para creatividad
            })
        )
        
        return json.loads(response['body'].read())['content'][0]['text']
    
    def generate_campaign(self, product, platforms, variations=3):
        """Genera campaña completa multi-plataforma"""
        campaign = {}
        
        for platform in platforms:
            campaign[platform] = []
            for i in range(variations):
                tone = ['profesional', 'casual', 'entusiasta'][i]
                post = self.generate_social_media_post(product, platform, tone)
                campaign[platform].append({
                    'variation': i + 1,
                    'tone': tone,
                    'content': post
                })
        
        return campaign

# Uso
generator = ContentGenerator()

campaign = generator.generate_campaign(
    product="Nuevo smartphone XYZ Pro",
    platforms=['twitter', 'instagram', 'linkedin'],
    variations=3
)
```

**Resultados:**
- 90% reducción en tiempo de creación
- 3x más variaciones para A/B testing
- Consistencia de marca
- Costo: $50/mes vs $5,000 de freelancer

### 4. Asistente de Código

**Escenario**: Equipo de desarrollo usa asistente IA

**Implementación:**

```python
class CodeAssistant:
    def __init__(self):
        self.bedrock = boto3.client('bedrock-runtime')
        
    def generate_code(self, description, language, framework=None):
        prompt = f"""
        Genera código {language} para: {description}
        {f'Usando framework: {framework}' if framework else ''}
        
        Requisitos:
        1. Código limpio y bien comentado
        2. Manejo de errores
        3. Buenas prácticas
        4. Tests unitarios
        
        Formato:
        ```{language}
        [código]
        ```
        
        Explicación: [breve explicación]
        """
        
        response = self.bedrock.invoke_model(
            modelId='anthropic.claude-3-5-sonnet-20240620-v1:0',
            body=json.dumps({
                "messages": [{"role": "user", "content": prompt}],
                "max_tokens": 2000,
                "temperature": 0.2
            })
        )
        
        return json.loads(response['body'].read())['content'][0]['text']
    
    def review_code(self, code, language):
        prompt = f"""
        Revisa este código {language}:
        
        ```{language}
        {code}
        ```
        
        Analiza:
        1. Bugs potenciales
        2. Vulnerabilidades de seguridad
        3. Optimizaciones
        4. Mejores prácticas
        5. Legibilidad
        
        Proporciona sugerencias concretas.
        """
        
        response = self.bedrock.invoke_model(
            modelId='anthropic.claude-3-5-sonnet-20240620-v1:0',
            body=json.dumps({
                "messages": [{"role": "user", "content": prompt}],
                "max_tokens": 1500,
                "temperature": 0.1
            })
        )
        
        return json.loads(response['body'].read())['content'][0]['text']

# Uso
assistant = CodeAssistant()

# Generar código
code = assistant.generate_code(
    description="API REST para gestión de usuarios con autenticación JWT",
    language="Python",
    framework="FastAPI"
)

# Revisar código
review = assistant.review_code(code, "Python")
```

## 9.2 Mejores Prácticas de Implementación

### 1. Prompt Engineering

**Estructura óptima:**

```python
SYSTEM_PROMPT = """
[ROL]
Eres un [rol específico] experto en [dominio].

[RESPONSABILIDADES]
Tus tareas son:
1. [Tarea 1]
2. [Tarea 2]
3. [Tarea 3]

[REGLAS]
- [Regla 1]
- [Regla 2]
- [Regla 3]

[FORMATO]
Estructura tus respuestas así:
[formato esperado]

[TONO]
[Descripción del tono]
"""

USER_PROMPT = """
[CONTEXTO]
[Información de fondo]

[TAREA]
[Qué debe hacer]

[RESTRICCIONES]
[Limitaciones o requisitos]

[EJEMPLOS] (opcional)
[Ejemplos de entrada/salida]
"""
```

**Ejemplo concreto:**

```python
system = """
Eres un analista financiero experto en inversiones.

Responsabilidades:
1. Analizar datos financieros
2. Identificar tendencias
3. Proporcionar recomendaciones

Reglas:
- Basa análisis en datos proporcionados
- Menciona riesgos claramente
- No garantices retornos
- Cita fuentes de datos

Formato:
1. Resumen ejecutivo (2-3 líneas)
2. Análisis detallado
3. Recomendación
4. Riesgos

Tono: Profesional, objetivo, basado en datos
"""

user = """
Contexto:
Empresa XYZ, sector tecnología, últimos 3 trimestres:
Q1: Ingresos $10M, Ganancia $2M
Q2: Ingresos $12M, Ganancia $2.5M
Q3: Ingresos $15M, Ganancia $3M

Tarea:
Analiza el desempeño y recomienda si invertir.

Restricciones:
- Horizonte de inversión: 1 año
- Perfil de riesgo: Moderado
"""
```

### 2. Manejo de Errores

```python
class RobustBedrockClient:
    def __init__(self):
        self.bedrock = boto3.client('bedrock-runtime')
        self.max_retries = 3
        
    def invoke_with_retry(self, model_id, prompt, **kwargs):
        for attempt in range(self.max_retries):
            try:
                response = self.bedrock.invoke_model(
                    modelId=model_id,
                    body=json.dumps({
                        "messages": [{"role": "user", "content": prompt}],
                        **kwargs
                    })
                )
                return json.loads(response['body'].read())
                
            except ClientError as e:
                error_code = e.response['Error']['Code']
                
                if error_code == 'ThrottlingException':
                    # Esperar y reintentar
                    wait_time = 2 ** attempt  # Exponential backoff
                    time.sleep(wait_time)
                    continue
                    
                elif error_code == 'ValidationException':
                    # Error de validación, no reintentar
                    raise ValueError(f"Prompt inválido: {e}")
                    
                elif error_code == 'ModelTimeoutException':
                    # Timeout, reintentar
                    continue
                    
                else:
                    # Otro error, propagar
                    raise
                    
            except Exception as e:
                # Error inesperado
                print(f"Error inesperado: {e}")
                if attempt == self.max_retries - 1:
                    raise
                continue
        
        raise Exception("Máximo de reintentos alcanzado")
```

### 3. Optimización de Costos

**Estrategias:**

```python
class CostOptimizedClient:
    def __init__(self):
        self.cache = {}  # Caché simple
        
    def select_model(self, task_complexity, latency_requirement):
        """Seleccionar modelo óptimo según requisitos"""
        
        if task_complexity == 'simple' and latency_requirement == 'low':
            return 'anthropic.claude-3-haiku-20240307-v1:0'
        
        elif task_complexity == 'medium':
            return 'anthropic.claude-3-5-sonnet-20240620-v1:0'
        
        elif task_complexity == 'complex':
            return 'anthropic.claude-3-opus-20240229-v1:0'
        
        else:
            # Default: balance
            return 'anthropic.claude-3-5-sonnet-20240620-v1:0'
    
    def invoke_with_cache(self, prompt, model_id, ttl=3600):
        """Usar caché para prompts repetidos"""
        
        cache_key = f"{model_id}:{hash(prompt)}"
        
        # Buscar en caché
        if cache_key in self.cache:
            cached_time, cached_response = self.cache[cache_key]
            if time.time() - cached_time < ttl:
                return cached_response
        
        # Invocar modelo
        response = self.invoke_model(model_id, prompt)
        
        # Guardar en caché
        self.cache[cache_key] = (time.time(), response)
        
        return response
    
    def batch_process(self, prompts, model_id):
        """Procesar en lote para eficiencia"""
        
        # Agrupar prompts similares
        # Procesar en paralelo
        # Retornar resultados
        
        with ThreadPoolExecutor(max_workers=10) as executor:
            futures = [
                executor.submit(self.invoke_model, model_id, prompt)
                for prompt in prompts
            ]
            results = [f.result() for f in futures]
        
        return results
```

### 4. Monitoreo y Observabilidad

```python
import logging
from datetime import datetime

class ObservableBedrockClient:
    def __init__(self):
        self.bedrock = boto3.client('bedrock-runtime')
        self.cloudwatch = boto3.client('cloudwatch')
        self.logger = logging.getLogger(__name__)
        
    def invoke_with_metrics(self, model_id, prompt, user_id=None):
        start_time = time.time()
        
        try:
            # Invocar modelo
            response = self.bedrock.invoke_model(
                modelId=model_id,
                body=json.dumps({
                    "messages": [{"role": "user", "content": prompt}],
                    "max_tokens": 500
                })
            )
            
            result = json.loads(response['body'].read())
            latency = time.time() - start_time
            
            # Extraer métricas
            input_tokens = len(prompt.split())  # Aproximado
            output_tokens = len(result['content'][0]['text'].split())
            
            # Enviar a CloudWatch
            self.send_metrics({
                'Latency': latency,
                'InputTokens': input_tokens,
                'OutputTokens': output_tokens,
                'Success': 1
            })
            
            # Log
            self.logger.info(f"Invocación exitosa: {latency:.2f}s, {input_tokens + output_tokens} tokens")
            
            return result
            
        except Exception as e:
            latency = time.time() - start_time
            
            # Métrica de error
            self.send_metrics({
                'Latency': latency,
                'Success': 0,
                'Error': 1
            })
            
            # Log de error
            self.logger.error(f"Error en invocación: {e}")
            
            raise
    
    def send_metrics(self, metrics):
        """Enviar métricas a CloudWatch"""
        metric_data = []
        
        for name, value in metrics.items():
            metric_data.append({
                'MetricName': name,
                'Value': value,
                'Unit': 'None',
                'Timestamp': datetime.now()
            })
        
        self.cloudwatch.put_metric_data(
            Namespace='CustomBedrock',
            MetricData=metric_data
        )
```

## 9.3 Patrones de Diseño

### 1. Chain of Thought (Cadena de Pensamiento)

```python
def solve_with_chain_of_thought(problem):
    prompt = f"""
    Resuelve este problema paso a paso:
    
    Problema: {problem}
    
    Piensa en voz alta:
    1. ¿Qué información tengo?
    2. ¿Qué necesito calcular?
    3. ¿Qué pasos debo seguir?
    4. Ejecuta cada paso
    5. Verifica el resultado
    
    Formato:
    Pensamiento: [tu razonamiento]
    Respuesta: [respuesta final]
    """
    
    return invoke_bedrock(prompt)
```

### 2. Few-Shot Learning

```python
def classify_with_examples(text, examples):
    prompt = f"""
    Clasifica el siguiente texto en una categoría.
    
    Ejemplos:
    {format_examples(examples)}
    
    Texto a clasificar: {text}
    Categoría:
    """
    
    return invoke_bedrock(prompt)

def format_examples(examples):
    return "\n".join([
        f"Texto: {ex['text']}\nCategoría: {ex['category']}\n"
        for ex in examples
    ])
```

### 3. Self-Consistency

```python
def answer_with_self_consistency(question, n=5):
    """Generar múltiples respuestas y tomar la más común"""
    
    answers = []
    for _ in range(n):
        response = invoke_bedrock(question, temperature=0.7)
        answers.append(response)
    
    # Encontrar respuesta más común
    from collections import Counter
    most_common = Counter(answers).most_common(1)[0][0]
    
    return most_common
```

## Preguntas de Repaso

1. **¿Cuál es el modelo más apropiado para un chatbot de alto volumen?**
   - a) Claude 3 Opus
   - b) Claude 3 Haiku
   - c) Claude 3.5 Sonnet
   - d) Titan Image Generator

2. **¿Qué temperatura usar para análisis legal que requiere precisión?**
   - a) 0.9-1.0
   - b) 0.6-0.8
   - c) 0.1-0.3
   - d) No importa

3. **¿Cuál es una buena práctica para reducir costos?**
   - a) Usar siempre el modelo más caro
   - b) Implementar caché para prompts repetidos
   - c) No monitorear el uso
   - d) Usar temperatura alta siempre

4. **¿Qué es Chain of Thought?**
   - a) Un modelo de lenguaje
   - b) Hacer que el modelo razone paso a paso
   - c) Un servicio de AWS
   - d) Un tipo de cifrado

5. **¿Para qué sirve el exponential backoff?**
   - a) Aumentar creatividad
   - b) Manejar throttling con reintentos espaciados
   - c) Reducir tokens
   - d) Mejorar seguridad

## Respuestas

1. **b** - Claude 3 Haiku (rápido y económico)
2. **c** - 0.1-0.3 (baja temperatura para precisión)
3. **b** - Implementar caché para prompts repetidos
4. **b** - Hacer que el modelo razone paso a paso
5. **b** - Manejar throttling con reintentos espaciados

---

**Capítulo Anterior**: [Agentes en Bedrock](file:///C:/Users/doeku/Web/AI/08_agentes_bedrock.md)

**Próximo Capítulo**: [Preparación para Certificación](file:///C:/Users/doeku/Web/AI/10_preparacion_certificacion.md)
