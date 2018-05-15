---
title: Preferencias de Xamarin.Essentials
description: La clase de preferencias guarda las preferencias de aplicación en un almacén de clave/valor.
ms.assetid: AA81BCBD-79BA-448F-942B-BA4415CA50FF
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 882ff8e0f10948f4f88303f8bcc3d45b3cecf5fd
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
---
# <a name="xamarinessentials-preferences"></a>Preferencias de Xamarin.Essentials

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
