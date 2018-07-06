---
title: 'Xamarin.Essentials: vibración'
description: Este documento describe la clase de vibración en Xamarin.Essentials, que le permite iniciar y detener la funcionalidad de Vibrar durante un período de tiempo deseado.
ms.assetid: 7E8B24C4-2625-4DAE-A129-383542D34F1E
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: ca21f43631c261cd384f9049f30f0fa29e2ca44e
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855174"
---
# <a name="xamarinessentials-vibration"></a>Xamarin.Essentials: vibración

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **vibración** clase le permite iniciar y detener la funcionalidad de Vibrar durante un período de tiempo deseado.

## <a name="getting-started"></a>Introducción

Para tener acceso a la **vibración** se requiere la funcionalidad de la siguiente configuración específica de plataforma.

# <a name="androidtabandroid"></a>[Android](#tab/android)

El permiso Vibrar es necesario y debe configurarse en el proyecto de Android. Esto se puede agregar de las maneras siguientes:

Abra el **AssemblyInfo.cs** archivo bajo el **propiedades** carpeta y agregue:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Vibrate)]
```

O actualice el manifiesto de Android:

Abra el **AndroidManifest.xml** de archivos en el **propiedades** carpeta y agregue lo siguiente dentro de la **manifiesto** nodo.

```xml
<uses-permission android:name="android.permission.VIBRATE" />
```

O haga clic con el botón derecho en el proyecto de Android y abra las propiedades del proyecto. En **manifiesto de Android** encontrar el **los permisos necesarios:** área y compruebe el **VIBRAR** permiso. Esto actualizará automáticamente el **AndroidManifest.xml** archivo.

# <a name="iostabios"></a>[iOS](#tab/ios)

No se requiere ninguna configuración adicional.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

No se requiere ninguna configuración adicional.

-----

## <a name="using-vibration"></a>Uso de vibración

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad de vibración se puede solicitar para un período de tiempo o el valor predeterminado de 500 milisegundos.

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

# <a name="androidtabandroid"></a>[Android](#tab/android)

No hay diferencias entre las plataformas.

# <a name="iostabios"></a>[iOS](#tab/ios)

* Vibrará sólo cuando el dispositivo se establece en "Vibrar en anillo".
* Siempre vibrará durante 500 milisegundos.
* No se puede cancelar la vibración.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

No hay diferencias entre las plataformas.

-----

## <a name="api"></a>API

- [Código fuente de vibración](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Vibration)
- [Documentación de API de vibración](xref:Xamarin.Essentials.Vibration)
