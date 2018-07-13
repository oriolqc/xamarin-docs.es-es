---
title: Etiqueta de Xamarin.Forms
description: En este artículo se explica cómo usar la clase de etiqueta de Xamarin.Forms para mostrar único y varias línea de texto en aplicaciones.
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: ce602a84ea1024dc22298a3ec1567a9a34ad4a82
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995971"
---
# <a name="xamarinforms-label"></a>Etiqueta de Xamarin.Forms

_Mostrar texto en Xamarin.Forms_

El `Label` vista se utiliza para mostrar texto, único y varias líneas. Pueden tener las etiquetas de texto de color y fuentes personalizadas (familias de tamaños y opciones). En este artículo se tratan los siguientes temas:

- **[Truncamiento y ajuste](#Truncation_and_Wrapping)**  &ndash; truncamiento y opciones para tratar las situaciones donde el texto no encaje en una sola línea de ajuste.
- **[Fuente](#Font)**  &ndash; las opciones de fuente.
- **[Color](#Color)**  &ndash; las opciones de color del texto de etiqueta.
- **[Texto con formato](#Formatted_Text)**  &ndash; opciones para mostrar texto con varios en línea de formatos y estilos.

## <a name="styling-label"></a>Etiqueta de aplicación de estilos

Las siguientes secciones tratan las propiedades de configuración de `Label` manualmente en una base por instancia. Tenga en cuenta que los conjuntos de propiedades se puede agrupar en un estilo que se aplica de manera coherente a una o varias vistas. Esto puede mejorar la legibilidad del código y realizar cambios de diseño sea más fácil de implementar. Consulte [estilos](~/xamarin-forms/user-interface/text/styles.md) para obtener más información.

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>Truncamiento y ajuste

Se pueden establecer etiquetas para controlar el texto que no cabe en una sola línea en una de varias maneras, expuestos por la `LineBreakMode` propiedad. [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) es una enumeración de las siguientes opciones:

- **HeadTruncation** &ndash; trunca el encabezado del texto, que muestra el extremo.
- **CharacterWrap** &ndash; ajusta el texto en una nueva línea en un límite de caracteres.
- **MiddleTruncation** &ndash; muestra el principio y al final del texto, con el reemplazo intermedio mediante puntos suspensivos.
- **NoWrap** &ndash; no ajusta el texto, mostrar solo mayor cantidad de texto que puede cabe en una sola línea.
- **TailTruncation** &ndash; muestra el principio del texto, truncar final.
- **WordWrap** &ndash; ajusta el texto en el límite de palabras.

## <a name="font"></a>Tipo de letra

Consulte [trabajar con fuentes](~/xamarin-forms/user-interface/text/fonts.md) para obtener más información.

## <a name="color"></a>Color

`Label`s puede establecerse para usar un color de texto personalizado a través de la enlazable `TextColor` propiedad.

Atención especial es necesaria para asegurarse de que los colores se podrán usar en cada plataforma. Dado que cada plataforma tiene distintos valores predeterminados para los colores de texto y fondo, deberá tener cuidado al elegir un valor predeterminado que funciona en cada uno.

Use el código siguiente para establecer el color del texto de una etiqueta:

Mediante código:

```csharp
public partial class LabelPage : ContentPage
{
    public LabelPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var label = new Label { Text="This is a label.", TextColor = Color.FromHex("#77d065"), FontSize = 20 };
        layout.Children.Add(label);
    }
}
```

En XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.LabelPage"
Title="Label Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
      <Label TextColor="#77d065" FontSize = "20" Text="This is a label." />
    </StackLayout>
  </ContentPage.Content>
</ContentPage>
```

![](label-images/textcolor.png "Ejemplo de etiqueta TextColor")

<a name="Formatted_Text" />

## <a name="formatted-text"></a>Texto con formato

Las etiquetas de exponen un `FormattedText` propiedad que permite presentar texto con varias fuentes y colores en la misma vista.

El `FormattedText` propiedad es de tipo [ `FormattedString` ](xref:Xamarin.Forms.FormattedString). Cadenas con formato se componen de uno o varios `Span`, cada uno con las siguientes propiedades:

- **BackgroundColor** &ndash; puede usarse para establecer un color de fondo, por ejemplo, para lograr un efecto de marcador de resaltado.
- **Atributos de fuente** &ndash; puede ser definido en negrita, cursiva o ninguno.
- **FontFamily** &ndash; establece la fuente que se usará.
- **FontSize** &ndash; establece el tamaño del texto.
- **ForegroundColor** &ndash; establece el color del texto.
- **Texto** &ndash; el texto que se presentará.

El código de C# siguiente muestra una etiqueta donde está la primera palabra en negrita y la última palabra está en roja:

```csharp
public partial class LabelPage : ContentPage
{
    public LabelPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
    var label = new Label { FontSize = 20 };
    var s = new FormattedString ();
    s.Spans.Add (new Span{ Text = "Red Bold", FontAttributes = FontAttributes.Bold });
    s.Spans.Add (new Span{ Text = "Default" });
    s.Spans.Add (new Span{ Text = "italic small", FontSize =  Device.GetNamedSize(NamedSize.Small, typeof(Label)), FontAttributes = FontAttributes.Italic});
    label.FormattedText = s;
        layout.Children.Add(label);
    }
}
```

En XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.LabelPage"
Title="Label Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
      <Label FontSize=20>
        <Label.FormattedText>
          <FormattedString>
            <Span Text="Red Bold" ForegroundColor="Red" FontAttributes="Bold" />
            <Span Text="Default" />
            <Span Text="italic small" FontAttributes="Italic" FontSize="Small" />
          </FormattedString>
        </Label.FormattedText>
      </Label>
    </StackLayout>
  </ContentPage.Content>
</ContentPage>
```

![](label-images/formattedtext.png "Ejemplo de etiqueta FormattedText")


## <a name="related-links"></a>Vínculos relacionados

- [Creación de aplicaciones móviles con Xamarin.Forms, capítulo 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [Texto (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [API de etiqueta](xref:Xamarin.Forms.Label)
