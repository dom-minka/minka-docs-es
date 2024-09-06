# Confirmar un crédito

El estado de la orden refleja las reglas del protocolo de dos fases de confirmación. Una vez que todos los participantes preparan con éxito una orden, se mueve inmediatamente a `committed`. La segunda fase del protocolo es iniciada por una autoridad centralizada llamada coordinador de transacciones distribuidas. El libro mayor realiza este rol y confirma automáticamente la orden una vez que se cumplen las condiciones requeridas.

La segunda fase del protocolo requiere a los participantes solo confirmar la operación, no pueden cambiar el resultado. La operación de confirmación siempre debe tener éxito, la fase de confirmación no puede ser abortada o fallar. En caso de cualquier fallo, es la responsabilidad del participante resolverlo y recuperarse.

Cuando procesamos una llamada de confirmación de crédito, sabemos que la orden ya está confirmada por el libro mayor, por lo que es seguro transferir los fondos a la cuenta de destino. Necesitamos acreditar la cuenta del usuario y enviar una referencia única (`coreId`) de esta operación como prueba al libro mayor:

```powershell
commit-credit for intent tzhDDHFFvUwt3hnl7S086 received:
---------------------------------------------------------------------------
 - Source: svgs:1001001345@teslabank.io
 - Target: svgs:1001009422@mintbank.dev
 - Amount: $12.00
 - Symbol: usd
---------------------------------------------------------------------------

? Type c to confirm,r to enter REPL shell, or i to ignore: Confirm
? Core Id: mint.124
> Credit committed, reply sent to ledger ✔

update for intent tzhDDHFFvUwt3hnl7S086 received:
---------------------------------------------------------------------------
 - Status: completed  [received intent status]
---------------------------------------------------------------------------
```

Después de enviar esta prueba, la orden cambia a `completed`, porque todos los participantes han confirmado la confirmación. Podemos verificar el estado de la orden nuevamente en Studio para ver este cambio también:

<figure><img src="../../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

También podemos examinar las pruebas de la orden en detalle si abrimos la página de detalles completos de la orden. Por ejemplo, el estado completado muestra que la orden fue primero confirmada por el libro mayor y que hemos confirmado enviando la firma 2PC con un core id:

<figure><img src="../../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

