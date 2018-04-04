---
title: Resumen de capítulo 26. Diseños personalizados
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2B7F4346-414E-49FF-97FB-B85E92D98A21
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 5d2dc3e809026a36d186c9a582fcd047f6be24fe
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-26-custom-layouts"></a>Resumen de capítulo 26. Diseños personalizados

Xamarin.Forms incluye varias clases derivadas de [ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/):

* `StackLayout`,
* `Grid`,
* `AbsoluteLayout` y 
* `RelativeLayout`.

En este capítulo se describe cómo crear sus propias clases que derivan de `Layout<View>`.

## <a name="an-overview-of-layout"></a>Información general de diseño

No hay ningún sistema centralizado que controla el diseño de Xamarin.Forms. Cada elemento es responsable de determinar cuál debe ser su propio tamaño y cómo representarse a sí misma dentro de un área concreto.

### <a name="parents-and-children"></a>Elementos primarios y secundarios

Todos los elementos que tiene elementos secundarios son responsable de la posición de los elementos secundarios dentro de sí mismo. Es el elemento primario que quien determina lo que cambiar el tamaño de sus elementos secundarios debe basarse en el tamaño tiene disponible y desea que el tamaño del elemento secundario como.

### <a name="sizing-and-positioning"></a>Ajuste de tamaño y colocar

Diseño comienza en la parte superior del árbol visual a la página y, a continuación, recorre todas las ramas. El método público más importante en el diseño es [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) definido por `VisualElement`. Cualquier elemento que sea un elemento primario a las llamadas de otros elementos `Layout` para cada uno de sus elementos secundarios para dar el elemento secundario de un tamaño y posición en relación a sí mismo en forma de un [ `Rectangle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Rectangle/) valor. Estos `Layout` llamadas propagan a través del árbol visual.

Una llamada a `Layout` es necesaria para un elemento que se va a aparecer en la pantalla y hace que las siguientes propiedades de solo lectura a establecerse. Son coherentes con el `Rectangle` pasado al método:

- [`Bounds`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Bounds/) de tipo `Rectangle`
- [`X`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.X/) de tipo `double`
- [`Y`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Y/) de tipo `double`
- [`Width`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Width/) de tipo `double`
- [`Height`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Height/) de tipo `double`

Versiones anteriores a la `Layout` llamar, `Height` y `Width` tienen valores ficticios &ndash;1.

Una llamada a `Layout` desencadena también llamadas a los métodos protegidos siguientes:

- [`SizeAllocated`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.SizeAllocated/p/System.Double/System.Double/), que llama
- [`OnSizeAllocated`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnSizeAllocated/p/System.Double/System.Double/), que se puede invalidar.

Por último, se desencadena el evento siguiente:

- [`SizeChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.SizeChanged/)

El `OnSizeAllocated` método es reemplazado por `Page` y `Layout`, que son las clases solo dos de Xamarin.Forms que puede tener elementos secundarios. Las llamadas de método invalidado

- [`UpdateChildrenLayout`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.UpdateChildrenLayout()/) para `Page` derivados y [ `UpdateChildrenLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.UpdateChildrenLayout()/) para `Layout` derivados, que llama
- [`LayoutChildren`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) para `Page` derivados y [ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) para `Layout` derivados.

`LayoutChildren` a continuación, llama `Layout` para cada uno de los elementos secundarios del elemento. Si al menos un elemento secundario tiene un nuevo `Bounds` establecer, a continuación, se desencadena el evento siguiente:

- [`LayoutChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.LayoutChanged/) para `Page` derivados y [ `LayoutChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Layout.LayoutChanged/) para `Layout` derivados

### <a name="constraints-and-size-requests"></a>Las restricciones y las solicitudes de tamaño

Para `LayoutChildren` para llamar de forma inteligente `Layout` en todos sus elementos secundarios, debe conocer un *preferido* o *deseado* tamaño para los elementos secundarios. Por lo tanto, las llamadas a `Layout` para cada uno de los elementos secundarios generalmente están precedidas por llamadas a

- [`GetSizeRequest`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.GetSizeRequest/p/System.Double/System.Double/)

Después de que se publicó el libro, el `GetSizeRequest` se en desuso y se reemplazaron por (método)

- [`Measure`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/)

El `Measure` método admite la [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) propiedad e incluye un argumento de tipo [ `MeasureFlag` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MeasureFlags/), que tiene dos miembros:

- [`IncludeMargins`](https://developer.xamarin.com/api/field/Xamarin.Forms.MeasureFlags.IncludeMargins/)
- [`None`](https://developer.xamarin.com/api/field/Xamarin.Forms.MeasureFlags.None/) para que no incluya los márgenes

Para muchos elementos, `GetSizeRequest` o `Measure` Obtiene el tamaño nativo del elemento de su representador. Ambos métodos tienen parámetros de anchura y altura *restricciones*. Por ejemplo, un `Label` utilizará la restricción de ancho para determinar cómo ajustar varias líneas de texto.

Ambos `GetSizeRequest`y `Measure` devuelve un valor de tipo [ `SizeRequest` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SizeRequest/), que tiene dos propiedades:

- [`Request`](https://developer.xamarin.com/api/property/Xamarin.Forms.SizeRequest.Request/) de tipo `Size`
- [`Minimum`](https://developer.xamarin.com/api/property/Xamarin.Forms.SizeRequest.Minimum/) de tipo `Size`

Muy a menudo estos dos valores son iguales y la `Minimum` normalmente se puede pasar por alto el valor.

`VisualElement` También define un método protegido similar a `GetSizeRequest` que se llama desde `GetSizeRequest`:

- [`OnSizeRequest`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnSizeRequest/p/System.Double/System.Double/) Devuelve un `SizeRequest` valor

Ese método ahora está en desuso y se reemplaza por:

- [`OnMeasure`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/)

Cada clase que deriva de `Layout` o `Layout<T>` debe invalidar `OnSizeRequest` o `OnMeasure`. Esto es que una clase de diseño determina su propio tamaño, que normalmente se basa en el tamaño de sus elementos secundarios, que obtiene mediante una llamada a `GetSizeRequest` o `Measure` en los elementos secundarios. Antes y después de llamar a `OnSizeRequest` o `OnMeasure`, `GetSizeRequest` o `Measure` realiza ajustes en función de las siguientes propiedades:

- [`WidthRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/)de tipo `double`, afecta a la `Request` propiedad de `SizeRequest`
- [`HeightRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) de tipo `double`, afecta a la `Request` propiedad de `SizeRequest`
- [`MinimumWidthRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.MinimumWidthRequest/) de tipo `double`, afecta a la `Minimum` propiedad de `SizeRequest`
- [`MinimumHeightRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.MinimumHeightRequest/) de tipo `double`, afecta a la `Minimum` propiedad de `SizeRequest`

### <a name="infinite-constraints"></a>Restricciones de infinitas

Los argumentos de restricción pasan a `GetSizeRequest` (o `Measure`) y `OnSizeRequest` (o `OnMeasure`) puede ser infinito (es decir, los valores de `Double.PositiveInfinity`). Sin embargo, la `SizeRequest` procedentes de estos métodos no pueden contener dimensiones infinitos.

Restricciones de infinitas indican que el tamaño solicitado debe reflejar el tamaño del elemento natural. Vertical `StackLayout` llamadas `GetSizeRequest` (o `Measure`) en sus elementos secundarios con una restricción de alto infinito. Llama a un diseño de pila horizontal `GetSizeRequest` (o `Measure`) en sus elementos secundarios con una restricción de ancho infinito. Un `AbsoluteLayout` llamadas `GetSizeRequest` (o `Measure`) en sus elementos secundarios con restricciones de ancho y alto infinitos.

### <a name="peeking-inside-the-process"></a>Inspeccionar dentro del proceso

El [ **ExploreChildSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes) restricción de muestra y tamaño solicitan información para un diseño sencillo.

## <a name="deriving-from-layoutview"></a>Derivar de diseño<View>

Una clase de diseño personalizado que deriva de `Layout<View>`. Tiene dos responsabilidades:

- Invalidar `OnMeasure` para llamar a `Measure` en elementos secundarios de la del diseño. Devolver un tamaño solicitado para el diseño del propio
- Invalidar `LayoutChildren` para llamar a `Layout` en elementos secundarios del todo el diseño

El `for` o `foreach` bucle de estos reemplazos debe omitir cualquier elemento secundario cuyo `IsVisible` propiedad está establecida en `false`.

Una llamada a `OnMeasure` no está garantizado. `OnMeasure` no se llamará si el elemento primario del diseño es que rigen el tamaño del diseño (por ejemplo, un diseño que se llena una página). Por esta razón, `LayoutChildren` no puede confiar en tamaños de secundarios obtenidos durante el `OnMeasure` llamar. Muy a menudo, `LayoutChildren` debe llamar propio `Measure` en elementos secundarios del diseño, o puede implementar algún tipo de tamaño de almacenamiento en caché lógica (que se explicará más adelante).

### <a name="an-easy-example"></a>Un ejemplo sencillo

El [ **VerticalStackDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo) ejemplo contiene una representación simplificada [ `VerticalStack` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs) clase y una demostración de su uso.

### <a name="vertical-and-horizontal-positioning-simplified"></a>Horizontal y vertical posicionamiento simplificado

Uno de los trabajos que `VerticalStack` debe realizar tiene lugar durante la `LayoutChildren` invalidar. El método usa el secundario `HorizontalOptions` propiedad para determinar cómo colocar el elemento secundario dentro de su ranura en la `VerticalStack`. En su lugar, puede llamar al método estático [ `Layout.LayoutChildIntoBoundingRect` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion/p/Xamarin.Forms.VisualElement/Xamarin.Forms.Rectangle/). Este método llama a `Measure` en el elemento secundario y utiliza sus `HorizontalOptions` y `VerticalOptions` propiedades para colocar el elemento secundario dentro del rectángulo especificado.

### <a name="invalidation"></a>Invalidación

A menudo un cambio de propiedad de un elemento afecta al modo en que ese elemento aparece en el diseño. El diseño debe ser invalidado para desencadenar un nuevo diseño.

`VisualElement` define un método protegido [ `InvalidateMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.InvalidateMeasure()/), que se denomina generalmente el controlador de cambio de propiedad de cualquier propiedad enlazable cuyo cambio afecta al tamaño del elemento. El `InvalidateMeasure` método activa un [ `MeasureInvalidated` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.MeasureInvalidated/) eventos.

El `Layout` clase define un método protegido similar denominado [ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/), que un `Layout` derivado debe llamar a cualquier cambio que afecta a cómo se coloca y se cambia el tamaño de sus elementos secundarios.

### <a name="some-rules-for-coding-layouts"></a>Algunas reglas de codificación diseños

1. Propiedades definidas por `Layout<T>` derivados deben respaldar propiedades enlazables y los controladores de cambio de propiedad deben llamar a `InvalidateLayout`.

2. A `Layout<T>` debería invalidar derivado que define las propiedades adjuntas enlazables [ `OnAdded` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout%3CT%3E.OnAdded/p/T/) para agregar un controlador de cambio de propiedad con sus elementos secundarios y [ `OnRemoved` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout%3CT%3E.OnRemoved/p/T/) para quitar dichos controlador. El controlador debe comprobar cambios en estos adjunta propiedades enlazables y responder mediante una llamada a `InvalidateLayout`.

3. A `Layout<T>` derivado que implementa una caché de tamaños de secundarios debe invalidar `InvalidateLayout` y [ `OnChildMeasureInvalidated` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.OnChildMeasureInvalidated()/) y borrar la memoria caché cuando se llaman a estos métodos.

### <a name="a-layout-with-properties"></a>Un diseño con propiedades

El [ `WrapLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) supone que todos sus elementos secundarios tienen el mismo tamaño y ajusta los elementos secundarios de una fila (o columna) a la siguiente. Define un `Orientation` propiedad como `StackLayout`, y `ColumnSpacing` y `RowSpacing` propiedades como `Grid`, y lo almacena en caché los tamaños de secundarios.

El [ **PhotoWrap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap) ejemplo coloca un `WrapLayout` en un `ScrollView` para mostrar las fotografías almacenadas.

### <a name="no-unconstrained-dimensions-allowed"></a>¡No hay dimensiones sin restricciones permitidos!

El [ `UniformGridLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs) en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca está diseñada para mostrar todos sus elementos secundarios dentro de sí mismo. Por lo tanto, no se puede ocupar dimensiones sin restricciones y produce una excepción si se encuentra uno.

El [ **PhotoGrid** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid) muestra `UniformGridLayout`:

[![Captura de pantalla triple de cuadrícula de fotos](images/ch26fg08-small.png "diseño de cuadrícula uniforme")](images/ch26fg08-large.png#lightbox "uniforme diseño de cuadrícula")

### <a name="overlapping-children"></a>Elementos secundarios superpuestos

Un `Layout<T>` derivado puede superponerse a sus elementos secundarios. Sin embargo, los elementos secundarios se representan en su orden en el `Children` colección y no el orden en que sus `Layout` se llaman a métodos.

La `Layout` clase define dos métodos que le permiten mover un elemento secundario dentro de la colección:

- [`LowerChild`](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LowerChild/p/Xamarin.Forms.View/) Para mover a un elemento secundario al principio de la colección
- [`RaiseChild`](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.RaiseChild/p/Xamarin.Forms.View/) Para mover a un elemento secundario al final de la colección

Para los elementos secundarios superpuestos, los elementos secundarios al final de la colección visualmente aparecen encima de los elementos secundarios al principio de la colección.

El [ `OverlapLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca define una propiedad adjunta para indicar el orden de procesamiento y, por tanto, permitir que uno de sus elementos secundarios que se mostrará encima de los demás. El [ **StudentCardFile** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile) ejemplo se muestra cómo hacerlo:

[![Triple captura de pantalla de cuadrícula de archivo de tarjeta de estudiante](images/ch26fg10-small.png "elementos secundarios del diseño que se superponen")](images/ch26fg10-large.png#lightbox "elementos secundarios del diseño que se superponen")

### <a name="more-attached-bindable-properties"></a>Varias propiedades enlazables de conectar

El [ `CartesianLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca define propiedades enlazables adjuntas para especificar dos `Point` valores y un valor del grosor y manipula `BoxView` elementos similares a las líneas.

El [ **UnitCube** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube) ejemplo que usa para diseñar un cubo 3D.

### <a name="layout-and-layoutto"></a>Diseño y LayoutTo

A `Layout<T>` derivado puede llamar a `LayoutTo` en lugar de `Layout` para animar el diseño. El [ `AnimatedCartesianLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs) clase hace esto y el [ **AnimatedUnitCube** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube) ejemplo muestra.



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 26 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [Ejemplos de capítulo 26](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [Crear diseños personalizados](~/xamarin-forms/user-interface/layouts/custom.md)
