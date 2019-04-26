---
title: Trabajar con controles segmentados de tvOS en Xamarin
description: Este documento describe cómo trabajar con tvOS controles segmentados en una aplicación compilada con Xamarin. Describe los iconos de segmento y texto, eventos, modificar la apariencia del control y mucho más.
ms.prod: xamarin
ms.assetid: 23AD94CC-E93A-40B1-8E2B-ECD21FA355BE
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 98a770d05014e0498b805ed9ffa0c84314efc765
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61375038"
---
# <a name="working-with-tvos-segmented-controls-in-xamarin"></a>Trabajar con controles segmentados de tvOS en Xamarin

Un Control segmentado proporciona un conjunto de elementos lineales, cada uno de los cuales puede contener un icono o el texto y se usa para proporcionar un conjunto de opciones relacionadas para el usuario.

[![](segmented-controls-images/segment01.png "Controles de segmento de ejemplo")](segmented-controls-images/segment01.png#lightbox)

Apple tiene las siguientes sugerencias para trabajar con controles segmentados:

- **Proporcionar suficiente espacio** -atención debe tomar para proporcionar suficiente espacio entre sí [elementos enfocable](~/ios/tvos/app-fundamentals/navigation-focus.md) y un Control segmentado. Se selecciona un segmento individual cuando es enfocado (no cuando se hace clic en) y el usuario puede cambiar por accidente segmentos cuando deseen seleccione otro elemento enfocable en el segmento actual.
- **Usar vistas de división para el filtrado de contenido** -controles segmentados no realizar buenas opciones para filtrar como vistas de división se diseñaron para facilitar la navegación entre el contenido y los filtros de contenido.
- **Límite en cuanto al número máximo de segmentos de siete** -debe intentar mantener el número máximo de segmentos por debajo de los ocho (8) como esto es más fácil analizar desde en toda la habitación en el sofá y más fácil navegar.
- **Usar el tamaño del contenido de segmento coherente** : todos los segmentos tienen el mismo ancho y, si es posible, debe intentar mantener el contenido en cada segmento del mismo tamaño. Esto no sólo hace que los controles de segmento más agradable, pero hace que sea más fácil de leer un vistazo.
- **Evite mezclar los iconos y texto** -cada segmento individual puede contener un icono o texto, pero no ambos. Aunque es posible mezclar iconos y texto en el mismo Control segmentado, esto se debe evitar.

<a name="About-Segment-Icons" />

## <a name="about-segment-icons"></a>Acerca de los iconos de segmento

Apple sugiere el uso sencillas y reconocibles de imágenes para iconos de segmento, como una lupa para la búsqueda. Los iconos demasiado complejos son difíciles de reconocer en una pantalla de TV a través de la sala, por lo que es mejor limitar los iconos de representaciones sencillas.

No se pueden mezclar texto y los iconos en un segmento determinado y no se debe mezclar los iconos y texto en un único Control segmentado. Debe ser todos los iconos o todo el texto.

<a name="Summary" />

## <a name="segment-text"></a>Texto de segmento

Apple realiza las siguientes sugerencias para trabajar con texto de segmento:

- **Utilice Short, nombres significativos** -título el segmento debe indicar claramente el tipo de contenido que el usuario debe esperar al seleccionar el segmento especificado. Por ejemplo: Música o vídeos.
- **Utilice mayúsculas y minúsculas de título** -todas las palabras del título segmentos deben escribirse en mayúsculas, excepto los artículos, conjunciones y preposiciones de menos de cuatro (4) caracteres.
- **Utilice Short, títulos centrado** -mantener los títulos, breves y centrados en el tipo de contenido que puede esperar cuando se selecciona el segmento.

Nuevamente, no se pueden mezclar texto y los iconos en un segmento determinado y no se debe mezclar los iconos y texto en un único Control segmentado.

<a name="Segment-Controls-and-Storyboards" />

## <a name="segment-controls-and-storyboards"></a>Controles de segmento y guiones gráficos

Es la manera más fácil trabajar con controles de segmento en una aplicación Xamarin.tvOS agregarlos a la interfaz de usuario de la aplicación mediante el Diseñador de iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. En el **panel de solución**, haga doble clic en el `Main.storyboard` de archivo y abrirlo para su edición.
1. Arrastre un **Control segmento** desde el **cuadro de herramientas** y colóquela en la vista: 

    [![](segmented-controls-images/segment02.png "Un Control de segmento")](segmented-controls-images/segment02.png#lightbox)
1. En el **ficha Widget** de la **panel propiedad**, puede ajustar varias propiedades del Control de segmento, como su **estilo** y **estado**: 

    [![](segmented-controls-images/segment03.png "La pestaña de Widget")](segmented-controls-images/segment03.png#lightbox)
1. Use la **segmentos** campo para controlar el número de segmentos en el controlador.
1. Seleccione un segmento determinado desde el **desplegable segmento** para ajustar sus propiedades individuales, como **título** o **imagen** y controlar si un segmento determinado es  **Habilitado** o **seleccionados** cuando se muestre el control.
1. Finalmente, asigne **nombres** a los controles para que pueda responder a ellos en C# código. Por ejemplo: 

    [![](segmented-controls-images/segment04.png "Asigne un nombre")](segmented-controls-images/segment04.png#lightbox)
1. Guarde los cambios.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. En el **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` de archivo y abrirlo para su edición.
1. Arrastre un **Control segmento** desde el **cuadro de herramientas** y colóquela en la vista: 

    [![](segmented-controls-images/segment02-vs.png "Un Control de segmento")](segmented-controls-images/segment02-vs.png#lightbox)
1. En el **ficha Widget** de la **Property Explorer**, puede ajustar varias propiedades del Control de segmento, como su **estilo** y **estado**: 

    [![](segmented-controls-images/segment03-vs.png "La pestaña de Widget")](segmented-controls-images/segment03-vs.png#lightbox)
1. Use la **segmentos** campo para controlar el número de segmentos en el controlador.
1. Seleccione un segmento determinado desde el **desplegable segmento** para ajustar sus propiedades individuales, como **título** o **imagen** y controlar si un segmento determinado es  **Habilitado** o **seleccionados** cuando se muestre el control.
1. Finalmente, asigne **nombres** a los controles para que pueda responder a ellos en C# código. Por ejemplo: 

    [![](segmented-controls-images/segment04-vs.png "Asigne un nombre")](segmented-controls-images/segment04-vs.png#lightbox)
1. Guarde los cambios.
    
-----

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Hola, Guía de inicio rápido de tvOS](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Working-with-Segmented-Controls" />

## <a name="working-with-segmented-controls"></a>Trabajar con controles segmentados

Como se indicó anteriormente, s que control segmentado proporciona un conjunto de elementos lineales, cada uno de los cuales puede contener un icono o el texto y se usa para proporcionar un conjunto de opciones relacionadas para el usuario.

Hay varias maneras diferentes que puede trabajar con controles segmentados en la aplicación Xamarin.tvOS.

<a name="Exposed-as-Outlets-and-Actions" />

## <a name="exposed-as-names-and-events"></a>Exponer como los nombres y eventos

Si ha creado el Control de segmento en el Diseñador de interfaz y expone como un Control con nombre y un evento puede usar el siguiente código para responder a las variables de segmento:

```csharp
partial void PlayerCountChanged (Foundation.NSObject sender) {

    // Take action based on the number of players selected
    switch(PlayerCount.SelectedSegment) {
    case 0:
        // Do something if the segment is selected
        ...
        break;
    case 1:
        // Do something if the segment is selected
        ...
        break;
    case 2:
        // Do something if the segment is selected
        ...
        break;
    }
}
```

En el caso del ejemplo anterior, el Control del segmento se expone como un `PlayerCount` nombre y un `PlayerCountChanged` acción de evento. Para obtener más información sobre cómo trabajar con las acciones y salidas, consulte el [escribiendo el código con las salidas y acciones](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code) sección de nuestra [Hola, Guía de inicio rápido de tvOS](~/ios/tvos/get-started/hello-tvos.md).

El `SelectedSegment` propiedad obtiene o establece el índice del segmento seleccionado actualmente como un cero (0) en función. Por lo que si tiene segmentos cinco (5), el primer segmento tiene un índice de cero (0) y el último un índice de cuatro (4).

<a name="Modifying-Segments" />

## <a name="modifying-segments"></a>Modificación de segmentos

En cualquier momento puede modificar el número y el contenido de los controles segmentados. Use el siguiente código para insertar un icono nuevo segmento:

```csharp
// Icon Segment
SegmentedControl.InsertSegment(UIImage.FromFile("icon.png"), 0, true);

// Text Segment
SegmentedControl.InsertSegment("New Segment", 0, true);
```

El segundo parámetro define dónde estará el segmento insertan por medio de un índice de base cero (0). Si el último parámetro es `true` se animará la inserción.

Para quitar un segmento determinado, use lo siguiente:

```csharp
SegmentedControl.RemoveSegmentAtIndex(0, true);
```

O bien, las siguientes opciones para quitar todos los segmentos:

```csharp
SegmentedControl.RemoveAllSegments();
```

Nuevamente, si el último parámetro es `true`, se animará la eliminación. Use el `NumberOfSegments` propiedad para devolver el número de segmentos del actual.

Para obtener el **título** o **icono** para un segmento determinado, use lo siguiente:

```csharp
// Get title
var title = SegmentedControl.TitleAt(0);

// Get icon
var icon = SegmentedControl.ImageAt(0);
```

Y para cambiar la **título** o **icono**, use lo siguiente:

```csharp
// Set title
SegmentedControl.SetTitle("New Title", 0);

// Set icon
SegmentedControl.SetImage(UIImage.FromFile("icon.png"), 0);
```

Para ver si es un segmento determinado **habilitado**, use lo siguiente:

```csharp
if (SegmentedControl.IsEnabled(0)) {
    // Do something
    ...
}
```

Y a **habilitar/deshabilitar** un dado segmento, use lo siguiente:

```csharp
SegmentedControl.SetEnabled(false, 0);
```

<a name="Modifying-the-Segmented-Controls-Appearance" />

## <a name="modifying-the-segmented-controls-appearance"></a>Modificar la apariencia del Control segmentado

Puede usar el código siguiente para cambiar el fondo de un segmento determinado a una imagen:

```csharp
SegmentedControl.SetBackgroundImage (UIImage.FromFile("background.png"), UIControlState.Normal, UIBarMetrics.Default);
```

Donde `UIControlState` especifica el estado del control que se va a establecer la imagen para como:

- Normal
- resaltado
- Deshabilitado
- Seleccionado
- Con foco

Y `UIBarMetrics` especifica las métricas que se usará como:

- Default
- Compacto
- DefaultPrompt
- CompactPrompt

Además, puede establecer el divisor entre los segmentos con:

```csharp
SegmentedControl.SetDividerImage (UIImage.FromFile("divider.png"), UIControlState.Normal, UIControlState.Normal, UIBarMetrics.Default);
```

Donde el primero `UIControlState` especifica el estado del segmento a la izquierda del divisor y el segundo `UIControlState` especifica el estado del segmento a la derecha.

<a name="Summary" />

## <a name="summary"></a>Resumen

Este artículo trata de diseñar y trabajar con el Control segmentado dentro de una aplicación Xamarin.tvOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicación de tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
