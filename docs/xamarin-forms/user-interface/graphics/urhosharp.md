---
title: Uso de UrhoSharp en Xamarin.Forms
description: Este artículo explica cómo se puede utilizar UrhoSharp para agregar gráficos 3D a una aplicación de Xamarin.Forms para visualización avanzada.
ms.prod: xamarin
ms.assetid: 0646B98E-CC04-4537-9715-9F82338FD7FF
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/11/2016
ms.openlocfilehash: 3ada1f45532797d77d52968343a7d7b3ab8f7948
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35244524"
---
# <a name="using-urhosharp-in-xamarinforms"></a>Uso de UrhoSharp en Xamarin.Forms

## <a name="what-is-urhosharp"></a>¿Qué es UrhoSharp?

[UrhoSharp](~/graphics-games/urhosharp/index.md) es un poderoso motor 3D para desarrolladores de Xamarin y. NET. El [Introducción](~/graphics-games/urhosharp/introduction.md) contiene más explicaciones acerca de la biblioteca de UrhoSharp y [estas notas](~/graphics-games/urhosharp/using.md) describen cómo programar escenas y acciones.

UrhoSharp puede utilizarse para representar gráficos en aplicaciones de Xamarin.Forms.
Esto [ejemplo](https://github.com/xamarin/urho-samples/tree/master/FormsSample) muestra cómo se podría utilizar UrhoSharp para construir un gráfico 3D interactivo:

![](urhosharp-images/ios-animation.gif "Gráfico 3D de interactivo UrhoSharp en iOS")
![](urhosharp-images/android-animation.gif "UrhoSharp de gráfico interactivo 3D en Android")

## <a name="adding-the-urhosharp-nuget-packages"></a>Agregar los paquetes de UrhoSharp Nuget

Antes de usar UrhoSharp, los desarrolladores necesitan agregar el paquete de UrhoSharp Nuget a su solución. Esta guía se da por supuesto un proyecto de Xamarin.Forms con un iOS, Android y .NET estándar proyecto de biblioteca. Todo el código se escribirá en el proyecto de biblioteca estándar. NET; pero UrhoSharp Nuget se deben agregar a los proyectos de Android y iOS demasiado.

El paquete de UrhoSharp.Forms Nuget contiene todos los objetos necesarios para crear objetos de UrhoSharp. El paquete de nuget UrhoSharp.Forms incluye la `UrhoSurface` (clase), que se utiliza para hospedar UrhoSharp de Xamarin.Forms.
Para empezar, haga doble clic en el **paquetes** carpeta en el proyecto de biblioteca de .NET estándar y seleccione **agregar paquetes...** . Escriba el término de búsqueda **UrhoSharp.Forms**, seleccione **UrhoSharp de Xamarin.Forms**, a continuación, haga clic en **Agregar paquete**.

[![](urhosharp-images/add-package-sml.png "Agregar cuadro de diálogo de paquetes")](urhosharp-images/add-package.png#lightbox "Agregar cuadro de diálogo de paquetes")

El paquete de UrhoSharp.Forms NuGet se agregarán al proyecto:

![](urhosharp-images/packages.png "Carpeta de paquetes")

Repita los pasos anteriores para los proyectos específicos de la plataforma (por ejemplo, iOS y Android).

## <a name="walkthrough-adding-urhosharp-to-a-xamarinforms-app"></a>Tutorial: Agregar UrhoSharp a una aplicación de Xamarin.Forms

Estos pasos describen el código en el ejemplo de Xamarin.Forms UrhoSharp:

1. [Crear una página de formularios de Xamarin](#1)
2. [Agregar el UrhoSurface](#2)
3. [Compilar una aplicación Urho](#3)
4. [Agregar la clase de gráficos a la UrhoSurface](#4)
5. [Interactuar con UrhoSharp](#5)

Tenga en cuenta que el ejemplo usa características de C# 6 y no puede compilarse en versiones anteriores de Visual Studio.

<a name="1"/>

### <a name="1-create-a-xamarin-forms-page"></a>1. Crear una página de formularios de Xamarin

El código siguiente muestra una página de Xamarin.Forms `UrhoPage` antes de que cualquier código relacionado con el Urho se ha agregado:

```csharp
public class UrhoPage : ContentPage
{
  Slider selectedBarSlider, rotationSlider;

  public UrhoPage()
  {
    // we'll add Urho later

    rotationSlider = new Slider(0, 500, 250);

    selectedBarSlider = new Slider(0, 5, 2.5);

    Title = " UrhoSharp + Xamarin.Forms";
    Content = new StackLayout {
      Padding = new Thickness (12, 12, 12, 40),
      VerticalOptions = LayoutOptions.FillAndExpand,
      Children = {
        rotationSlider,
        new Label { Text = "SELECTED VALUE:" },
        selectedBarSlider,
      }
    };
  }
```

<a name="2"/>

### <a name="2-add-the-urhosurface"></a>2. Agregar el UrhoSurface

UrhoSharp puede estar hospedado en un `ContentPage` al igual que otros controles de Xamarin.Forms.
El fragmento de código siguiente muestra un `UrhoSurface` agrega a la página de Xamarin.Forms:

```csharp
using Urho;
using Urho.Forms;
...
public class UrhoPage : ContentPage
{
  UrhoSurface urhoSurface;

  public UrhoPage()
  {
    urhoSurface = new UrhoSurface();
    urhoSurface.VerticalOptions = LayoutOptions.FillAndExpand;
...
    Content = new StackLayout {
    Padding = new Thickness (12, 12, 12, 40),
    VerticalOptions = LayoutOptions.FillAndExpand,
    Children = {
      urhoSurface,  // added
      new Label { Text = "ROTATION:" },
      rotationSlider,
      new Label { Text = "SELECTED VALUE:" },
      selectedBarSlider,
    }
  };
```

<a name="3"/>

### <a name="3-build-a-urho-application"></a>3. Compilar una aplicación Urho

Hacer referencia a la `Charts` clase para la implementación de los gráficos 3D de Urho utilizados en este ejemplo. El esquema del código básico se muestra a continuación: tenga en cuenta que la clase implementa `Urho.Application` que es diferente a la `Xamarin.Forms.Application` clase que se implementa en **App.cs**.

```csharp
using Urho;
using Urho.Actions;
using Urho.Gui;
using Urho.Shapes;

namespace FormsSample
{
    public class Charts : Urho.Application
    {
    public Charts (ApplicationOptions options = null) : base(options) { }
    protected override void Start ()
    {
      ...
    }
    protected override void OnUpdate(float timeStep)
    {
      ...
    }
```

El [UrhoSharp documentación](~/graphics-games/urhosharp/index.md) contiene más información sobre cómo crear escenas 3D y acciones.

<a name="4"/>

### <a name="4-add-the-charts-class-to-the-urhosurface"></a>4. Agregar la clase de gráficos a la UrhoSurface

Use la `UrhoSurface.Show<T>` método genérico para agregar la aplicación Urho a la página de Xamarin.Forms. El siguiente fragmento de código muestra el código adicional necesario para crear la `Charts` clase:

```csharp
public class UrhoPage : ContentPage
{
  Charts urhoApp;
  ...
  protected override async void OnAppearing ()
  {
    urhoApp = await urhoSurface.Show<Charts> (new ApplicationOptions(assetsFolder: null)
      { Orientation = ApplicationOptions.OrientationType.Portrait });
  }
```

Nota: la `Show<T>` método es asincrónico y debería llamarse con el `await` palabra clave.

<a name="5"/>

### <a name="5-interacting-with-urhosharp"></a>5. Interactuar con UrhoSharp

En el ejemplo se permite que las barras del gráfico poder seleccionar y modificar. El `Charts` clase expone el `Bars` y `SelectedBar` para habilitar esta interacción.

Cada barra de"" tiene un controlador de eventos de selección agregado después el `Charts` clase se ha representado, una iteración sobre expuesto `Bars` colección:

```csharp
protected override async void OnAppearing ()
{
  urhoApp = await urhoSurface.Show<Charts>(new ApplicationOptions(assetsFolder: null) { Orientation = ApplicationOptions.OrientationType.Portrait });
  foreach (var bar in urhoApp.Bars)
  {
    bar.Selected += OnBarSelection;
  }
}
```

El controlador de eventos utiliza el valor de la Xamarin.Forms `Slider` control para ajustar el alto de la barra determinado:

```csharp
private void OnBarSelection(Bar bar)
{
  //reset value
  selectedBarSlider.ValueChanged -= OnValuesSliderValueChanged;
  selectedBarSlider.Value = bar.Value;
  selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
}

void OnValuesSliderValueChanged(object sender, ValueChangedEventArgs e)
{  // C# 6
  if (urhoApp?.SelectedBar != null)
  {
    urhoApp.SelectedBar.Value = (float)e.NewValue;
  }
}
```

Por último, conecte los dos `Slider` controla para que cuando su valor cambia lienzo UrhoSharp se ve afectado. El primer control deslizante gira la imagen del gráfico 3D y el segundo control deslizante ajusta el alto de la barra seleccionada.

```csharp
rotationSlider = new Slider(0, 500, 250);
rotationSlider.ValueChanged += (s, e) => urhoApp?.Rotate((float)(e.NewValue - e.OldValue));

selectedBarSlider = new Slider(0, 5, 2.5);
selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
```

Las animaciones en el [superior de la página](#) mostrar la ejecución de ejemplo.

## <a name="summary"></a>Resumen

Esta página muestra cómo se puede utilizar UrhoSharp para agregar visualización de datos 3D a Xamarin.Forms. Leer la [UrhoSharp documentación](~/graphics-games/urhosharp/index.md) para obtener más información sobre cómo crear escenas Urho que pueden incluirse en las aplicaciones de Xamarin.Forms con el método mostrado anteriormente.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de gráficos (C# 6)](https://github.com/xamarin/urho-samples/tree/master/FormsSample)
- [UrhoSharp](~/graphics-games/urhosharp/index.md)
