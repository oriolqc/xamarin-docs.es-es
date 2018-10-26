---
title: Vistas Web en Xamarin.iOS
description: Este documento describen las distintas formas de que una aplicación Xamarin.iOS puede mostrar contenido web. Describe UIWebView, WKWebView, SFSafariViewController, Safari y seguridad de transporte de la aplicación.
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 9adf514e4fc510617e3f4d801569935ee4a03f25
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104367"
---
# <a name="web-views-in-xamarinios"></a>Vistas Web en Xamarin.iOS

Durante la vigencia de iOS, Apple ha lanzado varias formas para desarrolladores de aplicaciones incorporar la funcionalidad de la vista en sus aplicaciones web. La mayoría de los usuarios utilizan el explorador web integrado de Safari en su dispositivo iOS y, por tanto, esperan que la funcionalidad de vista web desde otras aplicaciones sea coherente con esta experiencia. Se esperan de los gestos mismos funcione, el rendimiento sea de mismo nivel y la funcionalidad de la misma.

En este artículo, analizaremos cada una de las tres vistas Web proporcionadas por Apple: `UIWebView`, `WKWebview`, y `SFSafariViewController`, sus similitudes y diferencias y cómo se puede usar. 

iOS 11 introdujo nuevos cambios a ambos `WKWebView` y `SFSafariViewController`. Para obtener más información, consulte el [Web los cambios en la Guía de iOS 11](~/ios/platform/introduction-to-ios11/web.md) guía.

## <a name="uiwebview"></a>UIWebView

`UIWebView` es el método heredado de Apple para proporcionar contenido web en la aplicación. Se publicó en iOS 2.0 y ha quedado obsoleta en 8.0.

Si tiene previsto admitir las versiones de iOS anteriores a la 8.0, tendrá que usar `UIWebView`. Debido al hecho de que `UIWebView` menor optimizado para rendimiento que las alternativas, se recomienda que debe comprobar la versión de iOS del usuario. Si lo 8.0 o posterior, mediante cualquiera de las opciones se explica a continuación creará una mejor experiencia de usuario.
 
Para agregar un UIWebView a la aplicación de Xamarin.iOS, use el siguiente código:
 
```
webView = new UIWebView (View.Bounds);
View.AddSubview(webView);

var url = "https://xamarin.com"; // NOTE: https secure request
webView.LoadRequest(new NSUrlRequest(new NSUrl(url)));
```

Esto genera la vista web siguientes:

[![](uiwebview-images/webview.png "El efecto de ScalesPagesToFit")](uiwebview-images/webview.png#lightbox)

Para obtener más información sobre el uso de `UIWebView`, consulte las recetas siguientes:


- [Carga una página Web](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_a_web_page)
- [Carga el contenido Local](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_local_content)
- [Cargar documentos que no son Web](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_non-web_documents)

## <a name="wkwebview"></a>WKWebView

`WKWebView` se introdujo en iOS 8 que permite a los desarrolladores implementar una interfaz similar a la de Safari móvil de exploración web. Esto es debido, en parte, al hecho de que `WKWebView` utiliza el motor Nitro Javascript, el mismo motor usando Safari móvil. `WKWebView` siempre se debe usar over UIWebView eran posibles debido a la [mayor rendimiento](http://blog.initlabs.com/post/100113463211/wkwebview-vs-uiwebview), fácil de usar gestos y la facilidad de la interacción entre la aplicación y de la página web integrados.
  
`WKWebView` se pueden agregar a la aplicación de forma casi idéntica a UIWebView, sin embargo, ya que el desarrollador tiene un mayor control sobre la interfaz de usuario/experiencia de usuario y la funcionalidad. Crear y mostrar el objeto de vista web mostrará la página solicitada, sin embargo, puede controlar cómo se presenta la vista, cómo el usuario puede navegar y cómo el usuario sale de la vista.  

El código siguiente se puede usar para iniciar un `WKWebView` en la aplicación de Xamarin.iOS:

```csharp
    WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
    View.AddSubview(webView);

    var url = new NSUrl("https://xamarin.com");
    var request = new NSUrlRequest(url);
    webView.LoadRequest(request);
```

Esto genera la vista web siguientes:

[![](uiwebview-images/wkwebview.png "Una vista web de ejemplo sin ScalesPagesToFit")](uiwebview-images/wkwebview.png#lightbox)

Es importante tener en cuenta que `WKWebView` está en el espacio de nombres de WebKit, por lo que tendrá que agregar esto mediante la directiva a la parte superior de la clase.

`WKWebView` También puede usarse en aplicaciones de Xamarin.Mac y, por tanto, es posible que desee considerar su uso si va a crear una aplicación de Mac e iOS multiplataforma.

El [administrar alertas de JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/handle_javascript_alerts) receta también proporciona información sobre el uso de WKWebView con Javascript

<a name="safariviewcontroller" />

## <a name="sfsafariviewcontroller"></a>SFSafariViewController
 
 `SFSafariViewController` es la manera más reciente para proporcionar contenido web de la aplicación y está disponible en iOS 9 y versiones posteriores. A diferencia de `UIWebView` o `WKWebView`, `SFSafariViewController` es un controlador de vista y no se puede utilizar con otras vistas. Debe presentar `SFSafariViewController` como un nuevo controlador de vista, en la misma forma podría suponer cualquier controlador de vista.
 
 `SFSafariViewController` es esencialmente una "mini safari' que se puede incrustar en la aplicación. Al igual que WKWebView utiliza el mismo motor de Javascript Nitro, pero también proporciona una gama de características adicionales de Safari como el relleno automático, lector y la capacidad de compartir cookies y datos con Safari móvil. Interacción entre el usuario y la `SFSafariViewController` no es accesible para la aplicación. La aplicación no tendrá acceso a cualquiera de las características predeterminadas de Safari.
 
También, de forma predeterminada, implementa un **realiza** botón, lo que permite al usuario volver fácilmente a la aplicación y reenviar y realizar una copia de los botones de navegación, lo que permite el usuario puede navegar por una pila de páginas web. Además, también proporciona el usuario con una dirección de la barra lo que les proporciona la tranquilidad de que se encuentran en la página web esperado. La barra de direcciones no permite al usuario cambiar la dirección url. 

Estas implementaciones no se puede cambiar, lo `SFSafariViewController` es ideal para usar como el explorador predeterminado si la aplicación desea presentar una página Web sin ninguna personalización.

El código siguiente se puede usar para iniciar un `SFSafariViewController` en la aplicación de Xamarin.iOS:

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

Esto genera la vista web siguientes:

[![](uiwebview-images/sfsafariviewcontroller.png "Una vista web de ejemplo con SFSafariViewController")](uiwebview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

También es posible abrir la aplicación móvil de Safari desde dentro de la aplicación, utilizando el código siguiente:

```csharp
var url = new NSUrl("https://xamarin.com");

UIApplication.SharedApplication.OpenUrl(url);

```

Esto genera la vista web siguientes:

[![](uiwebview-images/safari.png "Una página web que se presenta en Safari")](uiwebview-images/safari.png#lightbox)

Navegar por los usuarios fuera de su aplicación a Safari generalmente siempre se debe evitar. La mayoría de los usuarios no esperan navegación fuera de la aplicación, por lo que si sale de la aplicación, los usuarios no pueden nunca devuelven, básicamente matar engagement.

mejoras de iOS 9 permiten al usuario volver fácilmente a su aplicación a través de un botón Atrás en el que se proporciona en la esquina superior izquierda de la página de Safari.

## <a name="app-transport-security"></a>Seguridad de transporte de la aplicación

Seguridad de transporte de la aplicación, o *ATS* introducida por Apple iOS 9 para asegurarse de que se ajustan todas las comunicaciones de internet para proteger los procedimientos recomendados de conexión.

Para obtener más información sobre ATS, incluida la forma de implementarlo en su aplicación, consulte el [App Transport Security](~/ios/app-fundamentals/ats.md) guía.

## <a name="related-links"></a>Vínculos relacionados

- [WebViews (ejemplo)](https://developer.xamarin.com/samples/monotouch/WebView/)
