---
title: Etiqueta de Xamarin.Forms
description: En este artículo se explica cómo usar la clase de etiqueta de Xamarin.Forms para mostrar único y varias línea de texto en aplicaciones.
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/16/2018
ms.openlocfilehash: b5069381126db0859508480df5596ed5ec85686f
ms.sourcegitcommit: 4c0093ee5d4aeb16c0e6f0c740c4796736971651
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2018
ms.locfileid: "39203041"
---
# <a name="xamarinforms-label"></a>Etiqueta de Xamarin.Forms

_Mostrar texto en Xamarin.Forms_

El [ `Label` ](xref:Xamarin.Forms.Label) vista se utiliza para mostrar texto, único y varias líneas. Pueden tener las etiquetas de texto de color y fuentes personalizadas (familias de tamaños y opciones).

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>Truncamiento y ajuste

Se pueden establecer etiquetas para controlar el texto que no cabe en una sola línea en una de varias maneras, expuestos por la `LineBreakMode` propiedad. [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) es una enumeración con los valores siguientes:

- **HeadTruncation** &ndash; trunca el encabezado del texto, que muestra el extremo.
- **CharacterWrap** &ndash; ajusta el texto en una nueva línea en un límite de caracteres.
- **MiddleTruncation** &ndash; muestra el principio y al final del texto, con el reemplazo intermedio mediante puntos suspensivos.
- **NoWrap** &ndash; no ajusta el texto, mostrar solo mayor cantidad de texto que puede cabe en una sola línea.
- **TailTruncation** &ndash; muestra el principio del texto, truncar final.
- **WordWrap** &ndash; ajusta el texto en el límite de palabras.

## <a name="fonts"></a>Fuentes

Para obtener más información acerca de cómo especificar las fuentes en un `Label`, consulte [fuentes](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="colors"></a>Colores

`Label`s puede establecerse para usar un color de texto personalizado a través de la enlazable [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor) propiedad.

Atención especial es necesaria para asegurarse de que los colores se podrán usar en cada plataforma. Dado que cada plataforma tiene distintos valores predeterminados para los colores de texto y fondo, deberá tener cuidado al elegir un valor predeterminado que funciona en cada uno.

Use el siguiente XAML para establecer el color del texto de una etiqueta:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo">
    <StackLayout Padding="5,10">
      <Label TextColor="#77d065" FontSize = "20" Text="This is a label." />
    </StackLayout>
</ContentPage>
```

El código de C# equivalente es:

```csharp
public partial class LabelPage : ContentPage
{
    public LabelPage ()
    {
        InitializeComponent ();

        var layout = new StackLayout { Padding = new Thickness(5,10) };
        var label = new Label { Text="This is a label.", TextColor = Color.FromHex("#77d065"), FontSize = 20 };
        layout.Children.Add(label);
        this.Content = layout;
    }
}
```

Las capturas de pantalla siguientes muestran el resultado de establecer el `TextColor` propiedad:

![](label-images/textcolor.png "Ejemplo de etiqueta TextColor")

Para obtener más información acerca de los colores, consulte [colores](~/xamarin-forms/user-interface/colors.md).

<a name="Formatted_Text" />

## <a name="formatted-text"></a>Texto con formato

Las etiquetas de exponen un [ `FormattedText` ](xref:Xamarin.Forms.Label.FormattedText) propiedad que permite la presentación de texto con varias fuentes y colores en la misma vista.

El `FormattedText` propiedad es de tipo [ `FormattedString` ](xref:Xamarin.Forms.FormattedString), que consta de uno o varios [ `Span` ](xref:Xamarin.Forms.Span) instancias, se establece a través de la [ `Spans` ](xref:Xamarin.Forms.FormattedString.Spans) propiedad . Cada `Span` posee las siguientes propiedades:

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) : el color del fondo del intervalo.
- [`Font`](xref:Xamarin.Forms.Span.Font) : la fuente para el texto del intervalo.
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes) : los atributos de fuente para el texto del intervalo.
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily) – la familia de fuentes a la que pertenece la fuente para el texto del intervalo.
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize) – el tamaño de la fuente para el texto del intervalo.
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor) : el color del texto en el intervalo. Esta propiedad está obsoleta y se ha reemplazado por el `TextColor` propiedad.
- [`Style`](xref:Xamarin.Forms.Span.Style) : el estilo que se aplican al intervalo.
- [`Text`](xref:Xamarin.Forms.Span.Text) : el texto del intervalo.
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor) : el color del texto en el intervalo.

En el ejemplo de XAML siguiente se muestra un `FormattedText` propiedad que consta de tres `Span` instancias:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo - XAML">
    <StackLayout Padding="5,10">
        ...
        <Label>
            <Label.FormattedText>
                <FormattedString>
                    <Span Text="Red Bold, " TextColor="Red" FontAttributes="Bold" />
                    <Span Text="default, " Style="{DynamicResource BodyStyle}" />
                    <Span Text="italic small." FontAttributes="Italic" FontSize="Small" />
                </FormattedString>
            </Label.FormattedText>
        </Label>
    </StackLayout>
</ContentPage>
```

El código de C# equivalente es:

```csharp
public class LabelPageCode : ContentPage
{
    public LabelPageCode ()
    {
        var layout = new StackLayout{ Padding = new Thickness (5, 10) };
        ...
        var formattedString = new FormattedString ();
        formattedString.Spans.Add (new Span{ Text = "Red bold, ", ForegroundColor = Color.Red, FontAttributes = FontAttributes.Bold });
        formattedString.Spans.Add (new Span { Text = "default, ", Style = Device.Styles.BodyStyle });
        formattedString.Spans.Add (new Span { Text = "italic small.", FontAttributes = FontAttributes.Italic, FontSize =  Device.GetNamedSize(NamedSize.Small, typeof(Label)) });

        layout.Children.Add (new Label { FormattedText = formattedString });
        this.Content = layout;
    }
}
```

> [!NOTE]
> El [ `Text` ](xref:Xamarin.Forms.Span.Text) propiedad de un `Span` puede establecerse mediante enlace de datos. Para obtener más información, consulte [enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Las capturas de pantalla siguientes muestran el resultado de la configuración de la `FormattedString` propiedad a tres `Span` instancias:

![](label-images/formattedtext.png "Ejemplo de etiqueta FormattedText")

## <a name="styling-a-label"></a>Aplicar un estilo a una etiqueta

Las secciones anteriores tratan la configuración [ `Label` ](xref:Xamarin.Forms.Label) las propiedades de por instancia. Sin embargo, los conjuntos de propiedades se pueden agrupar en un estilo que se aplica de manera coherente a una o varias vistas. Esto puede mejorar la legibilidad del código y realizar cambios de diseño sea más fácil de implementar. Para obtener más información, consulte [estilos](~/xamarin-forms/user-interface/text/styles.md).

## <a name="related-links"></a>Vínculos relacionados

- [Creación de aplicaciones móviles con Xamarin.Forms, capítulo 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [Texto (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [API de etiqueta](xref:Xamarin.Forms.Label)
