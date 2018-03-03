---
title: Vista de pila
description: "Este artículo incluye el uso del nuevo control UIStackView en una aplicación de Xamarin.iOS para administrar un conjunto de subvistas en la vista una pila organizada horizontal o verticalmente."
ms.topic: article
ms.prod: xamarin
ms.assetid: 20246E87-2A49-438A-9BD7-756A1B50A617
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 4555906512ecc36e3387f1b2483753e7f50a51ae
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="stack-view"></a>Vista de pila

_Este artículo incluye el uso del nuevo control UIStackView en una aplicación de Xamarin.iOS para administrar un conjunto de subvistas en la vista una pila organizada horizontal o verticalmente._

> [!IMPORTANT]
> Tenga en cuenta que aunque se admite StackView en el Diseñador de iOS, pueden producirse errores de facilidad de uso al usar el canal estable. Cambiar los canales de Beta o alfa deben mitigar este problema. Hemos decidido presentar este tutorial mediante Xcode hasta que se implementan las correcciones necesarias en el canal estable.

El control de vista de pila (`UIStackView`) aprovecha la eficacia de diseño automático y las clases de tamaño para administrar una pila de subvistas, horizontal o verticalmente, que responde dinámicamente para el tamaño de pantalla y la orientación del dispositivo iOS.

El diseño de todas las subvistas adjunta a una vista de pila se administran en él basándose en las propiedades de desarrollador definido como eje, distribución, alineación y el espaciado:

[ ![](uistackview-images/stacked01.png "Diagrama de diseño de la vista de pila")](uistackview-images/stacked01.png)

Cuando se usa un `UIStackView` en una aplicación Xamarin.iOS, el desarrollador puede definir las subvistas ya sea dentro de un guión gráfico en el Diseñador de iOS o agregando y quitando subvistas en código C#.

Este documento consta de dos partes: un inicio rápido para ayudarle a implementar el primer bloque ver y, a continuación, algunos detalles más técnicos acerca de cómo funciona.

## <a name="uistackview-quickstart"></a>Inicio rápido de UIStackView

Como una introducción rápida a la `UIStackView` (control), vamos a crear una interfaz simple que permite al usuario que escriba una clasificación de 1 a 5. Usaremos dos vistas de la pila: uno para organizar la interfaz verticalmente en el dispositivo de la pantalla y otra para organizar los iconos de clasificación de 1 a 5 horizontalmente por la pantalla.

### <a name="define-the-ui"></a>Definir la interfaz de usuario

Iniciar un nuevo proyecto de Xamarin.iOS y editar la **Main.storyboard** archivo en el generador de interfaz de Xcode. En primer lugar, arrastre un único **vista de pila Vertical** en el **View Controller**:

[ ![](uistackview-images/quick01.png "Arrastre una sola vista de pila Vertical en el controlador de vista")](uistackview-images/quick01.png)

En el **atributo Inspector**, establezca las siguientes opciones:

[ ![](uistackview-images/quick02.png "Establecer las opciones de vista de pila")](uistackview-images/quick02.png)

Dónde:

- **Eje** : determina si la vista de pila organiza las subvistas o **horizontalmente** o **verticalmente**.
- **Alineación** : controla cómo se alinean las subvistas dentro de la vista de pila.
- **Distribución** : controla cómo se cambia el tamaño de las subvistas dentro de la vista de pila.
- **Espaciado** : controla el espacio mínimo entre cada vista secundaria en la vista de pila.
- **Línea base relativa** : si se activa, el espaciado vertical de cada vista secundaria se derivan de su línea base.
- **Diseño márgenes relativa** – coloca las subvistas respecto a los márgenes de diseño estándar.

Cuando se trabaja con una vista de pila, se puede considerar el **alineación** como el **X** y **Y** ubicación de la vista secundaria y la **distribución** como la **Alto** y **ancho**.

> [!IMPORTANT]
> **Nota:** `UIStackView` está diseñado como una vista del contenedor de representación no y por lo tanto, no se dibuja en el lienzo al igual que otras subclases de `UIView`. Por lo que establecer las propiedades como `BackgroundColor` o reemplazar `DrawRect` no tiene ningún efecto visual.

Continuar al diseño de interfaz de la aplicación mediante la adición de una etiqueta, ImageView, dos botones y una vista de pila Horizontal para que se parezca a lo siguiente:

[ ![](uistackview-images/quick03.png "Diseñar la interfaz de usuario de la vista de pila")](uistackview-images/quick03.png)

Configurar la vista de pila Horizontal con las siguientes opciones:

[ ![](uistackview-images/quick04.png "Configurar las opciones de vista de pila Horizontal")](uistackview-images/quick04.png)

Porque no queremos que el icono que representa cada punto de"" de la clasificación para ajustarse al agregarlo a la vista de pila Horizontal, hemos configurado la **alineación** a **Center** y  **Distribución** a **rellenar igualmente**.

Por último, conecte el siguiente **tomas** y **acciones**:

[ ![](uistackview-images/quick05.png "Las salidas de la vista de pila y las acciones")](uistackview-images/quick05.png)

### <a name="populate-a-uistackview-from-code"></a>Rellenar un UIStackView desde el código

Vuelva a Visual Studio para Mac y editar la **ViewController.cs** de archivos y agregue el código siguiente:

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

### <a name="testing-the-ui"></a>Probar la interfaz de usuario

Todas las necesarias elementos de interfaz de usuario y código en su lugar, ahora puede ejecutar y probar la interfaz. Cuando se muestra la interfaz de usuario, todos los elementos en la vista de pila Vertical estarán espaciados igualmente de arriba a abajo.

Cuando el usuario puntea el **aumentar clasificación** botón, otro "estrella" se agrega a la pantalla (hasta un máximo de 5):

[ ![](uistackview-images/intro01.png "Ejecutar la aplicación de ejemplo")](uistackview-images/intro01.png)

"Estrellas" se centra automáticamente y se distribuya equitativamente en la vista de pila Horizontal. Cuando el usuario puntea el **Disminuir clasificación** botón, una "estrella" se quitará (hasta que se dejan ninguno).

## <a name="stack-view-details"></a>Ver detalles de la pila

Ahora que tenemos una idea general de lo que el `UIStackView` control es y cómo funciona, echemos un vistazo más profundo en algunas de sus características y detalles.

### <a name="auto-layout-and-size-classes"></a>Clases de tamaño y diseño automático

Tal y como se ha explicado anteriormente, cuando una vista secundaria se agrega a una vista de pila su diseño totalmente se controla mediante esa vista de pila con diseño automático y las clases de tamaño para colocar y cambiar el tamaño de las vistas organizadas.

La vista de pila le _pin_ la vista de primera y última secundaria en su colección de la **superior** y **inferior** bordes para las vistas de pila Vertical o el **deja**y **derecha** bordes para las vistas de pila Horizontal. Si establece la `LayoutMarginsRelativeArrangement` propiedad `true`, a continuación, la vista ancla el subvistas a los márgenes relevantes en lugar del borde.

La vista de pila utiliza la vista de secundaria `IntrinsicContentSize` propiedad al calcular el tamaño de subvistas a lo largo de las personalizaciones definidas `Axis` (excepto para el `FillEqually Distribution`). El `FillEqually Distribution` cambia el tamaño de todas las subvistas para que sean del mismo tamaño, lo que rellenar la vista de pila a lo largo de la `Axis`.

Con la excepción de la `Fill Alignment`, la vista de pila utiliza la vista de secundaria `IntrinsicContentSize` propiedad para calcular el tamaño de la vista perpendicular a la determinada `Axis`. Para el `Fill Alignment`, todas las subvistas tamaño se ajusta para que rellenen la vista de pila perpendicular a la determinada `Axis`.

### <a name="positioning-and-sizing-the-stack-view"></a>Posición y ajustar el tamaño de la vista de pila

Mientras que la vista de la pila tiene control total sobre el diseño de cualquier vista secundaria (en función de propiedades como `Axis` y `Distribution`), debe colocar la vista de pila (`UIStackView`) dentro de su vista primaria utilizando clases de tamaño y diseño automático.

Por lo general, esto significa que al menos dos bordes de la vista de pila para expandir y contraer, definiendo así su posición de anclaje. Sin restricciones adicionales, la vista de pila se ajustará automáticamente para ajustarse a todos sus subvistas como sigue:

 - El tamaño a lo largo de su `Axis` será la suma de todos los tamaños de la vista secundaria más espacio que se haya definido entre cada vista secundaria.
 - Si el `LayoutMarginsRelativeArrangement` propiedad es `true`, el tamaño de pila vistas también incluirá espacio para los márgenes.
 - El tamaño perpendicular a la `Axis` se establecerá en la vista secundaria más grande en la colección.

Además, puede especificar restricciones para la vista de pila **alto** y **ancho**. En este caso, las subvistas se dispondrán (tamaño) para rellenar el espacio especificado por la vista de pila según lo determinado por la `Distribution` y `Alignment` propiedades.

Si el `BaselineRelativeArrangement` propiedad es `true`, las subvistas se debe disponer en función de la línea de base de la primera o la última vista secundaria, en lugar de utilizar el **arriba**, **inferior** o **Center* -  **Y** posición. Estos se calculan en el contenido de la vista de pila de la siguiente manera:

 - Una vista de pila Vertical devolverá la primera vista de secundaria para la primera línea de base y el último de los últimos. Si cualquiera de estos subvistas son vistas de la pila, se usará su línea de base de primer o último.
 - Una vista de pila Horizontal usará su vista secundaria más alto para la línea de base primero y último. Si la vista más alta es también una vista de pila, usará vista secundaria más alto el su como la línea de base.

> [!IMPORTANT]
> **Nota:** alineación de línea base no funciona en los tamaños de la vista secundaria estirada o comprimido tal y como se calculará la línea de base a la posición incorrecta. Para la alineación de línea base, asegúrese de que la vista de secundaria **alto** coincide con la vista de contenido intrínseco **alto**.

### <a name="common-stack-view-uses"></a>Usos comunes de vista de pila

Hay varios tipos de diseño que funcionan correctamente con los controles de vista de pila. Función de Apple, estos son algunos de los usos más comunes:

- **Definir el tamaño a lo largo del eje** , por lo que ambos bordes a lo largo de la vista de pilas de anclaje `Axis` y uno de los bordes adyacentes para establecer la posición, la pila de vista crecerá a lo largo del eje para ajustarse al espacio definido por sus subvistas.
 -  **Definir la posición de la vista secundaria** – crece con anclados con bordes adyacentes de la vista de pila para su vista primaria, la vista de pila en ambas dimensiones para ajustarse a su contenedor subvistas.
- **Definir el tamaño y la posición de la pila** – por fijar los cuatro bordes de la vista de pila para la vista primaria, la vista de pila organiza las subvistas en función del espacio definido dentro de la vista de pila.
 -  **Definir tamaño Perpendicular al eje** , por lo que ambos perpendicular bordes a la vista de pilas de anclaje `Axis` y uno de los bordes a lo largo del eje para establecer la posición, la pila de vista crecerá perpendicular al eje para ajustarse al espacio definido por su subvistas.

### <a name="managing-the-appearance"></a>Administrar la apariencia

El `UIStackView` está diseñado como una vista del contenedor de representación no y por lo tanto, no se dibuja en el lienzo al igual que otras subclases de `UIView`. Establecer las propiedades como `BackgroundColor` o reemplazar `DrawRect` no tiene ningún efecto visual.

Hay varias propiedades que controlan cómo una vista de pila organizará su colección de subvistas:

- **Eje** : determina si la vista de pila organiza las subvistas o **horizontalmente** o **verticalmente**.
- **Alineación** : controla cómo se alinean las subvistas dentro de la vista de pila.
- **Distribución** : controla cómo se cambia el tamaño de las subvistas dentro de la vista de pila.
- **Espaciado** : controla el espacio mínimo entre cada vista secundaria en la vista de pila.
- **Línea base relativa** : si `true`, el espaciado vertical de cada vista secundaria se derivan de su línea base.
- **Diseño márgenes relativa** – coloca las subvistas respecto a los márgenes de diseño estándar.

Normalmente utilizará una vista de pila para organizar un número reducido de subvistas. Interfaces de usuario más complejas se pueden crear mediante el anidamiento de una o varias vistas de pila dentro de otros (como hicimos el [UIStackView Quickstart](#UIStackView-Quickstart) anteriormente).

Puede optimizar aún más la apariencia de interfaces de usuario agregando restricciones adicionales a las subvistas (por ejemplo, para controlar el alto o el ancho). Sin embargo, debe tener cuidado no debe incluir las restricciones conflictivas a las introducidas por la vista de pila propio.

### <a name="maintaining-arranged-views-and-sub-views-consistency"></a>Mantener organizados vistas y la coherencia de vistas de Sub

La vista de pila se asegurará de que su `ArrangedSubviews` propiedad siempre es un subconjunto de sus `Subviews` propiedad utilizando las reglas siguientes:

 - Si se agrega una vista secundaria a la `ArrangedSubviews` colección, se agregará automáticamente a la `Subviews` colección (a menos que ya forme parte de esa colección).
 - Si se quita una vista secundaria de la `Subviews` colección (que se quitan de la presentación), también se quita de la `ArrangedSubviews` colección.
 - Quitar una vista secundaria desde la `ArrangedSubviews` colección, no quita de la `Subviews` colección. Por lo que ya no se coloquen en la vista de pila, pero seguirán estando visible en pantalla.

El `ArrangedSubviews` recopilación siempre es un subconjunto de la `Subview` colección, sin embargo el orden de las subvistas individuales dentro de cada colección es independiente y controlada por el texto siguiente:

 - El orden de las subvistas dentro de la `ArrangedSubviews` colección determinar su orden de presentación en la pila.
 - El orden de las subvistas dentro de la `Subview` colección determina el orden Z (o en capas) dentro de la vista al principio.

### <a name="dynamically-changing-content"></a>Cambiar dinámicamente el contenido

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

## <a name="summary"></a>Resumen

En este artículo ha cubierto el nuevo `UIStackView` controlar (para iOS 9) para administrar un conjunto de subvistas en una pila ya sea horizontal o verticalmente organizada en una aplicación de Xamarin.iOS.
Comienza con un ejemplo sencillo de usar vistas de pila para crear una interfaz de usuario y finaliza con una visión más detallada de pila de vistas y sus propiedades y características.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [What's New en iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Referencia de UIStackView](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIStackView_Class_Reference/)
- [Introducción a UIStackView (vídeo)](https://university.xamarin.com/lightninglectures/introducing-uistackview-on-ios9)
