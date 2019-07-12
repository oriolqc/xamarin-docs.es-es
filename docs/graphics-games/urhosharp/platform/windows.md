---
title: Soporte técnico de Windows de UrhoSharp
description: Este documento explica la compatibilidad con Windows UrhoSharp. Describe cómo crear un proyecto, configurar e iniciar Urho, integrar con WPF e integrar con UWP.
ms.prod: xamarin
ms.assetid: A4F36014-AE4E-4F07-A1AC-F264AAA68ACF
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 46a028da577a4c49e18cccb681351d7614bb196b
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832640"
---
# <a name="urhosharp-windows-support"></a>Soporte técnico de Windows de UrhoSharp

Aunque Urho es una biblioteca de clases portable, y permite la misma API que se usará en toda la plataforma distintos para la lógica de juego, deberá inicializar Urho en su controlador específico de plataforma y en algunos casos, deseará aprovechar las ventajas de las características específicas de plataforma .

En las páginas siguientes, se supone que `MyGame` es una subclase de la `Application` clase.

**Arquitecturas compatibles:** sólo de 64 bits de Windows.

Puede ver ejemplos completos que muestran cómo usar esto en nuestro [ejemplos](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples)

## <a name="standalone-project"></a>Proyecto independiente

### <a name="creating-a-project"></a>Creación de un proyecto

Crear un proyecto de consola, hacen referencia a Urho NuGet y, a continuación, asegúrese de que puede encontrar los recursos (es decir, los directorios que contiene el directorio de datos).

### <a name="configuring-and-launching-urho"></a>Configura e inicia Urho

Para iniciar la aplicación, puede hacer esto:

```csharp
DesktopUrhoInitializer.AssetsDirectory = "../Assets";
new MyGame().Run();
```

### <a name="example"></a>Ejemplo

[Ejemplo completo](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/Desktop)

## <a name="integrated-with-wpf"></a>Integrado con WPF

### <a name="creating-a-project"></a>Creación de un proyecto

Crear un proyecto de WPF, hacen referencia a Urho NuGet y, a continuación, asegúrese de que puede encontrar los recursos (es decir, los directorios que contiene el directorio de datos).

### <a name="configuring-and-launching-urho-from-wpf"></a>Configura e inicia Urho de WPF

Crear una subclase de `Window` y configurar los recursos similar al siguiente:

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

### <a name="creating-a-project"></a>Creación de un proyecto

Crear un proyecto de UWP, hacen referencia a Urho NuGet y, a continuación, asegúrese de que puede encontrar los recursos (es decir, los directorios que contiene el directorio de datos).

### <a name="configuring-and-launching-urho-from-uwp"></a>Configura e inicia Urho de UWP

Crear una subclase de `Window` y configurar los recursos similar al siguiente:

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

## <a name="integrated-with-windows-forms"></a>Integrado con Windows Forms

### <a name="creating-a-project"></a>Creación de un proyecto

Crear un proyecto de Windows Forms, hacen referencia a Urho NuGet y, a continuación, asegúrese de que puede encontrar los recursos (es decir, los directorios que contiene el directorio de datos).

### <a name="configuring-and-launching-urho-from-windowsforms"></a>Configura e inicia Urho desde Windows.Forms

Iniciar Urho de su formulario, consulte [ejemplo completo](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/WinForms/SamplesForm.cs)
