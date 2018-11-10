---
title: 'Xamarin.Essentials: Vibración'
description: En este documento se describe la clase Vibration de Xamarin.Essentials, que permite iniciar y detener la funcionalidad de vibración durante un período de tiempo determinado.
ms.assetid: 7E8B24C4-2625-4DAE-A129-383542D34F1E
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: ff2e718953d86eb59b28fcaa8640e04f6bf422f3
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675307"
---
# <a name="xamarinessentials-vibration"></a>Xamarin.Essentials: Vibración

![Versión preliminar de NuGet](~/media/shared/pre-release.png)

La clase **Vibration** permite iniciar y detener la funcionalidad de vibración durante un período de tiempo determinado.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

Para acceder a la funcionalidad de **Vibration**, se requiere la siguiente configuración específica para la plataforma.

# <a name="androidtabandroid"></a>[Android](#tab/android)

El permiso Vibrate (Vibrar) es necesario y se debe configurar en el proyecto de Android. Se puede agregar de las siguientes maneras:

Abra el archivo **AssemblyInfo.cs** de la carpeta **Propiedades** y agregue lo siguiente:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Vibrate)]
```

O BIEN, actualice el manifiesto de Android:

Abra el archivo **AndroidManifest.xml** de la carpeta **Propiedades** y agregue lo siguiente dentro del nodo **manifest**.

```xml
<uses-permission android:name="android.permission.VIBRATE" />
```

O haga clic con el botón derecho en el proyecto de Android y abra las propiedades del proyecto. En **Manifiesto de Android**, busque el área **Permisos requeridos:** y active el permiso **VIBRATE** (Vibrar). Esto actualizará automáticamente el archivo **AndroidManifest.xml**.

# <a name="iostabios"></a>[iOS](#tab/ios)

No se requiere configuración adicional.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

No hay diferencias entre las plataformas.

-----

## <a name="using-vibration"></a>Uso de Vibration

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad de Vibration se puede solicitar para un período de tiempo concreto o el valor predeterminado de 500 milisegundos.

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

Se puede solicitar la cancelación de la vibración del dispositivo con el método `Cancel`:

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

* Solo vibra cuando el dispositivo se establece en "Vibrar al sonar".
* Siempre vibra durante 500 milisegundos.
* No se puede cancelar la vibración.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

No hay diferencias entre las plataformas.

-----

## <a name="api"></a>API

- [Código fuente de Vibration](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Vibration)
- [Documentación de API para Vibration](xref:Xamarin.Essentials.Vibration)
