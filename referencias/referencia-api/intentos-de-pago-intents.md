---
icon: money-bill-1
---

# Intentos de pago (intents)

## Contexto

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

{% swagger src="../../.gitbook/assets/minka-api-v2-es.yaml" path="/intents" method="post" %}
[minka-api-v2-es.yaml](../../.gitbook/assets/minka-api-v2-es.yaml)
{% endswagger %}

{% swagger src="../../.gitbook/assets/minka-api-v2-es.yaml" path="/intents/{id}" method="get" %}
[minka-api-v2-es.yaml](../../.gitbook/assets/minka-api-v2-es.yaml)
{% endswagger %}

{% swagger src="../../.gitbook/assets/minka-api-v2-es.yaml" path="/intents/{id}/proofs" method="post" %}
[minka-api-v2-es.yaml](../../.gitbook/assets/minka-api-v2-es.yaml)
{% endswagger %}

## Intents

&#x20;Un intent en Minka Ledger es una intención de pago compuesta por varios transacciónes.&#x20;

Los intents permiten la gestión de pagos de manera estructurada y segura.

