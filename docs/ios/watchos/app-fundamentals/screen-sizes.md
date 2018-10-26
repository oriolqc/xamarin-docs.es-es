---
title: Trabajar con tamaños de pantalla de Xamarin de watchOS
description: Este documento describe cómo trabajar con diversos tamaños de pantalla de watchOS. Describe el Diseñador de la interfaz, el simulador, watchOS watchOS y recursos de imagen.
ms.prod: xamarin
ms.assetid: 840DF939-2F59-4ABA-87D8-92AAC8A92BC4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: b2f4cc71c1993e51ed55b51edd7c50d393e60873
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117504"
---
# <a name="working-with-watchos-screen-sizes-in-xamarin"></a>Trabajar con tamaños de pantalla de Xamarin de watchOS

Apple Watch está disponible en dos tamaños de pantalla:

- **38mm**
  - píxeles lógicos de 136 x 170 (píxeles físicos 340 x 272)

- **42mm**
  - píxeles lógicos de 156 x 195 (píxeles físicos 312 x 390).

Tamaño de la pantalla se debe tener en cuenta al diseñar y probar sus aplicaciones.

## <a name="watchos-interface-designer"></a>Diseñador de la interfaz de watchOS

De forma predeterminada, Visual Studio para Mac diseñador mostrará ver controladores de interfaz en **Any Apple Watch**.

![](screen-sizes-images/screen-any-sml.png "Los controladores de interfaz de diseñador muestra inspección en Any Apple Watch")

Use el menú de tamaño para editar y obtener una vista previa el guion gráfico en cualquiera de los tamaños de pantalla disponibles: **38mm** o **42mm**:

![](screen-sizes-images/screen-menu-sml.png "Seleccionar el tamaño de 38mm o 42mm")

El tamaño de pantalla más grande a veces representará el contenido que se trunca u oculto en la pantalla más pequeña.
No olvide probar en ambos tamaños.


### <a name="interface-design"></a>Diseño de interfaces

La aplicación debe mostrar el mismo contenido en la pantalla, independientemente del tamaño y debe expandir o contraer elementos según corresponda. En Visual Studio para Mac Designer, en el Inspector de atributos, se debe usar **relativa al contenedor** o **tamaño para ajustarse a contenido** con preferencia a tamaños fijos.

![](screen-sizes-images/sizeattributepanel-sml.png "Uso con relación al contenedor o el tamaño para ajustar el contenido con preferencia a tamaños fijos")

Dado que la pantalla de inspección está rodeada por un bisel negro, no se recomienda proporcionar relleno alrededor de la interfaz. Permitir que los elementos en el borde de la pantalla de rest y dejar que el bisel forman un borde alrededor de la aplicación natural.


## <a name="watchos-simulator"></a>Simulador de watchOS

Cuando las pruebas en el simulador pueden cambiar fácilmente entre los tamaños de dos pantalla mediante la **Hardware > dispositivo** menú.

![](screen-sizes-images/simulator.png "El simulador puede alternar entre los tamaños de dos pantalla mediante el menú del dispositivo de Hardware")


## <a name="image-resources"></a>Recursos de imagen

Debe usar varios activos de imagen si un solo recurso no muestren correctamente en diferentes tamaños. Catálogos de activos de imagen permiten especificarse para cada tamaño de los mapas de bits independientes:

![](screen-sizes-images/images-xcassets.png "Editor del catálogo de activos de imagen")

```csharp
// specify the asset name, the correct size will automatically be loaded
staticImage.SetImage(UIImage.FromBundle("Walkway"));
```

Como alternativa, puede usar código para determinar el tamaño de pantalla y cargar imágenes diferentes por completo:

```csharp
bool large = WKInterfaceDevice.CurrentDevice.ScreenBounds.Size.Width > 136.0;
// Load image depending on screen size
using (var image = UIImage.FromBundle (large ? "42mm-Walkway" : "38mm-Walkway"))
{
   myImage.SetImage (image);

}
```

Obtenga más información sobre uso de la [control imagen](~/ios/watchos/user-interface/image.md).



## <a name="related-links"></a>Vínculos relacionados

- [Introducción a watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
