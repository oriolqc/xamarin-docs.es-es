---
title: Implementación de una clase HybridWebView
description: En este artículo se muestra cómo crear a un representador personalizado para un control personalizado de clase HybridWebView, que demuestra cómo mejorar los controles específicos de la plataforma web para permitir el código de C# que se debe invocar desde JavaScript.
ms.prod: xamarin
ms.assetid: 58DFFA52-4057-49A8-8682-50A58C7E842C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/19/2018
ms.openlocfilehash: aa060bd16bc0220f6a6026106ff6c8d786daebc1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105043"
---
# <a name="implementing-a-hybridwebview"></a>Implementación de una clase HybridWebView

_Controles de interfaz de usuario personalizado de Xamarin.Forms deben derivar de la clase de vista, que se utiliza para colocar los diseños y controles en la pantalla. En este artículo se muestra cómo crear a un representador personalizado para un control personalizado de clase HybridWebView, que demuestra cómo mejorar los controles específicos de la plataforma web para permitir el código de C# que se debe invocar desde JavaScript._

Cada vista Xamarin.Forms tiene un representador que lo acompaña para cada plataforma que crea una instancia de un control nativo. Cuando un [ `View` ](xref:Xamarin.Forms.View) se representa mediante una aplicación de Xamarin.Forms en iOS, el `ViewRenderer` se crea la instancia de clase, que a su vez crea una instancia nativa `UIView` control. En la plataforma Android, el `ViewRenderer` crea una instancia de la clase un `View` control. En la plataforma Universal de Windows (UWP), el `ViewRenderer` nativo crea una instancia de clase `FrameworkElement` control. Para obtener más información acerca de las clases de control nativo que se asignan a los controles de Xamarin.Forms y el representador, consulte [clases Base del representador y los controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

El siguiente diagrama ilustra la relación entre el [ `View` ](xref:Xamarin.Forms.View) y los controles nativos correspondientes que lo implementan:

![](hybridwebview-images/view-classes.png "Relación entre la clase de vista y sus clases nativas de implementación")

El proceso de representación puede utilizarse para implementar las personalizaciones específicas de la plataforma mediante la creación de un representador personalizado para un [ `View` ](xref:Xamarin.Forms.View) en cada plataforma. El proceso para hacer esto es como sigue:

1. [Crear](#Creating_the_HybridWebView) el `HybridWebView` control personalizado.
1. [Consumir](#Consuming_the_HybridWebView) la `HybridWebView`de Xamarin.Forms.
1. [Crear](#Creating_the_Custom_Renderer_on_each_Platform) el representador personalizado para el `HybridWebView` en cada plataforma.

Cada elemento ahora se explicará a su vez para implementar un `HybridWebView` representador que mejora los controles específicos de la plataforma web para permitir el código de C# que se debe invocar desde JavaScript. El `HybridWebView` instancia se utilizará para mostrar una página HTML que pide al usuario que escriba su nombre. A continuación, cuando el usuario hace clic en un botón HTML, se invocará una función JavaScript que C# `Action` que muestra una ventana emergente que contiene el nombre de los usuarios.

Para obtener más información sobre el proceso para invocar C# desde JavaScript, consulte [invocar C# desde JavaScript](#Invoking_C_from_JavaScript). Para obtener más información acerca de la página HTML, vea [creación de la página Web](#Creating_the_Web_Page).

<a name="Creating_the_HybridWebView" />

## <a name="creating-the-hybridwebview"></a>Creación de la clase HybridWebView

El `HybridWebView` control personalizado se puede crear mediante la creación de subclases del [ `View` ](xref:Xamarin.Forms.View) clase, como se muestra en el ejemplo de código siguiente:

```csharp
public class HybridWebView : View
{
  Action<string> action;
  public static readonly BindableProperty UriProperty = BindableProperty.Create (
    propertyName: "Uri",
    returnType: typeof(string),
    declaringType: typeof(HybridWebView),
    defaultValue: default(string));

  public string Uri {
    get { return (string)GetValue (UriProperty); }
    set { SetValue (UriProperty, value); }
  }

  public void RegisterAction (Action<string> callback)
  {
    action = callback;
  }

  public void Cleanup ()
  {
    action = null;
  }

  public void InvokeAction (string data)
  {
    if (action == null || data == null) {
      return;
    }
    action.Invoke (data);
  }
}
```

El `HybridWebView` control personalizado se crea en el proyecto de biblioteca estándar de .NET y define la API para el control siguiente:

- Un `Uri` propiedad que especifica la dirección de la página web que se cargue.
- Un `RegisterAction` método que se registra un `Action` con el control. Se invocará la acción registrada de JavaScript contenido en el archivo HTML al que hace referencia a través de la `Uri` propiedad.
- Un `CleanUp` método que quita la referencia al registrado `Action`.
- Un `InvokeAction` método que invoca registrado `Action`. Este método se llamará desde un representador personalizado en cada proyecto específico de plataforma.

<a name="Consuming_the_HybridWebView" />

## <a name="consuming-the-hybridwebview"></a>Consumo de la clase HybridWebView

El `HybridWebView` control personalizado puede hacer referencia en XAML en el proyecto de biblioteca estándar de .NET declarando un espacio de nombres para su ubicación y utilizando el prefijo de espacio de nombres en el control personalizado. El siguiente ejemplo de código muestra cómo el `HybridWebView` control personalizado puede utilizarse en una página XAML:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
             x:Class="CustomRenderer.HybridWebViewPage"
             Padding="0,20,0,0">
    <ContentPage.Content>
        <local:HybridWebView x:Name="hybridWebView" Uri="index.html"
          HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand" />
    </ContentPage.Content>
</ContentPage>
```

El `local` prefijo de espacio de nombres puede tener cualquier nombre. Sin embargo, el `clr-namespace` y `assembly` valores deben coincidir con los detalles del control personalizado. Una vez que se declara el espacio de nombres, el prefijo se utiliza para hacer referencia al control personalizado.

El siguiente ejemplo de código muestra cómo el `HybridWebView` control personalizado puede utilizarse en una página de C#:

```csharp
public class HybridWebViewPageCS : ContentPage
{
  public HybridWebViewPageCS ()
  {
    var hybridWebView = new HybridWebView {
      Uri = "index.html",
      HorizontalOptions = LayoutOptions.FillAndExpand,
      VerticalOptions = LayoutOptions.FillAndExpand
    };
    ...
    Padding = new Thickness (0, 20, 0, 0);
    Content = hybridWebView;
  }
}
```

El `HybridWebView` instancia se utilizará para mostrar un control web nativos en cada plataforma. Tiene `Uri` propiedad está establecida en un archivo HTML que se almacena en cada proyecto específico de plataforma y que se mostrará el control web nativos. El HTML representado pide al usuario que escriba su nombre, con una función de JavaScript invoca un C# `Action` en respuesta a un clic de botón HTML.

El `HybridWebViewPage` registra la acción que se debe invocar desde JavaScript, tal como se muestra en el ejemplo de código siguiente:

```csharp
public partial class HybridWebViewPage : ContentPage
{
  public HybridWebViewPage ()
  {
    ...
    hybridWebView.RegisterAction (data => DisplayAlert ("Alert", "Hello " + data, "OK"));
  }
}
```

Esta acción llama a la [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) método para mostrar un elemento emergente que muestra el nombre especificado en la página HTML que muestra el `HybridWebView` instancia.

Ahora se puede agregar a un representador personalizado a cada proyecto de aplicación para mejorar los controles específicos de la plataforma web al permitir que el código de C# que se debe invocar desde JavaScript.

<a nane="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Crear al representador personalizado en cada plataforma

El proceso de creación de la clase de representador personalizado es como sigue:

1. Crear una subclase de la `ViewRenderer<T1,T2>` clase que representa el control personalizado. El primer argumento de tipo debe ser el control personalizado, el representador de es, en este caso `HybridWebView`. El segundo argumento de tipo debe ser el control nativo que implementará la vista personalizada.
1. Invalidar el `OnElementChanged` método que representa la lógica personalizada de control y escribir para personalizarla. Este método se llama cuando se crea el correspondiente control personalizado de Xamarin.Forms.
1. Agregar un `ExportRenderer` a la clase de representador personalizado para especificar que se utilizará para representar el control personalizado de Xamarin.Forms. Este atributo se usa para registrar al representador personalizado con Xamarin.Forms.

> [!NOTE]
> Para la mayoría de los elementos de Xamarin.Forms, es opcional proporcionar a un representador personalizado en cada proyecto de la plataforma. Si no está registrado un representador personalizado, se usará el representador predeterminado de la clase base del control. Sin embargo, los representadores personalizados son necesarios en cada proyecto de la plataforma al representar un [vista](xref:Xamarin.Forms.View) elemento.

El siguiente diagrama ilustra las responsabilidades de cada proyecto de la aplicación de ejemplo, junto con las relaciones entre ellos:

![](hybridwebview-images/solution-structure.png "Responsabilidades del proyecto de representador personalizado de la clase HybridWebView")

El `HybridWebView` control personalizado se representa mediante las clases de representador específica de la plataforma, que se derivan de la `ViewRenderer` clase para cada plataforma. Esto da como resultado de cada `HybridWebView` control personalizado que se representa con controles específicos de la plataforma web, como se muestra en las capturas de pantalla siguiente:

![](hybridwebview-images/screenshots.png "Clase HybridWebView en cada plataforma")

El `ViewRenderer` clase expone la `OnElementChanged` método, que se llama cuando se crea el control personalizado de Xamarin.Forms para representar el control nativo web correspondiente. Este método toma un `ElementChangedEventArgs` parámetro que contiene `OldElement` y `NewElement` propiedades. Estas propiedades representan el elemento de Xamarin.Forms que el representador *era* adjunto y el elemento de Xamarin.Forms que el representador *es* adjunta, respectivamente. En la aplicación de ejemplo la `OldElement` propiedad será `null` y `NewElement` propiedad contendrá una referencia a la `HybridWebView` instancia.

Una versión reemplazada de la `OnElementChanged` método en cada clase de presentador específica de la plataforma, es el lugar para realizar la personalización y creación de instancias de control web nativos. El `SetNativeControl` método se debe usar para crear instancias del control web nativos, y este método también asignará la referencia de control para el `Control` propiedad. Además, se puede obtener una referencia al control de Xamarin.Forms que se representa mediante el `Element` propiedad.

En algunas circunstancias el `OnElementChanged` método puede llamarse varias veces. Por lo tanto, para evitar pérdidas de memoria, debe tener cuidado al crear una instancia de un nuevo control nativo. El enfoque que usar al crear instancias de un nuevo control nativo en un presentador personalizado se muestra en el ejemplo de código siguiente:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (Control == null) {
    // Instantiate the native control and assign it to the Control property with
    // the SetNativeControl method
  }

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    // Configure the control and subscribe to event handlers
  }
}
```

Solo se debe crear una instancia de un nuevo control nativo una vez, cuando la propiedad `Control` es `null`. Solo se debe configurar el control y suscribir los controladores de eventos cuando se adjunta el presentador personalizado a un nuevo elemento de Xamarin.Forms. De forma similar, solo se debe cancelar la suscripción de los controladores de eventos que se han suscrito cuando cambia el elemento al que está asociado el presentador. Adoptar este enfoque le ayudará a crear a un representador personalizado de alto rendimiento que no sufra pérdidas de memoria.

Cada clase de presentador personalizado se decora con un `ExportRenderer` atributo que se registra el representador con Xamarin.Forms. El atributo toma dos parámetros: el nombre de tipo del control personalizado de Xamarin.Forms que se procesa y el nombre de tipo del representador personalizado. El `assembly` prefijo para el atributo especifica que el atributo se aplica a todo el ensamblado.

Las secciones siguientes describen la estructura de la página web que se cargan con cada control nativo web, el proceso para invocar C# desde JavaScript y su implementación en cada clase de representador personalizado específico de la plataforma.

<a name="Creating_the_Web_Page" />

### <a name="creating-the-web-page"></a>Creación de la página Web

El ejemplo de código siguiente muestra la página web que será mostrado por el `HybridWebView` control personalizado:

```html
<html>
<body>
<script src="http://code.jquery.com/jquery-1.11.0.min.js"></script>
<h1>HybridWebView Test</h1>
<br/>
Enter name: <input type="text" id="name">
<br/>
<br/>
<button type="button" onclick="javascript:invokeCSCode($('#name').val());">Invoke C# Code</button>
<br/>
<p id="result">Result:</p>
<script type="text/javascript">
function log(str)
{
    $('#result').text($('#result').text() + " " + str);
}

function invokeCSCode(data) {
    try {
        log("Sending Data:" + data);
        invokeCSharpAction(data);
    }
    catch (err){
          log(err);
    }
}
</script>
</body>
</html>
```

Permite que un usuario que escriba su nombre en la página web un `input` elemento y proporciona un `button` elemento que va a invocar código de C# al hacer clic en. El proceso para lograr esto es como sigue:

- Cuando el usuario hace clic en el `button` elemento, el `invokeCSCode` se denomina función de JavaScript, con el valor de la `input` elemento que se pasan a la función.
- El `invokeCSCode` llamadas de función el `log` función para mostrar los datos que está enviando a la de C# `Action`. A continuación, llama el `invokeCSharpAction` método para invocar el C# `Action`, pasando el parámetro recibido desde el `input` elemento.

El `invokeCSharpAction` función de JavaScript no está definido en la página web y se insertará en él por cada representador personalizado.

<a name="Invoking_C_from_JavaScript" />

### <a name="invoking-c-from-javascript"></a>Invocación de C# desde JavaScript

El proceso para invocar C# desde JavaScript es idéntico en cada plataforma:

- El representador personalizado crea un control nativo web y carga el archivo HTML especificado por el `HybridWebView.Uri` propiedad.
- Una vez que se carga la página web, el representador personalizado inserta el `invokeCSharpAction` función de JavaScript en la página web.
- Cuando el usuario escribe su nombre y hace clic en el código HTML `button` elemento, el `invokeCSCode` función se invoca, que a su vez invoca el `invokeCSharpAction` función.
- El `invokeCSharpAction` función invoca un método en el representador personalizado, que a su vez invoca el `HybridWebView.InvokeAction` método.
- El `HybridWebView.InvokeAction` método invoca registrado `Action`.

Las secciones siguientes describen cómo se implementa este proceso en cada plataforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Crear al representador personalizado en iOS

El ejemplo de código siguiente muestra al representador personalizado para la plataforma de iOS:

```csharp
[assembly: ExportRenderer (typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.iOS
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, WKWebView>, IWKScriptMessageHandler
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.webkit.messageHandlers.invokeAction.postMessage(data);}";
        WKUserContentController userController;

        protected override void OnElementChanged (ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged (e);

            if (Control == null) {
                userController = new WKUserContentController ();
                var script = new WKUserScript (new NSString (JavaScriptFunction), WKUserScriptInjectionTime.AtDocumentEnd, false);
                userController.AddUserScript (script);
                userController.AddScriptMessageHandler (this, "invokeAction");

                var config = new WKWebViewConfiguration { UserContentController = userController };
                var webView = new WKWebView (Frame, config);
                SetNativeControl (webView);
            }
            if (e.OldElement != null) {
                userController.RemoveAllUserScripts ();
                userController.RemoveScriptMessageHandler ("invokeAction");
                var hybridWebView = e.OldElement as HybridWebView;
                hybridWebView.Cleanup ();
            }
            if (e.NewElement != null) {
                string fileName = Path.Combine (NSBundle.MainBundle.BundlePath, string.Format ("Content/{0}", Element.Uri));
                Control.LoadRequest (new NSUrlRequest (new NSUrl (fileName, false)));
            }
        }

        public void DidReceiveScriptMessage (WKUserContentController userContentController, WKScriptMessage message)
        {
            Element.InvokeAction (message.Body.ToString ());
        }
    }
}
```

El `HybridWebViewRenderer` clase carga la página web especificada en el `HybridWebView.Uri` propiedad nativo [ `WKWebView` ](https://developer.xamarin.com/api/type/WebKit.WKWebView/) control y el `invokeCSharpAction` función de JavaScript se inserta en la página web. Una vez que el usuario escribe su nombre y hace clic en el código HTML `button` elemento, el `invokeCSharpAction` se ejecuta la función de JavaScript, con el `DidReceiveScriptMessage` método que se llama después de que se recibe un mensaje desde la página web. A su vez, este método invoca el `HybridWebView.InvokeAction` método, que se invocará la acción registrada para mostrar la ventana emergente.

Esta funcionalidad se consigue del siguiente modo:

- Siempre que el `Control` propiedad es `null`, se efectúan las siguientes operaciones:
  - Un [ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/) instancia se crea, lo que permite la publicación de mensajes y de inyección de scripts de usuario en una página web.
  - Un [ `WKUserScript` ](https://developer.xamarin.com/api/type/WebKit.WKUserScript/) se crea una instancia para inyectar el `invokeCSharpAction` función de JavaScript en la página web después de cargar la página web.
  - El [ `WKUserContentController.AddScript` ](https://developer.xamarin.com/api/member/WebKit.WKUserContentController.AddUserScript/p/WebKit.WKUserScript/) método agrega el [ `WKUserScript` ](https://developer.xamarin.com/api/type/WebKit.WKUserScript/) instancia al controlador de contenido.
  - El [ `WKUserContentController.AddScriptMessageHandler` ](https://developer.xamarin.com/api/member/WebKit.WKUserContentController.AddScriptMessageHandler/p/WebKit.IWKScriptMessageHandler/System.String/) método agrega un controlador de mensajes de secuencia de comandos denominado `invokeAction` a la [ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/) instancia, lo que hará que la función de JavaScript `window.webkit.messageHandlers.invokeAction.postMessage(data)` definirse en todos marcos en todas las vistas web que usarán el `WKUserContentController` instancia.
  - Un [ `WKWebViewConfiguration` ](https://developer.xamarin.com/api/type/WebKit.WKWebViewConfiguration/) se crea la instancia, con el [ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/) instancia que se va a establecer como el controlador de contenido.
  - Un [ `WKWebView` ](https://developer.xamarin.com/api/type/WebKit.WKWebView/) se crea una instancia de control y el `SetNativeControl` método se llama para asignar una referencia a la `WKWebView` el control a la `Control` propiedad.
- Siempre que el representador personalizado se adjunta a un nuevo elemento de Xamarin.Forms:
  - El [ `WKWebView.LoadRequest` ](https://developer.xamarin.com/api/member/WebKit.WKWebView.LoadRequest/p/Foundation.NSUrlRequest/) método carga el archivo HTML especificado por el `HybridWebView.Uri` propiedad. El código especifica que el archivo se almacena en el `Content` carpeta del proyecto. Una vez que se muestra la página web, el `invokeCSharpAction` función de JavaScript se insertará en la página web.
- Cuando el representador de elemento se asocia a los cambios:
  - Libera los recursos.

> [!NOTE]
> La `WKWebView` clase solo se admite en iOS 8 y versiones posteriores.

### <a name="creating-the-custom-renderer-on-android"></a>Crear al representador personalizado en Android

En el ejemplo de código siguiente se muestra al representador personalizado para la plataforma Android:

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.Droid
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, Android.Webkit.WebView>
    {
        const string JavascriptFunction = "function invokeCSharpAction(data){jsBridge.invokeAction(data);}";
        Context _context;

        public HybridWebViewRenderer(Context context) : base(context)
        {
            _context = context;
        }

        protected override void OnElementChanged(ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged(e);

            if (Control == null)
            {
                var webView = new Android.Webkit.WebView(_context);
                webView.Settings.JavaScriptEnabled = true;
                webView.SetWebViewClient(new JavascriptWebViewClient($"javascript: {JavascriptFunction}"));
                SetNativeControl(webView);
            }
            if (e.OldElement != null)
            {
                Control.RemoveJavascriptInterface("jsBridge");
                var hybridWebView = e.OldElement as HybridWebView;
                hybridWebView.Cleanup();
            }
            if (e.NewElement != null)
            {
                Control.AddJavascriptInterface(new JSBridge(this), "jsBridge");
                Control.LoadUrl($"file:///android_asset/Content/{Element.Uri}");
            }
        }
    }
}
```

El `HybridWebViewRenderer` clase carga la página web especificada en el `HybridWebView.Uri` propiedad nativo [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) control y el `invokeCSharpAction` función de JavaScript se inserta en la página web, una vez que tenga la página web terminado de cargarse, con el `OnPageFinished` invalidar en el `JavascriptWebViewClient` clase:

```csharp
public class JavascriptWebViewClient : WebViewClient
{
    string _javascript;

    public JavascriptWebViewClient(string javascript)
    {
        _javascript = javascript;
    }

    public override void OnPageFinished(WebView view, string url)
    {
        base.OnPageFinished(view, url);
        view.EvaluateJavascript(_javascript, null);
    }
}
```

Una vez que el usuario escribe su nombre y hace clic en el código HTML `button` elemento, el `invokeCSharpAction` se ejecuta la función de JavaScript. Esta funcionalidad se consigue del siguiente modo:

- Siempre que el `Control` propiedad es `null`, se efectúan las siguientes operaciones:
  - Nativo [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) se crea la instancia, JavaScript está habilitado en el control y un `JavascriptWebViewClient` instancia se establece como la implementación de `WebViewClient`.
  - El `SetNativeControl` método se llama para asignar una referencia a nativo [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) el control a la `Control` propiedad.
- Siempre que el representador personalizado se adjunta a un nuevo elemento de Xamarin.Forms:
  - El [ `WebView.AddJavascriptInterface` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.AddJavascriptInterface/p/Java.Lang.Object/System.String/) inserta un nuevo método `JSBridge` instancia dentro del marco principal de contexto de JavaScript del objeto WebView, asígnele el nombre `jsBridge`. Esto permite que los métodos en el `JSBridge` clase para tener acceso desde JavaScript.
  - El [ `WebView.LoadUrl` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.LoadUrl/p/System.String/) método carga el archivo HTML especificado por el `HybridWebView.Uri` propiedad. El código especifica que el archivo se almacena en el `Content` carpeta del proyecto.
  - En el `JavascriptWebViewClient` (clase), el `invokeCSharpAction` función de JavaScript se inserta en la página web una vez que la página ha terminado de cargarse.
- Cuando el representador de elemento se asocia a los cambios:
  - Libera los recursos.

Cuando el `invokeCSharpAction` se ejecuta la función de JavaScript, a su vez invoca el `JSBridge.InvokeAction` método, que se muestra en el ejemplo de código siguiente:

```csharp
public class JSBridge : Java.Lang.Object
{
  readonly WeakReference<HybridWebViewRenderer> hybridWebViewRenderer;

  public JSBridge (HybridWebViewRenderer hybridRenderer)
  {
    hybridWebViewRenderer = new WeakReference <HybridWebViewRenderer> (hybridRenderer);
  }

  [JavascriptInterface]
  [Export ("invokeAction")]
  public void InvokeAction (string data)
  {
    HybridWebViewRenderer hybridRenderer;

    if (hybridWebViewRenderer != null && hybridWebViewRenderer.TryGetTarget (out hybridRenderer))
    {
      hybridRenderer.Element.InvokeAction (data);
    }
  }
}
```

La clase debe derivar de `Java.Lang.Object`, y los métodos que se exponen a JavaScript deben ser representativos con el `[JavascriptInterface]` y `[Export]` atributos. Por lo tanto, cuando el `invokeCSharpAction` función de JavaScript se inserta en la página web y se ejecuta, llamará el `JSBridge.InvokeAction` método debido a que se está decorada con el `[JavascriptInterface]` y `[Export("invokeAction")]` atributos. A su vez, el `InvokeAction` método invoca el `HybridWebView.InvokeAction` método, lo cual invoca la acción registrada para mostrar la ventana emergente.

> [!NOTE]
> Los proyectos que utilizan el `[Export]` atributo debe incluir una referencia a `Mono.Android.Export`, o se producirá un error del compilador.

Tenga en cuenta que el `JSBridge` clase mantiene una `WeakReference` a la `HybridWebViewRenderer` clase. Esto es para evitar la creación de una referencia circular entre las dos clases. Para obtener más información, consulte [referencias débiles](https://msdn.microsoft.com/library/ms404247(v=vs.110).aspx) en MSDN.

### <a name="creating-the-custom-renderer-on-uwp"></a>Creando al representador personalizado en UWP

En el ejemplo de código siguiente se muestra al representador personalizado para UWP:

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.UWP
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, Windows.UI.Xaml.Controls.WebView>
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.external.notify(data);}";

        protected override void OnElementChanged(ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged(e);

            if (Control == null)
            {
                SetNativeControl(new Windows.UI.Xaml.Controls.WebView());
            }
            if (e.OldElement != null)
            {
                Control.NavigationCompleted -= OnWebViewNavigationCompleted;
                Control.ScriptNotify -= OnWebViewScriptNotify;
            }
            if (e.NewElement != null)
            {
                Control.NavigationCompleted += OnWebViewNavigationCompleted;
                Control.ScriptNotify += OnWebViewScriptNotify;
                Control.Source = new Uri(string.Format("ms-appx-web:///Content//{0}", Element.Uri));
            }
        }

        async void OnWebViewNavigationCompleted(WebView sender, WebViewNavigationCompletedEventArgs args)
        {
            if (args.IsSuccess)
            {
                // Inject JS script
                await Control.InvokeScriptAsync("eval", new[] { JavaScriptFunction });
            }
        }

        void OnWebViewScriptNotify(object sender, NotifyEventArgs e)
        {
            Element.InvokeAction(e.Value);
        }
    }
}
```

El `HybridWebViewRenderer` clase carga la página web especificada en el `HybridWebView.Uri` propiedad nativo `WebView` control y el `invokeCSharpAction` función de JavaScript se inserta en la página web, una vez haya cargado la página web, con el `WebView.InvokeScriptAsync` método. Una vez que el usuario escribe su nombre y hace clic en el código HTML `button` elemento, el `invokeCSharpAction` se ejecuta la función de JavaScript, con el `OnWebViewScriptNotify` método que se llama después de que se recibe una notificación en la página web. A su vez, este método invoca el `HybridWebView.InvokeAction` método, que se invocará la acción registrada para mostrar la ventana emergente.

Esta funcionalidad se consigue del siguiente modo:

- Siempre que el `Control` propiedad es `null`, se efectúan las siguientes operaciones:
  - El `SetNativeControl` método se llama para crear una instancia nueva nativo `WebView` controlar y asignar una referencia a ella para el `Control` propiedad.
- Siempre que el representador personalizado se adjunta a un nuevo elemento de Xamarin.Forms:
  - Controladores de eventos para el `NavigationCompleted` y `ScriptNotify` se registran los eventos. El `NavigationCompleted` evento desencadena cuando ya sea nativo `WebView` control ha terminado de cargar el contenido actual o si no ha podido navegación. El `ScriptNotify` evento desencadena cuando el contenido en nativo `WebView` control utiliza JavaScript para pasar una cadena a la aplicación. Se activa el página web la `ScriptNotify` eventos mediante una llamada a `window.external.notify` al pasar un `string` parámetro.
  - El `WebView.Source` propiedad está establecida en el URI del archivo HTML especificado por el `HybridWebView.Uri` propiedad. El código se da por supuesto que el archivo se almacena en el `Content` carpeta del proyecto. Una vez que se muestra la página web, el `NavigationCompleted` evento se activará y `OnWebViewNavigationCompleted` se invocará el método. El `invokeCSharpAction` función de JavaScript, a continuación, se insertará en la página web con el `WebView.InvokeScriptAsync` método, siempre que el panel de navegación se completó correctamente.
- Cuando el representador de elemento se asocia a los cambios:
  - Los eventos son cancelar la suscripción.

## <a name="summary"></a>Resumen

Este artículo demuestra cómo crear un representador personalizado para un `HybridWebView` control personalizado, que muestra cómo mejorar los controles específicos de la plataforma web para permitir el código de C# que se debe invocar desde JavaScript.


## <a name="related-links"></a>Vínculos relacionados

- [CustomRendererHybridWebView (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/hybridwebview/)
- [Llamar a C# desde JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript)
