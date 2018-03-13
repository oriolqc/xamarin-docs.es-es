---
title: "Pruebas en dispositivos de inspección"
description: "Implementación de aplicaciones para probar en su Apple Watch"
ms.topic: article
ms.prod: xamarin
ms.assetid: A72A7D38-FAE8-4DD2-843D-54B74C5078D7
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 211f8c7066e86dac3a8351b913da0185093dcb70
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="testing-on-watch-devices"></a>Pruebas en dispositivos de inspección

Una vez que ha seguido el [pasos de implementación](~/ios/watchos/deploy-test/index.md) para crear identificadores de aplicación y grupos de aplicaciones (si es necesario), siga las instrucciones de esta página para:

- [Configuración de los dispositivos](#devices) en el centro de desarrollo de Apple, y
- [Crear perfiles de aprovisionamiento de desarrollo](#profiles), a continuación,
- [Implementar y probar](#testing) en un Apple Watch.

<a name="devices" />

## <a name="devices"></a>Dispositivos

Probar aplicaciones de iOS en un iPhone real o iPad siempre ha requerido el dispositivo se registre en el centro de desarrollo. La lista de dispositivos tiene el siguiente aspecto (haga clic en el signo más  **+**  para agregar un dispositivo nuevo):

![](device-images/devices-sml.png "La lista de dispositivos tiene el siguiente aspecto")

Inspecciones no son diferentes: ahora debe agregar el dispositivo de Apple Watch antes de implementar aplicaciones. Buscar el reloj UDID mediante **Xcode** (**Windows > dispositivos** lista). Cuando está conectado el teléfono emparejado también se mostrará información de la inspección:

[![](device-images/xcode-devices-sml.png "Información de inspección emparejados")](device-images/xcode-devices.png#lightbox)

Cuando conozca el reloj UDID, agregar a la lista de dispositivos en el centro de desarrollo:

![](device-images/devices-watch-sml.png "La inspección UDID en la lista de dispositivos")

Una vez que se ha agregado el dispositivo de inspección, asegúrese de que está seleccionado cualquier nuevo o existente en desarrollo o en perfiles de aprovisionamiento de ad hoc creados:

![](device-images/devices-provisioning.png "Lista de dispositivos disponibles")

No olvide que si se modifica un perfil de aprovisionamiento existente para descargar y volver a instalarlo.

<a name="profiles" />

## <a name="development-provisioning-profiles"></a>Desarrollo de perfiles de aprovisionamiento

Para compilar para realizar pruebas en el dispositivo que necesita para crear un **perfil de aprovisionamiento de desarrollo** para cada identificador de aplicación de la solución.

Si tiene un carácter comodín ID de aplicación, *solamente un perfil de aprovisionamiento será necesario*; pero si tiene un identificador de aplicación independiente para cada proyecto, a continuación, necesitará un perfil de aprovisionamiento para cada Id. de aplicación:

![](device-images/provisioningprofile-development.png "El perfil de aprovisionamiento de desarrollo")

Una vez que haya creado los tres perfiles, aparecen en la lista. No olvide descargar e instalar cada uno de ellos:

![](device-images/provisioningprofiles.png "Los perfiles de aprovisionamiento de desarrollo disponibles")

Puede comprobar el perfil de aprovisionamiento en el **opciones de proyecto** seleccionando el **generar > agrupación de firma de iOS** pantalla y seleccionando la **versión** o **Depurar iPhone** configuración.

El **perfil de aprovisionamiento de** lista mostrará todos los perfiles coincidentes: debería ver los perfiles de coincidencias que ha creado en esta lista desplegable:

![](device-images/options-selectprofile.png "La lista de perfiles de aprovisionamiento")


<a name="testing" />

## <a name="testing-on-a-watch-device"></a>Pruebas en un dispositivo de inspección

Una vez haya configurado el dispositivo, el Id. de aplicaciones y los perfiles de aprovisionamiento, está listo para probar.

1. Asegúrese de que está conectado su iPhone y el reloj ya está emparejado con iPhone.

2. Asegúrese de que la configuración se establece en **versión** o **depurar**.

3. Asegúrese de que el dispositivo conectado iPhone está seleccionado en la lista de destino.

4. Haga doble clic en el proyecto de aplicación de iOS (no la inspección o extensión) y elija **establecer como proyecto de inicio**.

5. Haga clic en el **ejecutar** botón (o elija un **iniciar** opción desde el **ejecutar** menú).

6. Compile la solución y se implementará la aplicación de iOS en iPhone.
  Si la aplicación de iOS o el aprovisionamiento de extensión de supervisión no está configurado correctamente, a continuación, en el iPhone se producirá un error.

7. Si finaliza correctamente la implementación, el iPhone intentarán volver a enviar la aplicación de inspección para la inspección emparejada. El icono de la aplicación aparecerá en la pantalla de supervisión con un círculo *instalar* indicador de progreso.

8. Si la aplicación de inspección se instala correctamente, el icono permanecerá en la pantalla de inspección - toque para comenzar a probar la aplicación.


## <a name="troubleshooting"></a>Solución de problemas

Si se produce un error durante la implementación use la **Vista > rellena > registro de dispositivo** para obtener más información sobre el error. A continuación se enumeran algunos errores y sus causas:

### <a name="error-mt3001-could-not-aot-the-assembly"></a>Error MT3001: Podría no AOT ensamblado

Esto puede ocurrir mientras se compila en modo de depuración para implementar en un dispositivo de Apple Watch.

Para *temporalmente* solucionar este problema, deshabilite **compilaciones incrementales** en la extensión de inspección **Project Options > compilar > watchOS compilación** ventana:

[![](device-images/disable-incremental-sml.png "La casilla de verificación compilaciones incrementales")](device-images/disable-incremental.png#lightbox)

Este problema se solucionará en una versión futura, después del cual las generaciones incrementales pueden habilitarse de nuevo para aprovechar las ventajas de los tiempos de compilación más rápidos.


### <a name="watch-app-fails-to-start-while-debugging-on-device"></a>Ver la que aplicación no se puede iniciar durante la depuración en el dispositivo

Al intentar depurar una aplicación de inspección en un dispositivo físico, sólo el icono & spinner carga aparecen (y finalmente tiempo de espera). Esto se solucionará en una futura versión; una solución alternativa es ejecutar una versión de lanzamiento (que no se le permitirá depurar).


### <a name="invalid-application-executable-or-application-verification-failed"></a>Archivo ejecutable de aplicación no válida o error de comprobación de aplicación

```csharp
Failed to install [APPNAME]
Invalid executable/Application Verification Failed
```

![](device-images/invalid-application-executable.png "Alerta de archivo ejecutable de aplicación no válida")

Si aparecen estos mensajes *en la pantalla de inspección* después de que la aplicación ha intentado instalar, podría haber un par de problemas:

- El propio dispositivo inspección no se ha agregado como un dispositivo en el centro de desarrollo de Apple. Siga las instrucciones para [configurar correctamente los dispositivos](#devices).

- Los perfiles de aprovisionamiento de desarrollo que se va a usar para las pruebas no tenía el dispositivo de inspección incluido; o bien, después de que el reloj se agregó a los perfiles de aprovisionamiento no volver a descargar y volver a instalar. Siga las instrucciones para [configurar los perfiles de aprovisionamiento correctamente](#profiles).

- Si el **registro de dispositivo de iOS** contiene `The system version is lower than the minimum OS version specified for bundle...Have 8.2; need 8.3` , a continuación, observar la aplicación **Info.plist** tiene incorrecta **MinimumOSVersion** valor.
  Debe ser **8.2** : si ha instalado 6.3 Xcode tendrá que editar manualmente el origen de inserción establece en 8.2.

- La aplicación de inspección **Entitlements.plist** incorrectamente un derecho habilitó (por ejemplo, grupos de aplicaciones) que no debería tener.

- La aplicación de inspección **Id. de aplicación** tiene incorrectamente un derecho habilitado (por ejemplo, grupos de aplicaciones) en el centro de desarrollo que no debería tener.



### <a name="install-never-finished"></a>Nunca instale terminado

```csharp
SPErrorGizmoInstallNeverFinishedErrorMessage
```

Este error podría indicar las claves innecesarias (y no válidas) de la aplicación de inspección **Info.plist** archivo. No es necesario incluir claves destinadas a la extensión de aplicación o inspección de iOS en la aplicación de inspección.

<!--eg. NSLocationAlwaysUsageDescription -->


### <a name="waiting-for-debugger-to-connect"></a>"en espera de conexión de depurador"

Si el **salida de la aplicación** ventana se queda bloqueada que muestra

```csharp
waiting for debugger to connect
```

Compruebe si alguno de los NuGets que se han incluido en el proyecto tiene una dependencia **Microsoft.Bcl.Build**. Esto se agrega automáticamente con algunas bibliotecas publicado por Microsoft, incluidos los populares [Microsoft Http Client Libraries](http://www.nuget.org/packages/Microsoft.Net.Http/).

El **Microsoft.Bcl.Build.targets** archivo que se agrega a la **.csproj** puede interferir con la presentación de las extensiones de iOS durante la implementación. Puede realizar un seguimiento del [error](https://bugzilla.xamarin.com/show_bug.cgi?id=29912).
Una posible solución alternativa consiste en Editar el archivo .csproj o mover manualmente los **Microsoft.Bcl.Build.targets** a ser el último elemento.

