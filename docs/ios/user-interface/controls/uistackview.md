---
title: Vistas de la pila de Xamarin.iOS
description: En este artículo se describe el uso el nuevo control UIStackView en una aplicación de Xamarin.iOS para administrar un conjunto de subvistas en cualquiera una pila organizada horizontalmente o verticalmente.
ms.prod: xamarin
ms.assetid: 20246E87-2A49-438A-9BD7-756A1B50A617
ms.technology: xamarin-ios
ms.custom: xamu-video
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 6d5be72a9329675a65b0d6873d13894b314b50e7
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61088143"
---
# <a name="stack-views-in-xamarinios"></a>Vistas de la pila de Xamarin.iOS

_En este artículo se describe el uso el nuevo control UIStackView en una aplicación de Xamarin.iOS para administrar un conjunto de subvistas en cualquiera una pila organizada horizontalmente o verticalmente._

> [!IMPORTANT]
> Tenga en cuenta que mientras StackView es compatible con el Diseñador de iOS, puede producirse errores de facilidad de uso al usar el canal estable. Cambiar los canales alfa o Beta debe mitigar este problema. Hemos decidido presentar este tutorial con Xcode hasta que se implementan las correcciones necesarias en el canal estable.

El control de vista de pila (`UIStackView`) aprovecha la eficacia del diseño automático y las clases de tamaño para administrar una pila de subvistas, ya sea horizontal o verticalmente, que responde dinámicamente para el tamaño de pantalla y la orientación del dispositivo iOS.

En función de propiedades definido por el desarrollador como eje, distribución, alineación y espaciado administra el diseño de todas las subvistas asociada a una vista de pila:

[![](uistackview-images/stacked01.png "Diagrama de diseño de la vista de pila")](uistackview-images/stacked01.png#lightbox)

Cuando se usa un `UIStackView` en una aplicación de Xamarin.iOS, el desarrollador puede definir las subvistas ya sea dentro de un guión gráfico en el Diseñador de iOS, así como agregar y quitar subvistas en código C#.

Este documento se compone de dos partes: un inicio rápido para ayudarle a implementar su primera pila ver y, a continuación, algunos detalles más técnicos acerca de cómo funciona.

> [!VIDEO https://youtube.com/embed/p3po6507Ip8]

**UIStackView, de manera [Xamarin University](https://university.xamarin.com/)**

## <a name="uistackview-quickstart"></a>Guía de inicio rápido de UIStackView

Como una introducción rápida a la `UIStackView` control, vamos a crear una interfaz simple que permite al usuario que escriba una calificación de 1 a 5. Vamos a usar dos vistas de la pila: uno para organizar la interfaz verticalmente en el dispositivo de la pantalla y uno para organizar los iconos de clasificación de 1 a 5 horizontalmente por la pantalla.

### <a name="define-the-ui"></a>Definir la interfaz de usuario

Iniciar un nuevo proyecto de Xamarin.iOS y editar la **Main.storyboard** archivo en Interface Builder de Xcode. En primer lugar, arrastre un único **vista de pila Vertical** en el **View Controller**:

[![](uistackview-images/quick01.png "Arrastre una sola vista de pila Vertical en el controlador de vista")](uistackview-images/quick01.png#lightbox)

En el **Inspector de atributos**, establezca las siguientes opciones:

[![](uistackview-images/quick02.png "Establecer las opciones de vista de pila")](uistackview-images/quick02.png#lightbox)

Dónde:

- **Eje** : determina si la vista de pila organiza las subvistas bien **horizontalmente** o **verticalmente**.
- **Alineación** : controla cómo se alinean las subvistas dentro de la vista de pila.
- **Distribución** : controla cómo se cambia el tamaño de las subvistas dentro de la vista de pila.
- **Espaciado** : controla el espacio mínimo entre cada vista secundaria en la vista de pila.
- **Línea base relativa** : si se activa, el espaciado vertical de cada vista secundaria se deriva de su línea base.
- **Diseño márgenes relativa** : coloca las subvistas respecto a los márgenes de diseño estándar.

Cuando se trabaja con una vista de pila, se puede considerar el **alineación** como el **X** y **Y** ubicación de la vista secundaria y la **distribución** como la **Alto** y **ancho**.

> [!IMPORTANT]
> `UIStackView` se ha diseñado como una vista del contenedor no son de representación y por lo tanto, no se dibuja en el lienzo, al igual que otras subclases de `UIView`. Por lo tanto establecer propiedades como `BackgroundColor` o invalidar `DrawRect` no tendrá ningún efecto visual.

Continúa con el diseño de interfaz de la aplicación mediante la adición de una etiqueta, ImageView, dos botones y una vista de pila Horizontal para que se parezca al siguiente:

[![](uistackview-images/quick03.png "Diseñar la interfaz de usuario de la vista de pila")](uistackview-images/quick03.png#lightbox)

Configurar la vista de pila Horizontal con las siguientes opciones:

[![](uistackview-images/quick04.png "Configurar las opciones de vista de pila Horizontal")](uistackview-images/quick04.png#lightbox)

Porque no queremos el icono que representa cada "punto" en la clasificación para estirarla cuando se agrega a la vista de pila Horizontal, hemos establecido el **alineación** a **Center** y  **Distribución** a **rellenar igualmente**.

Por último, conectar los siguientes **salidas** y **acciones**:

[![](uistackview-images/quick05.png "Las salidas de la vista de pila y las acciones")](uistackview-images/quick05.png#lightbox)

### <a name="populate-a-uistackview-from-code"></a>Rellenar un UIStackView desde el código

Vuelva a Visual Studio para Mac y edite el **ViewController.cs** archivo y agregue el código siguiente:

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

### <a name="testing-the-ui"></a>Probar la interfaz de usuario

Con todas las necesarias elementos de interfaz de usuario y código en su lugar, ahora puede ejecutar y probar la interfaz. Cuando se muestre la interfaz de usuario, todos los elementos en la vista de pila Vertical estarán espaciados igualmente de arriba a abajo.

Cuando el usuario pulsa el **aumentar clasificación** botón, se agrega otro "estrella" a la pantalla (hasta un máximo de 5):

[![](uistackview-images/intro01.png "Ejecución de la aplicación de ejemplo")](uistackview-images/intro01.png#lightbox)

El "estrellas" se centra automáticamente y distribuidas uniformemente en la vista de pila Horizontal. Cuando el usuario pulsa el **Disminuir clasificación** botón, una "estrella" se quitará (hasta que se dejan ninguno).

## <a name="stack-view-details"></a>Ver detalles de la pila

Ahora que tenemos una idea general de lo que el `UIStackView` control es y cómo funciona, echemos un vistazo más profundo a algunas de sus características y detalles.

### <a name="auto-layout-and-size-classes"></a>Diseño automático y las clases de tamaño

Como hemos visto anteriormente, cuando una vista secundaria se agrega a una vista de pila su diseño se controla completamente mediante esa vista de pila con diseño automático y las clases de tamaño para colocar y cambiar el tamaño de las vistas organizadas.

La vista de pila le _pin_ la vista de primera y última secundaria en su colección a la **superior** y **inferior** bordes para las vistas de pila Vertical o el **deja**y **derecha** bordes para las vistas de pila Horizontal. Si establece la `LayoutMarginsRelativeArrangement` propiedad `true`, a continuación, la vista ancla las subvistas a los márgenes pertinentes en lugar del borde.

La vista de pila se usa la vista de secundaria `IntrinsicContentSize` propiedad al calcular el tamaño de las subvistas a lo largo de las personalizaciones definidas `Axis` (excepto para el `FillEqually Distribution`). El `FillEqually Distribution` cambia el tamaño de todas las subvistas para que sean del mismo tamaño, rellenar, por tanto, la vista de pila a lo largo del `Axis`.

Con la excepción de la `Fill Alignment`, la vista de pila se usa la vista de secundaria `IntrinsicContentSize` propiedad para calcular el tamaño de la vista perpendicular a la determinada `Axis`. Para el `Fill Alignment`, todas las subvistas el tamaño se ajusta para que rellenen perpendicular a la vista de pila el determinado `Axis`.

### <a name="positioning-and-sizing-the-stack-view"></a>Posición y cambio de tamaño de la vista de pila

Mientras que la vista de la pila tiene control total sobre el diseño de cualquier vista secundaria (según las propiedades como `Axis` y `Distribution`), deberá colocar la vista de pila (`UIStackView`) de esta vista primaria con diseño automático y las clases de tamaño.

Por lo general, esto significa que al menos dos bordes de la vista de pila para expandir y contraer, definiendo así su posición de anclaje. Sin restricciones adicionales, la vista de pila se ajustará automáticamente para ajustarse a todos sus subvistas como sigue:

 - El tamaño a lo largo de su `Axis` será la suma de todos los tamaños de la vista secundaria más cualquier espacio que se ha definido entre cada vista secundaria.
 - Si el `LayoutMarginsRelativeArrangement` propiedad es `true`, el tamaño de las vistas de la pila también incluirá espacio para los márgenes.
 - El tamaño perpendicular a la `Axis` se establecerá en la vista secundaria más grande en la colección.

Además, puede especificar restricciones para la vista de pila **alto** y **ancho**. En este caso, las subvistas se debe disponer (tamaño) para rellenar el espacio especificado por la vista de pila según lo determinado por la `Distribution` y `Alignment` propiedades.

Si el `BaselineRelativeArrangement` propiedad es `true`, las subvistas se dispondrán en función de la línea de base de la primera o última vista secundaria, en lugar de usar el **superior**, **inferior** o **Center** -  **Y** posición. Estos se calculan en el contenido de la vista de pila como sigue:

 - Una vista de pila Vertical devolverá la primera vista de secundaria para la primera línea de base y la última durante los últimos. Si cualquiera de estos subvistas son vistas de pila, se usará su línea base primero o último.
 - Una vista de pila Horizontal usará su vista secundaria más alto para la línea de base primero y último. Si la vista más alta también es una pila, usará vista secundaria más alto el es como la línea base.

> [!IMPORTANT]
> Alineación de línea base no funciona en los tamaños de la vista secundaria ajustada o comprimidos se calculará la línea base para una posición incorrecta. Para la alineación de línea base, asegúrese de que la vista de secundaria **alto** coincide con la vista de contenido intrínseco **alto**.

### <a name="common-stack-view-uses"></a>Usos comunes de vista de pila

Hay varios tipos de diseño que funcionan bien con los controles de vista de pila. Función de Apple, estos son algunos de los usos más comunes:

- **Definir el tamaño a lo largo del eje** : Anclando los dos bordes a lo largo de la vista de pila `Axis` y uno de los bordes adyacentes para establecer la posición, la pila de vista crecerá a lo largo del eje para ajustarse al espacio definido por sus subvistas.
 -  **Definir la posición de la vista secundaria** – Anclando con bordes adyacentes de la vista de pila para su vista primaria, la vista de pila crecerá en ambas dimensiones para ajustarse a su contenedor subvistas.
- **Definir el tamaño y posición de la pila** – Anclando los cuatro bordes de la vista de pila para la vista primaria, la vista de pila organiza las subvistas según el espacio definido dentro de la vista de pila.
 -  **Definir el tamaño Perpendicular al eje** : Anclando ambos perpendicular bordes a la vista de pila `Axis` y uno de los bordes del eje para establecer la posición, la pila de vista crecerá perpendicular al eje para ajustarse al espacio definido por su subvistas.

### <a name="managing-the-appearance"></a>Administración de la apariencia

El `UIStackView` está diseñado como una vista del contenedor no son de representación y por lo tanto, no se dibuja en el lienzo, al igual que otras subclases de `UIView`. Establecer propiedades tales como `BackgroundColor` o invalidar `DrawRect` no tendrá ningún efecto visual.

Hay varias propiedades que controlan cómo organizará una vista de pila a su colección de las subvistas:

- **Eje** : determina si la vista de pila organiza las subvistas bien **horizontalmente** o **verticalmente**.
- **Alineación** : controla cómo se alinean las subvistas dentro de la vista de pila.
- **Distribución** : controla cómo se cambia el tamaño de las subvistas dentro de la vista de pila.
- **Espaciado** : controla el espacio mínimo entre cada vista secundaria en la vista de pila.
- **Línea base relativa** : si `true`, el espaciado vertical de cada vista secundaria se deriva de su línea base.
- **Diseño márgenes relativa** : coloca las subvistas respecto a los márgenes de diseño estándar.

Normalmente, usará una vista de pila para organizar un pequeño número de subvistas. Interfaces de usuario más complejas se pueden crear mediante el anidamiento de una o varias vistas de pila dentro de otros (como hicimos en el [UIStackView Quickstart](#uistackview-quickstart) anteriormente).

Puede ajustar aún más la apariencia de las interfaces de usuario agregando restricciones adicionales a las subvistas (por ejemplo, al control el alto o ancho). Sin embargo, debe tener cuidado para que no incluya restricciones en conflicto para los introducidos por la vista de pila propio.

### <a name="maintaining-arranged-views-and-sub-views-consistency"></a>Mantener organizan las vistas y la coherencia de las vistas de Sub

La vista de pila se asegurará de que su `ArrangedSubviews` propiedad siempre es un subconjunto de sus `Subviews` propiedad utilizando las reglas siguientes:

 - Si se agrega una vista secundaria a la `ArrangedSubviews` colección, automáticamente se agregará a la `Subviews` colección (a menos que ya forme parte de esa colección).
 - Si se quita una vista secundaria de la `Subviews` colección (quitado de pantalla), también se quitará el `ArrangedSubviews` colección.
 - Quitar una vista secundaria desde la `ArrangedSubviews` colección no quita de la `Subviews` colección. Por lo que ya no se distribuyen por la vista de pila, pero seguirán estando visible en pantalla.

El `ArrangedSubviews` colección siempre es un subconjunto de los `Subview` colección, pero el orden de las subvistas individuales dentro de cada colección es independiente y controlada por el texto siguiente:

 - El orden de las subvistas dentro de la `ArrangedSubviews` colección determinar su orden de visualización dentro de la pila.
 - El orden de las subvistas dentro de la `Subview` colección determina el orden Z (o en capas) dentro de la vista atrás hacia delante.

### <a name="dynamically-changing-content"></a>Cambiar dinámicamente el contenido

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

## <a name="summary"></a>Resumen

En este artículo ha cubierto el nuevo `UIStackView` control (para iOS 9) para administrar un conjunto de subvistas en una pila bien organizada horizontalmente o verticalmente en una aplicación de Xamarin.iOS.
Se inició con un ejemplo sencillo de usar vistas de pila para crear una interfaz de usuario y finaliza con una visión más detallada de las vistas de la pila y sus propiedades y características.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [Novedades de iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Referencia de UIStackView](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIStackView_Class_Reference/)
- [Introducción a UIStackView (vídeo)](https://university.xamarin.com/lightninglectures/introducing-uistackview-on-ios9)
