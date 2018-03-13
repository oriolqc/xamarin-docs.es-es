---
title: Notificaciones en Xamarin.Android
ms.topic: article
ms.prod: xamarin
ms.assetid: 2E54F1D0-45F4-43A7-B3A3-4F483B7150CB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: bc39faa37adae660a7751313d0d573237fadce94
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="notifications-in-xamarinandroid"></a>Notificaciones en Xamarin.Android


## <a name="overview"></a>Información general

Esta sección explica cómo implementar las notificaciones en Xamarin.Android. Describe los distintos elementos de interfaz de usuario de una notificación de Android y se describe la API del implica crear y mostrar una notificación.


## <a name="sections"></a>Secciones

### <a name="local-notifications-in-androidlocal-notificationsmd"></a>[Notificaciones locales en Android](local-notifications.md)

Esta sección explica cómo implementar notificaciones locales en Xamarin.Android. Describe los distintos elementos de interfaz de usuario de una notificación de Android y se describe la API del implica crear y mostrar una notificación. 

### <a name="walkthrough---using-local-notifications-in-xamarinandroidlocal-notifications-walkthroughmd"></a>[Tutorial: usar notificaciones Local en Xamarin.Android](local-notifications-walkthrough.md)  
 
Este tutorial describe cómo usar notificaciones locales en una aplicación Xamarin.Android. Muestra los conceptos básicos de la creación y publicación de una notificación. Cuando el usuario hace clic en la notificación en el cajón de notificaciones se inicia una segunda actividad. 


## <a name="for-further-reading"></a>Más información

[Mensajería de nube de firebase](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) &ndash; Firebase en la nube de mensajería (FCM) es un servicio que facilita la mensajería entre aplicaciones móviles y aplicaciones de servidor. Mensajería de nube de firebase se puede usar para implementar notificaciones remotas (también denominadas notificaciones de inserción) en aplicaciones de Xamarin.Android.

[Notificaciones de](http://developer.android.com/guide/topics/ui/notifiers/notifications.html) &ndash; tema de desarrollador Android esta es la guía definitiva para notificaciones Android. Incluye un diseño de sección de consideraciones que le ayuda a diseñar las notificaciones de modo que se ajusten a las instrucciones de la interfaz de usuario de Android. Proporciona más información sobre la navegación de preserviing al iniciar una actividad y se explica cómo mostrar el progreso de una notificación y controlar la reproducción del medio en la pantalla de bloqueo. 

[NotificationListenerService](https://developer.xamarin.com/api/type/Android.Service.Notification.NotificationListenerService/) &ndash; Android este servicio permite a la aplicación escuchar (e interactuar con) todas las notificaciones se registran en el dispositivo Android, no solo las notificaciones que la aplicación se registra para recibir. Tenga en cuenta que el usuario debe asignar explícitamente permisos a la aplicación para que pueda escuchar las notificaciones en el dispositivo.





## <a name="related-links"></a>Vínculos relacionados

- [Notificaciones locales (ejemplo)](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Notificaciones remotas (ejemplo)](https://developer.xamarin.com/samples/monodroid/RemoteNotifications/)
