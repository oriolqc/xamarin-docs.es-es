---
title: 'Tutorial: ubicación en segundo plano en Xamarin.iOS'
description: Este documento proporciona un tutorial sobre cómo usar la información de ubicación en una aplicación de Xamarin.iOS backgrounded. Describe el programa de instalación necesario, interfaz de usuario y Estados de la aplicación.
ms.prod: xamarin
ms.assetid: F8EEA0FD-5614-47FE-ADAC-80A5BCA6EB5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: fa8a48e165764a449af4bc5414d2e66aecea8269
ms.sourcegitcommit: 495680e74c72e7c570e68cde95d3d3643b1fcc8a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/02/2019
ms.locfileid: "58870149"
---
# <a name="walkthrough---background-location-in-xamarinios"></a>Tutorial: ubicación en segundo plano en Xamarin.iOS

En este ejemplo, vamos a compilar aplicaciones de la ubicación que imprime información acerca de la ubicación actual de un iOS: latitud, longitud y otros parámetros de la pantalla. Esta aplicación demostrará cómo realizar correctamente las actualizaciones de ubicación mientras la aplicación está activa o Backgrounded.

En este tutorial se explica alguna clave procesamiento en segundo plano de los conceptos, como registrar una aplicación como una aplicación en segundo plano necesarias, la suspensión de las actualizaciones de la interfaz de usuario cuando la aplicación se pasa a segundo plano y trabajar con el `WillEnterBackground` y `WillEnterForeground` `AppDelegate` métodos .

## <a name="application-set-up"></a>Aplicación configurada


1. En primer lugar, cree un nuevo **iOS > aplicación > aplicación de vista única (C#)**. Llámelo _ubicación_ y asegúrese de que se han seleccionado iPad y iPhone.

1. Una aplicación de la ubicación se califica como una aplicación en segundo plano necesarias en iOS. Registrar la aplicación como una aplicación de ubicación mediante la edición de la **Info.plist** archivo del proyecto.

    En el Explorador de soluciones, haga doble clic en el **Info.plist** archivo para abrirlo y desplácese hasta el final de la lista. Coloque una marca de verificación por ambos el **habilitar modos en segundo plano** y **actualizaciones de ubicación** casillas de verificación.

    En Visual Studio para Mac, será similar a algo parecido a esto:

    [![](location-walkthrough-images/image7.png "Marque las casillas de las actualizaciones de ubicación y los modos en segundo plano habilitar")](location-walkthrough-images/image7.png#lightbox)

    En Visual Studio, **Info.plist** debe actualizarse manualmente agregando el siguiente par clave-valor:

    ```xml
    <key>UIBackgroundModes</key>
    <array>
      <string>location</string>
    </array>
    ```

1. Ahora que está registrada la aplicación, pueden obtener datos de ubicación del dispositivo. En iOS, la `CLLocationManager` clase se utiliza para tener acceso a información de ubicación y puede provocar los eventos que proporcionan actualizaciones de ubicación.

1. En el código, cree una nueva clase denominada `LocationManager` que proporciona un único lugar para varias pantallas y código para suscribirse a las actualizaciones de ubicación. En el `LocationManager` de clases, cree una instancia de la `CLLocationManager` llamado `LocMgr`:

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

    El código anterior establece un número de propiedades y los permisos en el [CLLocationManager](xref:CoreLocation.CLLocationManager) clase:

    - `PausesLocationUpdatesAutomatically` : Es un valor booleano que se puede establecer dependiendo de si se permite el sistema para pausar las actualizaciones de ubicación. En algunos dispositivos el valor predeterminado es `true`, que puede hacer que el dispositivo seguir recibiendo actualizaciones de ubicación después de unos 15 minutos de fondo.
    - `RequestAlwaysAuthorization` -Se debe pasar este método para proporcionar al usuario de la aplicación la opción para permitir que la ubicación para tener acceso en segundo plano. `RequestWhenInUseAuthorization` También se puede pasar si desea dar al usuario la opción para permitir que la ubicación para tener acceso solo cuando la aplicación está en primer plano.
    - `AllowsBackgroundLocationUpdates` : Es una propiedad booleana, introducida en iOS 9 que se pueden establecer para permitir que una aplicación recibir actualizaciones de ubicación cuando se suspende.

    > [!IMPORTANT]
    > iOS 8 (y versiones posteriores) también requiere una entrada en el **Info.plist** archivo para mostrar al usuario como parte de la solicitud de autorización.

1. Agregue una clave `NSLocationAlwaysUsageDescription` o `NSLocationWhenInUseUsageDescription` con una cadena que se mostrará al usuario en la alerta que solicita acceso a datos de ubicación.

1. iOS 9 que requiere cuando se usa `AllowsBackgroundLocationUpdates` el **Info.plist** incluye la clave `UIBackgroundModes` con el valor `location`. Si ha completado el paso 2 de este tutorial, esto debería ya en el archivo Info.plist.


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

    Hay varios aspectos importantes que ocurre en este método. En primer lugar, realizamos una comprobación para ver si la aplicación tiene acceso a datos de ubicación en el dispositivo. Comprobarlo mediante una llamada a `LocationServicesEnabled` en el `CLLocationManager`. Este método devolverá **false** si el usuario ha denegado el acceso a información de ubicación de la aplicación.

1. A continuación, indique la frecuencia con el Administrador de ubicación para actualizar. `CLLocationManager` proporciona muchas opciones para filtrar y cómo configurar datos de ubicación, incluida la frecuencia de las actualizaciones. En este ejemplo, establezca el `DesiredAccuracy` para actualizar cada vez que cambia la ubicación mediante un medidor. Para obtener más información sobre cómo configurar la frecuencia de actualización de la ubicación y otras preferencias, consulte el [referencia de clase CLLocationManager](https://developer.apple.com/library/ios/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html) en la documentación de Apple.

1. Por último, llame a `StartUpdatingLocation` en el `CLLocationManager` instancia. Esto indica al administrador de ubicación para obtener una corrección inicial en la ubicación actual y a empezar a enviar las actualizaciones

Hasta ahora, el Administrador de ubicación creado, configurada con los tipos de datos que deseamos recibir, y determinó la ubicación inicial. Ahora el código necesita representar los datos de ubicación para la interfaz de usuario. Podemos hacer esto con un evento personalizado que toma un `CLLocation` como argumento:

```csharp
// event for the location changing
public event EventHandler<LocationUpdatedEventArgs>LocationUpdated = delegate { };
```

El siguiente paso es suscribirse a las actualizaciones de la ubicación desde la `CLLocationManager`y generar personalizado `LocationUpdated` cuando hay nuevos datos de ubicación disponible, pasa la ubicación como un argumento de evento. Para ello, cree una nueva clase **LocationUpdateEventArgs.cs**. Este código es accesible dentro de la aplicación principal y devuelve la ubicación del dispositivo cuando se provoca el evento:

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

1. Use el Diseñador de iOS para que se mostrará la información de ubicación de la pantalla. Haga doble clic en el **Main.storyboard** archivo para empezar.

    En el guión gráfico, arrastre varias etiquetas a la pantalla para que actúe como marcadores de posición para la información de ubicación. En este ejemplo, hay etiquetas para la latitud, longitud, altitud, cursos y velocidad.

    El diseño debería parecerse al siguiente:

    ![](location-walkthrough-images/image8.png "Un ejemplo de diseño de interfaz de usuario en el Diseñador de iOS")

1. En el panel de solución, haga doble clic en el `ViewController.cs` archivo y editarlo para crear una nueva instancia de la llamada y de LocationManager `StartLocationUpdates`en él.
  Cambie el código para el siguiente aspecto:

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

    Esto iniciará las actualizaciones de ubicación en el inicio de la aplicación, aunque se mostrará ningún dato.

1. Ahora que se reciben las actualizaciones de ubicación, actualice la pantalla con la información de ubicación. El siguiente método obtiene la ubicación de nuestros `LocationUpdated` evento y lo muestra en la interfaz de usuario:

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

Todavía es necesario suscribirse a la `LocationUpdated` eventos en el AppDelegate y llame al método nuevo para actualizar la interfaz de usuario. Agregue el código siguiente en `ViewDidLoad,` justo después de la `StartLocationUpdates` llamar:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // It is better to handle this with notifications, so that the UI updates
    // resume when the application re-enters the foreground!
    Manager.LocationUpdated += HandleLocationChanged;

}
```


Ahora, cuando se ejecuta la aplicación, debe tener un aspecto similar al siguiente:

[![](location-walkthrough-images/image5.png "Ejecutar una aplicación de ejemplo")](location-walkthrough-images/image5.png#lightbox)

## <a name="handling-active-and-background-states"></a>Los estados activo y en segundo plano de control

1. La aplicación es generar actualizaciones de ubicación mientras está en primer plano y activo. Para demostrar lo que sucede cuando la aplicación entra en segundo plano, invalidar el `AppDelegate` cambios de estado de los métodos que realizan el seguimiento de aplicación para que la aplicación escribe en la consola cuando realiza la transición entre el primer y el segundo plano:

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

    Agregue el código siguiente en el `LocationManager` continuamente imprimir ubicación actualizada son estando disponibles en segundo plano los datos a la salida de la aplicación, para comprobar la información de ubicación:

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

1. Hay un problema restante con el código: intentando actualizar la interfaz de usuario cuando se pasa a segundo plano la aplicación iOS causa se terminará lo. Cuando la aplicación entra en segundo plano, el código debe cancelar la suscripción a las actualizaciones de ubicación y detener la actualización de la interfaz de usuario.

    iOS nos proporciona notificaciones cuando la aplicación está a punto de transición a una aplicación diferente Estados. En este caso, nos podemos suscribirse a la `ObserveDidEnterBackground` notificación.

    El fragmento de código siguiente muestra cómo usar una notificación para informar a la vista de cuándo se debe detener las actualizaciones de la interfaz de usuario. Esto se ubicarán en `ViewDidLoad`:

    ```csharp
    UIApplication.Notifications.ObserveDidEnterBackground ((sender, args) => {
        Manager.LocationUpdated -= HandleLocationChanged;
    });
    ```

    Cuando se ejecuta la aplicación, el resultado tendrá un aspecto similar al siguiente:

    ![](location-walkthrough-images/image6.png "Ejemplo de la salida de la ubicación en la consola")

1. La aplicación imprime las actualizaciones de ubicación en la pantalla cuando se trabaja en primer plano y continúa imprimir los datos en la ventana de salida de la aplicación mientras se trabaja en segundo plano.

Sigue siendo de solo uno de los problemas pendiente: actualizaciones de la interfaz de usuario inicia la pantalla cuando se carga la aplicación por primera vez, pero no tiene ninguna manera de saber cuándo la aplicación ha entrado en volver a primer plano. Si la aplicación backgrounded se vuelve a incorporar a primer plano, no reanudación las actualizaciones de la interfaz de usuario.

Para solucionar este problema, anidar una llamada para iniciar las actualizaciones de la interfaz de usuario dentro de otra notificación, que se activará cuando la aplicación entra en estado activo:

```csharp
UIApplication.Notifications.ObserveDidBecomeActive ((sender, args) => {
  Manager.LocationUpdated += HandleLocationChanged;
});
```

Ahora la interfaz de usuario empezarán a actualizarse cuando la aplicación se inicia por primera vez y reanudar actualización siempre que la aplicación vuelve al primer plano.

En este tutorial, creamos una aplicación de iOS con buen comportamiento, tenga en cuenta el fondo que imprime los datos de ubicación en la pantalla y la ventana de salida de la aplicación.


## <a name="related-links"></a>Vínculos relacionados

- [Ubicación (parte 4) (ejemplo)](https://developer.xamarin.com/samples/monotouch/Location/)
- [Referencia de Framework ubicación principal](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CoreLocation_Framework/_index.html)
