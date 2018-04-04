---
title: Trabajar con tamaños de pantalla
ms.prod: xamarin
ms.assetid: 840DF939-2F59-4ABA-87D8-92AAC8A92BC4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 600b3de6cbf31bd4c3221eb1bf81eda7b4678c09
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-screen-sizes"></a>Trabajar con tamaños de pantalla

Apple Watch está disponible en dos tamaños de pantalla:

- **38mm**
  - 136 x 170 lógicos píxeles (píxeles físicos 272 x 340)

- **42mm**
  - píxeles 156 x 195 lógicos (312 x 390 píxeles físicos).

Tamaño de la pantalla también debe tener en cuenta al diseñar y probar las aplicaciones.

## <a name="watchos-interface-designer"></a>Diseñador de la interfaz de watchOS

De forma predeterminada, Visual Studio para Mac diseñador mostrará ver controladores de interfaz en **Any Apple Watch**.

![](screen-sizes-images/screen-any-sml.png "Los controladores de interfaz de diseñador muestra inspección en cualquier Apple Watch")

Use el menú de tamaño para editar y obtener una vista previa el guión gráfico en cualquiera de los tamaños de pantalla disponibles: **38mm** o **42mm**:

![](screen-sizes-images/screen-menu-sml.png "Elegir el tamaño de 38mm o 42mm")

El tamaño máximo de pantalla a veces representará el contenido que se trunca u oculta en la pantalla más pequeña.
No olvide probar en dos tamaños.


### <a name="interface-design"></a>Diseño de interfaces

La aplicación debe mostrar el mismo contenido en la pantalla, independientemente del tamaño y debe expandir o contraer elementos según corresponda. En Visual Studio para el Diseñador de Mac, en el Inspector de atributo, se debe usar **relativa al contenedor** o **tamaño para ajustarse a contenido** con preferencia a tamaños fijos.

![](screen-sizes-images/sizeattributepanel-sml.png "Usar relativo al contenedor o el tamaño para ajustar el contenido con preferencia a tamaños fijos")

Dado que la pantalla de inspección está rodeada por un bisel negro, no se recomienda proporcionar relleno alrededor de la interfaz. Permitir que los elementos apoyado en el borde de la pantalla y deje que el bisel forman un borde natural alrededor de la aplicación.


## <a name="watchos-simulator"></a>watchOS simulador

Cuando prueba en el simulador puede cambiar fácilmente entre los tamaños de dos pantalla con el **Hardware > dispositivo** menú.

![](screen-sizes-images/simulator.png "El simulador puede alternar entre los tamaños de dos pantalla mediante el menú de dispositivo de Hardware")


## <a name="image-resources"></a>Recursos de imagen

Debe usar varios activos de imagen si un solo activo no muestren correctamente en diferentes tamaños. Catálogos de activos de imagen permite que los mapas de bits independientes que se especifique para cada tamaño de:

![](screen-sizes-images/images-xcassets.png "Editor de catálogo de activos de imagen")

```csharp
// specify the asset name, the correct size will automatically be loaded
staticImage.SetImage(UIImage.FromBundle("Walkway"));
```

Como alternativa, utilice el código para determinar el tamaño de pantalla y cargar diferentes imágenes por completo:

```csharp
bool large = WKInterfaceDevice.CurrentDevice.ScreenBounds.Size.Width > 136.0;
// Load image depending on screen size
using (var image = UIImage.FromBundle (large ? "42mm-Walkway" : "38mm-Walkway"))
{
   myImage.SetImage (image);

}
```

Obtenga más información sobre uso de la [control de imagen](~/ios/watchos/user-interface/image.md).



## <a name="related-links"></a>Vínculos relacionados

- [Introducción a watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
