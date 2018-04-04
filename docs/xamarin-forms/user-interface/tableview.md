---
title: TableView
description: Presentar los menús de desplazamiento, la configuración y los formularios de entrada.
ms.prod: xamarin
ms.assetid: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: dc55f3fe70450c71b639cf33166720fc27d45a10
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="tableview"></a>TableView

[TableView](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) es una vista para mostrar listas desplazables de datos o las opciones que hay filas que no comparten la misma plantilla. A diferencia de [ListView](~/xamarin-forms/user-interface/listview/index.md), TableView no tiene el concepto de un `ItemsSource`, por lo que los elementos deben agregarse como elementos secundarios manualmente.

Esta guía se compone de las siguientes secciones:

- **[Casos de uso](#Use_Cases)**  &ndash; cuándo utilizar TableView en lugar de ListView o una vista personalizada.
- **[Estructura de TableView](#TableView_Structure)**  &ndash; la jerarquía de vistas que se necesita dentro de un objeto TableView.
- **[Apariencia TableView](#TableView_Appearance)**  &ndash; las opciones de personalización para TableView.
- **[Las celdas integradas](#Built-In_Cells)**  &ndash; opciones de celda integrados, incluidos los [EntryCell](#EntryCell) y [SwitchCell](#SwitchCell).
- **[Las celdas personalizadas](#Custom_Cells)**  &ndash; cómo hacer que sus propios celdas personalizados.

![](tableview-images/tableview-all-sml.png "En el ejemplo se TableView")

<a name="Use_Cases" />

## <a name="use-cases"></a>Casos de uso

TableView resulta útil cuando:

- presentar una lista de configuración,
- recopilar los datos en un formulario, o
- Mostrar datos que se presentación de forma diferente de fila a fila (por ejemplo, números, porcentajes e imágenes).

TableView controla el desplazamiento y para presentar las filas en las secciones atractivas, una necesidad común para los escenarios anteriores. El `TableView` control utiliza cada plataforma subyacente vista equivalente cuando esté disponible, crear una apariencia nativa para cada plataforma.

<a name="TableView_Structure" />

## <a name="tableview-structure"></a>Estructura de TableView

Elementos de un `TableView` se organizan en secciones. En la raíz de la `TableView` es el `TableRoot`, que es primario a uno o varios `TableSections`:

```csharp
Content = new TableView {
    Root = new TableRoot {
        new TableSection...
    },
    Intent = TableIntent.Settings
};
```

Cada `TableSection` consta de un encabezado y uno o más ViewCells. Aquí vemos la `TableSection`del `Title` propiedad establecida en *"Anillo"* en el constructor:

```csharp
var section = new TableSection ("Ring") { //TableSection constructor takes title as an optional parameter
    new SwitchCell {Text = "New Voice Mail"},
    new SwitchCell {Text = "New Mail", On = true}
};
```

Para lograr el mismo diseño como arriba en XAML:

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

## <a name="tableview-appearance"></a>TableView apariencia

TableView expone el `Intent` propiedad, que es una enumeración de las siguientes opciones:

- **Datos** &ndash; para su uso al mostrar las entradas de datos. Tenga en cuenta que [ListView](~/xamarin-forms/user-interface/listview/index.md) puede ser una mejor opción para el desplazamiento de listas de datos.
- **Formulario** &ndash; para su uso cuando el TableView está actuando como un formulario.
- **Menú** &ndash; para su uso cuando se presenta un menú de las selecciones.
- **Configuración de** &ndash; para su uso cuando se muestra una lista de valores de configuración.

El `TableIntent` que elija puede afectar a cómo el `TableView` aparece en cada plataforma. Aunque hay no desactive las diferencias, es una práctica recomendada seleccionar las `TableIntent` que mejor ajuste a cómo piensa usar la tabla.

<a name="Built-In_Cells" />

## <a name="built-in-cells"></a>Celdas integradas

Xamarin.Forms incluye celdas integradas para recopilar y mostrar información. Aunque ListView y TableView pueden usar todas las celdas de la mismas, es las más relevantes para un escenario de TableView lo siguiente:

- **SwitchCell** &ndash; para presentar y capturar un estado verdadero/falso, junto con una etiqueta de texto.
- **EntryCell** &ndash; para presentar y capturar el texto.

Vea [aspecto de la celda de ListView](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md) para obtener una descripción detallada de [TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell) y [ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell).

<a name="switchcell" />

### <a name="switchcell"></a>SwitchCell
[`SwitchCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell/) es el control que se utilizará para presentar y capturar un activado/desactivado o `true` / `false` estado.

SwitchCells tiene una línea de texto que desee editar y una propiedad de encendido/apagado. Ambas propiedades son enlazables.

- `Text` &ndash; texto que se mostrará al lado del conmutador.
- `On` &ndash; Si se muestra el conmutador como on u off.

Tenga en cuenta que la `SwitchCell` expone el `OnChanged` eventos, lo que le permite responder a cambios de estado de la celda.

![](tableview-images/switch-cell.png "Ejemplo de SwitchCell")

<a name="entrycell" />

### <a name="entrycell"></a>EntryCell
[`EntryCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell/) es útil cuando se necesitan para mostrar datos de texto que el usuario puede editar. `EntryCell`s ofrecen las siguientes propiedades que se pueden personalizar:

- `Keyboard` &ndash; El teclado para mostrar mientras se edita. Hay opciones para cosas como los valores numéricos, correo electrónico, números de teléfono, etcetera. [Consulte los documentos de la API](http://developer.xamarin.com/api/type/Xamarin.Forms.Keyboard/).
- `Label` &ndash; Texto de etiqueta que se muestran a la derecha del campo de entrada de texto.
- `LabelColor` &ndash; El color del texto de etiqueta.
- `Placeholder` &ndash; Texto que se muestra en el campo de entrada cuando es nulo o está vacío. Este texto desaparece cuando comienza la entrada de texto.
- `Text` &ndash; El texto en el campo de entrada.
- `HorizontalTextAlignment` &ndash; La alineación horizontal del texto. Puede ser centro, izquierda o derecha alineado. [Consulte los documentos de la API](http://developer.xamarin.com/api/type/Xamarin.Forms.TextAlignment/).

Tenga en cuenta que `EntryCell` expone el `Completed` evento, que se desencadena cuando el usuario presiona 'done' en el teclado mientras edita el texto.

![](tableview-images/entry-cell.png "Ejemplo de EntryCell")

<a name="Custom_Cells" />

## <a name="custom-cells"></a>Celdas personalizadas
Cuando las celdas integradas no son suficientes, celdas personalizadas se pueden utilizar para presentar y capturar los datos de la manera que tenga sentido para la aplicación. Por ejemplo, puede que desee presentar un control deslizante para permitir que un usuario puede elegir la opacidad de una imagen.

Todas las celdas personalizadas deben derivarse de [ `ViewCell` ](http://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/), la misma clase base que todos los de la celda integrada tipos de uso.

Este es un ejemplo de una celda personalizado:

![](tableview-images/custom-cell.png "Ejemplo de celda personalizado")

### <a name="xaml"></a>XAML
El código XAML para crear el diseño anterior es menor que:

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

El XAML anterior realiza mucho. Vamos a interrumpir hacia abajo:

- El elemento raíz en el `TableView` es la `TableRoot`.
- Hay un `TableSection` inmediatamente debajo de la `TableRoot`.
- El `ViewCell` se define directamente en la sección. A diferencia de `ListView`, `TableView` no requiere ese personalizado (o ninguno) las celdas se definen en un `ItemTemplate`.
- Un StackLayout se utiliza para administrar el diseño de la celda personalizado. Cualquier diseño se puede usar aquí.

### <a name="cnum"></a>C&num;

Dado que `TableView` funciona con datos estáticos, o datos que se ha cambiado manualmente, no tiene el concepto de una plantilla de elementos. En su lugar, las celdas personalizadas manualmente creadas y colocadas en la tabla. Tenga en cuenta que la técnica de creación personalizado de celdas que se hereda de `ViewCell`, a continuación, agregarla a la `TableView` como se haría una celda integrada, también se admite.
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

C# anterior realiza mucho. Vamos a interrumpir hacia abajo:

- El elemento raíz en el `TableView` es la `TableRoot`.
- Hay un `TableSection` inmediatamente debajo de la `TableRoot`.
- El `ViewCell` se define directamente en la sección. A diferencia de `ListView`, `TableView` no requiere ese personalizado (o ninguno) las celdas se definen en un `ItemTemplate`.
- Un StackLayout se utiliza para administrar el diseño de la celda personalizado. Cualquier diseño se puede usar aquí.

Tenga en cuenta que nunca se define una clase para la celda personalizada. En su lugar, el `ViewCell`de la propiedad view se establece para una instancia determinada de `ViewCell`.



## <a name="related-links"></a>Vínculos relacionados

- [TableView (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView)
