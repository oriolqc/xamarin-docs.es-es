---
title: 'Xamarin.Essentials: Información del dispositivo'
description: Este documento describe la clase de DeviceInfo en Xamarin.Essentials, que proporciona información sobre el dispositivo se ejecuta la aplicación.
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: b7246afca19607ef2f70288d4643696f4ac35d52
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782403"
---
# <a name="xamarinessentials-device-information"></a>Xamarin.Essentials: Información del dispositivo

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **DeviceInfo** clase proporciona información sobre el dispositivo se ejecuta la aplicación.

## <a name="using-deviceinfo"></a>Uso de DeviceInfo

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

La siguiente información se expone a través de la API:

```csharp
// Device Model (SMG-950U)
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

`DeviceInfo.Platform` se corresponde con una cadena de constante que se asigna al sistema operativo. Los valores se pueden comprobar con el `Platforms` clase:

- **DeviceInfo.Platforms.iOS** : iOS
- **DeviceInfo.Platforms.Android** : Android
- **DeviceInfo.Platforms.UWP** – UWP
- **DeviceInfo.Platforms.Unsupported** : no compatible

## <a name="idiomsxrefxamarinessentialsdeviceinfoidioms"></a>[Expresiones](xref:Xamarin.Essentials.DeviceInfo.Idioms)

`DeviceInfo.Idiom` Correlaciona una cadena de constante que se asigna al tipo de dispositivo de la aplicación se está ejecutando. Los valores se pueden comprobar con el `Idioms` clase:

- **DeviceInfo.Idioms.Phone** : teléfono
- **DeviceInfo.Idioms.Tablet** – Tablet PC
- **DeviceInfo.Idioms.Desktop** : escritorio
- **DeviceInfo.Idioms.TV** : TV
- **DeviceInfo.Idioms.Unsupported** : no compatible

## <a name="device-type"></a>Tipo de dispositivo

`DeviceInfo.DeviceType` Correlaciona una enumeración para determinar si la aplicación se ejecuta en uno físico o un dispositivo virtual. Un dispositivo virtual es un simulador o emulador.

## <a name="api"></a>API

- [Código fuente de DeviceInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceInfo)
- [Documentación de la API de DeviceInfo](xref:Xamarin.Essentials.DeviceInfo)
