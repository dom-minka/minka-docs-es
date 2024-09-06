# Abortar un crédito

Anteriormente creamos una orden exitosa, en este capítulo crearemos otra orden similar a la primera, pero fallaremos el paso de preparación para ver cómo se ve el flujo en caso de errores.

Podemos crear una orden similar a la primera, solo cambiamos el monto y la descripción para que sea más fácil distinguirlas:

```powershell
minka intent create
? Handle: OkW16jw4guaOKymaRAq9I
? Schema: transfer
? Action: transfer
? Source: svgs:1001001345@teslabank.io
? Target: svgs:1001009422@mintbank.dev
? Symbol: usd
? Amount: 5
? Add another action? No
? Attach a policy? No
? Intent commit mode: auto
? Add custom data for this intent? No
? Signers: teslabank
? Signer password for teslabank [hidden]

Intent summary:
---------------------------------------------------------------------------
Handle: OkW16jw4guaOKymaRAq9I
Schema: transfer

Action: transfer
 - Source: svgs:1001001345@teslabank.io
 - Target: svgs:1001009422@mintbank.dev
 - Symbol: usd
 - Amount: $5.00


? Sign this intent using signer teslabank? Yes

✅ Intent signed and sent to ledger ach
Luid: $int.ZrOccF04DEywjaKvi
Intent status: pending
```

En la consola de nuestro puente recibiremos un nuevo crédito de preparación, pero ahora lo fallaremos:

```powershell
prepare-credit for intent OkW16jw4guaOKymaRAq9I received:
---------------------------------------------------------------------------
 - Source: svgs:1001001345@teslabank.io
 - Target: svgs:1001009422@mintbank.dev
 - Amount: $5.00
 - Symbol: usd
---------------------------------------------------------------------------

? Type p to prepare, f to fail, r to enter REPL shell, or i to ignore: Fail
? Core Id:
> Credit failed, reply sent to ledger ✔
```

Después de recibir la prueba fallida, el libro mayor procederá a abortar esta orden. El libro mayor notifica a todos los participantes que la orden ha sido abortada y que necesitan revertir cualquier operación que hayan realizado para esta orden. Nuestro puente recibirá una llamada de aborto de crédito:

```powershell
abort-credit for intent OkW16jw4guaOKymaRAq9I received:
---------------------------------------------------------------------------
 - Source: svgs:1001001345@teslabank.io
 - Target: svgs:1001009422@mintbank.dev
 - Amount: $5.00
 - Symbol: usd
---------------------------------------------------------------------------

Error
 - Reason: core.bridge-prepare-failed
 - Detail: Bridge(s) failed to process intent: test-pc
 - Caused by
   - Reason: bridge.entry-rejected
   - Detail: Entry was manually rejected by the bridge

? Type c to confirm,r to enter REPL shell, or i to ignore: Confirm
? Core Id: mint.1475
> Credit aborted, reply sent to ledger ✔

update for intent OkW16jw4guaOKymaRAq9I received:
---------------------------------------------------------------------------
 - Status: rejected  [received intent status]
---------------------------------------------------------------------------

Error
 - Reason: core.bridge-prepare-failed
 - Detail: Bridge(s) failed to process intent: test-pc
 - Caused by
   - Reason: bridge.entry-rejected
   - Detail: Entry was manually rejected by the bridge
```

En el aborto de crédito deberíamos limpiar cualquier efecto secundario de la preparación de crédito. No realizamos ningún movimiento de saldo, por lo que no hay nada que revertir aquí.

Podemos verificar cómo se ve nuestra orden en Studio también:

<figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

Studio muestra que la orden no fue preparada, que hemos recibido un solo fallo y un solo aborto de prueba.

Podemos examinar estas pruebas en detalle abriendo la página de detalles de la orden:

<figure><img src="../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

Cada prueba que falló con algún error contiene todos los detalles del error y un core id, si se realizó una operación central. El error mostrado en la orden (lado izquierdo) es el último error que ocurrió para esta orden.

## ¿Qué sigue?

En este tutorial, hemos conectado un puente local al libro mayor y hemos aprendido más sobre el protocolo de dos fases de confirmación para sincronizar los movimientos de saldo entre sistemas financieros. Puedes crear más órdenes y probar varias otras situaciones utilizando las herramientas que hemos mostrado en este tutorial.

No mostramos cómo funcionan las operaciones de débito, puedes verificarlo por tu cuenta. La operación de débito funciona de la misma manera que los créditos, pero hay algunas diferencias en la lógica que tu integración debe realizar en cada fase. Por ejemplo, la preparación de débito debe reservar fondos de la cuenta de origen, mientras que la preparación de crédito solo valida.

Exploraremos estos detalles específicos en tutoriales posteriores que te mostrarán cómo implementar un servicio de puente en NodeJs.

