---
title: SiriKit
description: "Este artículo muestra cómo usar SiriKit en una aplicación Xamarin.iOS para proporcionar servicios que son accesibles para el usuario mediante Siri en un dispositivo iOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 4E1FF652-28F0-4566-B383-9D12664401A4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: c4fdf61b35ca28af82e3890242d54a75e50d2f82
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="sirikit"></a>SiriKit

_Este artículo muestra cómo usar SiriKit en una aplicación Xamarin.iOS para proporcionar servicios que son accesibles para el usuario mediante Siri en un dispositivo iOS._

Nuevo en iOS 10, SiriKit permite que una aplicación iOS proporcionar servicios que son accesibles para el usuario mediante Siri y la aplicación de mapas de un dispositivo iOS mediante extensiones de aplicaciones y a los nuevos **intentos** y **calidades de interfaz de usuario** marcos de trabajo.

Siri funciona con el concepto de **dominios**, grupos de conocer las acciones para las tareas relacionadas. Cada interacción que tiene una aplicación con Siri debe estar en uno de sus dominios de servicio conocido, como se indica a continuación:

- Una llamada de vídeo o audio.
- Transporte de reserva.
- Administrar entrenamientos.
- Mensajería.
- Buscar fotografías.
- Enviar o recibir pagos.

Cuando el usuario realiza una solicitud de Siri que implican a uno de los servicios de extensión de la aplicación, SiriKit envía la extensión de un **intención** objeto que describe la solicitud del usuario junto con cualquier dato de apoyo. La extensión de la aplicación, a continuación, genera la correspondiente **respuesta** de objeto para el determinado **intención**, que detalla cómo la extensión puede atender la solicitud.

## <a name="understanding-sirikit-conceptsiosplatformsirikitunderstanding-sirikitmd"></a>[Descripción de los conceptos de SiriKit](~/ios/platform/sirikit/understanding-sirikit.md)

Este artículo tratan los conceptos clave que será necesarios para trabajar con SiriKit en una aplicación de Xamarin.iOS. Cubre los nuevos intentos y puntos de extensión de interfaz de usuario de intentos y cómo funcionan con la aplicación y vocabulario del usuario para abrir una aplicación en Siri.

## <a name="implementing-sirikitiosplatformsirikitimplementing-sirikitmd"></a>[Implementar SiriKit](~/ios/platform/sirikit/implementing-sirikit.md)

Este artículo tratan los pasos necesarios para implementar la compatibilidad de SiriKit en las aplicaciones de Xamarin.iOS. El desarrollador debe leer a la Guía de descripción de conceptos SiriKit anteriormente antes de intentar agregar compatibilidad de SiriKit a una aplicación, como se tratan los conceptos clave que será necesario para la implementación correcta.





## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de ElizaChat](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [Guía de programación de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Referencia de marco de intentos](https://developer.apple.com/reference/intents)
- [Referencia de marco de interfaz de usuario de intentos](https://developer.apple.com/reference/intentsui)
