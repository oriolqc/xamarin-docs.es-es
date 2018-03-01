---
title: Control de imagen
ms.topic: article
ms.prod: xamarin
ms.assetid: B741C207-3427-46F3-9C90-A52BF8933FA4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: f2416cb4f29eb74cf9cbc3db0a0bb3aa60806589
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="image-control"></a>Control de imagen

watchOS proporciona un [ `WKInterfaceImage` ](https://developer.xamarin.com/api/type/WatchKit.WKInterfaceImage/) control para mostrar imágenes y animaciones simples. Algunos controles pueden tener también una imagen de fondo (por ejemplo, botones, grupos y controladores de interfaz).

![](image-images/image-walkway.png "Imagen que muestra de Apple Watch") ![ ] (image-images/image-animation.png "de Apple Watch con una animación simple")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

Usar imágenes de catálogo de activos para agregar imágenes a las aplicaciones de Kit de inspección.
Solo  **@2x**  versiones están necesarias, puesto que todos ver dispositivos tienen pantallas de la Retina.

![](image-images/asset-universal-sml.png "Solo 2 versiones de x están necesarias, puesto que todos ver dispositivos tienen pantallas de Retina")

Es recomendable para asegurarse de que las imágenes por sí mismos tienen el tamaño correcto para la presentación de inspección. *Evitar* con imágenes incorrectamente con tamaño (más especialmente grandes) y ajustar la escala para mostrarlos en el reloj.

Puede utilizar los tamaños de Kit de inspección (38mm y 42mm) en una imagen de catálogo de activos para especificar imágenes diferentes para cada tamaño de presentación.

![](image-images/asset-watch-sml.png "Puede utilizar los tamaños de inspección Kit 38 y 42mm en una imagen de catálogo de activos para especificar imágenes diferentes para cada tamaño de presentación")


## <a name="images-on-the-watch"></a>Imágenes en el reloj

Es la manera más eficaz para mostrar imágenes *incluirlos en el proyecto de aplicación de inspección* y mostrarlos mediante el `SetImage(string imageName)` método.

Por ejemplo, el [WatchKitCatalog](https://developer.xamarin.com/samples/WatchKitCatalog/) ejemplo tiene un número de imágenes que se agreguen a un catálogo de activos en el proyecto de aplicación de inspección:

![](image-images/asset-whale-sml.png "El ejemplo WatchKitCatalog tiene un número de imágenes que se agreguen a un catálogo de activos en el proyecto de aplicación de inspección")

Estos pueden cargarse y mostrarse sobre el uso de inspección eficazmente `SetImage` con el parámetro de nombre de cadena:

```csharp
myImageControl.SetImage("Whale");
myOtherImageControl.SetImage("Worry");
```

### <a name="background-images"></a>Imágenes de fondo

Se aplica la misma lógica para la `SetBackgroundImage (string imageName)` en el `Button`, `Group`, y `InterfaceController` clases. Obtener el mejor rendimiento se logra mediante el almacenamiento de las imágenes en la propia aplicación de inspección.


## <a name="images-in-the-watch-extension"></a>Imágenes en la extensión de inspección

Además de cargar imágenes que se almacenan en la propia aplicación de inspección, puede enviar imágenes desde el paquete de extensión a la aplicación de inspección para mostrar (o puede descargar imágenes desde una ubicación remota y mostrarlos).

Para cargar imágenes de la extensión de inspección, crear `UIImage` instancias y, a continuación, llame a `SetImage` con el `UIImage` objeto.

Por ejemplo, el [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) ejemplo tiene una imagen denominada **Bumblebee** en el proyecto de extensión de inspección:

![](image-images/asset-bumblebee-sml.png "El ejemplo WatchKitCatalog tiene una imagen denominada Bumblebee en el proyecto de extensión de inspección")

Se producirá el siguiente código:

- la imagen que se cargan en memoria, y
- se muestra en el reloj.

```csharp
using (var image = UIImage.FromBundle ("Bumblebee")) {
    myImageControl.SetImage (image);
}
```


## <a name="animations"></a>Animaciones

Para animar un conjunto de imágenes, se debe comiencen con el mismo prefijo y tienen un sufijo numérico.

El [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) ejemplo tiene una serie de imágenes numeradas en el proyecto de aplicación de inspección con el **Bus** prefijo:

![](image-images/asset-bus-animation-sml.png "El ejemplo de WatchKitCatalog tiene una serie de imágenes numeradas en el proyecto de aplicación de inspección con el prefijo de Bus")

Para mostrar estas imágenes como una animación, cargar la imagen mediante `SetImage` con el nombre de prefijo y, después, llame `StartAnimating`:

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
> aplicaciones de watchOS 3 se ejecutan por completo en el dispositivo. La siguiente información es watchOS 1 solo para aplicaciones.



Si la aplicación utiliza repetidamente una imagen que se almacena en la extensión (o se ha descargado), es posible almacenar en caché la imagen en el almacenamiento de la inspección, para aumentar el rendimiento para las pantallas posteriores.

Utilice la `WKInterfaceDevice`s `AddCachedImage` método para transferir la imagen a la inspección y, a continuación, usar `SetImage` con el parámetro de nombre de imagen como una cadena para mostrarla:

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

La caché de unos 20 MB de tamaño. Se mantiene entre los distintos reinicios de la aplicación y, cuando se llena es su responsabilidad para borrar archivos mediante `RemoveCachedImage` o `RemoveAllCachedImages` métodos en la `WKInterfaceDevice.CurrentDevice` objeto.



## <a name="related-links"></a>Vínculos relacionados

- [WatchKitCatalog (ejemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Documento de imagen de Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Images.html)
