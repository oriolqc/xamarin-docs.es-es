---
title: Conceptos básicos de aplicación Xamarin.Android
description: Conceptos de las aplicaciones principales
ms.prod: xamarin
ms.assetid: 935B8BFE-23B7-4239-5C87-F4A503B889CB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: caa51fa0a70da1b799d56c706e6de974f61a14d9
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
ms.locfileid: "32436435"
---
# <a name="xamarinandroid-application-fundamentals"></a>Conceptos básicos de aplicación Xamarin.Android

Esta sección proporciona a una guía sobre algunas de las tareas de cosas o conceptos que los desarrolladores deben tener en cuenta al desarrollar aplicaciones de Android más comunes.

## <a name="accessibilityandroidapp-fundamentalsaccessibilitymd"></a>[Accesibilidad](~/android/app-fundamentals/accessibility.md)

Esta página describe cómo usar las API de accesibilidad Android para compilar aplicaciones de acuerdo con la [lista de comprobación de accesibilidad](~/cross-platform/app-fundamentals/accessibility.md).

##  <a name="understanding-android-api-levelsandroidapp-fundamentalsandroid-api-levelsmd"></a>[Descripción de los niveles de API de Android](~/android/app-fundamentals/android-api-levels.md)

Esta guía describe cómo Android usa niveles de API para administrar la compatibilidad de aplicaciones en las distintas versiones de Android, y se explica cómo configurar el proyecto de Xamarin.Android para implementar estos niveles de API de la aplicación. Además, esta guía explica cómo escribir código en tiempo de ejecución que se ocupa de diferentes niveles de API y proporciona una lista de referencia de todos los niveles de API de Android, números de versión (por ejemplo, Android 8.0), Android nombres de código (por ejemplo, Oreos) y los códigos de versión de compilación.



##  <a name="resources-in-androidandroidapp-fundamentalsresources-in-androidindexmd"></a>[Recursos de Android](~/android/app-fundamentals/resources-in-android/index.md)

En este artículo introduce el concepto de recursos Android en documentos y Xamarin.Android cómo utilizarlas. Se describe cómo usar los recursos en la aplicación Android para admitir varios dispositivos, incluidos las densidades y diferentes tamaños de pantalla y localización de la aplicación.




##  <a name="activity-lifecycleandroidapp-fundamentalsactivity-lifecycleindexmd"></a>[Ciclo de vida de la actividad](~/android/app-fundamentals/activity-lifecycle/index.md)

Las actividades son un bloque de creación fundamental de las aplicaciones de Android y pueden existir en un número de estados diferentes. El ciclo de vida de actividad comienza con la creación de instancias y termina con la destrucción e incluye muchos Estados en medio. Cuando una actividad cambia de estado, se llama al método de evento adecuado del ciclo de vida, le notifica la actividad de cambio de estado inminente y lo que le permite ejecutar el código para adaptarse a ese cambio. Este artículo examina el ciclo de vida de las actividades y explica la responsabilidad que tiene una actividad durante cada uno de estos cambios de estado como parte de una aplicación con comportamiento correcto y confiable.

##  <a name="localizationandroidapp-fundamentalslocalizationmd"></a>[Localización](~/android/app-fundamentals/localization.md)

Este artículo explica cómo localizar una Xamarin.Android a otros idiomas mediante traducir cadenas y las imágenes alternativas.

## <a name="servicesandroidapp-fundamentalsservicesindexmd"></a>[Servicios](~/android/app-fundamentals/services/index.md)

Este artículo tratan los servicios de Android, que son componentes de Android que permiten que el trabajo se realiza en segundo plano. Explica los diferentes escenarios apropiados para servicios y muestra cómo se implementan para realizar tareas en segundo plano de larga ejecución, así como para proporcionar una interfaz para las llamadas a procedimiento remoto.

## <a name="broadcast-receiversandroidapp-fundamentalsbroadcast-receiversmd"></a>[Receptores de difusión](~/android/app-fundamentals/broadcast-receivers.md)

Esta guía describe cómo crear y usar receptores de difusión, un componente de Android que responde a las difusiones de todo el sistema, en Xamarin.Android.



##  <a name="permissionsandroidapp-fundamentalspermissionsmd"></a>[Permisos](~/android/app-fundamentals/permissions.md)

Puede usar la compatibilidad con herramientas integrada en Visual Studio para Mac o en Visual Studio para crear y agregar permisos para el manifiesto de Android. Este documento describe cómo agregar permisos de Visual Studio y Xamarin Studio.



##  <a name="graphics-and-animationandroidapp-fundamentalsgraphics-and-animationmd"></a>[Gráficos y animación](~/android/app-fundamentals/graphics-and-animation.md)

Android proporciona un marco muy amplio y diverso para admitir las animaciones y los gráficos 2D. Este documento presenta estos marcos de trabajo y explica cómo crear animaciones y gráficos personalizados y usarlos en una aplicación Xamarin.Android.


##  <a name="cpu-architecturesandroidapp-fundamentalscpu-architecturesmd"></a>[Arquitecturas de CPU](~/android/app-fundamentals/cpu-architectures.md)

Xamarin.Android es compatible con varias arquitecturas de CPU, incluidos los dispositivos de 32 bits y 64 bits. Este artículo explica cómo elegir como destino una aplicación a una o varias arquitecturas de CPU compatible con Android.




##  <a name="handling-rotationandroidapp-fundamentalshandling-rotationmd"></a>[Control de giro](~/android/app-fundamentals/handling-rotation.md)

En este artículo se describe cómo controlar los cambios de orientación de dispositivo en Xamarin.Android. Se describe cómo trabajar con el sistema Android recursos para cargar automáticamente los recursos para una orientación de dispositivo en particular, así como el modo controlar mediante programación la orientación cambia. A continuación, describen las técnicas para mantener el estado cuando se gira un dispositivo.



##  <a name="android-audioandroidapp-fundamentalsandroid-audiomd"></a>[Audio Android](~/android/app-fundamentals/android-audio.md)

El sistema operativo Android proporciona una amplia compatibilidad para multimedia, que abarca audio y vídeo. Esta guía se centra en audio en Android y cubre la reproducción y grabación de audio con el Reproductor de audio integrado y clases de grabadora, así como la API de audio de bajo nivel. También explica cómo trabajar con eventos de Audio de difusión por otras aplicaciones, para que los desarrolladores pueden crear aplicaciones con comportamiento correcto.




##  <a name="notificationsandroidapp-fundamentalsnotificationsindexmd"></a>[Notificaciones](~/android/app-fundamentals/notifications/index.md)

Esta sección explica cómo implementar notificaciones locales y remotas en Xamarin.Android. Describe los distintos elementos de interfaz de usuario de una notificación de Android y se describe la API del implica crear y mostrar una notificación. Para las notificaciones remotas, se explican Google Cloud Messaging tanto Firebase Cloud Messaging. Se incluyen ejemplos de código y tutoriales paso a paso.



##  <a name="touchandroidapp-fundamentalstouchindexmd"></a>[Entrada táctil](~/android/app-fundamentals/touch/index.md)

En esta sección se explica que los conceptos y detalles de implementación de gestos de toque en Android. API táctil se introdujo y explica seguidas de una exploración de identificadores de gestos.



##  <a name="httpclient-stack-and-ssltlsandroidapp-fundamentalshttp-stackmd"></a>[Pila HttpClient y SSL/TLS](~/android/app-fundamentals/http-stack.md)

En esta sección se explica los selectores HttpClient pila y la implementación de SSL/TLS para Android. Estas opciones determinan la implementación HttpClient y SSL/TLS que se usará en las aplicaciones de Xamarin.Android.


##  <a name="writing-responsive-applicationswriting-responsive-appsmd"></a>[Escribir aplicaciones con capacidad de respuesta](writing-responsive-apps.md)

Este artículo describe cómo usar subprocesos para que una aplicación Xamarin.Android siga respondiendo moviendo tareas de larga duración a un subproceso en segundo plano.