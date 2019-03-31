---
title: CallKit en Xamarin.iOS
description: Este artículo trata sobre la nueva API CallKit que publicó en iOS 10 y cómo implementarla en las aplicaciones de Xamarin.iOS VOIP de Apple.
ms.prod: xamarin
ms.assetid: 738A142D-FFD2-4738-B3ED-57C273179848
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: 6db9ff0085c17f07d07a7591f5d735793bfbc5f9
ms.sourcegitcommit: 946ce514fd6575aa6b93ff24181e02a60b24b106
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/30/2019
ms.locfileid: "58678046"
---
# <a name="callkit-in-xamarinios"></a>CallKit en Xamarin.iOS

La nueva API de CallKit en iOS 10 proporciona una manera para las aplicaciones VOIP integrar con la interfaz de usuario de iPhone y proporcionar una interfaz conocida y experiencia para el usuario final. Con esta API usuarios pueden ver e interactuar con llamadas VOIP desde la pantalla de bloqueo del dispositivo iOS y administrar contactos con la aplicación de teléfono **favoritos** y **recientes** vistas.

## <a name="about-callkit"></a>Acerca de CallKit

Según Apple, CallKit es un nuevo marco que se elevará 3rd aplicaciones de voz sobre IP (VOIP) de terceros para un día 1 de terceros en iOS 10. La API CallKit permite que las aplicaciones VOIP integrar con la interfaz de usuario de iPhone y proporcionar una interfaz conocida y experiencia para el usuario final. Al igual que la aplicación de teléfono integrada, un usuario puede ver e interactuar con llamadas VOIP desde la pantalla de bloqueo del dispositivo iOS y administrar contactos con la aplicación de teléfono **favoritos** y **recientes** vistas.

Además, la API de CallKit proporciona la capacidad para crear extensiones de aplicación que se puede asociar un número de teléfono con un nombre (identificador de llamada) o indicar el sistema cuando un número debe ser bloqueados (llamar a bloqueo).

### <a name="the-existing-voip-app-experience"></a>La experiencia de aplicación existente de VOIP

Antes de tratar la nueva API CallKit y sus capacidades, eche un vistazo a la experiencia del usuario actual con un 3rd entidad aplicación VOIP en iOS 9 (y menor) usando una aplicación VOIP ficticia denominada MonkeyCall. MonkeyCall es una aplicación sencilla que permite al usuario enviar y recibir llamadas VOIP con las API de iOS existentes.

Actualmente, si el usuario recibe una llamada entrante en MonkeyCall y se bloquea su iPhone, la notificación recibida en la pantalla de bloqueo es indistinguible de cualquier otro tipo de notificación (los de los mensajes como o aplicaciones de correo por ejemplo).

Si el usuario desea responder a la llamada, tendría deslice la notificación MonkeyCall para abrir la aplicación y escriba su código de acceso (o Touch Id. de usuario) para desbloquear el teléfono antes de que podrían aceptar la llamada e iniciar la conversación.

La experiencia es complicada si el teléfono está desbloqueado. De nuevo, la llamada entrante de MonkeyCall se muestra como un banner de notificación estándar que se desliza en desde la parte superior de la pantalla. Puesto que la notificación es temporal, pueden fácilmente perder por el usuario obligarles a abrir el centro de notificaciones y buscar la notificación específica para responder, a continuación, llame a o busque e inicie la aplicación MonkeyCall de manualmente.

### <a name="the-callkit-voip-app-experience"></a>La experiencia de aplicación CallKit VOIP

Mediante la implementación de las nuevas APIs CallKit en la aplicación MonkeyCall, se puede mejorar en gran medida la experiencia del usuario con una llamada entrante de VOIP en iOS 10. Tome como ejemplo el usuario que recibe una llamada VOIP cuando su teléfono está bloqueado desde arriba. Implementando CallKit, la llamada aparecerá en pantalla de bloqueo del iPhone, tal como lo haría si se recibió la llamada de la aplicación de teléfono integrada, con la pantalla completa, interfaz de usuario nativa y la funcionalidad de pasar el dedo para respuesta estándar.

Nuevamente, si el iPhone se desbloquea cuando se recibe una llamada MonkeyCall VOIP, la misma pantalla completa, la interfaz de usuario nativa y la funcionalidad de respuesta de pasar el dedo y pulse para rechazar estándar de integrados se presenta la aplicación de teléfono y MonkeyCall tiene la opción de reproducir un tono personalizado .

CallKit proporciona funcionalidad adicional a MonkeyCall, lo que permite su VOIP llama para interactuar con otros tipos de llamadas que aparezca en los paneles recientes integradas y listas favorito, para usar las características integradas de bloque y no molestar, iniciar llamadas MonkeyCall de Siri y ofrece la posibilidad de que los usuarios asignar MonkeyCall llamadas a las personas de la aplicación de contactos.

Las secciones siguientes tratarán en la arquitectura CallKit, entrantes y salientes llaman flujos y la API CallKit en detalle.

## <a name="the-callkit-architecture"></a>La arquitectura CallKit

En iOS 10, Apple ha adoptado CallKit en todos los servicios del sistema de modo que las llamadas realizadas en CarPlay, por ejemplo, se sabe que la interfaz de usuario del sistema a través de CallKit. En el ejemplo que se indican a continuación, puesto que MonkeyCall adopta CallKit, se sabe que el sistema en la misma manera que estos servicios del sistema integrada y obtiene todas las características de la mismas:

[![](callkit-images/callkit01.png "La pila del servicio CallKit")](callkit-images/callkit01.png#lightbox)

Eche un vistazo más de cerca en el App MonkeyCall en el diagrama anterior. La aplicación contiene todo el código para comunicarse con su propia red y contiene sus propias Interfaces de usuario. Vincula en CallKit para comunicarse con el sistema:

[![](callkit-images/callkit02.png "Arquitectura de la aplicación MonkeyCall")](callkit-images/callkit02.png#lightbox)

Hay dos interfaces principales en CallKit que usa la aplicación:

- `CXProvider` -Esto permite que la aplicación MonkeyCall informar al sistema de las notificaciones de fuera de banda que se produzcan.
- `CXCallController` -Permite que la aplicación MonkeyCall informar al sistema de las acciones del usuario local.

### <a name="the-cxprovider"></a>El CXProvider

Como se indicó anteriormente, `CXProvider` permite que una aplicación informar al sistema de las notificaciones de fuera de banda que se produzcan. Se trata de una notificación de que no se producen debido a las acciones del usuario local, pero se producen debido a eventos externos, como las llamadas entrantes.

Una aplicación debe usar el `CXProvider` para lo siguiente:

- Informe de una llamada entrante al sistema.
- Informar de un valor que llamada realizada se ha conectado al sistema.
- Notificar al usuario remoto termina la llamada al sistema.

Cuando la aplicación desea comunicarse con el sistema, usa el `CXCallUpdate` clase y cuando el sistema necesita para comunicarse con la aplicación, usa el `CXAction` clase:

[![](callkit-images/callkit03.png "Comunicación con el sistema a través de un CXProvider")](callkit-images/callkit03.png#lightbox)

### <a name="the-cxcallcontroller"></a>El CXCallController

El `CXCallController` permite que una aplicación informar al sistema de las acciones del usuario local como el usuario a partir de una llamada VOIP. Implementando un `CXCallController` obtiene de la aplicación que entran en juego con otros tipos de llamadas en el sistema. Por ejemplo, si ya hay una llamada de telefonía activas en curso, `CXCallController` puede permitir que la aplicación VOIP realizar esa llamada en espera e iniciar o responder a una llamada VOIP.

Una aplicación debe usar el `CXCallController` para lo siguiente:

- Informe cuando el usuario ha iniciado una llamada saliente en el sistema.
- Informe cuando el usuario responde a una llamada entrante al sistema.
- Informe cuando el usuario finaliza una llamada al sistema.

Cuando la aplicación desea comunicar acciones de usuario local para el sistema, usa el `CXTransaction` clase:

[![](callkit-images/callkit04.png "La notificación al sistema mediante un CXCallController")](callkit-images/callkit04.png#lightbox)

## <a name="implementing-callkit"></a>Implementar CallKit

Las secciones siguientes mostrará cómo implementar CallKit en una aplicación de Xamarin.iOS VOIP. Por ejemplo, este documento usará código desde la aplicación MonkeyCall VOIP ficticia. El código presentado aquí representa varias clases auxiliares, el CallKit le partes específicas se trata detalladamente en las secciones siguientes.

### <a name="the-activecall-class"></a>La clase ActiveCall

La `ActiveCall` clase se usa por la aplicación MonkeyCall para contener toda la información acerca de una llamada VOIP en el que está activa actualmente como sigue:

```csharp
using System;
using CoreFoundation;
using Foundation;

namespace MonkeyCall
{
    public class ActiveCall
    {
        #region Private Variables
        private bool isConnecting;
        private bool isConnected;
        private bool isOnhold;
        #endregion

        #region Computed Properties
        public NSUuid UUID { get; set; }
        public bool isOutgoing { get; set; }
        public string Handle { get; set; }
        public DateTime StartedConnectingOn { get; set;}
        public DateTime ConnectedOn { get; set;}
        public DateTime EndedOn { get; set; }

        public bool IsConnecting {
            get { return isConnecting; }
            set {
                isConnecting = value;
                if (isConnecting) StartedConnectingOn = DateTime.Now;
                RaiseStartingConnectionChanged ();
            }
        }

        public bool IsConnected {
            get { return isConnected; }
            set {
                isConnected = value;
                if (isConnected) {
                    ConnectedOn = DateTime.Now;
                } else {
                    EndedOn = DateTime.Now;
                }
                RaiseConnectedChanged ();
            }
        }

        public bool IsOnHold {
            get { return isOnhold; }
            set {
                isOnhold = value;
            }
        }
        #endregion

        #region Constructors
        public ActiveCall ()
        {
        }

        public ActiveCall (NSUuid uuid, string handle, bool outgoing)
        {
            // Initialize
            this.UUID = uuid;
            this.Handle = handle;
            this.isOutgoing = outgoing;
        }
        #endregion

        #region Public Methods
        public void StartCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call starting successfully
            completionHandler (true);

            // Simulate making a starting and completing a connection
            DispatchQueue.MainQueue.DispatchAfter (new DispatchTime(DispatchTime.Now, 3000), () => {
                // Note that the call is starting
                IsConnecting = true;

                // Simulate pause before connecting
                DispatchQueue.MainQueue.DispatchAfter (new DispatchTime (DispatchTime.Now, 1500), () => {
                    // Note that the call has connected
                    IsConnecting = false;
                    IsConnected = true;
                });
            });
        }

        public void AnswerCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call being answered
            IsConnected = true;
            completionHandler (true);
        }

        public void EndCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call ending
            IsConnected = false;
            completionHandler (true);
        }
        #endregion

        #region Events
        public delegate void ActiveCallbackDelegate (bool successful);
        public delegate void ActiveCallStateChangedDelegate (ActiveCall call);

        public event ActiveCallStateChangedDelegate StartingConnectionChanged;
        internal void RaiseStartingConnectionChanged ()
        {
            if (this.StartingConnectionChanged != null) this.StartingConnectionChanged (this);
        }

        public event ActiveCallStateChangedDelegate ConnectedChanged;
        internal void RaiseConnectedChanged ()
        {
            if (this.ConnectedChanged != null) this.ConnectedChanged (this);
        }
        #endregion
    }
}
```

`ActiveCall` contiene varias propiedades que definen el estado de la llamada y dos eventos que pueden generarse cuando cambia el estado de la llamada. Puesto que esto es sólo un ejemplo, hay tres métodos utilizados para simulated inicial, responder y final de una llamada.

### <a name="the-startcallrequest-class"></a>La clase StartCallRequest

La `StartCallRequest` clase estática, proporciona varios métodos auxiliares que se usará cuando se llama trabajar con los salientes:

```csharp
using System;
using Foundation;
using Intents;

namespace MonkeyCall
{
    public static class StartCallRequest
    {
        public static string URLScheme {
            get { return "monkeycall"; }
        }

        public static string ActivityType {
            get { return INIntentIdentifier.StartAudioCall.GetConstant ().ToString (); }
        }

        public static string CallHandleFromURL (NSUrl url)
        {
            // Is this a MonkeyCall handle?
            if (url.Scheme == URLScheme) {
                // Yes, return host
                return url.Host;
            } else {
                // Not handled
                return null;
            }
        }

        public static string CallHandleFromActivity (NSUserActivity activity)
        {
            // Is this a start call activity?
            if (activity.ActivityType == ActivityType) {
                // Yes, trap any errors
                try {
                    // Get first contact
                    var interaction = activity.GetInteraction ();
                    var startAudioCallIntent = interaction.Intent as INStartAudioCallIntent;
                    var contact = startAudioCallIntent.Contacts [0];

                    // Get the person handle
                    return contact.PersonHandle.Value;
                } catch {
                    // Error, report null
                    return null;
                }
            } else {
                // Not handled
                return null;
            }
        }
    }
}
```

El `CallHandleFromURL` y `CallHandleFromActivity` clases se usan en el AppDelegate para obtener el identificador de contacto de la persona que se llama en una llamada saliente. Para obtener más información, consulte el [controlar llamadas salientes](#handling-outgoing-calls) sección más adelante.

### <a name="the-activecallmanager-class"></a>La clase ActiveCallManager

La `ActiveCallManager` clase controla todas las llamadas en la aplicación MonkeyCall.

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using CallKit;

namespace MonkeyCall
{
    public class ActiveCallManager
    {
        #region Private Variables
        private CXCallController CallController = new CXCallController ();
        #endregion

        #region Computed Properties
        public List<ActiveCall> Calls { get; set; }
        #endregion

        #region Constructors
        public ActiveCallManager ()
        {
            // Initialize
            this.Calls = new List<ActiveCall> ();
        }
        #endregion

        #region Private Methods
        private void SendTransactionRequest (CXTransaction transaction)
        {
            // Send request to call controller
            CallController.RequestTransaction (transaction, (error) => {
                // Was there an error?
                if (error == null) {
                    // No, report success
                    Console.WriteLine ("Transaction request sent successfully.");
                } else {
                    // Yes, report error
                    Console.WriteLine ("Error requesting transaction: {0}", error);
                }
            });
        }
        #endregion

        #region Public Methods
        public ActiveCall FindCall (NSUuid uuid)
        {
            // Scan for requested call
            foreach (ActiveCall call in Calls) {
                if (call.UUID.Equals(uuid)) return call;
            }

            // Not found
            return null;
        }

        public void StartCall (string contact)
        {
            // Build call action
            var handle = new CXHandle (CXHandleType.Generic, contact);
            var startCallAction = new CXStartCallAction (new NSUuid (), handle);

            // Create transaction
            var transaction = new CXTransaction (startCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void EndCall (ActiveCall call)
        {
            // Build action
            var endCallAction = new CXEndCallAction (call.UUID);

            // Create transaction
            var transaction = new CXTransaction (endCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void PlaceCallOnHold (ActiveCall call)
        {
            // Build action
            var holdCallAction = new CXSetHeldCallAction (call.UUID, true);

            // Create transaction
            var transaction = new CXTransaction (holdCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void RemoveCallFromOnHold (ActiveCall call)
        {
            // Build action
            var holdCallAction = new CXSetHeldCallAction (call.UUID, false);

            // Create transaction
            var transaction = new CXTransaction (holdCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }
        #endregion
    }
}
```

Nuevo, ya que se trata de una simulación solo, el `ActiveCallManager` solo mantiene una colección de `ActiveCall` objetos y tiene una rutina para buscar una llamada determinada por su `UUID` propiedad. También incluye métodos para iniciar, finalizar y cambiar el estado en espera de una llamada saliente. Para obtener más información, consulte el [controlar llamadas salientes](#handling-outgoing-calls) sección más adelante.

### <a name="the-providerdelegate-class"></a>La clase ProviderDelegate

Como se dijo anteriormente, un `CXProvider` proporciona comunicación bidireccional entre la aplicación y el sistema para las notificaciones de fuera de banda. El desarrollador debe proporcionar una personalizada `CXProviderDelegate` y adjuntarlo a la `CXProvider` para la aplicación controlar los eventos de CallKit fuera de banda. MonkeyCall usa las siguientes `CXProviderDelegate`:

```csharp
using System;
using Foundation;
using CallKit;
using UIKit;

namespace MonkeyCall
{
    public class ProviderDelegate : CXProviderDelegate
    {
        #region Computed Properties
        public ActiveCallManager CallManager { get; set;}
        public CXProviderConfiguration Configuration { get; set; }
        public CXProvider Provider { get; set; }
        #endregion

        #region Constructors
        public ProviderDelegate (ActiveCallManager callManager)
        {
            // Save connection to call manager
            CallManager = callManager;

            // Define handle types
            var handleTypes = new [] { (NSNumber)(int)CXHandleType.PhoneNumber };

            // Get Image Template
            var templateImage = UIImage.FromFile ("telephone_receiver.png");

            // Setup the initial configurations
            Configuration = new CXProviderConfiguration ("MonkeyCall") {
                MaximumCallsPerCallGroup = 1,
                SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
                IconTemplateImageData = templateImage.AsPNG(),
                RingtoneSound = "musicloop01.wav"
            };

            // Create a new provider
            Provider = new CXProvider (Configuration);

            // Attach this delegate
            Provider.SetDelegate (this, null);

        }
        #endregion

        #region Override Methods
        public override void DidReset (CXProvider provider)
        {
            // Remove all calls
            CallManager.Calls.Clear ();
        }

        public override void PerformStartCallAction (CXProvider provider, CXStartCallAction action)
        {
            // Create new call record
            var activeCall = new ActiveCall (action.CallUuid, action.CallHandle.Value, true);

            // Monitor state changes
            activeCall.StartingConnectionChanged += (call) => {
                if (call.isConnecting) {
                    // Inform system that the call is starting
                    Provider.ReportConnectingOutgoingCall (call.UUID, call.StartedConnectingOn.ToNSDate());
                }
            };

            activeCall.ConnectedChanged += (call) => {
                if (call.isConnected) {
                    // Inform system that the call has connected
                    provider.ReportConnectedOutgoingCall (call.UUID, call.ConnectedOn.ToNSDate ());
                }
            };

            // Start call
            activeCall.StartCall ((successful) => {
                // Was the call able to be started?
                if (successful) {
                    // Yes, inform the system
                    action.Fulfill ();

                    // Add call to manager
                    CallManager.Calls.Add (activeCall);
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformAnswerCallAction (CXProvider provider, CXAnswerCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Attempt to answer call
            call.AnswerCall ((successful) => {
                // Was the call successfully answered?
                if (successful) {
                    // Yes, inform system
                    action.Fulfill ();
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformEndCallAction (CXProvider provider, CXEndCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Attempt to answer call
            call.EndCall ((successful) => {
                // Was the call successfully answered?
                if (successful) {
                    // Remove call from manager's queue
                    CallManager.Calls.Remove (call);

                    // Yes, inform system
                    action.Fulfill ();
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformSetHeldCallAction (CXProvider provider, CXSetHeldCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Update hold status
            call.isOnHold = action.OnHold;

            // Inform system of success
            action.Fulfill ();
        }

        public override void TimedOutPerformingAction (CXProvider provider, CXAction action)
        {
            // Inform user that the action has timed out
        }

        public override void DidActivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
        {
            // Start the calls audio session here
        }

        public override void DidDeactivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
        {
            // End the calls audio session and restart any non-call
            // related audio
        }
        #endregion

        #region Public Methods
        public void ReportIncomingCall (NSUuid uuid, string handle)
        {
            // Create update to describe the incoming call and caller
            var update = new CXCallUpdate ();
            update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);

            // Report incoming call to system
            Provider.ReportNewIncomingCall (uuid, update, (error) => {
                // Was the call accepted
                if (error == null) {
                    // Yes, report to call manager
                    CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
                } else {
                    // Report error to user here
                    Console.WriteLine ("Error: {0}", error);
                }
            });
        }
        #endregion
    }
}
```

Cuando se crea una instancia de este delegado, se pasa el `ActiveCallManager` que usará para controlar cualquier actividad de llamada. A continuación, define los tipos de identificador (`CXHandleType`) que el `CXProvider` responderá a:

```csharp
// Define handle types
var handleTypes = new [] { (NSNumber)(int)CXHandleType.PhoneNumber };
```

Y obtiene la imagen de plantilla que se aplicará al icono de la aplicación cuando una llamada está en curso:

```csharp
// Get Image Template
var templateImage = UIImage.FromFile ("telephone_receiver.png");
```

Estos valores obtengan agrupados en un `CXProviderConfiguration` que se usará para configurar el `CXProvider`:

```csharp
// Setup the initial configurations
Configuration = new CXProviderConfiguration ("MonkeyCall") {
    MaximumCallsPerCallGroup = 1,
    SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
    IconTemplateImageData = templateImage.AsPNG(),
    RingtoneSound = "musicloop01.wav"
};
```

El delegado, a continuación, crea un nuevo `CXProvider` con estas configuraciones y se adjunta a él:

```csharp
// Create a new provider
Provider = new CXProvider (Configuration);

// Attach this delegate
Provider.SetDelegate (this, null);
```

Al usar CallKit, la aplicación ya no se cree y controle sus propias sesiones de audio, en su lugar, necesitará configurar y utilizar una sesión de audio que el sistema creará y controlar para él. 

Si se tratara de una aplicación real, el `DidActivateAudioSession` se usaría el método para iniciar la llamada con un objeto previamente configurado `AVAudioSession` suministradas por el sistema:

```csharp
public override void DidActivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // Start the call's audio session here...
}
```

También se utilizarían las `DidDeactivateAudioSession` audio sesión proporciona el método finalice y libere su conexión con el sistema:

```csharp
public override void DidDeactivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // End the calls audio session and restart any non-call
    // releated audio
}
```

El resto del código se tratarán en detalle en las secciones siguientes.

### <a name="the-appdelegate-class"></a>La clase AppDelegate

MonkeyCall utiliza AppDelegate para contener instancias de la `ActiveCallManager` y `CXProviderDelegate` que se usará en toda la aplicación:

```csharp
using Foundation;
using UIKit;
using Intents;
using System;

namespace MonkeyCall
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        #region Constructors
        public override UIWindow Window { get; set; }
        public ActiveCallManager CallManager { get; set; }
        public ProviderDelegate CallProviderDelegate { get; set; }
        #endregion

        #region Override Methods
        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Initialize the call handlers
            CallManager = new ActiveCallManager ();
            CallProviderDelegate = new ProviderDelegate (CallManager);

            return true;
        }

        public override bool OpenUrl (UIApplication app, NSUrl url, NSDictionary options)
        {
            // Get handle from url
            var handle = StartCallRequest.CallHandleFromURL (url);

            // Found?
            if (handle == null) {
                // No, report to system
                Console.WriteLine ("Unable to get call handle from URL: {0}", url); 
                return false;
            } else {
                // Yes, start call and inform system
                CallManager.StartCall (handle);
                return true;
            }
        }

        public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
        {
            var handle = StartCallRequest.CallHandleFromActivity (userActivity);

            // Found?
            if (handle == null) {
                // No, report to system
                Console.WriteLine ("Unable to get call handle from User Activity: {0}", userActivity);
                return false;
            } else {
                // Yes, start call and inform system
                CallManager.StartCall (handle);
                return true;
            }
        }

        ...
        #endregion
    }
}
```

El `OpenUrl` y `ContinueUserActivity` invalidar los métodos se usan cuando la aplicación está procesando una llamada saliente. Para obtener más información, consulte el [controlar llamadas salientes](#handling-outgoing-calls) sección más adelante.

## <a name="handling-incoming-calls"></a>Controlar las llamadas entrantes

Hay varios Estados y los procesos que una llamada VOIP entrante puede pasar durante un flujo de trabajo de llamada entrante típico, como:

- Informar al usuario (y el sistema) que existe una llamada entrante.
- Recibir notificación cuando el usuario desea responder a la llamada y la inicialización de la llamada con el otro usuario.
- Informar al sistema y la comunicación de red cuando el usuario desea finalizar la llamada actual.

Las siguientes secciones se tardará una visión detallada de cómo una aplicación puede utilizar CallKit para controlar el flujo de trabajo llamada entrante, nuevo con la aplicación MonkeyCall VOIP como ejemplo.

### <a name="informing-user-of-incoming-call"></a>Para informar al usuario de la llamada entrante

Cuando un usuario remoto ha iniciado una conversación de VOIP con el usuario local, ocurre lo siguiente:

[![](callkit-images/callkit05.png "Un usuario remoto ha iniciado una conversación de VOIP")](callkit-images/callkit05.png#lightbox)

1. La aplicación recibe una notificación de su red de comunicaciones que hay una llamada entrante de VOIP.
2. La aplicación usa el `CXProvider` para enviar un `CXCallUpdate` al sistema para informar de la llamada.
3. El sistema publica la llamada a la interfaz de usuario del sistema, los servicios del sistema y otras aplicaciones VOIP con CallKit.

Por ejemplo, en el `CXProviderDelegate`:

```csharp
public void ReportIncomingCall (NSUuid uuid, string handle)
{
    // Create update to describe the incoming call and caller
    var update = new CXCallUpdate ();
    update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);

    // Report incoming call to system
    Provider.ReportNewIncomingCall (uuid, update, (error) => {
        // Was the call accepted
        if (error == null) {
            // Yes, report to call manager
            CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
        } else {
            // Report error to user here
            Console.WriteLine ("Error: {0}", error);
        }
    });
}
```

Este código crea un nuevo `CXCallUpdate` de instancia y asocia un identificador que identifica el llamador. A continuación, usa el `ReportNewIncomingCall` método de la `CXProvider` clase para informar al sistema de la llamada. Si es correcto, la llamada se agrega a la colección de la aplicación de llamadas activas, si no lo está, el error debe notificarse al usuario.

### <a name="user-answering-incoming-call"></a>Llamada entrante de respuesta de usuario

Si el usuario desea responder a la llamada VOIP entrante, ocurre lo siguiente:

[![](callkit-images/callkit06.png "El usuario responde a la llamada VOIP entrante")](callkit-images/callkit06.png#lightbox)

1. La interfaz de usuario del sistema informa al sistema que el usuario desea responder a la llamada VOIP.
2. El sistema envía una `CXAnswerCallAction` a la aplicación `CXProvider` para informar de la intención de respuesta.
3. La aplicación informa a su red de comunicación que el usuario responde a la llamada y la llamada VOIP continúa como de costumbre.

Por ejemplo, en el `CXProviderDelegate`:

```csharp
public override void PerformAnswerCallAction (CXProvider provider, CXAnswerCallAction action)
{
    // Find requested call
    var call = CallManager.FindCall (action.CallUuid);

    // Found?
    if (call == null) {
        // No, inform system and exit
        action.Fail ();
        return;
    }

    // Attempt to answer call
    call.AnswerCall ((successful) => {
        // Was the call successfully answered?
        if (successful) {
            // Yes, inform system
            action.Fulfill ();
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

Este código busca primero la llamada determinada en su lista de llamadas activas. Si no se encuentra la llamada, se notifica el sistema y el método finaliza. Si se encuentra, el `AnswerCall` método de la `ActiveCall` clase se llama para iniciar la llamada y el sistema de información si se realiza correctamente o se produce un error.

### <a name="user-ending-incoming-call"></a>Usuario final de la llamada entrante

Si el usuario desea terminar la llamada desde dentro de la interfaz de usuario de la aplicación, ocurre lo siguiente:

[![](callkit-images/callkit07.png "El usuario finaliza la llamada desde dentro de la interfaz de usuario de la aplicación")](callkit-images/callkit07.png#lightbox)

1. La aplicación crea `CXEndCallAction` que obtiene agrupado en un `CXTransaction` que se envía al sistema para informar a lo que finaliza la llamada.
2. El sistema comprueba el intento de llamar a End y envía el `CXEndCallAction` a la aplicación a través de la `CXProvider`.
3. La aplicación, a continuación, informa a su red de comunicación que finaliza la llamada.

Por ejemplo, en el `CXProviderDelegate`:

```csharp
public override void PerformEndCallAction (CXProvider provider, CXEndCallAction action)
{
    // Find requested call
    var call = CallManager.FindCall (action.CallUuid);

    // Found?
    if (call == null) {
        // No, inform system and exit
        action.Fail ();
        return;
    }

    // Attempt to answer call
    call.EndCall ((successful) => {
        // Was the call successfully answered?
        if (successful) {
            // Remove call from manager's queue
            CallManager.Calls.Remove (call);

            // Yes, inform system
            action.Fulfill ();
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

Este código busca primero la llamada determinada en su lista de llamadas activas. Si no se encuentra la llamada, se notifica el sistema y el método finaliza. Si se encuentra, el `EndCall` método de la `ActiveCall` clase se llama para finalizar la llamada y el sistema de información si se realiza correctamente o se produce un error. Si se realiza correctamente, la llamada se quita de la colección de llamadas activas.

## <a name="managing-multiple-calls"></a>Administración de varias llamadas

La mayoría de las aplicaciones VOIP pueden controlar varias llamadas a la vez. Por ejemplo, si actualmente hay una llamada VOIP activa y la notificación de obtiene de la aplicación que existe es una nueva llamada entrante, el usuario puede pausar o colgar en la primera llamada para responder a la otra.

En la situación asigne anterior, el sistema le enviará un `CXTransaction` a la aplicación que incluye una lista de varias acciones (como el `CXEndCallAction` y `CXAnswerCallAction`). Todas estas acciones se deben cumplir de forma individual, para que el sistema puede actualizar la interfaz de usuario de forma adecuada.

## <a name="handling-outgoing-calls"></a>Llama a control saliente

Si el usuario pulsa una entrada en la lista de recientes (en la aplicación de teléfono), por ejemplo, que está en una llamada a la que pertenecen a la aplicación, se enviará un _iniciar el intento de llamar a_ por el sistema:

[![](callkit-images/callkit08.png "Recepción de un intento de llamada de inicio")](callkit-images/callkit08.png#lightbox)

1. La aplicación creará un _Iniciar acción de llamada a_ según el inicio llamar a propósito que recibió del sistema. 
2. La aplicación usará el `CXCallController` para solicitar la acción de llamada a iniciar desde el sistema.
3. Si el sistema acepta la acción, se devolverá a la aplicación a través de la `XCProvider` delegar.
4. La llamada saliente inicia la aplicación con su red de comunicación.

Para obtener más información sobre las intenciones, consulte nuestra [intenciones y extensiones de interfaz de usuario de intenciones](~/ios/platform/sirikit/understanding-sirikit.md) documentación. 

### <a name="the-outgoing-call-lifecycle"></a>El ciclo de vida de la llamada saliente

Cuando se trabaja con CallKit y una llamada saliente, la aplicación debe informar al sistema de los siguientes eventos de ciclo de vida:

1. **Iniciando** -informar al sistema que va a iniciar una llamada saliente.
2. **Iniciar** -informar al sistema que se ha iniciado una llamada saliente.
3. **Conexión** -informar al sistema que se está conectando a la llamada saliente.
4. **Conectado** -informar a la salida de llamada se ha conectado y que ambas partes pueden hablar ahora.

Por ejemplo, el código siguiente iniciará una llamada saliente:

```csharp
private CXCallController CallController = new CXCallController ();
...

private void SendTransactionRequest (CXTransaction transaction)
{
    // Send request to call controller
    CallController.RequestTransaction (transaction, (error) => {
        // Was there an error?
        if (error == null) {
            // No, report success
            Console.WriteLine ("Transaction request sent successfully.");
        } else {
            // Yes, report error
            Console.WriteLine ("Error requesting transaction: {0}", error);
        }
    });
}

public void StartCall (string contact)
{
    // Build call action
    var handle = new CXHandle (CXHandleType.Generic, contact);
    var startCallAction = new CXStartCallAction (new NSUuid (), handle);

    // Create transaction
    var transaction = new CXTransaction (startCallAction);

    // Inform system of call request
    SendTransactionRequest (transaction);
}
```

Crea un `CXHandle` y lo usa para configurar un `CXStartCallAction` que está integrado en un `CXTransaction` que es enviado al sistema con el `RequestTransaction` método de la `CXCallController` clase. Mediante una llamada a la `RequestTransaction` método, el sistema puede colocar cualquier existente llamadas en espera, independientemente del origen (aplicación de teléfono, FaceTime, VOIP, etc.), antes de que se inicia la llamada nuevo.

La solicitud para iniciar una llamada saliente de VOIP puede proceder de varios orígenes diferentes, como Siri, una entrada en una tarjeta de contacto (en la aplicación contactos) o en la lista de recientes (en la aplicación de teléfono). En estas situaciones, la aplicación se enviará un intento de llamar a iniciar dentro de un `NSUserActivity` y debe controlarla el AppDelegate:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    var handle = StartCallRequest.CallHandleFromActivity (userActivity);

    // Found?
    if (handle == null) {
        // No, report to system
        Console.WriteLine ("Unable to get call handle from User Activity: {0}", userActivity);
        return false;
    } else {
        // Yes, start call and inform system
        CallManager.StartCall (handle);
        return true;
    }
}
```

Aquí el `CallHandleFromActivity` método de la clase auxiliar `StartCallRequest` se utiliza para obtener el identificador a la persona que se llama (consulte [la clase StartCallRequest](#the-startcallrequest-class) anteriormente).

El `PerformStartCallAction` método de la [ProviderDelegate clase](#the-providerdelegate-class) se usa para iniciar la llamada saliente real, por último e informar al sistema de su ciclo de vida:

```csharp
public override void PerformStartCallAction (CXProvider provider, CXStartCallAction action)
{
    // Create new call record
    var activeCall = new ActiveCall (action.CallUuid, action.CallHandle.Value, true);

    // Monitor state changes
    activeCall.StartingConnectionChanged += (call) => {
        if (call.IsConnecting) {
            // Inform system that the call is starting
            Provider.ReportConnectingOutgoingCall (call.UUID, call.StartedConnectingOn.ToNSDate());
        }
    };

    activeCall.ConnectedChanged += (call) => {
        if (call.IsConnected) {
            // Inform system that the call has connected
            Provider.ReportConnectedOutgoingCall (call.UUID, call.ConnectedOn.ToNSDate ());
        }
    };

    // Start call
    activeCall.StartCall ((successful) => {
        // Was the call able to be started?
        if (successful) {
            // Yes, inform the system
            action.Fulfill ();

            // Add call to manager
            CallManager.Calls.Add (activeCall);
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

Crea una instancia de la `ActiveCall` clase (para almacenar información acerca de la llamada en curso) y la rellena con la persona que se llama. El `StartingConnectionChanged` y `ConnectedChanged` eventos se utilizan para supervisar y notificar el ciclo de vida de la llamada saliente. Se inicia la llamada y el sistema informa de que se llevó a cabo la acción.

### <a name="ending-an-outgoing-call"></a>Finaliza una llamada saliente

Cuando el usuario ha terminado con una llamada saliente y desea terminarlo, se puede usar el código siguiente:

```csharp
private CXCallController CallController = new CXCallController ();
...

private void SendTransactionRequest (CXTransaction transaction)
{
    // Send request to call controller
    CallController.RequestTransaction (transaction, (error) => {
        // Was there an error?
        if (error == null) {
            // No, report success
            Console.WriteLine ("Transaction request sent successfully.");
        } else {
            // Yes, report error
            Console.WriteLine ("Error requesting transaction: {0}", error);
        }
    });
}

public void EndCall (ActiveCall call)
{
    // Build action
    var endCallAction = new CXEndCallAction (call.UUID);

    // Create transaction
    var transaction = new CXTransaction (endCallAction);

    // Inform system of call request
    SendTransactionRequest (transaction);
}
```

Si crea un `CXEndCallAction` con el UUID de la llamada al final, lo empaqueta en un `CXTransaction` que es enviado al sistema con el `RequestTransaction` método de la `CXCallController` clase. 

## <a name="additional-callkit-details"></a>Detalles adicionales de CallKit

En esta sección se tratan algunos detalles adicionales que el desarrollador tendrá que tener en cuenta al trabajar con CallKit como:

- Configuración del proveedor
- Errores de acción
- Restricciones del sistema
- Audio VOIP

### <a name="provider-configuration"></a>Configuración del proveedor

La configuración del proveedor permite que una aplicación de iOS 10 VOIP personalizar la experiencia del usuario (dentro de la interfaz de usuario nativa de llamada) cuando trabaja con CallKit.

Una aplicación puede realizar los siguientes tipos de personalizaciones:

- Mostrar un nombre traducido.
- Habilitar la compatibilidad de la llamada de vídeo.
- Personalizar los botones de la interfaz de usuario en llamadas al presentar su propio icono de imagen de plantilla. Interacción del usuario con botones personalizados se envía directamente a la aplicación a procesarse. 

### <a name="action-errors"></a>Errores de acción

aplicaciones de iOS 10 VOIP mediante CallKit deben controlar las acciones que se producen errores en forma correcta y mantener al usuario informado del estado de acción en todo momento. 

El ejemplo siguiente se tener en cuenta:

1. La aplicación ha recibido una acción de llamada a iniciar y ha comenzado el proceso de inicialización de una nueva llamada VOIP con su red de comunicación.
2. Debido a la limitada o ninguna capacidad de comunicación de red, se produce un error de esta conexión.
3. La aplicación *debe* enviar el **producirá un error en** mensaje a la acción de llamada a iniciar (`Action.Fail()`) para informar al sistema del error.
4. Esto permite al sistema informar al usuario del estado de la llamada. Por ejemplo, para mostrar la interfaz de usuario llame a un error.

Además, debe responder a una aplicación de iOS 10 VOIP _errores de tiempo de espera_ que puede producirse cuando no se puede procesar una acción esperada dentro de un período determinado de tiempo. Cada tipo de acción proporcionado por CallKit tiene un valor máximo de tiempo de espera asociado con él. Estos valores de tiempo de espera asegurarse de que cualquier acción CallKit solicitado por el usuario se controlan de forma con capacidad de respuesta, evitando así que el sistema operativo fluidas y sensibles también.

Existen varios métodos en el delegado de proveedor (`CXProviderDelegate`) que se debe invalidar para controlar correctamente esta situaciones de tiempo de espera también.

### <a name="system-restrictions"></a>Restricciones del sistema

Según el estado actual del dispositivo iOS que ejecuta la aplicación de iOS 10 VOIP, se aplican ciertas restricciones del sistema.

Por ejemplo, una llamada VOIP entrante puede estar restringida por el sistema si:

1. La persona que llama se encuentra en la lista del usuario bloqueado llamador.
2. Dispositivos iOS del usuario está en el modo de-no molestar.

Si una llamada VOIP está restringida por cualquiera de estas situaciones, utilice el siguiente código para controlarlo:

```csharp
public class ProviderDelegate : CXProviderDelegate
{
...

    public void ReportIncomingCall (NSUuid uuid, string handle)
    {
        // Create update to describe the incoming call and caller
        var update = new CXCallUpdate ();
        update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);
    
        // Report incoming call to system
        Provider.ReportNewIncomingCall (uuid, update, (error) => {
            // Was the call accepted
            if (error == null) {
                // Yes, report to call manager
                CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
            } else {
                // Report error to user here
                if (error.Code == (int)CXErrorCodeIncomingCallError.CallUuidAlreadyExists) {
                    // Handle duplicate call ID
                } else if (error.Code == (int)CXErrorCodeIncomingCallError.FilteredByBlockList) {
                    // Handle call from blocked user
                } else if (error.Code == (int)CXErrorCodeIncomingCallError.FilteredByDoNotDisturb) {
                    // Handle call while in do-not-disturb mode
                } else {
                    // Handle unknown error
                }
            }
        });
    }

}
```

### <a name="voip-audio"></a>Audio VOIP

CallKit proporciona varias ventajas para controlar los recursos de sonido que requiera una aplicación de iOS 10 VOIP durante una llamada VOIP en vivo. Una de las mayores ventajas es que la aplicación sesión audio habrá elevados prioridades cuando se ejecuta en iOS 10. Este es el mismo nivel de prioridad que el teléfono integradas y aplicaciones FaceTime y este nivel de prioridad mejorada impedirá que otras aplicaciones en ejecución interrumpir la sesión de audio de la aplicación VOIP.

Además, CallKit tiene acceso a otras sugerencias de enrutamientos de audio que puede mejorar el rendimiento y enrutar de forma inteligente audio VOIP a determinados dispositivos de salida durante una llamada live según las preferencias del usuario y Estados de dispositivo. Por ejemplo, en función de los dispositivos conectados, como bluetooth auriculares, una conexión activa a CarPlay o configuración de accesibilidad.

Durante el ciclo de vida de un típico VOIP llamada mediante CallKit, la aplicación tendrá que configurar el Stream de Audio que CallKit lo proporcionan. Eche un vistazo en el ejemplo siguiente:

[![](callkit-images/callkit09.png "La secuencia de acciones de llamada de inicio")](callkit-images/callkit09.png#lightbox)

1. Se recibe una acción de llamada a iniciar la aplicación para responder a una llamada entrante.
2. Antes de que esta acción se cumple con la aplicación, proporciona la configuración que se requerirá para su `AVAudioSession`.
3. La aplicación informa al sistema que se ha cumplido la acción.
4. Antes de la llamada se conecta, CallKit proporciona una alta prioridad `AVAudioSession` coincide con la configuración que solicitó la aplicación. La aplicación se notificará a través de la `DidActivateAudioSession` método de su `CXProviderDelegate`.

## <a name="working-with-call-directory-extensions"></a>Trabajar con las extensiones de directorio de llamada

Cuando se trabaja con CallKit, _llamar a las extensiones de directorio_ proporcionan una manera de agregar números de llamadas bloqueados e identificar los números que son específicos de una aplicación determinada de VOIP en los contactos de la aplicación de contacto en el dispositivo iOS.

### <a name="implementing-a-call-directory-extension"></a>Implementar una extensión de directorio de llamada

Para implementar una extensión de directorio llamar a una aplicación de Xamarin.iOS, realice lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Abra la solución de la aplicación en Visual Studio para Mac.
2. Haga doble clic en el nombre de la solución en el **el Explorador de soluciones** y seleccione **agregar** > **Agregar nuevo proyecto**.
3. Seleccione **iOS** > **extensiones** > **llamar a las extensiones de directorio** y haga clic en el **siguiente** botón: 

    [![](callkit-images/calldir01.png "Crear una nueva extensión de directorio de llamar a")](callkit-images/calldir01.png#lightbox)
4. Escriba un **nombre** para la extensión y haga clic en el **siguiente** botón: 

    [![](callkit-images/calldir02.png "Escribir un nombre para la extensión")](callkit-images/calldir02.png#lightbox)
5. Ajustar el **nombre del proyecto** o **nombre de la solución** si es necesario y haga clic en el **crear** botón: 

    [![](callkit-images/calldir03.png "Creación del proyecto")](callkit-images/calldir03.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Abra la solución de la aplicación en Visual Studio.
2. Haga doble clic en el nombre de la solución en el **el Explorador de soluciones** y seleccione **agregar** > **Agregar nuevo proyecto**.
3. Seleccione **iOS** > **extensiones** > **llamar a las extensiones de directorio** y haga clic en el **siguiente** botón: 

    [![](callkit-images/calldir01w.png "Crear una nueva extensión de directorio de llamar a")](callkit-images/calldir01.png#lightbox)
4. Escriba un **nombre** para la extensión y haga clic en el **Aceptar** botón

-----

Esto agregará un `CallDirectoryHandler.cs` clase al proyecto que es similar al siguiente:

```csharp
using System;

using Foundation;
using CallKit;

namespace MonkeyCallDirExtension
{
    [Register ("CallDirectoryHandler")]
    public class CallDirectoryHandler : CXCallDirectoryProvider, ICXCallDirectoryExtensionContextDelegate
    {
        #region Constructors
        protected CallDirectoryHandler (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void BeginRequest (CXCallDirectoryExtensionContext context)
        {
            context.Delegate = this;

            if (!AddBlockingPhoneNumbers (context)) {
                Console.WriteLine ("Unable to add blocking phone numbers");
                var error = new NSError (new NSString ("CallDirectoryHandler"), 1, null);
                context.CancelRequest (error);
                return;
            }

            if (!AddIdentificationPhoneNumbers (context)) {
                Console.WriteLine ("Unable to add identification phone numbers");
                var error = new NSError (new NSString ("CallDirectoryHandler"), 2, null);
                context.CancelRequest (error);
                return;
            }

            context.CompleteRequest (null);
        }
        #endregion

        #region Private Methods
        private bool AddBlockingPhoneNumbers (CXCallDirectoryExtensionContext context)
        {
            // Retrieve phone numbers to block from data store. For optimal performance and memory usage when there are many phone numbers,
            // consider only loading a subset of numbers at a given time and using autorelease pool(s) to release objects allocated during each batch of numbers which are loaded.
            //
            // Numbers must be provided in numerically ascending order.

            long [] phoneNumbers = { 14085555555, 18005555555 };

            foreach (var phoneNumber in phoneNumbers)
                context.AddBlockingEntry (phoneNumber);

            return true;
        }

        private bool AddIdentificationPhoneNumbers (CXCallDirectoryExtensionContext context)
        {
            // Retrieve phone numbers to identify and their identification labels from data store. For optimal performance and memory usage when there are many phone numbers,
            // consider only loading a subset of numbers at a given time and using autorelease pool(s) to release objects allocated during each batch of numbers which are loaded.
            //
            // Numbers must be provided in numerically ascending order.

            long [] phoneNumbers = { 18775555555, 18885555555 };
            string [] labels = { "Telemarketer", "Local business" };

            for (var i = 0; i < phoneNumbers.Length; i++) {
                long phoneNumber = phoneNumbers [i];
                string label = labels [i];
                context.AddIdentificationEntry (phoneNumber, label);
            }

            return true;
        }
        #endregion

        #region Public Methods
        public void RequestFailed (CXCallDirectoryExtensionContext extensionContext, NSError error)
        {
            // An error occurred while adding blocking or identification entries, check the NSError for details.
            // For Call Directory error codes, see the CXErrorCodeCallDirectoryManagerError enum.
            //
            // This may be used to store the error details in a location accessible by the extension's containing app, so that the
            // app may be notified about errors which occurred while loading data even if the request to load data was initiated by
            // the user in Settings instead of via the app itself.
        }
        #endregion
    }
}
```

El `BeginRequest` método en el controlador de directorio llamar deberá modificarse para proporcionar la funcionalidad necesaria. En el caso del ejemplo anterior, intenta establecer la lista de números bloqueados y está disponibles en la base de datos de contactos de la aplicación VOIP. Si bien las solicitudes se produce un error por cualquier motivo, cree un `NSError` para describir el error y pasarle el `CancelRequest` método de la `CXCallDirectoryExtensionContext` clase.

Para establecer el uso de números bloqueado el `AddBlockingEntry` método de la `CXCallDirectoryExtensionContext` clase. Los números proporcionados al método _debe_ estar en orden ascendente numéricamente. Para obtener un rendimiento óptimo y el uso de memoria cuando hay que muchos números de teléfono, considere la posibilidad de sólo cargar un subconjunto de los números en un momento dado y usar grupos autorelease para liberar los objetos asignados durante cada lote de números que se cargan.

Para informar a la aplicación de contacto de los números de contacto que se sabe que la aplicación VOIP, utilice el `AddIdentificationEntry` método de la `CXCallDirectoryExtensionContext` clase y proporcione el número y una etiqueta de identificación. De nuevo, los números proporcionados al método _debe_ estar en orden ascendente numéricamente. Para obtener un rendimiento óptimo y el uso de memoria cuando hay que muchos números de teléfono, considere la posibilidad de sólo cargar un subconjunto de los números en un momento dado y usar grupos autorelease para liberar los objetos asignados durante cada lote de números que se cargan.

## <a name="summary"></a>Resumen

Este artículo trata la nueva API CallKit que publicó en iOS 10 y cómo implementarla en las aplicaciones de Xamarin.iOS VOIP de Apple. Ha mostrado cómo CallKit permite que una aplicación integrar en el sistema de iOS, cómo ofrece paridad de características con las aplicaciones integradas (por ejemplo, Phone) y cómo aumenta la visibilidad de una aplicación a lo largo de iOS en ubicaciones como el bloqueo y pantallas de inicio, a través de las interacciones de Siri y las aplicaciones de contactos.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
