---
title: "Vistas de creación HTML mediante plantillas de Razor"
description: " Uso de una página Web de pantalla completa para representar HTML puede ser una manera sencilla y eficaz para representar formatos complejos de una manera multiplataforma, especialmente si ya tiene el código HTML, Javascript y CSS desde un proyecto de sitio Web."
ms.topic: article
ms.prod: xamarin
ms.assetid: D8B87C4F-178E-48D9-BE43-85066C46F05C
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: 5c69b8e71cac5d9f0385728ca75a5f311cb24fc0
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="building-html-views-using-razor-templates"></a>Vistas de creación HTML mediante plantillas de Razor

En el mundo de desarrollo para dispositivos móviles el término "aplicación híbrida" suele hacer referencia a una aplicación que presenta algunas (o todas) de sus pantallas como páginas HTML dentro de un control de Visor de web hospedada.

Hay algunos entornos de desarrollo que permiten compilar su aplicación móvil completamente en HTML y Javascript, sin embargo, esas aplicaciones pueden sufrir problemas de rendimiento cuando intente llevar a cabo el procesamiento complejo o los efectos de la interfaz de usuario y son también limitado en la plataforma. funciones que pueden obtener acceso.

Xamarin ofrece lo mejor de ambos mundos, especialmente si se usa el motor de plantillas de HTML de Razor. Con Xamarin tiene la flexibilidad necesaria para generar vistas HTML con plantilla multiplataforma que usan Javascript y CSS, pero también tienen acceso completo a la API de la plataforma subyacente y procesamiento rápido con C#.

Este documento explica cómo utilizar el vistas HTML + Javascript + CSS que se pueden usar en las distintas plataformas móviles con Xamarin de compilación del motor de plantillas de Razor.

## <a name="using-web-views-programmatically"></a>Utilizar vistas Web mediante programación

Antes de que Aprendemos sobre Razor en esta sección explica cómo utilizar vistas web para mostrar contenido HTML directamente – específicamente contenido HTML que se genera dentro de una aplicación.

Xamarin ofrece acceso completo a las API de la plataforma subyacente en iOS y Android, por lo que es fácil crear y mostrar HTML mediante C#. A continuación, se muestra la sintaxis básica para cada plataforma.

### <a name="ios"></a>iOS

También se muestran el HTML en un control de UIWebView en Xamarin.iOS toma unas pocas líneas de código:

```csharp
var webView = new UIWebView (View.Bounds);
View.AddSubview(webView);
string contentDirectoryPath = Path.Combine (NSBundle.MainBundle.BundlePath, "Content/");
var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadHtmlString(html, NSBundle.MainBundle.BundleUrl);
```

Consulte la [iOS UIWebView](http://docs.xamarin.com/recipes/ios/content_controls/web_view/) recetas para obtener más información sobre cómo utilizar el control de UIWebView.

### <a name="android"></a>Android

Mostrar HTML en un control WebView mediante Xamarin.Android se realiza en unas pocas líneas de código:

```csharp
// webView is declared in an AXML layout file
var webView = FindViewById<WebView> (Resource.Id.webView);
var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadDataWithBaseURL("file:///android_asset/", html, "text/html", "UTF-8", null);
```

Consulte la [WebView Android](http://docs.xamarin.com/recipes/android/controls/webview/) recetas para obtener más información sobre cómo utilizar el control WebView.

### <a name="specifying-the-base-directory"></a>Especificación del directorio Base

En ambas plataformas hay un parámetro que especifica el directorio base de la página HTML. Esta es la ubicación en el sistema de archivos del dispositivo que se utiliza para resolver las referencias relativas a recursos como imágenes y archivos CSS. Por ejemplo, como etiquetas

```html
<link rel="stylesheet" href="style.css" />
<img src="monkey.jpg" />
<script type="text/javascript" src="jscript.js">
```

hacer referencia a estos archivos: **style.css**, **monkey.jpg** y **jscript.js**. La configuración del directorio base indica la vista web donde se encuentran estos archivos por lo que se pueden cargar en la página.

#### <a name="ios"></a>iOS

La salida de la plantilla se representa en iOS con el siguiente código de C#:

```csharp
webView.LoadHtmlString (page, NSBundle.MainBundle.BundleUrl);
```

El directorio base se especifica como `NSBundle.MainBundle.BundleUrl` que hace referencia al directorio que la aplicación se instala en. Todos los archivos de la **recursos** carpeta se copian en esta ubicación, como el **style.css** archivo que se muestra aquí:

 ![solución de iPhoneHybrid](images/image1_240x163.png)

Debe ser la acción de compilación para todos los archivos de contenido estático **BundleResource**:

 ![acción de compilación del proyecto de iOS: BundleResource](images/image2_250x131.png)

#### <a name="android"></a>Android

Android también requiere un directorio base que se pasarán como un parámetro cuando se muestran las cadenas html en una vista web.

```csharp
webView.LoadDataWithBaseURL("file:///android_asset/", page, "text/html", "UTF-8", null);
```

La cadena especial **file:///android_asset/** hace referencia a la carpeta de activos de Android en su aplicación, se muestra aquí que contenga el **style.css** archivo.

 ![Solución de AndroidHybrid](images/image3_240x167.png)

Debe ser la acción de compilación para todos los archivos de contenido estático **AndroidAsset**.

 ![Acción de compilación del proyecto de Android: AndroidAsset](images/image4_250x71.png)

### <a name="calling-c-from-html-and-javascript"></a>Llamar a C# desde HTML y Javascript

Cuando se carga una página html en una vista web, trata los formularios y vínculos como lo haría si la página se cargó desde un servidor. Esto significa que si el usuario hace clic en un vínculo o envía un formulario de la vista web intentarán navegar hasta el destino especificado.

Si el vínculo es a un servidor externo (por ejemplo, google.com), a continuación, la vista web intentará cargar el sitio Web externo (suponiendo que existe una conexión a internet).

```html
<a href="http://google.com/">Google</a>
```

Si el vínculo es relativo, a continuación, la vista web intentará cargar dicho contenido desde el directorio base. Obviamente ninguna conexión de red es necesaria para que funcione, como el contenido se almacena en la aplicación en el dispositivo.

```html
<a href="somepage.html">Local content</a>
```

Acciones de formulario siguen la misma regla.

```html
<form method="get" action="http://google.com/"></form>
<form method="get" action="somepage.html"></form>
```

No va a hospedar un servidor web en el cliente; Sin embargo, puede usar las mismas técnicas de comunicación de servidor empleadas en patrones de diseño dinámico de hoy en día para llamar a los servicios a través de HTTP GET y administrar las respuestas de forma asincrónica mediante la emisión de Javascript (o Javascript que realiza la llamada ya hospeda en la vista web). Esto le permite pasar fácilmente los datos desde el código HTML en código C# para el procesamiento, a continuación, en mostrar de nuevo los resultados en la página HTML.

IOS y Android proporcionan un mecanismo para el código de aplicación interceptar estos eventos de navegación para que el código de aplicación puede responder (si es necesario). Esta característica es fundamental para crear aplicaciones híbridas porque permite interactuar con la vista web código nativo.

#### <a name="ios"></a>iOS

El evento ShouldStartLoad en la vista web de iOS se puede invalidar para permitir que el código de aplicación controlar una solicitud de navegación (como un clic en un vínculo). Los parámetros del método proporcionan toda la información

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
    public override bool ShouldOverrideUrlLoading (WebView webView, string url) {
        // return true if handled in code
        // return false to let the web view follow the link
    }
}
```

y, a continuación, establezca al cliente en la vista web:

```csharp
webView.SetWebViewClient (new HybridWebViewClient ());
```

### <a name="calling-javascript-from-c"></a>Llamar a Javascript desde C#

Además de informar a una vista web para cargar una nueva página HTML, código de C# también puede ejecutar Javascript en la página mostrada actualmente. Bloques de código de Javascript completos se pueden crear utilizando cadenas de C# y ejecutar, o puede elaborar llamadas de método a Javascript ya disponible en la página a través de `script` etiquetas.

#### <a name="android"></a>Android

Cree el código de Javascript para ejecutarse y, a continuación, agregarle un prefijo con "javascript:" y dar instrucciones a la vista web para cargar esa cadena:

```csharp
var js = "alert('test');";
webView.LoadUrl ("javascript:" + js);
```

#### <a name="ios"></a>iOS

vistas de iOS web proporcionan un método específicamente para llamar a Javascript:

```csharp
var js = "alert('test');";
webView.EvaluateJavascript (js);
```

### <a name="summary"></a>Resumen

Esta sección presenta las características de los controles de vista web en Android y iOS que nos permiten crear aplicaciones híbridas con Xamarin, incluidos:

-  La capacidad para cargar HTML de cadenas que se genera en el código,
-  La capacidad de hacer referencia a archivos locales (CSS, Javascript, imágenes u otros archivos HTML),
-  La capacidad para interceptar las solicitudes de navegación en código C#,
-  La capacidad de llamar a Javascript desde el código C#.


La siguiente sección presenta Razor, lo que facilita el proceso crear el código HTML para usar en aplicaciones híbridas.

## <a name="what-is-razor"></a>¿Qué es Razor?

Razor es un motor de plantillas que se introdujo con ASP.NET MVC, originalmente para ejecutarse en el servidor y generar el código HTML que se sirvan para exploradores web.

El motor de plantillas de Razor extiende la sintaxis HTML estándar con C# para que pueda expresar la colocación e incorporar fácilmente las hojas de estilos CSS y Javascript. La plantilla puede hacer referencia a una clase de modelo, que puede ser cualquier tipo personalizado y cuyas propiedades son accesibles directamente desde la plantilla. Uno de sus principales ventajas es la capacidad de mezclar fácilmente sintaxis HTML y C#.

Plantillas de Razor no están limitadas a uso de servidor, también pueden incluirse en las aplicaciones de Xamarin. Usar plantillas de Razor junto con la capacidad para trabajar con vistas web mediante programación permite a las aplicaciones sofisticadas híbridas multiplataforma compilarse con Xamarin.

### <a name="razor-template-basics"></a>Conceptos básicos de plantilla de Razor

Los archivos de plantilla de Razor tienen un **.cshtml** la extensión de archivo. Se pueden agregar a un proyecto de Xamarin en la sección de plantillas de texto en el **nuevo archivo** cuadro de diálogo:

 ![Nuevo archivo - plantilla de Razor](images/image5_400x201.png)

Una plantilla sencilla que Razor ( **RazorView.cshtml**) se muestra a continuación.

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
-  El `@model` directiva debe ir seguida de un tipo. En este ejemplo se pasa a la plantilla de una cadena simple, pero podría tratarse de cualquier clase personalizada.
-  Cuando `@Model` se hace referencia a lo largo de la plantilla, proporciona una referencia al objeto se pasa a la plantilla cuando se genera (en este ejemplo será una cadena).
-  El IDE generará automáticamente una clase parcial de plantillas (archivos con la **.cshtml** extensión). Puede ver este código pero que no se debe editar.
 ![RazorView.cshtml](images/image6_125x34.png) la clase parcial se denomina RazorView para que coincida con el nombre de archivo de plantilla .cshtml. Es este nombre que se utiliza para hacer referencia a la plantilla en código C#.
- `@using` las instrucciones también se pueden incluidas en la parte superior de una plantilla de Razor para incluir espacios de nombres adicionales.


A continuación, se puede generar la salida HTML final con el siguiente código de C#. Tenga en cuenta que especificamos el modelo para que sea una cadena "Hola mundo" que se incorporará a la salida de la plantilla representado.

```csharp
var template = new RazorView () { Model = "Hello World" };
var page = template.GenerateString ();
```

Este es el resultado que se muestra en una vista web en el simulador y el emulador de Android de iOS:

 ![Hello World](images/image7_523x135.png)

### <a name="more-razor-syntax"></a>Sintaxis de Razor más

En esta sección a que vamos a introducir algunos sintaxis Razor básica para ayudarle a empezar usarlo. Los ejemplos de esta sección rellenan la siguiente clase con datos y mostrarlo mediante Razor:

```csharp
public class Monkey {
    public string Name { get; set; }
    public DateTime Birthday { get; set; }
    public List<string> FavoriteFoods { get; set; }
}
```

Todos los ejemplos utilizan el siguiente código de inicialización de datos

```csharp
var animal = new Monkey {
    Name = "Rupert",
    Birthday=new DateTime(2011, 04, 01),
    FavoriteFoods = new List<string>
        {"Bananas", "Banana Split", "Banana Smoothie"}
};
```

#### <a name="displaying-model-properties"></a>Mostrar las propiedades del modelo

Cuando el modelo es una clase con propiedades, que son fácilmente hace referencia en la plantilla de Razor como se muestra en esta plantilla de ejemplo:

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

El resultado final se muestra aquí en una vista web en el simulador y el emulador de Android de iOS:

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

Puede escribir complejos línea expresiones de C# (por ejemplo, dar formato a la edad) que rodean el código con `@()`.

Varias instrucciones de C# pueden escribirse encerrarlas con `@{}`.

#### <a name="if-else-statements"></a>Instrucciones if-else

Bifurcaciones de código se pueden expresar con `@if` tal como se muestra en este ejemplo de plantilla.

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

Al igual que las construcciones de bucle `foreach` también se pueden agregar. El `@` se puede utilizar el prefijo en la variable de bucle ( `@food` en este caso) para representar en HTML.

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

El resultado de la plantilla anterior se muestra con el simulador y el emulador de Android de iOS:

 ![Rupert X Monkey](images/image9_520x277.png)

En esta sección se trata los aspectos básicos del uso de plantillas de Razor para representar vistas simples de solo lectura. La siguiente sección explica cómo crear aplicaciones más completas con Razor pueden aceptar la entrada de usuario e interactuar entre Javascript en la vista HTML y C#.

## <a name="using-razor-templates-with-xamarin"></a>Uso de plantillas de Razor con Xamarin

En esta sección se explica cómo usar la compilación de su propia aplicación híbrida con las plantillas de solución en Visual Studio para Mac. Hay tres plantillas disponibles desde el **archivo > Nuevo > solución...**  ventana:

- **Android > aplicaciones > aplicación WebView Android**
- **iOS > aplicaciones > aplicación WebView**
- **Proyecto de ASP.NET MVC**



El **nueva solución** ventana aspecto para iPhone y Android proyectos: compatibilidad con el motor de plantillas de Razor resalta la descripción de la solución a la derecha.

 ![Creación de iPhone y Android soluciones](images/image13_1139x959.png)

Tenga en cuenta que puede agregar fácilmente un **.cshtml** plantilla de Razor para *cualquier* existente proyecto de Xamarin, no es necesario utilizar estas plantillas de solución. proyectos de iOS no requieren un guión gráfico usar Razor; basta con agregar mediante programación un control UIWebView a cualquier vista y se pueden procesar plantillas Razor todo en código C#.

El contenido de solución de plantilla predeterminado de iPhone y Android proyectos se muestra a continuación:

 ![iPhone y Android plantillas](images/image10_428x310.png)

Las plantillas proporcionan la infraestructura de aplicación de la lista para usar para cargar una plantilla de Razor con un objeto de modelo de datos, procesar proporcionados por el usuario y comunicarse de vuelta con el usuario a través de Javascript.

Las partes importantes de la solución son:

-  Contenido estático, como el **style.css** archivo.
-  Archivos de plantilla de Razor .cshtml como **RazorView.cshtml** .
-  Modelo de clases que se hace referencia en las plantillas de Razor como **ExampleModel.cs** .
-  La clase específica de la plataforma que crea la vista web y representa la plantilla, como el `MainActivity` en Android y `iPhoneHybridViewController` en iOS.


En la siguiente sección se explica cómo funcionan los proyectos.

### <a name="static-content"></a>Contenido estático

Contenido estático incluye las hojas de estilos CSS, imágenes, archivos Javascript u otro contenido que se pueden vincular desde o hace referencia a un archivo HTML que se muestran en una vista web.

Los proyectos de plantilla incluyen una hoja de estilos mínima para demostrar cómo se incluye el contenido estático en una aplicación híbrida. Se hace referencia a la hoja de estilos CSS en la plantilla similar al siguiente:

```html
<link rel="stylesheet" href="style.css" />
```

Puede agregar cualquier hoja de estilos y archivos Javascript que necesita, incluidos los marcos de trabajo como JQuery.

### <a name="razor-cshtml-templates"></a>Razor cshtml plantillas

La plantilla incluye una Razor **.cshtml** archivo que previamente se haya escrito el código para facilitar la comunicación de datos entre el HTML/Javascript y C#. Esto le permitirá realizar copias de aplicaciones híbridas sofisticados que no solo muestran datos de solo lectura del modelo, pero también aceptar entradas de usuario en el código HTML y pasarla al código de C# para el procesamiento o almacenamiento de información de compilación.

#### <a name="rendering-the-template"></a>La plantilla de representación

Llamar a la `GenerateString` en una plantilla representa HTML listo para su presentación en una vista web. Si la plantilla utiliza un modelo, a continuación, se debe proporcionar antes de representarlo. Este diagrama ilustra cómo representación funciona – no que los recursos estáticos pueden resolverse con la vista web en tiempo de ejecución, mediante el directorio de base proporcionado para buscar los archivos especificados.

 ![Diagrama de flujo de Razor](images/image12_700x421.png)

#### <a name="calling-c-code-from-the-template"></a>Llamar a código de C# desde la plantilla

Comunicación desde una vista web representado llamadas a C# se realiza estableciendo la dirección URL de la vista web y, a continuación, interceptando la solicitud en C# para atender la solicitud nativo sin volver a cargar la vista web.

Puede verse un ejemplo de cómo se controla el botón del RazorView. El botón tiene el siguiente código HTML:

```html
<input type="button" name="UpdateLabel" value="Click" onclick="InvokeCSharpWithFormValues(this)" />
```

El `InvokeCSharpWithFormValues` función Javascript lee todos los valores desde el formulario HTML y establece la `location.href` para la vista web:

```javascript
location.href = "hybrid:" + elm.name + "?" + qs;
```

Este paso intenta desplazarse por la vista web a una dirección URL con un esquema personalizado (p. ej. `hybrid:`)

```
hybrid:UpdateLabel?textbox=SomeValue&UpdateLabel=Click
```

Cuando la vista web nativo procesa esta solicitud de navegación, tenemos la oportunidad de intercepte. En iOS, esto se realiza mediante el control HandleShouldStartLoad evento del UIWebView. En Android, simplemente subclase el WebViewClient utiliza en el formulario y reemplazar ShouldOverrideUrlLoading.

El funcionamiento interno de estos dos interceptores de navegación es básicamente la misma.

En primer lugar, compruebe la dirección URL que está intentando cargar, vista web y si no se inicia con el esquema personalizado (`hybrid:`), permitir que el panel de navegación que se produzca como normal.

Para el esquema de dirección URL personalizado, todo el contenido de la dirección URL entre el esquema y el "?" es el nombre del método tratarse (en este caso, "UpdateLabel"). Todo el contenido de la cadena de consulta se tratará como parámetros para la llamada al método:

```csharp
var resources = url.Substring(scheme.Length).Split('?');
var method = resources [0];
var parameters = System.Web.HttpUtility.ParseQueryString(resources[1]);
```

`UpdateLabel` en este ejemplo realiza una cantidad mínima de manipulación de cadenas en el parámetro de cuadro de texto (anteponiéndole "C# dice" en la cadena) y, a continuación, vuelve a llamar a la vista web.

Después de controlar la dirección URL, el método anula el panel de navegación para que la vista web no intenta finalizar navegar a la dirección URL personalizada.

#### <a name="manipulating-the-template-from-c"></a>Manipulación de la plantilla de C#

Comunicación con una vista web HTML representada de C# se realiza mediante una llamada de Javascript en la vista web. En iOS, esto se realiza mediante una llamada a `EvaluateJavascript` en el UIWebView:

```csharp
webView.EvaluateJavascript (js);
```

En Android, Javascript puede invocar en la vista web carga el código de Javascript como una dirección URL mediante el `"javascript:"` esquema de dirección URL:

```csharp
webView.LoadUrl ("javascript:" + js);
```

## <a name="making-an-app-truly-hybrid"></a>Hace que una aplicación realmente híbrido

Estas plantillas no permiten el uso de controles nativos en cada plataforma: toda la pantalla se rellena con una vista web única.

HTML puede ser bueno para la creación de prototipos y mostrar los tipos de elementos web es mejor como texto enriquecido y el diseño dinámico. Sin embargo, no todas las tareas son adecuadas para HTML y Javascript: desplazarse a través de listas largas de datos, por ejemplo, realiza mejor con controles de interfaz de usuario nativos como (UITableView en iOS) o ListView en Android.

Las vistas web en la plantilla se pueden ampliar fácilmente con controles específicos de la plataforma: basta con modificar la **MainStoryboard.storyboard** en el Diseñador de iOS o **Resources/layout/Main.axml** en Android.

### <a name="razortodo-sample"></a>RazorTodo Sample

El [RazorTodo](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo) repositorio contiene dos soluciones independientes para mostrar las diferencias entre una aplicación completamente orientada a HTML y una aplicación que combina HTML con los controles nativos:

-  **RazorTodo** -aplicaciones orientadas a completamente HTML mediante plantillas de Razor.
-  **RazorNativeTodo** : usar controles de vista de lista nativo para iOS y Android pero muestra la pantalla de edición con HTML y Razor.


Estas aplicaciones de Xamarin se ejecutan en iOS y Android, uso de bibliotecas de clases portables (PCLs) para compartir código común, como las clases de base de datos y el modelo. Razor **.cshtml** plantillas también pueden incluirse en la PCL para fácilmente estén compartidos entre plataformas.

Las aplicaciones de ejemplo incorporan Twitter uso compartido y la API de texto a voz de la plataforma nativa, que muestra que las aplicaciones híbridas con Xamarin seguir tengan acceso a toda la funcionalidad subyacente de vistas de HTML de Razor basada en plantillas.

El **RazorTodo** aplicación usa plantillas de HTML de Razor para las vistas de lista y edición. Esto significa que podemos crear la aplicación casi por completo en una biblioteca de clases Portable compartida (incluida la base de datos y **.cshtml** plantillas de Razor). Las capturas de pantalla siguiente muestran las aplicaciones iOS y Android.

 ![RazorTodo](images/Both_700x290.png)

El **RazorNativeTodo** aplicación usa una plantilla de HTML Razor para la vista de edición, pero se implementa una lista de desplazamiento nativo en cada plataforma. Esto proporciona una serie de ventajas, como:

-  Rendimiento - los controles nativos de desplazamiento utilizan virtualización para asegurarse de desplazamiento rápido y suave incluso con muy largas listas de datos.
-  Experiencia nativo: elementos de interfaz de usuario específica de la plataforma fácilmente están habilitados, como la compatibilidad con el desplazamiento rápido índices en iOS y Android.


Una ventaja clave de la creación de aplicaciones híbridas con Xamarin es que puede iniciar con una interfaz de usuario completamente basadas en HTML (por ejemplo, el primer ejemplo) y, a continuación, agregar la funcionalidad específica de la plataforma cuando sea necesario (como el segundo ejemplo se muestra). Las pantallas de lista nativo y HTML de Razor edición pantallas en ambos iOS y Android se muestran a continuación.

 ![RazorNativeTodo](images/BothNative_700x290.png)

## <a name="summary"></a>Resumen

Este artículo explica las características de los controles de vista web disponibles en iOS y Android que facilitan la creación aplicaciones híbridas.

A continuación, se describe el motor de plantillas de Razor y la sintaxis que puede usarse para generar HTML fácilmente en las aplicaciones de Xamarin con. **cshtml** archivos de plantilla de Razor. También se describe el Visual Studio para Mac plantillas de solución que permiten rápidamente empezar a compilar aplicaciones híbridas con Xamarin.

Por último, introdujo los ejemplos de RazorTodo que muestran cómo unir vistas web con interfaces de usuario nativas y las API.

### <a name="related-links"></a>Vínculos relacionados

- [RazorTodo Sample](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo)
- [MVC 3: motor de vista Razor (Microsoft)](http://www.asp.net/mvc/videos/mvc-3/mvc-3-razor-view-engine)
- [Introducción a la programación Web de ASP.NET mediante la sintaxis Razor (Microsoft)](http://www.asp.net/web-pages/tutorials/basics/2-introduction-to-asp-net-web-programming-using-the-razor-syntax)