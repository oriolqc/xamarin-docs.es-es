---
title: Haz Android
ms.topic: article
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/06/2017
ms.openlocfilehash: e9936bb523db8ba8777df94a03bf12f9fa718fca
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="android-beam"></a>Haz Android

Haz Android es una nueva tecnología de cerca de comunicación de campo (NFC) en Android 4 que permite a las aplicaciones compartir información a través de NFC en cerca.

[![Diagrama que ilustra dos dispositivos cerca del uso compartido de información](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png#lightbox)

Haz Android funciona mediante la inserción de mensajes a través de NFC cuando los dos dispositivos están en el intervalo. Dispositivos unos 4cm entre sí pueden compartir datos con Android carretera. Crea un mensaje de una actividad en un dispositivo y especifica una actividad (o actividades) que puede controlarlo inserta. Cuando la actividad especificada está en primer plano y los dispositivos están en el intervalo, haz Android envía el mensaje en el segundo dispositivo. En el dispositivo receptor, se invoca un intento que contiene los datos del mensaje.

Android admite dos formas de configurar mensajes con haz Android:

-   `SetNdefPushMessage` -Antes de que se inicia la carretera Android, una aplicación puede llamar a SetNdefPushMessage para especificar un NdefMessage para insertar NFC y la actividad que se inserta. Este mecanismo se utiliza mejor cuando un mensaje no cambia mientras una aplicación está en uso.

-   `SetNdefPushMessageCallback` -Cuando se inicia la carretera Android, una aplicación puede controlar una devolución de llamada para crear un NdefMessage. Este mecanismo permite la creación de mensajes se retrasa hasta que los dispositivos están en el intervalo. Es compatible con escenarios donde el mensaje puede variar en función de lo que sucede en la aplicación.


En cualquier caso, para enviar datos con haz Android, una aplicación envía una `NdefMessage`, empaquetar los datos en varios `NdefRecords`. ¡Eche un vistazo a los puntos clave que deben solucionarse antes de que se puede desencadenar carretera Android. En primer lugar, se trabajará con el estilo de devolución de llamada de creación de un `NdefMessage`.


## <a name="creating-a-message"></a>Creación de un mensaje

Podemos registrar devoluciones de llamada con un `NfcAdapter` en la actividad `OnCreate` método. Por ejemplo, suponiendo un `NfcAdapter` denominado `mNfcAdapter` se declara como una variable de clase en la actividad, podemos escribir el código siguiente para crear la devolución de llamada que se construirá el mensaje:

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

La actividad, que implementa `NfcAdapter.ICreateNdefMessageCallback`, se pasa a la `SetNdefPushMessageCallback` método anterior. Cuando se inicia la carretera Android, el sistema llamará `CreateNdefMessage`, de que la actividad puede construir un `NdefMessage` tal y como se muestra a continuación:

```csharp
public NdefMessage CreateNdefMessage (NfcEvent evt)
{
    DateTime time = DateTime.Now;
    var text = ("Beam me up!\n\n" + "Beam Time: " +
        time.ToString ("HH:mm:ss"));
    NdefMessage msg = new NdefMessage (
        new NdefRecord[]{ CreateMimeRecord (
            "application/com.example.android.beam",
            Encoding.UTF8.GetBytes (text)) });
        } };
    return msg;
}

public NdefRecord CreateMimeRecord (String mimeType, byte [] payload)
{
    byte [] mimeBytes = Encoding.UTF8.GetBytes (mimeType);
    NdefRecord mimeRecord = new NdefRecord (
        NdefRecord.TnfMimeMedia, mimeBytes, new byte [0], payload);
    return mimeRecord;
}
```


## <a name="receiving-a-message"></a>Recibir un mensaje

En el lado receptor, el sistema invoca un intento con el `ActionNdefDiscovered` acción, desde el que se puede extraer el NdefMessage como se indica a continuación:

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

Para obtener un ejemplo de código completo que usa un haz Android, se muestra la ejecución en la captura de pantalla siguiente, consulte la [demostración carretera Android](https://developer.xamarin.com/samples/monodroid/AndroidBeamDemo/) en la Galería de ejemplos.

[![Capturas de pantalla de ejemplo de la demostración de carretera Android](android-beam-images/24.png)](android-beam-images/24.png#lightbox)



## <a name="related-links"></a>Vínculos relacionados

- [Demostración de carretera Android (ejemplo)](https://developer.xamarin.com/samples/monodroid/AndroidBeamDemo/)
- [Introducción a helado Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
