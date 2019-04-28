---
title: Controles de interfaz de usuario de Xamarin de watchOS
description: Este documento describe los distintos controles que están disponibles para su uso en interfaces de usuario para watchOS. Proporciona una descripción de las etiquetas, botones, conmutadores, controles deslizantes, imágenes, separadores, mapas y mucho más.
ms.prod: xamarin
ms.assetid: EDFAD203-02EA-4A74-9CE2-7B8513BC90E1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/19/2016
ms.openlocfilehash: c8ef76f24b017f5e3e6bec9d39534f3626e79147
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60893088"
---
# <a name="watchos-user-interface-controls-in-xamarin"></a>Controles de interfaz de usuario de Xamarin de watchOS

El [ **WatchKitCatalog** ](https://github.com/xamarin/monotouch-samples/tree/master/watchOS/WatchKitCatalog) muestra varios controles para watchOS. Guión gráfico de la aplicación se muestra aquí (haga clic para ampliar):

[![](images/storyboard-sml.png "Diseño de ejemplo para watchOS")](images/storyboard.png#lightbox)

Los nombres de todos los controles mediante programación tiene como prefijo `WKInterface` (p ej. `WKInterfaceLabel`, `WKInterfaceButton`).

|Control|Descripción|Captura de pantalla|
|---|---|---|
|Etiqueta|Use `SetText` y otras propiedades para controlar la apariencia del texto en un control label. `NSAttributedString` También se admite.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/LabelDetailController.cs)|![](Images/label.png)|
|Botón|Crear y establecer propiedades en el guión gráfico. CTRL + arrastrar para agregar un `Action` para implementar un controlador para cuando se hace clic en.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ButtonDetailController.cs)|![](Images/button.png)|
|Modificador|Use `SetOn` para controlar el estado del conmutador.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SwitchDetailController.cs)|![](Images/switch.png)|
|Slider|Son posibles muchos estilos diferentes.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SliderDetailController.cs)|![](Images/slider.png)|
|Imagen|Usar `myImage.SetImage("MyWatchImage")` para cargar imágenes en el reloj, o `WKInterfaceDevice.CurrentDevice.AddCachedImage` para almacenarlas en caché para su uso repetido en el reloj.<br />[Documentación del Control de imagen](~/ios/watchos/user-interface/image.md)<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ImageDetailController.cs)|![](Images/image.png)|
|Separador|Utilice los separadores para ayudar a crear la inspección atractiva interfaces de usuario.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SeparatorDetailController.cs)|![](Images/separator.png)| 
|Asignación|La imagen del mapa se muestra estáticamente en el reloj, pero puede controlar muchos aspectos de su apariencia, incluida la adición de PIN.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MapDetailController.cs)|![](Images/map.png)|
|Película & InlineMove|Películas pueden abierto por sí solas o en línea<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MovieDetailController.cs)|![](Images/movie.png)|
|Agrupar|Utilice grupos para facilitar la creación de inspección atractiva interfaces de usuario.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GroupDetailController.cs)|![](Images/group.png)|
|Tabla|Una versión simplificada de las tablas en iOS. Implemente `DidSelectRow` para responder a la selección del usuario (o utilice un segue).<br />[Documentación del Control de tabla](~/ios/watchos/user-interface/table.md)<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/Table%20Detail%20Controller/TableDetailController.cs)|![](Images/table.png)|
|Dispositivo|`WKInterfaceDevice.CurrentDevice` incluye propiedades como `ScreenBounds`, `ScreenScale`, y `PreferredContentSizeCategory`.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/DeviceDetailController.cs)|![](Images/device.png)|
|[Menu](~/ios/watchos/user-interface/menu.md)|Definir el menú presionado force en el guión gráfico e implementar las acciones para cada botón en el código.<br />[Documentación de menú del Control (Force Touch)](~/ios/watchos/user-interface/menu.md)<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ControllerDetailController.cs)|![](Images/controller.png)|
|Entrada de texto|Use `PresentTextInputController` y `WKTextInputMode` enumeración.<br />[Documentación de entrada de texto](~/ios/watchos/user-interface/text-input.md)<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TextInputController.cs)|![](Images/textinput.png)|
|Corona digital|La corona Digital puede usarse para dirigir un selector o su rotación se puede realizar el seguimiento en el código.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/CrownDetailController.cs)|![](Images/digital-crown.png)|
|Gestos|Hay cuatro tipos de reconocimiento de gestos que se pueden agregar a una escena: TAP, deslice el dedo, panorámica y LongPress.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GestureDetailController.cs)|![](Images/gestures.png)|


## <a name="related-links"></a>Vínculos relacionados

- [WatchKitCatalog (ejemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Referencia de API del Kit de inspección](xref:WatchKit)
