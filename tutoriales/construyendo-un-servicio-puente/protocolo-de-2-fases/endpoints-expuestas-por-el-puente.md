# Endpoints expuestas por el puente

A continuación se muestra un resumen de las APIs REST que el puente expone para implementar el protocolo de dos fases de confirmación:

*   **POST /v2/credits**

    Llamado durante el protocolo de dos fases de confirmación cuando el puente necesita **preparar un crédito** de entrada. El puente debe verificar si la cuenta existe, está activa y realizar otras comprobaciones necesarias para asegurarse de que la intención puede proceder.
*   **POST /v2/credits/:handle/commit**

    Llamado durante el protocolo de dos fases de confirmación cuando el puente necesita **confirmar un crédito** de entrada. Esta solicitud debe tener éxito y se considera exitosa incluso si hay un error al procesar. En ese caso, el banco necesita resolver el problema manualmente.
*   **POST /v2/credits/:handle/abort**

    Llamado durante el protocolo de dos fases de confirmación cuando el puente necesita **abortar un crédito** de entrada. Esta solicitud se llama si hay un problema al procesar la intención. No puede fallar como la confirmación y debe revertir completamente la intención.
*   **POST /v2/debits**

    Llamado durante el protocolo de dos fases de confirmación cuando el puente necesita **preparar un débito** de entrada. Lo mismo que para el crédito, pero también debe mantener o reservar los fondos necesarios en la cuenta de origen.
*   **POST /v2/debits/:handle/commit**

    Llamado durante el protocolo de dos fases de confirmación cuando el puente necesita **confirmar un débito** de entrada. Lo mismo que para el crédito.
*   **POST /v2/debits/:handle/abort**

    Llamado durante el protocolo de dos fases de confirmación cuando el puente necesita **abortar un débito** de entrada. Lo mismo que para el crédito, pero potencialmente también debe liberar los fondos.

El proyecto del puente expone adaptadores que nos permiten proporcionar lógica personalizada requerida para procesar mensajes de dos fases de confirmación en los sistemas bancarios centrales. Implementaremos esos adaptadores en los siguientes capítulos.
