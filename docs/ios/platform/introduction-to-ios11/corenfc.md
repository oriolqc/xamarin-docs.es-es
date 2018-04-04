---
title: Core NFC
description: Etiquetas de lectura a cerca de comunicación campo (NFC) con iOS 11
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 846B59D3-F66A-48F3-A78C-84217697194E
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/25/2016
ms.openlocfilehash: db0c417c1c79a988821a93b1cf20d94161f5c47a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="core-nfc"></a>Core NFC

_Etiquetas de lectura a cerca de comunicación campo (NFC) con iOS 11_

CoreNFC es un nuevo marco en iOS 11 que proporciona acceso a la _cerca de comunicación de campo_ radio (NFC) para leer etiquetas desde dentro de las aplicaciones. Funciona en iPhone 7, más de 7, 8, 8 signos más y X.

El lector de etiquetas NFC en dispositivos iOS es compatible con todos los tipos de etiquetas NFC del 1 al 5 que contienen _formato de intercambio de datos de NFC_ información (NDEF).

Hay algunas restricciones para tener en cuenta:

- CoreNFC sólo es compatible con la etiqueta leer (no se pueden escribir o formato).
- Exámenes de etiqueta deben ser iniciada por el usuario y el tiempo de espera después de 60 segundos.
- Las aplicaciones deben ser visibles en primer plano para el análisis.
- CoreNFC sólo se pueden probar en dispositivos reales (no en el simulador).

Esta página describe la configuración necesaria para usar CoreNFC y muestra cómo utilizar la API mediante el ["TFCTagReader" código de ejemplo](https://developer.xamarin.com/samples/monotouch/ios11/NFCTagReader/).

## <a name="configuration"></a>Configuración

Para habilitar CoreNFC, debe configurar tres elementos en el proyecto:

- Un **Info.plist** clave de privacidad.
- Un **Entitlements.plist** entrada.
- Un perfil de aprovisionamiento con **lectura de etiqueta NFC** capacidad.

### <a name="infoplist"></a>Info.plist

Agregar el **NFCReaderUsageDescription** clave de privacidad y texto, que se muestra al usuario mientras se lleva a cabo la detección. Usar un mensaje adecuado para la aplicación (por ejemplo, explique el propósito del análisis):

```xml
<key>NFCReaderUsageDescription</key>
<string>NFC tag to read NDEF messages into the application</string>
```

### <a name="entitlementsplist"></a>Entitlements.plist

La aplicación debe solicitar la **cerca de lectura de etiqueta de campo comunicaciones** capacidad con la clave/valor siguiente par de su **Entitlements.plist**:

```xml
<key>com.apple.developer.nfc.readersession.formats</key>
<array>
  <string>NDEF</string>
</array>
```

### <a name="provisioning-profile"></a>Perfil de aprovisionamiento

Crear un nuevo **Id. de aplicación** y asegúrese de que el **lectura de etiqueta NFC** servicio esté marcado:

[![Página de Id. de aplicación nuevo Portal para desarrolladores con la lectura de etiqueta NFC seleccionado](corenfc-images/app-services-nfc-sml.png)](corenfc-images/app-services-nfc.png#lightbox)

A continuación, debe crear un nuevo perfil de aprovisionamiento para este ID de aplicación, a continuación, descargar e instalar en el desarrollo de Mac.

## <a name="reading-a-tag"></a>Leer una etiqueta

Una vez que el proyecto está configurado, agregue `using CoreNFC;` a la parte superior del archivo y siga estos tres pasos para implementar NFC funcionalidad de lectura de etiquetas:

### <a name="1-implement-infcndefreadersessiondelegate"></a>1. Implemente `INFCNdefReaderSessionDelegate`

La interfaz tiene dos métodos para implementarse:

- `DidDetect` : Se llama cuando se lee correctamente una etiqueta.
- `DidInvalidate` : Se llama cuando se produce un error o se alcanza el tiempo de espera 60 segundos.

#### <a name="diddetect"></a>DidDetect

En el código de ejemplo, cada mensaje analizado se agrega a una vista de tabla:

```csharp
public void DidDetect(NFCNdefReaderSession session, NFCNdefMessage[] messages)
{
    foreach (NFCNdefMessage msg in messages)
    {  // adds the messages to a list view
        DetectedMessages.Add(msg);
    }
    DispatchQueue.MainQueue.DispatchAsync(() =>
    {
        this.TableView.ReloadData();
    });
}
```

Este método puede llamarse varias veces (y se puede pasar una matriz de mensajes) si la sesión permite varias lecturas de etiqueta. Este valor se establece mediante el tercer parámetro de la `Start` método (explica en [paso 2](#step2)).

#### <a name="didinvalidate"></a>DidInvalidate

Invalidación puede producirse por una serie de motivos:

- Se produjo un error al buscar.
- La aplicación haya dejado de estar en primer plano.
- El usuario decide cancelar el análisis.
- El análisis se ha cancelado por la aplicación.

El código siguiente muestra cómo controlar un error:

```csharp
public void DidInvalidate(NFCNdefReaderSession session, NSError error)
{
    var readerError = (NFCReaderError)(long)error.Code;
    if (readerError != NFCReaderError.ReaderSessionInvalidationErrorFirstNDEFTagRead &&
        readerError != NFCReaderError.ReaderSessionInvalidationErrorUserCanceled)
    {
      // some error handling
    }
}
```

Una vez que se ha invalidado una sesión, se debe crear un nuevo objeto de sesión para que se vuelva a examinar.

<a name="step2" />

### <a name="2-start-an-nfcndefreadersession"></a>2. Iniciar un `NFCNdefReaderSession`

Examen debe comenzar con una solicitud de usuario, como presionar un botón.
El código siguiente se crea e inicia una sesión de análisis:

```csharp
Session = new NFCNdefReaderSession(this, null, true);
Session?.BeginSession();
```

Los parámetros para el `NFCNdefReaderSession` constructor son los siguientes:

- `delegate` : Una implementación de `INFCNdefReaderSessionDelegate`. En el código de ejemplo, el delegado se implementa en el controlador de vista de tabla, por lo tanto, `this` se utiliza como parámetro del delegado.
- `queue` : La cola que se administran las devoluciones de llamada en. Puede ser `null`, en cuyo caso Asegúrese de usar el `DispatchQueue.MainQueue` al actualizar los controles de la interfaz de usuario (como se muestra en el ejemplo).
- `invalidateAfterFirstRead` : Cuando `true`, el análisis se detiene tras la primera detección correcta; cuando `false` análisis continuará y se devuelven varios resultados hasta que el análisis se canceló o se alcanza el tiempo de espera 60 segundos.


### <a name="3-cancel-the-scanning-session"></a>3. Cancelar la sesión de análisis

El usuario puede cancelar la sesión de análisis a través de un botón proporcionados por el sistema en la interfaz de usuario:

![Botón de cancelación mientras se analizaba el](corenfc-images/scan-cancel-sml.png)

La aplicación mediante programación puede cancelar la exploración mediante una llamada a la `InvalidateSession` método:

```csharp
Session.InvalidateSession();
```

En de ambos casos, el delegado `DidInvalidate` se llama al método.

## <a name="summary"></a>Resumen

CoreNFC permite a la aplicación leer datos de etiquetas NFC. Admite la lectura de una variedad de formatos de etiqueta (tipos NDEF 1 a 5), pero no admite escritura o dar formato.


## <a name="related-links"></a>Vínculos relacionados

- [NFCTagReader (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios11/NFCTagReader/)
- [Introducción a Core NFC (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/718/)
