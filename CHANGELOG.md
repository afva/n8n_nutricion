# CHANGELOG -- SnackCheck

Todos los cambios relevantes realizados durante el desarrollo de
**SnackCheck** se documentan en este archivo.

## \[1.0.0\] -- 2026-10-04

### Diseño inicial

-   Revisión de los requisitos y definición del proceso completo del
    workflow.
-   Diseño de las etapas principales: entrada, validación, consulta de
    datos, comprobación de información nutricional, clasificación,
    generación de la explicación y respuesta final.

### Webhook y gestión de la entrada

-   Creación del nodo **Webhook** como punto de entrada mediante método
    `POST`.
-   Comprobación del comportamiento de las respuestas HTTP `200`, `400`
    y `500`.
-   Implementación de una comprobación para peticiones vacías.
-   Implementación de una comprobación para códigos de barras con valor
    `null`.
-   Validación de la longitud del código de barras.
-   Validación para comprobar que el código de barras contiene
    únicamente caracteres numéricos.
-   Creación de respuestas específicas para entradas no válidas.

### Integración con Open Food Facts

-   Configuración de la conexión con la API de **Open Food Facts** desde
    n8n.
-   Realización de pruebas de conexión para comprobar respuestas
    correctas y posibles errores.
-   Implementación del tratamiento de productos no encontrados.
-   Extracción de los campos necesarios para continuar el análisis
    nutricional.

### Validación de datos nutricionales

-   Comprobación de que el producto dispone de información suficiente
    antes de realizar la clasificación.
-   Creación de una ruta específica para productos con datos
    nutricionales insuficientes.
-   Configuración de una respuesta informativa cuando no es posible
    realizar la valoración.

### Lógica de clasificación

-   Aplicación de las reglas definidas para evaluar azúcar, sal y grasa.
-   Cálculo de los valores necesarios para construir el semáforo
    nutricional.
-   Cálculo del número de nutrientes considerados altos.
-   Clasificación del resultado del semáforo como **saludable, moderado
    o no saludable**.
-   Conversión del **Nutri-Score** a los mismos niveles de
    clasificación.
-   Comparación de la clasificación obtenida mediante el semáforo y la
    obtenida mediante Nutri-Score.
-   Selección de la clasificación más restrictiva como veredicto final.
-   Uso de la clasificación basada en nutrientes cuando el Nutri-Score
    no está disponible.

### Integración de inteligencia artificial

-   Incorporación de **Groq LLM** después de calcular el veredicto
    mediante reglas deterministas.
-   Envío al modelo de los datos nutricionales relevantes y de la
    clasificación final.
-   Uso de la IA para transformar el resultado técnico en una
    explicación breve, clara y fácil de entender.
-   Adaptación del tono de la respuesta según el resultado: saludable,
    moderado o no saludable.
-   Mantenimiento de la lógica de decisión fuera del LLM para evitar que
    la IA modifique el veredicto calculado por el workflow.

### Pruebas y correcciones

-   Realización de pruebas progresivas durante la construcción de cada
    etapa del workflow.
-   Pruebas de entradas vacías, valores nulos, formatos incorrectos y
    códigos no numéricos.
-   Pruebas de conexión y respuesta de Open Food Facts.
-   Pruebas de disponibilidad de los campos nutricionales necesarios.
-   Pruebas de la lógica del semáforo y de la clasificación nutricional.
-   Pruebas de integración con Groq y de generación de la respuesta
    final.
-   Corrección de errores encontrados durante el desarrollo.
-   Realización de pruebas completas del flujo de principio a fin hasta
    obtener ejecuciones correctas y estables.
