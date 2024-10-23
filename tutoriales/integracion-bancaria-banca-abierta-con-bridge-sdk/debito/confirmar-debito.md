# Confirmar débito

Ahora implementaremos el endpoint de confirmación de débito.

```typescript
commit(context: TransactionContext): Promise<CommitResult> {
    console.log('RECIBIDO POST /v2/debits/commit')

    let result: CommitResult
    let transaction

    try {
      const extractedData = extractor.extractAndValidateData(context.entry)

      transaction = core.release(
        extractedData.address.account,
        extractedData.amount,
        `${context.entry.handle}-release`,
      )

      if (transaction.status !== 'COMPLETED') {
        throw new Error(transaction.errorReason)
      }

      transaction = core.debit(
        extractedData.address.account,
        extractedData.amount,
        `${context.entry.handle}-debit`,
      )

      if (transaction.status !== 'COMPLETED') {
        throw new Error(transaction.errorReason)
      }

      result = {
        status: ResultStatus.Committed,
        coreId: transaction.id.toString(),
      }
    } catch (e) {
      result = {
        status: ResultStatus.Suspended,
      }
    }

    return Promise.resolve(result)
  }
```

De nuevo, puedes ver que el procesamiento para `commit` de débito es un poco más complejo que para `commit` de crédito porque primero necesitamos liberar los fondos y luego ejecutar una operación de débito. Dependiendo de cómo se implementen las órdenes de transferencia, es posible que en un núcleo real esto pueda ser una sola operación.

Después de ejecutar `Test 2` nuevamente, deberíamos obtener lo siguiente.

```
RECIBIDO POST /v2/debits
ENVIADA firma a Ledger
{
  "handle": "deb_dd6AXq9DBtm2fdtaK",
  "status": "prepared",
  "coreId": "8",
  "moment": "2023-03-09T09:38:43.852Z"
}
RECIBIDO POST /v2/debits/deb_dd6AXq9DBtm2fdtaK/commit
ENVIADA firma a Ledger
{
  "handle": "deb_dd6AXq9DBtm2fdtaK",
  "status": "committed",
  "coreId": "10",
  "moment": "2023-03-09T09:38:44.296Z"
}

```
