---
title: SiriKit en Xamarin.iOS
description: Este artículo muestra cómo usar SiriKit en una aplicación de Xamarin.iOS para proporcionar servicios que son accesibles para el usuario mediante Siri en un dispositivo iOS.
ms.prod: xamarin
ms.assetid: 84E5681A-F557-4967-AA99-F831169157AA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 9f7cbb3f7d9e448947ec8163a8660616910e750f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117530"
---
# <a name="sirikit-in-xamarinios"></a>SiriKit en Xamarin.iOS

_Este artículo muestra cómo usar SiriKit en una aplicación de Xamarin.iOS para proporcionar servicios que son accesibles para el usuario mediante Siri en un dispositivo iOS._

Nuevo IOS 10, permite que una aplicación de iOS proporcionar servicios que son accesibles para el usuario mediante Siri y la aplicación de mapas de un dispositivo iOS mediante extensiones de aplicación y el nuevo SiriKit **intenciones** y **Intents UI** marcos de trabajo.

Siri funciona con el concepto de **dominios**, sabe de grupos de acciones para las tareas relacionadas. Cada interacción que tiene una aplicación con Siri debe estar en uno de sus dominios de servicio conocido como sigue:

- Una llamada de vídeo o audio.
- Transporte de reserva.
- Administración de sesiones de ejercicios.
- Mensajería.
- Buscar fotografías.
- Enviar o recibir los pagos.

Cuando el usuario realiza una solicitud de Siri que implican a uno de los servicios de la extensión de aplicación, SiriKit envía la extensión de un **intención** objeto que describe la solicitud del usuario junto con cualquier dato de apoyo. La extensión de la aplicación, a continuación, genera adecuado **respuesta** de objeto para el determinado **intención**, que detalla cómo la extensión puede atender la solicitud.

## <a name="understanding-sirikit-conceptsiosplatformsirikitunderstanding-sirikitmd"></a>[Descripción de los conceptos de SiriKit](~/ios/platform/sirikit/understanding-sirikit.md)

En este artículo se trata los conceptos clave que será necesarios para trabajar con SiriKit, una aplicación de Xamarin.iOS. Se describe cómo el nuevo intenciones y puntos de extensión de interfaz de usuario de intenciones y cómo funcionan con la aplicación y vocabulario del usuario para abrir una aplicación a Siri.

## <a name="implementing-sirikitiosplatformsirikitimplementing-sirikitmd"></a>[Implementación de SiriKit](~/ios/platform/sirikit/implementing-sirikit.md)

En este artículo se trata los pasos necesarios para implementar la compatibilidad de SiriKit en las aplicaciones de Xamarin.iOS. El desarrollador debe leer a la Guía de descripción de conceptos de SiriKit anteriormente antes de intentar agregar soporte de SiriKit a una aplicación, como se tratan los conceptos clave que será necesario para una implementación correcta.





## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de ElizaChat](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [Guía de programación de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Referencia de Framework de intenciones](https://developer.apple.com/reference/intents)
- [Referencia de Framework de la interfaz de usuario de intenciones](https://developer.apple.com/reference/intentsui)
