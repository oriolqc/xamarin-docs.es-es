---
title: Registrar las aplicaciones se ejecuten en segundo plano
ms.prod: xamarin
ms.assetid: 8F89BE63-DDB5-4740-A69D-F60AEB21150D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 58ee68f765372094d68c3cf30ed6a631a67fe313
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="registering-applications-to-run-in-the-background"></a>Registrar las aplicaciones se ejecuten en segundo plano

Registrar tareas individuales para fondo privilegios funciona para algunas aplicaciones, pero ¿qué ocurre si una aplicación se pidió constantemente para realizar tareas importantes y de larga ejecución, como obtener instrucciones para el usuario a través de GPS? Este tipo de aplicaciones en su lugar, deben registrarse como aplicaciones conocidas de segundo plano necesarias.

Registrar una aplicación envía una señal a iOS que la aplicación debe emitirse privilegios especiales necesarios para realizar tareas en segundo plano.

## <a name="application-registration-categories"></a>Categorías de registro de aplicación

Aplicaciones registradas pueden se dividen en varias categorías:

-  **Audio** -reproductores de música y otras aplicaciones que funcionan con el contenido de audio pueden que esté registrados para continuar con la reproducción de audio incluso cuando la aplicación ya no está en primer plano. Si una aplicación de esta categoría intenta hacer algo distinto de reproducción de audio o descarga en segundo plano, se terminará iOS.
-  **VoIP** -obtención aplicaciones de voz sobre IP (VoIP) de los mismos privilegios concedidos a las aplicaciones de audio para realizar procesamiento de audio en segundo plano. También pueden responder según sea necesario a los servicios de VoIP que ofrece para mantener activa sus conexiones.
-  **Bluetooth y accesorios externos** -reservado para las aplicaciones que necesitan comunicarse con dispositivos Bluetooth y otros accesorios de hardware externo, registro en estas categorías permite que la aplicación debe permanecer conectado al hardware.
-  **Quiosco** -aplicación de un quiosco puede continuar con la sincronización de contenido en segundo plano.
-  **Ubicación** : aplicaciones que hacen uso de GPS o datos de ubicación de red pueden enviar y recibir actualizaciones de la ubicación en segundo plano.
-  **FETCH (iOS 7 +)** : una aplicación que se ha registrado para obtener privilegios de captura de fondo pueden comprobar un proveedor para el contenido nuevo a intervalos regulares, presentar al usuario con contenido actualizado cuando regresa a la aplicación.
-  **Notificaciones remotas (iOS 7 +)** -pueden registrarse para recibir notificaciones de un proveedor de aplicaciones y use la notificación para iniciar una actualización antes de que el usuario abre la aplicación. Las notificaciones pueden aparecen en forma de notificaciones de inserción o participar para reactivar la aplicación en modo silencioso.


Las aplicaciones se pueden registrar estableciendo la **necesario modos en segundo plano** propiedad en la aplicación *Info.plist*. Una aplicación puede registrar en tantas categorías como requiere:

 [![](registering-applications-to-run-in-background-images/bgmodes.png "Establecer los modos en segundo plano")](registering-applications-to-run-in-background-images/bgmodes.png#lightbox)

Para obtener una guía paso a paso para registrar una aplicación para actualizaciones de la ubicación de segundo plano, consulte la [tutorial de ubicación de fondo](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/location-walkthrough.md).

## <a name="application-does-not-run-in-background-property"></a>Aplicación no se ejecuta en segundo plano propiedad

Otra propiedad que se pueden establecer en *Info.plist* es el *aplicación no se ejecuta en segundo plano*, o `UIApplicationExitsOnSuspend` propiedad:

 [![](registering-applications-to-run-in-background-images/plist.png "Deshabilitación de segundo plano que ejecuta")](registering-applications-to-run-in-background-images/plist.png#lightbox)

Esto tiene el mismo efecto que establecer la configuración de aplicación actualización en segundo plano en off en iOS 7 +, pero solo puede cambiarse en el lado de desarrollador y está disponible para iOS 4 y versiones posteriores. La aplicación se suspenderá inmediatamente después de escribir el fondo y no podrá realizar cualquier procesamiento.

Utilice esta propiedad si la aplicación no está diseñada para controlar el procesamiento en segundo plano, ya que ayuda a evitar un comportamiento inesperado.

## <a name="background-fetch-and-remote-notifications"></a>Captura de fondo y notificaciones remotas

Captura de fondo y notificaciones remotas son las categorías de registro especiales introducidos en iOS 7. Estas categorías permiten a las aplicaciones recibir el nuevo contenido de un proveedor y actualizar en segundo plano. La siguiente sección explora fetch y notificaciones remotas con más detalle y presenta también reconocimiento de ubicación como medio para actualizar una aplicación en segundo plano en iOS 6.
