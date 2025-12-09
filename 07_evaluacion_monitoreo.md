# Capítulo 7: Evaluación y Monitoreo de Modelos

## 7.1 Importancia de la Evaluación

### ¿Por qué Evaluar?

**Razones clave:**
1. **Verificar desempeño**: ¿El modelo cumple los requisitos?
2. **Comparar modelos**: ¿Cuál es el mejor para mi caso?
3. **Detectar problemas**: Sesgo, drift, degradación
4. **Optimizar costos**: Modelo adecuado = menor costo
5. **Cumplimiento**: Demostrar que el modelo es justo y preciso

### Ciclo de Vida del Modelo

```
Desarrollo → Evaluación → Despliegue → Monitoreo → Reentrenamiento
                ↑                                        ↓
                └────────────────────────────────────────┘
```

## 7.2 Evaluación en Amazon Bedrock

### Model Evaluation en Bedrock

**Función**: Comparar modelos con métricas estándar

#### Tipos de Evaluación

**1. Automatic Evaluation**
- Métricas predefinidas
- Datasets estándar
- Resultados cuantitativos

**2. Human Evaluation**
- Revisión manual
- Evaluación cualitativa
- Feedback subjetivo

### Crear Job de Evaluación

```python
import boto3

bedrock = boto3.client('bedrock')

# Crear evaluación
response = bedrock.create_evaluation_job(
    jobName='eval-claude-vs-titan',
    jobDescription='Comparar Claude y Titan para Q&A',
    roleArn='arn:aws:iam::123456789:role/BedrockEvalRole',
    
    # Modelos a evaluar
    inferenceConfig={
        'models': [
            {
                'bedrockModel': {
                    'modelIdentifier': 'anthropic.claude-3-sonnet-20240229-v1:0'
                }
            },
            {
                'bedrockModel': {
                    'modelIdentifier': 'amazon.titan-text-express-v1'
                }
            }
        ]
    },
    
    # Dataset de evaluación
    evaluationDatasetLocation={
        's3Uri': 's3://mi-bucket/eval-dataset.jsonl'
    },
    
    # Métricas
    evaluationTaskTypes=['Summarization', 'QuestionAndAnswer'],
    
    # Dónde guardar resultados
    outputDataConfig={
        's3Uri': 's3://mi-bucket/eval-results/'
    }
)
```

### Formato del Dataset

```jsonl
{"prompt": "¿Qué es Amazon Bedrock?", "reference": "Amazon Bedrock es un servicio..."}
{"prompt": "Explica RAG", "reference": "RAG es una arquitectura que combina..."}
```

### Métricas Disponibles

**Para Generación de Texto:**
- **ROUGE**: Similitud con texto de referencia
- **BLEU**: Calidad de traducción/generación
- **BERTScore**: Similitud semántica

**Para Q&A:**
- **Exactitud**: Respuesta correcta vs incorrecta
- **F1 Score**: Balance precisión/recall

**Para Resumen:**
- **ROUGE-L**: Subsecuencias comunes más largas
- **Coherencia**: Qué tan coherente es el resumen

### Resultados

**Dashboard muestra:**
- Métricas por modelo
- Comparación lado a lado
- Ejemplos de respuestas
- Recomendaciones

**Ejemplo de resultado:**
```
Claude 3 Sonnet:
- ROUGE-L: 0.85
- BERTScore: 0.92
- Latencia promedio: 1.2s
- Costo por 1K tokens: $0.003

Titan Text Express:
- ROUGE-L: 0.78
- BERTScore: 0.85
- Latencia promedio: 0.8s
- Costo por 1K tokens: $0.0002

Recomendación: Claude para calidad, Titan para costo
```

## 7.3 Comparación de Modelos en Playground

### Modo Comparación

**Pasos:**
1. Ir a Bedrock Playground
2. Seleccionar "Compare"
3. Elegir 2-3 modelos
4. Ingresar prompt
5. Ejecutar

**Métricas mostradas:**
- **Time to first token**: Latencia inicial
- **Total time**: Tiempo completo
- **Input tokens**: Tokens de entrada
- **Output tokens**: Tokens generados
- **Total tokens**: Input + Output
- **Costo estimado**: Por invocación

### Ejemplo Práctico

**Prompt**: "Explica qué es serverless en 100 palabras"

**Resultados:**

| Métrica | Claude 3.5 Sonnet | Claude 3 Haiku | Titan Express |
|---------|-------------------|----------------|---------------|
| Latencia | 1.2s | 0.4s | 0.6s |
| Input tokens | 15 | 15 | 15 |
| Output tokens | 120 | 95 | 110 |
| Calidad (subjetiva) | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ |
| Costo | $0.0018 | $0.00012 | $0.00007 |

**Decisión**: Depende del caso de uso
- Calidad máxima → Sonnet
- Balance → Haiku
- Volumen alto → Titan

## 7.4 SageMaker Clarify

### ¿Qué es Clarify?

**Servicio para**:
- Detectar sesgo en datos y modelos
- Explicar predicciones
- Monitorear modelos en producción

### Análisis de Sesgo

#### Pre-training Bias

**Detectar sesgo en datos ANTES de entrenar**

```python
from sagemaker import clarify

# Configurar procesador
clarify_processor = clarify.SageMakerClarifyProcessor(
    role=role,
    instance_count=1,
    instance_type='ml.m5.xlarge',
    sagemaker_session=session
)

# Configurar análisis de sesgo
bias_config = clarify.BiasConfig(
    label_values_or_threshold=[1],  # Valor positivo
    facet_name='gender',  # Característica sensible
    facet_values_or_threshold=[0]  # Grupo de referencia
)

# Ejecutar
clarify_processor.run_pre_training_bias(
    data_config=data_config,
    bias_config=bias_config,
    output_path='s3://bucket/bias-report/'
)
```

**Métricas de sesgo:**
- **Class Imbalance (CI)**: Desbalance entre clases
- **Difference in Proportions of Labels (DPL)**: Diferencia en etiquetas positivas

#### Post-training Bias

**Detectar sesgo en predicciones del modelo**

```python
# Configurar modelo
model_config = clarify.ModelConfig(
    model_name='mi-modelo',
    instance_type='ml.m5.xlarge',
    instance_count=1,
    accept_type='application/json'
)

# Ejecutar análisis post-training
clarify_processor.run_post_training_bias(
    data_config=data_config,
    bias_config=bias_config,
    model_config=model_config,
    output_path='s3://bucket/bias-report/'
)
```

**Métricas adicionales:**
- **Disparate Impact (DI)**: Impacto desproporcionado
- **Difference in Conditional Acceptance (DCA)**: Diferencia en aceptación

### Explicabilidad con SHAP

```python
# Configurar SHAP
shap_config = clarify.SHAPConfig(
    baseline=[baseline_data],
    num_samples=100,
    agg_method='mean_abs'
)

# Ejecutar análisis
clarify_processor.run_explainability(
    data_config=data_config,
    model_config=model_config,
    explainability_config=shap_config,
    output_path='s3://bucket/explainability-report/'
)
```

**Resultado**: Importancia de cada característica

### Reporte de Clarify

**Contenido del reporte:**
- Gráficas de distribución
- Métricas de sesgo
- Valores SHAP
- Recomendaciones

**Formato**: HTML interactivo + JSON

## 7.5 Monitoreo con CloudWatch

### Métricas de Bedrock

**Métricas automáticas:**
- `InvocationCount`: Número de invocaciones
- `InvocationLatency`: Latencia
- `InputTokenCount`: Tokens de entrada
- `OutputTokenCount`: Tokens de salida
- `InvocationErrors`: Errores

### Crear Dashboard

```python
cloudwatch = boto3.client('cloudwatch')

# Crear dashboard
dashboard_body = {
    "widgets": [
        {
            "type": "metric",
            "properties": {
                "metrics": [
                    ["AWS/Bedrock", "InvocationCount", {"stat": "Sum"}],
                    [".", "InvocationLatency", {"stat": "Average"}]
                ],
                "period": 300,
                "stat": "Average",
                "region": "us-east-1",
                "title": "Bedrock - Uso y Latencia"
            }
        },
        {
            "type": "metric",
            "properties": {
                "metrics": [
                    ["AWS/Bedrock", "InputTokenCount", {"stat": "Sum"}],
                    [".", "OutputTokenCount", {"stat": "Sum"}]
                ],
                "period": 300,
                "region": "us-east-1",
                "title": "Bedrock - Tokens"
            }
        }
    ]
}

cloudwatch.put_dashboard(
    DashboardName='Bedrock-Monitoring',
    DashboardBody=json.dumps(dashboard_body)
)
```

### Alarmas

```python
# Alarma para latencia alta
cloudwatch.put_metric_alarm(
    AlarmName='bedrock-latencia-alta',
    ComparisonOperator='GreaterThanThreshold',
    EvaluationPeriods=2,
    MetricName='InvocationLatency',
    Namespace='AWS/Bedrock',
    Period=300,
    Statistic='Average',
    Threshold=3000,  # 3 segundos
    ActionsEnabled=True,
    AlarmActions=['arn:aws:sns:us-east-1:123:alertas'],
    AlarmDescription='Latencia mayor a 3 segundos'
)

# Alarma para errores
cloudwatch.put_metric_alarm(
    AlarmName='bedrock-errores',
    ComparisonOperator='GreaterThanThreshold',
    EvaluationPeriods=1,
    MetricName='InvocationErrors',
    Namespace='AWS/Bedrock',
    Period=300,
    Statistic='Sum',
    Threshold=10,
    ActionsEnabled=True,
    AlarmActions=['arn:aws:sns:us-east-1:123:alertas'],
    AlarmDescription='Más de 10 errores en 5 minutos'
)
```

## 7.6 Detección de Drift

### ¿Qué es Model Drift?

**Definición**: Degradación del modelo con el tiempo

**Causas:**
- Cambio en distribución de datos
- Cambio en comportamiento de usuarios
- Cambio en el dominio del problema

**Ejemplo:**
```
Modelo de recomendación de productos:
- Enero: 95% precisión
- Marzo: 92% precisión
- Junio: 85% precisión

Causa: Preferencias de usuarios cambiaron
```

### Tipos de Drift

**1. Data Drift**
- Distribución de entrada cambia
- Ejemplo: Más usuarios móviles que desktop

**2. Concept Drift**
- Relación entre entrada y salida cambia
- Ejemplo: Definición de "spam" evoluciona

**3. Prediction Drift**
- Distribución de predicciones cambia
- Ejemplo: Más predicciones "positivas"

### Detección

**SageMaker Model Monitor:**

```python
from sagemaker.model_monitor import DataCaptureConfig, ModelMonitor

# Configurar captura de datos
data_capture_config = DataCaptureConfig(
    enable_capture=True,
    sampling_percentage=100,
    destination_s3_uri='s3://bucket/data-capture'
)

# Desplegar con monitoreo
predictor = model.deploy(
    initial_instance_count=1,
    instance_type='ml.m5.xlarge',
    data_capture_config=data_capture_config
)

# Crear baseline
monitor = ModelMonitor(
    role=role,
    instance_count=1,
    instance_type='ml.m5.xlarge',
    max_runtime_in_seconds=3600
)

# Sugerir baseline
monitor.suggest_baseline(
    baseline_dataset='s3://bucket/baseline.csv',
    dataset_format=DatasetFormat.csv(header=True),
    output_s3_uri='s3://bucket/baseline-results'
)

# Programar monitoreo
monitor.create_monitoring_schedule(
    monitor_schedule_name='modelo-drift-monitor',
    endpoint_input=predictor.endpoint_name,
    output_s3_uri='s3://bucket/monitoring-results',
    schedule_cron_expression='cron(0 * * * ? *)'  # Cada hora
)
```

**Alertas automáticas** cuando se detecta drift

## 7.7 Human-in-the-Loop

### Amazon Augmented AI (A2I)

**Función**: Incorporar revisión humana en workflows de ML

### Casos de Uso

**1. Revisión de respuestas de baja confianza**
```python
from sagemaker import get_execution_role
from sagemaker.a2i import A2I

# Configurar flujo de revisión humana
human_task_config = {
    'WorkteamArn': 'arn:aws:sagemaker:us-east-1:123:workteam/team',
    'HumanTaskUiArn': 'arn:aws:sagemaker:us-east-1:123:human-task-ui/ui',
    'TaskTitle': 'Revisar respuesta de chatbot',
    'TaskDescription': 'Verificar si la respuesta es apropiada',
    'TaskCount': 1,
    'TaskAvailabilityLifetimeInSeconds': 3600,
    'TaskTimeLimitInSeconds': 600
}

# Crear flujo
a2i = boto3.client('sagemaker-a2i-runtime')

response = a2i.start_human_loop(
    HumanLoopName=f'review-{timestamp}',
    FlowDefinitionArn='arn:aws:sagemaker:us-east-1:123:flow-definition/review',
    HumanLoopInput={
        'InputContent': json.dumps({
            'prompt': user_question,
            'response': model_response,
            'confidence': 0.65  # Baja confianza
        })
    }
)
```

**2. Validación de contenido sensible**
- Revisar respuestas que mencionan temas delicados
- Verificar que no haya información incorrecta
- Asegurar tono apropiado

**3. Feedback continuo**
- Usuarios marcan respuestas como útiles/no útiles
- Equipo revisa respuestas marcadas
- Mejora continua del sistema

## 7.8 Métricas Clave

### Para Modelos Generativos

**1. Calidad de Respuesta**
- ROUGE, BLEU, BERTScore
- Evaluación humana (1-5 estrellas)

**2. Latencia**
- Time to first token
- Total response time
- P50, P90, P99

**3. Costo**
- Costo por invocación
- Costo por usuario
- Costo total mensual

**4. Satisfacción del Usuario**
- Thumbs up/down
- Tasa de abandono
- Tiempo de interacción

**5. Seguridad**
- Tasa de bloqueo por guardrails
- Detecciones de PII
- Intentos de jailbreak

### Dashboard Completo

**Métricas a monitorear:**
```
┌─────────────────────────────────────┐
│  Dashboard de Producción            │
├─────────────────────────────────────┤
│ Uso:                                │
│  - 10,000 invocaciones/día          │
│  - 500 usuarios activos             │
│                                     │
│ Rendimiento:                        │
│  - Latencia P50: 1.2s               │
│  - Latencia P90: 2.5s               │
│  - Tasa de error: 0.5%              │
│                                     │
│ Calidad:                            │
│  - Satisfacción: 4.2/5              │
│  - Thumbs up: 85%                   │
│                                     │
│ Seguridad:                          │
│  - Bloqueos por guardrail: 12/día   │
│  - PII detectado: 3/día             │
│                                     │
│ Costos:                             │
│  - Hoy: $45                         │
│  - Mes: $1,200                      │
│  - Proyección: $1,350               │
└─────────────────────────────────────┘
```

## Preguntas de Repaso

1. **¿Qué es model drift?**
   - a) Un error de sintaxis
   - b) Degradación del modelo con el tiempo
   - c) Un tipo de cifrado
   - d) Una métrica de costo

2. **¿Qué servicio de AWS ayuda a detectar sesgo en modelos?**
   - a) CloudWatch
   - b) SageMaker Clarify
   - c) Lambda
   - d) S3

3. **¿Qué métrica mide la latencia inicial de respuesta?**
   - a) Total time
   - b) Time to first token
   - c) InvocationCount
   - d) OutputTokenCount

4. **¿Para qué sirve Amazon A2I?**
   - a) Cifrado de datos
   - b) Incorporar revisión humana en workflows
   - c) Monitoreo de costos
   - d) Gestión de claves

5. **¿Qué es SHAP?**
   - a) Un modelo de lenguaje
   - b) Una técnica para explicar predicciones
   - c) Un servicio de AWS
   - d) Un tipo de cifrado

## Respuestas

1. **b** - Degradación del modelo con el tiempo
2. **b** - SageMaker Clarify
3. **b** - Time to first token
4. **b** - Incorporar revisión humana en workflows
5. **b** - Una técnica para explicar predicciones

---

**Capítulo Anterior**: [Seguridad en Aplicaciones de IA](file:///C:/Users/doeku/Web/AI/06_seguridad_aplicaciones_ia.md)

**Próximo Capítulo**: [Agentes en Amazon Bedrock](file:///C:/Users/doeku/Web/AI/08_agentes_bedrock.md)
