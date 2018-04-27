---
title: Fuentes
description: Configuración de las fuentes de Xamarin.Forms
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 52c86c63c328729211c4fbd22bd10b5eb1e56615
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/27/2018
---
# <a name="fonts"></a>Fuentes

Este artículo describe cómo Xamarin.Forms le permite especificar los atributos de fuente (incluidas peso y tamaño) de los controles que muestran texto. Puede ser información de fuentes [especificadas en el código](#Setting_Font_in_Code) o [especificado en Xaml](#Setting_Font_in_Xaml).
También es posible utilizar un [fuente personalizada](#Using_a_Custom_Font).

<a name="Setting_Font_in_Code" />

## <a name="setting-font-in-code"></a>Establece la fuente en código

Utilice las tres propiedades relacionadas con la fuente de todos los controles que muestran texto:

- **FontFamily** &ndash; el `string` nombre de la fuente.
- **FontSize** &ndash; el tamaño de fuente como un `double`.
- **Atributos de fuente** &ndash; una cadena que especifica la información de estilo como *cursiva* y **negrita** (mediante el `FontAttributes` enumeración en C#).

Este código muestra cómo crear una etiqueta y especifique el tamaño de fuente y el peso para mostrar:

```csharp
var about = new Label {
    FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
    FontAttributes = FontAttributes.Bold,
    Text = "Medium Bold Font"
};
```

<a name="FontSize" />

### <a name="font-size"></a>Tamaño de fuente

El `FontSize` propiedad puede establecerse en un valor double, por ejemplo:

```csharp
label.FontSize = 24;
```

También puede usar el `NamedSize` enumeración que tiene cuatro opciones integradas; Xamarin.Forms elige el mejor tamaño para cada plataforma.

-  **Micro**
-  **Pequeña**
-  **Medio**
-  **Grandes**


El `NamedSize` enumeración puede ser utilizado, donde un `FontSize` puede especificarse mediante la `Device.GetNamedSize` método para convertir el valor para un `double`:

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

<a name="FontAttributes" />

### <a name="font-attributes"></a>Atributos de fuente

Estilos de fuente como **negrita** y *cursiva* se pueden establecer en el `FontAttributes` propiedad. Actualmente se admiten los siguientes valores:

-  **Ninguno**
-  **Negrita**
-  **cursiva**

El `FontAttribute` enumeración puede utilizarse como se indica a continuación (puede especificar un único atributo o `OR` ellos juntos):

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="formattedstring"></a>FormattedString

Algunos controles de Xamarin.Forms (como `Label`) también admite atributos de fuente distintos dentro de una cadena con el `FormattedString` clase. A `FormattedString` consta de uno o más `Span`s, cada uno de los cuales puede tener sus propio formato atributos.

La `Span` clase tiene los siguientes atributos:

* **Texto** &ndash; que muestre el valor
* **FontFamily** &ndash; el nombre de fuente
* **FontSize** &ndash; el tamaño de fuente
* **Atributos de fuente** &ndash; como información de estilo *cursiva* y **negrita**
* **ForegroundColor** &ndash; color del texto
* **BackgroundColor** &ndash; color de fondo

Un ejemplo de crear y mostrar un `FormattedString` se muestra a continuación &ndash; tenga en cuenta que se asigna a las etiquetas `FormattedText` propiedad y no el `Text` propiedad.

```csharp
var labelFormatted = new Label ();
var fs = new FormattedString ();
fs.Spans.Add (new Span { Text="Red, ", ForegroundColor = Color.Red, FontSize = 20, FontAttributes = FontAttributes.Italic });
fs.Spans.Add (new Span { Text=" blue, ", ForegroundColor = Color.Blue, FontSize = 32 });
fs.Spans.Add (new Span { Text=" and green!", ForegroundColor = Color.Green, FontSize = 12 });
labelFormatted.FormattedText = fs;
```


### <a name="setting-font-info-per-platform"></a>Establecer la información de fuente por plataforma

Como alternativa, el `Device.RuntimePlatform` propiedad puede utilizarse para establecer los nombres de fuente diferentes en cada plataforma, como se muestra en este código:

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/Lobster-Regular.ttf#Lobster",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

Es una buena fuente de información de fuentes para iOS [iosfonts.com](http://iosfonts.com).

<a name="Setting_Font_in_Xaml" />

## <a name="setting-the-font-in-xaml"></a>Establecer la fuente en Xaml

Xamarin.Forms controla dicho texto para mostrar todos tienen un `Font` propiedad que se pueden establecer en Xaml. La manera más sencilla para establecer la fuente en Xaml es usar los valores de enumeración de tamaño con nombre, como se muestra en este ejemplo:

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

Hay un convertidor integrado para la `Font` propiedad que permite que todos los valores de fuente se exprese como un valor de cadena en Xaml. Los ejemplos siguientes muestran cómo se pueden especificar atributos de fuente y tamaños en Xaml:

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

Para especificar varias `Font` opciones, se combinan la configuración necesaria en una cadena de atributo de fuente. Debe tener el formato la cadena del atributo de fuente `"[font-face],[attributes],[size]"`. Es importante el orden de los parámetros, todos los parámetros son opcionales y varios `attributes` se pueden especificar, por ejemplo:

```xaml
<Label Text="Small bold text" FontAttributes="Bold" FontSize="Micro" />
<Label Text="Really big italic text" FontAttributes="Italic" FontSize="72" />
```

La `FormattedString` clase también puede usarse en XAML, como se muestra aquí:

```xaml
<Label>
    <Label.FormattedText>
        <FormattedString>
            <FormattedString.Spans>
                <Span Text="Red, " ForegroundColor="Red" FontAttributes="Italic" FontSize="20" />
                <Span Text=" blue, " ForegroundColor="Blue" FontSize="32" />
                <Span Text=" and green! " ForegroundColor="Green" FontSize="12"/>
            </FormattedString.Spans>
        </FormattedString>
    </Label.FormattedText>
</Label>
```

[`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#providing-platform-values) Puede también pueden utilizar en XAML para representar una fuente diferente en cada plataforma. En el ejemplo siguiente utiliza una fuente personalizada de iOS (<span style="font-family:MarkerFelt-Thin">fino MarkerFelt</span>) y especifica un solo tamaño y atributos de las otras plataformas:

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="MarkerFelt-Thin" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP" Value="Assets/Fonts/Lobster-Regular.ttf#Lobster" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

Al especificar un nombre de fuente personalizados, siempre es una buena idea usar `OnPlatform`, ya que es difícil encontrar una fuente que está disponible en todas las plataformas.

<a name="Using_a_Custom_Font" />

## <a name="using-a-custom-font"></a>Uso de una fuente personalizada

El uso de una fuente que no sea de los tipos de letra integradas requiere algún código específico de la plataforma. Esta captura de pantalla muestra la fuente personalizada **bogavante** de [fuentes de código abierto de Google](https://www.google.com/fonts) representa el uso de Xamarin.Forms.

 [![Fuente personalizada de iOS y Android](fonts-images/custom-sml.png "Custom fuentes Example")](fonts-images/custom.png#lightbox "Custom fuentes Example")

A continuación, se describen los pasos necesarios para cada plataforma. Cuando se incluyen archivos de fuente personalizada con una aplicación, asegúrese de comprobar que permite la licencia de la fuente de distribución.

### <a name="ios"></a>iOS

Es posible mostrar una fuente personalizada por asegurarse de que se carga primero, a continuación, hacer referencia a ella por su nombre mediante el Xamarin.Forms `Font` métodos.
Siga las instrucciones de [esta entrada de blog](http://blog.xamarin.com/custom-fonts-in-ios/):

1. Agregue el archivo de fuente con **acción de compilación: BundleResource**, y
2. Actualización la **Info.plist** archivo (**fuentes proporcionadas por la aplicación**, o `UIAppFonts`, key), a continuación,
3. Hacer referencia a él por su nombre siempre que defina una fuente de Xamarin.Forms.

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" : null // set only for iOS
}
```

### <a name="android"></a>Android

Xamarin.Forms para Android puede hacer referencia a una fuente personalizada que se ha agregado al proyecto siguiendo un estándar de nomenclatura específico. Primero agregue el archivo de fuente a la **activos** carpeta en el proyecto de aplicación y el conjunto *acción de compilación: AndroidAsset*. A continuación, utilice la ruta de acceso completa y *nombre de la fuente* separados por una almohadilla (#) como el nombre de la fuente de Xamarin.Forms, como se muestra en el siguiente fragmento de código:

```csharp
new Label
{
  Text = "Hello, Forms!",
  FontFamily = Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : null // set only for Android
}
```

### <a name="windows"></a>Windows

Xamarin.Forms para plataformas de Windows puede hacer referencia a una fuente personalizada que se ha agregado al proyecto siguiendo un estándar de nomenclatura específico. Primero agregue el archivo de fuente a la **/Assets/fuentes/** carpeta en el proyecto de aplicación y se establece la <span class="UIItem">compilar: contenido de la acción</span>. A continuación, utilizar el nombre completo de ruta de acceso y la fuente, seguido por una almohadilla (#) y el <span class="UIItem">nombre de la fuente</span>, como se muestra en el siguiente fragmento de código:

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.UWP ? "Assets/Fonts/Lobster-Regular.ttf#Lobster" : null // set only for UWP apps
}
```

> [!NOTE]
> Tenga en cuenta que el nombre de archivo de fuente y el nombre de la fuente pueden ser diferentes. Para conocer el nombre de fuente en Windows, haga clic en el archivo .ttf y seleccione **vista previa**. A continuación, se puede determinar el nombre de la fuente de la ventana de vista previa.

El código común de la aplicación ya está completo. El código del marcador de teléfono específico de la plataforma se implementará como [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

### <a name="xaml"></a>XAML

También puede usar [ `Device.RuntimePlatform` ](~/xamarin-forms/platform/device.md#providing-platform-values) en XAML que se debe representar una fuente personalizada:

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="Lobster-Regular" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP" Value="Assets/Fonts/Lobster-Regular.ttf#Lobster" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

<a name="Summary" />

## <a name="summary"></a>Resumen

Xamarin.Forms ofrece valores predeterminados básicos para que puedas cambiar el tamaño de texto fácilmente para todas las plataformas admitidas. También le permite especificar el tipo de fuente y tamaño &ndash; incluso de forma diferente para cada plataforma &ndash; cuando se requiere un control más minucioso. El `FormattedString` clase puede usarse para construir una cadena que contiene las especificaciones de fuente diferente utilizando el `Span` clase.

Información de fuentes también puede especificarse en Xaml mediante atributos de una fuente con el formato correcto o la `FormattedString` elemento con `Span` elementos secundarios.


## <a name="related-links"></a>Vínculos relacionados

- [FontsSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFonts/)
- [Texto (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text/)
