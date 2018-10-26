---
title: Xamarin.Forms se compilan los enlaces
description: En este artículo se explica cómo usar enlaces compilados para mejorar el rendimiento de enlace de datos en las aplicaciones de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: ABE6B7F7-875E-4402-A1D2-845CE374402B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2018
ms.openlocfilehash: 0b350082c834076a1d69427644259087d64bf26a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111702"
---
# <a name="xamarinforms-compiled-bindings"></a>Xamarin.Forms se compilan los enlaces

_Enlaces compilados se resuelven más rápidamente que los enlaces clásicos, lo que mejora el rendimiento de enlace de datos en las aplicaciones de Xamarin.Forms._

Enlaces de datos tienen dos problemas principales:

1. No hay ninguna validación en tiempo de compilación de expresiones de enlace. En su lugar, los enlaces se resuelven en tiempo de ejecución. Por lo tanto, todos los enlaces no válidos no se detectan hasta en tiempo de ejecución cuando la aplicación no se comporta según lo esperado o aparecen mensajes de error.
1. No son más rentable. Los enlaces se resuelven en tiempo de ejecución mediante la inspección de objetos de uso general (reflejo) y la sobrecarga de hacerlo varía en función de la plataforma.

Enlaces compilados mejoran el rendimiento de enlace de datos en las aplicaciones de Xamarin.Forms mediante la resolución de expresiones de enlace en tiempo de compilación en lugar de en tiempo de ejecución. Además, esta validación en tiempo de compilación de expresiones de enlace permite un mejor experiencia de solución de problemas porque los enlaces no válidos se notifican como errores de compilación de desarrollador.

Es el proceso para usar enlaces compilados:

1. Habilitar la compilación de XAML. Para obtener más información sobre la compilación de XAML, vea [compilación XAML](~/xamarin-forms/xaml/xamlc.md).
1. Establecer un `x:DataType` atributo un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) para el tipo del objeto que el `VisualElement` y sus elementos secundarios se enlazará. Tenga en cuenta que este atributo se vuelva a definir en cualquier ubicación en una jerarquía de vistas.

> [!NOTE]
> Se recomienda para establecer el `x:DataType` atributo del mismo nivel en la jerarquía de vistas como la [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) está establecido.

En tiempo de compilación XAML, las expresiones de enlace no válido se notificarán como errores de compilación. Sin embargo, el compilador XAML solo notificará un error de compilación para la primera expresión de enlace no válido que encuentra. Las expresiones de enlace válido que se definen en el `VisualElement` o sus elementos secundarios será compilado, independientemente de si el [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) está establecido en XAML o código. Compilar una expresión de enlace genera un código compilado que se obtendrá un valor de una propiedad en el *origen*y establézcalo en la propiedad en el *destino* que se especifica en el marcado. Además, dependiendo de la expresión de enlace, el código generado puede observar los cambios en el valor de la *origen* propiedad y actualizar el *destino* propiedad y se puede insertar los cambios desde el *destino* a la *origen*.

> [!IMPORTANT]
> Enlaces compilados actualmente están deshabilitados para las expresiones de enlace que definen el [ `Source` ](xref:Xamarin.Forms.Binding.Source) propiedad. Esto es porque el `Source` propiedad siempre se establece mediante la `x:Reference` extensión de marcado, que no se puede resolver en tiempo de compilación.

## <a name="using-compiled-bindings"></a>Uso de enlaces compilados

El **compilado Selector de colores** página se muestra el uso compilados enlaces entre las vistas de Xamarin.Forms y las propiedades de ViewModel:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.CompiledColorSelectorPage"
             Title="Compiled Color Selector">
    ...
    <StackLayout x:DataType="local:HslColorViewModel">
        <StackLayout.BindingContext>
            <local:HslColorViewModel Color="Sienna" />
        </StackLayout.BindingContext>
        <BoxView Color="{Binding Color}"
                 ... />
        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />
            <Slider Value="{Binding Hue}" />
            <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />
            <Slider Value="{Binding Saturation}" />
            <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />
            <Slider Value="{Binding Luminosity}" />
            <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
        </StackLayout>
    </StackLayout>    
</ContentPage>
```

La raíz [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) crea una instancia de la `HslColorViewModel` e inicializa el `Color` propiedad dentro de las etiquetas de elemento de propiedad para el [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) propiedad. Esta raíz `StackLayout` también define la `x:DataType` atributo como el tipo de modelo de vista, lo que indica que las expresiones de enlace en la raíz `StackLayout` jerarquía de vistas que se va a compilar. Esto se puede comprobar si cambia cualquiera de las expresiones de enlace para enlazar a una propiedad ViewModel inexistente, lo que dará lugar a un error de compilación.

> [!IMPORTANT]
> El `x:DataType` se puede volver a definido en cualquier momento en una jerarquía de vistas de atributo.

El [ `BoxView` ](xref:Xamarin.Forms.BoxView), [ `Label` ](xref:Xamarin.Forms.Label) elementos, y [ `Slider` ](xref:Xamarin.Forms.Slider) vistas heredan el contexto de enlace desde el [ `StackLayout` ](xref:Xamarin.Forms.StackLayout). Estas vistas son todos los destinos de enlace que hacen referencia a las propiedades de origen en el modelo de vista. Para el [ `BoxView.Color` ](xref:Xamarin.Forms.BoxView.Color) propiedad y el [ `Label.Text` ](xref:Xamarin.Forms.Label.Text) propiedad, los enlaces de datos son `OneWay` : se establecen las propiedades de la vista de las propiedades en ViewModel. Sin embargo, el [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) propiedad usa un `TwoWay` enlace. Esto permite que cada `Slider` debe establecerse de ViewModel y también del modelo de vista debe establecerse de cada uno `Slider`.

Cuando la aplicación se ejecuta primero, el [ `BoxView` ](xref:Xamarin.Forms.BoxView), [ `Label` ](xref:Xamarin.Forms.Label) elementos, y [ `Slider` ](xref:Xamarin.Forms.Slider) elementos están establecidos de la clase ViewModel basada en inicial `Color` propiedad establecida cuando se crea una instancia de ViewModel. Esto se muestra en las capturas de pantalla siguiente:

[![Compilar Selector de colores](compiled-bindings-images/compiledcolorselector-small.png "compilado Selector de colores")](compiled-bindings-images/compiledcolorselector-large.png#lightbox "compilado Selector de colores")

Como se manipulan los controles deslizantes, el [ `BoxView` ](xref:Xamarin.Forms.BoxView) y [ `Label` ](xref:Xamarin.Forms.Label) elementos se actualizan en consecuencia.

Para obtener más información acerca de este selector de colores, consulte [ViewModels y notificaciones de cambio de propiedad](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#viewmodels-and-property-change-notifications).

## <a name="using-compiled-bindings-in-a-datatemplate"></a>Uso de enlaces compilados en una DataTemplate

Los enlaces en un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) se interpretan en el contexto del objeto con plantilla. Por lo tanto, cuando utilice compila los enlaces en un `DataTemplate`, `DataTemplate` debe declarar el tipo de su objeto de datos mediante el `x:DataType` atributo.

El **compila la lista de colores** página se muestra el uso de enlaces compilados en un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.CompiledColorListPage"
             Title="Compiled Color List">
    <Grid>
        ...
        <ListView x:Name="colorListView"
                  ItemsSource="{x:Static local:NamedColor.All}"
                  ... >
            <ListView.ItemTemplate>
                <DataTemplate x:DataType="local:NamedColor">
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <BoxView Color="{Binding Color}"
                                     ... />
                            <Label Text="{Binding FriendlyName}"
                                   ... />
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
        <!-- The BoxView doesn't use compiled bindings -->
        <BoxView Color="{Binding Source={x:Reference colorListView}, Path=SelectedItem.Color}"
                 ... />
    </Grid>
</ContentPage>
```

El [ `ListView.ItemsSource` ](xref:Xamarin.Forms.ListView) propiedad está establecida en estático `NamedColor.All` propiedad. El `NamedColor` clase usa la reflexión de .NET para enumerar todos los campos públicos estáticos en la [ `Color` ](xref:Xamarin.Forms.Color) estructura y almacenarlas con sus nombres en una colección que es accesible desde estático `All` propiedad. Por lo tanto, el `ListView` se rellena con todos los `NamedColor` instancias. Para cada elemento de la `ListView`, el contexto de enlace para el elemento está establecido en un `NamedColor` objeto. El [ `BoxView` ](xref:Xamarin.Forms.BoxView) y [ `Label` ](xref:Xamarin.Forms.Label) elementos en el [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) están enlazados a `NamedColor` propiedades.

Tenga en cuenta que el [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) define la `x:DataType` atributo sea el `NamedColor` tipo, que indica que los en las expresiones de enlace el `DataTemplate` jerarquía de vistas que se va a compilar. Esto se puede comprobar si se cambia cualquiera de las expresiones de enlace para enlazar a un inexistente `NamedColor` propiedad, que se producirá un error de compilación.

Cuando la aplicación se ejecuta primero, el [ `ListView` ](xref:Xamarin.Forms.ListView) se rellena con `NamedColor` instancias. Cuando un elemento de la `ListView` está activada, el [ `BoxView.Color` ](xref:Xamarin.Forms.BoxView.Color) propiedad se establece en el color del elemento seleccionado en el `ListView`:

[![Compila la lista de colores](compiled-bindings-images/compiledcolorlist-small.png "compilado Color lista]")](compiled-bindings-images/compiledcolorlist-large.png#lightbox "Compiled Color List")

Seleccionar otros elementos de la [ `ListView` ](xref:Xamarin.Forms.BoxView) actualiza el color de la [ `BoxView` ](xref:Xamarin.Forms.BoxView).

## <a name="combining-compiled-bindings-with-classic-bindings"></a>Enlaces de combinación que se compilan con enlaces clásicos

Las expresiones de enlace solo se compilan para la jerarquía de vistas que el `x:DataType` atributo se define en. Por el contrario, todas las vistas en una jerarquía en el que el `x:DataType` no está definido el atributo usará enlaces clásicos. Por lo tanto, es posible combinar compilados enlaces y enlaces clásicos en una página. Por ejemplo, en la anterior sección las vistas dentro de la [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) utilizan enlaces compilados, mientras que el [ `BoxView` ](xref:Xamarin.Forms.BoxView) que se establece en el color seleccionado en el [ `ListView` ](xref:Xamarin.Forms.ListView) no lo hace.

Estructurar una cuidadosa de `x:DataType` atributos, por tanto, pueden provocar una página mediante enlaces compilados y clásicos. Como alternativa, el `x:DataType` atributo se puede volver a definido en cualquier momento en una jerarquía de vistas para `null` utilizando el `x:Null` extensión de marcado. Esto indica que las expresiones de enlace dentro de la jerarquía de vistas utilizará enlaces clásicos. El *enlaces mixto* página muestra este enfoque:

```xaml
<StackLayout x:DataType="local:HslColorViewModel">
    <StackLayout.BindingContext>
        <local:HslColorViewModel Color="Sienna" />
    </StackLayout.BindingContext>
    <BoxView Color="{Binding Color}"
             VerticalOptions="FillAndExpand" />
    <StackLayout x:DataType="{x:Null}"
                 Margin="10, 0">
        <Label Text="{Binding Name}" />
        <Slider Value="{Binding Hue}" />
        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />
        <Slider Value="{Binding Saturation}" />
        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />
        <Slider Value="{Binding Luminosity}" />
        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
    </StackLayout>
</StackLayout>   
```

La raíz [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) establece la `x:DataType` atributo sea el `HslColorViewModel` tipo, que indica que cualquier expresión de enlace en la raíz `StackLayout` jerarquía de vistas que se va a compilar. Sin embargo, interno `StackLayout` redefine el `x:DataType` atributo `null` con el `x:Null` expresión marcado. Por lo tanto, las expresiones de enlace de interno `StackLayout` usan enlaces clásicos. Solo el [ `BoxView` ](xref:Xamarin.Forms.BoxView), dentro de la raíz `StackLayout` ver la jerarquía, los enlaces de usos compilados.

Para obtener más información sobre la `x:Null` expresión marcado, vea [x: Null Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#null).

## <a name="performance"></a>Rendimiento

Enlaces compilados mejoran el rendimiento, con la ventaja de rendimiento varying de enlace de datos. Las pruebas unitarias revelan que:

- Un enlace compilado que utiliza la notificación de cambio de propiedad (es decir, un `OneWay`, `OneWayToSource`, o `TwoWay` enlace) se resuelve aproximadamente 8 veces más rápidos que un enlace clásico.
- Un enlace compilado que no utiliza la notificación de cambio de propiedad (es decir, un `OneTime` enlace) se resuelve aproximadamente 20 veces más rápidos que un enlace clásico.
- Establecer el [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) en un enlace compilado que utiliza la propiedad notificación de cambio (es decir, un `OneWay`, `OneWayToSource`, o `TwoWay` enlace) es aproximadamente 5 veces más rápido que establecer el `BindingContext`en un enlace clásico.
- Establecer el [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) en un enlace compilado que no usa la propiedad notificación de cambio (es decir, un `OneTime` enlace) es aproximadamente 7 veces más rápido que el valor la `BindingContext` en un enlace clásico.

Estas diferencias de rendimiento se pueden ampliar en dispositivos móviles, depende de la plataforma que se va a usar, la versión del sistema operativo que se utiliza y el dispositivo en el que se ejecuta la aplicación.

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de enlace de datos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
