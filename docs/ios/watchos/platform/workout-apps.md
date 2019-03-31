---
title: las aplicaciones de entrenamiento de Xamarin de watchOS
description: En este artículo se trata las mejoras de Apple ha realizado en las aplicaciones de entrenamiento de watchOS 3 y cómo implementarlos en Xamarin.
ms.prod: xamarin
ms.assetid: F1D19635-A738-43E5-9873-1FC1BA44EEDF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 02db7dce6ba38b6c1e943ff189ff69efb7cc1c08
ms.sourcegitcommit: 946ce514fd6575aa6b93ff24181e02a60b24b106
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/30/2019
ms.locfileid: "58677838"
---
# <a name="watchos-workout-apps-in-xamarin"></a>las aplicaciones de entrenamiento de Xamarin de watchOS

_En este artículo se trata las mejoras de Apple ha realizado en las aplicaciones de entrenamiento de watchOS 3 y cómo implementarlos en Xamarin._


Nuevo para watchOS 3, entrenamiento relacionados con las aplicaciones tienen la capacidad de ejecutar en segundo plano en el Apple Watch y obtener acceso a datos HealthKit. Su aplicación de iOS 10 basado primario también tiene la capacidad para iniciar la aplicación para watchOS 3 según sin intervención del usuario.

Los siguientes temas se tratarán en detalle:

## <a name="about-workout-apps"></a>Acerca de las aplicaciones de entrenamiento

Los usuarios de aplicaciones de idoneidad y entrenamiento pueden ser altamente dedicados, dedicar varias horas del día hacia sus objetivos de salud y estado. Como resultado, esperan que las aplicaciones con capacidad de respuesta, fácil de usar que recopilarán y mostrarán los datos y se integran perfectamente con el estado de Apple con precisión.

Una aplicación bien diseñada de fitness o entrenamiento ayuda a los usuarios a sus actividades para alcanzar sus objetivos de la idoneidad del gráfico. Mediante el uso de la Apple Watch, las aplicaciones de idoneidad y entrenamiento tienen acceso instantáneo a ritmo cardíaco, detección de grabación y la actividad de calorías.

[![](workout-apps-images/workout01.png "Ejemplo de aplicación de idoneidad y entrenamiento")](workout-apps-images/workout01.png#lightbox)

Nuevo para watchOS 3, _ejecutando en segundo plano_ ofrece entrenamiento relacionados con las aplicaciones la capacidad para ejecutar en segundo plano en el Apple Watch y obtener acceso a datos HealthKit.

Este documento se presentan la característica de ejecución en segundo plano, abarcan el ciclo de vida de aplicación de entrenamiento y muestran cómo una aplicación de entrenamiento puede contribuir a que el usuario _actividad anillos_ en el Apple Watch.

## <a name="about-workout-sessions"></a>Acerca de las sesiones de entrenamiento

El corazón de todas las aplicaciones de entrenamiento es un _sesión de entrenamiento_ (`HKWorkoutSession`) que el usuario puede iniciar y detener. La API de la sesión de entrenamiento es fácil de implementar y ofrece varias ventajas a una aplicación de ejercicio físico, como:

- Movimiento y calorías graban según el tipo de actividad de detección.
- Contribución automática para anillos de actividad del usuario.
- Mientras está en una sesión, la aplicación se mostrará automáticamente cada vez que el usuario activa el dispositivo (ya sea por generar su muñeca o interactuar con el Apple Watch).

## <a name="about-background-running"></a>Acerca de la ejecución en segundo plano

Como se indicó anteriormente, con watchOS 3 una aplicación de entrenamiento se puede establecer para ejecutar en segundo plano. Uso en segundo plano está ejecutando una aplicación de entrenamiento puede procesar datos de sensores de la Apple Watch mientras se ejecuta en segundo plano. Por ejemplo, una aplicación puede seguir supervisar la tasa de corazón del usuario, incluso aunque ya no se muestra en pantalla.

Ejecución en segundo plano también proporciona la capacidad de presentar los comentarios en directo al usuario en cualquier momento durante una sesión activa de ejercicio físico, como enviar una alerta hápticos para informar al usuario de su progreso actual.

Además, la ejecución en segundo plano permite que la aplicación actualizar rápidamente su interfaz de usuario, por lo que el usuario tiene los datos más recientes cuando rápidamente Eche un vistazo a su Apple Watch.

Para mantener un rendimiento alto en Apple Watch, una aplicación de inspección mediante la ejecución en segundo plano debe limitar la cantidad de trabajo en segundo plano para ahorrar batería. Si una aplicación está usando una cantidad excesiva de CPU mientras se encuentra en segundo plano, puede obtener suspendido para watchOS.

### <a name="enabling-background-running"></a>Lo que permite ejecutar en segundo plano

Para habilitar la ejecución en segundo plano, realice lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en la aplicación de iPhone de la extensión de inspección complementaria `Info.plist` archivo para abrirlo y editarlo.
2. Cambie a la **origen** vista: 

    [![](workout-apps-images/plist01.png "La vista del origen")](workout-apps-images/plist01.png#lightbox)
3. Agregar una nueva clave denominada `WKBackgroundModes` y establezca el **tipo** a `Array`: 

    [![](workout-apps-images/plist02.png "Agregar una nueva clave denominada WKBackgroundModes")](workout-apps-images/plist02.png#lightbox)
4. Agregar un nuevo elemento a la matriz con el **tipo** de `String` y un valor de `workout-processing`: 

    [![](workout-apps-images/plist03.png "Agregar un nuevo elemento a la matriz con el tipo de cadena y un valor de procesamiento de entrenamiento")](workout-apps-images/plist03.png#lightbox)
5. Guarde los cambios en el archivo.

## <a name="starting-a-workout-session"></a>Iniciar una sesión de entrenamiento

Hay tres pasos principales para iniciar una sesión de entrenamiento:

[![](workout-apps-images/workout02.png "Los tres pasos principales para iniciar una sesión de entrenamiento")](workout-apps-images/workout02.png#lightbox)

1. La aplicación debe solicitar autorización para acceder a los datos en HealthKit.
2. Crear un objeto de configuración de entrenamiento para el tipo de entrenamiento que se está iniciando.
3. Cree e inicie una sesión de entrenamiento mediante la configuración de ejercicio físico recién creada.

### <a name="requesting-authorization"></a>Solicitud de autorización

Antes de que una aplicación puede tener acceso a los datos del usuario HealthKit, debe solicitar y recibir autorización del usuario. Según la naturaleza de la aplicación de entrenamiento que tenga los siguientes tipos de solicitudes:

- Autorización para escribir datos:
    - Entrenamientos
- Autorización para leer los datos:
    - Energía quemada
    - distancia
    - Ritmo cardíaco  

Antes de que una aplicación puede solicitar autorización, debe configurarse para tener acceso a HealthKit.

Haga lo siguiente:

1. Haga doble clic en el archivo `Entitlements.plist` en el **Explorador de soluciones** para abrirlo para su edición.
2. Desplácese hacia abajo y compruebe **habilitar HealthKit**: 

    [![](workout-apps-images/auth01.png "Verificación Habilitar HealthKit")](workout-apps-images/auth01.png#lightbox)
3. Guarde los cambios en el archivo.
4. Siga las instrucciones de la [identificador de aplicación explícito y el perfil de aprovisionamiento](~/ios/platform/healthkit.md) y [asociar el Id. de aplicación y el aprovisionamiento de perfil con la aplicación de Xamarin.iOS](~/ios/platform/healthkit.md) secciones de la [Introducción a HealthKit](~/ios/platform/healthkit.md) artículo para aprovisionar correctamente la aplicación.
5. Por último, use las instrucciones de la [Kit de programación de mantenimiento](~/ios/platform/healthkit.md) y [solicitando permiso desde el usuario](~/ios/platform/healthkit.md) secciones de la [Introducción a HealthKit](~/ios/platform/healthkit.md) artículo a petición autorización para tener acceso a almacén de datos del usuario HealthKit.

### <a name="setting-the-workout-configuration"></a>Establecer la configuración de entrenamiento

Las sesiones de entrenamiento se crean mediante un objeto de configuración de entrenamiento (`HKWorkoutConfiguration`) que especifica el tipo de ejercicio físico (como `HKWorkoutActivityType.Running`) y la ubicación de entrenamiento (como `HKWorkoutSessionLocationType.Outdoor`):

```csharp
using HealthKit;
...

// Create a workout configuration
var configuration = new HKWorkoutConfiguration () {
    ActivityType = HKWorkoutActivityType.Running,
    LocationType = HKWorkoutSessionLocationType.Outdoor
};
```

### <a name="creating-a-workout-session-delegate"></a>Creación de un delegado de la sesión de entrenamiento 

Para controlar los eventos que se pueden producir durante una sesión de entrenamiento, la aplicación deberá crear una instancia de delegado de la sesión de entrenamiento. Agregue una nueva clase al proyecto y lo base fuera de la `HKWorkoutSessionDelegate` clase. El ejemplo de una ejecución al aire libre, podría ser similar al siguiente:

```csharp
using System;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
    public class OutdoorRunDelegate : HKWorkoutSessionDelegate
    {
        #region Computed Properties
        public HKHealthStore HealthStore { get; private set; }
        public HKWorkoutSession WorkoutSession { get; private set;}
        #endregion

        #region Constructors
        public OutdoorRunDelegate (HKHealthStore healthStore, HKWorkoutSession workoutSession)
        {
            // Initialize
            this.HealthStore = healthStore;
            this.WorkoutSession = workoutSession;

            // Attach this delegate to the session
            workoutSession.Delegate = this;
        }
        #endregion

        #region Override Methods
        public override void DidFail (HKWorkoutSession workoutSession, NSError error)
        {
            // Handle workout session failing
            RaiseFailed ();
        }

        public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
        {
            // Take action based on the change in state
            switch (toState) {
            case HKWorkoutSessionState.NotStarted:
                break;
            case HKWorkoutSessionState.Paused:
                RaisePaused ();
                break;
            case HKWorkoutSessionState.Running:
                RaiseRunning ();
                break;
            case HKWorkoutSessionState.Ended:
                RaiseEnded ();
                break;
            }

        }

        public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
        {
            base.DidGenerateEvent (workoutSession, @event);
        }
        #endregion

        #region Events
        public delegate void OutdoorRunEventDelegate ();

        public event OutdoorRunEventDelegate Failed;
        internal void RaiseFailed ()
        {
            if (this.Failed != null) this.Failed ();
        }


        public event OutdoorRunEventDelegate Paused;
        internal void RaisePaused ()
        {
            if (this.Paused != null) this.Paused ();
        }

        public event OutdoorRunEventDelegate Running;
        internal void RaiseRunning ()
        {
            if (this.Running != null) this.Running ();
        }

        public event OutdoorRunEventDelegate Ended;
        internal void RaiseEnded ()
        {
            if (this.Ended != null) this.Ended ();
        }
        #endregion
    }
}
```

Esta clase crea varios eventos que se generará como el estado de los cambios de la sesión de entrenamiento (`DidChangeToState`) y si se produce un error en la sesión de entrenamiento (`DidFail`). 

### <a name="creating-a-workout-session"></a>Creación de una sesión de entrenamiento

Mediante la configuración de ejercicio y entrenamiento sesión delegado creado anteriormente para crear una nueva sesión de entrenamiento e iniciarlo con el almacén del usuario predeterminado HealthKit:

```csharp
using HealthKit;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public OutdoorRunDelegate RunDelegate { get; set; }
#endregion
...

private void StartOutdoorRun ()
{
    // Create a workout configuration
    var configuration = new HKWorkoutConfiguration () {
        ActivityType = HKWorkoutActivityType.Running,
        LocationType = HKWorkoutSessionLocationType.Outdoor
    };

    // Create workout session
    // Start workout session
    NSError error = null;
    var workoutSession = new HKWorkoutSession (configuration, out error);

    // Successful?
    if (error != null) {
        // Report error to user and return
        return;
    }

    // Create workout session delegate and wire-up events
    RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

    RunDelegate.Failed += () => {
        // Handle the session failing
    };

    RunDelegate.Paused += () => {
        // Handle the session being paused
    };

    RunDelegate.Running += () => {
        // Handle the session running
    };

    RunDelegate.Ended += () => {
        // Handle the session ending
    };

    // Start session
    HealthStore.StartWorkoutSession (workoutSession);
}
```

Si la aplicación inicia esta sesión de entrenamiento y el usuario vuelve a cambiar a su esfera del reloj, se mostrará un pequeño icono verde "se está ejecutando man" por encima de la cara:

[![](workout-apps-images/workout03.png "Un diminuto verde ejecución man icono que aparece encima de la cara")](workout-apps-images/workout03.png#lightbox)

Si el usuario pulsa este icono, se le dirigirá volver a la aplicación.

## <a name="data-collection-and-control"></a>Recopilación de datos y control

Una vez que se configuran y se inicia una sesión de entrenamiento, necesitará la aplicación recopilar datos acerca de la sesión (por ejemplo, el ritmo cardíaco del usuario) y controlar el estado de la sesión:

[![](workout-apps-images/workout04.png "Diagrama de Control y la recopilación de datos")](workout-apps-images/workout04.png#lightbox)

1. **Observar ejemplos** -la aplicación debe recuperar la información de HealthKit que se actúa sobre ella y se mostrará al usuario.
2. **Observa los eventos** -la aplicación deberá responder a eventos generados por HealthKit o desde la interfaz de usuario de la aplicación (por ejemplo, el usuario pausa el entrenamiento).
3. **Escriba el estado de ejecución** -la sesión se ha iniciado y está ejecutando actualmente.
4. **Escriba el estado de pausa** -el usuario pausó la sesión actual de entrenamiento y volver a iniciarla en una fecha posterior. El usuario puede alternar entre los Estados de ejecución y en pausa varias veces en una única sesión de entrenamiento.
5. **Finalizar la sesión de entrenamiento** : en cualquier momento el usuario puede finalizar la sesión de entrenamiento o puede expirar y terminar por sí mismo si fuese un entrenamiento medido (por ejemplo, una ejecución de dos millas).

El último paso es guardar los resultados de la sesión de entrenamiento en el almacén de datos del usuario HealthKit.

### <a name="observing-healthkit-samples"></a>Observar ejemplos HealthKit

La aplicación deberá abrir un _consulta de objeto delimitador_ para cada uno de los datos HealthKit puntos que está interesado en, como el ritmo cardiaco o energía activa grabado. Para cada punto de datos que se observa, un controlador de actualización deberá crearse para capturar datos nuevos cuando se envían a la aplicación.

Desde estos puntos de datos, la aplicación puede acumular totales (por ejemplo, la distancia total de ejecución) y actualizar la interfaz de usuario de TI según sea necesario. Además, la aplicación puede notificar a los usuarios al alcanzar un objetivo específico o una mención especial, por ejemplo, completar el siguiente millas de una ejecución.

Eche un vistazo al código de ejemplo siguiente:

```csharp
private void ObserveHealthKitSamples ()
{
    // Get the starting date of the required samples
    var datePredicate = HKQuery.GetPredicateForSamples (WorkoutSession.StartDate, null, HKQueryOptions.StrictStartDate);

    // Get data from the local device
    var devices = new NSSet<HKDevice> (new HKDevice [] { HKDevice.LocalDevice });
    var devicePredicate = HKQuery.GetPredicateForObjectsFromDevices (devices);

    // Assemble compound predicate
    var queryPredicate = NSCompoundPredicate.CreateAndPredicate (new NSPredicate [] { datePredicate, devicePredicate });

    // Get ActiveEnergyBurned
    var queryActiveEnergyBurned = new HKAnchoredObjectQuery (HKQuantityType.Create (HKQuantityTypeIdentifier.ActiveEnergyBurned), queryPredicate, null, HKSampleQuery.NoLimit, (query, addedObjects, deletedObjects, newAnchor, error) => {
        // Valid?
        if (error == null) {
            // Yes, process all returned samples
            foreach (HKSample sample in addedObjects) {
                var quantitySample = sample as HKQuantitySample;
                ActiveEnergyBurned += quantitySample.Quantity.GetDoubleValue (HKUnit.Joule);
            }
            
            // Update User Interface
            ...
        }
    });

    // Start Query
    HealthStore.ExecuteQuery (queryActiveEnergyBurned);
                                          
}
```

Crea un predicado para establecer la fecha de inicio que se va a obtener datos para el uso de la `GetPredicateForSamples` método. Crea un conjunto de dispositivos para extraer información HealthKit desde mediante la `GetPredicateForObjectsFromDevices` método, en este caso, solo la local Apple Watch (`HKDevice.LocalDevice`). Los dos predicados se combinan en un predicado compuesta (`NSCompoundPredicate`) mediante el `CreateAndPredicate` método.

Un nuevo `HKAnchoredObjectQuery` se crea para el punto de datos deseado (en este caso `HKQuantityTypeIdentifier.ActiveEnergyBurned` para el punto de datos grabado de energía activo), se impone ningún límite en la cantidad de datos devueltos (`HKSampleQuery.NoLimit`) y se define un controlador de actualización para controlar que es de datos vuelva a la aplicación desde HealthKit. 

El controlador de actualización se llamará siempre que se entregan nuevos datos a la aplicación para el punto de datos determinado. Si se devuelve ningún error, la aplicación puede con seguridad leer los datos, realizar los cálculos necesarios y actualizar su interfaz de usuario según sea necesario.

El código establece bucles en todos los ejemplos (`HKSample`) devueltos en la `addedObjects` de matriz y los convierte en un ejemplo de cantidad (`HKQuantitySample`). A continuación, obtiene el valor doble de la muestra como un joule (`HKUnit.Joule`) y se acumula en el total acumulado de energía activa grabado para el entrenamiento y actualiza la interfaz de usuario.

### <a name="achieved-goal-notification"></a>Notificación de objetivo alcanzado

Como se mencionó anteriormente, cuando el usuario logra un objetivo en la aplicación de entrenamiento (por ejemplo, para completar la primera millas de una ejecución), puede enviar comentarios hápticos para el usuario mediante el motor Taptic. La aplicación también debe actualizar su interfaz de usuario en este momento, ya que el usuario más probable es que aumente su muñeca para ver el evento que se le solicite los comentarios.

Para reproducir los comentarios hápticos, use el siguiente código:

```csharp
// Play haptic feedback
WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);
```

### <a name="observing-events"></a>Observa los eventos

Los eventos son las marcas de tiempo que la aplicación puede usar para resaltar ciertos puntos durante el entrenamiento del usuario. Algunos eventos se creada directamente por la aplicación y se guardan en el entrenamiento y algunos eventos se crearán automáticamente por HealthKit.

Para observar los eventos que se crean mediante HealthKit, la aplicación invalidarán el `DidGenerateEvent` método de la `HKWorkoutSessionDelegate`:

```csharp
using System.Collections.Generic;
...

public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
    base.DidGenerateEvent (workoutSession, @event);
    
    // Save HealthKit generated event
    WorkoutEvents.Add (@event);
    
    // Take action based on the type of event
    switch (@event.Type) {
    case HKWorkoutEventType.Lap:
        break;
    case HKWorkoutEventType.Marker:
        break;
    case HKWorkoutEventType.MotionPaused:
        break;
    case HKWorkoutEventType.MotionResumed:
        break;
    case HKWorkoutEventType.Pause:
        break;
    case HKWorkoutEventType.Resume:
        break;
    }
}
```

Apple ha agregado los siguientes nuevos tipos de eventos en watchOS 3:

- `HKWorkoutEventType.Lap` -Son para los eventos que dividir el entrenamiento en partes de la misma distancia. Por ejemplo, para marcar una vuelta alrededor de una pista mientras se está ejecutando.
- `HKWorkoutEventType.Marker` -Son arbitrarios puntos de interés en el entrenamiento. Por ejemplo, alcanzar un punto específico en la ruta de una ejecución al aire libre.

Estos nuevos tipos se pueden creados por la aplicación y almacenan en el ejercicio físico para su uso posterior en la creación de gráficos y estadísticas.

Para crear un evento de marcador, realice lo siguiente:

```csharp
using System.Collections.Generic;
...

public float MilesRun { get; set; }
public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

public void ReachedNextMile ()
{
    // Create and save marker event
    var markerEvent = HKWorkoutEvent.Create (HKWorkoutEventType.Marker, NSDate.Now);
    WorkoutEvents.Add (markerEvent);

    // Notify user
    NotifyUserOfReachedMileGoal (++MilesRun);
}
```

Este código crea una nueva instancia de un evento de marcador (`HKWorkoutEvent`) y lo guarda en una colección de eventos (lo que más adelante se escribirán en la sesión de entrenamiento) privada y notifica al usuario sobre el evento a través de haptics.

### <a name="pausing-and-resuming-workouts"></a>Pausar y reanudar entrenamientos

En cualquier momento en una sesión de entrenamiento, el usuario puede pausar el entrenamiento temporalmente y reanudarla más adelante. Por ejemplo, podría hacer una pausa la ejecución de un interior para tomar una llamada importante y reanudar la ejecución una vez completada la llamada.

Interfaz de usuario de la aplicación debe proporcionar una manera de pausar y reanudar el entrenamiento (mediante una llamada a HealthKit) para que el Apple Watch puede ahorrar espacio de alimentación y de datos mientras el usuario ha suspendido su actividad. Además, la aplicación debe ignorar los nuevos puntos de datos que pueden recibirse cuando la sesión de entrenamiento está en pausa.

HealthKit responderá para pausar y reanudar las llamadas mediante la generación de eventos de pausar y reanudar. Mientras está en pausa la sesión de entrenamiento, no hay nuevos eventos o datos se enviarán a la aplicación por HealthKit hasta que se reanuda la sesión.

Para pausar y reanudar una sesión de entrenamiento, use el código siguiente:

```csharp
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public HKWorkoutSession WorkoutSession { get; set;}
...

public void PauseWorkout ()
{
    // Pause the current workout
    HealthStore.PauseWorkoutSession (WorkoutSession);
}

public void ResumeWorkout ()
{
    // Pause the current workout
    HealthStore.ResumeWorkoutSession (WorkoutSession);
}
```

Se pueden controlar los eventos de pausar y reanudar que se generará desde HealthKit invalidando el `DidGenerateEvent` método de la `HKWorkoutSessionDelegate`:

```csharp
public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
    base.DidGenerateEvent (workoutSession, @event);

    // Take action based on the type of event
    switch (@event.Type) {
    case HKWorkoutEventType.Pause:
        break;
    case HKWorkoutEventType.Resume:
        break;
    }
}
```

### <a name="motion-events"></a>Eventos de movimiento

También nuevo para watchOS 3, son el movimiento en pausa (`HKWorkoutEventType.MotionPaused`) y se reanuda el movimiento (`HKWorkoutEventType.MotionResumed`) eventos. Estos eventos se generan automáticamente por HealthKit durante un ejercicio de ejecución cuando el usuario inicia y deja de moverse.

Cuando la aplicación recibe un evento de movimiento en pausa, debe detener la recopilación de datos hasta que el usuario reanuda el movimiento y se recibe el evento se reanuda el movimiento. Aplicación no debe pausar la sesión de entrenamiento en respuesta a un evento de movimiento en pausa.

> [!IMPORTANT]
> Solo se admiten los eventos de movimiento en pausa y reanudación de movimiento para el tipo de actividad RunningWorkout (`HKWorkoutActivityType.Running`).

De nuevo, se pueden controlar estos eventos invalidando el `DidGenerateEvent` método de la `HKWorkoutSessionDelegate`:

```csharp
public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
    base.DidGenerateEvent (workoutSession, @event);
    
    // Take action based on the type of event
    switch (@event.Type) {
    case HKWorkoutEventType.MotionPaused:
        break;
    case HKWorkoutEventType.MotionResumed:
        break;
    }
}

```

## <a name="ending-and-saving-the-workout-session"></a>Finalizar y guardar la sesión de entrenamiento

Cuando el usuario ha completado su entrenamiento, la aplicación deberá finalizar la sesión actual de entrenamiento y guárdelo en la base de datos HealthKit. Entrenamientos guardados en HealthKit se mostrará automáticamente en la lista de actividades de entrenamiento.

Nuevo en iOS 10, esto incluye la lista de lista de actividades de entrenamiento en iPhone también del usuario. Por lo que aunque el Apple Watch no esté próxima, aparecerá el entrenamiento en el teléfono.

Ejercicios físicos que se incluyen las muestras de energía actualizarán el anillo de mover del usuario en la aplicación de las actividades para que aplicaciones de terceros 3rd ahora pueden contribuir a los objetivos de cambio diarios del usuario.

Los pasos siguientes son necesarios para finalizar y guardar una sesión de entrenamiento:

[![](workout-apps-images/workout05.png "Finalizar y guardar el diagrama de la sesión de entrenamiento")](workout-apps-images/workout05.png#lightbox)

1. En primer lugar, la aplicación debe finalizar la sesión de entrenamiento.
2. La sesión de entrenamiento se guarda en HealthKit.
3. Agregar todas las muestras (por ejemplo, energía quemada o distancia) a la sesión de entrenamiento guardado.

### <a name="ending-the-session"></a>Finalizar la sesión

Para finalizar la sesión de entrenamiento, llame a la `EndWorkoutSession` método de la `HKHealthStore` pasando el `HKWorkoutSession`:

```csharp
public HKHealthStore HealthStore { get; private set; }
public HKWorkoutSession WorkoutSession { get; private set;}
...

public void EndOutdoorRun ()
{
    // End the current workout session
    HealthStore.EndWorkoutSession (WorkoutSession);
}
```

Esto restablecerá los sensores de dispositivos a su modo normal. Cuando termine de HealthKit finalizar el entrenamiento, recibirá una devolución de llamada la `DidChangeToState` método de la `HKWorkoutSessionDelegate`:

```csharp
public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
{
    // Take action based on the change in state
    switch (toState) {
    ...
    case HKWorkoutSessionState.Ended:
        StopObservingHealthKitSamples ();
        RaiseEnded ();
        break;
    }

}
```

### <a name="saving-the-session"></a>Guardando la sesión

Una vez que la aplicación ha finalizado la sesión de entrenamiento, deberá crear un entrenamiento (`HKWorkout`) y guárdelo (junto con un tipo de eventos) en el almacén de datos HealthKit (`HKHealthStore`):

```csharp
public HKHealthStore HealthStore { get; private set; }
public HKWorkoutSession WorkoutSession { get; private set;}
public float MilesRun { get; set; }
public double ActiveEnergyBurned { get; set;}
public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

private void SaveWorkoutSession ()
{
    // Build required workout quantities 
    var energyBurned = HKQuantity.FromQuantity (HKUnit.Joule, ActiveEnergyBurned);
    var distance = HKQuantity.FromQuantity (HKUnit.Mile, MilesRun);

    // Create any required metadata
    var metadata = new NSMutableDictionary ();
    metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));

    // Create workout
    var workout = HKWorkout.Create (HKWorkoutActivityType.Running, 
                                    WorkoutSession.StartDate, 
                                    NSDate.Now, 
                                    WorkoutEvents.ToArray (), 
                                    energyBurned, 
                                    distance, 
                                    metadata);

    // Save to HealthKit
    HealthStore.SaveObject (workout, (successful, error) => {
        // Handle any errors
        if (error == null) {
            // Was the save successful
            if (successful) {

            }
        } else {
            // Report error
        }
    });

}
```

Este código crea el requieren la cantidad total de la energía quemada y distancia para el entrenamiento como `HKQuantity` objetos. Se crea un diccionario de metadatos que definen el entrenamiento y se especifica la ubicación del ejercicio:

```csharp
metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));
```

Un nuevo `HKWorkout` se crea el objeto con el mismo `HKWorkoutActivityType` como el `HKWorkoutSession`, total de las fechas inicial y finales, la lista de eventos (que se acumulan en las secciones anteriores), la energía quemada, distancia y diccionario de metadatos. Este objeto se guarda en el estado de Store y controlan los errores.  

### <a name="adding-samples"></a>Incorporación de ejemplos

Cuando la aplicación guarda un conjunto de ejemplos para un entrenamiento, HealthKit genera una conexión entre los ejemplos y el entrenamiento de sí mismo para que la aplicación pueda consultar HealthKit en una fecha posterior para todos los ejemplos relacionados con un entrenamiento determinado. Con esta información, la aplicación puede generar gráficos de los datos de entrenamiento y representarlas en una escala de tiempo de entrenamiento.

Para que una aplicación contribuir a mover anillo la aplicación de la actividad, debe incluir ejemplos de energía con el entrenamiento guardado. Además, los totales de distancia y de energía deben coincidir con la suma de todas las muestras que la aplicación se asocia con un entrenamiento guardado.

Para agregar ejemplos para un entrenamiento guardada, haga lo siguiente:

```csharp
using System.Collections.Generic;
using WatchKit;
using HealthKit;
...

public HKHealthStore HealthStore { get; private set; }
public List<HKSample> WorkoutSamples { get; set; } = new List<HKSample> ();
...


private void SaveWorkoutSamples (HKWorkout workout)
{
    // Add samples to saved workout
    HealthStore.AddSamples (WorkoutSamples.ToArray (), workout, (success, error) => {
        // Handle any errors
        if (error == null) {
            // Was the save successful
            if (success) {

            }
        } else {
            // Report error
        }
    });
}
```

Opcionalmente, la aplicación puede calcular y crear un subconjunto más pequeño de ejemplos o de una muestra de mega (abarcan todo el ejercicio físico) que, a continuación, se asocia con el entrenamiento guardado.

## <a name="workouts-and-ios-10"></a>Entrenamientos y iOS 10

Todas las aplicaciones de entrenamiento watchOS 3 tiene una aplicación de entrenamiento en función de iOS 10 primario y, lo que es nuevo en iOS 10, esta aplicación de iOS se puede usar para iniciar un entrenamiento que se coloque el Apple Watch en el modo de entrenamiento (sin intervención del usuario) y ejecutar la aplicación watchOS en el modo de ejecución de fondo (consulte [sobre fondo ejecutando](#about-background-running) anteriormente para obtener más detalles).

Mientras se ejecuta la aplicación para watchOS, puede usar WatchConnectivity para mensajería y comunicación con la aplicación para iOS principal.

Eche un vistazo a cómo funciona este proceso:

[![](workout-apps-images/workout06.png "diagrama de comunicación de Apple Watch y iPhone")](workout-apps-images/workout06.png#lightbox)

1. La aplicación de iPhone crea un `HKWorkoutConfiguration` de objeto y establece el tipo de entrenamiento y la ubicación.
2. La `HKWorkoutConfiguration` objeto se envía a la versión de Apple Watch de la aplicación y, si aún no se está ejecutando, se inicia el sistema.
3. Con el pasado en la configuración de entrenamiento, la aplicación para watchOS 3 inicia una nueva sesión de entrenamiento (`HKWorkoutSession`).

> [!IMPORTANT]
> Para la aplicación de iPhone primaria iniciar un entrenamiento en el Apple Watch, la aplicación para watchOS 3 debe tener en segundo plano ejecuta habilitado. Consulte [lo que permite ejecutar en segundo plano](#enabling-background-running) anteriormente para obtener más detalles.

Este proceso es muy similar al proceso de iniciar una sesión de entrenamiento en la aplicación para watchOS 3 directamente. En el iPhone, use el código siguiente:

```csharp
using System;
using HealthKit;
using WatchConnectivity;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set; } = new HKHealthStore ();
public WCSession ConnectivitySession { get; set; } = WCSession.DefaultSession;
#endregion
...

private void StartOutdoorRun ()
{
    // Can the app communicate with the watchOS version of the app?
    if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
        // Create a workout configuration
        var configuration = new HKWorkoutConfiguration () {
            ActivityType = HKWorkoutActivityType.Running,
            LocationType = HKWorkoutSessionLocationType.Outdoor
        };

        // Start watch app
        HealthStore.StartWatchApp (configuration, (success, error) => {
            // Handle any errors
            if (error == null) {
                // Was the save successful
                if (success) {
                    ...
                }
            } else {
                // Report error
                ...
            }
        });
    }
}
```

Este código garantiza que está instalada la versión de watchOS de la aplicación y la versión de iPhone puede conectarse a él en primer lugar:

```csharp
if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
    ...
}
```

A continuación, crea un `HKWorkoutConfiguration` como de costumbre y usa el `StartWatchApp` método de la `HKHealthStore` para enviarlo a la de Apple Watch e iniciar la sesión de entrenamiento y de la aplicación.

Y en la aplicación del reloj del sistema operativo, use el siguiente código en el `WKExtensionDelegate`:

```csharp
using WatchKit;
using HealthKit;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public OutdoorRunDelegate RunDelegate { get; set; }
#endregion
...


public override void HandleWorkoutConfiguration (HKWorkoutConfiguration workoutConfiguration)
{
    // Create workout session
    // Start workout session
    NSError error = null;
    var workoutSession = new HKWorkoutSession (workoutConfiguration, out error);

    // Successful?
    if (error != null) {
        // Report error to user and return
        return;
    }

    // Create workout session delegate and wire-up events
    RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

    RunDelegate.Failed += () => {
        // Handle the session failing
    };

    RunDelegate.Paused += () => {
        // Handle the session being paused
    };

    RunDelegate.Running += () => {
        // Handle the session running
    };

    RunDelegate.Ended += () => {
        // Handle the session ending
    };

    // Start session
    HealthStore.StartWorkoutSession (workoutSession);
}
```

Toma la `HKWorkoutConfiguration` y crea un nuevo `HKWorkoutSession` y adjunta una instancia de personalizado `HKWorkoutSessionDelegate`. Se inicia la sesión de entrenamiento frente a HealthKit Health Store del usuario.

## <a name="bringing-all-the-pieces-together"></a>Reúne todas las piezas

Realizar toda la información presentada en este documento, una aplicación de entrenamiento en función de watchOS 3 y su aplicación en función de entrenamiento para iOS 10 principal podrían incluir las siguientes partes:

1. **iOS 10 `ViewController.cs`**  -controla el inicio de una sesión de conectividad de inspección y un entrenamiento en el Apple Watch.
2. **watchOS 3 `ExtensionDelegate.cs`**  -controla la versión de watchOS 3 de la aplicación de entrenamiento.
3. **watchOS 3 `OutdoorRunDelegate.cs`**  -personalizada `HKWorkoutSessionDelegate` para controlar los eventos para el entrenamiento.

> [!IMPORTANT]
> El código que se muestra en las secciones siguientes solo incluye las partes necesarias para implementar las características nuevas y mejoradas proporcionadas a las aplicaciones de entrenamiento en watchOS 3. No se incluye todo el código auxiliar y el código para presentar y actualizar la interfaz de usuario, pero se pueden crear fácilmente siguiendo otra documentación para watchOS.<p/>



### <a name="viewcontrollercs"></a>ViewController.cs

El `ViewController.cs` archivo en la versión de iOS 10 primario de la aplicación workout incluiría el código siguiente:

```csharp
using System;
using HealthKit;
using UIKit;
using WatchConnectivity;

namespace MonkeyWorkout
{
    public partial class ViewController : UIViewController
    {
        #region Computed Properties
        public HKHealthStore HealthStore { get; set; } = new HKHealthStore ();
        public WCSession ConnectivitySession { get; set; } = WCSession.DefaultSession;
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Private Methods
        private void InitializeWatchConnectivity ()
        {
            // Is Watch Connectivity supported?
            if (!WCSession.IsSupported) {
                // No, abort
                return;
            }

            // Is the session already active?
            if (ConnectivitySession.ActivationState != WCSessionActivationState.Activated) {
                // No, start session
                ConnectivitySession.ActivateSession ();
            }
        }

        private void StartOutdoorRun ()
        {
            // Can the app communicate with the watchOS version of the app?
            if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
                // Create a workout configuration
                var configuration = new HKWorkoutConfiguration () {
                    ActivityType = HKWorkoutActivityType.Running,
                    LocationType = HKWorkoutSessionLocationType.Outdoor
                };

                // Start watch app
                HealthStore.StartWatchApp (configuration, (success, error) => {
                    // Handle any errors
                    if (error == null) {
                        // Was the save successful
                        if (success) {
                            ...
                        }
                    } else {
                        // Report error
                        ...
                    }
                });
            }
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Start Watch Connectivity
            InitializeWatchConnectivity ();
        }
        #endregion
    }
}
```

### <a name="extensiondelegatecs"></a>ExtensionDelegate.cs

El `ExtensionDelegate.cs` archivo en la versión de watchOS 3 de la aplicación workout incluiría el código siguiente:

```csharp
using System;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
    public class ExtensionDelegate : WKExtensionDelegate
    {
        #region Computed Properties
        public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
        public OutdoorRunDelegate RunDelegate { get; set; }
        #endregion

        #region Constructors
        public ExtensionDelegate ()
        {
            
        }
        #endregion

        #region Private Methods
        private void StartWorkoutSession (HKWorkoutConfiguration workoutConfiguration)
        {
            // Create workout session
            // Start workout session
            NSError error = null;
            var workoutSession = new HKWorkoutSession (workoutConfiguration, out error);

            // Successful?
            if (error != null) {
                // Report error to user and return
                return;
            }

            // Create workout session delegate and wire-up events
            RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

            RunDelegate.Failed += () => {
                // Handle the session failing
                ...
            };

            RunDelegate.Paused += () => {
                // Handle the session being paused
                ...
            };

            RunDelegate.Running += () => {
                // Handle the session running
                ...
            };

            RunDelegate.Ended += () => {
                // Handle the session ending
                ...
            };
            
            RunDelegate.ReachedMileGoal += (miles) => {
                // Handle the reaching a session goal
                ...
            };

            RunDelegate.HealthKitSamplesUpdated += () => {
                // Update UI as required
                ...
            };

            // Start session
            HealthStore.StartWorkoutSession (workoutSession);
        }

        private void StartOutdoorRun ()
        {
            // Create a workout configuration
            var workoutConfiguration = new HKWorkoutConfiguration () {
                ActivityType = HKWorkoutActivityType.Running,
                LocationType = HKWorkoutSessionLocationType.Outdoor
            };

            // Start the session
            StartWorkoutSession (workoutConfiguration);
        }
        #endregion

        #region Override Methods
        public override void HandleWorkoutConfiguration (HKWorkoutConfiguration workoutConfiguration)
        {
            // Start the session
            StartWorkoutSession (workoutConfiguration);
        }
        #endregion
    }
}
```

### <a name="outdoorrundelegatecs"></a>OutdoorRunDelegate.cs

El `OutdoorRunDelegate.cs` archivo en la versión de watchOS 3 de la aplicación workout incluiría el código siguiente:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
    public class OutdoorRunDelegate : HKWorkoutSessionDelegate
    {
        #region Private Variables
        private HKAnchoredObjectQuery QueryActiveEnergyBurned;
        #endregion

        #region Computed Properties
        public HKHealthStore HealthStore { get; private set; }
        public HKWorkoutSession WorkoutSession { get; private set;}
        public float MilesRun { get; set; }
        public double ActiveEnergyBurned { get; set;}
        public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
        public List<HKSample> WorkoutSamples { get; set; } = new List<HKSample> ();
        #endregion

        #region Constructors
        public OutdoorRunDelegate (HKHealthStore healthStore, HKWorkoutSession workoutSession)
        {
            // Initialize
            this.HealthStore = healthStore;
            this.WorkoutSession = workoutSession;

            // Attach this delegate to the session
            workoutSession.Delegate = this;

        }
        #endregion

        #region Private Methods
        private void ObserveHealthKitSamples ()
        {
            // Get the starting date of the required samples
            var datePredicate = HKQuery.GetPredicateForSamples (WorkoutSession.StartDate, null, HKQueryOptions.StrictStartDate);

            // Get data from the local device
            var devices = new NSSet<HKDevice> (new HKDevice [] { HKDevice.LocalDevice });
            var devicePredicate = HKQuery.GetPredicateForObjectsFromDevices (devices);

            // Assemble compound predicate
            var queryPredicate = NSCompoundPredicate.CreateAndPredicate (new NSPredicate [] { datePredicate, devicePredicate });

            // Get ActiveEnergyBurned
            QueryActiveEnergyBurned = new HKAnchoredObjectQuery (HKQuantityType.Create (HKQuantityTypeIdentifier.ActiveEnergyBurned), queryPredicate, null, HKSampleQuery.NoLimit, (query, addedObjects, deletedObjects, newAnchor, error) => {
                // Valid?
                if (error == null) {
                    // Yes, process all returned samples
                    foreach (HKSample sample in addedObjects) {
                        // Accumulate totals
                        var quantitySample = sample as HKQuantitySample;
                        ActiveEnergyBurned += quantitySample.Quantity.GetDoubleValue (HKUnit.Joule);

                        // Save samples
                        WorkoutSamples.Add (sample);
                    }

                    // Inform caller
                    RaiseHealthKitSamplesUpdated ();
                }
            });

            // Start Query
            HealthStore.ExecuteQuery (QueryActiveEnergyBurned);
                                                  
        }

        private void StopObservingHealthKitSamples ()
        {
            // Stop query
            HealthStore.StopQuery (QueryActiveEnergyBurned);
        }

        private void ResumeObservingHealthkitSamples ()
        {
            // Resume current queries 
            HealthStore.ExecuteQuery (QueryActiveEnergyBurned);
        }

        private void NotifyUserOfReachedMileGoal (float miles)
        {
            // Play haptic feedback
            WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);

            // Raise event
            RaiseReachedMileGoal (miles);
        }

        private void SaveWorkoutSession ()
        {
            // Build required workout quantities
            var energyBurned = HKQuantity.FromQuantity (HKUnit.Joule, ActiveEnergyBurned);
            var distance = HKQuantity.FromQuantity (HKUnit.Mile, MilesRun);

            // Create any required metadata
            var metadata = new NSMutableDictionary ();
            metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));

            // Create workout
            var workout = HKWorkout.Create (HKWorkoutActivityType.Running, 
                                            WorkoutSession.StartDate, 
                                            NSDate.Now, 
                                            WorkoutEvents.ToArray (), 
                                            energyBurned, 
                                            distance, 
                                            metadata);

            // Save to HealthKit
            HealthStore.SaveObject (workout, (successful, error) => {
                // Handle any errors
                if (error == null) {
                    // Was the save successful
                    if (successful) {
                        // Add samples to workout
                        SaveWorkoutSamples (workout);
                    }
                } else {
                    // Report error
                    ...
                }
            });

        }

        private void SaveWorkoutSamples (HKWorkout workout)
        {
            // Add samples to saved workout
            HealthStore.AddSamples (WorkoutSamples.ToArray (), workout, (success, error) => {
                // Handle any errors
                if (error == null) {
                    // Was the save successful
                    if (success) {
                        ...
                    }
                } else {
                    // Report error
                    ...
                }
            });
        }
        #endregion

        #region Public Methods
        public void PauseWorkout ()
        {
            // Pause the current workout
            HealthStore.PauseWorkoutSession (WorkoutSession);
        }

        public void ResumeWorkout ()
        {
            // Pause the current workout
            HealthStore.ResumeWorkoutSession (WorkoutSession);
        }

        public void ReachedNextMile ()
        {
            // Create and save marker event
            var markerEvent = HKWorkoutEvent.Create (HKWorkoutEventType.Marker, NSDate.Now);
            WorkoutEvents.Add (markerEvent);

            // Notify user
            NotifyUserOfReachedMileGoal (++MilesRun);
        }

        public void EndOutdoorRun ()
        {
            // End the current workout session
            HealthStore.EndWorkoutSession (WorkoutSession);
        }
        #endregion

        #region Override Methods
        public override void DidFail (HKWorkoutSession workoutSession, NSError error)
        {
            // Handle workout session failing
            RaiseFailed ();
        }

        public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
        {
            // Take action based on the change in state
            switch (toState) {
            case HKWorkoutSessionState.NotStarted:
                break;
            case HKWorkoutSessionState.Paused:
                StopObservingHealthKitSamples ();
                RaisePaused ();
                break;
            case HKWorkoutSessionState.Running:
                if (fromState == HKWorkoutSessionState.Paused) {
                    ResumeObservingHealthkitSamples ();
                } else {
                    ObserveHealthKitSamples ();
                }
                RaiseRunning ();
                break;
            case HKWorkoutSessionState.Ended:
                StopObservingHealthKitSamples ();
                SaveWorkoutSession ();
                RaiseEnded ();
                break;
            }

        }

        public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
        {
            base.DidGenerateEvent (workoutSession, @event);

            // Save HealthKit generated event
            WorkoutEvents.Add (@event);

            // Take action based on the type of event
            switch (@event.Type) {
            case HKWorkoutEventType.Lap:
                ...
                break;
            case HKWorkoutEventType.Marker:
                ...
                break;
            case HKWorkoutEventType.MotionPaused:
                ...
                break;
            case HKWorkoutEventType.MotionResumed:
                ...
                break;
            case HKWorkoutEventType.Pause:
                ...
                break;
            case HKWorkoutEventType.Resume:
                ...
                break;
            }
        }
        #endregion

        #region Events
        public delegate void OutdoorRunEventDelegate ();
        public delegate void OutdoorRunMileGoalDelegate (float miles);

        public event OutdoorRunEventDelegate Failed;
        internal void RaiseFailed ()
        {
            if (this.Failed != null) this.Failed ();
        }


        public event OutdoorRunEventDelegate Paused;
        internal void RaisePaused ()
        {
            if (this.Paused != null) this.Paused ();
        }

        public event OutdoorRunEventDelegate Running;
        internal void RaiseRunning ()
        {
            if (this.Running != null) this.Running ();
        }

        public event OutdoorRunEventDelegate Ended;
        internal void RaiseEnded ()
        {
            if (this.Ended != null) this.Ended ();
        }

        public event OutdoorRunMileGoalDelegate ReachedMileGoal;
        internal void RaiseReachedMileGoal (float miles)
        {
            if (this.ReachedMileGoal != null) this.ReachedMileGoal (miles);
        }

        public event OutdoorRunEventDelegate HealthKitSamplesUpdated;
        internal void RaiseHealthKitSamplesUpdated ()
        {
            if (this.HealthKitSamplesUpdated != null) this.HealthKitSamplesUpdated ();
        }
        #endregion
    }
}
```

## <a name="best-practices"></a>Procedimientos recomendados

Apple sugiere el uso de los siguientes procedimientos recomendados al diseñar e implementar aplicaciones de entrenamiento en iOS 10 y watchOS 3:

- Asegúrese de que la aplicación de watchOS 3 entrenamiento todavía es funcional incluso cuando no se puede conectar a iPhone y la versión de iOS 10 de la aplicación.
- Usar HealthKit distancia al GPS no está disponible, ya que es capaz de generar los ejemplos de distancia sin GPS.
- Permite al usuario iniciar el ejercicio físico desde el Apple Watch o iPhone.
- Permitir que la aplicación mostrar los ejercicios de otros orígenes (por ejemplo, otras aplicaciones de terceros 3rd) en sus vistas de datos históricos.
- Asegúrese de que la aplicación realiza no mostrar eliminado entrenamientos en los datos históricos.

## <a name="summary"></a>Resumen

Este artículo ha tratado las mejoras de Apple ha realizado en las aplicaciones de entrenamiento de watchOS 3 y cómo implementarlos en Xamarin.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Introducción a HealthKit](~/ios/platform/healthkit.md)
