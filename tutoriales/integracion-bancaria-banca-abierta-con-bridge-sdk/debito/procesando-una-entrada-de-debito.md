# Procesando una entrada de débito

Ahora haremos lo mismo que hicimos pora el crédito, pero para los endpoints de débito. Comencemos con el manejador de preparación de débito. (archivo **`src/adapters/debit.adapter.ts)`**&#x20;

```jsx
  prepare(context: TransactionContext): Promise<PrepareResult> {
    console.log('RECIBIDO POST /v2/debits')

    let result: PrepareResult
    let transaction

    try {
      const extractedData = extractor.extractAndValidateData(this.getEntry(context))

      transaction = core.hold(
        extractedData.address.account,
        extractedData.amount,
        `${context.entry.handle}-hold`,
      )

      if (transaction.status !== 'COMPLETED') {
        throw new Error(transaction.errorReason)
      }

      result = {
        status: ResultStatus.Prepared,
        coreId: transaction.id.toString(),
      }
    } catch (e: any) {
      result = {
        status: ResultStatus.Failed,
        error: {
          reason: LedgerErrorReason.BridgeUnexpectedCoreError,
          detail: e.message,
          failId: undefined,
        },
      }
    }

    return Promise.resolve(result)
  }
```

Puedes notar que el procesamiento para la acción `prepare` es un poco más complejo que el de su contraparte de crédito porque tenemos que retener los fondos.

Si nos encontramos con problemas, establecemos el estado como `failed` y notificamos a Ledger, lo que iniciará un aborto. Si en cambio notificamos a Ledger que estamos `prepared`, ya no podemos fallar cuando recibamos la acción `commit`.

