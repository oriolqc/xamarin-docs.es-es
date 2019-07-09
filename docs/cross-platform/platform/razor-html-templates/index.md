---
title: Vistas HTML de creación mediante plantillas de Razor
description: " Uso de una página Web de pantalla completa para representar HTML puede ser una manera sencilla y eficaz para representar formatos complejos de forma multiplataforma, especialmente si ya tiene el HTML, JavaScript y CSS de un proyecto de sitio Web."
ms.prod: xamarin
ms.assetid: D8B87C4F-178E-48D9-BE43-85066C46F05C
author: asb3993
ms.author: amburns
ms.date: 07/24/2018
ms.openlocfilehash: 539f59b9835cab6281327bcd1a37482ef82b62cc
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650175"
---
# <a name="building-html-views-using-razor-templates"></a>Vistas HTML de creación mediante plantillas de Razor

En el mundo del desarrollo móvil el término "aplicación híbrida" suele hacer referencia a una aplicación que presenta algunas (o todas) de sus pantallas como páginas HTML dentro de un control de visor web hospedadas.

Hay algunos entornos de desarrollo que permiten compilar su aplicación móvil completamente en HTML y JavaScript, sin embargo, esas aplicaciones pueden sufrir problemas de rendimiento cuando se intenta llevar a cabo un procesamiento complejo o los efectos de la interfaz de usuario y están también limitados en la plataforma puede tener acceso a las características.

Xamarin ofrece lo mejor de ambos mundos, especialmente si se usa el motor de plantillas HTML de Razor. Con Xamarin tiene la flexibilidad necesaria para generar vistas HTML con plantilla entre plataformas que usan JavaScript y CSS, pero también tienen acceso completo a las API de plataforma subyacente y rápida de procesamiento mediante C#.

Este documento explica cómo usar el motor de plantillas de Razor para generar vistas HTML + JavaScript + CSS que se pueden usar en las plataformas móviles con Xamarin.

## <a name="using-web-views-programmatically"></a>Con vistas Web mediante programación

Antes de que nos enteremos de Razor en esta sección se explica cómo utilizar las vistas web para mostrar el contenido HTML directamente: específicamente contenido HTML que se genera dentro de una aplicación.

Xamarin ofrece acceso completo a las API de plataforma subyacente en iOS y Android, por lo que es fácil de crear y mostrar HTML mediante C#. A continuación, se muestra la sintaxis básica para cada plataforma.

### <a name="ios"></a>iOS

También se muestran el HTML en un control UIWebView en Xamarin.iOS tiene unas pocas líneas de código:

```csharp
var webView = new UIWebView (View.Bounds);
View.AddSubview(webView);
string contentDirectoryPath = Path.Combine (NSBundle.MainBundle.BundlePath, "Content/");
var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadHtmlString(html, NSBundle.MainBundle.BundleUrl);
```

Consulte la [iOS UIWebView](http://docs.xamarin.com/recipes/ios/content_controls/web_view/) recetas para obtener más detalles sobre el uso del control UIWebView.

### <a name="android"></a>Android

Mostrar HTML en un control WebView con Xamarin.Android se realiza en unas pocas líneas de código:

```csharp
// webView is declared in an AXML layout file
var webView = FindViewById<WebView> (Resource.Id.webView);

// enable JavaScript execution in your html view so you can provide "alerts" and other js
webView.SetWebChromeClient(new WebChromeClient());

var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadDataWithBaseURL("file:///android_asset/", html, "text/html", "UTF-8", null);
```

Consulte la [Android WebView](http://docs.xamarin.com/recipes/android/controls/webview/) recetas para obtener más información acerca de cómo utilizar el control WebView.

### <a name="specifying-the-base-directory"></a>Especifica el directorio Base

En ambas plataformas hay un parámetro que especifica el directorio base para la página HTML. Esta es la ubicación en el sistema de archivos del dispositivo que se usa para resolver las referencias relativas a recursos como imágenes y archivos CSS. Por ejemplo, como las etiquetas

```html
<link rel="stylesheet" href="style.css" />
<img src="monkey.jpg" />
<script type="text/javascript" src="jscript.js">
```

hacer referencia a estos archivos: **style.css**, **monkey.jpg** y **jscript.js**. La configuración del directorio base indica a la vista web donde se encuentran estos archivos para que puedan ser cargados en la página.

#### <a name="ios"></a>iOS

El resultado de la plantilla se representa en iOS con el siguiente código de C#:

```csharp
webView.LoadHtmlString (page, NSBundle.MainBundle.BundleUrl);
```

El directorio base se especifica como `NSBundle.MainBundle.BundleUrl` que hace referencia al directorio que la aplicación se instala en. Todos los archivos de la **recursos** carpeta se copian en esta ubicación, como el **style.css** archivo que se muestra aquí:

 ![solución de iPhoneHybrid](images/image1_240x163.png)

Debe ser la acción de compilación para todos los archivos de contenido estático **BundleResource**:

 ![acción de compilación del proyecto de iOS: BundleResource](images/image2_250x131.png)

#### <a name="android"></a>Android

Android requiere también un directorio base que se pasará como un parámetro cuando se muestran las cadenas html en una vista web.

```csharp
webView.LoadDataWithBaseURL("file:///android_asset/", page, "text/html", "UTF-8", null);
```

La cadena especial **file:///android_asset/** hace referencia a la carpeta de activos de Android en la aplicación, se muestra aquí que contenga el **style.css** archivo.

 ![Solución de AndroidHybrid](images/image3_240x167.png)

Debe ser la acción de compilación para todos los archivos de contenido estático **AndroidAsset**.

 ![Acción de compilación del proyecto de Android: AndroidAsset](images/image4_250x71.png)

### <a name="calling-c-from-html-and-javascript"></a>Una llamada a C# de HTML y JavaScript

Cuando se carga una página html en una vista web, trata los formularios y los vínculos como si se cargó la página desde un servidor. Esto significa que si el usuario hace clic en un vínculo o envía un formulario de la vista web intentará navegue hasta el destino especificado.

Si el vínculo es a un servidor externo (por ejemplo, google.com), a continuación, la vista web intentará cargar el sitio Web externo (suponiendo que no hay una conexión a internet).

```html
<a href="http://google.com/">Google</a>
```

Si el vínculo es relativo, a continuación, la vista web intentará cargar ese contenido desde el directorio base. Obviamente ninguna conexión de red es necesaria para que funcione, como el contenido se almacena en la aplicación en el dispositivo.

```html
<a href="somepage.html">Local content</a>
```

Las acciones del formulario siguen la misma regla.

```html
<form method="get" action="http://google.com/"></form>
<form method="get" action="somepage.html"></form>
```

No va a hospedar un servidor web en el cliente; Sin embargo, puede usar las mismas técnicas de comunicación de servidor empleadas en patrones de diseño dinámico de hoy en día para llamar a los servicios a través de HTTP GET y administrar las respuestas de forma asincrónica mediante la emisión de JavaScript (o JavaScript que realiza la llamada ya hospedado en la vista web). Esto le permite pasar fácilmente los datos desde el código HTML en código C# para el procesamiento, a continuación, para mostrar de nuevo los resultados en la página HTML.

IOS y Android proporcionan un mecanismo para el código de aplicación interceptar estos eventos de navegación para que el código de la aplicación puede responder (si es necesario). Esta característica es fundamental para crear aplicaciones híbridas, ya que permite código nativo interactuar con la vista web.

#### <a name="ios"></a>iOS

El evento ShouldStartLoad en la vista web de iOS se puede invalidar para permitir que el código de aplicación controlar una solicitud de navegación (por ejemplo, un clic del vínculo). Los parámetros del método proporcionan toda la información

```csharp
bool HandleShouldStartLoad (UIWebView webView, NSUrlRequest request, UIWebViewNavigationType navigationType) {
    // return true if handled in code
    // return false to let the web view follow the link
}
```

y, a continuación, asigne al controlador de eventos:

```csharp
webView.ShouldStartLoad += HandleShouldStartLoad;
```

#### <a name="android"></a>Android

En Android simplemente subclase WebViewClient y, a continuación, implemente código para responder a la solicitud de navegación.

```csharp
class HybridWebViewClient : WebViewClient {
    public override bool ShouldOverrideUrlLoading (WebView webView, IWebResourceRequest request) {
        // return true if handled in code
        // return false to let the web view follow the link
    }
}
```

y, a continuación, establezca al cliente en la vista web:

```csharp
webView.SetWebViewClient (new HybridWebViewClient ());
```

### <a name="calling-javascript-from-c"></a>Una llamada a JavaScript desdeC#

Además de indicando una vista web para cargar una nueva página HTML, C# código también puede ejecutar JavaScript dentro de la página mostrada actualmente. Bloques de código completos de JavaScript se pueden crear mediante C# cadenas y se ejecuta, o puede crear llamadas de método a ya está disponible en la página a través de JavaScript `script` etiquetas.

#### <a name="android"></a>Android

Crear el código JavaScript para ejecutarse y, a continuación, un prefijo con "javascript:" y dar instrucciones a la vista web para cargar esa cadena:

```csharp
var js = "alert('test');";
webView.LoadUrl ("javascript:" + js);
```

#### <a name="ios"></a>iOS

vistas de iOS web proporcionan un método específicamente para llamar a JavaScript:

```csharp
var js = "alert('test');";
webView.EvaluateJavascript (js);
```

### <a name="summary"></a>Resumen

Esta sección presenta las características de los controles de vista web en Android y iOS que nos permiten crear aplicaciones híbridas con Xamarin, incluyendo:

-  La capacidad para cargar el HTML de cadenas que se genera en el código,
-  La capacidad de hacer referencia a archivos locales (CSS, JavaScript, imágenes u otros archivos HTML)
-  La capacidad de interceptar las solicitudes de navegación en el código de C#
-  La posibilidad de llamar a JavaScript desde C# código.


La siguiente sección presenta Razor, que facilita la tarea crear el código HTML para usar en aplicaciones híbridas.

## <a name="what-is-razor"></a>¿Qué es Razor?

Razor es un motor de plantillas que se introdujo con ASP.NET MVC, originalmente para ejecutarse en el servidor y generar HTML para proporcionarse a los exploradores web.

El motor de plantillas Razor amplía la sintaxis HTML estándar con C# para que pueda expresar la distribución e incorporar fácilmente de las hojas de estilo CSS y JavaScript. La plantilla puede hacer referencia a una clase de modelo, que puede ser cualquier tipo personalizado y cuyas propiedades se pueden acceder directamente desde la plantilla. Una de sus principales ventajas es la capacidad de mezclar la sintaxis HTML y C# con facilidad.

Plantillas de Razor no se limitan a uso del lado servidor, también pueden incluirse en las aplicaciones de Xamarin. Uso de plantillas de Razor junto con la capacidad para trabajar con vistas web mediante programación permite a las aplicaciones híbridas multiplataforma sofisticada compilarse con Xamarin.

### <a name="razor-template-basics"></a>Conceptos básicos de plantilla de Razor

Los archivos de plantilla de Razor tienen una **.cshtml** la extensión de archivo. Pueden agregarse a un proyecto de Xamarin en la sección de plantillas de texto en el **nuevo archivo** cuadro de diálogo:

 ![Archivo nuevo: plantilla de Razor](images/image5_400x201.png)

Una plantilla de Razor sencilla ( **RazorView.cshtml**) se muestra a continuación.

```html
@model string
<html>
    <body>
    <h1>@Model</h1>
    </body>
</html>
```

Tenga en cuenta las siguientes diferencias con respecto a un archivo HTML normal:

-  El `@` símbolo tiene un significado especial en las plantillas de Razor: indica que es la siguiente expresión de C# que se debe evaluar.
- `@model` Directiva siempre aparece como la primera línea de un archivo de plantilla de Razor.
-  El `@model` directiva debe ir seguida de un tipo. En este ejemplo se que se pasa una cadena sencilla a la plantilla, pero podría tratarse de cualquier clase personalizada.
-  Cuando `@Model` se hace referencia a lo largo de la plantilla, proporciona una referencia al objeto se pasa a la plantilla cuando se genera (en este ejemplo será una cadena).
-  El IDE generará automáticamente una clase parcial de plantillas (archivos con la **.cshtml** extensión). Puede ver este código, pero no debe editar.
 ![RazorView.cshtml](images/image6_125x34.png) la clase parcial se denomina RazorView para que coincida con el nombre de archivo de plantilla .cshtml. Es este nombre que se usa para hacer referencia a la plantilla en el código de C#.
- `@using` las instrucciones también pueden incluidas en la parte superior de una plantilla de Razor debe incluir espacios de nombres adicionales.


A continuación, se puede generar la salida HTML final con el siguiente código de C#. Tenga en cuenta que especificamos el modelo para que sea una cadena "Hello World" que se incorporará a la salida de la plantilla representada.

```csharp
var template = new RazorView () { Model = "Hello World" };
var page = template.GenerateString ();
```

Este es el resultado que se muestra en una vista web en el emulador de Android y de simulador de iOS:

 ![Hello World](images/image7_523x135.png)

### <a name="more-razor-syntax"></a>Sintaxis de Razor más

En esta sección, vamos a introducir cierta sintaxis básica de Razor para ayudarle a empezar a usarlo. Los ejemplos de esta sección rellenan la clase siguiente con los datos y mostrarla mediante Razor:

```csharp
public class Monkey {
    public string Name { get; set; }
    public DateTime Birthday { get; set; }
    public List<string> FavoriteFoods { get; set; }
}
```

Todos los ejemplos usan el siguiente código de inicialización de datos

```csharp
var animal = new Monkey {
    Name = "Rupert",
    Birthday=new DateTime(2011, 04, 01),
    FavoriteFoods = new List<string>
        {"Bananas", "Banana Split", "Banana Smoothie"}
};
```

#### <a name="displaying-model-properties"></a>Mostrar las propiedades del modelo

Cuando el modelo es una clase con propiedades, que fácilmente se utilizan en la plantilla de Razor como se muestra en esta plantilla de ejemplo:

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @(Model.Birthday.ToString("d MMMM yyyy"))</p>
    </body>
</html>
```

Esto se puede representar en una cadena con el código siguiente:

```csharp
var template = new RazorView () { Model = animal };
var page = template.GenerateString ();
```

El resultado final se muestra aquí en una vista web en el emulador de Android y de simulador de iOS:

 ![Rupert](images/image8_516x160.png)

#### <a name="c-statements"></a>Instrucciones de C#

C# más complejas se pueden incluir en la plantilla, como las actualizaciones de la propiedad de modelo y el cálculo de edad en este ejemplo:

```html
@model Monkey
<html>
    <body>
    @{
        Model.Name = "Rupert X. Monkey";
        Model.Birthday = new DateTime(2011,3,1);
    }
    <h1>@Model.Name</h1>
    <p>Birthday: @Model.Birthday.ToString("d MMMM yyyy")</p>
    <p>Age: @(Math.Floor(DateTime.Now.Date.Subtract (Model.Birthday.Date).TotalDays/365)) years old</p>
    </body>
</html>
```

Puede escribir complejos línea expresiones de C# (por ejemplo, para dar formato a la edad) rodeando el código con `@()`.

Se pueden escribir varias instrucciones de C# enmarcándolos con `@{}`.

#### <a name="if-else-statements"></a>Instrucciones if-else

Las bifurcaciones de código se pueden expresar con `@if` tal como se muestra en este ejemplo de plantilla.

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @(Model.Birthday.ToString("d MMMM yyyy"))</p>
    <p>Age: @(Math.Floor(DateTime.Now.Date.Subtract (Model.Birthday.Date).TotalDays/365)) years old</p>
    <p>Favorite Foods:</p>
    @if (Model.FavoriteFoods.Count == 0) {
        <p>No favorites</p>
    } else {
        <p>@Model.FavoriteFoods.Count favorites</p>
    }
    </body>
</html>
```

#### <a name="loops"></a>Bucles

Bucle de construcciones como `foreach` también se pueden agregar. El `@` prefijo puede usarse en la variable de bucle ( `@food` en este caso) se representan en HTML.

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @Model.Birthday.ToString("d MMMM yyyy")</p>
    <p>Age: @(Math.Floor(DateTime.Now.Date.Subtract (Model.Birthday.Date).TotalDays/365)) years old</p>
    <p>Favorite Foods:</p>
    @if (Model.FavoriteFoods.Count == 0) {
        <p>No favorites</p>
    } else {
        <ul>
            @foreach (var food in @Model.FavoriteFoods) {
                <li>@food</li>
            }
        </ul>
    }
    </body>
</html>
```

La salida de la plantilla anterior se muestra en ejecución en el emulador de Android y de simulador de iOS:

 ![Rupert X Monkey](images/image9_520x277.png)

En esta sección se ha descrito los aspectos básicos del uso de plantillas de Razor para representar vistas simples de solo lectura. La siguiente sección explica cómo crear aplicaciones más completas con Razor que pueden aceptar la entrada de usuario e interoperar entre JavaScript en la vista HTML y C#.

## <a name="using-razor-templates-with-xamarin"></a>Uso de plantillas de Razor con Xamarin

En esta sección se explica cómo usar la compilación de su propia aplicación híbrida con las plantillas de solución en Visual Studio para Mac. Existen tres plantillas disponibles en el **archivo > Nuevo > solución...**  ventana:

- **Android > aplicación > aplicación de Android WebView**
- **iOS > aplicación > aplicación WebView**
- **Proyecto de ASP.NET MVC**



El **nueva solución** ventana similar a este para iPhone y proyectos de Android: la descripción de la solución en la parte derecha resalta la compatibilidad con el motor de plantillas Razor.

 ![Creación de iPhone y Android soluciones](images/image13_1139x959.png)

Tenga en cuenta que se puede agregar fácilmente un **.cshtml** plantilla Razor a *cualquier* existente proyecto de Xamarin, no es necesario usar estas plantillas de solución. los proyectos de iOS no es necesario un guión gráfico usar Razor basta con agregar un control UIWebView a cualquier vista mediante programación y plantillas de Razor se pueden representar todo en código C#.

El contenido de solución de plantilla predeterminado para iPhone y Android proyectos se muestra a continuación:

 ![iPhone y Android plantillas](images/image10_428x310.png)

Las plantillas proporcionan infraestructura de aplicación de lista para usar para cargar una plantilla de Razor con un objeto de modelo de datos, procesar la entrada de usuario y comunicarse con el usuario a través de JavaScript.

Las partes importantes de la solución son:

-  Contenido estático, como el **style.css** archivo.
-  Al igual que los archivos de plantilla de Razor .cshtml **RazorView.cshtml** .
-  Modelo de clases que se hace referencia en las plantillas de Razor como **ExampleModel.cs** .
-  La clase específica de la plataforma que crea la vista web y representa la plantilla, como el `MainActivity` en Android y el `iPhoneHybridViewController` en iOS.


La siguiente sección explica cómo funcionan los proyectos.

### <a name="static-content"></a>Contenido estático

El contenido estático incluye las hojas de estilo CSS, imágenes, archivos JavaScript u otro contenido que se puede vincular desde o al que hace referencia un archivo HTML que se muestran en una vista web.

Los proyectos de plantilla incluyen una hoja de estilos mínima para demostrar cómo incluir contenido estático en la aplicación híbrida. Se hace referencia a la hoja de estilos CSS en la plantilla como esta:

```html
<link rel="stylesheet" href="style.css" />
```

Puede agregar cualquier hoja de estilos y archivos de JavaScript que necesita, incluidos los marcos, como JQuery.

### <a name="razor-cshtml-templates"></a>Razor cshtml plantillas

La plantilla incluye un Razor **.cshtml** archivo que previamente se ha escrito el código para facilitar la comunicación de datos entre el HTML/JavaScript y C#. Esto le permitirá realizar una copia de aplicaciones híbridas sofisticados que no sólo mostrar datos de solo lectura desde el modelo, pero también acepta la entrada del usuario en el código HTML y pasarlo al código de C# para su procesamiento o almacenamiento de compilación.

#### <a name="rendering-the-template"></a>La plantilla de representación

Una llamada a la `GenerateString` en una plantilla representa HTML listo para su presentación en una vista web. Si la plantilla utiliza un modelo, a continuación, se debe proporcionar antes de representarlo. Este diagrama ilustra cómo representación funciona – no que los recursos estáticos pueden resolverse con la vista web en tiempo de ejecución con el directorio base proporcionado para buscar los archivos especificados.

 ![Diagrama de flujo de Razor](images/image12_700x421.png)

#### <a name="calling-c-code-from-the-template"></a>Llamar a código de C# desde la plantilla

Comunicación desde una vista web representado devolviendo la llamada a C# se realiza estableciendo la dirección URL de la vista web y, a continuación, interceptar la solicitud en C# para atender la solicitud nativa sin volver a cargar la vista web.

Puede verse un ejemplo de cómo se controla el botón de RazorView. El botón tiene el siguiente código HTML:

```html
<input type="button" name="UpdateLabel" value="Click" onclick="InvokeCSharpWithFormValues(this)" />
```

El `InvokeCSharpWithFormValues` función JavaScript lee todos los valores desde el formulario HTML y establece el `location.href` para la vista web:

```javascript
location.href = "hybrid:" + elm.name + "?" + qs;
```

Este paso intenta desplazarse por la vista web a una dirección URL con un esquema personalizado (p ej. `hybrid:`)

```
hybrid:UpdateLabel?textbox=SomeValue&UpdateLabel=Click
```

Cuando la vista web nativos procese esta solicitud de navegación, tenemos la oportunidad de interceptarlo. En iOS, esto se realiza mediante el control HandleShouldStartLoad evento del UIWebView. En Android, simplemente subclase el WebViewClient utiliza en el formulario e invalidar ShouldOverrideUrlLoading.

Los aspectos internos de estos dos interceptores de navegación es básicamente el mismo.

En primer lugar, compruebe la dirección URL de vista web está intentando cargar, y si no se inicia con el esquema personalizado (`hybrid:`), permitir la navegación que se produzca con normalidad.

Para el esquema de dirección URL personalizado, todo el contenido de la dirección URL entre el esquema y el "?" es el nombre del método para controlar (en este caso, "UpdateLabel"). Todo el contenido de la cadena de consulta se tratará como parámetros para la llamada al método:

```csharp
var resources = url.Substring(scheme.Length).Split('?');
var method = resources [0];
var parameters = System.Web.HttpUtility.ParseQueryString(resources[1]);
```

`UpdateLabel` en este ejemplo realiza una cantidad mínima de manipulación de cadenas en el parámetro del cuadro de texto (anteponiendo "C# dice" en la cadena) y, a continuación, vuelve a llamar a la vista web.

Después de controlar la dirección URL, el método anula el panel de navegación para que la vista web no intenta finalizar navegar a la dirección URL personalizada.

#### <a name="manipulating-the-template-from-c"></a>Manipulación de la plantilla de C#

Comunicación con una vista web HTML representada de C# se realiza mediante una llamada a JavaScript en la vista web. En iOS, esto se realiza mediante una llamada a `EvaluateJavascript` en el UIWebView:

```csharp
webView.EvaluateJavascript (js);
```

En Android, JavaScript se puede invocar en la vista web al cargar el código de JavaScript como una dirección URL mediante el `"javascript:"` esquema de dirección URL:

```csharp
webView.LoadUrl ("javascript:" + js);
```

## <a name="making-an-app-truly-hybrid"></a>Hacer que una aplicación híbrida verdaderamente

Estas plantillas no hace uso de controles nativos en cada plataforma, toda la pantalla se rellena con una vista web única.

HTML puede ser muy útil para la creación de prototipos y mostrar los tipos de elementos web es mejor como texto enriquecido y el diseño dinámico. Sin embargo, no todas las tareas se ajustan a HTML y JavaScript, desplazarse a través de las listas largas de datos, por ejemplo, realiza mejor mediante los controles de interfaz de usuario nativos como (UITableView de iOS) o ListView en Android.

Las vistas web en la plantilla pueden ampliarse fácilmente con controles específicos de la plataforma: basta con Editar la **MainStoryboard.storyboard** en el Diseñador de iOS o la **Resources/layout/Main.axml** en Android.

### <a name="razortodo-sample"></a>Ejemplo de RazorTodo

El [RazorTodo](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo) repositorio contiene dos soluciones independientes para mostrar las diferencias entre una aplicación controlada por completo en HTML y una aplicación que combina HTML con los controles nativos:

-  **RazorTodo** -aplicación controlada por completo en HTML mediante plantillas de Razor.
-  **RazorNativeTodo** : usa los controles de vista de lista nativo para iOS y Android, pero muestra la pantalla de edición HTML y Razor.


Estas aplicaciones de Xamarin que se ejecutan en iOS y Android, utilizando las bibliotecas de clases portables (PCL) para compartir código común, como las clases de base de datos y el modelo. Razor **.cshtml** plantillas también pueden incluirse en la PCL por lo que están compartir fácilmente entre plataformas.

Ambas aplicaciones de ejemplo incorporan el uso compartido de Twitter y API de texto a voz de la plataforma nativa, que muestra que las aplicaciones híbridas con Xamarin todavía tienen acceso a toda la funcionalidad subyacente de las vistas de HTML Razor basada en plantillas.

El **RazorTodo** aplicación usa plantillas HTML de Razor para las vistas de lista y edición. Esto significa que podemos crear la aplicación casi por completo en una biblioteca de clases Portable compartida (incluida la base de datos y **.cshtml** plantillas de Razor). Las capturas de pantalla siguiente muestran las aplicaciones iOS y Android.

 ![RazorTodo](images/Both_700x290.png)

El **RazorNativeTodo** aplicación usa una plantilla de HTML Razor para la vista de edición, pero implementa una lista de desplazamiento nativa en cada plataforma. Esto proporciona una serie de ventajas como:

-  Rendimiento: los controles nativos de desplazamiento usan la virtualización para asegurarse de que el desplazamiento rápido y sin problemas incluso con muy largas listas de datos.
-  Experiencia nativa - elementos de interfaz de usuario específicas de la plataforma son fácilmente habilitados, como la compatibilidad con el desplazamiento rápido índices en iOS y Android.


Una ventaja clave de la creación de aplicaciones híbridas con Xamarin es que puede iniciar con una interfaz de usuario controladas por completo en HTML (por ejemplo, el primer ejemplo) y, a continuación, agregar la funcionalidad específica de la plataforma cuando sea necesario (como se muestra en el segundo ejemplo). Las pantallas de lista nativo y HTML Razor edición pantallas en iOS y Android se muestran a continuación.

 ![RazorNativeTodo](images/BothNative_700x290.png)

## <a name="summary"></a>Resumen

Este artículo explica las características de los controles de vista web disponibles en iOS y Android que facilitan la creación de aplicaciones híbridas.

A continuación, se describe el motor de plantillas de Razor y la sintaxis que se puede usar para generar HTML fácilmente en las aplicaciones de Xamarin con. **cshtml** archivos de plantilla de Razor. También se describe el Visual Studio para Mac, plantillas de solución que le permiten rápidamente empezar a crear aplicaciones híbridas con Xamarin.

Por último presentó los ejemplos de RazorTodo que demuestran cómo combinar las vistas web con interfaces de usuario nativas y las API.

### <a name="related-links"></a>Vínculos relacionados

- [RazorTodo Sample](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo)
- [MVC 3: motor de vistas Razor (Microsoft)](http://www.asp.net/mvc/videos/mvc-3/mvc-3-razor-view-engine)
- [Introducción a la programación Web de ASP.NET mediante la sintaxis Razor (Microsoft)](http://www.asp.net/web-pages/tutorials/basics/2-introduction-to-asp-net-web-programming-using-the-razor-syntax)
