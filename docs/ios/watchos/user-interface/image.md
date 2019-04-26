---
title: Controles de imagen en Xamarin de watchOS
description: Este documento describe cómo usar controles de imagen en una aplicación para watchOS con Xamarin. Describe el control WKInterfaceImage, el método SetImage, agregar imágenes a una extensión de inspección, animaciones y mucho más.
ms.prod: xamarin
ms.assetid: B741C207-3427-46F3-9C90-A52BF8933FA4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 6a2b8c99156963ae167aecd29a618d0feeffbdc7
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61229037"
---
# <a name="watchos-image-controls-in-xamarin"></a>Controles de imagen en Xamarin de watchOS

watchOS proporciona un [`WKInterfaceImage`](xref:WatchKit.WKInterfaceImage) control para mostrar imágenes y animaciones simples. Algunos controles también pueden tener una imagen de fondo (por ejemplo, los controladores de interfaz, grupos y botones).

![](image-images/image-walkway.png "Imagen que muestra de Apple Watch") ![](image-images/image-animation.png "de Apple Watch con una animación simple")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

Usar las imágenes del catálogo de activos para agregar imágenes a las aplicaciones de Watch Kit.
Solo **@2x** versiones son necesarias, ya que todos ver dispositivos tienen pantallas de Retina.

![](image-images/asset-universal-sml.png "Sólo 2 versiones de x son necesarias, ya que todos ver dispositivos tienen pantallas de Retina")

Es recomendable para garantizar que las imágenes a sí mismos son el tamaño correcto para la presentación de inspección. *Evitar* con imágenes con tamaño incorrectamente (son especialmente grandes) y el escalado para mostrarlos en el reloj.

Puede usar los tamaños del Kit de inspección (38mm y 42mm) en una imagen del catálogo de activos para especificar diferentes imágenes para cada tamaño de presentación.

![](image-images/asset-watch-sml.png "Puede usar los tamaños de inspección Kit 38 y 42mm en una imagen del catálogo de activos para especificar diferentes imágenes para cada tamaño de presentación")


## <a name="images-on-the-watch"></a>Imágenes en el reloj

Es la manera más eficaz para mostrar imágenes *incluirlos en el proyecto de aplicación de inspección* y mostrarlos mediante el `SetImage(string imageName)` método.

Por ejemplo, el [WatchKitCatalog](https://developer.xamarin.com/samples/WatchKitCatalog/) ejemplo tiene un número de imágenes que se agregan a un catálogo de recursos en el proyecto de aplicación de inspección:

![](image-images/asset-whale-sml.png "El ejemplo WatchKitCatalog tiene un número de imágenes que se agregan a un catálogo de recursos en el proyecto de aplicación de inspección")

Estos pueden cargarse y mostrarse sobre el uso de inspección eficazmente `SetImage` con el parámetro de nombre de cadena:

```csharp
myImageControl.SetImage("Whale");
myOtherImageControl.SetImage("Worry");
```

### <a name="background-images"></a>Imágenes de fondo

Se aplica la misma lógica para la `SetBackgroundImage (string imageName)` en el `Button`, `Group`, y `InterfaceController` clases. Obtener el mejor rendimiento se logra mediante el almacenamiento de las imágenes en la propia aplicación de inspección.


## <a name="images-in-the-watch-extension"></a>Imágenes de la extensión de inspección

Además de cargar las imágenes almacenadas en la propia aplicación de inspección, puede enviar imágenes desde el paquete de extensión para la aplicación del reloj para su visualización (o puede descargar imágenes desde una ubicación remota y mostrarlos).

Para cargar imágenes de la extensión de inspección, crear `UIImage` instancias y, a continuación, llame a `SetImage` con el `UIImage` objeto.

Por ejemplo, el [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) ejemplo tiene una imagen denominada **Bumblebee** en el proyecto de extensión de inspección:

![](image-images/asset-bumblebee-sml.png "El ejemplo WatchKitCatalog tiene una imagen denominada Bumblebee en el proyecto de extensión de inspección")

Se generará el código siguiente:

- la imagen se cargue en memoria, y
- se muestra en el reloj.

```csharp
using (var image = UIImage.FromBundle ("Bumblebee")) {
    myImageControl.SetImage (image);
}
```


## <a name="animations"></a>Animaciones

Para animar un conjunto de imágenes, deben comiencen con el mismo prefijo y tienen un sufijo numérico.

El [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) ejemplo tiene una serie de imágenes numeradas en el proyecto de aplicación de supervisión con el **Bus** prefijo:

![](image-images/asset-bus-animation-sml.png "El ejemplo de WatchKitCatalog tiene una serie de imágenes numeradas en el proyecto de aplicación de supervisión con el prefijo de Bus")

Para mostrar estas imágenes como una animación, cargar la imagen mediante `SetImage` con el prefijo del nombre y, después, llame `StartAnimating`:

```csharp
animatedImage.SetImage ("Bus");
animatedImage.StartAnimating ();
```

Llamar a `StopAnimating` en el control de imagen para detener la animación de bucle:

```csharp
animatedImage.StopAnimating ();
```


<a name="cache" />

## <a name="appendix-caching-images-watchos-1"></a>Apéndice: Almacenamiento en caché de imágenes (watchOS 1)

> [!IMPORTANT]
> las aplicaciones para watchOS 3 ejecutan completamente en el dispositivo. La siguiente información es watchOS 1 solo para aplicaciones.

Si la aplicación usa varias veces una imagen que se almacena en la extensión (o se ha descargado), es posible almacenar en caché la imagen en el almacenamiento de la inspección, para aumentar el rendimiento para las pantallas posteriores.

Utilice la `WKInterfaceDevice`s `AddCachedImage` método para transferir la imagen a la inspección y, a continuación, usar `SetImage` con el parámetro de nombre de imagen como una cadena para que aparezca:

```csharp
var device = WKInterfaceDevice.CurrentDevice;
using (var image = UIImage.FromBundle ("Bumblebee")) {
    if (!device.AddCachedImage (image, "Bumblebee")) {
            Console.WriteLine ("Image cache full.");
        } else {
            cachedImage.SetImage ("Bumblebee");
        }
    }
}
```

Puede consultar el contenido de la caché de imágenes en el código mediante `WKInterfaceDevice.CurrentDevice.WeakCachedImages`.


### <a name="managing-the-cache"></a>Administrar la memoria caché

La memoria caché unos 20 MB de tamaño. Se mantiene entre reinicios de aplicación y, cuando se llena es su responsabilidad para borrar los archivos mediante `RemoveCachedImage` o `RemoveAllCachedImages` métodos en el `WKInterfaceDevice.CurrentDevice` objeto.



## <a name="related-links"></a>Vínculos relacionados

- [WatchKitCatalog (ejemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Documento de imagen de Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Images.html)
