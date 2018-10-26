---
title: Trabajar con vistas de tvOS apiladas en Xamarin
description: Este documento describe cómo funcionaba con tvOS vistas apiladas en una aplicación compilada con Xamarin. Proporciona una descripción general de las vistas apiladas y describe el diseño automático y colocar y ajustar el tamaño de una vista apilada, común usa, integración con guiones gráficos y mucho más.
ms.prod: xamarin
ms.assetid: 00B07F85-F30B-4DD4-8664-A61D0A1CDB0E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: f51ed3d6dbbfc8a7e430c2949485838a7471e545
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110770"
---
# <a name="working-with-tvos-stacked-views-in-xamarin"></a>Trabajar con vistas de tvOS apiladas en Xamarin

El control de vista de pila (`UIStackView`) aprovecha la eficacia del diseño automático y las clases de tamaño para administrar una pila de subvistas, ya sea horizontal o verticalmente, que responde dinámicamente a los cambios de contenido y el tamaño de pantalla del dispositivo Apple TV.

En función de propiedades definido por el desarrollador como eje, distribución, alineación y espaciado administra el diseño de todas las subvistas asociada a una vista de pila:

[![](stacked-views-images/stacked01.png "Diagrama de diseño de la vista secundaria")](stacked-views-images/stacked01.png#lightbox)

Cuando se usa un `UIStackView` en una aplicación Xamarin.tvOS, el desarrollador puede definir las subvistas ya sea dentro de un guión gráfico en el Diseñador de iOS, así como agregar y quitar subvistas en C# código.

## <a name="about-stacked-view-controls"></a>Acerca de los controles de vista apiladas 

El `UIStackView` está diseñado como una vista del contenedor no son de representación y por lo tanto, no se dibuja en el lienzo, al igual que otras subclases de `UIView`. Establecer propiedades tales como `BackgroundColor` o invalidar `DrawRect` no tendrá ningún efecto visual.

Hay varias propiedades que controlan cómo organizará una vista de pila a su colección de las subvistas:

- **Eje** : determina si la vista de pila organiza las subvistas bien **horizontalmente** o **verticalmente**.
- **Alineación** : controla cómo se alinean las subvistas dentro de la vista de pila.
- **Distribución** : controla cómo se cambia el tamaño de las subvistas dentro de la vista de pila.
- **Espaciado** : controla el espacio mínimo entre cada vista secundaria en la vista de pila.
- **Línea base relativa** : si `true`, el espaciado vertical de cada vista secundaria se deriva de su línea base.
- **Diseño márgenes relativa** : coloca las subvistas respecto a los márgenes de diseño estándar.

Normalmente, usará una vista de pila para organizar un pequeño número de subvistas. Interfaces de usuario más complejas se pueden crear mediante el anidamiento de una o varias vistas de pila dentro de otros.

Puede ajustar aún más la apariencia de las interfaces de usuario agregando restricciones adicionales a las subvistas (por ejemplo, al control el alto o ancho). Sin embargo, debe tener cuidado para que no incluya restricciones en conflicto para los introducidos por la vista de pila propio.

<a name="Auto-Layout-and-Size-Classes" />

## <a name="auto-layout-and-size-classes"></a>Diseño automático y las clases de tamaño

Cuando se agrega una vista secundaria a una vista de pila su diseño lo controla totalmente esa vista de pila con diseño automático y las clases de tamaño para colocar y cambiar el tamaño de las vistas organizadas.

La vista de pila le _pin_ la vista de primera y última secundaria en su colección a la **superior** y **inferior** bordes para las vistas de pila Vertical o el **deja**y **derecha** bordes para las vistas de pila Horizontal. Si establece la `LayoutMarginsRelativeArrangement` propiedad `true`, a continuación, la vista ancla las subvistas a los márgenes pertinentes en lugar del borde.

La vista de pila se usa la vista de secundaria `IntrinsicContentSize` propiedad al calcular el tamaño de las subvistas a lo largo de las personalizaciones definidas `Axis` (excepto para el `FillEqually Distribution`). El `FillEqually Distribution` cambia el tamaño de todas las subvistas para que sean del mismo tamaño, rellenar, por tanto, la vista de pila a lo largo del `Axis`.

Con la excepción de la `Fill Alignment`, la vista de pila se usa la vista de secundaria `IntrinsicContentSize` propiedad para calcular el tamaño de la vista perpendicular a la determinada `Axis`. Para el `Fill Alignment`, todas las subvistas el tamaño se ajusta para que rellenen perpendicular a la vista de pila el determinado `Axis`.

<a name="Positioning-and-Sizing-the-Stack-View" />

## <a name="positioning-and-sizing-the-stack-view"></a>Posición y cambio de tamaño de la vista de pila

Mientras que la vista de la pila tiene control total sobre el diseño de cualquier vista secundaria (según las propiedades como `Axis` y `Distribution`), deberá colocar la vista de pila (`UIStackView`) de esta vista primaria con diseño automático y las clases de tamaño.

Por lo general, esto significa que al menos dos bordes de la vista de pila para expandir y contraer, definiendo así su posición de anclaje. Sin restricciones adicionales, la vista de pila se ajustará automáticamente para ajustarse a todos sus subvistas como sigue:

* El tamaño a lo largo de su `Axis` será la suma de todos los tamaños de la vista secundaria más cualquier espacio que se ha definido entre cada vista secundaria.
* Si el `LayoutMarginsRelativeArrangement` propiedad es `true`, el tamaño de las vistas de la pila también incluirá espacio para los márgenes.
* El tamaño perpendicular a la `Axis` se establecerá en la vista secundaria más grande en la colección.

Además, puede especificar restricciones para la vista de pila **alto** y **ancho**. En este caso, las subvistas se debe disponer (tamaño) para rellenar el espacio especificado por la vista de pila según lo determinado por la `Distribution` y `Alignment` propiedades.

Si el `BaselineRelativeArrangement` propiedad es `true`, las subvistas se dispondrán en función de la línea de base de la primera o última vista secundaria, en lugar de usar el **superior**, **inferior** o **Center* -  **Y** posición. Estos se calculan en el contenido de la vista de pila como sigue:

* Una vista de pila Vertical devolverá la primera vista de secundaria para la primera línea de base y la última durante los últimos. Si cualquiera de estos subvistas son vistas de pila, se usará su línea base primero o último.
* Una vista de pila Horizontal usará su vista secundaria más alto para la línea de base primero y último. Si la vista más alta también es una pila, usará vista secundaria más alto el es como la línea base.

> [!IMPORTANT]
> Alineación de línea base no funciona en los tamaños de la vista secundaria ajustada o comprimidos se calculará la línea base para una posición incorrecta. Para la alineación de línea base, asegúrese de que la vista de secundaria **alto** coincide con la vista de contenido intrínseco **alto**.




<a name="Common-Stack-View-Uses" />

## <a name="common-stack-view-uses"></a>Usos comunes de vista de pila

Hay varios tipos de diseño que funcionan bien con los controles de vista de pila. Función de Apple, estos son algunos de los usos más comunes:

- **Definir el tamaño a lo largo del eje** : Anclando los dos bordes a lo largo de la vista de pila `Axis` y uno de los bordes adyacentes para establecer la posición, la pila de vista crecerá a lo largo del eje para ajustarse al espacio definido por sus subvistas.
*  **Definir la posición de la vista secundaria** – Anclando con bordes adyacentes de la vista de pila para su vista primaria, la vista de pila crecerá en ambas dimensiones para ajustarse a su contenedor subvistas.
- **Definir el tamaño y posición de la pila** – Anclando los cuatro bordes de la vista de pila para la vista primaria, la vista de pila organiza las subvistas según el espacio definido dentro de la vista de pila.
*  **Definir el tamaño Perpendicular al eje** : Anclando ambos perpendicular bordes a la vista de pila `Axis` y uno de los bordes del eje para establecer la posición, la pila de vista crecerá perpendicular al eje para ajustarse al espacio definido por su subvistas.

<a name="Stack-Views-and-Storyboards" />

## <a name="stack-views-and-storyboards"></a>Los guiones gráficos y vistas de pila

Es la manera más fácil trabajar con vistas de pila en una aplicación Xamarin.tvOS agregarlos a la interfaz de usuario de la aplicación mediante el Diseñador de iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. En el **panel de solución**, haciendo doble clic en el `Main.storyboard` de archivo y abrirlo para su edición.
1. Definir el diseño de los elementos individuales que se va a agregar a la vista de pila: 

    [![](stacked-views-images/layout01.png "Ejemplo de diseño de elemento")](stacked-views-images/layout01.png#lightbox)
1. Agregar las restricciones necesarias para los elementos que se va a asegurar que se escalan correctamente. Este paso es importante una vez que el elemento se agrega a la vista de pila.
1. Realice el número necesario de copias (cuatro en este caso): 

    [![](stacked-views-images/layout02.png "El número necesario de copias")](stacked-views-images/layout02.png#lightbox)
1. Arrastre un **vista de pila** desde el **cuadro de herramientas** y colóquela en la vista: 

    [![](stacked-views-images/layout03.png "Una vista de pila")](stacked-views-images/layout03.png#lightbox)
1. Seleccione la vista de pila, en la **ficha Widget** de la **panel de propiedades** seleccione **rellenar** para el **alineación**, **relleno Igualmente** para el **distribución** y escriba `25` para el **espaciado**: 

    [![](stacked-views-images/layout04.png "La pestaña de Widget")](stacked-views-images/layout04.png#lightbox)
1. Colocar la vista de pila en la pantalla donde lo desee y agrega restricciones para conservarla en la ubicación deseada.
1. Seleccione los elementos individuales y arrástrelos hasta la vista de pila: 

    [![](stacked-views-images/layout05.png "Los elementos individuales en la vista de pila")](stacked-views-images/layout05.png#lightbox)
1. Se ajustarán el diseño y los elementos se colocarán en la vista de pila basándose en los atributos que estableció anteriormente.
1. Asignar **nombres** en el **ficha Widget** de la **Explorador de propiedades** para trabajar con los controles de IU en C# código.
1. Guarde los cambios.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. En el **el Explorador de soluciones**, haciendo doble clic en el `Main.storyboard` de archivo y abrirlo para su edición.
1. Definir el diseño de los elementos individuales que se va a agregar a la vista de pila: 

    [![](stacked-views-images/layout01.png "Ejemplo de diseño de elemento")](stacked-views-images/layout01.png#lightbox)
1. Agregar las restricciones necesarias para los elementos que se va a asegurar que se escalan correctamente. Este paso es importante una vez que el elemento se agrega a la vista de pila.
1. Realice el número necesario de copias (cuatro en este caso): 

    [![](stacked-views-images/layout02.png "El número necesario de copias")](stacked-views-images/layout02.png#lightbox)
1. Arrastre un **vista de pila** desde el **cuadro de herramientas** y colóquela en la vista: 

    [![](stacked-views-images/layout03-vs.png "Una vista de pila")](stacked-views-images/layout03-vs.png#lightbox)
1. Seleccione la vista de pila, en la **ficha Widget** de la **Explorador de propiedades** seleccione **rellenar** para el **alineación**, **relleno Igualmente** para el **distribución** y escriba `25` para el **espaciado**: 

    [![](stacked-views-images/layout04-vs.png "La pestaña de Widget")](stacked-views-images/layout04-vs.png#lightbox)
1. Colocar la vista de pila en la pantalla donde lo desee y agrega restricciones para conservarla en la ubicación deseada.
1. Seleccione los elementos individuales y arrástrelos hasta la vista de pila: 

    [![](stacked-views-images/layout05-vs.png "Los elementos individuales en la vista de pila")](stacked-views-images/layout05-vs.png#lightbox)
1. Se ajustarán el diseño y los elementos se colocarán en la vista de pila basándose en los atributos que estableció anteriormente.
1. Asignar **nombres** en el **ficha Widget** de la **Explorador de propiedades** para trabajar con los controles de IU en C# código.
1. Guarde los cambios.

-----

> [!IMPORTANT]
> Aunque es posible asignar acciones como `TouchUpInside` a un elemento de interfaz de usuario (como un `UIButton`) en el diseñador al crear un controlador de eventos de iOS, nunca se llamará porque Apple TV no tiene una entrada táctil de pantalla o admitir eventos de toque. Siempre debe usar el valor predeterminado `Action Type` al crear acciones para tvOS elementos de la interfaz de usuario.

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Hola, Guía de inicio rápido de tvOS](~/ios/tvos/get-started/hello-tvos.md).

En el caso de nuestro ejemplo, se han expuesto una toma de corriente y acción para el Control de segmento y una salida para cada tarjeta de jugador"". En el código, hemos ocultar y mostrar Reproductor basado en el segmento actual. Por ejemplo:

```csharp
partial void PlayerCountChanged (Foundation.NSObject sender) {

    // Take Action based on the segment
    switch(PlayerCount.SelectedSegment) {
    case 0:
        Player1.Hidden = false;
        Player2.Hidden = true;
        Player3.Hidden = true;
        Player4.Hidden = true;
        break;
    case 1:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = true;
        Player4.Hidden = true;
        break;
    case 2:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = false;
        Player4.Hidden = true;
        break;
    case 3:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = false;
        Player4.Hidden = false;
        break;
    }
}
```

Cuando se ejecuta la aplicación, igualmente se distribuirá los cuatro elementos en la vista de pila:

[![](stacked-views-images/layout06.png "Cuando se ejecuta la aplicación, igualmente se distribuirá los cuatro elementos en la vista de pila")](stacked-views-images/layout06.png#lightbox)

Si se reduce el número de jugadores, se ocultan las vistas sin usar y la vista de pila de ajustar el diseño para que quepa:

[![](stacked-views-images/layout07.png "Si se reduce el número de jugadores, se ocultan las vistas sin usar y la vista de pila de ajustar el diseño para que quepa")](stacked-views-images/layout07.png#lightbox)

<a name="Populate-a-Stack-View-from-Code" />

### <a name="populate-a-stack-view-from-code"></a>Rellenar una vista de pila del código

Además de definir totalmente el contenido y el diseño de una vista de pila en el Diseñador de iOS, puede crear y quitar dinámicamente desde C# código.

Realice el siguiente ejemplo que utiliza una vista de pila para controlar "estrellas" en una revisión (de 1 a 5):

```csharp
public int Rating { get; set;} = 0;
...

partial void IncreaseRating (Foundation.NSObject sender) {

    // Maximum of 5 "stars"
    if (++Rating > 5 ) {
        // Abort
        Rating = 5;
        return;
    }

    // Create new rating icon and add it to stack
    var icon = new UIImageView (new UIImage("icon.png"));
    icon.ContentMode = UIViewContentMode.ScaleAspectFit;
    RatingView.AddArrangedSubview(icon);

    // Animate stack
    UIView.Animate(0.25, ()=>{
        // Adjust stack view
        RatingView.LayoutIfNeeded();
    });

}

partial void DecreaseRating (Foundation.NSObject sender) {

    // Minimum of zero "stars"
    if (--Rating < 0) {
        // Abort
        Rating =0;
        return;
    }

    // Get the last subview added
    var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];

    // Remove from stack and screen
    RatingView.RemoveArrangedSubview(icon);
    icon.RemoveFromSuperview();

    // Animate stack
    UIView.Animate(0.25, ()=>{
        // Adjust stack view
        RatingView.LayoutIfNeeded();
    });
}
```

Echemos un vistazo a algunas partes de este código en detalle. En primer lugar, usamos un `if` instrucciones para comprobar que no hay más de cinco "estrellas" o menor que cero.

Para agregar una nueva "estrella" cargamos su imagen y establezca su **modo contenido** a **escalar Ajustar aspecto**:

```csharp
var icon = new UIImageView (new UIImage("icon.png"));
icon.ContentMode = UIViewContentMode.ScaleAspectFit;
```

Esto impide que el icono de "estrella" está distorsionado cuando se agrega a la vista de pila.

A continuación, se agrega el nuevo icono de "estrella" a la colección de vistas de la pila subvistas:

```csharp
RatingView.AddArrangedSubview(icon);
```

Observará que se ha agregado el `UIImageView` a la `UIStackView`del `ArrangedSubviews` propiedad y no en el `SubView`. Cualquier vista que desea que la vista de pila para controlar su diseño debe agregarse a la `ArrangedSubviews` propiedad.

Para quitar una vista secundaria desde una vista de pila, primero se obtiene la vista secundaria para quitar:

```csharp
var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];
```

A continuación, necesitamos quitarlo de ambos el `ArrangedSubviews` recopilación y la vista Super:

```csharp
// Remove from stack and screen
RatingView.RemoveArrangedSubview(icon);
icon.RemoveFromSuperview();
```

Quitar una vista secundaria de solo el `ArrangedSubviews` colección se toma del control de la vista de pila, pero no se quita de la pantalla.

<a name="Dynamically-Changing-Content" />

## <a name="dynamically-changing-content"></a>Cambiar dinámicamente el contenido

Una vista de pila se ajustará automáticamente el diseño de las subvistas cuando una vista secundaria se agrega, quita u oculta. También se ajuste el diseño si se ajusta a cualquier propiedad de la vista de pila (como su `Axis`).

Mediante la colocación de un bloque de animación, por ejemplo, se pueden animar los cambios de diseño:

```csharp
// Animate stack
UIView.Animate(0.25, ()=>{
    // Adjust stack view
    RatingView.LayoutIfNeeded();
});
```

Muchas de las propiedades de la vista de la pila pueden especificarse mediante las clases de tamaño en un guión gráfico. Estas propiedades estarán automáticamente animado es la respuesta a cambios de tamaño o la orientación.

<a name="Summary" />

## <a name="summary"></a>Resumen

Este artículo trata de diseñar y trabajar con vista apiladas dentro de una aplicación Xamarin.tvOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicación de tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
