---
title: Crear un diseño personalizado
description: 'Xamarin.Forms define cuatro clases de diseño: StackLayout, AbsoluteLayout, RelativeLayout y cuadrícula, y cada uno de ellos organiza a sus elementos secundarios de forma diferente. Sin embargo, a veces es necesario organizar el contenido de la página con un diseño no proporcionado por Xamarin.Forms. En este artículo se explica cómo escribir una clase de diseño personalizado y se muestra una clase de WrapLayout dependientes de la orientación que sus elementos secundarios se organiza horizontalmente en la página y, a continuación, se ajusta la presentación de los elementos secundarios subsiguientes a filas adicionales.'
ms.prod: xamarin
ms.assetid: B0CFDB59-14E5-49E9-965A-3DCCEDAC2E31
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/29/2017
ms.openlocfilehash: 7f2a443ded0d6b41db237e9842cd80e016bb5251
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2018
ms.locfileid: "34847752"
---
# <a name="creating-a-custom-layout"></a>Crear un diseño personalizado

_Xamarin.Forms define cuatro clases de diseño: StackLayout, AbsoluteLayout, RelativeLayout y cuadrícula, y cada uno de ellos organiza a sus elementos secundarios de forma diferente. Sin embargo, a veces es necesario organizar el contenido de la página con un diseño no proporcionado por Xamarin.Forms. En este artículo se explica cómo escribir una clase de diseño personalizado y se muestra una clase de WrapLayout dependientes de la orientación que sus elementos secundarios se organiza horizontalmente en la página y, a continuación, se ajusta la presentación de los elementos secundarios subsiguientes a filas adicionales._

## <a name="overview"></a>Información general

En Xamarin.Forms, se derivan todas las clases de diseño de la [ `Layout<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) clase y restringir el tipo genérico para [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) y sus tipos derivados. A su vez, el `Layout<T>` clase se deriva de la [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) (clase), que proporciona el mecanismo de secundarios de ajuste de tamaño y colocar elementos.

Todos los elementos visuales es responsable de determinar su propio tamaño preferido, que se conoce como el *solicitado* tamaño. [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/), [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/), y [ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) tipos derivados son responsables de determinar la ubicación y el tamaño de sus secundarios o elementos secundarios, con respecto a ellos mismos. Por lo tanto, el diseño implica una relación de elementos primarios y secundarios, donde el elemento primario determina cuál debe ser el tamaño de sus elementos secundarios, pero intentará adaptarse al tamaño solicitado del elemento secundario.

Se requiere un conocimiento exhaustivo de los ciclos de diseño e invalidación de Xamarin.Forms para crear un diseño personalizado. Ahora se explicará estos ciclos.

## <a name="layout"></a>Diseño

Diseño comienza en la parte superior del árbol visual con una página y pasa a través de todas las ramas del árbol visual para abarcar todos los elementos visuales en una página. Elementos que son nodos principales de otros elementos están responsables de ajustar el tamaño y la posición de sus elementos secundarios con respecto a ellos mismos.

El [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) clase define un [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) método que mide un elemento para las operaciones de diseño, y un [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) método que especifica el área rectangular que se representará en el elemento. Cuando se inicia una aplicación y se muestra la primera página, un *ciclo de diseño* primero que se compone de `Measure` llamadas y, a continuación, `Layout` llama, se inicia en el [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) objeto:

1. Durante el ciclo de diseño, cada elemento primario es responsable de llamar a la `Measure` método en sus elementos secundarios.
1. Después de haberse medidos de los elementos secundarios, cada elemento primario es responsable de llamar a la `Layout` método en sus elementos secundarios.

Este ciclo se garantiza que todos los elementos visuales en la página recibe llamadas a la `Measure` y `Layout` métodos. El proceso se muestra en el diagrama siguiente:

![](custom-images/layout-cycle.png "Ciclo de diseño de Xamarin.Forms")

> [!NOTE]
> Tenga en cuenta que los ciclos de diseño también pueden producirse en un subconjunto del árbol visual si algo cambia para afectar al diseño. Esto incluye los elementos que se agregan o quitan de una colección, como en un [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), un cambio en el [ `IsVisible` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsVisible/) propiedad de un elemento o un cambio en el tamaño de un elemento.

Cada clase de Xamarin.Forms que tiene un `Content` o un `Children` propiedad tiene un reemplazable [ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) método. Clases de diseño personalizado que derivan de [ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) debe invalidar este método y asegúrese de que el [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) y [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) métodos son se llama en todos los elementos secundarios del elemento, para proporcionar la distribución personalizada deseada.

Además, cada clase que deriva de [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) o [ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) debe invalidar el [ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/) método, que es donde una clase de diseño Determina el tamaño que deba estar realizando llamadas a la [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) métodos de sus elementos secundarios.

> [!NOTE]
> Elementos que determinan su tamaño en función de *restricciones*, que indican la cantidad de espacio está disponible para un elemento dentro primario del elemento. Las restricciones se pasan a la [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) y [ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/) métodos pueden oscilar entre 0 y `Double.PositiveInfinity`. Un elemento es *limitada*, o *totalmente restringido*, cuando recibe una llamada a su [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) método con argumentos no infinita; está restringido el elemento un tamaño determinado. Un elemento es *sin restricciones*, o *parcialmente restringido*, cuando recibe una llamada a su `Measure` método con un argumento como mínimo igual a `Double.PositiveInfinity` : puede ser la restricción infinita considerar que indica el ajuste automático de tamaño.

## <a name="invalidation"></a>Invalidación

Invalidación es el proceso por el cual un cambio en un elemento de una página desencadena un nuevo ciclo de diseño. Los elementos se consideran no válidos cuando ya no tienen el tamaño correcto o la posición. Por ejemplo, si la [ `FontSize` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.FontSize/) propiedad de un [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) cambios, el `Button` se dice que es válida porque ya no tiene el tamaño correcto. Cambiar el tamaño de la `Button` , a continuación, se puede tener un efecto dominó de cambios de diseño a través del resto de una página.

Invalidarán elementos propios invocando la [ `InvalidateMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.InvalidateMeasure()/) método, por lo general cuando cambia una propiedad del elemento que podría dar lugar a un nuevo tamaño del elemento. Este método se activa la [ `MeasureInvalidated` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.MeasureInvalidated/) evento, que controla el elemento primario del elemento para desencadenar un nuevo ciclo de diseño.

El [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) clase establece un controlador para el [ `MeasureInvalidated` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.MeasureInvalidated/) eventos en cada elemento secundario que se agrega a su `Content` propiedad o `Children` colección y desasocia el controlador cuando el se quita el elemento secundario. Por lo tanto, todos los elementos en el árbol visual que tiene elementos secundarios es una alerta cuando uno de sus elementos secundarios cambia de tamaño. El siguiente diagrama muestra cómo un cambio en el tamaño de un elemento en el árbol visual puede producir cambios que ripple el árbol:

![](custom-images/invalidation.png "Invalidación del árbol Visual")

Sin embargo, la `Layout` clase intenta limitar el impacto de un cambio de tamaño de un elemento secundario en el diseño de una página. Si el diseño es tamaño limitada, a continuación, un cambio de tamaño del elemento secundario no afecta a ningún elemento mayor que el diseño del elemento primario en el árbol visual. Sin embargo, normalmente un cambio en el tamaño de un diseño afecta a cómo el diseño organiza a sus elementos secundarios. Por lo tanto, cualquier cambio de tamaño del diseño iniciará un ciclo de diseño para el diseño y el diseño recibirá llamadas a su [ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/) y [ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) métodos.

El [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) clase también define un [ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/) método que tiene un propósito similar a la [ `InvalidateMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.InvalidateMeasure()/) método. El `InvalidateLayout` debe invocar el método cada vez que se realiza un cambio que afecta a cómo el diseño posiciones y tamaños de sus elementos secundarios. Por ejemplo, el `Layout` (clase), se invoca el `InvalidateLayout` método cada vez que se agrega o se quita de un diseño de un elemento secundario.

El [ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/) se puede invalidar para implementar una memoria caché para reducir al mínimo las llamadas repetitivas de la [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) métodos de elementos secundarios del diseño. Reemplazar el `InvalidateLayout` método proporcionará una notificación cuando se agregan o se quita del diseño los elementos secundarios. De forma similar, el [ `OnChildMeasureInvalidated` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.OnChildMeasureInvalidated()/) método se puede invalidar para proporcionar una notificación cuando uno de los elementos secundarios del diseño cambia de tamaño. Para las invalidaciones de método, un diseño personalizado debería responder mediante la desactivación de la memoria caché. Para obtener más información, consulte [calcular y almacenar datos en caché](#caching).

## <a name="creating-a-custom-layout"></a>Crear un diseño personalizado

El proceso para crear un diseño personalizado es como sigue:

1. Cree una clase que se derive de la clase `Layout<View>`. Para obtener más información, consulte [crear un WrapLayout](#creating).
1. [*opcional*] agregar propiedades, respaldadas por propiedades enlazables, para todos los parámetros que se deben establecer en la clase de diseño. Para obtener más información, consulte [agregar propiedades respaldado por propiedades enlazables](#adding_properties).
1. Invalidar el [ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/) método que se invoca el [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) método en todo el diseño los elementos secundarios y devuelve un tamaño solicitado para el diseño. Para obtener más información, consulte [invalidación del método OnMeasure](#onmeasure).
1. Invalidar el [ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) método que se invoca el [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) método en elementos secundarios de la del diseño. Error al invocar el [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) método en cada elemento secundario en un diseño dará como resultado de la unidad secundaria nunca recibe un tamaño correcto o la posición y, por tanto, el elemento secundario no será visible en la página. Para obtener más información, consulte [invalidación del método LayoutChildren](#layoutchildren).

  > [!NOTE]
>  Al enumerar los elementos secundarios en el [ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/) y [ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) invalidaciones, omita cualquier elemento secundario cuyo [ `IsVisible` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsVisible/) propiedad está establecida en `false`. Así se asegurará de que el diseño personalizado no deja espacio para elementos secundarios visibles.

1. [*opcional*] invalidar la [ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/) método para recibir una notificación cuando se agrega o se quita del diseño de elementos secundarios. Para obtener más información, consulte [invalidación del método InvalidateLayout](#invalidatelayout).
1. [*opcional*] invalidar la [ `OnChildMeasureInvalidated` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.OnChildMeasureInvalidated()/) método para recibir una notificación cuando uno de los elementos secundarios del diseño cambia de tamaño. Para obtener más información, consulte [invalidación del método OnChildMeasureInvalidated](#onchildmeasureinvalidated).

> [!NOTE]
> Tenga en cuenta que la [ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/) invalidación no se invoca si el tamaño del diseño se rige por su elemento primario, en lugar de sus elementos secundarios. Sin embargo, se invocará la invalidación si una o ambas de las restricciones son infinitos, o si la clase de diseño tiene no predeterminado [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) o [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) valores de propiedad. Por este motivo, el [ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) invalidación no puede confiar en tamaños de secundarios obtenidos durante la [ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/) llamada al método. En su lugar, `LayoutChildren` debe invocar el [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) método en elementos secundarios del diseño, antes de invocar el [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) método. O bien, obtiene el tamaño de los elementos secundarios en el `OnMeasure` invalidación puede almacenarse en caché para evitar más adelante `Measure` invocaciones en el `LayoutChildren` invalidación, pero la clase de diseño necesitará saber cuándo deben obtenerse de nuevo los tamaños. Para obtener más información, consulte [cálculo y almacenamiento en caché de datos de diseño](#caching).

Puede usar la clase de diseño agregándolo a una [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)y mediante la adición de elementos secundarios para el diseño. Para obtener más información, consulte [consumiendo el WrapLayout](#consuming).

<a name="creating" />

### <a name="creating-a-wraplayout"></a>Crear un WrapLayout

La aplicación de ejemplo muestra un dependientes de la orientación `WrapLayout` clase que sus elementos secundarios se organiza horizontalmente en la página y, a continuación, se ajusta la presentación de los elementos secundarios subsiguientes a filas adicionales.

El `WrapLayout` clase asigna la misma cantidad de espacio por cada miembro secundario, conocido como el *tamaño de la celda*, según el tamaño máximo de los elementos secundarios. Los elementos secundarios más pequeños que el tamaño de la celda se puede colocar dentro de la celda en función de sus [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) y [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) valores de propiedad.

El `WrapLayout` definición de clase se muestra en el ejemplo de código siguiente:

```csharp
public class WrapLayout : Layout<View>
{
  Dictionary<Size, LayoutData> layoutDataCache = new Dictionary<Size, LayoutData>();
  ...
}
```

<a name="caching" />

#### <a name="calculating-and-caching-layout-data"></a>Calcular y almacenar en caché datos de diseño

El `LayoutData` estructura almacena los datos de una colección de elementos secundarios en un número de propiedades:

- `VisibleChildCount` : el número de elementos secundarios que están visibles en el diseño.
- `CellSize` – el tamaño máximo de todos los elementos secundarios, ajustadas al tamaño del diseño.
- `Rows` : el número de filas.
- `Columns` : el número de columnas.

El `layoutDataCache` campo se usa para almacenar varios `LayoutData` valores. Cuando la aplicación se inicia, dos `LayoutData` se almacenarán en caché objetos en el `layoutDataCache` diccionario para la orientación actual: uno de los argumentos de restricción a la `OnMeasure` invalidación y otro para el `width` y `height` argumentos para el `LayoutChildren` invalidar. Al girar el dispositivo en orientación horizontal, el `OnMeasure` invalidar y `LayoutChildren` se invocará nuevo reemplazo, lo que producirá dos otro `LayoutData` objetos que se está almacenando en caché en el diccionario. Sin embargo, cuando el dispositivo se vuelve a la orientación vertical, ningún cálculo más es necesario porque el `layoutDataCache` ya tiene los datos necesarios.

El siguiente ejemplo de código muestra la `GetLayoutData` método, que calcula las propiedades de la `LayoutData` estructurada en función de un tamaño determinado:

```csharp
LayoutData GetLayoutData(double width, double height)
{
  Size size = new Size(width, height);

  // Check if cached information is available.
  if (layoutDataCache.ContainsKey(size))
  {
    return layoutDataCache[size];
  }

  int visibleChildCount = 0;
  Size maxChildSize = new Size();
  int rows = 0;
  int columns = 0;
  LayoutData layoutData = new LayoutData();

  // Enumerate through all the children.
  foreach (View child in Children)
  {
    // Skip invisible children.
    if (!child.IsVisible)
      continue;

    // Count the visible children.
    visibleChildCount++;

    // Get the child's requested size.
    SizeRequest childSizeRequest = child.Measure(Double.PositiveInfinity, Double.PositiveInfinity);

    // Accumulate the maximum child size.
    maxChildSize.Width = Math.Max(maxChildSize.Width, childSizeRequest.Request.Width);
    maxChildSize.Height = Math.Max(maxChildSize.Height, childSizeRequest.Request.Height);
  }

  if (visibleChildCount != 0)
  {
    // Calculate the number of rows and columns.
    if (Double.IsPositiveInfinity(width))
    {
      columns = visibleChildCount;
      rows = 1;
    }
    else
    {
      columns = (int)((width + ColumnSpacing) / (maxChildSize.Width + ColumnSpacing));
      columns = Math.Max(1, columns);
      rows = (visibleChildCount + columns - 1) / columns;
    }

    // Now maximize the cell size based on the layout size.
    Size cellSize = new Size();

    if (Double.IsPositiveInfinity(width))
      cellSize.Width = maxChildSize.Width;
    else
      cellSize.Width = (width - ColumnSpacing * (columns - 1)) / columns;

    if (Double.IsPositiveInfinity(height))
      cellSize.Height = maxChildSize.Height;
    else
      cellSize.Height = (height - RowSpacing * (rows - 1)) / rows;

    layoutData = new LayoutData(visibleChildCount, cellSize, rows, columns);
  }

  layoutDataCache.Add(size, layoutData);
  return layoutData;
}
```

El `GetLayoutData` método realiza las siguientes operaciones:

- Determina si un calculado `LayoutData` valor ya está en la memoria caché y lo devuelve si está disponible.
- En caso contrario, enumera a través de todos los elementos secundarios, invocar el `Measure` método en cada elemento secundario con un ancho infinito y el alto y determina el tamaño máximo de secundarios.
- Siempre que hay al menos un elemento secundario visible, calcula el número de filas y columnas necesarias y, a continuación, calcula un tamaño de la celda para los elementos secundarios en función de las dimensiones de la `WrapLayout`. Tenga en cuenta que el tamaño de celda normalmente es ligeramente más amplio que el tamaño máximo de secundarios, pero que también podría ser más pequeño si la `WrapLayout` no es lo suficientemente ancha para la más amplia secundario o lo suficientemente alto para el elemento secundario más alto.
- Almacena el nuevo `LayoutData` valor en la memoria caché.

<a name="adding_properties" />

#### <a name="adding-properties-backed-by-bindable-properties"></a>Agregar propiedades respaldadas por propiedades enlazables

El `WrapLayout` clase define `ColumnSpacing` y `RowSpacing` propiedades cuyos valores se utilizan para separar las filas y columnas en el diseño y que están respaldadas por propiedades enlazables. Las propiedades enlazables se muestran en el ejemplo de código siguiente:

```csharp
public static readonly BindableProperty ColumnSpacingProperty = BindableProperty.Create(
  "ColumnSpacing",
  typeof(double),
  typeof(WrapLayout),
  5.0,
  propertyChanged: (bindable, oldvalue, newvalue) =>
  {
    ((WrapLayout)bindable).InvalidateLayout();
  });

public static readonly BindableProperty RowSpacingProperty = BindableProperty.Create(
  "RowSpacing",
  typeof(double),
  typeof(WrapLayout),
  5.0,
  propertyChanged: (bindable, oldvalue, newvalue) =>
  {
    ((WrapLayout)bindable).InvalidateLayout();
  });
```

Se invoca el controlador de cambio de propiedad de cada propiedad se puede enlazar la `InvalidateLayout` invalidación del método para desencadenar un nuevo diseño se pasan en el `WrapLayout`. Para obtener más información, consulte [invalidación del método InvalidateLayout](#invalidatelayout) y [invalidación del método OnChildMeasureInvalidated](#onchildmeasureinvalidated).

<a name="onmeasure" />

#### <a name="overriding-the-onmeasure-method"></a>Invalidación del método OnMeasure

El `OnMeasure` invalidación se muestra en el ejemplo de código siguiente:

```csharp
protected override SizeRequest OnMeasure(double widthConstraint, double heightConstraint)
{
  LayoutData layoutData = GetLayoutData(widthConstraint, heightConstraint);
  if (layoutData.VisibleChildCount == 0)
  {
    return new SizeRequest();
  }

  Size totalSize = new Size(layoutData.CellSize.Width * layoutData.Columns + ColumnSpacing * (layoutData.Columns - 1),
                layoutData.CellSize.Height * layoutData.Rows + RowSpacing * (layoutData.Rows - 1));
  return new SizeRequest(totalSize);
}
```

La invalidación, se invoca el `GetLayoutData` método y construcciones un `SizeRequest` objeto a partir de los datos devueltos, también teniendo en cuenta la `RowSpacing` y `ColumnSpacing` valores de propiedad. Para obtener más información sobre la `GetLayoutData` método, consulte [calcular y almacenar datos en caché](#caching).

> [!IMPORTANT]
> El [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) y [ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/) métodos nunca deben solicitar una dimensión infinita devolviendo un [ `SizeRequest` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SizeRequest/) valor con una propiedad establecida en `Double.PositiveInfinity`. Sin embargo, al menos uno de los argumentos de restricción a `OnMeasure` puede ser `Double.PositiveInfinity`.

<a name="layoutchildren" />

#### <a name="overriding-the-layoutchildren-method"></a>Invalidación del método LayoutChildren

El `LayoutChildren` invalidación se muestra en el ejemplo de código siguiente:

```csharp
protected override void LayoutChildren(double x, double y, double width, double height)
{
  LayoutData layoutData = GetLayoutData(width, height);

  if (layoutData.VisibleChildCount == 0)
  {
    return;
  }

  double xChild = x;
  double yChild = y;
  int row = 0;
  int column = 0;

  foreach (View child in Children)
  {
    if (!child.IsVisible)
    {
      continue;
    }

    LayoutChildIntoBoundingRegion(child, new Rectangle(new Point(xChild, yChild), layoutData.CellSize));
    if (++column == layoutData.Columns)
    {
      column = 0;
      row++;
      xChild = x;
      yChild += RowSpacing + layoutData.CellSize.Height;
    }
    else
    {
      xChild += ColumnSpacing + layoutData.CellSize.Width;
    }
  }
}
```

La invalidación comienza con una llamada a la `GetLayoutData` (método) y, a continuación, enumera todos los elementos secundarios a tamaño y colocarlos en la celda de cada elemento secundario. Esto se logra invocando la [ `LayoutChildIntoBoundingRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion/p/Xamarin.Forms.VisualElement/Xamarin.Forms.Rectangle/) método, que se usa para colocar un elemento secundario dentro de un rectángulo en función de su [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) y [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)valores de propiedad. Esto equivale a hacer una llamada a la secundaria [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) método.

> [!NOTE]
> Tenga en cuenta que el rectángulo pasado a la `LayoutChildIntoBoundingRegion` método incluye el área completa en el que puede residir el elemento secundario.

Para obtener más información sobre la `GetLayoutData` método, consulte [calcular y almacenar datos en caché](#caching).

<a name="invalidatelayout" />

#### <a name="overriding-the-invalidatelayout-method"></a>Invalidación del método InvalidateLayout

El [ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/) invalidación se invoca cuando los elementos secundarios se agregan o se quitan del diseño, o cuando una de las `WrapLayout` cambios en las propiedades de valor, como se muestra en el ejemplo de código siguiente:

```csharp
protected override void InvalidateLayout()
{
  base.InvalidateLayout();
  layoutInfoCache.Clear();
}
```

La invalidación invalida el diseño y descarta toda la información de diseño almacenado en caché.

> [!NOTE]
> Para detener la [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) clase invocar la [ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/) reemplazo del método cada vez que un elemento secundario se agrega o quita de un diseño, el [ `ShouldInvalidateOnChildAdded` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.ShouldInvalidateOnChildAdded/p/Xamarin.Forms.View/) y [ `ShouldInvalidateOnChildRemoved` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.ShouldInvalidateOnChildRemoved/p/Xamarin.Forms.View/) métodos y devuelven `false`. La clase de diseño, a continuación, puede implementar un proceso personalizado cuando se agregan o quitan elementos secundarios.

<a name="onchildmeasureinvalidated" />

#### <a name="overriding-the-onchildmeasureinvalidated-method"></a>Invalidación del método OnChildMeasureInvalidated

El [ `OnChildMeasureInvalidated` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.OnChildMeasureInvalidated()/) invalidación se invoca cuando uno de los elementos secundarios del diseño cambia de tamaño y se muestra en el ejemplo de código siguiente:

```csharp
protected override void OnChildMeasureInvalidated()
{
  base.OnChildMeasureInvalidated();
  layoutInfoCache.Clear();
}
```

La invalidación invalida el diseño del elemento secundario y descarta toda la información de diseño almacenado en caché.

<a name="consuming" />

### <a name="consuming-the-wraplayout"></a>Consumir el WrapLayout

El `WrapLayout` clase puede utilizarse colocándolo en una [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) tipo derivado, como se muestra en el siguiente ejemplo de código XAML:

```xaml
<ContentPage ... xmlns:local="clr-namespace:ImageWrapLayout">
    <ScrollView Margin="0,20,0,20">
        <local:WrapLayout x:Name="wrapLayout" />
    </ScrollView>
</ContentPage>
```

El código equivalente en C# se muestra a continuación:

```csharp
public class ImageWrapLayoutPageCS : ContentPage
{
  WrapLayout wrapLayout;

  public ImageWrapLayoutPageCS()
  {
    wrapLayout = new WrapLayout();

    Content = new ScrollView
    {
      Margin = new Thickness(0, 20, 0, 20),
      Content = wrapLayout
    };
  }
  ...
}
```

Los elementos secundarios, a continuación, pueden agregarse a la `WrapLayout` según sea necesario. El siguiente ejemplo de código muestra [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) se agregan elementos a la `WrapLayout`:

```csharp
protected override async void OnAppearing()
{
  base.OnAppearing();

  var images = await GetImageListAsync();
  foreach (var photo in images.Photos)
  {
    var image = new Image
    {
      Source = ImageSource.FromUri(new Uri(photo + string.Format("?width={0}&height={0}&mode=max", Device.RuntimePlatform == Device.UWP ? 120 : 240)))
    };
    wrapLayout.Children.Add(image);
  }
}

async Task<ImageList> GetImageListAsync()
{
  var requestUri = "https://docs.xamarin.com/demo/stock.json";
  using (var client = new HttpClient())
  {
    var result = await client.GetStringAsync(requestUri);
    return JsonConvert.DeserializeObject<ImageList>(result);
  }
}
```

Cuando la página que contiene el `WrapLayout` aparece, la aplicación de ejemplo de forma asincrónica tiene acceso a un archivo remoto de JSON que contiene una lista de fotos, crea un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) para cada elemento de fotografías y lo agrega a la `WrapLayout`. El resultado es el aspecto que se muestra en las capturas de pantalla siguiente:

![](custom-images/portait-screenshots.png "Capturas de pantalla de vertical de aplicación de ejemplo")

El siguientes capturas de pantalla se muestra el `WrapLayout` después de que se han girado para orientación horizontal:

![](custom-images/landscape-ios.png "Captura de pantalla de horizontal de aplicación de iOS de ejemplo")
![](custom-images/landscape-android.png "captura de pantalla de ejemplo Android aplicación horizontal") 
 ![ ] (custom-images/landscape-uwp.png " Captura de pantalla de ejemplo UWP aplicación horizontal")

El número de columnas en cada fila depende del tamaño de fotografía, el ancho de pantalla y el número de píxeles por unidad independiente del dispositivo. El [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elementos cargar de forma asincrónica las fotos y, por tanto, la `WrapLayout` clase recibirá llamadas frecuentes a su [ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) método cada `Image` elemento recibe un nuevo tamaño en función de la foto cargada.

## <a name="summary"></a>Resumen

Este artículo se explica cómo escribir una clase de diseño personalizado y muestra un dependientes de la orientación `WrapLayout` clase que sus elementos secundarios se organiza horizontalmente en la página y, a continuación, se ajusta la presentación de los elementos secundarios subsiguientes a filas adicionales.


## <a name="related-links"></a>Vínculos relacionados

- [WrapLayout (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/CustomLayout/WrapLayout/)
- [Diseños personalizados](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter26.md)
- [Crear diseños personalizados en Xamarin.Forms (vídeo)](https://evolve.xamarin.com/session/56e20f83bad314273ca4d81c)
- [Diseño<T>](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/)
- [Diseño](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/)
- [VisualElement](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)
