---
title: Publicación en la tienda de aplicaciones de TV de Apple
description: Este documento describe cómo publicar una aplicación en la tienda de aplicaciones de TV de Apple. Describe cómo configurar, aprovisionar, crear y enviar una solicitud de tvOS creada con Xamarin.
ms.prod: xamarin
ms.assetid: 52448C93-DC19-40FA-BF8C-608AE680FF49
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: ac905caaf0bdefe7f0c5502be0bd63102ca5a813
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789309"
---
# <a name="publishing-to-the-apple-tv-app-store"></a>Publicación en la tienda de aplicaciones de TV de Apple

En orden distribuir aplicaciones a todos los dispositivos de Apple TV, Apple requiere que las aplicaciones se publiquen a través del *App Store de Apple TV*, realizar la ubicación de la compra integral para las aplicaciones de tvOS de la tienda de aplicaciones. Los desarrolladores de muchos tipos de aplicaciones puede mayúsculas o minúsculas en el éxito masivo de este único punto de distribución. App Store de Apple TV es una solución inmediata, los desarrolladores de aplicaciones de la oferta, sistemas de pago y de distribución.

El proceso de enviar una aplicación a la tienda de aplicaciones de Apple TV implica:

1. Crear un *Id. de aplicación* y seleccionar *Derechos*.
2. Crear un *perfil de aprovisionamiento de distribución*.
3. Con este perfil para compilar la aplicación.
4. Enviar tu aplicación a través de *iTunes Connect*.


En este artículo se explica todos los pasos necesarios para aprovisionar, crear y enviar una aplicación para la distribución de TV App Store de Apple.

<a name="Before_you_Submit" />

## <a name="before-you-submit-an-application"></a>Antes de enviar una aplicación

Tras enviar una aplicación de publicación a la tienda de aplicaciones de Apple TV, pasa a través de un proceso de revisión de Apple para asegurarse de que cumple las directrices de Apple para la calidad y el contenido. Si la aplicación no cumple estas directrices, Apple la rechazará, por lo que se deberá abordar la no conformidad mencionada por Apple y, después, volver a enviarla.
En vista de ello, y para tener más posibilidades de superar la revisión de Apple, familiarícese con estas directrices e intente adaptar su aplicación a ellas. Directrices de Apple están disponibles en [directrices de revisión de App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html) y [preparar la aplicación de envío para el nuevo Televisor de Apple](https://developer.apple.com/tvos/submit/).

Aquí se muestran un par de cosas a tener en cuenta a la hora de enviar una aplicación:

1. Asegúrese de que la descripción de la aplicación coincide con la funcionalidad incluida en la aplicación.
2. Prueba que no bloqueará la aplicación en el uso normal. Esto incluye el uso en cada dispositivo de Apple TV que se admiten.


Apple también mantiene una lista de sugerencias sobre cómo presentar TV App Store de Apple. Puede leerlas en [Distribución en el App Store](https://developer.apple.com/appstore/resources/submission/tips.html).

<a name="Configuring_your_Application_in_iTunes_Connect" />

## <a name="configuring-your-application-in-itunes-connect"></a>Configuración de la aplicación en iTunes Connect

[iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) es un conjunto de herramientas basadas en web, entre otras cosas, administrar las aplicaciones de tvOS en la tienda de aplicaciones de TV de Apple. La aplicación Xamarin.tvOS tendrá que esté correctamente configurado y configurado en iTunes Connect antes de que puede enviarse a Apple para su revisión y en última instancia, se publican para la venta o como una aplicación gratuita en la tienda de aplicaciones de TV de Apple.

Haga lo siguiente:

1. Compruebe que los contratos adecuados se encuentran en vigor y actualizados en la sección **Contratos, impuestos y banca** de iTunes Connect para publicar una aplicación de iOS como gratuita o para su venta.
2. Crear un nuevo **iTunes Connect registro** para la aplicación y especificar su **nombre para mostrar** (tal y como se muestra en la tienda de aplicaciones de TV de Apple).
3. Seleccione un **Precio de venta** o especifique que la aplicación se publicará de manera gratuita.
4. Proporcionar un **icono de almacén de la aplicación** (iconos grandes) y capturas de pantalla de la aplicación en acción, en los dispositivos de Apple TV admitidos. Consulte nuestro [trabajar con imágenes de iconos y](~/ios/tvos/app-fundamentals/icons-images.md) guía para obtener más detalles.
5. Proporcionar un método clear, concisa **descripción** de la aplicación, incluidas sus características y beneficiarse al usuario final.
6. Proporcionar **categorías**, **categorías secundarias**, y **palabras clave** para ayudar al usuario a encontrar la aplicación en la tienda de aplicaciones de TV de Apple.
7. Proporcione las direcciones URL de **Contacto** y **Soporte técnico** a su sitio web requeridas por Apple.
8. Establezca su aplicación **clasificación**, que se usa por controles parentales en la tienda de aplicaciones de TV de Apple.
9. Configure tecnologías opcionales del App Store, como **Game Center** y **Compra desde la aplicación**.

Para obtener más información, vea nuestra [Configure su aplicación tvOS en iTunes Connect](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md) documentación.

<a name="Preparing_for_App_Store_Distribution" />

## <a name="preparing-for-app-store-distribution"></a>Preparación de la distribución a través del App Store

Para publicar una aplicación en App Store de Apple TV, primero debe generar para la distribución, lo que implica varios pasos. En las secciones siguientes tratan todo lo necesario para preparar una aplicación de Xamarin.tvOS para la publicación para que se puede crear y enviar a la tienda de aplicaciones de TV de Apple para su revisión y versión.

<a name="Provisioning_for_Application_Services" />

### <a name="provisioning-for-application-services"></a>Aprovisionamiento de servicios de aplicación

Apple proporciona una selección de especiales de los servicios de aplicación, también denominados derechos, que se pueden activar para su aplicación tvOS cuando se crea un identificador único para el mismo. Si usas derechos personalizados o no, debe seguir crear un identificador único para la aplicación de Xamarin.tvOS antes de poder publicarlo en el almacén de aplicación de TV de Apple.

La creación de un id. de aplicación y la selección opcional de derechos incluye los siguientes pasos mediante el portal de aprovisionamiento de iOS basado en web de Apple:

1. Seleccione **aprovisionamiento** > **desarrollo**.
2. Haga clic en el botón **+** y proporcione un **Nombre** y un **Id. de agrupación** para la nueva aplicación.
3. Desplácese hasta la parte inferior de la pantalla y seleccionar cualquiera **servicios de aplicaciones** que requerirá la aplicación Xamarin.tvOS.
4. Haga clic en el botón **Continuar** y siga las instrucciones en pantalla para crear el nuevo id. de aplicación.

Además de seleccionar y configurar los servicios de aplicación necesarios al definir el identificador de la aplicación, también debe configurar el identificador de la aplicación y sus derechos en el proyecto Xamarin.tvOS mediante la edición de ambos el `Info.plist` y `Entitlements.plist` archivos.

Haga lo siguiente en Visual Studio para Mac:

1. Haga doble clic en el archivo `Info.plist` en el **Explorador de soluciones** para abrirlo para su edición.
2. En el **tvOS destino de la aplicación** sección, escriba un nombre para la aplicación y escriba la **identificador de paquete** que creó al definir la ID. App
3. Guarde los cambios en el archivo `Info.plist`.
4. Haga doble clic en el archivo `Entitlements.plist` en el **Explorador de soluciones** para abrirlo para su edición.
5. Seleccionar y configurar los derechos necesarios para su aplicación Xamarin.tvOS para que coincidan con el programa de instalación que ha realizado anteriormente una vez definido el App ID.
6. Guarde los cambios en el archivo `Entitlements.plist`.

Para obtener instrucciones detalladas, vea nuestra documentación sobre [Aprovisionamiento de servicios de aplicación](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices). Mientras se redactó este documento para iOS, se utilizan los mismos pasos para aprovisionar una aplicación Xamarin.tvOS.

<a name="Setting_the_Apps_Icons_and_Launch_Screens" />

### <a name="setting-the-apps-icons-launch-image-and-top-shelf-image"></a>Configuración de los iconos de aplicaciones, la imagen de inicio y la imagen de estante superior

Para que una aplicación tvOS sean aceptados por Apple para su inclusión en la tienda de aplicaciones de Apple TV, requiere los iconos apropiados, lanzamiento e imágenes de estante superior para todos los dispositivos de Apple TV que se ejecutarán en. Necesita agregar los activos de imagen requiere que se compilarán en un `Assets.car` de archivos e incluidos en el paquete de la aplicación Xamarin.tvOS antes de que se cargue en iTunes Connect.

Para obtener instrucciones detalladas, consulte nuestro [trabajar con imágenes de iconos y](~/ios/tvos/app-fundamentals/icons-images.md) documentación.

<a name="Creating_and_Installing_a_Distribution_Profile" />

### <a name="creating-and-installing-a-distribution-profile"></a>Creación e instalación de un perfil de distribución

usa tvOS *perfiles de aprovisionamiento* para controlar cómo se puede implementar una compilación de la aplicación en particular. Estos son archivos que contienen información sobre el certificado que se ha usado para firmar una aplicación, el *Id. de aplicación* y el lugar en el que puede instalarse la aplicación. Para el desarrollo y la distribución ad hoc, el perfil de aprovisionamiento también incluye la lista de dispositivos permitidos en los que puede implementar la aplicación. Sin embargo, para la distribución de App Store de Apple TV, solo información de Id. de aplicación y de certificado se incluyen, ya que es el único mecanismo para la distribución de pública a través de la tienda de aplicaciones de TV de Apple.

El aprovisionamiento incluye los siguientes pasos con el portal de aprovisionamiento de iOS basado en web de Apple:

1.  Seleccione **Aprovisionamiento** > **Distribución**.
2.  Haga clic en el **+** botón y seleccione el tipo de perfil de distribución que desea crear como **App Store de Apple TV**.
3.  Seleccione **Id. de aplicación** en la lista desplegable para el que quiere crear un perfil de distribución.
4.  Seleccione el certificado requerido para firmar la aplicación.
5.  Escriba un **Nombre** para el nuevo **Perfil de distribución** y genere el perfil.
6.  Actualizar la lista de perfiles disponibles en Xcode.
7.  Seleccione el perfil en Visual Studio para el aprovisionamiento de distribución la **tienda de aplicaciones** _configuración de compilación_.

Para obtener instrucciones detalladas, vea las secciones [Crear un perfil de distribución](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#creatingprofile) y [Seleccionar un perfil de distribución en un proyecto de Xamarin.iOS](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile). Una vez más, los dos documentos son específicos de iOS pero se usa la misma técnica para las aplicaciones de tvOS.


<a name="Setting_the_Build_Configuration_for_your_Application" />

### <a name="setting-the-build-configuration-for-your-application"></a>Establecer la configuración de compilación de la aplicación

De forma predeterminada, cuando se crea una nueva aplicación de Xamarin.tvOS _las configuraciones de compilación_ se crean automáticamente tanto para **depurar** y **versión** implementación. Antes de realizar la compilación final de la aplicación que vayan a enviar a Apple, hay algunas modificaciones que debe realizar en la base de **versión** configuración.

Haga lo siguiente:

1. Haga doble clic en el **nombre del proyecto** en el **el Explorador de soluciones** y selección **opciones** para abrirlos para su edición.
2. Si desea usar una versión específica de tvOS, selecciónela en **tvOS compilación** > **versión de SDK de iOS**. Para la versión preliminar de compatibilidad con tvOS, deje este valor establecido en **predeterminado**.
3. Vinculación reduce el tamaño total de la aplicación puede distribuir mediante la eliminación de las clases de métodos, propiedades, sin usar, etc. y en la mayoría de los casos deben dejar el valor predeterminado de **framework SDK de vínculo sólo**. En algunas situaciones, como cuando se usan algunas bibliotecas de terceros determinadas, puede verse forzado a establecer este valor en **Don't link (No vincular)** para impedir que se quite el elemento necesario.
4. Para distribuir una aplicación Xamarin.tvOS, debe usar el compilador de optimización de LLVM. Asegúrese de que el **usar el compilador de optimización de LLVM** casilla está activada en la **versión** configuración.
5. Apple también requiere que las aplicaciones de tvOS usan bitcode. Nuevo en el **versión** configuración, agregue `--bitcode=asmonly` a la **argumentos adicionales mtouch** cuadro.
6. El **archivos de imagen PNG optimizar para iOS** debe comprobarse la casilla de verificación que esto le ayudará a más disminuir tamaño de entrega de la aplicación.
7. La depuración *no* debe habilitarse porque hará que la compilación sea innecesariamente más grande.


<a name="Building_and_Submitting_the_Distributable" />

## <a name="building-and-submitting-the-distributable"></a>Compilación y envío del distribuible

Con la aplicación Xamarin.tvOS configurada correctamente, ya está listo para realizar la compilación de distribución final se va a enviar a Apple para su revisión y versión.

#### <a name="build-your-archive"></a>Compilar el archivo

1. Seleccione la configuración **Versión | Dispositivo** en Visual Studio para Mac:

    ![](app-store-publishing-images/buildxs01new.png "Seleccione la configuración de lanzamiento")
2. En el menú **Compilación**, seleccione **Archivo para publicar**:

    [![](app-store-publishing-images/buildxs02new.png "Selección de Archivo para publicar")](app-store-publishing-images/buildxs02new.png#lightbox)
3. Una vez creado el archivo, se mostrará la vista **Archives (Archivos)**:

    [![](app-store-publishing-images/buildxs03new.png "La vista de archivos")](app-store-publishing-images/buildxs03new.png#lightbox)

### <a name="sign-and-distribute-your-app"></a>Firmar y distribuir la aplicación

Cada vez que compile la aplicación para el archivo, se abrirá automáticamente la *Archives View (Vista de archivos)*, que muestra todos los proyectos archivados; agrupados por solución. De manera predeterminada, esta vista solo muestra la solución abierta actual. Para ver todas las soluciones que tienen archivos, haga clic en la opción **Show all archives (Mostrar todos los archivos)**.

Se recomienda que se mantengan los archivos que se implementan en los clientes (implementaciones App Store o Enterprise), por lo que cualquier información de depuración generada puede considerarse con una fecha posterior.

Para firmar la aplicación y prepararla para la distribución:

1. Seleccione el **firmar y distribuir...** , se muestra a continuación:

    [![](app-store-publishing-images/buildxs04new.png ", Seleccione theSign y distribuir...")](app-store-publishing-images/buildxs04new.png#lightbox)
2. De este modo, se abrirá el asistente para publicación. Seleccione el canal de distribución **App Store** para crear un paquete y abra Application Loader:

    [![](app-store-publishing-images/distribute01.png "Seleccione el canal de distribución de la tienda de aplicaciones")](app-store-publishing-images/distribute01.png#lightbox)
3. En la pantalla de perfil de aprovisionamiento, seleccione su identidad de firma y el correspondiente perfil de aprovisionamiento o volver a iniciar sesión con otra identidad:

    [![](app-store-publishing-images/distribute02.png "Seleccione la identidad de firma y el correspondiente perfil de aprovisionamiento")](app-store-publishing-images/distribute02.png#lightbox)
4. Compruebe los detalles del paquete y haga clic en **Publicar** para guardar el paquete de `.ipa`:

    [![](app-store-publishing-images/distribute03.png "Compruebe los detalles del paquete")](app-store-publishing-images/distribute03.png#lightbox)
5. Una vez que `.ipa` se haya guardado, la aplicación está lista para cargarse en iTunes Connect mediante Application Loader:

    [![](app-store-publishing-images/distribute04.png "Cargar a iTunes conectarse mediante el cargador de aplicación")](app-store-publishing-images/distribute04.png#lightbox)

Una vez que se ha creado y archivado su compilación de distribución, ahora está listo para enviar su aplicación a iTunes Connect.

<a name="Submitting_Your_App_to_Apple" />

## <a name="submitting-your-app-to-apple"></a>Enviar su aplicación a Apple

Una vez que se ha completado la compilación de distribución, está listo para enviar su aplicación de iOS a Apple para su revisión y publicación en el App Store.


El flujo de trabajo de archivo en Visual Studio para Mac abrirá automáticamente, cargador de la aplicación una vez que se guardó el `.ipa`:

2. Seleccione *Deliver Your App* (Entregar la aplicación) y haga clic en el botón *Elegir*:

    [![](app-store-publishing-images/publishvs01.png "Selección de Deliver Your App (Entregar la aplicación)")](app-store-publishing-images/publishvs01.png#lightbox)

3. Seleccione el archivo IPA o ZIP que ha creado anteriormente y haga clic en el botón **Aceptar**.
4. Application Loader validará el archivo:

    [![](app-store-publishing-images/publishvs02.png "La pantalla de validación de cargador de la aplicación")](app-store-publishing-images/publishvs02.png#lightbox)
5. Haga clic en el botón *Siguiente* y la aplicación se validará en el App Store:

    [![](app-store-publishing-images/publishvs03.png "La aplicación que se está validando frente a la tienda de aplicaciones")](app-store-publishing-images/publishvs03.png#lightbox)
6. Haga clic en el botón **Enviar** para enviar la aplicación a Apple para su revisión.
7. Application Loader le informará cuando el archivo se haya cargado correctamente.

<a name="iTunes_Connect_Status" />

### <a name="itunes-connect-status"></a>Estado de iTunes Connect

Si vuelve a registrarse en iTunes Connect y seleccione la aplicación de la lista de aplicaciones disponibles, ahora debe mostrar el estado en iTunes Connect, que es **espera para su revisión** (puede leer temporalmente **cargar recibido** mientras se procesa):

[![](app-store-publishing-images/image21.png "El estado en iTunes conectarse mostrando espera para su revisión")](app-store-publishing-images/image21.png#lightbox)

<a name="Troubleshooting" />

## <a name="troubleshooting"></a>Solución de problemas

Si tiene problemas para enviar tu aplicación Xamarin.tvOS a la tienda de aplicaciones de Apple TV, vea nuestra [solución de problemas](~/ios/tvos/troubleshooting.md) guía. Contiene varios problemas conocidos que pueden surgir y cómo resolverlos en el Xamarin.tvOS.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se presenta a una guía paso a paso para configurar, compilar y enviar una aplicación de publicación de TV App Store de Apple. Primero, ha tratado los pasos necesarios para crear e instalar un perfil de aprovisionamiento de distribución. A continuación, desplazará a través de cómo usar Visual Studio para Mac para crear una compilación de distribución. Por último, mostró cómo usar iTunes Connect y la herramienta para archivar Xcode para presentar una solicitud a la tienda de aplicaciones de TV de Apple.


## <a name="related-links"></a>Vínculos relacionados

- [Trabajo con iconos e imágenes](~/ios/tvos/app-fundamentals/icons-images.md)
- [Preparar el envío de la aplicación para el nuevo Televisor de Apple](https://developer.apple.com/tvos/submit/)
- [Sugerencias de envío al App Store](https://developer.apple.com/appstore/resources/submission/tips.html)
- [Rechazos de aplicaciones comunes](https://developer.apple.com/app-store/review/rejections/)
- [Directrices de revisión del App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html)
