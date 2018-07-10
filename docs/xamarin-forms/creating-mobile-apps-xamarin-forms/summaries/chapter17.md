---
title: Resumen del capítulo 17. Dominar la cuadrícula
description: 'Creación de aplicaciones móviles con Xamarin.Forms: resumen del capítulo 17. Dominar la cuadrícula'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 71EDEF9C-4220-4D2E-A235-43F1EC8746C1
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: b71859d0848d7bf790b3cc4beddc67a5ea86d340
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935483"
---
# <a name="summary-of-chapter-17-mastering-the-grid"></a>Resumen del capítulo 17. Dominar la cuadrícula

El [ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) es un mecanismo eficaz de diseño que organiza sus elementos secundarios en filas y columnas de celdas. Al contrario que el código HTML similar `table` elemento, el `Grid` es únicamente para fines de diseño en lugar de presentación.

## <a name="the-basic-grid"></a>La cuadrícula básica

`Grid` se deriva de [ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/), que define un [ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout%3CT%3E.Children/) propiedad que `Grid` hereda. Puede rellenar esta recopilación en XAML o código.

### <a name="the-grid-in-xaml"></a>La cuadrícula en XAML

La definición de un `Grid` en XAML comienza generalmente con llene la [ `RowDefinitions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.RowDefinitions/) y [ `ColumnDefinitions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.ColumnDefinitions/) colecciones de la `Grid` con [ `RowDefinition` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RowDefinition/) y [ `ColumnDefinition` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ColumnDefinition/) objetos. Se trata de cómo establecer el número de filas y columnas de la `Grid`y sus propiedades.

`RowDefinition` tiene un [ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.RowDefinition.Height/) propiedad y `ColumnDefinition` tiene un [ `Width` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ColumnDefinition.Width/) propiedad, ambos de tipo [ `GridLength` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GridLength/), una estructura.

En XAML, el [ `GridLengthTypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GridLengthTypeConverter/) convierte las cadenas de texto simple en `GridLength` valores. En segundo plano, el [ `GridLength` constructor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.GridLength.GridLength/p/System.Double/Xamarin.Forms.GridUnitType/) crea el `GridLength` valor basado en un número y un valor de tipo [ `GridUnitType` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GridUnitType/), una enumeración con tres miembros:

- [`Absolute`](xref:Xamarin.Forms.GridUnitType.Absolute) &mdash; el ancho o alto se especifica en unidades independientes del dispositivo (un número en XAML)
- [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto) &mdash; el alto o ancho es situado según el contenido de la celda ("Auto" en XAML)
- [`Star`](xref:Xamarin.Forms.GridUnitType.Star) &mdash; se asigna proporcionalmente sobrante alto o ancho (un número con "\*", denominado *estrella*, en XAML)

Cada miembro secundario de la `Grid` deben asignarse también una fila y columna (explícita o implícitamente). Abarca la fila y columna intervalos son opcionales. Estos se especifican mediante las propiedades enlazables adjuntas &mdash; propiedades definidas por el `Grid` pero establezca en elementos secundarios de la `Grid`. `Grid` define cuatro propiedades enlazables adjuntas estáticas:

- [`RowProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.RowProperty/) &mdash; la fila de base cero; el valor predeterminado es 0
- [`ColumnProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.ColumnProperty/) &mdash; la columna de base cero; el valor predeterminado es 0
- [`RowSpanProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.RowSpanProperty/) &mdash; el número de filas abarca el elemento secundario; el valor predeterminado es 1
- [`ColumnSpanProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.ColumnSpanProperty/) &mdash; el número de columnas abarca el elemento secundario; el valor predeterminado es 1

En el código, un programa puede usar los ocho métodos estáticos para establecer y obtener estos valores:

- [`Grid.SetRow`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetRow/p/Xamarin.Forms.BindableObject/System.Int32/) y [`Grid.GetRow`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetRow/p/Xamarin.Forms.BindableObject/)
- [`Grid.SetColumn`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetColumn/p/Xamarin.Forms.BindableObject/System.Int32/) y [`Grid.GetColumn`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetColumn/p/Xamarin.Forms.BindableObject/)
- [`Grid.SetRowSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetRowSpan/p/Xamarin.Forms.BindableObject/System.Int32/) y [`Grid.GetRowSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetRowSpan/p/Xamarin.Forms.BindableObject/)
- [`Grid.SetColumnSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetColumnSpan/p/Xamarin.Forms.BindableObject/System.Int32/) y [`Grid.GetColumnSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetColumnSpan/p/Xamarin.Forms.BindableObject/)

En XAML use los siguientes atributos para establecer estos valores:

- `Grid.Row`
- `Grid.Column`
- `Grid.RowSpan`
- `Grid.ColumnSpan`

El [ **SimpleGridDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SimpleGridDemo) ejemplo muestra cómo crear e inicializar un `Grid` en XAML.

El `Grid` hereda el [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) propiedad desde `Layout` y define dos propiedades adicionales que proporcionan el espaciado entre las filas y columnas:

- [`RowSpacing`](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.RowSpacing/) tiene un valor predeterminado de 6
- [`ColumnSpacing`](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.ColumnSpacing/) tiene un valor predeterminado de 6

El `RowDefinitions` y `ColumnDefinitions` colecciones no son estrictamente necesarias. Si está ausente, el `Grid` crea filas y columnas para la `Grid` elementos secundarios y todas ellas proporciona un valor predeterminado `GridLength` de "\*" (asterisco).

### <a name="the-grid-in-code"></a>La cuadrícula en el código

El [ **GridCodeDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCodeDemo) ejemplo muestra cómo crear y rellenar un `Grid` en el código. Puede establecer las propiedades adjuntas para cada elemento secundario directamente o indirectamente mediante una llamada adicional `Add` métodos como [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.Add/p/Xamarin.Forms.View/System.Int32/System.Int32/System.Int32/System.Int32/) definido por el [Grid.IGridList<T> ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid+IGridList%3CT%3E/) interfaz.

### <a name="the-grid-bar-chart"></a>El gráfico de barras de la cuadrícula

El [ **GridBarChart** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridBarChart) ejemplo muestra cómo agregar varios `BoxView` elementos a un `Grid` con la mayor parte [ `AddHorizontal` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.AddHorizontal/p/System.Collections.Generic.IEnumerable%7BXamarin.Forms.View%7D/) método. De forma predeterminada, estos `BoxView` elementos tienen el mismo ancho. El alto de cada `BoxView` , a continuación, se puede controlar que se asemeje a un gráfico de barras.

El `Grid` en el **GridBarChart** recursos compartidos de ejemplo una `AbsoluteLayout` primario con un inicialmente invisible `Frame`. El programa también establece una `TapGestureRecognizer` en cada `BoxView` para usar el `Frame` para mostrar información acerca de la barra de derivados.

### <a name="alignment-in-the-grid"></a>Alineación de la cuadrícula

El [ **GridAlignment** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridAlignment) ejemplo muestra cómo usar el `VerticalOptions` y `HorizontalOptions` propiedades para alinear los elementos secundarios en un `Grid` celda.

El [ **SpacingButtons** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SpacingButtons) ejemplo igualmente espacios `Button` elementos en el centro de `Grid` celdas.

### <a name="cell-dividers-and-borders"></a>Separadores de celdas y bordes

El `Grid` no incluye una característica que dibuja los bordes o separadores de celdas. Sin embargo, puede crear sus propios.

El [ **GridCellDividers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellDividers) muestra cómo definir otras filas y columnas específicamente para fino `BoxView` elementos para imitar líneas divisorias.

El [ **GridCellBorders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellBorders) programa no crea ninguna de las celdas adicional, pero en su lugar se alinea `BoxView` elementos en cada celda para imitar un borde de celda.

## <a name="almost-real-life-grid-examples"></a>Ejemplos de la cuadrícula de casi reales

El [ **KeypadGrid** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/KeypadGrid) ejemplo usa un `Grid` para mostrar un teclado numérico:

[![Triple captura de pantalla de cuadrícula de teclado](images/ch17fg12-small.png "teclado cuadrícula")](images/ch17fg12-large.png#lightbox "cuadrícula de teclado")

### <a name="responding-to-orientation-changes"></a>Responder a los cambios de orientación

El `Grid` puede ayudar a estructurar un programa para responder a los cambios de orientación. El [ **GridRgbSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridRgbSliders) muestra una técnica que mueve un elemento entre una segunda fila de un teléfono con orientación vertical y la segunda columna de un teléfono orientado a horizontal.

Inicializa el programa `Slider` elementos a un intervalo de 0 a 255 y utiliza los enlaces de datos para mostrar el valor de los controles deslizantes en formato hexadecimal. Dado que el `Slider` valores son de punto flotante y la cadena de formato para hexadecimal solo funciona con números enteros, de .NET un [ `DoubleToIntConvert` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DoubleToIntConverter.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca de ayuda.



## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 17 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch17-Apr2016.pdf)
- [Ejemplos de capítulo 17](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)
- [Grid](~/xamarin-forms/user-interface/layouts/grid.md)
