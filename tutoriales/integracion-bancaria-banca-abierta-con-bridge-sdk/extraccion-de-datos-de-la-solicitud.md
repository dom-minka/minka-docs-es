# Extracción de datos de la solicitud

Para extraer datos de las solicitudes, utilizaremos un par de funciones que colocaremos en `src/extractor.ts`. Por supuesto, los datos de la solicitud también deben ser validados.

> Actualice la siguiente constante:
>
> ```
> const BANK_WALLET =["[Nombre de su wallet]"];
> ```

```typescript
// Populate this with the wallet handle you created, should be env var
const BANK_WALLET =["[Nombre de su wallet]"];
const SCHEMAS = ["caho", "ccte","dbmo","svgs"];
const SYMBOLS= ["usd","cop"];
// Factor for usd is 100
const USD_FACTOR = 100;

// Address regex used for validation and component extraction
const ADDRESS_REGEX =/^(((?<schema>[a-zA-Z0-9_\-+.]+):)?(?<handle>[a-zA-Z0-9_\-+.]+))(@(?<parent>[a-zA-Z0-9_\-+.]+))?$/;   

export function extractAndValidateAddress(address: string) {
  const result = ADDRESS_REGEX.exec(address);
  if (!result?.groups) {
    throw new Error(`Invalid address, got ${address}`);
  }
  const { schema, handle: account, parent } = result.groups;
  
  if (!BANK_WALLET.find(s=> s==parent)) {
    throw new Error(
      `Expected address parent to be ${BANK_WALLET}, got ${parent}`
    );
  }
  if (!SCHEMAS.find(s=> s==schema)) {
    throw new Error(`Expected address schema to be ${SCHEMAS}, got ${schema}`);
  }
  if (!account || account.length === 0) {
    throw new Error("Account missing from credit request");
  }

  return {
    schema,
    account,
    parent,
  };
}

export function extractAndValidateAmount(rawAmount: number) {
  const amount = Number(rawAmount);
  if (!Number.isInteger(amount) || amount <= 0) {
    throw new Error(`Positive integer amount expected, got ${amount}`);
  }
  return amount / USD_FACTOR;
}

export function extractAndValidateSymbol(symbol: string) {
  // In general symbols other than usd are possible, but
  // we only support usd in the tutorial
  if (!SYMBOLS.find(s=> s==symbol)) {
    throw new Error(`Symbol ${SYMBOLS} expected, got ${symbol}`);
  }
  return symbol;
}

export function extractAndValidateData(entry: {
  schema: string;
  target: string;
  source: string;
  amount: number;
  symbol: string;
}) {
  const rawAddress = entry?.schema === "credit" ? entry.target : entry.source;
	if(!rawAddress) {
    throw new Error("Address missing from entry");
  }
  const address = extractAndValidateAddress(rawAddress);
  const amount = extractAndValidateAmount(entry.amount);
  const symbol = extractAndValidateSymbol(entry.symbol);

  return {
    address,
    amount,
    symbol,
  };
}
```



