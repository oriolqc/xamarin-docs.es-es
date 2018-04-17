---
title: Mensajería de Google
description: Esta sección contiene a guías que describen cómo implementar aplicaciones de Xamarin.Android con servicios de mensajería de Google.
ms.prod: xamarin
ms.assetid: 85E8DF92-D160-4763-A7D3-458B4C31635F
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/12/2018
ms.openlocfilehash: cf1eaec3dfee7c3457a4614147c9b5564843b2a7
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="google-messaging"></a>Mensajería de Google

_Esta sección contiene a guías que describen cómo implementar aplicaciones de Xamarin.Android con servicios de mensajería de Google._

## <a name="firebase-cloud-messagingfirebase-cloud-messagingmd"></a>[Mensajería en la nube de Firebase](firebase-cloud-messaging.md)

Mensajería de nube de firebase (FCM) es un servicio que facilita la mensajería entre aplicaciones móviles y aplicaciones de servidor. FCM es el sucesor de Google para Google Cloud Messaging. Este artículo proporciona información general de cómo funciona la FCM y proporciona un procedimiento paso a paso para adquirir las credenciales para que la aplicación puede usar los servicios FCM.

## <a name="remote-notifications-with-firebase-cloud-messagingremote-notifications-with-fcmmd"></a>[Notificaciones remotas con Firebase de mensajería en la nube](remote-notifications-with-fcm.md)

Este tutorial proporciona una explicación paso a paso sobre cómo usar la mensajería de nube de Firebase para implementar notificaciones remotas (también denominadas notificaciones de inserción) en una aplicación Xamarin.Android. Muestra cómo implementar las diferentes clases que son necesarios para las comunicaciones con Firebase en la nube de mensajería (FCM), se proporcionan ejemplos de cómo configurar el manifiesto de Android para tener acceso a FCM y muestra la mensajería de nivel inferior mediante el Firebase Consola.

## <a name="google-cloud-messaginggoogle-cloud-messagingmd"></a>[Google Cloud Messaging](google-cloud-messaging.md)

Esta sección proporciona una descripción general de la conexión compartida a Internet (GCM, Google Cloud Messaging) enruta mensajes entre la aplicación y un servidor de aplicaciones, y proporciona un procedimiento paso a paso para adquirir las credenciales para que la aplicación puede usar los servicios GCM. (Tenga en cuenta que se han producido FCM GCM).

> [!NOTE]
> Se han producido GCM [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) (FCM).
> GCM API de cliente y servidor [han quedado en desuso](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html) y ya no estará disponible tan pronto como el 11 de abril de 2019.

## <a name="remote-notifications-with-google-cloud-messagingremote-notifications-with-gcmmd"></a>[Notificaciones remotas con la mensajería de nube de Google](remote-notifications-with-gcm.md)

Esta sección proporciona una explicación paso a paso de cómo implementar notificaciones remotas en Xamarin.Android usan la mensajería de nube de Google.
Se explican los distintos componentes que se deben aprovechar para habilitar la mensajería de nube de Google en una aplicación Android.


