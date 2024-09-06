---
icon: spider-web
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Errores genéricos de API

Estos errores ocurren durante el procesamiento de solicitudes de API.

Surgen de la capa inicial de manejo de solicitudes, que no es consciente de la semántica de los registros del libro mayor. Cada error está asociado con un código de estado de respuesta HTTP específico.

<table><thead><tr><th width="102">Status</th><th>Reason</th><th>Detail</th></tr></thead><tbody><tr><td><code>400</code></td><td><strong><code>api.body-malformed</code></strong></td><td>Formato de cuerpo de solicitud inválido, no se puede analizar como JSON.</td></tr><tr><td><code>400</code></td><td><strong><code>api.query-malformed</code></strong></td><td>Formato de parámetro de consulta de solicitud inválido, usado cuando los parámetros de la solicitud no se pueden analizar.</td></tr><tr><td><code>404</code></td><td><strong><code>api.route-not-found</code></strong></td><td>El método y la ruta solicitados no existen</td></tr><tr><td><code>408</code></td><td><strong><code>api.request-timeout</code></strong></td><td>El procesamiento de la solicitud tomó demasiado tiempo.</td></tr><tr><td><code>429</code></td><td><strong><code>api.rate-limit-exceeded</code></strong></td><td>El número de solicitudes excedió el límite de velocidad. Intente nuevamente más tarde.</td></tr><tr><td><code>500</code></td><td><strong><code>api.unexpected-error</code></strong></td><td>Error inesperado al procesar la solicitud.</td></tr></tbody></table>
