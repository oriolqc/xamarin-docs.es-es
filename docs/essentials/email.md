---
title: 'Xamarin.Essentials: correo electrónico'
description: La clase de correo electrónico en Xamarin.Essentials permite que una aplicación abrir la aplicación de correo electrónico predeterminada con una información especificada como el asunto, el cuerpo y los destinatarios (TO, CC, CCO).
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: f113cebfebf4238fd4b75ad8ab248e2abf61efea
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353911"
---
# <a name="xamarinessentials-email"></a>Xamarin.Essentials: correo electrónico

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **correo electrónico** clase permite que una aplicación abrir la aplicación de correo electrónico predeterminada con una información especificada como el asunto, el cuerpo y los destinatarios (TO, CC, CCO).

## <a name="using-email"></a>Uso de correo electrónico

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad de correo electrónico funciona mediante una llamada a la `ComposeAsync` método un `EmailMessage` que contiene información sobre el correo electrónico:

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

## <a name="api"></a>API

- [Código fuente de correo electrónico](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [Documentación de API de correo electrónico](xref:Xamarin.Essentials.Email)
