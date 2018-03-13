---
title: "Tutorial: con la ubicación de fondo"
ms.topic: article
ms.prod: xamarin
ms.assetid: F8EEA0FD-5614-47FE-ADAC-80A5BCA6EB5F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: b10894d6b18d78d682825000726c5ef2cbe5ba6b
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="walkthrough---using-background-location"></a>Tutorial: con la ubicación de fondo

En este ejemplo, vamos a compilar aplicaciones de ubicación que imprime información acerca de la ubicación actual de iOS: latitud, longitud y otros parámetros de la pantalla. Esta aplicación muestra cómo realizar correctamente las actualizaciones de ubicación mientras la aplicación está activo o Backgrounded.

En este tutorial se explica algunos clave backgrounding conceptos, como registrar una aplicación como una aplicación de fondo necesario, la suspensión de las actualizaciones de la interfaz de usuario cuando la aplicación se backgrounded y trabajar con la `WillEnterBackground` y `WillEnterForeground` `AppDelegate` métodos .

## <a name="application-set-up"></a>Aplicación configurada


1. En primer lugar, cree un nuevo **iOS > aplicaciones > única de la vista de aplicación (C#)**. Llámelo _ubicación_ y asegúrese de que ha seleccionado iPad y iPhone.

1. Una aplicación de ubicación se califica como una aplicación de fondo necesarios en iOS. Registrar la aplicación como una aplicación de ubicación mediante la edición de la **Info.plist** archivo para el proyecto.

    En el Explorador de soluciones, haga doble clic en el **Info.plist** archivo para abrirlo y desplácese hasta la parte inferior de la lista. Colocar una marca de verificación por ambos el **habilitar modos en segundo plano** y **ubicación actualizaciones** casillas de verificación.


    En Visual Studio para Mac, tendrá una apariencia similar algo parecido a esto:

    [![](location-walkthrough-images/image7.png "Colocar una marca de verificación Habilitar modos en segundo plano y las casillas de verificación de las actualizaciones de ubicación")](location-walkthrough-images/image7.png#lightbox)

    En Visual Studio, **Info.plist** debe actualizarse manualmente mediante la adición de los siguientes pares de clave/valor:

    ```xml
    <key>UIBackgroundModes</key>
    <array>
        <string>location</string>
    </array>
    ```

1. Ahora que la aplicación está registrada, pueden obtener datos de ubicación del dispositivo. En iOS, el `CLLocationManager` clase se utiliza para tener acceso a información de ubicación y puede generar los eventos que proporcionan actualizaciones de ubicación.

1. En el código, cree una nueva clase denominada `LocationManager` que proporciona un único lugar para varias pantallas y código para suscribirse a las actualizaciones de ubicación. En el `LocationManager` clase, asegúrese de una instancia de la `CLLocationManager` denominado `LocMgr`:

    ```csharp
    public class LocationManager
    {
        protected CLLocationManager locMgr;

        public LocationManager () {
            this.locMgr = new CLLocationManager();
            this.locMgr.PausesLocationUpdatesAutomatically = false;

            // iOS 8 has additional permissions requirements
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                locMgr.RequestAlwaysAuthorization (); // works in background
                //locMgr.RequestWhenInUseAuthorization (); // only in foreground
            }

            if (UIDevice.CurrentDevice.CheckSystemVersion (9, 0)) {
                locMgr.AllowsBackgroundLocationUpdates = true;
            }
        }

        public CLLocationManager LocMgr {
            get { return this.locMgr; }
        }
    }
    ```

    El código anterior establece un número de propiedades y los permisos en el [CLLocationManager](https://developer.xamarin.com/api/type/CoreLocation.CLLocationManager/) clase:

    - `PausesLocationUpdatesAutomatically` : Es un valor booleano que se pueden establecer dependiendo de si el sistema se puede pausar las actualizaciones de ubicación. En algunos dispositivos el valor predeterminado es `true`, que puede hacer que el dispositivo seguir recibiendo actualizaciones de ubicación después de unos 15 minutos de fondo.
    - `RequestAlwaysAuthorization` -No debe pasar a este método para asignar al usuario de aplicación de la opción para permitir que la ubicación que se tenga acceso en segundo plano. `RequestWhenInUseAuthorization` También puede pasarse si desea volver a asignar al usuario la opción para permitir la ubicación tener acceso a solo cuando la aplicación está en primer plano.
    - `AllowsBackgroundLocationUpdates` : Es una propiedad booleana, introducida en iOS 9 que se pueden establecer para permitir que una aplicación recibir actualizaciones de ubicación cuando suspende.

    > [!IMPORTANT]
    > **ADVERTENCIA**: iOS 8 (y versiones posteriores) también requiere una entrada en el **Info.plist** archivo para mostrar al usuario como parte de la solicitud de autorización.

1. Agregar una clave `NSLocationAlwaysUsageDescription` o `NSLocationWhenInUseUsageDescription` con una cadena que se mostrará al usuario en la alerta que solicita acceso a datos de ubicación.

1. iOS 9 requiere que al usar `AllowsBackgroundLocationUpdates` el **Info.plist** incluye la clave `UIBackgroundModes` con el valor `location`. Si ha completado el paso 2 de este tutorial, esto debería ya está en el archivo Info.plist.


1. Dentro de la `LocationManager` de clases, cree un método llamado `StartLocationUpdates` con el código siguiente. Este código muestra cómo para empezar a recibir actualizaciones de la ubicación desde la `CLLocationManager`:

    ```csharp
    if (CLLocationManager.LocationServicesEnabled) {
        //set the desired accuracy, in meters
        LocMgr.DesiredAccuracy = 1;
        LocMgr.LocationsUpdated += (object sender, CLLocationsUpdatedEventArgs e) =>
        {
            // fire our custom Location Updated event
            LocationUpdated (this, new LocationUpdatedEventArgs (e.Locations [e.Locations.Length - 1]));
        };
        LocMgr.StartUpdatingLocation();
    }
    ```

    Hay varias cosas importantes que ocurre en este método. En primer lugar, se realiza una comprobación para ver si la aplicación tiene acceso a los datos de ubicación en el dispositivo. Comprobarlo mediante una llamada a `LocationServicesEnabled` en el `CLLocationManager`. Este método devolverá **false** si el usuario ha denegado el acceso a la aplicación a la información de ubicación.

1. A continuación, indicar al administrador de ubicación con qué frecuencia actualizar. `CLLocationManager` proporciona muchas opciones para el filtrado y cómo configurar datos de ubicación, incluida la frecuencia de las actualizaciones. En este ejemplo, establezca el `DesiredAccuracy` para actualizar cada vez que cambia la ubicación de un medidor. Para obtener más información acerca de cómo configurar la frecuencia de actualización de la ubicación y otras preferencias, consulte el [CLLocationManager Class Reference](http://developer.apple.com/library/ios/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html) en la documentación de Apple.

1. Por último, llame a `StartUpdatingLocation` en el `CLLocationManager` instancia. Esto indica al administrador de ubicación para obtener una corrección inicial en la ubicación actual y comenzar a enviar las actualizaciones

Hasta ahora, el Administrador de ubicación se ha creado, configurado con los tipos de datos que desea recibir, y determinó la ubicación inicial. Ahora el código necesario representar los datos de ubicación para la interfaz de usuario. Podemos hacer esto con un evento personalizado que toma un `CLLocation` como argumento:

```csharp
// event for the location changing
public event EventHandler<LocationUpdatedEventArgs>LocationUpdated = delegate { };
```

El siguiente paso es suscribirse a las actualizaciones de la ubicación de la `CLLocationManager`y generar personalizado `LocationUpdated` cuando hay nuevos datos de ubicación disponible, pasa la ubicación como un argumento de evento. Para ello, cree una nueva clase **LocationUpdateEventArgs.cs**. Este código es accesible dentro de la aplicación principal y devuelve la ubicación del dispositivo cuando se produce el evento:

```csharp
public class LocationUpdatedEventArgs : EventArgs
{
    CLLocation location;

    public LocationUpdatedEventArgs(CLLocation location)
    {
       this.location = location;
    }

    public CLLocation Location
    {
       get { return location; }
    }
}
```

## <a name="user-interface"></a>Interfaz de usuario

1. Use el Diseñador de iOS para compilar la pantalla que muestra la información de ubicación. Haga doble clic en el **Main.storyboard** archivo para empezar.

    En el guión gráfico, arrastre varias etiquetas en la pantalla para que actúe como marcadores de posición para la información de ubicación. En este ejemplo, hay etiquetas de latitud, longitud, altitud, curso y velocidad.

    El diseño debe ser similar a lo siguiente:

    ![](location-walkthrough-images/image8.png "Un ejemplo de diseño de interfaz de usuario en el Diseñador de iOS")

1. En el panel de soluciones, haga doble clic en el `ViewController.cs` archivo y modificarlo para crear una nueva instancia de la LocationManager y llamar a `StartLocationUpdates`en él.
  Cambie el código por el siguiente aspecto:

    ```csharp
    #region Computed Properties
    public static bool UserInterfaceIdiomIsPhone {
                get { return UIDevice.CurrentDevice.UserInterfaceIdiom == UIUserInterfaceIdiom.Phone; }
            }

    public static LocationManager Manager { get; set;}
    #endregion

    #region Constructors
    public ViewController (IntPtr handle) : base (handle)
    {
    // As soon as the app is done launching, begin generating location updates in the location manager
        Manager = new LocationManager();
        Manager.StartLocationUpdates();
    }

    #endregion
    ```

    Esto iniciará las actualizaciones de la ubicación en el inicio de la aplicación, aunque no se mostrará ningún dato.

1. Ahora que se reciben las actualizaciones de ubicación, actualizar la pantalla con la información de ubicación. El siguiente método obtiene la ubicación de nuestros `LocationUpdated` eventos y lo muestra en la interfaz de usuario:

    ```csharp
    #region Public Methods
    public void HandleLocationChanged (object sender, LocationUpdatedEventArgs e)
    {
        // Handle foreground updates
        CLLocation location = e.Location;

        LblAltitude.Text = location.Altitude + " meters";
        LblLongitude.Text = location.Coordinate.Longitude.ToString ();
        LblLatitude.Text = location.Coordinate.Latitude.ToString ();
        LblCourse.Text = location.Course.ToString ();
        LblSpeed.Text = location.Speed.ToString ();

        Console.WriteLine ("foreground updated");
    }
    #endregion
    ```

Necesitamos suscribirse a la `LocationUpdated` evento en nuestros AppDelegate y llame al método nuevo para actualizar la interfaz de usuario. Agregue el código siguiente en `ViewDidLoad,` justo después de la `StartLocationUpdates` llamar:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // It is better to handle this with notifications, so that the UI updates
    // resume when the application re-enters the foreground!
    Manager.LocationUpdated += HandleLocationChanged;

}
```


Ahora, cuando se ejecuta la aplicación, debería ser similar al siguiente:

[![](location-walkthrough-images/image5.png "Ejecutar una aplicación de ejemplo")](location-walkthrough-images/image5.png#lightbox)

## <a name="handling-active-and-background-states"></a>Control de los estados activo y en segundo plano

1. La aplicación salida de actualizaciones de ubicación mientras está en primer plano y activa. Para mostrar lo que sucede cuando la aplicación entra en el fondo, reemplace el `AppDelegate` cambios de estado de los métodos que realizan el seguimiento de aplicación para que la aplicación escribe en la consola cuando realiza una transición entre el primer plano y segundo plano:

    ```csharp
    public override void DidEnterBackground (UIApplication application)
    {
        Console.WriteLine ("App entering background state.");
    }

    public override void WillEnterForeground (UIApplication application)
    {
        Console.WriteLine ("App will enter foreground");
    }
    ```

    Agregue el código siguiente en el `LocationManager` para imprimir continuamente la ubicación actualizada son estando disponibles en segundo plano los datos a la salida de la aplicación, para comprobar la información de ubicación:

    ```csharp
    public class LocationManager
    {
        public LocationManager ()
        {
        ...
        LocationUpdated += PrintLocation;
        }
        ...

        //This will keep going in the background and the foreground
        public void PrintLocation (object sender, LocationUpdatedEventArgs e) {
        CLLocation location = e.Location;
        Console.WriteLine ("Altitude: " + location.Altitude + " meters");
        Console.WriteLine ("Longitude: " + location.Coordinate.Longitude);
        Console.WriteLine ("Latitude: " + location.Coordinate.Latitude);
        Console.WriteLine ("Course: " + location.Course);
        Console.WriteLine ("Speed: " + location.Speed);
        }
    }
    ```

1. Hay un problema restante con el código: intentar actualizar la interfaz de usuario cuando la aplicación se backgrounded causa iOS se finalizará lo. Cuando la aplicación entra en segundo plano, el código debe cancelar la suscripción a las actualizaciones de la ubicación y detener la actualización de la interfaz de usuario.

    iOS nos proporciona notificaciones cuando la aplicación está a punto de transición a una aplicación de diferentes Estados. En este caso, se puede suscribirse a la `ObserveDidEnterBackground` notificación.

    El fragmento de código siguiente muestra cómo utilizar una notificación para informar a la vista cuando se detenga las actualizaciones de la interfaz de usuario. Esto se ubicarán en `ViewDidLoad`:

    ```csharp
    UIApplication.Notifications.ObserveDidEnterBackground ((sender, args) => {
        Manager.LocationUpdated -= HandleLocationChanged;
    });
    ```

    Cuando se ejecuta la aplicación, la salida tendrá un aspecto similar al siguiente:

    ![](location-walkthrough-images/image6.png "Ejemplo de la salida de la ubicación en la consola de")

1. La aplicación imprime las actualizaciones de la ubicación en la pantalla cuando se trabaja en primer plano y continúa imprimir los datos en la ventana de salida de la aplicación mientras trabaja en segundo plano.

Solo uno de los problemas pendiente permanece: las actualizaciones de la interfaz de usuario inicia la pantalla cuando se carga la aplicación por primera vez, pero no tiene ninguna manera de saber cuando la aplicación se vuelve a escribir el primer plano. Si la aplicación backgrounded vuelve en primer plano, no reanudar las actualizaciones de la interfaz de usuario.

Para solucionar este problema, anidar una llamada para iniciar las actualizaciones de la interfaz de usuario dentro de otra notificación, que se activará cuando la aplicación entra en el estado activo:

```csharp
UIApplication.Notifications.ObserveDidBecomeActive ((sender, args) => {
  Manager.LocationUpdated += HandleLocationChanged;
});
```

Ahora la interfaz de usuario empezarán a actualizarse cuando se inicia la aplicación por primera vez y reanudar actualizar en cualquier momento la aplicación vuelve a estar en primer plano.

En este tutorial, creamos una aplicación de iOS con buen comportamiento, tenga en cuenta el fondo que imprime los datos de ubicación en la pantalla y la ventana de salida de la aplicación.


## <a name="related-links"></a>Vínculos relacionados

- [Ubicación (parte 4) (ejemplo)](https://developer.xamarin.com/samples/monotouch/Location/)
- [Referencia de marco de trabajo de ubicación principal](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CoreLocation_Framework/_index.html)
