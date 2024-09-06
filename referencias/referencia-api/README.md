---
icon: webhook
---

# Referencia API

##

La referencia de la API de Minka Ledger está basada en la especificación OpenAPI 3.0 y se puede encontrar aquí en formato YAML.

{% file src="../../.gitbook/assets/minka-api-v2-es.yaml" %}

Las interacciones con el ledger utilizado por el sistema de pagos se basan en una API RESTful madura que opera a través de mensajes en formato JSON.

Cada interacción está firmada criptográficamente y sigue el formato de compartir el objeto de datos verificado por un hash y metadatos referenciados, con el fin de asegurar la no repudación, verificación y auditabilidad.

<pre class="language-json"><code class="lang-json"><strong>{
</strong>  "hash": { ... },
  "data": { ... },
  "meta": { ... }
}
</code></pre>

La documentación se divide en dos segmentos: la iniciación de intenciones de pago y la provisión de una interfaz de puente en el lado de los participantes para garantizar la orquestación de pagos.
