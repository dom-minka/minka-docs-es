---
icon: house
---

# Introducción

Esta guía ofrece información sobre cómo conectar su sistema central a un sistema de pagos inmediatos como un participante directo.

El componente que permite la conectividad entre su sistema y el sistema de pagos lo llamamos "puente" y actúa esencialmente como un “Hub de Pagos”.

Construir el puente es simple e incluye la creación de una interfaz API REST, la estructuración de mensajes en formato JSON, y la gestión de claves privadas para firma de los mensajes.

El puente y las interacciones con el sistema de pagos se basan en el protocolo de Minka, que es agnóstico a casos de uso o redes de pago. Esto permite al participante utilizar una única conexión para habilitar múltiples casos de uso de pago o acceder a diferentes redes de pago.

La mayor parte de esta guía está destinada tanto a equipos de tecnología como de producto, sin requerir un conocimiento técnico profundo.

Por otro lado, la implementación del protocolo en su entorno productivo requiere conocimiento de tecnologías web, APIs REST y su propio sistema core.

{% hint style="info" %}
Los participantes directos del sistema de pagos inmediatos suelen ser Instituciones Financieras, Fintechs, Billeteras o Cooperativas de Crédito.
{% endhint %}

{% hint style="info" %}
La documentación se basa en el marco de documentación moderno - [Diataxis](https://www.diataxis.fr/).
{% endhint %}
