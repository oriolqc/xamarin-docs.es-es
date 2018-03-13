---
title: Fragmentos
description: "Android 3.0 se presentaron fragmentos, que muestra cómo admitir diseños más flexibles para los tamaños de pantalla diferentes muchos encontrados en teléfonos y tabletas. Este artículo explica cómo utilizar fragmentos para desarrollar aplicaciones de Xamarin.Android y también cómo admitir fragmentos en previamente Android dispositivos (API nivel 11) 3.0."
ms.topic: article
ms.prod: xamarin
ms.assetid: 1AFB4242-A337-F8E0-83D9-B8D850D7F384
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 8595ecb63e49a4768120e98f41826b74c2dd43e4
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="fragments"></a>Fragmentos

_Android 3.0 se presentaron fragmentos, que muestra cómo admitir diseños más flexibles para los tamaños de pantalla diferentes muchos encontrados en teléfonos y tabletas. Este artículo explica cómo utilizar fragmentos para desarrollar aplicaciones de Xamarin.Android y también cómo admitir fragmentos en previamente Android dispositivos (API nivel 11) 3.0._

## <a name="fragments-overview"></a>Información general de fragmentos

Los tamaños de pantalla más grandes que se encuentra en la mayoría de tabletas agregan un nivel adicional de complejidad al desarrollo de Android, un diseño que se ha diseñado para la pantalla pequeña no funcionan necesariamente así para pantallas más grandes y viceversa. Para reducir el número de las complicaciones que se introdujeron, Android 3.0 agregó dos nuevas características, *fragmentos* y *paquetes de compatibilidad*.

Fragmentos pueden considerarse como módulos de interfaz de usuario. Permiten a los programadores dividir la interfaz de usuario en partes aislados y reutilizables que se pueden ejecutar en actividades independientes. En tiempo de ejecución, las actividades por sí mismos determinará qué fragmentos a usar.

Paquetes de compatibilidad llamó originalmente *bibliotecas de compatibilidad* y permite fragmentos para usarse en dispositivos que ejecutan versiones de Android anteriores a Android 3.0 (API nivel 11).

Por ejemplo, la imagen siguiente muestra cómo una aplicación usa fragmentos a través de distintos factores de forma de dispositivo.

[![Diagrama de cómo se utilizan los fragmentos en tabletas y teléfonos móviles](images/00.png)](images/00.png#lightbox)

*Fragmento A* contiene una lista, mientras que *fragmento B* contiene los detalles de un elemento seleccionado en la lista. Cuando la aplicación se ejecuta en una tableta, pueden mostrar ambos fragmentos en la misma actividad. Cuando se ejecuta la misma aplicación en un auricular (con su tamaño de pantalla anterior), los fragmentos se hospedan en dos actividades independientes. Fragmento de A y B de fragmento son los mismos en ambos factores de forma, pero las actividades que hospedan son diferentes.

Para coordinar y administrar todos estos fragmentos de una actividad, Android introdujo una nueva clase denominada el *FragmentManager*. Cada actividad tiene su propia instancia de un `FragmentManager` para agregar, eliminar y buscar hospedan fragmentos. En el diagrama siguiente ilustra la relación entre los fragmentos y actividades:

[![Diagrama que ilustra las relaciones entre la actividad, el Administrador de fragmentos y fragmentos](images/01.png)](images/01.png#lightbox)

En algunos cuanto, fragmentos pueden considerarse como controles compuestos o mini actividades. Agrupe los elementos de interfaz de usuario en módulos reutilizables que, a continuación, se pueden utilizar independientemente los desarrolladores en actividades. Un fragmento tiene una jerarquía de vista, al igual que una actividad, pero, a diferencia de una actividad, se puede compartir entre pantallas. Vistas difieren de fragmentos en que fragmentos tienen su propio ciclo de vida; no lo hacen las vistas.

Mientras la actividad es un host a uno o más fragmentos, no es directamente compatible con los de los fragmentos por sí mismos. Del mismo modo, los fragmentos no son directamente en cuenta otros fragmentos de la actividad de hospedaje. Sin embargo, los fragmentos y actividades son conscientes de la `FragmentManager` en su actividad. Mediante el uso de la `FragmentManager`, es posible que una actividad o un fragmento obtener una referencia a una instancia concreta de un fragmento y, a continuación, llamar a métodos en esa instancia. De esta manera, la actividad o fragmentos pueden comunicarse e interactuar con otros fragmentos.

Esta guía contiene una cobertura integral sobre cómo usar los fragmentos, incluidos:

-   **Crear fragmentos** : cómo crear un fragmento básico y métodos clave que deben implementarse.
-   **Fragmentar la administración y las transacciones** : cómo manipular los fragmentos en tiempo de ejecución.
-   **Paquete de compatibilidad con Android** : cómo usar las bibliotecas que permiten fragmentos para utilizarse en versiones anteriores de Android.


## <a name="requirements"></a>Requisitos

Fragmentos de están disponibles en el SDK de Android a partir de nivel de API 11 (Android 3.0), como se muestra en la captura de pantalla siguiente:

[![Seleccione el nivel de API en el Administrador de SDK de Android](images/02.png)](images/02.png#lightbox)

Los fragmentos son disponible en Xamarin.Android 4.0 y versiones posteriores. Una aplicación Xamarin.Android destino debe ser al menos el nivel de API 11 (Android 3.0) o superior para poder usar los fragmentos. La plataforma de destino se puede establecer en las opciones de proyecto, tal y como se muestra a continuación:

[![Establecer el nivel de API del marco de destino en las opciones de proyecto](images/03.png)](images/03.png#lightbox)

Es posible utilizar fragmentos en versiones anteriores de Android mediante el paquete de compatibilidad con Android y Xamarin.Android 4.2 o posterior. Cómo hacer esto se trata con más detalle en los documentos de esta sección.


## <a name="related-links"></a>Vínculos relacionados

- [Galería de panal (ejemplo)](https://developer.xamarin.com/samples/monodroid/HoneycombGallery)
- [Fragmentos](http://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Paquete de soporte](http://developer.android.com/sdk/compatibility-library.html)
- [MOTODEV seminario Web: Introducción a fragmentos](http://motodev.adobeconnect.com/p9h1aqk3ttn/)
