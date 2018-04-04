---
title: Implementar con fragmentos
description: Android 3.0 se presentaron fragmentos. Los fragmentos son componentes modulares y autónomos que se utilizan para ayudar a resolver la complejidad de las aplicaciones de escritura que pueden ejecutarse en pantallas de diferentes tamaños. Este artículo le guía a través de cómo utilizar fragmentos para desarrollar aplicaciones de Xamarin.Android y cómo admitir fragmentos en 3.0 dispositivos previamente Android.
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 81f1f992de450ee62c4c1d2e80da858b024be594
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="implementing-with-fragments"></a>Implementar con fragmentos

_Android 3.0 se presentaron fragmentos. Los fragmentos son componentes modulares y autónomos que se utilizan para ayudar a resolver la complejidad de las aplicaciones de escritura que pueden ejecutarse en pantallas de diferentes tamaños. Este artículo le guía a través de cómo utilizar fragmentos para desarrollar aplicaciones de Xamarin.Android y cómo admitir fragmentos en 3.0 dispositivos previamente Android._


## <a name="overview"></a>Información general

En esta sección, le mostraremos cómo crear una aplicación que se mostrará una lista de objetivos de Shakespeare y una oferta de cada play seleccionado. Nuestra aplicación usará fragmentos para que le podemos definir nuestros componentes de interfaz de usuario en un solo lugar, pero, a continuación, se utilicen en diferentes factores de forma. Por ejemplo, las siguientes capturas de pantalla muestran la aplicación se ejecuta en una tableta de 10", así como en un teléfono:

[![Capturas de pantalla de la aplicación de ejemplo que se ejecuta en Tablet PC y teléfono](images/intro-screenshot-sml.png)](images/intro-screenshot.png#lightbox)

En esta sección se tratará los siguientes temas:

- **Crear fragmentos** &ndash; muestra cómo crear un fragmento para mostrar una lista de los objetivos de Shakespeare y otro fragmento que se va a mostrar una oferta de cada play.

- **Compatibilidad con diferentes tamaños de pantalla** &ndash; muestra cómo al diseño de la aplicación para aprovechar las ventajas de los tamaños de pantalla más grandes.

- **Con el paquete de compatibilidad con Android** &ndash; implementa el paquete de compatibilidad con Android, a continuación, realiza algunos cambios menores a las actividades en la aplicación, lo que permite que se ejecute en las versiones anteriores de Android.


## <a name="requirements"></a>Requisitos

Este tutorial requiere Xamarin.Android 4.0 o posterior. También será necesario instalar el paquete de compatibilidad con Android, tal como se describe en la documentación de fragmentos.


## <a name="introduction"></a>Introducción

En el ejemplo que vamos a crear en esta sección, las actividades no contienen lógica para cargar la lista, responder a la selección del usuario o mostrar la oferta para la reproducción seleccionada. Esta lógica existe en los fragmentos individuales.
Al colocar esta lógica en los fragmentos por sí mismos, se puede dividir el flujo de trabajo de la aplicación para admitir las pantallas grandes con una actividad o pantallas pequeñas con varias actividades sin tener que escribir una lógica diferente para cada actividad. En una tableta, ambos fragmentos estará en una actividad. En un teléfono, los fragmentos se hospedará en actividades diferentes.

Esta aplicación incluye las siguientes partes:

 **MainActivity** : muestra uno o ambos de los fragmentos, según el tamaño de la pantalla. Se trata de la actividad de inicio.

 **TitlesFragment** : muestra una lista de los objetivos de Shakespeare desde el que el usuario puede seleccionar.

 **DetailsFragment** : muestra la oferta de la reproducción seleccionada.

 **DetailsActivity** : hospeda y muestra el DetailsFragment.
Esta actividad se usa en dispositivos con pantallas pequeñas, como los teléfonos.



## <a name="related-links"></a>Vínculos relacionados

- [FragmentsWalkthrough (ejemplo)](https://developer.xamarin.com/samples/monodroid/FragmentsWalkthrough/)
- [Información general del diseñador](~/android/user-interface/android-designer/index.md)
- [Ejemplos de Xamarin.Android: panal Galería](https://developer.xamarin.com/samples/HoneycombGallery/)
- [Implementación de fragmentos](http://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Paquete de soporte](http://developer.android.com/sdk/compatibility-library.html)
