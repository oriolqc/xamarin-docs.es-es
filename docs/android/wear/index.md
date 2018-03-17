---
title: Android Wear
description: Crear aplicaciones para dispositivos wearable Android.
ms.topic: article
ms.prod: xamarin
ms.assetid: 3BE4A128-2D88-4500-9E48-20375EA99A49
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/16/2018
ms.openlocfilehash: 31114df0b631aea909e82f3a8b836d5ef922d2c1
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/17/2018
---
# <a name="android-wear"></a>Android Wear

Desgaste Android es una versión de Android que está diseñada para dispositivos wearable como inspecciones inteligentes. En esta sección se incluye instrucciones sobre cómo instalar y configurar las herramientas necesarias para el desarrollo de desgaste, un tutorial paso a paso para crear el primer dispositivo de desgaste y una lista de ejemplos que le pueden resultar de para crear su propio usan aplicaciones.

##  <a name="getting-startedandroidwearget-startedindexmd"></a>[Introducción](~/android/wear/get-started/index.md)

Presenta el desgaste Android, se describe cómo instalar y configurar el equipo para el desarrollo de desgaste y proporciona los pasos necesarios para ayudarle a crear y ejecutar la primera aplicación Android desgaste en un emulador o dispositivo de uso.

##  <a name="user-interfaceandroidwearuser-interfaceindexmd"></a>[Interfaz de usuario](~/android/wear/user-interface/index.md)

Explica específicos de desgaste Android controlan y proporcionan vínculos a ejemplos que muestran cómo utilizar estos controles.

##  <a name="platform-featuresandroidwearplatformindexmd"></a>[Características de la plataforma](~/android/wear/platform/index.md)

Documentos de esta sección tratan características específicas del desgaste Android. Aquí encontrará un tema que describe cómo crear un WatchFace.

##  <a name="screen-sizesandroidwearscreen-sizesmd"></a>[Tamaños de pantalla](~/android/wear/screen-sizes.md)

Obtener una vista previa y optimizar su interfaz de usuario para los tamaños de pantalla disponibles.

##  <a name="deployment--testingandroidweardeploy-testindexmd"></a>[Implementación y pruebas](~/android/wear/deploy-test/index.md)

Explica cómo implementar la aplicación Android desgaste en un dispositivo Android desgaste o al emulador Android configurado para el uso. También incluye sugerencias y obtener información acerca de cómo configurar una conexión de Bluetooth entre el equipo de desarrollo y un dispositivo Android de depuración.

##  <a name="wear-apishttpsdeveloperandroidcomreferenceandroidsupportwearable"></a>[Usan las API](https://developer.android.com/reference/android/support/wearable)

El sitio para desarrolladores de Android proporciona información detallada acerca de la clave API desgaste como [actividad Wearable](https://developer.android.com/reference/android/support/wearable/activity/package-summary.html), [intentos](https://developer.android.com/reference/com/google/android/wearable/intent/package-summary.html), [autenticación](https://developer.android.com/reference/android/support/wearable/authentication/package-summary.html), [ Complicaciones](https://developer.android.com/reference/android/support/wearable/complications/package-summary.html), [complicaciones representación](https://developer.android.com/reference/android/support/wearable/complications/rendering/package-summary.html), [notificaciones](https://developer.android.com/reference/android/support/wearable/notifications/package-summary.html), [vistas](https://developer.android.com/reference/android/support/wearable/view/package-summary.html), y [WatchFace](https://developer.android.com/reference/android/support/wearable/watchface/package-summary.html).



## <a name="samples"></a>Muestras

Puede encontrar un número de [ejemplos](https://developer.xamarin.com/samples/android/Android%20Wear/) con Android usan (o ir directamente a [github](https://github.com/xamarin/monodroid-samples/tree/master/wear)). 

|Ejemplo|Descripción|Captura de pantalla|
|--- |--- |--- |
|[SkeletonWear](https://developer.xamarin.com/samples/SkeletonWear/)|Un ejemplo sencillo de los conceptos básicos de proyectos wearable, incluidos FilterUserControl y notificaciones interactivas.|![Captura de pantalla de Skeletonwear](images/skeleton.png)|
|[WatchViewStub](https://developer.xamarin.com/samples/WatchViewStub/)|Una simple demostración del control WatchViewStub que detecta la forma de pantalla y carga automáticamente el diseño correcto.  Ver cómo funciona WatchViewStub en el **Resources/layout/main_actvity.xml** diseño.|![Captura de pantalla de WatchViewStub](images/watchview.png)|
|[RecipeAssistant](https://developer.xamarin.com/samples/RecipeAssistant/)|Demostración de páginas de notificación de desgaste, en forma de pasos de receta. Las notificaciones se crean en RecipeService.cs.|![Captura de pantalla de RecipeAssistant](images/recipeassist.png)|
|[ElizaChat](https://developer.xamarin.com/samples/ElizaChat/)|Ejemplo divertida de interactuar con un asistente de"personal" llama Eliza, usar desgaste interactivas notificaciones para crear una conversación mediante respuestas preestablecidas.|![Captura de pantalla de ElizaChat](images/eliza.png)|
|[GridViewPager](https://developer.xamarin.com/samples/GridViewPager/)|FilterUserControl implementa el modelo de navegación 2D, donde el usuario lectores de crédito verticalmente y horizontalmente para navegar por las opciones y el contenido.|![Captura de pantalla de FilterUserControl](images/gridviewpager.png)|
|[WatchFace](https://developer.xamarin.com/samples/monodroid/wear/WatchFace)|WatchFace es una cara de inspección personalizado con estilo de señales analógicas hora, minuto y segundo manos. Este ejemplo muestra cómo crear un servicio de cara de inspección que dibuja la hora actual y los eventos de cambio de visibilidad y el modo de ambiente de identificadores. Incluye un receptor de difusión que realiza escuchas para los cambios de zona horaria y se actualiza automáticamente el tiempo en consecuencia.|![Captura de pantalla de WatchFace](images/gridviewpager.png)|


##  <a name="videos"></a>Vídeos

Consulte estos vídeo vínculos que tratan Xamarin.Android con desgaste son compatibles con:

|Descripción|Captura de pantalla|
|--- |--- |
|[Android L y mucho más](http://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/) &ndash; la vista previa de desarrollador L Android introdujo una gran cantidad de nuevas API para que los desarrolladores aprovechar las ventajas de, como Material de diseño, las notificaciones y las animaciones nuevas, para nombrar algunos.|![Captura de pantalla de vídeo de presentación](images/video-android-l.png)|
|[C# está en mi oídos y en los ojos: Google Glass y desgaste Android](https://www.youtube.com/watch?v=80H8tXByZQc) &ndash; Wearable informática podría parecer algo desde el futuro (o un episodio de Inspector Gadget), pero muchas personas ya están adoptando el futuro hoy en día. Los desarrolladores de C# lo saben y ya tienen las herramientas y los conocimientos para aprovechar la eficacia de los dispositivos wearable (de evolucionar 2014).|![Captura de pantalla de vídeo de presentación](images/video-eyes-ears.png)|
|[' S new en Xamarin.Android](https://www.youtube.com/watch?v=Gpqc2XZIQfU) &ndash; L Android, desgaste Android, TV Android, Auto Android, Material de diseño y arte; lo que hace este medio a usted como desarrollador de Xamarin? de evolucionar 2014.|![Captura de pantalla de vídeo de presentación](Images/video-whats-new.png)|


<!--

March 18
http://blog.xamarin.com/android-wear/

August 14
http://blog.xamarin.com/android-l-developer-preview-android-wear-support/

August 27
http://blog.xamarin.com/tips-for-your-first-android-wear-app/

Watch Face
https://github.com/Redth/Xamarin.Wear.WatchFace
-->
