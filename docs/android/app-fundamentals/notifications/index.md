---
title: Notificaciones en Xamarin.Android
ms.prod: xamarin
ms.assetid: 2E54F1D0-45F4-43A7-B3A3-4F483B7150CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
---

# <a name="notifications-in-xamarinandroid"></a>Notificaciones en Xamarin.Android


## <a name="overview"></a>Información general

Esta sección explica cómo implementar notificaciones en Xamarin.Android. Describe los distintos elementos de interfaz de usuario de una notificación de Android y habla de la API del implicada en crear y mostrar una notificación.


## <a name="sections"></a>Secciones

### <a name="local-notifications-in-androidlocal-notificationsmd"></a>[Notificaciones locales en Android](local-notifications.md)

Esta sección explica cómo implementar notificaciones locales en Xamarin.Android. Se describen los distintos elementos de interfaz de usuario de una notificación de Android y se describe la API del implicada en crear y mostrar una notificación. 

### <a name="walkthrough---using-local-notifications-in-xamarinandroidlocal-notifications-walkthroughmd"></a>[Tutorial: uso de notificaciones locales en Xamarin.Android](local-notifications-walkthrough.md)  
 
Este tutorial describe cómo usar notificaciones locales en una aplicación de Xamarin.Android. Muestra los conceptos básicos de creación y publicación de una notificación. Cuando el usuario hace clic en la notificación en el cajón de notificaciones se inicia una segunda actividad. 


## <a name="for-further-reading"></a>Más información

[Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) &ndash; Firebase Cloud Messaging (FCM) es un servicio que facilita la mensajería entre aplicaciones móviles y aplicaciones de servidor. Firebase Cloud Messaging puede usarse para implementar notificaciones remotas (también denominadas notificaciones push) en las aplicaciones de Xamarin.Android.

[Las notificaciones](https://developer.android.com/guide/topics/ui/notifiers/notifications.html) &ndash; tema para desarrolladores de Android esta es la guía definitiva para las notificaciones de Android. Incluye un diseño en la sección de consideraciones que le ayuda a diseñar las notificaciones para que se adapten a las instrucciones de la interfaz de usuario de Android. Obtener información general sobre navegación preserviing proporciona al iniciar una actividad y se explica cómo mostrar el progreso en una notificación y control de reproducción de multimedia en la pantalla de bloqueo. 

[NotificationListenerService](https://developer.xamarin.com/api/type/Android.Service.Notification.NotificationListenerService/) &ndash; Android este servicio permite que la aplicación escuchar (e interactuar con) todas las notificaciones se registran en el dispositivo Android, no solo las notificaciones que la aplicación se registra para recibir. Tenga en cuenta que el usuario debe otorgar explícitamente permiso a la aplicación para que sea capaz de escuchar notificaciones en el dispositivo.





## <a name="related-links"></a>Vínculos relacionados

- [Notificaciones locales (ejemplo)](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Notificaciones remotas (ejemplo)](https://developer.xamarin.com/samples/monodroid/RemoteNotifications/)
