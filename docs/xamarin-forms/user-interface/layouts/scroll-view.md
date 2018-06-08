---
title: ScrollView
description: Usar ScrollView para presentar los diseños que no se pueden ajustar en una única pantalla y tienen contenido dejar espacio para el teclado.
ms.prod: xamarin
ms.assetid: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/22/2016
ms.openlocfilehash: 708fb39aa2e56861a8c9fc47ab30bd20ed20188e
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2018
ms.locfileid: "34847542"
---
# <a name="scrollview"></a>ScrollView

[`ScrollView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) contiene diseños y les permite desplazamiento fuera de la pantalla. `ScrollView` También se utiliza para permitir que las vistas se mueva automáticamente a la parte visible de la pantalla cuando se muestra el teclado.

[![](scroll-view-images/layouts-sml.png "Diseños de Xamarin.Forms")](scroll-view-images/layouts.png#lightbox "diseños de Xamarin.Forms")

En este artículo se tratan los aspectos siguientes:

- **[Propósito](#Purpose)**  &ndash; el propósito de `ScrollView` y cuando se utiliza.
- **[Uso de](#Usage)**  &ndash; cómo usar `ScrollView` en la práctica.
- **[Propiedades](#Properties)**  &ndash; propiedades públicas que se pueden leer y modificar.
- **[Métodos](#Methods)**  &ndash; métodos públicos que se puedan llamar para desplazar la vista.
- **[Eventos](#Events)**  &ndash; eventos que pueden usarse para escuchar los cambios en los Estados de la vista.

## <a name="purpose"></a>Propósito

`ScrollView` puede usarse para asegurarse de que vistas mayor se muestran correctamente en los teléfonos más pequeños. Por ejemplo, un diseño que funciona en un iPhone 6s puede recortarse en un iPhone 4s. Mediante un `ScrollView` permitiría las partes recortadas del diseño que se mostrará en la pantalla más pequeña.

## <a name="usage"></a>Uso

> [!NOTE]
> `ScrollView`s no debería estar anidado. Además, `ScrollView`s no debería estar anidado con otros controles que proporcionan el desplazamiento, como `ListView` y `WebView`.

`ScrollView` expone un `Content` propiedad que puede establecerse una sola vista ni el diseño. Considere este ejemplo de un diseño con un gran boxView, seguido por un `Entry`:

```xaml
<ContentPage.Content>
    <ScrollView>
        <StackLayout>
            <BoxView BackgroundColor="Red" HeightRequest="600" WidthRequest="150" />
            <Entry />
        </StackLayout>
    </ScrollView>
</ContentPage.Content>
```

En C#:

```csharp
var scroll = new ScrollView();
Content = scroll;
var stack = new StackLayout();
stack.Children.Add(new BoxView { BackgroundColor = Color.Red,    HeightRequest = 600, WidthRequest = 600 });
stack.Children.Add(new Entry());
```

Antes de que el usuario se desplaza hacia abajo, solo el `BoxView` está visible:

![](scroll-view-images/scroll-start.png "BoxView en ScrollView")

Tenga en cuenta que, cuando el usuario comienza a escribir texto en el `Entry`, la vista se desplaza para mantener visible en pantalla:

![](scroll-view-images/scroll-end.png "Entrada de ScrollView")

## <a name="properties"></a>Propiedades

ScrollView tiene las siguientes propiedades:

- **Contenido** &ndash; Obtiene o establece la vista para mostrar en el `ScrollView`.
- **[ContentSize](https://developer.xamarin.com/api/type/Xamarin.Forms.Size/)**  &ndash; de sólo lectura, obtiene el tamaño del contenido, que tiene un componente de ancho y alto. Se trata de una propiedad enlazable
- **[Orientación](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollOrientation/)**  &ndash; se trata de un [ `ScrollOrientation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollOrientation/), que es una enumeración que se puede establecer en `Horizontal`, `Vertical`, o `Both`.
- **ScrollX** &ndash; de sólo lectura, obtiene la posición de desplazamiento actual en la dimensión X.
- **ScrollY** &ndash; de sólo lectura, obtiene la posición de desplazamiento actual en la dimensión Y.

## <a name="methods"></a>Métodos

`ScrollView` Proporciona un `ScrollToAsync` método, que se puede usar para desplazar la vista si se usa coordenadas o especificando una vista concreta que debe hacerse visible.

Cuando se usa coordenadas, especifique la `x` y `y` coordenadas, junto con un valor booleano que indica si el desplazamiento debe animarse:

```csharp
scroll.ScrollToAsync(0, 150, true); //scrolls so that the position at 150px from the top is visible

scroll.ScrollToAsync(label, ScrollToPosition.Start, true); //scrolls so that the label is at the start of the list
```

Al desplazarse a un elemento determinado, el `ScrollToPosition` especifica enumeración donde se mostrará el elemento en la vista:

- **Centro de** &ndash; desplaza el elemento hasta el centro de la parte visible de la vista.
- **End** &ndash; desplaza el elemento al final de la parte visible de la vista.
- **MakeVisible** &ndash; desplaza el elemento para que esté visible en la vista.
- **Iniciar** &ndash; desplaza el elemento al principio de la parte visible de la vista.

El `IsAnimated` propiedad especifica cómo se puede desplazar la vista. Cuando establece en true, una animación suave se usará, en lugar de al instante mover el contenido en la vista.

## <a name="events"></a>Eventos

`ScrollView` expone un solo evento, `Scrolled`. `Scrolled` se produce cuando ha finalizado la vista de desplazamiento. El controlador de eventos `Scrolled` toma `ScrolledEventArgs`, que tiene el `ScrollX` y `ScrollY` propiedades. El siguiente ejemplo muestra cómo actualizar una etiqueta con la actual posición de desplazamiento de un `ScrollView`:

```csharp
Label label = new Label { Text = "Position: " };
ScrollView scroll = new ScrollView();
scroll.Scrolled += (object sender, ScrolledEventArgs e) => {
    label.Text = "Position: " + e.ScrollX + " x " + e.ScrollY;
};
```

Tenga en cuenta que las posiciones de desplazamiento pueden ser negativas, porque el efecto de rebote al desplazarse al final de una lista.


## <a name="related-links"></a>Vínculos relacionados

- [Diseño (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Ejemplo de BusinessTumble (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
