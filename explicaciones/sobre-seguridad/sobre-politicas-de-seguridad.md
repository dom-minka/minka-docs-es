---
icon: user-police
---

# Sobre políticas de seguridad

### ¿Qué es una política de seguridad?

Una política de seguridad es un registro del Libro Mayor que representa un conjunto de reglas de acceso que pueden adjuntarse a los registros.

{% code title="" %}
```tsx
{
   "data": {
     "handle": "bank",
     "record": "wallet",
     "schema": "access",
     "values": [{
       "action": "update",
       "signer": {
         "$circle": "bank-circle"
       }
     }, {
       "action": "read",
       "bearer": {
         "$signer": {
           "$circle": "bank-circle"
         }
       }
     }],
     "custom": {
       ...
     },
     "access": [...],
   },   
   "hash": "...",
   "meta": {...}
}
```
{% endcode %}

Ejemplo de un payload de una política que representa un conjunto de reglas de acceso que pueden aplicarse a las billeteras en el sistema. Estas reglas de acceso permiten a los firmantes del círculo bank-circle actualizar y leer todas las billeteras en el sistema.



### Dirigiendo registros - `record`

Para crear una política es necesario definir la clase objetivo de registro que puede reutilizarla y esto se hace con las propiedades `record` y `filter`.

A continuación, hay algunos ejemplos de políticas que definen reglas de seguridad para ser utilizadas por los registros del Libro Mayor.

```tsx
{
   "data": {
     "handle": "signer-management",
     "record": "signer",
     "schema": "access",
     "values": [{
       "action": "update",
       "signer": {
         "$circle": "owner"
       }
     }, {
       "action": "read",
       "bearer": {
         "$signer": {
           "$circle": "owner"
         }
       }
     }],
     "custom": {
       ...
     },
     "access": [...],
   },   
   "hash": "...",
   "meta": {...}
}
```

Ejemplo de un payload de política que define reglas de seguridad para leer y actualizar firmantes en el libro mayor. Un firmante puede ser actualizado por cualquier firmante  - firma en el cuerpo de `meta.proofs` del círculo owner. Un firmante también puede ser leído por cualquier firmante - mediante autenticación por token JWT  del círculo `owner`.

```tsx
{
   "data": {
     "handle": "record-factory",
     "record": "any",
     "schema": "access",
     "values": [{
       "action": "create",
       "signer": {
         "$circle": "owner"
       }
     }],
     "custom": {
       ...
     },
     "access": [...],
   },   
   "hash": "...",
   "meta": {...}
}
```

Ejemplo de un payload de política que define reglas de seguridad para crear cualquier registro en el libro mayor.

```tsx
{
   "data": {
     "handle": "wallet-reader",
     "record": "wallet",
     "schema": "access",
     "filter": {
        "schema": "bank-wallet" 
     },
     "values": [{
       "action": "read",
       "signer": {
         "$circle": "bank"
       }
     }],
     "custom": {
       ...
     },
     "access": [...],
   },   
   "hash": "...",
   "meta": {...}
}
```

Ejemplo de payload de política que define reglas de seguridad para leer billeteras con el esquema `bank-wallet`.

#### Extendiendo políticas - `extend`

Las políticas pueden ser extendidas, lo que significa que una política puede reutilizar valores de una política existente utilizando la propiedad `extend`

```tsx
// Política "reader"
{
   "data": {
     "handle": "reader",
     "record": "any",
     "schema": "access",
     "values": [{
       "action": "read",
       "signer": {
         "$circle": "admin"
       }
     }],
     "custom": {
       ...
     },
     "access": [...],
   },   
   "hash": "...",
   "meta": {...}
}

// Política "wallet-reader"
{
   "data": {
     "handle": "wallet-reader", 
     "extend": "reader",
     "record": "wallet",
     "values": [{
       "action": "read",
       "signer": {
         "$circle": "bank"
       }
     }],
     "custom": {
       ...
     },
     "access": [...],
   },   
   "hash": "...",
   "meta": {...}
}
```

Ejemplo de una política llamada `wallet-reader` que extiende las reglas de acceso de la política `reader`. Los valores de la política `reader` se añaden a los valores de su hijo - `wallet-reader` - cuando se evalúan para otorgar acceso.

### Añadiendo filtro a los valores de la política - `filter`

Los valores de la política aceptan `filter`, que se utiliza para filtrar a qué registros se aplica el valor. El filtro puede definirse con cualquier contenido, y su valor se utiliza para coincidir con las propiedades de los `data` de un registro.

```tsx
// Símbolo "usd"
{
   "data": {
     "handle": "usd",
     "factor": 100,
     "schema": "fiat",
     ...
   },   
   "hash": "...",
   "meta": {...}
}

// Símbolo "bitcoin"
{
   "data": {
     "handle": "bitcoin",
     "factor": 100000000,
     "schema": "crypto",
     ...
   },   
   "hash": "...",
   "meta": {...}
}

// Política "symbol-reader"
{
   "data": {
     "handle": "symbol-reader", 
     "record": "symbol",
     "schema": "access",
     "values": [{
       "action": "read",
       "signer": {
         "$circle": "bank"
       },
       "filter": {
         "schema": "fiat"
       },
     }, {
       "action": "read",
       "signer": {
         "$circle": "exchange"
       },
       "filter": {
         "schema": "crypto"
       }
     }],
     "custom": {
       ...
     },
     "access": [...],
   },   
   "hash": "...",
   "meta": {...}
}
```

Ejemplo de dos `symbols` - `usd` y `bitcoin`  y una política llamada `symbol-reader` que implementa dos reglas de acceso para leer un `symbol`.  Aunque esta política se aplica a cada `symbol`, el primer valor no se utilizará para otorgar acceso a la lectura del símbolo `bitcoin` debido a la condición del filtro solo símbolos con esquema `fiat` así como el segundo no puede ser utilizado por símbolos con esquema diferente a `crypto`.

### Adjuntando funciones incorporadas a los valores de la política - `invoke`

Los valores de la política aceptan `invoke`, que puede utilizarse para adjuntar funciones incorporadas a las políticas.

Sigue las funciones implementadas y sus respectivos registros.

Aquí tienes la tabla en formato Markdown con la traducción al español, manteniendo los nombres técnicos sin traducir:

<table><thead><tr><th width="120">Record</th><th width="352">Nombre de la Función</th><th>Descripción</th></tr></thead><tbody><tr><td>wallet</td><td>wallet.canSpendAllChangedRouteTargets</td><td>Una ruta de acción <code>forward</code> puede ser añadida a una wallet solo si el firmante tiene acceso para <code>spend</code> la billetera objetivo de la ruta.</td></tr><tr><td>intent</td><td>intent.canReadAnyClaimWallet</td><td>Un intent puede ser leído solo si el usuario tiene permiso para leer cualquiera de las billeteras involucradas en cualquier claim de este intent como <code>source</code> o <code>target</code>.</td></tr><tr><td>intent</td><td>intent.canReadAnyClaimWalletInThread</td><td>Un intent puede ser leído solo si el usuario tiene permiso para leer cualquiera de las billeteras involucradas en cualquier claim del thread del que este intent es parte - como <code>source</code> o <code>target</code>. Para intent threads con un solo intent, esta función tiene el mismo efecto que <code>intent.canReadAnyClaimWallet</code></td></tr><tr><td>intent</td><td>intent.canSpendEveryClaimWallet</td><td>Los intents pueden ser creados solo si el firmante tiene acceso para <code>spend</code> todas las billeteras participantes de este intent - como <code>source</code> y <code>target</code>.</td></tr></tbody></table>



```tsx
// Política "wallet-mutation"
{
   "data": {
     "handle": "wallet-mutation", 
     "record": "wallet",
     "schema": "access",
     "values": [{
       "action": "create",
       "invoke": "wallet.canSpendAllChangedRouteTargets"
     }, {
       "action": "update",
       "invoke": "wallet.canSpendAllChangedRouteTargets"
     }],
     "custom": {
       ...
     },
     "access": [...],
   },   
   "hash": "...",
   "meta": {...}
}
```

Ejemplo de una política llamada `wallet-mutation` que invoca la función `wallet.canSpendAllChangedRouteTargets` para otorgar acceso para crear o actualizar una bileltera en el sistema.

### Uso de la política

Una política puede adjuntarse a las reglas de acceso de los registros del Libro Mayor haciendo referencia a su `handle`

```tsx
{
   "data": {
     "handle": "bank-wallet",
     "custom": {
       ...
     },
     "access": [{
       "policy": "bank"
     }],
   },   
   "hash": "...",
   "meta": {...}
}
```

Ejemplo de una billetera que reutiliza la política `bank` para definir restricciones de acceso a este registro.&#x20;



Las políticas también pueden reutilizarse junto con las reglas de acceso regulares.

```tsx
tsxCopy{
   "data": {
     "handle": "bank-wallet",
     "custom": {
       ...
     },
     "access": [{
       "policy": "bank"
     }, {
        "action": "spend",
        "signer": {
          "handle": "treasury"
       }
     }],
   },   
   "hash": "...",
   "meta": {...}
}
```

Ejemplo de una billetera que reutiliza la política `bank` para definir restricciones de acceso a este registro y también define su propia regla de acceso para gastar dinero.

Las políticas de seguridad son poderosas ya que centralizan las reglas de seguridad y facilitan su gestión. Consulte [Acerca de la Autorización](sobre-autorizacion.md) para más detalles.
