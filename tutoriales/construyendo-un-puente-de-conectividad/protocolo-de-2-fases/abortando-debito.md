# Abortando débito

### Abortando débitos

En el aborto de débitos tenemos que revertir una transacción que hicimos anteriormente en la preparación. Los controladores de aborto no deben fallar, por lo que necesitamos asegurarnos de que podemos recuperarnos de forma confiable de cualquier posible error técnico en el sistema.

> Se recomienda implementar reintentos en caso de fallos en los servicios bancarios centrales. El servicio programador del puente nos permite usarlo para este propósito, ya que nos permite reprogramar trabajos para más tarde.

Nuestro controlador de aborto de débito es relativamente simple, solo necesita realizar una acción opuesta a la de la preparación de débito:

```tsx
import ...

export class DebitBankAdapter extends IBankAdapter {
  async prepare(...
  }
  
  async commit( ...
  }
  
  async abort(context: TransactionContext): Promise<AbortResult> {
    try {
  		const { entry } = context
		  const {
		    prefix: accountNumber,
      } = LedgerAddress.parse(entry.source.handle)
      const decimalAmount = await LedgerAmount.toDecimal(
        entry.amount,
        entry.symbol.handle,
        this.ledgerSdk,
      )
		
	    const transaction = bankSdk.credit(
	      accountNumber,
	      decimalAmount,
	      `${context.entry.handle}-reverse-debit`,
      )
      
      const account = coreSdk.getAccount(accountNumber)
      console.log(
        `Reverse successful, new balance: ${account.getBalance()}\n`
      )
    
	    return {
	      status: ResultStatus.Aborted,
		    coreId: `${config.BRIDGE_DOMAIN}.${transaction.id}`,
	    }
    } catch (e: Error) {
      console.log(`Error while aborting debit: ${e.message}\n`)
      
      // Retry after 1 minute
      const suspendUntil = new Date()
      suspendUntil.setMinutes(suspendUntil.getMinutes() + 1)
      return {
        status: ResultStatus.Suspended,
        suspendUntil,
      }
    }
  }
}
```

> La estrategia de reintento implementada arriba es muy simplista, cuando construyamos un servicio listo para producción implementaremos una estrategia más avanzada, por ejemplo retroceso exponencial con un número limitado de reintentos.
