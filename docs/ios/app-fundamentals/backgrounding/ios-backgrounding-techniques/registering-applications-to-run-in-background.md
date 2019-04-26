---
title: Registro de aplicaciones de Xamarin.iOS se ejecutan en segundo plano
description: Este documento describe cómo registrar una aplicación de Xamarin.iOS para ejecutarse en segundo plano. Describe Audio aplicaciones, VoIP aplicaciones, accesorios externos y bluetooth y mucho más.
ms.prod: xamarin
ms.assetid: 8F89BE63-DDB5-4740-A69D-F60AEB21150D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: a0a66571d0249ef6fd65ff382f14c38f48a8af37
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61393719"
---
# <a name="registering-xamarinios-apps-to-run-in-the-background"></a>Registro de aplicaciones de Xamarin.iOS se ejecutan en segundo plano

Registro de tareas individuales para el fondo privilegios funciona para algunas aplicaciones, pero ¿qué ocurre si una aplicación se denomina constantemente al realizar tareas importantes y de larga ejecución, como obtener instrucciones para el usuario a través de GPS? Las aplicaciones como estas en su lugar se deben registrar como aplicaciones en segundo plano necesarias conocidas.

Registrar una aplicación de señales para iOS que la aplicación debe tener privilegios especiales necesarios para realizar tareas en segundo plano.

## <a name="application-registration-categories"></a>Categorías de registro de aplicaciones

Las aplicaciones registradas pueden dividirse en varias categorías:

-  **Audio** -reproductores de música y otras aplicaciones que funcionan con el contenido de audio se pueden registrar para continuar con la reproducción de audio incluso cuando la aplicación ya no está en primer plano. Si una aplicación de esta categoría intenta hacer algo distinto de reproducción de audio o descarga mientras está en segundo plano, iOS la finalizará.
-  **VoIP** -obtención aplicaciones de voz a través de Internet Protocol (VoIP) de los mismos privilegios que se conceden a las aplicaciones de audio para continuar procesando el audio en segundo plano. También pueden responder según sea necesario a los servicios de VoIP que promoverlas para mantener sus conexiones activas.
-  **Bluetooth y accesorios externos** -reservado para las aplicaciones que necesitan comunicarse con dispositivos Bluetooth y otros accesorios de hardware externo, el registro en estas categorías permite que la aplicación para mantenerse en contacto con el hardware.
-  **Newsstand** -aplicación de quiosco A puede continuar con la sincronización de contenido en segundo plano.
-  **Ubicación** : las aplicaciones que hacen usan de GPS o datos de ubicación de red pueden enviar y recibir actualizaciones de ubicación en segundo plano.
-  **FETCH (iOS 7 +)** : una aplicación que se ha registrado para obtener privilegios de captura en segundo plano pueden comprobar un proveedor de contenido nuevo a intervalos regulares, presentación del usuario con contenido actualizado cuando vuelvan a la aplicación.
-  **Notificaciones remotas (iOS 7 +)** -pueden registrarse para recibir notificaciones de un proveedor de aplicaciones y usar la notificación para iniciar una actualización antes de que el usuario abre la aplicación. Notificaciones pueden proceder en forma de notificaciones de inserción o participar para reactivar la aplicación en modo silencioso.


Se pueden registrar aplicaciones estableciendo el **necesario modos en segundo plano** propiedad en la aplicación *Info.plist*. Puede registrar una aplicación en tantas categorías como lo requiere:

 [![](registering-applications-to-run-in-background-images/bgmodes.png "Establecer los modos en segundo plano")](registering-applications-to-run-in-background-images/bgmodes.png#lightbox)

Para obtener una guía paso a paso para registrar una aplicación para las actualizaciones de ubicación en segundo plano, consulte la [tutorial de ubicación en segundo plano](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/location-walkthrough.md).

## <a name="application-does-not-run-in-background-property"></a>Aplicación no se ejecuta en la propiedad Background

Otra propiedad que se puede establecer en *Info.plist* es el *aplicación no se ejecuta en segundo plano*, o `UIApplicationExitsOnSuspend` propiedad:

 [![](registering-applications-to-run-in-background-images/plist.png "Deshabilitar ejecución en segundo plano")](registering-applications-to-run-in-background-images/plist.png#lightbox)

Esto tiene el mismo efecto que establecer la configuración de actualización de aplicación en segundo plano a desactivado en iOS 7 +, excepto que solo puede cambiarse en el lado del desarrollador y está disponible para iOS 4 y versiones posteriores. La aplicación se suspenderá inmediatamente después de escribir el fondo y no podrá realizar cualquier procesamiento.

Utilice esta propiedad si la aplicación no está diseñada para controlar el procesamiento en segundo plano, ya que ayuda a evitar un comportamiento inesperado.

## <a name="background-fetch-and-remote-notifications"></a>Captura de fondo y las notificaciones remotas

Captura de fondo y las notificaciones remotas son categorías especiales de registro introducidas en iOS 7. Estas categorías permiten a las aplicaciones recibir contenido nuevo de un proveedor y actualizan en segundo plano. La siguiente sección explora fetch y notificaciones remotas con mayor detalle y también introduce el reconocimiento de ubicación como medio para actualizar una aplicación en segundo plano en iOS 6.
