---
title: Xamarin.Forms BoxView
description: En este artículo se explica cómo usar un rectángulo de color para la decoración, gráficos e interacción de una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 4CBF703D-84A0-4CDF-A433-5926B587782A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2017
ms.openlocfilehash: edb2785362f2cc7377d9adb0c1a89a6fa2b08111
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35244320"
---
# <a name="xamarinforms-boxview"></a>Xamarin.Forms BoxView

[`BoxView`](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) Representa un rectángulo simple de un ancho especificado, el alto y el color. Puede usar `BoxView` para la decoración, rudimentarios gráficos y para la interacción con el usuario a través de la entrada táctil.

Porque Xamarin.Forms no cuenta con un sistema de gráficos vectoriales integrados, los `BoxView` ayuda a compensar. Algunos de los programas de ejemplo que se describe en este artículo se utiliza `BoxView` para representar los gráficos. El `BoxView` puede cambiará de tamaño para ser similar a una línea de un ancho específico y el grosor y, a continuación, girar cualquier ángulo utilizando la `Rotation` propiedad.

Aunque `BoxView` puede imitar gráficos sencillos, quizá desee investigar [SkiaSharp uso de Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) para los requisitos de gráficos más sofisticados.

En este artículo se trata los temas siguientes:

- **[Establecer el tamaño y el BoxView Color](#colorandsize)**  &ndash; establecer el `BoxView` propiedades.
- **[Decoración de texto de representación](#textdecorations)**  &ndash; utilizar un `BoxView` para las líneas de representación.
- **[Mostrar lista de colores con BoxView](#listingcolors)**  &ndash; mostrar todo el sistema de colores en un `ListView`.
- **[Reproducir el juego del ciclo de vida por subclases BoxView](#subclassing)**  &ndash; implementar un autómata celular famoso.
- **[Creación de un reloj Digital](#digitalclock)**  &ndash; simular una presentación de matriz de puntos.
- **[Creación de un reloj analógico](#analogclock)**  &ndash; transformar y animar `BoxView` elementos.

<a name="colorandsize" />

## <a name="setting-boxview-color-and-size"></a>Configuración BoxView Color y tamaño

Muy a menudo se configuran las tres propiedades siguientes de `BoxView`:

- [`Color`](https://developer.xamarin.com/api/property/Xamarin.Forms.BoxView.Color/) Para establecer su color.
- [`WidthRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/) Para establecer el ancho de la `BoxView` en unidades independientes del dispositivo.
- [`HeightRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) Para establecer el alto de la `BoxView`.

El `Color` propiedad es de tipo `Color`; la propiedad puede establecerse en cualquier `Color` valor, incluidos los 141 campos estáticos de solo lectura de denominan colores comprendido alfabéticamente `AliceBlue` a `YellowGreen`.

El `WidthRequest` y `HeightRequest` propiedades solo desempeñan una función si la `BoxView` es *sin restricciones* en diseño. Esto sucede cuando el contenedor de diseño necesita conocer el elemento secundario del tamaño, por ejemplo, cuando la `BoxView` es un elemento secundario de una celda de ajuste automático de tamaño de la `Grid` diseño. A `BoxView` también está restringida cuando su `HorizontalOptions` y `VerticalOptions` propiedades se establecen en valores distintos de `LayoutOptions.Fill`. Si el `BoxView` es sin restricciones, pero la `WidthRequest` y `HeightRequest` no se establecen propiedades, a continuación, el ancho o alto se establecen en valores predeterminados de 40 unidades o aproximadamente 1/4 pulgadas en dispositivos móviles.

El `WidthRequest` y `HeightRequest` propiedades se omiten si la `BoxView` es *limitada* en el diseño, en el que el contenedor de diseño de caso impone su propio tamaño en el `BoxView`.

Un `BoxView` puede restringida a una dimensión y sin restricciones en la otra. Por ejemplo, si la `BoxView` es un elemento secundario de un vertical `StackLayout`, la dimensión vertical de la `BoxView` es sin restricciones y su dimensión horizontal generalmente está restringido. Pero hay excepciones para esa dimensión horizontal: si la `BoxView` tiene su `HorizontalOptions` propiedad establecida en un valor distinto de `LayoutOptions.Fill`, entonces la dimensión horizontal también es sin restricciones. También es posible que el `StackLayout` para tener una dimensión horizontal sin restricciones, en cuyo caso el `BoxView` también será horizontalmente sin restricciones.

El [ **BasicBoxView** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView) ejemplo muestra un cuadrado de pulgada de uno no presentan restricciones `BoxView` en el centro de su página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:BasicBoxView"
             x:Class="BasicBoxView.MainPage">

    <BoxView Color="CornflowerBlue"
             WidthRequest="160"
             HeightRequest="160"
             VerticalOptions="Center"
             HorizontalOptions="Center" />

</ContentPage>
```

Este es el resultado:

[![Básico BoxView](boxview-images/basicboxview-small.png "BoxView básica")](boxview-images/basicboxview-large.png#lightbox "BasicBoxView")

Si el `VerticalOptions` y `HorizontalOptions` propiedades se quitan de la `BoxView` etiqueta o se establecen `Fill`, la `BoxView` deja de estar limitado por el tamaño de la página y se expande para rellenar la página.

A `BoxView` también puede ser un elemento secundario de un `AbsoluteLayout`. En ese caso, la ubicación y el tamaño de la `BoxView` se establecen utilizando el `LayoutBounds` enlazable propiedad adjunta. El `AbsoluteLayout` se describe en el artículo [ **AbsoluteLayout**](~/xamarin-forms/user-interface/layouts/absolute-layout.md).

Verá ejemplos de todos estos casos en los programas de ejemplo que siguen.

<a name="textdecorations" />

## <a name="rendering-text-decorations"></a>Representación de decoraciones de texto

Puede usar el `BoxView` para agregar algunos decoraciones simples en las páginas en forma de líneas horizontales y verticales. El [ **TextDecoration** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/TextDecoration) ejemplo se muestra cómo hacerlo. Todos los objetos visuales del programa se definen en el **MainPage.xaml** archivo, que contiene varios `Label` y `BoxView` elementos en el `StackLayout` se muestra aquí:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:TextDecoration"
             x:Class="TextDecoration.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="BoxView">
                <Setter Property="Color" Value="Black" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <ScrollView Margin="15">
        <StackLayout>

            ···

        </StackLayout>
    </ScrollView>
</ContentPage>
```

Todas las marcas que sigue de son elementos secundarios de la `StackLayout`. Esta marca consta de varios tipos de decorativo `BoxView` elementos que se usan con el `Label` elemento:

[![Decoración de texto](boxview-images/textdecoration-small.png "decoración de texto")](boxview-images/textdecoration-large.png#lightbox "decoración de texto")

El encabezado de gama alta en la parte superior de la página se logra con un `AbsoluteLayout` cuyos elementos secundarios son cuatro `BoxView` elementos y un `Label`, todos los de los cuales están asignados ubicaciones específicas y tamaños:

```xaml
<AbsoluteLayout>
    <BoxView AbsoluteLayout.LayoutBounds="0, 10, 200, 5" />
    <BoxView AbsoluteLayout.LayoutBounds="0, 20, 200, 5" />
    <BoxView AbsoluteLayout.LayoutBounds="10, 0, 5, 65" />
    <BoxView AbsoluteLayout.LayoutBounds="20, 0, 5, 65" />
    <Label Text="Stylish Header"
           FontSize="24"
           AbsoluteLayout.LayoutBounds="30, 25, AutoSize, AutoSize"/>
</AbsoluteLayout>
```

En el archivo XAML, el `AbsoluteLayout` va seguido de un `Label` con formato de texto que describe la `AbsoluteLayout`.

Se puede subrayar una cadena de texto, incluya tanto el `Label` y `BoxView` en un `StackLayout` que tiene su `HorizontalOptions` valor establecido en algo distinto de `Fill`. El ancho de la `StackLayout` , a continuación, se rige por el ancho de la `Label`, que luego impone ese ancho en el `BoxView`. El `BoxView` se asigna solo un alto explícito:

```xaml
<StackLayout HorizontalOptions="Center">
    <Label Text="Underlined Text"
           FontSize="24" />
    <BoxView HeightRequest="2" />
</StackLayout>
```

No se puede usar esta técnica para subrayar las palabras individuales dentro de las cadenas de texto más largas o un párrafo.

También es posible utilizar un `BoxView` similares a un elemento HTML `hr` elemento (regla horizontal). Simplemente dejar que el ancho de la `BoxView` determinarse por su contenedor primario, que en este caso es el `StackLayout`:

```xaml
<BoxView HeightRequest="3" />
```

Por último, puede dibujar una línea vertical en un lado de un párrafo de texto, incluya tanto el `BoxView` y `Label` en horizontal `StackLayout`. En este caso, el alto de la `BoxView` es igual que el alto de `StackLayout`, que se rige por el alto de la `Label`:

```xaml
<StackLayout Orientation="Horizontal">
    <BoxView WidthRequest="4"
             Margin="0, 0, 10, 0" />
    <Label>

        ···

    </Label>
/StackLayout>
```
<a name="listingcolors" />

## <a name="listing-colors-with-boxview"></a>Mostrar lista de colores con BoxView

El `BoxView` es conveniente para mostrar colores. Este programa utiliza una `ListView` para enumerar todos los estático de solo lectura campos públicos de la Xamarin.Forms `Color` estructura:

[![Colores de ListView](boxview-images/listviewcolors-small.png "ListView colores")](boxview-images/listviewcolors-large.png#lightbox "colores de ListView")

El [ **ListViewColors** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/ListViewColors/) programa incluye una clase denominada `NamedColor`. El constructor estático utiliza la reflexión para tener acceso a todos los campos de la `Color` estructurar y crear un `NamedColor` objeto para cada uno de ellos. Estos se almacenan en el método estático `All` propiedad:

```csharp
public class NamedColor
{
    // Instance members.
    private NamedColor()
    {
    }

    public string Name { private set; get; }

    public string FriendlyName { private set; get; }

    public Color Color { private set; get; }

    public string RgbDisplay { private set; get; }

    // Static members.
    static NamedColor()
    {
        List<NamedColor> all = new List<NamedColor>();
        StringBuilder stringBuilder = new StringBuilder();

        // Loop through the public static fields of the Color structure.
        foreach (FieldInfo fieldInfo in typeof(Color).GetRuntimeFields ())
        {
            if (fieldInfo.IsPublic &&
                fieldInfo.IsStatic &&
                fieldInfo.FieldType == typeof (Color))
            {
                // Convert the name to a friendly name.
                string name = fieldInfo.Name;
                stringBuilder.Clear();
                int index = 0;

                foreach (char ch in name)
                {
                    if (index != 0 && Char.IsUpper(ch))
                    {
                        stringBuilder.Append(' ');
                    }
                    stringBuilder.Append(ch);
                    index++;
                }

                // Instantiate a NamedColor object.
                Color color = (Color)fieldInfo.GetValue(null);

                NamedColor namedColor = new NamedColor
                {
                    Name = name,
                    FriendlyName = stringBuilder.ToString(),
                    Color = color,
                    RgbDisplay = String.Format("{0:X2}-{1:X2}-{2:X2}",
                                               (int)(255 * color.R),
                                               (int)(255 * color.G),
                                               (int)(255 * color.B))
                };

                // Add it to the collection.
                all.Add(namedColor);
            }
        }
        all.TrimExcess();
        All = all;
    }

    public static IList<NamedColor> All { private set; get; }
}
```

Los objetos visuales de programa se describen en el archivo XAML. El `ItemsSource` propiedad de la `ListView` se establece en el método estático `NamedColor.All` propiedad, lo que significa que la `ListView` muestra todos los individuales `NamedColor` objetos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ListViewColors"
             x:Class="ListViewColors.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="10, 20, 10, 0" />
            <On Platform="Android, UWP" Value="10, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <ListView SeparatorVisibility="None"
              ItemsSource="{x:Static local:NamedColor.All}">
        <ListView.RowHeight>
            <OnPlatform x:TypeArguments="x:Int32">
                <On Platform="iOS, Android" Value="80" />
                <On Platform="UWP" Value="90" />
            </OnPlatform>
        </ListView.RowHeight>

        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <ContentView Padding="5">
                        <Frame OutlineColor="Accent"
                               Padding="10">
                            <StackLayout Orientation="Horizontal">
                                <BoxView Color="{Binding Color}"
                                         WidthRequest="50"
                                         HeightRequest="50" />
                                <StackLayout>
                                    <Label Text="{Binding FriendlyName}"
                                           FontSize="22"
                                           VerticalOptions="StartAndExpand" />
                                    <Label Text="{Binding RgbDisplay, StringFormat='RGB = {0}'}"
                                           FontSize="16"
                                           VerticalOptions="CenterAndExpand" />
                                </StackLayout>
                            </StackLayout>
                        </Frame>
                    </ContentView>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

El `NamedColor` objetos se les ha aplicado el `ViewCell` objeto que se establece como la plantilla de datos de la `ListView`. Esta plantilla incluye una `BoxView` cuyo `Color` propiedad está enlazada a la `Color` propiedad de la `NamedColor` objeto.

<a name="subclassing" />

## <a name="playing-the-game-of-life-by-subclassing-boxview"></a>El juego del ciclo de vida por subclases BoxView

El juego del ciclo de vida es un autómata celular inventaron por matemático John Conway y utiliza habitualmente en las páginas de *científicos American* en la década de 1970. El artículo de Wikipedia proporciona una buena introducción [juego del ciclo de vida de Conway](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life).

El Xamarin.Forms [ **GameOfLife** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife/) programa define una clase denominada `LifeCell` que se deriva de `BoxView`. Esta clase encapsula la lógica de una celda individual en el juego del ciclo de vida:

```csharp
class LifeCell : BoxView
{
    bool isAlive;

    public event EventHandler Tapped;

    public LifeCell()
    {
        BackgroundColor = Color.White;

        TapGestureRecognizer tapGesture = new TapGestureRecognizer();
        tapGesture.Tapped += (sender, args) =>
        {
            Tapped?.Invoke(this, EventArgs.Empty);
        };
        GestureRecognizers.Add(tapGesture);
    }

    public int Col { set; get; }

    public int Row { set; get; }

    public bool IsAlive
    {
        set
        {
            if (isAlive != value)
            {
                isAlive = value;
                BackgroundColor = isAlive ? Color.Black : Color.White;
            }
        }
        get
        {
            return isAlive;
        }
    }
}
```

`LifeCell` agrega tres propiedades más `BoxView`: el `Col` y `Row` propiedades almacenan la posición de la celda dentro de la cuadrícula y el `IsAlive` propiedad indica su estado. El `IsAlive` propiedad también se establece la `Color` propiedad de la `BoxView` en negro si la celda está activo y en blanco si la celda no está activa.

`LifeCell` También se instala un `TapGestureRecognizer` para permitir al usuario alternar entre los Estados de celdas punteando en ellos. La clase traduce la `Tapped` eventos desde el reconocedor de gestos en su propio `Tapped` eventos.

El **GameOfLife** programa también incluye una `LifeGrid` clase que encapsula gran parte de la lógica de juego, y un `MainPage` clase que controla los objetos visuales del programa. Se trata de una superposición que describe las reglas del juego. Aquí es el programa de acción que muestra unos cientos `LifeCell` objetos en la página:

[![Juego del ciclo de vida](boxview-images/gameoflife-small.png "juego del ciclo de vida")](boxview-images/gameoflife-large.png#lightbox "juego del ciclo de vida")

<a name="digitalclock" />

## <a name="creating-a-digital-clock"></a>Creación de un reloj Digital

El [ **DotMatrixClock** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/DotMatrixClock/) programa crea 210 `BoxView` elementos para simular los puntos de una pantalla de 5 por 7 matriciales antigua. Puede leer el tiempo en modo vertical u horizontal, pero es mayor en horizontal:

[![Reloj matriciales](boxview-images/dotmatrixclock-small.png "reloj matriciales")](boxview-images/dotmatrixclock-large.png#lightbox "reloj matriciales")

El archivo XAML poco más que crear una instancia el `AbsoluteLayout` utilizado para el reloj:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DotMatrixClock"
             x:Class="DotMatrixClock.MainPage"
             Padding="10"
             SizeChanged="OnPageSizeChanged">

    <AbsoluteLayout x:Name="absoluteLayout"
                    VerticalOptions="Center" />
</ContentPage>
```

Todo lo demás aparece en el archivo de código subyacente. La lógica de presentación de matriz de puntos se simplifica en gran medida con la definición de varias matrices que describen los puntos correspondientes a cada uno de los 10 dígitos y dos puntos:

```csharp
public partial class MainPage : ContentPage
{
    // Total dots horizontally and vertically.
    const int horzDots = 41;
    const int vertDots = 7;

    // 5 x 7 dot matrix patterns for 0 through 9.
    static readonly int[, ,] numberPatterns = new int[10, 7, 5]
    {
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 1, 1}, { 1, 0, 1, 0, 1},
            { 1, 1, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 1, 0, 0}, { 0, 1, 1, 0, 0}, { 0, 0, 1, 0, 0}, { 0, 0, 1, 0, 0},
            { 0, 0, 1, 0, 0}, { 0, 0, 1, 0, 0}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0},
            { 0, 0, 1, 0, 0}, { 0, 1, 0, 0, 0}, { 1, 1, 1, 1, 1}
        },
        {
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 1, 0, 0}, { 0, 0, 0, 1, 0},
            { 0, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 0, 1, 0}, { 0, 0, 1, 1, 0}, { 0, 1, 0, 1, 0}, { 1, 0, 0, 1, 0},
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 0, 1, 0}
        },
        {
            { 1, 1, 1, 1, 1}, { 1, 0, 0, 0, 0}, { 1, 1, 1, 1, 0}, { 0, 0, 0, 0, 1},
            { 0, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 1, 1, 0}, { 0, 1, 0, 0, 0}, { 1, 0, 0, 0, 0}, { 1, 1, 1, 1, 0},
            { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 1, 0, 0},
            { 0, 1, 0, 0, 0}, { 0, 1, 0, 0, 0}, { 0, 1, 0, 0, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0},
            { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 1},
            { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0}, { 0, 1, 1, 0, 0}
        },
    };

    // Dot matrix pattern for a colon.
    static readonly int[,] colonPattern = new int[7, 2]
    {
        { 0, 0 }, { 1, 1 }, { 1, 1 }, { 0, 0 }, { 1, 1 }, { 1, 1 }, { 0, 0 }
    };

    // BoxView colors for on and off.
    static readonly Color colorOn = Color.Red;
    static readonly Color colorOff = new Color(0.5, 0.5, 0.5, 0.25);

    // Box views for 6 digits, 7 rows, 5 columns.
    BoxView[, ,] digitBoxViews = new BoxView[6, 7, 5];

    ···

}
```

Estos campos concluyan con una matriz tridimensional de `BoxView` elementos para almacenar los patrones de punto de los seis dígitos.

El constructor crea todos los la `BoxView` elementos de dígitos y dos puntos y también inicializa el `Color` propiedad de la `BoxView` elementos para los dos puntos:

```csharp
public partial class MainPage : ContentPage
{

    ···

    public MainPage()
    {
        InitializeComponent();

        // BoxView dot dimensions.
        double height = 0.85 / vertDots;
        double width = 0.85 / horzDots;

        // Create and assemble the BoxViews.
        double xIncrement = 1.0 / (horzDots - 1);
        double yIncrement = 1.0 / (vertDots - 1);
        double x = 0;

        for (int digit = 0; digit < 6; digit++)
        {
            for (int col = 0; col < 5; col++)
            {
                double y = 0;

                for (int row = 0; row < 7; row++)
                {
                    // Create the digit BoxView and add to layout.
                    BoxView boxView = new BoxView();
                    digitBoxViews[digit, row, col] = boxView;
                    absoluteLayout.Children.Add(boxView,
                                                new Rectangle(x, y, width, height),
                                                AbsoluteLayoutFlags.All);
                    y += yIncrement;
                }
                x += xIncrement;
            }
            x += xIncrement;

            // Colons between the hours, minutes, and seconds.
            if (digit == 1 || digit == 3)
            {
                int colon = digit / 2;

                for (int col = 0; col < 2; col++)
                {
                    double y = 0;

                    for (int row = 0; row < 7; row++)
                    {
                        // Create the BoxView and set the color.
                        BoxView boxView = new BoxView
                            {
                                Color = colonPattern[row, col] == 1 ?
                                            colorOn : colorOff
                            };
                        absoluteLayout.Children.Add(boxView,
                                                    new Rectangle(x, y, width, height),
                                                    AbsoluteLayoutFlags.All);
                        y += yIncrement;
                    }
                    x += xIncrement;
                }
                x += xIncrement;
            }
        }

        // Set the timer and initialize with a manual call.
        Device.StartTimer(TimeSpan.FromSeconds(1), OnTimer);
        OnTimer();
    }

    ···

}
```

Este programa utiliza la posición relativa y la característica de ajuste de tamaño de `AbsoluteLayout`. El ancho y alto de cada `BoxView` se establecen en valores fraccionarios, específicamente el 85% de 1 dividido por el número de puntos horizontales y verticales. Las posiciones también se establecen en los valores fraccionarios.

Dado que todas las posiciones y tamaños son en relación con el tamaño total de la `AbsoluteLayout`, el `SizeChanged` sólo necesita establecer el controlador de la página una `HeightRequest` de la `AbsoluteLayout`:

```csharp
public partial class MainPage : ContentPage
{

    ···

    void OnPageSizeChanged(object sender, EventArgs args)
    {
        // No chance a display will have an aspect ratio > 41:7
        absoluteLayout.HeightRequest = vertDots * Width / horzDots;
    }

    ···

}
```

El ancho de la `AbsoluteLayout` se establece automáticamente porque ajusta a todo el ancho de la página.

El código final de la `MainPage` clase procesa la devolución de llamada de temporizador y colores de los puntos de cada dígito. La definición de las matrices multidimensionales al principio del archivo de código subyacente facilita esta lógica de la parte más sencilla del programa:

```csharp
public partial class MainPage : ContentPage
{

    ···

    bool OnTimer()
    {
        DateTime dateTime = DateTime.Now;

        // Convert 24-hour clock to 12-hour clock.
        int hour = (dateTime.Hour + 11) % 12 + 1;

        // Set the dot colors for each digit separately.
        SetDotMatrix(0, hour / 10);
        SetDotMatrix(1, hour % 10);
        SetDotMatrix(2, dateTime.Minute / 10);
        SetDotMatrix(3, dateTime.Minute % 10);
        SetDotMatrix(4, dateTime.Second / 10);
        SetDotMatrix(5, dateTime.Second % 10);
        return true;
    }

    void SetDotMatrix(int index, int digit)
    {
        for (int row = 0; row < 7; row++)
            for (int col = 0; col < 5; col++)
            {
                bool isOn = numberPatterns[digit, row, col] == 1;
                Color color = isOn ? colorOn : colorOff;
                digitBoxViews[index, row, col].Color = color;
            }
    }
}
```
<a name="analogclock" />

## <a name="creating-an-analog-clock"></a>Creación de un reloj analógico

Puede parecer un reloj de matriz de puntos que sea una aplicación obvia de `BoxView`, pero `BoxView` elementos también son capaces de ser consciente de un reloj analógico:

[![Reloj de BoxView](boxview-images/boxviewclock-small.png "BoxView reloj")](boxview-images/boxviewclock-large.png#lightbox "BoxView reloj")

Todos los objetos visuales en el [ **BoxViewClock** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock/) programa son elementos secundarios de un `AbsoluteLayout`. Estos elementos tienen un tamaño mediante la `LayoutBounds` propiedad adjunta y girado usando el `Rotation` propiedad.

Los tres `BoxView` elementos para las manecillas del reloj se crea una instancia en el archivo XAML, pero no se coloca o tamaño:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:BoxViewClock"
             x:Class="BoxViewClock.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <AbsoluteLayout x:Name="absoluteLayout"
                    SizeChanged="OnAbsoluteLayoutSizeChanged">

        <BoxView x:Name="hourHand"
                 Color="Black" />

        <BoxView x:Name="minuteHand"
                 Color="Black" />

        <BoxView x:Name="secondHand"
                 Color="Black" />
    </AbsoluteLayout>
</ContentPage>
```

El constructor del archivo de código subyacente crea una instancia de la 60 `BoxView` elementos para las marcas de graduación alrededor de la circunferencia del reloj:

```csharp
public partial class MainPage : ContentPage
{

    ···

    BoxView[] tickMarks = new BoxView[60];

    public MainPage()
    {
        InitializeComponent();

        // Create the tick marks (to be sized and positioned later).
        for (int i = 0; i < tickMarks.Length; i++)
        {
            tickMarks[i] = new BoxView { Color = Color.Black };
            absoluteLayout.Children.Add(tickMarks[i]);
        }

        Device.StartTimer(TimeSpan.FromSeconds(1.0 / 60), OnTimerTick);
    }

    ···

}
```

El tamaño y la posición de todos los `BoxView` elementos se produce en el `SizeChanged` controlador para la `AbsoluteLayout`. Llama a un poco estructura interna de la clase `HandParams` describe el tamaño de cada una de las tres manos en relación con el tamaño total del reloj:

```csharp
public partial class MainPage : ContentPage
{
    // Structure for storing information about the three hands.
    struct HandParams
    {
        public HandParams(double width, double height, double offset) : this()
        {
            Width = width;
            Height = height;
            Offset = offset;
        }

        public double Width { private set; get; }   // fraction of radius
        public double Height { private set; get; }  // ditto
        public double Offset { private set; get; }  // relative to center pivot
    }

    static readonly HandParams secondParams = new HandParams(0.02, 1.1, 0.85);
    static readonly HandParams minuteParams = new HandParams(0.05, 0.8, 0.9);
    static readonly HandParams hourParams = new HandParams(0.125, 0.65, 0.9);

    ···

 }
```

El `SizeChanged` controlador determina el centro y el radio de la `AbsoluteLayout`y, a continuación, cambia el tamaño y los coloca los 60 `BoxView` elementos que se usan como las marcas de graduación. El `for` bucle finaliza estableciendo la `Rotation` propiedad de cada una de estas `BoxView` elementos. Al final de la `SizeChanged` controlador, el `LayoutHand` método se llama para cambiar el tamaño y la posición de las manecillas del reloj tres:

```csharp
public partial class MainPage : ContentPage
{

    ···

    void OnAbsoluteLayoutSizeChanged(object sender, EventArgs args)
    {
        // Get the center and radius of the AbsoluteLayout.
        Point center = new Point(absoluteLayout.Width / 2, absoluteLayout.Height / 2);
        double radius = 0.45 * Math.Min(absoluteLayout.Width, absoluteLayout.Height);

        // Position, size, and rotate the 60 tick marks.
        for (int index = 0; index < tickMarks.Length; index++)
        {
            double size = radius / (index % 5 == 0 ? 15 : 30);
            double radians = index * 2 * Math.PI / tickMarks.Length;
            double x = center.X + radius * Math.Sin(radians) - size / 2;
            double y = center.Y - radius * Math.Cos(radians) - size / 2;
            AbsoluteLayout.SetLayoutBounds(tickMarks[index], new Rectangle(x, y, size, size));
            tickMarks[index].Rotation = 180 * radians / Math.PI;
        }

        // Position and size the three hands.
        LayoutHand(secondHand, secondParams, center, radius);
        LayoutHand(minuteHand, minuteParams, center, radius);
        LayoutHand(hourHand, hourParams, center, radius);
    }

    void LayoutHand(BoxView boxView, HandParams handParams, Point center, double radius)
    {
        double width = handParams.Width * radius;
        double height = handParams.Height * radius;
        double offset = handParams.Offset;

        AbsoluteLayout.SetLayoutBounds(boxView,
            new Rectangle(center.X - 0.5 * width,
                          center.Y - offset * height,
                          width, height));

        // Set the AnchorY property for rotations.
        boxView.AnchorY = handParams.Offset;
    }

    ···

}
```

El `LayoutHand` método tamaños y coloca cada manecilla para señalar directamente hasta la posición de 12:00. Al final del método, el `AnchorY` propiedad se establece en la posición correspondiente al centro del reloj. Esto indica que el centro de rotación.

Las manos se giran en la función de devolución de llamada de temporizador:

```csharp
public partial class MainPage : ContentPage
{

    ···

    bool OnTimerTick()
    {
        // Set rotation angles for hour and minute hands.
        DateTime dateTime = DateTime.Now;
        hourHand.Rotation = 30 * (dateTime.Hour % 12) + 0.5 * dateTime.Minute;
        minuteHand.Rotation = 6 * dateTime.Minute + 0.1 * dateTime.Second;

        // Do an animation for the second hand.
        double t = dateTime.Millisecond / 1000.0;

        if (t < 0.5)
        {
            t = 0.5 * Easing.SpringIn.Ease(t / 0.5);
        }
        else
        {
            t = 0.5 * (1 + Easing.SpringOut.Ease((t - 0.5) / 0.5));
        }

        secondHand.Rotation = 6 * (dateTime.Second + t);
        return true;
    }
}
```

La manecilla de los segundos se tratan de forma ligeramente diferente: se aplica una animación a la función de aceleración para hacer que el movimiento parezca mecánico en lugar de smooth. En cada ciclo, la manecilla de los segundos atrás extrae un poco y, a continuación, se insertan su destino. Este poco de código agrega mucho al realismo del movimiento.

## <a name="conclusion"></a>Conclusión

La `BoxView` puede parecer simple en principio, pero a medida ha visto, puede ser bastante versátil y pueden reproducir casi objetos visuales que normalmente sólo son posibles gráficos vectoriales. Para los gráficos más sofisticados, consulte [SkiaSharp uso de Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md).


## <a name="related-links"></a>Vínculos relacionados

- [BoxView básica (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView)
- [Decoración de texto (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/TextDecoration)
- [Cuadro de lista de color (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/ColorListBox)
- [Juego del ciclo de vida (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife)
- [Matriz de puntos de reloj (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/DotMatrixClock)
- [Reloj BoxView (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock)
- [BoxView](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/)
