---
title: 'Xamarin.Essentials: Información del dispositivo'
description: Este documento describe la clase DeviceInfo en Xamarin.Essentials, que proporciona información sobre el dispositivo la aplicación se está ejecutando.
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 18fe081372cc190e5ead2045f36d63652f8702c3
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353807"
---
# <a name="xamarinessentials-device-information"></a>Xamarin.Essentials: Información del dispositivo

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **DeviceInfo** clase proporciona información sobre el dispositivo la aplicación se está ejecutando.

## <a name="using-deviceinfo"></a>Uso de DeviceInfo

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La siguiente información se expone a través de la API:

```csharp
// Device Model (SMG-950U, iPhone10,6)
var device = DeviceInfo.Model;

// Manufacturer (Samsung)
var manufacturer = DeviceInfo.Manufacturer;

// Device Name (Motz's iPhone)
var deviceName = DeviceInfo.Name;

// Operating System Version Number (7.0)
var version = DeviceInfo.VersionString;

// Platform (Android)
var platform = DeviceInfo.Platform;

// Idiom (Phone)
var idiom = DeviceInfo.Idiom;

// Device Type (Physical)
var deviceType = DeviceInfo.DeviceType;
```

## <a name="platformsxrefxamarinessentialsdeviceinfoplatforms"></a>[Plataformas](xref:Xamarin.Essentials.DeviceInfo.Platforms)

`DeviceInfo.Platform` se correlaciona con una cadena constante que se asigna al sistema operativo. Los valores se pueden comprobar con el `Platforms` clase:

- **DeviceInfo.Platforms.iOS** : iOS
- **DeviceInfo.Platforms.Android** : Android
- **DeviceInfo.Platforms.UWP** – UWP
- **DeviceInfo.Platforms.Unsupported** : no compatible

## <a name="idiomsxrefxamarinessentialsdeviceinfoidioms"></a>[Expresiones](xref:Xamarin.Essentials.DeviceInfo.Idioms)

`DeviceInfo.Idiom` pone en correlación una cadena constante que se asigna al tipo de dispositivo de la aplicación se ejecuta en. Los valores se pueden comprobar con el `Idioms` clase:

- **DeviceInfo.Idioms.Phone** : teléfono
- **DeviceInfo.Idioms.Tablet** – Tablet
- **DeviceInfo.Idioms.Desktop** : escritorio
- **DeviceInfo.Idioms.TV** – TV
- **DeviceInfo.Idioms.Unsupported** : no compatible

## <a name="device-type"></a>Tipo de dispositivo

`DeviceInfo.DeviceType` pone en correlación una enumeración para determinar si la aplicación se ejecuta en un dispositivo físico o virtual. Un dispositivo virtual es un simulador o emulador.

## <a name="platform-implementation-specifics"></a>Detalles de implementación de plataforma

# <a name="iostabios"></a>[iOS](#tab/ios)

iOS no expone una API para desarrolladores obtener el nombre del dispositivo iOS específico. En su lugar, se devuelve un identificador de hardware, como _iPhone10, 6_ que hace referencia en el iPhone X. Una asignación de estos identificadores no proporcionadas por Apple, pero se puede encontrar en [iPhone Wiki](https://www.theiphonewiki.com/wiki/Models) (un origen de origen no oficial).

--------------

## <a name="api"></a>API

- [Código fuente de DeviceInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceInfo)
- [Documentación de la API de DeviceInfo](xref:Xamarin.Essentials.DeviceInfo)
