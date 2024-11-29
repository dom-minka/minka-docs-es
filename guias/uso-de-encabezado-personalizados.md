# Uso de encabezado personalizados

## Añadiendo Headers Personalizados al SDK

### Descripción General

El SDK permite agregar headers HTTP personalizados que se incluirán automáticamente en todas las peticiones. Esto es útil cuando necesitamos incluir información adicional de autenticación o configuración.

### Método setHeader()

El SDK proporciona el método `setHeader()` para añadir headers personalizados:

```typescript
sdk.setHeader('nombreHeader', 'valorHeader');
```

#### Ejemplos de Uso

```typescript
// Añadir un header de autenticación
sdk.setHeader('clientId', 'mi-id-cliente');

// Añadir un header de configuración
sdk.setHeader('x-custom-config', 'valor-configuracion');

// Añadir un header de rastreo
sdk.setHeader('x-trace-id', 'id-123456');
```

### Configuración mediante Options

También es posible configurar los headers directamente en el objeto de opciones al inicializar el SDK:

```typescript
const ledger: LedgerClientOptions = {
  ledger: {
    handle: config.LEDGER_HANDLE,
    signer: {
      format: 'ed25519-raw',
      public: config.LEDGER_PUBLIC_KEY,
    },
  },
  headers: {
    'clientId': config.CLIENT_ID,
    'clientSecret': config.CLIENT_SECRET
  },
  server: config.LEDGER_SERVER,
  ... 
}
```

Esta forma de configuración es especialmente útil cuando necesitas establecer múltiples headers junto con otras opciones del SDK en el momento de la inicialización.

### Características

* Los headers se mantienen durante toda la vida útil del SDK
* Se aplican automáticamente a todas las peticiones
* No hay límite en la cantidad de headers personalizados

### Consideraciones

* Los nombres de los headers son case-insensitive
* Los valores deben ser strings
* Los headers se envían en todas las peticiones al servidor
* Es importante no exponer información sensible en headers innecesarios
