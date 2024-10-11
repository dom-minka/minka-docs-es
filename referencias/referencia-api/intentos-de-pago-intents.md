---
icon: money-bill-1
---

# intentos de Pago (Intents)

#### Intentos de pago

Los intentos de pago (intents) constituyen un mecanismo para ejecutar modificaciones en el sistema de pago (ledger). Las modificaciones más frecuentes implican transferencias de saldos entre cuentas (wallets) en el sistema de pago en tiempo real, que pueden pertenecer a participantes directos o terceros conectados al sistema.

Para procesar el intent y aplicar la modificación en el ledger, es necesario firmarlo con la llave privada. La información del cambio está contenida en el objeto "data", el cual incluye todos los detalles necesarios para efectuar la modificación. El objeto "data" se encripta, generando un hash que permite verificar la autoría del cambio.

Este proceso asegura que solo el propietario de la llave privada pueda realizar modificaciones en el ledger, manteniendo la seguridad y confiabilidad del sistema.

Cada mensaje se estructura en formato JSON, similar a JWT, separando el objeto "data", que contiene el mensaje enviado, del objeto "meta", que incluye información sobre tiempo, firmas y contexto.

```json
{
  "hash": { ... },
  "data": { ... },
  "meta": { ... }
}
```

Los intents garantizan la atomicidad de los cambios en el ledger, lo que significa que si hay múltiples cambios, todos se persistirán completamente o no se persistirá ningún cambio en absoluto.

#### Claims (Transacciónes)

Los intents se componen de claims que representan una transacción ejecutada dentro en el flujo de pago representado por el intent.

Cada claim tiene una estructura simple que se utiliza para representar un cambio. Por ejemplo, un claim que representa una transferencia de dinero tendrá la siguiente estructura simple que incluye monto, moneda, tipo de transacción, fuente y desitno.

```json

    "claims": [
      {
        "action": "transfer",
        "amount": 4200,
        "symbol": {
          "handle": "usd"
        },
        "source": {
          "handle": "svgs:1001001345@teslabank.io"
        },
        "target": {
          "handle": "svgs:1001009422@mintbank.dev"
        }
      }
    ],

```

{% hint style="info" %}
Este modelo permite construir interfaces que son agnósticas al caso de uso, la moneda o la red.
{% endhint %}

#### Estados de intento de pago

Los estados del intent se actualizan en función de las firmas o pruebas presentadas, lo que permite avanzar en su procesamiento hasta completarse o rechazarse.

Las firma del intent es una prueba que garantiza la autenticidad e integridad de intento de pago.

Las firmas de los intents también son el mecanismo para lograr el acuerdo entre múltiples partes involucradas en el flujo de pago sin la necesidad de confiar en un mediador de terceros.

En nuestro ejemplo de un intent de intercambio donde están involucradas 2 cuentas (wallets), se requerirán las firmas de los propietarios de ambas cuentas para poder validar el intent.

Ambas partes saben que el intent necesita 2 firmas para ser validado y cada una decidirá de manera independiente si los términos del intercambio son aceptables y lo confirmará con su firma.

Los estados de intento de pago son y se realizan agregando pruebas (proofs) al intent:

| Estado      |                                                                                                                                         |
| ----------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| `Pending`   | El intent ha sido enviado pero aún está en proceso. Los cambios en los datos del ledger no se han persistido todavía.                   |
| `Prepared`  | El intent está preparado por todos los participantes y puede ser procesado.                                                             |
| `Committed` | El intent se procesó con éxito. Los cambios en los datos del ledger están persistidos y son irreversibles. Eventualmente se completará. |
| `Completed` | El intent se procesó correctamente.                                                                                                     |
| `Rejected`  | El procesamiento del intent falló. Los cambios en los datos del ledger son rechazados y no pueden ser persistidos.                      |
