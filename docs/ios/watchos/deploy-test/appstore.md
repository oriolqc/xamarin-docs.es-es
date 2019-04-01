---
title: Implementar aplicaciones watchOS en la aplicación de Store
description: Este documento describe cómo implementar las aplicaciones para watchOS con Xamarin a la aplicación de Store. Echa un vistazo a iTunes Connect y los perfiles de aprovisionamiento de distribución, y también proporciona algunas sugerencias para solucionar problemas.
ms.prod: xamarin
ms.assetid: DBE16040-70D2-4F61-B5F3-C8D213DBC754
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: bf86759832a1aba0ccc1c144981af6ea4eae8670
ms.sourcegitcommit: 946ce514fd6575aa6b93ff24181e02a60b24b106
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/30/2019
ms.locfileid: "58677799"
---
# <a name="deploying-watchos-apps-to-the-app-store"></a>Implementar aplicaciones watchOS en la aplicación de Store

> [!IMPORTANT]
> No olvide revisar [Guía de envío del Kit de Apple Watch](https://developer.apple.com/app-store/watch/)y ver el [Troubleshooting](#troubleshooting) sección para los problemas que pueda tener.

- Asegúrese de que tener:
  - [**Perfiles de aprovisionamiento de distribución** ](#provisioning) creados para sus proyectos.
  - El **destino de implementación** (`MinimumOSVersion`) para iOS principal aplicación establecido en **8.2** o versiones anteriores (no se admite 8.3).

- En [ **iTunes Connect**](#iTunes_Connect):

  - Cree su iOS de entrada de la aplicación (o agregar un **nueva versión** a una aplicación existente).
  - Agregar las capturas de pantalla y el icono de inspección.

- A continuación, en [Visual Studio para Mac](#xamarin_studio) (Visual Studio no se admite actualmente):

  - Haga clic en la aplicación de iOS y elija **establecer como proyecto de inicio**.
  - Cambie a la **App Store** configuración.
  - Use la **archivo** característica crear un archivo de aplicación.

- Por último, cambie a [Xcode 6.2 +](#xcode)

  - Vaya a la **Ventana > Organizador** y elija **archivos**.
  - Seleccione la aplicación y el archivo de la lista.
  - (Opcionalmente) **Validar...**  el archivo.
  - **Enviar...**  el archivo y siga los pasos para cargar en iTunes conexión para su revisión y aprobación.

Lea sugerencias específicas relacionadas con estos elementos a continuación. Consulte la [Troubleshooting](#troubleshooting) sección si tiene problemas.

<a name="provisioning" />

## <a name="distribution-provisioning-profiles"></a>Los perfiles de aprovisionamiento de distribución

Para crear aplicaciones para la implementación de App Store, deberá crear un **perfil de aprovisionamiento de distribución** para cada identificador de aplicación de la solución.

Si tiene un Id. de aplicación, comodín *perfil de aprovisionamiento de un solo será necesario*; pero si tiene un identificador de aplicación independiente para cada proyecto, necesitará un perfil de aprovisionamiento para cada identificador de aplicación:

![](appstore-images/provisioningprofile-distribution-sml.png "El perfil de distribución de App Store")

Una vez que haya creado los tres perfiles, aparecen en la lista. No olvide descargar e instalar cada uno de ellos (haciendo doble clic en él):

![](appstore-images/provisioningprofiles-sml.png "La lista de perfiles disponibles")

Puede comprobar el perfil de aprovisionamiento en la **opciones de proyecto** seleccionando el **compilar > firma de lote de iOS** pantalla y seleccionando la **AppStore | iPhone** configuración.

El **perfil de aprovisionamiento** lista mostrará todos los perfiles coincidentes; debería ver los perfiles coincidentes que ha creado en la lista desplegable.

![](appstore-images/options-selectprofile-sml.png "El cuadro de diálogo de firma de lote de iOS")

<a name="iTunes_Connect"/>

## <a name="itunes-connect"></a>iTunes Connect

Siga el [información general sobre la distribución de aplicaciones](~/ios/deploy-test/app-distribution/index.md), en particular:

- [Configuración de una aplicación en iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Publicación en App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)

Al configurar la aplicación en iTunes Connect, no olvide agregar el icono de inspección y capturas de pantalla:

![](appstore-images/itunesconnect-watch-sml.png "El icono de inspección y capturas de pantalla en iTunes Connect")

El archivo de icono debe ser de 1024 x 1024 píxeles y tendrá una máscara circular aplicada a él cuando se muestre. El icono no debe tener un canal alfa.

Captura de pantalla de al menos uno es necesario, hasta cinco pueden enviarse.
Deben ser 312 x 390 píxeles y demostrar la aplicación de inspección en acción.
Puede usar el simulador de inspección de 42mm tomar capturas de pantalla a este tamaño.


<a name="xamarin_studio" />

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. Asegúrese de que la aplicación de iOS es el proyecto de inicio. Si no es así, haga doble clic para instalarlo:

  ![](appstore-images/xs-startup.png "Establecer el proyecto de inicio")

2. Elija la **AppStore** configuración de compilación:

  ![](appstore-images/xs-appstore.png "La configuración de compilación AppStore")

3. Elija la **compilar > archivo** elemento de menú para iniciar el proceso de archivo:

  ![](appstore-images/xs-archive.png "El menú compilar")

También puede elegir el **Vista > archivos...**  elemento de menú para ver los archivos que se han creado anteriormente.

  ![](appstore-images/xs-archives-sml.png "La vista archivos")

<a name="xcode" />

## <a name="xcode"></a>Xcode

Xcode mostrará automáticamente los archivos creados en Visual Studio para Mac.

1. Iniciar Xcode y elija **Ventana > Organizador**:

  ![](appstore-images/xc-organizer.png "El menú Ventana")

2. Cambie a la **archivos** pestaña y seleccione el archivo que se creó con Visual Studio para Mac:

  ![](appstore-images/xc-archives.png "La pestaña archivos")

3. Opcionalmente, **validar...**  el archivo, a continuación, elija **enviar...**  para cargar la aplicación en iTunes Connect.

4. Elija el equipo de desarrollo (si pertenece a más de uno) y, a continuación, confirme el envío:

  ![](appstore-images/xc-submit1.png "La sección del equipo de desarrollo")

5. Visite iTunes Connect nuevamente para ver el binario cargado. Vaya a la página de configuración de la aplicación y elija **Prerelease** en el menú superior para ver el **compilaciones** lista:

  [![](appstore-images/itc-prerelease-sml.png "La página de configuración de aplicaciones en iTunes Connect")](appstore-images/itc-prerelease.png#lightbox)

A continuación, puede enviar la aplicación para su aprobación en la **versiones** página. Hacer referencia a la [información general sobre la distribución de aplicaciones de iOS](~/ios/deploy-test/app-distribution/index.md) para obtener más información.


## <a name="troubleshooting"></a>Solución de problemas

Estos son algunos errores que pueden surgir al enviar a la aplicación de Store y los pasos que puede seguir para solucionarlos.

### <a name="archive-menu-option-is-not-visible-in-visual-studio-for-mac"></a>Opción de menú de archivo no está visible en Visual Studio para Mac

Siga el [pasos anteriores](#xamarin_studio) para configurar la solución para archivar. Si no se establece correctamente el proyecto de inicio, asegúrese de que se establece primero la configuración de compilación para depurar o liberar antes de intentar cambiar el proyecto de inicio. A continuación, establezca la configuración de compilación de nuevo en **AppStore**.

### <a name="invalid-icon"></a>Icono no válido

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/AppIcon27.5x27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

Siga el [instrucciones para quitar el canal alfa](~/ios/watchos/troubleshooting.md) en sus iconos.

### <a name="cfbundleversion-mismatch"></a>Error de coincidencia del valor de CFBundleVersion

```csharp
CFBundleVersion Mismatch. The CFBundleVersion value '1' of watch application
'...watchkitextension.appex/WatchApp.app' does not match the CFBundleVersion
value '1.0' of its containing iOS application `YouriOS.app`.
```

Todos los proyectos de la solución de la aplicación de iOS, la extensión de inspección y la aplicación Watch - deberían usar el mismo número de versión. Edite cada uno **Info.plist** archivo para que el número de versión coincide exactamente.

### <a name="missing-icons"></a>Faltan iconos

```csharp
Missing Icons. No icons found for watch application '...watchkitextension.appex/WatchApp.app'.
Please make sure that its Info.plist file includes entries for CFBundleIconFiles.
```

Siga las instrucciones de la [trabajar con iconos](~/ios/watchos/app-fundamentals/icons.md) para agregar todas las imágenes necesarias para el proyecto de aplicación de inspección.

### <a name="missing-icon"></a>Falta un icono

```csharp
Missing Icon. The watch application '...watchkitextension.appex/WatchApp.app'
is missing icon with name pattern '*44x44@2x.png' (Home Screen 42mm).
```

Asegúrese de tener la versión más reciente de Visual Studio para Mac y que su **AppIcon.appiconset** contiene un conjunto completo de imágenes. Si sigue viendo este error, vea el origen de la **Contents.json** para confirmar que contiene una entrada para todas las imágenes necesarias. Como alternativa, una vez que se ha asegurado de que está usando la versión más reciente de Xamarin, eliminar y volver a crear la **AppIcon.appiconset**.

> [!IMPORTANT]
> Hay un problema conocido en Visual Studio para la compatibilidad con icono de inspección de Mac: espera que una imagen de píxel de 88 x 88 para la **29x29@3x** imagen (que debe ser 87 x 87 píxeles).


No se puede solucionar este problema en Visual Studio para Mac: modificar el recurso de imagen en Xcode o editar manualmente el **Contents.json** archivo (para que coincida con [este ejemplo](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132)).



### <a name="invalid-watchkit-support"></a>Soporte técnico de WatchKit no válidos

```csharp
Invalid WatchKit Support - The bundle contains an invalid implementation of WatchKit.
The app may have been built or signed with non-compliant or pre-release tools.
```

Este mensaje puede aparecer durante la validación y presentación, o en un correo electrónico automatizado de iTunes Connect después de una carga aparentemente correcta.
<!--
Ensure you are using the latest version of Xcode and Xamarin's tools.
-->
> [!IMPORTANT]
> Debe **archivo** la aplicación en Visual Studio para Mac y, a continuación, cambie a Xcode 6.2 + validar y cargar en iTunes Connect.


Use el canal estable Xamarin y Xcode 6.2 +.



### <a name="invalid-provisioning-profile"></a>Perfil de aprovisionamiento no válido

```csharp
Invalid Provisioning Profile. The provisioning profile included in the bundle
...iOSWatchApp.watchkitapp [iOSWatchApp.app/PlugIns/...iOSWatchApp.watchkitextension.appex/WatchApp.app]
is invalid. [Missing code-signing certificate.]
```

**Perfiles de aprovisionamiento de distribución** debe proporcionarse para los tres proyectos en una solución de aplicación de inspección: la aplicación de iOS, la extensión de inspección y la aplicación Watch - ya sea explícitamente (tres perfiles) o a través de un perfil único carácter comodín. Compruebe que los perfiles de aprovisionamiento existen en el centro de desarrollo de iOS y que ha descargado y agregarlos a su equipo Mac.

### <a name="invalid-code-signing-entitlements"></a>Los derechos de firma de código no válido

```csharp
ITMS-90046: Invalid Code Signing Entitlements. Your application bundle's signature contains
code signing entitlements that are not supported on iOS. Specifically, value
'...watchkitextension' for key 'application-identifier' in '...watchkitextension'
is not supported. The value should be a string startign with your TEAMID, followed
by a dot '.' followed by the bundle identifier.
```

Asegúrese de los perfiles de aprovisionamiento están configurados correctamente en el centro de desarrollo de Apple y que ha descargado e instalado en ellos. Compruebe también que se establecen en Visual Studio para la ventana de propiedades del equipo Mac para cada proyecto.

### <a name="invalid-architecture"></a>Arquitectura no válido

```csharp
Invalid architecture: Apps that include an app extension
and framework must support arm64.
```

Solo puede agregar aplicaciones de Watch [Unified API (64 bits)](~/cross-platform/macios/unified/index.md) aplicaciones Xamarin.iOS.
Haga doble clic en el proyecto de aplicación de iOS, a continuación, vaya a **Opciones > compilar > compilación de iOS > pestaña avanzadas** y asegúrese de que el **arquitecturas compatibles** para el iPhone AppStore configuración incluye **ARM64** (p ej. **ARMv7 + ARM64**).

### <a name="this-bundle-is-invalid"></a>Este paquete no es válido.

```csharp
ITMS-90068: This bundle is invalid. The value provided for the key
MinimumOSVersion '8.3' is not acceptable.
```

La aplicación de iOS primario debe tener el MinimumOSVersion establecido en '8.2' o versiones anteriores.

### <a name="non-public-api-usage"></a>Uso de la API no públicos

```csharp
Your app contains non-public API usage.
Please review the errors, and resubmit your application.
```

Asegúrese de que usa la versión más reciente de las herramientas de Xcode y Xamarin.
El código no debe tener acceso a ninguna API que no sean públicos.

### <a name="build-error-mt5309"></a>Crear Error MT5309

```csharp
Error MT5309: Native linking error: clang: error: no such file or directory:
```

Este error probablemente es el resultado de la necesidad de cambiar el nombre de la instalación de Xcode desde **Xcode.app**. Por ejemplo, se producirá este error si cambia el nombre de la instalación a **XCode 6.2.app**.



## <a name="related-links"></a>Vínculos relacionados

- [Guía de envío de WatchKit de Apple](https://developer.apple.com/app-store/watch/)
