---
title: Fragmentos de implementación - tutorial
description: Este artículo le guía a través de cómo utilizar fragmentos para desarrollar aplicaciones de Xamarin.Android.
ms.topic: tutorial
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/26/2018
ms.openlocfilehash: 92c68298d7abd2570efd89e12d7cfb6364e90972
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="implementing-fragments---walkthrough"></a>Fragmentos de implementación - tutorial

_Los fragmentos son componentes independientes modulares que pueden ayudar a reducir la complejidad de las aplicaciones Android que tienen como destino dispositivos con una variedad de tamaños de pantalla. Este artículo le guía por el proceso de crear y utilizar fragmentos al desarrollar aplicaciones de Xamarin.Android._

## <a name="overview"></a>Información general

En esta sección, analizaremos cómo crear y utilizar fragmentos en una aplicación Xamarin.Android. Esta aplicación mostrará los títulos de desempeña varias por William Shakespeare en una lista. Cuando el usuario puntea en el título de un juego, la aplicación mostrará una oferta desde que se reproducen en una actividad independiente:

[![Aplicación que se ejecuta en un teléfono Android en modo vertical](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

Al gira el teléfono como horizontal, cambiará la apariencia de la aplicación: la lista de ofertas y desempeña aparecerá en la misma actividad. Cuando se selecciona un objetivo, la oferta se mostrará en la misma actividad:

[![Aplicación que se ejecuta en un teléfono Android en modo horizontal](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

Por último, si la aplicación se ejecuta en un Tablet PC:

[![Aplicación que se ejecuta en una tableta de Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)

Esta aplicación de ejemplo puede adaptar fácilmente a los distintos factores de forma y orientaciones con cambios mínimos en el código mediante el uso de fragmentos y [diseños alternativos](/xamarin/android/app-fundamentals/resources-in-android/alternate-resources).

Los datos de la aplicación existirán en dos matrices de cadenas que están codificados en la aplicación como matrices de cadenas de C#. Cada una de las matrices actuará como origen de datos para un fragmento.  Una matriz contendrá el nombre de algunos desempeña por Shakespeare, y la otra matriz contendrá una oferta desde que se reproducen. Cuando se inicia la aplicación, mostrará los nombres de reproducción en un `ListFragment`. Cuando el usuario hace clic en un juego en el `ListFragment`, la aplicación se iniciará otra actividad que se mostrará la oferta.

La interfaz de usuario de la aplicación constará de dos diseños, uno para vertical y otro para el modo horizontal. En tiempo de ejecución Android determina qué diseño de carga según la orientación del dispositivo y proporcionará ese diseño a la actividad que se va a representar. Toda la lógica para responder a los clics del usuario y mostrar los datos se incluirán en fragmentos. Las actividades de la aplicación existen únicamente como contenedores que hospedarán los fragmentos.

En este tutorial se divide en dos guías. El [primera parte](./walkthrough.md) se centrará en las partes principales de la aplicación. Se creará un único conjunto de diseños (optimizado para el modo vertical), junto con dos fragmentos y dos actividades:

1. `MainActivity` &nbsp; Se trata de la actividad de inicio de la aplicación.
1. `TitlesFragment` &nbsp; Este fragmento mostrará una lista de los títulos de los objetivos que se hayan escrito William Shakespeare. Se hospedará en `MainActivity`.
1. `PlayQuoteActivity` &nbsp; `TitlesFragment` se iniciará el `PlayQuoteActivity` en respuesta a la que el usuario seleccione un juego en `TitlesFragment`.
1. `PlayQuoteFragment` &nbsp; Este fragmento mostrará una oferta de reproducción por William Shakespeare. Se hospedará en `PlayQuoteActivity`.

El [segunda parte de este tutorial](./walkthrough-landscape.md) veremos la adición de un diseño alternativo (optimizado para el modo horizontal) que se mostrará ambos fragmentos en la pantalla. Además, algunos cambios de código secundario se realizará en el código para que la aplicación adaptará su comportamiento para que el número de fragmentos que se muestran al mismo tiempo en la pantalla.

## <a name="related-links"></a>Vínculos relacionados

- [FragmentsWalkthrough (ejemplo)](https://developer.xamarin.com/samples/monodroid/FragmentsWalkthrough/)
- [Información general del diseñador](~/android/user-interface/android-designer/index.md)
- [Implementación de fragmentos](http://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Paquete de soporte](http://developer.android.com/sdk/compatibility-library.html)
