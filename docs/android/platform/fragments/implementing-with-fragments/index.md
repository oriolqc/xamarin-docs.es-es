---
title: Implementación de fragmentos - tutorial
description: En este artículo le guía a través del uso de fragmentos para desarrollar aplicaciones de Xamarin.Android.
ms.topic: tutorial
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/26/2018
---

# <a name="implementing-fragments---walkthrough"></a>Implementación de fragmentos - tutorial

_Los fragmentos son componentes modulares y autónomos que pueden ayudar a reducir la complejidad de las aplicaciones de Android que tenga como destino dispositivos con una variedad de tamaños de pantalla. En este artículo le guía a través de cómo crear y utilizar fragmentos al desarrollar aplicaciones de Xamarin.Android._

## <a name="overview"></a>Información general

En esta sección, guiaremos a través de cómo crear y utilizar fragmentos de una aplicación de Xamarin.Android. Esta aplicación mostrará los títulos de desempeña varias por William Shakespeare en una lista. Cuando el usuario puntea en el título de un juego, la aplicación mostrará una oferta desde el que se reproducen en una actividad independiente:

[![Aplicación que se ejecuta en un teléfono Android en modo vertical](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

Cuando se rote el teléfono para el modo horizontal, cambiará el aspecto de la aplicación: la lista de objetivos y las comillas aparecerá en la misma actividad. Cuando se selecciona un objetivo, la oferta se mostrará en la misma actividad:

[![Aplicación que se ejecuta en un teléfono Android en modo horizontal](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

Por último, si la aplicación se ejecuta en una tableta:

[![Aplicación que se ejecuta en una tableta Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)

Esta aplicación de ejemplo puede adaptar fácilmente a los distintos factores de forma y orientaciones con cambios mínimos en el código mediante el uso de fragmentos y [diseños alternativos](/xamarin/android/app-fundamentals/resources-in-android/alternate-resources).

Los datos de la aplicación existirá en dos matrices de cadenas que están codificados en la aplicación como C# matrices de cadenas. Cada una de las matrices servirá como el origen de datos para un fragmento.  Una matriz contendrá el nombre de algunos objetivos por Shakespeare, y la otra matriz contendrá una oferta desde el que se reproducen. Cuando se inicia la aplicación, mostrará los nombres de reproducción en un `ListFragment`. Cuando el usuario hace clic en un juego en el `ListFragment`, la aplicación se iniciará otra actividad que se mostrará la oferta.

La interfaz de usuario para la aplicación constará de dos diseños, uno para vertical y otro para el modo horizontal. En tiempo de ejecución, Android determinará qué diseño de carga según la orientación del dispositivo y proporcionará ese diseño a la actividad a representar. Toda la lógica para responder a clics de usuario y mostrar los datos se incluirán en fragmentos. Las actividades de la aplicación existen solo como contenedores que se va a hospedar los fragmentos.

En este tutorial se divide en dos guías. El [primera parte](./walkthrough.md) se centrará en las partes principales de la aplicación. Se creará un único conjunto de diseños (optimizado para el modo vertical), junto con dos fragmentos y dos actividades:

1. `MainActivity` &nbsp; Se trata de la actividad de inicio de la aplicación.
1. `TitlesFragment` &nbsp; Este fragmento mostrará una lista de títulos de los objetivos que se escribieron William Shakespeare. Se hospedará en `MainActivity`.
1. `PlayQuoteActivity` &nbsp; `TitlesFragment` se iniciará el `PlayQuoteActivity` en respuesta al usuario seleccionar un juego en `TitlesFragment`.
1. `PlayQuoteFragment` &nbsp; Este fragmento mostrará una oferta de un juego por William Shakespeare. Se hospedará en `PlayQuoteActivity`.

El [segunda parte de este tutorial](./walkthrough-landscape.md) trataremos agregaremos un diseño alternativo (optimizado para el modo horizontal) que se mostrará ambos fragmentos en la pantalla. Además, algunos pequeños cambios de código se realizará en el código para que la aplicación adapta su comportamiento para el número de fragmentos que se muestran al mismo tiempo en la pantalla.

## <a name="related-links"></a>Vínculos relacionados

- [FragmentsWalkthrough (ejemplo)](https://developer.xamarin.com/samples/monodroid/FragmentsWalkthrough/)
- [Información general del diseñador](~/android/user-interface/android-designer/index.md)
- [Implementación de fragmentos](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Paquete de soporte técnico](https://developer.android.com/sdk/compatibility-library.html)
