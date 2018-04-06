---
title: Publicación en el App Store
description: En este artículo se muestra cómo configurar, compilar y publicar una aplicación de Xamarin.iOS para su distribución a través del App Store. Incluye una guía paso a paso que trata sobre cómo preparar su aplicación para la distribución, cómo usar las herramientas de Apple para enviar su aplicación a revisar y, por último, cómo publicar la aplicación en el App Store.
ms.prod: xamarin
ms.assetid: DFBCC0BA-D233-4DC4-8545-AFBD3768C3B9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/23/2017
ms.openlocfilehash: 5d78cb81f27ce7478719ff9f11f4eb38fddc3981
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="publishing-to-the-app-store"></a>Publicación en el App Store

_En este artículo se muestra cómo configurar, compilar y publicar una aplicación de Xamarin.iOS para su distribución a través del App Store. Incluye una guía paso a paso que trata sobre cómo preparar su aplicación para la distribución, cómo usar las herramientas de Apple para enviar su aplicación a revisar y, por último, cómo publicar la aplicación en el App Store._

Para distribuir aplicaciones en todos los dispositivos iOS, Apple necesita que las aplicaciones se publiquen a través de *App Store*, de manera que App Store sea la ubicación de compra integral de las aplicaciones iOS. Con más de 500 000 aplicaciones en la tienda, los desarrolladores de muchos tipos de aplicaciones se han aprovechado del éxito masivo de este único punto de distribución. App Store es una solución llave en mano que ofrece a los desarrolladores de aplicaciones sistemas de pago y de distribución.

El proceso de enviar una aplicación a App Store incluye:

1. Crear un **Id. de aplicación** y seleccionar **Derechos**.
2. Creación de un **perfil de aprovisionamiento de distribución**.
3. Usar este perfil para compilar la aplicación.
4. Enviar la aplicación mediante **iTunes Connect**.


En este artículo se incluyen todos los pasos necesarios para aprovisionar, compilar y enviar una aplicación para la distribución en el App Store.

## <a name="before-you-submit-an-application"></a>Antes de enviar una aplicación

Antes de enviar una aplicación para su publicación en el App Store, esta pasa a través de un proceso de revisión de Apple para garantizar que cumple sus directrices en materia de calidad y contenido. Si la aplicación no cumple estas directrices, Apple la rechazará, por lo que se deberá abordar la no conformidad mencionada por Apple y, después, volver a enviarla.
En vista de ello, y para tener más posibilidades de superar la revisión de Apple, familiarícese con estas directrices e intente adaptar su aplicación a ellas. Las directrices de Apple están disponibles en: [Directrices de revisión del App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html).

Aquí se muestran un par de cosas a tener en cuenta a la hora de enviar una aplicación:

1. Asegúrese de que la descripción de la aplicación coincide con la función que se incluye en la aplicación.
2. Pruebe que la aplicación no se bloquee con un uso normal. Esto incluye el uso en cada dispositivo iOS que admita.


Apple también mantiene una lista de sugerencias de envío al App Store. Puede leerlas en [Distribución en el App Store](https://developer.apple.com/appstore/resources/submission/tips.html).

## <a name="configuring-your-application-in-itunes-connect"></a>Configuración de la aplicación en iTunes Connect

[iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) es un conjunto de herramientas basadas en web que, entre otras cosas, permite administrar aplicaciones de iOS en el App Store. La aplicación de Xamarin.iOS deberá estar correctamente instalada y configurada en iTunes Connect para que se pueda enviar a Apple para su revisión y publicación como una aplicación gratuita o para su venta en el App Store.

Haga lo siguiente:

1. Compruebe que los contratos adecuados se encuentran en vigor y actualizados en la sección **Contratos, impuestos y banca** de iTunes Connect para publicar una aplicación de iOS como gratuita o para su venta.
2. Cree un nuevo **Registro de iTunes Connect** para la aplicación y especifique su **Nombre para mostrar** (como se muestra en el App Store).
3. Seleccione un **Precio de venta** o especifique que la aplicación se publicará de manera gratuita.
5. Proporcione una **Descripción** clara y concisa de la aplicación con sus características y beneficios para el usuario final.
6. Proporcione **Categorías**, **Subcategorías** y **Palabras clave** para ayudar al usuario a encontrar la aplicación en el App Store.
7. Proporcione las direcciones URL de **Contacto** y **Soporte técnico** a su sitio web requeridas por Apple.
8. Establezca la **Clasificación** de la aplicación, que se usa para informar de controles parentales en el App Store.
9. Configure tecnologías opcionales del App Store, como **Game Center** y **Compra desde la aplicación**.

Para obtener información más detallada, vea la documentación [Configuración de una aplicación en iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md).

## <a name="preparing-for-app-store-distribution"></a>Preparación de la distribución a través del App Store

Para publicar una aplicación en el App Store, primero necesita compilarla para su distribución, que implica varios pasos. En las siguientes secciones se incluye todo lo necesario para preparar una aplicación de Xamarin.iOS para su publicación, de manera que pueda compilarse y enviarse a App Store para su revisión y publicación.

### <a name="provisioning-for-application-services"></a>Aprovisionamiento de servicios de aplicación

Apple proporciona una selección de servicios de aplicación especiales, también denominados derechos, que pueden activarse para la aplicación de iOS cuando cree un identificador único para esta. Tanto si usa los derechos personalizados como si no lo hace, todavía necesitará crear un identificador único para su aplicación de Xamarin.iOS antes de que se pueda publicar en el App Store.

La creación de un id. de aplicación y la selección opcional de derechos incluye los siguientes pasos mediante el portal de aprovisionamiento de iOS basado en web de Apple:

1. En la sección **Certificados, identificadores y perfiles**, seleccione **Identificadores** > **Id. de aplicación**.
2. Haga clic en el botón **+** y proporcione un **Nombre** y un **Id. de agrupación** para la nueva aplicación.
3. Desplácese hasta la parte inferior de la pantalla y seleccione cualquier **Servicio de aplicación** que necesitará su aplicación de Xamarin.iOS.
4. Haga clic en el botón **Continuar** y siga las instrucciones en pantalla para crear el nuevo id. de aplicación.

Además de seleccionar y configurar los servicios de aplicación necesarios al definir el id. de aplicación, también necesita configurar el id. de aplicación y los derechos en su proyecto de Xamarin.iOS editando los archivos `Info.plist` y `Entitlements.plist`.

Haga lo siguiente:

1. Haga doble clic en el archivo `Info.plist` en el **Explorador de soluciones** para abrirlo para su edición.
2. En la sección **Destino de la aplicación de iOS**, escriba un nombre para la aplicación y el **Id. de agrupación** que ha creado al definir el id. de aplicación.
3. Guarde los cambios en el archivo `Info.plist`.
4. Haga doble clic en el archivo `Entitlements.plist` en el **Explorador de soluciones** para abrirlo para su edición.
5. Seleccione y configure los derechos necesarios para la aplicación de Xamarin.iOS, de manera que coincidan con la configuración que ha realizado anteriormente al definir el id. de aplicación.
6. Guarde los cambios en el archivo `Entitlements.plist`.

Para obtener instrucciones detalladas, vea nuestra documentación sobre [Aprovisionamiento de servicios de aplicación](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices).

### <a name="setting-the-store-icons"></a>Establecimiento de los iconos de Store

Los iconos de Application Store ahora deben entregarse en un catálogo de recursos. Para agregar un icono de App Store, primero localice el conjunto de imágenes **AppIcon** en el archivo **Assets.xcassets** del proyecto.

El icono requerido en el catálogo de recursos se llama **App Store** y debe tener un tamaño de **1024 x 1024**. Apple ha manifestado que el icono de App Store del catálogo de recursos no puede ser transparente ni contener un canal alfa.

Para obtener más información sobre la configuración del icono de Store, consulte la guía [App Store Icon](~/ios/app-fundamentals/images-icons/app-store-icon.md) (Icono de App Store).

### <a name="setting-the-apps-icons-and-launch-screens"></a>Establecer los iconos de aplicaciones y las pantallas de inicio

Para que Apple acepte una aplicación de iOS para que se incluya en el App Store, necesita pantallas de inicio e iconos adecuados para todos los dispositivos iOS en los que se va a ejecutar. Los iconos de la aplicación se agregan a los proyectos en un catálogo de recursos, mediante una imagen **AppIcon** establecida en el archivo **Assets.xcassets**. Las pantallas de inicio se agregan a través de Storyboard.

Para obtener instrucciones detalladas sobre cómo crear iconos de aplicaciones y pantallas de inicio, consulte las guías [Application Icon](~/ios/app-fundamentals/images-icons/app-icons.md) (Icono de aplicaciones) y [Launch Screens](~/ios/app-fundamentals/images-icons/launch-screens.md) (Pantallas de inicio).

### <a name="creating-and-installing-a-distribution-profile"></a>Creación e instalación de un perfil de distribución

iOS usa *perfiles de aprovisionamiento* para controlar cómo puede implementarse una compilación de aplicación determinada. Estos son archivos que contienen información sobre el certificado que se ha usado para firmar una aplicación, el *Id. de aplicación* y el lugar en el que puede instalarse la aplicación. Para el desarrollo y la distribución ad hoc, el perfil de aprovisionamiento también incluye la lista de dispositivos permitidos en los que puede implementar la aplicación. En cambio, para la distribución en el App Store, solo se incluye información del id. de aplicación y certificado, ya que el único mecanismo para la distribución pública es a través del App Store.

El aprovisionamiento incluye los siguientes pasos con el portal de aprovisionamiento de iOS basado en web de Apple:

1.  Seleccione **Aprovisionamiento** > **Distribución**.
2.  Haga clic en el botón **+** y seleccione el tipo de perfil de distribución que quiere crear como **App Store**.
3.  Seleccione **Id. de aplicación** en la lista desplegable para el que quiere crear un perfil de distribución.
4.  Seleccione un certificado de producción (distribución) válido para firmar la aplicación.
5.  Escriba un **Nombre** para el nuevo **Perfil de distribución** y genere el perfil.
6.  En el equipo Mac, abra XCode y vaya a **Preferencias > [seleccione su id. de aplicación]> Ver detalles...**. Descargue todos los perfiles disponibles en Xcode en el equipo Mac.
7.  Vuelva al IDE y, debajo de las opciones **iOS Bundle Signing (Firma de compilación de iOS)**, seleccione el perfil de aprovisionamiento de distribución para la _Configuración de compilación_ correcta (esta será **App Store** o **Release**).

Para obtener instrucciones detalladas, vea las secciones [Crear un perfil de distribución](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioningprofile) y [Seleccionar un perfil de distribución en un proyecto de Xamarin.iOS](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile).

## <a name="setting-the-build-configuration-for-your-application"></a>Establecer la configuración de compilación de la aplicación

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Haga lo siguiente:

1. Haga clic con el botón derecho en el **Nombre del proyecto** en el **Panel de solución** y seleccione **Opción** para abrirlas para su edición.
2. Seleccione **Compilación de iOS** y **Versión | iPhone** en el elemento desplegable **Configuración**:

    ![](publishing-to-the-app-store-images/configurevs01.png "Selección de AppStore en la lista desplegable Configuración")

3. Si tiene una versión de iOS determinada a la que se va a dirigir, puede seleccionarla en la lista desplegable **Versión del SDK**; de lo contrario, deje este valor con la configuración predeterminada.
4. La vinculación reduce el tamaño total del distribuible de la aplicación eliminando clases, propiedades y métodos sin usar, entre otros. En la mayoría de los casos, debe dejarse el valor predeterminado de **Link SDK assemblies only (Vincular solo ensamblados del SDK)**. En algunas situaciones, como cuando se usan algunas bibliotecas de terceros determinadas, puede verse forzado a establecer este valor en **No vincular** para impedir que se quiten los elementos necesarios. Para obtener más información, consulte la guía [Mecánica de la compilación iOS](~/ios/deploy-test/ios-build-mechanics.md).
5. La casilla **Optimize PNG image files for iOS (Optimizar archivos de imagen PNG para iOS)** debe estar activada, ya que ayudará a disminuir más el tamaño del entregable de la aplicación.
6. La depuración _no_ debe habilitarse porque hará que la compilación sea innecesariamente grande.
8. En el caso de iOS 11, tendrá que seleccionar una de las arquitecturas de dispositivo que admite **ARM64**. Para obtener más información sobre la compilación de dispositivos iOS de 64 bits, vea la sección **Habilitar compilaciones de 64 bits de aplicaciones de Xamarin.iOS** de la documentación [32/64 bit Platform Considerations (Consideraciones de la plataforma de 32 o 64 bits)](~/cross-platform/macios/32-and-64/index.md).
9. Puede que quiera usar opcionalmente el compilador **LLVM** que crea código más rápido y más pequeño, en cambio, tarda más en compilarse.
10. Teniendo en cuenta las necesidades de la aplicación, puede que también quiera ajustar el tipo de **Recolección de elementos no usados** que se está usando y la configuración de **Internacionalización**.
11. Guarde los cambios en la configuración de compilación.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

De manera predeterminada, cuando crea una aplicación nueva de Xamarin.iOS en Visual Studio, las _configuraciones de compilación_ se crean automáticamente para la implementación **Ad Hoc** y **App Store**. Antes de realizar la compilación final de la aplicación que enviará a Apple, existen algunas modificaciones que necesitará realizar en la configuración base.

Haga lo siguiente:

1. Haga clic con el botón derecho en el **Nombre del proyecto** en el **Explorador de soluciones** y seleccione **Propiedades** para abrirlas para su edición.
2. Seleccione **Compilación de iOS** y **AppStore** (o **Versión** si AppStore no está disponible) en la lista desplegable **Configuración**:

    ![](publishing-to-the-app-store-images/configurevs01.png "Selección de AppStore en la lista desplegable Configuración")

3. Si tiene una versión de iOS determinada a la que se va a dirigir, puede seleccionarla en la lista desplegable **Versión del SDK**; de lo contrario, deje este valor con la configuración predeterminada.
4. La vinculación reduce el tamaño total del distribuible de la aplicación eliminando clases, propiedades y métodos sin usar, entre otros. En la mayoría de los casos, debe dejarse el valor predeterminado de **Link SDK assemblies only (Vincular solo ensamblados del SDK)**. En algunas situaciones, como cuando se usan algunas bibliotecas de terceros determinadas, puede verse forzado a establecer este valor en **No vincular** para impedir que se quiten los elementos necesarios. Para obtener más información, consulte la guía [Mecánica de la compilación iOS](~/ios/deploy-test/ios-build-mechanics.md).
5. La casilla **Optimize PNG image files for iOS (Optimizar archivos de imagen PNG para iOS)** debe estar activada, ya que ayudará a disminuir más el tamaño del entregable de la aplicación.
6. La depuración _no_ debe habilitarse porque hará que la compilación sea innecesariamente grande.
7. Haga clic en la pestaña **Opciones avanzadas**:

    ![](publishing-to-the-app-store-images/configurevs02.png "Pestaña Opciones avanzadas")

8. Si su aplicación de Xamarin.iOS tiene como destino dispositivos iOS de 64 bits e iOS 8, necesitará seleccionar una de las arquitecturas de dispositivos que admita **ARM64**. Para obtener más información sobre la compilación de dispositivos iOS de 64 bits, vea la sección **Habilitar compilaciones de 64 bits de aplicaciones de Xamarin.iOS** de la documentación [32/64 bit Platform Considerations (Consideraciones de la plataforma de 32 o 64 bits)](~/cross-platform/macios/32-and-64/index.md).
9. Puede que quiera usar opcionalmente el compilador **LLVM** que crea código más rápido y más pequeño, en cambio, tarda más en compilarse.
10. Teniendo en cuenta las necesidades de la aplicación, puede que también quiera ajustar el tipo de **Recolección de elementos no usados** que se está usando y la configuración de **Internacionalización**.
11. Guarde los cambios en la configuración de compilación.

-----

## <a name="building-and-submitting-the-distributable"></a>Compilación y envío del distribuible

Con la aplicación de Xamarin.iOS configurada correctamente, ahora está listo para realizar la compilación de distribución final que enviará a Apple para su revisión y publicación.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

### <a name="build-your-archive"></a>Compilar el archivo

1. Seleccione la configuración **Versión | Dispositivo** en Visual Studio para Mac:

    ![](publishing-to-the-app-store-images/buildxs01new.png "Selección de la configuración Versión | Dispositivo")
1. En el menú **Compilación**, seleccione **Archivo para publicar**:

    ![](publishing-to-the-app-store-images/buildxs02new.png "Selección de Archivo para publicar")

1. Una vez creado el archivo, se mostrará la vista **Archives (Archivos)**:

    ![](publishing-to-the-app-store-images/buildxs03new.png "Muestra de la vista Archivos")


> [!NOTE]
> Aunque las configuraciones _App Store_ y _Ad Hoc_ anteriores ahora se hayan quitado de todos los proyectos de plantilla de Visual Studio para Mac, todavía puede encontrar esos proyectos antiguos en estas configuraciones. Si este es el caso, puede seguir usando la configuración **App Store | Device (App Store | Dispositivo)** en el paso 1 de la lista anterior.

### <a name="sign-and-distribute-your-app"></a>Firmar y distribuir la aplicación

 Cada vez que compile la aplicación para el archivo, se abrirá automáticamente la **Archives View (Vista de archivos)**, que muestra todos los proyectos archivados; agrupados por solución. De manera predeterminada, esta vista solo muestra la solución abierta actual. Para ver todas las soluciones que tienen archivos, haga clic en la opción **Show all archives (Mostrar todos los archivos)**.

 Se recomienda que se mantengan los archivos que se implementan en los clientes (implementaciones App Store o Enterprise), por lo que cualquier información de depuración generada puede considerarse con una fecha posterior.

 Para firmar la aplicación y prepararla para la distribución:


1. Seleccione **Firmar y distribuir…**, que se muestra a continuación:

    ![](publishing-to-the-app-store-images/buildxs04new.png "Selección de Firmar y distribuir...")

1. De este modo, se abrirá el asistente para publicación. Seleccione el canal de distribución **App Store** para crear un paquete y abra Application Loader:

    ![](publishing-to-the-app-store-images/distribute01.png "Apertura de Application Loader")

1. En la pantalla del perfil de aprovisionamiento, seleccione su identidad de firma y el perfil de aprovisionamiento correspondiente o vuelva a iniciar sesión con otra identidad:

    ![](publishing-to-the-app-store-images/distribute02.png "Selección de la identidad de firma y el correspondiente perfil de aprovisionamiento")

1. Compruebe los detalles del paquete y haga clic en **Publicar** para guardar el paquete de `.ipa`:

    ![](publishing-to-the-app-store-images/distribute03.png "Comprobación de los detalles del paquete")

1. Una vez que `.ipa` se haya guardado, la aplicación está lista para cargarse en iTunes Connect mediante Application Loader:

    ![](publishing-to-the-app-store-images/distribute04.png "Pantalla de publicación realizada correctamente")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

El complemento de Xamarin para Visual Studio no admite en estos momentos el flujo de trabajo de archivado para publicar aplicaciones de iOS en App Store. Como resultado, debe cargar un archivo IPA que se ha creado mediante el comando **Compilar IPA ad hoc**, que se describe a continuación.


## <a name="build-an-ipa"></a>Compilar un archivo IPA

 En esta sección se describe la compilación de un archivo IPA, similar al flujo de trabajo cuando se usa la distribución Ad Hoc o Enterprise. En cambio, se firmará con el perfil de aprovisionamiento de App Store que se ha creado anteriormente.

 Haga lo siguiente:

1. Haga clic con el botón derecho en el nombre del proyecto de Xamarin.iOS en el **Explorador de soluciones** y seleccione **Properties (Propiedades)** para abrirlo y editarlo:

    ![](publishing-to-the-app-store-images/imagevs01.png "Selección de Propiedades")
1. Seleccione **Firma del lote de iOS** y cambie el perfil de aprovisionamiento a un perfil de aprovisionamiento de App Store:

    ![](publishing-to-the-app-store-images/ipa01.png "Selección de Firma del lote de iOS y cambie el perfil de aprovisionamiento a un perfil de aprovisionamiento de App Store")
1. Seleccione **Opciones IPA de iOS** y **Ad hoc** en la lista desplegable **Configuración** (si no aparece Ad hoc, seleccione **Versión** en su lugar):

    ![](publishing-to-the-app-store-images/imagevs02.png "Selección de Ad hoc en la lista desplegable Configuración")

1. También puede especificar un **nombre de paquete** para el IPA. En caso contrario, tendrá el mismo nombre que el proyecto de Xamarin.iOS.
1. Guarde los cambios en Project Properties (Propiedades del proyecto).
1. Seleccione **Ad hoc** en la lista desplegable **Configuración de compilación** de Visual Studio para Mac:

    ![](publishing-to-the-app-store-images/imagevs05.png "Selección de Ad hoc en la lista desplegable Configuración de compilación")
1. Compile el proyecto para crear el paquete IPA.
1. El archivo IPA se compilará en la carpeta `Bin` > _Dispositivo iOS_ > `Ad Hoc`:

    ![](publishing-to-the-app-store-images/imagevs06.png "IPA mostrado en el Explorador de archivos")

-----


## <a name="customizing-the-ipa-location"></a>Personalización de la ubicación del IPA

Se ha agregado una nueva propiedad `IpaPackageDir` de **MSBuild** para facilitar la personalización de la ubicación de salida del archivo `.ipa`. Si `IpaPackageDir` está establecido en una ubicación personalizada, el archivo `.ipa` se colocará en esa ubicación en lugar de en el subdirectorio predeterminado con marca de tiempo. Esto puede ser útil durante la creación automatizada de compilaciones que se basan en una ruta de acceso del directorio específica para funcionar correctamente, como las que se usan para las compilaciones de integración continua (CI).

Hay varias maneras de usar la nueva propiedad:

Por ejemplo, para provocar la salida del archivo `.ipa` al directorio predeterminado anterior (como en Xamarin.iOS 9.6 y versiones anteriores), puede establecer la propiedad `IpaPackageDir` en `$(OutputPath)` mediante uno de los siguientes enfoques. Ambos enfoques son compatibles con todas las compilaciones de Xamarin.iOS de Unified API, incluidas las compilaciones IDE, así como compilaciones de línea de comandos que usan `xbuild`, `msbuild` o `mdtool`:

  - La primera opción se basa en establecer la propiedad `IpaPackageDir` dentro de un elemento `<PropertyGroup>` en un archivo **MSBuild**. Por ejemplo, podría agregar el siguiente elemento `<PropertyGroup>` a la parte inferior del archivo `.csproj` del proyecto de la aplicación de iOS (justo antes de la etiqueta de cierre `</Project>`):

      ```xml
        <PropertyGroup>
            <IpaPackageDir>$(OutputPath)</IpaPackageDir>
        </PropertyGroup>
      ```
  - Un mejor enfoque consiste en agregar un elemento `<IpaPackageDir>` a la parte inferior del elemento `<PropertyGroup>` existente que corresponde a la configuración que se ha usado para compilar el archivo `.ipa`. Esta opción es mejor porque preparará el proyecto para la futura compatibilidad con una configuración prevista en la página de propiedades del proyecto de Opciones IPA para iOS (iOS IPA Options). Si actualmente usa la configuración de `Release|iPhone` para compilar el archivo `.ipa`, el grupo completo de propiedades actualizadas puede ser similar al siguiente:

      ```xml
      <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhone' )
        <Optimize>true</Optimize>
        <OutputPath>bin\iPhone\Release</OutputPath>
        <ErrorReport>prompt</ErrorReport>
        <WarningLevel>4</WarningLevel>
        <ConsolePause>false</ConsolePause>
        <CodesignKey>iPhone Developer</CodesignKey>
        <MtouchUseSGen>true</MtouchUseSGen>
        <MtouchUseRefCounting>true</MtouchUseRefCounting>
        <MtouchFloat32>true</MtouchFloat32>
        <CodesignEntitlements>Entitlements.plist</CodesignEntitlements>
        <MtouchLink>SdkOnly</MtouchLink>
        <MtouchArch>;ARMv7, ARM64</MtouchArch>
        <MtouchHttpClientHandler>HttpClientHandler</MtouchHttpClientHandler>
        <MtouchTlsProvider>Default</MtouchTlsProvider>
        <PlatformTarget>x86&</PlatformTarget>
        <BuildIpa>true</BuildIpa>
        <IpaPackageDir>$(OutputPath</IpaPackageDir>
      </PropertyGroup>
      ```
Una técnica alternativa para las compilaciones de línea de comandos `msbuild` o `xbuild` es agregar un argumento de línea de comandos `/p:` para establecer la propiedad `IpaPackageDir`. Observe que, en este caso, `msbuild` no expande expresiones `$()` que se pasan a la línea de comandos, por lo que no es posible usar la sintaxis `$(OutputPath)`. En su lugar, debe proporcionar un nombre de ruta de acceso completa. El comando `xbuild` de Mono expande las expresiones `$()`, pero sigue siendo preferible usar un nombre de ruta de acceso completa porque `xbuild` quedará obsoleto finalmente en favor de la [versión multiplataforma de `msbuild`](http://www.mono-project.com/docs/about-mono/releases/4.4.0/#msbuild-preview-for-os-x) en versiones futuras. Un ejemplo completo que usa este enfoque podría ser similar al siguiente en Windows:

```bash
msbuild /p:Configuration="Release" /p:Platform="iPhone" /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser" /p:IpaPackageDir="%USERPROFILE%\Builds" /t:Build SingleViewIphone1.sln
```

O al siguiente en Mac:

```bash
xbuild /p:Configuration="Release" /p:Platform="iPhone" /p:IpaPackageDir="$HOME/Builds" /t:Build SingleViewIphone1.sln
```

Una vez que se ha creado y archivado su compilación de distribución, ahora está listo para enviar su aplicación a iTunes Connect.

### <a name="automatically-copy-app-bundles-back-to-windows"></a>Copia automática de lotes de aplicaciones .app en Windows

[!include[](~/ios/includes/copy-app-bundle-to-windows.md)]

## <a name="submitting-your-app-to-apple"></a>Enviar su aplicación a Apple

> [!NOTE]
> Apple ha cambiado recientemente su proceso de comprobación para las aplicaciones de iOS, y puede rechazar aplicaciones con el elemento `iTunesMetadata.plist` incluido en el archivo IPA. Si encuentra el error `ERROR: ERROR ITMS-90047: "Disallowed paths ( "iTunesMetadata.plist" ) found at: Payload/iPhoneApp1.app"`, la solución alternativa que se describe [aquí](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1) debe resolver el problema.

Una vez que se ha completado la compilación de distribución, está listo para enviar su aplicación de iOS a Apple para su revisión y publicación en el App Store.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

 Haga lo siguiente:

1. Inicie **Xcode**.
2. Desde el menú **Ventana**, seleccione **Organizador**.
3. Haga clic en la pestaña **Archivos** y seleccione el archivo que se ha compilado anteriormente:

    ![](publishing-to-the-app-store-images/publishxs01.png "Selección del archivo que se va a enviar")
4. Haga clic en el botón **Validar...**
5. Seleccione la cuenta que se va a comprobar y haga clic en el botón **Elegir**:

    ![](publishing-to-the-app-store-images/publishxs02.png "Selección de la cuenta en la que se va a comprobar")
6. Haga clic en el botón **Validar**:

    ![](publishing-to-the-app-store-images/publishxs03.png "Cuadro de diálogo Resumen de archivo")
7. Si existe algún problema con la agrupación, se mostrará ahora.
8. Corrija cualquier problema y vuelva a compilar el archivo en Visual Studio para Mac.
9. Haga clic en el botón **Enviar...**
10. Seleccione la cuenta que se va a comprobar y haga clic en el botón **Elegir**:

    ![](publishing-to-the-app-store-images/publishxs04.png "Selección de la cuenta en la que se va a comprobar")
11. Haga clic en el botón **Enviar**:

    ![](publishing-to-the-app-store-images/publishxs05.png "Cuadro de diálogo Resumen de archivo")
12. Xcode le informará cuando finalice la carga del archivo en iTunes Connect.


El flujo de trabajo del archivo en Visual Studio para Mac abrirá Application Loader automáticamente, una vez que haya guardado el archivo _.ipa_:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

El envío de su aplicación a Apple para su revisión se realiza mediante la aplicación Application Loader. Estos pasos deben realizarse en el host de la compilación de Mac. Puede descargar una copia de Application Loader desde [aquí](https://itunesconnect.apple.com/apploader/ApplicationLoader_3.0.dmg).

-----

1. Seleccione *Deliver Your App* (Entregar la aplicación) y haga clic en el botón *Elegir*:

    [![](publishing-to-the-app-store-images/publishvs01.png "Selección de Deliver Your App (Entregar la aplicación)")](publishing-to-the-app-store-images/publishvs01.png#lightbox)

2. Seleccione el archivo IPA o ZIP que ha creado anteriormente y haga clic en el botón **Aceptar**.

3. Application Loader validará el archivo:

    [![](publishing-to-the-app-store-images/publishvs02.png "Pantalla de validación")](publishing-to-the-app-store-images/publishvs02.png#lightbox)
4. Haga clic en el botón *Siguiente* y la aplicación se validará en el App Store:

    [![](publishing-to-the-app-store-images/publishvs03.png "Validación en App Store")](publishing-to-the-app-store-images/publishvs03.png#lightbox)
5. Haga clic en el botón **Enviar** para enviar la aplicación a Apple para su revisión.
6. Application Loader le informará cuando el archivo se haya cargado correctamente.

## <a name="itunes-connect-status"></a>Estado de iTunes Connect

Si vuelve a registrarse en iTunes Connect y selecciona su aplicación de la lista de aplicaciones disponibles, el estado en iTunes Connect debe mostrar ahora que es **Waiting for Review (Esperando revisión)** (puede mostrar **Upload Received (Carga recibida)** temporalmente mientras se procesa):

[![](publishing-to-the-app-store-images/image21.png "El estado en iTunes Connect debe mostrar que está esperando revisión")](publishing-to-the-app-store-images/image21.png#lightbox)

## <a name="summary"></a>Resumen

En este artículo se ha presentado una guía paso a paso para configurar, compilar y enviar una aplicación para su publicación en el App Store. Primero, ha tratado los pasos necesarios para crear e instalar un perfil de aprovisionamiento de distribución. Después, ha avanzado a través de la manera de usar Visual Studio y Visual Studio para Mac para crear una compilación de distribución. Por último, ha mostrado cómo usar iTunes Connect y la herramienta para enviar una aplicación a App Store.


## <a name="related-links"></a>Vínculos relacionados

- [Working with Images (Trabajo con imágenes)](~/ios/app-fundamentals/images-icons/index.md)
- [Guía de flujo de trabajo de desarrollo de aplicaciones de iOS: distribución de aplicaciones](http://developer.apple.com/library/ios/#documentation/Xcode/Conceptual/ios_development_workflow/35-Distributing_Applications/distributing_applications.html)
- [Sugerencias de envío al App Store](https://developer.apple.com/appstore/resources/submission/tips.html)
- [Rechazos de aplicaciones comunes](https://developer.apple.com/app-store/review/rejections/)
- [Directrices de revisión del App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html)
