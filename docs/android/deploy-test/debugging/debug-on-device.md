---
title: Depurar en un dispositivo
description: En este artículo se explica cómo depurar una aplicación de Xamarin.Android en un dispositivo Android físico.
ms.topic: article
ms.prod: xamarin
ms.assetid: 153D3746-A27F-198B-48FE-D219C0133A79
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: df6d2b246d4f0298ff31228b5db82084a55a8dd4
ms.sourcegitcommit: 7b88081a979381094c771421253d8a388b2afc16
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/30/2018
---
# <a name="debug-on-device"></a>Depurar en un dispositivo

_En este artículo se explica cómo depurar una aplicación Xamarin.Android en un dispositivo Android físico._

## <a name="debug-on-device-overview"></a>Información general sobre la depuración en un dispositivo

Es posible depurar una aplicación Xamarin.Android en un dispositivo Android mediante Visual Studio para Mac o Visual Studio. Antes de que realizar la depuración en un dispositivo, se debe [configurar para el desarrollo](~/android/get-started/installation/set-up-device-for-development.md) y conectarlo a su PC o Mac.


## <a name="debug-application"></a>Depuración de una aplicación

Una vez que se conecte un dispositivo al equipo, se realiza la depuración de una aplicación de Xamarin.Android de la misma manera que cualquier otro producto de Xamarin o aplicación .NET. Asegúrese de que la configuración de **Debug (Depuración)** y el dispositivo externo estén seleccionados en el IDE. Esto garantizará que los símbolos de depuración necesarios estén disponibles y que el IDE se pueda conectar a la aplicación en ejecución: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Configuración de depuración seleccionada](debug-on-device-images/image1-vs.png)

A continuación, se establece un punto de interrupción en el código:

![Punto de interrupción establecido en la línea de código](debug-on-device-images/image2-vs.png)

Una vez seleccionado el dispositivo, Xamarin.Android se conecta al dispositivo, implementa la aplicación y, a continuación, la ejecuta. Al alcanzar el punto de interrupción, el depurador detiene la aplicación, lo que permite que la aplicación se depure de forma similar a cualquier otra aplicación de C#: 

![Punto de interrupción alcanzado](debug-on-device-images/image3-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Configuración de depuración seleccionada](debug-on-device-images/image1-xs.png)

A continuación, se establece un punto de interrupción en el código:

![Punto de interrupción establecido en la línea de código](debug-on-device-images/image2-xs.png)

Una vez seleccionado el dispositivo, Xamarin.Android se conecta al dispositivo, implementa la aplicación y, a continuación, la ejecuta. Al alcanzar el punto de interrupción, el depurador detiene la aplicación, lo que permite que la aplicación se depure de forma similar a cualquier otra aplicación de C#: 

![Punto de interrupción alcanzado](debug-on-device-images/image3-xs.png)

-----



## <a name="summary"></a>Resumen

En este documento se describe cómo depurar una aplicación de Xamarin.Android estableciendo un punto de interrupción y seleccionando el dispositivo de destino.


## <a name="related-links"></a>Vínculos relacionados

- [Configurar el dispositivo para el desarrollo](~/android/get-started/installation/set-up-device-for-development.md)
- [Establecer el atributo Debuggable](~/android/deploy-test/debuggable-attribute.md)
