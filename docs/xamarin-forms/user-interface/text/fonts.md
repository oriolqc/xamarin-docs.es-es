---
title: Fuentes de Xamarin.Forms
description: En este artículo se explica cómo especificar información de fuente en los controles que muestran texto en las aplicaciones de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/28/2019
ms.openlocfilehash: de77be818abbe1250946ee2ce1599235b79d8c01
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2019
ms.locfileid: "67512980"
---
# <a name="fonts-in-xamarinforms"></a>Fuentes de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFonts/)

Este artículo describe cómo Xamarin.Forms le permite especificar atributos de fuente (incluidas peso y tamaño) en los controles que muestran texto. Puede ser información de fuente [especificado en el código](#Setting_Font_in_Code) o [especificado en XAML](#Setting_Font_in_Xaml). Tiene ' también es posible utilizar un [fuente personalizada](#Using_a_Custom_Font), y [mostrar iconos de fuente](#display-font-icons).

<a name="Setting_Font_in_Code" />

## <a name="set-the-font-in-code"></a>Establecer la fuente en el código

Use las tres propiedades relacionadas con la fuente de todos los controles que muestran texto:

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

Xamarin.Forms también define los campos de la [ `NamedSize` ](xref:Xamarin.Forms.NamedSize) enumeración que representan los tamaños de fuente específico. Para obtener más información acerca de los tamaños de fuente con nombre, vea [denominado tamaños de fuente](#named-font-sizes).

<a name="FontAttributes" />

### <a name="font-attributes"></a>Atributos de fuente

Estilos de fuente como **negrita** y *cursiva* se pueden establecer en el `FontAttributes` propiedad. Actualmente se admiten los siguientes valores:

-  **Ninguno**
-  **Negrita**
-  **Cursiva**

El `FontAttribute` enumeración se puede usar como sigue (se puede especificar un atributo único o `OR` juntarlas):

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="set-font-info-per-platform"></a>Conjunto de información de fuente por plataforma

Como alternativa, el `Device.RuntimePlatform` propiedad puede usarse para establecer los nombres de fuente diferente en cada plataforma, como se muestra en este código:

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/Lobster-Regular.ttf#Lobster",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

Es una buena fuente de información de fuentes para iOS [iosfonts.com](http://iosfonts.com).

<a name="Setting_Font_in_Xaml" />

## <a name="set-the-font-in-xaml"></a>Establezca la fuente en XAML

Xamarin.Forms controla ese texto para mostrar todos tienen un `FontSize` propiedad que se puede establecer en XAML. La manera más sencilla para establecer la fuente en XAML es usar los valores de enumeración de tamaño con nombre, como se muestra en este ejemplo:

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

Hay un convertidor de tipos integrado para el `FontSize` propiedad que permite que todos los valores de fuente se exprese como un valor de cadena en XAML. Además, el `FontAttributes` propiedad puede utilizarse para especificar atributos de fuente:

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

[`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#providing-platform-specific-values) También se puede utilizar en XAML para representar una fuente diferente en cada plataforma. El ejemplo siguiente utiliza un nombre de fuente personalizados en iOS (<span style="font-family:MarkerFelt-Thin">MarkerFelt Thin</span>) y especifica solo tamaño y atributos en las otras plataformas:

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

Al especificar un nombre de fuente personalizada, siempre es una buena idea usar `OnPlatform`, ya que es difícil encontrar una fuente que está disponible en todas las plataformas.

## <a name="named-font-sizes"></a>Tamaños de fuente con nombre

Xamarin.Forms define los campos de la [ `NamedSize` ](xref:Xamarin.Forms.NamedSize) enumeración que representan los tamaños de fuente específico. La tabla siguiente muestra el `NamedSize` miembros y sus tamaños predeterminados en iOS, Android y la plataforma Universal de Windows (UWP):

| Miembro | iOS | Android | UWP |
| --- | --- | --- | --- |
| `Default` | 16 | 14 | 14 |
| `Micro` | 11 | 10 | 15.667 |
| `Small` | 13 | 14 | 18.667 |
| `Medium` | 16 | 17 | 22.667 |
| `Large` | 20 | 22 | 32 |
| `Body` | 17 | 16 | 14 |
| `Header` | 17 | 96 | 46 |
| `Title` | 28 | 24 | 24 |
| `Subtitle` | 22 | 16 | 20 |
| `Caption` | 12 | 12 | 12 |

Los tamaños de fuente con nombre se pueden establecer mediante XAML y código. Además, el `Device.GetNamedSize` se puede llamar el método para devolver un `double` que representa el tamaño de fuente con nombre:

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

> [!NOTE]
> En iOS y Android, tamaños de fuente con nombre realizará el escalado automático en función de las opciones de accesibilidad del sistema operativo. Este comportamiento se puede deshabilitar en iOS con una plataforma específica. Para obtener más información, consulte [accesibilidad escalado denominada tamaños de fuente en iOS](~/xamarin-forms/platform/ios/named-font-size-scaling.md).

<a name="Using_a_Custom_Font" />

## <a name="use-a-custom-font"></a>Usar una fuente personalizada

El uso de una fuente que no sea de los tipos de letra integrados requiere algún código específico de la plataforma. Esta captura de pantalla muestra la fuente personalizada **Lobster** desde [fuentes de código abierto de Google](https://www.google.com/fonts) representar mediante Xamarin.Forms.

 [![Fuente personalizada en iOS y Android](fonts-images/custom-sml.png "ejemplo de fuentes personalizado")](fonts-images/custom.png#lightbox "ejemplo de fuentes personalizado")

A continuación, se describen los pasos necesarios para cada plataforma. Cuando se incluyen archivos de fuente personalizada con una aplicación, asegúrese de comprobar que permite la licencia de la fuente de distribución.

### <a name="ios"></a>iOS

Es posible mostrar una fuente personalizada en primer lugar, lo que garantiza que se carga y, luego, que hace referencia a él por su nombre mediante Xamarin.Forms `Font` métodos.
Siga las instrucciones de [esta entrada de blog](https://blog.xamarin.com/custom-fonts-in-ios/):

1. Agregar el archivo de fuentes con **acción de compilación: BundleResource**, y
2. Actualización de la **Info.plist** archivo (**fuentes proporcionadas por la aplicación**, o `UIAppFonts`, key), a continuación,
3. Hacer referencia al mismo nombre siempre que defina una fuente de Xamarin.Forms.

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" : null // set only for iOS
}
```

### <a name="android"></a>Android

Xamarin.Forms para Android puede hacer referencia a una fuente personalizada que se ha agregado al proyecto siguiendo un estándar de nomenclatura. Primero agregue el archivo de fuente a la **activos** carpeta en el proyecto de aplicación y establezca *acción de compilación: AndroidAsset*. A continuación, utilice la ruta de acceso completa y *nombre de la fuente* separados por una almohadilla (#) como el nombre de fuente en Xamarin.Forms, como se muestra en el siguiente fragmento de código:

```csharp
new Label
{
  Text = "Hello, Forms!",
  FontFamily = Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : null // set only for Android
}
```

### <a name="windows"></a>Windows

Xamarin.Forms para plataformas de Windows puede hacer referencia a una fuente personalizada que se ha agregado al proyecto siguiendo un estándar de nomenclatura. Primero agregue el archivo de fuente a la **/Assets/fuentes/** carpeta en el proyecto de aplicación y establezca el <span class="UIItem">compilar: contenido de la acción</span>. A continuación, utilice la fuente y la ruta de acceso de nombre de archivo completo, seguido por una almohadilla (#) y el <span class="UIItem">nombre de la fuente</span>, tal y como se muestra el siguiente fragmento de código:

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.UWP ? "Assets/Fonts/Lobster-Regular.ttf#Lobster" : null // set only for UWP apps
}
```

> [!NOTE]
> Tenga en cuenta que el nombre de archivo de fuente y el nombre de fuente pueden ser diferentes. Para conocer el nombre de fuente en Windows, haga clic en el archivo .ttf y seleccione **Preview**. A continuación, se puede determinar el nombre de fuente desde la ventana Vista previa.

El código común de la aplicación ya está completo. El código del marcador de teléfono específico de la plataforma se implementará como [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

### <a name="xaml"></a>XAML

También puede usar [ `Device.RuntimePlatform` ](~/xamarin-forms/platform/device.md#interact-with-the-ui-from-background-threads) en XAML para representar una fuente personalizada:

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

## <a name="display-font-icons"></a>Mostrar iconos de fuente

Iconos de la fuente pueden mostrar las aplicaciones de Xamarin.Forms mediante la especificación de los datos del icono de fuente en un `FontImageSource` objeto. Esta clase, que se deriva de la [ `ImageSource` ](xref:Xamarin.Forms.ImageSource) class, tiene las siguientes propiedades:

- `Glyph` : el valor de carácter unicode del icono de fuente, especificado como un `string`.
- `Size` : un `double` valor que indica el tamaño, en unidades independientes del dispositivo, del icono de la fuente representada. El valor predeterminado es 30.
- `FontFamily` : un `string` que representa la familia de fuentes a la que pertenece el icono de fuente.
- `Color` – opcional [ `Color` ](xref:Xamarin.Forms.Color) valor que se usará al mostrar el icono de fuente.

Estos datos se usan para crear un archivo PNG, que puede mostrarse en cualquier vista que puede mostrar un `ImageSource`. Este enfoque permite que los iconos de la fuente, como los emojis, que va a mostrar varias vistas, en lugar de limitar la presentación de iconos de fuente para un solo texto, ya que presenta la vista, como un [ `Label` ](xref:Xamarin.Forms.Label).

> [!IMPORTANT]
> Actualmente solo se pueden especificar iconos de la fuente por su representación de caracteres unicode.

En el siguiente ejemplo XAML tiene un icono de única fuente mostrado por un [ `Image` ](xref:Xamarin.Forms.Image) vista:

```xaml
<Image BackgroundColor="#D1D1D1">
    <Image.Source>
        <FontImageSource Glyph="&#xf30c;"
                         FontFamily="{OnPlatform iOS=Ionicons, Android=ionicons.ttf#}"
                         Size="44" />
    </Image.Source>
</Image>
```

Este código muestra un icono de XBox, desde la familia de fuentes Ionicons, en un [ `Image` ](xref:Xamarin.Forms.Image) vista. Tenga en cuenta que mientras el unicode de caracteres de este icono es `\uf30c`, tiene que ser caracteres de escape en XAML y por lo que se convierte en `&#xf30c;`. El código de C# equivalente es:

```csharp
Image image = new Image { BackgroundColor = Color.FromHex("#D1D1D1") };
image.Source = new FontImageSource
{
    Glyph = "\uf30c",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Ionicons" : "ionicons.ttf#",
    Size = 44
};
```

Las siguientes capturas de pantalla, desde el [diseños enlazable](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindableLayouts/) de ejemplo, se muestran varios iconos de fuente mostrados por un diseño enlazable:

![Captura de pantalla de los iconos de la fuente que se muestra, en iOS y Android](fonts-images/font-image-source.png "iconos de fuente que se muestran en una vista de imagen")

## <a name="related-links"></a>Vínculos relacionados

- [FontsSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFonts/)
- [Texto (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text/)
- [Diseños enlazables (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindableLayouts/)
- [Diseños enlazables](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
