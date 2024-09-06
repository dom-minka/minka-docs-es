---
icon: cloud-snow
---

# Errores de libro mayor (core)

Estos errores son creados por el libro mayor (core) que maneja las intenciones y los movimientos de saldo de sistema de pagos.&#x20;

Dado que el  del libro mayor procesa las intenciones de forma asíncrona en segundo plano, estos errores no se enviarán en las respuestas HTTP, por lo que no hay códigos de estado HTTP asociados con este grupo de errores.

Estos errores se agregan a la firma de la intención cuando ocurren.&#x20;

La firma agregada tendrá `custom.status = 'aborted'` y `custom.reason` igual a una de las razones en este grupo de razones **core.**\* y `custom.detail` con un mensaje que explica el error en más detalle.



| Status | Reason                              | Detail                                                                                                                                                                                                                                                                                               |
| ------ | ----------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `N/A`  | `core.source-invalid`               | No se puede resolver el origen del movimiento de saldo a una cartera válida.                                                                                                                                                                                                                         |
| `N/A`  | `core.target-invalid`               | No se puede resolver el destino del movimiento de saldo a una cartera válida.                                                                                                                                                                                                                        |
| `N/A`  | `core.symbol-invalid`               | No se puede resolver el símbolo del movimiento de saldo.                                                                                                                                                                                                                                             |
| `N/A`  | `core.insufficient-balance`         | La cartera de origen del movimiento de saldo no tiene saldo suficiente.                                                                                                                                                                                                                              |
| `N/A`  | `core.intent-expired`               | La intención expiró antes de que se recolectaran todas las firmas requeridas. Una firma con esta`reason`es añadida a la intención por un trabajo en segundo plano que periódicamente verifica las intenciones y aborta todas las que siguen**pendientes**después de un tiempo de espera predefinido. |
| `N/A`  | `core.limit-exceeded`               | La ejecución de la intención excedió un límite. Los límites pueden ser excedidos por la cartera de origen o destino y por diferentes razones. El`detail`del error contendrá más detalles.                                                                                                            |
| `N/A`  | `core.bridge-prepare-failed`        | El puente reportó un fallo en la fase de**preparación**. Cuando el núcleo del libro mayor añade una firma con esta`reason`, establecerá`failId`a la`reason`recibida en la firma**fallida**del puente.                                                                                                |
| `N/A`  | `core.bridge-unreachable`           | No se puede entregar el evento de**preparación**al puente. Se usa cuando todos los intentos definidos por la estrategia de reintento han fallado.                                                                                                                                                    |
| `N/A`  | `core.bridge-unsupported-operation` | No se puede enviar la solicitud al puente porque no soporta la operación.                                                                                                                                                                                                                            |
| `N/A`  | `core.routing-failed`               | Cuando no se pudo realizar el enrutamiento de una intención. Por ejemplo, cuando la cartera de destino tiene rutas definidas pero ninguna de ellas coincide con la intención actual.                                                                                                                 |
| `N/A`  | `core.thread-size-exceeded`         | Cuando el enrutamiento de una intención es demasiado profundo. Se usa para evitar bucles de enrutamiento en las carteras.                                                                                                                                                                            |
| `N/A`  | `core.unexpected-error`             | Ocurrió un error inesperado en el núcleo del libro mayor al procesar la intención.                                                                                                                                                                                                                   |
