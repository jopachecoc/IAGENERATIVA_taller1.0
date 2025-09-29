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

