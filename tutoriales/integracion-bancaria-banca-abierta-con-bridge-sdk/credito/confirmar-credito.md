# Confirmar crédito

Hemos procesado con éxito la solicitud `prepare` para una Entrada de crédito y Ledger continuó procesando hasta el punto donde obtuvimos **POST /v2/credits/:handle/commit**. Ahora necesitaremos implementar la solicitud `commit` para continuar.

Usaremos el mismo patrón que usamos para procesar la preparación, así que editaremos la función de confirmación de crédito en consecuencia. (archivo **`src/adapters/credit.adapter.ts)`**

```jsx
commit(context: TransactionContext): Promise<CommitResult> {
    console.log('RECIBIDO POST /v2/credits/commit')

    let result: CommitResult
    let transaction
    try {
      const extractedData = extractor.extractAndValidateData(this.getEntry(context))

      transaction = core.credit(
        extractedData.address.account,
        extractedData.amount,
        `${context.entry.handle}-credit`,
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

Como sabes por [Acerca de las Intenciones](../../../referencias/referencia-api/intentos-de-pago-intents.md), no se nos permite fallar la solicitud `commit`, así que si algo sucede durante el procesamiento, simplemente suspendemos el procesamiento por ahora.

Para todos los efectos, la Entrada ya está `committed` cuando recibimos el `commit`, simplemente aún no ha sido procesada por el Banco.

Ahora podemos ejecutar `Test 1` de nuevo y deberíamos obtener algo como esto.

```bash
RECIBIDO POST /v2/credits
ENVIADA firma al Ledger
{
  "handle": "cre_iJnI2KGWCI7CIfeWL",
  "status": "prepared",
  "moment": "2023-03-09T09:13:22.725Z"
}
RECIBIDO POST /v2/credits/cre_iJnI2KGWCI7CIfeWL/commit
ENVIADA firma al Ledger
{
  "handle": "cre_iJnI2KGWCI7CIfeWL",
  "status": "committed",
  "coreId": "8",
  "moment": "2023-03-09T09:13:23.201Z"
}
```

En este punto, Ledger también actualizará la intención con el estado final usando **`PUT /v2/intents/:handle`**, pero no lo vemos aquí porque el SDK lo maneja por nosotros.
