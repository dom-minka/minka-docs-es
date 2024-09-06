# Creando una intención

Con todo configurado y conectado, podemos crear nuestra primera intención de pago. Para esto, usaremos `Tesla Bank` para enviar un pago a un usuario desde nuestro propio banco. Podemos crear una intención de esta manera:

> Para efectos del manual, se usa como banco de prueba `mintbank.dev`, usted deberá usar el nombre de la wallet que aparece en la [vista de Studio](../unirse-al-sistema-de-pagos/descripcion-de-vistas.md)

```powershell
? minka intent create
? Handle: WNcInNUiswwG5yqcZ6oHV
? Schema: transfer
? Action: transfer
? Source: svgs:1001001345@teslabank.io
? Target: svgs:1001009422@mintbank.dev
? Symbol: usd
? Amount: 3
? Add another action? No
? Attach a policy? No
? Intent commit mode: auto
? Add custom data for this intent? No
? Signers: teslabank
? Signer password for teslabank [hidden]

Intent summary:
---------------------------------------------------------------------------
Handle: WNcInNUiswwG5yqcZ6oHV
Schema: transfer

Action: transfer
 - Source: svgs:1001001345@teslabank.io
 - Target: svgs:1001009422@mintbank.dev
 - Symbol: usd
 - Amount: $3.00


? Sign this intent using signer teslabank? Yes

✅ Intent signed and sent to ledger ach
Luid: $int.5AG45sQYpmbHqHJc8
Intent status: pendingminka intent create
? Handle: WNcInNUiswwG5yqcZ6oHV
? Schema: transfer
? Action: transfer
? Source: svgs:1001001345@teslabank.io
? Target: svgs:1001009422@mintbank.dev
? Symbol: usd
? Amount: 3
? Add another action? No
? Attach a policy? No
? Intent commit mode: auto
? Add custom data for this intent? No
? Signers: teslabank
? Signer password for teslabank [hidden]

Intent summary:
---------------------------------------------------------------------------
Handle: WNcInNUiswwG5yqcZ6oHV
Schema: transfer

Action: transfer
 - Source: svgs:1001001345@teslabank.io
 - Target: svgs:1001009422@mintbank.dev
 - Symbol: usd
 - Amount: $3.00


? Sign this intent using signer teslabank? Yes

✅ Intent signed and sent to ledger ach
Luid: $int.5AG45sQYpmbHqHJc8
Intent status: pending
```

> Necesitamos firmar esta intención usando el firmante `teslabank`, que es un firmante remoto disponible en el ledger ya que el usuario fuente es un usuario de ese banco.

> La selección del firmante `teslabank` se realiza con la barra espaciadora, notará que queda seleccionada&#x20;
>
> <img src="../../.gitbook/assets/image.png" alt="" data-size="original">



Para la información del usuario y los números de cuenta, podemos usar cualquier valor que tenga un formato correcto. Tesla Bank es un banco de demostración que no valida estos datos y no hemos conectado ninguna integración personalizada para nuestro banco, así que todas las intenciones de pago se aceptan por defecto.

Si abrimos nuestro panel de estudio ahora, deberíamos ver nuestra intención bajo transferencias:

<figure><img src="../../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>
