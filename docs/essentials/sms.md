---
title: 'Xamarin.Essentials: SMS'
description: La clase Sms en Xamarin.Essentials permite a una aplicación abrir la aplicación predeterminada de SMS con un mensaje para enviar a un destinatario especificado.
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: a93a67b83ea8f435a5e3ad5d26e1d6cbbb7092f7
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815602"
---
# <a name="xamarinessentials-sms"></a>Xamarin.Essentials: SMS

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

La clase **Sms** permite a una aplicación abrir la aplicación predeterminada de SMS con un mensaje para enviar a un destinatario especificado.

## <a name="using-sms"></a>Uso de Sms

Agregue la referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La clase SMS funciona mediante una llamada al metodo `ComposeAsync` con un objeto `SmsMessage` que contiene el destinatario y el cuerpo del mensaje, de los cuales ambos son opcionales.

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

## <a name="api"></a>API

- [Código fuente de SMS](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Sms)
- [Documentación de la API de SMS](xref:Xamarin.Essentials.Sms)
