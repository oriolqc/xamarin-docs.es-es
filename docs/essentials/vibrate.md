---
title: Vibración Xamarin.Essentials
description: La clase de vibración le permite iniciar y detener la funcionalidad de vibrate para un período de tiempo deseado.
ms.assetid: 7E8B24C4-2625-4DAE-A129-383542D34F1E
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 53271f3cee06f33cea4fa0bd28d3cff1baf0cd3e
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-vibration"></a>Vibración Xamarin.Essentials

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **vibración** clase le permite iniciar y detener la funcionalidad de vibrate para un período de tiempo deseado.

## <a name="getting-started"></a>Introducción

Para tener acceso a la **vibración** se requiere la siguiente configuración específica de plataforma de funcionalidad.

# <a name="androidtabandroid"></a>[Android](#tab/android)

El permiso Vibrate es necesario y debe configurarse en el proyecto de Android. Esto puede agregarse de las maneras siguientes:

Abra la **AssemblyInfo.cs** de archivos en el **propiedades** carpeta y agregar:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Vibrate)]
```

O bien, actualice el manifiesto de Android:

Abra la **AndroidManifest.xml** de archivos en el **propiedades** carpeta y agregue lo siguiente dentro de la **manifiesto** nodo.

```xml
<uses-permission android:name="android.permission.VIBRATE" />
```

O haga clic con el botón secundario en el proyecto de Android y abra las propiedades del proyecto. En **manifiesto Android** buscar el **los permisos necesarios:** área y compruebe el **VIBRATE** permiso. Esto actualizará automáticamente el **AndroidManifest.xml** archivo.

# <a name="iostabios"></a>[iOS](#tab/ios)

No se necesita ninguna instalación adicional.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

No se necesita ninguna instalación adicional.

-----

## <a name="using-vibration"></a>Uso de vibración

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad de vibración se puede solicitar para una cierta cantidad de tiempo o el valor predeterminado de 500 milisegundos.

```csharp
try
{
    // Use default vibration length
    Vibration.Vibrate();

    // Or use specified time
    var duration = TimeSpan.FromSeconds(1);
    Vibration.Vibrate(duration);
}
catch (FeatureNotSupportedException ex)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Other error has occurred.
}
```

Se puede solicitar la cancelación de vibración de dispositivo con el `Cancel` método:

```csharp
try
{
    Vibration.Cancel();
}
catch (FeatureNotSupportedException ex)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Other error has occurred.
}
```

## <a name="platform-differences"></a>Diferencias entre las plataformas

| Plataforma | Diferencia |
| --- | --- |
| iOS | Siempre vibra durante 500 milisegundos. |
| iOS | No es posible cancelar la vibración. |

## <a name="api"></a>API

- [Código fuente de vibración](https://github.com/xamarin/Essentials/tree/master/Essentials/Vibration)
- [Documentación de API de vibración](xref:Xamarin.Essentials.Vibration)
