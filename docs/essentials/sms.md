---
title: SMS Xamarin.Essentials
description: La clase de Sms permite a una aplicación abrir la aplicación de SMS predeterminado con un mensaje especificado para enviar a un destinatario.
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 460aea01381934f1862946c6e17e314560e889f2
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="xamarinessentials-sms"></a>SMS Xamarin.Essentials

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **Sms** clase permite a una aplicación abrir la aplicación de SMS predeterminado con un mensaje especificado para enviar a un destinatario.

## <a name="using-sms"></a>Uso de Sms

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad SMS funciona mediante una llamada a la `ComposeAsync` método un `SmsMessage` que contiene el destinatario del mensaje y el cuerpo del mensaje, ambos de los cuales son opcionales.

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

- [Código de origen de SMS](https://github.com/xamarin/Essentials/tree/master/Essentials/Sms)
- [Documentación de la API de SMS](xref:Xamarin.Essentials.Sms)
