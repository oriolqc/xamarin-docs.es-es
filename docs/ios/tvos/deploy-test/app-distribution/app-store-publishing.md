---
title: Publicación en el Store de Apple TV App
description: Este documento describe cómo publicar una aplicación en el Store de Apple TV App. Describe cómo configurar, aprovisionar, compilar y enviar una aplicación de tvOS con Xamarin.
ms.prod: xamarin
ms.assetid: 52448C93-DC19-40FA-BF8C-608AE680FF49
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: b941bcc8588e7fb0377430cca2829ad72ecbc8c6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122405"
---
# <a name="publishing-to-the-apple-tv-app-store"></a>Publicación en el Store de Apple TV App

Distribuir aplicaciones a todos los dispositivos de Apple TV, Apple requiere que las aplicaciones se publiquen a través del *App Store de Apple TV*, que hace el Store de la aplicación en la ubicación de la compra integral para aplicaciones de tvOS. Los desarrolladores de muchos tipos de aplicaciones puede han aprovechado del éxito masivo de este único punto de distribución. El Store de Apple TV App es una solución llave en mano, ofrece a los desarrolladores de aplicaciones sistemas de pago y de distribución.

El proceso de enviar una aplicación en el Store de Apple TV App implica:

1. Crear un *Id. de aplicación* y seleccionar *Derechos*.
2. Crear un *perfil de aprovisionamiento de distribución*.
3. Con este perfil para compilar la aplicación.
4. Enviar la aplicación a través de *iTunes Connect*.


En este artículo trataremos todos los pasos necesarios para aprovisionar, compilar y enviar una aplicación para la distribución del App Store de Apple TV.

<a name="Before_you_Submit" />

## <a name="before-you-submit-an-application"></a>Antes de enviar una aplicación

Después de enviar una aplicación para su publicación en el Store de Apple TV App, pasa por un proceso de revisión de Apple para garantizar que cumple las directrices de Apple para la calidad y contenido. Si la aplicación no cumple estas directrices, Apple la rechazará, por lo que se deberá abordar la no conformidad mencionada por Apple y, después, volver a enviarla.
En vista de ello, y para tener más posibilidades de superar la revisión de Apple, familiarícese con estas directrices e intente adaptar su aplicación a ellas. Están disponibles en las directrices de Apple [directrices de revisión del App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html) y [preparar su aplicación de envío para el nuevo Televisor Apple](https://developer.apple.com/tvos/submit/).

Aquí se muestran un par de cosas a tener en cuenta a la hora de enviar una aplicación:

1. Asegúrese de que la descripción de la aplicación coincide con la funcionalidad incluida en la aplicación.
2. Compruebe que la aplicación no se bloquea con un uso normal. Esto incluye el uso en cada dispositivo Apple TV que admita.


Apple también mantiene una lista de sugerencias de envío al App Store de Apple TV. Puede leerlas en [Distribución en el App Store](https://developer.apple.com/appstore/resources/submission/tips.html).

<a name="Configuring_your_Application_in_iTunes_Connect" />

## <a name="configuring-your-application-in-itunes-connect"></a>Configuración de la aplicación en iTunes Connect

[iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) es un conjunto de herramientas basadas en web, entre otras cosas, administrar las aplicaciones de tvOS en el Store de Apple TV App. La aplicación Xamarin.tvOS deberá estar correctamente instalada y configurada en iTunes Connect antes de que se puede enviar a Apple para su revisión y en última instancia, liberarse para su venta o como una aplicación gratuita en el Store de Apple TV App.

Haga lo siguiente:

1. Compruebe que los contratos adecuados se encuentran en vigor y actualizados en la sección **Contratos, impuestos y banca** de iTunes Connect para publicar una aplicación de iOS como gratuita o para su venta.
2. Cree un nuevo **registro de iTunes Connect** para la aplicación y especifique su **nombre para mostrar** (tal y como se muestra en el Store de Apple TV App).
3. Seleccione un **Precio de venta** o especifique que la aplicación se publicará de manera gratuita.
4. Proporcione un **App Store icono** (iconos grandes) y capturas de pantalla de la aplicación en acción, en los dispositivos de Apple TV admitidos. Consulte nuestra [trabajar con iconos e imágenes](~/ios/tvos/app-fundamentals/icons-images.md) guía para obtener más detalles.
5. Proporcionan un claro, conciso **descripción** de la aplicación con sus características y beneficios para el usuario final.
6. Proporcionar **categorías**, **subcategorías**, y **palabras clave** para ayudar al usuario a encontrar la aplicación en el Store de Apple TV App.
7. Proporcione las direcciones URL de **Contacto** y **Soporte técnico** a su sitio web requeridas por Apple.
8. Establezca su aplicación **clasificación**, que se usa por controles parentales en el Store de Apple TV App.
9. Configure tecnologías opcionales del App Store, como **Game Center** y **Compra desde la aplicación**.

Para obtener más información, consulte nuestra [configurar la aplicación tvOS en iTunes Connect](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md) documentación.

<a name="Preparing_for_App_Store_Distribution" />

## <a name="preparing-for-app-store-distribution"></a>Preparación de la distribución a través del App Store

Para publicar una aplicación en el Store de Apple TV App, primero necesita compilarla para su distribución, que implica varios pasos. Las secciones siguientes se tratan todo lo necesario para preparar una aplicación Xamarin.tvOS para la publicación para que se puede compilar y enviarla a la Store de Apple TV App para revisión y publicación.

<a name="Provisioning_for_Application_Services" />

### <a name="provisioning-for-application-services"></a>Aprovisionamiento de servicios de aplicación

Apple proporciona una selección de servicios de aplicación especiales, también denominados derechos, que pueden activarse para la aplicación de tvOS cuando se crea un identificador único para ella. Si usas derechos personalizados o no, todavía deberá crear un identificador único para la aplicación Xamarin.tvOS antes de que se puede publicar en el Store de Apple TV App.

La creación de un id. de aplicación y la selección opcional de derechos incluye los siguientes pasos mediante el portal de aprovisionamiento de iOS basado en web de Apple:

1. Seleccione **aprovisionamiento** > **desarrollo**.
2. Haga clic en el botón **+** y proporcione un **Nombre** y un **Id. de agrupación** para la nueva aplicación.
3. Desplácese hasta la parte inferior de la pantalla y seleccione cualquier **App Services** será necesario que la aplicación Xamarin.tvOS.
4. Haga clic en el botón **Continuar** y siga las instrucciones en pantalla para crear el nuevo id. de aplicación.

Además de seleccionar y configurar los servicios de aplicación necesarios al definir el identificador de la aplicación, también deberá configurar el Id. de aplicación y los derechos en el proyecto Xamarin.tvOS editando el `Info.plist` y `Entitlements.plist` archivos.

Siga este procedimiento en Visual Studio para Mac:

1. Haga doble clic en el archivo `Info.plist` en el **Explorador de soluciones** para abrirlo para su edición.
2. En el **destino de la aplicación tvOS** sección, escriba un nombre para la aplicación y el **identificador de paquete** que creó al definir el ID. App
3. Guarde los cambios en el archivo `Info.plist`.
4. Haga doble clic en el archivo `Entitlements.plist` en el **Explorador de soluciones** para abrirlo para su edición.
5. Seleccionar y configurar los derechos necesarios para usted Xamarin.tvOS aplicación para que coincidan con la configuración que ha realizado anteriormente al definir el ID. App
6. Guarde los cambios en el archivo `Entitlements.plist`.

Para obtener instrucciones detalladas, vea nuestra documentación sobre [Aprovisionamiento de servicios de aplicación](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices). Aunque este documento se escribió para iOS, se usan los mismos pasos para aprovisionar una aplicación Xamarin.tvOS.

<a name="Setting_the_Apps_Icons_and_Launch_Screens" />

### <a name="setting-the-apps-icons-launch-image-and-top-shelf-image"></a>Establecer los iconos de aplicaciones, la imagen de inicio y la imagen del estante superior

Para que una aplicación de tvOS sean aceptados por Apple para su inclusión en el Store de Apple TV App, requiere iconos adecuados, lanzamiento e imágenes de estante superior para todos los que se ejecutará en dispositivos de Apple TV. Necesita agregará los activos de imagen requiere que se compilarán en un `Assets.car` de archivos e incluidos en el paquete de la aplicación Xamarin.tvOS antes de cargarse en iTunes Connect.

Para obtener instrucciones detalladas, consulte nuestra [trabajar con iconos e imágenes](~/ios/tvos/app-fundamentals/icons-images.md) documentación.

<a name="Creating_and_Installing_a_Distribution_Profile" />

### <a name="creating-and-installing-a-distribution-profile"></a>Creación e instalación de un perfil de distribución

usa tvOS *perfiles de aprovisionamiento* para controlar cómo se puede implementar una compilación de la aplicación en particular. Estos son archivos que contienen información sobre el certificado que se ha usado para firmar una aplicación, el *Id. de aplicación* y el lugar en el que puede instalarse la aplicación. Para el desarrollo y la distribución ad hoc, el perfil de aprovisionamiento también incluye la lista de dispositivos permitidos en los que puede implementar la aplicación. Sin embargo, para la distribución del App Store de Apple TV, información de Id. de certificado y la aplicación solo se incluyen, ya que es el único mecanismo para la distribución pública mediante el Store de Apple TV App.

El aprovisionamiento incluye los siguientes pasos con el portal de aprovisionamiento de iOS basado en web de Apple:

1.  Seleccione **Aprovisionamiento** > **Distribución**.
2.  Haga clic en el **+** botón y seleccione el tipo de perfil de distribución que desea crear como **App Store de Apple TV**.
3.  Seleccione **Id. de aplicación** en la lista desplegable para el que quiere crear un perfil de distribución.
4.  Seleccione el certificado necesario para firmar la aplicación.
5.  Escriba un **Nombre** para el nuevo **Perfil de distribución** y genere el perfil.
6.  Actualice la lista de perfiles disponibles en Xcode.
7.  Seleccione el perfil de Visual Studio para de aprovisionamiento de distribución la **App Store** _configuración de compilación_.

Para obtener instrucciones detalladas, vea las secciones [Crear un perfil de distribución](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#creatingprofile) y [Seleccionar un perfil de distribución en un proyecto de Xamarin.iOS](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile). Nuevamente, los dos documentos son específicos de iOS, pero la misma técnica se usa para aplicaciones de tvOS.


<a name="Setting_the_Build_Configuration_for_your_Application" />

### <a name="setting-the-build-configuration-for-your-application"></a>Establecer la configuración de compilación de la aplicación

De forma predeterminada, cuando se crea una nueva aplicación Xamarin.tvOS, _las configuraciones de compilación_ se crean automáticamente tanto para **depurar** y **versión** implementación. Antes de realizar la compilación final de la aplicación que se enviará a Apple, hay algunas modificaciones que debe realizar en la base de **versión** configuración.

Haga lo siguiente:

1. Haga doble clic en el **nombre del proyecto** en el **el Explorador de soluciones** y selección **opciones** para abrirlas para su edición.
2. Si desea usar una versión específica de tvOS, selecciónelo en **tvOS compilación** > **iOS SDK versión**. Para la versión preliminar de la compatibilidad con tvOS, deje este valor establecido en **predeterminado**.
3. Vinculación reduce el tamaño total de la aplicación distribuible mediante la eliminación sin usar clases de métodos, propiedades, etc. y en la mayoría de los casos deben dejarse en el valor predeterminado de **vínculo SDK framework sólo**. En algunas situaciones, como cuando se usan algunas bibliotecas de terceros determinadas, puede verse forzado a establecer este valor en **Don't link (No vincular)** para impedir que se quite el elemento necesario.
4. Para enviar una aplicación Xamarin.tvOS, deberá usar el compilador de optimización de LLVM. Asegúrese de que el **usar el compilador de optimización de LLVM** esté marcada la casilla bajo la **versión** configuración.
5. También es necesario que las aplicaciones de tvOS usan bitcode Apple. Nuevo en el **versión** configuración, agregue `--bitcode=asmonly` a la **argumentos mtouch adicionales** cuadro.
6. El **archivos de imagen PNG optimizar para iOS** debe comprobarse la casilla de verificación ya que ayudará a disminuir más tamaño del entregable de la aplicación.
7. La depuración *no* debe habilitarse porque hará que la compilación sea innecesariamente más grande.


<a name="Building_and_Submitting_the_Distributable" />

## <a name="building-and-submitting-the-distributable"></a>Compilación y envío del distribuible

Con la aplicación Xamarin.tvOS configurada correctamente, ahora está listo para realizar la compilación de distribución final que enviará a Apple para su revisión y publicación.

#### <a name="build-your-archive"></a>Compilar el archivo

1. Seleccione la configuración **Versión | Dispositivo** en Visual Studio para Mac:

    ![](app-store-publishing-images/buildxs01new.png "Seleccione la configuración de lanzamiento")
2. En el menú **Compilación**, seleccione **Archivo para publicar**:

    [![](app-store-publishing-images/buildxs02new.png "Selección de Archivo para publicar")](app-store-publishing-images/buildxs02new.png#lightbox)
3. Una vez creado el archivo, se mostrará la vista **Archives (Archivos)**:

    [![](app-store-publishing-images/buildxs03new.png "La vista archivos")](app-store-publishing-images/buildxs03new.png#lightbox)

### <a name="sign-and-distribute-your-app"></a>Firmar y distribuir la aplicación

Cada vez que compile la aplicación para el archivo, se abrirá automáticamente la *Archives View (Vista de archivos)*, que muestra todos los proyectos archivados; agrupados por solución. De manera predeterminada, esta vista solo muestra la solución abierta actual. Para ver todas las soluciones que tienen archivos, haga clic en la opción **Show all archives (Mostrar todos los archivos)**.

Se recomienda que se mantengan los archivos que se implementan en los clientes (implementaciones App Store o Enterprise), por lo que cualquier información de depuración generada puede considerarse con una fecha posterior.

Para firmar la aplicación y prepararla para la distribución:

1. Seleccione el **firmar y distribuir...** , que se muestra a continuación:

    [![](app-store-publishing-images/buildxs04new.png ", Seleccione theSign y distribuir...")](app-store-publishing-images/buildxs04new.png#lightbox)
2. De este modo, se abrirá el asistente para publicación. Seleccione el canal de distribución **App Store** para crear un paquete y abra Application Loader:

    [![](app-store-publishing-images/distribute01.png "Seleccione el canal de distribución del App Store")](app-store-publishing-images/distribute01.png#lightbox)
3. En la pantalla de perfil de aprovisionamiento, seleccione su identidad de firma y perfil de aprovisionamiento correspondiente o vuelva a iniciar sesión con otra identidad:

    [![](app-store-publishing-images/distribute02.png "Seleccione la identidad de firma y el correspondiente perfil de aprovisionamiento")](app-store-publishing-images/distribute02.png#lightbox)
4. Compruebe los detalles del paquete y haga clic en **Publicar** para guardar el paquete de `.ipa`:

    [![](app-store-publishing-images/distribute03.png "Compruebe los detalles del paquete")](app-store-publishing-images/distribute03.png#lightbox)
5. Una vez que `.ipa` se haya guardado, la aplicación está lista para cargarse en iTunes Connect mediante Application Loader:

    [![](app-store-publishing-images/distribute04.png "Cargar en iTunes Connect mediante Application Loader")](app-store-publishing-images/distribute04.png#lightbox)

Una vez que se ha creado y archivado su compilación de distribución, ahora está listo para enviar su aplicación a iTunes Connect.

<a name="Submitting_Your_App_to_Apple" />

## <a name="submitting-your-app-to-apple"></a>Enviar su aplicación a Apple

Una vez que se ha completado la compilación de distribución, está listo para enviar su aplicación de iOS a Apple para su revisión y publicación en el App Store.


El flujo de trabajo de archivo en Visual Studio para Mac abrirá Application Loader automáticamente, una vez que haya guardado el `.ipa`:

2. Seleccione *Deliver Your App* (Entregar la aplicación) y haga clic en el botón *Elegir*:

    [![](app-store-publishing-images/publishvs01.png "Selección de Deliver Your App (Entregar la aplicación)")](app-store-publishing-images/publishvs01.png#lightbox)

3. Seleccione el archivo IPA o ZIP que ha creado anteriormente y haga clic en el botón **Aceptar**.
4. Application Loader validará el archivo:

    [![](app-store-publishing-images/publishvs02.png "La pantalla de validación del cargador de aplicaciones")](app-store-publishing-images/publishvs02.png#lightbox)
5. Haga clic en el botón *Siguiente* y la aplicación se validará en el App Store:

    [![](app-store-publishing-images/publishvs03.png "La aplicación que se va a validar el Store de la aplicación")](app-store-publishing-images/publishvs03.png#lightbox)
6. Haga clic en el botón **Enviar** para enviar la aplicación a Apple para su revisión.
7. Application Loader le informará cuando el archivo se haya cargado correctamente.

<a name="iTunes_Connect_Status" />

### <a name="itunes-connect-status"></a>Estado de iTunes Connect

Si vuelve a registrarse en iTunes Connect y seleccione la aplicación en la lista de aplicaciones disponibles, el estado en iTunes Connect debe mostrar ahora que se trata **esperando revisión** (temporalmente puede leerlo **cargar recibido** mientras se procesa):

[![](app-store-publishing-images/image21.png "El estado en iTunes Connect mostrando esperando revisión")](app-store-publishing-images/image21.png#lightbox)

<a name="Troubleshooting" />

## <a name="troubleshooting"></a>Solución de problemas

Si tiene problemas para enviar su aplicación Xamarin.tvOS al Store de Apple TV App, consulte nuestra [Troubleshooting](~/ios/tvos/troubleshooting.md) guía. Contiene varios problemas conocidos que pueden surgir y cómo resolverlos en el Xamarin.tvOS.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se presenta a una guía paso a paso para configurar, compilar y enviar una aplicación para la publicación de Apple TV App Store. Primero, ha tratado los pasos necesarios para crear e instalar un perfil de aprovisionamiento de distribución. A continuación, ha avanzado a través de cómo usar Visual Studio para Mac para crear una compilación de distribución. Por último, le mostramos cómo usar iTunes Connect y la herramienta para archivar Xcode para enviar una aplicación para el Store de Apple TV App.


## <a name="related-links"></a>Vínculos relacionados

- [Trabajo con iconos e imágenes](~/ios/tvos/app-fundamentals/icons-images.md)
- [Preparar el envío de la aplicación para el nuevo Televisor Apple](https://developer.apple.com/tvos/submit/)
- [Sugerencias de envío al App Store](https://developer.apple.com/appstore/resources/submission/tips.html)
- [Rechazos de aplicaciones comunes](https://developer.apple.com/app-store/review/rejections/)
- [Directrices de revisión del App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html)
