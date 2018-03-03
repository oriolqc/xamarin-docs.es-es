---
title: "Características de Bean jalea"
description: "Este documento proporciona una introducción de alto nivel de las nuevas características para a los desarrolladores que se introdujeron en Android 4.1. Estas características incluyen: mejorado notificaciones, las actualizaciones de carretera Android para compartir archivos de gran tamaño, las actualizaciones de detección de redes de multimedia, peer-to-peer, animaciones, nuevos permisos."
ms.topic: article
ms.prod: xamarin
ms.assetid: 23F57634-2EF9-5C15-C710-B3E19A5AF7E1
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/14/2017
ms.openlocfilehash: 2e54bfc4bea3955dc80a747c4ecce485b78ada1d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="jelly-bean-features"></a>Características de Bean jalea

_Este documento proporciona una introducción de alto nivel de las nuevas características para a los desarrolladores que se introdujeron en Android 4.1. Estas características incluyen: mejorado notificaciones, las actualizaciones de carretera Android para compartir archivos de gran tamaño, las actualizaciones de detección de redes de multimedia, peer-to-peer, animaciones, nuevos permisos._

<a name="Overview" />


## <a name="overview"></a>Información general

Android 4.1 (API nivel 16), también conocido como "jalea Bean", fue la versión 9 de julio de 2012. En este artículo se proporciona una introducción de alto nivel a algunas de las nuevas características de Android 4.1 para los desarrolladores que utilizan Xamarin.Android. Algunas de estas nuevas características introducidas son mejoras a las animaciones para iniciar una actividad, sonidos nuevos con una cámara y compatibilidad mejorada para la navegación de la pila de aplicación. Ahora es posible cortar y pegar con propósitos.

Se ha mejorado la estabilidad de aplicaciones Android con la capacidad de aislar la dependencia de los proveedores de contenido inestables. Servicios también pueden ser aislados para que sean accesibles solo por la actividad que ha iniciado.

Ha agregado compatibilidad para la detección de servicio de red mediante servicios basados en Bonjour, UPnP o DNS de multidifusión. Ahora es posible para las notificaciones más completos que se han aplicado formato de texto, botones de acción e imágenes de gran tamaño.

Por último, se han agregado nuevos varios permisos en Android 4.1.

 <a name="Requirements" />


## <a name="requirements"></a>Requisitos

Desarrollar aplicaciones de Xamarin.Android uso jalea Bean requiere Xamarin.Android 4.2.6 o superior y Android 4.1 (API nivel 16) instalarse mediante el Administrador de SDK de Android como se muestra en la siguiente captura de pantalla:

[![Seleccionar Android 4.1 en el Administrador de SDK de Android](jelly-bean-images/image1.png)](jelly-bean-images/image1.png)

 <a name="What's_New" />


## <a name="whats-new"></a>Novedades

 <a name="Animations" />


### <a name="animations"></a>Animaciones

Las actividades se pueden iniciar mediante animaciones de zoom o animaciones personalizadas mediante la `ActivityOptions` clase. Se proporcionan los siguientes métodos nuevos para admitir estas animaciones:

-   `MakeScaleUpAnimation` : Se creará una animación que se amplía una ventana de actividad desde una posición inicial y el tamaño de la pantalla.
-   `MakeThumbnailScaleUpAnimation` : Se creará una animación que permite escalar de una imagen en miniatura de la posición especificada en la pantalla.
-   `MakeCustomAnimation` : Se crea una animación desde los recursos de la aplicación. Hay una animación para cuando se abra la actividad y otro para cuando se detenga la actividad.


El nuevo `TimeAnimator` clase proporciona una interfaz `TimeAnimator.ITimeListener` que puede notificar a una aplicación cada vez que cambia un marco en una animación. Por ejemplo, considere la siguiente implementación de `TimeAnimator.ITimeListener`:

```csharp
class MyTimeListener : Java.Lang.Object,  TimeAnimator.ITimeListener
{
    public void OnTimeUpdate(TimeAnimator animation, long totalTime, long deltaTime)
    {
        Log.Debug("Activity1", "totalTime={0}, deltaTime={1}", totalTime, deltaTime);
    }
}
```

Y ahora se utiliza la clase, una instancia de `TimeAnimator` se crea, y se establece el agente de escucha:

```csharp
var animator = new TimeAnimator();
animator.SetTimeListener(new MyTimeListener());
animator.Start();
```

Como el `TimeAnimator` instancia se está ejecutando, invocará `ITimeAnimator.ITimeListener`, que, a continuación, se registrará el tiempo la animación ha estado ejecutando y cuánto tiempo tal y como está desde la última vez que el método se ha invocado.

 <a name="Application_Stack_Navigation" />


### <a name="application-stack-navigation"></a>Navegación de la pila de aplicación

Android 4.1 mejora la navegación de la pila de aplicación que se introdujo en Android 3.0. Mediante la especificación de la `ParentName` propiedad de la `ActivityAttribute`, Android puede abrir la actividad primaria correcto cuando el usuario presiona el [botón](http://developer.android.com/design/patterns/navigation.html#up-vs-back) en la barra de acción - Android creará una instancia de la actividad especificada por el `ParentName`propiedad. Esto permite a las aplicaciones conservar la jerarquía de actividades que realizan una tarea determinada.

Para la mayoría de las aplicaciones establecer el `ParentName` en la actividad es suficiente información para Android proporcionar el comportamiento correcto para navegar por la pila de la aplicación; Android sintetizar la pila de retroceso necesaria mediante la creación de una serie de intentos para cada actividad primaria. Sin embargo, dado que se trata de una pila de la aplicación artificial, cada actividad sintético no tendrán el estado guardado que tendría una actividad natural. Para proporcionar el estado guardado en una actividad primaria sintético, puede reemplazar una actividad el `OnPrepareNavigationUpTaskStack` método. Este método recibe un `TaskStackBuilder` objetos de instancia que tendrá una colección de la intención de que Android utilizará para crear la pila de retroceso. La actividad puede modificar estas calidades de modo que, tal y como se crea la actividad sintética, recibirá la información de estado correcto.

Para escenarios más complejos, existen nuevos métodos en la clase de actividad que puede utilizarse para controlar el comportamiento de la navegación y construir la pila de retroceso:

-   `OnNavigateUp` : Al reemplazar este método es posible realizar una acción personalizada cuando el <span class="ui">seguridad</span> se presiona el botón.
-   `NavigateUpTo` : Llamar a este método hará que la aplicación navegar desde la actividad actual a la actividad especificada por un propósito determinado.
-   `ParentActivityIntent` : Se utiliza para obtener un propósito que se iniciará la actividad primaria de la actividad actual.
-   `ShouldUpRecreateTask` : Este método se utiliza para consultar si se debe crear la pila de retroceso sintética para navegar hasta una actividad primaria. Devuelve `true` si se debe crear la pila sintética. 
-   `FinishAffinity` : Llamar a este método finalizará la actividad actual y todas las actividades por debajo de él en la tarea actual que tienen el mismo afinidad de tarea.
-   `OnCreateNavigateUpTaskStack` : Este método se invalida cuando es necesario tener un control completo sobre cómo se crea la pila sintética.


 <a name="Camera" />


### <a name="camera"></a>Cámara

Hay una nueva interfaz, `Camera.IAutoFocusMoveCallback`, que puede utilizarse para detectar cuándo se inicia o se detiene mover el foco automáticamente. Un ejemplo de esta nueva interfaz puede verse en el fragmento siguiente:

```csharp
public class AutoFocusCallbackActivity : Activity, Camera.IAutoFocusCallback
{
    public void OnAutoFocus(bool success, Camera camera)
    {
        // camera is an instance of the camera service object.

        if (success)
        {
            // Auto focus was successful - do something here.
        }
        else
        {
            // Auto focus didn't happen for some reason - react to that here.
        }
    }
}
```

La nueva clase `MediaActionSound` proporciona un conjunto de API para producir sonidos para las diversas acciones de medios. Existen varias acciones que pueden producirse con una cámara, se definen por la enumeración `Android.Media.MediaActionSoundType`:

-   `MediaActionSoundType.FocusComplete` : Este sonido que se reproduce cuando haya finalizado el establecimiento del foco.
-   `MediaActionSoundType.ShutterClick` – Este sonido se reproducirá cuando se toma una imagen de imagen fija.
-   `MediaActionSoundType.StartVideoRecording` – Se utiliza este sonido indicar el inicio de la grabación de vídeo.
-   `MediaActionSoundType.StopVideoRecording` – Este sonido se reproducirá para indicar el fin de grabación de vídeo.


Un ejemplo de cómo usar la `MediaActionSound` clase puede verse en el fragmento siguiente:

```csharp
var mediaActionPlayer = new MediaActionSound();

// Preload the sound for a shutter click.
mediaActionPlayer.Load(MediaActionSoundType.ShutterClick);
var button = FindViewById<Button>(Resource.Id.MyButton);

// Play the sound on a button click.
button.Click += (sender, args) => mediaActionPlayer.Play(MediaActionSoundType.ShutterClick);

// This releases the preloaded resources. Don’t make any calls on
// mediaActionPlayer after this.
mediaActionPlayer.Release();
```

 <a name="Connectivity" />


### <a name="connectivity"></a>Conectividad

 <a name="Android_Beam" />


#### <a name="android-beam"></a>Haz Android

Haz Android es una tecnología de NFC en función que permite que los dos dispositivos Android para comunicarse entre sí. Android 4.1 ofrece compatibilidad mejorada para la transferencia de archivos de gran tamaño. Cuando se usa el nuevo método `NfcAdapter.SetBeamPushUris()` Android cambiará entre los mecanismos de transporte alternativo (como Bluetooth) para lograr una velocidad de transferencia rápida.

 <a name="Network_Services_Discovery" />


#### <a name="network-services-discovery"></a>Detección de servicios de red

Android 4.1 contiene nuevas API para la detección de servicio multidifusión basadas en DNS.
Esto permite que una aplicación detectar y conectarse a través de Wi-Fi con otros dispositivos, como impresoras y cámaras, dispositivos de medios. Estas API nueva que se encuentran en el `Android.Net.Nsd` paquete.

Para crear un servicio que se puede utilizar en otros servicios, la `NsdServiceInfo` clase se utiliza para crear un objeto que se va a definir las propiedades de un servicio. A continuación, se proporciona este objeto a `NsdManager.RegisterService()` junto con una implementación de `NsdManager.ResolveListener`. Las implementaciones de `NsdManager.ResolveListener` se usan para notificar un registro correcto y anular el registro del servicio.

Detectar los servicios de la red y la implementación de `Nsd.DiscoveryListener` pasado a `NsdManager.discoverServices()`.

 <a name="Network_Usage" />


#### <a name="network-usage"></a>Uso de red

Un nuevo método `ConnectivityManager.IsActiveNetworkMetered` permite que un dispositivo comprobar si está conectado a una red de uso medido. Este método puede utilizarse para ayudar a administrar el uso de datos con precisión informando a los usuarios que puede haber cargos costosos para las operaciones de datos.

 <a name="WiFi_Direct_Service_Discovery" />


#### <a name="wifi-direct-service-discovery"></a>Detección de servicios directos de Wi-Fi

El `WifiP2pManager` clase se introdujo en Android 4.0 para admitir *zeroconf*. Zeroconf (cero redes de configuración) es un conjunto de técnicas que permite a los dispositivos (equipos, impresoras, teléfonos) para conectarse a redes automáticamente, con la intervención de los operadores de red humano o servidores de configuración especial.

En jalea Bean, `WifiP2pManager` puede detectar dispositivos mediante cercanos *Bonjour* o *Upnp*. ¡Bonjour es la implementación de Apple zeroconf. UPnP es un conjunto de protocolos de red que también es compatible con zeroconf. Los métodos siguientes agregarlos a la `WiFiP2pManager` para admitir la detección de servicios de Wi-Fi:

-   `AddLocalService()` : Este método se usa anunciar una aplicación como un servicio a través de Wi-Fi para la detección por pares.
-   `AddServiceRequest(` ): Este método consiste en enviar una solicitud de detección de servicio para el marco de trabajo. Se utiliza para inicializar la detección de servicios de Wi-Fi.
-   `SetDnsSdResponseListeners()` : Este método se usa para registrar las devoluciones de llamada que se invocará en recibir una respuesta a las solicitudes de detección de Bonjour.
-   `SetUpnpServiceResponseListener()` : Este método se usa para registrar las devoluciones de llamada que se invocará en recibir una respuesta a solicitudes de detección de Upnp.


 <a name="Content_Providers" />


### <a name="content-providers"></a>Proveedores de contenido

El `ContentResolver` clase ha recibido un nuevo método, `AcquireUnstableContentProvider`. Este método permite que una aplicación adquirir un proveedor de contenido "inestable". Normalmente, cuando una aplicación adquiere un proveedor de contenido y se bloquea ese proveedor de contenido, por lo que tendrá la aplicación. Con esta llamada de método, una aplicación no se bloqueará si el proveedor de contenido se bloquea. En su lugar, `Android.OS.DeadObjectionException` se producirá de llamadas en el proveedor de contenido para informar a una aplicación que ha desaparecido el proveedor de contenido. Un proveedor de contenido "inestable" es útil al interactuar con proveedores de contenido de otras aplicaciones: es menos probable que el código con errores desde otra aplicación afecte a otra aplicación.

 <a name="Copy_and_Paste_With_Intents" />


### <a name="copy-and-paste-with-intents"></a>Copiar y pegar con propósitos

El `Intent` ahora la clase puede tener un `ClipData` objeto asociado a él a través de la `Intent.ClipData` propiedad. Este método permite datos adicionales desde el Portapapeles para transmitir con la intención. Una instancia de `ClipData` puede contener uno o varios `ClipData.Item`. `ClipData.Item`de elementos de los siguientes tipos:

-   **Texto** : se trata de cualquier cadena de texto, cualquier HTML o abarca cualquier cadena cuyo formato es compatible con el estilo de Android integrado.
-  **Intención** : cualquiera `Intent` objeto.
-   **URI** : Esto puede ser cualquier URI, como un marcador HTTP o el URI para un proveedor de contenido.


 <a name="Isolated_Services" />


### <a name="isolated-services"></a>Servicios aislado

Un servicio aislado es un servicio que se ejecuta en su propio proceso especial y no tiene ningún permiso de su propio. Es la única comunicación con el servicio al iniciar el servicio y enlazar a él a través de la API del servicio. Es posible declarar un servicio como aislado estableciendo la propiedad `IsolatedProcess="true"` en el `ServiceAttribute` que adorna una clase de servicio.

 <a name="Media" />


### <a name="media"></a>Multimedia

El nuevo `Android.Media.MediaCodec` clase proporciona una API para códecs de bajo nivel medio. Las aplicaciones pueden consultar el sistema para averiguar qué códecs de bajo niveles están disponibles en el dispositivo.

El nuevo `Android.Media.Audiofx.AudioEffect` subclases se agregaron para admitir el procesamiento previo en audio capturado de audio adicional:

-   `Android.Media.Audiofx.AcousticEchoCanceler` : Esta clase se utiliza para procesamiento previo de audio para quitar la señal de una parte remota de una señal de audio capturada. Por ejemplo, quitando el eco de una aplicación de comunicación de voz.
-   `Android.Media.Audiofx.AutomaticGainControl` : Esta clase se utiliza para normalizar la señal capturada por aumentar o reducir una señal de entrada para que la señal de salida es constante.
-   `Android.Media.Audiofx.NoiseSuppressor` : Esta clase quitará el ruido de fondo de la señal capturada.


No todos los dispositivos será compatible con estos efectos. El método `AudioEffect.IsAvailable` debe ser llamado por una aplicación para ver si el efecto de audio en cuestión se admite en el dispositivo que ejecuta la aplicación.

El `MediaPlayer` clase ahora es compatible con la reproducción huecos con la `SetNextMediaPlayer()` método. Este nuevo método especifica MediaPlayer siguiente para iniciar cuando el Reproductor multimedia actual finalice su reproducción.

Las siguientes clases nuevas proporcionan mecanismos estándar y la interfaz de usuario para la selección de donde se van a reproducir multimedia:

-   `MediaRouter` : Esta clase permite a las aplicaciones controlar el enrutamiento de canales de medios desde un dispositivo para altavoces externos u otros dispositivos.
-   `MediaRouterActionProvider` y `MediaRouteButton` : estas clases ayudan a proporcionar una interfaz de usuario coherente para seleccionar y reproducir archivos multimedia.


 <a name="Notifications" />


### <a name="notifications"></a>Notificaciones

Android 4.1 permite a las aplicaciones más flexibilidad y control con la presentación de las notificaciones. Las aplicaciones ahora pueden mostrar notificaciones más grande y mejores a los usuarios. Un nuevo método `NotificationBuilder.SetStyle()` permite uno de tres nuevos nuevo estilo que se establecerá en las notificaciones:

-   `Notification.BigPictureStyle` : Es una clase auxiliar que genera notificaciones que tendrán una imagen en ellos. La siguiente imagen muestra un ejemplo de una notificación con una imagen grande:


 [ ![Captura de pantalla de ejemplo de una notificación de BigPictureStyle](jelly-bean-images/image2.png)](jelly-bean-images/image2.png)

-   `Notification.BigTextStyle` : Es una clase auxiliar que genera notificaciones que tendrán varias líneas de texto, como el correo electrónico. Un ejemplo de este nuevo tipo de notificación se puede ver en la captura de pantalla siguiente:


 [ ![Captura de pantalla de ejemplo de una notificación de BigTextStyle](jelly-bean-images/image3.png)](jelly-bean-images/image3.png)

-   `Notification.InboxStyle` – Ésta es una clase auxiliar que genera notificaciones que contienen una lista de cadenas, como fragmentos de código de un mensaje de correo electrónico, como se muestra en esta captura de pantalla:


 [ ![Captura de pantalla de ejemplo de una notificación de Notification.InboxStyle](jelly-bean-images/image4.png)](jelly-bean-images/image4.png)

Es posible agregar hasta dos botones de acción en la parte inferior de un mensaje de notificación cuando la notificación está usando el estilo normal o superior.
Un ejemplo de esto se puede ver en la captura de pantalla siguiente, donde los botones de acción son visibles en la parte inferior de la notificación:

 [ ![Captura de pantalla de ejemplo de botones de acción que se muestran a continuación un mensaje de notificación](jelly-bean-images/image5.png)](jelly-bean-images/image5.png)

La `Notification` clase ha recibido nuevas constantes que permiten a un desarrollador especificar uno de los cinco niveles de prioridad para una notificación. Estos se pueden establecer en una notificación mediante el `Priority` propiedad.

 <a name="Permissions" />


### <a name="permissions"></a>Permisos

Se han agregado los siguientes permisos nuevos:

-   `READ_EXTERNAL_STORAGE` -La aplicación requiere acceso de solo lectura en un almacenamiento externo. Actualmente todas las aplicaciones tienen acceso de lectura de forma predeterminada, pero las versiones futuras de Android requerirá aplicaciones solicitan explícitamente el acceso de lectura.
-   `READ_USER_DICTIONARY` : Permite un acceso de lectura al diccionario de palabras del usuario.
-   `READ_CALL_LOG` : Permite que una aplicación obtener información acerca de las llamadas entrantes y salientes, leer el registro de la llamada.
-   `WRITE_CALL_LOG` : Permite que una aplicación escribir en el registro de llamada en el teléfono.
-   `WRITE_USER_DICTIONARY` : Permite que una aplicación escribir en el diccionario de palabras del usuario.


Un cambio importante tener en cuenta `READ_EXTERNAL_STORAGE` : actualmente este permiso se concede automáticamente Android. Las futuras versiones de Android requerirá una aplicación para solicitar este permiso antes de conceder el permiso.

 <a name="Summary" />


## <a name="summary"></a>Resumen

En este artículo se introdujo algunas de las API nuevas que están disponibles en Android 4.1 (API nivel 16). Resaltan algunos de los cambios de animaciones y animar el inicio de una actividad y se introduce la nueva API de detección de redes de otros dispositivos mediante protocolos como Bonjour o UPnP. Otros cambios en la API se resaltan, como la capacidad para cortar y pegar los datos a través del color, la capacidad de utilizar servicios aislado o proveedores de contenido "inestables".

En este artículo, a continuación, se dirigió introducir las actualizaciones a las notificaciones y trata algunos de los nuevos permisos que se han introducido con Android 4.1


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de animación de tiempo (ejemplo)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/TimeAnimatorExample/)
- [Android 4.1 API](http://developer.android.com/about/versions/android-4.1.html)
- [Tareas y pilas atrás](http://developer.android.com/guide/components/tasks-and-back-stack.html)
- [Navegación con atrás y arriba](http://developer.android.com/design/patterns/navigation.html)
