---
icon: arrows-rotate-reverse
---

# Débitos y Créditos (bridge)

## Contexto

Un puente es un conector bidireccional entre sistemas de pago que actúa como traductor entre protocolos. En el caso de un participante, conecta su core transaccional con el sistema de pagos utilizando el protocolo Minka.

La interfaz necesaria que debe exponer el participante es la capacidad de iniciar movimientos de débito y crédito en su núcleo transaccional.

### Protocolo

Utilizaremos el protocolo de compromiso en dos fases que orquestará su core y de otros participantes.

El protocolo de compromiso en dos fases es un método utilizado en sistemas de bases de datos distribuidas para lograr atomicidad entre múltiples nodos involucrados en una transacción.

El protocolo consta de dos fases: una fase de preparación y una fase de compromiso.

La fase de preparación debe validar y asegurar que una operación pueda ejecutarse, pero una operación no se considera completada hasta que se reciba una solicitud de compromiso o anulación del libro mayor. Si un participante confirma que una preparación fue exitosa, debe garantizar que el compromiso de esta operación no pueda fallar.

Todas las llamadas en el protocolo son asíncronas para garantizar una respuesta rápida y una buena visibilidad del estado del procesamiento.

El sistema llama a los puntos finales de débito y crédito del participante, mientras que las respuestas se envían al sistema agregando pruebas a la interfaz de intenciones.

### Interfaces

A continuación se presenta un resumen de las API REST que expone el puente para implementar el protocolo de compromiso en dos fases:

<table><thead><tr><th width="201">Endpoint</th><th>Descripción</th><th></th></tr></thead><tbody><tr><td><strong>POST /v2/credits</strong></td><td>Se llama durante el compromiso en dos fases cuando el Puente necesita <strong>preparar un crédito</strong>. </td><td>El Puente debe verificar si la cuenta existe, está activa y realizar otras comprobaciones necesarias para asegurar que la intención pueda proceder.</td></tr><tr><td><strong>POST /v2/credits/:handle/commit</strong></td><td>Se llama durante el compromiso en dos fases cuando el Puente necesita <strong>confirmar un crédito</strong>. </td><td>Esta solicitud debe tener éxito y se considera exitosa incluso si hay un error durante el procesamiento. En este momento se realiza crédito en su sistema. </td></tr><tr><td><strong>POST /v2/credits/:handle/abort</strong></td><td>Se llama durante el compromiso en dos fases cuando el Puente necesita <strong>anular un crédito</strong>. </td><td>Esta solicitud se invoca si hay un problema durante el procesamiento de la intención. No puede fallar, al igual que la confirmación, y debe revertir completamente la intención.</td></tr><tr><td><strong>POST /v2/debits</strong></td><td>Se llama durante el compromiso en dos fases cuando el Puente necesita <strong>preparar un débito</strong>. </td><td>El Puente debe verificar si la cuenta existe, está activa y realizar otras comprobaciones necesarias para asegurar que la intención pueda proceder. También necesita retener o reservar los fondos necesarios en la cuenta de origen.</td></tr><tr><td><strong>POST /v2/debits/:handle/commit</strong></td><td>Se llama durante el compromiso en dos fases cuando el Puente necesita <strong>confirmar un débito</strong>.</td><td>Esta solicitud debe tener éxito y se considera exitosa incluso si hay un error durante el procesamiento. </td></tr><tr><td><strong>POST /v2/debits/:handle/abort</strong></td><td>Se llama durante el compromiso en dos fases cuando el Puente necesita <strong>anular un débito</strong>.</td><td>Igual que para el crédito, pero potencialmente también necesita liberar los fondos.</td></tr></tbody></table>

## Referencia API
