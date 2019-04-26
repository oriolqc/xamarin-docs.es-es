---
title: Vistas de colección en Xamarin.iOS
description: Las vistas de colección permitir que el contenido se muestren con diseños arbitrarios. Le permiten crear fácilmente los diseños de cuadrícula de fábrica, mientras también se admiten los diseños personalizados.
ms.prod: xamarin
ms.assetid: F4B85F25-0CB5-4FEA-A3B5-D22FCDC81AE4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: f57f2a2bc17690b7a1e0a72c583b0e94519ca4db
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61079444"
---
# <a name="collection-views-in-xamarinios"></a>Vistas de colección en Xamarin.iOS

_Las vistas de colección permitir que el contenido se muestren con diseños arbitrarios. Le permiten crear fácilmente los diseños de cuadrícula de fábrica, mientras también se admiten los diseños personalizados._

Las vistas de colección, disponibles en el `UICollectionView` class, son un concepto nuevo en iOS 6 que introducen presentar varios elementos en la pantalla con los diseños. Los patrones para proporcionar datos a un `UICollectionView` para crear elementos e interactuar con esos elementos, siga el misma delegación y los datos del origen de patrones de uso frecuente en el desarrollo de iOS.

Sin embargo, las vistas de colección funcionan con un subsistema de diseño que es independiente de la `UICollectionView` propio. Por lo tanto, basta con proporcionar un diseño diferente puede cambiar fácilmente la presentación de una vista de colección.

iOS proporciona una clase de diseño denominada `UICollectionViewFlowLayout` que permite diseños basados en línea como una cuadrícula que se creen con ningún trabajo adicional. Además, los diseños personalizados pueden también crearse que permiten a cualquier presentación que pueda imaginar.

## <a name="uicollectionview-basics"></a>Conceptos básicos de UICollectionView

La `UICollectionView` clase se compone de tres elementos diferentes:

-  **Las celdas** – vistas controladas por datos para cada elemento
-  **Vistas adicionales** – controlada por datos vistas asociadas con una sección.
-  **Vistas de decoración** : datos no controlado por las vistas creadas por un diseño

## <a name="cells"></a>Celdas

Las celdas son objetos que representan un único elemento en el conjunto de datos que se presenta la vista de colección. Cada celda es una instancia de la `UICollectionViewCell` (clase), que se compone de tres vistas diferentes, como se muestra en la ilustración siguiente:

 [![](uicollectionview-images/01-uicollectionviewcell.png "Cada celda se compone de tres vistas diferentes, como se muestra aquí")](uicollectionview-images/01-uicollectionviewcell.png#lightbox)

La `UICollectionViewCell` clase tiene las siguientes propiedades para cada una de estas vistas:

-   `ContentView` : Esta vista contiene el contenido que presenta la celda. Se representa en el orden z superior en la pantalla.
-   `SelectedBackgroundView` – Celdas tienen compatibilidad para la selección integrada. Esta vista se usa para indicar visualmente que se ha seleccionado una celda. Se representa justo debajo de la `ContentView` cuando se selecciona una celda.
-   `BackgroundView` : Las celdas también pueden mostrar un fondo, que presenta el `BackgroundView` . Esta vista se representa por debajo de la `SelectedBackgroundView` .


Estableciendo el `ContentView` que sea menor que el `BackgroundView` y `SelectedBackgroundView`, el `BackgroundView` puede usarse para enmarcar visualmente el contenido, mientras el `SelectedBackgroundView` se mostrará cuando se selecciona una celda, como se muestra a continuación:

 [![](uicollectionview-images/02-cells.png "Los elementos de una celda diferente")](uicollectionview-images/02-cells.png#lightbox)

Las celdas de la captura de pantalla anterior se crean mediante la herencia de `UICollectionViewCell` y estableciendo el `ContentView`, `SelectedBackgroundView` y `BackgroundView` propiedades, respectivamente, como se muestra en el código siguiente:

```csharp
public class AnimalCell : UICollectionViewCell
{
        UIImageView imageView;

        [Export ("initWithFrame:")]
        public AnimalCell (CGRect frame) : base (frame)
        {
            BackgroundView = new UIView{BackgroundColor = UIColor.Orange};

            SelectedBackgroundView = new UIView{BackgroundColor = UIColor.Green};

            ContentView.Layer.BorderColor = UIColor.LightGray.CGColor;
            ContentView.Layer.BorderWidth = 2.0f;
            ContentView.BackgroundColor = UIColor.White;
            ContentView.Transform = CGAffineTransform.MakeScale (0.8f, 0.8f);

            imageView = new UIImageView (UIImage.FromBundle ("placeholder.png"));
            imageView.Center = ContentView.Center;
            imageView.Transform = CGAffineTransform.MakeScale (0.7f, 0.7f);

            ContentView.AddSubview (imageView);
        }

        public UIImage Image {
            set {
                imageView.Image = value;
            }
        }
}
```

 <a name="Supplementary_Views" />


## <a name="supplementary-views"></a>Vistas adicionales

Con las vistas son que presentan información asociada con cada sección de un `UICollectionView`. Al igual que las celdas, con las vistas son controladas por datos. Donde las celdas presentan los datos de elemento de un origen de datos, las vistas adicionales presentan la sección de datos, como el género de música en una biblioteca de música o de las categorías de libro en una estantería.

Por ejemplo, una vista con podría usarse para presentar un encabezado para una sección concreta, como se muestra en la ilustración siguiente:

 [![](uicollectionview-images/02a-supplementary-view.png "Una vista adicionales se utiliza para presentar un encabezado para una sección concreta, como se muestra aquí")](uicollectionview-images/02a-supplementary-view.png#lightbox)

Para utilizar una vista adicional, primero debe estar registrado en el `ViewDidLoad` método:

```csharp
CollectionView.RegisterClassForSupplementaryView (typeof(Header), UICollectionElementKindSection.Header, headerId);
```

A continuación, debe devolverse mediante el uso de la vista `GetViewForSupplementaryElement`, creado mediante el uso de `DequeueReusableSupplementaryView`y se hereda de `UICollectionReusableView`. El siguiente fragmento de código generará el SupplementaryView se muestra en la captura de pantalla anterior:

```csharp
public override UICollectionReusableView GetViewForSupplementaryElement (UICollectionView collectionView, NSString elementKind, NSIndexPath indexPath)
        {
            var headerView = (Header)collectionView.DequeueReusableSupplementaryView (elementKind, headerId, indexPath);
            headerView.Text = "Supplementary View";
            return headerView;
        }

```

Vistas adicionales son más genéricas que solo los encabezados y pies de página.
Se puede colocarse en cualquier lugar en la vista de colección y puede estar formados por todas las vistas, que hace su apariencia totalmente personalizable.

 <a name="Decoration_Views" />


## <a name="decoration-views"></a>Vistas de decoración

Decoración las vistas son puramente visual que se pueden mostrar en un `UICollectionView`. A diferencia de las celdas y vistas adicionales, no son controlada por datos. Siempre se crean dentro de subclase de un diseño o puede cambiar posteriormente como el diseño del contenido. Por ejemplo, se podría usar una vista de decoración para presentar una vista en segundo plano que se desplaza con el contenido en el `UICollectionView`, tal y como se muestra a continuación:

 [![](uicollectionview-images/02c-decoration-view.png "Vista de decoración con un fondo rojo")](uicollectionview-images/02c-decoration-view.png#lightbox)

 El siguiente fragmento de código cambia el fondo a rojo en los ejemplos `CircleLayout` clase:

 ```csharp
 public class MyDecorationView : UICollectionReusableView
    {
        [Export ("initWithFrame:")]
        public MyDecorationView (CGRect frame) : base (frame)
        {
            BackgroundColor = UIColor.Red;
        }
    }
 ```


## <a name="data-source"></a>Origen de datos

Al igual que con otras partes de iOS, como `UITableView` y `MKMapView`, `UICollectionView` obtiene sus datos desde un *origen de datos*, que se expone en Xamarin.iOS a través de la **`UICollectionViewDataSource`** clase. Esta clase es responsable de proporcionar contenido a los `UICollectionView` como:

-  **Las celdas** – devuelto desde `GetCell` método.
-  **Vistas adicionales** – devuelto desde `GetViewForSupplementaryElement` método.
-  **Número de secciones** – devuelto desde `NumberOfSections` método. El valor predeterminado es 1 si no ha implementado.
-  **Número de elementos por sección** – devuelto desde `GetItemsCount` método.

### <a name="uicollectionviewcontroller"></a>UICollectionViewController
Para mayor comodidad, la `UICollectionViewController` clase está disponible. Esto se configura automáticamente para que sea tanto el delegado, que se describe en la sección siguiente, y del origen de datos para su `UICollectionView` vista.

Igual que con `UITableView`, el `UICollectionView` clase solo llamará a su origen de datos para obtener las celdas de elementos que se encuentran en la pantalla.
Las celdas que se desplazan fuera de la pantalla se colocan en a una cola para su reutilización, como se muestra en la siguiente imagen:

 [![](uicollectionview-images/03-cell-reuse.png "Las celdas que se desplazan fuera de la pantalla se colocan en a una cola para su reutilización como se muestra aquí")](uicollectionview-images/03-cell-reuse.png#lightbox)

Se ha simplificado la reutilización de la celda con `UICollectionView` y `UITableView`. Ya no necesita crear una celda directamente en el origen de datos si aún no está disponible en la cola de reutilización, como las celdas se registran con el sistema. Si una celda no está disponible al realizar la llamada a la celda de la cola de reutilización de la cola, iOS creará automáticamente según el tipo o nib que se registró.
También está disponible para las vistas con la misma técnica.

Por ejemplo, considere el siguiente código que registra el `AnimalCell` clase:

```csharp
static NSString animalCellId = new NSString ("AnimalCell");
CollectionView.RegisterClassForCell (typeof(AnimalCell), animalCellId);
```

Cuando un `UICollectionView` necesita una celda porque el elemento está en la pantalla, el `UICollectionView` llama a su origen de datos `GetCell` método. Al igual que su funcionamiento con UITableView, este método es responsable de configurar una celda de los datos de respaldo, que serían un `AnimalCell` clase en este caso.

El código siguiente muestra una implementación de `GetCell` que devuelve un `AnimalCell` instancia:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, Foundation.NSIndexPath indexPath)
{
        var animalCell = (AnimalCell)collectionView.DequeueReusableCell (animalCellId, indexPath);

        var animal = animals [indexPath.Row];

        animalCell.Image = animal.Image;

        return animalCell;
}
```

La llamada a `DequeReusableCell` es donde la celda se pondrá o anular en cola de la cola de reutilización o, si una celda no está disponible en la cola, se crea según el tipo registrado en la llamada a `CollectionView.RegisterClassForCell`.

En este caso, al registrar el `AnimalCell` (clase), se creará un nuevo iOS `AnimalCell` internamente y se devuelve cuando se realiza una llamada a una celda de la cola, tras el cual se configura con la imagen contenida en la clase animal y devuelven para su visualización a la `UICollectionView`.

 <a name="Delegate" />


### <a name="delegate"></a>delegado

El `UICollectionView` clase utiliza un delegado del tipo `UICollectionViewDelegate` para admitir la interacción con los contenidos de la `UICollectionView`. Esto permite el control de:

-  **Selección de celda** : determina si se ha seleccionado una celda.
-  **Resaltado de celda** : determina si una celda actualmente se está tocando.
-  **Los menús de celda** : menú que se muestra para una celda en respuesta a un gesto de presión prolongada.


Al igual que con el origen de datos, el `UICollectionViewController` está configurado de forma predeterminada como el delegado para el `UICollectionView`.

 <a name="Cell_HighLighting" />


#### <a name="cell-highlighting"></a>Resaltado de celda

Cuando se presiona una celda, las transiciones de celda en un estado de resaltado, y no está seleccionada esta opción hasta que el usuario levanta el dedo hacia de la celda. Esto permite un cambio temporal en la apariencia de la celda antes de que realmente está seleccionada. Tras la del selección, la celda `SelectedBackgroundView` se muestra. La siguiente ilustración muestra el estado resaltado justo antes de que la selección:

 [![](uicollectionview-images/04-cell-highlight.png "Esta ilustración muestra el estado resaltado justo antes de que la selección")](uicollectionview-images/04-cell-highlight.png#lightbox)

Para implementar el resaltado, el `ItemHighlighted` y `ItemUnhighlighted` métodos de la `UICollectionViewDelegate` puede utilizarse. Por ejemplo, el código siguiente aplicará un fondo amarillo de la `ContentView` cuando se resalta la celda y un fondo blanco sin resaltado, tal como se muestra en la imagen anterior:

```csharp
public override void ItemHighlighted (UICollectionView collectionView, NSIndexPath indexPath)
{
        var cell = collectionView.CellForItem(indexPath);
        cell.ContentView.BackgroundColor = UIColor.Yellow;
}

public override void ItemUnhighlighted (UICollectionView collectionView, NSIndexPath indexPath)
{
        var cell = collectionView.CellForItem(indexPath);
        cell.ContentView.BackgroundColor = UIColor.White;
}
```

 <a name="Disabling_Selection" />


#### <a name="disabling-selection"></a>Deshabilitar selección

La selección está habilitada de forma predeterminada en `UICollectionView`. Para deshabilitar la selección, invalidar `ShouldHighlightItem` y devolver false como se muestra a continuación:

```csharp
public override bool ShouldHighlightItem (UICollectionView collectionView, NSIndexPath indexPath)
{
        return false;
}
```

Cuando se deshabilita el resaltado, también se deshabilita el proceso de selección de una celda. Además, hay también un `ShouldSelectItem` método que controla la selección directamente, aunque si `ShouldHighlightItem` se implementa y devuelve false, `ShouldSelectItem` no se llama.

 `ShouldSelectItem` permite la selección a estar activado o desactivado de forma del elemento por elemento, cuando `ShouldHighlightItem` no está implementada. También permite resaltar sin seleccionar, si `ShouldHighlightItem` se implementa y devuelve true mientras `ShouldSelectItem` devuelve false.

 <a name="Cell_Menus" />


#### <a name="cell-menus"></a>Menús de celda

Cada celda de un `UICollectionView` es capaz de mostrar un menú que permite cortar, copiar y pegar que, opcionalmente, se deben admitir. Para crear un menú de edición en una celda:

1.  Invalidar `ShouldShowMenu` y devolver true si el elemento debe mostrar un menú.
1.  Invalidar `CanPerformAction` y devolver true para todas las acciones que puede llevar a cabo el elemento, que será cualquiera de cortar, copiar o pegar.
1.  Invalidar `PerformAction` para llevar a cabo la edición, copia de la operación de pegado.


Captura de pantalla siguiente muestra el menú cuando una celda de cuánto tiempo se presiona:

 [![](uicollectionview-images/04a-menu.png "Esta captura de pantalla muestra el menú cuando una celda de cuánto tiempo se presiona")](uicollectionview-images/04a-menu.png#lightbox)

 <a name="Layout" />


## <a name="layout"></a>Diseño

`UICollectionView` admite un sistema de diseño que permite la colocación de todos sus elementos, las celdas, con vistas y vistas de decoración, administrarse independientemente de la `UICollectionView` propio.
Con el sistema de diseño, una aplicación puede admitir diseños, como la cuadrícula como hemos visto en este artículo, así como proporcionar diseños personalizados.

 <a name="Layout_Basics" />


### <a name="layout-basics"></a>Conceptos básicos de diseño

Los diseños de un `UICollectionView` se definen en una clase que hereda de `UICollectionViewLayout`. Es responsable de crear los atributos de diseño para todos los elementos de la implementación del diseño el `UICollectionView`. Hay dos maneras de crear un diseño:

-  Usar la integrada `UICollectionViewFlowLayout` .
-  Proporcionar un diseño personalizado heredando de `UICollectionViewLayout` .


 <a name="Flow_Layout" />


### <a name="flow-layout"></a>Diseño de flujo

La `UICollectionViewFlowLayout` clase proporciona un diseño basado en línea es adecuado para organizar contenido en una cuadrícula de celdas, como hemos visto.

Para usar un diseño de flujo:

-  Cree una instancia de `UICollectionViewFlowLayout` :


```csharp
var layout = new UICollectionViewFlowLayout ();
```

-  Pase la instancia al constructor de la `UICollectionView` :


```csharp
simpleCollectionViewController = new SimpleCollectionViewController (layout);
```

Esto es todo lo que es necesario para el contenido de diseño en una cuadrícula. Además, cuando cambia la orientación, la `UICollectionViewFlowLayout` controla reorganizar el contenido adecuadamente, tal como se muestra a continuación:

 [![](uicollectionview-images/05-layout-orientation.png "Ejemplo de los cambios de orientación")](uicollectionview-images/05-layout-orientation.png#lightbox)

 <a name="Section_Inset" />


#### <a name="section-inset"></a>Sección bajorrelieve

Para proporcionar algo de espacio alrededor del `UIContentView`, diseños tienen un `SectionInset` propiedad de tipo `UIEdgeInsets`. Por ejemplo, el código siguiente proporciona un búfer de 50 píxeles alrededor de cada sección de la `UIContentView` cuando planteados por un `UICollectionViewFlowLayout`:

```csharp
var layout = new UICollectionViewFlowLayout ();
layout.SectionInset = new UIEdgeInsets (50,50,50,50);
```

Esto da como resultado espaciado alrededor de la sección tal como se muestra a continuación:

 [![](uicollectionview-images/06-sectioninset.png "Espaciado alrededor de la sección, como se muestra aquí")](uicollectionview-images/06-sectioninset.png#lightbox)

 <a name="Subclassing_UICollectionViewFlowLayout" />


#### <a name="subclassing-uicollectionviewflowlayout"></a>Creación de subclases de UICollectionViewFlowLayout

En las ediciones al uso `UICollectionViewFlowLayout` directamente, también se puede crear subclases para personalizar aún más el diseño del contenido a lo largo de una línea. Por ejemplo, esto puede usarse para crear un diseño que no se ajustan las celdas en una cuadrícula, pero en su lugar, se crea una sola fila con un efecto de desplazamiento horizontal, tal como se muestra a continuación:

 [![](uicollectionview-images/07-line-layout.png "Una sola fila con un efecto de desplazamiento horizontal")](uicollectionview-images/07-line-layout.png#lightbox)

Para implementar esto mediante la creación de subclases `UICollectionViewFlowLayout` requiere:

-  Inicializar las propiedades de diseño que se aplican al diseño del propio o todos los elementos del diseño en el constructor.
-  Reemplazar `ShouldInvalidateLayoutForBoundsChange` , y devuelve true, que, cuando los límites de la `UICollectionView` cambios, el diseño de las celdas se volverá a calcular. Esto se usa en este caso garantizar el código para que se aplicará la transformación aplicada a la celda de promedio durante el desplazamiento.
-  Reemplazar `TargetContentOffset` para hacer el promedio ajustar en el centro de la celda del `UICollectionView` como se detiene el desplazamiento.
-  Reemplazar `LayoutAttributesForElementsInRect` para devolver una matriz de `UICollectionViewLayoutAttributes` . Cada `UICollectionViewLayoutAttribute` contiene información sobre cómo el diseño del elemento concreto, incluidas las propiedades como su `Center` , `Size` , `ZIndex` y `Transform3D` .


El código siguiente muestra este tipo de implementación:

```csharp
using System;
using CoreGraphics;
using Foundation;
using UIKit;
using CoreGraphics;
using CoreAnimation;

namespace SimpleCollectionView
{
    public class LineLayout : UICollectionViewFlowLayout
    {
        public const float ITEM_SIZE = 200.0f;
        public const int ACTIVE_DISTANCE = 200;
        public const float ZOOM_FACTOR = 0.3f;

        public LineLayout ()
        {
            ItemSize = new CGSize (ITEM_SIZE, ITEM_SIZE);
            ScrollDirection = UICollectionViewScrollDirection.Horizontal;
            SectionInset = new UIEdgeInsets (400,0,400,0);
            MinimumLineSpacing = 50.0f;
        }

        public override bool ShouldInvalidateLayoutForBoundsChange (CGRect newBounds)
        {
            return true;
        }

        public override UICollectionViewLayoutAttributes[] LayoutAttributesForElementsInRect (CGRect rect)
        {
            var array = base.LayoutAttributesForElementsInRect (rect);
            var visibleRect = new CGRect (CollectionView.ContentOffset, CollectionView.Bounds.Size);

            foreach (var attributes in array) {
                if (attributes.Frame.IntersectsWith (rect)) {
                    float distance = (float)(visibleRect.GetMidX () - attributes.Center.X);
                    float normalizedDistance = distance / ACTIVE_DISTANCE;
                    if (Math.Abs (distance) < ACTIVE_DISTANCE) {
                        float zoom = 1 + ZOOM_FACTOR * (1 - Math.Abs (normalizedDistance));
                        attributes.Transform3D = CATransform3D.MakeScale (zoom, zoom, 1.0f);
                        attributes.ZIndex = 1;
                    }
                }
            }
            return array;
        }

        public override CGPoint TargetContentOffset (CGPoint proposedContentOffset, CGPoint scrollingVelocity)
        {
            float offSetAdjustment = float.MaxValue;
            float horizontalCenter = (float)(proposedContentOffset.X + (this.CollectionView.Bounds.Size.Width / 2.0));
            CGRect targetRect = new CGRect (proposedContentOffset.X, 0.0f, this.CollectionView.Bounds.Size.Width, this.CollectionView.Bounds.Size.Height);
            var array = base.LayoutAttributesForElementsInRect (targetRect);
            foreach (var layoutAttributes in array) {
                float itemHorizontalCenter = (float)layoutAttributes.Center.X;
                if (Math.Abs (itemHorizontalCenter - horizontalCenter) < Math.Abs (offSetAdjustment)) {
                    offSetAdjustment = itemHorizontalCenter - horizontalCenter;
                }
            }
            return new CGPoint (proposedContentOffset.X + offSetAdjustment, proposedContentOffset.Y);
        }

    }
}
```

 <a name="Custom_Layout" />


### <a name="custom-layout"></a>Diseño personalizado

Además de usar `UICollectionViewFlowLayout`, también se pueden personalizar por completo diseños al heredar directamente de `UICollectionViewLayout`.

Los métodos principales para invalidar son:

-   `PrepareLayout` : Se usa para realizar cálculos geométricos iniciales que se usará durante el proceso de diseño.
-   `CollectionViewContentSize` : Devuelve el tamaño del área utilizada para mostrar el contenido.
-   `LayoutAttributesForElementsInRect` – Como con el ejemplo UICollectionViewFlowLayout mostrado anteriormente, este método se usa para proporcionar información a la `UICollectionView` respecto a cómo el diseño de cada elemento. Sin embargo, a diferencia del `UICollectionViewFlowLayout` , al crear un diseño personalizado, puede colocar los elementos que desee.


Por ejemplo, podría presentarse el mismo contenido en un diseño circular tal como se muestra a continuación:

 [![](uicollectionview-images/08-circle-layout.png "Circular personalizada diseño como se muestra aquí")](uicollectionview-images/08-circle-layout.png#lightbox)

Lo eficaz de diseños es que cambiar desde el diseño de cuadrícula a un diseño de desplazamiento horizontal y posteriormente a este diseño circular requiere solo la clase de diseño proporcionada para el `UICollectionView` se puede cambiar. No hay nada en el `UICollectionView`, su delegado o datos del origen de los cambios de código en absoluto.


## <a name="changes-in-ios-9"></a>Cambios en iOS 9


En iOS 9, la vista de colección (`UICollectionView`) ahora admite arrastra la reordenación de elementos desde el principio mediante la adición de un reconocedor de movimiento predeterminado nuevo y varios métodos auxiliares de nuevo.

Con estos nuevos métodos, puede implementar fácilmente arrastrar para cambiar el orden en la vista de colección y tiene la opción de personalizar la apariencia de los elementos durante cualquier fase del proceso de reordenación.

[![](uicollectionview-images/intro01.png "Un ejemplo del proceso de reordenación")](uicollectionview-images/intro01.png#lightbox)

En este artículo, le echamos un vistazo a la implementación de arrastrar para volver a ordenar en una aplicación de Xamarin.iOS, así como algunos de los otros cambios iOS 9 ha realizado en el control de vista de colección:

- [Fácil de reordenación de elementos](#Easy-Reordering-of-Items)
    - [Ejemplo sencillo de reordenación](#Simple-Reordering-Example)
    - [Uso de un reconocedor de gestos personalizados](#Using-a-Custom-Gesture-Recognizer)
    - [Los diseños personalizados y la reordenación de](#Custom-Layouts-and-Reording)
- [Cambios de la vista de colección](#collection-view-changes)

<a name="Easy-Reordering-of-Items" />

## <a name="reordering-of-items"></a>Reordenación de elementos

Como se indicó anteriormente, uno de los cambios más importantes a la vista de colección en iOS 9 fue la adición de funcionalidad de arrastrar para volver a ordenar sencilla desde el principio.

En iOS 9, la forma más rápida para agregar la reordenación de una vista de colección es usar un `UICollectionViewController`.
El controlador de vista de colección tiene ahora un `InstallsStandardGestureForInteractiveMovement` propiedad, que agrega un estándar *reconocedor de gestos* que admite operaciones de arrastre para reordenar los elementos de la colección.
Puesto que el valor predeterminado es `true`, solo tiene que implementar el `MoveItem` método de la `UICollectionViewDataSource` clase para admitir arrastrar para volver a ordenar. Por ejemplo:

```csharp
public override void MoveItem (UICollectionView collectionView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
{
    // Reorder our list of items
    ...
}
```
<a name="Simple-Reordering-Example" />

### <a name="simple-reordering-example"></a>Ejemplo sencillo de reordenación

Un ejemplo rápido, inicie un nuevo proyecto de Xamarin.iOS y editar la **Main.storyboard** archivo. Arrastre un `UICollectionViewController` a la superficie de diseño:

[![](uicollectionview-images/quick01.png "Agregar un UICollectionViewController")](uicollectionview-images/quick01.png#lightbox)

Seleccione la vista de colección (puede ser más fácil de hacerlo desde el esquema del documento). En la pestaña diseño del panel de propiedades, establezca los siguientes tamaños, como se muestra en la captura de pantalla siguiente:

- **Tamaño de la celda**: Ancho: 60 | Alto: 60
- **Tamaño del encabezado**: Ancho: 0 | Alto: 0
- **Tamaño del pie de página**: Ancho: 0 | Alto: 0
- **Espaciado mínimo**: Para las celdas – 8 | Líneas: 8
- **Sección de márgenes**: Parte superior: 16 | Inferior: 16 | Izquierda: 16 | Derecha: 16

[![](uicollectionview-images/quick04.png "Establecer los tamaños de la vista de colección")](uicollectionview-images/quick04.png#lightbox)

A continuación, edite el valor predeterminado de celda:
    - Cambiar el color de fondo en azul
    - Agregue una etiqueta para que actúe como el título de la celda
    - Establece el identificador de reutilización en **celda**

[![](uicollectionview-images/quick02.png "Editar el valor predeterminado de celda")](uicollectionview-images/quick02.png#lightbox)

Agregar restricciones para mantener la etiqueta a centrar dentro de la celda cuando se produzcan cambios de tamaño:

En el **panel propiedad** para el _CollectionViewCell_ y establezca el **clase** a `TextCollectionViewCell`:

[![](uicollectionview-images/quick05.png "Defina la clase como TextCollectionViewCell")](uicollectionview-images/quick05.png#lightbox)

Establecer el **vista de colección reutilizable** a `Cell`:

[![](uicollectionview-images/quick06.png "Establece la vista de colección reutilizable en la celda")](uicollectionview-images/quick06.png#lightbox)

Por último, seleccione la etiqueta y asígnele el nombre `TextLabel`:

[![](uicollectionview-images/quick07.png "nombre de etiqueta TextLabel")](uicollectionview-images/quick07.png#lightbox)

Editar la `TextCollectionViewCell` de clases y agregue las siguientes propiedades.:

```csharp
using System;
using Foundation;
using UIKit;

namespace CollectionView
{
    public partial class TextCollectionViewCell : UICollectionViewCell
    {
        #region Computed Properties
        public string Title {
            get { return TextLabel.Text; }
            set { TextLabel.Text = value; }
        }
        #endregion

        #region Constructors
        public TextCollectionViewCell (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

Aquí el `Text` propiedad de la etiqueta se expone como el título de la celda, por lo que puede establecerse desde el código.

Agregue un nuevo C# clase al proyecto y llámelo `WaterfallCollectionSource`. Edite el archivo y dele un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;

namespace CollectionView
{
    public class WaterfallCollectionSource : UICollectionViewDataSource
    {
        #region Computed Properties
        public WaterfallCollectionView CollectionView { get; set;}
        public List<int> Numbers { get; set; } = new List<int> ();
        #endregion

        #region Constructors
        public WaterfallCollectionSource (WaterfallCollectionView collectionView)
        {
            // Initialize
            CollectionView = collectionView;

            // Init numbers collection
            for (int n = 0; n < 100; ++n) {
                Numbers.Add (n);
            }
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView) {
            // We only have one section
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section) {
            // Return the number of items
            return Numbers.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get a reusable cell and set {~~it's~>its~~} title from the item
            var cell = collectionView.DequeueReusableCell ("Cell", indexPath) as TextCollectionViewCell;
            cell.Title = Numbers [(int)indexPath.Item].ToString();

            return cell;
        }

        public override bool CanMoveItem (UICollectionView collectionView, NSIndexPath indexPath) {
            // We can always move items
            return true;
        }

        public override void MoveItem (UICollectionView collectionView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
        {
            // Reorder our list of items
            var item = Numbers [(int)sourceIndexPath.Item];
            Numbers.RemoveAt ((int)sourceIndexPath.Item);
            Numbers.Insert ((int)destinationIndexPath.Item, item);
        }
        #endregion
    }
}
```

Esta clase se el origen de datos para la vista de colección y proporcione la información para cada celda de la colección.
Tenga en cuenta que el `MoveItem` método se implementa para permitir arrastrar elementos de la colección sea reordenados.

Agregue otra nueva C# clase al proyecto y llámelo `WaterfallCollectionDelegate`. Edite este archivo y dele un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;

namespace CollectionView
{
    public class WaterfallCollectionDelegate : UICollectionViewDelegate
    {
        #region Computed Properties
        public WaterfallCollectionView CollectionView { get; set;}
        #endregion

        #region Constructors
        public WaterfallCollectionDelegate (WaterfallCollectionView collectionView)
        {

            // Initialize
            CollectionView = collectionView;

        }
        #endregion

        #region Overrides Methods
        public override bool ShouldHighlightItem (UICollectionView collectionView, NSIndexPath indexPath) {
            // Always allow for highlighting
            return true;
        }

        public override void ItemHighlighted (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get cell and change to green background
            var cell = collectionView.CellForItem(indexPath);
            cell.ContentView.BackgroundColor = UIColor.FromRGB(183,208,57);
        }

        public override void ItemUnhighlighted (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get cell and return to blue background
            var cell = collectionView.CellForItem(indexPath);
            cell.ContentView.BackgroundColor = UIColor.FromRGB(164,205,255);
        }
        #endregion
    }
}
```

Esto actuará como el delegado para nuestra vista de colección. Se han reemplazado los métodos para resaltar una celda cuando el usuario interactúa con él en la vista de colección.

Agregue un último C# clase al proyecto y llámelo `WaterfallCollectionView`. Edite este archivo y dele un aspecto similar al siguiente:

```csharp
using System;
using UIKit;
using System.Collections.Generic;
using Foundation;

namespace CollectionView
{
    [Register("WaterfallCollectionView")]
    public class WaterfallCollectionView : UICollectionView
    {

        #region Constructors
        public WaterfallCollectionView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Initialize
            DataSource = new WaterfallCollectionSource(this);
            Delegate = new WaterfallCollectionDelegate(this);

        }
        #endregion
    }
}
```

Tenga en cuenta que `DataSource` y `Delegate` que creamos anteriormente se establecen cuando se construye la vista de colección de su guión gráfico (o **.xib** archivo).

Editar el **Main.storyboard** archivo nuevo y seleccione la vista de colección y cambie a la **propiedades**. Establecer el **clase** a personalizado `WaterfallCollectionView` clase que definimos anteriormente:

Guarde los cambios realizados en la interfaz de usuario y ejecutar la aplicación.
Si el usuario selecciona un elemento de la lista y lo arrastra a una nueva ubicación, los demás elementos se animan automáticamente cuando se mueven fuera de la vista del elemento.
Cuando el usuario coloca el elemento en una nueva ubicación, permanecerá en esa ubicación. Por ejemplo:

[![](uicollectionview-images/intro01.png "Un ejemplo de arrastrar un elemento a una nueva ubicación")](uicollectionview-images/intro01.png#lightbox)

<a name="Using-a-Custom-Gesture-Recognizer" />

### <a name="using-a-custom-gesture-recognizer"></a>Uso de un reconocedor de gestos personalizados

En casos donde no se puede usar un `UICollectionViewController` y debe usar normal `UIViewController`, o si desea tener más control sobre el gesto de arrastrar y colocar, puede crear su propio reconocedor de gestos personalizado y agregarlo a la vista de colección cuando se cargue la vista. Por ejemplo:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Create a custom gesture recognizer
    var longPressGesture = new UILongPressGestureRecognizer ((gesture) => {

        // Take action based on state
        switch(gesture.State) {
        case UIGestureRecognizerState.Began:
            var selectedIndexPath = CollectionView.IndexPathForItemAtPoint(gesture.LocationInView(View));
            if (selectedIndexPath !=null) {
                CollectionView.BeginInteractiveMovementForItem(selectedIndexPath);
            }
            break;
        case UIGestureRecognizerState.Changed:
            CollectionView.UpdateInteractiveMovementTargetPosition(gesture.LocationInView(View));
            break;
        case UIGestureRecognizerState.Ended:
            CollectionView.EndInteractiveMovement();
            break;
        default:
            CollectionView.CancelInteractiveMovement();
            break;
        }

    });

    // Add the custom recognizer to the collection view
    CollectionView.AddGestureRecognizer(longPressGesture);
}
```

Aquí vamos a usar varios métodos nuevos que se agregan a la vista de colección para implementar y controlar la operación de arrastre:

 - `BeginInteractiveMovementForItem` -Marca el inicio de una operación de movimiento.
 - `UpdateInteractiveMovementTargetPosition` -Se envía cuando se actualiza la ubicación del elemento.
 - `EndInteractiveMovement` -Marca el final de un elemento se mueva.
 - `CancelInteractiveMovement` : Marca el usuario canceló la operación de traslado.

Cuando se ejecuta la aplicación, la operación de arrastre funcionará exactamente igual que el valor predeterminado arrastre reconocedor de gestos que se incluye con la vista de colección.

<a name="Custom-Layouts-and-Reording" />

### <a name="custom-layouts-and-reordering"></a>Los diseños personalizados y la reordenación de

En iOS 9, se agregaron varios nuevos métodos para trabajar con diseños de arrastre para volver a ordenar y personalizados en una vista de colección. Para explorar esta característica, vamos a agregar un diseño personalizado a la colección.

En primer lugar, agregue un nuevo C# clase llamada `WaterfallCollectionLayout` al proyecto. Edítelo y darle un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;
using CoreGraphics;

namespace CollectionView
{
    [Register("WaterfallCollectionLayout")]
    public class WaterfallCollectionLayout : UICollectionViewLayout
    {
        #region Private Variables
        private int columnCount = 2;
        private nfloat minimumColumnSpacing = 10;
        private nfloat minimumInterItemSpacing = 10;
        private nfloat headerHeight = 0.0f;
        private nfloat footerHeight = 0.0f;
        private UIEdgeInsets sectionInset = new UIEdgeInsets(0, 0, 0, 0);
        private WaterfallCollectionRenderDirection itemRenderDirection = WaterfallCollectionRenderDirection.ShortestFirst;
        private Dictionary<nint,UICollectionViewLayoutAttributes> headersAttributes = new Dictionary<nint, UICollectionViewLayoutAttributes>();
        private Dictionary<nint,UICollectionViewLayoutAttributes> footersAttributes = new Dictionary<nint, UICollectionViewLayoutAttributes>();
        private List<CGRect> unionRects = new List<CGRect>();
        private List<nfloat> columnHeights = new List<nfloat>();
        private List<UICollectionViewLayoutAttributes> allItemAttributes = new List<UICollectionViewLayoutAttributes>();
        private List<List<UICollectionViewLayoutAttributes>> sectionItemAttributes = new List<List<UICollectionViewLayoutAttributes>>();
        private nfloat unionSize = 20;
        #endregion

        #region Computed Properties
        [Export("ColumnCount")]
        public int ColumnCount {
            get { return columnCount; }
            set {
                WillChangeValue ("ColumnCount");
                columnCount = value;
                DidChangeValue ("ColumnCount");

                InvalidateLayout ();
            }
        }

        [Export("MinimumColumnSpacing")]
        public nfloat MinimumColumnSpacing {
            get { return minimumColumnSpacing; }
            set {
                WillChangeValue ("MinimumColumnSpacing");
                minimumColumnSpacing = value;
                DidChangeValue ("MinimumColumnSpacing");

                InvalidateLayout ();
            }
        }

        [Export("MinimumInterItemSpacing")]
        public nfloat MinimumInterItemSpacing {
            get { return minimumInterItemSpacing; }
            set {
                WillChangeValue ("MinimumInterItemSpacing");
                minimumInterItemSpacing = value;
                DidChangeValue ("MinimumInterItemSpacing");

                InvalidateLayout ();
            }
        }

        [Export("HeaderHeight")]
        public nfloat HeaderHeight {
            get { return headerHeight; }
            set {
                WillChangeValue ("HeaderHeight");
                headerHeight = value;
                DidChangeValue ("HeaderHeight");

                InvalidateLayout ();
            }
        }

        [Export("FooterHeight")]
        public nfloat FooterHeight {
            get { return footerHeight; }
            set {
                WillChangeValue ("FooterHeight");
                footerHeight = value;
                DidChangeValue ("FooterHeight");

                InvalidateLayout ();
            }
        }

        [Export("SectionInset")]
        public UIEdgeInsets SectionInset {
            get { return sectionInset; }
            set {
                WillChangeValue ("SectionInset");
                sectionInset = value;
                DidChangeValue ("SectionInset");

                InvalidateLayout ();
            }
        }

        [Export("ItemRenderDirection")]
        public WaterfallCollectionRenderDirection ItemRenderDirection {
            get { return itemRenderDirection; }
            set {
                WillChangeValue ("ItemRenderDirection");
                itemRenderDirection = value;
                DidChangeValue ("ItemRenderDirection");

                InvalidateLayout ();
            }
        }
        #endregion

        #region Constructors
        public WaterfallCollectionLayout ()
        {
        }

        public WaterfallCollectionLayout(NSCoder coder) : base(coder) {

        }
        #endregion

        #region Public Methods
        public nfloat ItemWidthInSectionAtIndex(int section) {

            var width = CollectionView.Bounds.Width - SectionInset.Left - SectionInset.Right;
            return (nfloat)Math.Floor ((width - ((ColumnCount - 1) * MinimumColumnSpacing)) / ColumnCount);
        }
        #endregion

        #region Override Methods
        public override void PrepareLayout ()
        {
            base.PrepareLayout ();

            // Get the number of sections
            var numberofSections = CollectionView.NumberOfSections();
            if (numberofSections == 0)
                return;

            // Reset collections
            headersAttributes.Clear ();
            footersAttributes.Clear ();
            unionRects.Clear ();
            columnHeights.Clear ();
            allItemAttributes.Clear ();
            sectionItemAttributes.Clear ();

            // Initialize column heights
            for (int n = 0; n < ColumnCount; n++) {
                columnHeights.Add ((nfloat)0);
            }

            // Process all sections
            nfloat top = 0.0f;
            var attributes = new UICollectionViewLayoutAttributes ();
            var columnIndex = 0;
            for (nint section = 0; section < numberofSections; ++section) {
                // Calculate section specific metrics
                var minimumInterItemSpacing = (MinimumInterItemSpacingForSection == null) ? MinimumColumnSpacing :
                    MinimumInterItemSpacingForSection (CollectionView, this, section);

                // Calculate widths
                var width = CollectionView.Bounds.Width - SectionInset.Left - SectionInset.Right;
                var itemWidth = (nfloat)Math.Floor ((width - ((ColumnCount - 1) * MinimumColumnSpacing)) / ColumnCount);

                // Calculate section header
                var heightHeader = (HeightForHeader == null) ? HeaderHeight :
                    HeightForHeader (CollectionView, this, section);

                if (heightHeader > 0) {
                    attributes = UICollectionViewLayoutAttributes.CreateForSupplementaryView (UICollectionElementKindSection.Header, NSIndexPath.FromRowSection (0, section));
                    attributes.Frame = new CGRect (0, top, CollectionView.Bounds.Width, heightHeader);
                    headersAttributes.Add (section, attributes);
                    allItemAttributes.Add (attributes);

                    top = attributes.Frame.GetMaxY ();
                }

                top += SectionInset.Top;
                for (int n = 0; n < ColumnCount; n++) {
                    columnHeights [n] = top;
                }

                // Calculate Section Items
                var itemCount = CollectionView.NumberOfItemsInSection(section);
                List<UICollectionViewLayoutAttributes> itemAttributes = new List<UICollectionViewLayoutAttributes> ();

                for (nint n = 0; n < itemCount; n++) {
                    var indexPath = NSIndexPath.FromRowSection (n, section);
                    columnIndex = NextColumnIndexForItem (n);
                    var xOffset = SectionInset.Left + (itemWidth + MinimumColumnSpacing) * (nfloat)columnIndex;
                    var yOffset = columnHeights [columnIndex];
                    var itemSize = (SizeForItem == null) ? new CGSize (0, 0) : SizeForItem (CollectionView, this, indexPath);
                    nfloat itemHeight = 0.0f;

                    if (itemSize.Height > 0.0f && itemSize.Width > 0.0f) {
                        itemHeight = (nfloat)Math.Floor (itemSize.Height * itemWidth / itemSize.Width);
                    }

                    attributes = UICollectionViewLayoutAttributes.CreateForCell (indexPath);
                    attributes.Frame = new CGRect (xOffset, yOffset, itemWidth, itemHeight);
                    itemAttributes.Add (attributes);
                    allItemAttributes.Add (attributes);
                    columnHeights [columnIndex] = attributes.Frame.GetMaxY () + MinimumInterItemSpacing;
                }
                sectionItemAttributes.Add (itemAttributes);

                // Calculate Section Footer
                nfloat footerHeight = 0.0f;
                columnIndex = LongestColumnIndex();
                top = columnHeights [columnIndex] - MinimumInterItemSpacing + SectionInset.Bottom;
                footerHeight = (HeightForFooter == null) ? FooterHeight : HeightForFooter(CollectionView, this, section);

                if (footerHeight > 0) {
                    attributes = UICollectionViewLayoutAttributes.CreateForSupplementaryView (UICollectionElementKindSection.Footer, NSIndexPath.FromRowSection (0, section));
                    attributes.Frame = new CGRect (0, top, CollectionView.Bounds.Width, footerHeight);
                    footersAttributes.Add (section, attributes);
                    allItemAttributes.Add (attributes);
                    top = attributes.Frame.GetMaxY ();
                }

                for (int n = 0; n < ColumnCount; n++) {
                    columnHeights [n] = top;
                }
            }

            var i =0;
            var attrs = allItemAttributes.Count;
            while(i < attrs) {
                var rect1 = allItemAttributes [i].Frame;
                i = (int)Math.Min (i + unionSize, attrs) - 1;
                var rect2 = allItemAttributes [i].Frame;
                unionRects.Add (CGRect.Union (rect1, rect2));
                i++;
            }

        }

        public override CGSize CollectionViewContentSize {
            get {
                if (CollectionView.NumberOfSections () == 0) {
                    return new CGSize (0, 0);
                }

                var contentSize = CollectionView.Bounds.Size;
                contentSize.Height = columnHeights [0];
                return contentSize;
            }
        }

        public override UICollectionViewLayoutAttributes LayoutAttributesForItem (NSIndexPath indexPath)
        {
            if (indexPath.Section >= sectionItemAttributes.Count) {
                return null;
            }

            if (indexPath.Item >= sectionItemAttributes [indexPath.Section].Count) {
                return null;
            }

            var list = sectionItemAttributes [indexPath.Section];
            return list [(int)indexPath.Item];
        }

        public override UICollectionViewLayoutAttributes LayoutAttributesForSupplementaryView (NSString kind, NSIndexPath indexPath)
        {
            var attributes = new UICollectionViewLayoutAttributes ();

            switch (kind) {
            case "header":
                attributes = headersAttributes [indexPath.Section];
                break;
            case "footer":
                attributes = footersAttributes [indexPath.Section];
                break;
            }

            return attributes;
        }

        public override UICollectionViewLayoutAttributes[] LayoutAttributesForElementsInRect (CGRect rect)
        {
            var begin = 0;
            var end = unionRects.Count;
            List<UICollectionViewLayoutAttributes> attrs = new List<UICollectionViewLayoutAttributes> ();


            for (int i = 0; i < end; i++) {
                if (rect.IntersectsWith(unionRects[i])) {
                    begin = i * (int)unionSize;
                }
            }

            for (int i = end - 1; i >= 0; i--) {
                if (rect.IntersectsWith (unionRects [i])) {
                    end = (int)Math.Min ((i + 1) * (int)unionSize, allItemAttributes.Count);
                    break;
                }
            }

            for (int i = begin; i < end; i++) {
                var attr = allItemAttributes [i];
                if (rect.IntersectsWith (attr.Frame)) {
                    attrs.Add (attr);
                }
            }

            return attrs.ToArray();
        }

        public override bool ShouldInvalidateLayoutForBoundsChange (CGRect newBounds)
        {
            var oldBounds = CollectionView.Bounds;
            return (newBounds.Width != oldBounds.Width);
        }
        #endregion

        #region Private Methods
        private int ShortestColumnIndex() {
            var index = 0;
            var shortestHeight = nfloat.MaxValue;
            var n = 0;

            // Scan each column for the shortest height
            foreach (nfloat height in columnHeights) {
                if (height < shortestHeight) {
                    shortestHeight = height;
                    index = n;
                }
                ++n;
            }

            return index;
        }

        private int LongestColumnIndex() {
            var index = 0;
            var longestHeight = nfloat.MinValue;
            var n = 0;

            // Scan each column for the shortest height
            foreach (nfloat height in columnHeights) {
                if (height > longestHeight) {
                    longestHeight = height;
                    index = n;
                }
                ++n;
            }

            return index;
        }

        private int NextColumnIndexForItem(nint item) {
            var index = 0;

            switch (ItemRenderDirection) {
            case WaterfallCollectionRenderDirection.ShortestFirst:
                index = ShortestColumnIndex ();
                break;
            case WaterfallCollectionRenderDirection.LeftToRight:
                index = ColumnCount;
                break;
            case WaterfallCollectionRenderDirection.RightToLeft:
                index = (ColumnCount - 1) - ((int)item / ColumnCount);
                break;
            }

            return index;
        }
        #endregion

        #region Events
        public delegate CGSize WaterfallCollectionSizeDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, NSIndexPath indexPath);
        public delegate nfloat WaterfallCollectionFloatDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, nint section);
        public delegate UIEdgeInsets WaterfallCollectionEdgeInsetsDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, nint section);

        public event WaterfallCollectionSizeDelegate SizeForItem;
        public event WaterfallCollectionFloatDelegate HeightForHeader;
        public event WaterfallCollectionFloatDelegate HeightForFooter;
        public event WaterfallCollectionEdgeInsetsDelegate InsetForSection;
        public event WaterfallCollectionFloatDelegate MinimumInterItemSpacingForSection;
        #endregion
    }
}
```

Esto puede ser utilizado de clase para proporcionar una columna personalizada de dos, el diseño de tipo de cascada a la vista de colección.
El código usa la codificación de clave-valor (a través de la `WillChangeValue` y `DidChangeValue` métodos) para proporcionar enlace de datos de nuestras propiedades calculadas en esta clase.

A continuación, edite el `WaterfallCollectionSource` y realice los cambios e incorporaciones siguientes:

```csharp
private Random rnd = new Random();
...

public List<nfloat> Heights { get; set; } = new List<nfloat> ();
...

public WaterfallCollectionSource (WaterfallCollectionView collectionView)
{
    // Initialize
    CollectionView = collectionView;

    // Init numbers collection
    for (int n = 0; n < 100; ++n) {
        Numbers.Add (n);
        Heights.Add (rnd.Next (0, 100) + 40.0f);
    }
}
```

Esto creará un alto aleatorio para cada uno de los elementos que se mostrará en la lista.

A continuación, edite el `WaterfallCollectionView` de clases y agregue la siguiente propiedad auxiliar:

```csharp
public WaterfallCollectionSource Source {
    get { return (WaterfallCollectionSource)DataSource; }
}
```

Así resultará más fácil de obtener nuestro origen de datos (y el alto del elemento) del diseño personalizado.

Por último, edite el controlador de vista y agregue el código siguiente:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    var waterfallLayout = new WaterfallCollectionLayout ();

    // Wireup events
    waterfallLayout.SizeForItem += (collectionView, layout, indexPath) => {
        var collection = collectionView as WaterfallCollectionView;
        return new CGSize((View.Bounds.Width-40)/3,collection.Source.Heights[(int)indexPath.Item]);
    };

    // Attach the custom layout to the collection
    CollectionView.SetCollectionViewLayout(waterfallLayout, false);
}
```

Esto crea una instancia de nuestro diseño personalizado, Establece el evento para proporcionar el tamaño de cada elemento y asocia el nuevo diseño a la vista de colección.

Si se vuelve a ejecuta la aplicación de Xamarin.iOS, la vista de colección ahora tendrá un aspecto similar al siguiente:

[![](uicollectionview-images/custom01.png "La vista de colección ahora tendrá un aspecto similar al siguiente")](uicollectionview-images/custom01.png#lightbox)

Se pueden seguir de arrastre para reordenar elementos como antes, pero los elementos ahora cambiará de tamaño para ajustarse a su nueva ubicación cuando se quitan.

## <a name="collection-view-changes"></a>Cambios de la vista de colección

En las secciones siguientes, echaremos una visión detallada de los cambios realizados en cada clase en la vista de colección mediante iOS 9.

### <a name="uicollectionview"></a>UICollectionView

Se realizaron los siguientes cambios o adiciones en el `UICollectionView` clase IOS 9:

 - `BeginInteractiveMovementForItem` : Marca el inicio de una operación de arrastre.
 - `CancelInteractiveMovement` – Informa a la colección de vista que el usuario ha cancelado una operación de arrastre.
 - `EndInteractiveMovement` – Informa a la colección de vista que el usuario ha terminado una operación de arrastre.
 - `GetIndexPathsForVisibleSupplementaryElements` : Devuelve el `indexPath` de un encabezado o pie de página en una sección de vista de colección.
 - `GetSupplementaryView` : Devuelve el encabezado especificado o el pie de página.
 - `GetVisibleSupplementaryViews` : Devuelve una lista de todos los encabezado visible y pies de página.
 - `UpdateInteractiveMovementTargetPosition` – Informa a la colección de vista que el usuario se ha movido o mueve un elemento durante una operación de arrastre.

### <a name="uicollectionviewcontroller"></a>UICollectionViewController

Se realizaron los siguientes cambios o adiciones en el `UICollectionViewController` clase en iOS 9:

 - `InstallsStandardGestureForInteractiveMovement` – If `true` se usará el nuevo reconocedor de gestos que admite automáticamente arrastrar para volver a ordenar.
 - `CanMoveItem` – Informa a la vista de colección si se puede arrastrar reordenado un elemento determinado.
 - `GetTargetContentOffset` : Se usa para obtener el desplazamiento de un elemento de vista de colección dada.
 - `GetTargetIndexPathForMove` : Obtiene el `indexPath` de un elemento determinado para una operación de arrastre.
 - `MoveItem` : Mueve el orden de un elemento determinado en la lista.


### <a name="uicollectionviewdatasource"></a>UICollectionViewDataSource

Se realizaron los siguientes cambios o adiciones en el `UICollectionViewDataSource` clase en iOS 9:

 - `CanMoveItem` – Informa a la vista de colección si se puede arrastrar reordenado un elemento determinado.
 - `MoveItem` : Mueve el orden de un elemento determinado en la lista.

### <a name="uicollectionviewdelegate"></a>UICollectionViewDelegate

Se realizaron los siguientes cambios o adiciones en el `UICollectionViewDelegate` clase en iOS 9:

 - `GetTargetContentOffset` : Se usa para obtener el desplazamiento de un elemento de vista de colección dada.
 - `GetTargetIndexPathForMove` : Obtiene el `indexPath` de un elemento determinado para una operación de arrastre.

### <a name="uicollectionviewflowlayout"></a>UICollectionViewFlowLayout

Se realizaron los siguientes cambios o adiciones en el `UICollectionViewFlowLayout` clase en iOS 9:

 - `SectionFootersPinToVisibleBounds` : Los pies de página de la sección a los límites de la vista de colección visible se queda bloqueado.
 - `SectionHeadersPinToVisibleBounds` – Destacable a los límites de la vista de colección visibles los encabezados de sección.

### <a name="uicollectionviewlayout"></a>UICollectionViewLayout

Se realizaron los siguientes cambios o adiciones en el `UICollectionViewLayout` clase en iOS 9:

 - `GetInvalidationContextForEndingInteractiveMovementOfItems` : Devuelve el contexto de invalidación al final de una operación de arrastre cuando el usuario finaliza la operación de arrastrar o cancela.
 - `GetInvalidationContextForInteractivelyMovingItems` : Devuelve el contexto de invalidación del principio de una operación de arrastre.
 - `GetLayoutAttributesForInteractivelyMovingItem` : Obtiene los atributos de diseño para un elemento dado mientras se arrastra un elemento.
 - `GetTargetIndexPathForInteractivelyMovingItem` : Devuelve el `indexPath` del elemento que está en el momento dado al arrastrar un elemento.

### <a name="uicollectionviewlayoutattributes"></a>UICollectionViewLayoutAttributes

Se realizaron los siguientes cambios o adiciones en el `UICollectionViewLayoutAttributes` clase en iOS 9:

 - `CollisionBoundingPath` : Devuelve la ruta de acceso de colisión de dos elementos durante una operación de arrastre.
 - `CollisionBoundsType` : Devuelve el tipo de colisión (como un `UIDynamicItemCollisionBoundsType`) que se produjo durante una operación de arrastre.

### <a name="uicollectionviewlayoutinvalidationcontext"></a>UICollectionViewLayoutInvalidationContext

Se realizaron los siguientes cambios o adiciones en el `UICollectionViewLayoutInvalidationContext` clase en iOS 9:

 - `InteractiveMovementTarget` : Devuelve el elemento de destino de una operación de arrastre.
 - `PreviousIndexPathsForInteractivelyMovingItems` : Devuelve el `indexPaths` de otros elementos implicados en un arrastre para reordenar la operación.
 - `TargetIndexPathsForInteractivelyMovingItems` : Devuelve el `indexPaths` de elementos que se reordenarán como resultado de una operación de arrastre para volver a ordenar.

### <a name="uicollectionviewsource"></a>UICollectionViewSource

Se realizaron los siguientes cambios o adiciones en el `UICollectionViewSource` clase en iOS 9:

 - `CanMoveItem` – Informa a la vista de colección si se puede arrastrar reordenado un elemento determinado.
 - `GetTargetContentOffset` : Devuelve los desplazamientos de los elementos que se va a mover a través de una operación de arrastre para volver a ordenar.
 - `GetTargetIndexPathForMove` : Devuelve el `indexPath` de un elemento que se moverán durante una operación de arrastre para volver a ordenar.
 - `MoveItem` : Mueve el orden de un elemento determinado en la lista.

## <a name="summary"></a>Resumen

En este artículo ha cubierto los cambios realizados en las vistas de colección en iOS 9 y se describe cómo se implementan en Xamarin.iOS.
Trata de implementar una acción de arrastre para volver a ordenar simple en una vista de colección; uso de un reconocedor de gestos personalizados con arrastrar para reordenar; y cómo arrastrar para volver a ordenar afecta a un diseño de vista de colección personalizada.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Ejemplo de la vista de colección](https://developer.xamarin.com/samples/monotouch/ios9/CollectionView/)
- [SimpleCollectionView (ejemplo)](https://developer.xamarin.com/samples/SimpleCollectionView/)
- [Eventos, protocolos y delegados](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Trabajar con tablas y vistas](~/ios/user-interface/controls/tables/index.md)
