---
title: 'Parte 6: Pruebas y aprobaciones de App Store'
description: Este documento describe cómo probar un aplicación multiplataforma en el dispositivo, administrar casos de prueba, automatice las pruebas, ejecutar pruebas unitarias y trabajar a través del proceso de envío de la aplicación.
ms.prod: xamarin
ms.assetid: 46E0578A-7EB9-C105-ABB0-A043E501F36B
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 0faf7c9e4ff7c96cdfd25ab6d6658726ef247b32
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61275412"
---
# <a name="part-6---testing-and-app-store-approvals"></a>Parte 6: Pruebas y aprobaciones de App Store

## <a name="testing"></a>Pruebas

Muchas aplicaciones (incluso las aplicaciones Android, en algunas tiendas) tendrá que pasar un proceso de aprobación antes de que se publiquen; por lo que las pruebas son fundamental para garantizar la aplicación alcanza el mercado (por no hablar se realiza correctamente con sus clientes). Las pruebas pueden tomar muchas formas, desde pruebas a la administración de pruebas beta a través de una amplia variedad de hardware de unidades de nivel de desarrollador.

### <a name="test-on-all-platforms"></a>En todas las plataformas de pruebas

Hay ligeras diferencias entre lo que admita .NET en Windows phone, tableta y los dispositivos de escritorio, así como limitaciones de iOS que impiden que el código dinámico se genera sobre la marcha. El plan de pruebas del código en varias plataformas, como desarrollar, o programar la hora de refactorizar y actualizar el elemento de modelo de la aplicación al final del proyecto.

Siempre es recomendable usar el simulador o emulador para probar varias versiones del sistema operativo y también distintas capacidades y configuraciones de dispositivos.

También debe probar en tantos dispositivos de hardware físicos diferentes como pueda.

#### <a name="devices-in-cloud"></a>Dispositivos en la nube

El ecosistema móvil, teléfono y tableta crece constantemente, que no se pueda probar en el número creciente de dispositivos disponibles. Para solucionar este problema una serie de servicios ofrece la capacidad de controlar distintos dispositivos de forma remota para que se pueden instalar aplicaciones y probadas sin necesidad de invertir directamente en una gran cantidad de hardware.

[App Center Test](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest) ofrece una forma sencilla para probar aplicaciones de iOS y Android en cientos de dispositivos diferentes.

### <a name="test-management"></a>Administración de pruebas

Al probar las aplicaciones de su organización o administrar un programa beta con usuarios externos, existen dos desafíos:

- **Distribución** : administrar el proceso de aprovisionamiento (especialmente para dispositivos iOS) y obtener versiones actualizadas de software a los evaluadores.
- **Comentarios** : recopilación de información sobre el uso de la aplicación así como información detallada sobre los errores que pueden producirse.


Hay una serie de servicios de ayuda para solucionar estos problemas, proporcionando la infraestructura que se basa en la aplicación para recopilar e informar sobre errores y uso, y también optimiza el proceso de aprovisionamiento para ayudar a suscripción y administrar sus dispositivos y los evaluadores .

[Visual Studio App Center](/appcenter/) ofrece una solución a estos problemas, que proporciona información de uso de aplicaciones sofisticada, los informes de bloqueo y distribución de la versión de prueba.

### <a name="test-automation"></a>Automatización de prueba

Xamarin [UITest](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest) puede usarse para crear scripts de prueba que se pueden ejecutar localmente o cargar en interfaz de usuario automatizadas [App Center Test](https://docs.microsoft.com/appcenter/test-cloud/).

## <a name="unit-testing"></a>Pruebas unitarias

### <a name="touchunit"></a>Touch.Unit

Xamarin.iOS incluye un marco de pruebas unitarias denominado Touch.Unit que sigue el estilo de JUnit/NUnit escribir pruebas.

Consulte nuestra [las pruebas unitarias con Xamarin.iOS](~/ios/deploy-test/touch.unit.md) documentación para obtener más información sobre las pruebas de escritura y ejecución Touch.Unit.

### <a name="andrunit"></a>Andr.Unit

Hay un equivalente de código abierto de Touch.Unit para Android llamado Andr.Unit. Puede descargarlo desde [github](https://github.com/spouliot/Andr.Unit) y obtenga información sobre la herramienta en [ @spouliotdel blog](http://spouliot.wordpress.com/2011/10/30/andr-unit-joins-the-family/).

## <a name="app-store-approvals"></a>Aprobaciones de App Store

Apple y Microsoft operan el único almacén en sus plataformas: la App Store y Marketplace, respectivamente. Bloquear sus dispositivos e implementar un proceso de revisión de aplicación rigurosas para controlar la calidad de las aplicaciones disponibles para su descarga. Naturaleza abierta de Android, significa que hay una serie de opciones de almacén que abarcan desde Google Play, que es ampliamente disponibles con ningún proceso de revisión, que Appstore de Amazon para Android y específicos del hardware esfuerzos como aplicaciones de Samsung que tienen más una distribución limitada e implementar un proceso de aprobación.

Esperando a una aplicación para su revisión puede ser muy estresante: a menudo, las presiones del negocio implican aplicaciones que se envían para su aprobación con muy poco margen de error antes de una fecha de lanzamiento de "destino". El propio proceso puede tardar hasta dos semanas y no es necesariamente transparente: hay comentarios limitado sobre el progreso de la aplicación hasta que finalmente se rechazado o aprobado. Rechazo puede significar que falta una ventana de marketing de oportunidades, especialmente si produce más de una vez y pasan semanas entre la fecha de lanzamiento original y, por último, se aprueba la aplicación.

### <a name="be-prepared"></a>Prepárese

La primera parte del Consejo: planear el lanzamiento de la aplicación con antelación y tener en cuenta un posible rechazo y reenviarlos a. Cada almacén, deberá crear una cuenta antes de enviar su aplicación: hacerlo tan pronto como sea posible.
Mientras la suscripción de Google Play sólo tarda unos minutos si las aplicaciones son gratuitas, el proceso obtiene mucho más complicado si vende ellos y necesita proporcionar banca y la información fiscal. Apple y los procesos de Microsoft son ambos mucho más complicado que de Google, que puede tardar una semana o más para que tu cuenta aprobada por lo que esta vez tenerse en cuenta los planes de lanzamiento.

Una vez aprobada su cuenta, está listo para enviar una aplicación. Se trata el proceso real para enviar aplicaciones en la siguiente documentación:

- [Publicar en App Store de iOS de Apple](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [Preparar una aplicación de Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md)
- Los desarrolladores de Windows deben visitar el [centro de desarrollo de Windows](https://developer.microsoft.com/windows/windows-apps) para obtener información acerca de enviar sus aplicaciones.

El resto de esta sección tratan las cosas que debe tener en cuenta para asegurarse de que la aplicación se aprueba sin las interrupciones.

### <a name="quality"></a>Calidad

Parece obvio, pero las aplicaciones suelen obtener rechazarán porque no cumplen un cierto nivel de calidad: después de todo, este es el motivo por qué los almacenes protegidos tienen un proceso de aprobación en primer lugar.

Los bloqueos son un motivo habitual de rechazo. Si resulta demasiado fácil cometer el bloqueo de aplicación, se garantiza que se rechace. La mayoría de los desarrolladores no envían sus aplicaciones con la expectativa de que le bloqueará, aunque a menudo hacen. Pruebe la aplicación exhaustivamente antes de enviarlo, centrándose no solo en lo que todo funciona, pero también que gestionan escenarios habituales de error móviles, como problemas de red y las restricciones de recursos, como memoria o espacio de almacenamiento. Usar el simulador y dispositivos físicos para probar, independientemente de cómo se ejecuta código en un simulador, solo los dispositivos pueden demostrar el rendimiento real de una aplicación. Usar como muchos dispositivos diferentes que puede encontrar y dar de alta un equipo de evaluadores de beta si puede, servicios de terceros pueden ayudar a administrar comentarios y la distribución beta.

Todos los sistemas operativos móviles terminará una aplicación que no se inicia con la rapidez suficiente. El período de tiempo permitido varía, pero en general deben tener como objetivo aplicaciones con capacidad de respuesta en cuestión de segundos y usar tareas en segundo plano para realizar cualquier trabajo que tomaría más tiempo. Se rechazarán las aplicaciones que tarden mucho en cargarse o responden no de uso normal. Siempre proporcionar comentarios del usuario cuando algo que sucede en segundo plano o la aplicación parecerá que han dejado de responder y una vez más, se rechazan.

### <a name="check-your-edge-cases"></a>Compruebe los casos de Edge

Una captura comunes para los desarrolladores no se puede solucionar edge-casos, especialmente aquellas que requieren volver a configurar el simulador o dispositivo para probar correctamente. Puede ser fácil olvidarse de que no todos los clientes van a "Permitir" de la aplicación para tener acceso a su ubicación porque después de que el desarrollador ha aceptado la solicitud de una vez, nunca se pedirá a intentarlo. Los permisos y el uso de la red están centrados específicamente en durante el proceso de aprobación, lo que significa que puede dar lugar a un pequeño descuido en estas áreas de rechazo.

En la lista siguiente es un buen punto de partida para la comprobación de casos extremos que se han perdido:

- **Los clientes pueden "Denegar" acceso a los servicios** : especialmente en iOS, tener acceso a datos, como información de ubicación geográfica se proporcionan solo si el usuario concede el permiso a la aplicación. Evaluadores de aplicaciones con frecuencia deben volver a instalar la aplicación en su estado inicial y no permitir las solicitudes de permiso para asegurarse de que la aplicación se comporta de forma adecuada. Alternar permiso en y desactivarse para comprobar el comportamiento correcto, tal como los clientes cambien de opinión.
- **Los clientes están en todas partes** : no se supone que solo se usa una aplicación en la ciudad o el país donde se desarrolló! Todo código que funciona con las coordenadas GPS, valores de fecha y hora y divisas puede afectado por configuración regional y la ubicación del cliente. Todas las plataformas ofrecen un simulador que le permite especificar distintas ubicaciones y las configuraciones regionales - usarlo a ubicaciones de prueba en otros hemisferios y con las referencias culturales que dan formato a fechas y divisas diferente. Los valores de latitud y longitud pueden ser positivo o negativo, el separador decimal podría ser un punto o una coma y fechas pueden dar formato a una gran variedad de formas - tratar con ella.
- **Las conexiones de red lentas** : a menudo, los desarrolladores de aplicaciones funcionan en un 'mundo ideal' rápidos, siempre trabajar la conectividad de red, que obviamente no es el caso en el mundo real. Las pruebas con la conectividad de red lenta (por ejemplo, una mala conexión 3 G), así como sin acceso de red son fundamental para garantizar que no se incluyan una aplicación con errores. El proceso de aprobación siempre incluirá una prueba con el dispositivo en modo de avión, así que asegúrese de que haya probado usted mismo.
- **Hardware varía** : no olvide probar en el hardware más antiguo, más lento que va a admitir. Hay dos aspectos que podrían afectar a la aplicación: rendimiento, lo que podría ser inutilizable en un dispositivo anterior y compatibilidad con las características de hardware, como una cámara, micrófono, GPS, giroscopio u otro componente opcional. Las aplicaciones deben reducirse correctamente (y no frente a bloqueos) cuando un componente no está disponible.

### <a name="guidelines-are-more-than-just-a-guide"></a>Instrucciones son algo más que un 'manual'

Apple es famoso por ser estricto sobre el cumplimiento de las directrices de interfaz humana sea una de las principales ventajas de su plataforma de coherencia (y el aumento percibido de facilidad de uso). Microsoft ha adoptado un enfoque similar con las aplicaciones de Windows para implementar la interfaz de usuario estilo Metro. El proceso de aprobación para ambas plataformas implicará la aplicación que se evalúa para el cumplimiento de la filosofía de diseño correspondiente.

Tampoco pretendemos decir que innovación de la interfaz de usuario no es compatible o se recomienda, pero hay algunas cosas que "simplemente no debe hacer" o la aplicación se rechazará.

En iOS, esto incluye el uso incorrecto de iconos integrados o mediante otras metáforas bien establecidas de forma que no sea coherente; Por ejemplo, mediante el icono de 'crear' para algo distinto de crear contenido nuevo.

Los desarrolladores de Windows deben ser del mismo modo cuidados; Error en un error común admitir correctamente el hardware nuevo botón según las directrices de Microsoft.

Anime a los diseñadores de leer y seguir las instrucciones de diseño para cada plataforma.

### <a name="implementing-platform-specific-features"></a>Implementación de características específicas de plataforma

Las cosas son un poco más estrictas en cuanto a la implementación específica de la plataforma de servicios, especialmente en iOS. Para evitar el rechazo automático por Apple, hay algunas reglas para seguir con las siguientes características de iOS:

- **En la aplicación adquiere** : las aplicaciones deben implementar mecanismos de pago externo para productos digitales como moneda en juego, características de la aplicación, suscripciones de revistas y mucho más. las aplicaciones de iOS deben usar el servicio basado en iTunes de Apple para este tipo de funcionalidad. Hay un escape - apps, como el lector Kindle y algunas aplicaciones basadas en la suscripción le permiten adquirir contenido en otro lugar que se asocia a una "cuenta" que, a continuación, puede acceder a través de la aplicación, pero en este caso la aplicación no puede contener vínculos o referencias a la fuera de la aplicación proceso de compra (o bien, una vez más, deberá rechazarán).
- **copia de seguridad de iCloud** – con la llegada de iCloud, los revisores de Apple son mucho más estrictos con respecto a cómo las aplicaciones usan almacenamiento (para garantizar la experiencia de copia de seguridad remoto del cliente es agradable). Las aplicaciones que puede obtener rechazado desperdician capaz de copia de seguridad de espacio de almacenamiento, por lo que usar la carpeta de caché adecuadamente y seguimiento de Apple del otras directrices relacionadas con el almacenamiento.
- **Newsstand** – periódicos y revistas aplicaciones son una excelente elección para Newsstand de Apple, sin embargo, las aplicaciones deben implementar al menos la renovación automática suscripción y soporte técnico de descargas en segundo plano para poder ser autorizadas.
- **Asigna** – es cada vez más habitual para agregar superposiciones y otras características para móviles mapas, pero tenga no cuidado ocultar el mapa 'créditos' información (por ejemplo, el logotipo de Google en iOS5) que si lo hace, se producirá de rechazo.

### <a name="manage-your-metadata"></a>Administrar los metadatos

Además de los problemas técnicos obvios que pueden dar lugar a una aplicación que se ha rechazado, hay algo más sutiles aspectos de su envío a la que podría dar lugar a rechazo, especialmente en torno a los metadatos (descripción, palabras clave y las imágenes de marketing) que usted Enviar con la aplicación para que aparezcan en la App Store o el catálogo.

- **Imágenes** : siga las instrucciones de la plataforma para los iconos de aplicación y almacenar las imágenes. No use imágenes de marca comercial, hemos visto las aplicaciones se rechazan porque sus iconos con todas las características de un dibujo de un iPhone.
- **Marcas comerciales** : Evite usar las marcas comerciales distinto del suyo propio. Las aplicaciones se ha denegado para mencionar marcas comerciales en la descripción de la aplicación o incluso en las palabras clave en Apple App Store.
- **Descripción** : no utilizar la palabra "beta" ni de ninguna manera indicar que la aplicación no está lista para el estreno. No se mencionan otras plataformas móviles (incluso si la aplicación es multiplataforma). Lo más importante, asegúrese de que la aplicación hace exactamente lo que dice que lo hace. Si la lista una serie de características de la descripción, sea obvio cómo usar cada una de esas características u obtendrá un rechazo "características mencionadas en la descripción de la aplicación no están implementados".

Ponga como mucho esfuerzo en los metadatos de la aplicación como en desarrollo y pruebas. Las aplicaciones obtener rechazadas para infracciones en los metadatos por lo que merece la pena dedicar tiempo a hacerlo bien.

### <a name="app-stores-not-for-everyone"></a>Tiendas de aplicaciones: No para todos los usuarios

El objetivo principal de los almacenes en cada plataforma es la distribución de consumidor: la capacidad de llegar a clientes tantos como sea posible. Sin embargo, no todas las aplicaciones se dirigen a los consumidores, hay un rápido crecimiento de base de internas y extranet similar a las aplicaciones que requieren una distribución limitada a empleados, proveedores y clientes. Estas aplicaciones no son "para la venta" y no necesitan aprobación, desde la distribución de los controles de desarrollador a un grupo de usuarios cerrado.
Soporte técnico para este tipo de implementación varía según la plataforma.

Android ofrece la máxima flexibilidad en este sentido: las aplicaciones pueden instalarse directamente desde un archivo adjunto de correo electrónico o dirección URL (siempre y cuando lo permite la configuración del dispositivo). Esto significa que es muy fácil crear y distribuir aplicaciones corporativas internas o publicar aplicaciones para determinados clientes o proveedores.

Apple proporciona una opción de implementación interna para los desarrolladores de iOS Developer Enterprise Program, que omite el proceso de aprobación de App Store y permite a las empresas distribuir aplicaciones internas a sus empleados inscritos.
Desgraciadamente esta licencia no trata la necesidad de distribución de aplicaciones de extranet similares para otros grupos de clientes o proveedores de cerrado. [Enterprise (y Ad Hoc) implementación](~/ios/deploy-test/app-distribution/ipa-support.md)

### <a name="app-store-summary"></a>Resumen de la App Store

El proceso de revisión puede ser complicado, pero como el resto del ciclo de vida de desarrollo puede ayudar a garantizar el éxito con algo de planificación y atención al detalle. Todas se trata de unos pocos pasos sencillos: leer y comprender las directrices de interfaz de usuario debe cumplir para, seguir las reglas si implementa características específicas de plataforma, realizar pruebas exhaustivas (a continuación, haga algunas pruebas más) y, por último, asegúrese de que los metadatos de la aplicación es correcta antes de enviar.

Una última palabra de aviso para los desarrolladores de publicación en Google Play: la falta de proceso de aprobación puede parecer que facilita el trabajo - pero los clientes será incluso más exigentes que un equipo de revisión. Siga estas directrices porque aunque podría obtener rechaza la aplicación, en caso contrario, será a los clientes realizar el rechazo.
