---
title: EventKit
description: "Esta guía proporciona información general sobre cómo obtener acceso y trabajar con calendarios, CalendarEvents y avisos de los datos almacenados en la base de datos de calendario, tal y como se expone a través de la EventKit. Incluye las clases principales y su función en la programación de EventKit, así como un número de tareas comunes asociadas con el marco de trabajo EventKit."
ms.topic: article
ms.prod: xamarin
ms.assetid: 00E88629-357D-1FCD-4FCE-1330D5D9D32C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: a08bc67a9af653a9a646ad62071df0400ce58c12
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="eventkit"></a>EventKit

_Esta guía proporciona información general sobre cómo obtener acceso y trabajar con calendarios, CalendarEvents y avisos de los datos almacenados en la base de datos de calendario, tal y como se expone a través de la EventKit. Incluye las clases principales y su función en la programación de EventKit, así como un número de tareas comunes asociadas con el marco de trabajo EventKit._

iOS tiene dos relacionados con el calendario aplicaciones integradas: la aplicación de calendario y la aplicación de avisos. Es lo suficientemente sencilla comprender cómo la aplicación de calendario administra datos del calendario, pero la aplicación de avisos es menos obvia. Recordatorios realmente pueden tener fechas asociadas con ellos en términos de cuando están debido, cuando está completa, etcetera. Por lo tanto, iOS almacena todos los datos de calendario, ya sea en los eventos de calendario o avisos, en una ubicación, llama a la *base de datos de calendario*.

El marco de trabajo de EventKit proporciona una manera de tener acceso a la *calendarios*, *eventos de calendario*, y *recordatorios* datos que almacena la base de datos de calendario. Acceso a los calendarios y el calendario de eventos ha estado disponible desde iOS 4, pero el acceso a los avisos es nuevo en iOS 6.

En esta guía vamos a cubrir:

-   **Conceptos básicos de EventKit** – este incorporará los componentes fundamentales de EventKit a través de las clases principales y proporciona una descripción de su uso. Esta sección es necesaria leer antes de abordar la siguiente parte del documento. 
-   **Tareas comunes** : la sección tareas comunes se pretende ser una referencia rápida sobre cómo realizar tareas comunes como; de calendario eventos y recordatorios, así como usar los controladores integrados para enumerar los calendarios, crear, guardar y recuperar creación y modificación de los eventos de calendario. En esta sección necesita no puede leer parte delantera hacia atrás, tal y como ha diseñado para ser una referencia para tareas específicas. 


Todas las tareas de esta guía están disponibles en la aplicación de ejemplo complementarias:

 [![](eventkit-images/01.png "Las pantallas de aplicación de ejemplo complementarias")](eventkit-images/01.png#lightbox)

## <a name="requirements"></a>Requisitos

EventKit se introdujo en iOS 4.0, pero el acceso a los datos de recordatorios se introdujo en iOS 6.0. Por lo tanto, para hacer el desarrollo de EventKit general, deberá deba tener como destino de al menos versión 4.0 y 6.0 para recordatorios.

Además, la aplicación de avisos no está disponible en el simulador, lo que significa que los datos de recordatorios también no estará disponibles, a menos que agregue primero. Además, las solicitudes de acceso solo se muestran al usuario en el dispositivo real. Por lo tanto, desarrollo EventKit mejor se ha probado en el dispositivo.

## <a name="event-kit-basics"></a>Fundamentos del Kit de eventos

Cuando se trabaja con EventKit, es importante tener una idea de las clases comunes y su uso. Todas estas clases se encuentra en la `EventKit` y `EventKitUI` (para el `EKEventEditController`).

### <a name="eventstore"></a>EventStore

El *EventStore* es la clase más importante en EventKit porque es necesario realizar ninguna operación en EventKit. Se puede considerarse como el almacenamiento persistente o el motor de base de datos, para todos los datos de EventKit. Desde `EventStore` tiene acceso a los calendarios y los eventos de calendario en la aplicación de calendario, así como los avisos en la aplicación de avisos.

Dado que `EventStore` es como un motor de base de datos, debe ser larga duración, lo que significa que se debe crear y destruir lo mínimo posible durante la duración de una instancia de la aplicación. De hecho, se recomienda que una vez que se crea una instancia de un `EventStore` en una aplicación, mantener esa referencia alrededor durante toda la duración de la aplicación, a menos que esté seguro de que no necesita volver a. Además, todas las llamadas deben pasar a una sola `EventStore` instancia. Por este motivo, se recomienda el patrón Singleton para mantener una única instancia disponible.

#### <a name="creating-an-event-store"></a>Crear un almacén de eventos

El código siguiente muestra un método eficaz para crear una única instancia de la `EventStore` clase y ponerlo a disposición estáticamente desde dentro de una aplicación:

```csharp
public class App
{
    public static App Current {
            get { return current; }
    }
    private static App current;

    public EKEventStore EventStore {
            get { return eventStore; }
    }
    protected EKEventStore eventStore;

    static App ()
    {
            current = new App();
    }
    protected App () 
    {
            eventStore = new EKEventStore ( );
    }
}
```

El código anterior usa el patrón Singleton para crear una instancia de la `EventStore` cuando se carga la aplicación. La `EventStore` puede tener acceso global desde dentro de la aplicación como se indica a continuación:

```csharp
App.Current.EventStore;
```

Tenga en cuenta que todos los ejemplos aquí usa este modelo, por lo que hacen referencia a la `EventStore` a través de `App.Current.EventStore`.

#### <a name="requesting-access-to-calendar-and-reminder-data"></a>Solicitando acceso al calendario y los datos de aviso

Para poder tener acceso a los datos a través de la EventStore, una aplicación debe solicitar acceso a los datos de eventos de calendario o datos de avisos, según lo que se necesita. Para facilitar esto, el `EventStore` expone un método llamado `RequestAccess` que, cuando se llama, se mostrará una vista de alertas para el usuario que se lo indica que la aplicación solicita acceso a los datos de calendario o datos de recordatorio, dependiendo de qué `EKEntityType`se pasa a él. Dado que genera una vista de alertas, la llamada es asincrónica y llamará a un controlador de finalización que se pasa como un `NSAction` (o expresión Lambda) a él que recibirá los dos parámetros; un valor booleano de si no se ha concedido acceso y un `NSError`, que, si le distinta de null contiene información de error en la solicitud. Por ejemplo, la siguiente codificada solicitará acceso a datos de eventos de calendario y mostrar una alerta ver si la solicitud no se ha concedido.

```csharp
App.Current.EventStore.RequestAccess (EKEntityType.Event, 
    (bool granted, NSError e) => {
            if (granted)
                    //do something here
            else
                    new UIAlertView ( "Access Denied", 
"User Denied Access to Calendar Data", null,
"ok", null).Show ();
            } );
```

Una vez que se concedió la solicitud, se recordará siempre que la aplicación está instalada en el dispositivo y no mostrará una alerta al usuario.
Sin embargo, solo se concede acceso al tipo de recurso, los eventos de calendario o avisos concedidos. Si una aplicación necesita tener acceso a ambos, debe solicitar ambas.

Dado que se recuerda el permiso, es relativamente barato realizar la solicitud cada vez, por lo que es una buena idea siempre solicitar acceso antes de realizar una operación.

Además, dado que se llama al controlador de finalización en un subproceso independiente (sin interfaz de usuario), las actualizaciones de la interfaz de usuario en el controlador de finalización se deben llamar a través de `InvokeOnMainThread`, en caso contrario, se producirá una excepción y, si no se detecta, la aplicación se bloqueará.

### <a name="ekentitytype"></a>EKEntityType

`EKEntityType` es una enumeración que describe el tipo de `EventKit` elemento o datos. Tiene dos valores: `Event` y un aviso. Se utiliza en una serie de métodos, incluidos los `EventStore.RequestAccess` para indicar `EventKit` qué tipo de datos para obtener acceso a o recuperar.

### <a name="ekcalendar"></a>EKCalendar

 *EKCalendar* representa un calendario, que contiene un grupo de eventos de calendario. Calendarios pueden almacenarse en muchos lugares diferentes, como localmente, en *iCloud*, en un 3rd party ubicación del proveedor como un *Exchange Server* o *Google*, etcetera. Muchas veces `EKCalendar` se usa para indicar `EventKit` dónde debe buscar eventos y dónde guardarlos.

### <a name="ekeventeditcontroller"></a>EKEventEditController

 *EKEventEditController* puede encontrarse en el `EventKitUI` espacio de nombres y es un controlador integrado que puede usarse para editar o crear eventos de calendario. Parecido a integrado en los controladores de cámara, `EKEventEditController` hace el trabajo pesado para mostrar la interfaz de usuario y control de guardar.

### <a name="ekevent"></a>EKEvent

 *EKEvent* representa un evento de calendario. Ambos `EKEvent` y `EKReminder` heredarlo `EKCalendarItem` y tener campos como `Title`, `Notes`, y así sucesivamente.

### <a name="ekreminder"></a>EKReminder

 *EKReminder* representa un elemento de recordatorio.

### <a name="ekspan"></a>EKSpan

*EKSpan* es una enumeración que describe el intervalo de eventos al modificar los eventos que pueden repetirse y tienen dos valores: *esta* y *FutureEvents*. `ThisEvent` significa que los cambios sólo se producen el evento concreto de la serie que se hace referencia, mientras que `FutureEvents` afectará a ese evento y todas las repeticiones futuras.

## <a name="tasks"></a>Tareas

Para facilitar su uso, uso de EventKit se ha dividido en tareas comunes, que se describe en las secciones siguientes.

### <a name="enumerate-calendars"></a>Enumerar los calendarios

Para enumerar los calendarios que el usuario ha configurado en el dispositivo, llame `GetCalendars` en el `EventStore` y pase el tipo de calendario (avisos o eventos) que desea recibir:

```csharp
EKCalendar[] calendars = 
App.Current.EventStore.GetCalendars ( EKEntityType.Event );
```

### <a name="add-or-modify-an-event-using-the-built-in-controller"></a>Agregar o modificar un evento con el controlador integrado

El *EKEventEditViewController* realiza mucho trabajo pesado para usted si desea crear o editar un evento con la misma interfaz de usuario que se presenta al usuario cuando se usa la aplicación de calendario:

 [![](eventkit-images/02.png "La interfaz de usuario que se presenta al usuario cuando se usa la aplicación de calendario")](eventkit-images/02.png#lightbox)

Para usarla, querrá declararlo como una variable de nivel de clase para que no obtengan recolección si se declara dentro de un método:

```csharp
public class HomeController : DialogViewController
{
        protected CreateEventEditViewDelegate eventControllerDelegate;
        ...
}
```

A continuación, para iniciarlo: crear instancias de él, asígnele una referencia a la `EventStore`, conecte un *EKEventEditViewDelegate* delegan en él y, a continuación, mostrarlo mediante la `PresentViewController`:

```csharp
EventKitUI.EKEventEditViewController eventController = 
        new EventKitUI.EKEventEditViewController ();

// set the controller's event store - it needs to know where/how to save the event
eventController.EventStore = App.Current.EventStore;

// wire up a delegate to handle events from the controller
eventControllerDelegate = new CreateEventEditViewDelegate ( eventController );
eventController.EditViewDelegate = eventControllerDelegate;

// show the event controller
PresentViewController ( eventController, true, null );
```

Si lo desea, si desea rellenar previamente el evento, puede crear un nuevo evento (como se muestra a continuación), o puede recuperar un evento guardado:

```csharp
EKEvent newEvent = EKEvent.FromStore ( App.Current.EventStore );
// set the alarm for 10 minutes from now
newEvent.AddAlarm ( EKAlarm.FromDate ( DateTime.Now.AddMinutes ( 10 ) ) );
// make the event start 20 minutes from now and last 30 minutes
newEvent.StartDate = DateTime.Now.AddMinutes ( 20 );
newEvent.EndDate = DateTime.Now.AddMinutes ( 50 );
newEvent.Title = "Get outside and exercise!";
newEvent.Notes = "This is your reminder to go and exercise for 30 minutes.”;
```

Si desea rellenar previamente la interfaz de usuario, asegúrese de establecer la propiedad de evento en el controlador:

```csharp
eventController.Event = newEvent;
```

Para usar un evento existente, consulte la *recuperar un evento con el identificador* sección más adelante.

El delegado debe invalidar el `Completed` método, que es llamado por el controlador cuando el usuario ha terminado con el cuadro de diálogo:

```csharp
protected class CreateEventEditViewDelegate : EventKitUI.EKEventEditViewDelegate
{
        // we need to keep a reference to the controller so we can dismiss it
        protected EventKitUI.EKEventEditViewController eventController;

        public CreateEventEditViewDelegate (EventKitUI.EKEventEditViewController eventController)
        {
                // save our controller reference
                this.eventController = eventController;
        }

        // completed is called when a user eith
        public override void Completed (EventKitUI.EKEventEditViewController controller, EKEventEditViewAction action)
        {
                eventController.DismissViewController (true, null);
                }
        }
}
```

Si lo desea, en el delegado, puede comprobar la *acción* en el  `Completed` /método siguiente para modificar el evento y se vuelve a guardar o realizar otras acciones, si se cancela, etc.:

```csharp
public override void Completed (EventKitUI.EKEventEditViewController controller, EKEventEditViewAction action)
{
        eventController.DismissViewController (true, null);

        switch ( action ) {

        case EKEventEditViewAction.Canceled:
                break;
        case EKEventEditViewAction.Deleted:
                break;
        case EKEventEditViewAction.Saved:
                // if you wanted to modify the event you could do so here,
// and then save:
                //App.Current.EventStore.SaveEvent ( controller.Event, )
                break;
        }
}
```

### <a name="creating-an-event-programmatically"></a>Crear un evento mediante programación

Para crear un evento en el código, use la *FromStore* método de fábrica en el `EKEvent` clase y establecer los datos en ella:

```csharp
EKEvent newEvent = EKEvent.FromStore ( App.Current.EventStore );
// set the alarm for 10 minutes from now
newEvent.AddAlarm ( EKAlarm.FromDate ( DateTime.Now.AddMinutes ( 10 ) ) );
// make the event start 20 minutes from now and last 30 minutes
newEvent.StartDate = DateTime.Now.AddMinutes ( 20 );
newEvent.EndDate = DateTime.Now.AddMinutes ( 50 );
newEvent.Title = "Get outside and do some exercise!";
newEvent.Notes = "This is your motivational event to go and do 30 minutes of exercise. Super important. Do this.";
```

Debe establecer el calendario que desea que el evento que se guarda en, pero si no tienes ninguna preferencia, puede usar el valor predeterminado:

```csharp
newEvent.Calendar = App.Current.EventStore.DefaultCalendarForNewEvents;
```

Para guardar el evento, llame a la *SaveEvent* método en el `EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveEvent ( newEvent, EKSpan.ThisEvent, out e );
```

Una vez guardada, la *EventIdentifier* se actualizará la propiedad con un identificador único que puede usarse posteriormente para recuperar el evento:

```csharp
Console.WriteLine ("Event Saved, ID: " + newEvent.CalendarItemIdentifier);
```

 `EventIdentifier` es que una cadena con formato GUID.

### <a name="create-a-reminder-programmatically"></a>Crear un aviso mediante programación

Crear un aviso en el código es muy parecida a la creación de un evento de calendario:

```csharp
EKReminder reminder = EKReminder.Create ( App.Current.EventStore );
reminder.Title = "Do something awesome!";
reminder.Calendar = App.Current.EventStore.DefaultCalendarForNewReminders;
```

Para guardar, llame a la *SaveReminder* método en el `EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveReminder ( reminder, true, out e );
```

### <a name="retrieving-an-event-by-id"></a>Recuperar un evento por Id.

Para recuperar un evento y que es el Id., use la *EventFromIdentifier* método en el `EventStore` y pasarle el `EventIdentifier` que se extrae del evento:

```csharp
EKEvent mySavedEvent = App.Current.EventStore.EventFromIdentifier ( newEvent.EventIdentifier );
```

Para los eventos, hay dos propiedades de identificador, pero `EventIdentifier` es la única que funciona para este.

### <a name="retrieving-a-reminder-by-id"></a>Recuperar un aviso por Id.

Para recuperar un aviso, utilice la *GetCalendarItem* método en el `EventStore` y pasarle el *CalendarItemIdentifier*:

```csharp
EKCalendarItem myReminder = App.Current.EventStore.GetCalendarItem ( reminder.CalendarItemIdentifier );
```

Dado que `GetCalendarItem` devuelve un `EKCalendarItem`, deben convertirse a `EKReminder` si necesita tener acceso a datos de aviso o usar la instancia como un `EKReminder` más adelante.

No use `GetCalendarItem` para los eventos de calendario, como en el momento de escribir este artículo, no funciona.

### <a name="deleting-an-event"></a>Eliminar un evento

Para eliminar un evento de calendario, llame a *RemoveEvent* en su `EventStore` y pasar una referencia para el evento y la correspondiente `EKSpan`:

```csharp
NSError e;
App.Current.EventStore.RemoveEvent ( mySavedEvent, EKSpan.ThisEvent, true, out e);
```

Tenga en cuenta sin embargo, una vez eliminado un evento, la referencia de evento será `null`.

### <a name="deleting-a-reminder"></a>Eliminar un aviso

Para eliminar un aviso, llame a *RemoveReminder* en el `EventStore` y pasar una referencia para el recordatorio:

```csharp
NSError e;
App.Current.EventStore.RemoveReminder ( myReminder as EKReminder, true, out e);
```

Tenga en cuenta que en el código anterior es una conversión a `EKReminder`, porque `GetCalendarItem` se usó para recuperarlo

### <a name="searching-for-events"></a>Buscar eventos

Para buscar eventos de calendario, debe crear un *NSPredicate* objeto a través de la *PredicateForEvents* método en el `EventStore`. Un `NSPredicate` es una consulta de objeto de datos que iOS se utiliza para localizar coincidencias:

```csharp
DateTime startDate = DateTime.Now.AddDays ( -7 );
DateTime endDate = DateTime.Now;
// the third parameter is calendars we want to look in, to use all calendars, we pass null
NSPredicate query = App.Current.EventStore.PredicateForEvents ( startDate, endDate, null );
```

Una vez que haya creado la `NSPredicate`, use la *EventsMatching* método en el `EventStore`:

```csharp
// execute the query
EKCalendarItem[] events = App.Current.EventStore.EventsMatching ( query );
```

Tenga en cuenta que las consultas son sincrónicas (bloqueo) y pueden tardar mucho tiempo, en función de la consulta, por lo que puede poner en marcha un nuevo subproceso o tarea para que lo haga.

### <a name="searching-for-reminders"></a>Buscando recordatorios

Buscando recordatorios es similar a los eventos; requiere un predicado, pero la llamada ya es asincrónica, por lo que no necesitan preocuparse bloquear el subproceso que:

```csharp
// create our NSPredicate which we'll use for the query
NSPredicate query = App.Current.EventStore.PredicateForReminders ( null );

// execute the query
App.Current.EventStore.FetchReminders (
        query, ( EKReminder[] items ) => {
                // do someting with the items
        } );
```

## <a name="summary"></a>Resumen

Este documento le asignó una visión general de ambos las partes importantes de framework EventKit y un número de las tareas más comunes. Sin embargo, el marco de trabajo de EventKit es muy grande y eficaz e incluye características que aún no ha introducido aquí, como: actualizaciones por lotes, configurar alarmas, configuración de periodicidad en eventos, registrar y la escucha de cambios en la base de datos de calendario establecer GeoFences y mucho más.  Para obtener más información, consulte de Apple [calendario y la Guía de programación de avisos](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html).


## <a name="related-links"></a>Vínculos relacionados

- [Calendarios (ejemplo)](https://developer.xamarin.com/samples/monotouch/Calendars/)
- [Introducción a iOS 6](~/ios/platform/introduction-to-ios6/index.md)
- [Introducción a los calendarios y avisos](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)
