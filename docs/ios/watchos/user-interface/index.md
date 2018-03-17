---
title: Interfaz de usuario de watchOS
ms.topic: article
ms.prod: xamarin
ms.assetid: EDFAD203-02EA-4A74-9CE2-7B8513BC90E1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/19/2016
ms.openlocfilehash: f7a7b565372970cc487b664ed7415cf876e290b6
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/17/2018
---
# <a name="watchos-user-interface"></a>Interfaz de usuario de watchOS

El [ **WatchKitCatalog** ](https://github.com/xamarin/monotouch-samples/tree/master/watchOS/WatchKitCatalog) ejemplo muestra varios controles watchOS. Guión gráfico de la aplicación se muestra aquí (haga clic para acercar o alejar):

[![](images/storyboard-sml.png "Diseño de watchOS de ejemplo")](images/storyboard.png#lightbox)

Los nombres de todos los controles mediante programación tiene como prefijo `WKInterface` (p. ej. `WKInterfaceLabel`, `WKInterfaceButton`).

|Control|Descripción|Captura de pantalla|
|---|---|---|
|Etiqueta|Use `SetText` y otras propiedades para controlar la apariencia del texto en un control de etiqueta. `NSAttributedString` También se admite.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/LabelDetailController.cs)|![](Images/label.png)|
|Botón|Crear y establecer propiedades en el guión gráfico. CTRL + arrastrar para agregar una `Action` para implementar un controlador para cuando se hace clic en.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ButtonDetailController.cs)|![](Images/button.png)|
|Modificador|Use `SetOn` para controlar el estado del conmutador.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SwitchDetailController.cs)|![](Images/switch.png)|
|Slider|Muchos estilos diferentes son posibles.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SliderDetailController.cs)|![](Images/slider.png)|
|Imagen|Usar `myImage.SetImage("MyWatchImage")` para cargar imágenes en el reloj, o `WKInterfaceDevice.CurrentDevice.AddCachedImage` para almacenarlos en memoria caché para su uso repetido en el reloj.<br />[Documentación de Control de imagen](~/ios/watchos/user-interface/image.md)<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ImageDetailController.cs)|![](Images/image.png)|
|Separador|Utilice los separadores para ayudar a crear atractiva inspección interfaces de usuario.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SeparatorDetailController.cs)|![](Images/separator.png)| 
|Asignación|La imagen del mapa se muestra estáticamente en el reloj, pero puede controlar muchos aspectos de su apariencia, incluida la adición de PIN.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MapDetailController.cs)|![](Images/map.png)|
|Película & InlineMove|Películas pueden abrir por sí solas o en línea<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MovieDetailController.cs)|![](Images/movie.png)|
|Agrupar|Utilizar grupos para ayudar a crear atractiva inspección interfaces de usuario.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GroupDetailController.cs)|![](Images/group.png)|
|Tabla|Una versión simplificada de tablas en iOS. Implemente `DidSelectRow` para responder a la selección del usuario (o usar un segue).<br />[Documentación de Control de tabla](~/ios/watchos/user-interface/table.md)<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/Table%20Detail%20Controller/TableDetailController.cs)|![](Images/table.png)|
|Dispositivo|`WKInterfaceDevice.CurrentDevice` incluye propiedades como `ScreenBounds`, `ScreenScale`, y `PreferredContentSizeCategory`.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/DeviceDetailController.cs)|![](Images/device.png)|
|[Menu](~/ios/watchos/user-interface/menu.md)|Defina el menú presione force en el guión gráfico e implemente las acciones para cada botón en el código.<br />[Documentación de menú Control (Force Touch)](~/ios/watchos/user-interface/menu.md)<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ControllerDetailController.cs)|![](Images/controller.png)|
|Entrada de texto|Use `PresentTextInputController` y `WKTextInputMode` enumeración.<br />[Documentación de entrada de texto](~/ios/watchos/user-interface/text-input.md)<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TextInputController.cs)|![](Images/textinput.png)|
|Copa digital|La copa Digital puede usarse para controlar un selector o su rotación puede realizar el seguimiento en el código.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/CrownDetailController.cs)|![](Images/digital-crown.png)|
|Gestos|Hay cuatro tipos de reconocimiento de gestos que se pueden agregar a una escena: Tap, deslice el dedo, panorámica y LongPress.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GestureDetailController.cs)|![](Images/gestures.png)|


## <a name="related-links"></a>Vínculos relacionados

- [WatchKitCatalog (ejemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Referencia de API de Kit de inspección](https://developer.xamarin.com/api/namespace/WatchKit/)
