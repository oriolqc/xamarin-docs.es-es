---
title: iOS 7 introducción de la interfaz de usuario
description: iOS 7 presenta una gran cantidad de cambios de la interfaz de usuario. Este artículo resalta algunos de los cambios más grandes tanto en el aspecto visual de los controles en las API compatibles con el nuevo diseño.
ms.prod: xamarin
ms.assetid: FADCEA7C-8968-42A1-9E9E-F4BBAB7BCF2C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 3f5ea8abd41e718f9ac947c5acb290dffe400ddd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="ios-7-user-interface-overview"></a>iOS 7 introducción de la interfaz de usuario

_iOS 7 presenta una gran cantidad de cambios de la interfaz de usuario. Este artículo resalta algunos de los cambios más grandes tanto en el aspecto visual de los controles en las API compatibles con el nuevo diseño._

iOS 7 se centra en el contenido a través de chrome. Elementos de la interfaz de usuario de iOS 7 la importancia de chrome mediante la eliminación de atributos, como bordes extraños y barras de estado, barras de navegación, que reducen la cantidad de espacio utilizado en las vistas de contenido en la pantalla. En iOS 7, el contenido está diseñado para utilizar toda la pantalla.

iOS 7 introduce varios otros cambios: color se utiliza para distinguir los elementos de la interfaz de usuario, en lugar de atributos como bordes de botón. Muchos elementos, como barras de navegación y barras de estado, ahora son difuminadas y translúcidas o transparentes, con vistas contenidas teniendo área por debajo de ellos. Estas vistas de contenido se representan mediante las barras difuminadas, ofrecer una sensación de profundidad en la interfaz de usuario.

Este artículo tratan algunos de los cambios realizados en elementos de la interfaz de usuario de iOS 7, así como diversas API relacionadas con el nuevo diseño de interfaz de usuario.

## <a name="view-and-control-changes"></a>Ver y controlar los cambios

Todas las vistas en UIKit se ajustan a la nueva apariencia y funcionamiento de iOS 7. Esta sección resalta algunos de los cambios realizados en estas vistas, así como las API relacionadas que han cambiado para admitir la nueva interfaz de usuario.

### <a name="uibutton"></a>UIButton

Botones creado a partir de la `UIButton` clase están ahora sin bordes con ninguna experiencia de forma predeterminada, tal y como se muestra a continuación:

 ![](ios7-ui-images/button.png "Ejemplo UIButton")

El `UIButtonType.RoundedRect` estilo está en desuso. Si se utiliza en iOS 7, `UIButtonType.RoundedRect` dará como resultado `UIButtonType.System` utilizado, lo que produce el estilo de botón predeterminado sin fondo o bordes visibles, como se indicó anteriormente.

### <a name="uibarbuttonitem"></a>UIBarButtonItem

Similar a `UIButton`, botones de la barra también están sin márgenes, toma como valor predeterminado para el nuevo `UIBarButtonItemStyle.Plain` estilo que se muestra a continuación:

 ![](ios7-ui-images/barbuttonplain.png "Ejemplo UIBarButtonItem")

Además, la `UIBarButtonItemStyle.Bordered` estilo está en desuso. Establecer `UIBarButtonItemStyle.Bordered` en iOS 7 producirá la `UIBarButtonItemStyle.Plain` de estilo que se va a usar.

El `UIBarButtonItemStyle.Done` estilo no está en desuso. Sin embargo, se también creará un botón sin márgenes, solo con un estilo de texto en negrita, como se muestra:

 ![](ios7-ui-images/barbuttondone.png "UIBarButtonItem de ejemplo en el estilo listo")

### <a name="uialertview"></a>UIAlertView

Además del cambio de estilo para la nueva apariencia y funcionamiento de iOS 7, vistas de alerta ya no admiten la personalización a través de la vista secundaria. Aunque `UIAlertView` hereda de `UIView`, si se llama `AddSubview` en un `UIAlertView` no tiene ningún efecto. Por ejemplo, considere el siguiente código:

```csharp
UIBarButtonItem button = new UIBarButtonItem ("Bar Button", UIBarButtonItemStyle.Plain, (s,e) =>
{
    UIAlertView alert = new UIAlertView ("Title", "Message", null, "Cancel", "OK");

    alert.AddSubview (new UIView () {
        Frame = new CGRect(50, 50,100, 100),
        BackgroundColor = UIColor.Green
    });

    alert.Show ();
});
```

Esto produce una vista de alerta estándar, con la vista secundaria que se omiten, tal y como se muestra a continuación:

 ![](ios7-ui-images/alert.png "Ejemplo UIAlertView")
 
 Nota: UIAlertView ha quedado obsoleta en iOS 8. Ver el [controlador alerta](https://developer.xamarin.com/recipes/ios/standard_controls/alertcontroller/) receta sobre el uso de una vista de alertas en iOS 8 y versiones posteriores.

### <a name="uisegmentedcontrol"></a>UISegmentedControl

Controles segmentados en iOS 7 son transparentes y admiten el color del matiz. El color del matiz se usa para el color de borde y texto. Cuando se selecciona un segmento, el color se intercambia entre el fondo y el texto, con el color del matiz usado para resaltar el segmento seleccionado, tal y como se muestra a continuación:

 ![](ios7-ui-images/segmentedcontrol.png "Ejemplo UISegmentedControl")

Además, la `UISegmentedControlStyle` está en desuso en iOS 7.

### <a name="picker-views"></a>Vistas de selector

La API para las vistas de selector es en gran medida intacto; Sin embargo, directrices de diseño 7 iOS ahora establecen vistas selector deben presentarse en línea, en lugar de como entradas vistas animados desde la parte inferior de la pantalla o a través de un nuevo controlador de inserta en la pila del controlador de navegación, como en iOS anteriores versiones. Esto puede verse en la aplicación de calendario del sistema:

 ![](ios7-ui-images/inlinepicker.png "Esto puede verse en la aplicación de calendario del sistema")

### <a name="uisearchdisplaycontroller"></a>UISearchDisplayController

La barra de búsqueda se muestra ahora en el panel de navegación barra cuando el `UISearchDisplayController.DisplaysSearchBarInNavigationBar` propiedad está establecida en true. Cuando se establece en false: el valor predeterminado: la barra de navegación está oculto cuando se muestra el controlador de búsqueda.

Captura de pantalla siguiente muestra la barra de búsqueda dentro de un `UISearchDisplayController`:

 ![](ios7-ui-images/searchbar.png "Ejemplo UISearchDisplayController")

### <a name="uitableview"></a>UITableView

Las API de alrededor de `UITableView` son principalmente sin cambios; sin embargo, ha cambiado considerablemente el estilo de acuerdo con el nuevo diseño de interfaz de usuario. También es ligeramente distinta en la jerarquía de vista interna. Este cambio no afecta a la mayoría de las aplicaciones, pero es algo a tener en cuenta.

#### <a name="grouped-table-style"></a>Estilo de tabla agrupados

Ha actualizado el estilo agrupado que se ha cambiado, con el contenido que se extiende ahora con los bordes de la pantalla, tal y como se muestra a continuación:

 ![](ios7-ui-images/table1.png "Estilo de tabla agrupada muestras")

#### <a name="separatorinset"></a>SeparatorInset

Ahora se pueden aplica sangría a los separadores de fila estableciendo la `UITableVIewCell.SeparatorInset` propiedad. Por ejemplo, el código siguiente se utilizaría para aplicar sangría a las celdas desde el borde izquierdo:

```csharp
cell.SeparatorInset = new UIEdgeInsets (0, 50, 0, 0);
```

Esto se produce en la vista de tabla con las celdas con sangría tal y como se muestra a continuación:

 ![](ios7-ui-images/separatorinset.png "Ejemplo UITableView SeparatorInset")

#### <a name="table-button-styles"></a>Estilos de botón de tabla

Todos los distintos botones que se utilizan en las vistas de tabla se han cambiado. Captura de pantalla siguiente presenta una vista de tabla en modo de edición:

 ![](ios7-ui-images/table2.png "Esta captura de pantalla presenta una vista de tabla en modo de edición")

### <a name="additional-control-changes"></a>Cambios de Control adicionales

Otros controles UIKit han cambiado, incluidos los controles deslizantes, conmutadores y steppers. Estos cambios son puramente visual. Para obtener más información, consulte de Apple [Guía de transición de la interfaz de usuario de iOS 7](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/TransitionGuide/index.html).

## <a name="general-user-interface-changes"></a>Cambios en la interfaz de usuario general

Además de los cambios en UIKit, iOS 7 presenta una variedad de cambios visuales en la interfaz de usuario, incluidos:

-  Contenido de pantalla completa
-  Apariencia de la barra
-  Color del matiz

<a name="fullscreen" />

### <a name="full-screen-content"></a>Contenido de pantalla completa

iOS 7 está diseñado para permitir que las aplicaciones aprovechar toda la pantalla. Ver controladores ahora aparecen superpuestos en una barra de estado y la barra de navegación - si existiese - frente a cuando aparece debajo de las barras de estado y la navegación.

Cuando prepare la aplicación para iOS 7, puede volver a alinear subvistas visualmente utilizando *el generador de interfaz* o *Xamarin iOS diseñador*. También puede usar una de las nuevas API para ayudar a controlar mediante programación el contenido de pantalla completa. Estas API se presentan a continuación.

#### <a name="toplayoutguide-and-bottomlayoutguide"></a>TopLayoutGuide y BottomLayoutGuide

 `TopLayoutGuide` y `BottomLayoutGuide` actuar como referencia para donde deben comenzar o finalizar, vistas para que el contenido no se solapa con una translúcido `UIKit` barra, como en el ejemplo siguiente:

 [![](ios7-ui-images/clipped.png "Contenido de ejemplo no se solapa con una barra de UIKit translúcida")](ios7-ui-images/clipped.png#lightbox)

Estas API pueden usarse para calcular el desplazamiento de una vista de la parte superior o inferior de la pantalla y ajustan la selección de ubicación de contenido según corresponda:

```csharp
public override void ViewDidLayoutSubviews ()
{
    base.ViewDidLayoutSubviews ();

    if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
        nfloat displacement_y = this.TopLayoutGuide.Length;

        //load subviews with displacement
    }

}
```

Podemos usar el valor calculado anteriormente para establecer nuestro `ImageView`del desplazamiento de la parte superior de la pantalla, por lo que está visible la imagen completa:

 [![](ios7-ui-images/good2.png "Desplazamiento de ImageViews de ejemplo desde la parte superior de la pantalla")](ios7-ui-images/good2.png#lightbox)

Hacer referencia a la [ImageViewer](https://developer.xamarin.com/samples/mobile/iOS7-ui-updates) para obtener un ejemplo funcional.

El valor de desplazamiento se genera dinámicamente después de la vista se ha agregado a la jerarquía, por lo que al intentar leer `TopLayoutGuide` y `BottomLayoutGuide` valores en `ViewDidLoad` devolverá 0. Calcular el valor después de la vista haya cargado: por ejemplo, en la `ViewDidLayoutSubviews`.

> [!IMPORTANT]
> `TopLayoutGuide` y `BottomLayoutGuide` están en desuso en iOS 11 a favor del diseño del área segura. Apple han manifestado que el uso de la zona segura de es compatible con la versión de iOS anterior a iOS 11. Para obtener más información, consulte el [actualizar su aplicación para iOS 11](~/ios/platform/introduction-to-ios11/updating-your-app/visual-design.md#fullscreen) guía.

#### <a name="edgesforextendedlayout"></a>EdgesForExtendedLayout

Esta API especifica qué bordes de una vista deben ampliarse a pantalla completa, con independencia de transparencia de la barra. En iOS 7, barras de navegación y barras de herramientas aparecen nivel por encima de la vista del controlador: a diferencia de en iOS anteriores versiones, en el que no ocupan el mismo espacio. El valor predeterminado muestra la aplicación de fotos de iOS 7 `UIViewController.EdgesForExtendedLayout` valor, `UIRectEdge.All`. Este valor rellena los cuatro bordes en la vista de contenido, crear el efecto que se superponen y pantalla completa:

 [![](ios7-ui-images/photos.png "Ejemplo EdgesForExtendedLayout")](ios7-ui-images/photos.png#lightbox)

Puntee en la imagen quita las barras y muestra la pantalla completa de imagen:

 [![](ios7-ui-images/photos2.png "EdgesForExtendedLayout con las barras quitado")](ios7-ui-images/photos2.png#lightbox)

Dado que el contenido de pantalla completa es el valor predeterminado, las aplicaciones configuradas para iOS 6 tendrá parte de la vista que se recorta, como se muestra en la captura de pantalla siguiente:

 [![](ios7-ui-images/clipped.png "Aplicaciones configuradas para iOS 6 tendrá parte de la vista que se recorta, como se muestra en esta captura de pantalla")](ios7-ui-images/clipped.png#lightbox)

Modificar el `UIViewController.EdgesForExtendedLayout` propiedad se ajusta a este comportamiento. Se puede especificar que la vista no se llene los bordes, por lo que nuestro punto de vista, evitará mostrar el contenido en el espacio ocupado por navegación o barras de herramientas (en cada orientación):

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
    this.EdgesForExtendedLayout = UIRectEdge.None;
}
```

En nuestra aplicación, veremos nuevo se vuelve a colocar la vista, por lo que está visible la imagen completa:

 [![](ios7-ui-images/good.png "Ejemplo con toda la imagen visible")](ios7-ui-images/good.png#lightbox)

Tenga en cuenta que mientras los efectos de la `TopLayoutGuide/BottomLayoutGuide` y `EdgesForExtendedLayout` API son similares, que están concebidos para rellenar distintos objetivos. Cambiar el `EdgesForExtendedLayout` configuración del valor predeterminado puede corregir las vistas recortadas en aplicaciones diseñadas para iOS 6, pero un diseño de buena iOS 7 debe respetar el estéticos de pantalla completa y proporcionar una experiencia de visualización, confiando en completo pantalla `TopLayoutGuide` y `BottomLayoutGuide`para colocar correctamente el contenido que está pensada para su manipulación en un lugar cómodo para el usuario.

Hacer referencia a la [ImageViewer](https://developer.xamarin.com/samples/mobile/iOS7-ui-updates) para obtener un ejemplo funcional.

### <a name="status-and-navigation-bars"></a>Estado y las barras de navegación

La barra de estado y barras de navegación se representan con transparencia. Barras de estado son transparentes, mientras que las barras de herramientas y barras de navegación son translúcido y difuminado para transmitir la sensación de profundidad en la interfaz de usuario. Captura de pantalla siguiente muestra este desenfoque y transparencia, donde se muestra el color de fondo azul de la vista de colección a través de barras el estado y la navegación, darles un aspecto azul claro:

 ![](ios7-ui-images/transparent-navbar.png "Ejemplo de estado y barra desenfoque de navegación")

#### <a name="status-bar-styles"></a>Estilos de barra de estado

Junto con desenfoque y transparencia, al primer plano de una barra de estado puede ser claro u oscuro (oscuro es el valor predeterminado). El estilo de barra de estado puede establecerse entre el controlador de vista. También puede establecer un controlador de vista si se oculta o se muestra la barra de estado.

Por ejemplo, el siguiente código se reemplaza el `PreferredStatusBarStyle` método de un controlador de vista para que muestren un primer plano claro de la barra de estado:

```csharp
public override UIStatusBarStyle PreferredStatusBarStyle ()
{
    return UIStatusBarStyle.LightContent;
}
```

Esto hace que aparezca en la barra de estado a la siguiente:

 ![](ios7-ui-images/light-status-bar.png "Barra de estado de ejemplo")

Para ocultar la barra de estado desde el código del controlador de vista, invalidar `PrefersStatusBarHidden`, tal y como se muestra a continuación:

```csharp
public override bool PrefersStatusBarHidden ()
{
    return true;
}
```

Esto oculta la barra de estado:

 ![](ios7-ui-images/status-bar-hidden.png "Barra de estado oculto")

### <a name="tint-color"></a>Color del matiz

Botones ahora se muestran como texto de chrome menor. Se puede controlar el color del texto con el nuevo `TintColor` propiedad `UIView`. Establecer el `TintColor` se aplica el color de la jerarquía de vista completa de la vista que se establece. Para aplicar un `TintColor`a lo largo de una aplicación, establecer esta propiedad en el `Window`. También puede detectar cuándo cambia el color del matiz a través de la `UIView.TintColorDidChange` método.

Por ejemplo, la captura de pantalla siguiente muestra el efecto de cambiar el color del matiz en la vista del controlador de navegación en púrpura:

 ![](ios7-ui-images/tint-color.png "Color púrpura en una vista de controladores de navegación")

El color del matiz puede aplicarse a imágenes así cuando la `RenderingMode` está establecido en `UIImageRenderingMode.AlwaysTemplate`.

> [!IMPORTANT]
> No puede establecerse utilizando el color del matiz `UIAppearance`.


### <a name="dynamic-type"></a>Tipo dinámico

En iOS 7, el usuario puede especificar el tamaño del texto de la configuración del sistema. Con el tipo dinámico, la fuente se ajusta dinámicamente para que se muestren correctamente con independencia del tamaño. `UIFont.PreferredFontForTextStyle` debe utilizarse para obtener una fuente que está optimizada para el tamaño controlado por el usuario.

## <a name="summary"></a>Resumen

Este artículo tratan los cambios realizados en elementos de la interfaz de usuario de iOS 7. Examina algunos de los cambios realizados en las vistas y los controles en UIKit, resaltado de ambos cambios visuales, así como cambios en las API relacionadas. Por último, presenta nuevas API para trabajar con contenido de pantalla completa, la compatibilidad de color de nuevo un tono y tipo dinámico.

## <a name="related-links"></a>Vínculos relacionados

- [ImageViewer (ejemplo)](https://developer.xamarin.com/samples/monotouch/iOS7-ui-updates)
