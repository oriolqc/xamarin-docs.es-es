---
title: "Implementar un Reproductor de vídeo"
ms.topic: article
ms.prod: xamarin
ms.assetid: 0CE9BEE7-4F81-4A00-B9B3-5E2535CD3050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: a889be5ee31f667117d2c36859e667980f0e6610
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="implementing-a-video-player"></a>Implementar un Reproductor de vídeo

En ocasiones es deseable para reproducir archivos de vídeo en una aplicación de Xamarin.Forms. Esta serie de artículos describe cómo escribir representadores personalizados de iOS, Android y la plataforma Universal de Windows (UWP) para una clase de Xamarin.Forms denominada `VideoPlayer`.

En el [ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) de ejemplo, todos los archivos que se implementan y admiten `VideoPlayer` están en carpetas denominadas `FormsVideoLibrary` e identificado con el espacio de nombres `FormsVideoLibrary` o espacios de nombres que comenzar `FormsVideoLibrary`. Esta organización y nomenclatura debe hacerlo más fácil copiar los archivos del Reproductor de vídeo en su propia solución de Xamarin.Forms.

`VideoPlayer` puede reproducir archivos de vídeo entre tres tipos de orígenes:

- Internet con una dirección URL
- Un recurso incrustado en la aplicación de plataforma
- Biblioteca de vídeos del dispositivo

Reproductores de vídeo requieren *controles de transporte*, que son botones para reproducir y pausar el vídeo y una posición de la barra que muestra el progreso a través del vídeo y permite al usuario Omitir rápidamente a una ubicación diferente. `VideoPlayer` Puede usar los controles de transporte y posición barra proporcionada por la plataforma (tal y como se muestra a continuación), o puede proporcionar controles de transporte personalizado y una barra de posición. Este es el programa que se ejecuta en la plataforma Universal de Windows, iOS y Android:

[![Reproducir vídeo Web](web-videos-images/playwebvideo-small.png "reproducir vídeo Web")](web-videos-images/playwebvideo-large.png#lightbox "reproducir vídeo de Web")

Por supuesto, puede activar el teléfono lateralmente para ampliarla.

Un Reproductor de vídeo más sofisticado tendría algunas características adicionales, como control de volumen y un mecanismo para interrumpir el vídeo cuando recibe una llamada de teléfono a través de, una manera de mantener la pantalla activa durante la reproducción.

La siguiente serie de artículos progresivamente muestra cómo se generan las clases auxiliares y los representadores de plataforma:

## <a name="creating-the-platform-video-playersplayer-creationmd"></a>[Crear los reproductores de vídeo de plataforma](player-creation.md)

Cada plataforma requiere un `VideoPlayerRenderer` clase que crea y mantiene un control de Reproductor de vídeo compatible con la plataforma. Este artículo muestra la estructura del representador de clases y cómo se crean los jugadores.

## <a name="playing-a-web-videoweb-videosmd"></a>[Reproducir un vídeo de Web](web-videos.md)

Probablemente el origen más común de vídeos para un Reproductor de vídeo es Internet. Este artículo describe cómo ver un vídeo de Web pueda hacer referencia y usar como origen para el Reproductor de vídeo.

## <a name="binding-video-sources-to-the-playersource-bindingsmd"></a>[Orígenes de vídeo de enlace para el Reproductor](source-bindings.md)

Este artículo se utiliza un `ListView` para presentar una colección de vídeos para reproducir. Un programa muestra cómo el archivo de código subyacente puede establecer el origen de vídeo del Reproductor de vídeo, pero un segundo programa muestra cómo puede utilizar datos de enlace entre el `ListView` y el Reproductor de vídeo.

## <a name="loading-application-resource-videosloading-resourcesmd"></a>[La carga de vídeos de recursos de aplicación](loading-resources.md)

Vídeos se pueden incrustar como recursos en los proyectos de plataforma. Este artículo muestra cómo almacenar esos recursos y cargarlos más adelante en el programa para reproducirse en el Reproductor de vídeo.

## <a name="accessing-the-devices-video-libraryaccessing-librarymd"></a>[Obtener acceso a la biblioteca de vídeos del dispositivo](accessing-library.md)

Cuando se crea un vídeo con la cámara del dispositivo, el archivo de vídeo se almacena en la biblioteca de imágenes del dispositivo. Este artículo muestra cómo obtener acceso a selector de la imagen del dispositivo para seleccionar el vídeo y, a continuación, reproducir mediante el Reproductor de vídeo.

## <a name="custom-video-transport-controlscustom-transportmd"></a>[Controles de transporte personalizado de vídeo](custom-transport.md)

Aunque los reproductores de vídeo en cada plataforma de proporcionan sus propios controles de transporte en forma de botones para **reproducir** y **pausa**, puede suprimir la presentación de los botones y proporcionar sus propios. Este artículo muestra cómo.

## <a name="custom-video-positioningcustom-positioningmd"></a>[Posición de vídeo personalizada](custom-positioning.md)

Cada uno de los reproductores de vídeo de plataforma tiene una barra de posición que muestra el progreso del vídeo y permite omitir hacia delante o hacia atrás hasta una posición concreta. En este artículo se muestra cómo se puede reemplazar esa barra de posición con un control personalizado.





## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de Reproductor de vídeo (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
