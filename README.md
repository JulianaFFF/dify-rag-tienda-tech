# Agente de Soporte RAG con Control Estricto de Alucinaciones

## Descripción del Proyecto

Este repositorio contiene la implementación de un prototipo de asistente virtual para soporte interno, desarrollado sobre la plataforma Dify.ai utilizando una arquitectura RAG (Generación Aumentada por Recuperación). 

El objetivo principal del sistema es proporcionar respuestas precisas y automatizadas sobre políticas operativas y de servicio (en este caso, el manual de devoluciones y garantías de "Tienda Tech"), eliminando por completo el riesgo de alucinaciones mediante el uso de restricciones a nivel de prompt y una estrategia de búsqueda semántica.

---

## Arquitectura del Sistema

El asistente está construido bajo una estructura de Chatflow modular en Dify.ai, asegurando un procesamiento secuencial y auditable de cada consulta.

El flujo de procesamiento sigue las siguientes etapas:

1. **Entrada del Usuario (Nodo Inicio):** Captura la consulta realizada por el usuario a través de la interfaz de chat.
2. **Recuperación de Conocimiento (Knowledge Retrieval):** Consulta la base de datos vectorial cargada previamente para extraer los fragmentos de texto más relevantes según el significado de la pregunta.
3. **Procesamiento en el LLM (Large Language Model):** Inyecta el contexto recuperado junto con la consulta del usuario en el System Prompt preconfigurado.
4. **Respuesta:** Devuelve la respuesta generada al usuario o activa el mecanismo de seguridad (guardrail) si la información no se encuentra en el contexto provisto.

---

## Componentes Técnicos y Configuración

* **Plataforma de Orquestación:** Dify.ai (Cloud Chatflow).
* **Base de Conocimiento:** Archivo estructurado en formato Markdown (`reglas_y_politicas_servicio.md`).
* **Estrategia de Indexación y Recuperación:** 
  * **Método:** Búsqueda Vectorial / Embeddings (Alta Calidad). Se descartó la búsqueda por índice invertido tradicional para evitar pérdidas de contexto por falta de coincidencias exactas de palabras clave.
  * **Top K:** Configurado en 4 fragmentos para garantizar que el modelo reciba contexto suficiente sin saturar la ventana de atención del LLM.
* **Modelo de Lenguaje:** GPT-3.5-Turbo / GPT-4o-Mini (según el proveedor configurado).
* **Mecanismo de Guardrail:** Instrucción estricta dentro del System Prompt que obliga al modelo a responder con una frase estandarizada cuando la información solicitada no existe explícitamente en el documento de soporte.

---

## Demostración en Video

En el siguiente enlace se encuentra la grabación en video donde se explica la arquitectura del flujo en el canvas de Dify, la configuración del nodo de recuperación y la validación en tiempo real de las respuestas en scope y out-of-scope:

* **Enlace al video de demostración (Loom):** [Insertar enlace de Loom aquí]

---

## Casos de Prueba y Validación de Guardrails

El comportamiento del sistema fue validado utilizando dos categorías principales de consultas:

### 1. Consulta dentro del Alcance (In-Scope)
* **Pregunta:** "¿Cuál es el plazo máximo para solicitar una devolución?"
* **Comportamiento esperado:** El sistema recupera la sección correspondiente del documento y responde de forma directa indicando el plazo de 30 días calendario y las condiciones requeridas.
* **Resultado:** Exitoso.

### 2. Consulta fuera del Alcance (Out-of-Scope / Prueba Trampa)
* **Pregunta:** "¿Cuál es el beneficio de estacionamiento para empleados?"
* **Comportamiento esperado:** El sistema identifica que el tema no figura en la base de conocimiento y activa la respuesta de seguridad sin inventar o deducir información.
* **Resultado:** Exitoso. Respuesta emitida: *"Lo siento, no cuento con esa información en los manuales autorizados."*

---

## Estructura del Repositorio

* `README.md`: Documentación técnica completa del proyecto.
* `reglas_y_politicas_servicio.md`: Documento fuente en Markdown utilizado como base de conocimiento.
* `dify_rag_agent_workflow.yml`: Archivo DSL exportado desde Dify que contiene la configuración y topología completa del flujo para su reimportación.

---

## Pasos para Replicar o Importar este Flujo en Dify

1. Iniciar sesión en la plataforma de Dify.ai.
2. Crear una nueva aplicación seleccionando la opción "Importar desde archivo DSL".
3. Cargar el archivo `dify_rag_agent_workflow.yml` incluido en este repositorio.
4. Crear un nuevo Conjunto de Datos (Knowledge Base) e importar el archivo `reglas_y_politicas_servicio.md`.
5. Asegurarse de configurar el método de indexación en "Alta Calidad" (Búsqueda Vectorial) y enlazar este conjunto de datos al nodo de Recuperación de Conocimiento dentro del Chatflow.
6. Guardar, publicar y probar el asistente a través del panel de Vista Previa.
