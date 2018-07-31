---
title: 'Xamarin.Essentials: preferencias'
description: Este documento describe la clase de preferencias en Xamarin.Essentials, que guarda las preferencias de aplicación en un almacén clave/valor. Describe cómo usar la clase y los tipos de datos que se pueden almacenar.
ms.assetid: AA81BCBD-79BA-448F-942B-BA4415CA50FF
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 4a45587c79cfbbcd1198f100915e698289f74950
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353755"
---
# <a name="xamarinessentials-preferences"></a>Xamarin.Essentials: preferencias

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **preferencias** clase ayuda a almacenar las preferencias de aplicación en un almacén clave/valor.

## <a name="using-preferences"></a>Uso de las preferencias

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Para guardar un valor para un determinado _clave_ en Preferencias:

```csharp
Preferences.Set("my_key", "my_value");
```

Para recuperar un valor de las preferencias o un valor predeterminado si no se establece:

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

Además de estos métodos tomar en opcional `sharedName` que puede utilizarse para crear contenedores adicionales de la preferencia. Leer las especificaciones de implementación de la plataforma siguiente.

## <a name="supported-data-types"></a>Tipos de datos admitidos

Se admiten los siguientes tipos de datos en **preferencias**:

- **bool**
- **double**
- **int**
- **float**
- **long**
- **string**
- **DateTime**

## <a name="implementation-details"></a>Detalles de implementación

Los valores de `DateTime` se almacenan en un formato binario de 64 bits (entero largo) mediante dos métodos definidos por el `DateTime` clase: el [ `ToBinary` ](xref:System.DateTime.ToBinary) método se usa para codificar el `DateTime` valor y el [ `FromBinary` ](xref:System.DateTime.FromBinary(System.Int64)) método descodifica el valor. Consulte la documentación de estos métodos para los ajustes que se podrían realizar descodificados a los valores cuando un `DateTime` está almacenado, es decir, no es un valor de hora Universal coordinada (UTC).

## <a name="platform-implementation-specifics"></a>Detalles de implementación de plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Todos los datos se almacenan en [preferencias compartidas](https://developer.android.com/training/data-storage/shared-preferences.html). Si no hay ningún `sharedName` se especifica se utilizan las preferencias compartido predeterminado, o bien, el nombre se utiliza para obtener un **privada** compartido preferencias con el nombre especificado.

# <a name="iostabios"></a>[iOS](#tab/ios)

[Valores NSUserDefaults](https://docs.microsoft.com/en-us/xamarin/ios/app-fundamentals/user-defaults) se utiliza para almacenar valores en dispositivos iOS. Si no hay ningún `sharedName` se especifica la `StandardUserDefaults` son utiliza, o bien el nombre se utiliza para crear un nuevo `NSUserDefaults` con el nombre especificado que se usa para el `NSUserDefaultsType.SuiteName`.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[ApplicationDataContainer](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdatacontainer) se usa para almacenar los valores en el dispositivo. Si no hay ningún `sharedName` se especifica la `LocalSettings` son utiliza, o bien el nombre se utiliza para crear un nuevo contenedor dentro de `LocalSettings`.

--------------

## <a name="persistence"></a>Persistencia

Desinstalar la aplicación hará que todos los _preferencias_ va a quitar. Hay una excepción a esto, que para las aplicaciones destinadas a y se ejecutan en Android 6.0 (nivel de API 23) o versiones posteriores que usan [ __copia de seguridad automática__](https://developer.android.com/guide/topics/data/autobackup). Esta característica está activada de forma predeterminada y conserva los datos de aplicación, incluidos __preferencias compartidas__, que es lo que el **preferencias** utiliza la API. Puede deshabilitar esta forma de Google siguiente [documentación](https://developer.android.com/guide/topics/data/autobackup).

## <a name="limitations"></a>Limitaciones

Al almacenar una cadena, esta API está destinada a almacenar pequeñas cantidades de texto.  Rendimiento puede ser una si se intenta utilizar para almacenar grandes cantidades de texto.

## <a name="api"></a>API

- [Preferencias de código fuente](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Preferences)
- [Documentación sobre las preferencias de API](xref:Xamarin.Essentials.Preferences)
