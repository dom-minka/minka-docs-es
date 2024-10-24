# Procesamiento de solicitudes

Vemos que recibimos una solicitud al endpoint **`POST /v2/credits`**

> 💡 Esta solicitud es parte del protocolo de confirmación en dos fases del Ledger, lee más sobre esto en [Acerca de las Intenciones](../../../referencias/referencia-api/intentos-de-pago-intents.md).

El SDK de Bridge hace la mayor parte del trabajo pesado por nosotros:

* guardar la solicitud
* idempotencia
* algunos errores
* validación de firmas
* notificar al ledger sobre el procesamiento de entradas
* reintentos
* coordinar el procesamiento de solicitudes
* procesar actualizaciones finales de intenciones

Solo necesitaremos escribir 6 métodos para procesar realmente la solicitud en el núcleo bancario. Es posible tener manejadores de solicitudes tanto sincronos como asíncronos, pero nos ceñiremos a los síncronos para este tutorial.

Necesitamos prepararnos para acreditar la cuenta del cliente, lo que implica cosas como verificar que existe, que está activa y similares en el núcleo bancario.

En caso de débito, también necesitaremos verificar que el cliente tiene fondos suficientes y poner una retención sobre esos fondos.

Después de que una preparación tiene éxito, ya no se nos permite fallar en la confirmación. En ese punto, la responsabilidad de confirmar o abortar la transferencia se pasa al Ledger.

