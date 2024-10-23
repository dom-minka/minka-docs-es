# Integración bancaria (banca abierta) con Bridge SDK

Este tutorial te muestra cómo conectar un banco a una red ACH basada en la nube construida utilizando el Minka Ledger. Para este propósito, implementaremos un servicio puente que conecta los sistemas centrales del banco con el Ledger en la nube. Utilizaremos la biblioteca `@minka/bridge-sdk` para hacer que el proceso de integración sea más rápido.

Como siempre al construir un servicio listo para producción, sigue tus mejores prácticas habituales en diseño y seguridad de aplicaciones.

💡 El código está escrito usando TypeScript, pero también puedes usar JavaScript.

Aquí hay un diagrama que te ayudará a entender el papel del Puente en la arquitectura general del sistema.



```mermaid
graph TD
  b1c(Núcleo del Banco 1) --> b1b(Puente del Banco 1)
  m1(Móvil 1) --> b1mb(Backend Móvil del Banco 1)
  b1mb --> b1b
  b1bdb(BD del Puente del Banco 1) --> b1b

  b1b --> l(Ledger)
  l --> b2b(Puente del Banco 2)
  b2b --> b2c(Núcleo del Banco 2)
  b2b --> b2mb(Backend Móvil del Banco 2)
  b2b --> b2bdb(BD del Puente del Banco 2)
  b2mb --> m2(Móvil 2)
```

