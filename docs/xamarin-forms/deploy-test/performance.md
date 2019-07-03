---
title: Rendimiento de Xamarin.Forms
description: Existen muchas técnicas para aumentar el rendimiento de aplicaciones de Xamarin.Forms. En conjunto, estas técnicas pueden reducir considerablemente la cantidad de trabajo que está realizando una CPU y la cantidad de memoria consumida por una aplicación. En este artículo se describen y se explican estas técnicas.
ms.prod: xamarin
ms.assetid: 0be84c56-6698-448d-be5a-b4205f1caa9f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: b445c1f8d3d440ecf609d5f3c1b7cc7147343fe0
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/20/2019
ms.locfileid: "67268609"
---
# <a name="xamarinforms-performance"></a>Rendimiento de Xamarin.Forms

_Existen muchas técnicas para aumentar el rendimiento de aplicaciones de Xamarin.Forms. En conjunto, estas técnicas pueden reducir considerablemente la cantidad de trabajo que está realizando una CPU y la cantidad de memoria consumida por una aplicación. En este artículo se describen y se explican estas técnicas._

> [!VIDEO https://youtube.com/embed/RZvdql3Ev0E]

**Evolve 2016: Optimizar el rendimiento de las aplicaciones con Xamarin.Forms**

## <a name="overview"></a>Información general

El mal rendimiento de una aplicación se manifiesta de muchas formas. Puede hacer que parezca que una aplicación deja de responder, puede ocasionar un desplazamiento lento y puede reducir la duración de la batería. La optimización del rendimiento conlleva mucho más que la mera implementación de código eficaz. También debe tenerse en cuenta la experiencia de rendimiento de la aplicación del usuario. Por ejemplo, asegurarse de que las operaciones se ejecuten sin evitar que el usuario realice otras actividades puede ayudar a mejorar su experiencia.

Existen varias técnicas para aumentar el rendimiento, y la percepción de rendimiento, de una aplicación de Xamarin.Forms. Son los siguientes:

- [Habilitar el compilador XAML](#xamlc)
- [Elegir el diseño correcto](#correctlayout)
- [Habilitar la compresión de diseño](#layoutcompression)
- [Usar representadores rápidos](#fastrenderers)
- [Reducir enlaces innecesarios](#databinding)
- [Optimizar el rendimiento de diseño](#optimizelayout)
- [Optimizar el rendimiento de ListView](#optimizelistview)
- [Optimizar los recursos de imagen](#optimizeimages)
- [Reducir el tamaño del árbol visual](#visualtree)
- [Reducir el tamaño del diccionario de recursos de aplicación](#resourcedictionary)
- [Usar el patrón de representador personalizado](#rendererpattern)

> [!NOTE]
>  Antes de leer este artículo, debería leer [Rendimiento multiplataforma](~/cross-platform/deploy-test/memory-perf-best-practices.md), donde se explican técnicas no específicas de una plataforma para mejorar el uso de memoria y el rendimiento de las aplicaciones compiladas con la plataforma Xamarin.

<a name="xamlc" />

## <a name="enable-the-xaml-compiler"></a>Habilitar el compilador XAML

XAML se puede compilar de forma opcional directamente en lenguaje intermedio (IL) con el compilador XAML (XAMLC). XAMLC ofrece una serie de ventajas:

- Realiza la comprobación en tiempo de compilación de XAML, notificando al usuario de los errores.
- Reduce parte del tiempo de carga y creación de instancias para los elementos XAML.
- Facilita reducir el tamaño de archivo del ensamblado final al dejar de incluir archivos .xaml.

XAMLC está deshabilitado de forma predeterminada para garantizar la compatibilidad con versiones anteriores. Pero se puede habilitar en el nivel de ensamblado y el nivel de clase. Para más información, vea [Compilación de XAML](~/xamarin-forms/xaml/xamlc.md).

<a name="correctlayout" />

## <a name="choose-the-correct-layout"></a>Elegir el diseño correcto

Un diseño capaz de mostrar varios elementos secundarios, pero que solo tiene un elemento secundario, es poco rentable. Por ejemplo, el siguiente ejemplo de código muestra un [`StackLayout`](xref:Xamarin.Forms.StackLayout) con un único elemento secundario:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <ContentPage.Content>
        <StackLayout>
            <Image Source="waterfront.jpg" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Es un desperdicio y el elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) debería eliminarse, como se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <ContentPage.Content>
        <Image Source="waterfront.jpg" />
    </ContentPage.Content>
</ContentPage>
```

Además, no intente reproducir el aspecto de un diseño específico mediante combinaciones de otros diseños, dado que como resultado se realizarían cálculos de diseño innecesarios. Por ejemplo, no intente reproducir un diseño [`Grid`](xref:Xamarin.Forms.Grid) mediante una combinación de instancias [`StackLayout`](xref:Xamarin.Forms.StackLayout). El ejemplo de código siguiente muestra un ejemplo de esta práctica incorrecta:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
    <ContentPage.Content>
        <StackLayout>
            <StackLayout Orientation="Horizontal">
                <Label Text="Name:" />
                <Entry Placeholder="Enter your name" />
            </StackLayout>
            <StackLayout Orientation="Horizontal">
                <Label Text="Age:" />
                <Entry Placeholder="Enter your age" />
            </StackLayout>
            <StackLayout Orientation="Horizontal">
                <Label Text="Occupation:" />
                <Entry Placeholder="Enter your occupation" />
            </StackLayout>
            <StackLayout Orientation="Horizontal">
                <Label Text="Address:" />
                <Entry Placeholder="Enter your address" />
            </StackLayout>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Es una pérdida de tiempo porque se realizan cálculos de diseño innecesarios. En su lugar, el diseño deseado puede lograrse mejor mediante un [`Grid`](xref:Xamarin.Forms.Grid), como se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
    <ContentPage.Content>
        <Grid>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="100" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>
            <Grid.RowDefinitions>
                <RowDefinition Height="30" />
                <RowDefinition Height="30" />
                <RowDefinition Height="30" />
                <RowDefinition Height="30" />
            </Grid.RowDefinitions>
            <Label Text="Name:" />
            <Entry Grid.Column="1" Placeholder="Enter your name" />
            <Label Grid.Row="1" Text="Age:" />
            <Entry Grid.Row="1" Grid.Column="1" Placeholder="Enter your age" />
            <Label Grid.Row="2" Text="Occupation:" />
            <Entry Grid.Row="2" Grid.Column="1" Placeholder="Enter your occupation" />
            <Label Grid.Row="3" Text="Address:" />
            <Entry Grid.Row="3" Grid.Column="1" Placeholder="Enter your address" />
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

<a name="layoutcompression" />

## <a name="enable-layout-compression"></a>Habilitar la compresión de diseño

La compresión de diseño quita diseños especificados del árbol visual, en un intento de mejorar el rendimiento de la representación de la página. La ventaja de rendimiento que esto ofrece varía según la complejidad de una página, la versión del sistema operativo que se va a usar y el dispositivo en el que se ejecuta la aplicación. Sin embargo, las mejoras de rendimiento más importantes se apreciarán en los dispositivos más antiguos. Para obtener más información, vea [Layout Compression](~/xamarin-forms/user-interface/layouts/layout-compression.md) (Compresión de diseño).

<a name="fastrenderers" />

## <a name="use-fast-renderers"></a>Usar representadores rápidos

Los representadores rápidos reducen la inflación y los costos de representación de controles de Xamarin.Forms en Android mediante la reducción de la jerarquía de control nativo resultante. Además, esto mejora el rendimiento porque crea menos objetos, lo cual a su vez genera un árbol visual menos complejo y menos uso de memoria. Para obtener más información, consulte [Fast Renderers](~/xamarin-forms/internals/fast-renderers.md) (Representadores rápidos).

<a name="databinding" />

## <a name="reduce-unnecessary-bindings"></a>Reducir enlaces innecesarios

No use enlaces para el contenido que se puede establecer fácilmente de forma estática. No hay ninguna ventaja en enlazar datos que no necesitan ser enlazados, ya que los enlaces no son rentables. Por ejemplo, establecer `Button.Text = "Accept"` tiene una menor sobrecarga que enlazar [`Button.Text`](xref:Xamarin.Forms.Button.Text) a una propiedad `string` de ViewModel propiedad con valor "Accept".

<a name="optimizelayout" />

## <a name="optimize-layout-performance"></a>Optimizar el rendimiento del diseño

Xamarin.Forms 2 presentó un motor de diseño optimizado que afecta a las actualizaciones de diseño. Para obtener el mejor rendimiento posible, siga estas instrucciones:

- Reduzca la profundidad de las jerarquías de diseño especificando valores de propiedad [`Margin`](xref:Xamarin.Forms.View.Margin), lo que permite la creación de diseños con menos vistas ajustadas. Para más información, vea [Márgenes y relleno](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).
- Cuando se usa un [`Grid`](xref:Xamarin.Forms.Grid), intente asegurarse de establecer en tamaño [`Auto`](xref:Xamarin.Forms.GridLength.Auto) el menor número posible de filas y columnas. Cada fila o columna de tamaño automático hará que el motor de diseño tenga que realizar cálculos de diseño adicionales. En su lugar, use filas y columnas de tamaño fijo si es posible. Como alternativa, establezca las filas y columnas para ocupar una cantidad proporcional de espacio con el valor de enumeración [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star), siempre que el árbol primario siga estas directrices de diseño.
- No establezca las propiedades [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) y [`HorizontalOptions`](xref:Xamarin.Forms.View.VerticalOptions) de un diseño a menos que sea necesario. Los valores predeterminados de [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) y [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) permiten la optimización de diseño óptima. Cambiar estas propiedades tiene un costo y consume memoria, incluso cuando se establecen en los valores predeterminados.
- Evite el uso de un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) siempre que sea posible. Como resultado, la CPU tendrá que realizar mucho más trabajo.
- Cuando se usa un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout), evite el uso de la propiedad [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) siempre que sea posible.
- Cuando se usa un [`StackLayout`](xref:Xamarin.Forms.StackLayout), asegúrese de que solo un elemento secundario se establece en [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands). Esta propiedad garantiza que el elemento secundario especificado ocupa el mayor espacio que el `StackLayout` puede asignarle y es poco rentable realizar estos cálculos más de una vez.
- No llame a ninguno de los métodos de la clase [`Layout`](xref:Xamarin.Forms.Layout), dado que provocan que se realicen cálculos de diseño costosos. En su lugar, es probable que se pueda obtener el comportamiento de diseño deseado estableciendo las propiedades [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) y [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY). Como alternativa, cree una subclase de la clase [`Layout<View>`](xref:Xamarin.Forms.Layout`1) para lograr el comportamiento de diseño deseado.
- No actualice ninguna instancia [`Label`](xref:Xamarin.Forms.Label) con más frecuencia de la necesaria, dado que el cambio de tamaño de la etiqueta puede producir que se vuelva a calcular la pantalla completa.
- No establezca la propiedad [`Label.VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) a menos sea necesario.
- Establezca el [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) de las instancias [`Label`](xref:Xamarin.Forms.Label) en [`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap) siempre que sea posible.

<a name="optimizelistview" />

## <a name="optimize-listview-performance"></a>Optimizar el rendimiento de ListView

Cuando se usa un control [`ListView`](xref:Xamarin.Forms.ListView) hay una serie de experiencias del usuario que deben optimizarse:

- **Inicialización**: el intervalo de tiempo que comienza cuando se crea el control y que termina cuando los elementos se muestran en pantalla.
- **Desplazamiento**: la capacidad de desplazarse por la lista y garantizar que la interfaz de usuario no se retrasa con los gestos de toque.
- **Interacción** para agregar, eliminar y seleccionar elementos.

El control [`ListView`](xref:Xamarin.Forms.ListView) requiere una aplicación para proporcionar datos y plantillas de celda. La forma de conseguirlo tendrá un gran impacto en el rendimiento del control. Para más información, vea [Rendimiento de ListView](~/xamarin-forms/user-interface/listview/performance.md).

<a name="optimizeimages" />

## <a name="optimize-image-resources"></a>Optimizar los recursos de imagen

Mostrar recursos de imagen puede aumentar considerablemente la superficie de memoria de la aplicación. Por tanto, solo se deberían crear cuando fuera necesario y deberían liberarse en cuanto la aplicación no los necesitara. Por ejemplo, si una aplicación muestra una imagen mediante la lectura de sus datos desde una secuencia, asegúrese de que esa secuencia se crea solo cuando sea necesario y que se libera cuando ya no es necesaria. Esto se consigue mediante la creación de la secuencia cuando se crea la página, o cuando se desencadena el evento [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) y, después, mediante la eliminación de la secuencia cuando se desencadena el evento [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing).

Al descargar una imagen para mostrar con el método [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)), guarde en la memoria caché la imagen descargada asegurándose de que la propiedad [`UriImageSource.CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled) está establecida en `true`. Para más información, vea [Trabajar con imágenes](~/xamarin-forms/user-interface/images.md).

Para más información, vea [Optimizar los recursos de imagen](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages).

<a name="visualtree" />

## <a name="reduce-the-visual-tree-size"></a>Reducir el tamaño del árbol visual

Reducir el número de elementos en una página hará que la página se procese más rápido. Hay dos técnicas principales para conseguir esto. La primera es ocultar los elementos que no están visibles. La propiedad [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) de cada elemento determina si el elemento debe ser parte del árbol visual o no. Por tanto, si un elemento no es visible porque está oculto detrás de otros elementos, quite el elemento o establezca su propiedad `IsVisible` en `false`.

La segunda técnica es quitar los elementos innecesarios. Por ejemplo, en el ejemplo de código siguiente se muestra un diseño de página que muestra una serie de elementos [`Label`](xref:Xamarin.Forms.Label):

```xaml
<ContentPage.Content>
    <StackLayout>
        <StackLayout Padding="20,20,0,0">
            <Label Text="Hello" />
        </StackLayout>
        <StackLayout Padding="20,20,0,0">
            <Label Text="Welcome to the App!" />
        </StackLayout>
        <StackLayout Padding="20,20,0,0">
            <Label Text="Downloading Data..." />
        </StackLayout>
    </StackLayout>
</ContentPage.Content>
```

Se puede mantener el mismo diseño de página con un número reducido de elementos, como se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage.Content>
  <StackLayout Padding="20,20,0,0" Spacing="25">
    <Label Text="Hello" />
    <Label Text="Welcome to the App!" />
    <Label Text="Downloading Data..." />
  </StackLayout>
</ContentPage.Content>
```

<a name="resourcedictionary" />

## <a name="reduce-the-application-resource-dictionary-size"></a>Reducir el tamaño del diccionario de recursos de aplicación

Todos los recursos que se usan en la aplicación se deben almacenar en el diccionario de recursos de la aplicación para evitar la duplicación. Esto facilita reducir la cantidad de código XAML que tiene que analizarse en la aplicación. El siguiente ejemplo de código muestra el recurso `HeadingLabelStyle`, que se usa en toda la aplicación, por lo que se define en el diccionario de recursos de la aplicación:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Resources.App">
     <Application.Resources>
         <ResourceDictionary>
            <Style x:Key="HeadingLabelStyle" TargetType="Label">
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="TextColor" Value="Red" />
            </Style>
         </ResourceDictionary>
     </Application.Resources>
</Application>
```

Pero el código de XAML que es específico de una página no debería incluirse en el diccionario de recursos de la aplicación, dado que los recursos se analizarán en el inicio de la aplicación en lugar de cuando los solicite una página. Si una página que no es la página de inicio usa un recurso, se debe colocar en el diccionario de recursos para esa página, para así reducir el código de XAML que se analiza cuando se inicia la aplicación. El siguiente ejemplo de código muestra el recurso `HeadingLabelStyle`, que solo se usa en una página, por lo que se define en el diccionario de recursos de la página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Test.HomePage"
             Padding="0,20,0,0">
     <ContentPage.Resources>
          <ResourceDictionary>
            <Style x:Key="HeadingLabelStyle" TargetType="Label">
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="TextColor" Value="Red" />
            </Style>
        </ResourceDictionary>
     </ContentPage.Resources>
    <ContentPage.Content>
      ...
    </ContentPage.Content>
</ContentPage>

```

Para más información sobre los dominios de aplicación, vea [`Working with Styles`](~/xamarin-forms/user-interface/styles/index.md).

<a name="rendererpattern" />

## <a name="use-the-custom-renderer-pattern"></a>Usar el patrón de presentador personalizado

La mayoría de las clases de representador exponen el método `OnElementChanged`, que se llama cuando se crea un control personalizado de Xamarin.Forms para representar el control nativo correspondiente. Las clases de representador personalizadas, en cada clase de representador específica de la plataforma, invalidan después este método para crear instancias y personalizar el control nativo. El método `SetNativeControl` se usa para crear una instancia del control nativo y este método también asignará la referencia de control a la propiedad `Control`.

Aun así, en algunos casos se puede llamar al método `OnElementChanged` varias veces. Por lo tanto, para evitar pérdidas de memoria que pueden afectar al rendimiento, debe tener cuidado al crear una instancia de un nuevo control nativo. El enfoque que usar al crear instancias de un nuevo control nativo en un presentador personalizado se muestra en el ejemplo de código siguiente:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    if (Control == null) {
      // Instantiate the native control
    }
    // Configure the control and subscribe to event handlers
  }
}
```

Solo se debe crear una instancia de un nuevo control nativo una vez, cuando la propiedad `Control` es `null`. Además, solo se debe crear, configurar el control y suscribir los controladores de eventos cuando se adjunta el presentador personalizado a un nuevo elemento de Xamarin.Forms. De forma similar, solo se debe cancelar la suscripción de los controladores de eventos que se han suscrito cuando cambia el elemento al que está asociado el presentador. Adoptar este enfoque facilita crear un presentador personalizado de rendimiento eficaz que no sufra pérdidas de memoria.

> [!IMPORTANT]
> El método `SetNativeControl` solo se debe llamar si `e.NewElement` no es `null`.

Para más información sobre presentadores personalizados, vea [Personalización de controles en cada plataforma](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="summary"></a>Resumen

En este artículo se han descrito y explicado técnicas para aumentar el rendimiento de las aplicaciones de Xamarin.Forms. En conjunto, estas técnicas pueden reducir considerablemente la cantidad de trabajo que está realizando una CPU y la cantidad de memoria consumida por una aplicación.


## <a name="related-links"></a>Vínculos relacionados

- [Rendimiento multiplataforma](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Rendimiento de ListView](~/xamarin-forms/user-interface/listview/performance.md)
- [Representadores rápidos](~/xamarin-forms/internals/fast-renderers.md)
- [Compresión de diseño](~/xamarin-forms/user-interface/layouts/layout-compression.md)
- [XamlCompilation](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)
- [XamlCompilationOptions](xref:Xamarin.Forms.Xaml.XamlCompilationOptions)
