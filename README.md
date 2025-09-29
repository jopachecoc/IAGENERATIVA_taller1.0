# Fase 1: Selección y Justificación del Modelo de IA

## Selección y Justificación del Modelo de IA

En esta fase, los estudiantes deberán seleccionar y justificar un modelo de IA generativa para resolver el problema de **EcoMarket**. No hay una única respuesta correcta; lo importante es la justificación.

### Respuesta

Teniendo en cuenta las siguientes características de la empresa:

- Tamaño de empresa: **pequeño**  
- **Experimentando crecimiento exponencial**
- Recibe miles de consultas diarias (chat, correo y redes sociales)
- El **80%** de las consultas son repetitivas (devoluciones, estado del pedido)

### Modelo Seleccionado

Se propone una **solución híbrida** que combina:

- Un **LLM pequeño con fine-tuning**, que abarca el **80%** de los registros (consultas repetitivas).
- Un **LLM grande**, para el **20% restante** (consultas complejas).
- **Intervención humana** para casos sensibles o de alta complejidad.

Este enfoque busca **reducir considerablemente el tiempo promedio de respuesta**, actualmente en **24 horas**.

#### Justificación

- El **LLM pequeño** es más económico y, al estar **fine-tuned**, ofrece respuestas **muy precisas** en consultas repetitivas sin necesidad de la complejidad de un LLM grande.
- El **LLM grande (GPT-4)** se utilizará exclusivamente para el **20% de las consultas más complejas**, donde se necesita un mayor nivel de razonamiento.
- La combinación permite escalar el sistema sin saturar el modelo grande, ideal para la **fase de expansión** de la empresa.

---

## Proveedor Recomendado

### Modelo Pequeño

Se recomienda **GPT-3.5 Turbo con fine-tuning**, debido a:

- Buen balance entre **costo y calidad**
- Ideal para entrenar sobre **mensajes de soporte repetitivos**

### Modelo Grande

Se recomienda **GPT-4**, por su capacidad para:

- Resolver consultas con **razonamiento complejo**
- Responder adecuadamente en casos de:
  - Consultas abiertas
  - Quejas y reclamos
  - Problemas técnicos

> **Nota:** También se pueden considerar otras opciones como **Claude**, **DeepSeek**, u otros modelos, según:
> - La coherencia de las respuestas otorgadas
> - El **costo por token**



# Arquitectura Propuesta

## Capa de Decisión

La **Capa de Decisión** es el punto de entrada de todas las consultas, cuya función principal es **clasificarlas en tres categorías**:

- **Repetitivas**
- **Complejas**
- **Sensibles**

### Implementación de la Capa de Decisión

Esta capa será implementada mediante una combinación de enfoques heurísticos y modelos de lenguaje. El flujo propuesto es el siguiente:

1. **Reglas Heurísticas**
   - Se aplicarán reglas robustas basadas en:
     - Palabras clave
     - Expresiones regulares
     - Diccionarios de términos críticos
   - Estas reglas actuarán como **primer filtro de clasificación**, permitiendo identificar rápidamente las consultas más comunes y mitigar el uso innecesario de modelos más costosos, como GPT-3.5.

2. **Clasificación con GPT-3.5 (Zero-Shot Classification)**
   - Si la consulta no puede ser clasificada mediante reglas heurísticas, se utilizará **GPT-3.5** en modo **zero-shot classification**.
   - Este enfoque permite aprovechar el conocimiento del modelo sin necesidad de entrenar un clasificador específico.
   - Es una solución **rápida, económica y escalable**, ideal para una implementación ágil que evite demoras y preserve la reputación de la empresa.
   - Posteriormente, una vez que se disponga de un conjunto de datos clasificados, se podrá entrenar un **modelo ligero como BERT** para reducir aún más los costos en etapas posteriores.

3. **Clasificación con LLM Grande (GPT-4)**
   - En caso de que GPT-3.5 no logre clasificar con una probabilidad de confianza aceptable, se utilizará **GPT-4** aplicando técnicas de **one-shot classification**.
   - Esta solución garantiza mayor precisión en casos donde la complejidad de la consulta lo amerite.

---

Una vez determinada la categoría de la consulta, el sistema continúa hacia la **capa de respuesta**, donde se aplicará la solución correspondiente según el tipo de consulta identificada.


## Capa de Respuesta

### 2.1. Consultas Repetitivas → Respuesta mediante Fine-Tuning + Datos Internos

Para las consultas clasificadas como **repetitivas**, no se requiere un análisis cognitivo complejo, sino más bien **precisión** y **acceso directo a la información interna**. Por esta razón, se propone la utilización de un **modelo pequeño fine-tuned** con datos específicos de la empresa.

#### Ventajas de este enfoque:

- **Reducción significativa de costos**, ya que el uso de un LLM pequeño es considerablemente más económico que uno de gran escala.
- **Precisión especializada**, ideal para este tipo de consultas de carácter repetitivo.
- **Escalabilidad rápida y eficiente**, adaptándose fácilmente al crecimiento en el volumen de solicitudes.

#### Capacidades del modelo fine-tuned:

- Adopta el **tono de marca** en sus respuestas.
- Cubre tanto respuestas **estáticas** (políticas de la empresa, características de productos) como **dinámicas** (estado del pedido, nivel de stock, número de guía).
- Minimiza los errores al ofrecer únicamente respuestas previamente entrenadas.
- Permite **integración directa con fuentes de información interna**, tales como:
  - ERP
  - API de la transportadora
  - Estado de envíos
  - Bases de datos internas

#### Impacto esperado:

- Esta solución cubrirá aproximadamente el **80% de las consultas**, asegurando:
  - Alta consistencia
  - Cumplimiento de las políticas de comunicación de la empresa
  - Optimización de costos al evitar el uso de modelos de lenguaje de gran escala innecesariamente

> **Nota técnica:** La integración con las bases de datos internas es **sencilla y de rápida implementación**; bastan unas pocas líneas de código para completar la conexión y habilitar el sistema de respuesta automática.

### 2.2. Consultas Complejas → Respuesta mediante LLM Grande (GPT-4)

Las consultas clasificadas como **complejas** requieren un tratamiento avanzado, ya que suelen implicar:

- **Mayor nivel de razonamiento**
- Acceso y análisis de **múltiples fuentes de información**
- **Toma de decisiones no triviales**, como diagnósticos o procesos de reembolso

#### Modelo propuesto

Se recomienda el uso de **GPT-4** debido a sus capacidades avanzadas en:

- Razonamiento lógico y contextual
- Coherencia y fluidez en la redacción
- **Empatía** en la formulación de respuestas, ideal para situaciones delicadas que requieren mantener la satisfacción del cliente

#### Enfoque de implementación

Para este tipo de consultas, lo ideal es implementar una arquitectura basada en **RAG (Retrieval-Augmented Generation)** que combine:

1. **Contexto de la consulta del usuario**
2. **Información interna de EcoMarket**, alojada en bases de datos vectoriales
3. Aplicación de **GPT-4** como generador final de la respuesta

Este enfoque garantiza una **respuesta oportuna, precisa y adaptada al contexto del negocio**, facilitando además una **integración eficiente** con los sistemas existentes.


### 2.3. Consultas Sensibles → Escalado a Humano

En caso de que una consulta sea clasificada como **sensible**, se aplicará un **último filtro** en el cual la atención será **derivada directamente a un asesor humano**.

El objetivo es asegurar que este tipo de interacciones sean gestionadas con el **criterio, sensibilidad y experiencia** humana necesaria, manteniendo la calidad de atención que la empresa ya ofrece actualmente.

---

## Conclusión

La arquitectura planteada representa la solución más adecuada hasta el momento para **EcoMarket**, ya que aborda múltiples aspectos críticos como:

- **Costo**
- **Escalabilidad**
- **Integración**
- **Calidad de las respuestas**

Se trata de una arquitectura **completamente escalable**, diseñada para soportar un crecimiento exponencial en el número de consultas, gracias a su enfoque **híbrido y modular**. Esta estructura permite aumentar la capacidad de atención de forma rápida, sin generar bloqueos ni cuellos de botella, y asegurando **tiempos de respuesta eficientes**.

### Consideraciones clave:

- La **integración con los sistemas internos** de la empresa es técnicamente sencilla, siempre que se cuente con el conocimiento adecuado.
- Se recomienda iniciar con un **MVP (Producto Mínimo Viable)** que utilice **zero-shot classification**, con el fin de acelerar la implementación y puesta en marcha del sistema.
- La fase de respuesta con **RAG (Retrieval-Augmented Generation)** debe ser desarrollada por un profesional con dominio en el área, aprovechando las herramientas de integración que ofrecen los LLM como GPT, lo que reducirá significativamente los tiempos muertos del sistema.
- Aunque se ha seleccionado un proveedor (GPT) por su **calidad de respuesta** y **facilidad de integración**, se deja abierta la posibilidad de utilizar otros modelos (como **Claude** u otros) si se requiere reducir el costo por token y se puede tolerar una menor precisión en las respuestas.

---

# Fase 2: Evaluación de Fortalezas, Limitaciones y Riesgos Éticos

## Fortalezas

- **Respuestas inmediatas** para la mayoría de consultas: se reduce el tiempo promedio de respuesta de 24 horas a minutos, aumentando la satisfacción del cliente.
- **Atención 24/7**, fundamental para un ecommerce en crecimiento, sin depender de horarios laborales.
- **Consistencia en las respuestas**, evitando errores humanos frecuentes.
- **Mejora de la experiencia del cliente**, incentivando la recompra y recomendaciones.
- **Reducción de la tasa de abandono**, disminuyendo la probabilidad de cancelaciones o cambios de proveedor por parte del cliente.
- **Incremento en la retención del cliente**, gracias a respuestas rápidas y oportunas que generan un vínculo de confianza con la marca.
- **Alta escalabilidad**, permitiendo atender múltiples consultas en paralelo sin necesidad de ampliar el equipo de atención humana.
- **Protección de la reputación de la empresa**, evitando acumulación de consultas sin respuesta y pérdida de clientes.
- **Uniformidad en las respuestas**, independientemente del agente, lo que fortalece la identidad de marca y la percepción de profesionalismo.
- **Facilita la escalabilidad del servicio**, al no depender de procesos de capacitación o rotación de personal.
- **Mayor eficiencia operativa**, al reducir tiempos de aclaración posteriores gracias a respuestas claras y consistentes desde el inicio.
- **Empoderamiento del equipo humano**, al enfocarse exclusivamente en consultas complejas y sensibles, donde pueden aportar mayor valor con empatía y criterio.
- **Integración con sistemas internos**, permitiendo generar respuestas precisas en tiempo real (estado de pedidos, stock, guías, etc.).
- **Reducción de costos operativos**, ya que mantener un agente humano es más costoso que operar con la solución tecnológica propuesta.


## Limitaciones

- **Comprensión emocional limitada**: Aunque los grandes modelos LLM pueden generar respuestas con cierto grado de empatía textual, aún no son capaces de captar completamente contextos delicados como:
  - Reclamos intensos
  - Situaciones de emergencia
  - Casos de alta sensibilidad emocional  
  Esto puede generar respuestas frías o inadecuadas, provocando frustración en el cliente y potenciales escaladas a redes sociales o instancias legales.

- **Dependencia de datos internos**: La calidad de las respuestas generadas depende directamente de la confiabilidad, actualización y correcta categorización de los datos internos de la compañía.  
  Si la información está desactualizada o mal estructurada, el modelo ofrecerá respuestas incorrectas.

- **Errores en la clasificación de consultas**: Una clasificación errónea durante la fase de decisión puede desencadenar:
  - Costos innecesarios (por uso de modelos más avanzados)
  - Experiencias negativas para el usuario (si se le da una respuesta inadecuada)

- **Requerimientos técnicos elevados**: La implementación de componentes como:
  - RAG (Retrieval-Augmented Generation)
  - Fine-tuning
  - LLMs grandes
  - Conexiones con bases de datos y APIs externas  
  requiere una **inversión considerable en talento especializado y tecnología**.

- **Limitaciones en consultas multilingües**: Aunque el modelo puede funcionar correctamente en inglés o español neutro, pueden presentarse dificultades con:
  - Modismos
  - Regionalismos
  - Variaciones dialectales locales  
  Esto es especialmente relevante si el modelo no fue entrenado considerando dichas variantes, lo cual podría afectar la calidad de la respuesta en regiones específicas.


## ⚖️ Riesgos Éticos

### Calidad de los datos  
Es fundamental garantizar la **calidad de los datos** internos de la compañía. En muchos casos, estos deben ser corregidos manualmente de forma individual, lo que podría **retrasar el proyecto** si no se aborda de manera preventiva. Actualmente, EcoMarket presenta un riesgo significativo en esta área debido a la deficiencia en la estructura y actualización de su información.

### Alucinaciones del modelo  
Los modelos de lenguaje pueden generar **respuestas incorrectas o inventadas** (conocidas como alucinaciones) cuando la información no está clara o no está disponible.  
Por ejemplo: _"Su pedido ya ha llegado"_, cuando en realidad no es así.  
Esto puede provocar una **pérdida de confianza**, generar **quejas**, y en casos extremos, **reclamos legales** por parte de los clientes.

### Sesgos en las respuestas  
Si el conjunto de datos de entrenamiento contiene sesgos (por género, idioma, ubicación geográfica, etc.), el modelo tenderá a **replicarlos**.  
Para mitigar este riesgo se deben aplicar estrategias como:

- Pruebas de equidad (fairness testing)
- Monitoreo continuo del comportamiento del modelo
- Ajustes a los prompts o datasets
- Evaluaciones periódicas con foco en diversidad e inclusión

### Privacidad de los datos  
El modelo puede recibir y procesar **información sensible** de los usuarios, como:

- Direcciones
- Correos electrónicos
- Información personal o confidencial  

Esto representa un **riesgo de fuga de datos** si no se cuenta con una adecuada política de gobernanza de la información. Para mitigar este riesgo se recomienda:

- Anonimizar los datos sensibles
- Utilizar entornos seguros (por ejemplo, instancias privadas)
- Aplicar políticas claras de privacidad y retención de datos  
- Cumplir con normativas legales (como GDPR, Habeas Data, etc.)

> ⚠️ **No implementar estas medidas puede conllevar sanciones legales y pérdida de confianza por parte de los clientes.**

### Impacto en agentes humanos  
Existe la preocupación de que los agentes humanos puedan ser **reemplazados por la IA**. Sin embargo, en un entorno empresarial en constante evolución:

- Se espera que con el crecimiento de la empresa, se mantengan ciertos roles humanos clave.
- Otros agentes podrán ser reubicados para labores de **soporte al sistema de IA**, incluyendo:
  - Monitoreo
  - Control de calidad
  - Evaluación continua del desempeño del modelo
- Nuevas funciones y necesidades surgirán, permitiendo que los agentes humanos **evolucionen junto con la tecnología**.

---

## ⚖️ Otros Riesgos

### Dependencia tecnológica  
Quedar atado a un solo proveedor (OpenAI, Anthropic, DeepSeek) conlleva riesgos como:

- Cambios en precios
- Disponibilidad del servicio  

La idea es contar con un diseño modular que permita migrar rápidamente entre modelos utilizando APIs estandarizadas y bases de datos vectoriales propias (BDV), para minimizar el impacto ante modificaciones en costos o calidad.

### Escalabilidad y latencia  
Es fundamental balancear la carga de trabajo y manejar caché en respuestas repetitivas para:

- Mantener la satisfacción del cliente
- Garantizar un crecimiento constante y estable  

### Cumplimiento normativo y legal  
Diversas entidades gubernamentales pueden exigir:

- Transparencia
- Explicabilidad
- Trazabilidad del uso de la IA  

Para mitigar estos riesgos se deben implementar:

- Control de logs de interacción
- Políticas claras de uso de IA
- Transparencia frente al cliente

### Resistencia interna de la compañía  
La implementación de IA puede generar resistencia entre los empleados debido a la percepción de pérdida de empleos. Para minimizar esta resistencia es clave:

- Gestionar adecuadamente el cambio
- Mostrar las ventajas y nuevas oportunidades que ofrece la IA
- Reasignar funciones para que los empleados se enfoquen en el control y supervisión de la solución
- Incentivar la mejora en los tiempos de respuesta para consultas complejas  

Esto genera mayor satisfacción de los usuarios y brinda seguridad y continuidad laboral a los colaboradores.






  

