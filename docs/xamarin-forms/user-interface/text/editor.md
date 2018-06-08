---
title: Editor
description: Entrada de texto de varias líneas
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 317d4f140daeccc525c4267fca43e6164a8f7827
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848322"
---
# <a name="editor"></a>Editor

_Entrada de texto de varias líneas_

El `Editor` control se usa para aceptar la entrada de varias líneas. En este artículo se tratará:

- **[Personalización](#customization)**  &ndash; opciones de color y el teclado.
- **[Interactividad](#interactivity)**  &ndash; eventos que pueden escuchar para proporcionar interactividad.

## <a name="customization"></a>Personalización

### <a name="setting-and-reading-text"></a>Establecer y leer texto

El `Editor`, al igual que otras vistas de presentación de texto, expone la `Text` propiedad. Esta propiedad puede utilizarse para establecer y leer el texto presentado por el `Editor`. En el ejemplo siguiente se demuestra cómo establecer el `Text` propiedad en XAML:

```xaml
<Editor Text="I am an Editor" />
```

En C#:

```csharp
var MyEditor = new Editor { Text = "I am an Editor" };
```

Para leer texto, obtener acceso a la `Text` propiedad en C#:

```csharp
var text = MyEditor.Text;
```

### <a name="limiting-input-length"></a>Limitar la longitud de entrada

El [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) propiedad puede utilizarse para limitar la longitud de entrada que sea aceptable para el [ `Editor` ](xref:Xamarin.Forms.Editor). Esta propiedad debe establecerse en un entero positivo:

```xaml
<Editor ... MaxLength="10" />
```

```csharp
var editor = new Editor { ... MaxLength = 10 };
```

A [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) valor de propiedad de 0 indica que no se permitirá ninguna entrada y un valor de `int.MaxValue`, que es el valor predeterminado para un [ `Editor` ](xref:Xamarin.Forms.Editor), indica que no hay ninguna límite real en el número de caracteres que pueden escribirse.

### <a name="keyboards"></a>Teclados

El teclado que aparece cuando los usuarios interactúan con un `Editor` puede establecerse mediante programación a través de la [ ``Keyboard`` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Keyboard/) propiedad.

Las opciones para el tipo de teclado son:

- **Valor predeterminado** &ndash; el teclado predeterminado
- **Chat** &ndash; utilizado para texto & lugares donde son útiles emoji
- **Correo electrónico** &ndash; utiliza al especificar direcciones de correo electrónico
- **Numérico** &ndash; usa al escribir números
- **Teléfono** &ndash; utiliza al especificar números de teléfono
- **Dirección URL** &ndash; utilizado para especificar las rutas de acceso de archivo & direcciones web

Hay un [ejemplo de cada teclado](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/choose-keyboard-for-entry/) en nuestra sección de recetas.

### <a name="enabling-and-disabling-spell-checking"></a>Habilitar y deshabilitar el corrector ortográfico

El [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propiedad controla si corrector ortográfico está habilitado. De forma predeterminada, la propiedad se establece en `true`. Cuando el usuario escribe texto, se indican los errores de ortografía.

Sin embargo, en algunos escenarios de entrada de texto, por ejemplo, especificar un nombre de usuario, la revisión ortográfica proporciona una experiencia negativa de modo que deben deshabilitarse estableciendo la [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propiedad `false`:

```xaml
<Editor ... IsSpellCheckEnabled="false" />
```

```csharp
var editor = new Editor { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Cuando el [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propiedad está establecida en `false`y no la está usando un teclado personalizado, se deshabilitará el corrector ortográfico nativo. Sin embargo, si un [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) ha sido conjunto que deshabilita el corrector sobre la comprobación, como [ `Keyboard.Chat` ](xref:Xamarin.Forms.Keyboard.Chat), el `IsSpellCheckEnabled` propiedad se omite. Por lo tanto, la propiedad no se puede usar para habilitar el corrector ortográfico para un `Keyboard` que deshabilita de forma explícita.

### <a name="colors"></a>Colores

`Editor` se puede establecer para usar un color de fondo personalizada a través de la `BackgroundColor` propiedad. Un cuidado especial es necesario para garantizar que los colores se podrán usar en cada plataforma. Dado que cada plataforma tiene distintos valores predeterminados para el color de texto, debe establecer un color de fondo personalizado para cada plataforma. Vea [trabajar con ajustes de plataforma](~/xamarin-forms/platform/device.md) para obtener más información acerca de cómo optimizar la interfaz de usuario para cada plataforma.

En C#:

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        //dark blue on UWP & Android, light blue on iOS
        var editor = new Editor { BackgroundColor = Device.RuntimePlatform == Device.iOS ? Color.FromHex("#A4EAFF") : Color.FromHex("#2c3e50") };
        layout.Children.Add(editor);
    }
}
```

En XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="TextSample.EditorPage"
    Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor>
                <Editor.BackgroundColor>
                    <OnPlatform x:TypeArguments="x:Color">
                        <On Platform="iOS" Value="#a4eaff" />
                        <On Platform="Android, UWP" Value="#2c3e50" />
                    </OnPlatform>
                </Editor.BackgroundColor>
            </Editor>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

![](editor-images/textbackgroundcolor.png "Editor de ejemplo BackgroundColor")

Asegúrese de que los colores de fondo y de texto que elija puede utilizar en cada plataforma y no interferir con cualquier texto de marcador de posición.

## <a name="interactivity"></a>Interactividad

`Editor` expone dos eventos:

- [TextChanged](http://developer.xamarin.com/api/event/Xamarin.Forms.Editor.TextChanged/) &ndash; se produce cuando cambia el texto en el editor. Proporciona el texto antes y después del cambio.
- [Completado](http://developer.xamarin.com/api/event/Xamarin.Forms.Editor.Completed/) &ndash; se produce cuando el usuario ha finalizado la entrada presionando la tecla ENTRAR del teclado.

### <a name="completed"></a>Completada

El `Completed` eventos se usan para reaccionar a la finalización de una interacción con un `Editor`. `Completed` se produce cuando el usuario finaliza la entrada con un campo especificando la tecla ENTRAR del teclado. El controlador para el evento es un controlador de eventos genéricos, teniendo el remitente y `EventArgs`:

```csharp
void EditorCompleted (object sender, EventArgs e)
{
    var text = ((Editor)sender).Text; // sender is cast to an Editor to enable reading the `Text` property of the view.
}
```

Puede suscribir el evento completado en el código y XAML:

En C#:

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var editor = new Editor ();
        editor.Completed += EditorCompleted;
        layout.Children.Add(editor);
    }
}
```

En XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.EditorPage"
Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor Completed="EditorCompleted" />
        </StackLayout>
    </ContentPage.Content>
</Contentpage>
```

### <a name="textchanged"></a>TextChanged

El `TextChanged` eventos se usan para reaccionar a un cambio en el contenido de un campo.

`TextChanged` se produce cada vez que la `Text` de la `Editor` cambios. El controlador para el evento toma una instancia de `TextChangedEventArgs`. `TextChangedEventArgs` proporciona acceso a los valores antiguos y nuevos de la `Editor` `Text` a través de la `OldTextValue` y `NewTextValue` propiedades:

```csharp
void EditorTextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

Puede suscribir el evento completado en el código y XAML:

Mediante código:

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var editor = new Editor ();
        editor.TextChanged += EditorTextChanged;
        layout.Children.Add(editor);
    }
}
```

En XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.EditorPage"
Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor TextChanged="EditorTextChanged" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```


## <a name="related-links"></a>Vínculos relacionados

- [Texto (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [Editor de API](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/)
