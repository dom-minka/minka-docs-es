# Intentos de pagos

En el protocolo de Minka, el concepto de "intención" juega un papel crucial para garantizar transacciones de pago seguras y precisas.

Una intención es esencialmente una iniciación de pago previa a la transacción, creada antes de que ocurra la transacción real. Este mecanismo asegura que toda la información necesaria para la transacción se recopile y valide de antemano, mejorando significativamente la seguridad y precisión de los pagos.

Los "intents" representan una forma de ejecutar un cambio en el "ledger". Los cambios más comunes que implican un "intent" son el movimiento de saldos entre las cuentas (wallets) de la plataforma.

El "intent" se procesa en 3 pasos secuenciales: Aspectos, Compromiso en dos fases (2PC), Efectos.

l autor del "intent" lo construye y firma con su clave privada, asegurando así la integridad y autenticidad de su intención.

Los "intents" garantizan la atomicidad de los cambios en el "ledger", lo que significa que si hay múltiples cambios, se persistirán todos completamente o no se persistirá ninguno.

Hay 6 estados de "intent" relacionados con la atomicidad:

| Estado        | Descripción                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **pending**   | El "intent" ha sido enviado pero aún se está procesando, los cambios en los datos del "ledger" indicados en el "intent" aún no se han persistido.                                                                                                                                                                                                                                                |
| **aborted**   | El "intent" fue abortado mientras estaba en estado **pending** y eventualmente será **rejected**.                                                                                                                                                                                                                                                                                                |
| **prepared**  | El "intent" está preparado por todos los participantes y puede ser procesado - confirmado - si `config.commit` es `auto` o no está definido. Cuando el indicador de confirmación es`manual`, el "ledger" verificará y esperará una prueba de solicitud para abortar o confirmar. Ver [configuración de intent](https://www.notion.so/95a65838af8549f198ee2b708566265c?pvs=21) para más detalles. |
| **committed** | El "intent" se ha procesado con éxito y los cambios en los datos del "ledger" indicados en el "intent" se han persistido y no es posible revertir los cambios; eventualmente será **completed**.                                                                                                                                                                                                 |
| **completed** | El "intent" se procesó con éxito.                                                                                                                                                                                                                                                                                                                                                                |
| **rejected**  | El procesamiento del "intent" falló por alguna razón, los cambios en los datos del "ledger" indicados en el "intent" son rechazados y no es posible persistirlos más.                                                                                                                                                                                                                            |
