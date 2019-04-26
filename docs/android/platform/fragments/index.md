---
title: Fragmentos
description: Android 3.0 presentó fragmentos, que muestra cómo admitir diseños más flexibles de los muchas diferentes tamaños de pantalla que se encuentra en teléfonos y tabletas. En este artículo tratará cómo usar fragmentos para desarrollar aplicaciones de Xamarin.Android y también cómo admitir fragmentos en Android dispositivos (API nivel 11) 3.0.
ms.prod: xamarin
ms.assetid: 1AFB4242-A337-F8E0-83D9-B8D850D7F384
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: 4347ee40b0b72cc2d182ba1ef8f111212dd0ee47
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61026071"
---
# <a name="fragments"></a>Fragmentos

_Android 3.0 presentó fragmentos, que muestra cómo admitir diseños más flexibles de los muchas diferentes tamaños de pantalla que se encuentra en teléfonos y tabletas. En este artículo tratará cómo usar fragmentos para desarrollar aplicaciones de Xamarin.Android y también cómo admitir fragmentos en Android dispositivos (API nivel 11) 3.0._

## <a name="fragments-overview"></a>Información general de fragmentos

Los tamaños de pantalla más grandes que se encuentra en la mayoría de tabletas agregan una capa adicional de complejidad al desarrollo de Android, un diseño que se ha diseñado para la pequeña pantalla no necesariamente funcionará muy bien en pantallas más grandes y viceversa. Para reducir el número de las complicaciones que se introdujeron, Android 3.0 agrega dos nuevas características, *fragmentos* y *paquetes de soporte técnico*.

Fragmentos pueden considerarse como módulos de la interfaz de usuario. Permiten a los programadores dividir la interfaz de usuario en partes aisladas y reutilizables que se pueden ejecutar en actividades independientes. En tiempo de ejecución, las actividades propias decidirá qué fragmentos a usar.

Compatibilidad con paquetes llamó originalmente *bibliotecas de compatibilidad* y permiten fragmentos que se usará en los dispositivos que ejecutan versiones de Android anteriores a Android 3.0 (API nivel 11).

Por ejemplo, la imagen siguiente muestra cómo una aplicación usa fragmentos a través de diferentes factores de forma de dispositivo.

[![Diagrama de cómo se utilizan los fragmentos en tabletas y teléfonos móviles](images/00.png)](images/00.png#lightbox)

*Un fragmento* contiene una lista, mientras que *fragmento B* contiene los detalles de un elemento seleccionado en esa lista. Cuando la aplicación se ejecuta en un equipo Tablet PC, pueden mostrar ambos fragmentos en la misma actividad. Cuando se ejecuta la misma aplicación en un auricular (con su tamaño de pantalla anterior), los fragmentos se hospedan en dos actividades independientes. Fragmento de A y B fragmento son los mismos en ambos factores de forma, pero las actividades que se hospedan son diferentes.

Para ayudar a una actividad de coordinar y administrar todos estos fragmentos, Android introdujo una nueva clase denominada el *FragmentManager*. Cada actividad tiene su propia instancia de un `FragmentManager` para agregar, eliminar y buscar hospedan fragmentos. El siguiente diagrama ilustra la relación entre los fragmentos y actividades:

[![Diagrama que ilustra las relaciones entre la actividad, el Administrador de fragmentos y fragmentos](images/01.png)](images/01.png#lightbox)

En ciertos aspectos, fragmentos pueden considerarse como los controles compuestos o como mini actividades. Agrupan elementos de interfaz de usuario en módulos reutilizables que, a continuación, pueden utilizar independientemente los desarrolladores en actividades. Un fragmento tiene una jerarquía de vistas, al igual que una actividad, pero, a diferencia de una actividad, puede compartirse entre pantallas. Vistas de diferencian de los fragmentos fragmentos tienen su propio ciclo de vida; las vistas no lo hacen.

Mientras la actividad es un host a uno o varios fragmentos, no es directamente consciente de los fragmentos de ellos mismos. Del mismo modo, los fragmentos no son directamente consciente de otros fragmentos de la actividad de hospedaje. Sin embargo, fragmentos y actividades están enteradas de la `FragmentManager` en su actividad. Mediante el uso de la `FragmentManager`, es posible que una actividad o un fragmento obtener una referencia a una instancia específica de un fragmento y, a continuación, llamar a métodos en esa instancia. De este modo, la actividad o fragmentos pueden comunicarse e interactuar con otros fragmentos.

Esta guía contiene una completa cobertura sobre cómo usar fragmentos, incluidos:

-   **Crear fragmentos** : cómo crear un fragmento básico y métodos clave que deben implementarse.
-   **Administración y las transacciones de fragmento** : cómo manipular los fragmentos en tiempo de ejecución.
-   **Paquete de compatibilidad con Android** : cómo usar las bibliotecas que permiten fragmentos que se usará en las versiones anteriores de Android.


## <a name="requirements"></a>Requisitos

Fragmentos están disponibles en el SDK de Android a partir de nivel de API 11 (Android 3.0), como se muestra en la captura de pantalla siguiente:

[![Seleccionar el nivel de API de Android SDK Manager](images/02.png)](images/02.png#lightbox)

Fragmentos están disponibles en Xamarin.Android 4.0 o superior. Una aplicación de Xamarin.Android debe tener como destino al menos el nivel de API 11 (Android 3.0) o posterior para poder usar los fragmentos. La plataforma de destino se pueden establecer en el proyecto propiedades tal como se muestra a continuación:

[![Establecer el nivel de API del marco de destino en las opciones del proyecto](images/03-sml.png)](images/03.png#lightbox)

Es posible usar los fragmentos en versiones anteriores de Android mediante el paquete de compatibilidad con Android y Xamarin.Android 4.2 o posterior. Se trata cómo hacer esto con más detalle en los documentos de esta sección.


## <a name="related-links"></a>Vínculos relacionados

- [Galería Honeycomb (ejemplo)](https://developer.xamarin.com/samples/monodroid/HoneycombGallery)
- [Fragmentos](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Paquete de soporte técnico](https://developer.android.com/sdk/compatibility-library.html)
- [Seminario Web MOTODEV: Presentación de fragmentos](http://motodev.adobeconnect.com/p9h1aqk3ttn/)
