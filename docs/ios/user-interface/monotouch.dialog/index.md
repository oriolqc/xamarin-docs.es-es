---
title: Introducción a MonoTouch.Dialog para Xamarin.iOS
description: Este documento describe MonoTouch.Dialog (destino maestro. (D), un marco para el desarrollo de la interfaz de usuario rápida y declarativa con Xamarin.iOS. Describe cómo usar las APIs MonoTouch.Dialog para crear una interfaz en código o JSON y usar características como la incorporación de cambios para actualizar, búsqueda, carga de la imagen de fondo y mucho más.
ms.prod: xamarin
ms.assetid: 52A35B24-C23B-8461-A8FF-5928A2128FB0
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
ms.openlocfilehash: c291a440a1937d2b0f1c229e3fa969caedba9ab9
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675463"
---
# <a name="introduction-to-monotouchdialog-for-xamarinios"></a>Introducción a MonoTouch.Dialog para Xamarin.iOS

MonoTouch.Dialog, que se conoce como el destino maestro. D para abreviar, es un Kit de herramientas de desarrollo de la interfaz de usuario rápida que permite a los desarrolladores crear pantallas de la aplicación y la navegación mediante información en lugar de la tarea de creación de controladores de vistas, tablas, etcetera. Por lo tanto, proporciona una simplificación significativa de reducción de desarrollo y el código de interfaz de usuario. Por ejemplo, considere la siguiente captura de pantalla:

 [![](images/image1.png "Por ejemplo, considere la posibilidad de esta captura de pantalla")](images/image1.png#lightbox)

El código siguiente se utiliza para definir esta pantalla completa:

```csharp
public enum Category
{
    Travel,
    Lodging,
    Books
}
        
public class Expense
{
    [Section("Expense Entry")]

    [Entry("Enter expense name")]
    public string Name;
    [Section("Expense Details")]
  
    [Caption("Description")]
    [Entry]
    public string Details;
        
    [Checkbox]
    public bool IsApproved = true;
    [Caption("Category")]
    public Category ExpenseCategory;
}
```

Al trabajar con tablas en iOS, a menudo hay una gran cantidad de código redundante.
Por ejemplo, cada vez que se necesita una tabla, se necesita un origen de datos para rellenar la tabla. En una aplicación que tiene dos pantallas basadas en tablas que están conectadas a través de un controlador de navegación, cada pantalla comparte una gran parte del mismo código.

DESTINO MAESTRO. D. simplifica encapsular todo el código en una API genérica para la creación de la tabla. A continuación, proporciona una abstracción por encima de esa API que permite que un objeto declarativas enlace sintaxis que facilita aún más. Por lo tanto, hay dos API disponible en el destino maestro. D:

-   **API de bajo nivel elementos** : el *Elements API* se basa en la creación de un árbol jerárquico de los elementos que representan las pantallas y sus componentes. La API de elementos proporciona a los desarrolladores la máxima flexibilidad y control en la creación de interfaces de usuario. Además, la API de elementos ha avanzado compatibilidad con definición declarativa a través de JSON, que permite la declaración increíblemente rápido, así como la generación dinámica de la interfaz de usuario desde un servidor. 
-   **API de reflexión de alto nivel** : también conocido como el *enlace**API* , en las clases que se anotan con sugerencias de interfaz de usuario y, a continuación, el destino maestro. D. crea las pantallas en función de los objetos y proporciona un enlace entre lo que se muestra (opcionalmente) en pantalla, modificar y el objeto subyacente de seguridad automáticamente.   El ejemplo anterior muestra el uso de la API de reflexión. Esta API no proporciona el control específico que hace de los elementos de la API, pero reduce aún más complejidad creando automáticamente a la jerarquía de elementos en función de atributos de clase. 


DESTINO MAESTRO. D. viene empaquetada con un conjunto grande de creado en los elementos de interfaz de usuario para la creación de la pantalla, pero también reconoce la necesidad de elementos personalizados y los diseños de pantalla de opciones avanzadas. Por lo tanto, la extensibilidad es que una primera clase con las características preparadas en la API. Los desarrolladores pueden ampliar los elementos existentes o crear otros nuevos y, a continuación, se integran sin problemas.

Además, el destino maestro. D tiene una serie de características de experiencia de usuario de iOS comunes integradas, como "Deslizar para actualizar" admite imágenes asincrónicas carga y buscar soporte técnico.

Este artículo ofrecerá una visión completa de trabajar con destino maestro. D., incluidos:

-   **DESTINO MAESTRO. Componentes de D** : Esto se centrará en la comprensión de las clases que constituyen el destino maestro. D. Habilitar ponerse al día rápidamente. 
-   **Referencia de elementos** : una lista completa de los elementos integrados de destino maestro. D. 
-   **Uso avanzado** : Esto cubre características avanzadas como la incorporación de cambios para actualizar, búsqueda, carga de la imagen de fondo, utilizando LINQ para crear jerarquías de elementos y crear elementos personalizados, las celdas, y los controladores para usan con destino maestro. D. 

## <a name="setting-up-mtd"></a>Configuración de destino maestro. D.

DESTINO MAESTRO. D. se distribuye con Xamarin.iOS. Para ello, haga doble clic en el **referencias** nodo de un Xamarin.iOS del proyecto en Visual Studio 2017 o Visual Studio para Mac y agregue una referencia a la **MonoTouch.Dialog 1** ensamblado. A continuación, agregue `using MonoTouch.Dialog` instrucciones en el origen de código según sea necesario.

## <a name="understanding-the-pieces-of-mtd"></a>Descripción de las piezas del destino maestro. D.

Incluso cuando se usa la reflexión API, MT. D. crea una jerarquía de elementos bajo el capó, como si se crearon mediante la API de los elementos directamente. Además, la compatibilidad de JSON que se mencionan en la sección anterior crea elementos también. Por este motivo, es importante tener un conocimiento básico de las partes constituyentes del destino maestro. D.

DESTINO MAESTRO. D. compilaciones pantallas mediante las siguientes cuatro partes:

-  **DialogViewController**
-  **RootElement**
-  **Sección**
-  **Element**


### <a name="dialogviewcontroller"></a>DialogViewController

Un *DialogViewController*, o *DVC* para abreviar, hereda de `UITableViewController` y, por tanto, representa una pantalla con una tabla. DVCs pueden insertarse en un controlador de navegación al igual que un UITableViewController regular.

### <a name="rootelement"></a>RootElement

Un *RootElement* es el contenedor de nivel superior para los elementos que se incluyen en un DVC. Contiene secciones, que, a continuación, pueden contener elementos. No se representan RootElements; en su lugar, son simplemente los contenedores para lo que realmente se representa. Un RootElement se asigna a un DVC y, a continuación, el DVC representa a sus elementos secundarios.

### <a name="section"></a>Sección

Una sección es un grupo de celdas en una tabla. Como con una sección de tabla normal, opcionalmente, puede tener un encabezado y pie de página que puede ser texto o vistas incluso personalizadas, como se muestra en la captura de pantalla siguiente:

 [![](images/image2.png "Como con una sección de tabla normal, opcionalmente, puede tener un encabezado y pie de página que puede ser texto o vistas incluso personalizadas, como se muestra en esta captura de pantalla")](images/image2.png#lightbox)

### <a name="element"></a>Elemento

Un elemento representa una celda en la tabla real. DESTINO MAESTRO. D. viene empaquetada con una amplia variedad de elementos que representan diferentes tipos de datos o entradas diferentes. Por ejemplo, las capturas de pantalla siguientes muestran algunos de los elementos disponibles:

 [![](images/image3.png "Por ejemplo, este capturas de pantalla muestran algunos de los elementos disponibles")](images/image3.png#lightbox)

## <a name="more-on-sections-and-rootelements"></a>Más en las secciones y RootElements

Ahora analicemos las secciones y RootElements con mayor detalle.

### <a name="rootelements"></a>RootElements

Se requiere al menos un RootElement para iniciar el proceso de MonoTouch.Dialog.

Si un RootElement se inicializa con un valor de la sección o elemento, a continuación, este valor se utiliza para buscar a un elemento secundario de elemento que proporciona un resumen de la configuración, que se representa en el lado derecho de la pantalla. Por ejemplo, la captura de pantalla siguiente muestra una tabla de la izquierda con una celda que contiene el título de la pantalla de detalles de la derecha "Postre", junto con el valor del desierto seleccionado.

 [![](images/image4.png "Esta captura de pantalla muestra una tabla de la izquierda con una celda que contiene el título de la pantalla de detalles de la derecha, postre, junto con el valor del desierto seleccionado")](images/image4.png#lightbox) [![](images/image5.png "esto captura de pantalla siguiente muestra una tabla de la izquierda con una celda que contiene el título de la pantalla de detalles de la derecha, postre, junto con el valor del desierto seleccionado")](images/image5.png#lightbox)

Elementos raíz también se utiliza dentro de secciones para desencadenar la carga de una nueva página de configuración anidados, como se indicó anteriormente. Cuando se usa en este modo el título proporcionado se usa mientras se procesa dentro de una sección y también se usa como el título de la subpágina. Por ejemplo:

```csharp
var root = new RootElement ("Meals") {
    new Section ("Dinner"){
            new RootElement ("Dessert", new RadioGroup ("dessert", 2)) {
                new Section () {
                    new RadioElement ("Ice Cream", "dessert"),
                    new RadioElement ("Milkshake", "dessert"),
                    new RadioElement ("Chocolate Cake", "dessert")
                }
            }
        }
    }
```

En el ejemplo anterior, cuando el usuario puntea en "Postre," MonoTouch.Dialog creará una nueva página y navegar a ella con la raíz que se va a "Postre" y tener un grupo de radio con tres valores.

En este ejemplo concreto, el grupo de radio seleccionará "Pastel de Chocolate" en la sección "Postre" porque se pasa el valor "2" a la RadioGroup. Esto significa seleccionar el elemento 3 en la lista (índice de cero).

Llamar al método Add o mediante la sintaxis de inicializador de C# 4 agrega secciones.
Se proporcionan los métodos de inserción para insertar secciones con una animación.

Si crea el RootElement con una instancia de grupo (en lugar de un RadioGroup) el valor de resumen de RootElement cuando se muestre en una sección será el número acumulado de todos los BooleanElements y CheckboxElements que tienen la misma clave que el valor Group.Key.

### <a name="sections"></a>Secciones

Las secciones se utilizan para agrupar elementos en la pantalla y son elementos secundarios directos solo es válidos de RootElement. Las secciones pueden contener cualquiera de los elementos estándares, incluidos RootElements nuevo.

RootElements incrustados en una sección se utilizan para navegar a un nuevo nivel más profundo.

En las secciones se pueden tener encabezados y pies de página como cadenas o como UIViews.
Normalmente, sólo se utilizarán en las cadenas, pero para crear interfaces de usuario personalizados puede usar cualquier UIView como el encabezado o pie de página. Puede usar una cadena para crearlos similar al siguiente:

```csharp
var section = new Section ("Header", "Footer")
```

Para usar las vistas, simplemente pase las vistas al constructor:

```csharp
var header = new UIImageView (Image.FromFile ("sample.png"));
var section = new Section (header)
```

### <a name="getting-notified"></a>Permite recibir una notificación

#### <a name="handling-nsaction"></a>Control NSAction

DESTINO MAESTRO. Las superficies de D un `NSAction` como un delegado para controlar las devoluciones de llamada.
Por ejemplo, supongamos que desea controlar un evento de toque de una celda de tabla creado por el destino maestro. D. Al crear un elemento con el destino maestro. D., simplemente proporcione una devolución de llamada de función, tal como se muestra a continuación:

```csharp
new Section () {
        new StringElement ("Demo Callback", 
                delegate { Console.WriteLine ("Handled"); })
}
```

#### <a name="retrieving-element-value"></a>Recuperar el valor del elemento

Combinado con el `Element.Value` propiedad, la devolución de llamada puede recuperar el valor establecido en otros elementos. Por ejemplo, considere el siguiente código:

```csharp
var element = new EntryElement (task.Name, "Enter task description",
        task.Description);
                
var taskElement = new RootElement (task.Name){
        new Section () { element },
        new Section () { 
                new DateElement ("Due Date", task.DueDate)
        },
        new Section ("Demo Retrieving Element Value") {
                new StringElement ("Output Task Description", 
                        delegate { Console.WriteLine (element.Value); })
        }
};
```

Este código crea una interfaz de usuario, como se muestra a continuación. Para obtener un tutorial completo de este ejemplo, vea el [tutorial sobre la API de elementos](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md) tutorial.

 [![](images/image6.png "En combinación con la propiedad Element.Value, la devolución de llamada puede recuperar el valor establecido en otros elementos")](images/image6.png#lightbox)

Cuando el usuario presiona la celda de tabla de la parte inferior, se ejecuta el código de la función anónima, escribir el valor de la `element` de instancia para el **resultado de la aplicación** pad en Visual Studio para Mac.

## <a name="built-in-elements"></a>Elementos integrados

DESTINO MAESTRO. D. viene con un número de elementos de la celda de tabla integrado conocido como elementos.
Estos elementos se utilizan para mostrar una variedad de tipos diferentes de las celdas de tabla como cadenas, valores de punto flotante, fechas e imágenes incluso, por nombrar solo unos pocos. Cada elemento se encarga de mostrar el tipo de datos de forma adecuada. Por ejemplo, un elemento booleano mostrará un conmutador para alternar su valor. Del mismo modo, un elemento flotante mostrará un control deslizante para cambiar el valor de punto flotante.

Hay incluso más complejos elementos para admitir los tipos de datos más completos, como imágenes y html. Por ejemplo, un elemento html, se abrirá un UIWebView para cargar una página web cuando se selecciona, muestra un título en la celda de tabla.

### <a name="working-with-element-values"></a>Trabajar con valores de elemento

Elementos que se utilizan para capturar la entrada del usuario exponen una pública `Value` propiedad que contiene el valor actual del elemento en cualquier momento. Se actualiza automáticamente como el usuario usa la aplicación.

Este es el comportamiento para todos los elementos que forman parte de MonoTouch.Dialog, pero no es necesario para los elementos creados por el usuario.

### <a name="string-element"></a>Elemento de cadena

Un `StringElement` muestra un título en el lado izquierdo de una celda de tabla y el valor de cadena en el lado derecho de la celda.

 [![](images/image7.png "Un StringElement muestra un título en el lado izquierdo de una celda de tabla y el valor de cadena en el lado derecho de la celda")](images/image7.png#lightbox)

Para usar un `StringElement` como un botón, proporcione un delegado.

```csharp
new StringElement (
        "Click me",
        () => { new UIAlertView("Tapped", "String Element Tapped"
, null, "ok", null).Show(); })
```

 [![](images/image8.png "Para usar un StringElement como un botón, proporcione un delegado")](images/image8.png#lightbox)

### <a name="styled-string-element"></a>Elemento de estilo de cadena

Un `StyledStringElement` permite que las cadenas que se presentará con ambos estilos de celda de tabla integrados o con formato personalizado.

 [![](images/image9.png "Las cadenas que se presentará con ambos estilos de celda de tabla integrados permite que un StyledStringElement o con formato personalizado")](images/image9.png#lightbox)

El `StyledStringElement` clase se deriva de `StringElement`, pero permite a los desarrolladores personalización una gran cantidad de propiedades, como la fuente, color de texto, color de celda en segundo plano, el modo de interrupción de línea, número de líneas para mostrar, y si se debe mostrar un accesorio.

### <a name="multiline-element"></a>Elemento de varias líneas

 [![](images/image10.png "Elemento de varias líneas")](images/image10.png#lightbox)

### <a name="entry-element"></a>Elemento de entrada

El `EntryElement`, como el nombre indica, se usa para obtener la entrada del usuario. Es compatible con cadenas normales o contraseñas, donde se ocultan los caracteres.

 [![](images/image11.png "El EntryElement se usa para obtener la entrada de usuario")](images/image11.png#lightbox)

Se inicializa con tres valores:

-  Título de la entrada que se mostrará al usuario.
-  Texto de marcador de posición (este es el texto de atenuadas que proporciona una sugerencia al usuario). 
-  El valor del texto.


El marcador de posición y el valor pueden ser nulos. Sin embargo, el título es necesario.

En cualquier momento, el acceso a su valor de propiedad puede recuperar el valor de la `EntryElement`.

Además el `KeyboardType` propiedad puede establecerse en tiempo de creación para el estilo del tipo de teclado deseado para la entrada de datos. Esto puede usarse para configurar el teclado, mediante los valores de `UIKeyboardType` como se muestra a continuación:

-  Numérica
-  Teléfono
-  Dirección URL
-  Correo electrónico


### <a name="boolean-element"></a>Elemento booleano

 [![](images/image12.png "Elemento booleano")](images/image12.png#lightbox)

### <a name="checkbox-element"></a>Elemento de casilla de verificación

 [![](images/image13.png "Elemento de casilla de verificación")](images/image13.png#lightbox)

### <a name="radio-element"></a>Elemento de radio

Un `RadioElement` requiere un `RadioGroup` se especifiquen en el `RootElement`.

```csharp
mtRoot = new RootElement ("Demos", new RadioGroup("MyGroup", 0))
```

 [![](images/image14.png "Un RadioElement requiere un RadioGroup se especifiquen en el RootElement")](images/image14.png#lightbox)

 `RootElements` También se usan para coordinar los elementos de radio. El `RadioElement` miembros pueden abarcar varias secciones (por ejemplo implementar algo similar al selector de tono de timbre y tonos personalizado independiente de tonos de sistema). La vista de resumen mostrará el elemento de radio está seleccionado actualmente. Para ello, cree el `RootElement` con el constructor de grupo, similar al siguiente:

```csharp
var root = new RootElement ("Meals", new RadioGroup ("myGroup", 0))
```

El nombre del grupo en `RadioGroup` se usa para mostrar el valor seleccionado en la página contenedora (si existe) y el valor, que es cero en este caso, es el índice del primer elemento seleccionado.

### <a name="badge-element"></a>Elemento distintivo

 [![](images/image15.png "Elemento distintivo")](images/image15.png#lightbox)

### <a name="float-element"></a>Elemento float

 [![](images/image16.png "Elemento float")](images/image16.png#lightbox)

### <a name="activity-element"></a>Elemento de actividad

 [![](images/image17.png "Elemento de actividad")](images/image17.png#lightbox)

### <a name="date-element"></a>Elemento de fecha

 ![](images/image18.png "Elemento de fecha")

Cuando se selecciona la celda correspondiente a la DateElement, se presenta un selector de fecha, como se muestra a continuación:

 [![](images/image19.png "Cuando se selecciona la celda correspondiente a la DateElement, un selector de fecha se presenta como se muestra")](images/image19.png#lightbox)

### <a name="time-element"></a>Elemento Time

 [![](images/image20.png "Elemento Time")](images/image20.png#lightbox)

Cuando se selecciona la celda correspondiente a la TimeElement, un selector de hora se presenta como se muestra a continuación:

 [![](images/image21.png "Cuando se selecciona la celda correspondiente a la TimeElement, un selector de hora se presenta como se muestra")](images/image21.png#lightbox)

### <a name="datetime-element"></a>Elemento de fecha y hora

 [![](images/image22.png "Elemento de fecha y hora")](images/image22.png#lightbox)

Cuando se selecciona la celda correspondiente a la DateTimeElement, un selector de fecha y hora se presenta como se muestra a continuación:

 [![](images/image23.png "Cuando se selecciona la celda correspondiente a la DateTimeElement, un selector de fecha y hora se presenta como se muestra")](images/image23.png#lightbox)

### <a name="html-element"></a>Elemento HTML

 [![](images/image24.png "Elemento HTML")](images/image24.png#lightbox)

El `HTMLElement` muestra el valor de su `Caption` propiedad en la celda de tabla. Cuando se selecciona, el `Url` asignado al elemento se carga en un `UIWebView` , tal como se muestra a continuación:

 [![](images/image25.png "Cuando se selecciona, se carga la dirección Url asignada al elemento en un control UIWebView tal como se muestra a continuación")](images/image25.png#lightbox)

### <a name="message-element"></a>Elemento Message

 [![](images/image26.png "Elemento Message")](images/image26.png#lightbox)

### <a name="load-more-element"></a>Cargar más de un elemento

Utilice este elemento para permitir a los usuarios cargar más elementos en la lista. Puede personalizar la normal y títulos de carga, así como el color de fuente y texto.
El `UIActivity` indicador comienza la animación y el título de la carga se muestra cuando un usuario pulsa la celda y, a continuación, el `NSAction` pasado en el constructor se ejecuta. Una vez que el código en el `NSAction` haya finalizado, el `UIActivity` indicador se detiene la animación y se volverá a mostrar el título normal.

### <a name="uiview-element"></a>Elemento UIView

Además, cualquier personalizado `UIView` se pueden ver mediante el `UIViewElement`.

### <a name="owner-drawn-element"></a>Elemento dibujado por el propietario

Este elemento debe ser una subclase ya que es una clase abstracta. Se debe reemplazar el `Height(RectangleF bounds)` método en el que debe devolver el alto del elemento, así como `Draw(RectangleF bounds, CGContext context, UIView view)` en que se debe hacer todo el dibujo personalizado dentro de los límites especificados, utilizando los parámetros de contexto y la vista. Este elemento encarga de la creación de subclases un `UIView`y se coloca en la celda que se devolverán, dejando sólo tener que implementar dos invalidaciones simple. Puede ver una mejor implementación de ejemplo en la aplicación de ejemplo en el `DemoOwnerDrawnElement.cs` archivo.

Este es un ejemplo muy sencillo de implementación de la clase:

```csharp
public class SampleOwnerDrawnElement : OwnerDrawnElement
 {
    public SampleOwnerDrawnElement (string text) : base(UITableViewCellStyle.Default, "sampleOwnerDrawnElement")
    {
        this.Text = text;
    }

    public string Text
    {
        get;set;    
    }

    public override void Draw (RectangleF bounds, CGContext context, UIView view)
    {
        UIColor.White.SetFill();
        context.FillRect(bounds);

        UIColor.Black.SetColor();   
        view.DrawString(this.Text, new RectangleF(10, 15, bounds.Width - 20, bounds.Height - 30), UIFont.BoldSystemFontOfSize(14.0f), UILineBreakMode.TailTruncation);
    }

    public override float Height (RectangleF bounds)
    {
        return 44.0f;
    }
 }
```

### <a name="json-element"></a>Elemento JSON

El `JsonElement` es una subclase de `RootElement` que abarca un `RootElement` para poder cargar el contenido del elemento secundario anidado desde una dirección url local o remoto.

El `JsonElement` es un `RootElement` que se pueden crear instancias de dos formas. Crea una versión un `RootElement` que cargará el contenido a petición. Estos se crean mediante el uso de la `JsonElement` constructores que toman un argumento adicional al final, la dirección url para cargar el contenido de:

```csharp
var je = new JsonElement ("Dynamic Data", "http://tirania.org/tmp/demo.json");
```

El otro formulario crea los datos desde un archivo local o una existente `System.Json.JsonObject` que ya han analizado:

```csharp
var je = JsonElement.FromFile ("json.sample");
using (var reader = File.OpenRead ("json.sample"))
    return JsonElement.FromJson (JsonObject.Load (reader) as JsonObject, arg);
```

Para obtener más información sobre el uso de JSON con destino maestro. D., consulte el [tutorial del elemento JSON](http://docs.xamarin.com/guides/ios/user_interface/monotouch.dialog/json_element_walkthrough) tutorial.

## <a name="other-features"></a>Otras características

### <a name="pull-to-refresh-support"></a>Soporte técnico de incorporación de cambios para actualizar

 *Incorporación de cambios-a-* *actualizar* un efecto visual originalmente se encuentra en la *Tweetie2* app, que pasó a ser un efecto más popular entre varias aplicaciones.

Para agregar compatibilidad automática con la incorporación de cambios para actualizar a los cuadros de diálogo, solo deberá hacer dos cosas: enlazar un controlador de eventos para recibir una notificación cuando el usuario extrae los datos y notificar la `DialogViewController` cuando se ha cargado los datos para volver a su estado predeterminado.

Enlazar una notificación es sencillo; acaba de conectarse a la `RefreshRequested` eventos en el `DialogViewController`, similar al siguiente:

```csharp
dvc.RefreshRequested += OnUserRequestedRefresh;
```

A continuación, en el método `OnUserRequestedRefresh`, poner en cola la carga de algunos datos, algunos datos de solicitud desde la red o ponga en un subproceso para calcular los datos. Una vez que se han cargado los datos, debe notificar a los `DialogViewController` que los nuevos datos se están en y para restaurar la vista a su estado predeterminado, hacerlo mediante una llamada a `ReloadComplete`:

```csharp
dvc.ReloadComplete ();
```

### <a name="search-support"></a>Buscar soporte técnico

Para admitir la búsqueda, establezca el `EnableSearch` propiedad en su `DialogViewController`. También puede establecer el `SearchPlaceholder` propiedad que se utilizará como el texto de marca de agua en la barra de búsqueda.

Buscar cambiará el contenido de la vista cuando el usuario escribe. Busca los campos visibles y muestra que el usuario. La `DialogViewController` expone tres métodos para iniciar mediante programación, terminar o desencadenar una nueva operación de filtro en los resultados. Estos métodos son los siguientes:

-  `StartSearch`
-  `FinishSearch`
-  `PerformFilter`


El sistema es extensible, por lo que puede modificar este comportamiento si desea.

### <a name="background-image-loading"></a>Carga de la imagen de fondo

MonoTouch.Dialog incorpora el [TweetStation](https://github.com/migueldeicaza/TweetStation) cargador de imágenes de la aplicación. Este cargador de imágenes se puede usar para cargar imágenes en segundo plano, admite el almacenamiento en caché y puede notificar el código cuando se haya cargado la imagen.

También limitará el número de conexiones de red salientes.

El cargador de imágenes se implementa en el `ImageLoader` (clase), todo lo que necesita hacer es llamada la `DefaultRequestImage` método, deberá proporcionar el Uri para la imagen que desea cargar, así como una instancia de la `IImageUpdated` interfaz que será se invoca cuando la imagen de alta disponibilidad s ha cargado.

Por ejemplo el siguiente código carga una imagen desde una dirección Url en un `BadgeElement`:

```csharp
string uriString = "http://some-server.com/some image url";

var rootElement = new RootElement("Image Loader") {
        new Section(){
                new BadgeElement( ImageLoader.DefaultRequestImage( new Uri(uriString), this), "Xamarin")
        }
};
```

La clase ImageLoader expone un método de depuración que se puede llamar cuando desea liberar todas las imágenes que actualmente están almacenados en memoria caché. El código actual no tiene una caché de 50 imágenes. Si desea usar un tamaño de caché diferente (por ejemplo, si se espera que las imágenes para que sea demasiado grande que 50 imágenes sería demasiado), puede crear instancias de ImageLoader y pase el número de imágenes que desee conservar en la memoria caché.

## <a name="using-linq-to-create-element-hierarchy"></a>Uso de LINQ para crear la jerarquía de elementos

Mediante el uso inteligente de la sintaxis de inicialización de LINQ y C#, se puede usar LINQ para crear una jerarquía de elementos. Por ejemplo, el código siguiente crea una pantalla de algunas matrices de cadenas y las celdas de identificadores de selección a través de una función anónima que se pasa a cada `StringElement`:

```csharp
var rootElement = new RootElement ("LINQ root element") {
from x in new string [] { "one", "two", "three" }
select new Section (x) {
from y in "Hello:World".Split (':')
select (Element) new StringElement (y,
delegate { Debug.WriteLine("cell tapped"); })
}
};
```

Esto se pueden combinar fácilmente con un almacén de datos XML o datos de una base de datos para crear aplicaciones complejas casi por completo de datos.

## <a name="extending-mtd"></a>Extensión de destino maestro. D.

### <a name="creating-custom-elements"></a>Crear elementos personalizados

Puede crear su propio elemento heredando de ya sea un elemento existente o mediante la derivación de la clase raíz del elemento.

Para crear su propio elemento, desea invalidar los métodos siguientes:

```csharp
// To release any heavy resources that you might have
    void Dispose (bool disposing);

    // To retrieve the UITableViewCell for your element
    // you would need to prepare the cell to be reused, in the
    // same way that UITableView expects reusable cells to work
    UITableViewCell GetCell (UITableView tv)

    // To retrieve a "summary" that can be used with
    // a root element to render a summary one level up.  
    string Summary ()
    // To detect when the user has tapped on the cell
    void Selected (DialogViewController dvc, UITableView tableView, NSIndexPath path)
    // If you support search, to probe if the cell matches the user input
    bool Matches (string text)
```

Si el elemento puede tener un tamaño variable, deberá implementar la `IElementSizing` interfaz, que contiene un método:

```csharp
// Returns the height for the cell at indexPath.Section, indexPath.Row
    float GetHeight (UITableView tableView, NSIndexPath indexPath);
```

Si planea implementar su `GetCell` método mediante una llamada a `base.GetCell(tv)` y personalización de la celda devuelta, deberá invalidar también el `CellKey` propiedad para devolver una clave que sea única para el elemento, como este:

```csharp
static NSString MyKey = new NSString ("MyKey");
    protected override NSString CellKey {
        get {
            return MyKey;
        }
    }
```

Esto funciona para la mayoría de los elementos, pero no para el `StringElement` y `StyledStringElement` ya que estas usan su propio conjunto de claves para diversos escenarios de representación. Tendría que duplicar el código en esas clases.

### <a name="dialogviewcontrollers-dvcs"></a>DialogViewControllers (DVCs)

Usan la reflexión y la API de elementos de la misma `DialogViewController`. A veces desea personalizar la apariencia de la vista, o bien puede utilizar algunas características de la `UITableViewController` que van más allá de la creación de interfaces de usuario básica.

El `DialogViewController` es simplemente una subclase de la `UITableViewController` y personalizarlo de la misma manera que se desea personalizar una `UITableViewController`.

Por ejemplo, si desea cambiar el estilo de lista para ser `Grouped` o `Plain`, podría establecer este valor cambiando la propiedad cuando se crea el controlador, similar al siguiente:

```csharp
var myController = new DialogViewController (root, true){
        Style = UITableViewStyle.Grouped;
    }
```

Para obtener más información avanzada personalizaciones de la `DialogViewController`, como establecer el fondo, lo haría subclase lo y adecuada de invalidación métodos, como se muestra en el ejemplo siguiente:

```csharp
class SpiffyDialogViewController : DialogViewController {
    UIImage image;

    public SpiffyDialogViewController (RootElement root, bool pushing, UIImage image) 
        : base (root, pushing) 
    {
        this.image = image;
    }

    public override LoadView ()
    {
        base.LoadView ();
        var color = UIColor.FromPatternImage(image);
        TableView.BackgroundColor = UIColor.Clear;
        ParentViewController.View.BackgroundColor = color;
    }
}
```

Otro punto de personalización es los siguientes métodos virtuales en el `DialogViewController`:

```csharp
public override Source CreateSizingSource (bool unevenRows)
```

Este método debe devolver una subclase de `DialogViewController.Source` para los casos donde tamaño uniforme las celdas o una subclase de `DialogViewController.SizingSource` si las celdas son desiguales.

Esta invalidación se puede utilizar para capturar cualquiera de los `UITableViewSource` métodos. Por ejemplo, [TweetStation](https://github.com/migueldeicaza/TweetStation) lo use para realizar un seguimiento cuando el usuario se ha desplazado a la parte superior y actualizar en consecuencia el número de tweets no leídos.

## <a name="validation"></a>Validación

Los elementos no proporcionan validación propios como los modelos que son adecuados para las páginas web y aplicaciones de escritorio no se asignan directamente en el modelo de interacción de iPhone.

Si desea realizar la validación de datos, debe hacer esto cuando el usuario desencadena una acción con los datos especificados. Por ejemplo un <span class="ui">realiza</span> o <span class="ui">siguiente</span> botón en la barra de herramientas superior, o algunos `StringElement` utiliza como botón para ir a la siguiente fase.

Aquí es donde se realizaría la validación de entrada básica, y quizás más complejidad de la validación, como comprobar la validez de una combinación de usuario y contraseña con un servidor.

¿Cómo se notifica al usuario de un error es específico de la aplicación. Puede emerger una `UIAlertView` o mostrar una sugerencia.

## <a name="summary"></a>Resumen

Este artículo trata una gran cantidad de información acerca de MonoTouch.Dialog. También se han descrito los aspectos básicos de cómo MT. D funciona y trata los distintos componentes que constituyen el destino maestro. D. También se ha explicado la amplia gama de elementos y las personalizaciones de tabla admitidas por el destino maestro. D. y explica cómo MT. D. puede ampliarse con elementos personalizados. Además explica la compatibilidad de JSON en el destino maestro. D. que permite crear dinámicamente los elementos de JSON.


## <a name="related-links"></a>Vínculos relacionados

- [Presentación en pantalla: Miguel de Icaza crea una pantalla de inicio de sesión de iOS con MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [Presentación en pantalla - crear fácilmente interfaces de usuario de iOS con MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [Tutorial: creación de una aplicación con Elements API](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Tutorial: creación de una aplicación con Reflection API](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Tutorial: uso de un elemento JSON para crear una interfaz de usuario](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Marcado JSON de MonoTouch.Dialog](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Cuadro de diálogo de MonoTouch en Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Referencia de clase UITableViewController](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Referencia de clase UINavigationController](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
