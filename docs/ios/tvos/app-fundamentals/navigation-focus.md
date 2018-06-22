---
title: Trabajar con tvOS navegación y el foco en Xamarin
description: Este artículo explica el concepto de foco y cómo se utiliza para presentar y controlar la navegación dentro de una aplicación Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: DD72E95F-AE9B-47D2-B132-5FA5FBD8026E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 2af3306b605ee802b72b159d5f2759d71d292a64
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788737"
---
# <a name="working-with-tvos-navigation-and-focus-in-xamarin"></a>Trabajar con tvOS navegación y el foco en Xamarin

_Este artículo explica el concepto de foco y cómo se utiliza para presentar y controlar la navegación dentro de una aplicación Xamarin.tvOS._


Este artículo explica el concepto de [foco](#Focus-and-Selection) y cómo se utiliza para controlar [navegación](#Navigation) en la interfaz de usuario de la aplicación de Xamarin.tvOS. Examinaremos cómo usar los controles de navegación de tvOS integrados foco, resaltado y la selección para proporcionar navegación de interfaz de usuario de la aplicación Xamarin.tvOS.

[![](navigation-focus-images/intro01.png "aplicaciones de tvOS desplazamiento de la interfaz de usuario")](navigation-focus-images/intro01.png#lightbox)

A continuación, se echaremos un vistazo a cómo se puede utilizar el foco con [Parallax](#Focus-and-Parallax) y *superpuesto imágenes* para proporcionar sugerencias visuales para el estado actual de navegación para el usuario final.

Por último, analizaremos trabajar con [foco](#Working-with-Focus), [foco actualizaciones](#Working-with-Focus-Updates), [guías de foco](#Working-with-Focus-Guides), [foco en colecciones](#Working-with-Focus-in-Collections) y [ Habilitar Parallax](#Enabling-Parallax) para las vistas de la imagen de las aplicaciones de Xamarin.tvOS.

<a name="Navigation" />

## <a name="navigation"></a>Navegación

Los usuarios de la aplicación Xamarin.tvOS no será su interacción con su interfaz directamente como con iOS donde pulse imágenes en la pantalla del dispositivo, pero indirectamente de a través de la sala utilizando la [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote). Debe tener esto en cuenta al diseñar la interfaz de usuario de la aplicación para que fluyen de forma natural, pero aún mantiene al usuario sumergen en la experiencia de TV de Apple.

Una aplicación tvOS correcta implementa la navegación de una manera que sin problemas es compatible con el propósito de la aplicación y la estructura de los datos que y se muestra sin centra la atención en el panel de navegación propio. Diseñar la navegación por lo que parece natural y familiar sin que dominan la interfaz de usuario o foco el contenido y la experiencia del usuario de las aplicaciones de dibujo.

[![](navigation-focus-images/nav01.png "La aplicación de configuración de tvOS")](navigation-focus-images/nav01.png#lightbox)

Mientras usan generalmente un Televisor de Apple, el usuario navega a través de un conjunto apilado de las pantallas, cada uno de ellos presenta un conjunto determinado de contenido. A su vez, cada nueva pantalla puede dar lugar a uno o más pantallas secundarias de contenido mediante controles de interfaz de usuario estándar como [botones](~/ios/tvos/user-interface/buttons.md), [ficha barras](~/ios/tvos/user-interface/tab-bars.md), tablas, [vistas de colección](~/ios/tvos/user-interface/collection-views.md) o [ Dividir vistas](~/ios/tvos/user-interface/split-views.md).

Con cada nueva pantalla de datos, el usuario se desplaza más profunda en esta pila de pantallas. Mediante el uso de la **menú** botón del mando a distancia Siri, puede navegar de hacia atrás a través de la pila para volver a una pantalla anterior o el menú principal.

Apple sugiere teniendo presente lo siguiente al diseñar el panel de navegación de la aplicación tvOS:

- **Diseño de la navegación a hacer buscar contenido de manera rápida y fácil de** -los usuarios desean tener acceso al contenido dentro de la aplicación en el menor número de pesos, hace clic en y lectores de crédito como sea posible. Simplificar la navegación y organizar el contenido para el número mínimo de pantallas.
- **Crear un toque de uso de interfaz de fluido** -Asegúrese de que el usuario puede moverse entre _elementos puede recibir el foco_ con fricción mínima con el menor número de movimientos posible.
- **Diseño con el foco en mente** -puesto que el usuario está interactuando con contenido en la misma habitación, deba mover el foco a un elemento de la interfaz de usuario antes de interactuar con él mediante el mando a distancia Siri. Los usuarios obtendrán frustrados con la aplicación si requiere demasiados movimientos para alcanzar sus objetivos.
- **Proporcionar hacia atrás navegación mediante el botón de menú** : para crear una experiencia fácil y familiar, permitir a los usuarios navegar hacia atrás con la Siri Remote **menú** botón. Al presionar la **menú** botón siempre debe volver a la pantalla anterior o volver al menú principal de la aplicación. En el nivel superior de la aplicación, al presionar la **menú** botón debe volver a la pantalla Inicio TV de Apple.
- **Normalmente evitar mostrar un botón Atrás** : ya que, al presionar la **menú** botón del mando a distancia Siri navega hacia atrás a través de la pila de la pantalla, evitar que se muestre un control adicional que duplica este comportamiento. Una excepción a esta regla es para las compras pantallas o pantallas con acciones destructivas (por ejemplo, eliminando el contenido) donde un **cancelar** botón también se debe mostrar.
- **Mostrar las colecciones grandes en una sola pantalla, en lugar de muchas** -el Siri remoto se diseñó para facilitar desplazarse por una gran cantidad de contenido rápida y fácil con los gestos. Si la aplicación funciona con una gran colección de elementos puede recibir el foco, considere la posibilidad de mantenerlos en una sola pantalla en lugar de interrumpa muchas pantallas que requieren más navegación por parte del usuario.
- **Utilizar controles estándar para la navegación** -nuevo, para crear una experiencia de usuario más familiares y fácil, siempre que sea posible, utilice integrada `UIKit` controles como controles de página, ficha barras, segmentados controles, vistas de tablas, vistas de colección y división Vistas de navegación de la aplicación. Puesto que el usuario ya está familiarizado con estos elementos, intuitivamente podrá navegar por la aplicación.
- **Favorecer la exploración de contenido Horizontal** -debido a la naturaleza de Apple TV, Deslizar rápidamente de izquierda a derecha en el mando a distancia Siri resulte más natural que arriba o abajo. Tenga en cuenta esta opción al crear diseños de contenido de la aplicación.

<a name="Focus-and-Selection" />

## <a name="focus-and-selection"></a>El foco y la selección

En Apple TV, una imagen, botón u otro elemento de interfaz de usuario se considera _foco_ cuando es el destino de la navegación actual.

[![](navigation-focus-images/focus01.png "Ejemplo de foco y la selección")](navigation-focus-images/focus01.png#lightbox)

A diferencia de, los dispositivos de iOS donde el usuario interactúa directamente con elementos en pantalla táctil del dispositivo, los usuarios interactúan con tvOS elementos en la habitación mediante el mando a distancia Siri. Para presentar y controlar esta interacción del usuario, se usa Apple TV un _foco_ según el modelo.

Uso de movimientos y botón pulsa en la [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote), el usuario mueve el foco de un elemento de interfaz de usuario a otro. Una vez que ha cambiado el foco al elemento deseado, el usuario hace clic para seleccionar el contenido o activar la acción que representa ese elemento.

Como los cambios de foco, sutiles animaciones y efectos (por ejemplo, el efecto Parallax en imágenes) sirven para identificar claramente el elemento de la interfaz de usuario que tiene actualmente el foco.

Apple tiene las siguientes sugerencias para trabajar con el foco y la selección:

- **Utilizar controles de interfaz de usuario integrados para los efectos de movimiento** : mediante el uso de `UIKit` y la API de foco en la interfaz de usuario, el modelo de foco aplicará automáticamente el movimiento de forma predeterminada y los efectos visuales a los elementos de interfaz de usuario. Esto hace que la aplicación sentir nativo y familiar a los usuarios de la plataforma de TV de Apple y se permite para el movimiento fluido e intuitivo entre elementos puede recibir el foco.
- **Mover el foco en espera direcciones** -en Apple TV, casi todos los elementos usa manipulación indirecta. Por ejemplo, el usuario usa la instancia remota de Siri para mover el foco y el sistema desplaza automáticamente a la interfaz para mantener visible el elemento tiene el foco. Si la aplicación implementa este tipo de interacción, asegúrese de que el foco se desplaza en la dirección de los gestos del usuario. Por lo que si el usuario deslice el dedo en el enfoque de Siri remoto debe mover a la derecha (lo que puede provocar la pantalla para desplazarse a la izquierda). La única excepción a esta regla es los elementos de pantalla completa que usan la manipulación directa (donde Deslizar rápidamente la mueve el elemento hacia arriba).
- **Asegúrese de que el elemento con foco es Obvious** -porque los usuarios se interactúan con los elementos de interfaz de usuario a partir de ahí, es fundamental que el elemento tiene el foco destaque. Normalmente esto se controla automáticamente por integrado `UIKit` elementos. En controles personalizados, use características como el tamaño del elemento o de instantáneas para mostrar el foco.
- **Usar Parallax para realizar centrado elementos respondiendo** : pequeño, dar lugar a circulares movimientos en el servidor remoto Siri en movimiento suave, en tiempo real del elemento tiene el foco. Esto [efecto Parallax](#Focus-and-Parallax) está integrado en `UIKit` _superpuesto imágenes_ para dar al usuario una idea de conexión para el elemento tiene el foco.
- **Crear elementos puede recibir el foco del tamaño adecuado** -elementos grandes con un amplio espacio son más fáciles de seleccionar y desplazarse a elementos más pequeños.
- **Diseñar el elemento de interfaz de usuario para buscar buena bien centrado o Unfocused** -normalmente Apple TV representa el elemento enfocado al aumentar su tamaño. Asegúrese de que los elementos de interfaz de usuario de las aplicaciones un aspecto excelentes en cualquier tamaño de presentación y, si es necesario, proporcionan recursos para los elementos de un tamaño mayor.
- **Representar foco cambios toman con fluidez** -hacer la transición sin problemas entre los elementos de una animación **enfocado** y **Unfocused** estado para mantener las transiciones desde que se ha discordante.
- **No mostrar un Cursor** -los usuarios esperan navegar por la interfaz de usuario de la aplicación mediante el foco y no moviendo un cursor alrededor de la pantalla. La interfaz de usuario siempre debe utilizar el modelo de foco para presentar una experiencia de usuario coherente.

<a name="Working-with-Focus" />

### <a name="working-with-focus"></a>Trabajar con foco

Puede haber ocasiones en que desea crear un control personalizado que puede convertirse en un elemento puede recibir el foco. Si por lo que se invalide la `CanBecomeFocused` propiedad y valor devuelto `true`, else return `false`. Por ejemplo:

```csharp
public class myView : UIView
{
    public override bool CanBecomeFocused {
        get {return true;}
    }
}
```

En cualquier momento puede usar el `Focused` propiedad de un `UIKit` control para ver si es el elemento actual. Si `true` el elemento de interfaz de usuario tiene actualmente el foco, lo contrario no es así. Por ejemplo:

```csharp
// Is my view in focus?
if (myView.Focused) {
    // Do something
    ...
}
```

Mientras que directamente no se puede mover el foco a otro elemento de interfaz de usuario a través del código, puede especificar qué elemento de interfaz de usuario obtiene primero el foco cuando se carga una pantalla estableciendo su `PreferredFocusedView` propiedad `true`. Por ejemplo:

```csharp
// Make the play button the starting focus item
playButton.PreferredFocusedView = true;
```

<a name="Working-with-Focus-Updates" />

### <a name="working-with-focus-updates"></a>Trabajar con actualizaciones de foco 

Cuando el usuario hace que el foco hasta el desplazamiento de un elemento de interfaz de usuario a otro (por ejemplo, en respuesta a un gesto del mando a distancia Siri) un _eventos de actualización de foco_ se enviará el elemento pierde el foco y el elemento obtenga el foco.

Para elementos personalizados que heredan de `UIView` o `UIViewController`, puede invalidar varios métodos para trabajar con el evento de actualización de enfoque:

- **DidUpdateFocus** -se llamará a este método siempre que la vista Obtiene o pierde el foco.
- **ShouldUpdateFocus** -usar este método para definir dónde se puede mover foco.

Para solicitar que el motor de foco se desplaza nuevo enfoque a la `PreferredFocusedView` elemento de interfaz de usuario, llamada la `SetNeedsUpdateFocus` método del controlador de vista.

> [!IMPORTANT]
> Al llamar a `SetNeedsUpdateFocus` solo tiene efecto si se le llama con el controlador de vista contiene la vista que tiene actualmente el foco.




<a name="Working-with-Focus-Guides" />

### <a name="working-with-focus-guides"></a>Trabajar con guías de foco

El motor de foco integran tvOS es ideal para control de movimientos entre los elementos que se encuentran en una cuadrícula horizontales y verticales. Normalmente, es necesario que no hacen nada más que agregar que los elementos de interfaz de usuario para el diseño de la interfaz y el motor de foco controlará automáticamente el movimiento entre los elementos sin intervención del programador.

Sin embargo, puede haber ocasiones, debido a las necesidades para el diseño de la interfaz de usuario, cuando los elementos de interfaz de usuario no caen en una cuadrícula horizontales y verticales y podrían ser inaccesibles porque son diagonales entre sí. Esto sucede porque el motor de foco fue diseñado para controlar simple hacia arriba, hacia abajo, hacia la izquierda y derecha movimiento entre solo los elementos de interfaz de usuario.

Realice el siguiente diseño de interfaz de usuario para obtener un ejemplo:

 [![](navigation-focus-images/guide01.png "Trabajar con el ejemplo de guías de foco")](navigation-focus-images/guide01.png#lightbox)
 
Dado que la **más información** botón no se encuentra en una cuadrícula horizontal y vertical con el **comprar** botón sea accesible para el usuario. Sin embargo, esto puede corregir con facilidad utilizando un _foco guía_ para proporcionar sugerencias de movimiento al motor de foco. 

Una guía de foco (`UIFocusGuide`) expone un área no visibles de la vista como Focusable al motor de foco, por lo que el foco se redirija a otra vista.

Por lo tanto, para solucionar el ejemplo anterior, el código siguiente podría agregarse al controlador de vista para crear una guía de foco entre el **More Info** y **comprar** botones:

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

Primero, un nuevo `UIFocusGuide` se crea y se agrega a la colección de las guías de diseño de la vista mediante el `AddLayoutGuide` método.

A continuación, la Guía de foco arriba, izquierda, ancho y alto delimitadores se ajustan relativo a la **More Info** y **comprar** botones para colocarlo entre ellos. Vea:

[![](navigation-focus-images/guide02.png "Guía de foco de ejemplo")](navigation-focus-images/guide02.png#lightbox)

También es importante tener en cuenta que se está activando las nuevas restricciones que se crean mediante el establecimiento sus `Active` propiedad `true`:

```csharp
FocusGuide.LeftAnchor.ConstraintEqualTo (...).Active = true;
```

Con la nueva guía de foco establecido y se agregan a la vista, el controlador de vista `DidUpdateFocus` método se puede reemplazar y agrega el código siguiente para moverse entre la **More Info** y **comprar** botones:

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

Primero, este get de código la `NextFocusedView` desde el `UIFocusUpdateContext` que se haya pasado (`context`). Si esta vista es `null`, a continuación, no es necesario ningún procesamiento y el método ha salido.

Después, el `nextFocusableItem` se evalúa. Si coincide con el **más Info** o **comprar** botones, el foco se envía al botón opuesto con la Guía de foco `PreferredFocusedView` propiedad. Por ejemplo:

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

La hora de decidir si un elemento individual puede ser o no puede recibir el foco en un `UICollectionView` o un `UITableView`, podrá invalidar los métodos de la `UICollectionViewDelegate` o `UITableViewDelegate` respectivamente. Por ejemplo:

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

El `CanFocusItem` método `true` si el elemento actual puede tener el foco, en caso contrario devuelve `false`.

Si desea que un `UICollectionView` o un `UITableView` para recordar y restaurar el foco hasta el último elemento cuando se pierde y se vuelve a obtener el foco, establezca el `RemembersLastFocusedIndexPath` propiedad `true`.

<a name="Focus-and-Parallax" />

## <a name="focus-and-parallax"></a>El foco y Parallax

Como se mencionó anteriormente, un elemento de la interfaz de usuario se considera _foco_ cuando es el elemento actual durante un evento de navegación. Normalmente como un elemento entra en el foco, su tamaño aumenta ligeramente y que se eleve visualmente mediante una sombra. 

Si el usuario realiza un movimiento lento, circular en el servidor remoto Siri, el elemento con foco se influir hora de elegir en tiempo real en respuesta a este movimiento. Cuando se realiza el balanceo, un brillo iluminada se aplica a la imagen parezca que la superficie destacar. Después de un período determinado de inactividad, cualquier contenido fuera de foco, éste se atenúa y el elemento enfocado crecerá incluso mayor. 

Estos efectos funcionan conjuntamente para proporcionar una conexión entre el contenido en la pantalla de TV y el usuario que interactúa con la televisión de Apple desde el sofá mental.

En Apple TV, este efecto Parallax se utiliza en todo el sistema para transmitir una sensación de profundidad 3D y dynamics a elementos en el foco. tvOS usa una serie de transparente, [superpuesto imágenes](~/ios/tvos/app-fundamentals/icons-images.md#Layered-Images) que se mueve y se amplía de forma dinámica para crear este efecto.

Imágenes en capas son necesarias para el icono de la aplicación tvOS y es compatible con contenido dinámico de estante superior. Si bien no es necesario, Apple recomienda encarecidamente implementar imágenes en capas en cualquier otro elemento puede recibir el foco en la interfaz de usuario de la aplicación.

### <a name="enabling-parallax"></a>Habilitar Parallax

El `UIImageView` control (o cualquier control que herede de `UIImageView`) admite automáticamente el efecto de Parallax. De forma predeterminada, esta compatibilidad está deshabilitada para habilitarlo, utilice el código siguiente:

```csharp
myImageView.AdjustsImageWhenAncestorFocused = true;
```

Con esta propiedad establecida en `true`, la vista de la imagen automáticamente obtendrán el efecto Parallax cuando se selecciona por el usuario y en el foco.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha cubierto el concepto de foco y cómo se utiliza para controlar la navegación en la interfaz de usuario de la aplicación de Xamarin.tvOS. Examina cómo usar los controles de navegación de tvOS integrados foco, resaltado y la selección para proporcionar navegación. A continuación, examinando cómo foco puede usarse con Parallax e imágenes en capas para proporcionar sugerencias visuales para el estado actual de navegación para el usuario final. Por último, examinar trabajar con el foco, actualizaciones de foco, el foco en colecciones y habilitar Parallax.




## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guías de interfaz humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones tvos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
