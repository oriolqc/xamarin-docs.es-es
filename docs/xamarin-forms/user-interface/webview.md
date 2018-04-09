---
title: WebView
description: Presentar contenido web de red y locales o documentos.
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: a96c57b66e5debbbb7318c22e33a21eb9b998395
ms.sourcegitcommit: 271d3f7ea4abfcf87734d2c747a68cb8114d743c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/08/2018
---
# <a name="webview"></a>WebView

[WebView](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) es una vista para mostrar web y el código HTML contenida en la aplicación. A diferencia de `OpenUri`, que lleva al usuario en el explorador web en el dispositivo, `WebView` muestra el contenido HTML dentro de la aplicación.

Esta guía se compone de las siguientes secciones:

- **[Contenido](#Content)**  &ndash; WebView admite diversos orígenes de contenido, incluido HTML incrustado, páginas web y las cadenas HTML.
- **[Navegación](#Navigation)**  &ndash; WebView incluye compatibilidad para navegar a una página determinada y volviendo.
- **[Eventos](#Events)**  &ndash; escuchar y responder a las acciones realizadas por el usuario en la vista Web.
- **[Rendimiento](#Performance)**  &ndash; más información acerca de las características de rendimiento de WebView en cada plataforma.
- **[Permisos](#Permissions)**  &ndash; obtener información sobre cómo establecer permisos para que funcione WebView en la aplicación.
- **[Diseño](#Layout)**  &ndash; WebView tiene algunos requisitos muy especiales para la que se muestre. Obtenga información acerca de cómo asegurarse de que WebView muestra correctamente:

![](webview-images/in-app-browser.png "En el Explorador de la aplicación")

## <a name="content"></a>Contenido

WebView incluye soporte técnico para los siguientes tipos de contenido:

- Sitios Web HTML y CSS &ndash; WebView es totalmente compatible con sitios Web que se escriben con HTML y CSS, incluida la compatibilidad con JavaScript.
- Documentos &ndash; dado WebView se implementa mediante componentes nativos en cada plataforma, WebView es capaz de mostrar los documentos que están visibles en cada plataforma. Esto significa que los archivos PDF funcionan en iOS y Android, pero no de Windows Phone.
- Las cadenas HTML &ndash; WebView puede mostrar las cadenas HTML de la memoria.
- Archivos locales &ndash; WebView puede presentar cualquiera de los tipos de contenido anteriores incrustados en la aplicación.

> [!NOTE]
> `WebView` en Windows y Windows Phone no admiten Silverlight, Flash o todos los controles ActiveX, incluso si son compatibles con Internet Explorer en esa plataforma.

### <a name="websites"></a>Sitios web

Para mostrar un sitio Web de internet, establezca el `WebView`del [ `Source` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebViewSource/) propiedad a una dirección URL de cadena:

```csharp
var browser = new WebView {
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> Las direcciones URL se deben formar totalmente con el protocolo especificado (es decir, debe tener "http://" o "https://" antepone a él).

#### <a name="ios-and-ats"></a>iOS y ATS

Desde la versión 9, iOS solo permitirá que la aplicación para comunicarse con servidores que implementan la seguridad de los procedimientos recomendados de forma predeterminada. Valores se deben establecer `Info.plist` para habilitar la comunicación con servidores inseguras.

> [!NOTE]
> Si la aplicación requiere una conexión a un sitio Web inseguro, siempre debe escribir el dominio como una excepción mediante `NSExceptionDomains` en lugar de desactivar que ATS totalmente mediante `NSAllowsArbitraryLoads`. `NSAllowsArbitraryLoads` sólo debe utilizarse en situaciones de emergencias extremas.

A continuación muestra cómo habilitar un dominio específico (en este caso en xamarin.com) para omitir los requisitos de ATS:

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSExceptionDomains</key>
        <dict>
            <key>xamarin.com</key>
            <dict>
                <key>NSIncludesSubdomains</key>
                <true/>
                <key>NSTemporaryExceptionAllowsInsecureHTTPLoads</key>
                <true/>
                <key>NSTemporaryExceptionMinimumTLSVersion</key>
                <string>TLSv1.1</string>
            </dict>
        </dict>
    </dict>
```

Es un procedimiento recomendado para habilitar sólo algunos dominios omitir ATS, lo que le permite utilizar los sitios de confianza mientras se beneficien de la seguridad adicional en dominios no confiables. A continuación muestra el método menos seguro de deshabilitar ATS para la aplicación:

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSAllowsArbitraryLoads </key>
        <true/>
    </dict>
```

Vea [seguridad de transporte de la aplicación](~/ios/app-fundamentals/ats.md) para obtener más información acerca de esta nueva característica de iOS 9.

### <a name="html-strings"></a>Cadenas HTML

Si desea presentar una cadena HTML definido dinámicamente en el código, debe crear una instancia de [ `HtmlWebViewSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.HtmlWebViewSource/):

```csharp
var browser = new WebView();
var htmlSource = new HtmlWebViewSource();
htmlSource.Html = @"<html><body>
  <h1>Xamarin.Forms</h1>
  <p>Welcome to WebView.</p>
  </body></html>";
browser.Source = htmlSource;
```

![](webview-images/html-string.png "Cadena HTML que mostrar WebView")

En el código anterior, `@` se utiliza para marcar el código HTML como una cadena literal, lo que significa que se omiten todos los caracteres de escape habituales.

### <a name="local-html-content"></a>Contenido HTML local

WebView puede mostrar el contenido de HTML, CSS y Javascript incrustado dentro de la aplicación. Por ejemplo:

```html
<html>
  <head>
    <title>Xamarin Forms</title>
  </head>
  <body>
    <h1>Xamrin.Forms</h1>
    <p>This is an iOS web page.</p>
    <img src="XamarinLogo.png" />
  </body>
</html>
```

CSS:

```css
html,body {
  margin:0;
  padding:10;
}
body,p,h1 {
  font-family: Chalkduster;
}
```

Tenga en cuenta que las fuentes especificadas en el código CSS anterior debe personalizarse para cada plataforma, ya que no todas las plataformas tiene las mismas fuentes.

Para mostrar local contenido con un `WebView`, deberá abrir el archivo HTML como cualquier otro, a continuación, cargar el contenido como una cadena en el `Html` propiedad de un `HtmlWebViewSource`. Para obtener más información sobre los archivos de apertura, consulte [trabajar con archivos](~/xamarin-forms/app-fundamentals/files.md).

Las capturas de pantalla siguientes muestran el resultado de mostrar el contenido local en cada plataforma:

![](webview-images/local-content.png "Mostrar WebView el contenido Local")

Aunque se ha cargado la primera página, la `WebView` no tiene ningún conocimiento de dónde procede el código HTML. Que es un problema cuando se trabaja con páginas que hacen referencia a recursos locales. Ejemplos de cuándo podría deberse a se incluyen al vínculo local páginas unos de otros, una página hace uso de un archivo JavaScript independiente o una página que se vincula a una hoja de estilos CSS.  

Para resolver este problema, debe indicar el `WebView` dónde encontrar archivos en el sistema de archivos. Hacerlo estableciendo la `BaseUrl` propiedad en el `HtmlWebViewSource` utilizado por el `WebView`.

Puesto que el sistema de archivos en cada uno de los sistemas operativos es diferente, deberá determinar esa dirección URL en cada plataforma. Xamarin.Forms expone la `DependencyService` para resolver las dependencias en tiempo de ejecución en cada plataforma.

Para usar el `DependencyService`, primero defina una interfaz que se puede implementar en cada plataforma:

```csharp
public interface IBaseUrl { string Get(); }
```

Tenga en cuenta que hasta que la interfaz se implementa en cada plataforma, no se ejecutará la aplicación. En el proyecto común, asegúrese de que recuerde establecer el `BaseUrl` utilizando el `DependencyService`:

```csharp
var source = new HtmlWebViewSource();
source.BaseUrl = DependencyService.Get<IBaseUrl>().Get();
```

A continuación, se deben proporcionar implementaciones de la interfaz para cada plataforma.

#### <a name="ios"></a>iOS

En iOS, el contenido web debe estar ubicado en el directorio raíz del proyecto o **recursos** directorio con la acción de compilación *BundleResource*, como se muestra a continuación:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](webview-images/ios-vs.png "Archivos locales en iOS")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![](webview-images/ios-xs.png "Archivos locales en iOS")

-----

El `BaseUrl` debe establecerse en la ruta de acceso de la agrupación principal:

```csharp
[assembly: Dependency (typeof (BaseUrl_iOS))]
namespace WorkingWithWebview.iOS{
  public class BaseUrl_iOS : IBaseUrl {
    public string Get() {
      return NSBundle.MainBundle.BundlePath;
    }
  }
}
```

#### <a name="android"></a>Android

En Android, coloque HTML, CSS y las imágenes en la carpeta de activos con la acción de compilación *AndroidAsset* tal y como se muestra a continuación:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](webview-images/android-vs.png "Archivos locales en Android")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![](webview-images/android-xs.png "Archivos locales en Android")

-----

En Android, el `BaseUrl` debe establecerse en `"file:///android_asset/"`:

```csharp
[assembly: Dependency (typeof(BaseUrl_Android))]
namespace WorkingWithWebview.Android {
  public class BaseUrl_Android : IBaseUrl {
    public string Get() {
      return "file:///android_asset/";
    }
  }
}
```

En Android, archivos en la **activos** carpeta también son accesibles a través del contexto de Android actual, que se expone mediante el `MainActivity.Instance` propiedad:

```csharp
var assetManager = MainActivity.Instance.Assets;
using (var streamReader = new StreamReader (assetManager.Open ("local.html"))) {
  var html = streamReader.ReadToEnd ();
}
```

#### <a name="windows-phone"></a>Windows Phone

En Windows Phone, coloque HTML, CSS y las imágenes en la raíz del proyecto con la acción de compilación establecida en *contenido* tal y como se muestra a continuación:

![](webview-images/windows-vs.png "Archivos locales en Windows Phone")

En Windows Phone, la `BaseUrl` debe establecerse en `""`:

```csharp
[assembly: Dependency (typeof(BaseUrl_Windows))]
namespace WorkingWithWebview.Windows {
  public class BaseUrl_Windows : IBaseUrl {
    public string Get() {
      return "";
    }
  }
}
```

#### <a name="windows-runtime-and-universal-windows-platform"></a>En tiempo de ejecución de Windows y la plataforma Universal de Windows

En proyectos de Windows en tiempo de ejecución y la plataforma Universal de Windows (UWP), coloque HTML, CSS y las imágenes en la raíz del proyecto con la acción de compilación establecida en *contenido*.

El `BaseUrl` debe establecerse en `"ms-appx-web:///"`:

```csharp
[assembly: Dependency(typeof(BaseUrl))]
namespace WorkingWithWebview.UWP
{
    public class BaseUrl : IBaseUrl
    {
        public string Get()
        {
            return "ms-appx-web:///";
        }
    }
}
```

## <a name="navigation"></a>Navegación

Vista Web admite la navegación a través de varios métodos y propiedades que están disponibles:

- **GoForward()** &ndash; si `CanGoForward` es true, al llamar a `GoForward` se desplaza hacia delante a la siguiente página visitada.
- **GoBack()** &ndash; si `CanGoBack` es true, al llamar a `GoBack` se le remitirá a la última página visitada.
- **CanGoBack** &ndash; `true` si hay páginas para volver a `false` si el explorador se encuentre en la dirección URL de inicio.
- **CanGoForward** &ndash; `true` si el usuario navega hacia atrás y puede avanzar a una página que ya se ha visitado.

En las páginas, `WebView` no es compatible con los movimientos multitáctiles. Es importante para asegurarse de que el contenido está optimizada para móviles y aparece sin la necesidad de zoom.

Es habitual que las aplicaciones mostrar un vínculo dentro de un `WebView`, en lugar del explorador del dispositivo. En estos casos, resulta útil permitir la exploración normal, pero cuando una copia de los aciertos de usuario mientras están en el vínculo de inicio, la aplicación debe volver a la vista de aplicaciones normal.

Utilice las propiedades y métodos de navegación integradas para habilitar este escenario.

Empiece por crear la página de la vista de explorador:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="WebViewDemo.InAppDemo"
Title="In App Browser">
    <ContentPage.Content>
        <StackLayout>
            <StackLayout Orientation="Horizontal" Padding="10,10">
                <Button Text="Back" HorizontalOptions="StartAndExpand" Clicked="backClicked" />
                <Button Text="Forward" HorizontalOptions="End" Clicked="forwardClicked" />
            </StackLayout>
            <WebView x:Name="Browser" WidthRequest="1000" HeightRequest="1000" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

En el código subyacente:

```csharp
public partial class InAppDemo : ContentPage
{
  //sets the URL for the browser in the page at creation
    public InAppDemo (string URL)
    {
        InitializeComponent ();
        Browser.Source = URL;
    }


    private void backClicked(object sender, EventArgs e)
    {
    // Check to see if there is anywhere to go back to
        if (Browser.CanGoBack) {
            Browser.GoBack ();
        } else { // If not, leave the view
            Navigation.PopAsync ();
        }
    }

    private void forwardClicked(object sender, EventArgs e)
    {
        if (Browser.CanGoForward) {
            Browser.GoForward ();
        }
    }
}
```

Ya está.

![](webview-images/in-app-browser.png "Botones de navegación de WebView")

## <a name="events"></a>Eventos

WebView genera dos eventos que le ayudará a responder a cambios de estado:

- **Navegar por** &ndash; evento generado cuando el WebView comienza a cargarse una nueva página.
- **Navega** &ndash; evento generado cuando se carga la página y ha dejado de navegación.

Si prevé usar las páginas Web que toman mucho tiempo en cargar, considere el uso de esos eventos para implementar un indicador de estado. Por ejemplo:

Nuestro XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="WebViewDemo.LoadingDemo" Title="Loading Demo">
    <ContentPage.Content>
    <StackLayout>
      <Label x:Name="LoadingLabel"
        Text="Loading..."
        HorizontalOptions="Center"
        isVisible="false" />
      <WebView x:Name="Browser"
      HeightRequest="1000"
      WidthRequest="1000"
      Navigating="webOnNavigating"
      Navigated="webOnEndNavigating" />
    </StackLayout>
    </ContentPage.Content>
</ContentPage>
```
Nuestros dos controladores de eventos:

```csharp
void webOnNavigating (object sender, WebNavigatingEventArgs e)
{
            LoadingLabel.IsVisible = true;
}

void webOnEndNavigating (object sender, WebNavigatedEventArgs e)
{
            LoadingLabel.IsVisible = false;
}
```

Esto genera el siguiente resultado (cargar):

![](webview-images/loading-start.png "Ejemplo de evento de navegación de WebView")

Carga terminada:

![](webview-images/loading-end.png "Ejemplo de navegar de un evento de vista Web")

## <a name="performance"></a>Rendimiento

Los avances recientes que han visto cada uno de los exploradores web populares adoptar las tecnologías como la representación y la compilación de JavaScript acelerados por hardware. Desafortunadamente, debido a restricciones de seguridad, la mayoría de los avances actuales no estaba disponible en el el equaivalent iOS de `WebView`, `UIWebView`. Xamarin.Forms `WebView` utiliza `UIWebView`. Si es un problema, debe escribir un representador personalizado que utiliza `WKWebView`, que admite la exploración más rápida. Tenga en cuenta que `WKWebView` solo es compatible con iOS 8 y versiones más recientes.

WebView en Android predeterminada es tan rápido como el explorador integrado.

El [UWP WebView](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/web-view) utiliza el motor de representación de Microsoft Edge. Dispositivos de escritorio y Tablet PC deberían ver el mismo rendimiento que usar el mismo explorador Edge.

El `WebBrowser` control en Windows Phone 8 y Windows Phone 8.1 hace no las características de compatibilidad con la HTML5 más reciente y a menudo se puede tener un rendimiento deficiente. Tenga en cuenta cómo se mostrarán los sitios en el de Windows Phone `WebView`. No es suficiente probar en el Explorador de Internet.

## <a name="permissions"></a>Permisos

En orden para `WebView` para trabajar, debe asegurarse de que los permisos se establecen para cada plataforma. Tenga en cuenta que en algunas plataformas, `WebView` funcionará en modo de depuración, pero no cuando se compila para la versión. Eso es porque algunos permisos, como los de acceso a internet en Android, se establecen de manera predeterminada en Visual Studio para Mac cuando está en modo de depuración.

- **Windows Phone 8.0** &ndash; requiere `ID_CAP_WEBBROWSERCOMPONENT` para el control y `ID_CAP_NETWORKING` para acceder a internet.
- **Windows Phone 8.1 y UWP** &ndash; requiere la capacidad de Internet (cliente y servidor) para mostrar el contenido de la red.
- **Android** &ndash; requiere `INTERNET` solo al mostrar el contenido de la red. El contenido local no requiere ningún permiso especial.
- **iOS** &ndash; no requiere permisos especiales.

## <a name="layout"></a>Diseño

A diferencia de la mayoría de vistas de Xamarin.Forms, `WebView` requiere que `HeightRequest` y `WidthRequest` se especifican al contenido en StackLayout o RelativeLayout. Si no puede especificar las propiedades, los `WebView` no se representará.

Los ejemplos siguientes muestran diseños que intentar solucionarse, representación `WebView`s:

StackLayout con WidthRequest & HeightRequest:

```xaml
<StackLayout>
    <Label Text="test" />
    <WebView Source="http://www.xamarin.com/"
        HeightRequest="1000"
        WidthRequest="1000" />
</StackLayout>
```

RelativeLayout con WidthRequest & HeightRequest:

```xaml
<RelativeLayout>
    <Label Text="test"
        RelativeLayout.XConstraint= "{ConstraintExpression
                                      Type=Constant, Constant=10}"
        RelativeLayout.YConstraint= "{ConstraintExpression
                                      Type=Constant, Constant=20}" />
    <WebView Source="http://www.xamarin.com/"
        RelativeLayout.XConstraint="{ConstraintExpression Type=Constant,
                                     Constant=10}"
        RelativeLayout.YConstraint="{ConstraintExpression Type=Constant,
                                     Constant=50}"
        WidthRequest="1000" HeightRequest="1000" />
</RelativeLayout>
```

AbsoluteLayout *sin* WidthRequest & HeightRequest:

```xaml
<AbsoluteLayout>
    <Label Text="test" AbsoluteLayout.LayoutBounds="0,0,100,100" />
    <WebView Source="http://www.xamarin.com/"
      AbsoluteLayout.LayoutBounds="0,150,500,500" />
</AbsoluteLayout>
```

Cuadrícula *sin* WidthRequest & HeightRequest. Cuadrícula es uno de los diseños de algunas que no requieren la especificación de anchos y altos solicitadas.:

```xaml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition Height="100" />
        <RowDefinition Height="*" />
    </Grid.RowDefinitions>
    <Label Text="test" Grid.Row="0" />
    <WebView Source="http://www.xamarin.com/" Grid.Row="1" />
</Grid>
```


## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con WebView (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithWebview/)
- [WebView (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/WebView)
