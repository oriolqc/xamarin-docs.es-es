---
title: Personalización de una entrada
description: El control de entrada de Xamarin.Forms permite una sola línea de texto que se va a editarse. En este artículo se muestra cómo crear a un representador personalizado para el control de entrada, lo que permite que los desarrolladores reemplacen la representación nativa de forma predeterminada con su propia personalización específica de la plataforma.
ms.prod: xamarin
ms.assetid: 7B5DD10D-0411-424F-88D8-8A474DF16D8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 30326b8d52f39268015bdcbee1b84b9d9e5516b9
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998965"
---
# <a name="customizing-an-entry"></a>Personalización de una entrada

_El control de entrada de Xamarin.Forms permite una sola línea de texto que se va a editarse. En este artículo se muestra cómo crear a un representador personalizado para el control de entrada, lo que permite que los desarrolladores reemplacen la representación nativa de forma predeterminada con su propia personalización específica de la plataforma._

Todos los controles Xamarin.Forms tienen un representador que lo acompaña para cada plataforma que crea una instancia de un control nativo. Cuando un [ `Entry` ](xref:Xamarin.Forms.Entry) control se representa mediante una aplicación de Xamarin.Forms, en iOS el `EntryRenderer` se crea la instancia de clase, lo crea una instancia nativa `UITextField` control. En la plataforma Android, el `EntryRenderer` crea una instancia de la clase un `EditText` control. En la plataforma Universal de Windows (UWP), el `EntryRenderer` crea una instancia de la clase un `TextBox` control. Para obtener más información acerca de las clases de control nativo que se asignan a los controles de Xamarin.Forms y el representador, consulte [clases Base del representador y los controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

El siguiente diagrama ilustra la relación entre el [ `Entry` ](xref:Xamarin.Forms.Entry) control y los controles nativos correspondientes que lo implementan:

![](entry-images/entry-classes.png "Relación entre el Control de entrada y la implementación de controles nativos")

El proceso de representación puede aprovecharse para implementar las personalizaciones específicas de la plataforma mediante la creación de un representador personalizado para el [ `Entry` ](xref:Xamarin.Forms.Entry) control en cada plataforma. El proceso para hacer esto es como sigue:

1. [Crear](#Creating_the_Custom_Entry_Control) un control personalizado de Xamarin.Forms.
1. [Consumir](#Consuming_the_Custom_Control) el control personalizado de Xamarin.Forms.
1. [Crear](#Creating_the_Custom_Renderer_on_each_Platform) el representador personalizado para el control en cada plataforma.

Cada elemento ahora se explicará a su vez, para implementar un [ `Entry` ](xref:Xamarin.Forms.Entry) control que tiene un color de fondo diferente en cada plataforma.

<a name="Creating_the_Custom_Entry_Control" />

## <a name="creating-the-custom-entry-control"></a>Crear el Control de entrada personalizado

Personalizada [ `Entry` ](xref:Xamarin.Forms.Entry) control puede crearse mediante la creación de subclases del `Entry` controlar, como se muestra en el ejemplo de código siguiente:

```csharp
public class MyEntry : Entry
{
}
```

El `MyEntry` control se crea en el proyecto de biblioteca estándar de .NET y es simplemente una [ `Entry` ](xref:Xamarin.Forms.Entry) control. Personalización del control se llevará a cabo en el representador personalizado, por lo que no se requiere ninguna implementación adicional en el `MyEntry` control.

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Consumir el Control personalizado

El `MyEntry` control puede hacer referencia en XAML en el proyecto de biblioteca estándar de .NET al declarar un espacio de nombres para su ubicación y utilizando el prefijo de espacio de nombres en el elemento de control. El siguiente ejemplo de código muestra cómo el `MyEntry` control puede utilizarse en una página XAML:

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <local:MyEntry Text="In Shared Code" />
    ...
</ContentPage>
```

El `local` prefijo de espacio de nombres puede tener cualquier nombre. Sin embargo, el `clr-namespace` y `assembly` valores deben coincidir con los detalles del control personalizado. Una vez que el espacio de nombres se declara que el prefijo se utiliza para hacer referencia al control personalizado.

El siguiente ejemplo de código muestra cómo el `MyEntry` control puede utilizarse en una página de C#:

```csharp
public class MainPage : ContentPage
{
  public MainPage ()
  {
    Content = new StackLayout {
      Children = {
        new Label {
          Text = "Hello, Custom Renderer !",
        },
        new MyEntry {
          Text = "In Shared Code",
        }
      },
      VerticalOptions = LayoutOptions.CenterAndExpand,
      HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
  }
}
```

Este código crea una instancia de un nuevo [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) objeto que se mostrará un [ `Label` ](xref:Xamarin.Forms.Label) y `MyEntry` control centrado tanto vertical como horizontalmente en la página.

Ahora se puede agregar a un representador personalizado a cada proyecto de aplicación para personalizar la apariencia del control en cada plataforma.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Crear al representador personalizado en cada plataforma

El proceso de creación de la clase de representador personalizado es como sigue:

1. Crear una subclase de la `EntryRenderer` clase que representa el control nativo.
1. Invalidar el `OnElementChanged` método que representa la lógica de control y escribir nativa para personalizar el control. Este método se llama cuando se crea el control correspondiente de Xamarin.Forms.
1. Agregar un `ExportRenderer` a la clase de representador personalizado para especificar que se utilizará para representar el control de Xamarin.Forms. Este atributo se usa para registrar al representador personalizado con Xamarin.Forms.

> [!NOTE]
> Es opcional proporcionar a un representador personalizado en cada proyecto de la plataforma. Si no está registrado un representador personalizado, se usará el representador predeterminado de la clase base del control.

El siguiente diagrama ilustra las responsabilidades de cada proyecto de la aplicación de ejemplo, junto con las relaciones entre ellos:

![](entry-images/solution-structure.png "Responsabilidades del proyecto personalizado de MyEntry representador")

El `MyEntry` control se representa por específicos de la plataforma `MyEntryRenderer` clases que derivan de la `EntryRenderer` clase para cada plataforma. Esto da como resultado de cada `MyEntry` controlar que se representa con un color de fondo de específicos de la plataforma, como se muestra en las capturas de pantalla siguiente:

![](entry-images/screenshots.png "Control MyEntry en cada plataforma")

El `EntryRenderer` clase expone la `OnElementChanged` método, que se llama cuando se crea el control de Xamarin.Forms para representar el control nativo correspondiente. Este método toma un `ElementChangedEventArgs` parámetro que contiene `OldElement` y `NewElement` propiedades. Estas propiedades representan el elemento de Xamarin.Forms que el representador *era* adjunto y el elemento de Xamarin.Forms que el representador *es* adjunta, respectivamente. En la aplicación de ejemplo la `OldElement` propiedad será `null` y `NewElement` propiedad contendrá una referencia a la `MyEntry` control.

Una versión reemplazada de la `OnElementChanged` método en el `MyEntryRenderer` clase es el lugar para realizar la personalización de controles nativos. Una referencia con tipo para el control nativo que se va a usar en la plataforma puede obtenerse a través del `Control` propiedad. Además, se puede obtener una referencia al control de Xamarin.Forms que se representa mediante el `Element` propiedad, aunque no se utiliza en la aplicación de ejemplo.

Cada clase de presentador personalizado se decora con un `ExportRenderer` atributo que se registra el representador con Xamarin.Forms. El atributo toma dos parámetros: el nombre de tipo del control de Xamarin.Forms que se procesa y el nombre de tipo del representador personalizado. El `assembly` prefijo para el atributo especifica que el atributo se aplica a todo el ensamblado.

Las secciones siguientes describen la implementación de cada uno específico de plataforma `MyEntryRenderer` clase de representador personalizado.

### <a name="creating-the-custom-renderer-on-ios"></a>Crear al representador personalizado en iOS

El ejemplo de código siguiente muestra al representador personalizado para la plataforma de iOS:

```csharp
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer (typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.iOS
{
    public class MyEntryRenderer : EntryRenderer
    {
        protected override void OnElementChanged (ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged (e);

            if (Control != null) {
                // do whatever you want to the UITextField here!
                Control.BackgroundColor = UIColor.FromRGB (204, 153, 255);
                Control.BorderStyle = UITextBorderStyle.Line;
            }
        }
    }
}
```

La llamada a la clase base `OnElementChanged` método crea una instancia de un iOS `UITextField` control, con una referencia al control que se asigna en el representador `Control` propiedad. El color de fondo, a continuación, se establece en púrpura claro con el `UIColor.FromRGB` método.

### <a name="creating-the-custom-renderer-on-android"></a>Crear al representador personalizado en Android

En el ejemplo de código siguiente se muestra al representador personalizado para la plataforma Android:

```csharp
using Xamarin.Forms.Platform.Android;

[assembly: ExportRenderer(typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.Android
{
    class MyEntryRenderer : EntryRenderer
    {
        public MyEntryRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged(e);

            if (Control != null)
            {
                Control.SetBackgroundColor(global::Android.Graphics.Color.LightGreen);
            }
        }
    }
}
```

La llamada a la clase base `OnElementChanged` método crea una instancia de un Android `EditText` control, con una referencia al control que se asigna en el representador `Control` propiedad. A continuación, se establece el color de fondo a verde claro con el `Control.SetBackgroundColor` método.

### <a name="creating-the-custom-renderer-on-uwp"></a>Creando al representador personalizado en UWP

En el ejemplo de código siguiente se muestra al representador personalizado para UWP:

```csharp
[assembly: ExportRenderer(typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.UWP
{
    public class MyEntryRenderer : EntryRenderer
    {
        protected override void OnElementChanged(ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged(e);

            if (Control != null)
            {
                Control.Background = new SolidColorBrush(Colors.Cyan);
            }
        }
    }
}
```

La llamada a la clase base `OnElementChanged` crea una instancia de método un `TextBox` control, con una referencia al control que se asigna en el representador `Control` propiedad. El color de fondo, a continuación, se establece en cian mediante la creación de un `SolidColorBrush` instancia.

## <a name="summary"></a>Resumen

En este artículo se ha mostrado cómo crear un representador del control personalizado de Xamarin.Forms [ `Entry` ](xref:Xamarin.Forms.Entry) control, lo que permite que los desarrolladores reemplacen la representación nativa de forma predeterminada con su propia representación específica de la plataforma. Los representadores personalizados proporcionan un método eficaz para personalizar la apariencia de los controles de Xamarin.Forms. Se puede usar para los cambios de estilo pequeño o diseños sofisticados de específicos de la plataforma y personalización del comportamiento.


## <a name="related-links"></a>Vínculos relacionados

- [CustomRendererEntry (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/entry/)
