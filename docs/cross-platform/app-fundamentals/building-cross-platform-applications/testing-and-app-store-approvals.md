#<a name="---"></a>---
título: "Parte 6 – pruebas y aplicación tienda aprobaciones" ms.topic: artículo ms.prod: xamarin ms.assetid: 46E0578A-7EB9-C105-ABB0-A043E501F36B ms.technology: xamarin y multiplataforma autor: asb3993 ms.author: amburns ms.date: 23/03/2017
---

# <a name="part-6---testing-and-app-store-approvals"></a>Parte 6: probar y aprobaciones de tienda de aplicaciones


## <a name="testing"></a>Pruebas

Muchas aplicaciones (incluso de aplicaciones Android, en algunas tiendas) tiene que pasar un proceso de aprobación antes de publicarlos; Para probar es fundamental para garantizar la aplicación alcanza el mercado (por no hablar lo hace con los clientes). Pruebas, pueden tomar muchas formas, desde pruebas a la administración de pruebas de versiones beta a través de una amplia variedad de hardware de nivel de desarrollador unitarias.


### <a name="test-on-all-platforms"></a>En todas las plataformas de pruebas

Existen pequeñas diferencias entre admitido por .NET en Windows phone, Tablet PC y dispositivos de escritorio, así como las limitaciones de iOS que impiden que el código dinámico que se genera sobre la marcha. El plan de pruebas del código en varias plataformas como desarrollar, o programar la hora a la refactorización y actualizar el elemento de modelo de la aplicación al final del proyecto.

Siempre es recomendable utilizar el simulador o emulador para probar varias versiones del sistema operativo y también capacidades/configuraciones de dispositivo diferente.

También debe probar en tantos dispositivos de hardware físico diferente como pueda.


#### <a name="devices-in-cloud"></a>Dispositivos en la nube

El ecosistema de teléfono y Tablet PC móvil está aumentando el tiempo, que no se pueda probar en el número creciente de dispositivos disponibles. Para solucionar este problema una serie de servicios ofrece la capacidad de controlar de forma remota varios dispositivos diferentes para que las aplicaciones se pueden instalar y probar sin necesidad de invertir directamente en una gran cantidad de hardware.

[Prueba de la aplicación Centro](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest) ofrece una manera sencilla de probar aplicaciones de iOS y Android en cientos de diferentes dispositivos.


### <a name="test-management"></a>Administración de pruebas

Al probar las aplicaciones dentro de su organización o administrar un programa beta con usuarios externos, hay dos desafíos:

-   **Distribución** : administrar el proceso de aprovisionamiento (especialmente para dispositivos iOS) y obtener versiones actualizadas de software a los evaluadores.
-   **Comentarios** : recopilación de información sobre el uso de la aplicación e información detallada sobre los errores que pueden producirse.


Hay una serie de servicios de ayuda para solucionar estos problemas, debe proporcionar la infraestructura que está integrado en la aplicación para recopilar y notificar sobre el uso y errores y también lo que facilita el proceso de aprovisionamiento para ayudar a inicio de sesión y administrar sus dispositivos y evaluadores .

[Centro de aplicación de Visual Studio](/appcenter/) ofrece una solución a estos problemas, lo que proporciona información de uso de aplicaciones sofisticadas, informes de errores y distribución de la versión de prueba.



### <a name="test-automation"></a>Automatización de pruebas

Xamarin [UITest](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest) puede usarse para crear la interfaz de usuario automatizadas scripts de prueba que se pueden ejecutar localmente o cargar en [aplicación Center Test](https://docs.microsoft.com/appcenter/test-cloud/).




## <a name="unit-testing"></a>Pruebas unitarias



#### <a name="touchunit"></a>Touch.Unit

Xamarin.iOS incluye un marco de pruebas unitarias denominado Touch.Unit que sigue el estilo de JUnit/NUnit escribir pruebas.

Consulte nuestro [pruebas unitarias con Xamarin.iOS](~/ios/deploy-test/touch.unit.md) documentación para obtener más información sobre escribir pruebas y ejecutar Touch.Unit.



#### <a name="andrunit"></a>Andr.Unit

Hay un equivalente de código abierto de Touch.Unit para Android denominado Andr.Unit. Puede descargarlo desde [github](https://github.com/spouliot/Andr.Unit) y leer acerca de la herramienta en [ @spouliotdel blog](http://spouliot.wordpress.com/2011/10/30/andr-unit-joins-the-family/).




## <a name="app-store-approvals"></a>Aprobaciones de la tienda de aplicaciones

Apple y Microsoft operan el único almacén en las plataformas de: la tienda de aplicaciones y Marketplace respectivamente. Bloquear sus dispositivos e implementar un proceso de revisión de aplicación rigurosas para controlar la calidad de las aplicaciones disponibles para descargar. Naturaleza abierta de Android significa que hay una serie de opciones del almacén comprendido Play de Google, que es ampliamente disponibles y no tiene ningún proceso de revisión, para Appstore de Amazon para trabajos de Android y específica del hardware como aplicaciones de Samsung que tienen más una distribución limitada e implementar un proceso de aprobación.

Esperando a una aplicación que se debe revisar puede ser muy estrés: presiones del negocio significan a menudo las aplicaciones se envían para su aprobación con muy poco margen para el error antes de una fecha de inicio de "destino". El propio proceso puede tardar hasta dos semanas y no es necesariamente transparente: hay comentarios limitado sobre el progreso de la aplicación hasta que finalmente se rechazan o aprobado. Rechazo puede significar que falta una ventana de marketing de oportunidad, sobre todo si se produce más de una vez y pasan semanas entre la fecha de inicio original y cuando finalmente se aprueba la aplicación.



### <a name="be-prepared"></a>Prepárese

El primer fragmento de Consejo: planear con antelación el inicio de la aplicación y tener en cuenta para una posible rechazo y reenviarlos a. Cada almacén requiere crear una cuenta antes de enviar tu aplicación: hacer esto tan pronto como sea posible.
Mientras la suscripción de Google Play sólo tarda unos minutos si las aplicaciones son gratuitas, el proceso obtiene mucho más complejo si se les vende y necesita proporcione Banca e información de impuestos. Apple y procesos de Microsoft son ambos mucho más complejo que de Google, los cambios pueden tardar una semana o varias de ellas para obtener su cuenta aprobado por lo que separe el este tiempo en los planes de lanzamiento.

Una vez que se ha aprobado su cuenta, está listo para enviar una aplicación. El proceso real para enviar las aplicaciones se trata en la siguiente documentación:

-   [Publicar en App Store de iOS de Apple](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
-   [Preparar una aplicación para Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md)
-  Los desarrolladores de Windows deben visitar el [centro de desarrollo de Windows](https://developer.microsoft.com/en-us/windows/windows-apps) para obtener información acerca de enviar sus aplicaciones.


El resto de esta sección tratan aspectos que debe tener en cuenta para garantizar que la aplicación se aprueba sin las interrupciones.



### <a name="quality"></a>Calidad

Parece obvio, pero las aplicaciones suelen obtener rechazarán porque no cumplen un cierto nivel de calidad: después de todo, este es el motivo por qué los almacenes protegidos tienen un proceso de aprobación en primer lugar.

Bloqueos son una causa habitual de rechazo. Si es demasiado fácil realizar el bloqueo de aplicación, se garantiza que se rechacen. Mayoría de los desarrolladores no enviar sus aplicaciones con la expectativa de que le bloqueará, aunque lo hacen con frecuencia. Pruebe exhaustivamente la aplicación antes de enviarlo, centrarse no solo en asegurarse de que todo funciona, sino que gestionan escenarios habituales de error móviles, como problemas de red y restricciones de recursos como memoria o espacio de almacenamiento. Utilizar el simulador y los dispositivos físicos para probar: independientemente de la medida, el código se ejecuta en un simulador, solo un dispositivo puede mostrar el rendimiento real de una aplicación. Usar como muchos dispositivos diferentes que puede encontrar y dar de alta un equipo de evaluadores beta si puede, servicios de terceros pueden ayudar a administrar comentarios y la distribución beta.

Todos los sistemas operativos móviles elimina una aplicación que no se inicia con la suficiente rapidez. La longitud de tiempo permitido varía, pero en general deben tener como objetivo aplicaciones a la capacidad de respuesta en unos segundos y usar tareas en segundo plano para realizar cualquier trabajo que se tardaría más tiempo. Se rechazarán las aplicaciones que tardan demasiado tiempo en cargarse o responden no de uso normal. Siempre proporcionar comentarios del usuario cuando algo sucede en segundo plano, o la aplicación parecerá que se han bloqueado y una vez más, obtener rechazado.


### <a name="check-your-edge-cases"></a>Compruebe los casos de borde

Una captura comunes para los desarrolladores no se puede solucionar borde-casos, sobre todo aquellas que requieren volver a configurar el simulador o el dispositivo para probar correctamente. Puede ser fácil olvidarse de que no todos los clientes van a "Permitir" en la aplicación para tener acceso a su ubicación porque después de que el desarrollador ha aceptado la solicitud de una vez, nunca se pedirá nuevo. Permisos y uso de la red específicamente están centrados en durante el proceso de aprobación, lo que significa que puede dar lugar a un pequeño descuido en estas áreas de rechazo.

En la lista siguiente es un buen punto de partida para casos extremos que pueden haber pasado por alto la comprobación:

-   **Los clientes pueden "Denegar" acceso a los servicios** : especialmente en iOS, el acceso a datos, como solo se proporciona información de ubicación geográfica si el usuario concede el permiso a la aplicación. Evaluadores de aplicaciones con frecuencia deben volver a instalar la aplicación en su estado inicial y no permitir las solicitudes de permiso para asegurarse de que la aplicación se comporta de forma adecuada. Activar el permiso y off para comprobar un comportamiento correcto según los clientes cambian su opinión.
-   **Los clientes están en todas partes** – no suponga que una aplicación solo se utilizará en la ciudad o el país donde lo desarrolló! Todos los que el código funcione con coordenadas GPS, valores de fecha y hora y divisas puede afectado por configuración regional y la ubicación del cliente. Todas las plataformas ofrecen un simulador que le permite especificar distintas ubicaciones y las configuraciones regionales - usar para ubicaciones de prueba en otros hemisferios y con las referencias culturales que formatos diferentes para las fechas y monedas. Los valores de latitud y longitud pueden ser positivo o negativo, el separador decimal podría ser un punto o una coma, y las fechas se pueden aplicar formato una gran cantidad de formas - tratar con él.
-   **Las conexiones de red lentas** : los desarrolladores de aplicaciones a menudo funcionan en un 'mundo ideal' de rápido, siempre trabaja conectividad de red, lo que obviamente no es el caso en el mundo real. Pruebas con la conectividad de red lenta (por ejemplo, una mala conexión 3 G), así como sin acceso de red son esencial para garantizar que no enviar una aplicación con errores. El proceso de aprobación siempre incluirá una prueba con el dispositivo en modo de avión, por tanto, asegúrese de haber probado usted mismo.
-   **El hardware varía** – no olvide probar en el hardware más antiguo, más lento que tiene previsto admitir. Hay dos aspectos que pueden afectar a la aplicación: rendimiento, lo que podría ser inutilizable en un dispositivo anterior y compatibilidad con las características de hardware, como una cámara, micrófono, GPS, giroscopio u otro componente opcional. Las aplicaciones deben reducir correctamente (y de no bloqueo) cuando un componente no está disponible.




### <a name="guidelines-are-more-than-just-a-guide"></a>Instrucciones son algo más que simplemente 'manual'

Apple es famoso por ser estrictos respecto a su conformidad con las directrices de interfaz humana sea una de las ventajas claves de su plataforma de coherencia (y el aumento percibido de facilidad de uso). Microsoft ha tomado un enfoque similar con las aplicaciones de Windows para implementar la interfaz de usuario de estilo Metro. El proceso de aprobación para ambas plataformas implicará la aplicación que se va a evaluar para el cumplimiento de la filosofía de diseño relevantes.

Que no es la extensión decir que innovación de interfaz de usuario no es compatible o se recomienda, pero hay ciertas cosas que "simplemente no debe hacer" o la aplicación se rechazarán.

En iOS, esto incluye haciendo un uso indebido iconos integrados o mediante otras metáforas bien establecidas de forma no coherente; Por ejemplo, utilizando el icono 'crear' para algo distinto de crear nuevo contenido.

Los desarrolladores de Windows deben tener cuidados de forma similar; un error común es un error admitir correctamente el hardware nuevo botón según las directrices de Microsoft.

Anime a los diseñadores para leer y seguir las directrices de diseño para cada plataforma.



### <a name="implementing-platform-specific-features"></a>Implementar características específicas de plataforma

En cuanto a la implementación específica de la plataforma de servicios, especialmente en iOS las cosas son un poco más estrictas. Para evitar el rechazo automático por Apple, hay algunas reglas para seguir con las siguientes características de iOS:

-   **Compras desde la aplicación** : las aplicaciones deben implementar mecanismos de pago externo para productos digitales incluidas en el juego moneda, características de la aplicación, las suscripciones revistas y mucho más. aplicaciones de iOS deben usar el servicio basado en iTunes de Apple para este tipo de funcionalidad. Hay un escape - aplicaciones como el lector Kindle y algunas aplicaciones basadas en la suscripción le permiten adquirir contenido en otro lugar que se asocia a una "cuenta" que, a continuación, puede tener acceso a través de la aplicación, sin embargo en este caso la aplicación no debe contener vínculos o ser referencias a la aplicación de proceso de compra (o bien, una vez más, podrá rechazarse).
-   **copia de seguridad de iCloud** : con la llegada de iCloud, los revisores de Apple están mucho más estrictos con respecto a cómo las aplicaciones usar almacenamiento (para asegurarse de que es agradable la experiencia de copia de seguridad remoto del cliente). Aplicaciones que puede obtener rechazado desperdician capaz de copia de seguridad de espacio de almacenamiento, por lo que usar la carpeta de caché adecuadamente y otras instrucciones relacionadas con el almacenamiento de la seguimiento Apple.
-   **Quiosco** : periódicos y revistas aplicaciones son una excelente elección para quiosco de Apple, sin embargo, las aplicaciones deben implementar al menos una renovación automática suscripción y soporte técnico de descargas en segundo plano para poder ser autorizadas.
-   **Asigna** – resulta cada vez más habitual para agregar superposiciones y otras características para móviles mapas, sin embargo tenga cuidado no ocultar el mapa 'créditos' información (por ejemplo, el logotipo de Google en iOS5) que, si lo hace, se producirá en rechazo.




### <a name="manage-your-metadata"></a>Administrar los metadatos

Además de los problemas técnicos obvios que pueden dar lugar a una aplicación se rechaza, hay algo más sutiles aspectos de su envío que podría dar lugar a rechazo, especialmente en los metadatos (descripción, palabras clave e imágenes de marketing) que Enviar a la aplicación para que aparezcan en la tienda de aplicaciones o Marketplace.

-   **Imágenes** : siga las instrucciones de la plataforma para iconos de aplicación y almacenar las imágenes. No utilizar imágenes de marca comercial, hemos visto aplicaciones obtengan rechazadas porque sus iconos destacadas un dibujo de un iPhone.
-   **Marcas comerciales** : Evite utilizar las marcas comerciales distinto del suyo propio. Aplicaciones denegadas para que mencionen las marcas comerciales en la descripción de la aplicación o incluso en las palabras clave en la tienda de aplicaciones de Apple.
-   **Descripción** : no utilizar la palabra "beta" o en cualquier forma de indicar que la aplicación no está lista para usar. No se mencionan otras plataformas móviles (incluso si la aplicación es multiplataforma). Lo más importante, asegúrese de que la aplicación exactamente lo que dice que lo hace. Si una serie de características que se indican en la descripción, tenía mejor sea obvio cómo usar cada una de esas características u obtendrá un rechazo "características mencionadas en la descripción de la aplicación no están implementados".


Coloque el esfuerzo en los metadatos de la aplicación como en el desarrollo y pruebas. Las aplicaciones obtener rechazadas por infracciones en los metadatos por lo que merece la pena dedicar tiempo a realizar correctamente.



### <a name="app-stores-not-for-everyone"></a>Tiendas de aplicaciones: No para todos los usuarios

El objetivo principal de los almacenes en cada plataforma es la distribución de consumidor: la capacidad para llegar a clientes tantos como sea posible. Sin embargo, no todas las aplicaciones están dirigidas a los consumidores, hay un rápido crecimiento base de aplicaciones internas y extranet similar que requieren una distribución limitada a los empleados, proveedores y clientes. Estas aplicaciones no son "para la venta" y no necesitan aprobación, desde la distribución de los controles de programador a un grupo de usuarios cerrado.
Compatibilidad con este tipo de implementación varía según la plataforma.

Android ofrece la máxima flexibilidad en este sentido: las aplicaciones pueden instalarse directamente desde un archivo adjunto de correo electrónico o dirección URL (siempre y cuando lo permite la configuración del dispositivo). Esto significa que resulta muy fácil de crear y distribuir aplicaciones corporativas internas o publicar aplicaciones a proveedores o clientes específicos.

Apple proporciona una opción de implementación internos a los desarrolladores inscritos en iOS Developer Enterprise Program, que omite el proceso de aprobación de la tienda de aplicaciones y permite a las empresas distribuir aplicaciones internas a sus empleados.
Por desgracia esta licencia no contempla la necesidad de distribución de aplicaciones de extranet similar a otros grupos de clientes o de proveedores cerrados. [Enterprise (y Ad Hoc) implementación](~/ios/deploy-test/app-distribution/ipa-support.md)



### <a name="app-store-summary"></a>Resumen de la tienda de aplicaciones

El proceso de revisión puede ser abrumador, pero como el resto del ciclo de vida de desarrollo puede ayudar a garantizar la correcta ejecución con cierta planificación y atención al detalle. Todo consiste en tomar una unos pocos pasos sencillos: leer y comprender las directrices de interfaz de usuario debe cumplir para seguir las reglas si implementa características específicas de la plataforma, pruebe exhaustivamente (a continuación, haga algunas pruebas más) y por último, asegúrese de que los metadatos de la aplicación es correcta antes de enviar.

Una última palabra de aviso para los desarrolladores de publicación en Google Play: la falta de proceso de aprobación puede parecer que facilita el trabajo - pero sus clientes podrán incluso más exigentes que un equipo de revisión. Siga estas instrucciones como aunque la aplicación puede obtener rechazada, en caso contrario, será los clientes realizar el rechazo.

