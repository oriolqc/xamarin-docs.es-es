---
title: Jelly Bean características
description: 'Este documento proporcionará una introducción de alto nivel de las nuevas características para desarrolladores que se introdujeron en Android 4.1. Estas características incluyen: mejorada de las notificaciones, las actualizaciones a Android carretera para compartir archivos de gran tamaño, las actualizaciones de detección de redes de multimedia, peer-to-peer, animaciones, nuevos permisos.'
ms.prod: xamarin
ms.assetid: 23F57634-2EF9-5C15-C710-B3E19A5AF7E1
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 24fc14b0342591c56f5bf91862b0d94759a42834
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670108"
---
# <a name="jelly-bean-features"></a>Jelly Bean características

_Este documento proporcionará una introducción de alto nivel de las nuevas características para desarrolladores que se introdujeron en Android 4.1. Estas características incluyen: mejorada de las notificaciones, las actualizaciones a Android carretera para compartir archivos de gran tamaño, las actualizaciones de detección de redes de multimedia, peer-to-peer, animaciones, nuevos permisos._



## <a name="overview"></a>Información general

Android 4.1 (nivel de API 16), también conocido como "Jelly Bean", fue la versión 9 de julio de 2012. Este artículo ofrecerá una introducción de alto nivel a algunas de las nuevas características de Android 4.1 para los desarrolladores de Xamarin.Android. Algunas de estas nuevas características introducidas son mejoras a las animaciones para iniciar una actividad, nuevos sonidos de una cámara y compatibilidad mejorada para la navegación de la pila de aplicación. Ahora es posible cortar y pegar con intenciones.

Se ha mejorado la estabilidad de las aplicaciones de Android con la capacidad de aislar la dependencia de los proveedores de contenido inestables. También puede aislarse los servicios para que sean accesibles solo por la actividad que ha iniciado.

Ha agregado compatibilidad para la detección de servicio de red mediante servicios basados en DNS de multidifusión, UPnP o Bonjour. Ahora es posible para las notificaciones más completas que han aplicado formato a texto, botones de acción e imágenes de gran tamaño.

Por último se han agregado varios permisos nuevo en Android 4.1.



## <a name="requirements"></a>Requisitos

Para desarrollar aplicaciones de Xamarin.Android utilizando Jelly Bean requiere Xamarin.Android 4.2.6 o superior y Android 4.1 (nivel de API 16) instalarse a través de Android SDK Manager como se muestra en la siguiente captura de pantalla:

[![Seleccionar Android 4.1 en Android SDK Manager](jelly-bean-images/image1.png)](jelly-bean-images/image1.png#lightbox)



## <a name="whats-new"></a>Novedades



### <a name="animations"></a>Animaciones

Las actividades se pueden iniciar utilizando las animaciones de zoom o animaciones personalizadas utilizando el `ActivityOptions` clase. Se proporcionan los siguientes métodos nuevos para admitir estas animaciones:

-   `MakeScaleUpAnimation` : Se crea una animación que se amplía una ventana de actividad desde una posición inicial y el tamaño de la pantalla.
-   `MakeThumbnailScaleUpAnimation` : Se crea una animación que se escala verticalmente desde una imagen en miniatura de la posición especificada en la pantalla.
-   `MakeCustomAnimation` : Se crea una animación de los recursos de la aplicación. Hay una animación para cuando se abra la actividad y otra para cuando se detenga la actividad.


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

Como el `TimeAnimator` instancia se está ejecutando, éste invocará `ITimeAnimator.ITimeListener`, que, a continuación, se registrará el modo de cuánto tiempo ha sido el animador ejecución y cuánto tiempo tal como se ha desde la última vez el método se ha invocado.



### <a name="application-stack-navigation"></a>Navegación de la pila de aplicación

Android 4.1 mejora la navegación de la pila de aplicación que se introdujo en Android 3.0. Especificando el `ParentName` propiedad de la `ActivityAttribute`, Android puede abrir la actividad primaria adecuada cuando el usuario presiona el [botón](https://developer.android.com/design/patterns/navigation.html#up-vs-back) en la barra de acción - Android creará una instancia de la actividad especificada por el `ParentName`propiedad. Esto permite que las aplicaciones conservar la jerarquía de actividades que realizan una tarea determinada.

Para la mayoría de las aplicaciones establecer el `ParentName` en la actividad es información suficiente para Android proporcionar el comportamiento correcto para navegar por la pila de aplicaciones; Android la síntesis de la pila de retroceso necesaria mediante la creación de una serie de intentos para cada actividad primaria. Sin embargo, puesto que es una pila de aplicaciones artificial, cada actividad sintético no tendrá el estado guardado que tendría una actividad natural. Para proporcionar el estado guardado en una actividad primaria sintético, una actividad puede invalidar el `OnPrepareNavigationUpTaskStack` método. Este método recibe un `TaskStackBuilder` los objetos de instancia que tendrá una colección de intención que Android usará para crear la pila de retroceso. La actividad puede modificar estos intentos para que, al crear la actividad sintética, recibirá la información de estado adecuado.

Para escenarios más complejos, existen nuevos métodos en la clase de actividad que puede utilizarse para controlar el comportamiento de navegación y construir la pila de retroceso:

-   `OnNavigateUp` : Al reemplazar este método es posible llevar a cabo una acción personalizada cuando el <span class="ui">seguridad</span> está presionado.
-   `NavigateUpTo` : Llamar a este método hará que la aplicación navegar desde la actividad actual a la actividad especificada por un propósito determinado.
-   `ParentActivityIntent` : Se utiliza para obtener una intención que se iniciará la actividad primaria de la actividad actual.
-   `ShouldUpRecreateTask` : Este método se usa para consultar si se debe crear la pila de retroceso sintética para navegar hasta una actividad primaria. Devuelve `true` si se debe crear la pila sintética. 
-   `FinishAffinity` : Llamar a este método finalizará la actividad actual y todas las actividades debajo de él en la tarea actual que tengan el mismo afinidad de tarea.
-   `OnCreateNavigateUpTaskStack` : Este método se invalida cuando es necesario tener control completo sobre cómo se crea la pila sintética.




### <a name="camera"></a>Cámara

Hay una nueva interfaz, `Camera.IAutoFocusMoveCallback`, que puede usarse para detectar cuándo se ha iniciado o detenido mover el foco automáticamente. Un ejemplo de esta nueva interfaz puede verse en el siguiente fragmento de código:

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

La nueva clase `MediaActionSound` proporciona un conjunto de API para generar sonidos para las diversas acciones de medios. Existen varias acciones que pueden producirse con una cámara, se definen mediante la enumeración `Android.Media.MediaActionSoundType`:

-   `MediaActionSoundType.FocusComplete` : Este sonido que se reproduce cuando centrándose se ha completado.
-   `MediaActionSoundType.ShutterClick` – Este sonido se reproducirá cuando se toma una imagen de imagen fija.
-   `MediaActionSoundType.StartVideoRecording` – Este sonido se usa indicar el inicio de la grabación de vídeo.
-   `MediaActionSoundType.StopVideoRecording` – Este sonido se reproducirá para indicar el final de la grabación de vídeo.


Un ejemplo de cómo usar el `MediaActionSound` clase puede verse en el siguiente fragmento de código:

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



### <a name="connectivity"></a>Conectividad



#### <a name="android-beam"></a>Android Beam

Haz de Android es una tecnología de NFC en función que permite que dos de los dispositivos Android se comuniquen entre sí. Android 4.1 ofrece compatibilidad mejorada para la transferencia de archivos de gran tamaño. Cuando se usa el nuevo método `NfcAdapter.SetBeamPushUris()` Android cambiará entre los mecanismos de transporte alternativo (por ejemplo, Bluetooth) para lograr una velocidad de transferencia rápida.



#### <a name="network-services-discovery"></a>Detección de servicios de red

Android 4.1 contiene nuevas API para la detección de servicio multidifusión basados en DNS.
Esto permite que una aplicación detectar y conectarse a través de Wi-Fi para otros dispositivos como impresoras, cámaras y dispositivos de medios. Estas API nuevas que se encuentran en el `Android.Net.Nsd` paquete.

Para crear un servicio que se puede utilizar en otros servicios, el `NsdServiceInfo` clase se utiliza para crear un objeto que se van a definir las propiedades de un servicio. Este objeto, a continuación, se proporciona para `NsdManager.RegisterService()` junto con una implementación de `NsdManager.ResolveListener`. Las implementaciones de `NsdManager.ResolveListener` sirven para notificarle de un registro correcto y anular el registro del servicio.

Para detectar servicios en la red y la implementación de `Nsd.DiscoveryListener` pasa a `NsdManager.discoverServices()`.



#### <a name="network-usage"></a>Uso de red

Un nuevo método `ConnectivityManager.IsActiveNetworkMetered` permite que un dispositivo comprobar si está conectado a una red de uso medido. Este método puede utilizarse para ayudar a administrar el uso de datos con precisión informando a los usuarios que puede haber cargos costos para las operaciones de datos.



#### <a name="wifi-direct-service-discovery"></a>Detección de servicios directos de Wi-Fi

El `WifiP2pManager` clase se introdujo en Android 4.0 para admitir *zeroconf*. Zeroconf (cero redes configuración) es un conjunto de técnicas que permite a los dispositivos (equipos, impresoras, teléfonos) para conectarse a redes automáticamente, con la intervención de los operadores humanos o servidores de configuración especial.

En Jelly Bean, `WifiP2pManager` puede detectar dispositivos mediante cercanos *Bonjour* o *Upnp*. Bonjour es la implementación de Apple de zeroconf. UPnP es un conjunto de protocolos de red que también admite zeroconf. Los métodos siguientes agregarlos a la `WiFiP2pManager` para admitir la detección de servicios de Wi-Fi:

-   `AddLocalService()` : Este método se usa anunciar una aplicación como un servicio a través de Wi-Fi para la detección de equipos del mismo nivel.
-   `AddServiceRequest(` ): Este método consiste en enviar una solicitud de detección de servicio para el marco de trabajo. Sirve para inicializar la detección de servicios de Wi-Fi.
-   `SetDnsSdResponseListeners()` : Este método se usa para registrar devoluciones de llamada que se invocará cuando se reciba una respuesta a solicitudes de detección de Bonjour.
-   `SetUpnpServiceResponseListener()` : Este método se usa para registrar devoluciones de llamada que se invocará cuando se reciba una respuesta a solicitudes de detección Upnp.




### <a name="content-providers"></a>Proveedores de contenido

El `ContentResolver` clase recibió un nuevo método `AcquireUnstableContentProvider`. Este método permite que una aplicación adquirir un proveedor de contenido "inestable". Normalmente, cuando una aplicación adquiere un proveedor de contenido, y ese proveedor de contenido se bloquea, por lo que tendrá la aplicación. Con esta llamada al método, una aplicación no se bloqueará si el proveedor de contenido se bloquea. En su lugar, `Android.OS.DeadObjectionException` se iniciará desde las llamadas en el proveedor de contenido para informar a una aplicación que el proveedor de contenido que haya desparecido. Un proveedor de contenido "inestable" es útil cuando se interactúa con proveedores de contenido de otras aplicaciones, es menos probable que el código defectuoso desde otra aplicación afecte a otra aplicación.



### <a name="copy-and-paste-with-intents"></a>Copiar y pegar con intenciones

El `Intent` clase puede tener ahora un `ClipData` objeto asociado a él a través de la `Intent.ClipData` propiedad. Este método permite datos adicionales desde el Portapapeles para transmitir con la intención. Una instancia de `ClipData` puede contener uno o varios `ClipData.Item`. `ClipData.Item`de elementos de los siguientes tipos:

-   **Texto** : se trata de cualquier cadena de texto, cualquier HTML o cualquier cadena cuyo formato es compatible con el estilo de Android integrado abarca.
-  **Intención** : cualquiera `Intent` objeto.
-   **URI** : Esto puede ser cualquier URI, como un marcador HTTP o el URI para un proveedor de contenido.




### <a name="isolated-services"></a>Servicios aislado

Un servicio aislado es un servicio que se ejecuta en su propio proceso especial y no tiene permisos de su propio. Es la única comunicación con el servicio al iniciarse el servicio y enlazar a él a través de la API del servicio. Es posible declarar un servicio como aislado estableciendo la propiedad `IsolatedProcess="true"` en el `ServiceAttribute` que adorna una clase de servicio.


### <a name="media"></a>Multimedia

El nuevo `Android.Media.MediaCodec` clase proporciona una API para códecs de bajo nivel medio. Las aplicaciones pueden consultar el sistema para averiguar qué códecs de bajo niveles están disponibles en el dispositivo.

El nuevo `Android.Media.Audiofx.AudioEffect` subclases se han agregado para admitir el procesamiento previo en audio capturado de audio adicional:

-   `Android.Media.Audiofx.AcousticEchoCanceler` : Esta clase se utiliza para procesamiento previo de audio para quitar la señal de una parte remota de una señal de audio capturada. Por ejemplo, quitando el eco de una aplicación de comunicación de voz.
-   `Android.Media.Audiofx.AutomaticGainControl` : Esta clase se utiliza para normalizar la señal capturada por aumentar o reducir una señal de entrada para que la señal de salida es constante.
-   `Android.Media.Audiofx.NoiseSuppressor` : Esta clase quitará el ruido de fondo de la señal capturada.


No todos los dispositivos será compatible con estos efectos. El método `AudioEffect.IsAvailable` se debe llamar a una aplicación para ver si se admite el efecto de audio en cuestión en el dispositivo que ejecuta la aplicación.

El `MediaPlayer` clase ahora admite la reproducción de huecos con el `SetNextMediaPlayer()` método. Este nuevo método especifica MediaPlayer siguiente que se iniciará cuando el Reproductor de medios actual termine su reproducción.

Las siguientes clases nuevas proporcionan mecanismos estándar y la interfaz de usuario para seleccionar dónde se van a reproducir multimedia:

-   `MediaRouter` : Esta clase permite que las aplicaciones controlar el enrutamiento de canales de medios desde un dispositivo de altavoces externos u otros dispositivos.
-   `MediaRouterActionProvider` y `MediaRouteButton` – estas clases ayudan a proporcionar una interfaz de usuario coherente para seleccionar y reproducir archivos multimedia.




### <a name="notifications"></a>Notificaciones

Android 4.1 permite que las aplicaciones más flexibilidad y control con la presentación de las notificaciones. Las aplicaciones ahora pueden mostrar notificaciones más grande y mejores a los usuarios. Un nuevo método `NotificationBuilder.SetStyle()` permite que uno de tres nuevos nuevo estilo se puede establecer en las notificaciones:

-   `Notification.BigPictureStyle` : Es una clase auxiliar que genera notificaciones que tendrán una imagen en ellos. La siguiente imagen muestra un ejemplo de una notificación con una imagen grande:


 [![Captura de pantalla de ejemplo de una notificación BigPictureStyle](jelly-bean-images/image2.png)](jelly-bean-images/image2.png#lightbox)

-   `Notification.BigTextStyle` : Es una clase auxiliar que genera notificaciones que tendrán varias líneas de texto, como el correo electrónico. Un ejemplo de este nuevo estilo de notificación puede verse en la captura de pantalla siguiente:


 [![Captura de pantalla de ejemplo de una notificación BigTextStyle](jelly-bean-images/image3.png)](jelly-bean-images/image3.png#lightbox)

-   `Notification.InboxStyle` : Es una clase auxiliar que genera notificaciones que contienen una lista de cadenas, por ejemplo, fragmentos de código desde un mensaje de correo electrónico, como se muestra en esta captura de pantalla:


 [![Captura de pantalla de ejemplo de una notificación Notification.InboxStyle](jelly-bean-images/image4.png)](jelly-bean-images/image4.png#lightbox)

Es posible agregar un máximo de dos botones de acción en la parte inferior de un mensaje de notificación cuando la notificación está utilizando el estilo normal o superior.
Un ejemplo de esto se aprecia en la captura de pantalla siguiente, donde los botones de acción son visibles en la parte inferior de la notificación:

 [![Captura de pantalla de ejemplo de botones de acción aparece debajo de un mensaje de notificación](jelly-bean-images/image5.png)](jelly-bean-images/image5.png#lightbox)

La `Notification` clase ha recibido nuevas constantes que permiten a un desarrollador especificar uno de los cinco niveles de prioridad para una notificación. Estos se pueden establecer en una notificación mediante el `Priority` propiedad.



### <a name="permissions"></a>Permisos

Se han agregado los siguientes permisos nuevos:

-   `READ_EXTERNAL_STORAGE` -La aplicación requiere acceso de solo lectura en un almacenamiento externo. Actualmente todas las aplicaciones tienen acceso de lectura de forma predeterminada, pero las versiones futuras de Android requerirán aplicaciones solicitan explícitamente el acceso de lectura.
-   `READ_USER_DICTIONARY` : Permite un acceso de lectura al diccionario de word del usuario.
-   `READ_CALL_LOG` -Permite que una aplicación obtener información sobre las llamadas entrantes y salientes por leer el registro de la llamada.
-   `WRITE_CALL_LOG` -Permite que una aplicación escribir en el registro de llamadas en el teléfono.
-   `WRITE_USER_DICTIONARY` -Permite que una aplicación escribir en el diccionario de palabras del usuario.


Un cambio importante tener en cuenta `READ_EXTERNAL_STORAGE` : actualmente este permiso se concede automáticamente por Android. Las futuras versiones de Android requerirá una aplicación para solicitar este permiso antes de conceder el permiso.



## <a name="summary"></a>Resumen

Este artículo presenta algunas de las API nuevas que están disponibles en Android 4.1 (nivel de API 16). Se resaltan algunos de los cambios de las animaciones y animar el lanzamiento de una actividad y se introdujo la nueva API detección de redes de otros dispositivos mediante protocolos como Bonjour o UPnP. Otros cambios en la API se han resaltado también, como la capacidad para cortar y pegar los datos a través de intenciones, la capacidad de usar servicios aislado o proveedores de contenido "inestables".

En este artículo, a continuación, se pasa a introducir las actualizaciones a las notificaciones y trata algunos de los nuevos permisos que se han introducido con Android 4.1


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de animación de tiempo (ejemplo)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/TimeAnimatorExample/)
- [Android 4.1 API](https://developer.android.com/about/versions/android-4.1.html)
- [Tareas y pilas atrás](https://developer.android.com/guide/components/tasks-and-back-stack.html)
- [Navegación con atrás y arriba](https://developer.android.com/design/patterns/navigation.html)
