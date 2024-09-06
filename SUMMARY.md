# Table of contents

## Home

* [Introducción](README.md)
* [Resumen rapido](home/resumen-rapido.md)
* [Sobre la documentacion](home/sobre-la-documentacion.md)

## Tutoriales

* [Unirse al sistema de pagos](tutoriales/unirse-al-sistema-de-pagos-en-tiempo-real/README.md)
  * [Ingreso a la herramienta de administración - Studio](tutoriales/unirse-al-sistema-de-pagos-en-tiempo-real/ingreso-a-la-herramienta-de-administracion-studio.md)
  * [Descripción de Vistas](tutoriales/unirse-al-sistema-de-pagos-en-tiempo-real/descripcion-de-vistas.md)
* [Primer pago](tutoriales/creando-el-primer-pago/README.md)
  * [Instalación del CLI](tutoriales/creando-el-primer-pago/instalacion-del-cli.md)
  * [Conectándose al ledger](tutoriales/creando-el-primer-pago/conectandose-al-ledger.md)
  * [Creando una intención](tutoriales/creando-el-primer-pago/creando-una-intencion.md)
  * [Depurando solicitudes al ledger](tutoriales/creando-el-primer-pago/depurando-solicitudes-al-ledger.md)
* [Orchestrando participantes externos](tutoriales/integrando-con-un-riel-de-sistema-de-pago-de-tiempo-real/README.md)
  * [Creación de una clave privada](tutoriales/integrando-con-un-riel-de-sistema-de-pago-de-tiempo-real/creacion-de-una-clave-privada.md)
  * [Iniciar un puente local](tutoriales/integrando-con-un-riel-de-sistema-de-pago-de-tiempo-real/iniciar-un-puente-local.md)
  * [Registrar un puente con el libro mayor](tutoriales/integrando-con-un-riel-de-sistema-de-pago-de-tiempo-real/registrar-un-puente-con-el-libro-mayor.md)
  * [Crear una orden de pago](tutoriales/integrando-con-un-riel-de-sistema-de-pago-de-tiempo-real/crear-una-orden-de-pago.md)
  * [Preparar un crédito](tutoriales/integrando-con-un-riel-de-sistema-de-pago-de-tiempo-real/preparar-un-credito.md)
  * [Confirmar un crédito](tutoriales/integrando-con-un-riel-de-sistema-de-pago-de-tiempo-real/confirmar-un-credito.md)
  * [Abortar un crédito](tutoriales/integrando-con-un-riel-de-sistema-de-pago-de-tiempo-real/abortar-un-credito.md)
* [Construyendo un puente](tutoriales/construyendo-un-puente/README.md)
  * [Prerequisitos](tutoriales/construyendo-un-puente/prerequisitos.md)
  * [Procesar un Pago](tutoriales/construyendo-un-puente/procesar-un-pago.md)
  * [Proyecto Puente](tutoriales/construyendo-un-puente/proyecto-puente.md)
  * [Protocolo de 2 fases](tutoriales/construyendo-un-puente/protocolo-de-2-fases/README.md)
    * [Endpoints expuestas por el puente](tutoriales/construyendo-un-puente/protocolo-de-2-fases/endpoints-expuestas-por-el-puente.md)
    * [Preparación del crédito](tutoriales/construyendo-un-puente/protocolo-de-2-fases/preparacion-del-credito.md)
    * [Confirmación del crédito](tutoriales/construyendo-un-puente/protocolo-de-2-fases/confirmacion-del-credito.md)
    * [Abortando créditos](tutoriales/construyendo-un-puente/protocolo-de-2-fases/abortando-creditos.md)
    * [Preparación de débito](tutoriales/construyendo-un-puente/protocolo-de-2-fases/preparacion-de-debito.md)
    * [Confirmación de debito](tutoriales/construyendo-un-puente/protocolo-de-2-fases/confirmacion-de-debito.md)
    * [Abortando débito](tutoriales/construyendo-un-puente/protocolo-de-2-fases/abortando-debito.md)
  * [Proxy de solicitudes de ledger](tutoriales/construyendo-un-puente/proxy-de-solicitudes-de-ledger.md)
  * [Conclusiones](tutoriales/construyendo-un-puente/conclusiones.md)

## Guias

* [Cómo generar llaves y firmas](guias/como-generar-claves-de-firma.md)

## Referencias

* [Referencia API](referencias/referencia-api/README.md)
  * [Intentos de pago (intents)](referencias/referencia-api/intentos-de-pago-intents.md)
  * [Débitos y Créditos (bridge)](referencias/referencia-api/debitos-y-creditos-bridge.md)
* [Referencia Errores](referencias/referencia-errores/README.md)
  * [Errores de seguridad](referencias/referencia-errores/errores-de-seguridad.md)
  * [Errores genéricos de API](referencias/referencia-errores/errores-genericos-de-api.md)
  * [Errores de integridad criptográfica](referencias/referencia-errores/errores-de-integridad-criptografica.md)
  * [Errores nivel de puente](referencias/referencia-errores/errores-nivel-de-puente.md)
  * [Errores a nivel de registros](referencias/referencia-errores/errores-a-nivel-de-registros.md)
  * [Errores de libro mayor (core)](referencias/referencia-errores/errores-de-libro-mayor-core.md)

## Explicaciones

* [Sobre conceptos basicos](explicaciones/sobre-conceptos-basicos/README.md)
  * [Intentos de pagos](explicaciones/sobre-conceptos-basicos/intentos-de-pagos.md)
* [Sobre seguridad](explicaciones/sobre-seguridad/README.md)
  * [Sobre autenticación](explicaciones/sobre-seguridad/sobre-autenticacion/README.md)
    * [Autenticación mediante las firmas](explicaciones/sobre-seguridad/sobre-autenticacion/autenticacion-mediante-las-firmas.md)
    * [Autenticación con token JWT](explicaciones/sobre-seguridad/sobre-autenticacion/autenticacion-con-token-jwt.md)
  * [Sobre autorización](explicaciones/sobre-seguridad/sobre-autorizacion.md)
  * [Sobre políticas de seguridad](explicaciones/sobre-seguridad/sobre-politicas-de-seguridad.md)
