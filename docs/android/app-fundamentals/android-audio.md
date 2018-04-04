---
title: Audio Android
description: El sistema operativo Android proporciona una amplia compatibilidad para multimedia, que abarca audio y vídeo. Esta guía se centra en audio en Android y cubre la reproducción y grabación de audio con el Reproductor de audio integrado y clases de grabadora, así como la API de audio de bajo nivel. También explica cómo trabajar con eventos de Audio de difusión por otras aplicaciones, para que los desarrolladores pueden crear aplicaciones con comportamiento correcto.
ms.prod: xamarin
ms.assetid: 646ED563-C34E-256D-4B56-29EE99881C27
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/28/2018
ms.openlocfilehash: aff0d67549707129bfc85246318c33c522e4f1f6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="android-audio"></a>Audio Android

_El sistema operativo Android proporciona una amplia compatibilidad para multimedia, que abarca audio y vídeo. Esta guía se centra en audio en Android y cubre la reproducción y grabación de audio con el Reproductor de audio integrado y clases de grabadora, así como la API de audio de bajo nivel. También explica cómo trabajar con eventos de Audio de difusión por otras aplicaciones, para que los desarrolladores pueden crear aplicaciones con comportamiento correcto._


## <a name="overview"></a>Información general

Los dispositivos móviles modernos han adoptado funcionalidad que anteriormente, tendrías que hacer dedicados partes de un equipo &ndash; cámaras, reproductores de música y videograbadoras. Por este motivo, marcos multimedios se han convertido en una característica de primera clase en las API de dispositivos móviles.

Android proporciona una amplia compatibilidad para elementos multimedia. Este artículo examina trabajar con audio en Android y trata los temas siguientes

1.  **Reproducir archivos de Audio con el Reproductor de Media** &ndash; mediante integrado `MediaPlayer` clase para reproducir audio, incluidos los archivos de audio locales y los archivos de audio por secuencias con el `AudioTrack` clase.

2.  **Grabación de Audio** &ndash; mediante integrado `MediaRecorder` clase para grabar audio.

3.  **Trabajar con las notificaciones de sonido** &ndash; mediante notificaciones de sonido para crear aplicaciones con comportamiento correcto que respondan correctamente a eventos (por ejemplo, llamadas de teléfono entrantes) suspender o cancelar sus salidas de audio.

4.  **Trabajar con Audio de bajo nivel** &ndash; reproducir audio utilizando la `AudioTrack` clase escribiendo directamente en búferes de memoria. Grabación de audio utilizando la `AudioRecord` clase y la lectura directamente desde los búferes de memoria.


## <a name="requirements"></a>Requisitos

Esta guía requiere Android 2.0 (API nivel 5) o superior. Tenga en cuenta que se debe realizar la depuración de audio en Android en un dispositivo.

Es necesario para la solicitud de la `RECORD_AUDIO` permisos en **AndroidManifest.XML**:

![Requiere la sección permisos de manifiesto de Android con registro\_AUDIO habilitado](android-audio-images/image01.png)



## <a name="playing-audio-with-the-mediaplayer-class"></a>Reproducir archivos de Audio con MediaPlayer (clase)

Es la manera más sencilla de reproducir el audio en Android con integrado [MediaPlayer](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/) clase.
`MediaPlayer` puede reproducir archivos locales o remotos al pasar la ruta de acceso de archivo. Sin embargo, `MediaPlayer` muy distinguen el estado y llamar a uno de sus métodos en un estado incorrecto se producirá una excepción que se produzca. Es importante interactuar con `MediaPlayer` en el orden descrito a continuación para evitar errores.



### <a name="initializing-and-playing"></a>Inicializar y la reproducción

Reproducción de audio con `MediaPlayer` requiere la siguiente secuencia:

1. Crear instancias de un nuevo [MediaPlayer](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/) objeto.

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

La reproducción puede ser suspendida por una llamada a la [pausa](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Pause%28%29/) método:

```csharp
player.Pause();
```

Para reanudar la reproducción en pausa, llame a la [iniciar](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Start%28%29/) método.
Esto se reanudará desde la ubicación en pausa en la reproducción:

```csharp
player.Start();
```

Llamar a la [detener](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Stop%28%29/) método en el Reproductor finaliza una reproducción en curso:

```csharp
player.Stop();
```

Cuando ya no se necesita el Reproductor, se deben liberar los recursos mediante una llamada a la [versión](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Release%28%29/) método:

```csharp
player.Release();
```



## <a name="using-the-mediarecorder-class-to-record-audio"></a>Uso de la clase MediaRecorder para grabar Audio

El corolario a `MediaPlayer` de grabación de audio en Android es el [MediaRecorder](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/) clase. Al igual que el `MediaPlayer`, se tiene en cuenta el estado y pasa por varios Estados para llegar al punto donde puede iniciar la grabación. Para grabar audio, el `RECORD_AUDIO` se deben establecer permisos. Para obtener instrucciones sobre cómo establecer la aplicación vea permisos [trabajar con AndroidManifest.xml](~/android/platform/android-manifest.md).


### <a name="initializing-and-recording"></a>Inicializar y grabación

Grabación de audio con el `MediaRecorder` requiere los pasos siguientes:

1. Crear instancias de un nuevo [MediaRecorder](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/) objeto.

2. Especificar qué dispositivo de hardware para capturar la entrada de audio a través de la [SetAudioSource](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetAudioSource/p/Android.Media.AudioSource/) método.

3. Establecer el formato de audio de archivo de salida mediante la [SetOutputFormat](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetOutputFormat/p/Android.Media.OutputFormat/) método. Para obtener una lista de tipos de audio admitidos, consulte [Android Supported Media Formats](http://developer.android.com/guide/appendix/media-formats.html).

4. Llame a la [SetAudioEncoder](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetAudioEncoder/p/Android.Media.AudioEncoder/) método para establecer el tipo de codificación de audio.

5. Llame a la [SetOutputFile](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetOutputFile/p/System.String/) método para especificar el nombre del archivo de salida que se escriben los datos de audio en.

6. Llame a la [preparar](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Prepare%28%29/) método para inicializar la grabadora.

7. Llame a la [iniciar](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Start%28%29/) método para iniciar la grabación.


El ejemplo de código siguiente muestra esta secuencia:

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

Una vez el `MediaRecorder` ha sido detenido, llame a la [restablecer](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Reset%28%29/) método lo coloque nuevamente en su estado de inactividad:

```csharp
recorder.Reset();
```

Cuando el `MediaRecorder` es ya no es necesario, deben liberar sus recursos mediante una llamada a la [versión](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Release%28%29/) método:

```csharp
recorder.Release();
```


## <a name="managing-audio-notifications"></a>Administrar las notificaciones de sonido



### <a name="the-audiomanager-class"></a>La clase AudioManager

El [AudioManager](https://developer.xamarin.com/api/type/Android.Media.AudioManager/) clase proporciona acceso a las notificaciones de sonido que permiten a las aplicaciones cuando se producen eventos de audio. Este servicio también proporciona acceso a otras características de audio, como control de modo de volumen y timbre. El `AudioManager` permite que una aplicación controlar las notificaciones de sonido para controlar la reproducción de audio.



### <a name="managing-audio-focus"></a>Administración de la selección de Audio

Los recursos de audio del dispositivo (el Reproductor multimedia integrado y grabadora) se comparten entre todas las aplicaciones en ejecución.

Conceptualmente, esto es similar a las aplicaciones en un equipo de escritorio que sólo una aplicación tiene el foco del teclado: después de seleccionar una de las aplicaciones en ejecución haciendo lo clic del mouse, la entrada de teclado sólo se dirige a esa aplicación.

Audio foco es un concepto similar y evita que más de una aplicación de reproducción o grabación de audio al mismo tiempo. Es más complicado que el foco del teclado porque es voluntaria &ndash; la aplicación puede pasar por alto este hecho que actualmente no tiene el foco de audio y reproducir independientemente &ndash; y porque hay diferentes tipos de concentración de audio que pueden ser solicitado. Por ejemplo, si solo se espera que el solicitante reproduzca el audio de un tiempo muy corto, puede solicitar foco transitorio.

Enfoque de audio que puede conceder de forma inmediata, o inicialmente denegado y conceder más adelante. Por ejemplo, si una aplicación solicitudes concentración de audio durante una llamada de teléfono, se denegarán, pero también se podrá otorgar foco una vez finalizada la llamada de teléfono. En este caso, un agente de escucha está registrado con el fin de responder en consecuencia, si se retiran foco audio. Solicitar el foco de audio se usa para determinar si no es correcto reproducir o grabar audio.

Para obtener más información acerca del foco de audio, consulte [administrar Audio foco](http://developer.android.com/training/managing-audio/audio-focus.html).



#### <a name="registering-the-callback-for-audio-focus"></a>Registrar la devolución de llamada para el enfoque de Audio

Registrar el `FocusChangeListener` devolución de llamada de la `IOnAudioChangeListener` es una parte importante de obtención y liberación de foco del audio. Esto es porque la concesión de concentración de audio se puede retrasar hasta un momento posterior. Por ejemplo, una aplicación puede solicitar al reproducir música mientras hay una llamada de teléfono en curso. No se le concedan foco audio hasta que finalice la llamada de teléfono.

Por esta razón, el objeto de devolución de llamada se pasa como un parámetro en el `GetAudioFocus` método de la `AudioManager`, y es esta llamada que se registra la devolución de llamada. Si foco audio es denegado inicialmente pero conceder más adelante, la aplicación se informa al invocar `OnAudioFocusChange` en la devolución de llamada. El mismo método se usa para indicar que la aplicación que se se retiran foco audio.

Cuando la aplicación ha terminado de usar los recursos de sonido, se llama a la `AbandonFocus` método de la `AudioManager`y pasa a la devolución de llamada. Esto anula el registro de la devolución de llamada y libera los recursos de audio, para que otras aplicaciones pueden obtenga el foco de audio.



#### <a name="requesting-audio-focus"></a>Solicitar el foco de Audio

Los pasos necesarios para solicitar los recursos de audio del dispositivo son los siguientes:

1.  Obtener un identificador para el `AudioManager` servicio del sistema.

2.  Cree una instancia de la clase de devolución de llamada.

3.  Solicitar los recursos de audio del dispositivo mediante una llamada a la `RequestAudioFocus` método en el `AudioManager` . Los parámetros son el objeto de devolución de llamada, el tipo de secuencia (música, llamada de voz, anillo etc.) y el tipo de la derecha que se solicita el acceso (los recursos de sonido se pueden solicitar en breve o durante un período indefinido, por ejemplo).

4.  Si se concede la solicitud, el `playMusic` método se invoca inmediatamente, y el audio comienza a reproducir.

5.  Si se deniega la solicitud, se realiza ninguna otra acción. En este caso, el audio sólo se ejecutarán si la solicitud se concede en un momento posterior.


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

Una vez completada, la reproducción de la pista del `AbandonFocus` método en `AudioManager` se invoca. Esto permite que otra aplicación obtener los recursos de audio del dispositivo. Otras aplicaciones recibirán una notificación de este cambio de foco audio si han registrado sus propios agentes de escucha.


## <a name="low-level-audio-api"></a>API de Audio de nivel bajo

Las API de audio de bajo nivel proporcionan un mayor control sobre la reproducción de audio y grabación debido a que interactúan directamente con búferes de memoria en lugar de usar el URI de archivo. Hay algunos escenarios donde es preferible este enfoque. Estos escenarios incluyen:

1.  Durante la reproducción de archivos de audio cifrados.

2.  Al reproducir una sucesión de clips cortos.

3.  Audio de transmisión por secuencias.


### <a name="audiotrack-class"></a>Clase AudioTrack

El [AudioTrack](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/) clase utiliza las API de audio de bajo nivel para la grabación y es el equivalente de bajo nivel de la `MediaPlayer` clase.


#### <a name="initializing-and-playing"></a>Inicializar y la reproducción

Para reproducir audio, una nueva instancia de `AudioTrack` se deben crear instancias. La lista de argumentos pasado en el [constructor](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/#memberlist) especifica cómo reproducir el audio muestras que se incluyen en el búfer. Los argumentos son:

1.  Tipo de secuencia &ndash; voz, tono, música, sistema o alarma.

2.  Frecuencia &ndash; la frecuencia de muestreo expresada en Hz.

3.  Configuración de canal &ndash; Mono o estéreo.

4.  Formato de audio &ndash; codificación de 16 bits o de 8 bits.

5.  Tamaño de búfer &ndash; en bytes.

6.  Modo de búfer &ndash; transmisión por secuencias o estáticos.


Después de la construcción del [reproducir](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Play%28%29/) método `AudioTrack` se invoca, para configurarla para iniciar reproducción. Escribir el búfer de audio para el `AudioTrack` inicia la reproducción:

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

Llamar a la [detener](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Stop%28%29/) método finalizará permanentemente la reproducción:

```csharp
audioTrack.Stop();
```


#### <a name="cleanup"></a>Limpieza

Cuando el `AudioTrack` es ya no es necesario, deben liberar sus recursos mediante una llamada a [versión](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Release%28%29/):

```csharp
audioTrack.Release();
```


### <a name="the-audiorecord-class"></a>La clase AudioRecord

El [AudioRecord](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/) clase es el equivalente de `AudioTrack` en el lado de la grabación. Al igual que `AudioTrack`, utiliza los búferes de memoria directamente, en lugar de archivos y URI. Requiere que el `RECORD_AUDIO` permiso se puede definir en el manifiesto.


#### <a name="initializing-and-recording"></a>Inicializar y grabación

El primer paso consiste en crear un nuevo [AudioRecord](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/) objeto. La lista de argumentos pasado en el [constructor](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/#memberlist) proporciona toda la información necesaria para la grabación. A diferencia de en `AudioTrack`, donde los argumentos son en gran medida las enumeraciones, los argumentos equivalentes en `AudioRecord` son enteros. Se incluyen los siguientes:

1.  Hardware audio origen de entrada como micrófono.

2.  Tipo de secuencia &ndash; voz, tono, música, sistema o alarma.

3.  Frecuencia &ndash; la frecuencia de muestreo expresada en Hz.

4.  Configuración de canal &ndash; Mono o estéreo.

5.  Formato de audio &ndash; codificación de 16 bits o de 8 bits.

6.  Tamaño en bytes del búfer


Una vez el `AudioRecord` se construye su [StartRecording](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.StartRecording%28%29/) se invoca el método. Ahora está listo para empezar a grabar. El `AudioRecord` continuamente lee el búfer de audio de entrada y escribe esta entrada salida a un archivo de audio.

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

Llamar a la [detener](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.Stop%28%29/) método termina la grabación:

```csharp
audRecorder.Stop();
```


#### <a name="cleanup"></a>Limpieza

Cuando el `AudioRecord` objeto ya no es necesario, una llamada a su [versión](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.Release%28%29/) método libera todos los recursos asociados con ella:

```csharp
audRecorder.Release();
```


## <a name="summary"></a>Resumen

El sistema operativo Android proporciona un marco eficaz para reproducir, grabar y administración de audio. Este artículo trata cómo reproducir y grabar audio mediante el alto nivel `MediaPlayer` y `MediaRecorder` clases. A continuación, explorar cómo usar las notificaciones de sonido para compartir los recursos de audio del dispositivo entre distintas aplicaciones. Por último, abordan la reproducción y grabación de audio mediante las API de bajo nivel, que comunicarse directamente con búferes de memoria.


## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con Audio (ejemplo)](https://developer.xamarin.com/samples/Example_WorkingWithAudio/)
- [Reproductor de Media](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/)
- [Media Recorder](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/)
- [Administrador de audio](https://developer.xamarin.com/api/type/Android.Media.AudioManager/)
- [Pista de audio](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/)
- [Grabadora de audio](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/)
