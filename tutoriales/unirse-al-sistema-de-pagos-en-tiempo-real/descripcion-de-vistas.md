# Descripción de Vistas

## Wallets

En la página de billeteras (**Wallets**), deberías ver la billetera de tu banco. Las billeteras en el ledger se utilizan para mantener saldos. La billetera de tu banco se utilizará como la cuenta de liquidación y representa el saldo que tienes en la red de pagos en tiempo real:

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption><p>Vista de billeteras</p></figcaption></figure>

## Bridges

Los puentes (Bridges) se utilizan para integrar tus sistemas bancarios a la red de pagos en tiempo real. Configuraremos nuestro propio puente de demostración en este capítulo para ver cómo funciona. Por ahora, esta página debería estar vacía:

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption><p>Vista de puentes </p></figcaption></figure>

## Intents

Las intenciones (Intents) de pago se utilizan para representar todos los movimientos de saldo en el sistema. Puede haber muchos tipos de intenciones en los sistemas financieros, por ahora solo tenemos dos tipos configurados: `emitir`, `destruir` y `transferir`. La lista de intenciones se puede filtrar utilizando varios criterios como origen, destino, rango de fechas, estado, etc.

Las intenciones de emisión y destrucción se utilizan para introducir o eliminar la moneda del sistema. Deberías ver 2 intenciones de emisión que se utilizaron para emitir saldos iniciales a tu banco por parte de la ACH:

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption><p>Vista de intenciones</p></figcaption></figure>

Las intenciones de transferencia se utilizan para transferencias entre clientes bancarios. Los orígenes y destinos de estas intenciones son números de cuenta bancaria representados como direcciones del ledger:

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption><p>Vista de intenciones</p></figcaption></figure>

> Las direcciones de ledger se utilizan para identificar de manera única las cuentas bancarias, utilizando un formato similar al correo electrónico, por ejemplo `svgs:1001001003@mint.dev`. Esta dirección define un tipo de cuenta bancaria, número de cuenta bancaria y nombre del banco en un formato amigable para el usuario.

Si abrimos una vista previa de una intención, podemos ver que el procesamiento de la intención consta de varios estados, y que cada intención contiene información adicional sobre los clientes bancarios:

<figure><img src="../../.gitbook/assets/image (20).png" alt=""><figcaption><p>Detalle de una intención</p></figcaption></figure>

El modelo de seguridad del ledger se basa en firmas digitales. Cada operación del ledger debe ser firmada utilizando una clave privada con permisos suficientes. Podemos examinar una intención con más detalle abriendo una vista completa utilizando el icono en la parte superior izquierda del panel de detalles o presionando la tecla `espacio` del teclado:

<figure><img src="../../.gitbook/assets/image (21).png" alt=""><figcaption><p>Línea de tiempo de una intención</p></figcaption></figure>

La línea de tiempo a la derecha muestra las pruebas recopiladas por el ledger para procesar esta intención. Cada prueba contiene información verificable que identifica a la parte que la presentó, cuándo se presentó, e información adicional como referencias a operaciones del núcleo bancario. Por ejemplo, en la captura de pantalla anterior, podemos ver que la intención fue `completada` por `ach`.



## Members

La parte de acceso del panel de control te permite gestionar usuarios y permisos. Puedes usar la página de miembros (Members) para invitar a nuevos usuarios a tu organización:

<figure><img src="../../.gitbook/assets/image (22).png" alt=""><figcaption><p>Página de Miembros</p></figcaption></figure>

## Circles

Los círculos representan grupos de usuarios, los miembros de cada círculo tienen ciertos permisos y agregar un miembro a un círculo otorga esos permisos al miembro:

<figure><img src="../../.gitbook/assets/image (23).png" alt=""><figcaption><p>Vista de circulos</p></figcaption></figure>



## ¿Qué sigue?

A continuación, instalaremos la herramienta de línea de comandos de Minka y demostraremos cómo [podemos crear nuestro primer](../creando-el-primer-pago/) pago en tiempo real.

Puedes encontrar más detalles sobre cómo hacer esto en nuestros otros **tutoriales** y **guías prácticas**.
