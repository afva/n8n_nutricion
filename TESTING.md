# Registro de pruebas -- SnackCheck

## 1. Objetivo

Este documento recoge las pruebas realizadas durante el desarrollo del
workflow **SnackCheck** en n8n.

El historial de ejecuciones muestra un proceso iterativo de
construcción, prueba y corrección del flujo, con ejecuciones tanto
exitosas como fallidas durante el desarrollo. Las ejecuciones con error
forman parte del proceso de depuración y permitieron corregir problemas
de sintaxis, referencias entre nodos, estructura JSON y lógica del
workflow.

## 2. Evidencia de ejecución en n8n

El historial exportado de n8n contiene numerosas ejecuciones del
workflow **"proyecto nº 1"** realizadas el 4 de octubre de 2026.

Entre las últimas ejecuciones registradas se encuentran:

    Ejecución Hora         Duración Resultado
  ----------- ---------- ---------- -----------
         #439 23:50:46       939 ms PASS
         #438 23:49:29       828 ms PASS
         #437 23:49:24        23 ms PASS
         #436 23:48:46      1.121 s PASS
         #435 23:46:03      1.772 s PASS
         #434 23:44:56       1.58 s PASS
         #433 23:39:54      2.905 s PASS
         #432 23:37:23      3.192 s PASS
         #431 23:37:18        20 ms PASS
         #430 23:36:31      2.399 s PASS
         #429 23:34:40      3.012 s PASS

El historial también conserva ejecuciones fallidas anteriores. Estas se
utilizaron durante la depuración y no se consideran pruebas funcionales
finales.

## 3. Pruebas funcionales finales

Para validar el comportamiento completo del workflow se deben cubrir los
principales escenarios definidos en los requisitos del proyecto.

  ---------------------------------------------------------------------------------------
  ID          Caso de prueba  Entrada           Resultado         Resultado   Estado
                                                esperado          obtenido    
  ----------- --------------- ----------------- ----------------- ----------- -----------
  T01         Producto no     `3017620422003`   Clasificación no  Pendiente   Pendiente
              saludable --                      saludable y       de          
              Nutella                           respuesta         registrar   
                                                cautelosa         ejecución   
                                                                  final       

  T02         Refresco --     `5449000000996`   El Nutri-Score    Pendiente   Pendiente
              Coca-Cola                         debe impedir una  de          
                                                clasificación     registrar   
                                                incorrectamente   ejecución   
                                                saludable         final       

  T03         Producto con    Código del        Clasificación     Pendiente   Pendiente
              Nutri-Score A   producto          saludable y       de          
                              seleccionado      respuesta         registrar   
                                                alentadora        ejecución   
                                                                  final       

  T04         Código de       Sin código        Explicación clara Pendiente   Pendiente
              barras vacío                      de cómo utilizar  de          
                                                SnackCheck        registrar   
                                                                  ejecución   
                                                                  final       

  T05         Código no       `ABC123`          Mensaje de        Pendiente   Pendiente
              numérico                          entrada no válida de          
                                                sin caída del     registrar   
                                                workflow          ejecución   
                                                                  final       

  T06         Producto        Código no         Mensaje de        Pendiente   Pendiente
              inexistente     registrado en     producto no       de          
                              Open Food Facts   encontrado        registrar   
                                                                  ejecución   
                                                                  final       

  T07         Datos           Producto sin los  No clasificar y   Pendiente   Pendiente
              nutricionales   campos            devolver mensaje  de          
              insuficientes   nutricionales     de datos          registrar   
                              necesarios        insuficientes     ejecución   
                                                                  final       
  ---------------------------------------------------------------------------------------

> **Nota:** El historial exportado confirma que se realizaron numerosas
> ejecuciones, pero no incluye el detalle del barcode, la ruta seguida
> ni el cuerpo de respuesta de cada ejecución. Por ello, esos resultados
> no se atribuyen retrospectivamente a casos concretos sin evidencia.
> Las pruebas funcionales finales deben completarse con el resultado
> observado en n8n.

## 4. Aspectos comprobados durante el desarrollo

Durante las distintas iteraciones se trabajó y probó sobre los
siguientes componentes:

-   Recepción de datos mediante Webhook.
-   Validación de la entrada.
-   Consulta de productos mediante Open Food Facts.
-   Manejo de productos no encontrados.
-   Extracción y estructuración de datos nutricionales.
-   Evaluación de azúcar, sal y grasa.
-   Interpretación del Nutri-Score.
-   Cálculo de la clasificación nutricional.
-   Selección del veredicto más restrictivo.
-   Construcción de objetos JSON en nodos Set / Edit Fields.
-   Generación de una explicación mediante Groq.
-   Formateo de la respuesta final.
-   Gestión de rutas de error sin provocar una caída del workflow.

## 5. Criterio de aceptación

Una prueba se considera **PASS** cuando:

1.  El workflow termina sin errores técnicos.
2.  Se sigue la ruta correspondiente al tipo de entrada.
3.  La clasificación coincide con las reglas definidas en SnackCheck.
4.  La respuesta es clara y legible.
5.  En los casos de error o ausencia de datos, el workflow responde de
    forma controlada y no genera una clasificación incorrecta.

## 6. Resultado general

El historial de n8n demuestra un proceso iterativo de desarrollo con
múltiples ciclos de prueba, error y corrección. Antes de la entrega
final se documentarán las ejecuciones representativas de los siete
escenarios funcionales anteriores para dejar evidencia trazable del
comportamiento final de SnackCheck.
