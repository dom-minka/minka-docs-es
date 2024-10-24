# Manejo de errores

Hasta ahora hemos ignorado en su mayoría los errores o simplemente hemos devuelto valores genéricos. Sin embargo, necesitamos manejar los errores en algún momento. Para empezar, puedes consultar la [Referencia de errores](../../referencias/referencia-errores/errores-nivel-de-puente.md).

Para nosotros habrá dos tipos principales de errores. Los que responderemos directamente a todas las solicitudes de Entrada, Acción e Intención (que incluyen códigos de estado HTTP y códigos de error del Libro Mayor) y los que responderemos después del procesamiento (enviando firmas).

Puede haber muchas fuentes diferentes de errores para nuestra aplicación, como errores de validación, errores de aplicación o errores externos al comunicarse con el Core o la base de datos (problemas de red, tiempos de espera y similares). Además, al comunicarse con el Core, podemos obtener no solo tiempos de espera, sino también códigos de estado HTTP que indican problemas o respuestas exitosas que en sí mismas indican un error. Deberás cubrir todos estos casos para tu situación particular, pero daremos algunos ejemplos de varios errores que servirán como guía.

## Conclusión

Con todo lo que hemos cubierto hasta ahora, hemos construido un sistema de pagos en tiempo real entre cuentas bancarias. Ahora puedes consultar también el tutorial de iniciación de pagos.
