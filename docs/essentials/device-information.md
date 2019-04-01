---
title: 'Xamarin.Essentials: Información del dispositivo'
description: En este documento se describe la clase DeviceInfo de Xamarin.Essentials, que proporciona información sobre el dispositivo en el que se ejecuta la aplicación.
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: a7868277cd5e924d55ad688df1e8e07c81c5f074
ms.sourcegitcommit: 5d4e6677224971e2bc0268f405d192d0358c74b8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/21/2019
ms.locfileid: "58329330"
---
# <a name="xamarinessentials-device-information"></a>Xamarin.Essentials: Información del dispositivo

La clase **DeviceInfo** proporciona información sobre el dispositivo en el que se ejecuta la aplicación.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-deviceinfo"></a>Uso de DeviceInfo

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La información siguiente se expone a través de la API:

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

## <a name="platforms"></a>Plataformas

[`DeviceInfo.Platform`](xref:Xamarin.Essentials.DeviceInfo.Platform) se correlaciona con una cadena de constante que se asigna al sistema operativo. Los valores se pueden comprobar con el struct `DevicePlatform`:

- **DevicePlatform.iOS**: iOS
- **DevicePlatform.Android**: Android
- **DevicePlatform.UWP**: UWP
- **DevicePlatform.Unknown**: desconocido

## <a name="idioms"></a>Expresiones

[`DeviceInfo.Idiom`](xref:Xamarin.Essentials.DeviceInfo.Idiom) se correlaciona con una cadena de constante que se asigna al tipo de dispositivo en el que se ejecuta la aplicación. Los valores se pueden comprobar con el struct `DeviceIdiom`:

- **DeviceIdiom.Phone**: teléfono
- **DeviceIdiom.Tablet**: tableta
- **DeviceIdiom.Desktop**: escritorio
- **DeviceIdiom.TV**: TV
- **DeviceIdiom.Watch**: reloj
- **DeviceIdiom.Unknown**: desconocido

## <a name="device-type"></a>Tipo de dispositivo

`DeviceInfo.DeviceType` pone en correlación una enumeración para determinar si la aplicación se ejecuta en un dispositivo físico o virtual. Un dispositivo virtual es un simulador o emulador.

## <a name="platform-implementation-specifics"></a>Detalles de implementación de la plataforma

# <a name="iostabios"></a>[iOS](#tab/ios)

iOS no expone una API para que los desarrolladores obtengan el nombre del dispositivo iOS específico. En su lugar, se devuelve un identificador de hardware, como _iPhone10,6_ que hace referencia al iPhone X. Apple no proporciona una asignación de estos identificadores, pero se puede encontrar en [The iPhone Wiki](https://www.theiphonewiki.com/wiki/Models) (una fuente no oficial).

--------------

## <a name="api"></a>API

- [Código fuente de DeviceInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceInfo)
- [Documentación de API para DeviceInfo](xref:Xamarin.Essentials.DeviceInfo)
