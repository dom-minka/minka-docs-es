# Preparación del crédito

### Preparando créditos

Ahora podemos comenzar a implementar los adaptadores del protocolo de dos fases de confirmación en nuestro puente. Usaremos el SDK del banco que viene con el proyecto de forma predeterminada para simular un sistema bancario transaccional al que nos estamos conectando con el ledger.

Prepare credit es el primer punto final del puente que se llama cuando llega un pago entrante a nuestro banco. En este controlador necesitamos validar los datos de la intención de pago entrante y nuestro cliente de destino para asegurarnos de que la cuenta de destino existe y que puede recibir fondos.

Los bancos, por supuesto, pueden decidir ejecutar comprobaciones de fraude y cualquier número de otras comprobaciones en este punto.

La implementación predeterminada de prepare credit que viene con nuestro proyecto de inicio siempre confirma la operación. Echemos un vistazo al código del adaptador de crédito predeterminado:

```tsx
import { nanoid } from 'nanoid'
import {
  AbortResult,
  CommitResult,
  IBankAdapter,
  ResultStatus,
  PrepareResult,
  TransactionContext
} from '@minka/bridge-sdk'
import { config } from '../config'

export class CreditBankAdapter extends IBankAdapter {
  constructor(protected readonly ledgerSdk: LedgerSdk) {}

  async prepare(context: TransactionContext): Promise<PrepareResult> {
    // Siempre prepara exitosamente la operación entrante
    return {
      status: ResultStatus.Prepared,
    }
  }

  async commit(context: TransactionContext): Promise<CommitResult> {
    // Siempre confirma exitosamente la operación entrante y
    // genera un coreId aleatorio
		return {
      status: ResultStatus.Committed,
      coreId: `${config.BRIDGE_DOMAIN}.${nanoid(8}`,
    }
  }

  async abort(context: TransactionContext): Promise<AbortResult> {
    // Siempre aborta exitosamente la operación entrante
    return {
      status: ResultStatus.Aborted,
    }
  }
}
```

Como puedes ver, el adaptador tiene una función para cada operación y siempre devuelve un resultado exitoso por ahora.

Ahora vamos a hacer algunas validaciones básicas de la intención y comenzar a usar el `bankSdk` que viene con el proyecto para verificar la cuenta de destino antes de preparar un crédito:

```tsx
import { nanoid } from 'nanoid'
import {
  AbortResult,
  CommitResult,
  IBankAdapter,
  ResultStatus,
  PrepareResult,
  TransactionContext,
  EntryRejectedError,
  AccountNotFoundError,
  UnsupportedSymbolError,
  AccountInactiveError,
} from '@minka/bridge-sdk'
import { LedgerSdk, LedgerAddress } from '@minka/ledger-sdk'
import { config } from '../config'
import { bankSdk } from '../bank-sdk'

export class CreditBankAdapter extends IBankAdapter {
  async prepare(context: TransactionContext): Promise<PrepareResult> {		
		const { entry } = context
		// En el caso de los créditos, la cuenta de destino es nuestro usuario
		// Analiza la información de la cuenta de destino y verifica
		const {
		  schema: accountType,
		  prefix: accountNumber,
		  domain: bank,
		} = LedgerAddress.parse(entry.target.handle)
		
		// Esta intención no está relacionada con nuestro banco
		if (bank !== config.BRIDGE_DOMAIN) {
		  throw new EntryRejectedError('Invalid target domain.')
		}
		
		// Solo soportaremos cuentas transaccionales por ahora
		if (accountType !== 'tran') {
		  throw new AccountNotFoundError(
		    'Only transactional accounts are supported.'
		  )
		}
		
		// Solo soportaremos el símbolo usd
		if (entry.symbol.handle !== 'usd') {
		  throw new UnsupportedSymbolError(
		    `Expected symbol usd, but got ${entry.symbol.handle}.`
		  )
		}
		
		// Verifica que la cuenta existe en nuestro sistema y que está activa
		const account = bankSdk.getAccount(accountNumber)
		if (!account) {
		  throw new AccountNotFoundError(accountNumber)
		}
		
		if (!account.isActive()) {
		  throw new AccountInactiveError('Account disabled.')
		}
	 
		// Todas las comprobaciones son exitosas, prepara la operación
    return {
      status: ResultStatus.Prepared,
    }
  }
  
  async commit( ...
  }
  
  async abort( ...
  }
}
```

> El SDK del puente realiza validaciones técnicas relacionadas con la consistencia de los datos recibidos. Asegurará que solo recibamos llamadas de prepare credit válidas, también verificará que los datos entrantes están firmados por la clave pública del ledger configurada en el `.env` para evitar ataques de tipo "hombre en el medio" u otros ataques similares.

El código anterior demuestra varias validaciones que se pueden realizar al preparar un crédito. La fase de preparación del crédito generalmente implica solo validaciones, ya que no queremos acreditar cuentas de usuarios hasta que la transacción esté finalizada. También mostramos cómo validar los datos entrantes y también cómo se puede llamar a sistemas externos para realizar comprobaciones adicionales, como verificaciones de estado de cuenta, etc.

Nuestro sistema bancario simulado tiene varias cuentas preconfiguradas, podemos usarlas para pruebas:

```markdown
1001001001 -> no tiene transacciones,  balance:  0,00 USD
1001001002 -> tiene transacciones, balance: 70,00 USD
1001001003 -> tiene transacciones, balance:  0,00 USD
1001001004 -> cuenta inactiva
```

Podemos enviar un pago a una cuenta inactiva para ver que nuestras validaciones funcionan:

```powershell
$ minka intent create
? Handle: k39xjOxn04BW4vPoN
? Action: transfer
? Source: svgs:1001001212@teslabank.io
? Target: tran:1001001004@mintbank.dev
? Symbol: usd
? Amount: 4
? Add another action? No

Intent summary:
-----------------------------------------------------------------------
Handle: k39xjOxn04BW4vPoN
Action: transfer
- Source: svgs:1001001212@teslabank.io
- Target: tran:1001001004@mintbank.dev
- Symbol: usd
- Amount: $4.00

? Add custom data to this intent? No
? Signers: teslabank
? Key password for teslabank: [hidden]
? Sign this intent using signer teslabank? Yes

Intent signed and sent to ledger sandbox
Intent status: pending
```

En la consola de nuestro puente deberíamos ver que la intención falla:

```bash
Restaring due to changes...

prepare-credit for intent k39xjOxn04BW4vPoN received:
------------------------------------------
- Target: tran:1001001004@mintbank.dev
- Amount: $4.00
- Symbol: usd
-----------------------------------------
Credit failed, reply sent to ledger.
Reason: bridge.account-inactive
Detail: Account disabled.
CoreId: <null>

abort-credit for intent k39xjOxn04BW4vPoN received:
------------------------------------------
- Target: account:1001001004@mintbank.dev
- Amount: $4.00
- Symbol: usd
------------------------------------------
Credit aborted, reply sent to ledger.
CoreId: <null>

```





