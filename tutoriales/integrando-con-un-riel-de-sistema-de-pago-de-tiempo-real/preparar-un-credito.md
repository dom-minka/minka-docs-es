# Preparar un crédito

Esta solicitud es parte del protocolo de dos fases de confirmación que utiliza el libro mayor para asegurarse de que todos los participantes en una transacción distribuida cumplan con sus responsabilidades. Las confirmaciones enviadas al libro mayor deben ser las **pruebas** realizadas con una clave privada registrada con el puente y contener una **referencia de transacción** (`coreId`) de la operación realizada por el puente como evidencia.

El protocolo tiene dos fases: una fase de **preparación** y una fase de **confirmación**. La fase de **preparación** debe validar y asegurarse de que una operación puede ser ejecutada, pero una operación no se considera completada hasta que se recibe una solicitud de **confirmación** o **aborto** del libro mayor.

Para la operación de crédito, validaríamos que la información de la cuenta de destino coincide con nuestros registros, que la cuenta está activa y otros chequeos similares. Una vez que realizamos todas las validaciones necesarias, debemos confirmar esto al libro mayor enviando una prueba:

```powershell
Waiting for new requests...

prepare-credit for intent tzhDDHFFvUwt3hnl7S086 received:
---------------------------------------------------------------------------
 - Source: svgs:1001001345@teslabank.io
 - Target: svgs:1001009422@mintbank.dev
 - Amount: $12.00
 - Symbol: usd
---------------------------------------------------------------------------

? Type p to prepare, f to fail, r to enter REPL shell, or i to ignore: Prepare
? Core Id: 
> Credit prepared, reply sent to ledger ✔

update for intent tzhDDHFFvUwt3hnl7S086 received:
---------------------------------------------------------------------------
 - Status: prepared  [received intent status]
---------------------------------------------------------------------------
```

Hemos dejado el `coreId` vacío (" ") en el ejemplo anterior porque no realizamos ninguna transacción central, solo validamos los datos. En el caso de los créditos, generalmente no queremos realizar un movimiento de saldo a la cuenta de destino. Si lo hiciéramos, el usuario vería un saldo en su cuenta y podría usarlo antes de que la transacción sea confirmada por todos los participantes.

Ahora podemos verificar qué ha sucedido con nuestra orden en Studio al actualizar la página:

El estado de la orden ahora es `committed` y que dice `prepared (1)` también en el timeline. Esto significa que esta orden fue preparada por un participante. Nuestro Tesla Bank no tiene un puente configurado, por lo que solo necesitamos preparar esta orden. Basándonos en esto, una vez que enviamos nuestra prueba de preparación, la orden fue completamente preparada y el libro mayor procedió a la segunda fase, la confirmación.

<figure><img src="../../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

