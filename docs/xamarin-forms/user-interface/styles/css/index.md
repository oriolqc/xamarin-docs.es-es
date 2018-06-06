---
title: Aplicaciones de Xamarin.Forms con hojas de estilos en cascada (CSS) de la aplicación de estilos
description: Xamarin.Forms es compatible con los elementos de estilo visual con hojas de estilos en cascada (CSS).
ms.prod: xamarin
ms.assetid: C89D57A6-DAB9-4C42-963F-26D67627DDC2
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 76ca67f7ac8a8e27e5f502455d48874c775fc172
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34794090"
---
# <a name="styling-xamarinforms-apps-using-cascading-style-sheets-css"></a>Aplicaciones de Xamarin.Forms con hojas de estilos en cascada (CSS) de la aplicación de estilos

_Xamarin.Forms es compatible con los elementos de estilo visual con hojas de estilos en cascada (CSS)._

Xamarin.Forms 3.0 introduce la posibilidad de crear una aplicación de uso de CSS. Una hoja de estilos se compone de una lista de reglas, con cada regla que consta de uno o varios selectores y un bloque de declaración. Un bloque de declaración consta de una lista de declaraciones entre llaves, con cada declaración que consta de una propiedad, un signo de dos puntos y un valor. Cuando hay varias declaraciones en un bloque, se inserta un punto y coma como separador. En el ejemplo de código siguiente se muestra algunos CSS conforme Xamarin.Forms:

```css
^contentpage {
    background-color: lightgray;
}

#listView {
    background-color: lightgray;
}

stacklayout {
    margin: 20;
}

.mainPageTitle {
    font-style: bold;
    font-size: medium;
}

.mainPageSubtitle {
    margin-top: 15;
}

.detailPageTitle {
    font-style: bold;
    font-size: medium;
    text-align: center;
}

.detailPageSubtitle {
    text-align: center;
    font-style: italic;
}

listview image {
    height: 60;
    width: 60;
}

stacklayout>image {
    height: 200;
    width: 200;
}
```

En Xamarin.Forms, hojas de estilos CSS se analizan y se evalúan en tiempo de ejecución, en lugar de tiempo de compilación y las hojas de estilos se volver a analizar en uso.

> [!NOTE]
> Actualmente, todos los estilos que es posible con un estilo de XAML no se puede realizar con CSS. Sin embargo, los estilos XAML se pueden utilizar para complementar la CSS para las propiedades que actualmente no sean compatibles con Xamarin.Forms. Para obtener más información sobre los estilos XAML, vea [aplicaciones de Xamarin.Forms la aplicación de estilos con estilos de XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

El [MonkeyAppCSS](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/MonkeyAppCSS/) ejemplo muestra el uso de CSS para aplicar estilo a una aplicación sencilla y se muestra en las capturas de pantalla siguiente:

[![Página principal de MonkeyApp con estilos CSS](css-images/MonkeyAppMainPage.png "MonkeyApp Main Page con estilos CSS")](css-images/MonkeyAppMainPage-Large.png#lightbox "MonkeyApp Main Page con estilos CSS")

[![Página de detalles de MonkeyApp con estilos CSS](css-images/MonkeyAppDetailPage.png "MonkeyApp página de detalles con estilos CSS")](css-images/MonkeyAppDetailPage-Large.png#lightbox "MonkeyApp página de detalles con estilos CSS")

> [!NOTE]
> No es posible actualmente definir el estilo de color de fondo de un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) con una hoja de estilos. Por lo tanto, en la aplicación de ejemplo del [ `NavigationPage.BarBackgroundColor` ](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) propiedad está establecida en el código.

## <a name="consuming-a-style-sheet"></a>Consumir una hoja de estilos

El proceso para agregar una hoja de estilos a una solución es como sigue:

1. Agregue un archivo CSS vacío para el proyecto de biblioteca de .NET estándar.
1. Establecer la acción de compilación del archivo CSS **EmbeddedResource**.

### <a name="loading-a-style-sheet"></a>Carga una hoja de estilos

Hay una serie de métodos que puede utilizar para cargar una hoja de estilos.

### <a name="xaml"></a>XAML

Una hoja de estilos se puede cargar y analizar con el [ `StyleSheet` ](xref:Xamarin.Forms.StyleSheets.StyleSheet) clase antes de que se va a agregar a la [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) para la página:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    ...
</ContentPage>
```

El [ `StyleSheet.Source` ](xref:Xamarin.Forms.Xaml.StyleSheetExtension.Source) propiedad especifica la hoja de estilos como un URI relativo a la ubicación del archivo XAML envolvente o con respecto a la raíz del proyecto si el URI comienza con un `/`.

> [!WARNING]
> El archivo CSS no se cargará si es la acción de compilación no está establecida en **EmbeddedResource**.

Como alternativa, puede cargar y analizar con la hoja de estilos la [ `StyleSheet` ](xref:Xamarin.Forms.StyleSheets.StyleSheet) clase mediante la inclusión en un `CDATA` sección:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet>
            <![CDATA[
            ^contentpage {
                background-color: lightgray;
            }
            ]]>
        </StyleSheet>
    </ContentPage.Resources>
    ...
</ContentPage>
```

### <a name="c"></a>C#

En C#, una hoja de estilos puede cargarse como un recurso incrustado y agregarse a la [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) para la página:

```csharp
public partial class MyPage : ContentPage
{
    public MyPage()
    {
        InitializeComponent();

        this.Resources.Add(StyleSheet.FromAssemblyResource(
            IntrospectionExtensions.GetTypeInfo(typeof(MyPage)).Assembly,
            "MyProject.Assets.styles.css"));
    }
}
```

El primer argumento para el `StyleSheet.FromAssemblyResource` método es el ensamblado que contiene la hoja de estilos, mientras que el segundo argumento es un `string` que representa el identificador de recurso. Se puede obtener el identificador de recursos de la **propiedades** ventana cuando se selecciona el archivo CSS.

Como alternativa, se puede cargar la hoja de estilos desde una `StringReader` y se agrega a la [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) para la página:

```csharp
public partial class MyPage : ContentPage
{
    public MyPage()
    {
        InitializeComponent();

        using (var reader = new StringReader("^contentpage { background-color: lightgray; }"))
        {
            this.Resources.Add(StyleSheet.FromReader(reader));
        }
    }
}
```

El argumento de la `StyleSheet.FromReader` método es el `TextReader` que lea la hoja de estilos.

## <a name="selecting-elements-and-applying-properties"></a>Selección de elementos y aplicar propiedades

CSS utiliza selectores para determinar qué elementos se deben tener como destino. Estilos con la coincidencia de los selectores se aplican de forma consecutiva, en orden de definición. Los estilos definidos en un elemento específico siempre se aplican en último lugar. Para obtener más información acerca de los selectores compatibles, consulte [Selector referencia](#selector-reference).

CSS utiliza propiedades para definir el estilo de un elemento seleccionado. Cada propiedad tiene un conjunto de valores posibles, y algunas propiedades pueden afectar a cualquier tipo de elemento, mientras que otras se aplican a grupos de elementos. Para obtener más información acerca de las propiedades admitidas, vea [referencia de propiedad](#property-reference).

### <a name="selecting-elements-by-type"></a>Selección de elementos por tipo

Tipo con el entre mayúsculas y minúsculas se pueden seleccionar elementos en el árbol visual `element` selector:

```css
stacklayout {
    margin: 20;
}
```

Este selector identifica cualquier [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) elementos en las páginas que consumen la hoja de estilos y establecen sus márgenes en un grosor uniforme de 20.

> [!NOTE]
> El `element` selector no identifica subclases del tipo especificado.

### <a name="selecting-elements-by-base-class"></a>Selección de elementos por la clase base

Clase base con el entre mayúsculas y minúsculas se pueden seleccionar elementos en el árbol visual `^base` selector:

```css
^contentpage {
    background-color: lightgray;
}
```

Este selector identifica cualquier [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) elementos que consumen la hoja de estilos y establece su fondo de color para `lightgray`.

> [!NOTE]
> El `^base` selector es específico de Xamarin.Forms y no forma parte de la especificación de CSS.

### <a name="selecting-an-element-by-name"></a>Seleccionar un elemento por nombre

Se pueden seleccionar elementos individuales en el árbol visual con las mayúsculas y minúsculas `#id` selector:

```css
#listView {
    background-color: lightgray;
}
```

Este selector identifica el elemento cuyo [ `StyleId` ](xref:Xamarin.Forms.Element.StyleId) propiedad está establecida en `listView`. Sin embargo, si la `StyleId` propiedad no está establecida, el selector volverá a usar el `x:Name` del elemento. Por lo tanto, en el siguiente ejemplo XAML, el `#listView` selector identificará la [ `ListView` ](xref:Xamarin.Forms.ListView) cuyo `x:Name` atributo está establecido en `listView`y establecerá su color de fondo en `lightgray`.

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <StackLayout>
        <ListView x:Name="listView" ...>
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

### <a name="selecting-elements-with-a-specific-class-attribute"></a>Selección de elementos con un atributo de clase específica

Se pueden seleccionar elementos con un atributo de clase específica con las mayúsculas y minúsculas `.class` selector:

```css
.detailPageTitle {
    font-style: bold;
    font-size: medium;
    text-align: center;
}

.detailPageSubtitle {
    text-align: center;
    font-style: italic;
}
```

Una clase CSS puede asignarse a un elemento XAML estableciendo el [ `StyleClass` ](xref:Xamarin.Forms.VisualElement.StyleClass) propiedad del elemento en el nombre de clase CSS. Por lo tanto, en el siguiente ejemplo XAML, los estilos definidos por el `.detailPageTitle` clase están asignados a la primera [ `Label` ](xref:Xamarin.Forms.Label), mientras que los estilos definidos por el `.detailPageSubtitle` clase se asigna al segundo `Label`.

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <ScrollView>
        <StackLayout>
            <Label ... StyleClass="detailPageTitle" />
            <Label ... StyleClass="detailPageSubtitle"/>
            ...
        </StackLayout>
    </ScrollView>
</ContentPage>
```

### <a name="selecting-child-elements"></a>Selección de elementos secundarios

Se pueden seleccionar los elementos secundarios en el árbol visual con las mayúsculas y minúsculas `element element` selector:

```css
listview image {
    height: 60;
    width: 60;
}
```

Este selector identifica cualquier [ `Image` ](xref:Xamarin.Forms.Image) elementos secundarios de [ `ListView` ](xref:Xamarin.Forms.ListView) elementos y su alto y ancho se establece en 60. Por lo tanto, en el siguiente ejemplo XAML, el `listview image` selector identificará la [ `Image` ](xref:Xamarin.Forms.Image) que es un elemento secundario de la [ `ListView` ](xref:Xamarin.Forms.ListView)y su alto y ancho se establece en 60.

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <StackLayout>
        <ListView ...>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <Grid>
                            ...
                            <Image ... />
                            ...
                        </Grid>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

> [!NOTE]
> El `element element` selector no requiere el elemento secundario como una _directa_ elemento secundario del elemento primario: el elemento secundario puede tener un elemento primario diferente. Selección se produce siempre que un antecesor es el primer elemento especificado.

### <a name="selecting-direct-child-elements"></a>Selección de elementos secundarios directos

Dirigir los elementos secundarios en el árbol visual pueden seleccionarse con las mayúsculas y minúsculas `element>element` selector:

```css
stacklayout>image {
    height: 200;
    width: 200;
}
```

Este selector identifica cualquier [ `Image` ](xref:Xamarin.Forms.Image) directa de elementos que son elementos secundarios de [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) elementos y establece su alto y ancho a 200. Por lo tanto, en el siguiente ejemplo XAML, el `stacklayout>image` selector identificará la [ `Image` ](xref:Xamarin.Forms.Image) que es un elemento secundario directo de la [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)y establece su alto y ancho a 200.

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <ScrollView>
        <StackLayout>
            ...
            <Image ... />
            ...
        </StackLayout>
    </ScrollView>
</ContentPage>
```

> [!NOTE]
> El `element>element` selector requiere que el elemento secundario es un _directa_ secundarios del elemento primario.

## <a name="selector-reference"></a>Referencia de selector

Xamarin.Forms admite los selectores CSS siguientes:

|Selector|Ejemplo|Descripción|
|---|---|---|
|`.class`|`.header`|Selecciona todos los elementos con el `StyleClass` propiedad que contiene "encabezado". Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|
|`#id`|`#email`|Selecciona todos los elementos con `StyleId` establecido en `email`. Si `StyleId` no se establece, el retroceso a `x:Name`. Cuando se usa XAML, `x:Name` es preferible `StyleId`. Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|
|`*`|`*`|Selecciona todos los elementos.|
|`element`|`label`|Selecciona todos los elementos de tipo `Label`, pero no Sub-las clases. Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|
|`^base`|`^contentpage`|Selecciona todos los elementos con `ContentPage` como la clase base, incluidos los `ContentPage` propio. Tenga en cuenta que este selector distingue mayúsculas de minúsculas y no forma parte de la especificación de CSS.|
|`element,element`|`label,button`|Todos los selecciona `Button` elementos y todos `Label` elementos. Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|
|`element element`|`stacklayout label`|Todos los selecciona `Label` elementos dentro de un `StackLayout`. Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|
|`element>element`|`stacklayout>label`|Todos los selecciona `Label` elementos con `StackLayout` como elemento primario directo. Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|
|`element+element`|`label+entry`|Todos los selecciona `Entry` elementos directamente después de un `Label`. Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|
|`element~element`|`label~entry`|Todos los selecciona `Entry` elementos precedido por un `Label`. Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|

Estilos con la coincidencia de los selectores se aplican de forma consecutiva, en orden de definición. Los estilos definidos en un elemento específico siempre se aplican en último lugar.

> [!TIP]
> Selectores pueden combinarse sin limitación, como `StackLayout>ContentView>label.email`.

Los selectores siguientes no se admiten actualmente:

- `[attribute]`
- `@media` y `@supports`
- `:` y `::`

> [!NOTE]
> No se admiten especificidad e invalidaciones de especificidad.

## <a name="property-reference"></a>Referencia de propiedad

Las siguientes propiedades CSS son compatibles con Xamarin.Forms (en el **valores** son de tipos de columna, _cursiva_, mientras que los literales de cadena son `gray`):

|Property|Se aplica a|Valores|Ejemplo|
|---|---|---|---|
|`background-color`|`VisualElement`|_Color_ \| `initial` |`background-color: springgreen;`|
|`background-image`|`Page`|_Cadena_ \| `initial` |`background-image: bg.png;`|
|`border-color`|`Button`, `Frame`|_Color_ \| `initial`|`border-color: #9acd32;`|
|`border-width`|`Button`|_Doble_ \| `initial` |`border-width: .5;`|
|`color`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`|_Color_ \| `initial` |`color: rgba(255, 0, 0, 0.3);`|
|`direction`|`VisualElement`|`ltr` \| `rtl` \| `inherit` \| `initial` |`direction: rtl;`|
|`font-family`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_Cadena_ \| `initial` |`font-family: Consolas;`|
|`font-size`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_doble_ \| _namedsize_ \| `initial` |`font-size: 12;`|
|`font-style`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|`bold` \| `italic` \| `initial` |`font-style: bold;`|
|`height`|`VisualElement`|_Doble_ \| `initial` |`min-height: 250;`|
|`margin`|`View`|_Grosor_ \| `initial` |`margin: 6 12;`|
|`margin-left`|`View`|_Grosor_ \| `initial` |`margin-left: 3;`|
|`margin-top`|`View`|_Grosor_ \| `initial` |`margin-top: 2;`|
|`margin-right`|`View`|_Grosor_ \| `initial` |`margin-right: 1;`|
|`margin-bottom`|`View`|_Grosor_ \| `initial` |`margin-bottom: 6;`|
|`min-height`|`VisualElement`|_Doble_ \| `initial` |`min-height: 50;`|
|`min-width`|`VisualElement`|_Doble_ \| `initial` |`min-width: 112;`|
|`opacity`|`VisualElement`|_Doble_ \| `initial` |`opacity: .3;`|
|`padding`|`Layout`, `Page`|_Grosor_ \| `initial` |`padding: 6 12 12;`|
|`padding-left`|`Layout`, `Page`|_Doble_ \| `initial`|`padding-left: 3;`|
|`padding-top`|`Layout`, `Page`| _Doble_ \| `initial` |`padding-top: 4;`|
|`padding-right`|`Layout`, `Page`| _Doble_ \| `initial` |`padding-right: 2;`|
|`padding-bottom`|`Layout`, `Page`| _Doble_ \| `initial` |`padding-bottom: 6;`|
|`text-align`| `Entry`, `EntryCell`, `Label`, `SearchBar`|`left` \| `right` \| `center` \| `start` \| `end` \| `initial`. `left` y `right` debería evitarse en entornos de derecha a izquierda.| `text-align: right;`|
|`visibility`|`VisualElement`|`true` \| `visible` \| `false` \| `hidden` \| `collapse` \| `initial `|`visibility: hidden;`|
|`width`|`VisualElement`|_Doble_ \| `initial`|`min-width: 320;`|

> [!NOTE]
> `initial` es un valor válido para todas las propiedades. Borra el valor (restablece en el valor predeterminado) que se ha establecido de otro estilo.

Las siguientes propiedades no se admiten actualmente:

- `all: initial`.
- Propiedades del diseño (cuadro o cuadrícula).
- Propiedades de forma abreviada, como `font`, y `border`.

Además, no hay ningún `inherit` herencia de valor de modo que no se admite. Por lo tanto, por ejemplo, no es posible, establezca el `font-size` propiedad en un diseño de y espera que todos los [ `Label` ](xref:Xamarin.Forms.Label) instancias en el diseño para heredar el valor. La única excepción es el `direction` propiedad, que tiene un valor predeterminado de `inherit`.

### <a name="color"></a>Color

Los siguientes `color` se admiten valores:

- `X11` [colores](https://en.wikipedia.org/wiki/X11_color_names/), que coinciden con los colores CSS, los colores predefinidos de UWP y los colores de Xamarin.Forms. Tenga en cuenta que estos valores de color distinguen mayúsculas de minúsculas.
- hex colores: `#rgb`, `#argb`, `#rrggbb`, `#aarrggbb`
- los colores RGB: `rgb(255,0,0)`, `rgb(100%,0%,0%)`. Los valores son en el intervalo 0-255, o 0-100%.
- colores de RGBA: `rgba(255, 0, 0, 0.8)`, `rgba(100%, 0%, 0%, 0.8)`. Es el valor de opacidad en el intervalo de 0,0 a 1,0.
- HSL colores: `hsl(120, 100%, 50%)`. El valor h está en el intervalo 0-360, mientras s y l están en el intervalo 0-100%.
- colores de HSLA: `hsla(120, 100%, 50%, .8)`. Es el valor de opacidad en el intervalo de 0,0 a 1,0.

### <a name="thickness"></a>Thickness

Uno, dos, tres o cuatro `thickness` se admiten valores, separados por espacios en blanco:

- Un solo valor indica el grosor uniforme.
- Dos valores indican grosor vertical y horizontal.
- Tres valores indican la parte superior, horizontal (izquierda y derecha) y, luego, grosor de la parte inferior.
- Cuatro valores indican la parte superior, a continuación, derecha, inferior y, luego, grosor de la izquierda.

> [!NOTE]
> CSS `thickness` valores son distintos de XAML [ `Thickness` ](/api/type/Xamarin.Forms.Thickness/) valores. Por ejemplo, en XAML, un valor de dos `Thickness` indica el grosor horizontal y vertical, mientras que un valor de cuatro `Thickness` indica izquierda, superior y, luego, derecha, abajo, a continuación, grosor. Además, XAML `Thickness` valores están delimitadas por comas.

### <a name="namedsize"></a>NamedSize

Lo siguiente entre mayúsculas y minúsculas `namedsize` se admiten valores:

- `default`
- `micro`
- `small`
- `medium`
- `large`

El significado exacto de cada `namedsize` valor es dependiente de la plataforma y dependientes de la vista.

## <a name="css-in-xamarinforms-with-xamarinuniversity"></a>CSS de Xamarin.Forms con Xamarin.University

> [!VIDEO https://youtube.com/embed/va-Vb7vtan8]

**Xamarin.Forms 3.0 CSS, por [Universidad de Xamarin](https://university.xamarin.com/)**

## <a name="related-links"></a>Vínculos relacionados

- [MonkeyAppCSS (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/MonkeyAppCSS/)
- [Aplicación de estilos para aplicaciones Xamarin.Forms con estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
