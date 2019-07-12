---
title: Solucionar problemas de aplicaciones de tvOS con Xamarin
description: Este artículo proporciona algunas sugerencias para ayudar a solucionar problemas durante el desarrollo de una aplicación de tvOS con Xamarin. Describe un problema conocido y errores específicos.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 124E4953-4DFA-42B0-BCFC-3227508FE4A6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 6830df267aa0b9c4f12fbd53520206ea94fc8a38
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831892"
---
# <a name="troubleshooting-tvos-apps-built-with-xamarin"></a>Solucionar problemas de aplicaciones de tvOS con Xamarin

_En este artículo cubre sabe los problemas que pueden producirse al trabajar con el soporte técnico de tvOS de Xamarin._

<a name="Known-Issues" />

## <a name="known-issues"></a>Problemas conocidos

La versión actual de la compatibilidad con tvOS de Xamarin tiene los siguientes problemas conocidos:

- **Marco de trabajo mono** : no se puede descifrar los datos de Mono 4.2 Mono 4.3 Cryptography.ProtectedData. Como resultado, se producirá un error paquetes de NuGet restaurar el error `Data unprotection failed` cuando se configura un origen de NuGet protegidas.
    - **Solución alternativa** : en Visual Studio para Mac, deberá volver a agregar los orígenes de los paquetes de NuGet que use la autenticación de contraseña antes de volver a intentar restaurar los paquetes.
- **Visual Studio para Mac con F# complemento** : Error al crear un F# plantilla Android en Windows. Esto todavía debería funcionar correctamente en el equipo Mac.
- **Xamarin.Mac** : al ejecutar el proyecto de plantillas unificada de Xamarin.Mac con el Framework de destino establecido en `Unsupported`, el elemento emergente `Could not connect to the debugger` pueden aparecer.
    - **Posible solución** : cambiar la versión de Mono framework disponible en nuestro canal estable.
- **Xamarin Visual Studio y Xamarin.iOS** : al implementar aplicaciones de WatchKit en Visual studio, el error `The file ‘bin\iPhoneSimulator\Debug\WatchKitApp1WatchKitApp.app\WatchKitApp1WatchKitApp’ does not exist` pueden aparecer.

Informe de cualquiera de los errores encontrará en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

## <a name="troubleshooting"></a>solución de problemas

Las siguientes secciones enumeran algunos problemas conocidos que pueden producirse al usar tvOS 9 con Xamarin.tvOS y la solución a estos problemas:

### <a name="invalid-executable---the-executable-does-not-contain-bitcode"></a>Ejecutable no válido: el archivo ejecutable no contienen bitcode

Al intentar enviar una aplicación Xamarin.tvOS al Store de Apple TV App, puede aparecer un mensaje de error en el formulario _"Ejecutable no válido: el archivo ejecutable no contienen bitcode"_ .

Para solucionar este problema, realice lo siguiente:

1. En Visual Studio para Mac, haga doble clic en el archivo de proyecto Xamarin.tvOS en el **el Explorador de soluciones** y seleccione **opciones**.
2. Seleccione **tvOS compilación** y asegúrese de que se encuentra en la **versión** configuración: 

    [![](troubleshooting-images/ts01.png "Seleccione las opciones de compilación de tvOS")](troubleshooting-images/ts01.png#lightbox)
3. Agregar `--bitcode=asmonly` a la **argumentos mtouch adicionales** campo y haga clic en el **Aceptar** botón.
4. Vuelva a compilar la aplicación en el **versión** configuración.

### <a name="verifying-that-your-tvos-app-contains-bitcode"></a>Comprobando que la aplicación contiene Bitcode tvOS

Para comprobar que la compilación Xamarin.tvOS App contiene Bitcode, abra la aplicación Terminal y escriba lo siguiente:

```csharp
otool -l /path/to/your/tv.app/tv
```

En la salida, busque lo siguiente:

```csharp
Section
  sectname __bundle
   segname __LLVM
      addr 0x0000000100001000
      size 0x000000000000124f
    offset 4096
     align 2^0 (1)
    reloff 0
    nreloc 0
     flags 0x00000000
 reserved1 0
 reserved2 0
```

`addr` y `size` será diferente, pero otros campos deben ser idénticos.

Deberá asegurarse de que ningún tercero estático (`.a`) fueron creadas bibliotecas usa bibliotecas de tvOS (no iOS bibliotecas) y que también incluye información de bitcode.

Las aplicaciones o bibliotecas que incluyen bitcode válido el `size` será mayor que uno. Existen algunas situaciones donde puede tienen el marcador bitcode una biblioteca, pero no contienen bitcode válido. Por ejemplo:

**Bitcode no válido**

```csharp
 $ otool -arch arm64 libLibrary.a | grep __bitcode -A 3
   sect name __bitcode
   segname __LLVM
      add 0x0000000000000670
      size 0x0000000000000001
```

**Bitcode válido** 

```csharp
$ otool -l -arch arm64 libDownloadableAgent-tvos.a |grep __bitcode -A 3
   sectname __bitcode
   segname __LLVM
      addr 0x000000000001d2d0
      size 0x0000000000045440
```

Tenga en cuenta la diferencia en `size` entre las dos bibliotecas en el ejemplo enumerada se ejecuta por encima. La biblioteca debe generarse a partir de una compilación del archivo de Xcode con bitcode habilitado (configuración de Xcode `ENABLE_BITCODE`) como una solución a este problema de tamaño.

### <a name="apps-that-only-contain-the-arm64-slice-must-also-have-arm64-in-the-list-of-uirequireddevicecapabilities-in-infoplist"></a>Aplicaciones que solo contienen el segmento arm64 también deben tener "arm64" en la lista de UIRequiredDeviceCapabilities en Info.plist

Al enviar una aplicación en el Store de Apple TV App para la publicación, podría obtener un error en el formulario:

_"El aplicaciones que solo contienen el segmento arm64 también deben tener"arm64"en la lista de UIRequiredDeviceCapabilities en Info.plist"_

Si esto ocurre, edite su `Info.plist` de archivos y asegurarse de que tiene las siguientes claves:

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
    <string>arm64</string>
</array>
```

Volver a compilar la aplicación para la versión y vuelva a enviar en iTunes Connect.

### <a name="task-mtouch-execution----failed"></a>"MTouch"--no se pudo ejecutar la tarea

Si utilizas una biblioteca de terceros 3rd (por ejemplo, MonoGame) y la compilación de versión no se pudo con una larga serie de mensajes de error que terminan en `Task "MTouch" execution -- FAILED`, intente agregar `-gcc_flags="-framework OpenAL"` a su **toque argumentos**:

[![](troubleshooting-images/mtouch01.png "Ejecución de MTouch la tarea")](troubleshooting-images/mtouch01.png#lightbox)

También debe incluir `--bitcode=asmonly` en el **toque argumentos**, han establecido las opciones del vinculador en **vincular todo** y realice una compilación limpia.

### <a name="itms-90471-error-the-large-icon-is-missing"></a>ITMS-90471 error. Falta el icono grande

Si recibe un mensaje en el formulario "ITMS 90471 error. Icono grande falta"al intentar enviar una aplicación Xamarin.tvOS al Store de Apple TV App versión, por favor, compruebe lo siguiente:

1. Asegúrese de que se han incluido los activos de iconos grandes en su `Assets.car` archivo que creó mediante la [iconos de aplicación](~/ios/tvos/app-fundamentals/icons-images.md#App-Icons) documentación.
2. Asegúrese de que ha incluido el `Assets.car` de archivos desde el [trabajar con iconos e imágenes](~/ios/tvos/app-fundamentals/icons-images.md) documentación en el paquete de aplicación final.

### <a name="invalid-bundle--an-app-that-supports-game-controllers-must-also-support-the-apple-tv-remote"></a>Agrupación no válida: una aplicación que admite el juego también debe admitir Apple TV remoto

o 

### <a name="invalid-bundle--apple-tv-apps-with-the-gamecontroller-framework-must-include-the-gcsupportedgamecontrollers-key-in-the-apps-infoplist"></a>Agrupación no válida: las aplicaciones de Apple TV con el marco de trabajo de dispositivo debe incluir la clave GCSupportedGameControllers en Info.plist de la aplicación

Dispositivos de juego pueden utilizarse para mejorar el juego y proporcionar una idea de inmersión en un juego. También puede usarse para controlar la interfaz de Apple TV estándar, por lo que el usuario no tiene que cambiar entre el servidor remoto y el controlador.

Si va a enviar una aplicación Xamarin.tvOS con compatibilidad con dispositivos de juego para Apple TV App store y recibe un mensaje de error en forma de:


_Hemos descubierto uno o varios problemas con la entrega recientes del "nombre de la aplicación". La entrega se realizó correctamente, pero puede que desee corregir los problemas siguientes en la siguiente entrega:_

_Agrupación no válida: una aplicación que admite el juego también debe admitir Apple TV remoto._

o 

_Agrupación no válida: las aplicaciones de Apple TV con el marco de trabajo de dispositivo debe incluir la clave GCSupportedGameControllers en Info.plist de la aplicación._

La solución consiste en Agregar compatibilidad para el control remoto de Siri (`GCMicroGamepad`) a la aplicación `Info.plist` archivo. Se ha agregado el perfil de dispositivo de juego Micro Apple Siri remoto de destino. Por ejemplo, se incluyen las siguientes claves:

```xml
<key>GCSupportedGameControllers</key>  
  <array>  
    <dict>  
      <key>ProfileName</key>  
      <string>ExtendedGamepad</string>  
    </dict>  
    <dict>  
      <key>ProfileName</key>  
      <string>MicroGamepad</string>  
    </dict>  
  </array>  
<key>GCSupportsControllerUserInteraction</key>  
<true/>
```

> [!IMPORTANT]
> Juego de Bluetooth son una adquisición opcional que podrían hacer que los usuarios finales, la aplicación no puede obligar al usuario a comprar uno. Si la aplicación es compatible con dispositivos de juego también debe admitir el control remoto de Siri para que el juego sea utilizable por todos los usuarios de Apple TV.

Para obtener más información, consulte nuestra [trabajar con dispositivos de juego](~/ios/tvos/platform/remote-bluetooth.md#Working-with-Game-Controllers) sección de nuestra [Siri Remote y Bluetooth controladores](~/ios/tvos/platform/remote-bluetooth.md) documentación.

### <a name="incompatible-target-framework-netportable-versionv45-profileprofile78"></a>Plataforma de destino incompatible:. NetPortable, Version = v4.5, perfil = Profile78

Cuando se intenta incluir una biblioteca de clases Portable (PCL) en un proyecto Xamarin.tvOS podría obtener un mensaje para formar:

_Plataforma de destino incompatible:. NetPortable, Version = v4.5, perfil = Profile78_

Para solucionar este problema, agregue un archivo XML llamado `Xamarin.TVOS.xml` con el siguiente contenido:

```xml
<Framework Identifier="Xamarin.TVOS" MinimumVersion="1.0" Profile="*" DisplayName="Xamarin.TVOS"/>
```

La ruta de acceso siguiente:

```csharp
/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/Profile/Profile259/SupportedFrameworks/

```

Tenga en cuenta que el número de perfil en la ruta de acceso debe coincidir con el número de perfiles de PCL.

Con este archivo en su lugar, debe agregar el archivo PCL correctamente al proyecto Xamarin.tvOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicación de tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
