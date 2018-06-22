---
title: Cambios de Web en iOS 11
description: Este documento describen los cambios realizados en WebKit y el marco de trabajo de servicios de Safari en iOS 11. Describe cómo trabajar con actualizaciones en SFSafariViewController y nuevas características en WKWebView de la aplicación de estilos.
ms.prod: xamarin
ms.assetid: C74B2E94-177C-43D4-8D6C-9B528773C120
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/12/2016
ms.openlocfilehash: f5876a9d201950ebac45e8b1f786b0e97452a7f1
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787453"
---
# <a name="web-changes-in-ios-11"></a>Cambios de Web en iOS 11

iOS 11 presenta una nueva versión del explorador de web Safari – Safari 11.0: que incluye cambios en WebKit y SafariServices. Esta guía explora estos cambios.

## <a name="safariservices"></a>SafariServices

`SFSafariViewController` se introdujo en iOS 9 como una opción para mostrar el contenido web o autenticar a los usuarios de la aplicación. Encontrará más información sobre sus características en el [vistas Web](~/ios/user-interface/controls/uiwebview.md#safariviewcontroller) guía.

iOS 11 presenta las actualizaciones de estilo para el controlador de vista de Safari, dar a los usuarios una experiencia más uniforme entre una aplicación y la web. Por ejemplo, la eliminación de la barra ahora proporciona el controlador de vista de Safari la apariencia de un explorador en la aplicación, en lugar de un explorador mínima de direcciones. También puede personalizar la combinación de colores se adaptan la combinación de colores de la aplicación estableciendo el `preferredBarTintColor` y `PreferredControlTintColor` propiedades:

```csharp
sfViewController.PreferredControlTintColor = UIColor.White;
sfViewController.PreferredBarTintColor = UIColor.Purple;
```

El siguiente fragmento de código presenta las barras en púrpura y blanco, tal como se muestra en la siguiente imagen:

![Barras de SFSafariViewController representadas en púrpura y notas](web-images/image1.png)

También se puede cambiar el botón Descartar presentado en el controlador de vista de Safari estableciendo la `DismissButtonStyle` propiedad como `Done`, `Close`, o `Cancel`:

```csharp
sfViewController.DismissButtonStyle = SFSafariViewControllerDismissButtonStyle.Close;
```

![Descartar el texto del botón Cambiar](web-images/image2.png)

Este valor se puede cambiar mientras `SFSafariViewController` se presenta.


Dependiendo del contenido que se muestra dentro de un controlador de vista de Safari, podría ser necesario para asegurarse de que las barras de menús no contraen como el usuario se desplaza. Esto se habilita estableciendo la nueva `BarCollapsedEnabled` propiedad `false`:

```csharp
var config = new SFSafariViewControllerConfiguration();
config.BarCollapsingEnabled = false;

var sfViewController = new SFSafariViewController(url, config);
```

![Barra contraer deshabilitado](web-images/image3.png)

Apple también ha realizado las actualizaciones a la privacidad en el controlador de vista de Safari de iOS 11. Ahora, exploración de datos, como las cookies y el almacenamiento local solo existen en una base por aplicación, en lugar de en todas las instancias de controlador de vista de Safari. Esto impide que la actividad de exploración de usuario privada dentro de la aplicación.

Características adicionales como arrastra y coloca compatibilidad con direcciones URL y compatibilidad con `window.open()` también se han agregado a `SFSafariViewController` en iOS 11. También puede encontrar más información acerca de estas nuevas características de [documentación de Apple SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?changes=latest_minor).


## <a name="webkit"></a>WebKit

`WKWebView` se introdujo como parte de WebKit en iOS 8 como un medio de mostrar contenido web para el usuario. Es mucho más personalizable que `SFSafariViewController`, lo que le permite crear su propia interfaz de usuario y de navegación.

Apple ha introducido tres mejoras principales para `WKWebView` con iOS 11: 

- La capacidad de administrar las cookies
- Filtrado de contenido
- Carga de recursos personalizados. 

Administración de cookies se realiza a través del nuevo [ `WKHttpCookieStore` ](https://developer.apple.com/documentation/webkit/wkhttpcookiestore) (clase), que le permite agregar y eliminar las cookies, para obtener todas las cookies que se almacenan en un WKWebView así como observar la cookie almacenar cambios.

Contenido de filtrado le permite administrar el tipo de contenido que verá el usuario, lo que le permite asegurarse de que es segura, familia sencillo y, si es necesario, solo está disponible para un selecto grupo de usuarios. Esto se implementa a través del nuevo [ `WKContentRuleList` ](https://developer.apple.com/documentation/webkit/wkcontentrulelist) (clase), proporcionando pares de desencadenadores y acciones en JSON. Encontrará más información sobre estos desencadenadores y acciones en de Apple [reglas de bloqueo de contenido](https://developer.apple.com/library/content/documentation/Extensions/Conceptual/ContentBlockingRules/Introduction/Introduction.html) guía.

iOS 11 ahora le permite personalizar `WKWebView` con el recurso personalizado de carga para su contenido web. Esto se implementa a través de la `IWKUrlSchemeHandler` interfaz, que le permite manejar los esquemas de direcciones URL que no son nativos al Kit de Web. Esta interfaz tiene un método de inicio y detención que debe implementarse:

```csharp
public class MyHandler : NSObject, IWKUrlSchemeHandler {

    [Export("webView:startURLSchemeTask:")]
    public void StartUrlSchemeTask(WKWebView webView, IWKUrlSchemeTask urlSchemeTask){
        
        // Implement a IWKUrlSchemeTask here
        var response = new NSUrlResponse(urlSchemeTask.Request.Url, "text/html", ContentLength, null);
        urlSchemeTask.DidReceiveResponse(response);
        urlSchemeTask.DidReceiveData(someData);
        urlSchemeTask.DidFinish();
    }

    [Export("webView:stopURLSchemeTask:")]
    public void StopUrlSchemeTask(WKWebView webView, IWKUrlSchemeTask urlSchemeTask){
        throw new NotImplementedException();
    }

}
``` 

Una vez que el controlador se ha implementado, puede utilizarlo para establecer el `SetUrlSchemeHandler` propiedad en el `WKWebViewConfiguration`. A continuación, cargue la dirección URL de algo que usa el esquema personalizado:

```csharp
var config = new WKWebViewConfiguration();
config.SetUrlSchemeHandler(new MyHandler(), "xamarin-asset");

webView = new WKWebView (View.Frame, config);
webView.LoadRequest (new NSUrlRequest("xamarin-asset://xamarin.com"));
```

