# Simular el núcleo bancario

Para simular un núcleo bancario, crearemos un módulo simple que mantendrá los saldos de las cuentas de los clientes.

Tendrá conceptos de alto nivel similares a un ledger real, pero es rudimentario y solo para fines de demostración.

> 💡 En el mundo real, esto sería reemplazado por llamadas API al núcleo real.

Los detalles internos de este módulo no son importantes, así que siéntete libre de copiarlo y pegarlo. Lo que hay que notar es que este es un ledger simulado en memoria con algunas cuentas configuradas de antemano que nos permitirán simular diferentes casos.

Expone algunos métodos y lanzará errores en caso de, por ejemplo, saldo insuficiente. Los métodos `credit` y `debit` acreditan y debitan las cuentas de los clientes respectivamente. El método `hold` retendrá los saldos de los clientes para que no puedan gastarse y `release` los hará disponibles para gastar. Más adelante usaremos las cuentas preconfiguradas en varios escenarios.



Cree el archivo `src/core.ts` con el sigiuente código&#x20;

```typescript
export class CoreError extends Error {
    protected code: string
  
    constructor(message: string) {
      super(message)
      this.name = 'CoreError'
      this.code = '100'
    }
  }
  
  export class InsufficientBalanceError extends CoreError {
    constructor(message: string) {
      super(message)
      this.name = 'InsufficientBalanceError'
      this.code = '101'
    }
  }
  
  export class InactiveAccountError extends CoreError {
    constructor(message: string) {
      super(message)
      this.name = 'InactiveAccountError'
      this.code = '102'
    }
  }
  
  export class UnknownAccountError extends CoreError {
    constructor(message: string) {
      super(message)
      this.name = 'UnknownAccountError'
      this.code = '103'
    }
  }
  
  export class Account {
    public id: string
    public active: boolean
    public balance: number
    public onHold: number
  
    constructor(id: string, active = true) {
      this.id = id
      this.active = active
      this.balance = 0
      this.onHold = 0
    }
  
    debit(amount: number) {
      this.assertIsActive()
  
      if (this.getAvailableBalance() < amount) {
        throw new InsufficientBalanceError(
          `Insufficient available balance in account ${this.id}`,
        )
      }
  
      this.balance = this.balance - amount
    }
  
    credit(amount: number) {
      this.assertIsActive()
  
      this.balance = this.balance + amount
    }
  
    hold(amount: number) {
      this.assertIsActive()
  
      if (this.getAvailableBalance() < amount) {
        throw new InsufficientBalanceError(
          `Insufficient available balance in account ${this.id}`,
        )
      }
  
      this.onHold = this.onHold + amount
    }
  
    release(amount: number) {
      this.assertIsActive()
  
      if (this.onHold < amount) {
        throw new InsufficientBalanceError(
          `Insufficient balance on hold in account ${this.id}`,
        )
      }
  
      this.onHold = this.onHold - amount
    }
  
    getOnHold() {
      return this.onHold
    }
  
    getBalance() {
      return this.balance
    }
  
    getAvailableBalance() {
      return this.balance - this.onHold
    }
  
    isActive() {
      return this.active
    }
  
    assertIsActive() {
      if (!this.active) {
        throw new InactiveAccountError(`Account ${this.id} is inactive`)
      }
    }
  
    setActive(active: boolean) {
      this.active = active
    }
  }
  
  export class Transaction {
    public id: string
    public type: string
    public account: string
    public amount: number
    public status: string
    public idempotencyToken?: string
    public errorReason?: string
    public errorCode?: string
  
    constructor({ id, type, account, amount, status, idempotencyToken }: {
        id: string
        type: string
        account: string
        amount: number
        status: string
        idempotencyToken?: string
    }) {
      this.id = id
      this.type = type
      this.account = account
      this.amount = amount
      this.status = status
      this.errorReason = undefined
      this.errorCode = undefined
      this.idempotencyToken = idempotencyToken
    }
  }
  
  export class Ledger {
    accounts = new Map<string,Account>()
    transactions: Transaction[] = []
  
    constructor() {
      // account with no balance
      this.accounts.set('1', new Account('1'))
  
      // account with available balance 70
      this.accounts.set('2', new Account('2'))
      this.credit('2', 100)
      this.debit('2', 10)
      this.hold('2', 20)
  
      // account with no available balance 0
      this.accounts.set('3', new Account('3'))
      this.credit('3', 300)
      this.debit('3', 200)
      this.hold('3', 100)
  
      // inactive account
      this.accounts.set('4', new Account('4'))
      this.credit('4', 200)
      this.debit('4', 20)
      this.inactivate('4')
    }
  
    getAccount(accountId: string) {
      const account = this.accounts.get(accountId)
      if (!account) {
        throw new UnknownAccountError(`Account ${accountId} does not exist`)
      }
      return account
    }
  
    processTransaction(type: string, accountId: string, amount: number, idempotencyToken?: string) {
      if (idempotencyToken) {
        const existing = this.transactions.filter(
          (t) => t.idempotencyToken === idempotencyToken,
        )[0]
        if (existing) {
          return existing
        }
      }
  
      const nextTransactionId = this.transactions.length
      const transaction = new Transaction({
        id: nextTransactionId.toString(),
        type,
        account: accountId,
        amount,
        status: 'PENDING',
        idempotencyToken,
      })
      this.transactions[nextTransactionId] = transaction
      try {
        const account = this.getAccount(accountId)
        switch (type) {
          case 'CREDIT':
            account.credit(amount)
            break
          case 'DEBIT':
            account.debit(amount)
            break
          case 'HOLD':
            account.hold(amount)
            break
          case 'RELEASE':
            account.release(amount)
            break
        }
      } catch (error: any) {
        transaction.errorReason = error.message
        transaction.errorCode = error.code
        transaction.status = 'FAILED'
        return transaction
      }
      transaction.status = 'COMPLETED'
      return transaction
    }
  
    credit(accountId: string, amount: number, idempotencyToken?: string) {
      return this.processTransaction(
        'CREDIT',
        accountId,
        amount,
        idempotencyToken,
      )
    }
  
    debit(accountId: string, amount: number, idempotencyToken?: string) {
      return this.processTransaction('DEBIT', accountId, amount, idempotencyToken)
    }
  
    hold(accountId: string, amount: number, idempotencyToken?: string) {
      return this.processTransaction('HOLD', accountId, amount, idempotencyToken)
    }
  
    release(accountId: string, amount: number, idempotencyToken: string) {
      return this.processTransaction(
        'RELEASE',
        accountId,
        amount,
        idempotencyToken,
      )
    }
  
    activate(accountId: string) {
      return this.getAccount(accountId).setActive(true)
    }
  
    inactivate(accountId: string) {
      return this.getAccount(accountId).setActive(false)
    }
  
    printAccountTransactions(accountId: string) {
      console.log(
        `Id\t\tType\t\tAccount\t\tAmount\t\tStatus\t\t\tError Reason\t\tIdempotency Token`,
      )
      this.transactions
        .filter((t) => t.account === accountId)
        .forEach((t) =>
          console.log(
            `${t.id}\t\t${t.type}\t\t${t.account}\t\t${t.amount}\t\t${
              t.status
            }\t\t${t.errorReason || '-'}\t\t${t.idempotencyToken || '-'}`,
          ),
        )
    }
  
    printAccount(accountId: string) {
      const account = this.getAccount(accountId)
      console.log(
        JSON.stringify(
          {
            ...account,
            balance: account.getBalance(),
            availableBalance: account.getAvailableBalance(),
          },
          null,
          2,
        ),
      )
    }
  }
  
  const ledger = new Ledger()
  
  export default ledger
```



