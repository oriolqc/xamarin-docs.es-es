---
title: Vistas Web Xamarin.iOS
description: Este documento describe las distintas formas en que una aplicación de Xamarin.iOS puede mostrar el contenido web. Se trata de UIWebView, WKWebView, SFSafariViewController, Safari y seguridad de transporte de la aplicación.
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: f720eae68415ab9efe021e53c9da4875209cd221
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790501"
---
# <a name="web-views-in-xamarinios"></a>Vistas Web Xamarin.iOS

Sobre la duración de iOS Apple ha lanzado un número de formas para que los desarrolladores de aplicaciones incorporar la funcionalidad de vista web en sus aplicaciones. La mayoría de los usuarios utilizan el explorador web Safari integrado en su dispositivo iOS y, por tanto, esperan que la funcionalidad de vista web de otras aplicaciones es coherente con esta experiencia. Espera que los mismos movimientos para que funcione, el rendimiento sea en el mismo nivel y la funcionalidad de la misma.

En este artículo, analizaremos cada una de las tres vistas Web proporcionadas por Apple: `UIWebView`, `WKWebview`, y `SFSafariViewController`, sus similitudes y diferencias y cómo se puede usar. 

iOS 11 introdujeron nuevos cambios a ambos `WKWebView` y `SFSafariViewController`. Para obtener más información al respecto, consulte el [Web cambios en la Guía de iOS 11](~/ios/platform/introduction-to-ios11/web.md) guía.

## <a name="uiwebview"></a>UIWebView

`UIWebView` forma de Apple heredado de proporcionar contenido web en la aplicación. Se publicó en iOS 2.0 y está en desuso a partir de 8.0.

Si piensa admitir iOS versiones anteriores a 8.0, tendrá que usar `UIWebView`. Debido al hecho de que `UIWebView` es menor optimizado para el rendimiento de las alternativas, se recomienda que debe comprobar la versión de iOS del usuario. Si lo 8.0 o posterior, mediante cualquiera de las opciones se explican a continuación creará una mejor experiencia de usuario.
 
Para agregar una UIWebView a la aplicación Xamarin.iOS, utilice el código siguiente:
 
```
webView = new UIWebView (View.Bounds);
View.AddSubview(webView);

var url = "https://xamarin.com"; // NOTE: https secure request
webView.LoadRequest(new NSUrlRequest(new NSUrl(url)));
```

Esto produce la siguiente vista web:

[![](uiwebview-images/webview.png "El efecto de ScalesPagesToFit")](uiwebview-images/webview.png#lightbox)

Para obtener más información sobre el uso de `UIWebView`, consulte las recetas siguientes:


- [Carga una página Web](https://developer.xamarin.com/recipes/ios/content_controls/web_view/load_a_web_page/)
- [Cargar contenido Local](https://developer.xamarin.com/recipes/ios/content_controls/web_view/load_local_content/)
- [La carga de documentos no Web](https://developer.xamarin.com/recipes/ios/content_controls/web_view/load_non-web_documents/)

## <a name="wkwebview"></a>WKWebView

`WKWebView` se introdujo en iOS 8 Permitir que los desarrolladores de aplicaciones para implementar una interfaz similar a la de Safari móvil de exploración web. Esto es debe, en parte, al hecho de que `WKWebView` utiliza el motor de Nitro Javascript, el mismo motor utilizado Safari móvil. `WKWebView` siempre se debe utilizar over UIWebView eran posibles debido a la [aumentar el rendimiento](http://blog.initlabs.com/post/100113463211/wkwebview-vs-uiwebview), fácil de usar gestos y la facilidad de la interacción entre la aplicación y de la página web integrados.
  
`WKWebView` pueden agregarse a la aplicación de forma casi idéntica a UIWebView, sin embargo, como el programador dispone de un mayor control sobre la funcionalidad y la interfaz de usuario/UX. Crear y mostrar el objeto de vista web mostrará la página solicitada, sin embargo, puede controlar cómo se presenta la vista, cómo puede navegar el usuario y cómo el usuario sale de la vista.  

El código siguiente puede utilizarse para iniciar un `WKWebView` en la aplicación Xamarin.iOS:

```csharp
    WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
    View.AddSubview(webView);

    var url = new NSUrl("https://xamarin.com");
    var request = new NSUrlRequest(url);
    webView.LoadRequest(request);
```

Esto produce la siguiente vista web:

[![](uiwebview-images/wkwebview.png "Una vista web de ejemplo sin ScalesPagesToFit")](uiwebview-images/wkwebview.png#lightbox)

Es importante tener en cuenta que `WKWebView` está en el espacio de nombres WebKit, por lo que tendrá que agregar este using (directiva) en la parte superior de la clase.

`WKWebView` También puede usarse dentro de las aplicaciones de Xamarin.Mac y, por lo tanto, debería plantearse utilizarlo si va a crear una aplicación de Mac/iOS multiplataforma.

El [administrar alertas de JavaScript](https://developer.xamarin.com/recipes/ios/content_controls/web_view/handle_javascript_alerts/) receta también proporciona información sobre el uso de WKWebView con Javascript

<a name="safariviewcontroller" />

## <a name="sfsafariviewcontroller"></a>SFSafariViewController
 
 `SFSafariViewController` es la forma más reciente para proporcionar contenido web de la aplicación y está disponible en iOS 9 y versiones posteriores. A diferencia de `UIWebView` o `WKWebView`, `SFSafariViewController` es un controlador de vista y no se puede usar con otras vistas. Se debe presentar `SFSafariViewController` como un nuevo controlador de vista de la misma manera producirían cualquier controlador de vista.
 
 `SFSafariViewController` es básicamente un 'mini safari' que se puede incrustar en la aplicación. Al igual que WKWebView utiliza el mismo motor de Javascript de Nitro, pero también proporciona una gama de Safari características adicionales como autorrelleno, lector y la capacidad de compartir las cookies y datos con Safari móvil. Interacción entre el usuario y la `SFSafariViewController` no es accesible para la aplicación. La aplicación no tendrá acceso a cualquiera de las características predeterminadas de Safari.
 
También, de forma predeterminada, implementa una **realiza** botón, lo que permite al usuario volver fácilmente a la aplicación y reenviar y atrás, botones de navegación, permitir que el usuario navegar a través de una pila de páginas web. Además, también proporciona al usuario con una dirección de la barra que permiten la tranquilidad de saber que se encuentra en la página web esperado. La barra de direcciones no permite al usuario cambiar la dirección url. 

Estas implementaciones no se puede cambiar, por lo que `SFSafariViewController` es ideal para utilizar como el explorador predeterminado si desea que la aplicación presentar una página Web sin ninguna personalización.

El código siguiente puede utilizarse para iniciar un `SFSafariViewController` en la aplicación Xamarin.iOS:

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

Esto produce la siguiente vista web:

[![](uiwebview-images/sfsafariviewcontroller.png "Una vista web de ejemplo con SFSafariViewController")](uiwebview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

También es posible abrir la aplicación móvil de Safari desde dentro de la aplicación, utilizando el código siguiente:

```csharp
var url = new NSUrl("https://xamarin.com");

UIApplication.SharedApplication.OpenUrl(url);

```

Esto produce la siguiente vista web:

[![](uiwebview-images/safari.png "Una página web que se presenta en Safari")](uiwebview-images/safari.png#lightbox)

Navegar por los usuarios fuera de la aplicación en Safari debe siempre evitar en general. La mayoría de los usuarios no esperan navegación fuera de la aplicación, por lo que si sale de la aplicación, los usuarios no pueden nunca devuelven, básicamente la terminación de contratación.

mejoras de iOS 9 permiten al usuario volver fácilmente a la aplicación a través de un botón Atrás en la que se proporciona en la esquina superior izquierda de la página de Safari.

## <a name="app-transport-security"></a>Seguridad de transporte de la aplicación

Seguridad de transporte de la aplicación, o *ATS* introducida por Apple iOS 9 para asegurarse de que todas las comunicaciones de internet se ajustan para proteger los procedimientos recomendados de conexión.

Para obtener más información sobre ATS, incluida la forma de implementar en la aplicación, consulte el [seguridad de transporte de la aplicación](~/ios/app-fundamentals/ats.md) guía.

## <a name="related-links"></a>Vínculos relacionados

- [Vistas Web (ejemplo)](https://developer.xamarin.com/samples/monotouch/WebView/)
