# SnackCheck -- ¿Es saludable este producto?

SnackCheck es un workflow de **n8n** que recibe el código de barras de
un producto alimentario, consulta sus datos nutricionales en **Open Food
Facts** y devuelve una valoración breve y fácil de entender sobre si el
producto es **saludable, moderado o no saludable**.

El objetivo del proyecto es combinar validación de datos, consumo de una
API externa, lógica determinista e inteligencia artificial para
transformar información nutricional técnica en una respuesta útil y
comprensible para el usuario.

## Funcionamiento general

El workflow sigue este proceso:

1.  Recibe un código de barras mediante un Webhook.
2.  Comprueba que el código existe y que tiene un formato numérico
    válido.
3.  Consulta el producto en Open Food Facts.
4.  Comprueba que el producto existe y que contiene los datos
    nutricionales necesarios.
5.  Evalúa azúcar, sal y grasa por cada 100 g.
6.  Combina esta evaluación con el Nutri-Score del producto.
7.  Selecciona como veredicto final la clasificación más restrictiva.
8.  Utiliza un modelo de IA de Groq para explicar el resultado en un
    lenguaje breve y amigable.
9.  Devuelve la respuesta al usuario mediante Respond to Webhook.

## Arquitectura del workflow

Flujo principal:

`Webhook → Validación → Open Food Facts → Validación de datos → Clasificación nutricional → Decisión final → Groq LLM → Formateo → Response`

El workflow también incluye rutas alternativas para gestionar de forma
controlada:

-   Peticiones sin código de barras.
-   Códigos de barras no numéricos.
-   Productos no encontrados.
-   Productos sin suficientes datos nutricionales.

De esta forma, una entrada incorrecta o incompleta genera una respuesta
comprensible en lugar de provocar un fallo del workflow.

## Fuente de datos

Los datos nutricionales se obtienen de la API pública de **Open Food
Facts**.

Endpoint utilizado:

`GET https://world.openfoodfacts.org/api/v2/product/{barcode}.json`

Principales campos utilizados:

-   `product_name`
-   `brands`
-   `nutriscore_grade`
-   `nutriments.sugars_100g`
-   `nutriments.salt_100g`
-   `nutriments.fat_100g`
-   `nutriments['energy-kcal_100g']`

## Lógica de clasificación

### 1. Evaluación de nutrientes

Se analizan tres nutrientes por cada 100 g:

  Nutriente     Umbral alto
  ----------- -------------
  Azúcar          \> 22.5 g
  Sal              \> 1.5 g
  Grasa           \> 17.5 g

A partir del número de nutrientes clasificados como altos:

-   **0 nutrientes altos → Saludable**
-   **1 nutriente alto → Moderado**
-   **2 o 3 nutrientes altos → No saludable**

La cantidad de nutrientes altos también se utiliza como **puntuación de
preocupación**, con un valor entre 0 y 3.

### 2. Evaluación mediante Nutri-Score

El campo `nutriscore_grade` se transforma en una segunda clasificación:

-   **A o B → Saludable**
-   **C → Moderado**
-   **D o E → No saludable**

Si el Nutri-Score no está disponible, la clasificación se basa
únicamente en el análisis de nutrientes.

### 3. Veredicto final

Las dos evaluaciones se representan internamente mediante niveles:

-   **1 = Saludable**
-   **2 = Moderado**
-   **3 = No saludable**

El veredicto final utiliza el nivel más restrictivo de los dos.

Ejemplo:

`Semáforo = 1 + Nutri-Score = 3 → Veredicto final = 3 (No saludable)`

Esto evita que un producto pueda clasificarse como saludable únicamente
porque varios nutrientes individuales estén por debajo de los límites
establecidos cuando su Nutri-Score indica una valoración peor.

Si no existen suficientes datos nutricionales para realizar la
evaluación, el producto no se clasifica.

## Uso de Inteligencia Artificial

La IA **no decide** si un producto es saludable.

La clasificación se realiza previamente mediante reglas deterministas
dentro del workflow. El modelo de Groq recibe el resultado ya calculado
y se utiliza para convertirlo en una explicación natural, breve y
amigable para el usuario.

El tono se adapta al resultado:

-   🟢 **Saludable:** positivo y alentador.
-   🟡 **Moderado:** equilibrado y recomendando moderación.
-   🔴 **No saludable:** cauteloso y acompañado de una recomendación
    práctica.

De esta forma se separa la **lógica de decisión**, que es predecible y
reproducible, de la **generación del lenguaje**, donde la IA aporta una
experiencia más natural.

## Ejemplo de salida

``` text
🔴 Coca-Cola Original — No saludable

Aunque contiene poca grasa y sal, su valoración nutricional global es desfavorable y contiene 10,6 g de azúcar por cada 100 g. Es recomendable reservarla para un consumo ocasional y priorizar bebidas sin azúcar para el día a día.
```

## Manejo de errores

El workflow contempla diferentes situaciones antes de realizar la
clasificación:

  -----------------------------------------------------------------------
  Situación                           Comportamiento
  ----------------------------------- -----------------------------------
  Código de barras vacío              Explica cómo utilizar el servicio

  Código no numérico                  Devuelve un mensaje indicando el
                                      formato correcto

  Producto no encontrado              Informa de que el producto no
                                      existe en Open Food Facts

  Datos nutricionales insuficientes   No genera una clasificación y lo
                                      comunica al usuario

  Producto válido                     Continúa con la clasificación y
                                      generación del veredicto
  -----------------------------------------------------------------------

## Tecnologías y nodos utilizados

-   n8n
-   Webhook
-   Respond to Webhook
-   HTTP Request
-   IF
-   Set / Edit Fields
-   Open Food Facts API
-   Groq Chat Model
-   Basic LLM Chain

## Pruebas

El workflow debe comprobarse con diferentes escenarios:

1.  Producto no saludable.
2.  Producto con Nutri-Score A o B.
3.  Producto en el que el Nutri-Score empeora la clasificación obtenida
    mediante el semáforo.
4.  Producto sin suficientes datos nutricionales.
5.  Producto inexistente en Open Food Facts.
6.  Código de barras no numérico.
7.  Petición sin código de barras.

Productos utilizados como referencia durante las pruebas:

-   **Nutella:** `3017620422003`
-   **Coca-Cola:** `5449000000996`

## Estructura de la entrega

El proyecto incluye:

-   Workflow de n8n exportado en formato JSON.
-   Diagrama del flujo realizado en Excalidraw.
-   README del proyecto.
-   Registro de pruebas.
-   CHANGELOG.

## Versión

**1.0.0** -- Primera versión funcional de SnackCheck.
