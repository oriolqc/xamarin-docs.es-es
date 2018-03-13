---
title: "Introducción a MonoTouch.Dialog"
description: "El MonoTouch.Dialog (MT D) el Kit de herramientas es un marco de trabajo indispensable para el desarrollo de la interfaz de usuario en Xamarin.iOS rápido de aplicaciones. MONTE D. facilita la rápida y sencilla definir la interfaz de usuario mediante un enfoque declarativo, en lugar de la tarea de controladores de navegación, tablas, etcetera de aplicación compleja. Además, monte D tiene un conjunto flexible de las API que proporcionan a los programadores con un control completo o entrega el enfoque, así como otras características, como la imagen de fondo de deslizar para actualizar, cargar, buscar soporte técnico y la generación dinámica de interfaz de usuario a través de los datos JSON. Esta guía presentan las diferentes maneras de trabajar con MT D. y, a continuación, se adentra en uso avanzado de profundidad."
ms.topic: article
ms.prod: xamarin
ms.assetid: 52A35B24-C23B-8461-A8FF-5928A2128FB0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: b9bf4c5ee803aa60a2730703e64fcf73d07efdb5
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="introduction-to-monotouchdialog"></a>Introducción a MonoTouch.Dialog

_El MonoTouch.Dialog (MT D) el Kit de herramientas es un marco de trabajo indispensable para el desarrollo de la interfaz de usuario en Xamarin.iOS rápido de aplicaciones. MONTE D. facilita la rápida y sencilla definir la interfaz de usuario mediante un enfoque declarativo, en lugar de la tarea de controladores de navegación, tablas, etcetera de aplicación compleja. Además, monte D tiene un conjunto flexible de las API que proporcionan a los programadores con un control completo o entrega el enfoque, así como otras características, como la imagen de fondo de deslizar para actualizar, cargar, buscar soporte técnico y la generación dinámica de interfaz de usuario a través de los datos JSON. Esta guía presentan las diferentes maneras de trabajar con MT D. y, a continuación, se adentra en uso avanzado de profundidad._


MonoTouch.Dialog, que se conoce como MT D para abreviar, es un Kit de herramientas de desarrollo de interfaz de usuario rápida que permite a los desarrolladores crear pantallas de aplicación y navegación con información, en lugar de la tarea de creación de controladores de la vista, tablas, etcetera. Por lo tanto, proporciona una simplificación significativa de reducción de desarrollo y el código de interfaz de usuario. Por ejemplo, considere la siguiente captura de pantalla:

 [![](images/image1.png "Por ejemplo, considere esta captura de pantalla")](images/image1.png#lightbox)

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

Al trabajar con tablas en iOS, suele haber una gran cantidad de código redundante.
Por ejemplo, cada vez que se necesita una tabla, se necesita un origen de datos para rellenar la tabla. En una aplicación que tiene dos pantallas basadas en tablas que estén conectadas a través de un controlador de navegación, cada pantalla comparte una gran parte del mismo código.

MONTE D. simplifica encapsulando todo ese código a una API genérica para la creación de una tabla. A continuación, proporciona una abstracción sobre esa API que permite que un objeto declarativo enlace sintaxis que facilita aún más. Por lo tanto, hay dos API disponibles en MT D:

-   **API de bajo nivel elementos** : el *API elementos* se basa en la creación de un árbol jerárquico de elementos que representan las pantallas y sus componentes. La API de elementos proporciona a los desarrolladores la máxima flexibilidad y control en la creación de interfaces de usuario. Además, la API de elementos ha avanzado compatibilidad con definición declarativa a través de JSON, que permite la declaración increíblemente rápida, así como la generación dinámica de interfaz de usuario desde un servidor. 
-   **API de reflexión de alto nivel** : también conocida como la *enlace**API* , en qué clases se anotan con las sugerencias de interfaz de usuario y, a continuación, monte D automáticamente crea pantallas en función de los objetos y proporciona un enlace entre lo que se muestra (opcionalmente) en pantalla, modificar y realizar una copia del objeto subyacente. El ejemplo anterior muestra el uso de la API de reflexión. Esta API no proporciona el control específico encargada de la API de elementos, pero reduce aún más la complejidad al crear automáticamente la jerarquía de elementos en función de atributos de clase. 


MONTE D. viene empaquetada con un conjunto grande de integrados en elementos de interfaz de usuario para la creación de la pantalla, pero también reconoce la necesidad de elementos personalizados y diseños de pantalla de opciones avanzadas. Por lo tanto, la extensibilidad es que una primera clase destacadas cocida a la API. Los programadores pueden ampliar los elementos existentes o crear otras nuevas y, a continuación, se integren perfectamente.

Además, monte D tiene un número de características comunes de UX iOS integrados, como "Deslizar para actualizar" admite, imagen asincrónica de carga y buscar soporte técnico.

En este artículo se Eche un vistazo completa a trabajar con MT D., incluidos:

-   **MONTE Componentes de D** : Esto se centrará en la comprensión de las clases que constituyen MT D. para permitir poner al día rápidamente. 
-   **Referencia de elementos** : una lista completa de los elementos integrados de MT D. 
-   **Uso avanzado de** : Esto cubre características avanzadas como Deslizar para actualizar, búsqueda, carga de la imagen de fondo, utilizando LINQ para crear jerarquías de elementos y crear elementos personalizados, las celdas, y los controladores para usar con MT D. 

## <a name="understanding-the-pieces-of-mtd"></a>Descripción de las piezas de MT D.

Incluso cuando se usa la API, monte de reflexión D. crea una jerarquía de elementos en segundo plano, como si se crearon a través de la API de los elementos directamente. Además, la compatibilidad JSON se ha mencionado en la sección anterior crea elementos también. Por esta razón, es importante tener un conocimiento básico de las partes constituyentes de MT D.

MONTE D. genera pantallas con las cuatro partes siguientes:

-  **DialogViewController**
-  **RootElement**
-  **Sección**
-  **Element**


### <a name="dialogviewcontroller"></a>DialogViewController

A *DialogViewController*, o *DVC* para abreviar, hereda de `UITableViewController` y, por tanto, representa una pantalla con una tabla. DVCs se pueden insertar en un controlador de navegación como un UITableViewController regular.

### <a name="rootelement"></a>RootElement

A *RootElement* es el contenedor de nivel superior para los elementos que entran en un DVC. Contiene secciones, que, a continuación, pueden contener elementos. No se representan RootElements; en su lugar, se trata simplemente como contenedores para lo que realmente se representa. Un RootElement se asigna a un DVC y, a continuación, el DVC representa a sus elementos secundarios.

### <a name="section"></a>Sección

Una sección es un grupo de celdas en una tabla. Como con una sección de tabla normal, si lo desea puede tener un encabezado y pie de página que puede ser texto o vistas incluso personalizadas, como se muestra en la captura de pantalla siguiente:

 [![](images/image2.png "Como con una sección de tabla normal, si lo desea puede tener un encabezado y pie de página que puede ser texto o vistas incluso personalizadas, como se muestra en esta captura de pantalla")](images/image2.png#lightbox)

### <a name="element"></a>Elemento

Un elemento representa una celda de la tabla real. MONTE D va empaquetado con una amplia variedad de elementos que representan los tipos de datos diferentes o diferentes entradas. Por ejemplo, las capturas de pantalla siguientes ilustran algunos de los elementos disponibles:

 [![](images/image3.png "Por ejemplo, este capturas de pantalla muestran algunos de los elementos disponibles")](images/image3.png#lightbox)

## <a name="more-on-sections-and-rootelements"></a>Más en las secciones y RootElements

Analicemos ahora RootElements y secciones en mayor detalle.

### <a name="rootelements"></a>RootElements

Al menos un RootElement es necesario para iniciar el proceso de MonoTouch.Dialog.

Si un RootElement se inicializa con un valor de elemento de sección/este valor se utiliza para buscar a un elemento secundario de elemento que le proporcionará un resumen de la configuración, que se representa en el lado derecho de la pantalla. Por ejemplo, la captura de pantalla siguiente muestra una tabla de la izquierda con una celda que contiene el título de la pantalla de detalles de la derecha, "Dessert", junto con el valor de la desert seleccionado.

 [![](images/image4.png "Esta captura de pantalla muestra una tabla de la izquierda con una celda que contiene el título de la pantalla de detalles de la derecha, postres, junto con el valor de la desert seleccionado") ](images/image4.png#lightbox) [ ![ ] (images/image5.png "esto captura de pantalla siguiente muestra una tabla de la izquierda con una celda que contiene el título de la pantalla de detalles de la derecha, postres, junto con el valor de la desert seleccionado")](images/image5.png#lightbox)

Elementos raíz también se usa dentro de secciones para desencadenar la carga de una nueva página de configuración anidados, como se indicó anteriormente. Cuando se usa en este modo el título proporcionado se usa mientras procesa dentro de una sección y también se usa como el título de la subpágina. Por ejemplo:

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

En el ejemplo anterior, cuando el usuario puntea en "Postres" MonoTouch.Dialog creará una nueva página y navegar hasta él con la raíz que se va a "Postres" y tener un grupo de radio con tres valores.

En este ejemplo concreto, el grupo de radio seleccionará "Pastel de Chocolate" en la sección "Postres" porque se pasa el valor "2" a la RadioGroup. Esto significa que elija el elemento 3 de la lista (índice de cero).

Llamar al método Add o utilizando la sintaxis de inicializador de C# 4 agrega secciones.
Se proporcionan los métodos de inserción para insertar secciones con una animación.

Si creas RootElement con una instancia de grupo (en lugar de un RadioGroup) el valor de resumen de RootElement cuando se muestre en una sección será el recuento acumulado de todas las BooleanElements y CheckboxElements que tienen la misma clave que el valor de Group.Key.

### <a name="sections"></a>Secciones

Las secciones se utilizan para agrupar elementos en la pantalla y son los secundarios directos solo es válidos de RootElement. Secciones pueden contener cualquiera de los elementos estándares, incluidos RootElements nuevo.

RootElements incrustados en una sección sirven para navegar a un nuevo nivel más profundo.

Las secciones pueden tener encabezados y pies de página como cadenas o como UIViews.
Normalmente sólo se utilizarán en las cadenas, pero para crear interfaces de usuario personalizados puede usar cualquier UIView como el encabezado o el pie de página. Se puede usar una cadena para crearlos similar al siguiente:

```csharp
var section = new Section ("Header", "Footer")
```

Para utilizar vistas, simplemente pase las vistas al constructor:

```csharp
var header = new UIImageView (Image.FromFile ("sample.png"));
var section = new Section (header)
```

### <a name="getting-notified"></a>Recibir una notificación

#### <a name="handling-nsaction"></a>Control NSAction

MONTE Superficies de D un `NSAction` como un delegado para controlar las devoluciones de llamada.
Por ejemplo, supongamos que desea controlar un evento de entrada táctil para una celda de tabla creado por MT D. Al crear un elemento con MT D., simplemente proporcione una devolución de llamada de función, tal y como se muestra a continuación:

```csharp
new Section () {
        new StringElement ("Demo Callback", 
                delegate { Console.WriteLine ("Handled"); })
}
```

#### <a name="retrieving-element-value"></a>Recuperar el valor del elemento

Combinar con la `Element.Value` propiedad, la devolución de llamada puede recuperar el valor establecido en otros elementos. Por ejemplo, considere el siguiente código:

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

Este código crea una interfaz de usuario tal y como se muestra a continuación. Para obtener un tutorial completo de este ejemplo, vea el [elementos API tutorial](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md) tutorial.

 [![](images/image6.png "En combinación con la propiedad Element.Value, la devolución de llamada puede recuperar el valor establecido en otros elementos")](images/image6.png#lightbox)

Cuando el usuario presiona la celda de tabla de la parte inferior, que ejecuta el código de la función anónima, escribir el valor de la `element` de instancia para la **salida de la aplicación** panel en Visual Studio para Mac.

## <a name="built-in-elements"></a>Elementos integrados

MONTE D. incluye una serie de elementos de la celda de tabla integrados conocidos como elementos.
Estos elementos se utilizan para mostrar una variedad de tipos diferentes de las celdas de tabla como cadenas, valores de coma flotante, fechas e incluso imágenes, por nombrar solo unos pocos. Cada elemento se encarga de mostrar el tipo de datos de forma adecuada. Por ejemplo, un elemento booleano mostrará un conmutador para activar o desactivar su valor. Del mismo modo, un elemento de tipo float mostrará un control deslizante para cambiar el valor de tipo float.

Existen elementos incluso más complejos que admiten los tipos de datos más enriquecidos, como imágenes y html. Por ejemplo, un elemento html, que abrirá una UIWebView para cargar una página web cuando se selecciona, muestra un título en la celda de tabla.

### <a name="working-with-element-values"></a>Trabajar con valores de elemento

Los elementos que se utilizan para capturar la entrada del usuario exponen un complemento público `Value` propiedad que contiene el valor actual del elemento en cualquier momento. Se actualiza automáticamente como el usuario usa la aplicación.

Éste es el comportamiento de todos los elementos que forman parte de MonoTouch.Dialog, pero no es necesario para los elementos creados por el usuario.

### <a name="string-element"></a>Elemento de cadena

Un `StringElement` muestra un título en el lado izquierdo de una celda de tabla y el valor de cadena del lado derecho de la celda.

 [![](images/image7.png "Un StringElement muestra un título en el lado izquierdo de una celda de tabla y el valor de cadena del lado derecho de la celda")](images/image7.png#lightbox)

Para usar un `StringElement` como un botón, proporcionar un delegado.

```csharp
new StringElement (
        "Click me",
        () => { new UIAlertView("Tapped", "String Element Tapped"
, null, "ok", null).Show(); })
```

 [![](images/image8.png "Para usar un StringElement como un botón, proporcionar un delegado")](images/image8.png#lightbox)

### <a name="styled-string-element"></a>Elemento de estilo de cadena

Un `StyledStringElement` permite que las cadenas se presenten con ambos estilos de celda de tabla integrados o con formato personalizado.

 [![](images/image9.png "Un StyledStringElement permite que las cadenas se presenten con ambos estilos de celda de tabla integrados o con formato personalizado")](images/image9.png#lightbox)

El `StyledStringElement` clase se deriva de `StringElement`, pero permite a los desarrolladores personalizar un conjunto de propiedades como la fuente, el color del texto, color de celda de fondo, el modo de salto de línea, número de líneas que desea mostrar, y si se debe mostrar un accesorio.

### <a name="multiline-element"></a>Elemento de varias líneas

 [![](images/image10.png "Elemento de varias líneas")](images/image10.png#lightbox)

### <a name="entry-element"></a>Elemento de entrada

El `EntryElement`, como el nombre implica, se utiliza para obtener proporcionados por el usuario. Admite cadenas normales o contraseñas, donde los caracteres se ocultan.

 [![](images/image11.png "El EntryElement se utiliza para obtener proporcionados por el usuario")](images/image11.png#lightbox)

Se inicializa con tres valores:

-  El título de la entrada que se mostrará al usuario.
-  Texto de marcador de posición (es decir, el texto en gris horizontal que proporciona una sugerencia al usuario). 
-  El valor del texto.


El marcador de posición y el valor pueden ser nulos. Sin embargo, el título es necesario.

En cualquier momento, el acceso a su propiedad de valor puede recuperar el valor de la `EntryElement`.

Además el `KeyboardType` propiedad puede establecerse en tiempo de creación para el estilo del tipo de teclado deseado para la entrada de datos. Esto se puede usar para configurar el teclado usando los valores de `UIKeyboardType` como se muestra a continuación:

-  Numérica
-  Teléfono
-  Dirección URL
-  Correo electrónico


### <a name="boolean-element"></a>Elemento booleano

 [![](images/image12.png "Elemento booleano")](images/image12.png#lightbox)

### <a name="checkbox-element"></a>Elemento de casilla de verificación

 [![](images/image13.png "Elemento de casilla de verificación")](images/image13.png#lightbox)

### <a name="radio-element"></a>Elemento de radio

A `RadioElement` requiere un `RadioGroup` especificarse en el `RootElement`.

```csharp
mtRoot = new RootElement ("Demos", new RadioGroup("MyGroup", 0))
```

 [![](images/image14.png "Un RadioElement requiere un RadioGroup especificarse en RootElement")](images/image14.png#lightbox)

 `RootElements` También se usan para coordinar los elementos de radio. El `RadioElement` miembros pueden abarcar varias secciones (por ejemplo implementar algo similar para el selector de tono de anillo y tonos personalizado independiente de tonos de sistema). La vista de resumen mostrará el elemento de radio está seleccionado actualmente. Para ello, cree la `RootElement` con el constructor de grupo, similar al siguiente:

```csharp
var root = new RootElement ("Meals", new RadioGroup ("myGroup", 0))
```

El nombre del grupo de `RadioGroup` se usa para mostrar el valor seleccionado en la página contenedora (si existe) y el valor, que es cero en este caso, es el índice del primer elemento seleccionado.

### <a name="badge-element"></a>Elemento distintivo

 [![](images/image15.png "Badge Element")](images/image15.png#lightbox)

### <a name="float-element"></a>Elemento de punto flotante

 [![](images/image16.png "Elemento de punto flotante")](images/image16.png#lightbox)

### <a name="activity-element"></a>Elemento de actividad

 [![](images/image17.png "Elemento de actividad")](images/image17.png#lightbox)

### <a name="date-element"></a>Elemento de fecha

 ![](images/image18.png "Elemento de fecha")

Cuando se selecciona la celda correspondiente a la DateElement, se presenta un selector de fecha, tal y como se muestra a continuación:

 [![](images/image19.png "Cuando se selecciona la celda correspondiente a la DateElement, un selector de fecha se presenta como se muestra")](images/image19.png#lightbox)

### <a name="time-element"></a>Elemento Time

 [![](images/image20.png "Elemento Time")](images/image20.png#lightbox)

Cuando se selecciona la celda correspondiente a la TimeElement, se presenta un selector de tiempo, tal y como se muestra a continuación:

 [![](images/image21.png "Cuando se selecciona la celda correspondiente a la TimeElement, un selector de hora se presenta como se muestra")](images/image21.png#lightbox)

### <a name="datetime-element"></a>Elemento de fecha y hora

 [![](images/image22.png "Elemento de fecha y hora")](images/image22.png#lightbox)

Cuando se selecciona la celda correspondiente a la DateTimeElement, un selector de fecha y hora se presenta tal y como se muestra a continuación:

 [![](images/image23.png "Cuando se selecciona la celda correspondiente a la DateTimeElement, un selector de fecha y hora se presenta como se muestra")](images/image23.png#lightbox)

### <a name="html-element"></a>Elemento de HTML

 [![](images/image24.png "Elemento de HTML")](images/image24.png#lightbox)

El `HTMLElement` muestra el valor de su `Caption` propiedad en la celda de tabla. Cuando seleccionada, el `Url` asignado al elemento se carga en un `UIWebView` controlar tal y como se muestra a continuación:

 [![](images/image25.png "Cuando se selecciona, se carga la dirección Url asignada al elemento en un control UIWebView tal y como se muestra a continuación")](images/image25.png#lightbox)

### <a name="message-element"></a>Elemento Message

 [![](images/image26.png "Elemento Message")](images/image26.png#lightbox)

### <a name="load-more-element"></a>Cargar más de un elemento

Utilice este elemento para permitir que los usuarios carguen más elementos en la lista. Puede personalizar la normal y subtítulos de carga, así como la fuente y color del texto.
El `UIActivity` indicador inicia la animación y el título de la carga se muestra cuando un usuario puntee en la celda y, a continuación, el `NSAction` pasado en el constructor se ejecuta. Una vez el código en el `NSAction` haya finalizado, el `UIActivity` indicador se detiene la animación y vuelve a aparecer el título normal.

### <a name="uiview-element"></a>Elemento UIView

Además, personalizada `UIView` se pueden mostrar con el `UIViewElement`.

### <a name="owner-drawn-element"></a>Elemento dibujado por el propietario

Este elemento debe ser una subclase ya que es una clase abstracta. Debe invalidar el `Height(RectangleF bounds)` método en el que debe devolver el alto del elemento, así como `Draw(RectangleF bounds, CGContext context, UIView view)` en que debe realizar todo el dibujo personalizado dentro de los límites especificados, con los parámetros de contexto y la vista. Este elemento hace el trabajo pesado de creación de subclases un `UIView`y colocarlo en la celda que se devuelve, dejando que sólo es necesario implementar dos invalidaciones simples. Puede ver una mejor implementación de ejemplo en la aplicación de ejemplo en el `DemoOwnerDrawnElement.cs` archivo.

Este es un ejemplo muy sencillo de la implementación de la clase:

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

El `JsonElement` es una subclase de `RootElement` que abarca un `RootElement` para poder cargar el contenido de secundarios anidados de una dirección url local o remoto.

El `JsonElement` es un `RootElement` que se pueden crear instancias de dos formas. Crea una versión un `RootElement` que cargará el contenido a petición. Estos se crean usando la `JsonElement` constructores que toman un argumento adicional al final, la dirección url para cargar el contenido de:

```csharp
var je = new JsonElement ("Dynamic Data", "http://tirania.org/tmp/demo.json");
```

El otro formulario crea los datos desde un archivo local o una existente `System.Json.JsonObject` que ya han analizado:

```csharp
var je = JsonElement.FromFile ("json.sample");
using (var reader = File.OpenRead ("json.sample"))
    return JsonElement.FromJson (JsonObject.Load (reader) as JsonObject, arg);
```

Para obtener más información sobre cómo usar JSON con MT D., consulte el [JSON elemento tutorial](http://docs.xamarin.com/guides/ios/user_interface/monotouch.dialog/json_element_walkthrough) tutorial.

## <a name="other-features"></a>Otras características

### <a name="pull-to-refresh-support"></a>Compatibilidad de actualización de extracción

 *Extracción-a-* *actualizar* es un efecto visual originalmente se encuentra en la *Tweetie2* aplicación, que se pasó a ser un efecto popular entre varias aplicaciones.

Para agregar compatibilidad de actualización de extracción automática a los cuadros de diálogo, basta con hacer dos cosas: enlazar un controlador de eventos para recibir una notificación cuando el usuario extrae los datos y notificar el `DialogViewController` cuando los datos se han cargado para volver atrás y su estado predeterminado.

Enlazar una notificación es sencilla; sólo tiene que conectar a la `RefreshRequested` evento en el `DialogViewController`, similar a la siguiente:

```csharp
dvc.RefreshRequested += OnUserRequestedRefresh;
```

A continuación, en el método `OnUserRequestedRefresh`, algunos carga de datos de la cola, algunos datos de solicitud desde la red o crear un subproceso para calcular los datos. Una vez que se han cargado los datos, debe notificar el `DialogViewController` los nuevos datos en, y para restaurar la vista a su estado predeterminado, esto se hace una llamada a `ReloadComplete`:

```csharp
dvc.ReloadComplete ();
```

### <a name="search-support"></a>Se admite la búsqueda

Para admitir la búsqueda, establezca el `EnableSearch` propiedad en su `DialogViewController`. También puede establecer el `SearchPlaceholder` propiedad que se utilizará como el texto de marca de agua en la barra de búsqueda.

Buscar cambiará el contenido de la vista cuando el usuario. Busca los campos visibles y muestra los empleados para el usuario. La `DialogViewController` expone tres métodos para iniciar, terminar o desencadenar una nueva operación de filtro en los resultados mediante programación. Estos métodos son los siguientes:

-  `StartSearch`
-  `FinishSearch`
-  `PerformFilter`


El sistema es extensible, por lo que puede modificar este comportamiento si desea.

### <a name="background-image-loading"></a>Carga de la imagen de fondo

MonoTouch.Dialog incorpora el [TweetStation](https://github.com/migueldeicaza/TweetStation) cargador de imágenes de la aplicación. Este cargador de imágenes puede usarse para cargar imágenes en segundo plano, admite el almacenamiento en caché y puede notificar el código cuando la imagen se ha cargado.

También limitará el número de conexiones de red salientes.

El cargador de imágenes se implementa en el `ImageLoader` (clase), todo lo que necesita hacer es llamada la `DefaultRequestImage` método, deberá proporcionar el Uri para la imagen que desea cargar, así como una instancia de la `IImageUpdated` interfaz que será se invoca cuando la imagen de alta disponibilidad s ha cargado.

Por ejemplo el siguiente código carga una imagen desde una dirección Url en una `BadgeElement`:

```csharp
string uriString = "http://some-server.com/some image url";

var rootElement = new RootElement("Image Loader") {
        new Section(){
                new BadgeElement( ImageLoader.DefaultRequestImage( new Uri(uriString), this), "Xamarin")
        }
};
```

La clase ImageLoader expone un método de depuración que se puede llamar cuando desea liberar todas las imágenes que actualmente se almacenan en caché en memoria. El código actual tiene una memoria caché para 50 imágenes. Si desea usar un tamaño de caché diferente (por ejemplo, si se espera que las imágenes que sean demasiado grandes que 50 imágenes sería demasiado), puede crear instancias de ImageLoader y pase el número de imágenes que desea conservar en la memoria caché.

## <a name="using-linq-to-create-element-hierarchy"></a>Uso de LINQ para crear la jerarquía de elementos

Mediante el uso inteligente de la sintaxis de inicialización de LINQ y C#, LINQ se puede usar para crear una jerarquía de elementos. Por ejemplo, el código siguiente crea una pantalla de algunas matrices de cadenas y las celdas de identificadores de selección a través de una función anónima que se pasa en cada `StringElement`:

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

## <a name="extending-mtd"></a>Extender MT D.

### <a name="creating-custom-elements"></a>Creación de elementos personalizados

Puede crear su propio elemento heredando desde un elemento ya existente o derivando de la clase raíz del elemento.

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

Si el elemento puede tener un tamaño variable, debe implementar la `IElementSizing` interfaz, que contiene un método:

```csharp
// Returns the height for the cell at indexPath.Section, indexPath.Row
    float GetHeight (UITableView tableView, NSIndexPath indexPath);
```

Si planea implementar su `GetCell` método mediante una llamada a `base.GetCell(tv)` y personalizar la celda devuelta, debe invalidar el `CellKey` propiedad para devolver una clave que sea única para el elemento, similar a la siguiente:

```csharp
static NSString MyKey = new NSString ("MyKey");
    protected override NSString CellKey {
        get {
            return MyKey;
        }
    }
```

Esto funciona para la mayoría de los elementos, pero no para la `StringElement` y `StyledStringElement` como los utilizan su propio conjunto de claves para distintos escenarios de representación. Tendría que replicar el código de esas clases.

### <a name="dialogviewcontrollers-dvcs"></a>DialogViewControllers (DVCs)

La reflexión y la API de elementos utilizan el mismo `DialogViewController`. A veces desea personalizar la apariencia de la vista también puede utilizar algunas características de la `UITableViewController` que van más allá de la creación de interfaces de usuario básica.

El `DialogViewController` es simplemente una subclase de la `UITableViewController` y personalizarlo de la misma manera que personalizaría un `UITableViewController`.

Por ejemplo, si desea cambiar el estilo de lista para ser `Grouped` o `Plain`, puede establecer este valor cambiando la propiedad cuando se crea el controlador, similar al siguiente:

```csharp
var myController = new DialogViewController (root, true){
        Style = UITableViewStyle.Grouped;
    }
```

Para obtener más información avanzada las personalizaciones de la `DialogViewController`, como establecer el fondo, lo haría subclase lo e invalide los propios métodos, como se muestra en el ejemplo siguiente:

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

Este método debe devolver una subclase de `DialogViewController.Source` para casos donde tamaño uniforme sus celdas, o una subclase de `DialogViewController.SizingSource` si sus celdas son desiguales.

Puede utilizar esta invalidación para capturar cualquiera de los `UITableViewSource` métodos. Por ejemplo, [TweetStation](https://github.com/migueldeicaza/TweetStation) usa este método para realizar un seguimiento cuando el usuario se ha desplazado a la parte superior y actualizar en consecuencia el número de tweets no leídos.

## <a name="validation"></a>Validación

Elementos no proporcionan validación por sí mismos como los modelos que se adapta perfectamente a páginas web y aplicaciones de escritorio no corresponden directamente con el modelo de interacción de iPhone.

Si desea realizar la validación de datos, debe hacerlo cuando el usuario desencadena una acción con los datos especificados. Por ejemplo un <span class="ui">realiza</span> o <span class="ui">siguiente</span> botón en la barra de herramientas superior o algunos `StringElement` usar como un botón para ir a la siguiente fase.

Aquí es donde se realiza una validación de entrada básica, y quizás más complejidad de la validación como la comprobación de la validez de una combinación de usuario y contraseña con un servidor.

Cómo notificar al usuario de un error es específico de la aplicación. Se abrirá una `UIAlertView` o mostrar una sugerencia.

## <a name="summary"></a>Resumen

Este artículo trata una gran cantidad de información sobre MonoTouch.Dialog. También se han descrito los aspectos básicos de cómo MT D funciona y trata los distintos componentes que conforman el Monte D. También se presentó la amplia gama de elementos y las personalizaciones de la tabla admitidas MT D. y explica cómo MT D. puede ampliarse con los elementos personalizados. Además, explica la compatibilidad JSON en MT D. que permite crear elementos dinámicamente de JSON.


## <a name="related-links"></a>Vínculos relacionados

- [Presentación en pantalla - Miguel de Icaza crea una pantalla de inicio de sesión de iOS con MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [Presentación en pantalla - crear fácilmente interfaces de usuario de iOS con MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [Tutorial: creación de una aplicación con Elements API](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Tutorial: creación de una aplicación con Reflection API](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Tutorial: uso de un elemento JSON para crear una interfaz de usuario](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Marcado MonoTouch.Dialog JSON](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Cuadro de diálogo de MonoTouch en Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Referencia de clase UITableViewController](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Referencia de clase UINavigationController](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
