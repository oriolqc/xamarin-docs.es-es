---
title: Opciones de diseño
ms.prod: xamarin
ms.assetid: D8180FEC-F300-42C0-B029-66803E0C1A5F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 8f197bbffeabb708769c48f0130aa27a86b14386
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="layout-options"></a>Opciones de diseño

Hay dos mecanismos diferentes para controlar el diseño cuando se cambia el tamaño o girar una vista:

-  **Cambiar automáticamente el tamaño** : cambiar automáticamente el tamaño del inspector en el diseñador proporciona una manera de establecer el `AutoresizingMask` propiedades. Esto le permitirá un control se acoplan a los bordes de su contenedor o corregir su tamaño. Cambiar automáticamente el tamaño funciona en todas las versiones de iOS. Esto se describe con más detalle a continuación
-  **Autodiseño** : una nueva característica de iOS6 que permite el control exhaustivo sobre las relaciones de los controles de interfaz de usuario. Permitirá el control de las posiciones de los elementos con respecto a otros elementos en la superficie de diseño. En este tema se trata con más detalle en la [Autodiseño con el Diseñador de iOS de Xamarin](~/ios/user-interface/designer/designer-auto-layout.md) guía.


## <a name="autosizing"></a>Cambiar automáticamente el tamaño

Cuando un usuario cambia el tamaño de una ventana, por ejemplo, cuando se gira el dispositivo y los cambios de orientación, el sistema cambiará automáticamente el tamaño de las vistas dentro de esa ventana según sus reglas de ajuste automático de tamaño. Estas reglas se pueden establecer en C# con el `AutoresizingMask` propiedad de la `UIView` o en la **panel de propiedades** del iOS diseñador, como se muestra a continuación:

 [![](layout-options-images/image41.png "Visual Studio para Mac Diseñador")](layout-options-images/image41.png#lightbox)

Cuando se selecciona un control, esto le permite especificar manualmente la ubicación y las dimensiones del control, así como elegir **cambiar automáticamente el tamaño** comportamiento. Como se muestra en la siguiente captura de pantalla, podemos utilizar la springs y struts en el control de ajuste automático de tamaño para definir la relación de la vista seleccionada a su elemento primario:

 [![](layout-options-images/image42.png "Visual Studio para Mac Diseñador")](layout-options-images/image42.png#lightbox)

Ajustar un *primavera* hará que la vista cambiar el tamaño en función del ancho o alto de esta vista primaria. Ajustar un *strut* hará que la vista de mantener una distancia constante entre él y su vista primaria, en ese extremo concreto.

Esta configuración también puede establecerse en el código:

```csharp
textfield1.Frame = new RectangleF(15, 277, 79, 27);
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleRightMargin | UIViewAutoresizing.FlexibleBottomMargin;
```


Para probar la configuración de ajuste automático de tamaño, habilitar diferentes **admite orientaciones de dispositivo** en las opciones del proyecto:

 [![](layout-options-images/image43a.png "Configuración de ajuste automático de tamaño")](layout-options-images/image43a.png#lightbox)

Podemos utilizar el código siguiente, que hace que los controles de dos texto que se va a cambiar el tamaño horizontalmente en el código subyacente:

```csharp
textview1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
imageview1.AutoresizingMask = UIViewAutoresizing.FlexibleTopMargin | UIViewAutoresizing.FlexibleLeftMargin;
```


También podemos ajustar los controles mediante el diseñador. Al seleccionar el struts como se muestra a continuación hará que la imagen a permanecen alineados a la derecha sin recortarla la parte inferior de la vista:

 [![](layout-options-images/autoresize.png "Autorotation")](layout-options-images/autoresize.png#lightbox)

Estas capturas de pantalla muestran cómo cambiar el tamaño de los controles o la ubicación de por sí mismos cuando se gira la pantalla:

 [![](layout-options-images/image44a.png "Autorotation")](layout-options-images/image44a.png#lightbox)

Tenga en cuenta que la vista de texto y el campo de texto tanto se estiran para mantener la misma izquierda y derecha de los márgenes, debido a la `FlexibleWidth` configuración. La imagen tiene el superior e izquierdo margen flexible, lo que significa que conserva la parte inferior y el margen derecho: mantener la imagen en la vista cuando se gira la pantalla. Diseños complejos suelen requieran una combinación de estos valores en cada control visible para mantener la coherencia de la interfaz de usuario y para impedir que los controles que se superponen cuando cambian los límites de la vista (debido a la rotación u otro evento de cambio de tamaño).





## <a name="related-links"></a>Vínculos relacionados

- [Controles (ejemplo)](https://developer.xamarin.com/samples/Controls/)
