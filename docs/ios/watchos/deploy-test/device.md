---
title: Las pruebas en dispositivos de Apple Watch
description: Este documento describe cómo implementar una aplicación para watchOS creada con Xamarin para realizar pruebas en un Apple Watch real. Describe los dispositivos, el aprovisionamiento de perfiles, las pruebas y se proporcionan algunas sugerencias para solucionar problemas.
ms.prod: xamarin
ms.assetid: A72A7D38-FAE8-4DD2-843D-54B74C5078D7
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 9c15e9205b96a02caa182e47b71c6d36c8bff1aa
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671057"
---
# <a name="testing-on-apple-watch-devices"></a>Las pruebas en dispositivos de Apple Watch

Una vez que ha seguido el [pasos de implementación](~/ios/watchos/deploy-test/index.md) para crear identificadores de aplicación y los grupos de aplicaciones (si es necesario), siga las instrucciones de esta página para:

- [Configurar los dispositivos](#devices) en el centro de desarrollo de Apple y
- [Crear perfiles de aprovisionamiento de desarrollo](#profiles), a continuación,
- [Implementar y probar](#testing) en un Apple Watch.

<a name="devices" />

## <a name="devices"></a>Dispositivos

Probar aplicaciones de iOS en un real iPhone o iPad siempre ha requerido el dispositivo esté registrado en el centro de desarrollo. La lista de dispositivos tiene este aspecto (haga clic en el signo más **+** para agregar un nuevo dispositivo):

![](device-images/devices-sml.png "La lista de dispositivos tiene este aspecto")

Inspecciones no son diferentes: ahora debe agregar el dispositivo de Apple Watch antes de implementar las aplicaciones a él. Busque la inspección UDID mediante **Xcode** (**Windows > dispositivos** lista). Cuando se conecta el teléfono emparejado también se mostrará la información de la inspección:

[![](device-images/xcode-devices-sml.png "Información de inspección emparejados")](device-images/xcode-devices.png#lightbox)

Cuando conoce la inspección UDID, agréguelo a la lista de dispositivos en el centro de desarrollo:

![](device-images/devices-watch-sml.png "La inspección UDID en la lista de dispositivos")

Una vez que se ha agregado el dispositivo de inspección, asegúrese de que se selecciona cualquier nuevo o existente en desarrollo o en perfiles de aprovisionamiento de ad hoc que cree:

![](device-images/devices-provisioning.png "Lista de dispositivos disponibles")

No olvide que si edita un perfil de aprovisionamiento existente para descargar y volver a instalarlo.

<a name="profiles" />

## <a name="development-provisioning-profiles"></a>Los perfiles de aprovisionamiento de desarrollo

Para crear aplicaciones para las pruebas en el dispositivo, deberá crear un **perfil de aprovisionamiento de desarrollo** para cada identificador de aplicación de la solución.

Si tiene un Id. de aplicación, comodín *perfil de aprovisionamiento de un solo será necesario*; pero si tiene un identificador de aplicación independiente para cada proyecto, necesitará un perfil de aprovisionamiento para cada identificador de aplicación:

![](device-images/provisioningprofile-development.png "El perfil de aprovisionamiento de desarrollo")

Una vez que haya creado los tres perfiles, aparecen en la lista. No olvide descargar e instalar cada uno de ellos:

![](device-images/provisioningprofiles.png "Los perfiles de aprovisionamiento de desarrollo disponibles")

Puede comprobar el perfil de aprovisionamiento en la **opciones de proyecto** seleccionando el **compilar > firma de lote de iOS** pantalla y seleccionando la **versión** o **Depuración iPhone** configuración.

El **perfil de aprovisionamiento** lista mostrará todos los perfiles coincidentes; debería ver los perfiles coincidentes que ha creado en la lista desplegable:

![](device-images/options-selectprofile.png "La lista de perfiles de aprovisionamiento")


<a name="testing" />

## <a name="testing-on-a-watch-device"></a>Las pruebas en un dispositivo de inspección

Una vez haya configurado su dispositivo, los identificadores de aplicación y los perfiles de aprovisionamiento, está listo para probar.

1. Asegúrese de que está conectado su iPhone y el reloj ya está emparejado con el iPhone.

2. Asegúrese de que la configuración está establecida en **versión** o **depurar**.

3. Asegúrese de que el dispositivo iPhone conectado está seleccionado en la lista de destino.

4. Haga doble clic en el proyecto de aplicación de iOS (no la inspección o extensión) y elija **establecer como proyecto de inicio**.

5. Haga clic en el **ejecutar** botón (o elija un **iniciar** opción desde el **ejecutar** menú).

6. La solución se compilará y se implementará la aplicación de iOS en iPhone.
  Si la aplicación de iOS o el aprovisionamiento de extensión de supervisión no está configurado correctamente, a continuación, se podrá realizar la implementación para el iPhone.

7. Si la implementación se completa correctamente, el iPhone intentará enviar la aplicación del reloj para la inspección emparejada automáticamente. El icono de la aplicación aparecerá en la pantalla de supervisión con un círculo *instalar* indicador de progreso.

8. Si la aplicación del reloj se ha instalado correctamente, el icono permanecerá en la pantalla de inspección - toque para comenzar a probar la aplicación.


## <a name="troubleshooting"></a>Solución de problemas

Si se produce un error durante la implementación use la **Vista > paneles > registro de dispositivos** para obtener más información sobre el error. A continuación se enumeran algunos errores y sus causas:

### <a name="error-mt3001-could-not-aot-the-assembly"></a>Error MT3001: Podría no AOT el ensamblado

Esto puede ocurrir al compilar en modo de depuración para implementar en un dispositivo Apple Watch.

Para *temporalmente* solucionar este problema, deshabilite **compilaciones incrementales** en la extensión de inspección **opciones de proyecto > compilar > compilación para watchOS** ventana:

[![](device-images/disable-incremental-sml.png "La casilla de verificación compilaciones incrementales")](device-images/disable-incremental.png#lightbox)

Esto se corregirá en una versión futura, después del cual se pueden habilitar volver a las compilaciones incrementales para aprovechar las ventajas de los tiempos de compilación más rápidos.


### <a name="watch-app-fails-to-start-while-debugging-on-device"></a>Se produce un error en la aplicación Watch iniciarse durante la depuración en el dispositivo

Cuando se intenta depurar una aplicación de inspección en un dispositivo físico, sólo el icono de & cargando el indicador giratorio aparezcan (y, finalmente, tiempo de espera). Esto se solucionará en futuras versiones. una solución alternativa es ejecutar una versión de lanzamiento (que no le permitirá depurar).


### <a name="invalid-application-executable-or-application-verification-failed"></a>Archivo ejecutable de aplicación no válida o error de comprobación de aplicación

```csharp
Failed to install [APPNAME]
Invalid executable/Application Verification Failed
```

![](device-images/invalid-application-executable.png "Alerta de archivo ejecutable de aplicación no válida")

Si estos mensajes aparecen *en la pantalla de inspección* después de que ha intentado instalar la aplicación, podría haber un par de problemas:

- El propio dispositivo inspección no se ha agregado como un dispositivo en el centro de desarrollo de Apple. Siga las instrucciones para [configurar correctamente los dispositivos](#devices).

- Los perfiles de aprovisionamiento de desarrollo que se usa para las pruebas no tenía el dispositivo de inspección incluido; o bien, después de que el reloj se agregó a los perfiles de aprovisionamiento que no estaban volver a descargar y volver a instalar. Siga las instrucciones para [configurar correctamente los perfiles de aprovisionamiento](#profiles).

- Si el **registro de dispositivos iOS** contiene `The system version is lower than the minimum OS version specified for bundle...Have 8.2; need 8.3` del, a continuación, la aplicación del reloj **Info.plist** tiene el mal **MinimumOSVersion** valor.
  Debe ser **8.2** : si ha instalado 6.3 de Xcode es posible que deba modificar manualmente el origen de inserción establézcalo como 8.2.

- La aplicación Watch **Entitlements.plist** incorrectamente un derecho habilitó (por ejemplo, los grupos de aplicaciones) que no debería tener.

- La aplicación Watch **Id. de aplicación** tiene incorrectamente un derecho habilitado (por ejemplo, los grupos de aplicaciones) en el centro de desarrollo que no debería tener.



### <a name="install-never-finished"></a>Nunca instale finalizada

```csharp
SPErrorGizmoInstallNeverFinishedErrorMessage
```

Este error podría indicar claves innecesarias (y no válidas) en la aplicación Watch **Info.plist** archivo. No debe incluir claves destinadas a la extensión de aplicación o inspección de iOS en la aplicación del reloj.

<!--eg. NSLocationAlwaysUsageDescription -->


### <a name="waiting-for-debugger-to-connect"></a>"en espera para que conectar el depurador"

Si el **resultado de la aplicación** se bloquea la ventana que muestra

```csharp
waiting for debugger to connect
```

Compruebe si alguno de los paquetes de NuGet que se han incluido en el proyecto tiene una dependencia **Microsoft.Bcl.Build**. Esto se agrega automáticamente con algunas bibliotecas publicada por Microsoft, incluidos los populares [Microsoft Http Client Libraries](https://www.nuget.org/packages/Microsoft.Net.Http/).

El **Microsoft.Bcl.Build.targets** archivo que se agrega a la **.csproj** puede interferir con el empaquetado de extensiones de iOS durante la implementación. Puede realizar un seguimiento el [error](https://bugzilla.xamarin.com/show_bug.cgi?id=29912).
Una posible solución alternativa consiste en modificar el archivo .csproj y mover manualmente el **Microsoft.Bcl.Build.targets** a ser el último elemento.

