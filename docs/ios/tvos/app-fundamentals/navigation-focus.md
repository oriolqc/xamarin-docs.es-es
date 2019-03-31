---
title: Trabajar con tvOS, navegación y foco en Xamarin
description: Este artículo describe el concepto de foco y cómo se utiliza para presentar y controlar la navegación dentro de una aplicación Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: DD72E95F-AE9B-47D2-B132-5FA5FBD8026E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 3cb8d1c1d92146e70056c6cf562f2fa1cb028e7c
ms.sourcegitcommit: 946ce514fd6575aa6b93ff24181e02a60b24b106
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/30/2019
ms.locfileid: "58677877"
---
# <a name="working-with-tvos-navigation-and-focus-in-xamarin"></a>Trabajar con tvOS, navegación y foco en Xamarin

_Este artículo describe el concepto de foco y cómo se utiliza para presentar y controlar la navegación dentro de una aplicación Xamarin.tvOS._


Este artículo describe el concepto de [foco](#Focus-and-Selection) y cómo se usa para controlar [navegación](#Navigation) en la interfaz de usuario de una aplicación Xamarin.tvOS. Examinaremos cómo usar los controles de navegación de tvOS integrados foco, resaltado y la selección para proporcionar navegación de interfaz de usuario de la aplicación Xamarin.tvOS.

[![](navigation-focus-images/intro01.png "Desplazamiento de la interfaz de usuario de aplicaciones de tvOS")](navigation-focus-images/intro01.png#lightbox)

A continuación, le echamos un vistazo a cómo se puede usar el enfoque con [paralaje](#Focus-and-Parallax) y *imágenes con capas* para proporcionar indicaciones visuales para el estado actual de navegación para el usuario final.

Por último, veremos trabajando [foco](#Working-with-Focus), [foco actualizaciones](#Working-with-Focus-Updates), [foco guías](#Working-with-Focus-Guides), [foco en las colecciones de](#Working-with-Focus-in-Collections) y [ Habilitación de Parallax](#enabling-parallax) para las vistas de la imagen de sus aplicaciones Xamarin.tvOS.

<a name="Navigation" />

## <a name="navigation"></a>Navegación

Los usuarios de la aplicación Xamarin.tvOS no interactúa con su interfaz directamente como con iOS donde al pulsar imágenes en la pantalla del dispositivo, pero indirectamente desde a través de la sala utilizando el [Siri Remote](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote). Deberá tener esto en cuenta al diseñar la interfaz de usuario de la aplicación para que lo fluye de forma natural, pero conserva el usuario está inmerso en la experiencia de Apple TV de usuario.

Una aplicación de tvOS correcta implementa la navegación de forma que admite sin problemas el propósito de la aplicación y la estructura de los datos que presenta sin llamar a atención a la propia barra de navegación. Diseñar la navegación por lo que parece natural y familiar sin que dominan la interfaz de usuario o dibujo atención lejos el contenido y la experiencia del usuario de aplicaciones.

[![](navigation-focus-images/nav01.png "La aplicación de configuración de tvOS")](navigation-focus-images/nav01.png#lightbox)

Mientras se utiliza por lo general un dispositivo Apple TV, el usuario navega por un conjunto apilado de las pantallas, cada uno presenta un conjunto determinado de contenido. A su vez, cada nueva pantalla puede dar lugar a una o más pantallas secundarias de contenido mediante controles de interfaz de usuario estándar, como [botones](~/ios/tvos/user-interface/buttons.md), [barras de pestañas](~/ios/tvos/user-interface/tab-bars.md), tablas, [las vistas de colección](~/ios/tvos/user-interface/collection-views.md) o [ Dividir las vistas](~/ios/tvos/user-interface/split-views.md).

Con cada nueva pantalla de datos, el usuario se desplaza más profunda en esta pila de pantallas. Mediante el uso de la **menú** botón del mando a distancia Siri, puede navegan hacia atrás a través de la pila para volver a una pantalla anterior o el menú principal.

Apple sugiere lo teniendo este en cuenta al diseñar el panel de navegación de la aplicación tvOS:

- **Diseño de la navegación a la que buscar contenido manera rápida y fácil** -los usuarios desean tener acceso al contenido dentro de la aplicación en el menor número de pesos, hace clic en y pase el dedo como sea posible. Simplificar la navegación y organizar el contenido a los números mínimo de pantallas.
- **Creación de un toque de uso de interfaz de dinámica de fluidos** -Asegúrese de que un usuario puede desplazarse entre _elementos puede recibir el foco_ con fricción mínima utilizando el menor número de movimientos posible.
- **Diseño con el foco en mente** -dado que el usuario está interactuando con el contenido a través de la sala de reuniones, debe mover el foco a un elemento de interfaz de usuario antes de interactuar con él mediante el control remoto de Siri. Los usuarios obtendrán frustrados después de la aplicación si se requiere demasiados movimientos para alcanzar sus objetivos.
- **Proporcionar con versiones anteriores de navegación a través del botón de menú** : para crear una experiencia fácil y familiar, permitir a los usuarios navegar hacia atrás con el Siri Remote **menú** botón. Al presionar el **menú** botón siempre debe volver a la pantalla anterior o volver al menú principal de la aplicación. En el nivel superior de la aplicación, al presionar el **menú** botón debe volver a la pantalla Inicio TV de Apple.
- **Normalmente, evite mostrar un botón Atrás** : ya que, al presionar el **menú** botón del mando a distancia Siri navega hacia atrás a través de la pila de la pantalla, evitar que se muestre un control adicional que reproduce este comportamiento. Una excepción a esta regla es para comprar las pantallas o pantallas con acciones destructivas (como la eliminación de contenido) donde un **cancelar** botón también se debe mostrar.
- **Mostrar las colecciones grandes en una sola pantalla, en lugar de muchos** -The Siri remota se diseñó para que desplazarse por una gran cantidad de contenido rápida y sencilla gracias a los gestos. Si su aplicación funciona con una extensa colección de elementos puede recibir el foco, considere la posibilidad de mantenerlos en una sola pantalla en lugar de ellos dividir en varias pantallas que requieren una navegación más por parte del usuario.
- **Usar controles estándar para la navegación** -nuevo, para crear una experiencia de usuario más familiares y fácil, siempre que sea posible, use integrado `UIKit` controles como controles de página, barras de pestañas, controles segmentados, las vistas de tablas, vistas de colección y división Vistas para la navegación de la aplicación. Puesto que el usuario ya está familiarizado con estos elementos, intuitivamente podrá navegar por la aplicación.
- **Favorecer la exploración de contenido Horizontal** -debido a la naturaleza de Apple TV, deslice el dedo hacia izquierda a derecha en el equipo remoto Siri es más natural que arriba y abajo. Tenga en cuenta esta opción al diseñar diseños de contenido de la aplicación.

<a name="Focus-and-Selection" />

## <a name="focus-and-selection"></a>El foco y selección

En Apple TV, se considera que una imagen, botón u otro elemento de interfaz de usuario _foco_ cuando es el destino de la exploración actual.

[![](navigation-focus-images/focus01.png "Ejemplo de foco y selección")](navigation-focus-images/focus01.png#lightbox)

A diferencia de, los dispositivos iOS donde el usuario está interactuando directamente con elementos en pantalla táctil del dispositivo, los usuarios interactúan con los elementos de tvOS desde a distancia mediante el control remoto de Siri. Para presentar y controlar esta interacción del usuario, se usa Apple TV un _foco_ modelo basado en.

Mediante gestos y el botón se presiona en el [Siri Remote](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote), el usuario mueve el foco de un elemento de interfaz de usuario a otro. Una vez que ha cambiado el foco al elemento deseado, el usuario hace clic para seleccionar el contenido o activar la acción que representa ese elemento.

Como los cambios de foco, sutiles animaciones y efectos (por ejemplo, el efecto de paralaje en imágenes) se usan para identificar claramente el elemento de interfaz de usuario que tiene actualmente el foco.

Apple tiene las siguientes sugerencias para trabajar con el foco y la selección:

- **Utilizar controles de interfaz de usuario integrados para los efectos de movimiento** : mediante el uso de `UIKit` y la API de foco en la interfaz de usuario, el modelo de foco se aplicará automáticamente el movimiento de forma predeterminada y los efectos visuales a los elementos de interfaz de usuario. Esto hace que la aplicación sentir nativo y familiar a los usuarios de la plataforma de Apple TV y permite moverlas intuitiva y fluida entre los elementos de recibir el foco.
- **Mover el foco en espera direcciones** -en Apple TV, casi todos los elementos usa manipulación indirecta. Por ejemplo, el usuario usa el control remoto de Siri para mover el foco y el sistema desplaza automáticamente a la interfaz para mantener visible el elemento tiene el foco. Si la aplicación implementa este tipo de interacción, asegúrese de que el foco se mueve en dirección del gesto del usuario. Por lo que si el usuario deslice el dedo en el enfoque de Siri Remote debe mover a la derecha (que podría provocar que la pantalla para desplazarse a la izquierda). La única excepción a esta regla es que los elementos de pantalla completa que usan la manipulación directa (donde Deslizar seguridad mueve el elemento hacia arriba).
- **Asegúrese de que el elemento enfocado Obvious** -debido a los usuarios interactúan con los elementos de interfaz de usuario a partir de ahí, es fundamental que se destaca el elemento tiene el foco. Normalmente, esto se controlará automáticamente por integrado `UIKit` elementos. Para controles personalizados, usar características como el tamaño de elemento o de instantáneas para mostrar el foco.
- **Usar para realizar centrado elementos con capacidad de respuesta de paralaje** : pequeño, movimientos circulares en el equipo remoto Siri como resultado un movimiento suave, en tiempo real del elemento con foco. Esto [efecto de paralaje](#Focus-and-Parallax) está integrado en `UIKit` _imágenes con capas_ para proporcionar al usuario una sensación de conexión para el elemento con foco.
- **Crear elementos de recibir el foco del tamaño adecuado** -son más fáciles de seleccionar y desplazarse a elementos más pequeños elementos grandes con espacio suficiente.
- **Diseñar el elemento de interfaz de usuario para buscar buena bien centrado o Focused** -normalmente Apple TV representa el elemento enfocado al aumentar su tamaño. Asegúrese de que los elementos de interfaz de usuario de las aplicaciones muy bien en cualquier tamaño de presentación y, si es necesario, proporcionar recursos para los elementos de tamaño más grandes.
- **Representan los cambios de foco de forma fluida** -usar animación para atenuar sin problemas entre los elementos de un **Focused** y **Focused** estado para mantener las transiciones de ser discordante.
- **No mostrar un Cursor** -los usuarios esperan navegar por la interfaz de usuario de la aplicación con el foco y no moviendo un cursor alrededor de la pantalla. La interfaz de usuario siempre debe usar el modelo de foco para presentar una experiencia de usuario coherente.

<a name="Working-with-Focus" />

### <a name="working-with-focus"></a>Trabajar con foco

Puede haber ocasiones en los que desea crear un control personalizado que puede convertirse en un elemento puede recibir el foco. Si por lo que se invalide el `CanBecomeFocused` propiedad y devolución `true`, else return `false`. Por ejemplo:

```csharp
public class myView : UIView
{
    public override bool CanBecomeFocused {
        get {return true;}
    }
}
```

En cualquier momento puede usar el `Focused` propiedad de un `UIKit` control para ver si es el elemento actual. Si `true` el elemento de interfaz de usuario tiene actualmente el foco, de lo contrario, no. Por ejemplo:

```csharp
// Is my view in focus?
if (myView.Focused) {
    // Do something
    ...
}
```

Mientras que directamente no se puede mover el foco a otro elemento de interfaz de usuario a través del código, puede especificar qué elemento de interfaz de usuario primero obtiene el foco cuando una pantalla se carga estableciendo su `PreferredFocusedView` propiedad `true`. Por ejemplo:

```csharp
// Make the play button the starting focus item
playButton.PreferredFocusedView = true;
```

<a name="Working-with-Focus-Updates" />

### <a name="working-with-focus-updates"></a>Trabajar con las actualizaciones de foco 

Cuando el usuario hace que el enfoque pasar de un elemento de interfaz de usuario a otro (por ejemplo, en respuesta a un gesto en el equipo remoto Siri) un _eventos de actualización de foco_ se enviará el elemento pierde el foco y el elemento que se obtiene el foco.

Para elementos personalizados que heredan de `UIView` o `UIViewController`, puede reemplazar varios métodos para trabajar con el evento de actualización de foco:

- **DidUpdateFocus** : este método se llamará siempre que la vista Obtiene o pierde el foco.
- **ShouldUpdateFocus** : Use este método para definir dónde se puede mover el foco.

Para volver a solicitar que el motor de foco se mueve el foco a la `PreferredFocusedView` elemento de interfaz de usuario, llamada la `SetNeedsUpdateFocus` método del controlador de vista.

> [!IMPORTANT]
> Una llamada a `SetNeedsUpdateFocus` solo tiene efecto si se llama en el controlador de vista contiene la vista que tiene actualmente el foco.




<a name="Working-with-Focus-Guides" />

### <a name="working-with-focus-guides"></a>Trabajar con las guías de foco

El motor de enfoque integrado en tvOS es ideal para realizar movimientos entre los elementos que se encuentran en una cuadrícula horizontal y vertical. Por lo general, deberá hacer nada más que agregar que los elementos de interfaz de usuario para el diseño de la interfaz y el motor de foco controlará automáticamente el movimiento entre los elementos sin intervención del programador.

Sin embargo, puede haber ocasiones, debido a las necesidades de su diseño de interfaz de usuario, cuando los elementos de interfaz de usuario no se encuentran en una cuadrícula horizontal y vertical y podrían ser inaccesibles porque son diagonales entre sí. Esto sucede porque el motor de foco fue diseñado para controlar simple hacia arriba, abajo, izquierda y derecha movimiento entre solo los elementos de interfaz de usuario.

Realice el siguiente diseño de interfaz de usuario para obtener un ejemplo:

 [![](navigation-focus-images/guide01.png "Trabajar con las guías de foco de ejemplo")](navigation-focus-images/guide01.png#lightbox)
 
Porque el **More Info** botón no se encuentra en una cuadrícula horizontal y vertical con el **comprar** botón se quedaría inaccesible para el usuario. Sin embargo, esto puede corregir con facilidad mediante un _guía_ para proporcionar sugerencias de movimiento al motor de foco. 

Una guía de enfoque (`UIFocusGuide`) expone un área de la vista que no son visibles como Focusable al motor de foco, lo que permite que el foco se redirijan a otra vista.

Por lo tanto, para solucionar el ejemplo anterior, el código siguiente podría agregarse al controlador de vista para crear una guía de foco entre la **More Info** y **comprar** botones:

```csharp
public UIFocusGuide FocusGuide = new UIFocusGuide ();
...

public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Add Focus Guide to layout
    View.AddLayoutGuide (FocusGuide);

    // Define Focus Guide that will allow the user to move
    // between the More Info and Buy buttons.
    FocusGuide.LeftAnchor.ConstraintEqualTo (BuyButton.LeftAnchor).Active = true;
    FocusGuide.TopAnchor.ConstraintEqualTo (MoreInfoButton.TopAnchor).Active = true;
    FocusGuide.WidthAnchor.ConstraintEqualTo (BuyButton.WidthAnchor).Active = true;
    FocusGuide.HeightAnchor.ConstraintEqualTo (MoreInfoButton.HeightAnchor).Active = true;
}
```

Primero, un nuevo `UIFocusGuide` se crea y agrega a la colección de guía de diseño de la vista mediante el `AddLayoutGuide` método.

A continuación, se ajustan la Guía de foco Top, Left, Width y Height delimitadores respecto a la **More Info** y **comprar** botones para colocarlo entre ellos. Vea:

[![](navigation-focus-images/guide02.png "Guía de foco de ejemplo")](navigation-focus-images/guide02.png#lightbox)

También es importante tener en cuenta que se va a activar las restricciones de la nueva medida que se crean mediante el establecimiento sus `Active` propiedad `true`:

```csharp
FocusGuide.LeftAnchor.ConstraintEqualTo (...).Active = true;
```

Con la nueva guía de foco establece y agrega a la vista, el controlador de vista `DidUpdateFocus` se puede invalidar el método y el código siguiente se agrega a desplazarse por la **More Info** y **comprar** botones:

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    base.DidUpdateFocus (context, coordinator);

    // Get next focusable item from context
    var nextFocusableItem = context.NextFocusedView;

    // Anything to process?
    if (nextFocusableItem == null) return;

    // Decide the next focusable item based on the current
    // item with focus
    if (nextFocusableItem == MoreInfoButton) {
        // Move from the More Info to Buy button
        FocusGuide.PreferredFocusedView = BuyButton;
    } else if (nextFocusableItem == BuyButton) {
        // Move from the Buy to the More Info button
        FocusGuide.PreferredFocusedView = MoreInfoButton;
    } else {
        // No valid move
        FocusGuide.PreferredFocusedView = null;
    }
}
```

Este get de código, primero la `NextFocusedView` desde el `UIFocusUpdateContext` que se ha pasado (`context`). Si esta vista es `null`, no es necesario ningún procesamiento y se cerró el método.

A continuación, el `nextFocusableItem` se evalúa. Si coincide con alguno la **More Info** o **comprar** botones, se envía el foco al botón opuesto con la Guía de foco `PreferredFocusedView` propiedad. Por ejemplo:

```csharp
// Move from the More Info to Buy button
FocusGuide.PreferredFocusedView = BuyButton;
```

En caso de que ningún botón es el origen del cambio de foco, el `PreferredFocusedView` propiedad está desactivada:

```csharp
// No valid move
FocusGuide.PreferredFocusedView = null;
```

<a name="Working-with-Focus-in-Collections" />

### <a name="working-with-focus-in-collections"></a>Trabajar con el foco en colecciones

La hora de decidir si un elemento individual puede ser o no puede recibir el foco en un `UICollectionView` o un `UITableView`, deberá invalidar los métodos de la `UICollectionViewDelegate` o `UITableViewDelegate` respectivamente. Por ejemplo:

```csharp
public class CardHandDelegate : UICollectionViewDelegateFlowLayout
{
    ...
    public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
    {
        if (indexPath == null) {
            return false;
        } else {
            var controller = collectionView as CardHandViewController;
            return !controller.Hand [indexPath.Row].IsFaceDown;
        }
    }
}
```

El `CanFocusItem` devuelve del método `true` si el elemento actual puede tener el foco, en caso contrario devuelve `false`.

Si desea que un `UICollectionView` o un `UITableView` para recordar y restaura el foco al último elemento cuando pierde y vuelva a obtener el foco, establezca el `RemembersLastFocusedIndexPath` propiedad `true`.

<a name="Focus-and-Parallax" />

## <a name="focus-and-parallax"></a>El foco y paralaje

Como se indicó anteriormente, un elemento de interfaz de usuario se considera _foco_ cuando es el elemento actual durante un evento de navegación. Normalmente como un elemento entra el foco, su tamaño aumenta ligeramente y se eleva visualmente mediante una sombra. 

Si el usuario realiza un movimiento lento, circular en el equipo remoto Siri, el elemento enfocado se sway en tiempo real en respuesta a este movimiento. Cuando se produce el balanceo, se aplica un brillo iluminada a su imagen parezca la superficie destacar. Después de un período determinado de inactividad, cualquier contenido fuera de foco se atenúa y el elemento enfocado crecerá incluso mayor. 

Estos efectos funcionan conjuntamente para proporcionar una conexión entre el contenido en la pantalla de TV y el usuario que interactúa con Apple TV desde el sofá mental.

En Apple TV, este efecto de paralaje se utiliza en todo el sistema para transmitir una sensación de profundidad 3D y dynamics a elementos en el foco. tvOS usa una serie de transparente, [imágenes con capas](~/ios/tvos/app-fundamentals/icons-images.md#Layered-Images) que se mueve y se puede escalar dinámicamente para crear este efecto.

Imágenes superpuestas son necesarias para el icono de la aplicación tvOS y es compatible con contenido dinámico de estante superior. Aunque no es necesario, Apple recomienda implementar imágenes en capas en cualquier otro elemento enfocable situado en la interfaz de usuario de la aplicación.

### <a name="enabling-parallax"></a>Habilitación de paralaje

El `UIImageView` control (o cualquier control que hereda de `UIImageView`) admite automáticamente el efecto de paralaje. De forma predeterminada, esta compatibilidad está deshabilitada para habilitarla, use el siguiente código:

```csharp
myImageView.AdjustsImageWhenAncestorFocused = true;
```

Con esta propiedad establecida en `true`, la vista de imagen obtendrán automáticamente el efecto de paralaje cuando está seleccionado por el usuario y en el foco.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha cubierto el concepto de foco y cómo se usa para controlar la navegación en la interfaz de usuario de una aplicación Xamarin.tvOS. Examina cómo usar los controles de navegación de tvOS integrados foco, resaltado y la selección para proporcionar navegación. A continuación, examinamos cómo el foco puede utilizarse con paralaje e imágenes con capas para proporcionar indicaciones visuales para el estado actual de navegación para el usuario final. Por último, examina cómo trabajar con el foco, las actualizaciones de foco, el foco en las colecciones y habilitación de paralaje.




## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicación de tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
