---
title: Comprobación de la orientación del dispositivo
description: En este artículo, se explica cómo usar la clase DependencyService de Xamarin.Forms para acceder a la orientación del dispositivo desde código compartido.
ms.prod: xamarin
ms.assetid: 5F60975F-47DB-4361-B97C-2290D6F77D2F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: 52b82033cbd6fe0e1a44f5729c815074852230bf
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115424"
---
# <a name="checking-device-orientation"></a>Comprobación de la orientación del dispositivo

Este artículo le ayudará a usar [`DependencyService`](xref:Xamarin.Forms.DependencyService) para comprobar la orientación del dispositivo desde código compartido con las API nativas en cada plataforma. Este tutorial se basa en el complemento `DeviceOrientation` existente de Ali Özgür. Para obtener más información, vea el [repositorio de GitHub](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation).

- **[Crear la interfaz](#Creating_the_Interface)**: obtenga información sobre cómo se crea la interfaz en código compartido.
- **[Implementación de iOS](#iOS_Implementation)**: obtenga información sobre cómo implementar la interfaz en código nativo para iOS.
- **[Implementación de Android](#Android_Implementation)**: aprenda a implementar la interfaz en código nativo para Android.
- **[Implementación de UWP](#WindowsImplementation)**: aprenda a implementar la interfaz en código nativo para Plataforma universal de Windows (UWP).
- **[Implementación en código compartido](#Implementing_in_Shared_Code)**: aprenda a usar `DependencyService` para llamar a la implementación nativa desde código compartido.

La aplicación que usa `DependencyService` tiene la siguiente estructura:

![](device-orientation-images/orientation-diagram.png "Estructura de la aplicación DependencyService")

> [!NOTE]
> Es posible detectar si el dispositivo está en orientación vertical u horizontal en código compartido, como se muestra en [Device Orientation](~/xamarin-forms/user-interface/layouts/device-orientation.md#Reacting_to_Changes_in_Orientation) (Orientación del dispositivo). El método descrito en este artículo usa características nativas para obtener más información sobre la orientación, incluso si el dispositivo está del revés.

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Creación de la interfaz

En primer lugar, cree una interfaz en el código compartido que exprese la funcionalidad que planea implementar. En este ejemplo, la interfaz contiene un solo método:

```csharp
namespace DependencyServiceSample.Abstractions
{
    public enum DeviceOrientations
    {
        Undefined,
        Landscape,
        Portrait
    }

    public interface IDeviceOrientation
    {
        DeviceOrientations GetOrientation();
    }
}
```

Al codificar para esta interfaz en el código compartido, se permitirá que la aplicación de Xamarin.Forms acceda a las API de orientación del dispositivo en cada plataforma.

> [!NOTE]
> Las clases que implementan la interfaz deben tener un constructor sin parámetros que funcione con `DependencyService`.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementación de iOS

La interfaz tiene que implementarse en cada proyecto de aplicación específico de la plataforma. Tenga en cuenta que la clase tiene un constructor sin parámetros para que `DependencyService` pueda crear nuevas instancias:

```csharp
using UIKit;
using Foundation;

namespace DependencyServiceSample.iOS
{
    public class DeviceOrientationImplementation : IDeviceOrientation
    {
        public DeviceOrientationImplementation(){ }

        public DeviceOrientations GetOrientation()
        {
            var currentOrientation = UIApplication.SharedApplication.StatusBarOrientation;
            bool isPortrait = currentOrientation == UIInterfaceOrientation.Portrait
                || currentOrientation == UIInterfaceOrientation.PortraitUpsideDown;

            return isPortrait ? DeviceOrientations.Portrait: DeviceOrientations.Landscape;
        }
    }
}
```

Por último, agregue este atributo `[assembly]` encima de la clase (y fuera de cualquier espacio de nombres definido), incluidas las instrucciones `using` necesarias:

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS; //enables registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.iOS {
    ...
```

El atributo registra la clase como una implementación de la interfaz `IDeviceOrientation`, lo que significa que se puede usar `DependencyService.Get<IDeviceOrientation>` en el código compartido para crear una instancia de ella.

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementación de Android

El siguiente código implementa el método `IDeviceOrientation` en Android:

```csharp
using DependencyServiceSample.Droid;
using Android.Hardware;

namespace DependencyServiceSample.Droid
{
    public class DeviceOrientationImplementation : IDeviceOrientation
    {
        public DeviceOrientationImplementation() { }

        public static void Init() { }

        public DeviceOrientations GetOrientation()
        {
            IWindowManager windowManager = Android.App.Application.Context.GetSystemService(Context.WindowService).JavaCast<IWindowManager>();

            var rotation = windowManager.DefaultDisplay.Rotation;
            bool isLandscape = rotation == SurfaceOrientation.Rotation90 || rotation == SurfaceOrientation.Rotation270;
            return isLandscape ? DeviceOrientations.Landscape : DeviceOrientations.Portrait;
        }
    }
}
```

Agregue este atributo `[assembly]` encima de la clase (y fuera de cualquier espacio de nombres definido), incluidas las instrucciones `using` necesarias:

```csharp
using DependencyServiceSample.Droid; //enables registration outside of namespace
using Android.Hardware;

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.Droid {
    ...
```

Este atributo registra la clase como una implementación de la interfaz `IDeviceOrientaiton`, lo que significa que `DependencyService.Get<IDeviceOrientation>` puede usarse en el código compartido para crear una instancia de este.

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>Implementación de Plataforma universal de Windows

El siguiente código implementa la interfaz de `IDeviceOrientation` en la Plataforma universal de Windows:

```csharp
namespace DependencyServiceSample.WindowsPhone
{
    public class DeviceOrientationImplementation : IDeviceOrientation
    {
        public DeviceOrientationImplementation() { }

        public DeviceOrientations GetOrientation()
        {
            var orientation = Windows.UI.ViewManagement.ApplicationView.GetForCurrentView().Orientation;
            if (orientation == Windows.UI.ViewManagement.ApplicationViewOrientation.Landscape) {
                return DeviceOrientations.Landscape;
            }
            else {
                return DeviceOrientations.Portrait;
            }
        }
    }
}
```

Agregue el atributo `[assembly]` encima de la clase (y fuera de cualquier espacio de nombres definido), incluidas las instrucciones `using` necesarias:

```csharp
using DependencyServiceSample.WindowsPhone; //enables registration outside of namespace

[assembly: Dependency(typeof(DeviceOrientationImplementation))]
namespace DependencyServiceSample.WindowsPhone {
    ...
```

Este atributo registra la clase como una implementación de la interfaz `DeviceOrientationImplementation`, lo que significa que `DependencyService.Get<IDeviceOrientation>` puede usarse en el código compartido para crear una instancia de este.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementación en código compartido

Ahora es posible escribir y probar código compartido que accede a la interfaz de `IDeviceOrientation`. Esta página simple incluye un botón que actualiza su propio texto según la orientación del dispositivo. Usa `DependencyService` para obtener una instancia de la interfaz `IDeviceOrientation`, en tiempo de ejecución esta instancia es la implementación específica de la plataforma que tiene acceso completo al SDK nativo:

```csharp
public MainPage ()
{
    var orient = new Button {
        Text = "Get Orientation",
        VerticalOptions = LayoutOptions.CenterAndExpand,
        HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
    orient.Clicked += (sender, e) => {
       var orientation = DependencyService.Get<IDeviceOrientation>().GetOrientation();
       switch(orientation){
           case DeviceOrientations.Undefined:
                orient.Text = "Undefined";
                break;
           case DeviceOrientations.Landscape:
                orient.Text = "Landscape";
                break;
           case DeviceOrientations.Portrait:
                orient.Text = "Portrait";
                break;
       }
    };
    Content = orient;
}
```

Al ejecutar esta aplicación en las plataformas iOS, Android o Windows y pulsar el botón, se actualizará el texto del botón con la orientación del dispositivo.

![](device-orientation-images/orientation.png "Ejemplo de orientación del dispositivo")


## <a name="related-links"></a>Vínculos relacionados

- [Using DependencyService (sample)](https://developer.xamarin.com/samples/UsingDependencyService) (Uso de DependencyService [ejemplo])
- [DependencyService (sample)](https://developer.xamarin.com/samples/DependencyService/DependencyServiceSample/) (DependencyService [ejemplo])
- [Xamarin.Forms Samples](https://github.com/xamarin/xamarin-forms-samples) (Ejemplos de Xamarin.Forms)
