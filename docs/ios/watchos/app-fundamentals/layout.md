---
title: Trabajar con el diseño
ms.prod: xamarin
ms.assetid: BEDB62A1-2249-4459-986F-413A41E63DF0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 38e26544a907ffcafdec38e3e2758d9bdac7b977
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-layout"></a>Trabajar con el diseño

Crear diseños para el Apple Watch [tamaños de pantalla](~/ios/watchos/app-fundamentals/screen-sizes.md) presenta desafíos únicos.

## <a name="design-tips"></a>Sugerencias de diseño

El punto clave es: que la interfaz de usuario de leer y utilizable en una pantalla pequeña de inspección, con un dedo grande. No se dividen en la trampa de diseño para la **simulador de iOS** (que aparece muy grande) y un **puntero del mouse** (que funciona con destinos táctil pequeña)!

- Utilice un fondo negro - crea la ilusión de una pantalla más grande con bisel negro de inspección.

- No rellenar alrededor de su diseño de pantalla: el bisel constituye un relleno visual natural.

- Se centran en mejorar la legibilidad. Utilice los tamaños de fuentes y colores con prudencia para asegurarse de que el texto es legible. Use los estilos de texto integrado para obtener compatibilidad automática con tipo dinámico.

![](layout-images/type.png "Ejemplo de compatibilidad de tipo dinámico")

- Se centran en tamaños de destino táctil. Botones/tappable filas de tabla con etiquetas de texto deben abarcar toda la pantalla. Apple dice "no coloque nunca más de tres elementos side-by-side", y si usar iconos y etiquetas de texto no.

- Use la [ `Menu` control](~/ios/watchos/user-interface/menu.md) a la funcionalidad de exponer con menor frecuencia se utiliza para mantener el diseño de la aplicación claro y conciso.


## <a name="implementation"></a>Implementación

Kit de inspección incluye los siguientes controles para ayudarle a crear atractiva inspección diseños de aplicación:

### <a name="interface-controller"></a>Controlador de interfaz

El `WKInterfaceController` es la clase base todos de la escena.

La superficie de diseño para el controlador de interfaz se comporta como una vertical **grupo**: puede arrastrar otros controles a la controladora de interfaz y usarán automáticamente dispuesto en uno encima de otro:

![](layout-images/controller-scene.png "Los controles son automáticamente dispuesto en uno encima de otro")

Puede establecer la **posición** y **tamaño** propiedades en cada control para controlar su aspecto:

![](layout-images/positionsize-attributes.png "Establecer las propiedades de posición y el tamaño de cada control")

Cuando se establece el tamaño en **relativa al contenedor** puede proporcionar un valor proporcional y un ajuste de desplazamiento. Esta captura de pantalla muestra un botón que se ha configurado para usar el 80% del ancho de la pantalla de inspección (**0,8**):

![](layout-images/button-attributes.png "Proporcionar un valor proporcional y un ajuste de desplazamiento")


### <a name="group"></a>Agrupar

`WKInterfaceGroup` es un contenedor de diseño sencillo que se pueden configurar para apilar controla vertical u horizontalmente. Incluye espaciado entre cada control de forma predeterminada, pero se pueden modificar el espaciado (y márgenes) en el **atributos** inspector.

![](layout-images/group-attributes.png "Modificar el espaciado y los márgenes en el inspector de atributos")

Grupos pueden por sí mismos un tamaño y sitúa en relación con los controles a su alrededor y grupos se pueden anidar para crear diseños complejos.

![](layout-images/group-scene.png "Los grupos pueden anidarse para crear diseños complejos")


### <a name="separator"></a>Separador

El control de separador está diseñado para ayudar a proporcionan guías visuales en su diseño. Utilice separadores (o colores de fondo o imágenes) para ayudar al usuario a comprender el contenido que está relacionado con en la pantalla.

![](layout-images/separator-scene.png "Ejemplo de uso de separadores")

Tenga en cuenta los separadores azul y verde que no use todo el ancho de la pantalla se han configurado con una **Fixed** o **relativa al contenedor** tamaños.

### <a name="content-controls"></a>Controles de contenido

Ningún diseño estaría completa sin el `Label`, `Image`, `Button`, `Switch`, `Slider`, `Map`, y [otros controles](~/ios/watchos/user-interface/index.md).
Estos se pueden colocar en los diseños con **grupos** o la configuración de la posición y el tamaño de cada control.



## <a name="related-links"></a>Vínculos relacionados

- [WatchKitCatalog (ejemplo)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [Referencia de diseño de Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Layout.html)
- [Hacer referencia a los colores & Tipografía de Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/ColorandTypography.html)
