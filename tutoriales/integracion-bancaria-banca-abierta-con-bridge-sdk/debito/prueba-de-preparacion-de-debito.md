# Prueba de preparación de débito

Para probar el código anterior, crearemos una nueva intención que enviará dinero desde `account:1@mint` a `account:73514@tesla`.

```bash
$ minka intent create
? Handle: yKnJ6gRKEy5voiXb6
? Action: transfer
? Source: account:2@mint
? Target: account:73514@tesla
? Symbol: usd
? Amount: 10
? Add another action? No
? Add custom data? No
? Signers: mint
? Key password for tesla: *[hidden]*

Intent summary:
------------------------------------------------------------------------
Handle: yKnJ6gRKEy5voiXb6

Action: transfer
 - Source: account:2@mint
 - Target: account:73514@tesla
 - Symbol: usd
 - Amount: $10.00

? Sign this intent using signer mint? Yes

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
