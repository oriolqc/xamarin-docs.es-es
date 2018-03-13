---
title: Trabajar con vista apilada
description: "Este artículo tratan diseñar y trabajar con vista apiladas dentro de una aplicación Xamarin.tvOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 00B07F85-F30B-4DD4-8664-A61D0A1CDB0E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: c40e9291e81770435535de7b0c83d6c865cbed58
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="working-with-stacked-view"></a>Trabajar con vista apilada

_Este artículo tratan diseñar y trabajar con vista apiladas dentro de una aplicación Xamarin.tvOS._


El control de vista de pila (`UIStackView`) aprovecha la eficacia de diseño automático y las clases de tamaño para administrar una pila de subvistas, horizontal o verticalmente, que responde dinámicamente a los cambios de contenido y el tamaño de pantalla del dispositivo de TV de Apple.

El diseño de todas las subvistas adjunta a una vista de pila se administran en él basándose en las propiedades de desarrollador definido como eje, distribución, alineación y el espaciado:

[![](stacked-views-images/stacked01.png "Diagrama de diseño de la vista secundaria")](stacked-views-images/stacked01.png#lightbox)

Cuando se usa un `UIStackView` en una aplicación Xamarin.tvOS, el desarrollador puede definir las subvistas ya sea dentro de un guión gráfico en el Diseñador de iOS o agregando y quitando subvistas en código C#.

## <a name="about-stacked-view-controls"></a>Acerca de los controles de vista apiladas 

El `UIStackView` está diseñado como una vista del contenedor de representación no y por lo tanto, no se dibuja en el lienzo al igual que otras subclases de `UIView`. Establecer las propiedades como `BackgroundColor` o reemplazar `DrawRect` no tiene ningún efecto visual.

Hay varias propiedades que controlan cómo una vista de pila organizará su colección de subvistas:

- **Eje** : determina si la vista de pila organiza las subvistas o **horizontalmente** o **verticalmente**.
- **Alineación** : controla cómo se alinean las subvistas dentro de la vista de pila.
- **Distribución** : controla cómo se cambia el tamaño de las subvistas dentro de la vista de pila.
- **Espaciado** : controla el espacio mínimo entre cada vista secundaria en la vista de pila.
- **Línea base relativa** : si `true`, el espaciado vertical de cada vista secundaria se derivan de su línea base.
- **Diseño márgenes relativa** – coloca las subvistas respecto a los márgenes de diseño estándar.

Normalmente utilizará una vista de pila para organizar un número reducido de subvistas. Interfaces de usuario más complejas se pueden crear mediante el anidamiento de una o varias vistas de pila dentro de otros.

Puede optimizar aún más la apariencia de interfaces de usuario agregando restricciones adicionales a las subvistas (por ejemplo, para controlar el alto o el ancho). Sin embargo, debe tener cuidado no debe incluir las restricciones conflictivas a las introducidas por la vista de pila propio.

<a name="Auto-Layout-and-Size-Classes" />

## <a name="auto-layout-and-size-classes"></a>Clases de tamaño y diseño automático

Cuando se agrega una vista secundaria a una vista de pila su diseño lo controla totalmente esa vista de pila con diseño automático y las clases de tamaño para colocar y cambiar el tamaño de las vistas organizadas.

La vista de pila le _pin_ la vista de primera y última secundaria en su colección de la **superior** y **inferior** bordes para las vistas de pila Vertical o el **deja**y **derecha** bordes para las vistas de pila Horizontal. Si establece la `LayoutMarginsRelativeArrangement` propiedad `true`, a continuación, la vista ancla el subvistas a los márgenes relevantes en lugar del borde.

La vista de pila utiliza la vista de secundaria `IntrinsicContentSize` propiedad al calcular el tamaño de subvistas a lo largo de las personalizaciones definidas `Axis` (excepto para el `FillEqually Distribution`). El `FillEqually Distribution` cambia el tamaño de todas las subvistas para que sean del mismo tamaño, lo que rellenar la vista de pila a lo largo de la `Axis`.

Con la excepción de la `Fill Alignment`, la vista de pila utiliza la vista de secundaria `IntrinsicContentSize` propiedad para calcular el tamaño de la vista perpendicular a la determinada `Axis`. Para el `Fill Alignment`, todas las subvistas tamaño se ajusta para que rellenen la vista de pila perpendicular a la determinada `Axis`.

<a name="Positioning-and-Sizing-the-Stack-View" />

## <a name="positioning-and-sizing-the-stack-view"></a>Posición y ajustar el tamaño de la vista de pila

Mientras que la vista de la pila tiene control total sobre el diseño de cualquier vista secundaria (en función de propiedades como `Axis` y `Distribution`), debe colocar la vista de pila (`UIStackView`) dentro de su vista primaria utilizando clases de tamaño y diseño automático.

Por lo general, esto significa que al menos dos bordes de la vista de pila para expandir y contraer, definiendo así su posición de anclaje. Sin restricciones adicionales, la vista de pila se ajustará automáticamente para ajustarse a todos sus subvistas como sigue:

* El tamaño a lo largo de su `Axis` será la suma de todos los tamaños de la vista secundaria más espacio que se haya definido entre cada vista secundaria.
* Si el `LayoutMarginsRelativeArrangement` propiedad es `true`, el tamaño de pila vistas también incluirá espacio para los márgenes.
* El tamaño perpendicular a la `Axis` se establecerá en la vista secundaria más grande en la colección.

Además, puede especificar restricciones para la vista de pila **alto** y **ancho**. En este caso, las subvistas se dispondrán (tamaño) para rellenar el espacio especificado por la vista de pila según lo determinado por la `Distribution` y `Alignment` propiedades.

Si el `BaselineRelativeArrangement` propiedad es `true`, las subvistas se debe disponer en función de la línea de base de la primera o la última vista secundaria, en lugar de utilizar el **arriba**, **inferior** o **Center* -  **Y** posición. Estos se calculan en el contenido de la vista de pila de la siguiente manera:

* Una vista de pila Vertical devolverá la primera vista de secundaria para la primera línea de base y el último de los últimos. Si cualquiera de estos subvistas son vistas de la pila, se usará su línea de base de primer o último.
* Una vista de pila Horizontal usará su vista secundaria más alto para la línea de base primero y último. Si la vista más alta es también una vista de pila, usará vista secundaria más alto el su como la línea de base.

> [!IMPORTANT]
> **Nota:** alineación de línea base no funciona en los tamaños de la vista secundaria estirada o comprimido tal y como se calculará la línea de base a la posición incorrecta. Para la alineación de línea base, asegúrese de que la vista de secundaria **alto** coincide con la vista de contenido intrínseco **alto**.




<a name="Common-Stack-View-Uses" />

## <a name="common-stack-view-uses"></a>Usos comunes de vista de pila

Hay varios tipos de diseño que funcionan correctamente con los controles de vista de pila. Función de Apple, estos son algunos de los usos más comunes:

- **Definir el tamaño a lo largo del eje** , por lo que ambos bordes a lo largo de la vista de pilas de anclaje `Axis` y uno de los bordes adyacentes para establecer la posición, la pila de vista crecerá a lo largo del eje para ajustarse al espacio definido por sus subvistas.
*  **Definir la posición de la vista secundaria** – crece con anclados con bordes adyacentes de la vista de pila para su vista primaria, la vista de pila en ambas dimensiones para ajustarse a su contenedor subvistas.
- **Definir el tamaño y la posición de la pila** – por fijar los cuatro bordes de la vista de pila para la vista primaria, la vista de pila organiza las subvistas en función del espacio definido dentro de la vista de pila.
*  **Definir tamaño Perpendicular al eje** , por lo que ambos perpendicular bordes a la vista de pilas de anclaje `Axis` y uno de los bordes a lo largo del eje para establecer la posición, la pila de vista crecerá perpendicular al eje para ajustarse al espacio definido por su subvistas.

<a name="Stack-Views-and-Storyboards" />

## <a name="stack-views-and-storyboards"></a>Vistas de pila y los guiones gráficos

Es la manera más fácil trabajar con vistas de pila en una aplicación Xamarin.tvOS agregarlos a la interfaz de usuario de la aplicación mediante el Diseñador de iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En el **solución Pad**, haciendo doble clic en el `Main.storyboard` de archivo y abrirlo y editarlo.
1. Definir el diseño de los elementos individuales que se va a agregar a la vista de pila: 

    [![](stacked-views-images/layout01.png "Ejemplo de diseño de elemento")](stacked-views-images/layout01.png#lightbox)
1. Agregar las restricciones necesarias para los elementos para asegurarse de que se adaptan correctamente. Este paso es importante cuando el elemento se agrega a la vista de pila.
1. Realice el número necesario de copias (cuatro en este caso): 

    [![](stacked-views-images/layout02.png "El número necesario de copias")](stacked-views-images/layout02.png#lightbox)
1. Arrastre un **vista de pila** desde el **cuadro de herramientas** y colóquela en la vista: 

    [![](stacked-views-images/layout03.png "Una vista de pila")](stacked-views-images/layout03.png#lightbox)
1. Seleccione la vista de pila, en la **ficha Widget** de la **panel de propiedades** seleccione **rellenar** para el **alineación**, **de relleno Igualmente** para el **distribución** y escriba `25` para el **espaciado**: 

    [![](stacked-views-images/layout04.png "La pestaña de Widget")](stacked-views-images/layout04.png#lightbox)
1. Coloque la vista de pila en la pantalla donde quiera y agregar restricciones para mantener en la ubicación deseada.
1. Seleccione los elementos individuales y arrástrelos hasta la vista de pila: 

    [![](stacked-views-images/layout05.png "Los elementos individuales en la vista de pila")](stacked-views-images/layout05.png#lightbox)
1. Se ajustará el diseño y los elementos se organizarán en la vista de pila en función de los atributos establecidos anteriormente.
1. Asignar **nombres** en el **ficha Widget** de la **el Explorador de propiedades** para trabajar con los controles de interfaz de usuario en código C#.
1. Guarde los cambios.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. En el **el Explorador de soluciones**, haciendo doble clic en el `Main.storyboard` de archivo y abrirlo y editarlo.
1. Definir el diseño de los elementos individuales que se va a agregar a la vista de pila: 

    [![](stacked-views-images/layout01.png "Ejemplo de diseño de elemento")](stacked-views-images/layout01.png#lightbox)
1. Agregar las restricciones necesarias para los elementos para asegurarse de que se adaptan correctamente. Este paso es importante cuando el elemento se agrega a la vista de pila.
1. Realice el número necesario de copias (cuatro en este caso): 

    [![](stacked-views-images/layout02.png "El número necesario de copias")](stacked-views-images/layout02.png#lightbox)
1. Arrastre un **vista de pila** desde el **cuadro de herramientas** y colóquela en la vista: 

    [![](stacked-views-images/layout03-vs.png "Una vista de pila")](stacked-views-images/layout03-vs.png#lightbox)
1. Seleccione la vista de pila, en la **ficha Widget** de la **el Explorador de propiedades** seleccione **rellenar** para el **alineación**, **relleno Igualmente** para el **distribución** y escriba `25` para el **espaciado**: 

    [![](stacked-views-images/layout04-vs.png "La pestaña de Widget")](stacked-views-images/layout04-vs.png#lightbox)
1. Coloque la vista de pila en la pantalla donde quiera y agregar restricciones para mantener en la ubicación deseada.
1. Seleccione los elementos individuales y arrástrelos hasta la vista de pila: 

    [![](stacked-views-images/layout05-vs.png "Los elementos individuales en la vista de pila")](stacked-views-images/layout05-vs.png#lightbox)
1. Se ajustará el diseño y los elementos se organizarán en la vista de pila en función de los atributos establecidos anteriormente.
1. Asignar **nombres** en el **ficha Widget** de la **el Explorador de propiedades** para trabajar con los controles de interfaz de usuario en código C#.
1. Guarde los cambios.

-----

> [!IMPORTANT]
> **Nota:** Nota: aunque es posible asignar acciones como `TouchUpInside` a un elemento de interfaz de usuario (como un `UIButton`) en el diseñador al crear un controlador de eventos de iOS, nunca se llamará Apple TV no tiene un toque de pantalla o compatibilidad táctil eventos. Debe utilizar siempre el valor predeterminado `Action Type` al crear acciones tvos elementos de la interfaz de usuario.

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Hello, Guía de inicio rápido de tvOS](~/ios/tvos/get-started/hello-tvos.md).

En el caso de nuestro ejemplo, nos hemos expone una toma de corriente y acción para el Control de segmento y una salida para cada tarjeta de Reproductor de"". En el código, se ocultar y mostrar Reproductor tomando como base el segmento actual. Por ejemplo:

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

Cuando se ejecuta la aplicación, los cuatro elementos se distribuya equitativamente en la vista de pila:

[![](stacked-views-images/layout06.png "Cuando se ejecuta la aplicación, los cuatro elementos se distribuirán por igual en la vista de pila")](stacked-views-images/layout06.png#lightbox)

Si se reduce el número de jugadores, se ocultan las vistas no utilizadas y la vista de pila de ajustar el diseño para que se ajusten:

[![](stacked-views-images/layout07.png "Si se reduce el número de jugadores, se ocultan las vistas no utilizadas y la vista de pila de ajustar el diseño para que se ajusten")](stacked-views-images/layout07.png#lightbox)

<a name="Populate-a-Stack-View-from-Code" />

### <a name="populate-a-stack-view-from-code"></a>Rellenar una vista de pila de código

Además de definir totalmente el contenido y el diseño de una vista de pila en el Diseñador de iOS, puede crear y quitar de forma dinámica desde el código C#.

Considere el ejemplo siguiente que utiliza una vista de pila para controlar "estrellas" en una revisión (de 1 a 5):

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

¡Eche un vistazo a algunas partes de este código en detalle. En primer lugar, usamos un `if` instrucciones para comprobar que no hay más de cinco "estrellas" o menor que cero.

Para agregar una nueva "estrella" cargamos su imagen y establecer su **modo contenido** a **ajuste de escala aspecto**:

```csharp
var icon = new UIImageView (new UIImage("icon.png"));
icon.ContentMode = UIViewContentMode.ScaleAspectFit;
```

Esto impide que el icono de "estrella" está distorsionado cuando se agrega a la vista de pila.

A continuación, agregamos el icono de "estrella" nueva a la colección de la vista de pila de subvistas:

```csharp
RatingView.AddArrangedSubview(icon);
```

Observará que agregamos la `UIImageView` a la `UIStackView`del `ArrangedSubviews` propiedad y no a la `SubView`. Cualquier vista que desea que la vista de pila para controlar su diseño debe agregarse a la `ArrangedSubviews` propiedad.

Para quitar una vista secundaria de una vista de pila, primero tenemos la vista secundaria para quitar:

```csharp
var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];
```

A continuación, se necesita quitarlo de ambos el `ArrangedSubviews` recopilación y la vista de Super:

```csharp
// Remove from stack and screen
RatingView.RemoveArrangedSubview(icon);
icon.RemoveFromSuperview();
```

Quitar una vista secundaria de solo los `ArrangedSubviews` colección toma fuera del control de la vista de pila, pero no la quita de la pantalla.

<a name="Dynamically-Changing-Content" />

## <a name="dynamically-changing-content"></a>Cambiar dinámicamente el contenido

Una vista de pila se ajustará automáticamente el diseño de las subvistas cada vez que una vista secundaria se agrega, se quita o se oculta. El diseño también se ajustará si se ajusta a cualquier propiedad de la vista de pila (como su `Axis`).

Cambios de diseño se pueden animar mediante la colocación de un bloque de animación, por ejemplo:

```csharp
// Animate stack
UIView.Animate(0.25, ()=>{
    // Adjust stack view
    RatingView.LayoutIfNeeded();
});
```

Muchas de las propiedades de la vista de la pila pueden especificarse mediante las clases de tamaño dentro de un guión gráfico. Estas propiedades serán automáticamente animado es la respuesta a los cambios de tamaño y la orientación.

<a name="Summary" />

## <a name="summary"></a>Resumen

Diseñar y trabajar con vista apiladas dentro de una aplicación Xamarin.tvOS se ha descrito en este artículo.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guías de interfaz humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones tvos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
