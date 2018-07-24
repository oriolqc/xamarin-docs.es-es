---
title: Fuentes de Xamarin.Forms
description: En este artículo se explica cómo especificar información de fuente en los controles que muestran texto en las aplicaciones de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: e6635bc13214a5a4e728fa3e71db86a8ea1c39d6
ms.sourcegitcommit: 4c0093ee5d4aeb16c0e6f0c740c4796736971651
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2018
ms.locfileid: "39202960"
---
# <a name="fonts-in-xamarinforms"></a>Fuentes de Xamarin.Forms

Este artículo describe cómo Xamarin.Forms le permite especificar atributos de fuente (incluidas peso y tamaño) en los controles que muestran texto. Puede ser información de fuente [especificado en el código](#Setting_Font_in_Code) o [especificado en XAML](#Setting_Font_in_Xaml).
También es posible usar un [fuente personalizada](#Using_a_Custom_Font).

<a name="Setting_Font_in_Code" />

## <a name="setting-font-in-code"></a>Establecer fuente en el código

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

También puede usar el `NamedSize` enumeración que tiene cuatro opciones integradas; Xamarin.Forms elige el mejor tamaño para cada plataforma.

-  **Micro**
-  **Pequeño**
-  **Medio**
-  **Grandes**

El `NamedSize` enumeración puede ser dondequiera que usa un `FontSize` puede especificarse mediante la `Device.GetNamedSize` método para convertir el valor de un `double`:

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

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

### <a name="setting-font-info-per-platform"></a>Información de fuente de la configuración por plataforma

Como alternativa, el `Device.RuntimePlatform` propiedad puede usarse para establecer los nombres de fuente diferente en cada plataforma, como se muestra en este código:

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/Lobster-Regular.ttf#Lobster",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

Es una buena fuente de información de fuentes para iOS [iosfonts.com](http://iosfonts.com).

<a name="Setting_Font_in_Xaml" />

## <a name="setting-the-font-in-xaml"></a>Establecer la fuente en XAML

Xamarin.Forms controla ese texto para mostrar todos tienen un `Font` propiedad que se puede establecer en XAML. La manera más sencilla para establecer la fuente en XAML es usar los valores de enumeración de tamaño con nombre, como se muestra en este ejemplo:

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

Hay un convertidor de tipos integrado para el `Font` propiedad que permite que todos los valores de fuente se exprese como un valor de cadena en XAML. Los ejemplos siguientes muestran cómo se pueden especificar atributos de fuente y tamaños en XAML:

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

Para especificar varias `Font` opciones, se combinan la configuración necesaria en una sola `Font` cadena de atributo. La cadena de atributos de fuente se debe dar formato como `"[font-face],[attributes],[size]"`. Es importante el orden de los parámetros, todos los parámetros son opcionales y varios `attributes` se pueden especificar, por ejemplo:

```xaml
<Label Text="Small bold text" Font="Bold, Micro" />
<Label Text="Medium custom font" Font="MarkerFelt-Thin, 42" />
<Label Text="Really big bold and italic text" Font="Bold, Italic, 72"  />
```

[`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#providing-platform-values) También se puede utilizar en XAML para representar una fuente diferente en cada plataforma. El ejemplo siguiente utiliza un nombre de fuente personalizados en iOS (<span style="font-family:MarkerFelt-Thin">MarkerFelt Thin</span>) y especifica solo tamaño y atributos en las otras plataformas:

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

<a name="Using_a_Custom_Font" />

## <a name="using-a-custom-font"></a>Uso de una fuente personalizada

El uso de una fuente que no sea de los tipos de letra integrados requiere algún código específico de la plataforma. Esta captura de pantalla muestra la fuente personalizada **Lobster** desde [fuentes de código abierto de Google](https://www.google.com/fonts) representar mediante Xamarin.Forms.

 [![Fuente personalizada en iOS y Android](fonts-images/custom-sml.png "ejemplo de fuentes personalizado")](fonts-images/custom.png#lightbox "ejemplo de fuentes personalizado")

A continuación, se describen los pasos necesarios para cada plataforma. Cuando se incluyen archivos de fuente personalizada con una aplicación, asegúrese de comprobar que permite la licencia de la fuente de distribución.

### <a name="ios"></a>iOS

Es posible mostrar una fuente personalizada en primer lugar, lo que garantiza que se carga y, luego, que hace referencia a él por su nombre mediante Xamarin.Forms `Font` métodos.
Siga las instrucciones de [esta entrada de blog](http://blog.xamarin.com/custom-fonts-in-ios/):

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

También puede usar [ `Device.RuntimePlatform` ](~/xamarin-forms/platform/device.md#providing-platform-values) en XAML para representar una fuente personalizada:

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

Xamarin.Forms proporciona valores predeterminados básicos para que pueda cambiar el tamaño de texto fácilmente para todas las plataformas compatibles. También permite especificar el tipo de fuente y tamaño &ndash; incluso de forma diferente para cada plataforma &ndash; cuando se requiere un control más minucioso.

También se puede especificar información de fuentes en XAML mediante atributos de fuente con el formato correcto.

## <a name="related-links"></a>Vínculos relacionados

- [FontsSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFonts/)
- [Texto (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text/)
