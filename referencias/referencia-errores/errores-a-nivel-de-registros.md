---
icon: chart-network
---

# Errores a nivel de registros

Errores relacionados con los registros del libro mayor.&#x20;

Se utilizan cuando hay algún problema con los registros solicitados o enviados.

Estos errores ocurren durante el procesamiento de solicitudes de API, por lo que también tienen códigos de estado HTTP de respuesta asociados.

<table><thead><tr><th width="108">Status</th><th width="296">Reason</th><th>Detail</th></tr></thead><tbody><tr><td><code>404</code></td><td><strong><code>record.not-found</code></strong></td><td>El registro solicitado por el identificador no existe en la base de datos del libro mayor.</td></tr><tr><td><code>422</code></td><td><strong><code>record.relation-not-found</code></strong></td><td>El registro relacionado con en el registro recibido no existe en la base de datos del libro mayor.</td></tr><tr><td><code>409</code></td><td><strong><code>record.duplicated</code></strong></td><td>Ya existe un registro con el mismo identificador en la base de datos del libro mayor.</td></tr><tr><td><code>422</code></td><td><strong><code>record.schema-invalid</code></strong></td><td>El registro recibido no coincide con el esquema de registro esperado. </td></tr><tr><td><code>422</code></td><td><strong><code>record.invalid</code></strong></td><td>El esquema del registro es correcto pero no puede ser procesado por el servidor.</td></tr><tr><td><code>422</code></td><td><strong><code>record.drop-rejected</code></strong></td><td>La eliminación del registro no puede ser procesada por el servidor debido a una validación interna.</td></tr><tr><td><code>422</code></td><td><strong><code>labels-policy-violation</code></strong></td><td>Las etiquetas no pueden ser procesadas debido a una violación de política. Se refiere a validaciones. </td></tr></tbody></table>
