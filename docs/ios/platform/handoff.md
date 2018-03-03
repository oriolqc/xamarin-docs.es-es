---
title: Handoff
description: "Este artículo cubre trabajar con entrega en una aplicación Xamarin.iOS para transferir las actividades de usuario entre aplicaciones que se ejecutan en el usuario 's otros dispositivos."
ms.topic: article
ms.prod: xamarin
ms.assetid: 405F966A-4085-4621-AA15-33D663AD15CD
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 0b3471f607bbde6560af597b6b901e6fbd1ec0b0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="handoff"></a>Handoff

_Este artículo cubre trabajar con entrega en una aplicación Xamarin.iOS para transferir las actividades de usuario entre aplicaciones que se ejecutan en el usuario 's otros dispositivos._

Apple incorporó entrega en iOS 8 y OS X Yosemite (10.10) para proporcionar un mecanismo común para el usuario transferir actividades iniciadas en uno de sus dispositivos a otro dispositivo que ejecuta la misma aplicación u otra aplicación que admita la misma actividad.

[ ![](handoff-images/handoff02.png "Un ejemplo de llevar a cabo una operación de entrega")](handoff-images/handoff02.png)

En este artículo se echar un vistazo rápido a habilitar la actividad de uso compartido en una aplicación de Xamarin.iOS y cubrir el marco de trabajo de entrega en detalle:

## <a name="about-handoff"></a>Acerca de la entrega

Entrega (también conocido como continuidad) se introdujo por Apple iOS 8 y OS X Yosemite (10.10) como una manera para que el usuario iniciar una actividad en uno de sus dispositivos (iOS o Mac) y siga esa misma actividad en otro de sus dispositivos (según se identifica por iClou del usuario d. cuenta).

Entrega que se haya expandido en iOS 9 para también admiten nuevas capacidades de búsqueda mejoradas. Para obtener más información, vea nuestra [mejoras en la búsqueda](~/ios/platform/search/index.md) documentación.

Por ejemplo, el usuario puede iniciar un correo electrónico en su iPhone y continuar sin problemas del correo electrónico en su equipo Mac, con todos la misma información de mensaje que se rellena y el cursor en la misma ubicación que deja en iOS.

Cualquiera de las aplicaciones que comparten el mismo _Id. de equipo_ son aptos para utilizando Handoff continúe las actividades de usuario en aplicaciones siempre y cuando estas aplicaciones pueden ser entregados a través de la tienda de aplicaciones de iTunes o firmado por un desarrollador registrado (para Mac, Enterprise o aplicaciones Ad Hoc).

Cualquier `NSDocument` o `UIDocument` aplicaciones automáticamente tienen entrega admiten integradas y requieren unos cambios mínimos para admitir la entrega.

### <a name="continuing-user-activities"></a>Continuar las actividades del usuario

El `NSUserActivity` clase (junto con algunos cambios pequeños en `UIKit` y `AppKit`) proporciona compatibilidad para la definición de actividad de un usuario que potencialmente puede continuar en otro de los dispositivos del usuario.

Para que una actividad que se pasan a través a otro de los dispositivos del usuario, se deben encapsular en una instancia `NSUserActivity`, se marca como el _actividad actual_, tiene su carga conjunto (los datos que se usa para realizar la continuación) y el a continuación, debe transmitir a actividad en el dispositivo.

Entrega pasa la configuración mínima de información para definir la actividad que se va a continuar con los paquetes más grandes de datos que se sincronizan a través de iCloud.

En el dispositivo receptor, el usuario recibirá una notificación de que hay una actividad de continuación. Si el usuario decide seguir la actividad en el nuevo dispositivo, se inicia la aplicación especificada (si no se está ejecutando) y la carga desde el `NSUserActivity` se usa para reiniciar la actividad.

[ ![](handoff-images/handoffinteractions.png "Información general sobre continuar las actividades del usuario")](handoff-images/handoffinteractions.png)

Solo las aplicaciones que comparten el mismo desarrollador Id. de equipo y responderán a una determinada _tipo de actividad_ son aptas para la continuación. Una aplicación define los tipos de actividad que admite en el `NSUserActivityTypes` clave de su **Info.plist** archivo. Por tanto, un dispositivo ininterrumpido elige la aplicación para realizar la continuación basándose en el identificador de equipo, tipo de actividad y, opcionalmente, el _título de la actividad_.

La aplicación receptora utiliza información de la `NSUserActivity`del `UserInfo` diccionario para configurar la interfaz de usuario y restaurar el estado de la actividad determinada para que la transición parece no tener problemas para el usuario final.

Si la continuación requiere más información que puede enviarse a través de forma eficaz una `NSUserActivity`, la reanudación de la aplicación puede enviar una llamada a la aplicación de origen y establecer uno o varios flujos para transmitir los datos necesarios. Por ejemplo, si la actividad editando un documento de texto grande con varias imágenes, transmisión por secuencias se necesitarían para transferir la información necesaria para continuar la actividad en el dispositivo receptor. Para obtener más información, consulte el [compatibles con secuencias de continuación](#Supporting-Continuation-Streams) sección más adelante.

Como se indicó anteriormente, `NSDocument` o `UIDocument` basado en las aplicaciones automáticamente tienen entrega admiten integrada. Para obtener más información, consulte el [compatibilidad con entrega en aplicaciones basadas en el documento](#Supporting-Handoff-in-Document-Based-Apps) sección más adelante.

### <a name="the-nsuseractivity-class"></a>La clase NSUserActivity

La `NSUserActivity` clase es el objeto principal en un intercambio de entrega y se utiliza para encapsular el estado de una actividad de usuario que está disponible para la continuación. Creará una instancia de una copia de una aplicación `NSUserActivity` para cualquier actividad es compatible con y desea seguir en otro dispositivo. Por ejemplo, editor de documentos crearía una actividad para cada documento abierto actualmente. Sin embargo, solo en el documento más frontal (que se muestra en la pestaña o ventana superior) es el _actividad actual_ y, en consecuencia, están disponibles para la continuación.

Una instancia de `NSUserActivity` se identifica por ambos su `ActivityType` y `Title` propiedades. El `UserInfo` propiedad de diccionario se utiliza para transportar información sobre el estado de la actividad. Establecer el `NeedsSave` propiedad `true` si desea diferida a cargar la información de estado a través de la `NSUserActivity`del delegado. Use la `AddUserInfoEntries` método para combinar los nuevos datos de otros clientes en la `UserInfo` diccionario según sea necesario para conservar el estado de la actividad.

### <a name="the-nsuseractivitydelegate-class"></a>La clase NSUserActivityDelegate

El `NSUserActivityDelegate` se utiliza para mantener la información en un `NSUserActivity`del `UserInfo` diccionario actualizado y sincronizado con el estado actual de la actividad. Cuando el sistema necesita la información de la actividad que se va a actualizarse (como antes de continuación en otro dispositivo), llama a la `UserActivityWillSave` método del delegado.

Debe implementar la `UserActivityWillSave` método y hacer los cambios a la `NSUserActivity` (como `UserInfo`, `Title`, etc.) para asegurarse de que sigue reflejando el estado de la actividad actual. Cuando el sistema llama a la `UserActivityWillSave` método, el `NeedsSave` se borrará la marca. Si modifica cualquiera de las propiedades de datos de la actividad, debe establecer `NeedsSave` a `true` nuevo.

En lugar de utilizar el `UserActivityWillSave` método presentada anteriormente, si lo desea puede tener `UIKit` o `AppKit` administrar automáticamente la actividad del usuario. Para ello, establezca el objeto de servicio de respuesta `UserActivity` propiedad e implemente el `UpdateUserActivityState` método. Consulte la [compatibilidad con entrega en los servicios de respuesta](#Supporting-Handoff-in-Responders) sección para obtener más información.

### <a name="app-framework-support"></a>Compatibilidad de marco de la aplicación

Ambos `UIKit` (iOS) y `AppKit` (OS X) proporcionan compatibilidad integrada para la entrega en el `NSDocument`, servicio de respuesta (`UIResponder`/`NSResponder`), y `AppDelegate` clases. Mientras que cada sistema operativo implementa entrega ligeramente diferente, el mecanismo básico y las API son los mismos.

#### <a name="user-activities-in-document-based-apps"></a>Actividades de usuario en aplicaciones basadas en el documento

Basadas en documentos aplicaciones iOS y OS X automáticamente tienen soporte técnico de entrega integrado. Para activar esta compatibilidad, debe agregar una `NSUbiquitousDocumentUserActivityType` clave y valor para cada `CFBundleDocumentTypes` entrada en la aplicación **Info.plist** archivo.

Si esta clave está presente, ambos `NSDocument` y `UIDocument` crear automáticamente `NSUserActivity` instancias para documentos basados en iCloud del tipo especificado. Debe proporcionar un tipo de actividad para cada tipo de documento admitido por la aplicación y varios tipos de documentos pueden utilizar el mismo tipo de actividad. Ambos `NSDocument` y `UIDocument` rellenar automáticamente el `UserInfo` propiedad de la `NSUserActivity` con sus `FileURL` valor de la propiedad.

En OS X, el `NSUserActivity` administrados por `AppKit` y se asocia automáticamente con los servicios de respuesta la actividad actual cuando la ventana del documento se convierte en la ventana principal. En iOS, para `NSUserActivity` objetos administrados por `UIKit`, debe llamar a `BecomeCurrent` método explícitamente o que el documento `UserActivity` propiedad establecida en un `UIViewController` cuando la aplicación se pone en primer plano.

`AppKit` se restaurará automáticamente cualquier `UserActivity` propiedad creada de esta forma en OS X. Esto ocurre si el `ContinueUserActivity` método `false` o si está sin implementar. En esta situación, se abre el documento con la `OpenDocument` método de la `NSDocumentController` y, a continuación, recibirá un `RestoreUserActivityState` llamada al método.

Consulte la [compatibilidad con entrega en aplicaciones basadas en el documento](#Supporting-Handoff-in-Document-Based-Apps) sección para obtener más información.

#### <a name="user-activities-and-responders"></a>Los servicios de respuesta y las actividades del usuario

Ambos `UIKit` y `AppKit` puede administrar automáticamente una actividad de usuario si se establece como un objeto de servicio de respuesta `UserActivity` propiedad. Si se ha modificado el estado, deberá establecer el `NeedsSave` propiedad del servicio de la respuesta `UserActivity` a `true`. El sistema guardará automáticamente el `UserActivity` cuando sea necesario, después de dar el tiempo de respuesta para actualizar el estado mediante una llamada a su `UpdateUserActivityState` método.

Si varios servicios de respuesta comparten una sola `NSUserActivity` instancia, que reciben una `UpdateUserActivityState` devolución de llamada cuando el sistema actualiza el objeto de actividad de usuario. El servicio de respuesta se debe llamar a la `AddUserInfoEntries` método para actualizar la `NSUserActivity`del `UserInfo` diccionario para reflejar el estado actual de la actividad en este momento. El `UserInfo` diccionario se borra antes de cada `UpdateUserActivityState` llamar.

Para anular la asociación propio de una actividad, puede establecer un servicio de respuesta su `UserActivity` propiedad `null`. Cuando se administra un marco de aplicación `NSUserActivity` instancia no tiene más los servicios de respuesta asociados o documentos, se invalidan automáticamente.

Consulte la [compatibilidad con entrega en los servicios de respuesta](#Supporting-Handoff-in-Responders) sección para obtener más información.

#### <a name="user-activities-and-the-appdelegate"></a>Las actividades del usuario y la AppDelegate

La aplicación `AppDelegate` es su punto de entrada principal cuando se administran una continuación de entrega. Cuando el usuario responde a una notificación de entrega, se inicia la aplicación adecuada (si no se está ejecutando) y la `WillContinueUserActivityWithType` método de la `AppDelegate` se llamará. En este punto, la aplicación debe informar al usuario que se está iniciando la continuación.

El `NSUserActivity` instancia se entrega cuando el `AppDelegate`del `ContinueUserActivity` se llama al método. En este punto, debe configurar la interfaz de usuario de la aplicación y seguir la actividad determinada.

Consulte la [implementar entrega](#Implementing-Handoff) sección para obtener más información.

## <a name="enabling-handoff-in-a-xamarin-app"></a>Habilitar la entrega en una aplicación de Xamarin

Debido a los requisitos de seguridad impuestos por la entrega, una aplicación de Xamarin.iOS que usa el marco de trabajo de entrega debe estar configurada correctamente en el Portal de desarrollador de Apple y en el archivo de proyecto de Xamarin.iOS.

Haga lo siguiente:

1. Inicie sesión en el [Portal para desarrolladores de Apple](http://developer.apple.com).
2. Haga clic en **certificados, identificadores & perfiles**.
3. Si aún no lo ha hecho, haga clic en **identificadores** y crear un identificador para la aplicación (por ejemplo, `com.company.appname`), persona editar su identificador existente.
4. Asegúrese de que el **iCloud** se ha comprobado el servicio para el Id. especificado: 

    [ ![](handoff-images/provision01.png "Habilitar el servicio de iCloud para el identificador especificado.")](handoff-images/provision01.png)
5. Guarde los cambios.
4. Haga clic en **perfiles de aprovisionamiento** > **desarrollo** y crear un nuevo desarrollo de perfil de aprovisionamiento para su aplicación: 

    [ ![](handoff-images/provision02.png "Crear un nuevo perfil de aprovisionamiento para la aplicación de desarrollo")](handoff-images/provision02.png)
5. Ya sea descargar e instalar el nuevo perfil de aprovisionamiento o use Xcode para descargar e instalar el perfil.
6. Editar las opciones de proyecto Xamarin.iOS y asegúrese de que está usando el perfil de aprovisionamiento que acaba de crear: 

    [ ![](handoff-images/provision03.png "Seleccione el perfil de aprovisionamiento que acaba de crear")](handoff-images/provision03.png)
7. A continuación, edite la **Info.plist** de archivos y asegúrese de que está utilizando el identificador de aplicación que se usó para crear el perfil de aprovisionamiento: 

    [ ![](handoff-images/provision04.png "Establecer el Id. de aplicación")](handoff-images/provision04.png)
8. Desplácese hasta la **modos en segundo plano** sección y compruebe los siguientes elementos: 

    [ ![](handoff-images/provision05.png "Habilitar los modos de fondo requerido")](handoff-images/provision05.png)
9. Guarde los cambios a todos los archivos.

Con esta configuración en su lugar, la aplicación ahora está lista para tener acceso a las API de marco de trabajo de entrega. Para obtener información detallada sobre el aprovisionamiento, vea nuestra [aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md) y [aprovisionamiento de la aplicación](~/ios/get-started/installation/device-provisioning/index.md) guías.

## <a name="implementing-handoff"></a>Implementación de entrega

Pueden continuar las actividades del usuario entre aplicaciones que se firman con el mismo Id. de equipo de desarrollo y admiten el mismo tipo de actividad. Implementar la entrega en una aplicación de Xamarin.iOS, tendrá que crear un objeto de actividad de usuario (ya sea en `UIKit` o `AppKit`), actualizar el estado del objeto para realizar el seguimiento de la actividad y continuar con la actividad en un dispositivo receptor.

### <a name="identifying-user-activities"></a>Identificar las actividades de usuario

El primer paso en la implementación de entrega es identificar los tipos de actividades de usuario que admite la aplicación y ver cuál de estas actividades es buenos candidatos para la continuación en otro dispositivo. Por ejemplo: una aplicación ToDo podría admitir la edición de elementos como una _tipo de actividad de usuario_y permite examinar la lista de elementos disponibles porque hay otra.

Una aplicación puede crear tantos tipos de actividad de usuario como son obligatorios, uno para cualquier función que proporciona la aplicación. Para cada tipo de actividad de usuario, la aplicación necesitará realizar el seguimiento cuando una actividad del tipo comienza y termina y necesita mantener información de estado actualizada para continuar con la tarea en otro dispositivo.

Las actividades del usuario pueden continuar en cualquier aplicación firmada con el mismo identificador de equipo sin ninguna asignación uno a uno entre las aplicaciones emisoras y receptoras. Por ejemplo, una aplicación determinada puede crear cuatro tipos diferentes de actividades, que se consumen individuales, las diferentes aplicaciones en otro dispositivo. Esto es algo habitual entre una versión de Mac de la aplicación (que puede tener muchas características y funciones) y aplicaciones de iOS, donde cada aplicación es más pequeño y se centra en una tarea específica.

### <a name="creating-activity-type-identifiers"></a>Creación de identificadores de tipo de actividad

El _identificador de tipo de actividad_ se agrega una cadena corta a la `NSUserActivityTypes` matriz de la aplicación **Info.plist** archivo usado para identificar de forma única un determinado tipo de actividad de usuario. Habrá una entrada en la matriz por cada actividad que admite la aplicación. Apple sugiere utilizando una notación de estilo de DNS inversa para el identificador de tipo de actividad para evitar conflictos. Por ejemplo: `com.company-name.appname.activity` para aplicaciones específicas basadas en las actividades o `com.company-name.activity` para las actividades que se pueden ejecutar en varias aplicaciones.

El identificador de tipo de actividad se usa al crear un `NSUserActivity` instancia para identificar el tipo de actividad. Cuando una actividad continúa en otro dispositivo, el tipo de actividad (junto con el identificador de equipo de la aplicación) determina qué aplicación para iniciar para continuar con la actividad.

Por ejemplo, vamos a crear una aplicación de ejemplo denominada **MonkeyBrowser** ([descargar aquí](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/)). Esta aplicación presentará cuatro pestañas, cada uno con un diferentes direcciones URL está abierto en una vista de explorador web. El usuario podrá seguir una pestaña en un dispositivo de iOS diferente con la aplicación.

Para crear los identificadores de tipo de actividad necesaria para admitir este comportamiento, edite el **Info.plist** archivo y cambie a la **origen** vista. Agregar un `NSUserActivityTypes` clave y crear los siguientes identificadores:

[ ![](handoff-images/type01.png "La clave de NSUserActivityTypes y los identificadores necesarios en el editor de plist")](handoff-images/type01.png)

Se crean cuatro nuevos identificadores de tipo de actividad, uno para cada una de las pestañas en el ejemplo **MonkeyBrowser** aplicación. Al crear sus propias aplicaciones, reemplace el contenido de la `NSUserActivityTypes` de matriz con los identificadores de tipo de actividad específico a las actividades de la aplicación es compatible con.

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

El `UserActivityReceivedData` método se llama cuando una secuencia de continuación ha recibido datos desde un dispositivo envío. Para obtener más información, consulte el [compatibles con secuencias de continuación](#Supporting-Continuation-Streams) sección más adelante.

El `UserActivityWasContinued` método se llama cuando ha tomado otro dispositivo a través de una actividad desde el dispositivo actual. Según el tipo de actividad, como agregar un nuevo elemento a una lista de tareas, la aplicación podría necesita anular la actividad en el dispositivo de envío.

El `UserActivityWillSave` método se llama antes de que los cambios realizados en la actividad se guardan y sincronizados en todos los dispositivos disponibles localmente. Puede usar este método para realizar cambios en el último minuto para la `UserInfo` propiedad de la `NSUserActivity` instancia antes de enviarlo.

### <a name="creating-a-nsuseractivity-instance"></a>Creación de una instancia de NSUserActivity

Todas las actividades que la aplicación desea proporcionar la posibilidad de continuar en otro dispositivo se deben encapsular en un `NSUserActivity` instancia. La aplicación puede crear tantos actividades según sea necesario y la naturaleza de esas actividades depende de la funcionalidad y las características de la aplicación en cuestión. Por ejemplo, una aplicación de correo electrónico podría crear una actividad para crear un nuevo mensaje y otro para leer un mensaje.

Para nuestra aplicación de ejemplo, un nuevo `NSUserActivity` se crea cada vez que el usuario introduce una nueva dirección URL en uno de la vista de explorador web con pestañas. El código siguiente almacena el estado de una ficha determinada:

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

Crea un nuevo `NSUserActivity` mediante uno de los tipos de actividad de usuario creada anteriormente y proporciona un título de lenguaje natural para la actividad. Se adjunta a una instancia de la `NSUserActivityDelegate` creado anteriormente para observar de estado se cambia y se informa de iOS que esta actividad de usuario es la actividad actual.

### <a name="populating-the-userinfo-dictionary"></a>Rellenar el diccionario de información de usuario

Como hemos visto anteriormente, el `UserInfo` propiedad de la `NSUserActivity` clase es un `NSDictionary` de pares de clave y valor utilizadas para definir el estado de una actividad determinada. Los valores almacenados en `UserInfo` debe ser uno de los siguientes tipos: `NSArray`, `NSData`, `NSDate`, `NSDictionary`, `NSNull`, `NSNumber`, `NSSet`, `NSString`, o `NSURL`. `NSURL` los valores de datos que señalan a documentos de iCloud se ajustará automáticamente para que apunten a los mismos documentos en un dispositivo receptor.

En el ejemplo anterior, creamos un `NSMutableDictionary` de objetos y la rellena con una única clave que proporciona la dirección URL que estaba viendo actualmente en la ficha especificada. El `AddUserInfoEntries` usó el método de la actividad del usuario para actualizar la actividad con los datos que se usará para restaurar la actividad en el dispositivo receptor:

```csharp
// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);
```

Apple recomienda mantener la información enviada a la menor cantidad para asegurarse de que la actividad se envía de manera oportuna para el dispositivo receptor. Si se requiere, mayor información que puede editar una imagen que se adjunta a un documento debe enviarse, debe usar el secuencias de continuación. Consulte la [compatibles con secuencias de continuación](#Supporting-Continuation-Streams) sección para obtener más detalles.

### <a name="continuing-an-activity"></a>Continuar una actividad

Entrega informará automáticamente local dispositivos iOS y OS X que están en la proximidad física en el dispositivo de origen y se inicie sesión en la misma cuenta de iCloud, de la disponibilidad de las actividades del usuario crítico. Si el usuario decide continuar una actividad en un dispositivo nuevo, el sistema iniciará la aplicación adecuada (según el identificador de equipo y el tipo de actividad) y la información su `AppDelegate` que continuación tiene que producirse.

En primer lugar, el `WillContinueUserActivityWithType` se llama al método para la aplicación puede informar al usuario que la continuación se va a comenzar. Usamos el siguiente código en el **AppDelegate.cs** archivo de nuestra aplicación de ejemplo para controlar una continuación que se inicie:

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

En el ejemplo anterior, cada controlador de vista se registra con el `AppDelegate` y tiene un complemento público `PreparingToHandoff` método que muestra un indicador de actividad y un mensaje que el usuario informará de que la actividad está a punto de ser entregado en el dispositivo actual. Ejemplo:

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

El `ContinueUserActivity` de la `AppDelegate` se llamará para continuar realmente la actividad determinada. Una vez más, en nuestra aplicación de ejemplo:

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

Público `PerformHandoff` método de cada controlador de vista realmente valida la entrega y restaura la actividad en el dispositivo actual. En el caso del ejemplo, muestra la misma dirección URL en una pestaña determinada que estaba navegando por el usuario en un dispositivo diferente. Ejemplo:

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

El `ContinueUserActivity` método incluye un `UIApplicationRestorationHandler` que puede llamar a de documento o un servicio de respuesta en función de reanudación de actividad. Debe pasar un `NSArray` u objetos pueden restaurarse al controlador de restauración cuando se le llama. Por ejemplo:

```csharp
completionHandler (new NSObject[]{Tab4});
```

Para cada objeto que se pasa, su `RestoreUserActivityState` se llama al método. Cada objeto, a continuación, puede usar los datos de la `UserInfo` diccionario para restaurar su propio estado. Por ejemplo:

```csharp
public override void RestoreUserActivityState (NSUserActivity activity)
{
    base.RestoreUserActivityState (activity);

    // Log activity
    Console.WriteLine ("Restoring Activity {0}", activity.Title);
}
```

Para aplicaciones basadas en el documento, si no implementa la `ContinueUserActivity` método o se devuelve `false`, `UIKit` o `AppKit` automáticamente se puede reanudar la actividad. Consulte la [compatibilidad con entrega en aplicaciones basadas en el documento](#Supporting-Handoff-in-Document-Based-Apps) sección para obtener más información.

### <a name="failing-handoff-gracefully"></a>Error de entrega correctamente

Puesto que entrega se basa en la transmisión de información entre una colección flexible conectado dispositivos iOS y OS X, el proceso de transferencia a veces puede producir un error. Debe diseñar la aplicación para controlar estos errores correctamente e informar al usuario de las situaciones que surgen.

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

Un usuario puede desear continuar una actividad sin necesidad de una aplicación nativa adecuada instalada en el dispositivo deseado. En algunas situaciones, una interfaz basada en web puede proporcionar la funcionalidad necesaria y todavía se puede continuar con la actividad. Por ejemplo, la cuenta de correo electrónico del usuario puede proporcionar una interfaz de usuario de base de web para redactar y leer mensajes.

Si la aplicación nativa, origen conoce la dirección URL para la interfaz web (y la sintaxis necesaria para identificar el elemento especificado que se va a continuación), puede codificar esta información en el `WebpageURL` propiedad de la `NSUserActivity` instancia. Si el dispositivo receptor no tiene una aplicación nativa apropiada instalada para controlar la continuación, puede llamar a la interfaz web proporcionado.

## <a name="web-browser-to-native-app-handoff"></a>Explorador Web para la entrega de aplicaciones nativas

Si el usuario estaba usando una interfaz basada en web en el dispositivo de origen y la parte del dominio de notificaciones de una aplicación nativa en el dispositivo receptor el `WebpageURL` propiedad y, a continuación, el sistema utilizará esa aplicación, el identificador de la continuación. El nuevo dispositivo recibirá un `NSUserActivity` instancia que marca el tipo de actividad como `BrowsingWeb` y la `WebpageURL` contendrá la dirección URL que se visita el usuario, el `UserInfo` diccionario estará vacío.

Por una aplicación participar en este tipo de entrega, debe solicitar el dominio en un `com.apple.developer.associated-domains` derechos con el formato `<service>:<fully qualified domain name>` (por ejemplo: `activity continuation:company.com`).

Si el dominio especificado coincide con un `WebpageURL` valor de la propiedad, entrega descarga una lista de identificadores de aplicación aprobadas desde el sitio Web en ese dominio. El sitio Web debe proporcionar una lista de identificadores aprobados en un archivo JSON con signo denominado **apple aplicación sitio asociación** (por ejemplo, `https://company.com/apple-app-site-association`).

Este archivo JSON contiene un diccionario que especifica una lista de identificadores de aplicación en el formulario `<team identifier>.<bundle identifier>`. Por ejemplo:

```csharp
{
    "activitycontinuation": {
        "apps": [    "YWBN8XTPBJ.com.company.FirstApp",
            "YWBN8XTPBJ.com.company.SecondApp" ]
    }
}
```

Para firmar el archivo JSON (para que tenga el valor correcto `Content-Type` de `application/pkcs7-mime`), use la **Terminal** aplicación y un `openssl` comando con un certificado y clave emitido por una entidad de certificación de confianza para iOS (consulte [ http://support.Apple.com/kb/ht5012](http://support.apple.com/kb/ht5012) para obtener una lista). Por ejemplo:

```csharp
echo '{"activitycontinuation":{"apps":["YWBN8XTPBJ.com.company.FirstApp",
"YWBN8XTPBJ.com.company.SecondApp"]}}' > json.txt

cat json.txt | openssl smime -sign -inkey company.com.key
-signer company.com.pem
-certfile intermediate.pem
-noattr -nodetach
-outform DER > apple-app-site-association
```

El `openssl` comando da como resultado un archivo JSON firmado que se incluyen en el sitio Web en el **apple aplicación sitio asociación** dirección URL. Por ejemplo:

```csharp
https://example.com/apple-app-site-association.
```

La aplicación recibirá ninguna actividad cuyo `WebpageURL` dominio está en su `com.apple.developer.associated-domains` derechos. Solo el `http` y `https` son la compatibilidad con protocolos, cualquier otro protocolo, producirá una excepción.

## <a name="supporting-handoff-in-document-based-apps"></a>Compatibilidad con entrega en aplicaciones basadas en el documento

Como se indicó anteriormente, en iOS y OS X, aplicaciones basadas en el documento admite automáticamente la entrega de documentos basados en iCloud si la aplicación **Info.plist** archivo contiene un `CFBundleDocumentTypes` clave de `NSUbiquitousDocumentUserActivityType`. Por ejemplo:

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

En este ejemplo, la cadena es un designador de aplicación DNS inversa con el nombre de la actividad que se anexa. Si especifica este modo, no es necesario que las entradas de tipo de actividad se repite en el `NSUserActivityTypes` matriz de la **Info.plist** archivo.

El objeto de actividad de los usuarios creados automáticamente (disponible a través del documento `UserActivity` propiedad) se pueden hacer referencia a otros objetos de la aplicación y usar para restaurar el estado de continuación. Por ejemplo, para realizar un seguimiento de elemento selección documento de posición y. Debe establecer este actividades `NeedsSave` propiedad `true` cada vez que el estado de cambios y actualizar la `UserInfo` diccionario en el `UpdateUserActivityState` método.

El `UserActivity` propiedad se puede usar desde cualquier subproceso y se ajusta al protocolo (KVO) observación de clave y valor, por lo que puede usarse para mantener sincronizados un documento cuando se mueven de entrada y salida de iCloud. El `UserActivity` propiedad quedarán invalidada cuando se cierra el documento.

Para obtener más información, vea de Apple [soporte técnico de la actividad de usuario en aplicaciones basadas en el documento](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html#//apple_ref/doc/uid/TP40014338-CH3-SW5) documentación.

## <a name="supporting-handoff-in-responders"></a>Compatibilidad con entrega en los servicios de respuesta

Puede asociar los servicios de respuesta (se hereda de cualquiera `UIResponder` en iOS o `NSResponder` en OS X) a las actividades estableciendo sus `UserActivity` propiedades. El sistema guarda automáticamente el `UserActivity` propiedad en la correspondiente veces, el servicio de respuesta de llamada `UpdateUserActivityState` método para agregar los datos actuales para el objeto de actividad del usuario mediante el `AddUserInfoEntriesFromDictionary` método.

## <a name="supporting-continuation-streams"></a>Compatibilidad con secuencias de continuación

El se pueden producir situaciones donde la cantidad de información necesaria para continuar con una actividad no se puede transferir de forma eficaz la carga inicial de entrega. En estas situaciones, la aplicación receptora puede establecer una secuencia de uno o más entre sí y la aplicación de origen para transferir los datos.

La aplicación de origen que se establecerá el `SupportsContinuationStreams` propiedad de la `NSUserActivity` instancia `true`. Por ejemplo:

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

En el dispositivo de origen, el delegado de actividad de usuario recibe las secuencias mediante una llamada a su `DidReceiveInputStream` método para proporcionar los datos solicitados para continuar la actividad del usuario en el dispositivo de reanudación.

Usará un `NSInputStream` para proporcionar acceso de solo lectura para transmitir datos y un `NSOutputStream` proporcionan acceso de solo escritura. Las secuencias deben usarse en un modo de solicitud y respuesta, donde la aplicación receptora solicita más datos y le proporciona la aplicación de origen. Para que los datos escritos en el flujo de salida en el dispositivo de origen que se leen desde el flujo de entrada en el dispositivo continúa y viceversa.

Incluso en situaciones donde la secuencia de continuación son necesarios, debe haber un mínimo de hacia atrás y hacia delante la comunicación entre las dos aplicaciones.

Para obtener más información, consulte de Apple [mediante secuencias de continuación](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/AdoptingHandoff/AdoptingHandoff.html#//apple_ref/doc/uid/TP40014338-CH2-SW13) documentación.

## <a name="handoff-best-practices"></a>Prácticas recomendadas de entrega

La implementación correcta de la continuación perfecta de una actividad de usuario a través de entrega requiere un cuidadoso diseño debido a los diferentes componentes implicados. Apple sugiere la adopción de las prácticas recomendadas de seguimiento para las aplicaciones de entrega habilitado:

- Diseñar las actividades de usuario para requerir la carga más pequeña posible relacionar el estado de la actividad para continuar. Cuanto mayor sea la carga, más tiempo tardará la continuación para iniciar.
- Si debe transferir grandes cantidades de datos para la continuación correcta, tenga en cuenta los costos implicados en la carga de red y la configuración.
- Es habitual que una aplicación de Mac grande crear las actividades de usuarios que administran con varios, más pequeñas, aplicaciones específicas de las tareas en dispositivos iOS. Las versiones de SO y diferentes de aplicaciones se deben diseñar para funcionar bien juntos o generar un error.
- Al especificar los tipos de actividad, use la notación DNS inversa para evitar colisiones. Si una actividad es específica para una aplicación determinada, su nombre debe incluirse en la definición de tipo (por ejemplo `com.myCompany.myEditor.editing`). Si la actividad pueda funcionar en varias aplicaciones, quite el nombre de la aplicación de la definición (por ejemplo `com.myCompany.editing`).
- Si la aplicación debe actualizar el estado de una actividad de usuario (`NSUserActivity`) establece el `NeedsSave` propiedad `true`. En los momentos oportunos, entrega llamará al delegado `UserActivityWillSave` método de forma que pueda actualizar la `UserInfo` diccionario según sea necesario.
- Dado que el proceso de entrega no puede inicializar al instante en el dispositivo receptor, debe implementar la `AppDelegate`del `WillContinueUserActivity` e informar al usuario que una continuación está a punto de iniciarse.

## <a name="example-handoff-app"></a>Aplicación de entrega de ejemplo

Como ejemplo del uso de entrega en una aplicación Xamarin.iOS, hemos incluido el [ **MonkeyBrowser** ](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/) aplicación de ejemplo con esta guía. Tiene cuatro pestañas que el usuario puede utilizar para navegar por la red, cada uno con un tipo de actividad determinada, la aplicación: tiempo, favorito, descanso y trabajo.

En cualquier ficha, cuando el usuario introduce una nueva dirección URL y derivaciones de la **vaya** botón, un nuevo `NSUserActivity` se crea para esa pestaña que contiene la dirección URL que el usuario está explorando actualmente:

[ ![](handoff-images/handoff01.png "Aplicación de entrega de ejemplo")](handoff-images/handoff01.png)

Si otro de los dispositivos del usuario tiene el **MonkeyBrowser** aplicación instalada, ha iniciado sesión en iCloud con la misma cuenta de usuario, está en la misma red y cerca el dispositivo anterior, la actividad de entrega se mostrará en la página principal pantalla (en la esquina inferior izquierda):

[ ![](handoff-images/handoff02.png "La actividad de entrega que se muestran en la pantalla principal en la esquina inferior izquierda")](handoff-images/handoff02.png)

Si el usuario arrastra hacia arriba en el icono de entrega, la aplicación se iniciará y la actividad del usuario especificado en el `NSUserActivity` continuará en el nuevo dispositivo:

[ ![](handoff-images/handoff03.png "La actividad del usuario continúa en el nuevo dispositivo")](handoff-images/handoff03.png)

Cuando la actividad del usuario se ha enviado correctamente a Apple de otro dispositivo, el dispositivo de envío `NSUserActivity` recibirá una llamada a la `UserActivityWasContinued` método en su `NSUserActivityDelegate` que lo sepan que la actividad del usuario se ha transferido correctamente a otro dispositivo.

## <a name="summary"></a>Resumen

Este artículo ha proporcionado una introducción al marco de entrega que se usa para seguir una actividad de usuario entre varios dispositivos de Apple del usuario. A continuación, ha explicado cómo habilitar e implementar la entrega en una aplicación de Xamarin.iOS. Por último, también se han descrito los distintos tipos de continuaciones de entrega disponibles y las prácticas recomendadas de entrega.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Ejemplo de MonkeyBrowser](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [What's New en iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guía de HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [Directrices de interfaz de usuario de HomeKit](https://developer.apple.com/homekit/ui-guidelines/)
- [Referencia de HomeKit Framework](https://developer.apple.com/library/ios/home_kit_framework_ref)
