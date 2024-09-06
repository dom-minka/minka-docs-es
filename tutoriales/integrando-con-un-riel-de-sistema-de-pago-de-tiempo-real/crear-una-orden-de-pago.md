# Crear una orden de pago

Ahora veamos qué sucede si creamos una nueva orden de pago que necesita ser acreditada en nuestro banco. Enviaremos un pago desde Tesla Bank a nuestro banco utilizando la CLI. Dejaremos la ventana del terminal con nuestro puente en ejecución y abriremos una nueva ventana del terminal para crear esta orden:

```powershell
? minka intent create
? Handle: tzhDDHFFvUwt3hnl7S086
? Schema: transfer
? Action: transfer
? Source: svgs:1001001345@teslabank.io
? Target: svgs:1001009422@mintbank.dev
? Symbol: usd
? Amount: 12
? Add another action? No
? Attach a policy? No
? Intent commit mode: auto
? Add custom data for this intent? No
? Signers: teslabank
? Signer password for teslabank [hidden]

Intent summary:
---------------------------------------------------------------------------
Handle: tzhDDHFFvUwt3hnl7S086
Schema: transfer

Action: transfer
 - Source: svgs:1001001345@teslabank.io
 - Target: svgs:1001009422@mintbank.dev
 - Symbol: usd
 - Amount: $12.00


? Sign this intent using signer teslabank? Yes

✅ Intent signed and sent to ledger ach
Luid: $int.7U7E7CRBbyCcZQjYj
Intent status: pending
```

Después de crear la orden, inmediatamente verás que se ha recibido una nueva solicitud en la consola de nuestro puente:

```powershell
Waiting for new requests...

prepare-credit for intent tzhDDHFFvUwt3hnl7S086 received:
---------------------------------------------------------------------------
 - Source: svgs:1001001345@teslabank.io
 - Target: svgs:1001009422@mintbank.dev
 - Amount: $12.00
 - Symbol: usd
---------------------------------------------------------------------------

? Type p to prepare, f to fail, r to enter REPL shell, or i to ignore: (Prfih)
```

También podemos abrir esta orden en Studio para ver que está esperando en el estado `pending` hasta que continuemos el proceso en nuestro puente:

<figure><img src="../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

