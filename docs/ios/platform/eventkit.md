---
title: EventKit en Xamarin.iOS
description: Este documento describe eventkit de código y cómo usarlo en Xamarin.iOS. Describe los calendarios, los eventos de calendario y los avisos, busca las clases utilizadas comúnmente al programar con eventkit de código y mucho más.
ms.prod: xamarin
ms.assetid: 00E88629-357D-1FCD-4FCE-1330D5D9D32C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: ea03c8b382e2de29bd20ab1d696d7abb7733e182
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120234"
---
# <a name="eventkit-in-xamarinios"></a>EventKit en Xamarin.iOS

iOS tiene dos relacionados con el calendario aplicaciones integradas: la aplicación de calendario y la aplicación de recordatorios. Es fácil comprender cómo la aplicación de calendario administra datos de calendario, pero la aplicación de recordatorios es menos obvia. Recordatorios realmente pueden tener fechas asociadas con ellos en términos de cuando están debido, cuando se completen, etcetera. Por lo tanto, iOS almacena todos los datos de calendario, ya sea en los eventos de calendario o recordatorios en una ubicación, llamado el *base de datos de calendario*.

El marco de trabajo eventkit de código proporciona una manera de obtener acceso a la *calendarios*, *eventos de calendario*, y *recordatorios* datos que almacena la base de datos de calendario. Acceso a los calendarios y el calendario de eventos ha estado disponible desde iOS 4, pero el acceso a los avisos es nuevo en iOS 6.

En esta guía, vamos a cubrir:

-   **Conceptos básicos de EventKit** : Esto presentará las partes fundamentales de eventkit de código a través de las clases más importantes y se proporciona una descripción de su uso. En esta sección es necesaria leer antes de comenzar con la siguiente parte del documento. 
-   **Tareas comunes** – pretende ser una referencia rápida sobre cómo hacer cosas comunes como la sección tareas comunes; enumerar los calendarios, crear, guardar y recuperar de eventos y recordatorios, así como usar los controladores integrados para calendario creación y modificación de los eventos de calendario. En esta sección debe no leer delante hacia atrás, tal como lo está diseñada para ser una referencia para tareas concretas. 


Todas las tareas en esta guía están disponibles en la aplicación de ejemplo complementario:

 [![](eventkit-images/01.png "Las pantallas de aplicación de ejemplo complementario")](eventkit-images/01.png#lightbox)

## <a name="requirements"></a>Requisitos

EventKit se introdujo en iOS 4.0, pero el acceso a los datos de recordatorios se introdujo en iOS 6.0. Por lo tanto, para hacer desarrollo EventKit general, necesitará al menos tener como destino versiones 4.0 y 6.0 para los recordatorios.

Además, la aplicación de avisos no está disponible en el simulador, lo que significa que los datos de recordatorios también no estará disponibles, a menos que agregue en primer lugar. Además, solo se muestran las solicitudes de acceso al usuario en el dispositivo real. Por lo tanto, desarrollo EventKit mejor se ha probado en el dispositivo.

## <a name="event-kit-basics"></a>Conceptos básicos del Kit de eventos

Cuando se trabaja con eventkit de código, es importante tener una idea de las clases comunes y su uso. Todas estas clases se pueden encontrar en el `EventKit` y `EventKitUI` (para el `EKEventEditController`).

### <a name="eventstore"></a>EventStore

El *EventStore* es la clase más importante en EventKit porque es necesario realizar ninguna operación en eventkit de código. Se puede considerar como el almacenamiento persistente o motor de base de datos, para todos los datos de eventkit de código. Desde `EventStore` tienen acceso a los calendarios y eventos de calendario en la aplicación de calendario, así como los avisos en la aplicación de recordatorios.

Dado que `EventStore` es como un motor de base de datos, debe ser duradero, lo que significa que se debe crear y destruir la menor cantidad posible durante la vigencia de una instancia de aplicación. De hecho, se recomienda que una vez que cree una instancia de un `EventStore` en una aplicación, mantener esa referencia alrededor de toda la duración de la aplicación, a menos que esté seguro de que no necesita volver a. Además, todas las llamadas deben pasar a una sola `EventStore` instancia. Por este motivo, se recomienda el patrón Singleton para mantener una única instancia disponible.

#### <a name="creating-an-event-store"></a>Creación de un evento de Store

El código siguiente muestra una manera eficaz para crear una única instancia de la `EventStore` clase y que esté disponible estáticamente desde dentro de una aplicación:

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

El código anterior usa el patrón Singleton para crear una instancia de la `EventStore` cuando se cargue la aplicación. El `EventStore` pueden acceder globalmente desde dentro de la aplicación como sigue:

```csharp
App.Current.EventStore;
```

Tenga en cuenta que todos los ejemplos aquí usan este patrón, por lo que hacen referencia a la `EventStore` a través de `App.Current.EventStore`.

#### <a name="requesting-access-to-calendar-and-reminder-data"></a>Solicitar acceso a datos de calendario y aviso

Antes de que se les permita acceder a los datos a través de EventStore, una aplicación en primer lugar debe solicitar acceso a los datos de eventos de calendario o datos de recordatorios, según lo que se necesita. Para facilitar esto, el `EventStore` expone un método llamado `RequestAccess` que, cuando se llama, se mostrará una vista de alerta al usuario que le indica que la aplicación solicita acceso a los datos de calendario o datos de aviso, según qué `EKEntityType`se pasa a él. Porque genera una vista de alertas, la llamada es asincrónica y llamará a un controlador de finalización que se pasa como un `NSAction` (o expresión Lambda) a la que se recibirán dos parámetros; un valor booleano o si no se ha concedido acceso y un `NSError`, que, si le not null contiene información de error en la solicitud. Por ejemplo, la siguiente codificada solicitará acceso a datos de evento de calendario y mostrar una alerta ver si la solicitud no se ha concedido.

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

Una vez que se concedió la solicitud, se recordará siempre que la aplicación está instalada en el dispositivo y no aparecerá una alerta al usuario.
Sin embargo, solo se concede acceso al tipo de recurso, los eventos de calendario o recordatorios concedidos. Si una aplicación necesita acceso a ambos, debe solicitar ambos.

Dado que se recuerda permiso, es relativamente barato realizar la solicitud cada vez, por lo que es una buena idea solicitar siempre acceso antes de realizar una operación.

Además, dado que se llama al controlador de finalización en un subproceso independiente (sin interfaz de usuario), las actualizaciones de la interfaz de usuario en el controlador de finalización se deben llamar a través de `InvokeOnMainThread`, de lo contrario se producirá una excepción y si no se detecta, se bloqueará la aplicación.

### <a name="ekentitytype"></a>EKEntityType

`EKEntityType` es una enumeración que describe el tipo de `EventKit` elemento o los datos. Tiene dos valores: `Event` y recordatorio. Se utiliza en una serie de métodos, incluido `EventStore.RequestAccess` para indicar a `EventKit` qué tipo de datos para obtener acceso a o recuperar.

### <a name="ekcalendar"></a>EKCalendar

 *EKCalendar* representa un calendario, que contiene un grupo de eventos del calendario. Calendarios pueden almacenarse en muchos lugares diferentes, como localmente, en *iCloud*, en un 3rd party ubicación del proveedor, como un *Exchange Server* o *Google*, etcetera. Muchas veces `EKCalendar` se usa para indicar `EventKit` dónde buscar los eventos o dónde guardarlos.

### <a name="ekeventeditcontroller"></a>EKEventEditController

 *EKEventEditController* puede encontrarse en el `EventKitUI` espacio de nombres y es un controlador integrado que puede utilizarse para editar o crear eventos de calendario. De forma similar a la compilación en los controladores de la cámara, `EKEventEditController` hace el trabajo pesado por usted en mostrar la interfaz de usuario y control de guardar.

### <a name="ekevent"></a>EKEvent

 *EKEvent* representa un evento de calendario. Ambos `EKEvent` y `EKReminder` heredar `EKCalendarItem` y tienen campos como `Title`, `Notes`, y así sucesivamente.

### <a name="ekreminder"></a>EKReminder

 *EKReminder* representa un elemento de recordatorio.

### <a name="ekspan"></a>EKSpan

*EKSpan* es una enumeración que describe el intervalo de eventos al modificar los eventos que se pueden repetir y tienen dos valores: *esta* y *FutureEvents*. `ThisEvent` significa que los cambios sólo se producen el evento concreto en la serie que se hace referencia, mientras que `FutureEvents` afectarán a todas las futuras periodicidades y ese evento.

## <a name="tasks"></a>Tareas

Para facilitar su uso, el uso de EventKit ha dividido en las tareas comunes, descritas en las secciones siguientes.

### <a name="enumerate-calendars"></a>Enumerar los calendarios

Para enumerar los calendarios que el usuario ha configurado en el dispositivo, llame `GetCalendars` en el `EventStore` y pase el tipo de calendarios (avisos o eventos) que desea recibir:

```csharp
EKCalendar[] calendars = 
App.Current.EventStore.GetCalendars ( EKEntityType.Event );
```

### <a name="add-or-modify-an-event-using-the-built-in-controller"></a>Agregar o modificar un evento mediante el controlador integrado

El *EKEventEditViewController* hace mucho del trabajo pesado por usted si desea crear o editar un evento con la misma interfaz de usuario que se presenta al usuario cuando se usa la aplicación de calendario:

 [![](eventkit-images/02.png "La interfaz de usuario que se presenta al usuario cuando se usa la aplicación de calendario")](eventkit-images/02.png#lightbox)

Para ello, desea declararlo como una variable de nivel de clase para que no sea recolección si se declara dentro de un método:

```csharp
public class HomeController : DialogViewController
{
        protected CreateEventEditViewDelegate eventControllerDelegate;
        ...
}
```

A continuación, para iniciarlo: crear instancias de ella, cómo asignarle una referencia a la `EventStore`, conectar un *EKEventEditViewDelegate* delegan en él y, a continuación, mostrarlo utilizando `PresentViewController`:

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

Opcionalmente, si desea rellenar previamente el evento, puede crear un nuevo evento (como se muestra a continuación), o puede recuperar un evento guardado:

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

Para usar un evento existente, consulte el *recuperar un evento por Id. de* sección más adelante.

El delegado debe invalidar el `Completed` método, que es invocado por el controlador cuando el usuario ha terminado con el cuadro de diálogo:

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

Si lo desea, en el delegado, puede comprobar el *acción* en el `Completed` método para modificar el evento y vuelva a guardar o hacer otras cosas, si se cancela, etc.:

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

Para crear un evento en el código, utilice el *FromStore* método de fábrica en el `EKEvent` clase y establezca ningún dato en él:

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

Debe establecer el calendario que desea que el evento que se guarda en, pero si no dispone de ninguna preferencia, puede usar el valor predeterminado:

```csharp
newEvent.Calendar = App.Current.EventStore.DefaultCalendarForNewEvents;
```

Para guardar el evento, llame a la *SaveEvent* método en el `EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveEvent ( newEvent, EKSpan.ThisEvent, out e );
```

Después de guardarla, la *EventIdentifier* se actualizará la propiedad con un identificador único que puede usarse posteriormente para recuperar el evento:

```csharp
Console.WriteLine ("Event Saved, ID: " + newEvent.CalendarItemIdentifier);
```

 `EventIdentifier` es que una cadena con formato GUID.

### <a name="create-a-reminder-programmatically"></a>Crear mediante programación un recordatorio

Creación de un aviso en el código es el mismo que para crear un evento de calendario:

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

### <a name="retrieving-an-event-by-id"></a>Recuperación de un evento por Id.

Para recuperar un evento por él, es el Id., use la *EventFromIdentifier* método en el `EventStore` y pasarle el `EventIdentifier` que se extrajo de los eventos:

```csharp
EKEvent mySavedEvent = App.Current.EventStore.EventFromIdentifier ( newEvent.EventIdentifier );
```

Para los eventos, hay dos propiedades de identificador, pero `EventIdentifier` es la única que funciona para este.

### <a name="retrieving-a-reminder-by-id"></a>Recuperar un recordatorio por Id.

Para recuperar un recordatorio, utilice el *GetCalendarItem* método en el `EventStore` y pasarle el *CalendarItemIdentifier*:

```csharp
EKCalendarItem myReminder = App.Current.EventStore.GetCalendarItem ( reminder.CalendarItemIdentifier );
```

Dado que `GetCalendarItem` devuelve un `EKCalendarItem`, se debe convertir a `EKReminder` si debe tener acceso a datos de recordatorio o usar la instancia como un `EKReminder` más adelante.

No use `GetCalendarItem` para los eventos de calendario, como en el momento de escribir este artículo, no funciona.

### <a name="deleting-an-event"></a>Eliminar un evento

Para eliminar un evento de calendario, llame a *RemoveEvent* en su `EventStore` y pasarle una referencia para el evento y la correspondiente `EKSpan`:

```csharp
NSError e;
App.Current.EventStore.RemoveEvent ( mySavedEvent, EKSpan.ThisEvent, true, out e);
```

Tenga en cuenta sin embargo, una vez eliminado un evento, la referencia del evento será `null`.

### <a name="deleting-a-reminder"></a>Eliminar un recordatorio

Para eliminar un recordatorio, llame a *RemoveReminder* en el `EventStore` y pasarle una referencia al recordatorio:

```csharp
NSError e;
App.Current.EventStore.RemoveReminder ( myReminder as EKReminder, true, out e);
```

Tenga en cuenta que en el código anterior es una conversión a `EKReminder`, ya que `GetCalendarItem` se usó para recuperarlo

### <a name="searching-for-events"></a>Buscar eventos

Para buscar los eventos de calendario, debe crear un *NSPredicate* objeto a través de la *PredicateForEvents* método en el `EventStore`. Un `NSPredicate` es una consulta de objeto de datos que iOS usa para buscar coincidencias:

```csharp
DateTime startDate = DateTime.Now.AddDays ( -7 );
DateTime endDate = DateTime.Now;
// the third parameter is calendars we want to look in, to use all calendars, we pass null
NSPredicate query = App.Current.EventStore.PredicateForEvents ( startDate, endDate, null );
```

Una vez que haya creado el `NSPredicate`, utilice el *EventsMatching* método en el `EventStore`:

```csharp
// execute the query
EKCalendarItem[] events = App.Current.EventStore.EventsMatching ( query );
```

Tenga en cuenta que las consultas son sincrónicas (bloqueo) y pueden tardar mucho tiempo, dependiendo de la consulta, por lo que puede poner en marcha un nuevo subproceso o tarea que lo haga.

### <a name="searching-for-reminders"></a>Buscando recordatorios

Buscando recordatorios es similar a los eventos; requiere un predicado, pero la llamada ya es asincrónica, por lo que no hace falta preocuparse por el subproceso de bloqueo:

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

Este documento le da una visión general de ambos las partes importantes de framework eventkit de código y un número de las tareas más comunes. Sin embargo, el marco de trabajo eventkit de código es muy grande y eficaces e incluye características que aún no se han introducido aquí, como: actualizaciones por lotes, configuración de las alarmas, configuración de periodicidad en eventos, registrar y escuchar los cambios en la base de datos de calendario establecer las Geovallas y mucho más.  Para obtener más información, consulte Apple [calendario y la Guía de programación de recordatorios](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html).


## <a name="related-links"></a>Vínculos relacionados

- [Calendarios (ejemplo)](https://developer.xamarin.com/samples/monotouch/Calendars/)
- [Introducción a iOS 6](~/ios/platform/introduction-to-ios6/index.md)
- [Introducción a los calendarios y avisos](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)
