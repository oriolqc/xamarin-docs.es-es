---
title: "Resumen del capítulo 4. Desplazamiento de la pila"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 7A39FD4F-15AD-4F94-960E-9FEEB63FFD44
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 5559f9e6a4baf9d3f82701b5e3f341900ba83bae
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/15/2018
---
# <a name="summary-of-chapter-4-scrolling-the-stack"></a>Resumen del capítulo 4. Desplazamiento de la pila

En este capítulo se dedica principalmente a introducir el concepto de *diseño*, que es el término general para las clases y las técnicas que Xamarin.Forms se usa para organizar la presentación visual de varias vistas en la página.

Diseño implica varias clases que derivan de [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) y [ `Layout<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/). En este capítulo se centra en [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/).

También ha introducido en este capítulo son el [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/), [ `Frame` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/), y [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) clases.

## <a name="stacks-of-views"></a>Pilas de vistas

[`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) se deriva de `Layout<View>` y hereda un [ `Children` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) propiedad de tipo `IList<View>`. Agregar varios elementos de vista a esta colección, y `StackLayout` mostrarlos en una pila vertical u horizontal.

Establecer el [ `Orientation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Orientation/) propiedad de `StackLayout` a un miembro de la [ `StackOrientation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackOrientation/) enumeración, ya sea [ `Vertical` ](https://developer.xamarin.com/api/field/Xamarin.Forms.StackOrientation.Vertical/) o [ `Horizontal`](https://developer.xamarin.com/api/field/Xamarin.Forms.StackOrientation.Horizontal/). De manera predeterminada, es `Vertical`.

Establecer el [ `Spacing` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Spacing/) propiedad de `StackLayout` a una `double` valor para especificar un espacio entre los elementos secundarios. El valor predeterminado es 6.

En el código, puede agregar elementos a la `Children` colección de `StackLayout` en un `for` o `foreach` bucle como se muestra en el [ **ColorLoop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop) ejemplo, o se puede inicializar el `Children` colección con una lista de las vistas individuales como se demuestra en [ **ColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList). Los elementos secundarios deben derivar de `View` , pero puede incluir otros `StackLayout` objetos.

## <a name="scrolling-content"></a>Desplazamiento de contenido

Si un `StackLayout` contiene demasiados elementos secundarios que se muestra en una página, puede colocar el `StackLayout` en un [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) para permitir el desplazamiento.

Establecer el [ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ScrollView.Content/) propiedad de `ScrollView` a la vista que desee desplazar. Esto suele ser un `StackLayout`, pero puede ser cualquier vista.

Establecer el [ `Orientation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ScrollView.Orientation/) propiedad de `ScrollView` a un miembro de la [ `ScrollOrientation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollOrientation/) propiedad, [ `Vertical` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ScrollOrientation.Vertical/), [ `Horizontal` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ScrollOrientation.Horizontal/), o [ `Both` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ScrollOrientation.Both/). De manera predeterminada, es `Vertical`. Si el contenido de un `ScrollView` es un `StackLayout`, las dos orientaciones deben ser coherentes.

El [ **ReflectedColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors) ejemplo muestra el uso de `ScrollView` y `StackLayout` para mostrar los colores disponibles. El ejemplo también muestra cómo usar la reflexión de .NET para obtener todas las propiedades estáticas públicas y los campos de la `Color` estructura sin la necesidad de enumerar de forma explícita a ellos.

## <a name="the-expands-option"></a>La opción se expande

Cuando un `StackLayout` pilas de sus elementos secundarios, cada miembro secundario ocupa una posición determinada en el alto total de la `StackLayout` que depende de tamaño del elemento secundario y la configuración de su `HorizontalOptions` y `VerticalOptions` propiedades. Estas propiedades se les asignan valores de tipo [ `LayoutOptions` ](http://developer.xamstage.com/api/type/Xamarin.Forms.LayoutOptions/).

El `LayoutOptions` estructura define dos propiedades:

- [`Alignment`](https://developer.xamarin.com/api/property/Xamarin.Forms.LayoutOptions.Alignment/) del tipo de enumeración [ `LayoutAlignment` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutAlignment/) con cuatro miembros [ `Start` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Start/), [ `Center` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Center/), [ `End` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.End/), y [`Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Fill/)
- [`Expands`](https://developer.xamarin.com/api/property/Xamarin.Forms.LayoutOptions.Expands/) de tipo `bool`

Para su comodidad, la `LayoutOptions` estructura también define ocho campos estáticos de solo lectura de tipo `LayoutOptions` que abarcan todas las combinaciones de las propiedades de instancia de dos:

- [`LayoutOptions.Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/)
- [`LayoutOptions.Center`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Center/)
- [`LayoutOptions.End`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.End/)
- [`LayoutOptions.Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/)
- [`LayoutOptions.StartAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.StartAndExpand/)
- [`LayoutOptions.CenterAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.CenterAndExpand/)
- [`LayoutOptions.EndAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.EndAndExpand/)
- [`LayoutOptions.FillAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/)

El siguiente análisis implica una `StackLayout` con una orientación vertical de forma predeterminada. Horizontal `StackLayout` es análoga.

Vertical `StackLayout`, `HorizontalOptions` configuración determina cómo un elemento secundario se coloca horizontalmente dentro del ancho de la `StackLayout`. Un `Alignment` de `Start`, `Center`, o `End` hace que el elemento secundario como horizontalmente sin restricciones. El elemento secundario determina su propio ancho y se coloca a la izquierda, el centro o la derecha de la `StackLayout`. El `Fill` opción hace que el elemento secundario que se restringirá horizontalmente y rellene el ancho de la `StackLayout`.

Vertical `StackLayout`, cada miembro secundario no está restringida verticalmente y obtiene un valor vertical zona según el alto del elemento secundario, en cuyo caso el `VerticalOptions` configuración es irrelevante.

Si vertical `StackLayout` es sin restricciones&mdash;que es si su `VerticalOptions` configuración es `Start`, `Center`, o `End`, a continuación, el alto de la `StackLayout` es el alto total de sus elementos secundarios.

Sin embargo, si la vertical `StackLayout` verticalmente está restringido&mdash;si su `VerticalOptions` configuración es `Fill` &mdash;, a continuación, el alto de la `StackLayout` será el alto de su contenedor, que podría ser mayor que el total alto de sus elementos secundarios. Si ese es el caso, y si tiene al menos un elemento secundario un `VerticalOptions` configuración con un `Expands` marca de `true`, a continuación, el espacio adicional en el `StackLayout` se asignan de forma equitativa entre todos los elementos secundarios con un `Expands` marca de `true`. El alto total de los elementos secundarios, a continuación, significará que el alto de la `StackLayout`y el `Alignment` forma parte de la `VerticalOptions` configuración determina cómo el elemento secundario se coloca en la ranura.

Esto se muestra en el [ **VerticalOptionsDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo) ejemplo.

## <a name="frame-and-boxview"></a>Marco de BoxView

Estas dos vistas rectangulares a menudo se usan para fines de presentación.

El [ `Frame` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/) vista muestra un marco rectangular alrededor de otra vista, que puede ser un diseño como `StackLayout`. `Frame` hereda un [ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/) propiedad de [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) que establece en la vista que se mostrará en el `Frame`. El `Frame` es transparente de manera predeterminada. Establezca las tres propiedades siguientes para personalizar la apariencia del marco:

- El [ `OutlineColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.OutlineColor/) propiedad para que sea visible. Es común para establecer `OutlineColor` a `Color.Accent` cuando no conozca la combinación de colores subyacente.
- El [ `HasShadow` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.HasShadow/) propiedad puede establecerse en `true` para mostrar una sombra negra en dispositivos iOS.
- Establecer el [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) propiedad a un `Thickness` valor dejar un espacio entre el marco y el marco de contenido. El valor predeterminado es de 20 unidades en todos los lados.

El `Frame` tiene un valor predeterminado `HorizontalOptions` y `VerticalOptions` valores de `LayoutOptions.Fill`, lo que significa que el `Frame` acabe con su contenedor. Con otra configuración, el tamaño de la `Frame` se basa en el tamaño de su contenido.

El `Frame` se muestra en el [ **FramedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText) ejemplo.

El [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) muestra un área rectangular de color especificado por su [ `Color` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BoxView.Color/) propiedad.

Si el `BoxView` está restringido (su `HorizontalOptions` y `VerticalOptions` propiedades tienen sus valores predeterminados de `LayoutOptions.Fill`), el `BoxView` rellena el espacio disponible para él. Si el `BoxView` está restringida (con `HorizontalOptions` y `LayoutOptions` configuración de `Start`, `Center`, o `End`), tiene una dimensión predeterminada del cuadrado de 40 unidades. Un `BoxView` puede restringida a una dimensión y sin restricciones en la otra.

A menudo, se establecerá el el [ `WidthRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/) y [ `HeightRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) propiedades de `BoxView` para conceder a un tamaño específico. Esto se muestra en el [ **SizedBoxView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView) ejemplo.

Puede usar varias instancias de `StackLayout` para combinar una `BoxView` y varios `Label` instancias en un `Frame` para mostrar un color determinado y, a continuación, coloque cada una de estas vistas en un `StackLayout` en una `ScrollView` para crear el atractivo lista de colores que se muestran en la [ **ColorBlocks** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks) ejemplo:

[![Captura de pantalla triple de bloques de colores](images/ch04fg11-small.png "lista de colores")](images/ch04fg11-large.png#lightbox "lista de colores")

## <a name="a-scrollview-in-a-stacklayout"></a>¿Un ScrollView en un StackLayout?

Colocar un `StackLayout` en un `ScrollView` es común, pero poner un `ScrollView` en un `StackLayout` a veces también es conveniente. En teoría, esto no debería ser posible porque los elementos secundarios de un vertical `StackLayout` se verticalmente sin restricciones. Pero un `ScrollView` se debe restringir verticalmente. Se le debe proporcionar un alto concreto para que, a continuación, puede determinar el tamaño de su elemento secundario para el desplazamiento.

El truco consiste en dar la `ScrollView` secundarios de la `StackLayout` una `VerticalOptions` de `FillAndExpand`. Esto se muestra en el [ **BlackCat** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat) ejemplo.

El **BlackCat** ejemplo también muestra cómo definir y acceder a los recursos de programa que se incrustan en la biblioteca de clases portables (PCL). Esto también se puede lograr con proyectos de recurso compartido (SAP), pero el proceso es un poco más complicado, como el [ **BlackCatSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap) muestra.



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 4 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [Ejemplos de capítulo 4](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [Ejemplos de capítulo 4 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scroll-view.md)
