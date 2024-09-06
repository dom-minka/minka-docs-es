---
icon: money-bill-1
---

# Intentos de pago (intents)

## Contexto

### Intentos de pago

Los intents representan una forma de ejecutar un cambio en el sistema de pago (ledger). \
Los cambios más comunes que involucran intentos de pago son los movimientos de saldos entre billeteras (wallets) en el sistema de pago en tiempo real.

El autor del intent lo construye y firma con su clave privada, asegurando así la integridad y autenticidad de su intención de pago.&#x20;

Las solicitudes de cambios o escritura (POST) a Ledger siempre contienen objeto `data` diseñada para transmitir toda la información necesaria para realizar el cambio. Esta data esta encriptada con un llave privado generando un `hash` que permite verificar el author del cambio.&#x20;

Cada mensaje está estructurado en un formato JSON y similar a JWT, separando el objeto "data", que contiene el mensaje enviado, y "meta", que incluye información sobre tiempo, firmas y contexto.

```json
{
  "hash": { ... },
  "data": { ... },
  "meta": { ... }
}
```

Los intents garantizan la atomicidad de los cambios en el ledger, lo que significa que si hay múltiples cambios, todos se persistirán completamente o no se persistirá ningún cambio en absoluto.

### Estados de intent

Los estados del intent se actualizan en función de las firmas o pruebas presentadas, lo que permite avanzar en su procesamiento hasta completarse o rechazarse.

Las firma del intent es una prueba que garantiza la autenticidad e integridad del intent.&#x20;

{% hint style="info" %}
En otras palabras, garantiza que el firmante es realmente el autor o testigo del intent y que está de acuerdo con él y que el intent no ha sido manipulado durante su transporte o mientras está almacenado.
{% endhint %}

Las firmas de los intents también son el mecanismo para lograr el acuerdo entre múltiples partes involucradas en el intent sin la necesidad de confiar en un mediador de terceros.&#x20;

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

## Interfaz REST API

{% swagger src="../../.gitbook/assets/minka-api-v2-es.yaml" path="/intents" method="post" %}
[minka-api-v2-es.yaml](../../.gitbook/assets/minka-api-v2-es.yaml)
{% endswagger %}

{% swagger src="../../.gitbook/assets/minka-api-v2-es.yaml" path="/intents/{id}" method="get" %}
[minka-api-v2-es.yaml](../../.gitbook/assets/minka-api-v2-es.yaml)
{% endswagger %}

{% swagger src="../../.gitbook/assets/minka-api-v2-es.yaml" path="/intents/{id}/proofs" method="post" %}
[minka-api-v2-es.yaml](../../.gitbook/assets/minka-api-v2-es.yaml)
{% endswagger %}

