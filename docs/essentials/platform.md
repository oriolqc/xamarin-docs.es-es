---
title: Plataforma Xamarin.Essentials
description: La clase de la plataforma permite a las aplicaciones ejecutar código en el subproceso de ejecución principal.
ms.assetid: CD6D51E7-D933-4FE7-A7F7-392EF27812E0
author: charlespetzold
ms.author: chape
ms.date: 06/03/2018
ms.openlocfilehash: 82e248ee702e104dff98b342aec72179273fc34f
ms.sourcegitcommit: d9ecac62bcf743aff52408fbbd09c5509921a000
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2018
ms.locfileid: "36329952"
---
# <a name="xamarinessentials-platform"></a>Plataforma Xamarin.Essentials

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **plataforma** clase permite a las aplicaciones ejecutar código en el subproceso principal de ejecución y determinar si un bloque específico de código está ejecutando actualmente en el subproceso principal.

## <a name="background"></a>Fondo

Mayoría de sistemas operativos, como la plataforma Universal de Windows, iOS y Android, usar un modelo de subprocesamiento único para el código que implica la interfaz de usuario. Este modelo es necesario serializar los eventos de interfaz de usuario, incluidas las pulsaciones de teclas y entrada táctil correctamente. Este subproceso se suele denominar la _subproceso principal_ o _subproceso de interfaz de usuario_ o _subproceso de interfaz de usuario_. La desventaja de este modelo es que todo el código que tiene acceso a elementos de la interfaz de usuario debe ejecutarse en el subproceso principal de la aplicación. 

Aplicaciones a veces necesitan usar los eventos que llama el controlador de eventos en un subproceso secundario de ejecución. (Las clases de Xamarin.Essentials [ `Accelerometer` ](accelerometer.md), [ `Compass` ](compass.md), [ `Gyroscope` ](gyroscope.md), [ `Magnetometer` ](magnetometer.md), y [ `OrientationSensor` ](orientation-sensor.md) todos pueden devolver información sobre un subproceso secundario cuando se usa con velocidades más rápidas.) Si el controlador de eventos debe tener acceso a elementos de interfaz de usuario, se debe ejecutar ese código en el subproceso principal. El **plataforma** clase permite que la aplicación ejecutar este código en el subproceso principal.

## <a name="running-code-on-the-main-thread"></a>Ejecutar código en el subproceso principal

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

Para ejecutar código en el subproceso principal, llame el método estático `Platform.BeginInvokeOnMainThread` método. El argumento es un [ `Action` ](xref:System.Action) objeto, que es simplemente un método con ningún argumento y no devuelve ningún valor:

```csharp
Platform.BeginInvokeOnMainThread(() =>
{
    // Code to run on the main thread
});
```

Si necesita llamar a este método en una aplicación de Xamarin.Forms dentro de una clase que deriva de `Element` (que incluye cualquier clase que deriva de `View` o `Page`), el `Platform` nombre de clase debe ser completo con el nombre de espacio de nombres:

```csharp
Xamarin.Essentials.Platform.BeginInvokeOnMainThread(() =>
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

A continuación, puede ejecutar este método en el subproceso principal hace referencia a él en el `BeginInvokeOnMainThread` método:

```csharp
Xamarin.Essentials.Platform.BeginInvokeOnMainThread(MyMainThreadCode);
```

> [!NOTE]
> Xamarin.Forms tiene un método denominado [ `Device.BeginInvokeOnMainThread(Action)` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.device.begininvokeonmainthread) que hace lo mismo que `Xamarin.Essentials.Platform.BeginInvokeOnMainThread(Action)`. Aunque puede usar cualquiera de los métodos en una aplicación de Xamarin.Forms, considere la posibilidad de si tiene o no el código de llamada otro necesidad de una dependencia en Xamarin.Forms. Si no es así, `Xamarin.Essentials.Platform.BeginInvokeOnMainThread(Action)` es probablemente una mejor opción.

## <a name="determining-if-code-is-running-on-the-main-thread"></a>Determinar si se ejecuta código en el subproceso principal

La `Platform` clase también permite a una aplicación determinar si un bloque específico de código se ejecuta en el subproceso principal. El `IsMainThread` propiedad devuelve `true` si se ejecuta el código al llamar a la propiedad en el subproceso principal. Un programa puede utilizar esta propiedad para que se ejecute código diferente para el subproceso principal o un subproceso secundario:

```csharp
if (Xamarin.Essentials.Platform.IsMainThread)
{
    // Code to run if this is the main thread
}
else
{
    // Code to run if this is a secondary thread
}
```

Tal vez se pregunte si debe comprobar si el código se ejecuta en un subproceso secundario antes de llamar a `BeginInvokeOnMainThread`, por ejemplo, similar a la siguiente:

```csharp
if (Xamarin.Essentials.Platform.IsMainThread)
{
    MyMainThreadCode();
}
else
{
    Xamarin.Essentials.Platform.BeginInvokeOnMainThread(MyMainThreadCode);
}
```

Quizás sospeche que esta comprobación podría mejorar el rendimiento si el bloque de código se está ejecutando en el subproceso principal.

_Sin embargo, esta comprobación no es necesaria._ Las implementaciones de la plataforma de `BeginInvokeOnMainThread` ellos mismos comprobación si se realiza la llamada en el subproceso principal. Hay muy poco reducción del rendimiento si se llama a `BeginInvokeOnMainThread` cuando no es realmente necesario.

## <a name="api"></a>API

- [Código fuente de plataforma](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Platform)
- [Documentación de API de plataforma](xref:Xamarin.Essentials.Platform)
