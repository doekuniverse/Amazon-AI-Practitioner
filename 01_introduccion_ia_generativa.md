# Capítulo 1: Introducción a la IA Generativa en AWS

## 1.1 ¿Qué es la IA Generativa?

La **Inteligencia Artificial Generativa** es una rama de la IA que se enfoca en crear contenido nuevo y original. A diferencia de los modelos tradicionales de Machine Learning que clasifican o predicen, los modelos generativos pueden:

- **Generar texto** coherente y contextualmente relevante
- **Crear imágenes** a partir de descripciones
- **Producir código** funcional
- **Sintetizar audio y video**
- **Traducir entre idiomas**
- **Resumir documentos extensos**

### Características Principales

1. **Creatividad**: Capacidad de generar contenido original
2. **Contextualización**: Comprensión del contexto para respuestas relevantes
3. **Multimodalidad**: Trabajo con texto, imágenes, audio y video
4. **Adaptabilidad**: Ajuste a diferentes dominios y tareas

## 1.2 Modelos Pre-entrenados vs Modelos Personalizados

### Modelos Pre-entrenados (Foundation Models)

Los **modelos fundacionales** son modelos de IA que han sido entrenados con grandes cantidades de datos y tienen conocimiento general sobre múltiples dominios.

**Características:**
- Entrenados por empresas especializadas (OpenAI, Anthropic, Meta, etc.)
- Conocimiento amplio pero general
- Listos para usar sin entrenamiento adicional
- Ejemplos: GPT, Claude, LLaMA, Titan

**Ventajas:**
- No requieren infraestructura de entrenamiento
- Disponibles inmediatamente
- Actualizaciones constantes por los proveedores
- Menor costo inicial

**Limitaciones:**
- Conocimiento limitado a la fecha de entrenamiento
- No especializados en dominios específicos
- Pueden alucinar (generar información incorrecta)

### Modelos Personalizados

Son modelos que han sido ajustados o entrenados para tareas específicas.

**Tipos de Personalización:**

1. **Fine-tuning**: Ajuste fino para una tarea específica
2. **Continued Pre-training**: Continuar entrenamiento en un dominio amplio
3. **Training from Scratch**: Construir un modelo desde cero

## 1.3 Servicios de AWS para IA Generativa

AWS ofrece un ecosistema completo de servicios para implementar soluciones de IA Generativa:

### Amazon Bedrock

**Servicio principal** para IA Generativa en AWS. Proporciona acceso a múltiples modelos fundacionales a través de una API unificada.

**Características clave:**
- Acceso a modelos de múltiples proveedores
- Sin necesidad de gestionar infraestructura
- Pago por uso (por token)
- Seguridad y privacidad integradas
- Personalización mediante fine-tuning

**Modelos disponibles:**
- **Anthropic Claude**: Excelente para razonamiento y conversación
- **Meta LLaMA**: Modelos open-source potentes
- **Amazon Titan**: Modelos propios de AWS
- **AI21 Labs Jurassic**: Especializados en texto
- **Stability AI**: Generación de imágenes

### Amazon SageMaker

Plataforma completa de Machine Learning para casos que requieren **control total**.

**Casos de uso:**
- Entrenar modelos desde cero
- Fine-tuning avanzado
- Despliegue personalizado
- Control completo de infraestructura

**Componentes importantes:**
- **SageMaker JumpStart**: Plantillas y modelos pre-configurados
- **SageMaker Clarify**: Evaluación de sesgo y explicabilidad
- **SageMaker Model Monitor**: Monitoreo de modelos en producción
- **SageMaker Ground Truth**: Etiquetado de datos

### Amazon Q

Asistente de IA para desarrolladores y empresas.

**Variantes:**
- **Amazon Q Developer**: Ayuda con código y desarrollo
- **Amazon Q Business**: Respuestas basadas en datos empresariales

### Servicios Complementarios

- **Amazon Lex**: Chatbots conversacionales
- **Amazon Polly**: Texto a voz
- **Amazon Transcribe**: Voz a texto
- **Amazon Rekognition**: Análisis de imágenes y video
- **Amazon Comprehend**: Procesamiento de lenguaje natural

## 1.4 Introducción a Amazon Bedrock

### ¿Qué es Amazon Bedrock?

Amazon Bedrock es un servicio completamente administrado que proporciona acceso a modelos fundacionales de alta calidad a través de una **API unificada**.

### Ventajas de Amazon Bedrock

1. **Múltiples Modelos**: Acceso a modelos de diferentes proveedores
2. **Sin Infraestructura**: No necesitas gestionar servidores
3. **Seguridad**: Datos privados y seguros
4. **Personalización**: Fine-tuning sin complejidad
5. **Integración**: Fácil integración con otros servicios AWS
6. **Escalabilidad**: Escala automáticamente según demanda

### Componentes de Bedrock

#### 1. Playground

Interfaz web para experimentar con modelos sin código.

**Funcionalidades:**
- Probar diferentes modelos
- Ajustar hiperparámetros
- Comparar respuestas entre modelos
- Ver métricas de latencia y tokens

#### 2. Knowledge Bases (Bases de Conocimiento)

Implementación de RAG (Retrieval-Augmented Generation) sin código.

**Proceso:**
1. Subir documentos a S3
2. Crear base de conocimiento
3. Bedrock automáticamente:
   - Procesa documentos
   - Crea embeddings
   - Almacena en base de datos vectorial
   - Habilita búsqueda semántica

#### 3. Agents (Agentes)

Agentes inteligentes que pueden ejecutar acciones complejas.

**Capacidades:**
- Orquestación de tareas
- Integración con APIs
- Ejecución de acciones
- Toma de decisiones autónoma

#### 4. Guardrails

Barreras de protección para controlar el comportamiento del modelo.

**Controles disponibles:**
- Filtrado de contenido tóxico
- Bloqueo de temas específicos
- Detección de información sensible
- Validación de respuestas

### Modelos Disponibles en Bedrock

#### Anthropic Claude

**Versiones:**
- Claude 3.5 Sonnet: Mejor balance rendimiento/costo
- Claude 3 Opus: Máximo rendimiento
- Claude 3 Haiku: Más rápido y económico

**Fortalezas:**
- Razonamiento complejo
- Análisis de documentos largos
- Seguimiento de instrucciones
- Contexto extendido (200K tokens)

#### Meta LLaMA

**Versiones:**
- LLaMA 2 (7B, 13B, 70B)
- LLaMA 3 (8B, 70B)

**Fortalezas:**
- Open-source
- Multilingüe
- Buena relación calidad/precio

#### Amazon Titan

**Modelos:**
- Titan Text (Express, Lite, Premier)
- Titan Embeddings
- Titan Image Generator

**Fortalezas:**
- Integración nativa con AWS
- Optimizado para casos empresariales
- Soporte completo de AWS

### Conceptos Clave

#### Tokens

Unidad básica de procesamiento en modelos de lenguaje.

**Aproximaciones:**
- 1 token ≈ 4 caracteres en inglés
- 1 token ≈ 3/4 de una palabra
- 100 tokens ≈ 75 palabras

**Costos:**
- Se cobra por tokens de entrada (input)
- Se cobra por tokens de salida (output)
- Tokens de salida suelen ser más costosos

#### Hiperparámetros

Configuraciones que controlan el comportamiento del modelo:

1. **Temperature (Temperatura)**
   - Rango: 0.0 - 1.0
   - Baja (0.0-0.3): Respuestas determinísticas y conservadoras
   - Media (0.4-0.7): Balance entre creatividad y coherencia
   - Alta (0.8-1.0): Respuestas creativas e impredecibles

2. **Top P (Nucleus Sampling)**
   - Rango: 0.0 - 1.0
   - Controla la diversidad de tokens considerados
   - Valor típico: 0.9

3. **Top K**
   - Número de tokens candidatos a considerar
   - Valor típico: 40-50

4. **Max Tokens**
   - Longitud máxima de la respuesta
   - Importante para control de costos

#### Prompts

Instrucciones que se dan al modelo para generar respuestas.

**Componentes de un buen prompt:**
- **Contexto**: Información de fondo
- **Instrucción**: Qué debe hacer el modelo
- **Formato**: Cómo debe estructurar la respuesta
- **Ejemplos**: Few-shot learning (opcional)

**Ejemplo:**
```
Contexto: Eres un asistente experto en AWS.
Instrucción: Explica qué es Amazon Bedrock en 3 párrafos.
Formato: Usa lenguaje técnico pero accesible.
```

#### System Prompts

Instrucciones que definen el comportamiento general del modelo.

**Uso:**
- Definir personalidad del asistente
- Establecer reglas de comportamiento
- Configurar formato de respuestas
- Limitar temas de conversación

## 1.5 Casos de Uso Comunes

### 1. Chatbots Empresariales
- Atención al cliente 24/7
- Respuestas basadas en documentación interna
- Reducción de carga en equipos de soporte

### 2. Análisis de Documentos
- Extracción de información
- Resumen de contratos
- Análisis de sentimiento

### 3. Generación de Contenido
- Creación de artículos
- Generación de descripciones de productos
- Marketing y publicidad

### 4. Asistentes de Código
- Generación de código
- Debugging
- Documentación automática

### 5. Búsqueda Semántica
- Búsqueda en bases de conocimiento
- Recomendaciones personalizadas
- Q&A sobre documentación

## 1.6 Consideraciones Importantes

### Seguridad y Privacidad

- **Datos privados**: AWS no usa tus datos para entrenar modelos
- **Encriptación**: Datos en tránsito y en reposo
- **Control de acceso**: IAM para gestión granular
- **Cumplimiento**: Certificaciones de seguridad

### Costos

**Factores que afectan el costo:**
- Modelo seleccionado
- Tokens de entrada y salida
- Volumen de solicitudes
- Capacidad reservada vs on-demand

**Optimización:**
- Elegir el modelo adecuado para cada tarea
- Limitar tokens de salida
- Usar caché cuando sea posible
- Considerar capacidad reservada para alto volumen

### Limitaciones

- **Alucinaciones**: Pueden generar información incorrecta
- **Conocimiento limitado**: Hasta fecha de entrenamiento
- **Sesgo**: Pueden reflejar sesgos de datos de entrenamiento
- **Contexto limitado**: Ventana de contexto finita

## Preguntas de Repaso

1. **¿Cuál es la principal diferencia entre un modelo pre-entrenado y uno personalizado?**
   - a) El costo
   - b) El modelo pre-entrenado tiene conocimiento general, el personalizado está especializado
   - c) La velocidad de respuesta
   - d) El idioma soportado

2. **¿Qué servicio de AWS es el principal para IA Generativa?**
   - a) SageMaker
   - b) Lambda
   - c) Bedrock
   - d) EC2

3. **¿Qué es un token en el contexto de modelos de lenguaje?**
   - a) Una clave de API
   - b) La unidad básica de procesamiento de texto
   - c) Un tipo de modelo
   - d) Un hiperparámetro

4. **¿Qué hiperparámetro controla la creatividad del modelo?**
   - a) Max Tokens
   - b) Top K
   - c) Temperature
   - d) Top P

5. **¿Cuál es una ventaja clave de Amazon Bedrock?**
   - a) Es gratuito
   - b) Solo tiene un modelo disponible
   - c) Requiere gestionar infraestructura
   - d) Proporciona acceso a múltiples modelos sin gestionar infraestructura

## Respuestas

1. **b** - El modelo pre-entrenado tiene conocimiento general, el personalizado está especializado
2. **c** - Bedrock
3. **b** - La unidad básica de procesamiento de texto
4. **c** - Temperature
5. **d** - Proporciona acceso a múltiples modelos sin gestionar infraestructura

---

**Próximo Capítulo**: [Arquitectura RAG (Retrieval-Augmented Generation)](file:///C:/Users/doeku/Web/AI/02_arquitectura_rag.md)
