---
title: "Introducción a la entrada táctil 3D"
description: "Este artículo incluye el uso de la nueva iPhone 6s y iPhone 6s Plus 3D Touch gestos en la aplicación."
ms.topic: article
ms.prod: xamarin
ms.assetid: 806D051E-3791-40F7-9776-4E4D3E56F7F3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 66cc67b38d70992fe815732407317fab3dc52528
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-3d-touch"></a>Introducción a la entrada táctil 3D

_Este artículo incluye el uso de la nueva iPhone 6s y iPhone 6s Plus 3D Touch gestos en la aplicación._

[ ![](3d-touch-images/info01.png "Ejemplos de 3D Touch en aplicaciones habilitadas")](3d-touch-images/info01.png)

En este artículo le proporcionará e Introducción al uso de las nuevas API 3D Touch para agregar gestos confidenciales de presión a las aplicaciones de Xamarin.iOS que se ejecutan en el nuevo iPhone 6s y iPhone 6s Plus dispositivos.

Con 3D Touch, una aplicación de iPhone ahora es capaz de no solo indican que el usuario toca la pantalla del dispositivo, pero es capaz de detectar cuánto presión es ejerciendo el usuario y responder a los niveles de presión diferentes.

Táctil 3D proporciona las siguientes características a la aplicación:

- [Sensibilidad a la presión](#Pressure-Sensitivity) : aplicaciones ahora pueden medir de forma rígida o claro el usuario toca la pantalla y beneficiarse de dicha información.
  Por ejemplo, una aplicación de dibujo puede realizar una línea más gruesa o más delgados basándose en la presión, el usuario es tocar la pantalla.
- [Consultar y Pop](#Peek-and-Pop) -la aplicación ahora puede permitir que el usuario interactúe con sus datos sin tener que desplazarse fuera de su contexto actual. Si presiona de disco duro en la pantalla de la pantalla, puede consultar el elemento que le interesa (por ejemplo, una vista previa de un mensaje). Si presiona más difícil, puede extraer en el elemento.
- [Acciones rápidas](#Quick-Action) -pensar de acciones rápidas como los menús contextuales que pueden ser extrae telefónico cuando un usuario hace clic con un botón en un elemento de una aplicación de escritorio.
  Mediante acciones rápidas, puede agregar accesos directos a funciones de la aplicación directamente desde el icono de la aplicación en la pantalla Inicio.
- [Probar táctil 3D en el simulador](#Testing-3D-Touch-in-the-Simulator) -con el hardware de Mac correcta, puede probar aplicaciones de la pantalla táctil 3D en el simulador de iOS.

<a name="Pressure-Sensitivity" />

## <a name="pressure-sensitivity"></a>Sensibilidad a la presión

Como se indicó anteriormente, con nuevas propiedades de la [UITouch](https://developer.xamarin.com/api/type/UIKit.UITouch/) clase puede medir la cantidad de presión que el usuario está aplicando a la pantalla del dispositivo de iOS y utilizar esta información en la interfaz de usuario. Por ejemplo, realizando un trazo del pincel más translúcido u opaco basándose en la cantidad de presión.

[ ![](3d-touch-images/pressure01.png "Según la cantidad de presión de un trazo de pincel que se representan como más translúcido u opaco")](3d-touch-images/pressure01.png)

Como resultado de 3D Touch, si la aplicación se ejecuta en iOS 9 (o posterior) y el dispositivo iOS es capaz de apoyo 3D Touch, cambios de presión hará que el `TouchesMoved` evento.

Por ejemplo, al supervisar el `TouchesMoved` eventos de un [UIView](https://developer.xamarin.com/api/type/UIKit.UIView/), puede usar el código siguiente para obtener la presión actual que el usuario se aplica a la pantalla:

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

El `MaximumPossibleForce` propiedad devuelve el mayor valor posible para la `Force` propiedad de la [UITouch](https://developer.xamarin.com/api/type/UIKit.UITouch/) basándose en el dispositivo iOS que se ejecuta la aplicación en.

> [!IMPORTANT]
> **Nota:** hará que los cambios en la presión de la `TouchesMoved` evento, incluso si la X / coordenadas Y no han cambiado. Debido a este cambio de comportamiento, las aplicaciones iOS deben estar preparadas para la `TouchesMoved` eventos que se debe invocar con más frecuencia y de la X coordenadas X/y para ser el mismo que el último `TouchesMoved` llamar.




Para obtener más información, vea de Apple [TouchCanvas: mediante UITouch de forma eficaz](https://developer.apple.com/library/prerelease/ios/samplecode/TouchCanvas/) aplicación de ejemplo y [UITouch Class Reference](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITouch_Class/).

<a name="Peek-and-Pop" />

## <a name="peek-and-pop"></a>Peek y Pop

Táctil 3D proporciona nuevas formas para que un usuario interactúe con la información dentro de la aplicación más rápida que nunca, sin tener que navegar desde su ubicación actual.

Por ejemplo, si la aplicación muestra una tabla de mensajes, el usuario puede presionar en disco duro en un elemento para obtener una vista previa de su contenido en una vista de superposición (que Apple se hace referencia como un *Peek*).

[ ![](3d-touch-images/peekandpop01.png "Un ejemplo de ejecución del método Peek en contenido")](3d-touch-images/peekandpop01.png)

Si el usuario presiona más difícil, deberá especificar la vista de mensaje normal (lo que se conoce como *Pop*-ping en la vista).

### <a name="checking-for-3d-touch-availability"></a>Comprobar la disponibilidad de táctil 3D

Cuando se trabaja con un [UIViewController]() puede usar el siguiente código para ver si el dispositivo iOS que se esté ejecutando la aplicación es compatible con 3D Touch:

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

Puede llamar a este método antes de *o después* `ViewDidLoad()`. 

### <a name="handling-peek-and-pop"></a>POP e información de control

En un dispositivo iOS que puede controlar 3D Touch, podemos usar una instancia de la `UIViewControllerPreviewingDelegate` clase para controlar la presentación de **Peek** y **Pop** detalles de elemento. Por ejemplo, si ha surgido un controlador de vista de tabla denominada `MasterViewController ` podríamos usar el siguiente código para admitir **Peek** y **Pop**:

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

El `GetViewControllerForPreview` método se usa para realizar la **Peek** operación. Se obtiene acceso a los datos de celda y copias de seguridad de la tabla y, a continuación, carga el `DetailViewController` desde el guión gráfico actual. Estableciendo la `PreferredContentSize` a (0,0), te pedimos para el valor predeterminado **Peek** Ver tamaño. Por último, se desenfoque todo excepto la celda estamos mostrando con `previewingContext.SourceRect = cell.Frame` y se devuelve la nueva vista para su presentación.

El `CommitViewController` vuelve a utilizar la vista se creó en el **Peek** para el **Pop** ver cuando el usuario presiona más difícil.

### <a name="registering-for-peek-and-pop"></a>Registrarse para inspección y Pop

Desde el controlador de vista que va a permitir al usuario **Peek** y **Pop** elementos de, es necesario registrar para este servicio. En el ejemplo anterior de un controlador de vista de tabla (`MasterViewController`), se usaría el siguiente código:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Check to see if 3D Touch is available
    if (TraitCollection.ForceTouchCapability == UIForceTouchCapability.Available) {
        // Regiser for Peek and Pop
        RegisterForPreviewingWithDelegate(new PreviewingDelegate(this), View);
    }
    ...

}
```

Aquí estamos llamando a la `RegisterForPreviewingWithDelegate` método con una instancia de la `PreviewingDelegate` hemos creado anteriormente. En los dispositivos de iOS compatibles con 3D Touch, el usuario puede presionar disco duro en un elemento para leer en él. Si presiona incluso más fuerte, el elemento aparecerá en ella estándar Mostrar vista.

Para obtener más información, vea nuestra [iOS 9 ejemplo ApplicationShortcuts](https://developer.xamarin.com/samples/monotouch/iOS9/ViewControllerPreview/) y de Apple [ViewControllerPreviews: mediante la vista previa de las API de UIViewController](https://developer.apple.com/library/prerelease/ios/samplecode/ViewControllerPreviews/Introduction/Intro.html) aplicación de ejemplo, [ Referencia de clase UIPreviewAction](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewAction_Class/), [referencia de clase UIPreviewActionGroup](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionGroup_Class/) y [referencia del protocolo UIPreviewActionItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionItem_Protocol/).

<a name="Quick-Actions" />

## <a name="quick-actions"></a>Acciones rápidas

Con 3D Touch y acciones rápidas, puede agregar comunes, rápida y sencilla a los accesos directos de acceso a las funciones de la aplicación desde el icono de la pantalla de inicio en el dispositivo iOS.

Como se mencionó anteriormente, se puede considerar acciones rápidas como los menús contextuales que pueden ser extrae telefónico cuando un usuario hace clic con un botón en un elemento de una aplicación de escritorio. Debe utilizar acciones rápidas para proporcionar accesos directos a las funciones o características de la aplicación más comunes.

[ ![](3d-touch-images/quickactions01.png "Un ejemplo de un menú de acciones rápidas")](3d-touch-images/quickactions01.png)

<a name="Defining-Static-Quick-Actions" />

### <a name="defining-static-quick-actions"></a>Definir acciones rápidas estáticas

Si una o varias de las acciones rápido requeridas por la aplicación son estáticos y no es necesario cambiar, se puede definir en la aplicación `Info.plist` archivo. Editar este archivo en un editor externo y agregue las claves siguientes:

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

Aquí nos estamos definir dos elementos de acción rápida estáticos con las claves siguientes:

- `UIApplicationShortcutItemIconType` -Define el icono que se mostrará el elemento de acción rápida como uno de los siguientes valores:
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

* `UIApplicationShortcutItemSubtitle` -Define el subtítulo para el elemento.
* `UIApplicationShortcutItemTitle` -Define el título para el elemento.
* `UIApplicationShortcutItemType` : Es un valor de cadena que se usará para identificar el elemento en nuestra aplicación. Para obtener más información, vea la sección siguiente.

> [!IMPORTANT]
> **Nota:** elementos de acción rápida de acceso directo que se establecen en los `Info.plist` no se puede obtener acceso al archivo con el `Application.ShortcutItems` propiedad. Solo se pasan en a la `HandleShortcutItem` controlador de eventos. 




<a name="Identifying-Quick-Action-Items" />

### <a name="identifying-quick-action-items"></a>Identificación de los elementos de acción rápida

Como se vio anteriormente, cuando define los elementos de acción rápida en la aplicación `Info.plist`, se le asigna un valor de cadena para el `UIApplicationShortcutItemType` clave para identificarlos.

Para hacer que esos identificadores más fácil trabajar con código, agregue una clase denominada `ShortcutIdentifier` a la aplicación del proyecto y darle un aspecto similar al siguiente:

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

A continuación, debe modificar la aplicación `AppDelegate.cs` archivo para controlar el usuario seleccione un elemento de acción rápida de icono de la aplicación en la pantalla Inicio.

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

En primer lugar, definimos un complemento público `LaunchedShortcutItem` propiedad para realizar un seguimiento del último elemento de acción rápida seleccionado por el usuario. A continuación, reemplazamos el `FinishedLaunching` método y vea si `launchOptions` se han pasado y contiene un elemento de acción rápida. Si es así, se almacena la acción rápida en el `LaunchedShortcutItem` propiedad.

A continuación, reemplazamos el `OnActivated` método y pasar cualquiera seleccionada en elemento de inicio rápido para el `HandleShortcutItem` método al que se ha actuado sobre. Actualmente solo escribimos un mensaje a la **consola**. En una aplicación real, se deben controlar cualquier acción fue necesario. Después de que se ha realizado la acción, el `LaunchedShortcutItem` propiedad está desactivada.

Por último, si ya se estaba ejecutando la aplicación, el `PerformActionForShortcutItem` podría llamar al método para controlar el elemento de acción rápida, por lo que necesitamos reemplazarla y llamar a nuestro `HandleShortcutItem` método aquí también.

<a name="Creating-Dynamic-Quick-Action-Items" />

### <a name="creating-dynamic-quick-action-items"></a>Crear elementos de acción rápida dinámica

Además de definir estático acción rápida los elementos de la aplicación `Info.plist` archivo, puede crear dinámica sobre la marcha acciones rápidas. Para definir dos acciones rápidas dinámica nueva, editar su `AppDelegate.cs` archivo nuevo y modifique la `FinishedLaunching` método para buscar, como lo siguiente:

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

Ahora estamos comprobando para ver si el `application` ya contiene un conjunto de creación de forma dinámica `ShortcutItems`, si no es así crearemos dos nuevos `UIMutableApplicationShortcutItem` objetos para definir los nuevos elementos y agregarlos después a la `ShortcutItems` matriz.

El código se agrega ya en el [control de una acción rápida](#Handling-a-Quick-Action) sección anterior controlará estas acciones rápidas dinámica al igual que las estáticas.

Debe tenerse en cuenta que puede crear una combinación de los elementos de acción rápida estáticas y dinámicas (como se hace aquí), no está limitado a uno u otro.

Para obtener más información, consulte nuestro [iOS 9 ejemplo ViewControllerPreview](https://developer.xamarin.com/samples/monotouch/iOS9/ViewControllerPreview/) y ver de Apple [ApplicationShortcuts: usar UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/samplecode/ApplicationShortcuts/) aplicación de ejemplo, [ Referencia de clase UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutItem_class/), [referencia de clase UIMutableApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/) y [referencia de clase UIApplicationShortcutIcon](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutIcon_Class/).

<a name="Testing-3D-Touch-in-the-Simulator" />

## <a name="testing-3d-touch-in-the-simulator"></a>Prueba táctil 3D en el simulador

Cuando con la versión más reciente de Xcode y el simulador de iOS en un Mac compatible con un toque Force habilita el panel de seguimiento, puede probar 3D funcionalidad táctil en el simulador.

Para habilitar esta funcionalidad, ejecute cualquier aplicación en el hardware de iPhone simulados que es compatible con 3D Touch (iPhone 6s y versiones posteriores). A continuación, seleccione la **Hardware** menú en el archivo iOS Simulator y habilitar la **Force de panel de seguimiento de uso para entrada táctil 3D** elemento de menú:

[ ![](3d-touch-images/simulator01.png "Seleccione el menú de Hardware en el simulador de iOS y habilitar la fuerza de panel de seguimiento de uso de elemento de menú touch 3D")](3d-touch-images/simulator01.png)

Con esta característica está activa, puede presionar más difícil en el panel de seguimiento de Mac para habilitar la interacción táctil 3D tal como lo haría en un hardware real iPhone.

## <a name="summary"></a>Resumen

Este artículo presenta las nuevas 3D Touch API disponible en iOS 9 para el iPhone 6s e iPhone 6s Plus. Trata de sensibilidad a la presión agregar a una aplicación; usar Peek y Pop para mostrar rápidamente información de la aplicación desde el contexto actual sin navegación; y usar acciones rápidas para proporcionar accesos directos a la aplicación más utilizada del características.



## <a name="related-links"></a>Vínculos relacionados

- [iOS 9 ejemplo ViewControllerPreview](https://developer.xamarin.com/samples/monotouch/ios9/ViewControllerPreview/)
- [iOS 9 ejemplo ApplicationShortcuts](https://developer.xamarin.com/samples/monotouch/ios9/ApplicationShortcuts/)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Preparar las aplicaciones iPhone para entrada táctil 3D](https://developer.apple.com/ios/3d-touch/)
