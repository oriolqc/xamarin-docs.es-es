---
title: Cambios de web en iOS 11
description: Este documento describen los cambios realizados en WebKit y la plataforma de servicios de Safari en iOS 11. Describe cómo trabajar con un estilo de las actualizaciones en SFSafariViewController y las nuevas características de WKWebView.
ms.prod: xamarin
ms.assetid: C74B2E94-177C-43D4-8D6C-9B528773C120
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/12/2017
ms.openlocfilehash: ba691a6605dcf7e86a76ed13d4c8ef5f0984ff6e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61399730"
---
# <a name="web-changes-in-ios-11"></a>Cambios de web en iOS 11

iOS 11 introduce una nueva versión del explorador de web de Safari: Safari 11.0: que incluye los cambios de WebKit y SafariServices. Esta guía analiza estos cambios.

## <a name="safariservices"></a>SafariServices

`SFSafariViewController` se introdujo en iOS 9 como una opción para mostrar contenido web o autenticar a los usuarios de la aplicación. Encontrará más información sobre sus características en el [vistas Web](~/ios/user-interface/controls/uiwebview.md#safariviewcontroller) guía.

iOS 11 ha introducido las actualizaciones de estilo para el controlador de vista de Safari, proporcionar a los usuarios una experiencia más sencilla entre una aplicación y la web. Por ejemplo, la eliminación de la dirección de la barra ahora proporciona el controlador de vista la sensación de un explorador en la aplicación, en lugar de un mini explorador Safari. También puede personalizar la combinación de colores para ajustarse a la sesión con la combinación de colores de la aplicación estableciendo el `preferredBarTintColor` y `PreferredControlTintColor` propiedades:

```csharp
sfViewController.PreferredControlTintColor = UIColor.White;
sfViewController.PreferredBarTintColor = UIColor.Purple;
```

El fragmento de código siguiente representa las barras de color púrpura y en blanco, tal como se muestra en la siguiente imagen:

![Barras de SFSafariViewController representadas en color púrpura y en blanco](web-images/image1.png)

También se puede cambiar el botón Descartar presentado en el controlador de vista de Safari estableciendo el `DismissButtonStyle` propiedad como `Done`, `Close`, o `Cancel`:

```csharp
sfViewController.DismissButtonStyle = SFSafariViewControllerDismissButtonStyle.Close;
```

![Descartar el texto del botón Cambiar](web-images/image2.png)

Este valor se puede cambiar mientras `SFSafariViewController` se presenta.


Según el contenido que se muestra dentro de un controlador de vista de Safari, podría ser necesario para garantizar que las barras de menús no contraer como el usuario se desplaza. Esto se habilita estableciendo la nueva `BarCollapsedEnabled` propiedad `false`:

```csharp
var config = new SFSafariViewControllerConfiguration();
config.BarCollapsingEnabled = false;

var sfViewController = new SFSafariViewController(url, config);
```

![Barra contraer deshabilitado](web-images/image3.png)

Apple también ha realizado las actualizaciones a la privacidad en el controlador de vista de Safari en iOS 11. Ahora, exploración de datos, como las cookies y el almacenamiento local solo existen en por aplicación, en lugar de en todas las instancias de controlador de vista de Safari. Esto evita que la actividad de exploración de usuario privada dentro de la aplicación.

Características adicionales, como arrastrar y colocar compatibilidad con direcciones URL y compatibilidad con `window.open()` también se han agregado a `SFSafariViewController` en iOS 11. Puede encontrar más información sobre estas nuevas características en [la documentación de Apple SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?changes=latest_minor).


## <a name="webkit"></a>WebKit

`WKWebView` se presentó como parte de WebKit en iOS 8 como una forma de mostrar contenido web para el usuario. Es mucho más personalizable que `SFSafariViewController`, lo que permite crear su propia interfaz de usuario y navegación.

Apple ha introducido tres mejoras principales para `WKWebView` con iOS 11: 

- La capacidad de administrar las cookies
- Filtrado de contenido
- Carga de recursos personalizados. 

Administración de cookies se realiza a través de la nueva [ `WKHttpCookieStore` ](https://developer.apple.com/documentation/webkit/wkhttpcookiestore) (clase), que le permite agregar y eliminar las cookies, para obtener todas las cookies que se almacenan en un WKWebView y observar la cookie de almacenar los cambios.

Contenido de filtrado le permite administrar el tipo de contenido que verán los usuarios, lo que le permite asegurarse de que es seguro, familia compatible y, si es necesario, solo está disponible para un grupo selecto de usuarios. Esto se implementa a través del nuevo [ `WKContentRuleList` ](https://developer.apple.com/documentation/webkit/wkcontentrulelist) (clase), proporcionando pares de desencadenadores y acciones en JSON. Encontrará más información sobre estos desencadenadores y acciones en Apple [reglas de bloqueo de contenido](https://developer.apple.com/library/content/documentation/Extensions/Conceptual/ContentBlockingRules/Introduction/Introduction.html) guía.

iOS 11 ahora le permite personalizar `WKWebView` con recursos personalizados de carga para su contenido web. Esto se implementa a través de la `IWKUrlSchemeHandler` interfaz, que le permite controlar los esquemas de dirección URL que no son nativas para el Kit de Web. Esta interfaz tiene un método de inicio y detención que debe implementarse:

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

Una vez que se ha implementado el controlador, puede usarlo para establecer el `SetUrlSchemeHandler` propiedad en el `WKWebViewConfiguration`. A continuación, cargue la dirección URL de algo que utiliza el esquema personalizado:

```csharp
var config = new WKWebViewConfiguration();
config.SetUrlSchemeHandler(new MyHandler(), "xamarin-asset");

webView = new WKWebView (View.Frame, config);
webView.LoadRequest (new NSUrlRequest("xamarin-asset://xamarin.com"));
```

