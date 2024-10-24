# Prueba de preparación de débito

Para probar el código anterior, crearemos una nueva intención que enviará dinero desde `svgs:1@mintbank.dev` a `svgs:73514@teslabank.io`.

```bash
$ minka intent create
? Handle: yKnJ6gRKEy5voiXb6
? Action: transfer
? Source: svgs:1@mintbank.dev
? Target: svgs:73514@teslabank.io
? Symbol: usd
? Amount: 10
? Add another action? No
? Add custom data? No
? Signers: bridge@mintbank.dev
? Key password for teslabank: *[hidden]*

Intent summary:
------------------------------------------------------------------------
Handle: yKnJ6gRKEy5voiXb6

Action: transfer
 - Source: svgs:1@mintbank.dev
 - Target: svgs:73514@teslabank.io
 - Symbol: usd
 - Amount: $10.00

? Sign this intent using signer bridge@mintbank.dev? Yes

✅ Intent signed and sent to ledger sandbox
Intent status: pending
```

Ahora deberíamos estar viendo algo como esto.

```
RECIBIDO POST /v2/debits
ENVIADA firma a Ledger
{
  "handle": "deb_eqBQaDkpBxymG21hU",
  "status": "prepared",
  "coreId": "9",
  "moment": "2023-03-09T09:28:06.044Z"
}
RECIBIDO POST /v2/debits/deb_eqBQaDkpBxymG21hU/commit

```
