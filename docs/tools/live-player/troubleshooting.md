---
title: Solución de problemas de Xamarin Live Player
description: Este documento describe los problemas conocidos con la Xamarin Live Player y posibles correcciones. Describe problemas de conexión, problemas de configuración y mucho más.
ms.prod: xamarin
ms.assetid: 29A97ADA-80E0-40A1-8B26-C68FFABE7D26
author: topgenorth
ms.author: toopge
ms.date: 05/17/2017
ms.openlocfilehash: 3db14db2c64e024ef1c04275661f610f9407dfb7
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831337"
---
# <a name="troubleshooting-xamarin-live-player"></a>Solución de problemas de Xamarin Live Player

![Característica de vista previa](~/media/shared/preview.png)

En este artículo se explica algunos problemas comunes y proporciona los pasos para corregirlos.

## <a name="mobile-device-does-not-connect-after-scanning-barcode-or-entering-code"></a>No conectar dispositivo móvil después de análisis de código de barras (o escribiendo código)

Se produce cuando el dispositivo móvil ejecuta Xamarin Live Player no está en la misma red que el equipo que ejecuta el IDE. Consulte los siguientes:

- Confirme que el dispositivo y el equipo están en la misma red Wi-Fi.
  - Si el equipo también está conectado a una red cableada, pruebe a desconectar la conexión con cable.
- La red puede estar firmemente protegida (por ejemplo, algunas redes corporativas), que bloquea los puertos necesarios por Xamarin Live Player.
- Cierre la aplicación Xamarin Live Player y reinícielo.

## <a name="error-while-trying-to-deploy-message-in-ide"></a>Mensaje "Error al intentar implementar" en el IDE

**"Excepción IOException: no se puede leer datos de la conexión de transporte: operación de socket de no bloqueo bloquearía"**

A menudo, este error se produce cuando el dispositivo móvil ejecuta Xamarin Live Player no está en la misma red que el equipo que ejecuta Visual Studio; Esto suele ocurrir cuando se conecta a un dispositivo que previamente se ha emparejado correctamente.

* Compruebe que el dispositivo y el equipo están en la misma red Wi-Fi.
* La red puede estar firmemente protegida (por ejemplo, algunas redes corporativas), que bloquea los puertos necesarios por Xamarin Live Player. Los siguientes puertos son necesarios para Xamarin Live Player:
  * 37847: acceso a la red interno 
  * : 8090 acceso de red externo

## <a name="manually-configure-device"></a>Configurar manualmente el dispositivo

Si no puede conectarse al dispositivo a través de Wi-Fi puede intentar configurar manualmente su dispositivo mediante el archivo de configuración con los pasos siguientes:

**Paso 1: Abrir el archivo de configuración**

Vaya a la carpeta de datos de aplicación:

* Windows: **%userprofile%\AppData\Roaming**
* macOS: **~/Users/$USER/.config**

En esta carpeta encontrará **PlayerDeviceList.xml** si no existe deberá crear uno.

**Paso 2: Obtener la dirección IP**

En la aplicación de Xamarin Live Player, vaya a **sobre > prueba de conexión > iniciar la prueba de conexión**.

Tome nota de la dirección IP, necesitará la dirección IP que aparece al configurar el dispositivo.

**Paso 3: Obtener el código de emparejamiento**

Dentro de la derivación de Xamarin Live Player **par** o **nuevo par**, a continuación, presione **especificar manualmente**. Un código numérico se mostrará, que necesitará actualizar el archivo de configuración.

**Paso 4: Generar GUID**

Vaya a: https://www.guidgenerator.com/online-guid-generator.aspx y generar un nuevo guid y asegúrese de que es mayúscula en.

**Paso 5: Configurar dispositivo**

Abra el **PlayerDeviceList.xml** hasta en un editor como Visual Studio o Visual Studio Code. Deberá configurar el dispositivo manualmente en este archivo. De forma predeterminada, el archivo debe contener la siguiente vaciar `Devices` elemento XML:

```xml
<DeviceList xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<Devices>

</Devices>
</DeviceList>
```

**Agregue el dispositivo iOS:**

```xml
<PlayerDevice>
<SecretCode>ENTER-PAIR-CODE-HERE</SecretCode>
<UniqueIdentifier>ENTER-GUID-HERE</UniqueIdentifier>
<Name>iPhone Player</Name>
<Platform>iOS</Platform>
<AndroidApiLevel>0</AndroidApiLevel>
<DebuggerEndPoint>ENTER-IP-HERE:37847</DebuggerEndPoint>
<HostEndPoint />
<NeedsAppInstall>false</NeedsAppInstall>
<IsSimulator>false</IsSimulator>
<SimulatorIdentifier />
<LastConnectTimeUtc>2018-01-08T20:36:03.9492291Z</LastConnectTimeUtc>
</PlayerDevice>
```

**Agregar un dispositivo Android:**

```xml
<PlayerDevice>
<SecretCode>ENTER-PAIR-CODE-HERE</SecretCode>
<UniqueIdentifier>ENTER-GUID-HERE</UniqueIdentifier>
<Name>Android Player</Name>
<Platform>Android</Platform>
<AndroidApiLevel>24</AndroidApiLevel>
<DebuggerEndPoint>ENTER-IP-HERE:37847</DebuggerEndPoint>
<HostEndPoint />
<NeedsAppInstall>false</NeedsAppInstall>
<IsSimulator>false</IsSimulator>
<SimulatorIdentifier />
<LastConnectTimeUtc>2018-01-08T20:34:42.2332328Z</LastConnectTimeUtc>
</PlayerDevice>
```

**Cierre y vuelva a abrir Visual Studio.** El dispositivo debería aparecer en la lista.

## <a name="type-or-namespace-cannot-be-found-message-in-ide"></a>Mensaje "espacio de nombres o tipo no se encuentra" en el IDE

Compruebe que ha seleccionado un **proyecto de inicio** que coincide con el tipo de dispositivo (iOS o Android) y que la configuración coincide con ese tipo de dispositivo (p ej. **Depurar | iPhone simulador** para iOS).

## <a name="constructor-on-type-interpretedxamarinformsbutton-not-found-message-in-player"></a>Mensaje de "Constructor del tipo 'InterpretedXamarin.Forms.Button' no se encuentra" en el Reproductor

Algunas clases del sistema no se puede invalidar, por ejemplo:

```csharp
public class SomeCustomButton : Xamarin.Forms.Button { ... }
```

## <a name="mainactivitycs-resourcelayout-does-not-contain-a-definition-for-main"></a>"MainActivity.cs: 'Resource.Layout' no contiene una definición para 'Main'"

Este error se produce para los proyectos de Android con interfaces de usuario definidas en archivos AXML.
No se admiten actualmente archivos AXML en Xamarin Live Player.

### <a name="android-toolbar-and-tabs-render-incorrectly-using-xamarinforms"></a>Android barra de herramientas y las pestañas se representan incorrectamente con Xamarin.Forms

Los proyectos de Xamarin.Forms Android deben usar "Toolbar.axml" y "Tabbar.axml" para los nombres de los archivos pertinentes de diseño. La plantilla predeterminada utiliza estos nombres; cambiar el nombre de ellas dará lugar a problemas de representación.

Informe de problemas adicionales en [bugzilla](https://aka.ms/live-player-report-issue).

## <a name="related-links"></a>Vínculos relacionados

- [Limitaciones](~/tools/live-player/limitations.md)
- [Configuración](~/tools/live-player/install.md)
