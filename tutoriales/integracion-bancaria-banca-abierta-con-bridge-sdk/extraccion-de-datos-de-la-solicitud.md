# Extracción de datos de la solicitud

Para extraer datos de las solicitudes, utilizaremos un par de funciones que colocaremos en `src/extractor.ts`. Por supuesto, los datos de la solicitud también deben ser validados.



```typescript
// Rellena esto con el identificador de la billetera que creaste, debería ser una variable de entorno
const BANK_WALLET = "mint";

// El factor para usd es 100
const USD_FACTOR = 100;

// Expresión regular de dirección utilizada para validación y extracción de componentes
const ADDRESS_REGEX = /^(((?<schema>[a-zA-Z0-9_\-+.]+):)?(?<handle>[a-zA-Z0-9_\-+.]+))(@(?<parent>[a-zA-Z0-9_\-+.]+))?$/;

export function extractAndValidateAddress(address: string) {
  const result = ADDRESS_REGEX.exec(address);
  if (!result?.groups) {
    throw new Error(`Dirección inválida, se recibió ${address}`);
  }
  const { schema, handle: account, parent } = result.groups;

  if (parent !== BANK_WALLET) {
    throw new Error(
      `Se esperaba que el padre de la dirección fuera ${BANK_WALLET}, se recibió ${parent}`
    );
  }
  if (schema !== "account") {
    throw new Error(`Se esperaba que el esquema de la dirección fuera account, se recibió ${schema}`);
  }
  if (!account || account.length === 0) {
    throw new Error("Falta la cuenta en la solicitud de crédito");
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
    throw new Error(`Se esperaba una cantidad entera positiva, se recibió ${amount}`);
  }
  return amount / USD_FACTOR;
}

export function extractAndValidateSymbol(symbol: string) {
  // En general, son posibles otros símbolos además de usd, pero
  // solo admitimos usd en este tutorial
  if (symbol !== "usd") {
    throw new Error(`Se esperaba el símbolo usd, se recibió ${symbol}`);
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
    throw new Error("Falta la dirección en la entrada");
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



