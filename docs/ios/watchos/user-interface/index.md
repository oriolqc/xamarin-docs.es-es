---
title: Interfaz de usuario de watchOS
ms.topic: article
ms.prod: xamarin
ms.assetid: EDFAD203-02EA-4A74-9CE2-7B8513BC90E1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/19/2016
ms.openlocfilehash: 34063be728f8a13bbe5d68440d9aceba417c5627
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="watchos-user-interface"></a>Interfaz de usuario de watchOS

El [ **WatchKitCatalog** ](https://github.com/xamarin/monotouch-samples/tree/master/watchOS/WatchKitCatalog) ejemplo muestra varios controles watchOS. Guión gráfico de la aplicación se muestra aquí (haga clic para acercar o alejar):

[ ![](images/storyboard-sml.png "Diseño de watchOS de ejemplo")](images/storyboard.png)

Los nombres de todos los controles mediante programación tiene como prefijo `WKInterface` (p. ej. `WKInterfaceLabel`, `WKInterfaceButton`).


<table align="center" border="1" cellpadding="1" cellspacing="1">
  <thead>
      <th>
        <strong>Control</strong>
      </th>
      <th>
        <strong>Descripción</strong>
      </th>
      <th>
        <strong>captura de pantalla</strong>
      </th>
    </thead>
    <tbody>
    <tr>
      <td valign="top">
Etiqueta </td>
      <td valign="top">
Use <code>SetText</code> y otras propiedades para controlar la apariencia del texto en un control de etiqueta. <code>NSAttributedString</code> También se admite.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/LabelDetailController.cs">Código de catálogo</a>
      </td>
      <td>
        <img src="Images/label.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Botón </td>
      <td valign="top">
Crear y establecer propiedades en el guión gráfico. <kbd>CTRL + arrastrar</kbd> para agregar una <code>Action</code> para implementar un controlador para cuando se hace clic en.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ButtonDetailController.cs">Código de catálogo</a>
      </td>
      <td>
        <img src="Images/button.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Modificador </td>
      <td valign="top">
Use <code>SetOn</code> para controlar el estado del conmutador.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SwitchDetailController.cs">Código de catálogo</a>
      </td>
      <td>
        <img src="Images/switch.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Slider </td>
      <td valign="top">
Muchos estilos diferentes son posibles.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SliderDetailController.cs">Código de catálogo</a>
      </td>
      <td>
        <img src="Images/slider.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Imagen </td>
      <td valign="top">
Usar <code>myImage.SetImage("MyWatchImage")</code> para cargar imágenes en el reloj, o <code>WKInterfaceDevice.CurrentDevice.AddCachedImage</code> para almacenarlos en memoria caché para su uso repetido en el reloj.
        <br />
        <a href="~/ios/watchos/user-interface/image.md">Documentación de Control de imagen</a>
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ImageDetailController.cs">Código de catálogo</a>
      </td>
      <td>
        <img src="Images/image.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Separador </td>
      <td valign="top">
Utilice los separadores para ayudar a crear atractiva inspección interfaces de usuario.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SeparatorDetailController.cs">Código de catálogo</a>
      </td>
      <td>
        <img src="Images/separator.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Asignación </td>
      <td valign="top">
La imagen del mapa se muestra estáticamente en el reloj, pero puede controlar muchos aspectos de su apariencia, incluida la adición de PIN.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MapDetailController.cs">Código de catálogo</a>
      </td>
      <td>
        <img src="Images/map.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Película & InlineMove </td>
      <td valign="top">
Películas pueden abrir por sí solas o en línea <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MovieDetailController.cs">Código de catálogo</a>
      </td>
      <td>
        <img src="Images/movie.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Agrupar </td>
      <td valign="top">
Utilizar grupos para ayudar a crear atractiva inspección interfaces de usuario.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GroupDetailController.cs">Código de catálogo</a>
      </td>
      <td>
        <img src="Images/group.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Tabla </td>
      <td valign="top">
Una versión simplificada de tablas en iOS.
Implemente <code>DidSelectRow</code> para responder a la selección del usuario (o usar un segue).
        <br />
        <a href="~/ios/watchos/user-interface/table.md">Documentación de Control de tabla</a>
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TableDetailController.cs">Código de catálogo</a>
      </td>
      <td>
        <img src="Images/table.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Dispositivo </td>
      <td valign="top">
        <code>WKInterfaceDevice.CurrentDevice</code> incluye propiedades como <code>ScreenBounds</code>, <code>ScreenScale</code>, y <code>PreferredContentSizeCategory</code>.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/DeviceDetailController.cs">Código de catálogo</a>
      </td>
      <td>
        <img src="Images/device.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="~/ios/watchos/user-interface/menu.md">Menú</a>
      </td>
      <td valign="top">
Defina el menú presione force en el guión gráfico e implemente las acciones para cada botón en el código.
        <br />
        <a href="~/ios/watchos/user-interface/menu.md">Documentación de menú Control (Force Touch)</a>
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ControllerDetailController.cs">Código de catálogo</a>
      </td>
      <td>
        <img src="Images/controller.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Entrada de texto </td>
      <td valign="top">
Use <code>PresentTextInputController</code> y <code>WKTextInputMode</code> enumeración.
        <br />
        <a href="~/ios/watchos/user-interface/text-input.md">Documentación de entrada de texto</a>
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TextInputDetailController.cs">Código de catálogo</a>
      </td>
      <td>
        <img src="Images/textinput.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Copa digital </td>
      <td valign="top">
La copa Digital puede usarse para controlar un selector o su rotación puede realizar el seguimiento en el código.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/CrownDetailController.cs">Código de catálogo</a>
      </td>
      <td>
        <img src="Images/digital-crown.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Gestos </td>
      <td valign="top">
Hay cuatro tipos de reconocimiento de gestos que se pueden agregar a una escena: Tap, deslice el dedo, panorámica y LongPress.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GestureDetailController.cs">Código de catálogo</a>
      </td>
      <td>
        <img src="Images/gestures.png" class="tableimg">
      </td>
    </tr>
    </tbody>
</table>



## <a name="related-links"></a>Vínculos relacionados

- [WatchKitCatalog (ejemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Referencia de API de Kit de inspección](https://developer.xamarin.com/api/namespace/WatchKit/)
