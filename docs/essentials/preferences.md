---
title: 'Xamarin.Essentials: preferencias'
description: Este documento describe la clase de preferencias en Xamarin.Essentials, lo que evita las preferencias de aplicación en un almacén de clave/valor. Explica cómo utilizar la clase y los tipos de datos que se pueden almacenar.
ms.assetid: AA81BCBD-79BA-448F-942B-BA4415CA50FF
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: e453c04a953e60be2508670723d175bde3dc7c42
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782855"
---
# <a name="xamarinessentials-preferences"></a>Xamarin.Essentials: preferencias

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **preferencias** clase ayuda a almacenar las preferencias de aplicación en un almacén de clave/valor.

## <a name="using-secure-storage"></a>Uso de almacenamiento seguro

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

Para guardar un valor para una determinada _clave_ en Preferencias:

```csharp
Preferences.Set("my_key", "my_value");
```

Para recuperar un valor de preferencias o un valor predeterminado si no está establecido:

```csharp
var myValue = Preferences.Get("my_key", "default_value");
```

Para quitar el _clave_ de preferencias:

```csharp
Preferences.Remove("my_key");
```

Para quitar todas las preferencias:

```csharp
Preferences.Clear();
```

Además de estos métodos tomar en una función opcional `sharedName` que se puede utilizar para crear contenedores adicionales de preferencias. Lea los detalles de implementación de la plataforma a continuación.

## <a name="supported-data-types"></a>Tipos de datos admitidos

Se admiten los siguientes tipos de datos en **preferencias**:

- **bool**
- **double**
- **int**
- **float**
- **long**
- **string**

## <a name="platform-implementation-specifics"></a>Detalles de implementación de plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Todos los datos se almacenan en [preferencias compartidas](https://developer.android.com/training/data-storage/shared-preferences.html). Si no hay ningún `sharedName` se especifica se usan las preferencias predeterminadas compartido, o bien, el nombre se utiliza para obtener un **privada** compartido preferencias con el nombre especificado.

# <a name="iostabios"></a>[iOS](#tab/ios)

[NSUserDefaults](https://docs.microsoft.com/en-us/xamarin/ios/app-fundamentals/user-defaults) se utiliza para almacenar valores en dispositivos iOS. Si no hay ningún `sharedName` se especifica la `StandardUserDefaults` son utiliza, o bien el nombre es, utiliza para crear un nuevo `NSUserDefaults` con el nombre especificado para el `NSUserDefaultsType.SuiteName`.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[ApplicationDataContainer](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdatacontainer) se utiliza para almacenar los valores en el dispositivo. Si no hay ningún `sharedName` se especifica la `LocalSettings` son utiliza, else el nombre se utiliza para crear un nuevo contenedor dentro de `LocalSettings`.

--------------

## <a name="limitations"></a>Limitaciones

Al almacenar una cadena, esta API se ha diseñado para almacenar pequeñas cantidades de texto.  Rendimiento puede ser subpar si intenta utilizarlo para almacenar grandes cantidades de texto.

## <a name="api"></a>API

- [Las preferencias de código fuente](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Preferences)
- [Documentación de API de preferencias](xref:Xamarin.Essentials.Preferences)
