# Protocolo de 2 fases

## Protocolo de dos fases de confirmación

El puente actúa como un conector bidireccional entre sistemas de pago. Todas las características del ledger están disponibles como APIs REST, un estándar de interoperabilidad entre sistemas.

El puente expone APIs de protocolo de dos fases de confirmación para habilitar la entrega de eventos del ledger al puente. El propósito principal de estos puntos finales es realizar operaciones en el lado del banco en respuesta a eventos que ocurren fuera del banco. Por ejemplo, acreditar cuentas de usuarios como respuesta a un pago entrante.

El protocolo de dos fases de confirmación es un protocolo utilizado en sistemas de bases de datos distribuidos para lograr **atomicidad** **a través de múltiples nodos** involucrados en una transacción. Estamos utilizando este protocolo para asegurarnos de que los sistemas financieros múltiples procesen las transacciones de manera consistente.

Las confirmaciones enviadas al ledger deben ser **pruebas** hechas con una clave privada de un participante y contener una **referencia de transacción** de la operación realizada por el participante como evidencia. Las pruebas garantizan un nivel muy alto de seguridad en el sistema, nos permiten almacenar información sobre los participantes iniciadores junto con las transacciones, lo que hace que todo el sistema sea completamente auditable y garantiza la no repudación de todas las transacciones.

Registrar referencias de transacciones de sistemas externos garantiza que podemos lograr un proceso de reconciliación completamente automatizado. Todas las referencias están disponibles en el sistema y la referencia cruzada de operaciones se realiza por id, eliminando la necesidad de adivinar y evitando la necesidad de diversas heurísticas.

Los errores se manejan de manera similar, se debe enviar una prueba con detalles del error al ledger. En caso de que el puente esté inactivo, el ledger va a **reintentar** las solicitudes.

> Cada solicitud enviada por el ledger tiene un id único que sirve como **token de idempotencia** para evitar operaciones dobles. Este id se envía en el campo `handle` de las solicitudes entrantes.

Una implementación de lógica de reintento robusta es muy importante para asegurar que el protocolo de dos fases de confirmación funcione de manera confiable. Es por eso que el proyecto del puente viene con un componente de programador robusto que maneja la idempotencia y los reintentos de forma predeterminada.

El protocolo tiene dos fases: una fase de **preparación** y una fase de **confirmación**. La fase de **preparación** debe validar y asegurarse de que una operación puede ser ejecutada, pero una operación no se considera completa hasta que se recibe una solicitud de **confirmación** o **aborto** del ledger. Si un participante confirma que la preparación fue exitosa, **debe** asegurarse de que un compromiso de esta operación **no puede fallar**. Cualquier posible fallo debe ser resuelto por el participante.

Este protocolo permite al ledger coordinar transacciones entre múltiples participantes, solicita a todos los participantes que preparen la operación y **la confirma** si todos los participantes prepararon exitosamente. Si algunos participantes fallan, el ledger enviará una solicitud de **aborto** a todos para que todos realicen un rollback.

Para resumir, hay dos cosas importantes que entender sobre el protocolo de dos fases de confirmación:

1. Las solicitudes de preparación no son finales, aún pueden ser revertidas por el ledger
2. Si se recibe una solicitud de confirmación después de una preparación exitosa, debe tener éxito. La confirmación después de una preparación exitosa **no debe fallar**, el ledger va a reintentarla hasta que tenga éxito.

> Los bancos pueden implementar la fase de preparación de varias maneras. Puede ser una **reserva** de fondos en el núcleo bancario o una **transacción** que podría ser revertida en caso de que la entrada sea abortada.





