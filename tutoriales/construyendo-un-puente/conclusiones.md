# Conclusiones

El puente que hemos construido aquí debería darle una buena comprensión del protocolo de dos fases de confirmación y del proceso de integración de ledger en general. Tenemos una solución funcional que sincroniza dos ledgers de forma confiable, un ledger en la nube y nuestro núcleo bancario.

Aprenda más sobre todos los casos de uso y flujos detallados que se admiten en nuestra documentación técnica.

El código compartido aquí es de código abierto y puede usarlo libremente. Por supuesto, esto no es una solución final, lista para producción. Después de esto, necesita adaptar la solución a sus necesidades específicas, asegurarla adecuadamente y alojarla.

Además, nuestros SDK de puente y muestras de código abierto le brindan las herramientas necesarias para construir una integración completa, pero aún tendrá que implementar muchas cosas para que esta integración esté lista para la producción.

Todos los datos para la reconciliación están disponibles en el sistema, pero no proporciona informes de reconciliación reales. El sistema está diseñado para ser escalable utilizando procesamiento asincrónico, pero deberá ejecutar pruebas de rendimiento y configurar la solución adecuadamente para la producción. Debe almacenar secretos de forma segura en su infraestructura, construir observabilidad, monitoreo, notificaciones y muchas otras cosas que siempre debe hacer al implementar un nuevo servicio.

Minka también proporciona una solución de hub de pagos, que es un puente en la nube que resuelve todos los problemas mencionados anteriormente. Si está interesado en esta solución en lugar de construir todo usted mismo, comuníquese con su representante de ventas para obtener más información.
