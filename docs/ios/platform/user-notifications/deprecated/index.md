---
title: Tecnologías de notificación en desuso de Xamarin.iOS
description: Este documento describe las tecnologías de notificación de iOS que han quedado obsoletas en favor del marco de trabajo de las notificaciones de usuario, introducida en iOS 10.
ms.prod: xamarin
ms.assetid: 20C4F6E5-56DF-4A85-BBF0-E38C88586307
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/07/2016
ms.openlocfilehash: 4becc5e296fb6e2496d9ffd863f7137419480262
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788558"
---
# <a name="deprecated-notification-technologies-in-xamarinios"></a>Tecnologías de notificación en desuso de Xamarin.iOS

Esta sección muestra cómo implementar local y las notificaciones de inserción en Xamarin.iOS. Se explican los distintos elementos de interfaz de usuario de una notificación de iOS y se describe la API del implica crear y mostrar una notificación.

> [!IMPORTANT]
> La información de esta sección pertenece a iOS 9 y anteriores, se han quedado aquí para compatibilidad con versiones anteriores de iOS. Para iOS 10 y versiones posteriores, consulte la [Guía de la estructura de notificación de usuario](~/ios/platform/user-notifications/index.md) para admitir tanto locales como remotos de notificación en un dispositivo iOS.

## <a name="sections"></a>Secciones

<a name="Local Notifications In iOS" />

##  <a name="local-notifications-in-ioslocal-notifications-in-iosmd"></a>[Notificaciones locales en iOS](local-notifications-in-ios.md)

En esta sección se describe cómo implementar notificaciones locales en Xamarin.iOS. Se explican los distintos elementos de interfaz de usuario de una notificación de iOS y se describe la API del implica crear y mostrar una notificación.

<a name="Local Notifications Walkthrough" />

##  <a name="walkthrough---using-local-notifications-in-xamarinioslocal-notifications-in-ios-walkthroughmd"></a>[Tutorial: uso de notificaciones locales en Xamarin.iOS](local-notifications-in-ios-walkthrough.md)

En esta sección le mostraremos cómo usar notificaciones locales en una aplicación de Xamarin.iOS. Mostrarán los conceptos básicos de la creación y publicación de una notificación que se mostrará una alerta cuando recibe la aplicación.

<a name="Remote Notifications In iOS" />

##  <a name="remote-notifications-in-iosremote-notifications-in-iosmd"></a>[Notificaciones remotas de iOS](remote-notifications-in-ios.md)

Esta sección describirá las notificaciones de inserción en iOS. Incluye el servicio de puerta de enlace de notificaciones (APNS) de Apple Push y el papel que desempeña en las notificaciones de publicación para las aplicaciones de iOS. Explicará cómo crear los certificados de seguridad necesarios para habilitar las notificaciones de inserción y analizar. Por último, en esta sección se describe algunas de las tareas de mantenimiento de manera que los servidores de aplicaciones deben realizar para realizar un seguimiento de los dispositivos móviles de cliente.

## <a name="related-links"></a>Vínculos relacionados

- [Notificaciones (ejemplo)](https://developer.xamarin.com/samples/monotouch/Notifications/)
