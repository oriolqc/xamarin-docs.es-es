---
title: Android Wear
description: Creación de aplicaciones para Android dispositivos portátiles.
ms.prod: xamarin
ms.assetid: 3BE4A128-2D88-4500-9E48-20375EA99A49
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/16/2018
ms.openlocfilehash: bd8973b59853a4898336ba153de4835bad46e232
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113058"
---
# <a name="android-wear"></a>Android Wear

Android Wear es una versión de Android que está diseñado para dispositivos portátiles, como relojes inteligentes. Esta sección incluye instrucciones sobre cómo instalar y configurar las herramientas necesarias para el desarrollo de desgaste, un tutorial paso a paso para crear el primer dispositivo Wear y una lista de ejemplos que se pueden consultar para crear su propia desgaste de las aplicaciones.

##  <a name="getting-startedandroidwearget-startedindexmd"></a>[Introducción](~/android/wear/get-started/index.md)

Presenta Android Wear, se describe cómo instalar y configurar el equipo para el desarrollo de desgaste y proporciona los pasos necesarios para ayudarle a crear y ejecutar su primera aplicación Android Wear en un emulador o dispositivo Wear.

##  <a name="user-interfaceandroidwearuser-interfaceindexmd"></a>[Interfaz de usuario](~/android/wear/user-interface/index.md)

Explica Android Wear específicos controlan y proporcionan vínculos a ejemplos que muestran cómo usar estos controles.

##  <a name="platform-featuresandroidwearplatformindexmd"></a>[Características de la plataforma](~/android/wear/platform/index.md)

Documentos de esta sección tratan características específicas de Android Wear. Aquí encontrará un tema que describe cómo crear un WatchFace.

##  <a name="screen-sizesandroidwearscreen-sizesmd"></a>[Tamaños de pantalla](~/android/wear/screen-sizes.md)

Obtener una vista previa y optimizar su interfaz de usuario para los tamaños de pantalla disponibles.

##  <a name="deployment--testingandroidweardeploy-testindexmd"></a>[Implementación y pruebas](~/android/wear/deploy-test/index.md)

Explica cómo implementar la aplicación Android Wear en un dispositivo Android Wear o configurado para el desgaste del emulador de Android. También incluye depuración sugerencias e información acerca de cómo configurar una conexión Bluetooth entre el equipo de desarrollo y un dispositivo Android.

##  <a name="wear-apishttpsdeveloperandroidcomreferenceandroidsupportwearable"></a>[Use las API](https://developer.android.com/reference/android/support/wearable)

El sitio para desarrolladores de Android proporciona información detallada acerca de la clave de API Wear como [actividad portátiles](https://developer.android.com/reference/android/support/wearable/activity/package-summary.html), [intenciones](https://developer.android.com/reference/com/google/android/wearable/intent/package-summary.html), [autenticación](https://developer.android.com/reference/android/support/wearable/authentication/package-summary.html), [ Complicaciones](https://developer.android.com/reference/android/support/wearable/complications/package-summary.html), [complicaciones representación](https://developer.android.com/reference/android/support/wearable/complications/rendering/package-summary.html), [notificaciones](https://developer.android.com/reference/android/support/wearable/notifications/package-summary.html), [vistas](https://developer.android.com/reference/android/support/wearable/view/package-summary.html), y [WatchFace](https://developer.android.com/reference/android/support/wearable/watchface/package-summary.html).



## <a name="samples"></a>Muestras

Puede encontrar un número de [ejemplos](https://developer.xamarin.com/samples/android/Android%20Wear/) con Android Wear (o vaya directamente a [github](https://github.com/xamarin/monodroid-samples/tree/master/wear)). 

|Ejemplo|Descripción|Captura de pantalla|
|--- |--- |--- |
|[SkeletonWear](https://developer.xamarin.com/samples/SkeletonWear/)|Un ejemplo sencillo de los conceptos básicos de proyectos de portátiles, incluidos FilterUserControl y notificaciones interactivas.|![Captura de pantalla de Skeletonwear](images/skeleton.png)|
|[WatchViewStub](https://developer.xamarin.com/samples/WatchViewStub/)|Una simple demostración del control WatchViewStub que detecta la forma de pantalla y carga automáticamente el diseño correcto.  Ver cómo funciona WatchViewStub en el **Resources/layout/main_actvity.xml** diseño.|![Captura de pantalla de WatchViewStub](images/watchview.png)|
|[RecipeAssistant](https://developer.xamarin.com/samples/RecipeAssistant/)|Demostración de desgaste de las páginas de notificación, en forma de pasos de la receta. Las notificaciones se crean en RecipeService.cs.|![Captura de pantalla de RecipeAssistant](images/recipeassist.png)|
|[ElizaChat](https://developer.xamarin.com/samples/ElizaChat/)|Ejemplo divertida de la interacción con un asistente"personal" llamado Eliza, mediante notificaciones interactivas desgaste para crear una conversación mediante respuestas predefinidas.|![Captura de pantalla de ElizaChat](images/eliza.png)|
|[GridViewPager](https://developer.xamarin.com/samples/GridViewPager/)|FilterUserControl implementa el modelo de navegación 2D, donde el usuario pase el dedo verticalmente y horizontalmente para navegar por las opciones y el contenido.|![Captura de pantalla de FilterUserControl](images/gridviewpager.png)|
|[WatchFace](https://developer.xamarin.com/samples/monodroid/wear/WatchFace)|WatchFace es una esfera del reloj personalizado con estilo analógico hora, minuto y segundo manos. Este ejemplo muestra cómo crear un servicio de cara de inspección que dibuja la hora actual y los eventos de cambio de visibilidad y el modo de ambiente de identificadores. Incluye un receptor de difusión que escucha los cambios de zona horaria y se actualiza automáticamente el tiempo en consecuencia.|![Captura de pantalla de WatchFace](images/gridviewpager.png)|


##  <a name="videos"></a>Vídeos

Consulte estos vídeos los vínculos que tratan de Xamarin.Android con desgaste admiten:

|Descripción|Captura de pantalla|
|--- |--- |
|[Android L y mucho más](http://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/) &ndash; The Android L Developer Preview se introdujo una enorme cantidad de nuevas API para los desarrolladores pueden aprovechar, incluido Material Design, notificaciones y las animaciones nuevas, entre otras.|![Captura de pantalla de vídeo de presentación](images/video-android-l.png)|
|[C#en mis oídos y en los ojos: Google Glass y Android Wear](https://www.youtube.com/watch?v=80H8tXByZQc) &ndash; portátiles informática puede parecer algo en el futuro (o un episodio de Inspector Gadget), pero muchas personas ya están adoptando el futuro hoy mismo! C#los desarrolladores saben y ya tienen las herramientas y los conocimientos necesarios para aprovechar la eficacia de los dispositivos portátiles (desde evolucionan 2014).|![Captura de pantalla de vídeo de presentación](images/video-eyes-ears.png)|
|[Novedades de Xamarin.Android](https://www.youtube.com/watch?v=Gpqc2XZIQfU) &ndash; L Android, Android Wear, Android TV, Android Auto, Material Design y arte; ¿qué hace este significa para usted como desarrollador de Xamarin? desde 2014 evolucionan.|![Captura de pantalla de vídeo de presentación](Images/video-whats-new.png)|


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
