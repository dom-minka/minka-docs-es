# Registrar un puente con el libro mayor

Cada integración se identifica por una clave digital, hemos creado una nueva clave en el capítulo anterior, ahora registraremos una integración utilizando esta clave con el libro mayor.

Al registrar un puente, otorgaremos permisos a nuestra clave privada recién generada para realizar operaciones específicas. De forma predeterminada, una nueva clave no tiene ningún permiso en un libro mayor.

Podemos registrar un nuevo puente en la página `Bridges` en Studio haciendo clic en el botón `Create Bridge` en la parte superior izquierda:

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption><p>Vista de puentes</p></figcaption></figure>

Necesitamos definir un nombre único (handle) para nuestra integración y necesitamos proporcionar una URL del servidor en el primer paso. Para el handle, usaremos `bridge@mintbank.dev` y usaremos la `Public URL` que se imprimió por el comando `minka bridge start`

> Tenga presente que para esta guía usamos la billetera `mintbank.dev` la invitación que usted gestión en [unirse-al-sistema-de-pagos](../unirse-al-sistema-de-pagos/ "mention") será la billetera a la que debe asignar el puente (bridge)&#x20;

> Recuerde usar en los handles el dominio, para este ejemplo `bridge`@**dominio**

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption><p>Datos del puente</p></figcaption></figure>

En el siguiente paso, registraremos la clave pública de nuestro firmante de puente que creamos anteriormente y la asignaremos a un círculo llamado `bridge@mintbank.dev`:

> Recuerde que puede usar `minka signer show [handle]` para obtener la llave pública

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption><p>Datos del firmante</p></figcaption></figure>

> Los círculos son grupos de firmantes, agregar un firmante al círculo `bridge@minkbank.dev` otorgará un conjunto de permisos a este firmante que un puente necesita para operar correctamente.

El último paso es vincular este puente a nuestra billetera de banco `mintbank.dev`:

> Tenga presente que el nombre de su billetera debe ser diferente a `mintbank.dev`

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption><p>Asignar una billetera al puente</p></figcaption></figure>

Después de hacer todo esto, nuestro puente está registrado con el libro mayor, el firmante del puente tendrá todos los permisos necesarios para que el servicio del puente opere y este puente está asignado a nuestra billetera de banco. Podemos verificar que el puente está correctamente asignado abriendo los detalles de la billetera:

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Asignar un puente a una billetera significa que esta billetera está gestionada por una autoridad externa. Antes, todas las órdenes de pago se procesaban automáticamente en esta billetera. A partir de ahora, cada operación de billetera (débito o crédito) primero se enviará a nuestro puente para su aprobación.

Las órdenes de pago no se completarán a menos que nuestro puente procese estas operaciones en un sistema externo y apruebe enviando las pruebas requeridas al libro mayor. El algoritmo que se utiliza para sincronizar a los participantes se denomina dos fases de confirmación. Veremos cómo funciona este proceso en los próximos capítulos.

> Mantenga la consola del bridge abierta, para la continuación de este tutorial, abra una consola nueva.

Ahora podemos iniciar sesión con nuestro firmante de puente en la CLI:

```powershell
$ minka ledger login

It seems you're currently logged in as teslabank
and you'll be logged out if you proceed with login

? Do you want to proceed? Yes
? Signer: bridge@mintbank.dev
? Signer password for bridge@mintbank.dev *[hidden]*

✅ Logged in as bridge@mintbank.dev.
```





