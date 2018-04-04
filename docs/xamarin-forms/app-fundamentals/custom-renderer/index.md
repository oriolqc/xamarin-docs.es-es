---
title: Representadores personalizados
description: Interfaces de usuario de Xamarin.Forms se representan mediante los controles nativos de la plataforma de destino, permita que las aplicaciones de Xamarin.Forms conservar la apariencia y funcionamiento adecuada para cada plataforma. Representadores personalizados permiten a los desarrolladores invalidar este proceso para personalizar la apariencia y comportamiento de los controles de Xamarin.Forms en cada plataforma.
ms.prod: xamarin
ms.assetid: BF1CF23A-3BC9-4226-92E6-DAEEB91422F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 6a2ee4b09426e6b4ff6dac7e1fd5221fc5b6d750
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="custom-renderers"></a>Representadores personalizados

_Interfaces de usuario de Xamarin.Forms se representan mediante los controles nativos de la plataforma de destino, permita que las aplicaciones de Xamarin.Forms conservar la apariencia y funcionamiento adecuada para cada plataforma. Representadores personalizados permiten a los desarrolladores invalidar este proceso para personalizar la apariencia y comportamiento de los controles de Xamarin.Forms en cada plataforma._

## <a name="introduction-to-custom-renderersintroductionmd"></a>[Introducción a los representadores de personalizado](introduction.md)

Representadores personalizados proporcionan un método eficaz para personalizar la apariencia y el comportamiento de los controles de Xamarin.Forms. Pueden usarse para cambios de estilo pequeño o sofisticado diseño específico de la plataforma y personalización del comportamiento. Este artículo proporciona una introducción a los representadores personalizados y describe el proceso para crear a un representador personalizado.

## <a name="renderer-base-classes-and-native-controlsrenderersmd"></a>[Clases base del representador y los controles nativos](renderers.md)

Cada control Xamarin.Forms tiene un representador que lo acompañan para cada plataforma que crea una instancia de un control nativo. Este artículo enumeran las clases de control nativo que implementan cada página Xamarin.Forms, diseño, vista y celda y el representador.

## <a name="customizing-an-entryentrymd"></a>[Personalización de una entrada](entry.md)

El Xamarin.Forms [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) control permite una sola línea de texto que se pueda editar. En este artículo se muestra cómo crear un representador personalizado para el `Entry` control, permitiendo a los programadores reemplazar el procesamiento nativo de forma predeterminada con su propios personalización específica de la plataforma.

## <a name="customizing-a-contentpagecontentpagemd"></a>[Personalización de una página de contenido](contentpage.md)

A [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) es un elemento visual que muestra una vista única y ocupa la mayor parte de la pantalla. En este artículo se muestra cómo crear un representador personalizado para el `ContentPage` página, permitiendo a los programadores reemplazar el procesamiento nativo de forma predeterminada con su propios personalización específica de la plataforma.

## <a name="customizing-a-mapmapindexmd"></a>[Personalización de un mapa](map/index.md)

Xamarin.Forms.Maps proporciona una abstracción de multiplataforma para mostrar los mapas que usan la asignación nativo experimentan de API en cada plataforma, para proporcionar una asignación rápida y familiar para los usuarios. En este tema se muestra cómo crear los representadores personalizados para el `Map` control, permitiendo a los programadores reemplazar el procesamiento nativo de forma predeterminada con su propios personalización específica de la plataforma.

## <a name="customizing-a-listviewlistviewmd"></a>[Personalización de la clase ListView](listview.md)

Un Xamarin.Forms [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) es una vista que muestra una colección de datos como una lista vertical. Este artículo demuestra cómo crear a un representador personalizado que encapsula los controles de lista específica de la plataforma y las distribuciones de celda nativo, lo que permite más control sobre el rendimiento del control de lista nativo.

## <a name="customizing-a-viewcellviewcellmd"></a>[Personalización de la clase ViewCell](viewcell.md)

Un Xamarin.Forms [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) es una celda que se puede agregar a un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) o [ `TableView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/), que contiene una vista definida por el desarrollador. En este artículo se muestra cómo crear un representador personalizado para un `ViewCell` que se hospeda dentro de un Xamarin.Forms `ListView` control. Esto evita que los cálculos de diseño de Xamarin.Forms se llame varias veces durante la `ListView` desplazamiento.

## <a name="implementing-a-viewviewmd"></a>[Implementación de una vista](view.md)

Controles de interfaces de usuario personalizado de Xamarin.Forms deben derivarse de la [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) (clase), que se utiliza para colocar controles en la pantalla y diseños. Este artículo demuestra cómo crear a un representador personalizado para un control personalizado de Xamarin.Forms que se usa para mostrar una secuencia de vídeo de vista previa de la cámara del dispositivo.

## <a name="implementing-a-hybridwebviewhybridwebviewmd"></a>[Implementación de una clase HybridWebView](hybridwebview.md)

En este artículo se muestra cómo crear un representador personalizado para un `HybridWebView` control personalizado, que se muestra cómo mejorar los controles específicos de la plataforma web para permitir que el código de C# que se debe invocar desde JavaScript.

## <a name="implementing-a-video-playervideo-playerindexmd"></a>[Implementar un Reproductor de vídeo](video-player/index.md)

Este artículo muestra cómo escribir representadores para implementar un personalizado `VideoPlayer` control que puede reproducir vídeos desde la web, los vídeos que se incrustan como recursos de la aplicación o los vídeos almacenados en la biblioteca de vídeos en el dispositivo del usuario. Se muestran varias técnicas, incluida la implementación de métodos y propiedades enlazables de solo lectura. 


## <a name="related-links"></a>Vínculos relacionados

- [Efectos](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Representadores personalizados (vídeo de la Universidad de Xamarin)](https://developer.xamarin.com/videos/cross-platform/xamarinforms-custom-renderers/)
- [Ejemplo de representadores personalizados (vídeo de la Universidad de Xamarin)](http://bit.ly/xf-customrenderer)
