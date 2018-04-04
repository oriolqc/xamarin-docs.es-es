---
title: Trabajar con controles segmentados
description: Este artículo tratan diseñar y trabajar con controles segmentada dentro de una aplicación Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: 23AD94CC-E93A-40B1-8E2B-ECD21FA355BE
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: d4eac932c7fad628a0a65127bceb641f34ea5d79
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-segmented-controls"></a>Trabajar con controles segmentados

_Este artículo tratan diseñar y trabajar con controles segmentada dentro de una aplicación Xamarin.tvOS._


Un Control segmentados proporciona un conjunto de elementos lineales, cada uno de los cuales puede contener un icono o el texto y se utiliza para proporcionar un conjunto de opciones relacionadas para el usuario.

[![](segmented-controls-images/segment01.png "Controles de segmento de ejemplo")](segmented-controls-images/segment01.png#lightbox)

Apple tiene las siguientes sugerencias para trabajar con controles segmentados:

- **Proporcionan un amplio espacio** -cuidado deben tomar para proporcionar un amplio espacio entre sí [elementos puede recibir el foco](~/ios/tvos/app-fundamentals/navigation-focus.md) y un Control segmentados. Un segmento individual se habilita cuando es enfocado (no cuando se hace clic en) y el usuario puede cambiar accidentalmente segmentos cuando deseen seleccione otro elemento puede recibir el foco en el segmento actual.
- **Usar vistas de división para el filtrado de contenido** -segmentados controles no tienen una buena elección para el filtrado como vistas de división se diseñaron para facilitar la navegación entre el contenido y los filtros de contenido.
- **Límite en cuanto al número máximo de segmentos siete** -debe intentar mantener el número máximo de segmentos por debajo de ocho (8) como esto es más fácil analizar desde en el espacio en el sofá y sencilla para navegar.
- **Usar tamaño de contenido de segmento uniforme** : todos los segmentos tienen el mismo ancho y, si es posible, debe intentar mantener el contenido en cada segmento del mismo tamaño. Esto hace que los controles de segmento más agradable y facilita la lectura de un vistazo.
- **Evite mezclar los iconos y texto** -cada segmento individual puede contener un icono o texto, pero no ambos. Aunque es posible combinación de iconos y texto en el mismo Control segmentados, éste debería evitarse.

<a name="About-Segment-Icons" />

## <a name="about-segment-icons"></a>Acerca de los iconos de segmento

Apple sugiere utilizando sencillas y fácil de reconocer imágenes para iconos de segmento, como una lupa para la búsqueda. Iconos excesivamente complejas son difíciles de reconocer en una pantalla de TV a través de la habitación, por lo que es mejor limitar los iconos en representaciones de simples.

No se pueden mezclar texto e iconos en un segmento determinado y no debe mezclar los iconos y texto en un solo Control segmentados. Debe ser todos los iconos o todo el texto.

<a name="Summary" />

## <a name="segment-text"></a>Texto de segmento

Apple realiza las siguientes sugerencias para trabajar con texto de segmento:

- **Usar corta, nombres significativos** -título el segmento debería indicar claramente el tipo de contenido que el usuario debe esperar al seleccionar el segmento especificado. Por ejemplo: música o vídeos.
- **Utilice mayúsculas y minúsculas de título de manera** -deben estar en mayúsculas todas las palabras del título segmentos excepto artículos, conjunciones y preposiciones de menos de cuatro (4) caracteres.
- **Usar corta, títulos centrado** -mantener los títulos, breves y centrados en el tipo de contenido que pueden esperar cuando se selecciona el segmento.

Una vez más, no se pueden mezclar texto e iconos en un segmento determinado y no debe mezclar los iconos y texto en un solo Control segmentados.

<a name="Segment-Controls-and-Storyboards" />

## <a name="segment-controls-and-storyboards"></a>Controles de segmento y los guiones gráficos

Es la manera más fácil trabajar con controles de segmento en una aplicación Xamarin.tvOS agregarlos a la interfaz de usuario de la aplicación mediante el Diseñador de iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En el **solución Pad**, haga doble clic en el `Main.storyboard` de archivo y abrirlo y editarlo.
1. Arrastre un **Control Segment** desde el **cuadro de herramientas** y colóquela en la vista: 

    [![](segmented-controls-images/segment02.png "Un Control de segmento")](segmented-controls-images/segment02.png#lightbox)
1. En el **ficha Widget** de la **propiedad panel**, puede ajustar varias propiedades del Control de segmento como su **estilo** y **estado**: 

    [![](segmented-controls-images/segment03.png "La pestaña de Widget")](segmented-controls-images/segment03.png#lightbox)
1. Use la **segmentos** campo para controlar el número de segmentos en el controlador.
1. Seleccionar un segmento determinado desde el **desplegable segmento** para ajustar sus propiedades individuales como **título** o **imagen** y para controlar si un segmento determinado es  **Habilitado** o **seleccionados** cuando se muestre el control.
1. Por último, asignar **nombres** a los controles de modo que pueda responder a ellos en código C#. Por ejemplo: 

    [![](segmented-controls-images/segment04.png "Asigne un nombre")](segmented-controls-images/segment04.png#lightbox)
1. Guarde los cambios.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. En el **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` de archivo y abrirlo y editarlo.
1. Arrastre un **Control Segment** desde el **cuadro de herramientas** y colóquela en la vista: 

    [![](segmented-controls-images/segment02-vs.png "Un Control de segmento")](segmented-controls-images/segment02-vs.png#lightbox)
1. En el **ficha Widget** de la **Property Explorer**, puede ajustar varias propiedades del Control de segmento como su **estilo** y **estado**: 

    [![](segmented-controls-images/segment03-vs.png "La pestaña de Widget")](segmented-controls-images/segment03-vs.png#lightbox)
1. Use la **segmentos** campo para controlar el número de segmentos en el controlador.
1. Seleccionar un segmento determinado desde el **desplegable segmento** para ajustar sus propiedades individuales como **título** o **imagen** y para controlar si un segmento determinado es  **Habilitado** o **seleccionados** cuando se muestre el control.
1. Por último, asignar **nombres** a los controles de modo que pueda responder a ellos en código C#. Por ejemplo: 

    [![](segmented-controls-images/segment04-vs.png "Asigne un nombre")](segmented-controls-images/segment04-vs.png#lightbox)
1. Guarde los cambios.
    
-----

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Hello, Guía de inicio rápido de tvOS](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Working-with-Segmented-Controls" />

## <a name="working-with-segmented-controls"></a>Trabajar con controles segmentados

Como se mencionó anteriormente, s que segmentados Control proporciona un conjunto de elementos lineales, cada uno de los cuales puede contener un icono o el texto y se utiliza para proporcionar un conjunto de opciones relacionadas para el usuario.

Hay varias maneras de trabajar con controles segmentados en la aplicación Xamarin.tvOS.

<a name="Exposed-as-Outlets-and-Actions" />

## <a name="exposed-as-names-and-events"></a>Exponen como nombres y eventos

Si ha creado el Control de segmento en el Diseñador de la interfaz y expone como un Control con el nombre y un evento puede utilizar el siguiente código para responder a las variables de segmento:

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

En el caso del ejemplo anterior, el Control de segmento se expone como un `PlayerCount` nombre y un `PlayerCountChanged` acción del evento. Para obtener más información sobre cómo trabajar con las acciones y las salidas, consulte el [escribir el código con las salidas y las acciones](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code) sección de nuestro [Hello, Guía de inicio rápido de tvOS](~/ios/tvos/get-started/hello-tvos.md).

El `SelectedSegment` propiedad obtiene o establece el índice de segmento seleccionado actualmente como un cero (0) en función. Por lo que si tiene segmentos cinco (5), el primer segmento tendrán un índice de cero (0) y el último un índice de cuatro (4).

<a name="Modifying-Segments" />

## <a name="modifying-segments"></a>Modificación de segmentos

En cualquier momento puede modificar el número y el contenido de los controles segmentados. Utilice el código siguiente para insertar un nuevo icono de segmento:

```csharp
// Icon Segment
SegmentedControl.InsertSegment(UIImage.FromFile("icon.png"), 0, true);

// Text Segment
SegmentedControl.InsertSegment("New Segment", 0, true);
```

El segundo parámetro define donde el segmento se insertan por medio de un índice de base cero (0). Si el último parámetro es `true` se animará la inserción.

Para quitar un segmento determinado, use lo siguiente:

```csharp
SegmentedControl.RemoveSegmentAtIndex(0, true);
```

O bien, los procedimientos siguientes para quitar todos los segmentos:

```csharp
SegmentedControl.RemoveAllSegments();
```

De nuevo, si el último parámetro es `true`, se animará la eliminación. Use la `NumberOfSegments` propiedad para devolver el número actual de segmentos.

Para obtener la **título** o **icono** de un segmento determinado, utilice lo siguiente:

```csharp
// Get title
var title = SegmentedControl.TitleAt(0);

// Get icon
var icon = SegmentedControl.ImageAt(0);
```

Y para cambiar la **título** o **icono**, utilice lo siguiente:

```csharp
// Set title
SegmentedControl.SetTitle("New Title", 0);

// Set icon
SegmentedControl.SetImage(UIImage.FromFile("icon.png"), 0);
```

Para ver si es un segmento determinado **habilitado**, utilice lo siguiente:

```csharp
if (SegmentedControl.IsEnabled(0)) {
    // Do something
    ...
}
```

Y a **habilitar/deshabilitar** un dado segmento, utilice lo siguiente:

```csharp
SegmentedControl.SetEnabled(false, 0);
```

<a name="Modifying-the-Segmented-Controls-Appearance" />

## <a name="modifying-the-segmented-controls-appearance"></a>Modificar la apariencia del Control segmentados

Puede utilizar el código siguiente para cambiar el fondo de un segmento determinado a una imagen:

```csharp
SegmentedControl.SetBackgroundImage (UIImage.FromFile("background.png"), UIControlState.Normal, UIBarMetrics.Default);
```

Donde `UIControlState` especifica el estado del control que está configurando la imagen para que:

- Normal
- resaltado
- Deshabilitado
- Seleccionado
- Con foco

Y `UIBarMetrics` especifica las métricas que se usará como:

- Default
- Compactar
- DefaultPrompt
- CompactPrompt

Además, puede establecer el divisor entre los segmentos mediante:

```csharp
SegmentedControl.SetDividerImage (UIImage.FromFile("divider.png"), UIControlState.Normal, UIControlState.Normal, UIBarMetrics.Default);
```

Donde la primera `UIControlState` especifica el estado del segmento a la izquierda del divisor y el segundo `UIControlState` especifica el estado del segmento a la derecha.

<a name="Summary" />

## <a name="summary"></a>Resumen

Diseñar y trabajar con el Control segmentada dentro de una aplicación Xamarin.tvOS se ha descrito en este artículo.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guías de interfaz humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones tvos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
