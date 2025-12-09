# Capítulo 4: Personalización de Modelos

## 4.1 Introducción a la Personalización

Los modelos fundacionales tienen conocimiento general, pero a veces necesitamos **especializarlos** para tareas o dominios específicos.

### Opciones de Personalización

1. **RAG**: Agregar contexto sin modificar el modelo
2. **Fine-tuning**: Ajustar el modelo para una tarea específica
3. **Continued Pre-training**: Continuar entrenamiento en un dominio amplio
4. **Training from Scratch**: Construir un modelo completamente nuevo

## 4.2 Fine-tuning (Ajuste Fino)

### ¿Qué es Fine-tuning?

**Fine-tuning** es el proceso de tomar un modelo pre-entrenado y continuar su entrenamiento con un dataset específico para mejorar su desempeño en una tarea particular.

### Cuándo Usar Fine-tuning

**Casos ideales:**
- Necesitas un estilo de respuesta muy específico
- Quieres que el modelo siga instrucciones particulares
- Tienes datos etiquetados de calidad
- Necesitas especialización en una tarea concreta

**Ejemplos:**
- Chatbot con personalidad de marca específica
- Clasificador de documentos legales
- Generador de código en framework específico
- Asistente médico especializado

### Requisitos

**Datos necesarios:**
- **Cantidad mínima**: 100-1000 ejemplos
- **Calidad**: Datos etiquetados correctamente
- **Formato**: Pares de entrada-salida

**Ejemplo de datos para fine-tuning:**
```json
[
  {
    "prompt": "¿Cómo creo un bucket S3?",
    "completion": "Para crear un bucket S3: 1) Ve a la consola de S3..."
  },
  {
    "prompt": "¿Qué es una VPC?",
    "completion": "Una VPC (Virtual Private Cloud) es una red virtual aislada..."
  }
]
```

### Proceso de Fine-tuning

```
Modelo Base → Datos de Entrenamiento → Entrenamiento → Modelo Ajustado
```

**Pasos:**

1. **Preparar datos**
   - Recopilar ejemplos de calidad
   - Formato correcto (JSONL)
   - Dividir en train/validation

2. **Subir a S3**
   ```bash
   aws s3 cp training_data.jsonl s3://mi-bucket/fine-tuning/
   ```

3. **Crear job de fine-tuning**
   ```python
   import boto3
   
   bedrock = boto3.client('bedrock')
   
   response = bedrock.create_model_customization_job(
       jobName='mi-modelo-personalizado',
       customModelName='mi-modelo-aws-v1',
       roleArn='arn:aws:iam::123456789:role/BedrockRole',
       baseModelIdentifier='anthropic.claude-3-haiku-20240307-v1:0',
       trainingDataConfig={
           's3Uri': 's3://mi-bucket/fine-tuning/training_data.jsonl'
       },
       validationDataConfig={
           's3Uri': 's3://mi-bucket/fine-tuning/validation_data.jsonl'
       },
       outputDataConfig={
           's3Uri': 's3://mi-bucket/fine-tuning/output/'
       },
       hyperParameters={
           'epochCount': '3',
           'batchSize': '8',
           'learningRate': '0.00001'
       }
   )
   ```

4. **Monitorear entrenamiento**
   - Tiempo: 1-4 horas típicamente
   - Métricas: Loss, accuracy

5. **Evaluar modelo**
   - Probar con datos de validación
   - Comparar con modelo base

6. **Desplegar**
   - Usar el modelo personalizado en aplicaciones

### Ventajas del Fine-tuning

1. **Mejora el desempeño** en tareas específicas
2. **Menor cantidad de datos** que entrenar desde cero
3. **Menor costo** que pre-training completo
4. **Estilo consistente** de respuestas

### Limitaciones

1. **Requiere datos de calidad**: Garbage in, garbage out
2. **Costo de entrenamiento**: Aunque menor que desde cero
3. **Tiempo**: Horas de entrenamiento
4. **Especialización limitada**: Solo para la tarea entrenada

## 4.3 Instruction-based Fine-tuning

### ¿Qué es?

Tipo especial de fine-tuning enfocado en **seguir instrucciones** de manera precisa.

### Diferencia con Fine-tuning Regular

**Fine-tuning regular:**
- Mejora en una tarea (ej: clasificación)

**Instruction-based:**
- Mejora en seguir instrucciones complejas
- Ejecutar acciones específicas
- Entender comandos estructurados

### Caso de Uso: Integración con APIs

**Escenario**: Chatbot que interactúa con Stripe (pagos)

**Problema**: El modelo no sabe cómo usar la API de Stripe

**Solución**: Fine-tuning basado en instrucciones

**Datos de entrenamiento:**
```json
[
  {
    "instruction": "Crear una suscripción mensual de $99 para el cliente",
    "input": {
      "customer_id": "cus_123",
      "price": 99,
      "interval": "month"
    },
    "output": {
      "api_call": "stripe.subscriptions.create",
      "parameters": {
        "customer": "cus_123",
        "items": [{"price_data": {"currency": "usd", "product": "prod_123", "recurring": {"interval": "month"}, "unit_amount": 9900}}]
      }
    }
  }
]
```

**Resultado**: El modelo aprende a generar llamadas correctas a la API de Stripe

### Ejemplo Práctico

**Workshop en re:Invent:**
- Dataset: 50,000 ejemplos sintéticos
- Tiempo de entrenamiento: 15 minutos
- Resultado: Modelo que ejecuta acciones específicas

**Aplicaciones:**
- Automatización de tareas
- Integración con sistemas empresariales
- Asistentes que ejecutan acciones

## 4.4 Continued Pre-training

### ¿Qué es?

**Continued Pre-training** es continuar el entrenamiento de un modelo fundacional con un dataset grande de un dominio específico.

### Diferencia con Fine-tuning

| Aspecto | Fine-tuning | Continued Pre-training |
|---------|-------------|------------------------|
| **Objetivo** | Tarea específica | Dominio completo |
| **Datos** | Miles de ejemplos | Millones de ejemplos |
| **Tiempo** | Horas | Días/Semanas |
| **Costo** | Medio | Alto |
| **Resultado** | Especialista en tarea | Especialista en dominio |

### Cuándo Usar

**Casos ideales:**
- Tienes datos masivos de un dominio (medicina, finanzas, legal)
- Necesitas conocimiento profundo de un área
- Quieres competir en un mercado específico
- Tienes presupuesto significativo

**Ejemplos:**
- Modelo médico entrenado con millones de papers científicos
- Modelo financiero con datos de mercados
- Modelo legal con leyes y jurisprudencia

### Proceso

```
Modelo Base (ej: LLaMA 3) → Dataset de Dominio → Entrenamiento Continuo → Modelo Especializado
```

**Ejemplo: Modelo Médico**

1. **Modelo base**: LLaMA 3 70B
2. **Dataset**: 
   - 10M papers médicos
   - 5M historiales clínicos (anonimizados)
   - 2M guías clínicas
3. **Entrenamiento**: 2-4 semanas en cluster GPU
4. **Resultado**: Modelo experto en medicina

### Ventajas

1. **Conocimiento profundo** del dominio
2. **No parte desde cero**: Usa transferencia de aprendizaje
3. **Competitivo**: Puede superar modelos generales en el dominio

### Limitaciones

1. **Costo muy alto**: Infraestructura GPU costosa
2. **Tiempo significativo**: Semanas de entrenamiento
3. **Datos masivos necesarios**: Millones de ejemplos
4. **Expertise técnico**: Requiere equipo especializado

## 4.5 Comparación: RAG vs Fine-tuning vs Continued Pre-training

### Tabla Comparativa Completa

| Característica | RAG | Fine-tuning | Continued Pre-training |
|----------------|-----|-------------|------------------------|
| **Costo** | Bajo ($) | Medio ($$) | Alto ($$$$$) |
| **Tiempo de implementación** | Minutos | Horas | Semanas |
| **Datos necesarios** | Documentos | Miles | Millones |
| **Infraestructura** | No requiere | GPU (horas) | Cluster GPU (semanas) |
| **Actualización** | Inmediata | Reentrenar | Reentrenar |
| **Transparencia** | Alta (citas) | Media | Baja |
| **Especialización** | Contexto | Tarea | Dominio |
| **Modificación del modelo** | No | Sí | Sí |
| **Mantenimiento** | Bajo | Medio | Alto |

### Matriz de Decisión

**Pregunta 1: ¿Necesitas información actualizada constantemente?**
- **Sí** → RAG
- **No** → Siguiente pregunta

**Pregunta 2: ¿Tienes datos etiquetados de calidad?**
- **Sí, miles** → Fine-tuning
- **Sí, millones** → Continued Pre-training
- **No** → RAG

**Pregunta 3: ¿Cuál es tu presupuesto?**
- **Limitado** → RAG
- **Moderado** → Fine-tuning
- **Alto** → Continued Pre-training

**Pregunta 4: ¿Qué nivel de especialización necesitas?**
- **Acceso a información** → RAG
- **Tarea específica** → Fine-tuning
- **Dominio completo** → Continued Pre-training

### Combinaciones

**RAG + Fine-tuning:**
- Modelo ajustado para estilo de respuesta
- RAG para información actualizada
- **Ejemplo**: Chatbot bancario con personalidad específica y acceso a transacciones

**Fine-tuning + Continued Pre-training:**
- Pre-training en dominio amplio
- Fine-tuning para tarea específica
- **Ejemplo**: Modelo médico general → Especializado en radiología

## 4.6 Implementación en AWS

### SageMaker para Fine-tuning

**Ventajas de SageMaker:**
- Control total del proceso
- Múltiples algoritmos disponibles
- Integración con Bedrock
- Monitoreo avanzado

**Proceso:**

1. **Preparar datos en SageMaker**
   ```python
   import sagemaker
   from sagemaker.huggingface import HuggingFace
   
   # Configurar sesión
   sess = sagemaker.Session()
   role = sagemaker.get_execution_role()
   
   # Subir datos
   training_input_path = sess.upload_data(
       path='training_data',
       key_prefix='fine-tuning/training'
   )
   ```

2. **Configurar job de entrenamiento**
   ```python
   huggingface_estimator = HuggingFace(
       entry_point='train.py',
       source_dir='./scripts',
       instance_type='ml.p3.2xlarge',
       instance_count=1,
       role=role,
       transformers_version='4.26',
       pytorch_version='1.13',
       py_version='py39',
       hyperparameters={
           'epochs': 3,
           'train_batch_size': 32,
           'model_name': 'meta-llama/Llama-2-7b-hf'
       }
   )
   ```

3. **Entrenar**
   ```python
   huggingface_estimator.fit({'train': training_input_path})
   ```

4. **Desplegar**
   ```python
   predictor = huggingface_estimator.deploy(
       initial_instance_count=1,
       instance_type='ml.g4dn.xlarge'
   )
   ```

### Bedrock para Fine-tuning

**Ventajas de Bedrock:**
- Más simple que SageMaker
- Sin gestión de infraestructura
- Integración directa con modelos fundacionales

**Modelos soportados para fine-tuning:**
- Amazon Titan
- Meta LLaMA 2
- Cohere Command

**Limitaciones:**
- Menos control que SageMaker
- Modelos limitados (no todos soportan fine-tuning)

## 4.7 SageMaker JumpStart

### ¿Qué es JumpStart?

**SageMaker JumpStart** es un catálogo de soluciones pre-configuradas para Machine Learning y IA.

### Características

**Contenido:**
- Modelos fundacionales pre-entrenados
- Algoritmos de ML listos para usar
- Soluciones end-to-end
- Notebooks de ejemplo

**Ventajas:**
- No requiere expertise profundo
- Implementación rápida
- Mejores prácticas incluidas

### Casos de Uso

**1. Probar modelos fundacionales**
- Acceder a modelos de Hugging Face
- Desplegar con un clic
- Experimentar sin código

**2. Fine-tuning simplificado**
- Plantillas pre-configuradas
- Guías paso a paso
- Ejemplos de datos

**3. Soluciones específicas**
- Computer Vision
- NLP
- Detección de fraude
- Recomendaciones

### Ejemplo: Fine-tuning con JumpStart

**Pasos:**

1. **Abrir SageMaker Studio**
2. **Ir a JumpStart**
3. **Seleccionar modelo** (ej: LLaMA 2 7B)
4. **Elegir "Fine-tune"**
5. **Subir dataset**
6. **Configurar hiperparámetros**
7. **Entrenar** (con un clic)
8. **Evaluar y desplegar**

## 4.8 Mejores Prácticas

### Para Fine-tuning

**1. Calidad de datos**
- Revisar manualmente ejemplos
- Eliminar duplicados
- Balancear clases

**2. Tamaño del dataset**
- Mínimo: 100 ejemplos
- Recomendado: 1000-10000
- Más no siempre es mejor

**3. Hiperparámetros**
- Empezar con valores por defecto
- Learning rate bajo (0.00001 - 0.0001)
- Pocas épocas (1-5)

**4. Evaluación**
- Separar train/validation/test
- Métricas relevantes a la tarea
- Comparar con modelo base

### Para Continued Pre-training

**1. Dataset masivo**
- Millones de ejemplos
- Alta calidad y diversidad
- Limpieza exhaustiva

**2. Infraestructura**
- Cluster GPU potente
- Almacenamiento rápido
- Monitoreo constante

**3. Checkpoints**
- Guardar estado regularmente
- Poder reanudar entrenamiento
- Evaluar en checkpoints

## Preguntas de Repaso

1. **¿Cuál es la principal diferencia entre RAG y Fine-tuning?**
   - a) RAG es más caro
   - b) RAG no modifica el modelo, fine-tuning sí
   - c) RAG es más lento
   - d) Fine-tuning no requiere datos

2. **¿Cuántos ejemplos típicamente se necesitan para fine-tuning?**
   - a) 10-50
   - b) 100-1000
   - c) 1,000,000+
   - d) No se necesitan datos

3. **¿Qué técnica usarías para especializar un modelo en todo el dominio médico?**
   - a) RAG
   - b) Fine-tuning
   - c) Continued Pre-training
   - d) Prompt engineering

4. **¿Cuál es una ventaja de fine-tuning sobre RAG?**
   - a) Actualización inmediata
   - b) No requiere infraestructura
   - c) Estilo de respuesta consistente y personalizado
   - d) Transparencia en las fuentes

5. **¿Qué servicio de AWS simplifica el fine-tuning con plantillas?**
   - a) Lambda
   - b) SageMaker JumpStart
   - c) EC2
   - d) S3

## Respuestas

1. **b** - RAG no modifica el modelo, fine-tuning sí
2. **b** - 100-1000 (mínimo 100, recomendado 1000+)
3. **c** - Continued Pre-training (requiere datos masivos del dominio)
4. **c** - Estilo de respuesta consistente y personalizado
5. **b** - SageMaker JumpStart

---

**Capítulo Anterior**: [Modelos Fundacionales y Bedrock](file:///C:/Users/doeku/Web/AI/03_modelos_fundacionales_bedrock.md)

**Próximo Capítulo**: [IA Responsable y Ética](file:///C:/Users/doeku/Web/AI/05_ia_responsable_etica.md)
