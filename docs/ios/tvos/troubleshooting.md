---
title: Solución de problemas
description: Este artículo cubre conoce los problemas que pueden producirse al trabajar con compatibilidad con tvOS de Xamarin.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 124E4953-4DFA-42B0-BCFC-3227508FE4A6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 86106fa5ca53e93ccffb4dd141914c01ab65a506
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="troubleshooting"></a>Solución de problemas

_Este artículo cubre conoce los problemas que pueden producirse al trabajar con compatibilidad con tvOS de Xamarin._

<a name="Known-Issues" />

## <a name="known-issues"></a>Problemas conocidos

La versión actual de compatibilidad con tvOS de Xamarin tiene los siguientes problemas conocidos:

- **Marco de trabajo de mono** : no se puede descifrar los datos entre la versión 4.2 de Mono Mono 4.3 Cryptography.ProtectedData. Como resultado, se producirá un error paquetes de NuGet restaurar con el error `Data unprotection failed` cuando se configura un origen de NuGet protegido.
    - **Solución alternativa** : en Visual Studio para Mac, debe volver a agregar los paquetes de NuGet orígenes que use la autenticación de contraseña antes de volver a intentar restaurar los paquetes.
- **Visual Studio para Mac con F # Add-in** : Error al crear una plantilla de F # Android en Windows. Esto todavía debería funcionar correctamente en el equipo Mac.
- **Xamarin.Mac** : cuando el proyecto de plantillas unificada de Xamarin.Mac se ejecuta con la plataforma de destino establecido en `Unsupported`, la ventana emergente `Could not connect to the debugger` pueden aparecer.
    - **Posible solución** : cambiar la versión de framework Mono disponible en nuestro canal estable.
- **Visual Studio de Xamarin & Xamarin.iOS** : al implementar aplicaciones de WatchKit en Visual studio, el error `The file ‘bin\iPhoneSimulator\Debug\WatchKitApp1WatchKitApp.app\WatchKitApp1WatchKitApp’ does not exist` pueden aparecer.

Informe cualquiera errores encontrará a [Bugzilla](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

## <a name="troubleshooting"></a>Solución de problemas

Las siguientes secciones enumeran algunos problemas conocidos que pueden producirse al utilizar tvOS 9 con Xamarin.tvOS y la solución a estos problemas:

### <a name="invalid-executable---the-executable-does-not-contain-bitcode"></a>Archivo ejecutable no válido: el archivo ejecutable no contiene bitcode

Al intentar enviar una aplicación Xamarin.tvOS a la tienda de aplicaciones de Apple TV, podría obtener un mensaje de error en el formulario _"Ejecutable no válido: el archivo ejecutable no contiene bitcode"_.

Para solucionar este problema, realice lo siguiente:

1. En Visual Studio para Mac, haga doble clic en el archivo de proyecto Xamarin.tvOS en la **el Explorador de soluciones** y seleccione **opciones**.
2. Seleccione **tvOS compilación** y asegúrese de que se encuentra en la **versión** configuración: 

    [![](troubleshooting-images/ts01.png "Seleccione tvOS opciones de compilación")](troubleshooting-images/ts01.png#lightbox)
3. Agregar `--bitcode=asmonly` a la **argumentos adicionales mtouch** campo y haga clic en el **Aceptar** botón.
4. Vuelva a compilar la aplicación en el **versión** configuración.

### <a name="verifying-that-your-tvos-app-contains-bitcode"></a>Comprobar que la aplicación contiene Bitcode tvOS

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

`addr` y `size` pueden variar, pero otros campos deben ser idénticos.

Debe asegurarse de que ningún tercero estático (`.a`) se generaron las bibliotecas que está usando en bibliotecas de tvOS (no las bibliotecas de iOS) y que también incluye información de bitcode.

Las aplicaciones o bibliotecas que incluyen bitcode válida la `size` será mayor que uno. Existen algunas situaciones donde una biblioteca puede tener el marcador bitcode, pero no contienen bitcode válido. Por ejemplo:

**Bitcode no válido**

```csharp
 $ otool -arch arm64 libLibrary.a | grep __bitcode -A 3
   sect name __bitcode
   segname __LLVM
      add 0x0000000000000670
      size 0x0000000000000001
```

**Bitcode válida** 

```csharp
$ otool -l -arch arm64 libDownloadableAgent-tvos.a |grep __bitcode -A 3
   sectname __bitcode
   segname __LLVM
      addr 0x000000000001d2d0
      size 0x0000000000045440
```

Tenga en cuenta la diferencia en `size` entre las dos bibliotecas en el ejemplo de lista se ejecuta sobre. La biblioteca debe generarse a partir de una compilación de Xcode archivo con bitcode habilitado (configuración de Xcode `ENABLE_BITCODE`) como una solución a este problema de tamaño.

### <a name="apps-that-only-contain-the-arm64-slice-must-also-have-arm64-in-the-list-of-uirequireddevicecapabilities-in-infoplist"></a>Aplicaciones que solo contienen el segmento arm64 también deben tener "arm64" en la lista de UIRequiredDeviceCapabilities Info.plist

Al enviar una aplicación a la tienda de aplicaciones de TV de Apple para la publicación, podría aparecer un error en el formulario:

_"El aplicaciones que solo contienen el segmento arm64 también deben tener"arm64"en la lista de UIRequiredDeviceCapabilities Info.plist"_

Si esto ocurre, edite su `Info.plist` de archivos y asegúrese de que tiene las siguientes claves:

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
    <string>arm64</string>
</array>
```

Vuelva a compilar la aplicación para la versión y reenvíe a iTunes Connect.

### <a name="task-mtouch-execution----failed"></a>"MTouch"--no se pudo ejecutar la tarea

Si utilizas una biblioteca de fabricante 3rd (por ejemplo, MonoGame) y la compilación de versión no se pudo largas series de mensajes de error que terminen en `Task "MTouch" execution -- FAILED`, intente agregar `-gcc_flags="-framework OpenAL"` a su **argumentos adicionales táctil**:

[![](troubleshooting-images/mtouch01.png "MTouch ejecución de la tarea")](troubleshooting-images/mtouch01.png#lightbox)

También debe incluir `--bitcode=asmonly` en el **argumentos adicionales táctil**, han configurado las opciones del vinculador para **vínculo todos los** y realice una compilación limpia.

### <a name="itms-90471-error-the-large-icon-is-missing"></a>ITMS-90471 error. El icono grande es que faltan

Si recibe un mensaje en el formato "error ITMS 90471. Los iconos grandes encuentra falta"al intentar enviar una aplicación Xamarin.tvOS a la tienda de aplicaciones de TV de Apple para la versión, compruebe lo siguiente:

1. Asegúrese de que se han incluido los activos de iconos grandes en el `Assets.car` archivo que creó mediante la [iconos de aplicación](~/ios/tvos/app-fundamentals/icons-images.md#App-Icons) documentación.
2. Asegúrese de que ha incluido el `Assets.car` de archivos desde el [trabajar con imágenes de iconos y](~/ios/tvos/app-fundamentals/icons-images.md) documentación en el paquete de aplicación final.

### <a name="invalid-bundle--an-app-that-supports-game-controllers-must-also-support-the-apple-tv-remote"></a>Paquete no válido: una aplicación que es compatible con dispositivos de juego también debe admitir la TV Apple remoto

o 

### <a name="invalid-bundle--apple-tv-apps-with-the-gamecontroller-framework-must-include-the-gcsupportedgamecontrollers-key-in-the-apps-infoplist"></a>Paquete no válido: aplicaciones de Apple TV con la plataforma de dispositivo debe incluir la clave de GCSupportedGameControllers en Info.plist de la aplicación

Dispositivos de juego se pueden utilizar para mejorar el comportamiento del juego y proporcionar una idea de inmersión en un juego. También puede utilizar para controlar la interfaz de Apple TV estándar, por lo que el usuario no tiene que cambiar entre el servidor remoto y el controlador.

Si va a enviar una aplicación Xamarin.tvOS con compatibilidad con dispositivos de juego a la tienda de aplicaciones de TV de Apple y obtiene un mensaje de error en forma de:


_Se han detectado uno o más problemas con la entrega recientes para "nombre de la aplicación". La entrega tuvo éxito, pero puede que desee corregir los siguientes problemas en la entrega al siguiente:_

_Paquete no válido: una aplicación que es compatible con dispositivos de juego también debe admitir la TV Apple remoto._

o 

_Paquete no válido: aplicaciones de Apple TV con la plataforma de dispositivo debe incluir la clave de GCSupportedGameControllers en Info.plist de la aplicación._

La solución consiste en Agregar compatibilidad para la instancia remota de Siri (`GCMicroGamepad`) a la aplicación `Info.plist` archivo. Se ha agregado el perfil de dispositivo de juego Micro por Apple para tener como destino el mando a distancia Siri. Por ejemplo, se incluyen las siguientes claves:

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
> Dispositivos de juego de Bluetooth son una compra opcional que podría provocar que los usuarios finales, la aplicación no puede obligar al usuario a adquirirlo. Si la aplicación es compatible con dispositivos de juego también debe admitir el mando a distancia Siri para que el juego sea utilizable por todos los usuarios de Apple TV.

Para obtener más información, vea nuestra [trabajar con dispositivos de juego](~/ios/tvos/platform/remote-bluetooth.md#Working-with-Game-Controllers) sección de nuestro [Siri remoto y controladores de Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) documentación.

### <a name="incompatible-target-framework-netportable-versionv45-profileprofile78"></a>.NET framework de destino incompatible:. NetPortable, Version = v4.5, perfil = Profile78

Cuando se intenta incluir una biblioteca de clases portables (PCL) en un proyecto Xamarin.tvOS podría aparecer un mensaje para formar:

_.NET framework de destino incompatible:. NetPortable, Version = v4.5, perfil = Profile78_

Para solucionar este problema, agregue un archivo XML llamado ` Xamarin.TVOS.xml` con el siguiente contenido:

```xml
<Framework Identifier="Xamarin.TVOS" MinimumVersion="1.0" Profile="*" DisplayName="Xamarin.TVOS"/>
```

Para la ruta de acceso siguiente:

```csharp
/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/Profile/Profile259/SupportedFrameworks/

```

Tenga en cuenta que el número de perfil en la ruta de acceso debe coincidir con el número de perfil de la PCL.

Con este archivo en su lugar, debe agregar el archivo PCL correctamente para el proyecto Xamarin.tvOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guías de interfaz humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones tvos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
