---
title: Xamarin.Forms TableView
description: En este artículo se explica cómo usar la clase Xamarin.Forms TableView para presentar los menús de desplazamiento, la configuración y los formularios de entrada en las aplicaciones.
ms.prod: xamarin
ms.assetid: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/04/2018
ms.openlocfilehash: b8e851e735fa39d015e22ce511c39ad825bc97c9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120004"
---
# <a name="xamarinforms-tableview"></a>Xamarin.Forms TableView

[TableView](xref:Xamarin.Forms.TableView) es una vista para mostrar listas desplazables de datos o las opciones que hay filas que no comparten la misma plantilla. A diferencia de [ListView](~/xamarin-forms/user-interface/listview/index.md), TableView no tiene el concepto de un `ItemsSource`, por lo que los elementos deben agregarse como elementos secundarios manualmente.

Esta guía se compone de las siguientes secciones:

- **[Casos de uso](#Use_Cases)**  &ndash; cuándo utilizar TableView en lugar de ListView o una vista personalizada.
- **[Estructura TableView](#TableView_Structure)**  &ndash; la jerarquía de vistas que se necesita dentro de un objeto TableView.
- **[Apariencia TableView](#TableView_Appearance)**  &ndash; las opciones de personalización para TableView.
- **[Las celdas integradas](#Built-In_Cells)**  &ndash; opciones integradas de celda, incluida la [EntryCell](#EntryCell) y [SwitchCell](#SwitchCell).
- **[Las celdas personalizadas](#Custom_Cells)**  &ndash; cómo hacer que sus propios celdas personalizadas.

![](tableview-images/tableview-all-sml.png "Ejemplo de TableView")

<a name="Use_Cases" />

## <a name="use-cases"></a>Casos de uso

TableView resulta útil cuando:

- presentar una lista de configuración,
- recopilación de datos en un formulario, o
- mostrando datos que se presentan diferente de la fila a fila (por ejemplo, números, porcentajes e imágenes).

TableView controla el desplazamiento y el diseño de filas en las secciones atractivas, una necesidad común para los escenarios anteriores. El `TableView` utiliza control de la plataforma subyacente de vista equivalente cuando esté disponible para crear una apariencia nativa para cada plataforma.

<a name="TableView_Structure" />

## <a name="tableview-structure"></a>Estructura TableView

Elementos de un `TableView` se organiza en secciones. En la raíz de la `TableView` es el `TableRoot`, que es primario a uno o varios `TableSections`:

```csharp
Content = new TableView {
    Root = new TableRoot {
        new TableSection...
    },
    Intent = TableIntent.Settings
};
```

Cada `TableSection` consta de un encabezado y ViewCells uno o más. Aquí vemos el `TableSection`del `Title` propiedad establecida en *"Anillo"* en el constructor:

```csharp
var section = new TableSection ("Ring") { //TableSection constructor takes title as an optional parameter
    new SwitchCell {Text = "New Voice Mail"},
    new SwitchCell {Text = "New Mail", On = true}
};
```

Para lograr el mismo diseño que antes en XAML:

```xaml
<TableView Intent="Settings">
    <TableRoot>
        <TableSection Title="Ring">
            <SwitchCell Text="New Voice Mail" />
      <SwitchCell Text="New Mail" On="true" />
        </TableSection>
    </TableRoot>
</TableView>
```

<a name="TableView_Appearance" />

## <a name="tableview-appearance"></a>Apariencia TableView

TableView expone el `Intent` propiedad, que es una enumeración de las siguientes opciones:

- **Datos** &ndash; para su uso al mostrar las entradas de datos. Tenga en cuenta que [ListView](~/xamarin-forms/user-interface/listview/index.md) puede ser una mejor opción para el desplazamiento de listas de datos.
- **Formulario** &ndash; para su uso cuando la TableView está actuando como un formulario.
- **Menú** &ndash; para su uso cuando se presenta un menú de las selecciones.
- **Configuración de** &ndash; para su uso cuando se muestra una lista de valores de configuración.

El `TableIntent` que elija puede afectar a cómo el `TableView` aparece en cada plataforma. Incluso si hay no borrar las diferencias, es una práctica recomendada para seleccionar el `TableIntent` que más acerque cómo piensa usar la tabla.

<a name="Built-In_Cells" />

## <a name="built-in-cells"></a>Celdas integradas

Xamarin.Forms incluye celdas integradas para recopilar y mostrar información. Aunque ListView y TableView pueden usar todas las celdas de la mismas, es más relevantes para un escenario TableView lo siguiente:

- **SwitchCell** &ndash; para presentar y capturar un estado verdadero/falso, junto con una etiqueta de texto.
- **EntryCell** &ndash; para presentar y capturar texto.

Consulte [aspecto de la celda de ListView](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md) para obtener una descripción detallada de [TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell) y [ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell).

<a name="switchcell" />

### <a name="switchcell"></a>SwitchCell
[`SwitchCell`](xref:Xamarin.Forms.SwitchCell) es el control que se utilizará para presentar y capturar un activado/desactivado o `true` / `false` estado.

SwitchCells tiene una línea de texto que desee editar y una propiedad de activación/desactivación. Ambas propiedades son enlazables.

- `Text` &ndash; texto que se muestra al lado del conmutador.
- `On` &ndash; Si el modificador se muestra como activado o desactivado.

Tenga en cuenta que el `SwitchCell` expone el `OnChanged` eventos, que le permite responder a los cambios de estado de la celda.

![](tableview-images/switch-cell.png "Ejemplo de SwitchCell")

<a name="entrycell" />

### <a name="entrycell"></a>EntryCell
[`EntryCell`](xref:Xamarin.Forms.EntryCell) es útil cuando se necesita mostrar los datos de texto que el usuario puede editar. `EntryCell`s ofrecen las siguientes propiedades que se pueden personalizar:

- `Keyboard` &ndash; Para mostrar mientras se edita el teclado. Hay opciones para cosas como valores numéricos, correo electrónico, números de teléfono, etcetera. [Consulte la documentación de API](xref:Xamarin.Forms.Keyboard).
- `Label` &ndash; El texto de etiqueta para mostrar a la derecha del campo de entrada de texto.
- `LabelColor` &ndash; Color del texto de la etiqueta.
- `Placeholder` &ndash; Texto que se muestra en el campo de entrada cuando es nulo o está vacío. Este texto desaparece cuando comienza la entrada de texto.
- `Text` &ndash; El texto en el campo de entrada.
- `HorizontalTextAlignment` &ndash; La alineación horizontal del texto. Puede ser, izquierda o derecha centrado. [Consulte la documentación de API](xref:Xamarin.Forms.TextAlignment).

Tenga en cuenta que `EntryCell` expone el `Completed` evento, que se desencadena cuando el usuario presiona 'listo' en el teclado mientras edita el texto.

![](tableview-images/entry-cell.png "Ejemplo de EntryCell")

<a name="Custom_Cells" />

## <a name="custom-cells"></a>Celdas personalizadas
Cuando las celdas integradas no son suficientes, celdas personalizadas pueden utilizarse para presentar y capturar datos de la manera que tenga sentido para su aplicación. Por ejemplo, es posible que desee presentar un control deslizante para permitir que un usuario puede elegir la opacidad de una imagen.

Todas las celdas personalizadas deben derivarse de [ `ViewCell` ](xref:Xamarin.Forms.ViewCell), la misma clase base que todos los de la celda integrada tipos de uso.

Este es un ejemplo de una celda personalizada:

![](tableview-images/custom-cell.png "Ejemplo de la celda personalizada")

### <a name="xaml"></a>XAML
El XAML para crear el diseño anterior está por debajo:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="DemoTableView.TablePage" Title="TableView">
    <ContentPage.Content>
        <TableView Intent="Settings">
            <TableRoot>
                <TableSection Title="Getting Started">
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <Image Source="bulb.png" />
                            <Label Text="left"
                              TextColor="#f35e20" />
                            <Label Text="right"
                              HorizontalOptions="EndAndExpand"
                              TextColor="#503026" />
                        </StackLayout>
                    </ViewCell>
                </TableSection>
            </TableRoot>
        </TableView>
    </ContentPage.Content>
</ContentPage>

```

El XAML anterior hace mucho. Permítame desglosarlo:

- El elemento raíz en el `TableView` es el `TableRoot`.
- Hay un `TableSection` inmediatamente debajo de la `TableRoot`.
- El `ViewCell` se define directamente bajo la sección. A diferencia de `ListView`, `TableView` no requiere ese personalizado (o ninguno) se definen las celdas en una `ItemTemplate`.
- Un StackLayout se usa para administrar el diseño de la celda personalizada. Cualquier diseño podría usarse aquí.

### <a name="cnum"></a>C&num;

Dado que `TableView` funciona con datos estáticos o datos que se ha cambiado manualmente, no tiene el concepto de una plantilla de elemento. En su lugar, las celdas personalizadas se pueden manualmente crear y poner en la tabla. Tenga en cuenta que la técnica de creación personalizada de celda que se hereda de `ViewCell`, a continuación, agregarla a la `TableView` como lo haría una celda integrada, también se admite.
Este es el código de c# para realizar el diseño anterior:

```csharp
var table = new TableView();
table.Intent = TableIntent.Settings;
var layout = new StackLayout() { Orientation = StackOrientation.Horizontal };
layout.Children.Add (new Image() {Source = "bulb.png"});
layout.Children.Add (new Label() {
    Text = "left",
    TextColor = Color.FromHex("#f35e20"),
    VerticalOptions = LayoutOptions.Center
});
layout.Children.Add (new Label () {
    Text = "right",
    TextColor = Color.FromHex ("#503026"),
    VerticalOptions = LayoutOptions.Center,
    HorizontalOptions = LayoutOptions.EndAndExpand
});
table.Root = new TableRoot () {
    new TableSection("Getting Started") {
        new ViewCell() {View = layout}
    }
};

Content = table;
```

C# anterior hace mucho. Permítame desglosarlo:

- El elemento raíz en el `TableView` es el `TableRoot`.
- Hay un `TableSection` inmediatamente debajo de la `TableRoot`.
- El `ViewCell` se define directamente bajo la sección. A diferencia de `ListView`, `TableView` no requiere ese personalizado (o ninguno) se definen las celdas en una `ItemTemplate`.
- Un StackLayout se usa para administrar el diseño de la celda personalizada. Cualquier diseño podría usarse aquí.

Tenga en cuenta que nunca se define una clase para la celda personalizada. En su lugar, el `ViewCell`de propiedad de la vista está establecida para una instancia determinada de `ViewCell`.

## <a name="row-height"></a>Alto de fila

El [ `TableView` ](xref:Xamarin.Forms.TableView) clase tiene dos propiedades que pueden usarse para cambiar el alto de fila de celdas:

- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) : establece el alto de cada fila a una `int`.
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) : las filas tienen diferentes alturas si establece en `true`. Tenga en cuenta que al establecer esta propiedad en `true`, automáticamente se calcula y aplicada por Xamarin.Forms alto de las filas.

Cuando el alto del contenido en una celda de un [ `TableView` ](xref:Xamarin.Forms.TableView) se cambia la fila se actualiza implícitamente alto en la plataforma Universal de Windows (UWP) y Android. Sin embargo, en iOS debe obligársele a actualizar mediante el establecimiento la [ `HasUnevenRows` ](xref:Xamarin.Forms.TableView.HasUnevenRows) propiedad `true` y mediante una llamada a la [ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize) método.

El siguiente ejemplo XAML se muestra un [ `TableView` ](xref:Xamarin.Forms.TableView) que contiene un [ `ViewCell` ](xref:Xamarin.Forms.ViewCell):

```xaml
<ContentPage ...>
    <TableView ...
               HasUnevenRows="true">
        <TableRoot>
            ...
            <TableSection ...>
                ...
                <ViewCell x:Name="_viewCell"
                          Tapped="OnViewCellTapped">
                    <Grid Margin="15,0">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="Auto" />
                        </Grid.RowDefinitions>
                        <Label Text="Tap this cell." />
                        <Label x:Name="_target"
                               Grid.Row="1"
                               Text="The cell has changed size."
                               IsVisible="false" />
                    </Grid>
                </ViewCell>
            </TableSection>
        </TableRoot>
    </TableView>
</ContentPage>
```

Cuando el [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) se pulsa, el `OnViewCellTapped` se ejecuta el controlador de eventos:

```csharp
void OnViewCellTapped(object sender, EventArgs e)
{
    _target.IsVisible = !_target.IsVisible;
    _viewCell.ForceUpdateSize();
}
```

El `OnViewCellTapped` controlador de eventos muestra u oculta el segundo [ `Label` ](xref:Xamarin.Forms.Label) en el [ `ViewCell` ](xref:Xamarin.Forms.ViewCell)y actualiza explícitamente el tamaño de la celda mediante una llamada a la [ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize) método.

Las capturas de pantalla siguientes muestran la celda antes de que se puntea en:

![](tableview-images/cell-beforeresize.png "ViewCell antes de que se va a cambiar de tamaño")

Las capturas de pantalla siguientes muestran la celda después de que se puntea en:

![](tableview-images/cell-afterresize.png "ViewCell después de que se va a cambiar de tamaño")

> [!IMPORTANT]
> Si esta característica está sobreutilizada, hay grandes posibilidades de degradación del rendimiento.

## <a name="related-links"></a>Vínculos relacionados

- [TableView (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView)
