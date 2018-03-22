---
title: "Implementación en la tienda de aplicaciones"
description: "Implementación de aplicaciones de inspección en la tienda de aplicaciones"
ms.topic: article
ms.prod: xamarin
ms.assetid: DBE16040-70D2-4F61-B5F3-C8D213DBC754
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: c5b89570fdd3df80d39c6621fcd12a23babed9ee
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/22/2018
---
# <a name="deploying-to-the-app-store"></a>Implementación en la tienda de aplicaciones

> [!IMPORTANT]
> No olvide revisar [Watch Kit envío Guía de Apple](https://developer.apple.com/app-store/watch/)y ver el [solución de problemas](#Troubleshooting) sección es posible que tenga problemas.

- Asegúrese de que tener:
  - [**Perfiles de aprovisionamiento de distribución** ](#provisioning) creado para sus proyectos.
  - El **destino de implementación** (`MinimumOSVersion`) para iOS primario establecido de la aplicación en **8.2** o versiones anteriores (no se admite 8.3).

- En [ **iTunes Connect**](#iTunes_Connect):

  - Crear su iOS entrada de la aplicación (o agregar un **nueva versión** a una aplicación existente).
  - Agregar capturas de pantalla y el icono de inspección.

- A continuación, en [Visual Studio para Mac](#xamarin_studio) (Visual Studio no se admite actualmente):

  - Haga clic en la aplicación de iOS y elija **establecer como proyecto de inicio**.
  - Cambie a la **App Store** configuración.
  - Use la **archivo** característica crear un archivo de aplicación.

- Por último, cambie a [Xcode 6.2 +](#xcode)

  - Vaya a la **Ventana > Organizador** y elija **archivos**.
  - Seleccione la aplicación y el archivo de la lista.
  - (Opcional) **Validar...**  el archivo.
  - **Enviar...**  el archivo y siga los pasos para cargar en iTunes conectan para su revisión y aprobación.

Lea sugerencias específicas relacionadas con estos elementos a continuación. Consulte la [solución de problemas](#Troubleshooting) sección si tiene problemas.

<a name="provisioning" />

## <a name="distribution-provisioning-profiles"></a>Distribución de perfiles de aprovisionamiento

Para compilar para la implementación de la tienda de aplicaciones debe crear un **perfil de aprovisionamiento de distribución** para cada identificador de aplicación de la solución.

Si tiene un carácter comodín ID de aplicación, *solamente un perfil de aprovisionamiento será necesario*; pero si tiene un identificador de aplicación independiente para cada proyecto, a continuación, necesitará un perfil de aprovisionamiento para cada Id. de aplicación:

![](appstore-images/provisioningprofile-distribution-sml.png "El perfil de distribución de la tienda de aplicaciones")

Una vez que haya creado los tres perfiles, aparecen en la lista. No olvide descargar e instalar cada uno de ellos (haciendo doble clic en ella):

![](appstore-images/provisioningprofiles-sml.png "La lista de perfiles disponibles")

Puede comprobar el perfil de aprovisionamiento en el **opciones de proyecto** seleccionando el **generar > agrupación de firma de iOS** pantalla y seleccionando la **AppStore | iPhone** configuración.

El **perfil de aprovisionamiento de** lista mostrará todos los perfiles coincidentes: debería ver los perfiles de coincidencias que ha creado en esta lista desplegable.

![](appstore-images/options-selectprofile-sml.png "El cuadro de diálogo de la agrupación de firma de iOS")

<a name="iTunes_Connect"/>

## <a name="itunes-connect"></a>iTunes Connect

Siga el [información general de distribución de aplicaciones](~/ios/deploy-test/app-distribution/index.md), en particular:

- [Configurar una aplicación en iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Publicación en App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)

Al configurar la aplicación en iTunes Connect, no olvide agregar el icono de inspección y capturas de pantalla:

![](appstore-images/itunesconnect-watch-sml.png "El icono de inspección y capturas de pantalla en iTunes Connect")

El archivo de icono debe ser 1024 x 1024 píxeles y tendrá una máscara circular aplicada a él cuando se muestre. El icono no debería tener un canal alfa.

Captura de pantalla de al menos uno es necesario, hasta cinco se puede enviar.
Deben ser 312 x 390 píxeles y mostrar la aplicación de inspección en acción.
Puede usar el simulador de inspección de 42mm tomar capturas de pantalla a este tamaño.


<a name="xamarin_studio" />

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. Asegúrese de que la aplicación de iOS es el proyecto de inicio. Si no es así, haga clic en para instalarlo:

  ![](appstore-images/xs-startup.png "Establecer el proyecto de inicio")

2. Elija la **AppStore** configuración de compilación:

  ![](appstore-images/xs-appstore.png "La configuración de compilación de la tienda")

3. Elija la **generar > archivo** elemento de menú para iniciar el proceso de archivo:

  ![](appstore-images/xs-archive.png "El menú compilar")

También puede elegir la **Vista > archivos...**  elemento de menú para ver los archivos que se han creado previamente.

  ![](appstore-images/xs-archives-sml.png "La vista de archivos")

<a name="xcode" />

## <a name="xcode"></a>Xcode

Xcode mostrará automáticamente los archivos creados en Visual Studio para Mac.

1. Iniciar Xcode y elija **Ventana > Organizador**:

  ![](appstore-images/xc-organizer.png "El menú Ventana")

2. Cambie a la **archivos** pestaña y seleccione el archivo al que se creó con Visual Studio para Mac:

  ![](appstore-images/xc-archives.png "La pestaña archivos")

3. Si lo desea **validar...**  el archivo, a continuación, elija **enviar...**  para cargar la aplicación a iTunes Connect.

4. Elija el equipo de desarrollo (si pertenece a más de uno) y, a continuación, confirme el envío:

  ![](appstore-images/xc-submit1.png "La sección del equipo de desarrollo")

5. Visite iTunes Connect nuevo, para ver el archivo binario cargado. Vaya a la página de configuración de la aplicación y elija **versión preliminar** en el menú superior para ver el **compilaciones** lista:

  [![](appstore-images/itc-prerelease-sml.png "La página de configuración de aplicaciones en iTunes Connect")](appstore-images/itc-prerelease.png#lightbox)

También puede enviar, a continuación, la aplicación para su aprobación en la **versiones** página. Hacer referencia a la [información general de distribución de aplicaciones de iOS](~/ios/deploy-test/app-distribution/index.md) para obtener más información.


## <a name="troubleshooting"></a>Solución de problemas

Estos son algunos errores que puede surgir al enviar a la tienda de aplicaciones y los pasos que puede seguir para solucionarlos.

### <a name="archive-menu-option-is-not-visible-in-visual-studio-for-mac"></a>Opción de menú archivo no está visible en Visual Studio para Mac

Siga el [pasos anteriores](#xamarin_studio) para configurar la solución para el archivado. Si no se puede establecer el proyecto de inicio correctamente, asegúrese de que la configuración de compilación se establece primero en la depuración o lanzamiento antes de intentar cambiar el proyecto de inicio. Establezca la configuración de compilación de nuevo a **AppStore**.

### <a name="invalid-icon"></a>Icono no válido

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/AppIcons27.5x27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

Siga el [instrucciones para quitar el canal alfa](~/ios/watchos/troubleshooting.md) de los iconos.

### <a name="cfbundleversion-mismatch"></a>CFBundleVersion Mismatch

```csharp
CFBundleVersion Mismatch. The CFBundleVersion value '1' of watch application
'...watchkitextension.appex/WatchApp.app' does not match the CFBundleVersion
value '1.0' of its containing iOS application `YouriOS.app`.
```

Todos los proyectos de la solución de la aplicación de iOS, la extensión de inspección y la aplicación de inspección - deberían estar utilizando el mismo número de versión. Editar cada **Info.plist** de archivos para que el número de versión coincide exactamente.

### <a name="missing-icons"></a>Faltan iconos

```csharp
Missing Icons. No icons found for watch application '...watchkitextension.appex/WatchApp.app'.
Please make sure that its Info.plist file includes entries for CFBundleIconFiles.
```

Siga las instrucciones de la [trabajar con iconos](~/ios/watchos/app-fundamentals/icons.md) para agregar todas las imágenes necesarias para el proyecto de aplicación de inspección.

### <a name="missing-icon"></a>Falta el icono

```csharp
Missing Icon. The watch application '...watchkitextension.appex/WatchApp.app'
is missing icon with name pattern '*44x44@2x.png' (Home Screen 42mm).
```

Asegúrese de tener la versión más reciente de Visual Studio para Mac y que su **AppIcons.appiconset** contiene un conjunto completo de imágenes. Si todavía aparece este error, vea el código fuente de la **Contents.json** para confirmar contiene una entrada para todas las imágenes necesarias. O bien, una vez que se ha asegurado de que está utilizando la versión más reciente de Xamarin, eliminar y volver a crear la **AppIcons.appiconset**.

> [!IMPORTANT]
> Hay un problema conocido en Visual Studio para la compatibilidad con icono de inspección de Mac: espera una imagen 88 x 88 píxeles para el  **29x29@3x**  imagen (que debe ser 87 x 87 píxeles).


No se puede solucionar este problema en Visual Studio para Mac: modificar el recurso de imagen en Xcode o editar manualmente el **Contents.json** archivo (para que coincida con [este ejemplo](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132)).



### <a name="invalid-watchkit-support"></a>Compatibilidad de WatchKit no válido

```csharp
Invalid WatchKit Support - The bundle contains an invalid implementation of WatchKit.
The app may have been built or signed with non-compliant or pre-release tools.
```

Este mensaje puede aparecer durante la validación y el envío, o en un correo electrónico automático de iTunes Connect después de la carga aparentemente correcta.
<!--
Ensure you are using the latest version of Xcode and Xamarin's tools.
-->
> [!IMPORTANT]
> Debe **archivo** la aplicación en Visual Studio para Mac y, a continuación, cambiar a Xcode 6.2 + validar y cargar en iTunes Connect.


Use el canal de Xamarin estable y Xcode 6.2 +.



### <a name="invalid-provisioning-profile"></a>Perfil de aprovisionamiento no válido

```csharp
Invalid Provisioning Profile. The provisioning profile included in the bundle
...iOSWatchApp.watchkitapp [iOSWatchApp.app/PlugIns/...iOSWatchApp.watchkitextension.appex/WatchApp.app]
is invalid. [Missing code-signing certificate.]
```

**Perfiles de aprovisionamiento de distribución** debe especificarse para los tres proyectos en una solución de aplicación de inspección: la aplicación de iOS, la extensión de inspección y la aplicación de inspección: ya sea explícitamente (tres perfiles) o a través de un perfil único carácter comodín. Compruebe que los perfiles de aprovisionamiento existen en el centro de desarrollo de iOS y que han descargado y agregarlos a su equipo Mac.

### <a name="invalid-code-signing-entitlements"></a>Derechos de firma de código no válido

```csharp
ITMS-90046: Invalid Code Signing Entitlements. Your application bundle's signature contains
code signing entitlements that are not supported on iOS. Specifically, value
'...watchkitextension' for key 'application-identifier' in '...watchkitextension'
is not supported. The value should be a string startign with your TEAMID, followed
by a dot '.' followed by the bundle identifier.
```

Asegúrese de los perfiles de aprovisionamiento están configurados correctamente en el centro de desarrollo de Apple y que ha descargado e instalado. Compruebe también que se establecen en Visual Studio para la ventana de propiedades de Mac para cada proyecto.

### <a name="invalid-architecture"></a>Arquitectura no válida

```csharp
Invalid architecture: Apps that include an app extension
and framework must support arm64.
```

Solo puede agregar inspección aplicaciones [API unificada (64 bits)](~/cross-platform/macios/unified/index.md) Xamarin.iOS aplicaciones.
Haga doble clic en el proyecto de aplicación de iOS, a continuación, vaya a **Opciones > compilar > iOS compilación > ficha Opciones avanzadas** y asegúrese de que el **admite arquitecturas** para iPhone AppStore configuración incluye **ARM64** (p. ej. **ARMv7 + ARM64**).

### <a name="this-bundle-is-invalid"></a>Este paquete no es válido.

```csharp
ITMS-90068: This bundle is invalid. The value provided for the key
MinimumOSVersion '8.3' is not acceptable.
```

La aplicación de iOS principal debe tener el MinimumOSVersion establecido en '8.2' o versiones anteriores.

### <a name="non-public-api-usage"></a>Uso de la API no públicos

```csharp
Your app contains non-public API usage.
Please review the errors, and resubmit your application.
```

Asegúrese de que está utilizando la versión más reciente de herramientas Xcode y Xamarin.
El código no debe tener acceso a las API no público.

### <a name="build-error-mt5309"></a>Generar Error MT5309

```csharp
Error MT5309: Native linking error: clang: error: no such file or directory:
```

Este error suele ser el resultado de su necesidad cambiar el nombre de la instalación de Xcode de **Xcode.app**. Por ejemplo, este error se producirá si cambia el nombre de la instalación a **XCode 6.2.app**.



## <a name="related-links"></a>Vínculos relacionados

- [Guía de Apple WatchKit envío](https://developer.apple.com/app-store/watch/)
