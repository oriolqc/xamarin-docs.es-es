---
title: 'Xamarin.Essentials: MainThread'
description: La clase MainThread permite que las aplicaciones ejecutar código en el subproceso de ejecución principal.
ms.assetid: CD6D51E7-D933-4FE7-A7F7-392EF27812E1
author: charlespetzold
ms.author: chape
ms.date: 06/26/2018
ms.openlocfilehash: e07d36d3e9a5492e6e170b62dbacb36be44dbfa9
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831430"
---
# <a name="xamarinessentials-mainthread"></a>Xamarin.Essentials: MainThread

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **MainThread** clase permite que las aplicaciones ejecutar código en el subproceso principal de ejecución, y determinar si un determinado bloque de código está actualmente se está ejecutando en el subproceso principal.

## <a name="background"></a>Fondo

La mayoría de los sistemas operativos, como la plataforma Universal de Windows, iOS y Android, usar un modelo de subprocesamiento único para el código que implican la interfaz de usuario. Este modelo es necesaria para serializar los eventos de interfaz de usuario, incluidas las pulsaciones de teclas y entrada táctil correctamente. Este subproceso se suele denominar el _subproceso principal_ o el _el subproceso de interfaz de usuario_ o _el subproceso de interfaz de usuario_. La desventaja de este modelo es que todo el código que tiene acceso a elementos de la interfaz de usuario debe ejecutar en el subproceso principal de la aplicación. 

A veces, las aplicaciones deben utilizar los eventos que llama el controlador de eventos en un subproceso secundario de la ejecución de. (Las clases de Xamarin.Essentials [ `Accelerometer` ](accelerometer.md), [ `Compass` ](compass.md), [ `Gyroscope` ](gyroscope.md), [ `Magnetometer` ](magnetometer.md), y [ `OrientationSensor` ](orientation-sensor.md) todos podrían devolver información sobre un subproceso secundario cuando se usa con velocidades más rápidas.) Si el controlador de eventos debe tener acceso a elementos de interfaz de usuario, se debe ejecutar ese código en el subproceso principal. El **MainThread** clase permite que la aplicación ejecutar este código en el subproceso principal.

## <a name="running-code-on-the-main-thread"></a>Ejecutar código en el subproceso principal

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Para ejecutar código en el subproceso principal, llame a estático `MainThread.BeginInvokeOnMainThread` método. El argumento es un [ `Action` ](xref:System.Action) objeto, que es simplemente un método con ningún argumento y no devuelve ningún valor:

```csharp
MainThread.BeginInvokeOnMainThread(() =>
{
    // Code to run on the main thread
});
```

También es posible definir un método independiente para el código que se debe ejecutar en el subproceso principal:

```csharp
void MyMainThreadCode()
{
    // Code to run on the main thread
}
```

A continuación, puede ejecutar este método en el subproceso principal haciendo referencia a ella en el `BeginInvokeOnMainThread` método:

```csharp
MainThread.BeginInvokeOnMainThread(MyMainThreadCode);
```

> [!NOTE]
> Xamarin.Forms tiene un método llamado [ `Device.BeginInvokeOnMainThread(Action)` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.device.begininvokeonmainthread) que hace lo mismo que `MainThread.BeginInvokeOnMainThread(Action)`. Aunque puede usar cualquiera de estos métodos en una aplicación de Xamarin.Forms, considere la posibilidad de si el código de llamada tiene cualquier otra necesidad de una dependencia en Xamarin.Forms. Si no es así, `MainThread.BeginInvokeOnMainThread(Action)` es probablemente la mejor opción.

## <a name="determining-if-code-is-running-on-the-main-thread"></a>Determinar si se está ejecutando código en el subproceso principal

La `MainThread` clase también permite que una aplicación determinar si un determinado bloque de código se ejecuta en el subproceso principal. El `IsMainThread` propiedad devuelve `true` si el código que llama a la propiedad se está ejecutando en el subproceso principal. Un programa puede usar esta propiedad para ejecutar código diferente para el subproceso principal o un subproceso secundario:

```csharp
if (MainThread.IsMainThread)
{
    // Code to run if this is the main thread
}
else
{
    // Code to run if this is a secondary thread
}
```

Tal vez se pregunte si debe comprobar si el código se ejecuta en un subproceso secundario antes de llamar a `BeginInvokeOnMainThread`, por ejemplo, como esto:

```csharp
if (MainThread.IsMainThread)
{
    MyMainThreadCode();
}
else
{
    MainThread.BeginInvokeOnMainThread(MyMainThreadCode);
}
```

Quizás sospeche que esta comprobación puede mejorar el rendimiento si el bloque de código ya se está ejecutando en el subproceso principal.

_Sin embargo, esta comprobación no es necesaria._ Las implementaciones de la plataforma de `BeginInvokeOnMainThread` ellos mismos comprobación si se realiza la llamada en el subproceso principal. Hay muy poco reducción del rendimiento si se llama a `BeginInvokeOnMainThread` cuando no es realmente necesario.

## <a name="api"></a>API

- [Código fuente de MainThread](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/MainThread)
- [Documentación de API MainThread](xref:Xamarin.Essentials.MainThread)
