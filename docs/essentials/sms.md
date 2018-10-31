---
title: 'Xamarin.Essentials: SMS'
description: La clase SMS en Xamarin.Essentials permite que una aplicación abra la aplicación SMS predeterminada con un mensaje especificado para enviar a un destinatario.
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: fa55a17e99a11861b98c4d515df882ed3af58a0b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102742"
---
# <a name="xamarinessentials-sms"></a>Xamarin.Essentials: SMS

![Versión preliminar de NuGet](~/media/shared/pre-release.png)

La clase **SMS** permite que una aplicación abra la aplicación SMS predeterminada con un mensaje especificado para enviar un destinatario.

## <a name="using-sms"></a>Uso de SMS

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad SMS funciona mediante una llamada al método `ComposeAsync`, un `SmsMessage` que contiene el destinatario del mensaje y el cuerpo del mismo, ambos opcionales.

```csharp
public class SmsTest
{
    public async Task SendSms(string messageText, string recipient)
    {
        try
        {
            var message = new SmsMessage(messageText, recipient);
            await Sms.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException ex)
        {
            // Sms is not supported on this device.
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

Si quiere, puede pasar varios destinatarios a un `SmsMessage`:

```csharp
public class SmsTest
{
    public async Task SendSms(string messageText, string[] recipients)
    {
        try
        {
            var message = new SmsMessage(messageText, recipients);
            await Sms.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException ex)
        {
            // Sms is not supported on this device.
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

## <a name="api"></a>API

- [Código fuente de SMS](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Sms)
- [Documentación de API para SMS](xref:Xamarin.Essentials.Sms)
