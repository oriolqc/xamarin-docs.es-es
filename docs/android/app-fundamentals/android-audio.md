---
title: Android Audio
description: El sistema operativo Android proporciona amplia compatibilidad para multimedia, que abarca de audio y vídeo. Esta guía se centra en audio en Android y reproducir y grabar audio mediante las clases de la grabadora y el Reproductor de audio integrado, así como la API de audio de bajo nivel. También explica cómo trabajar con eventos de Audio de difusión por otras aplicaciones, para que los desarrolladores pueden crear aplicaciones y se comporten correctamente.
ms.prod: xamarin
ms.assetid: 646ED563-C34E-256D-4B56-29EE99881C27
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: a1a9dd06fb3cd6899dd3a564072bb63e413edf22
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667573"
---
# <a name="android-audio"></a>Android Audio

_El sistema operativo Android proporciona amplia compatibilidad para multimedia, que abarca de audio y vídeo. Esta guía se centra en audio en Android y reproducir y grabar audio mediante las clases de la grabadora y el Reproductor de audio integrado, así como la API de audio de bajo nivel. También explica cómo trabajar con eventos de Audio de difusión por otras aplicaciones, para que los desarrolladores pueden crear aplicaciones y se comporten correctamente._


## <a name="overview"></a>Información general

Los dispositivos móviles modernos han adoptado la funcionalidad que antes habría requerido dedicados partes de equipment &ndash; cámaras, reproductores de música y grabadores de vídeo. Por este motivo, marcos de trabajo multimedios se han convertido en una característica preferente en API móviles.

Android proporciona amplia compatibilidad para archivos multimedia. En este artículo examina cómo trabajar con audio en Android y se trata los temas siguientes

1.  **Reproducción de Audio con MediaPlayer** &ndash; usa el método integrado `MediaPlayer` clase reproducir audio, incluidos los archivos de audio locales y los archivos de audio por secuencias con el `AudioTrack` clase.

2.  **Grabación de Audio** &ndash; usa el método integrado `MediaRecorder` clase grabar audio.

3.  **Trabajar con las notificaciones de sonido** &ndash; mediante notificaciones de sonido para crear aplicaciones con buen comportamiento sacarán que respondan correctamente a eventos (como llamadas telefónicas entrantes) mediante la suspensión o cancelación de sus salidas de audio.

4.  **Trabajar con Audio de bajo nivel** &ndash; reproducir audio con la `AudioTrack` clase escribiendo directamente en los búferes de memoria. Grabación de audio utilizando el `AudioRecord` clase y la lectura directamente desde los búferes de memoria.


## <a name="requirements"></a>Requisitos

Esta guía requiere Android 2.0 (API nivel 5) o superior. Tenga en cuenta que se debe realizar la depuración de audio en Android en un dispositivo.

Es necesario para la solicitud la `RECORD_AUDIO` permisos en **AndroidManifest.XML**:

![Requiere la sección permisos de manifiesto de Android con registro\_AUDIO habilitado](android-audio-images/image01.png)



## <a name="playing-audio-with-the-mediaplayer-class"></a>Reproducción de Audio con la clase MediaPlayer

Es la manera más sencilla de reproducir audio en Android con integrado [MediaPlayer](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/) clase.
`MediaPlayer` puede reproducir archivos locales o remotos pasando la ruta de acceso de archivo. Sin embargo, `MediaPlayer` muy distinguen el estado y llamar a uno de sus métodos en un estado incorrecto se producirá una excepción que se produzca. Es importante interactuar con `MediaPlayer` en el orden en que se describe a continuación para evitar errores.



### <a name="initializing-and-playing"></a>Inicializar y reproducción en curso

La reproducción de audio con `MediaPlayer` requiere la siguiente secuencia:

1. Crear una nueva instancia [MediaPlayer](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/) objeto.

1. Configurar el archivo para reproducir a través de la [SetDataSource](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.SetDataSource/p/Java.IO.FileDescriptor/) método.

1. Llame a la [preparar](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Prepare/) método para inicializar el Reproductor.

1. Llame a la [iniciar](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Start/) método para iniciar la reproducción de audio.


El ejemplo de código siguiente muestra este uso:

```csharp
protected MediaPlayer player;
public void StartPlayer(String  filePath)
{
  if (player == null) {
    player = new MediaPlayer();
  } else {
    player.Reset();
    player.SetDataSource(filePath);
    player.Prepare();
    player.Start();
  }
}
```


### <a name="suspending-and-resuming-playback"></a>Suspender y reanudar la reproducción

La reproducción puede ser suspendida por una llamada a la [pausar](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Pause%28%29/) método:

```csharp
player.Pause();
```

Para reanudar la reproducción en pausa, llame a la [iniciar](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Start%28%29/) método.
Esto se reanudará desde la ubicación en pausa la reproducción:

```csharp
player.Start();
```

Una llamada a la [detener](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Stop%28%29/) método en el Reproductor finaliza una reproducción en curso:

```csharp
player.Stop();
```

Cuando ya no se necesita el Reproductor, se deben liberar los recursos mediante una llamada a la [versión](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Release%28%29/) método:

```csharp
player.Release();
```



## <a name="using-the-mediarecorder-class-to-record-audio"></a>Uso de la clase MediaRecorder para grabar Audio

El corolario `MediaPlayer` para grabación de audio en Android es el [MediaRecorder](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/) clase. Al igual que el `MediaPlayer`, se distingue del estado y realiza una transición por varios Estados para llegar al punto donde puede iniciar la grabación. Para grabar audio, el `RECORD_AUDIO` se debe establecer el permiso. Para obtener instrucciones sobre cómo establecer la aplicación vea permisos [trabajar con AndroidManifest.xml](~/android/platform/android-manifest.md).


### <a name="initializing-and-recording"></a>Inicializar y grabar

Grabación de audio con la `MediaRecorder` requiere los siguientes pasos:

1. Crear una nueva instancia [MediaRecorder](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/) objeto.

2. Especifique qué dispositivo de hardware para capturar la entrada de audio a través de la [SetAudioSource](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetAudioSource/p/Android.Media.AudioSource/) método.

3. Establecer el formato de audio de archivo de salida mediante la [SetOutputFormat](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetOutputFormat/p/Android.Media.OutputFormat/) método. Para obtener una lista de tipos de audio admitidos consulte [Android admite formatos multimedia](https://developer.android.com/guide/appendix/media-formats.html).

4. Llame a la [SetAudioEncoder](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetAudioEncoder/p/Android.Media.AudioEncoder/) método para establecer el tipo de codificación de audio.

5. Llame a la [SetOutputFile](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetOutputFile/p/System.String/) método para especificar el nombre del archivo de salida que se escriben los datos de audio.

6. Llame a la [preparar](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Prepare%28%29/) método para inicializar la grabadora.

7. Llame a la [iniciar](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Start%28%29/) método para iniciar la grabación.


Ejemplo de código siguiente muestra esta secuencia:

```csharp
protected MediaRecorder recorder;
void RecordAudio (String filePath)
{
  try {
    if (File.Exists (filePath)) {
      File.Delete (filePath);
    }
    if (recorder == null) {
      recorder = new MediaRecorder (); // Initial state.
    } else {
      recorder.Reset ();
      recorder.SetAudioSource (AudioSource.Mic);
      recorder.SetOutputFormat (OutputFormat.ThreeGpp);
      recorder.SetAudioEncoder (AudioEncoder.AmrNb);
      // Initialized state.
      recorder.SetOutputFile (filePath);
      // DataSourceConfigured state.
      recorder.Prepare (); // Prepared state
      recorder.Start (); // Recording state.
    }
  } catch (Exception ex) {
    Console.Out.WriteLine( ex.StackTrace);
  }
}
```


### <a name="stopping-recording"></a>Detener la grabación

Para detener la grabación, llame a la `Stop` método en el `MediaRecorder`:

```csharp
recorder.Stop();
```



### <a name="cleaning-up"></a>Limpiar

Una vez el `MediaRecorder` ha sido detenido, llame a la [restablecer](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Reset%28%29/) método ponerlo de nuevo en su estado de inactividad:

```csharp
recorder.Reset();
```

Cuando el `MediaRecorder` es ya no es necesario, deben liberar sus recursos mediante una llamada a la [versión](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Release%28%29/) método:

```csharp
recorder.Release();
```


## <a name="managing-audio-notifications"></a>Administrar las notificaciones de Audio



### <a name="the-audiomanager-class"></a>La clase AudioManager

El [AudioManager](https://developer.xamarin.com/api/type/Android.Media.AudioManager/) clase proporciona acceso a las notificaciones de sonido que permiten que las aplicaciones saber cuándo se producen los eventos de audio. Este servicio también proporciona acceso a otras características de audio, como control de modo de volumen y el timbre. El `AudioManager` permite que una aplicación controlar las notificaciones de audio para controlar la reproducción de audio.



### <a name="managing-audio-focus"></a>Administración de la selección de Audio

Todas las aplicaciones comparten los recursos de audio del dispositivo (el Reproductor integrado y grabadora).

Conceptualmente, esto es similar a las aplicaciones en un equipo de escritorio donde sólo una aplicación tiene el foco de teclado: después de seleccionar una de las aplicaciones en ejecución haciendo clic de mouse-, la entrada de teclado sólo se dirige a esa aplicación.

Audio foco es un concepto similar y evita que más de una aplicación de reproducción o grabación de audio al mismo tiempo. Es más complicado que el foco del teclado porque es voluntaria &ndash; la aplicación puede pasar por alto este hecho que actualmente no tiene el foco de audio y reproducir independientemente &ndash; y porque hay diferentes tipos de audio enfoque que puede ser solicitado. Por ejemplo, si solo se espera que el solicitante para reproducir el audio de muy poco tiempo, puede solicitar foco transitorio.

Foco audio que puede concederse inmediatamente, o inicialmente denegado y concede más adelante. Por ejemplo, si un aplicación solicitudes foco de audio durante una llamada de teléfono, se denegará, pero el foco también se les puede conceder una vez finalizada la llamada de teléfono. En este caso, un agente de escucha está registrado con el fin de responder en consecuencia si foco audio extraído. Solicitar el foco de audio se utiliza para determinar si es correcto reproducir o grabar audio.

Para obtener más información acerca del foco de audio, consulte [administrar Audio foco](https://developer.android.com/training/managing-audio/audio-focus.html).



#### <a name="registering-the-callback-for-audio-focus"></a>Registrar la devolución de llamada para el enfoque de Audio

Registrar el `FocusChangeListener` devolución de llamada desde el `IOnAudioChangeListener` es una parte importante de obtener y liberar el foco de audio. Esto es porque la concesión del enfoque de audio se puede retrasar hasta un momento posterior. Por ejemplo, una aplicación puede solicitar al reproducir música mientras hay una llamada de teléfono en curso. No se concederá audio foco hasta que finalice la llamada de teléfono.

Por este motivo, el objeto de devolución de llamada se pasa como un parámetro en el `GetAudioFocus` método de la `AudioManager`, y es esta llamada que se registra la devolución de llamada. Si audio foco es denegado inicialmente pero se concede más adelante, se informa a la aplicación mediante la invocación `OnAudioFocusChange` en la devolución de llamada. El mismo método se utiliza para indicar que el foco de audio se ha puesto fuera de la aplicación.

Cuando la aplicación ha terminado de utilizar los recursos de sonido, llama a la `AbandonFocus` método de la `AudioManager`y pasa de nuevo en la devolución de llamada. Esto anula el registro de la devolución de llamada y libera los recursos de audio, de modo que otras aplicaciones pueden obtener foco audio.



#### <a name="requesting-audio-focus"></a>Solicitar el foco de Audio

Los pasos necesarios para solicitar los recursos de audio del dispositivo son los siguientes:

1.  Obtener un identificador para el `AudioManager` servicio del sistema.

2.  Cree una instancia de la clase de devolución de llamada.

3.  Solicitar los recursos de audio del dispositivo mediante una llamada a la `RequestAudioFocus` método en el `AudioManager` . Los parámetros son el objeto de devolución de llamada, el tipo de secuencia (música, llamada de voz, anillo, etc.) y el tipo de acceso al que se solicita directamente (los recursos de sonido pueden solicitar momentáneamente o durante un período indefinido, por ejemplo).

4.  Si se concede la solicitud, el `playMusic` método se invoca de inmediato y el audio empieza a reproducir.

5.  Si se deniega la solicitud, se realiza ninguna otra acción. En este caso, el audio se reproducirá sólo si la solicitud se ha concedido en un momento posterior.


El ejemplo de código siguiente muestra estos pasos:

```csharp
Boolean RequestAudioResources(INotificationReceiver parent)
{
  AudioManager audioMan = (AudioManager) GetSystemService(Context.AudioService);
  AudioManager.IOnAudioFocusChangeListener listener  = new MyAudioListener(this);
  var ret = audioMan.RequestAudioFocus (listener, Stream.Music, AudioFocus.Gain );
  if (ret == AudioFocusRequest.Granted) {
    playMusic();
    return (true);
  } else if (ret == AudioFocusRequest.Failed) {
    return (false);
  }
  return (false);
}
```


#### <a name="releasing-audio-focus"></a>Liberar el foco de Audio

Una vez completada la reproducción de la pista, la `AbandonFocus` método `AudioManager` se invoca. Esto permite que otra aplicación obtener los recursos de audio del dispositivo. Otras aplicaciones recibirán una notificación de este cambio de foco de audio si han registrado sus propios agentes de escucha.


## <a name="low-level-audio-api"></a>API de Audio de nivel bajo

Las API de audio de bajo nivel proporcionan un mayor control sobre la reproducción de audio y grabación porque interactúan directamente con los búferes de memoria en lugar de usar los URI de archivo. Hay algunos escenarios donde este enfoque es preferible. Estos escenarios incluyen:

1.  Al reproducir desde archivos de audio codificados.

2.  Al reproducir una sucesión de clips cortos.

3.  Streaming de audio.


### <a name="audiotrack-class"></a>Clase AudioTrack

El [AudioTrack](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/) clase usa la API de audio de bajo nivel para la grabación y es el equivalente de bajo nivel la `MediaPlayer` clase.


#### <a name="initializing-and-playing"></a>Inicializar y reproducción en curso

Para reproducir el audio, una nueva instancia de `AudioTrack` se deben crear instancias. La lista de argumentos pasado en el [constructor](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/#memberlist) especifica cómo reproducir el audio muestra contenido en el búfer. Los argumentos son:

1.  Stream tipo &ndash; voz, tono, música, sistema o alarmas.

2.  Frecuencia &ndash; la frecuencia de muestreo que se expresa en Hz.

3.  Configuración de canal &ndash; Mono o estéreo.

4.  Formato de audio &ndash; de 8 bits o codificación de 16 bits.

5.  Tamaño del búfer &ndash; en bytes.

6.  Modo de búfer &ndash; streaming o estático.


Después de la construcción del [reproducir](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Play%28%29/) método `AudioTrack` se invoca, para configurarlo para iniciar reproducción. Escribir el búfer de audio para el `AudioTrack` inicia la reproducción:

```csharp
void PlayAudioTrack(byte[] audioBuffer)
{
  AudioTrack audioTrack = new AudioTrack(
    // Stream type
    Stream.Music,
    // Frequency
    11025,
    // Mono or stereo
    ChannelOut.Mono,
    // Audio encoding
    Android.Media.Encoding.Pcm16bit,
    // Length of the audio clip.
    audioBuffer.Length,
    // Mode. Stream or static.
    AudioTrackMode.Stream);

    audioTrack.Play();
    audioTrack.Write(audioBuffer, 0, audioBuffer.Length);
}
```


#### <a name="pausing-and-stopping-the-playback"></a>Pausar y detener la reproducción

Llame a la [pausar](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Pause%28%29/) método para pausar la reproducción:

```csharp
audioTrack.Pause();
```

Una llamada a la [detener](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Stop%28%29/) método finalizará permanentemente la reproducción:

```csharp
audioTrack.Stop();
```


#### <a name="cleanup"></a>Limpieza

Cuando el `AudioTrack` es ya no es necesario, deben liberar sus recursos mediante una llamada a [versión](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Release%28%29/):

```csharp
audioTrack.Release();
```


### <a name="the-audiorecord-class"></a>La clase AudioRecord

El [AudioRecord](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/) clase es el equivalente de `AudioTrack` en el lado de la grabación. Al igual que `AudioTrack`, utiliza los búferes de memoria directamente, en lugar de archivos y los URI. Requiere que el `RECORD_AUDIO` el conjunto de permisos en el manifiesto.


#### <a name="initializing-and-recording"></a>Inicializar y grabar

El primer paso es crear un nuevo [AudioRecord](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/) objeto. La lista de argumentos pasado en el [constructor](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/#memberlist) proporciona toda la información necesaria para la grabación. A diferencia de `AudioTrack`, donde los argumentos son en gran medida las enumeraciones, los argumentos equivalentes en `AudioRecord` son números enteros. Se incluyen los siguientes:

1.  Hardware audio origen de entrada como micrófono.

2.  Stream tipo &ndash; voz, tono, música, sistema o alarmas.

3.  Frecuencia &ndash; la frecuencia de muestreo que se expresa en Hz.

4.  Configuración de canal &ndash; Mono o estéreo.

5.  Formato de audio &ndash; de 8 bits o codificación de 16 bits.

6.  Tamaño en bytes del búfer


Una vez el `AudioRecord` se construye, su [StartRecording](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.StartRecording%28%29/) se invoca el método. Ahora está listo para empezar a grabar. El `AudioRecord` continuamente lee el búfer de audio de entrada y escribe esta entrada horizontal a un archivo de audio.

```csharp
void RecordAudio()
{
  byte[] audioBuffer = new byte[100000];
  var audRecorder = new AudioRecord(
    // Hardware source of recording.
    AudioSource.Mic,
    // Frequency
    11025,
    // Mono or stereo
    ChannelIn.Mono,
    // Audio encoding
    Android.Media.Encoding.Pcm16bit,
    // Length of the audio clip.
    audioBuffer.Length
  );
  audRecorder.StartRecording();
  while (true) {
    try
    {
      // Keep reading the buffer while there is audio input.
      audRecorder.Read(audioBuffer, 0, audioBuffer.Length);
      // Write out the audio file.
    } catch (Exception ex) {
      Console.Out.WriteLine(ex.Message);
      break;
    }
  }
}
```


#### <a name="stopping-the-recording"></a>Detener la grabación

Una llamada a la [detener](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.Stop%28%29/) método finaliza la grabación:

```csharp
audRecorder.Stop();
```


#### <a name="cleanup"></a>Limpieza

Cuando el `AudioRecord` objeto ya no es necesario, una llamada a su [versión](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.Release%28%29/) método libera todos los recursos asociados con él:

```csharp
audRecorder.Release();
```


## <a name="summary"></a>Resumen

El sistema operativo Android proporciona un marco eficaz para reproducir, grabar y administración de audio. Este artículo trata cómo reproducir y grabar audio con el alto nivel `MediaPlayer` y `MediaRecorder` clases. A continuación, hemos visto cómo usar notificaciones de sonido para compartir los recursos de audio del dispositivo entre las distintas aplicaciones. Por último, aborda cómo reproducir y grabar audio mediante las API de bajo nivel, que comunicarse directamente con los búferes de memoria.


## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con Audio (ejemplo)](https://developer.xamarin.com/samples/Example_WorkingWithAudio/)
- [Reproductor de Media](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/)
- [Grabadora de medios](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/)
- [Administrador de audio](https://developer.xamarin.com/api/type/Android.Media.AudioManager/)
- [Pista de audio](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/)
- [Grabadora de audio](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/)
