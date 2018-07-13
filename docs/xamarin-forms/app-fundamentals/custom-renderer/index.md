---
title: Representadores personalizados de Xamarin.Forms
description: Los representadores personalizados permiten que los desarrolladores reemplacen la representación de los controles nativos en cada plataforma, para personalizar la apariencia y comportamiento de los controles de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: BF1CF23A-3BC9-4226-92E6-DAEEB91422F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: c7ae25688b2f8635a9a89318e0b307e58add7a5a
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998752"
---
# <a name="xamarinforms-custom-renderers"></a>Representadores personalizados de Xamarin.Forms

_Interfaces de usuario de Xamarin.Forms se representan mediante los controles nativos de la plataforma de destino, lo que permite a las aplicaciones de Xamarin.Forms conservar la apariencia y comportamiento adecuada para cada plataforma. Los representadores personalizados permiten a los desarrolladores reemplazar este proceso para personalizar la apariencia y comportamiento de los controles de Xamarin.Forms en cada plataforma._

## <a name="introduction-to-custom-renderersintroductionmd"></a>[Introducción a los representadores personalizados](introduction.md)

Los representadores personalizados proporcionan un método eficaz para personalizar la apariencia y comportamiento de los controles de Xamarin.Forms. Se puede usar para los cambios de estilo pequeño o diseños sofisticados de específicos de la plataforma y personalización del comportamiento. En este artículo proporciona una introducción a los representadores personalizados y describe el proceso para crear a un representador personalizado.

## <a name="renderer-base-classes-and-native-controlsrenderersmd"></a>[Clases bases del representador y los controles nativos](renderers.md)

Todos los controles Xamarin.Forms tienen un representador que lo acompaña para cada plataforma que crea una instancia de un control nativo. En este artículo se enumera las clases de control nativo que implementan cada página de Xamarin.Forms, diseño, vista y celda y el representador.

## <a name="customizing-an-entryentrymd"></a>[Personalización de una entrada](entry.md)

Xamarin.Forms [ `Entry` ](xref:Xamarin.Forms.Entry) control permite una sola línea de texto que se va a editarse. En este artículo se muestra cómo crear un representador personalizado para el `Entry` control, lo que permite que los desarrolladores reemplacen la representación nativa de forma predeterminada con su propia personalización específica de la plataforma.

## <a name="customizing-a-contentpagecontentpagemd"></a>[Personalización de una página de contenido](contentpage.md)

Un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) es un elemento visual que muestra una vista única y ocupa la mayor parte de la pantalla. En este artículo se muestra cómo crear un representador personalizado para el `ContentPage` página, lo que permite que los desarrolladores reemplacen la representación nativa de forma predeterminada con su propia personalización específica de la plataforma.

## <a name="customizing-a-mapmapindexmd"></a>[Personalización de un mapa](map/index.md)

Xamarin.Forms.Maps para proporciona una abstracción de multiplataforma para mostrar los mapas que usan el mapa nativo experimentan API en cada plataforma, para proporcionar un mapa rápido y familiar para los usuarios. En este tema se muestra cómo crear representadores personalizados para el `Map` control, lo que permite que los desarrolladores reemplacen la representación nativa de forma predeterminada con su propia personalización específica de la plataforma.

## <a name="customizing-a-listviewlistviewmd"></a>[Personalización de la clase ListView](listview.md)

Un objeto Xamarin.Forms [ `ListView` ](xref:Xamarin.Forms.ListView) es una vista que muestra una colección de datos como una lista vertical. En este artículo se muestra cómo crear a un representador personalizado que encapsula los controles de lista específica de la plataforma y los diseños de celda nativo, lo que permite más control sobre el rendimiento del control de lista nativo.

## <a name="customizing-a-viewcellviewcellmd"></a>[Personalización de la clase ViewCell](viewcell.md)

Un objeto Xamarin.Forms [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) es una celda que se puede agregar a un [ `ListView` ](xref:Xamarin.Forms.ListView) o [ `TableView` ](xref:Xamarin.Forms.TableView), que contiene una vista definida por el desarrollador. En este artículo se muestra cómo crear un representador personalizado para un `ViewCell` que se hospeda dentro de un Xamarin.Forms `ListView` control. Esto detiene los cálculos de diseño de Xamarin.Forms desde que se llama varias veces durante `ListView` desplazamiento.

## <a name="implementing-a-viewviewmd"></a>[Implementación de una vista](view.md)

Controles de interfaces de usuario personalizado de Xamarin.Forms deben derivar de la [ `View` ](xref:Xamarin.Forms.View) (clase), que se utiliza para colocar los diseños y controles en la pantalla. En este artículo se muestra cómo crear a un representador personalizado para un control personalizado de Xamarin.Forms que se usa para mostrar una secuencia de vídeo de vista previa de la cámara del dispositivo.

## <a name="implementing-a-hybridwebviewhybridwebviewmd"></a>[Implementación de una clase HybridWebView](hybridwebview.md)

En este artículo se muestra cómo crear un representador personalizado para un `HybridWebView` control personalizado, que muestra cómo mejorar los controles específicos de la plataforma web para permitir el código de C# que se debe invocar desde JavaScript.

## <a name="implementing-a-video-playervideo-playerindexmd"></a>[Implementación de un Reproductor de vídeo](video-player/index.md)

En este artículo se muestra cómo escribir los representadores para implementar un personalizado `VideoPlayer` control que puede reproducir vídeos desde la web, vídeos insertados como recursos de la aplicación o vídeos almacenados en la biblioteca de vídeos en el dispositivo del usuario. Se muestran varias técnicas, incluida la implementación de métodos y propiedades enlazables de solo lectura.


## <a name="related-links"></a>Vínculos relacionados

- [Efectos](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Representadores personalizados (vídeo de Xamarin University)](https://developer.xamarin.com/videos/cross-platform/xamarinforms-custom-renderers/)
- [Ejemplo de los representadores personalizados (vídeo de Xamarin University)](http://bit.ly/xf-customrenderer)
