---
title: Android Wear
description: Crear aplicaciones para dispositivos wearable Android.
ms.topic: article
ms.prod: xamarin
ms.assetid: 3BE4A128-2D88-4500-9E48-20375EA99A49
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: ac83b74f39497333de7aa80079784adf61bf2e65
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
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



## <a name="samples"></a>Muestras

Puede encontrar un número de [ejemplos](https://developer.xamarin.com/samples/android/Android%20Wear/) con Android usan (o ir directamente a [github](https://github.com/xamarin/monodroid-samples/tree/master/wear)). 

<table align="center" border="1" cellpadding="1" cellspacing="1">
  <thead>
      <th>
          <strong>Ejemplo</strong>
      </th>
      <th>
          <strong>Descripción</strong>
      </th>
      <th>
          <strong>captura de pantalla</strong>
      </th>
  </thead>
  <tbody>
  <tr>
      <td valign="top">
          <a href="https://developer.xamarin.com/samples/SkeletonWear/">SkeletonWear</a>
      </td>
      <td valign="top">
Un ejemplo sencillo de los conceptos básicos de proyectos wearable, incluidos FilterUserControl y notificaciones interactivas.
      </td>
      <td>
          <img src="Images/skeleton.png" class="tableimg">
      </td>
  </tr>
  <tr>
      <td valign="top">
          <a href="https://developer.xamarin.com/samples/WatchViewStub/">WatchViewStub</a>
      </td>
      <td valign="top">
Una simple demostración del control WatchViewStub que detecta la forma de pantalla y carga automáticamente el diseño correcto.
Ver cómo funciona WatchViewStub en el <b>Resources/layout/main_actvity.xml</b> diseño.
      </td>
      <td>
          <img src="Images/watchview.png" class="tableimg">
      </td>
  </tr>
  <tr>
      <td valign="top">
          <a href="https://developer.xamarin.com/samples/RecipeAssistant/">RecipeAssistant</a>
      </td>
      <td valign="top">
Demostración de páginas de notificación de desgaste, en forma de pasos de receta. Las notificaciones se crean en <b>RecipeService.cs</b>.
      </td>
      <td>
          <img src="Images/recipeassist.png" class="tableimg">
      </td>
  </tr>
  <tr>
      <td valign="top">
          <a href="https://developer.xamarin.com/samples/ElizaChat/">ElizaChat</a>
      </td>
      <td valign="top">
Ejemplo divertida de interactuar con un asistente de"personal" llama Eliza, usar desgaste interactivas notificaciones para crear una conversación mediante respuestas preestablecidas.
      </td>
      <td>
          <img src="Images/eliza.png" class="tableimg">
      </td>
  </tr>
  <tr>
      <td valign="top">
          <a href="https://developer.xamarin.com/samples/GridViewPager/">GridViewPager</a>
      </td>
      <td valign="top">
FilterUserControl implementa el modelo de navegación 2D, donde el usuario lectores de crédito verticalmente y horizontalmente para navegar por las opciones y el contenido.
      </td>
      <td>
          <img src="Images/gridviewpager.png" class="tableimg">
      </td>
  </tr>
  <tr>
      <td valign="top">
          <a href="https://developer.xamarin.com/samples/monodroid/wear/WatchFace">WatchFace</a>
      </td>
      <td valign="top">
          <b>WatchFace</b> es una cara de inspección personalizado con estilo de señales analógicas hora, minuto y segundo manos. Este ejemplo muestra cómo crear un servicio de cara de inspección que dibuja la hora actual y los eventos de cambio de visibilidad y el modo de ambiente de identificadores. Incluye un receptor de difusión que realiza escuchas para los cambios de zona horaria y se actualiza automáticamente el tiempo en consecuencia.
      </td>
      <td>
          <img src="Images/watchface.png" class="tableimg">
      </td>
  </tr>
  </tbody>
</table>

##  <a name="videos"></a>Vídeos

Consulte estos vídeo vínculos que tratan Xamarin.Android con desgaste son compatibles con.

<table align="center" border="0" cellpadding="1" cellspacing="1">
    <tr>
        <td>
        <a href="http://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/"><img src="Images/video-android-l.png" border="0" /></td>
        <td><a href="http://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/">L Android y mucho más</a>
        <br />
La vista previa para desarrolladores de Android L introdujo una gran cantidad de nuevas API para que los desarrolladores aprovechar las ventajas de, como Material de diseño, las notificaciones y las animaciones nuevas, para nombrar algunos.</td>
    </tr>
    <tr>
        <td>
        <a href="https://www.youtube.com/watch?v=80H8tXByZQc"><img src="Images/video-eyes-ears.png" border="0" /></td>
        <td><a href="https://www.youtube.com/watch?v=80H8tXByZQc">C# está en mi oídos y en los ojos: Google Glass y se ocupa de Android</a>
        <br />
Informática wearable podría parecer algo desde el futuro (o un episodio de Inspector Gadget), pero muchas personas ya están adoptando el futuro hoy mismo! Los desarrolladores de C# lo saben y ya tienen las herramientas y los conocimientos para aprovechar la eficacia de los dispositivos wearable (de evolucionar 2014).</td>
    </tr>
    <tr>
        <td>
        <a href="https://www.youtube.com/watch?v=Gpqc2XZIQfU"><img src="Images/video-whats-new.png" border="0" /></td>
        <td><a href="https://www.youtube.com/watch?v=Gpqc2XZIQfU">Novedades de Xamarin.Android</a>
        <br />
        <i>L Android, desgaste Android, TV Android, Auto Android, diseño Material y carátulas; ¿Qué significa esto para usted como desarrollador de Xamarin? </i> de evolucionar 2014.</td>
    </tr>
</table>


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
