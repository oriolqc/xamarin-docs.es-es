---
title: Opciones de diseño de Xamarin.iOS
description: Este documento describen los diferentes métodos para diseñar interfaces de usuario de Xamarin.iOS. Describe el ajuste automático de tamaño y el diseño automático.
ms.prod: xamarin
ms.assetid: D8180FEC-F300-42C0-B029-66803E0C1A5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 1bcced2f43328bf5e7d1ebb171b3c92c9ec22493
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827754"
---
# <a name="layout-options-in-xamarinios"></a>Opciones de diseño de Xamarin.iOS

Hay dos mecanismos diferentes para controlar el diseño cuando se cambia el tamaño o girar una vista:

-  **Cambiar el tamaño automáticamente** : ajuste automático de tamaño el inspector en el diseñador proporciona una manera de establecer el `AutoresizingMask` propiedades. Esto permitirá que un control se acoplan a los bordes de su contenedor y corregir su tamaño. Tamaño automático funciona en todas las versiones de iOS. Esto se describe más detalladamente a continuación
-  **Diseño automático** : una nueva característica de iOS 6 que permite un mayor control sobre las relaciones de los controles de interfaz de usuario. Permitirá que el control de las posiciones de los elementos con respecto a otros elementos en la superficie de diseño. Este tema se trata con más detalle en la [diseño automático con el Diseñador de iOS de Xamarin](~/ios/user-interface/designer/designer-auto-layout.md) guía.

## <a name="autosizing"></a>Cambiar el tamaño automáticamente

Cuando un usuario cambia el tamaño de una ventana, como cuando se gira el dispositivo y los cambios de orientación, el sistema automáticamente cambiará el tamaño de las vistas dentro de esa ventana según sus reglas de ajuste automático de tamaño. Estas reglas se pueden establecer en C# utilizando el `AutoresizingMask` propiedad de la `UIView` o en el **panel de propiedades** de iOS Designer, como se muestra a continuación:

 [![](layout-options-images/image41.png "Visual Studio para Mac Diseñador")](layout-options-images/image41.png#lightbox)

Cuando se selecciona un control, esto le permite especificar manualmente la ubicación y las dimensiones del control, así como elegir **ajuste automático de tamaño** comportamiento. Como se muestra en la siguiente captura de pantalla, podemos usar el springs y struts en el control de tamaño automático para definir la relación de la vista seleccionada a su elemento principal:

 [![](layout-options-images/image42.png "Visual Studio para Mac Diseñador")](layout-options-images/image42.png#lightbox)

Ajustar un *spring* hará que la vista cambiar el tamaño según el ancho o alto de esta vista primaria. Ajustar un *strut* hará que la vista de mantener una distancia constante entre él y su vista primaria, en dicho borde determinado.

Esta configuración también puede establecerse en el código:

```csharp
textfield1.Frame = new RectangleF(15, 277, 79, 27);
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleRightMargin | UIViewAutoresizing.FlexibleBottomMargin;
```


Para probar la configuración de ajuste automático de tamaño, habilitar diferentes **orientaciones de dispositivos compatibles** en las opciones del proyecto:

 [![](layout-options-images/image43a.png "Configuración de ajuste automático de tamaño")](layout-options-images/image43a.png#lightbox)

Podemos usar el código siguiente, que hace que el texto de dos controles cambiar el tamaño horizontalmente en el código subyacente:

```csharp
textview1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
imageview1.AutoresizingMask = UIViewAutoresizing.FlexibleTopMargin | UIViewAutoresizing.FlexibleLeftMargin;
```


También podemos ajustar los controles mediante el diseñador. Seleccionar el struts como se muestra a continuación hará que la imagen permanezca alineado a la derecha sin que se recorta la parte inferior de la vista:

 [![](layout-options-images/autoresize.png "Rotación automática")](layout-options-images/autoresize.png#lightbox)

Estas capturas de pantalla muestran cómo cambiar el tamaño de los controles o la ubicación a sí mismos cuando se gira la pantalla:

 [![](layout-options-images/image44a.png "Rotación automática")](layout-options-images/image44a.png#lightbox)

Tenga en cuenta que la vista de texto y el campo de texto se ajustan para mantener la misma deja tanto a la derecha de los márgenes, debido a la `FlexibleWidth` configuración. La imagen tiene el superior e izquierdo margen flexible, lo que significa que conserva la parte inferior y el margen derecho: mantener la imagen en la vista cuando se gira la pantalla. Diseños complejos suelen requieran una combinación de estas opciones en todos los controles visibles para mantener la coherencia de la interfaz de usuario y para impedir que los controles se superpongan cuando cambian los límites de la vista (debido a una rotación u otro evento de cambio de tamaño).





## <a name="related-links"></a>Vínculos relacionados

- [Controles (ejemplo)](https://developer.xamarin.com/samples/monotouch/Controls/)
