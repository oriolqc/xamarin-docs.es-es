---
title: Principios de la aplicación de Xamarin.Android
description: Conceptos de aplicación
ms.prod: xamarin
ms.assetid: 935B8BFE-23B7-4239-5C87-F4A503B889CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 1d3bd071eeffb77f94a1b5f35f1df59f2d8c7a8a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105563"
---
# <a name="xamarinandroid-application-fundamentals"></a>Principios de la aplicación de Xamarin.Android

En esta sección se proporciona a una guía sobre algunas de las tareas de las cosas o conceptos que los desarrolladores deben tener en cuenta al desarrollar aplicaciones de Android más comunes.

## <a name="accessibilityandroidapp-fundamentalsaccessibilitymd"></a>[Accesibilidad](~/android/app-fundamentals/accessibility.md)

Esta página describe cómo usar las API de accesibilidad de Android para compilar aplicaciones de acuerdo con la [lista de comprobación de accesibilidad](~/cross-platform/app-fundamentals/accessibility.md).

##  <a name="understanding-android-api-levelsandroidapp-fundamentalsandroid-api-levelsmd"></a>[Descripción de los niveles de API de Android](~/android/app-fundamentals/android-api-levels.md)

Esta guía describe cómo Android usa los niveles de API para administrar la compatibilidad de aplicaciones en las distintas versiones de Android, y se explica cómo configurar los valores de proyecto de Xamarin.Android para implementar estos niveles de API en su aplicación. Además, esta guía explica cómo escribir código en tiempo de ejecución que se ocupa de diferentes niveles de API y proporciona una lista de referencia de todos los niveles de API de Android, números de versión (por ejemplo, Android 8.0), los nombres de código Android (por ejemplo, Oreo) y códigos de versión de compilación.



##  <a name="resources-in-androidandroidapp-fundamentalsresources-in-androidindexmd"></a>[Recursos de Android](~/android/app-fundamentals/resources-in-android/index.md)

Este artículo presenta el concepto de recursos de Android en documentos y Xamarin.Android cómo usarlas. Se describe cómo usar los recursos en la aplicación Android para admitir la localización de aplicaciones y varios dispositivos, incluidas las densidades y diferentes tamaños de pantalla.




##  <a name="activity-lifecycleandroidapp-fundamentalsactivity-lifecycleindexmd"></a>[Ciclo de vida de la actividad](~/android/app-fundamentals/activity-lifecycle/index.md)

Las actividades son un pilar fundamental de las aplicaciones de Android y pueden existir en un número de estados diferentes. El ciclo de vida de la actividad comienza con la creación de instancias y termina con la destrucción e incluye muchos Estados entre ellos. Cuando una actividad cambia de estado, se llama al método de evento adecuado del ciclo de vida, notificar a la actividad de la inminente cambio de estado y lo que le permite ejecutar el código para adaptarse a ese cambio. En este artículo examina el ciclo de vida de las actividades y se explica la responsabilidad que tiene una actividad durante cada uno de estos cambios de estado para formar parte de una aplicación confiable y se comporten correctamente.

##  <a name="localizationandroidapp-fundamentalslocalizationmd"></a>[Localización](~/android/app-fundamentals/localization.md)

En este artículo se explica cómo localizar Xamarin.Android a otros idiomas mediante la traducción de cadenas y proporcionar imágenes alternativas.

## <a name="servicesandroidapp-fundamentalsservicesindexmd"></a>[Servicios](~/android/app-fundamentals/services/index.md)

Este artículo tratan los servicios de Android, que son componentes de Android que permiten que el trabajo se realiza en segundo plano. Explica los diferentes escenarios más adecuados para servicios y muestra cómo se implementan ambas para llevar a cabo las tareas de larga ejecución en segundo plano, así como para proporcionar una interfaz para las llamadas a procedimiento remoto.

## <a name="broadcast-receiversandroidapp-fundamentalsbroadcast-receiversmd"></a>[Receptores de difusión](~/android/app-fundamentals/broadcast-receivers.md)

Esta guía explica cómo crear y usar receptores de difusión, un componente de Android que responde a las difusiones de todo el sistema, en Xamarin.Android.



##  <a name="permissionsandroidapp-fundamentalspermissionsmd"></a>[Permisos](~/android/app-fundamentals/permissions.md)

Puede usar la compatibilidad con las herramientas integrada en Visual Studio para Mac o Visual Studio para crear y agregar permisos para el manifiesto de Android. Este documento describe cómo agregar permisos de Visual Studio y Xamarin Studio.



##  <a name="graphics-and-animationandroidapp-fundamentalsgraphics-and-animationmd"></a>[Gráficos y animación](~/android/app-fundamentals/graphics-and-animation.md)

Android proporciona un marco de trabajo muy amplio y diverso para admitir las animaciones y gráficos 2D. Este documento presenta estos marcos de trabajo y explica cómo crear animaciones y gráficos personalizados y usarlos en una aplicación de Xamarin.Android.


##  <a name="cpu-architecturesandroidapp-fundamentalscpu-architecturesmd"></a>[Arquitecturas de CPU](~/android/app-fundamentals/cpu-architectures.md)

Xamarin.Android admite varias arquitecturas de CPU, incluidos los dispositivos de 32 bits y 64 bits. En este artículo se explica cómo elegir como destino una aplicación a una o varias arquitecturas de CPU compatible con Android.




##  <a name="handling-rotationandroidapp-fundamentalshandling-rotationmd"></a>[Control de giro](~/android/app-fundamentals/handling-rotation.md)

En este artículo se describe cómo controlar los cambios de orientación del dispositivo en Xamarin.Android. Se describe cómo trabajar con el sistema de recursos de Android para cargar automáticamente los recursos para una orientación de dispositivo determinado, así cómo controlar mediante programación la orientación cambia. A continuación, se describen técnicas para mantener el estado cuando se gira un dispositivo.



##  <a name="android-audioandroidapp-fundamentalsandroid-audiomd"></a>[Audio de Android](~/android/app-fundamentals/android-audio.md)

El sistema operativo Android proporciona amplia compatibilidad para multimedia, que abarca de audio y vídeo. Esta guía se centra en audio en Android y reproducir y grabar audio mediante las clases de la grabadora y el Reproductor de audio integrado, así como la API de audio de bajo nivel. También explica cómo trabajar con eventos de Audio de difusión por otras aplicaciones, para que los desarrolladores pueden crear aplicaciones y se comporten correctamente.




##  <a name="notificationsandroidapp-fundamentalsnotificationsindexmd"></a>[Notificaciones](~/android/app-fundamentals/notifications/index.md)

Esta sección explica cómo implementar las notificaciones locales y remotas en Xamarin.Android. Describe los distintos elementos de interfaz de usuario de una notificación de Android y habla de la API del implicada en crear y mostrar una notificación. Las notificaciones remotas, se explican tanto Google Cloud Messaging y Firebase Cloud Messaging. Se incluyen ejemplos de código y tutoriales paso a paso.



##  <a name="touchandroidapp-fundamentalstouchindexmd"></a>[Entrada táctil](~/android/app-fundamentals/touch/index.md)

En esta sección se explica que los conceptos y detalles de implementación de gestos de toque en Android. Las API de toque se introdujo y explican seguidas de una exploración de los reconocedores de gestos.



##  <a name="httpclient-stack-and-ssltlsandroidapp-fundamentalshttp-stackmd"></a>[Pila HttpClient y SSL/TLS](~/android/app-fundamentals/http-stack.md)

En esta sección se explica los selectores de pila HttpClient e implementación de SSL/TLS para Android. Esta configuración determina la implementación de HttpClient y SSL/TLS que se usará en las aplicaciones de Xamarin.Android.


##  <a name="writing-responsive-applicationswriting-responsive-appsmd"></a>[Escribir aplicaciones con capacidad de respuesta](writing-responsive-apps.md)

En este artículo se describe cómo usar el subprocesamiento para que una aplicación de Xamarin.Android siga respondiendo moviendo las tareas de larga ejecución en un subproceso en segundo plano.