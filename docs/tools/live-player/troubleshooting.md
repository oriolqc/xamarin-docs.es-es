---
title: Solución de problemas
description: Problemas conocidos con el Reproductor de Xamarin en vivo y cómo corregirlos.
ms.topic: article
ms.prod: xamarin
ms.assetid: 29A97ADA-80E0-40A1-8B26-C68FFABE7D26
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 05/17/2017
ms.openlocfilehash: ab075cad0c3f3456ed23f3eb175dcdb3aa493510
ms.sourcegitcommit: 17a9cf246a4d33cfa232016992b308df540c8e4f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/29/2018
---
# <a name="troubleshooting"></a>Solución de problemas

![Característica de vista previa](~/media/shared/preview.png)

En este artículo se explica algunos problemas comunes y proporciona los pasos necesarios para corregirlos.


## <a name="mobile-device-does-not-connect-after-scanning-barcode-or-entering-code"></a>Dispositivos móviles no se conectan después de análisis de código de barras (o entrada del código)

Se produce cuando el dispositivo móvil que se ejecuta Xamarin Player Live no está en la misma red que el equipo que ejecuta el IDE. Consulte lo siguiente:

- Confirme que el dispositivo y el equipo están en la misma red Wi-Fi.
  - Si el equipo también está conectado a una red con cable, pruebe a desconectar la conexión con cable.
- La red puede protegerse estrechamente (por ejemplo, algunas redes corporativas), que bloquea los puertos necesarios con el Reproductor de Xamarin en vivo.
- Cierre la aplicación de Xamarin Player en vivo y reiniciarlo.


## <a name="error-while-trying-to-deploy-message-in-ide"></a>Mensaje "Error al intentar implementar" en el IDE

**"IOException: no se puede leer datos de la conexión de transporte: la operación de socket que no sea de bloqueo se bloquearía"**

A menudo se produjo este error cuando el dispositivo móvil que se ejecuta Xamarin Player Live no está en la misma red que el equipo que ejecuta Visual Studio; Esto suele ocurrir cuando se conecta a un dispositivo que previamente se ha emparejado correctamente.

* Compruebe que el dispositivo y el equipo se encuentran en la misma red Wi-Fi.
* La red puede protegerse estrechamente (por ejemplo, algunas redes corporativas), que bloquea los puertos necesarios con el Reproductor de Xamarin en vivo. Los siguientes puertos son necesarios para el Reproductor de Xamarin en vivo:
  * 37847: acceso a la red interno 
  * 8090 – acceso a la red externos

## <a name="manually-configure-device"></a>Configurar manualmente el dispositivo

Si no puede conectarse al dispositivo a través de Wi-Fi puede intentar configurar manualmente el dispositivo mediante el archivo de configuración con los pasos siguientes:

**Paso 1: Abrir el archivo de configuración**

Ir a la carpeta de datos de aplicación:

* Windows: **%userprofile%\AppData\Roaming**
* macOS: **~/Users/$USER/.config**

En esta carpeta encontrará **PlayerDeviceList.xml** si no existe debe crear uno.

**Paso 2: Obtener dirección IP**

En la aplicación de Xamarin Player en vivo, vaya a **sobre > prueba de conexión > Iniciar prueba de conexión**.

Tome nota de la dirección IP, necesitará la dirección IP enumerada al configurar el dispositivo.

**Paso 3: Obtener el código de emparejamiento**

Dentro de la derivación de Xamarin Player Live **par** o **nuevo par**, a continuación, presione **escriba manualmente**. Un código numérico se mostrará, que tendrá que actualizar el archivo de configuración.

**Paso 4: Generar GUID**

Vaya a: https://www.guidgenerator.com/online-guid-generator.aspx y generar un nuevo guid y asegúrese de que está en mayúsculas en.


**Paso 5: Configurar dispositivo**

Abra la **PlayerDeviceList.xml** una copia de seguridad en un editor, como Visual Studio o código de Visual Studio. Debe configurar manualmente el dispositivo en este archivo. De forma predeterminada, el archivo debe contener la siguiente vacío `Devices` elemento XML:

```xml
<DeviceList xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<Devices>

</Devices>
</DeviceList>
```

**Agregar el dispositivo iOS:**

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


**Agregar dispositivo Android:**

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


## <a name="type-or-namespace-cannot-be-found-message-in-ide"></a>"Espacio de nombres o tipo de mensaje no se encontró" en el IDE

Compruebe que ha seleccionado un **proyecto de inicio** que corresponda al tipo de dispositivo (iOS o Android) y que la configuración coincide con ese tipo de dispositivo (p. ej. **Depurar | iPhone simulador** para iOS).

## <a name="constructor-on-type-interpretedxamarinformsbutton-not-found-message-in-player"></a>Mensaje de "Constructor en el tipo 'InterpretedXamarin.Forms.Button' no se encontró" en el Reproductor

Algunas clases del sistema no se puede invalidar, por ejemplo:

```csharp
public class SomeCustomButton : Xamarin.Forms.Button { ... }
```

## <a name="mainactivitycs-resourcelayout-does-not-contain-a-definition-for-main"></a>"MainActivity.cs: 'Resource.Layout' no contiene una definición para 'Main'"

Este error se produce para proyectos Android con interfaces de usuario definidas en los archivos AXML.
Archivos AXML no se admiten actualmente en el Reproductor de Xamarin en vivo.

### <a name="android-toolbar-and-tabs-render-incorrectly-using-xamarinforms"></a>Barra de herramientas de Android y pestañas que se usara incorrectamente Xamarin.Forms

Proyectos de Xamarin.Forms Android deben utilizar "Toolbar.axml" y "Tabbar.axml" para los nombres de los archivos de diseño relevantes. La plantilla predeterminada utiliza estos nombres; cambiarles dará lugar a problemas de representación.


Notifique los problemas adicionales en [bugzilla](https://aka.ms/live-player-report-issue).


## <a name="related-links"></a>Vínculos relacionados

- [Limitaciones](~/tools/live-player/limitations.md)
- [Configuración](~/tools/live-player/install.md)
