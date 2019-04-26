---
title: Entrega en Xamarin.iOS
description: En este artículo trata de trabajar con entrega en una aplicación de Xamarin.iOS para transferir las actividades del usuario entre aplicaciones que se ejecutan en el usuario de otros dispositivos del.
ms.prod: xamarin
ms.assetid: 405F966A-4085-4621-AA15-33D663AD15CD
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 1a5cc9f06fdca5944a9a3201ac15d63ca7f15453
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61385589"
---
# <a name="handoff-in-xamarinios"></a>Entrega en Xamarin.iOS

_En este artículo trata de trabajar con entrega en una aplicación de Xamarin.iOS para transferir las actividades del usuario entre aplicaciones que se ejecutan en el usuario de otros dispositivos del._

Apple introdujo Handoff en iOS 8 y OS X Yosemite (10.10) para proporcionar un mecanismo común para el usuario transferir las actividades iniciadas en uno de sus dispositivos, a otro dispositivo que ejecuta la misma aplicación u otra aplicación que admita la misma actividad.

[![](handoff-images/handoff02.png "Un ejemplo de realizar una operación de entrega")](handoff-images/handoff02.png#lightbox)

En este artículo se Eche un vistazo rápido a habilitar la actividad de uso compartido en una aplicación Xamarin.iOS y cubrir el marco de trabajo de entrega en detalle:

## <a name="about-handoff"></a>Acerca de la entrega

HANDOFF (también conocido como continuidad) se introdujo por Apple en iOS 8 y OS X Yosemite (10.10) como una forma para que el usuario iniciar una actividad en uno de sus dispositivos (iOS o Mac) y continuar con esa misma actividad en otro de sus dispositivos (que se identifica con iClou del usuario d. cuenta).

Entrega se expandió en iOS 9 para también admiten nuevas capacidades de búsqueda mejoradas. Para obtener más información, consulte nuestra [mejoras en la búsqueda](~/ios/platform/search/index.md) documentación.

Por ejemplo, el usuario puede iniciar un correo electrónico en su iPhone y continuar sin problemas el correo electrónico en su equipo Mac, con todos la misma información de mensaje que se rellena y el cursor en la misma ubicación que dejó en iOS.

Cualquiera de las aplicaciones que comparten el mismo _Id. de equipo_ son elegibles para usar Handoff para continuar las actividades de usuario en las aplicaciones siempre que estas aplicaciones son entregada mediante el Store de iTunes App o está firmado por un desarrollador registrado (para Mac, Enterprise o aplicaciones Ad Hoc).

Cualquier `NSDocument` o `UIDocument` en función de las aplicaciones tienen automáticamente entrega soporte técnico integrado y requieren cambios mínimos para admitir la entrega.

### <a name="continuing-user-activities"></a>Continuar con las actividades del usuario

El `NSUserActivity` clase (junto con algunos pequeños cambios que `UIKit` y `AppKit`) proporciona compatibilidad para definir la actividad de un usuario que potencialmente puede continuar en otro de los dispositivos del usuario.

Una actividad se pasa a otro de los dispositivos del usuario, se deben encapsular en una instancia `NSUserActivity`, se marca como el _actividad actual_, tiene su carga conjunto (los datos que se usa para realizar la continuación) y el actividad, a continuación, se debe transmitir a dicho dispositivo.

Entrega pasa el mínimo de información para definir la actividad debe continuar con los paquetes más grandes de datos que se sincronizan a través de iCloud.

En el dispositivo receptor, el usuario recibirá una notificación de que está disponible para la continuación de una actividad. Si el usuario decide seguir la actividad en el nuevo dispositivo, se inicia la aplicación especificada (si aún no está en ejecución) y la carga desde el `NSUserActivity` se utiliza para iniciar la actividad.

[![](handoff-images/handoffinteractions.png "Información general de continuar con las actividades del usuario")](handoff-images/handoffinteractions.png#lightbox)

Solo las aplicaciones que comparten el mismo Id. de equipo de desarrollador y responderán a una determinada _tipo de actividad_ son aptos para la continuación. Una aplicación define los tipos de actividad que admite en el `NSUserActivityTypes` clave de su **Info.plist** archivo. Por tanto, un dispositivo continuando elige la aplicación para realizar la continuación según el identificador de equipo, el tipo de actividad y, opcionalmente, el _título de la actividad_.

La aplicación receptora utiliza información de la `NSUserActivity`del `UserInfo` diccionario para configurar su interfaz de usuario y restaurar el estado de la actividad determinada para que se muestre la transición sin problemas para el usuario final.

Si la continuación requiere más información que puede enviarse a través de forma eficaz un `NSUserActivity`, la reanudación de la aplicación puede enviar una llamada a la aplicación original y establecer uno o varios flujos para transmitir los datos necesarios. Por ejemplo, si la actividad estaba editando un documento de texto grande con varias imágenes, transmisión por secuencias se necesitarían para transferir la información necesaria para continuar con la actividad en el dispositivo receptor. Para obtener más información, consulte el [compatibles con secuencias de continuación](#supporting-continuation-streams) sección más adelante.

Como se indicó anteriormente, `NSDocument` o `UIDocument` en función de las aplicaciones automáticamente tienen entrega soporte técnico integrado. Para obtener más información, consulte el [que admiten la entrega en aplicaciones basadas en documento](#supporting-handoff-in-document-based-apps) sección más adelante.

### <a name="the-nsuseractivity-class"></a>La clase NSUserActivity

La `NSUserActivity` clase es el objeto principal en un intercambio de entrega y se usa para encapsular el estado de una actividad de usuario que está disponible para la continuación. Creará una instancia de una copia de una aplicación `NSUserActivity` para cualquier actividad admite y desea seguir en otro dispositivo. Por ejemplo, editor de documentos crearía una actividad para cada documento abierto actualmente. Sin embargo, solo en el documento al frente (se muestra en la pestaña o ventana de primer plano) es el _actividad actual_ y, por tanto, están disponibles para la continuación.

Una instancia de `NSUserActivity` identificado por ambos su `ActivityType` y `Title` propiedades. El `UserInfo` propiedad de diccionario que se utiliza para transportar la información sobre el estado de la actividad. Establecer el `NeedsSave` propiedad `true` si desea diferida para cargar la información de estado a través de la `NSUserActivity`del delegado. Use la `AddUserInfoEntries` método para combinar los nuevos datos de otros clientes en la `UserInfo` diccionario según sea necesario para conservar el estado de la actividad.

### <a name="the-nsuseractivitydelegate-class"></a>La clase NSUserActivityDelegate

El `NSUserActivityDelegate` se usa para mantener la información en un `NSUserActivity`del `UserInfo` diccionario actualizado y sincronizado con el estado actual de la actividad. Cuando el sistema necesita la información de la actividad en actualizarse (como antes de continuación en otro dispositivo), llama a la `UserActivityWillSave` método del delegado.

Deberá implementar la `UserActivityWillSave` método y hacer los cambios a la `NSUserActivity` (como `UserInfo`, `Title`, etc.) para asegurarse de que refleja el estado de la actividad actual. Cuando el sistema llama a la `UserActivityWillSave` método, la `NeedsSave` borrado la marca. Si modifica cualquiera de las propiedades de datos de la actividad, se deberá establecer `NeedsSave` a `true` nuevo.

En lugar de usar el `UserActivityWillSave` método presentada más arriba, puede tener opcionalmente `UIKit` o `AppKit` administrar automáticamente la actividad del usuario. Para ello, establezca el objeto de servicio de respuesta `UserActivity` propiedad e implemente el `UpdateUserActivityState` método. Consulte la [que admiten la entrega en los servicios de respuesta](#supporting-handoff-in-responders) sección para obtener más información.

### <a name="app-framework-support"></a>Compatibilidad con el marco de aplicación

Ambos `UIKit` (iOS) y `AppKit` (OS X) ofrecen compatibilidad integrada para su entrega en el `NSDocument`, servicio de respuesta (`UIResponder`/`NSResponder`), y `AppDelegate` clases. Aunque cada sistema operativo implementa entrega una forma ligeramente diferente, el mecanismo básico y las API son los mismos.

#### <a name="user-activities-in-document-based-apps"></a>Actividades del usuario en aplicaciones basadas en documento

Basados en documento aplicaciones iOS y OS X automáticamente tienen compatibilidad de entrega integrado. Para activar esta compatibilidad, deberá agregar una `NSUbiquitousDocumentUserActivityType` clave y valor para cada `CFBundleDocumentTypes` entrada en la aplicación **Info.plist** archivo.

Si esta clave está presente, ambos `NSDocument` y `UIDocument` crear automáticamente `NSUserActivity` instancias de documentos de iCloud del tipo especificado. Deberá proporcionar un tipo de actividad para cada tipo de documento que admita la aplicación y varios tipos de documentos pueden usar el mismo tipo de actividad. Ambos `NSDocument` y `UIDocument` rellenar automáticamente el `UserInfo` propiedad de la `NSUserActivity` con sus `FileURL` el valor de propiedad.

En OS X, el `NSUserActivity` administrados por `AppKit` y asociado automáticamente con los servicios de respuesta se convierten en la actividad actual cuando la ventana de documento se convierte en la ventana principal. En iOS, para `NSUserActivity` objetos administrados por `UIKit`, debe llamar a `BecomeCurrent` método explícitamente o que el documento `UserActivity` propiedad establecida en un `UIViewController` cuando la aplicación se pone en primer plano.

`AppKit` se restaurará automáticamente cualquier `UserActivity` propiedad creada de esta forma en OS X. Esto se produce si el `ContinueUserActivity` devuelve del método `false` o si es no está implementada. En esta situación, se abre el documento con el `OpenDocument` método de la `NSDocumentController` y, a continuación, recibirá un `RestoreUserActivityState` llamada al método.

Consulte la [que admiten la entrega en aplicaciones basadas en documento](#supporting-handoff-in-document-based-apps) sección para obtener más información.

#### <a name="user-activities-and-responders"></a>Los servicios de respuesta y las actividades del usuario

Ambos `UIKit` y `AppKit` puede administrar automáticamente una actividad de usuario si se establece como un objeto de servicio de respuesta `UserActivity` propiedad. Si se ha modificado el estado, se deberá establecer el `NeedsSave` propiedad de la respuesta `UserActivity` a `true`. El sistema va a guardar automáticamente el `UserActivity` cuando sea necesario, después de dar el tiempo de respuesta para actualizar el estado mediante una llamada a su `UpdateUserActivityState` método.

Si varios servicios de respuesta comparten una sola `NSUserActivity` instancia, que reciben una `UpdateUserActivityState` devolución de llamada cuando el sistema actualiza el objeto de actividad del usuario. El servicio de respuesta necesita llamar a la `AddUserInfoEntries` método para actualizar la `NSUserActivity`del `UserInfo` diccionario para reflejar el estado actual de la actividad en este momento. El `UserInfo` diccionario se borra antes de cada `UpdateUserActivityState` llamar.

Para desasociar propio de una actividad, puede establecer un Respondedor su `UserActivity` propiedad `null`. Cuando administra un marco de aplicación `NSUserActivity` instancia no tiene más los servicios de respuesta asociados o documentos, queda invalidado de forma automática.

Consulte la [que admiten la entrega en los servicios de respuesta](#supporting-handoff-in-responders) sección para obtener más información.

#### <a name="user-activities-and-the-appdelegate"></a>Las actividades del usuario y AppDelegate

La aplicación `AppDelegate` es su punto de entrada principal cuando se administran una continuación de entrega. Cuando el usuario responde a una notificación de entrega, se inicia la aplicación adecuada (si no se está ejecutando) y el `WillContinueUserActivityWithType` método de la `AppDelegate` se llamará. En este momento, la aplicación debe informar al usuario que se está iniciando la continuación.

El `NSUserActivity` instancia se entrega cuando el `AppDelegate`del `ContinueUserActivity` se llama al método. En este momento, debe configurar la interfaz de usuario de la aplicación y seguir la actividad especificada.

Consulte la [implementar Handoff](#implementing-handoff) sección para obtener más información.

## <a name="enabling-handoff-in-a-xamarin-app"></a>Habilitar la entrega en una aplicación Xamarin

Debido a los requisitos de seguridad impuestos por la entrega, una aplicación de Xamarin.iOS que usa el marco de trabajo de entrega debe estar configurada correctamente en el Portal de desarrollador de Apple y en el archivo de proyecto de Xamarin.iOS.

Haga lo siguiente:

1. Inicie sesión en el [Portal Apple Developer](https://developer.apple.com).
2. Haga clic en **certificados, identificadores y perfiles**.
3. Si aún no lo ha hecho, haga clic en **identificadores** y crear un identificador para la aplicación (por ejemplo, `com.company.appname`), editar de lo contrario, su identificador existente.
4. Asegúrese de que el **iCloud** service se ha comprobado para el identificador dado:

    [![](handoff-images/provision01.png "Habilitar el servicio de iCloud para el identificador especificado")](handoff-images/provision01.png#lightbox)
5. Guarde los cambios.
4. Haga clic en **perfiles de aprovisionamiento** > **desarrollo** y creación de aplicación de un desarrollo nuevo perfil de aprovisionamiento para usted:

    [![](handoff-images/provision02.png "Crear un nuevo perfil para la aplicación de aprovisionamiento de desarrollo")](handoff-images/provision02.png#lightbox)
5. Ya sea descargar e instalar el nuevo perfil de aprovisionamiento o usar Xcode para descargar e instalar el perfil.
6. Editar las opciones del proyecto de Xamarin.iOS y asegúrese de que está usando el perfil de aprovisionamiento que acaba de crear:

    [![](handoff-images/provision03.png "Seleccione el perfil de aprovisionamiento que acaba de crear")](handoff-images/provision03.png#lightbox)
7. A continuación, edite su **Info.plist** de archivos y asegúrese de que está usando el identificador de aplicación que se usó para crear el perfil de aprovisionamiento:

    [![](handoff-images/provision04.png "Establecer Id. de aplicación")](handoff-images/provision04.png#lightbox)
8. Desplácese hasta la **modos en segundo plano** sección y compruebe los siguientes elementos:

    [![](handoff-images/provision05.png "Habilitar los modos de fondo que requiere")](handoff-images/provision05.png#lightbox)
9. Guarde los cambios a todos los archivos.

Con esta configuración en su lugar, la aplicación ahora está lista para tener acceso a las API de marco de trabajo de entrega. Para obtener información detallada sobre el aprovisionamiento, consulte nuestra [Device Provisioning](~/ios/get-started/installation/device-provisioning/index.md) y [aprovisionamiento de la aplicación](~/ios/get-started/installation/device-provisioning/index.md) guías.

## <a name="implementing-handoff"></a>Implementación de entrega

Pueden continuar las actividades del usuario entre las aplicaciones que se firman con el mismo Id. de equipo de desarrollador y admiten el mismo tipo de actividad. Implementación de entrega en una aplicación Xamarin.iOS requiere crear un objeto de actividad de usuario (ya sea en `UIKit` o `AppKit`), actualizar el estado del objeto para realizar un seguimiento de la actividad y continuar con la actividad en un dispositivo receptor.

### <a name="identifying-user-activities"></a>Identificar las actividades de usuario

El primer paso para implementar el comportamiento de la transición consiste en identificar los tipos de actividades de usuario que admite la aplicación y ver cuáles de estas actividades es buenos candidatos para la continuación en otro dispositivo. Por ejemplo: una aplicación ToDo podría admitir la edición de elementos como una _tipo de actividad de usuario_y permite examinar la lista de elementos disponibles que otra.

Una aplicación puede crear tantos tipos de actividad de usuario como son necesarios, uno para cualquier función que proporciona la aplicación. Para cada tipo de actividad del usuario, la aplicación deberá hacer un seguimiento cuando una actividad del tipo comienza y finaliza y es necesario mantener la información de estado actualizada para continuar con esa tarea en otro dispositivo.

Las actividades del usuario se pueden continuar en cualquier aplicación firmada con el mismo identificador de equipo sin ninguna asignación uno a uno entre las aplicaciones emisoras y receptoras. Por ejemplo, una aplicación determinada puede crear cuatro tipos diferentes de actividades, consumidos por diferentes aplicaciones individuales en otro dispositivo. Esto es habitual entre una versión de Mac de la aplicación (que podría tener muchas características y funciones) y aplicaciones de iOS, donde cada aplicación es más pequeño y centrado en una tarea específica.

### <a name="creating-activity-type-identifiers"></a>Creación de identificadores de tipo de actividad

El _identificador de tipo de actividad_ se agrega una cadena corta a la `NSUserActivityTypes` matriz de la aplicación **Info.plist** archivo usado para identificar de forma única un determinado tipo de actividad de usuario. Habrá una entrada en la matriz para cada actividad que admita la aplicación. Apple sugiere el uso de una notación de estilo de DNS inverso para el identificador de tipo de actividad para evitar colisiones. Por ejemplo: `com.company-name.appname.activity` para aplicaciones específicas basadas en las actividades o `com.company-name.activity` para las actividades que se pueden ejecutar en varias aplicaciones.

El identificador de tipo de actividad se usa al crear un `NSUserActivity` instancia para identificar el tipo de actividad. Cuando una actividad continúa en otro dispositivo, el tipo de actividad (junto con el identificador de equipo de la aplicación) determina qué aplicación iniciar para continuar con la actividad.

Por ejemplo, vamos a crear una aplicación de ejemplo denominada **MonkeyBrowser** ([descargar aquí](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/)). Esta aplicación presenta cuatro pestañas, cada uno con una Abrir dirección URL diferente en una vista de explorador web. El usuario podrá seguir cualquier pestaña en un dispositivo iOS diferente que ejecuta la aplicación.

Para crear los identificadores de tipo de actividad necesaria para admitir este comportamiento, edite el **Info.plist** de archivo y cambie a la **origen** vista. Agregar un `NSUserActivityTypes` clave y crear los siguientes identificadores:

[![](handoff-images/type01.png "La clave de NSUserActivityTypes y los identificadores necesarios en el editor de plist")](handoff-images/type01.png#lightbox)

Se crean cuatro nuevos identificadores de tipo de actividad, uno para cada una de las pestañas en el ejemplo **MonkeyBrowser** app. Al crear sus propias aplicaciones, reemplace el contenido de la `NSUserActivityTypes` de matriz con los identificadores de tipo de actividad específico a las actividades de la aplicación admite.

### <a name="tracking-user-activity-changes"></a>Seguimiento de cambios de la actividad de usuario

Cuando se crea una nueva instancia de la `NSUserActivity` (clase), se especificará un `NSUserActivityDelegate` instancia para realizar el seguimiento de cambios al estado de la actividad. Por ejemplo, el código siguiente puede utilizarse para realizar el seguimiento de cambios de estado:

```csharp
using System;
using CoreGraphics;
using Foundation;
using UIKit;

namespace MonkeyBrowse
{
    public class UserActivityDelegate : NSUserActivityDelegate
    {
        #region Constructors
        public UserActivityDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void UserActivityReceivedData (NSUserActivity userActivity, NSInputStream inputStream, NSOutputStream outputStream)
        {
            // Log
            Console.WriteLine ("User Activity Received Data: {0}", userActivity.Title);
        }

        public override void UserActivityWasContinued (NSUserActivity userActivity)
        {
            Console.WriteLine ("User Activity Was Continued: {0}", userActivity.Title);
        }

        public override void UserActivityWillSave (NSUserActivity userActivity)
        {
            Console.WriteLine ("User Activity will be Saved: {0}", userActivity.Title);
        }
        #endregion
    }
}
```

El `UserActivityReceivedData` método se llama cuando un Stream continuación ha recibido datos de un dispositivo de envío. Para obtener más información, consulte el [compatibles con secuencias de continuación](#supporting-continuation-streams) sección más adelante.

El `UserActivityWasContinued` método se llama cuando ha tomado otro dispositivo a través de una actividad desde el dispositivo actual. Según el tipo de actividad, como agregar un nuevo elemento a una lista de tareas, la aplicación podría necesita anular la actividad en el dispositivo de envío.

El `UserActivityWillSave` se llama al método antes de que se guarda y se sincronizan en todos los dispositivos disponibles localmente los cambios realizados en la actividad. Puede usar este método para realizar cambios de última hora a la `UserInfo` propiedad de la `NSUserActivity` instancia antes de enviarlo.

### <a name="creating-a-nsuseractivity-instance"></a>Creación de una instancia de NSUserActivity

Cada actividad que la aplicación desea proporcionar la posibilidad de continuar en otro dispositivo que se debe encapsular en un `NSUserActivity` instancia. La aplicación puede crear tantas actividades según sea necesario y la naturaleza de esas actividades depende de las funciones y características de la aplicación en cuestión. Por ejemplo, una aplicación de correo electrónico podría crear una actividad para crear un nuevo mensaje y otra para leer un mensaje.

Para nuestra aplicación de ejemplo, un nuevo `NSUserActivity` se crea cada vez que el usuario escribe una nueva dirección URL en uno de la vista de explorador web con pestañas. El código siguiente almacena el estado de una pestaña determinada:

```csharp
public NSString UserActivityTab1 = new NSString ("com.xamarin.monkeybrowser.tab1");
public NSUserActivity UserActivity { get; set; }
...

UserActivity = new NSUserActivity (UserActivityTab1);
UserActivity.Title = "Weather Tab";
UserActivity.Delegate = new UserActivityDelegate ();

// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);

// Inform Activity that it has been updated
UserActivity.BecomeCurrent ();
```

Crea un nuevo `NSUserActivity` utilizando uno de los tipos de actividad de usuario creada anteriormente y proporciona un título de lenguaje natural para la actividad. Adjunta a una instancia de la `NSUserActivityDelegate` creado anteriormente para ver el estado cambia y se informa de iOS que esta actividad de usuario es la actividad actual.

### <a name="populating-the-userinfo-dictionary"></a>Rellenar el diccionario UserInfo

Como hemos visto anteriormente, el `UserInfo` propiedad de la `NSUserActivity` clase es un `NSDictionary` de pares de clave-valor que se usan para definir el estado de una actividad determinada. Los valores almacenados en `UserInfo` debe ser uno de los siguientes tipos: `NSArray`, `NSData`, `NSDate`, `NSDictionary`, `NSNull`, `NSNumber`, `NSSet`, `NSString`, o `NSURL`. `NSURL` los valores de datos que señalan a documentos de iCloud se ajustarán automáticamente para que apunten a los mismos documentos en un dispositivo receptor.

En el ejemplo anterior, creamos un `NSMutableDictionary` de objetos y se rellena con una clave única que proporciona la dirección URL que estaba viendo actualmente en la ficha especificada. El `AddUserInfoEntries` usó el método de la actividad del usuario para actualizar la actividad con los datos que se usará para restaurar la actividad en el dispositivo receptor:

```csharp
// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);
```

Apple recomienda mantener la información enviada a la menor cantidad para garantizar que la actividad se envía de manera puntual en el dispositivo receptor. Si es necesario, información más grande, como se puede editar una imagen adjunta a un documento debe enviarse, debe utilizar la continuación secuencias. Consulte la [compatibles con secuencias de continuación](#supporting-continuation-streams) sección para obtener más detalles.

### <a name="continuing-an-activity"></a>Continuar con una actividad

Comportamiento de la transición automáticamente le informará local dispositivos iOS y OS X que están en la proximidad física a dispositivo de origen y ha iniciado sesión en la misma cuenta de iCloud, de la disponibilidad de las actividades del usuario crítico. Si el usuario decide continuar una actividad en un dispositivo nuevo, el sistema iniciará la aplicación adecuada (según el Id. de equipo y el tipo de actividad) y la información su `AppDelegate` que la continuación debe producirse.

En primer lugar, el `WillContinueUserActivityWithType` se llama al método por lo que puede informar al usuario que va a comenzar la continuación de la aplicación. Usamos el siguiente código en el **AppDelegate.cs** archivo de nuestra aplicación de ejemplo para controlar el inicio de una continuación:

```csharp
public NSString UserActivityTab1 = new NSString ("com.xamarin.monkeybrowser.tab1");
public NSString UserActivityTab2 = new NSString ("com.xamarin.monkeybrowser.tab2");
public NSString UserActivityTab3 = new NSString ("com.xamarin.monkeybrowser.tab3");
public NSString UserActivityTab4 = new NSString ("com.xamarin.monkeybrowser.tab4");
...

public FirstViewController Tab1 { get; set; }
public SecondViewController Tab2 { get; set;}
public ThirdViewController Tab3 { get; set; }
public FourthViewController Tab4 { get; set; }
...

public override bool WillContinueUserActivity (UIApplication application, string userActivityType)
{
    // Report Activity
    Console.WriteLine ("Will Continue Activity: {0}", userActivityType);

    // Take action based on the user activity type
    switch (userActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Inform view that it's going to be modified
        Tab1.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Inform view that it's going to be modified
        Tab2.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Inform view that it's going to be modified
        Tab3.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Inform view that it's going to be modified
        Tab4.PreparingToHandoff ();
        break;
    }

    // Inform system we handled this
    return true;
}
```

En el ejemplo anterior, cada controlador de vista se registra con el `AppDelegate` y tiene un público `PreparingToHandoff` método que muestra un indicador de actividad y un mensaje informa al usuario que la actividad está a entregar al dispositivo actual. Ejemplo:

```csharp
private void ShowBusy(string reason) {

    // Display reason
    BusyText.Text = reason;

    //Define Animation
    UIView.BeginAnimations("Show");
    UIView.SetAnimationDuration(1.0f);

    Handoff.Alpha = 0.5f;

    //Execute Animation
    UIView.CommitAnimations();
}
...

public void PreparingToHandoff() {
    // Inform caller
    ShowBusy ("Continuing Activity...");
}
```

El `ContinueUserActivity` de la `AppDelegate` se llamará para continuar realmente la actividad especificada. De nuevo, desde nuestra aplicación de ejemplo:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Report Activity
    Console.WriteLine ("Continuing User Activity: {0}", userActivity.ToString());

    // Get input and output streams from the Activity
    userActivity.GetContinuationStreams ((NSInputStream arg1, NSOutputStream arg2, NSError arg3) => {
        // Send required data via the streams
        // ...
    });

    // Take action based on the Activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Preform handoff
        Tab1.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab1});
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Preform handoff
        Tab2.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab2});
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Preform handoff
        Tab3.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab3});
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Preform handoff
        Tab4.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab4});
        break;
    }

    // Inform system we handled this
    return true;
}
```

El público `PerformHandoff` método de cada controlador de vista realmente realiza la entrega y restaura la actividad en el dispositivo actual. En el caso del ejemplo, muestra la misma dirección URL en una ficha especificada en la que el usuario era la exploración en un dispositivo diferente. Ejemplo:

```csharp
private void HideBusy() {

    //Define Animation
    UIView.BeginAnimations("Hide");
    UIView.SetAnimationDuration(1.0f);

    Handoff.Alpha = 0f;

    //Execute Animation
    UIView.CommitAnimations();
}
...

public void PerformHandoff(NSUserActivity activity) {

    // Hide busy indicator
    HideBusy ();

    // Extract URL from dictionary
    var url = activity.UserInfo ["Url"].ToString ();

    // Display value
    URL.Text = url;

    // Display the give webpage
    WebView.LoadRequest(new NSUrlRequest(NSUrl.FromString(url)));

    // Save activity
    UserActivity = activity;
    UserActivity.BecomeCurrent ();

}
```

El `ContinueUserActivity` método incluye un `UIApplicationRestorationHandler` que puede llamar a para el documento o el servicio de respuesta en función de reanudación de actividad. Deberá pasar un `NSArray` u objetos que se pueden restaurar en el controlador de restauración cuando se llama. Por ejemplo:

```csharp
completionHandler (new NSObject[]{Tab4});
```

Para cada objeto pasado, sus `RestoreUserActivityState` se llamará al método. Cada objeto, a continuación, pueden usar los datos en el `UserInfo` diccionario para restaurar su propio estado. Por ejemplo:

```csharp
public override void RestoreUserActivityState (NSUserActivity activity)
{
    base.RestoreUserActivityState (activity);

    // Log activity
    Console.WriteLine ("Restoring Activity {0}", activity.Title);
}
```

Para aplicaciones basadas en el documento, si no implementa la `ContinueUserActivity` método o devuelve `false`, `UIKit` o `AppKit` automáticamente se puede reanudar la actividad. Consulte la [que admiten la entrega en aplicaciones basadas en documento](#supporting-handoff-in-document-based-apps) sección para obtener más información.

### <a name="failing-handoff-gracefully"></a>Error de entrega correctamente

Puesto que la entrega se basa en la transmisión de información entre una colección conectada libremente entre sí dispositivos iOS y OS X, el proceso de transferencia a veces puede producir un error. Debe diseñar la aplicación para controlar correctamente estos errores e informar al usuario de las situaciones que surgen.

Si se produce un error, el `DidFailToContinueUserActivitiy` método de la `AppDelegate` se llamará. Por ejemplo:

```csharp
public override void DidFailToContinueUserActivitiy (UIApplication application, string userActivityType, NSError error)
{
    // Log information about the failure
    Console.WriteLine ("User Activity {0} failed to continue. Error: {1}", userActivityType, error.LocalizedDescription);
}
```

Debe usar proporcionado `NSError` para proporcionar información al usuario sobre el error.

## <a name="native-app-to-web-browser-handoff"></a>Aplicación nativa para la entrega de explorador Web

Un usuario que desee continuar una actividad sin necesidad de una aplicación nativa adecuada instalada en el dispositivo deseado. En algunas situaciones, una interfaz basada en web puede proporcionar la funcionalidad necesaria y todavía se puede continuar con la actividad. Por ejemplo, cuenta de correo electrónico del usuario puede proporcionar una interfaz de usuario basada en web para crear y leer mensajes.

Si la aplicación nativa, original conoce la dirección URL para la interfaz web (y la sintaxis necesaria para identificar el elemento especificado que se va a continuación), puede codificar esta información en el `WebpageURL` propiedad de la `NSUserActivity` instancia. Si el dispositivo receptor no tiene una aplicación nativa adecuada instalada para controlar la continuación, se puede llamar a la interfaz web proporcionado.

## <a name="web-browser-to-native-app-handoff"></a>Explorador Web para la entrega de aplicaciones nativas

Si el usuario estaba usando una interfaz basada en web en el dispositivo de origen y una aplicación nativa en el dispositivo receptor de notificaciones de la parte del dominio la `WebpageURL` propiedad y, a continuación, el sistema utilizará esa aplicación, el identificador de la continuación. El nuevo dispositivo recibirá un `NSUserActivity` instancia que marca el tipo de actividad como `BrowsingWeb` y el `WebpageURL` contendrá la dirección URL que se visita el usuario, el `UserInfo` diccionario estará vacío.

Para que una aplicación participar en este tipo de entrega, debe reclamar el dominio en un `com.apple.developer.associated-domains` derecho con el formato `<service>:<fully qualified domain name>` (por ejemplo: `activity continuation:company.com`).

Si el dominio especificado coincide con un `WebpageURL` descargas de valor de propiedad, entrega una lista de identificadores de aplicación aprobada desde el sitio Web en ese dominio. El sitio Web debe proporcionar una lista de identificadores aprobados en un archivo JSON con signo denominado **apple-app-sitio-asociación** (por ejemplo, `https://company.com/apple-app-site-association`).

Este archivo JSON contiene un diccionario que especifica una lista de identificadores de aplicación en el formulario `<team identifier>.<bundle identifier>`. Por ejemplo:

```csharp
{
    "activitycontinuation": {
        "apps": [    "YWBN8XTPBJ.com.company.FirstApp",
            "YWBN8XTPBJ.com.company.SecondApp" ]
    }
}
```

Para firmar el archivo JSON (para que tenga el valor correcto `Content-Type` de `application/pkcs7-mime`), use el **Terminal** app y una `openssl` comando con un certificado y clave emitido por una entidad de certificación de confianza para iOS (consulte [ https://support.apple.com/kb/ht5012 ](https://support.apple.com/kb/ht5012) para obtener una lista). Por ejemplo:

```csharp
echo '{"activitycontinuation":{"apps":["YWBN8XTPBJ.com.company.FirstApp",
"YWBN8XTPBJ.com.company.SecondApp"]}}' > json.txt

cat json.txt | openssl smime -sign -inkey company.com.key
-signer company.com.pem
-certfile intermediate.pem
-noattr -nodetach
-outform DER > apple-app-site-association
```

El `openssl` comando da como resultado un archivo JSON con signo que se coloca en su sitio Web en el **apple-app-sitio-asociación** dirección URL. Por ejemplo:

```csharp
https://example.com/apple-app-site-association.
```

La aplicación recibirá ninguna actividad cuyo `WebpageURL` dominio está en su `com.apple.developer.associated-domains` derecho. Solo el `http` y `https` protocolos son el soporte técnico, cualquier otro protocolo, producirá una excepción.

## <a name="supporting-handoff-in-document-based-apps"></a>Compatibilidad con entrega en aplicaciones basadas en documento

Como se indicó anteriormente, en iOS y OS X, las aplicaciones basadas en documento admite automáticamente la entrega de documentos de iCloud si la aplicación **Info.plist** archivo contiene un `CFBundleDocumentTypes` clave de `NSUbiquitousDocumentUserActivityType`. Por ejemplo:

```xml
<key>CFBundleDocumentTypes</key>
<array>
    <dict>
        <key>CFBundleTypeName</key>
        <string>NSRTFDPboardType</string>
        . . .
        <key>LSItemContentTypes</key>
        <array>
        <string>com.myCompany.rtfd</string>
        </array>
        . . .
        <key>NSUbiquitousDocumentUserActivityType</key>
        <string>com.myCompany.myEditor.editing</string>
    </dict>
</array>
```

En este ejemplo, la cadena es un designador de aplicación DNS inversa con el nombre de la actividad de anexado. Si especifica este modo, no es necesario que las entradas de tipo de actividad se repite en el `NSUserActivityTypes` matriz de los **Info.plist** archivo.

El objeto de actividad del usuario creado automáticamente (disponible a través del documento `UserActivity` propiedad) se puede hacer referencia a otros objetos en la aplicación y utilizado para restaurar el estado de continuación. Por ejemplo, para realizar un seguimiento de elemento selección y documento de colocar. Debe establecer este actividades `NeedsSave` propiedad `true` cada vez que el estado de los cambios y actualizar la `UserInfo` diccionario en el `UpdateUserActivityState` método.

El `UserActivity` propiedad puede usarse desde cualquier subproceso y se ajusta para el protocolo pares clave-valor de observación (KVO), por lo que puede usarse para mantener sincronizados un documento mientras se desplaza de entrada y salida de iCloud. El `UserActivity` se invalidará la propiedad cuando se cierra el documento.

Para obtener más información, consulte Apple [soporte técnico de la actividad de usuario en aplicaciones basadas en documento](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html#//apple_ref/doc/uid/TP40014338-CH3-SW5) documentación.

## <a name="supporting-handoff-in-responders"></a>Permitir la entrega en los servicios de respuesta

Puede asociar los servicios de respuesta (hereda de `UIResponder` en iOS o `NSResponder` en OS X) a las actividades estableciendo sus `UserActivity` propiedades. El sistema guarda automáticamente el `UserActivity` propiedad a veces, una llamada del servicio de respuesta adecuado `UpdateUserActivityState` método para agregar los datos actuales para el objeto de actividad del usuario mediante el `AddUserInfoEntriesFromDictionary` método.

## <a name="supporting-continuation-streams"></a>Compatibilidad con secuencias de continuación

La pueden darse situaciones donde la cantidad de información necesaria para continuar con una actividad no se pueden transferir eficazmente la carga inicial de entrega. En estas situaciones, la aplicación receptora puede establecer la secuencia de uno o más entre sí y la aplicación original para transferir los datos.

La aplicación original se establecerá el `SupportsContinuationStreams` propiedad del `NSUserActivity` a la instancia `true`. Por ejemplo:

```csharp
// Create a new user Activity to support this tab
UserActivity = new NSUserActivity (ThisApp.UserActivityTab1){
    Title = "Weather Tab",
    SupportsContinuationStreams = true
};
UserActivity.Delegate = new UserActivityDelegate ();

// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);

// Inform Activity that it has been updated
UserActivity.BecomeCurrent ();
```

A continuación, puede llamar la aplicación receptora la `GetContinuationStreams` método de la `NSUserActivity` en su `AppDelegate` para establecer la secuencia. Por ejemplo:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Report Activity
    Console.WriteLine ("Continuing User Activity: {0}", userActivity.ToString());

    // Get input and output streams from the Activity
    userActivity.GetContinuationStreams ((NSInputStream arg1, NSOutputStream arg2, NSError arg3) => {
        // Send required data via the streams
        // ...
    });

    // Take action based on the Activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Preform handoff
        Tab1.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab1});
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Preform handoff
        Tab2.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab2});
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Preform handoff
        Tab3.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab3});
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Preform handoff
        Tab4.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab4});
        break;
    }

    // Inform system we handled this
    return true;
}
```

En el dispositivo de origen, el delegado de actividad de usuario recibe las secuencias mediante una llamada a su `DidReceiveInputStream` método para proporcionar los datos que se solicitó que continuase la actividad del usuario en el dispositivo de reanudación.

Usará un `NSInputStream` para proporcionar acceso de solo lectura para transmitir datos y un `NSOutputStream` proporcionan acceso de solo escritura. Las secuencias se deben usar en un modo de solicitud y respuesta, donde la aplicación receptora solicita más datos y lo proporciona la aplicación original. Para que los datos escritos en el flujo de salida en el dispositivo de origen que se leen desde el flujo de entrada en el dispositivo continuo y viceversa.

Incluso en situaciones donde la continuación Stream son necesarios, debe haber un mínimo de atrás y hacia delante la comunicación entre las dos aplicaciones.

Para obtener más información, consulte Apple [mediante secuencias de continuación](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/AdoptingHandoff/AdoptingHandoff.html#//apple_ref/doc/uid/TP40014338-CH2-SW13) documentación.

## <a name="handoff-best-practices"></a>Prácticas recomendadas de entrega

Una implementación correcta de la continuación de una actividad de usuario a través de entrega sin problemas requiere un cuidadoso diseño debido a los distintos componentes implicados. Apple sugiere lo adoptar los siguientes procedimientos recomendados para las aplicaciones de entrega habilitada:

- Diseñar las actividades de usuario para requerir la carga más pequeña posible relacionar el estado de la actividad que se puede continuar. Cuanto mayor sea la carga, más tiempo tarda la continuación para empezar.
- Si se debe transferir grandes cantidades de datos para la continuación correcta, tenga en cuenta los costos implicados en la sobrecarga de red y la configuración.
- Es habitual que una gran aplicación de Mac crear actividades de usuario que se controlan mediante varias más pequeñas, específicos de la tarea de aplicaciones en dispositivos iOS. Las versiones de SO y diferentes de aplicaciones deben diseñarse para funcionar bien juntos o da errores leves.
- Al especificar los tipos de actividad, use la notación DNS inversa para evitar colisiones. Si una actividad es específica para una aplicación determinada, su nombre debe incluirse en la definición de tipo (por ejemplo `com.myCompany.myEditor.editing`). Si la actividad puede trabajar en varias aplicaciones, quite el nombre de la aplicación de la definición (por ejemplo `com.myCompany.editing`).
- Si la aplicación necesita para actualizar el estado de una actividad de usuario (`NSUserActivity`) establece el `NeedsSave` propiedad `true`. En los momentos adecuados, entrega llamará al delegado `UserActivityWillSave` método para que pueda actualizar la `UserInfo` diccionario según sea necesario.
- Dado que el proceso de entrega no podría inicializar al instante en el dispositivo receptor, es aconsejable implementar la `AppDelegate`del `WillContinueUserActivity` e informar al usuario que va a iniciar una continuación.

## <a name="example-handoff-app"></a>Aplicación de entrega de ejemplo

Como ejemplo del uso de entrega en una aplicación de Xamarin.iOS, hemos incluido la [ **MonkeyBrowser** ](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/) aplicación de ejemplo con esta guía. La aplicación tiene cuatro pestañas que el usuario puede utilizar para explorar la web, cada uno con un tipo de actividad determinado: Tiempo, como favorito, pausa para café y trabajo.

En cualquier ficha, cuando el usuario escribe una dirección URL y derivaciones de nuevo el **vaya** un nuevo botón `NSUserActivity` se crea para esa pestaña que contiene la dirección URL que el usuario está explorando actualmente:

[![](handoff-images/handoff01.png "Aplicación de entrega de ejemplo")](handoff-images/handoff01.png#lightbox)

Si otro de los dispositivos del usuario tiene el **MonkeyBrowser** aplicación instalada, ha iniciado sesión en iCloud con la misma cuenta de usuario, en la misma red y cerca del dispositivo anterior, se mostrará la actividad de entrega en la página principal pantalla (en la esquina inferior izquierda):

[![](handoff-images/handoff02.png "La actividad de entrega que se muestra en la pantalla principal en la esquina inferior izquierda")](handoff-images/handoff02.png#lightbox)

Si el usuario arrastra hacia arriba en el icono de entrega, se iniciará la aplicación y la actividad del usuario especificado en el `NSUserActivity` se continuará en el dispositivo nuevo:

[![](handoff-images/handoff03.png "La actividad del usuario continúa en el nuevo dispositivo")](handoff-images/handoff03.png#lightbox)

Cuando la actividad del usuario se ha enviado correctamente a Apple de otro dispositivo, el dispositivo de envío `NSUserActivity` recibirán una llamada a la `UserActivityWasContinued` método en su `NSUserActivityDelegate` le permita saber que la actividad del usuario se ha transferido correctamente a otro dispositivo.

## <a name="summary"></a>Resumen

En este artículo ha proporcionado una introducción a la infraestructura de entrega utilizada para continuar con una actividad de usuario entre varios dispositivos de Apple del usuario. A continuación, se ha mostrado cómo habilitar e implementar la entrega en una aplicación de Xamarin.iOS. Por último, también se han descrito los distintos tipos de continuaciones de entrega disponibles y las prácticas recomendadas de entrega.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Ejemplo de MonkeyBrowser](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [Novedades de iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guía de HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [Directrices de interfaz de usuario de HomeKit](https://developer.apple.com/homekit/ui-guidelines/)
- [Referencia de Framework HomeKit](https://developer.apple.com/library/ios/home_kit_framework_ref)
