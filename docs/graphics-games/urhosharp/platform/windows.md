---
title: Soporte técnico de Windows UrhoSharp
description: El programa de instalación específico de Windows y características para UrhoSharp.
ms.prod: xamarin
ms.assetid: A4F36014-AE4E-4F07-A1AC-F264AAA68ACF
author: charlespetzold
ms.author: chape
ms.date: 03/29/2017
ms.openlocfilehash: ac563e18c714a276ebe94a491999e61630d53ef3
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="urhosharp-windows-support"></a>Soporte técnico de Windows UrhoSharp

_Características y el programa de instalación específico de Windows_

Mientras se Urho es una biblioteca de clases portables y permite a las mismas API que se usará en la plataforma distintos para la lógica de juego, todavía tiene que inicializar Urho en el controlador específico de plataforma y en algunos casos, puede aprovechar las características específicas de plataforma .

En las páginas siguientes, se asume que `MyGame` es una subclase de la `Application` clase.

**Admite arquitecturas:** sólo de 64 bits de Windows.

Puede ver ejemplos completos que muestran cómo utilizar esto en nuestro [ejemplos](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples)

## <a name="standalone-project"></a>Proyecto independiente

### <a name="creating-a-project"></a>Crear un proyecto

Crear un proyecto de consola, hacen referencia a Urho NuGet y, a continuación, asegúrese de que puede localizar los activos (es decir, los directorios que contiene el directorio de datos).

### <a name="configuring-and-launching-urho"></a>Configurar e iniciar Urho

Para iniciar la aplicación, ello:

```csharp
DesktopUrhoInitializer.AssetsDirectory = "../Assets";
new MyGame().Run();
```

### <a name="example"></a>Ejemplo

[Ejemplo completo](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/Desktop)

## <a name="integrated-with-wpf"></a>Integrado con WPF

### <a name="creating-a-project"></a>Crear un proyecto

Crear un proyecto de WPF, hacen referencia a Urho NuGet y, a continuación, asegúrese de que puede encontrar los activos (es decir, los directorios que contiene el directorio de datos).

### <a name="configuring-and-launching-urho-from-wpf"></a>Configurar e iniciar Urho de WPF

Crear una subclase de `Window` y configurar sus activos similar al siguiente:

```csharp
    public partial class MainWindow : Window
    {
        Application currentApplication;

        public MainWindow()
        {
            InitializeComponent();
            DesktopUrhoInitializer.AssetsDirectory = @"../../Assets";
            Loaded += (s,e) => RunGame (new MyGame ());
        }

        async void RunGame(MyGame game)
        {
            var urhoSurface = new Panel { Dock = DockStyle.Fill };
            WindowsFormsHost.Child = urhoSurface;
            WindowsFormsHost.Focus();
            urhoSurface.Focus();
            await Task.Yield();
            var appOptions = new ApplicationOptions(assetsFolder: "Data")
                {
                    ExternalWindow = RunInSdlWindow.IsChecked.Value ? IntPtr.Zero : urhoSurface.Handle,
                    LimitFps = false, //true means "limit to 200fps"
                };
            currentApplication = Urho.Application.CreateInstance(value.Type, appOptions);
            currentApplication.Run();
        }
    }
```

### <a name="example"></a>Ejemplo

[Ejemplo completo](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/WPF)

## <a name="integrated-with-uwp"></a>Integrado con UWP

### <a name="creating-a-project"></a>Crear un proyecto

Crear un proyecto de UWP, hacen referencia a Urho NuGet y, a continuación, asegúrese de que puede localizar los activos (es decir, los directorios que contiene el directorio de datos).

### <a name="configuring-and-launching-urho-from-uwp"></a>Configurar e iniciar Urho de UWP

Crear una subclase de `Window` y configurar sus activos similar al siguiente:

```csharp
{
            InitializeComponent();
            GameTypes = typeof(Sample).GetTypeInfo().Assembly.GetTypes()
                .Where(t => t.GetTypeInfo().IsSubclassOf(typeof(Application)) && t != typeof(Sample))
                .Select((t, i) => new TypeInfo(t, $"{i + 1}. {t.Name}", ""))
                .ToArray();
            DataContext = this;
            Loaded += (s, e) => RunGame (new MyGame ());
        }

        public void RunGame(TypeInfo value)
        {
            //at this moment, UWP supports assets only in pak files (see PackageTool)
            currentApplication = UrhoSurface.Run(value.Type, "Data.pak");
        }
    }
```

### <a name="example"></a>Ejemplo

[Ejemplo completo](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/UWP)

## <a name="integrated-with-windowsforms"></a>Integrado con Windows.Forms

### <a name="creating-a-project"></a>Crear un proyecto

Crear un proyecto de Windows.Forms, hacen referencia a Urho NuGet y, a continuación, asegúrese de que puede localizar los activos (es decir, los directorios que contiene el directorio de datos).

### <a name="configuring-and-launching-urho-from-windowsforms"></a>Configurar e iniciar Urho de Windows.Forms

Inicie Urho desde el formulario, consulte [ejemplo completo](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/WinForms/SamplesForm.cs)
