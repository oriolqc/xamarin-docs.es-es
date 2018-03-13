---
title: Calendario
ms.topic: article
ms.prod: xamarin
ms.assetid: 78666541-CA14-4CD8-A94A-A9621C57813E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 9b744f4c8582aa9295645b2bdc22e6fddf2bedc3
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="calendar"></a>Calendario


## <a name="calendar-api"></a>API de calendario

Un nuevo conjunto de calendario APIs incorporadas en 4 Android admite aplicaciones que están diseñadas para leer o escribir datos en el proveedor de calendario. Estas API admiten una gran variedad de opciones de interacción con los datos de calendario, incluida la capacidad para leer y escribir los eventos, los asistentes y recordatorios. Mediante el proveedor de calendario en la aplicación, agregar a través de la API de datos aparecerá en la aplicación de calendario integrado que viene con Android 4.


## <a name="adding-permissions"></a>Agregar los permisos

Cuando se trabaja con el nuevo API de calendario en la aplicación, lo primero que debe hacer es agregar los permisos adecuados para el manifiesto de Android. Los permisos que necesita agregar son `android.permisson.READ_CALENDAR` y `android.permission.WRITE_CALENDAR`, dependiendo de si son leer o escribir datos de calendario.


## <a name="using-the-calendar-contract"></a>Cómo usar el contrato de calendario

Una vez configurados los permisos, puede interactuar con los datos de calendario mediante la `CalendarContract` clase. Esta clase proporciona un modelo de datos que las aplicaciones pueden usar cuando interactúa con el proveedor de calendario. El `CalendarContract` permite a las aplicaciones resolver los URI a entidades de calendario, como eventos y calendarios. También proporciona una manera de interactuar con varios campos en cada entidad, como el nombre del calendario y de Id., o inicio de un evento y fecha de finalización.

Veamos un ejemplo que utiliza la API de calendario. En este ejemplo, examinaremos cómo enumerar sus eventos y calendarios, así como cómo agregar un nuevo evento a un calendario.


## <a name="listing-calendars"></a>Lista de calendarios

En primer lugar, vamos a examinar cómo enumerar los calendarios que se han registrado en la aplicación de calendario. Para ello, se puede crear una instancia de un `CursorLoader`. Introdujo en Android 3.0 (API de 11), `CursorLoader` es la forma preferida para consumir un `ContentProvider`. Como mínimo, debemos especificar el Uri de contenido de calendarios y las columnas que desea devolver; Esta especificación de columna se conoce como un _proyección_.

Llamar a la `CursorLoader.LoadInBackground` método nos permite consultar un proveedor de contenido para los datos, como el proveedor de calendario.
`LoadInBackground` realiza la operación de carga real y devuelve un `Cursor` con los resultados de la consulta.

El `CalendarContract` nos ayuda a especificar el contenido de ambas `Uri` y la proyección. Para obtener el contenido `Uri` para consultar los calendarios, podemos usar simplemente la `CalendarContract.Calendars.ContentUri` propiedad similar al siguiente:

```csharp
var calendarsUri = CalendarContract.Calendars.ContentUri;
```

Mediante el `CalendarContract` para especificar qué calendario columnas que queremos es igual de sencillo. Simplemente agregaremos campos en la `CalendarContract.Calendars.InterfaceConsts` clase a una matriz. Por ejemplo, el código siguiente incluye el Id. del calendario, nombre para mostrar y nombre de cuenta:

```csharp
string[] calendarsProjection = {
    CalendarContract.Calendars.InterfaceConsts.Id,
    CalendarContract.Calendars.InterfaceConsts.CalendarDisplayName,
    CalendarContract.Calendars.InterfaceConsts.AccountName
};
```

El `Id` es importante incluir si usas un `SimpleCursorAdapter` para enlazar los datos a la interfaz de usuario, tal y como se verá en breve. Con el Uri de contenido y proyección en su lugar, creamos una instancia de la `CursorLoader` y llamar a la `CursorLoader.LoadInBackground` método para devolver un cursor con los datos del calendario, tal y como se muestra a continuación:

```csharp
var loader = new CursorLoader(this, calendarsUri, calendarsProjection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();

```

La interfaz de usuario para este ejemplo contiene un `ListView`, con cada elemento de la lista que representa un calendario único. El siguiente XML muestra el marcado que incluye el `ListView`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:orientation="vertical"
android:layout_width="fill_parent"
android:layout_height="fill_parent">
  <ListView
    android:id="@android:id/android:list"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content" />
</LinearLayout>
```

Además, es preciso especificar la interfaz de usuario para cada elemento de lista, que se coloca en un archivo XML independiente como sigue:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:orientation="vertical"
android:layout_width="fill_parent"
android:layout_height="wrap_content">
  <TextView android:id="@+id/calDisplayName"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:textSize="16dip" />
  <TextView android:id="@+id/calAccountName"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:textSize="12dip" />
</LinearLayout>
```

Desde este punto, es simplemente normal Android código para enlazar los datos desde la posición del cursor a la interfaz de usuario. Vamos a usar un `SimpleCursorAdapter` como se indica a continuación:

```csharp
string[] sourceColumns = {
    CalendarContract.Calendars.InterfaceConsts.CalendarDisplayName,
    CalendarContract.Calendars.InterfaceConsts.AccountName };

int[] targetResources = {
    Resource.Id.calDisplayName, Resource.Id.calAccountName };      

SimpleCursorAdapter adapter = new SimpleCursorAdapter (this,
    Resource.Layout.CalListItem, cursor, sourceColumns, targetResources);

ListAdapter = adapter;
```

En el código anterior, el adaptador tiene las columnas especificadas en el `sourceColumns` de matriz y los escribe en los elementos de la interfaz de usuario en el `targetResources` matriz para cada entrada de calendario en el cursor. La actividad que se usa aquí es una subclase de `ListActivity`; incluye la `ListAdapter` propiedad a la que se establezca el adaptador.

Esta es una captura de pantalla que muestra el resultado final, con la información de calendario que se muestra en el `ListView`:

[![CalendarDemo que se ejecutan en el emulador, mostrar dos entradas del calendario](calendar-images/11-calendar.png)](calendar-images/11-calendar.png#lightbox)



## <a name="listing-calendar-events"></a>Lista de los eventos de calendario

Siguiente Echemos un vistazo a cómo enumerar los eventos para un calendario determinado.
Basándose en el ejemplo anterior, le presentamos una lista de eventos cuando el usuario selecciona uno de los calendarios. Por lo tanto, se deberá controlar la selección de elementos en el código anterior:

```csharp
ListView.ItemClick += (sender, e) => {
    int i = (e as ItemEventArgs).Position;

    cursor.MoveToPosition(i);
    int calId =
        cursor.GetInt (cursor.GetColumnIndex (calendarsProjection [0]));

    var showEvents = new Intent(this, typeof(EventListActivity));
    showEvents.PutExtra("calId", calId);
    StartActivity(showEvents);
};
```

En este código, estamos creando un intento para abrir una actividad de tipo `EventListActivity`, pasar un identificador del calendario en el intento. Se necesitará el Id. de saber qué calendario para consultar los eventos. En el `EventListActivity`del `OnCreate` método, podemos recuperar el Id. de la `Intent` tal y como se muestra a continuación:

```csharp
_calId = Intent.GetIntExtra ("calId", -1);
```

Ahora vamos a eventos de consultas para este calendario identificador. El proceso para consultar los eventos es similar a la manera en que se consulta para obtener una lista de calendarios anteriormente, pero esta vez se trabajará con la `CalendarContract.Events` clase. El siguiente código crea una consulta para recuperar los eventos:

```csharp
var eventsUri = CalendarContract.Events.ContentUri;

string[] eventsProjection = {
    CalendarContract.Events.InterfaceConsts.Id,
    CalendarContract.Events.InterfaceConsts.Title,
    CalendarContract.Events.InterfaceConsts.Dtstart
};

var loader = new CursorLoader(this, eventsUri, eventsProjection,
                   String.Format ("calendar_id={0}", _calId), null, "dtstart ASC");
var cursor = (ICursor)loader.LoadInBackground();
```

En este código, obtenemos primero el contenido `Uri` para los eventos de la `CalendarContract.Events.ContentUri` propiedad. Luego, especificamos las columnas de evento que van a recuperar de la matriz eventsProjection.
Por último, creamos una instancia de un `CursorLoader` con esta información y la llamada el cargador `LoadInBackground` método para devolver un `Cursor` con los datos del evento.

Para mostrar los datos del evento en la interfaz de usuario, podemos usar marcado y código al igual que hicimos antes de mostrar la lista de calendarios. Una vez más, utilizamos `SimpleCursorAdapter` para enlazar los datos a un `ListView` tal como se muestra en el código siguiente:

```csharp
string[] sourceColumns = {
    CalendarContract.Events.InterfaceConsts.Title,
    CalendarContract.Events.InterfaceConsts.Dtstart };

int[] targetResources = {
    Resource.Id.eventTitle,
    Resource.Id.eventStartDate };

var adapter = new SimpleCursorAdapter (this, Resource.Layout.EventListItem,
    cursor, sourceColumns, targetResources);

adapter.ViewBinder = new ViewBinder ();       
ListAdapter = adapter;
```

La diferencia principal entre este código y el código que se usó previamente para mostrar la lista de calendario es el uso de un `ViewBinder`, que se establece en la línea:

```csharp
adapter.ViewBinder = new ViewBinder ();
```

La `ViewBinder` clase nos permite un mayor control sobre cómo se enlazar valores a las vistas. En este caso, usamos esto para convertir la hora de inicio de evento de milisegundos a una cadena de fecha, como se muestra en la siguiente implementación:

```csharp
class ViewBinder : Java.Lang.Object, SimpleCursorAdapter.IViewBinder
{    
    public bool SetViewValue (View view, Android.Database.ICursor cursor,
        int columnIndex)
    {
        if (columnIndex == 2) {
            long ms = cursor.GetLong (columnIndex);

            DateTime date = new DateTime (1970, 1, 1, 0, 0, 0,
                DateTimeKind.Utc).AddMilliseconds (ms).ToLocalTime ();

            TextView textView = (TextView)view;
            textView.Text = date.ToLongDateString ();

            return true;
        }
        return false;
    }    
}
```

Esto muestra una lista de eventos, tal y como se muestra a continuación:

[![Captura de pantalla de la aplicación de ejemplo muestra tres eventos de calendario](calendar-images/12-events.png)](calendar-images/12-events.png#lightbox)



## <a name="adding-a-calendar-event"></a>Agregar un evento de calendario

Hemos visto cómo leer datos del calendario. Ahora veamos cómo agregar un evento a un calendario. Para que funcione, no olvide incluir el `android.permission.WRITE_CALENDAR` permiso se ha mencionado anteriormente. Para agregar un evento a un calendario, se hará lo siguiente:

1.  Crear un `ContentValues` instancia.
1.  Utilice las claves de la `CalendarContract.Events.InterfaceConsts` clase para rellenar el `ContentValues` instancia.
1.  Establecer las zonas horarias de eventos, el inicio y finalización.
1.  Use un `ContentResolver` para insertar los datos del evento en el calendario.


El código siguiente muestra estos pasos:

```csharp
ContentValues eventValues = new ContentValues ();

eventValues.Put (CalendarContract.Events.InterfaceConsts.CalendarId,
    _calId);
eventValues.Put (CalendarContract.Events.InterfaceConsts.Title,
    "Test Event from M4A");
eventValues.Put (CalendarContract.Events.InterfaceConsts.Description,
    "This is an event created from Xamarin.Android");
eventValues.Put (CalendarContract.Events.InterfaceConsts.Dtstart,
    GetDateTimeMS (2011, 12, 15, 10, 0));
eventValues.Put (CalendarContract.Events.InterfaceConsts.Dtend,
    GetDateTimeMS (2011, 12, 15, 11, 0));

eventValues.Put(CalendarContract.Events.InterfaceConsts.EventTimezone,
    "UTC");
eventValues.Put(CalendarContract.Events.InterfaceConsts.EventEndTimezone,
    "UTC");

var uri = ContentResolver.Insert (CalendarContract.Events.ContentUri,
    eventValues);
```

Tenga en cuenta que si no se establece la zona horaria, una excepción de tipo `Java.Lang.IllegalArgumentException` se iniciará. Dado que los valores de hora de eventos se deben expresar en milisegundos desde la época, creamos un `GetDateTimeMS` (método) (en `EventListActivity`) para convertir las especificaciones de la fecha en formato de milisegundos:

```csharp
long GetDateTimeMS (int yr, int month, int day, int hr, int min)
{
    Calendar c = Calendar.GetInstance (Java.Util.TimeZone.Default);

    c.Set (Java.Util.CalendarField.DayOfMonth, 15);
    c.Set (Java.Util.CalendarField.HourOfDay, hr);
    c.Set (Java.Util.CalendarField.Minute, min);
    c.Set (Java.Util.CalendarField.Month, Calendar.December);
    c.Set (Java.Util.CalendarField.Year, 2011);

    return c.TimeInMillis;
}
```

Si se agrega un botón a la interfaz de usuario de la lista de eventos y ejecuta el código anterior en el botón, haga clic en controlador de eventos, el evento se agrega al calendario y se actualizaron en nuestra lista tal como se muestra a continuación:

[![Captura de pantalla de la aplicación de ejemplo con los eventos de calendario seguido de botón Agregar eventos de ejemplo](calendar-images/13.png)](calendar-images/13.png#lightbox)

Si se abre la aplicación de calendario, a continuación, veremos que se escribe el evento existe así:

[![Captura de pantalla de aplicación de calendario mostrando el evento de calendario seleccionado](calendar-images/14.png)](calendar-images/14.png#lightbox)

Como puede ver, Android permite el acceso sencillo y eficaz recuperar y conservar los datos del calendario, lo que permite a las aplicaciones sin problemas integrar capacidades de calendario.


## <a name="related-links"></a>Vínculos relacionados

- [Demostración de calendario (ejemplo)](https://developer.xamarin.com/samples/CalendarDemo/)
- [Introducción a helado Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
