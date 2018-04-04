---
title: Personalizar una entrada
description: El control de entrada de Xamarin.Forms permite una sola línea de texto que se pueda editar. Este artículo demuestra cómo crear a un representador personalizado para el control de entrada, permitiendo a los programadores reemplazar el procesamiento nativo de forma predeterminada con su propios personalización específica de la plataforma.
ms.prod: xamarin
ms.assetid: 7B5DD10D-0411-424F-88D8-8A474DF16D8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 1e8ef47ceb381a0e4e163aaa24795d46264195da
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="customizing-an-entry"></a>Personalizar una entrada

_El control de entrada de Xamarin.Forms permite una sola línea de texto que se pueda editar. Este artículo demuestra cómo crear a un representador personalizado para el control de entrada, permitiendo a los programadores reemplazar el procesamiento nativo de forma predeterminada con su propios personalización específica de la plataforma._

Cada control Xamarin.Forms tiene un representador que lo acompañan para cada plataforma que crea una instancia de un control nativo. Cuando un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) control se representa por una aplicación de Xamarin.Forms, en iOS la `EntryRenderer` se crea la instancia de clase, lo crea instancias nativo `UITextField` control. En la plataforma Android, el `EntryRenderer` crea una instancia de la clase un `EditText` control. En Windows Phone y la plataforma Universal de Windows (UWP), el `EntryRenderer` crea una instancia de la clase un `TextBox` control. Para obtener más información acerca de las clases de control nativo que se asignan a los controles de Xamarin.Forms y representador, consulte [clases del representador Base y los controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

En el diagrama siguiente ilustra la relación entre el [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) control y los controles nativos correspondientes que lo implementan:

![](entry-images/entry-classes.png "Relación entre el Control de entrada y la implementación de controles nativos")

El proceso de representación puede tomar ventaja de implementar personalizaciones específicas de la plataforma mediante la creación de un representador personalizado para el [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) control en cada plataforma. El proceso para hacer esto es como sigue:

1. [Crear](#Creating_the_Custom_Entry_Control) un control personalizado de Xamarin.Forms.
1. [Consumir](#Consuming_the_Custom_Control) el control personalizado de Xamarin.Forms.
1. [Crear](#Creating_the_Custom_Renderer_on_each_Platform) el representador personalizado para el control en cada plataforma.

Cada elemento ahora se tratarán a su vez, para implementar un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) control que tiene un color de fondo diferente en cada plataforma.

<a name="Creating_the_Custom_Entry_Control" />

## <a name="creating-the-custom-entry-control"></a>Crear el Control de entrada personalizado

Un personalizado [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) control se puede crear subclases el `Entry` controlar, como se muestra en el ejemplo de código siguiente:

```csharp
public class MyEntry : Entry
{
}
```

El `MyEntry` control se crea en el proyecto de biblioteca (PCL) de clases portables y es simplemente una [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) control. Personalización del control se llevará a cabo en el representador personalizado, por lo que no se requiere ninguna implementación adicional en el `MyEntry` control.

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Consumir el Control personalizado

El `MyEntry` control puede hacer referencia en XAML en el proyecto PCL declarar un espacio de nombres para su ubicación y el uso del prefijo de espacio de nombres en el elemento de control. El siguiente ejemplo de código muestra cómo el `MyEntry` control puede utilizarse en una página XAML:

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

Este código crea una instancia de un nuevo [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) objeto que se mostrará una [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) y `MyEntry` control centrado tanto vertical y horizontalmente en la página.

Ahora puede agregarse un representador personalizado a cada proyecto de aplicación para personalizar la apariencia del control en cada plataforma.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Crear al representador personalizado en cada plataforma

El proceso de creación de la clase de representador personalizado es como sigue:

1. Crear una subclase de la `EntryRenderer` clase que representa el control nativo.
1. Invalidar el `OnElementChanged` método que representa la lógica de control y escribir nativo para personalizar el control. Se llama a este método cuando se crea el control de Xamarin.Forms correspondiente.
1. Agregar un `ExportRenderer` atributo a la clase de representador personalizado para especificar que se utilizará para representar el control de Xamarin.Forms. Este atributo se usa para registrar al representador personalizado con Xamarin.Forms.

> [!NOTE]
> Es opcional proporcionar a un representador personalizado en cada proyecto de la plataforma. Si no se ha registrado un representador personalizado, se usará el representador predeterminado para la clase base del control.

El siguiente diagrama muestra las responsabilidades de cada proyecto de la aplicación de ejemplo, junto con las relaciones entre ellos:

![](entry-images/solution-structure.png "Responsabilidades del proyecto representador MyEntry personalizado")

El `MyEntry` control se representa por específica de la plataforma `MyEntryRenderer` clases que derivan de la `EntryRenderer` clase para cada plataforma. Esto da como resultado de cada `MyEntry` controlar se representen con un color de fondo de específico de la plataforma, como se muestra en las capturas de pantalla siguiente:

![](entry-images/screenshots.png "Control de MyEntry en cada plataforma")

El `EntryRenderer` clase expone el `OnElementChanged` método, que se llama cuando se crea el control de Xamarin.Forms para representar el control nativo correspondiente. Este método toma una `ElementChangedEventArgs` parámetro que contiene `OldElement` y `NewElement` propiedades. Estas propiedades representan el elemento Xamarin.Forms que el representador *era* adjunta y el elemento de Xamarin.Forms que el representador *es* adjunta, respectivamente. En la aplicación de ejemplo la `OldElement` propiedad será `null` y `NewElement` propiedad contendrá una referencia a la `MyEntry` control.

Una versión reemplazada de la `OnElementChanged` método en la `MyEntryRenderer` clase es el lugar donde realice la personalización de control nativo. Una referencia con tipo para el control nativo que se va a usar en la plataforma puede obtenerse a través del `Control` propiedad. Además, una referencia al control Xamarin.Forms que se esté representando puede obtenerse a través de la `Element` propiedad, aunque no se utiliza en la aplicación de ejemplo.

Cada clase de representador personalizado se decora con un `ExportRenderer` atributo que se registra el representador con Xamarin.Forms. El atributo toma dos parámetros: el nombre de tipo del control Xamarin.Forms que se procesa y el nombre del tipo de representador personalizado. El `assembly` prefijo para el atributo especifica que el atributo se aplica a todo el ensamblado.

Las secciones siguientes tratan la implementación de cada específica de la plataforma `MyEntryRenderer` clase representador personalizado.

### <a name="creating-the-custom-renderer-on-ios"></a>Crear al representador personalizado de iOS

En el ejemplo de código siguiente se muestra al representador personalizado para la plataforma de iOS:

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

La llamada a la clase base `OnElementChanged` método crea una instancia de un iOS `UITextField` control, con una referencia al control que se va a asignar para el representador `Control` propiedad. A continuación, se establece el color de fondo en púrpura claro con el `UIColor.FromRGB` método.

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

La llamada a la clase base `OnElementChanged` método crea una instancia de un Android `EditText` control, con una referencia al control que se va a asignar para el representador `Control` propiedad. A continuación, se establece el color de fondo en color verde claro con el `Control.SetBackgroundColor` método.

### <a name="creating-the-custom-renderer-on-windows-phone-and-uwp"></a>Crear el representador personalizado en Windows Phone y UWP

En el ejemplo de código siguiente se muestra al representador personalizado para Windows Phone y UWP:

```csharp
[assembly: ExportRenderer(typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.WinPhone81
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

La llamada a la clase base `OnElementChanged` crea una instancia de método un `TextBox` control, con una referencia al control que se va a asignar para el representador `Control` propiedad. El color de fondo, a continuación, se establece en cian mediante la creación de un `SolidColorBrush` instancia.

## <a name="summary"></a>Resumen

Este artículo demuestra cómo crear un representador de control personalizado para el Xamarin.Forms [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) control, permitiendo a los programadores reemplazar el procesamiento nativo de forma predeterminada con su propia representación específica de la plataforma. Representadores personalizados proporcionan un método eficaz para personalizar la apariencia de los controles de Xamarin.Forms. Pueden usarse para cambios de estilo pequeño o sofisticado diseño específico de la plataforma y personalización del comportamiento.


## <a name="related-links"></a>Vínculos relacionados

- [CustomRendererEntry (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/entry/)
