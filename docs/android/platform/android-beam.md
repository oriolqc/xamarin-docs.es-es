---
title: Android Beam
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/06/2017
ms.openlocfilehash: 9fcabc90875dda28ecdd5d94f1ca2f263ffe4886
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60954204"
---
# <a name="android-beam"></a>Android Beam

Haz de Android es una tecnología de cerca de campo Communication (NFC) introducida en Android 4.0 que permite compartir información a través de NFC en cerca de las aplicaciones.

[![Diagrama que ilustra dos dispositivos cerca de uso compartido de información](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png#lightbox)

Haz Android funciona mediante la inserción de los mensajes a través de NFC cuando dos dispositivos están en el intervalo. Aproximadamente 4cm entre sí los dispositivos pueden compartir datos con Android carretera. Crea un mensaje de una actividad en un dispositivo y especifica una actividad (o actividades) que puede controlar insertarla. Cuando la actividad especificada está en primer plano y los dispositivos están en el intervalo, haz Android insertará el mensaje en el segundo dispositivo. En el dispositivo receptor, una intención se invoca que contiene los datos del mensaje.

Android admite dos formas de configurar los mensajes con Android carretera:

-   `SetNdefPushMessage` -Antes de que se inicie la carretera Android, una aplicación puede llamar a SetNdefPushMessage para especificar un NdefMessage Insertar a través de NFC y la actividad que se inserta. Este mecanismo se usa mejor cuando un mensaje no cambia mientras una aplicación está en uso.

-   `SetNdefPushMessageCallback` -Cuando se inicia haz de Android, una aplicación puede controlar una devolución de llamada para crear un NdefMessage. Este mecanismo permite la creación del mensaje se retrasa hasta que los dispositivos están en el intervalo. Admite escenarios donde el mensaje puede variar en función de lo que sucede en la aplicación.


En cualquier caso, para enviar datos con Android carretera, una aplicación envía una `NdefMessage`, empaquetar los datos en varios `NdefRecords`. Echemos un vistazo a los puntos clave que deben solucionarse antes de que se puede desencadenar haz de Android. En primer lugar, vamos a trabajar con el estilo de devolución de llamada de creación de un `NdefMessage`.


## <a name="creating-a-message"></a>Creación de un mensaje

Podemos registrar devoluciones de llamada con un `NfcAdapter` en la actividad `OnCreate` método. Por ejemplo, si suponemos un `NfcAdapter` denominado `mNfcAdapter` se declara como una variable de clase en la actividad, podemos escribir el código siguiente para crear la devolución de llamada que se va a construir el mensaje:

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

La actividad, que implementa `NfcAdapter.ICreateNdefMessageCallback`, se pasa a la `SetNdefPushMessageCallback` método anterior. Cuando se inicia la carretera Android, el sistema llamará `CreateNdefMessage`, desde que la actividad puede construir un `NdefMessage` tal como se muestra a continuación:

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

En el lado receptor, el sistema invoca una intención con el `ActionNdefDiscovered` acción, desde el que podemos extraer el NdefMessage como sigue:

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

Para obtener un ejemplo de código completo que usa un haz Android, se muestra que se ejecutan en la captura de pantalla siguiente, vea el [demostración de Android de carretera](https://developer.xamarin.com/samples/monodroid/AndroidBeamDemo/) en la Galería de ejemplos.

[![Capturas de pantalla de ejemplo de la demostración de Android de carretera](android-beam-images/24.png)](android-beam-images/24.png#lightbox)



## <a name="related-links"></a>Vínculos relacionados

- [Demostración de Android de carretera (ejemplo)](https://developer.xamarin.com/samples/monodroid/AndroidBeamDemo/)
- [Introducción a Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma Android de 4.0](https://developer.android.com/sdk/android-4.0.html)
