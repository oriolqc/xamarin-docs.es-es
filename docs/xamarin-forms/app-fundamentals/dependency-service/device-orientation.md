---
title: Comprobando la orientación del dispositivo
description: En este artículo se explica cómo usar la clase Xamarin.Forms DependencyService para tener acceso a la orientación del dispositivo desde el código compartido.
ms.prod: xamarin
ms.assetid: 5F60975F-47DB-4361-B97C-2290D6F77D2F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: 620404a217b2e8a31192ae6613dcec023ac366cd
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995646"
---
# <a name="checking-device-orientation"></a>Comprobando la orientación del dispositivo

En este artículo le ayudará a usar [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) para comprobar la orientación del dispositivo desde el código compartido con las API nativas en cada plataforma. En este tutorial se basa en las existentes `DeviceOrientation` complemento por Ali Özgür. Consulte la [repositorio de GitHub](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation) para obtener más información.

- **[Creación de la interfaz](#Creating_the_Interface)**  &ndash; comprender cómo la interfaz se crea en código compartido.
- **[Implementación de iOS](#iOS_Implementation)**  &ndash; Obtenga información sobre cómo implementar la interfaz en código nativo para iOS.
- **[Implementación de Android](#Android_Implementation)**  &ndash; Obtenga información sobre cómo implementar la interfaz en código nativo para Android.
- **[Implementación de UWP](#WindowsImplementation)**  &ndash; Obtenga información sobre cómo implementar la interfaz en código nativo para la plataforma Universal de Windows (UWP).
- **[Implementar en código compartido](#Implementing_in_Shared_Code)**  &ndash; Aprenda a usar `DependencyService` para llamar a la implementación nativa desde código compartido.

La aplicación mediante `DependencyService` tendrá la siguiente estructura:

![](device-orientation-images/orientation-diagram.png "Estructura de la aplicación de DependencyService")

> [!NOTE]
> Es posible detectar si el dispositivo está en orientación vertical u horizontal en código compartido, como se muestra en [dispositivo Orientation]/guides/xamarin-forms/user-interface/layouts/device-orientation/#changes-in-orientation). El método descrito en este artículo usa las características nativas para obtener más información acerca de la orientación, incluso si el dispositivo está al revés.

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Creación de la interfaz

En primer lugar, cree una interfaz en el código compartido que expresa la funcionalidad que se va a implementar. En este ejemplo, la interfaz contiene un único método:

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

Codificar esta interfaz en el código compartido permitirá que la aplicación de Xamarin.Forms tener acceso a la orientación del dispositivo las API en cada plataforma.

> [!NOTE]
> Las clases que implementan la interfaz deben tener un constructor sin parámetros para que funcione con el `DependencyService`.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementación de iOS

La interfaz debe implementarse en cada proyecto de aplicación específico de la plataforma. Tenga en cuenta que la clase tiene un constructor sin parámetros para que el `DependencyService` puede crear nuevas instancias:

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

Por último, agregue esto `[assembly]` atributo por encima de la clase (y fuera de los espacios de nombres que se han definido), incluido cualquier necesario `using` instrucciones:

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS; //enables registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.iOS {
    ...
```

Este atributo registra la clase como una implementación de la `IDeviceOrientation` interfaz, lo que significa que `DependencyService.Get<IDeviceOrientation>` puede usarse para crear una instancia de ella en el código compartido.

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementación de Android

El código siguiente implementa `IDeviceOrientation` en Android:

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

Agregar esto `[assembly]` atributo por encima de la clase (y fuera de los espacios de nombres que se han definido), incluido cualquier necesario `using` instrucciones:

```csharp
using DependencyServiceSample.Droid; //enables registration outside of namespace
using Android.Hardware;

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.Droid {
    ...
```

Este atributo registra la clase como una implementación de la `IDeviceOrientaiton` interfaz, lo que significa que `DependencyService.Get<IDeviceOrientation>` se pueden usar en el código compartido puede crear una instancia de ella.

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>Implementación de la plataforma universal de Windows

El código siguiente implementa el `IDeviceOrientation` interfaz en la plataforma Universal de Windows:

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

Agregar el `[assembly]` atributo por encima de la clase (y fuera de los espacios de nombres que se han definido), incluido cualquier necesario `using` instrucciones:

```csharp
using DependencyServiceSample.WindowsPhone; //enables registration outside of namespace

[assembly: Dependency(typeof(DeviceOrientationImplementation))]
namespace DependencyServiceSample.WindowsPhone {
    ...
```

Este atributo registra la clase como una implementación de la `DeviceOrientationImplementation` interfaz, lo que significa que `DependencyService.Get<IDeviceOrientation>` se pueden usar en el código compartido puede crear una instancia de ella.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementar en código compartido

Ahora podemos escribir y probar el código compartido que tiene acceso a la `IDeviceOrientation` interfaz. Esta página simple incluye un botón que actualiza su propio texto según la orientación del dispositivo. Usa el `DependencyService` para obtener una instancia de la `IDeviceOrientation` interfaz &ndash; en tiempo de ejecución esta instancia será la implementación específica de la plataforma que tiene acceso completo a lo SDK nativo:

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

Dará como resultado que se ejecuta esta aplicación en iOS, Android o las plataformas Windows y presione el botón en el texto del botón Actualizar con la orientación del dispositivo.

![](device-orientation-images/orientation.png "Ejemplo de orientación del dispositivo")


## <a name="related-links"></a>Vínculos relacionados

- [Uso de DependencyService (ejemplo)](https://developer.xamarin.com/samples/UsingDependencyService)
- [DependencyService (ejemplo)](https://developer.xamarin.com/samples/DependencyService/DependencyServiceSample/)
- [Xamarin.Forms Samples](https://github.com/xamarin/xamarin-forms-samples) (Ejemplos de Xamarin.Forms)
