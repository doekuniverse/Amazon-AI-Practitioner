# Capítulo 10: Preparación para Certificación AWS AI Practitioner

## 10.1 Información del Examen

### Detalles Generales

**Nombre**: AWS Certified AI Practitioner

**Código**: AIF-C01

**Duración**: 90 minutos

**Número de preguntas**: 65 preguntas

**Formato**: Opción múltiple y respuesta múltiple

**Puntuación**: 100-1000 puntos (mínimo para aprobar: 700)

**Costo**: $75 USD

**Validez**: 3 años

**Idiomas**: Inglés, Japonés, Coreano, Chino Simplificado

### Dominios del Examen

| Dominio | Porcentaje |
|---------|-----------|
| **Dominio 1**: Fundamentos de IA y ML | 20% |
| **Dominio 2**: Fundamentos de IA Generativa | 24% |
| **Dominio 3**: Aplicaciones de Modelos Fundacionales | 28% |
| **Dominio 4**: Lineamientos para IA Responsable | 14% |
| **Dominio 5**: Seguridad, Cumplimiento y Gobernanza | 14% |

## 10.2 Dominio 1: Fundamentos de IA y ML (20%)

### Conceptos Clave

**Machine Learning:**
- Aprendizaje supervisado vs no supervisado
- Clasificación vs regresión
- Entrenamiento, validación, testing
- Overfitting y underfitting

**Deep Learning:**
- Redes neuronales
- Capas ocultas
- Funciones de activación
- Backpropagation

**Servicios de AWS:**
- **SageMaker**: Plataforma completa de ML
- **Rekognition**: Análisis de imágenes/video
- **Comprehend**: Procesamiento de lenguaje natural
- **Translate**: Traducción automática
- **Polly**: Texto a voz
- **Transcribe**: Voz a texto

### Preguntas de Práctica

**1. ¿Cuál es la diferencia entre aprendizaje supervisado y no supervisado?**
- a) Supervisado usa etiquetas, no supervisado no
- b) Supervisado es más rápido
- c) No supervisado es más preciso
- d) No hay diferencia

**Respuesta**: a

**2. ¿Qué servicio usarías para análisis de sentimiento?**
- a) Rekognition
- b) Comprehend
- c) Polly
- d) Translate

**Respuesta**: b

## 10.3 Dominio 2: Fundamentos de IA Generativa (24%)

### Conceptos Clave

**Modelos Fundacionales:**
- GPT (Generative Pre-trained Transformer)
- Modelos autorregresivos
- Ventana de contexto
- Tokens

**Embeddings:**
- Representación vectorial de texto
- Similitud semántica
- Modelos de embeddings

**Prompts:**
- Prompt engineering
- System prompts
- Few-shot learning
- Chain of thought

**Hiperparámetros:**
- Temperature
- Top P
- Top K
- Max tokens

### Preguntas de Práctica

**1. ¿Qué es un token?**
- a) Una clave de API
- b) Unidad básica de procesamiento de texto
- c) Un tipo de modelo
- d) Un servicio de AWS

**Respuesta**: b

**2. ¿Qué hiperparámetro controla la creatividad?**
- a) Max tokens
- b) Top K
- c) Temperature
- d) Todos los anteriores

**Respuesta**: c

**3. ¿Qué es un embedding?**
- a) Un tipo de modelo
- b) Representación vectorial de texto
- c) Un servicio de AWS
- d) Un hiperparámetro

**Respuesta**: b

## 10.4 Dominio 3: Aplicaciones de Modelos Fundacionales (28%)

### Conceptos Clave

**Amazon Bedrock:**
- Modelos disponibles (Claude, LLaMA, Titan)
- Playground
- Knowledge Bases
- Agents
- Guardrails
- APIs (invoke_model, invoke_model_with_response_stream)

**RAG (Retrieval-Augmented Generation):**
- Arquitectura
- Bases de datos vectoriales
- Orquestador
- Ventajas vs fine-tuning

**Fine-tuning:**
- Cuándo usar
- Datos necesarios
- Proceso
- Diferencia con RAG

**Continued Pre-training:**
- Cuándo usar
- Requisitos
- Costo

**Agentes:**
- Orquestación
- Action Groups
- Herramientas
- Casos de uso

### Preguntas de Práctica

**1. ¿Cuál es la principal ventaja de RAG sobre fine-tuning?**
- a) Es más rápido en inferencia
- b) Permite actualizar información sin reentrenar
- c) Es más barato siempre
- d) Mejora automáticamente el modelo

**Respuesta**: b

**2. ¿Qué componente coordina las búsquedas en RAG?**
- a) Base de datos vectorial
- b) Modelo de embeddings
- c) Orquestador
- d) Modelo de lenguaje

**Respuesta**: c

**3. ¿Cuántos ejemplos típicamente se necesitan para fine-tuning?**
- a) 10-50
- b) 100-1000+
- c) 1,000,000+
- d) No se necesitan datos

**Respuesta**: b

**4. ¿Qué hace un agente de Bedrock?**
- a) Solo responde preguntas
- b) Ejecuta acciones y usa herramientas autónomamente
- c) Solo genera imágenes
- d) Solo traduce idiomas

**Respuesta**: b

**5. ¿Qué modelo de Bedrock es mejor para alto volumen y bajo costo?**
- a) Claude 3 Opus
- b) Claude 3.5 Sonnet
- c) Claude 3 Haiku
- d) Titan Image Generator

**Respuesta**: c

## 10.5 Dominio 4: Lineamientos para IA Responsable (14%)

### Conceptos Clave

**Principios:**
- Fairness (Equidad)
- Explainability (Explicabilidad)
- Privacy (Privacidad)
- Security (Seguridad)
- Controllability (Controlabilidad)
- Governance (Gobernanza)

**Problemas:**
- Alucinaciones
- Toxicidad
- Sesgo
- Ilegalidad

**Soluciones:**
- Guardrails
- SageMaker Clarify
- Prompt engineering
- Human-in-the-loop

**Métricas:**
- SHAP
- Feature importance
- Métricas de sesgo

### Preguntas de Práctica

**1. ¿Qué es una alucinación?**
- a) Un error de sintaxis
- b) Información plausible pero incorrecta
- c) Un problema de latencia
- d) Un tipo de sesgo

**Respuesta**: b

**2. ¿Qué servicio detecta sesgo en modelos?**
- a) CloudWatch
- b) SageMaker Clarify
- c) Lambda
- d) S3

**Respuesta**: b

**3. ¿Qué son los Guardrails en Bedrock?**
- a) Límites de costo
- b) Barreras de protección para controlar comportamiento
- c) Métricas de rendimiento
- d) Tipos de modelos

**Respuesta**: b

**4. ¿Qué puede hacer un Guardrail con PII?**
- a) Solo BLOCK
- b) BLOCK o ANONYMIZE
- c) Solo ANONYMIZE
- d) No puede detectar PII

**Respuesta**: b

## 10.6 Dominio 5: Seguridad, Cumplimiento y Gobernanza (14%)

### Conceptos Clave

**Modelo de Responsabilidad Compartida:**
- Responsabilidades de AWS
- Responsabilidades del cliente

**Seguridad en Capas:**
- Protección de datos (KMS, S3, Macie)
- Protección de red (VPC, Security Groups)
- Identidad y acceso (IAM)
- Protección de aplicación (Guardrails)
- Detección y respuesta (CloudTrail, CloudWatch)

**Servicios:**
- **KMS**: Gestión de claves de cifrado
- **Macie**: Descubrimiento de datos sensibles
- **CloudTrail**: Auditoría de acciones
- **CloudWatch**: Monitoreo y métricas
- **IAM**: Control de acceso
- **Audit Manager**: Auditorías de cumplimiento

**OWASP Top 10 para LLMs:**
- Prompt Injection
- Insecure Output Handling
- Training Data Poisoning
- Model Denial of Service
- Supply Chain Vulnerabilities
- Sensitive Information Disclosure
- Insecure Plugin Design
- Excessive Agency
- Overreliance
- Model Theft

### Preguntas de Práctica

**1. ¿Quién es responsable del cifrado de datos?**
- a) Solo AWS
- b) Solo el cliente
- c) Ambos (AWS provee herramientas, cliente configura)
- d) Ninguno

**Respuesta**: c

**2. ¿Qué servicio descubre datos sensibles en S3?**
- a) CloudWatch
- b) Amazon Macie
- c) IAM
- d) KMS

**Respuesta**: b

**3. ¿Qué es Prompt Injection?**
- a) Un tipo de cifrado
- b) Manipular el modelo con prompts maliciosos
- c) Una métrica de rendimiento
- d) Un servicio de AWS

**Respuesta**: b

**4. ¿Qué registra CloudTrail?**
- a) Solo errores
- b) Solo costos
- c) Todas las acciones de API
- d) Solo invocaciones de modelos

**Respuesta**: c

**5. ¿Para qué sirve AWS KMS?**
- a) Monitoreo de logs
- b) Gestión de claves de cifrado
- c) Control de acceso
- d) Detección de anomalías

**Respuesta**: b

## 10.7 Conceptos Clave para Memorizar

### Servicios de AWS

| Servicio | Función Principal |
|----------|-------------------|
| **Bedrock** | Modelos fundacionales como servicio |
| **SageMaker** | Plataforma completa de ML |
| **SageMaker Clarify** | Detectar sesgo y explicabilidad |
| **Rekognition** | Análisis de imágenes/video |
| **Comprehend** | NLP y análisis de sentimiento |
| **Translate** | Traducción automática |
| **Polly** | Texto a voz |
| **Transcribe** | Voz a texto |
| **Lex** | Chatbots conversacionales |
| **KMS** | Gestión de claves de cifrado |
| **Macie** | Descubrimiento de datos sensibles |
| **CloudTrail** | Auditoría de acciones |
| **CloudWatch** | Monitoreo y métricas |

### Modelos en Bedrock

| Modelo | Mejor Para |
|--------|------------|
| **Claude 3 Opus** | Máximo rendimiento, tareas complejas |
| **Claude 3.5 Sonnet** | Balance rendimiento/costo |
| **Claude 3 Haiku** | Alto volumen, bajo costo |
| **LLaMA 3** | Open-source, control total |
| **Titan Text** | Integración nativa AWS |
| **Titan Embeddings** | Vectorización para RAG |
| **Titan Image** | Generación de imágenes |

### Comparaciones Importantes

**RAG vs Fine-tuning vs Continued Pre-training:**

| Aspecto | RAG | Fine-tuning | Continued Pre-training |
|---------|-----|-------------|------------------------|
| Costo | Bajo | Medio | Alto |
| Tiempo | Minutos | Horas | Semanas |
| Datos | Documentos | Miles | Millones |
| Actualización | Inmediata | Reentrenar | Reentrenar |
| Especialización | Contexto | Tarea | Dominio |

**Hiperparámetros:**

| Parámetro | Función | Rango | Valor Típico |
|-----------|---------|-------|--------------|
| Temperature | Creatividad | 0.0-1.0 | 0.7 |
| Top P | Diversidad | 0.0-1.0 | 0.9 |
| Top K | Candidatos | 1-500 | 40-50 |
| Max Tokens | Longitud | 1-límite | Variable |

## 10.8 Estrategias para el Examen

### Antes del Examen

**1. Estudiar el contenido oficial:**
- Guía del examen
- Whitepapers de AWS
- Documentación de servicios

**2. Práctica hands-on:**
- Usar Bedrock Playground
- Crear Knowledge Bases
- Implementar agentes simples
- Probar diferentes modelos

**3. Repasar conceptos:**
- Crear flashcards
- Hacer mapas mentales
- Resolver preguntas de práctica

### Durante el Examen

**1. Gestión del tiempo:**
- 90 minutos / 65 preguntas ≈ 1.4 min/pregunta
- Marcar preguntas difíciles para revisar
- No quedarse atascado en una pregunta

**2. Estrategia de respuesta:**
- Leer toda la pregunta cuidadosamente
- Eliminar opciones obviamente incorrectas
- Buscar palabras clave
- Si no sabes, adivina (no hay penalización)

**3. Palabras clave:**
- "MEJOR" → Puede haber varias correctas, elegir la óptima
- "MÁS APROPIADO" → Contexto específico
- "MENOS" → Buscar la opción incorrecta o menos adecuada

### Después del Examen

**Resultado inmediato**: Pass/Fail

**Puntuación detallada**: Disponible en 5 días hábiles

**Certificado digital**: Disponible en Credly

## 10.9 Recursos de Estudio

### Oficiales de AWS

1. **Guía del Examen**
   - Dominios y porcentajes
   - Preguntas de ejemplo
   - Servicios en scope

2. **AWS Skill Builder**
   - Cursos gratuitos
   - Labs prácticos
   - Exámenes de práctica

3. **Whitepapers**
   - "Machine Learning Lens"
   - "Generative AI on AWS"
   - "Responsible AI"

4. **Documentación**
   - Amazon Bedrock
   - SageMaker
   - Servicios de IA/ML

### Práctica

1. **AWS Free Tier**
   - Bedrock (limitado)
   - SageMaker (limitado)
   - Otros servicios de IA

2. **Workshops**
   - Amazon Bedrock Workshop (GitHub)
   - SageMaker Examples

3. **Exámenes de Práctica**
   - AWS Skill Builder
   - Plataformas de terceros

## 10.10 Preguntas de Práctica Final

**1. ¿Qué modelo usarías para un chatbot de alto volumen con presupuesto limitado?**
- a) Claude 3 Opus
- b) Claude 3.5 Sonnet
- c) Claude 3 Haiku
- d) Titan Image Generator

**Respuesta**: c

**2. ¿Cuál es la mejor solución para reducir alucinaciones?**
- a) Aumentar temperature
- b) Usar RAG con documentos verificados
- c) Usar más tokens
- d) Cambiar de modelo

**Respuesta**: b

**3. ¿Qué servicio ayuda a detectar sesgo en datos de entrenamiento?**
- a) CloudWatch
- b) SageMaker Clarify
- c) Lambda
- d) S3

**Respuesta**: b

**4. ¿Cuál NO es un principio de IA Responsable?**
- a) Fairness
- b) Explainability
- c) Profitability
- d) Privacy

**Respuesta**: c

**5. ¿Qué componente en RAG convierte texto en vectores?**
- a) Orquestador
- b) Modelo de embeddings
- c) Base de datos vectorial
- d) Modelo de lenguaje

**Respuesta**: b

**6. ¿Para qué sirve un Action Group en Bedrock Agents?**
- a) Cifrar datos
- b) Definir acciones que el agente puede ejecutar
- c) Monitorear costos
- d) Gestionar usuarios

**Respuesta**: b

**7. ¿Qué temperatura usar para análisis que requiere precisión?**
- a) 0.9-1.0
- b) 0.6-0.8
- c) 0.1-0.3
- d) No importa

**Respuesta**: c

**8. ¿Qué es model drift?**
- a) Un error de sintaxis
- b) Degradación del modelo con el tiempo
- c) Un tipo de cifrado
- d) Una métrica de costo

**Respuesta**: b

**9. ¿Quién es responsable de la seguridad física de los centros de datos?**
- a) Cliente
- b) AWS
- c) Ambos
- d) Ninguno

**Respuesta**: b

**10. ¿Qué métrica mide la latencia inicial de respuesta?**
- a) Total time
- b) Time to first token
- c) InvocationCount
- d) OutputTokenCount

**Respuesta**: b

## 10.11 Checklist de Preparación

### Conocimientos Técnicos

- [ ] Entiendo qué es IA Generativa
- [ ] Conozco los modelos disponibles en Bedrock
- [ ] Sé cuándo usar RAG vs fine-tuning
- [ ] Entiendo la arquitectura RAG
- [ ] Conozco los hiperparámetros y su efecto
- [ ] Sé qué son los embeddings
- [ ] Entiendo qué son los agentes
- [ ] Conozco los Guardrails de Bedrock
- [ ] Sé qué es SageMaker Clarify
- [ ] Entiendo el modelo de responsabilidad compartida
- [ ] Conozco los servicios de seguridad (KMS, Macie, CloudTrail)
- [ ] Sé qué es OWASP Top 10 para LLMs

### Experiencia Práctica

- [ ] He usado Bedrock Playground
- [ ] He creado una Knowledge Base
- [ ] He comparado modelos
- [ ] He ajustado hiperparámetros
- [ ] He implementado RAG básico
- [ ] He usado Guardrails
- [ ] He revisado métricas en CloudWatch
- [ ] He configurado IAM para Bedrock

### Preparación para el Examen

- [ ] He leído la guía oficial del examen
- [ ] He completado cursos en Skill Builder
- [ ] He hecho exámenes de práctica
- [ ] Conozco el formato del examen
- [ ] Tengo estrategia de tiempo
- [ ] He repasado conceptos clave
- [ ] Estoy familiarizado con la interfaz del examen

## 10.12 Consejos Finales

**1. No memorices, entiende:**
- Comprende los conceptos
- Entiende cuándo usar cada servicio
- Conoce las diferencias entre opciones

**2. Piensa en casos de uso:**
- ¿Qué usarías para X escenario?
- ¿Cuál es la solución más apropiada?
- ¿Qué consideraciones de costo/rendimiento?

**3. Practica con AWS:**
- Hands-on es crucial
- Experimenta con diferentes configuraciones
- Entiende cómo funcionan los servicios

**4. Gestiona tu tiempo:**
- No te quedes atascado
- Marca y revisa después
- Confía en tu preparación

**5. Lee cuidadosamente:**
- Palabras clave importan
- Elimina opciones incorrectas
- Elige la MEJOR respuesta

---

## ¡Éxito en tu Certificación!

Has completado este libro sobre IA Generativa en AWS. Con el conocimiento adquirido y práctica constante, estarás bien preparado para el examen AWS AI Practitioner.

**Recuerda:**
- La certificación valida tu conocimiento
- La experiencia práctica es invaluable
- Sigue aprendiendo y experimentando
- La IA Generativa está en constante evolución

**¡Mucho éxito!** 🚀

---

**Capítulo Anterior**: [Casos de Uso y Mejores Prácticas](file:///C:/Users/doeku/Web/AI/09_casos_uso_mejores_practicas.md)

**Inicio del Libro**: [Introducción a IA Generativa](file:///C:/Users/doeku/Web/AI/01_introduccion_ia_generativa.md)
