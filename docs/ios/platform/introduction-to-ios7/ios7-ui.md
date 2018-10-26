---
title: iOS 7 información general de la interfaz de usuario
description: iOS 7 presenta una gran cantidad de cambios de la interfaz de usuario. Este artículo resalta algunos de los cambios más grandes tanto en la apariencia visual de los controles en las API que admiten el nuevo diseño.
ms.prod: xamarin
ms.assetid: FADCEA7C-8968-42A1-9E9E-F4BBAB7BCF2C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 132265c27e1d1ba3b8f3fc8db10d7b3cfa746197
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109021"
---
# <a name="ios-7-user-interface-overview"></a>iOS 7 información general de la interfaz de usuario

_iOS 7 presenta una gran cantidad de cambios de la interfaz de usuario. Este artículo resalta algunos de los cambios más grandes tanto en la apariencia visual de los controles en las API que admiten el nuevo diseño._

iOS 7 se centra en el contenido a través de chrome. Elementos de interfaz de usuario de iOS 7 colaterales chrome mediante la eliminación de atributos como bordes extraños, barras de estado y las barras de navegación, que reducen la cantidad de espacio utilizado en las vistas de contenido de la pantalla. En iOS 7, el contenido está diseñado para usar toda la pantalla.

iOS 7 introduce varios otros cambios: color se utiliza para distinguir los elementos de interfaz de usuario, en lugar de atributos, como los bordes del botón. Muchos elementos, como barras de navegación y las barras de estado, ahora son difuminadas y translúcidas o transparentes, con vistas de contenido tomando el área por debajo de ellos. Estas vistas de contenido se representan mediante las barras de difuminado, ofrecer una sensación de profundidad de la interfaz de usuario.

En este artículo se trata algunos de los cambios realizados en elementos de interfaz de usuario de iOS 7, así como diversas API relacionadas con el nuevo diseño de interfaz de usuario.

## <a name="view-and-control-changes"></a>Vista y Control de cambios

Todas las vistas de UIKit se ajustan a la nueva apariencia de iOS 7. Esta sección resaltan algunos de los cambios a estas vistas, así como las API relacionadas que han cambiado para admitir la nueva interfaz de usuario.

### <a name="uibutton"></a>UIButton

Botones creados a partir de la `UIButton` clase ya está sin bordes, no en segundo plano de forma predeterminada, como se muestra a continuación:

 ![](ios7-ui-images/button.png "Ejemplo UIButton")

El `UIButtonType.RoundedRect` estilo está desusado. Si se utiliza en iOS 7, `UIButtonType.RoundedRect` dará como resultado `UIButtonType.System` emplean, lo que genera el estilo del botón predeterminado sin en segundo plano o bordes visibles, como se indicó anteriormente.

### <a name="uibarbuttonitem"></a>UIBarButtonItem

Similar a `UIButton`, botones de la barra también están sin bordes, de forma predeterminada al nuevo `UIBarButtonItemStyle.Plain` estilo que se muestra a continuación:

 ![](ios7-ui-images/barbuttonplain.png "Ejemplo UIBarButtonItem")

Además, el `UIBarButtonItemStyle.Bordered` estilo está desusado. Establecer `UIBarButtonItemStyle.Bordered` en iOS 7 provocará la `UIBarButtonItemStyle.Plain` de estilo que se va a usar.

El `UIBarButtonItemStyle.Done` estilo no está desusado. Sin embargo, también creará un botón sin márgenes, solo con un estilo de texto en negrita, como se muestra:

 ![](ios7-ui-images/barbuttondone.png "UIBarButtonItem de ejemplo en el estilo de listo")

### <a name="uialertview"></a>UIAlertView

Además del cambio de estilo para la nueva apariencia y comportamiento de iOS 7, vistas de alerta ya no admiten la personalización a través de la vista secundaria. Aunque `UIAlertView` hereda `UIView`, al llamar a `AddSubview` en un `UIAlertView` no tiene ningún efecto. Por ejemplo, considere el siguiente código:

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

Esto genera una vista de alerta estándar, con la vista secundaria que se omite, tal como se muestra a continuación:

 ![](ios7-ui-images/alert.png "Ejemplo UIAlertView")
 
 Nota: UIAlertView ha quedado en desuso en iOS 8. Ver el [controlador alerta](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller) receta sobre el uso de una vista de alertas en iOS 8 y versiones posteriores.

### <a name="uisegmentedcontrol"></a>UISegmentedControl

Controles segmentados en iOS 7 son transparentes y admiten color de tono. Se utiliza el color del matiz del color de texto y el borde. Cuando se selecciona un segmento, el color se intercambia entre el fondo y el texto, con el color del matiz usado para resaltar el segmento seleccionado, tal como se muestra a continuación:

 ![](ios7-ui-images/segmentedcontrol.png "Ejemplo UISegmentedControl")

Además, la `UISegmentedControlStyle` ha quedado obsoleto en iOS 7.

### <a name="picker-views"></a>Vistas de selector

La API para las vistas de selector es apenas han cambiado; Sin embargo, las directrices de diseño 7 iOS ahora establecen vistas selector deben estar presente en línea, en lugar de como vistas de entrada se anima desde la parte inferior de la pantalla o a través de un nuevo controlador había insertado en la pila de un controlador de navegación, como en iOS anteriores versiones. Esto puede verse en la aplicación de calendario del sistema:

 ![](ios7-ui-images/inlinepicker.png "Esto puede verse en la aplicación de calendario del sistema")

### <a name="uisearchdisplaycontroller"></a>UISearchDisplayController

Ahora se muestra la barra de búsqueda dentro de la barra de navegación barra cuando el `UISearchDisplayController.DisplaysSearchBarInNavigationBar` propiedad está establecida en true. Cuando se establece en false: el valor predeterminado: se oculta la barra de navegación cuando se muestra el controlador de búsqueda.

Captura de pantalla siguiente muestra la barra de búsqueda dentro de un `UISearchDisplayController`:

 ![](ios7-ui-images/searchbar.png "Ejemplo UISearchDisplayController")

### <a name="uitableview"></a>UITableView

Las API en torno a `UITableView` son principalmente sin cambios; sin embargo, ha cambiado radicalmente el estilo para cumplir con el nuevo diseño de interfaz de usuario. La jerarquía de vistas internas también es un poco diferente. Este cambio no afectará a la mayoría de las aplicaciones, pero es algo a tener en cuenta.

#### <a name="grouped-table-style"></a>Estilo de tabla agrupada

Actualizó el estilo agrupado cambiado con el contenido que ahora se extiende a los bordes de la pantalla, tal como se muestra a continuación:

 ![](ios7-ui-images/table1.png "Estilo de ejemplo de tabla agrupada")

#### <a name="separatorinset"></a>SeparatorInset

Ahora se pueden sangría a los separadores de fila estableciendo el `UITableVIewCell.SeparatorInset` propiedad. Por ejemplo, el código siguiente podría usarse para aplicar sangría a las celdas desde el borde izquierdo:

```csharp
cell.SeparatorInset = new UIEdgeInsets (0, 50, 0, 0);
```

Esto se produce en la vista de tabla con las celdas con sangría tal como se muestra a continuación:

 ![](ios7-ui-images/separatorinset.png "UITableView SeparatorInset de ejemplo")

#### <a name="table-button-styles"></a>Estilos de botón de tabla

Los distintos botones que se utilizan en las vistas de tabla han cambiado. Captura de pantalla siguiente presenta una vista de tabla en modo de edición:

 ![](ios7-ui-images/table2.png "Esta captura de pantalla, presenta una vista de tabla en modo de edición")

### <a name="additional-control-changes"></a>Cambios de Control adicionales

Otros controles de UIKit han cambiado, incluidos controles deslizantes, conmutadores y steppers. Estos cambios son meramente visual. Para obtener más información, consulte Apple [Guía de transición de la interfaz de usuario de iOS 7](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/TransitionGuide/index.html).

## <a name="general-user-interface-changes"></a>Cambios en la interfaz de usuario general

Además de los cambios en UIKit, iOS 7 presenta una variedad de cambios visuales en la interfaz de usuario, incluidos:

-  Contenido de pantalla completa
-  Apariencia de la barra
-  Color del matiz

<a name="fullscreen" />

### <a name="full-screen-content"></a>Contenido de pantalla completa

iOS 7 está diseñado para permitir que las aplicaciones aprovechar toda la pantalla. Controladores de vista aparecen ahora superpuestos en una barra de estado y la barra de navegación - si existe: en lugar de debajo de las barras de estado y la navegación.

Mientras se prepara la aplicación para iOS 7, puede alinear las subvistas visualmente mediante *Interface Builder* o *Xamarin iOS Designer*. También puede usar una de las nuevas API para ayudar a controlar mediante programación el contenido de pantalla completa. Estas API se presentan a continuación.

#### <a name="toplayoutguide-and-bottomlayoutguide"></a>TopLayoutGuide y BottomLayoutGuide

 `TopLayoutGuide` y `BottomLayoutGuide` actuar como referencia para donde deben comenzar o finalizar, las vistas para que el contenido no se solapa con una translúcido `UIKit` barra, como en el ejemplo siguiente:

 [![](ios7-ui-images/clipped.png "Contenido de ejemplo no se solapa con una barra de UIKit translúcida")](ios7-ui-images/clipped.png#lightbox)

Estas API se pueden usar para calcular el desplazamiento de la vista de la parte superior o inferior de la pantalla y ajustan la selección de ubicación de contenido en consecuencia:

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

Podemos usar el valor calculado anteriormente para establecer nuestro `ImageView`del desplazamiento de la parte superior de la pantalla, por lo que toda la imagen está visible:

 [![](ios7-ui-images/good2.png "Desplazamiento de ImageViews de ejemplo desde la parte superior de la pantalla")](ios7-ui-images/good2.png#lightbox)

Hacer referencia a la [ImageViewer](https://developer.xamarin.com/samples/mobile/iOS7-ui-updates) para obtener un ejemplo funcional.

El valor de desplazamiento se genera dinámicamente después de que se ha agregado la vista a la jerarquía, por lo que al intentar leer `TopLayoutGuide` y `BottomLayoutGuide` valores en `ViewDidLoad` devolverá 0. Calcular el valor después de la vista haya cargado: por ejemplo, en el `ViewDidLayoutSubviews`.

> [!IMPORTANT]
> `TopLayoutGuide` y `BottomLayoutGuide` están en desuso en iOS 11 en favor de la nueva disposición de área segura. Apple ha manifestado que el uso de la zona segura es compatible con la versión de iOS anterior a iOS 11. Para obtener más información, consulte el [actualización de la aplicación para iOS 11](~/ios/platform/introduction-to-ios11/updating-your-app/visual-design.md#fullscreen) guía.

#### <a name="edgesforextendedlayout"></a>EdgesForExtendedLayout

Esta API especifica qué bordes de una vista deben ampliarse a pantalla completa, con independencia de la barra translucidez. En iOS 7, las barras de navegación y las barras de herramientas aparecen bajo por encima de la vista del controlador: a diferencia de iOS anteriores versiones, donde no ocupan el mismo espacio. El valor predeterminado muestra la aplicación de iOS 7 fotos `UIViewController.EdgesForExtendedLayout` valor, `UIRectEdge.All`. Esta opción rellena los cuatro bordes en la vista de contenido, crear el efecto que se superponen y pantalla completa:

 [![](ios7-ui-images/photos.png "Ejemplo EdgesForExtendedLayout")](ios7-ui-images/photos.png#lightbox)

Al puntear en la imagen quita las barras y muestra la imagen de pantalla completa:

 [![](ios7-ui-images/photos2.png "EdgesForExtendedLayout con las barras quitado")](ios7-ui-images/photos2.png#lightbox)

Dado que el contenido de pantalla completa es el valor predeterminado, las aplicaciones configuradas para iOS 6 tendrá parte de la vista que se recorta, como se muestra en la captura de pantalla siguiente:

 [![](ios7-ui-images/clipped.png "Las aplicaciones configuradas para iOS 6 tendrá parte de la vista que se recorta, como se muestra en esta captura de pantalla")](ios7-ui-images/clipped.png#lightbox)

Modificar el `UIViewController.EdgesForExtendedLayout` propiedad el ajusta este comportamiento. Podemos especificar que la vista no rellenar ninguno de los márgenes, por lo que evita nuestra vista Mostrar el contenido en el espacio ocupado por la navegación o las barras de herramientas (en cualquier orientación):

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
    this.EdgesForExtendedLayout = UIRectEdge.None;
}
```

En nuestra aplicación, veremos la vista nuevo se cambia de posición, para que toda la imagen sea visible:

 [![](ios7-ui-images/good.png "Ejemplo con toda la imagen visible")](ios7-ui-images/good.png#lightbox)

Tenga en cuenta que mientras los efectos de la `TopLayoutGuide/BottomLayoutGuide` y `EdgesForExtendedLayout` API son similares, están diseñados para rellenar distintos objetivos. Cambiar el `EdgesForExtendedLayout` configuración desde el valor predeterminado, se corregirá vistas recortadas en las aplicaciones diseñadas para iOS 6, pero un diseño buena iOS 7 debe respetar la estética de pantalla completa y proporcionar una pantalla completa experiencia de visualización, confiando en `TopLayoutGuide` y `BottomLayoutGuide`para colocar correctamente el contenido que está pensado para ser manipulados en un lugar cómodo para el usuario.

Hacer referencia a la [ImageViewer](https://developer.xamarin.com/samples/mobile/iOS7-ui-updates) para obtener un ejemplo funcional.

### <a name="status-and-navigation-bars"></a>Estado y las barras de navegación

La barra de estado y las barras de navegación se representan con transparencia. Barras de estado son transparentes, mientras que las barras de herramientas y barras de navegación son translúcido y difuminado para transmitir la sensación de profundidad de la interfaz de usuario. Captura de pantalla siguiente muestra este desenfoque y la transparencia, donde se muestra el color de fondo azul de la vista de colección a través de las barras el estado y la navegación, lo que proporciona una apariencia azul clara:

 ![](ios7-ui-images/transparent-navbar.png "Ejemplo de estado y navegación de la barra de desenfoque")

#### <a name="status-bar-styles"></a>Estilos de barra de estado

Junto con difuminado y transparencia, el primer plano de una barra de estado puede ser luz u oscuro (oscuro es el valor predeterminado). El estilo de barra de estado se puede establecer desde el controlador de vista. También puede establecer un controlador de vista si se oculta o muestra la barra de estado.

Por ejemplo, el siguiente código invalida el `PreferredStatusBarStyle` método del controlador de vista para hacer que la barra de estado muestre un primer plano claro:

```csharp
public override UIStatusBarStyle PreferredStatusBarStyle ()
{
    return UIStatusBarStyle.LightContent;
}
```

Esto hace que aparezca la barra de estado como sigue:

 ![](ios7-ui-images/light-status-bar.png "Ejemplo de barra de estado")

Para ocultar la barra de estado desde el código del controlador de vista, invalidar `PrefersStatusBarHidden`, tal y como se muestra a continuación:

```csharp
public override bool PrefersStatusBarHidden ()
{
    return true;
}
```

Esto oculta la barra de estado:

 ![](ios7-ui-images/status-bar-hidden.png "Barra de estado oculta")

### <a name="tint-color"></a>Color del matiz

Botones aparecen como texto de menos de chrome. Se puede controlar el color del texto con el nuevo `TintColor` propiedad `UIView`. Establecer el `TintColor` se aplica el color a la jerarquía de vistas completo para la vista que lo establece. Para aplicar un `TintColor`a lo largo de una aplicación, establézcala en el `Window`. También puede detectar cuándo cambia el color del matiz a través de la `UIView.TintColorDidChange` método.

Por ejemplo, la captura de pantalla siguiente muestra el efecto de cambiar el color del matiz en la vista de un controlador de navegación en púrpura:

 ![](ios7-ui-images/tint-color.png "Color púrpura en una vista de los controladores de navegación")

El color del matiz se puede aplicar a las imágenes así cuando la `RenderingMode` está establecido en `UIImageRenderingMode.AlwaysTemplate`.

> [!IMPORTANT]
> No puede establecerse mediante el color del matiz `UIAppearance`.


### <a name="dynamic-type"></a>Tipo dinámico

En iOS 7, el usuario puede especificar el tamaño del texto de la configuración del sistema. Con el tipo dinámico, la fuente se ajusta dinámicamente para que aparezcan correctamente, independientemente del tamaño. `UIFont.PreferredFontForTextStyle` debe usarse para obtener una fuente que está optimizada para el tamaño controlado por el usuario.

## <a name="summary"></a>Resumen

En este artículo se trata los cambios realizados en elementos de interfaz de usuario de iOS 7. Examinan algunos de los cambios realizados en las vistas y los controles de UIKit, resaltando el objeto visual cambia, así como cambios a las API relacionadas. Por último, presenta nuevas API para trabajar con contenido de pantalla completa y la compatibilidad con color de tono nuevo tipo dinámico.

## <a name="related-links"></a>Vínculos relacionados

- [ImageViewer (ejemplo)](https://developer.xamarin.com/samples/monotouch/iOS7-ui-updates)
