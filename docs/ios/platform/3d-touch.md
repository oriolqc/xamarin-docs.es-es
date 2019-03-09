---
title: Introducción a 3D Touch en Xamarin.iOS
description: En este artículo se describe cómo usar gestos de toque 3D introducidos en el iPhone 6s y iPhone 6s Plus. Estos gestos permiten la sensibilidad a la presión, inspección y pop y acciones rápidas.
ms.prod: xamarin
ms.assetid: 806D051E-3791-40F7-9776-4E4D3E56F7F3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 6a12d157b3de7c3841f5d69d209c01fbc612f79b
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668743"
---
# <a name="introduction-to-3d-touch-in-xamarinios"></a>Introducción a 3D Touch en Xamarin.iOS

_En este artículo se describe el uso de la nueva gestos iPhone 6s y iPhone 6s Plus 3D Touch en su aplicación._

[![](3d-touch-images/info01.png "Ejemplos de 3D Touch en aplicaciones habilitadas")](3d-touch-images/info01.png#lightbox)

Este artículo se proporciona e Introducción al uso de las nuevas API 3D Touch para agregar gestos confidenciales presión a las aplicaciones de Xamarin.iOS que se ejecutan en el nuevo iPhone 6s y iPhone 6s Plus dispositivos.

Con 3D Touch, una aplicación de iPhone ahora es capaz de no solo indicar que el usuario toca la pantalla del dispositivo, pero es capaz de detectar la presión está ejerciendo el usuario y responder a los niveles de presión diferentes.

3D Touch proporciona las siguientes características a la aplicación:

- [Sensibilidad a la presión](#Pressure-Sensitivity) : aplicaciones ahora se pueden medir hard o claro, el usuario toca la pantalla y beneficiarse de dicha información.
  Por ejemplo, una aplicación de dibujo puede hacer que una línea más gruesa o más delgados en función de lo difícil el usuario está tocando la pantalla.
- [Inspeccionar y Pop](#Peek-and-Pop) -la aplicación ahora puede permitir que el usuario interactúe con sus datos sin tener que salir de su contexto actual. Al pulsar el disco duro en la pantalla de la pantalla, puede inspeccionar en el elemento que les interesa (por ejemplo, para obtener una vista previa de un mensaje). Si presiona más difícil, puede extraer en el elemento.
- [Acciones rápidas](#Quick-Actions) -pensar de acciones rápidas, como los menús contextuales que pueden ser sacado de la copia de seguridad cuando un usuario del botón secundario en un elemento de una aplicación de escritorio.
  Con acciones rápidas, puede agregar accesos directos a funciones en la aplicación directamente desde el icono de la aplicación en la pantalla Inicio.
- [Pruebas de 3D Touch en el simulador](#Testing-3D-Touch-in-the-Simulator) -con el hardware de Mac correcto, puede probar aplicaciones 3D Touch habilitada en el simulador de iOS.

<a name="Pressure-Sensitivity" />

## <a name="pressure-sensitivity"></a>Sensibilidad a la presión

Como se indicó anteriormente, con nuevas propiedades de la [UITouch](xref:UIKit.UITouch) clase puede medir la cantidad de presión que el usuario se aplica a la pantalla del dispositivo de iOS y use la información de la interfaz de usuario. Por ejemplo, realizando un trazo de pincel más translúcido u opaco basándose en la cantidad de presión.

[![](3d-touch-images/pressure01.png "Un trazo de pincel que se representan como más translúcido u opaco según la cantidad de presión")](3d-touch-images/pressure01.png#lightbox)

Como resultado de 3D Touch, si la aplicación se ejecuta en iOS 9 (o posterior) y el dispositivo iOS es capaz de apoyo 3D Touch, hará que los cambios en la presión de la `TouchesMoved` evento.

Por ejemplo, al supervisar el `TouchesMoved` eventos de un [UIView](xref:UIKit.UIView), puede usar el código siguiente para obtener la presión actual que se aplica al usuario a la pantalla:

```csharp
public override void TouchesMoved (NSSet touches, UIEvent evt)
{
    base.TouchesMoved (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        // Get the pressure
        var force = touch.Force;
        var maxForce = touch.MaximumPossibleForce;

        // Do something with the touch and the pressure
        ...
    }
}
```

El `MaximumPossibleForce` propiedad devuelve el valor más alto posible para el `Force` propiedad de la [UITouch](xref:UIKit.UITouch) basándose en el dispositivo iOS que se está ejecutando la aplicación.

> [!IMPORTANT]
> Hará que los cambios en la presión de la `TouchesMoved` evento, incluso si la X / coordenadas Y no han cambiado. Debido a este cambio de comportamiento, las aplicaciones de iOS deben estar preparadas para la `TouchesMoved` eventos que se debe invocar con más frecuencia y de la X coordenadas X/y a ser el mismo que el último `TouchesMoved` llamar.




Para obtener más información, consulte Apple [TouchCanvas: Uso UITouch de manera eficiente y eficaz](https://developer.apple.com/library/prerelease/ios/samplecode/TouchCanvas/) aplicación de ejemplo y [referencia de clase UITouch](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITouch_Class/).

<a name="Peek-and-Pop" />

## <a name="peek-and-pop"></a>Peek y Pop

3D Touch proporciona nuevas formas para interactuar con la información dentro de la aplicación más rápida que nunca, sin tener que navegar desde su ubicación actual.

Por ejemplo, si la aplicación muestra una tabla de mensajes, el usuario puede presionar disco duro en un elemento para obtener una vista previa de su contenido en una vista de superposición (que Apple como que hace referencia a un *Peek*).

[![](3d-touch-images/peekandpop01.png "Un ejemplo de inspección de contenido")](3d-touch-images/peekandpop01.png#lightbox)

Si el usuario presiona más difícil, deberá especificar la vista de mensaje normal (que se conoce como *Pop*-ping en la vista).

### <a name="checking-for-3d-touch-availability"></a>Comprobar la disponibilidad 3D Touch

Cuando se trabaja con un `UIViewController` puede usar el código siguiente para ver si la aplicación se está ejecutando en el dispositivo de iOS es compatible con 3D Touch:

```csharp
public override void TraitCollectionDidChange(UITraitCollection previousTraitCollection)
{
    //Important: call the base function
    base.TraitCollectionDidChange(previousTraitCollection);

    //See if the new TraitCollection value includes force touch
    if (TraitCollection.ForceTouchCapability == UIForceTouchCapability.Available) {
        //Do something with 3D touch, for instance...
        RegisterForPreviewingWithDelegate (this, View);
        ...
```

Este método se puede llamar antes *o después* `ViewDidLoad()`.

### <a name="handling-peek-and-pop"></a>POP e inspección de control

En un dispositivo iOS que puede controlar 3D Touch, podemos usar una instancia de la `UIViewControllerPreviewingDelegate` clase para controlar la presentación de **Peek** y **Pop** detalles de elemento. Por ejemplo, si tuviéramos un controlador de vista de tabla denominada `MasterViewController ` podríamos usar el siguiente código para admitir **Peek** y **Pop**:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;

namespace DTouch
{
    public class PreviewingDelegate : UIViewControllerPreviewingDelegate
    {
        #region Computed Properties
        public MasterViewController MasterController { get; set; }
        #endregion

        #region Constructors
        public PreviewingDelegate (MasterViewController masterController)
        {
            // Initialize
            this.MasterController = masterController;
        }

        public PreviewingDelegate (NSObjectFlag t) : base(t)
        {
        }

        public PreviewingDelegate (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        /// Present the view controller for the "Pop" action.
        public override void CommitViewController (IUIViewControllerPreviewing previewingContext, UIViewController viewControllerToCommit)
        {
            // Reuse Peek view controller for details presentation
            MasterController.ShowViewController(viewControllerToCommit,this);
        }

        /// Create a previewing view controller to be shown at "Peek".
        public override UIViewController GetViewControllerForPreview (IUIViewControllerPreviewing previewingContext, CGPoint location)
        {
            // Grab the item to preview
            var indexPath = MasterController.TableView.IndexPathForRowAtPoint (location);
            var cell = MasterController.TableView.CellAt (indexPath);
            var item = MasterController.dataSource.Objects [indexPath.Row];

            // Grab a controller and set it to the default sizes
            var detailViewController = MasterController.Storyboard.InstantiateViewController ("DetailViewController") as DetailViewController;
            detailViewController.PreferredContentSize = new CGSize (0, 0);

            // Set the data for the display
            detailViewController.SetDetailItem (item);
            detailViewController.NavigationItem.LeftBarButtonItem = MasterController.SplitViewController.DisplayModeButtonItem;
            detailViewController.NavigationItem.LeftItemsSupplementBackButton = true;

            // Set the source rect to the cell frame, so everything else is blurred.
            previewingContext.SourceRect = cell.Frame;

            return detailViewController;
        }
        #endregion
    }
}
```

El `GetViewControllerForPreview` método se usa para realizar la **Peek** operación. Se obtiene acceso a los datos de copia de seguridad y de celda de tabla y, a continuación, carga el `DetailViewController` desde el guión gráfico actual. Estableciendo el `PreferredContentSize` a (0,0), te pedimos para el valor predeterminado **Peek** tamaño de la vista. Por último, se desenfoca todo excepto la celda que mostramos con `previewingContext.SourceRect = cell.Frame` y se devuelve la nueva vista para su presentación.

El `CommitViewController` vuelve a usar la vista se creó en el **Peek** para el **Pop** ver cuando el usuario presiona más difícil.

### <a name="registering-for-peek-and-pop"></a>Registrarse para la inspección y Pop

Desde el controlador de vista que va a permitir que el usuario **Peek** y **Pop** elementos desde, se debe registrar para este servicio. En el ejemplo anterior de un controlador de vista de tabla (`MasterViewController`), se usaría el código siguiente:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Check to see if 3D Touch is available
    if (TraitCollection.ForceTouchCapability == UIForceTouchCapability.Available) {
        // Register for Peek and Pop
        RegisterForPreviewingWithDelegate(new PreviewingDelegate(this), View);
    }
    ...

}
```

Aquí estamos llamando a la `RegisterForPreviewingWithDelegate` método con una instancia de la `PreviewingDelegate` hemos creado anteriormente. En dispositivos iOS compatibles con 3D Touch, el usuario puede presionar disco duro en un elemento para Peek en él. Si presiona la tecla aún más difícil, el elemento se mostrará el mensaje en ella estándar mostrar la vista.

Para obtener más información, consulte nuestra [iOS 9 ejemplo ApplicationShortcuts](https://developer.xamarin.com/samples/monotouch/iOS9/ViewControllerPreview/) y Apple [ViewControllerPreviews: Mediante la vista previa de las API de UIViewController](https://developer.apple.com/library/prerelease/ios/samplecode/ViewControllerPreviews/Introduction/Intro.html) aplicación de ejemplo, [referencia de clase UIPreviewAction](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewAction_Class/), [referencia de clase UIPreviewActionGroup](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionGroup_Class/) y [UIPreviewActionItem Referencia del protocolo](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionItem_Protocol/).

<a name="Quick-Actions" />

## <a name="quick-actions"></a>Acciones rápidas

Con 3D Touch y acciones rápidas, puede agregar comunes, rápido y fácil a los métodos abreviados de acceso a las funciones en la aplicación desde el icono de pantalla de inicio en el dispositivo iOS.

Como se indicó anteriormente, se puede considerar acciones rápidas, como los menús contextuales que pueden ser sacado de la copia de seguridad cuando un usuario del botón secundario en un elemento de una aplicación de escritorio. Se deben utilizar acciones rápidas para proporcionar accesos directos a las funciones o características de la aplicación más comunes.

[![](3d-touch-images/quickactions01.png "Un ejemplo de un menú de acciones rápidas")](3d-touch-images/quickactions01.png#lightbox)


### <a name="defining-static-quick-actions"></a>Definir acciones rápidas estáticas

Si una o varias de las acciones rápidas requeridos por la aplicación son estáticos y no es necesario cambiar, puede definirlas en la aplicación `Info.plist` archivo. Edite este archivo en un editor externo y agregue las siguientes claves:

```xml
<key>UIApplicationShortcutItems</key>
<array>
    <dict>
        <key>UIApplicationShortcutItemIconType</key>
        <string>UIApplicationShortcutIconTypeSearch</string>
        <key>UIApplicationShortcutItemSubtitle</key>
        <string>Will search for an item</string>
        <key>UIApplicationShortcutItemTitle</key>
        <string>Search</string>
        <key>UIApplicationShortcutItemType</key>
        <string>com.company.appname.000</string>
    </dict>
    <dict>
        <key>UIApplicationShortcutItemIconType</key>
        <string>UIApplicationShortcutIconTypeShare</string>
        <key>UIApplicationShortcutItemSubtitle</key>
        <string>Will share an item</string>
        <key>UIApplicationShortcutItemTitle</key>
        <string>Share</string>
        <key>UIApplicationShortcutItemType</key>
        <string>com.company.appname.001</string>
    </dict>
</array>
```

Aquí se define dos elementos de acción rápida estáticos con las claves siguientes:

- `UIApplicationShortcutItemIconType` : Define el icono que se mostrará en el elemento de acción rápida como uno de los siguientes valores:
  - `UIApplicationShortcutIconTypeAdd`
  - `UIApplicationShortcutIconTypeAlarm`
  - `UIApplicationShortcutIconTypeAudio`
  - `UIApplicationShortcutIconTypeBookmark`
  - `UIApplicationShortcutIconTypeCapturePhoto`
  - `UIApplicationShortcutIconTypeCaptureVideo`
  - `UIApplicationShortcutIconTypeCloud`
  - `UIApplicationShortcutIconTypeCompose`
  - `UIApplicationShortcutIconTypeConfirmation`
  - `UIApplicationShortcutIconTypeContact`
  - `UIApplicationShortcutIconTypeDate`
  - `UIApplicationShortcutIconTypeFavorite`
  - `UIApplicationShortcutIconTypeHome`
  - `UIApplicationShortcutIconTypeInvitation`
  - `UIApplicationShortcutIconTypeLocation`
  - `UIApplicationShortcutIconTypeLove`
  - `UIApplicationShortcutIconTypeMail`
  - `UIApplicationShortcutIconTypeMarkLocation`
  - `UIApplicationShortcutIconTypeMessage`
  - `UIApplicationShortcutIconTypePause`
  - `UIApplicationShortcutIconTypePlay`
  - `UIApplicationShortcutIconTypeProhibit`
  - `UIApplicationShortcutIconTypeSearch`
  - `UIApplicationShortcutIconTypeShare`
  - `UIApplicationShortcutIconTypeShuffle`
  - `UIApplicationShortcutIconTypeTask`
  - `UIApplicationShortcutIconTypeTaskCompleted`
  - `UIApplicationShortcutIconTypeTime`
  - `UIApplicationShortcutIconTypeUpdate`

        ![](3d-touch-images/uiapplicationshortcuticontype.png "UIApplicationShortcutIconType imagery")

* `UIApplicationShortcutItemSubtitle` : Define el subtítulo para el elemento.
* `UIApplicationShortcutItemTitle` : Define el título del elemento.
* `UIApplicationShortcutItemType` : Es un valor de cadena que se utilizará para identificar el elemento en nuestra aplicación. Para obtener más información, vea la sección siguiente.

> [!IMPORTANT]
> Elementos de acceso directo de acción rápidos que se establecen en el `Info.plist` no se puede acceder al archivo con el `Application.ShortcutItems` propiedad. Solo se pasan en a la `HandleShortcutItem` controlador de eventos.





### <a name="identifying-quick-action-items"></a>Identificación de los elementos de acción rápida

Como vimos anteriormente, cuando define los elementos de acción rápida en la aplicación `Info.plist`, asigna un valor de cadena para el `UIApplicationShortcutItemType` clave para identificarlos.

Para facilitar los identificadores trabajar con código, agregue una clase denominada `ShortcutIdentifier` a la aplicación del proyecto y dele un aspecto similar al siguiente:

```csharp
using System;

namespace AppSearch
{
    public static class ShortcutIdentifier
    {
        public const string First = "com.company.appname.000";
        public const string Second = "com.company.appname.001";
        public const string Third = "com.company.appname.002";
        public const string Fourth = "com.company.appname.003";
    }
}
```

<a name="Handling-a-Quick-Action" />

### <a name="handling-a-quick-action"></a>Control de una acción rápida

A continuación, deberá modificar su aplicación `AppDelegate.cs` archivo para controlar el usuario selecciona un elemento de acción rápida desde el icono de la aplicación en la pantalla de inicio.

Realice las siguientes modificaciones:

```csharp
using System;
...

public UIApplicationShortcutItem LaunchedShortcutItem { get; set; }

public bool HandleShortcutItem(UIApplicationShortcutItem shortcutItem) {
    var handled = false;

    // Anything to process?
    if (shortcutItem == null) return false;

    // Take action based on the shortcut type
    switch (shortcutItem.Type) {
    case ShortcutIdentifier.First:
        Console.WriteLine ("First shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Second:
        Console.WriteLine ("Second shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Third:
        Console.WriteLine ("Third shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Fourth:
        Console.WriteLine ("Forth shortcut selected");
        handled = true;
        break;
    }

    // Return results
    return handled;
}

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    var shouldPerformAdditionalDelegateHandling = true;

    // Get possible shortcut item
    if (launchOptions != null) {
        LaunchedShortcutItem = launchOptions [UIApplication.LaunchOptionsShortcutItemKey] as UIApplicationShortcutItem;
        shouldPerformAdditionalDelegateHandling = (LaunchedShortcutItem == null);
    }

    return shouldPerformAdditionalDelegateHandling;
}

public override void OnActivated (UIApplication application)
{
    // Handle any shortcut item being selected
    HandleShortcutItem(LaunchedShortcutItem);

    // Clear shortcut after it's been handled
    LaunchedShortcutItem = null;
}

public override void PerformActionForShortcutItem (UIApplication application, UIApplicationShortcutItem shortcutItem, UIOperationHandler completionHandler)
{
    // Perform action
    completionHandler(HandleShortcutItem(shortcutItem));
}
```

En primer lugar, definimos una pública `LaunchedShortcutItem` propiedad para realizar un seguimiento del último elemento de acción rápida seleccionado por el usuario. A continuación, se muestra cómo reemplazar el `FinishedLaunching` método y vea si `launchOptions` se han pasado y contiene un elemento de acción rápida. Si lo hacen, almacenamos la acción rápida en el `LaunchedShortcutItem` propiedad.

A continuación, se muestra cómo reemplazar el `OnActivated` método y pasar cualquiera seleccionado el elemento de inicio rápido para la `HandleShortcutItem` método para actuar sobre ellos. Actualmente sólo estamos escribiendo un mensaje a la **consola**. En una aplicación real controlaría cualquier acción era necesaria. Después de que se ha realizado la acción, el `LaunchedShortcutItem` propiedad está desactivada.

Por último, si ya se estaba ejecutando la aplicación, el `PerformActionForShortcutItem` podría llamar al método para controlar el elemento de acción rápida, por lo que necesitamos invalidarlo y llamar a nuestra `HandleShortcutItem` método aquí también.


### <a name="creating-dynamic-quick-action-items"></a>Creación de elementos de acción rápida dinámico

Además de definir la acción rápida estático los elementos de la aplicación `Info.plist` archivo, puede crear dinámica sobre la marcha acciones rápidas. Para definir dos acciones rápidas dinámica nueva, editar su `AppDelegate.cs` archivo nuevo y modificar el `FinishedLaunching` método para buscar a lo siguiente:

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    var shouldPerformAdditionalDelegateHandling = true;

    // Get possible shortcut item
    if (launchOptions != null) {
        LaunchedShortcutItem = launchOptions [UIApplication.LaunchOptionsShortcutItemKey] as UIApplicationShortcutItem;
        shouldPerformAdditionalDelegateHandling = (LaunchedShortcutItem == null);
    }

    // Add dynamic shortcut items
    if (application.ShortcutItems.Length == 0) {
        var shortcut3 = new UIMutableApplicationShortcutItem (ShortcutIdentifier.Third, "Play") {
            LocalizedSubtitle = "Will play an item",
            Icon = UIApplicationShortcutIcon.FromType(UIApplicationShortcutIconType.Play)
        };

        var shortcut4 = new UIMutableApplicationShortcutItem (ShortcutIdentifier.Fourth, "Pause") {
            LocalizedSubtitle = "Will pause an item",
            Icon = UIApplicationShortcutIcon.FromType(UIApplicationShortcutIconType.Pause)
        };

        // Update the application providing the initial 'dynamic' shortcut items.
        application.ShortcutItems = new UIApplicationShortcutItem[]{shortcut3, shortcut4};
    }

    return shouldPerformAdditionalDelegateHandling;
}
```

Ahora estamos comprobando para ver si el `application` ya contiene un conjunto de forma dinámica crean `ShortcutItems`, si no es así, crearemos dos nuevos `UIMutableApplicationShortcutItem` objetos para definir los nuevos elementos y agregarlos a la `ShortcutItems` matriz.

El código ya se han agregado en el [controlar una acción rápida](#Handling-a-Quick-Action) va a controlar estas acciones rápidas dinámico como estático las sección anterior.

Debe tenerse en cuenta que puede crear una combinación de elementos de acción rápida estáticas y dinámicas (como se hace aquí), no está limitado a uno u otro.

Para obtener más información, consulte nuestra [iOS 9 ejemplo ViewControllerPreview](https://developer.xamarin.com/samples/monotouch/iOS9/ViewControllerPreview/) y vea de Apple [ApplicationShortcuts: Mediante UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/samplecode/ApplicationShortcuts/) aplicación de ejemplo, [referencia de clase UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutItem_class/), [referencia de clase UIMutableApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/) y [ Referencia de clase UIApplicationShortcutIcon](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutIcon_Class/).

<a name="Testing-3D-Touch-in-the-Simulator" />

## <a name="testing-3d-touch-in-the-simulator"></a>Prueba la entrada táctil 3D en el simulador

Cuando usen la versión más reciente de Xcode y el simulador de iOS en un Mac compatible con un toque Force habilita trackpad, puede probar la funcionalidad táctil 3D en el simulador.

Para habilitar esta funcionalidad, ejecute cualquier aplicación en iPhone simulado hardware compatible con 3D Touch (iPhone 6s y versiones posteriores). A continuación, seleccione el **Hardware** menú en iOS Simulator y habilite el **motivación Trackpad Use 3D touch** elemento de menú:

[![](3d-touch-images/simulator01.png "Seleccione el menú de Hardware en el simulador de iOS y habilite la fuerza de Trackpad Use 3D touch elemento de menú")](3d-touch-images/simulator01.png#lightbox)

Con esta característica está activa, puede presionar más difíciles en el panel de seguimiento del equipo Mac para habilitar la interacción táctil 3D tal como lo haría en hardware real iPhone.

## <a name="summary"></a>Resumen

En este artículo presenta las nuevas 3D Touch API está disponible en iOS 9 para el iPhone 6s y iPhone 6s Plus. Se ha descrito agregando sensibilidad a la presión a una aplicación; uso de Peek y Pop para mostrar rápidamente información de la aplicación desde el contexto actual sin navegación; y uso de acciones rápidas para proporcionar accesos directos a la aplicación más utilizada del características.



## <a name="related-links"></a>Vínculos relacionados

- [iOS 9 ViewControllerPreview ejemplo](https://developer.xamarin.com/samples/monotouch/ios9/ViewControllerPreview/)
- [iOS 9 ApplicationShortcuts ejemplo](https://developer.xamarin.com/samples/monotouch/ios9/ApplicationShortcuts/)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Preparar las aplicaciones de iPhone para 3D Touch](https://developer.apple.com/ios/3d-touch/)
