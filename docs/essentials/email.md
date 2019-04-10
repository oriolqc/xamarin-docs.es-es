---
title: 'Xamarin.Essentials: Correo electrónico'
description: La clase Email de Xamarin.Essentials permite que una aplicación abra la aplicación de correo electrónico predeterminada con información especificada incluido el asunto, el cuerpo y los destinatarios (PARA, CC, CCO).
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.openlocfilehash: 06b4f4b612d0cb44e467a9da6dbee3194338027d
ms.sourcegitcommit: 495680e74c72e7c570e68cde95d3d3643b1fcc8a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/02/2019
ms.locfileid: "58869967"
---
# <a name="xamarinessentials-email"></a>Xamarin.Essentials: Correo electrónico

La clase **Email** permite que una aplicación abra la aplicación de correo electrónico predeterminada con información especificada incluido el asunto, el cuerpo y los destinatarios (PARA, CC, CCO).

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-email"></a>Uso de Email

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Para usar la funcionalidad de Email se llama al método `ComposeAsync` con un valor `EmailMessage` que contiene información sobre el correo electrónico:

```csharp
public class EmailTest
{
    public async Task SendEmail(string subject, string body, List<string> recipients)
    {
        try
        {
            var message = new EmailMessage
            {
                Subject = subject,
                Body = body,
                To = recipients,
                //Cc = ccRecipients,
                //Bcc = bccRecipients
            };
            await Email.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException fbsEx)
        {
            // Email is not supported on this device
        }
        catch (Exception ex)
        {
            // Some other exception occurred
        }
    }
}
```


## <a name="platform-differences"></a>Diferencias entre plataformas

# [<a name="android"></a>Android](#tab/android)

No todos los clientes de correo electrónico para Android admiten `Html`. Puesto que no hay ninguna manera de detectar este problema, recomendamos usar `PlainText` para enviar correos electrónicos.

# [<a name="ios"></a>iOS](#tab/ios)

No hay diferencias entre las plataformas.

# [<a name="uwp"></a>UWP](#tab/uwp)

Solo admite `PlainText`, ya que el formato `BodyFormat` que intenta enviar `Html` producirá una excepción `FeatureNotSupportedException`.

-----

## <a name="file-attachments"></a>Datos adjuntos

![Características en versión preliminar](~/media/shared/preview.png)

El envío de archivos por correo electrónico está disponible como versión preliminar experimental en Xamarin.Essentials versión 1.1.0. Esta característica permite que una aplicación envíe archivos por correo electrónico a través de clientes de correo electrónico en el dispositivo. Para habilitar esta característica, establezca la siguiente propiedad en el código de inicio de la aplicación:

```csharp
ExperimentalFeatures.Enable(ExperimentalFeatures.EmailAttachments);
```

Una vez que se haya habilitado la característica, se puede enviar por correo electrónico cualquier archivo. Xamarin.Essentials detectará automáticamente el tipo de archivo (MIME) y solicitará que el archivo se agregue como datos adjuntos. Cada cliente de correo electrónico es diferente y podría admitir únicamente algunas extensiones de archivo o ninguna en absoluto.

A continuación se muestra un ejemplo en el que se escribe texto en el disco y se agrega como datos adjuntos a un correo electrónico:

```csharp
var message = new EmailMessage
{
    Subject = "Hello",
    Body = "World",
};

var fn = "Attachment.txt";
var file = Path.Combine(FileSystem.CacheDirectory, fn);
File.WriteAllText(file, "Hello World");

message.Attachments.Add(new EmailAttachment(file));

await Email.ComposeAsync(message);
```

## <a name="api"></a>API

- [Código fuente de Email](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [Documentación de API para Email](xref:Xamarin.Essentials.Email)
