---
title: Vista Web
ms.topic: article
ms.prod: xamarin
ms.assetid: 807F214A-166D-B342-0BBA-525517577F6B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 234bd79754ae7f328d3207757156089441fc588c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="web-view"></a>Vista Web

[`WebView`](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) puede crear su propia ventana para ver las páginas web (o incluso desarrollar un explorador completo). En este tutorial, creará un sencillo [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/) que puede ver y explorar páginas web.

Crear un nuevo proyecto denominado **HelloWebView**.

Abra **Resources/Layout/Main.axml** e inserte lo siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<WebView  xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/webview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" />
```

Dado que esta aplicación tendrá acceso a Internet, debe agregar que los permisos adecuados para el Android el archivo de manifiesto. Abra las propiedades del proyecto para especificar los permisos que requiere la aplicación para que funcione. Habilitar la `INTERNET` permiso tal y como se muestra a continuación:

![Establecer el permiso de INTERNET en el manifiesto de Android](web-view-images/01-set-internet-permissions.png)

Ahora, abra **MainActivity.cs** y agregue un mediante la directiva de Webkit:

```csharp
using Android.Webkit;
```

En la parte superior de la `MainActivity` clase, declare un [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) objeto:

```csharp
WebView web_view;
```

Cuando el **WebView** es más frecuentes para cargar una dirección URL, de forma predeterminada delegará la solicitud para el explorador predeterminado. Para tener la **WebView** carga la dirección URL (en lugar del explorador predeterminado), deben crear una subclase `Android.Webkit.WebViewClient` e invalide el `ShouldOverriderUrlLoading` método. Una instancia de esta personalización `WebViewClient` se proporciona a los `WebView`. Para ello, agregue anidada de los siguientes `HelloWebViewClient` dentro de la clase `MainActivity`:

```csharp
public class HelloWebViewClient : WebViewClient
{
    public override bool ShouldOverrideUrlLoading (WebView view, string url)
    {
        view.LoadUrl(url);
        return false;
    }
}
```

Cuando `ShouldOverrideUrlLoading` devuelve `false`, indica al Android que actual `WebView` instancia procesó la solicitud y que no es necesaria realizar ninguna otra acción. 

Si se dirige a nivel de API 24 o una versión posterior, utilice la sobrecarga de `ShouldOverrideUrlLoading` que toma un `IWebResourceRequest` para el segundo argumento en lugar de un `string`:

```csharp
public class HelloWebViewClient : WebViewClient
{
    // For API level 24 and later
    public override bool ShouldOverrideUrlLoading (WebView view, IWebResourceRequest request)
    {
        view.LoadUrl(request.Url.ToString());
        return false;
    }
}
```

A continuación, use el siguiente código para el [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle)) método:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "main" layout resource
    SetContentView (Resource.Layout.Main);

    web_view = FindViewById<WebView> (Resource.Id.webview);
    web_view.Settings.JavaScriptEnabled = true;
    web_view.SetWebViewClient(new HelloWebViewClient());
    web_view.LoadUrl ("https://www.xamarin.com/university");
}
```

Esto inicializa el miembro [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) con el de la [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/) diseño y habilita JavaScript para la [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) con [ `JavaScriptEnabled` ](https://developer.xamarin.com/api/property/Android.Webkit.WebSettings.JavaScriptEnabled/) 
 `= true` (consulte la [llamar a C\# desde JavaScript](https://developer.xamarin.com/recipes/android/controls/webview/call_csharp_from_javascript) receta para obtener información acerca de cómo llamada C\# funciones de JavaScript). Por último, se carga una página web inicial con [ `LoadUrl(String)` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/%2fM%2fLoadUrl).

Compile y ejecute la aplicación. Debería ver una aplicación de Visor de página web sencilla como lo ve en la siguiente captura de pantalla:

[![Ejemplo de aplicación mostrar una vista Web](web-view-images/02-simple-webview-app-sml.png)](web-view-images/02-simple-webview-app.png)

Para controlar la **volver** botón pulsación de tecla, agregue la siguiente instrucción using:

```csharp
using Android.Views;
```

A continuación, agregue el siguiente método dentro de la `HelloWebView` actividad:

```csharp
public override bool OnKeyDown (Android.Views.Keycode keyCode, Android.Views.KeyEvent e)
{
    if (keyCode == Keycode.Back && web_view.CanGoBack ())
    {
        web_view.GoBack ();
        return true;
    }
    return base.OnKeyDown (keyCode, e);
}
```

Esto [ `OnKeyDown(int, KeyEvent)` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnKeyDown/(Android.Views.Keycode%2cAndroid.Views.KeyEvent)) método de devolución de llamada que se llamará cuando se presiona un botón mientras se ejecuta la actividad. La condición dentro de usa el [ `KeyEvent` ](https://developer.xamarin.com/api/type/Android.Views.KeyEvent/) comprobar si se presionó la tecla es el **Atrás** botón y si el [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) es realmente capaz de navegar por (si tiene un historial). Si se cumplen, la [ `GoBack()` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.GoBack/) momento en que se llama al método, que le remitirá copia de un paso en el [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) historial. Devolver `true` indica que se ha controlado el evento. Si no se cumple esta condición, el evento se envía al sistema.

Vuelva a ejecutar la aplicación. Ahora podrá seguir los vínculos y navegar hacia atrás en el historial de la página:

[![Capturas de pantalla de ejemplo del botón Atrás en acción](web-view-images/03-back-button-sml.png)](web-view-images/03-back-button.png)


*Algunas partes de esta página son las modificaciones que se basa en el trabajo creado y comparten la Android Open Source Project y usarse de acuerdo con los términos que se describe en el*
[*licencia de atribución 2.5 de Creative Commons* ](http://creativecommons.org/licenses/by/2.5/).


## <a name="related-links"></a>Vínculos relacionados

- [Llamada de C# desde JavaScript](https://developer.xamarin.com/recipes/android/controls/webview/call_csharp_from_javascript)
- [Android.Webkit.WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView)
- [KeyEvent](https://developer.xamarin.com/api/type/Android.Webkit.WebView/Client)
