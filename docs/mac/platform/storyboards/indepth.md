---
title: Trabajo con guiones gráficos en Xamarin.Mac
description: Este documento se describe cómo trabajar con guiones gráficos en Xamarin.Mac, examinar cómo cargarlos desde el código, el ciclo de vida del controlador de vista, la cadena de servicio de respuesta, los objetos Segue, controladores de ventana, los reconocedores de gestos y mucho más.
ms.prod: xamarin
ms.assetid: DF4DF7C2-DDD7-4A32-B375-5C5446301EC5
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: e24b448bedc60a537bfcd4a5bfbdbe9562163818
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865928"
---
# <a name="working-with-storyboards-in-xamarinmac"></a>Trabajo con guiones gráficos en Xamarin.Mac

Un guión gráfico define todos de la interfaz de usuario para una aplicación determinada que se divide en una introducción a las funciones de sus controladores de vista. En Interface Builder de Xcode, cada uno de estos controladores se encuentra en su escena.

[![](indepth-images/intro01.png "Un guión gráfico en Interface Builder de Xcode")](indepth-images/intro01.png#lightbox)

El guión gráfico es un archivo de recursos (con las extensiones de `.storyboard`) que se incluya en el lote de la aplicación de Xamarin.Mac cuando se compila y se envían. Para definir el guión gráfico a partir de la aplicación, editarla de `Info.plist` de archivo y seleccione el **interfaz principal** desde el cuadro de lista desplegable: 

[![](indepth-images/sb01.png "El editor Info.plist")](indepth-images/sb01.png#lightbox)

<a name="Loading-from-Code" />

## <a name="loading-from-code"></a>Carga desde el código

Puede que en ocasiones cuando necesite cargar un guión gráfico específico desde el código y crear manualmente un controlador de vista. Puede usar el código siguiente para realizar esta acción:

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);
```

El `FromName` carga el archivo de guión gráfico con el nombre especificado que se ha incluido en el paquete de la aplicación. El `InstantiateControllerWithIdentifier` crea una instancia del controlador de vista con la identidad especificada. Establecer la identidad en Interface Builder de Xcode al diseñar la interfaz de usuario:

[![](indepth-images/sb02.png "Establecer el identificador de guión gráfico")](indepth-images/sb02.png#lightbox)

Si lo desea, puede usar el `InstantiateInitialController` método para cargar el controlador de vista que se ha asignado el controlador inicial en Interface Builder:

[![](indepth-images/sb03.png "Establecimiento del controlador inicial")](indepth-images/sb03.png#lightbox)

Está marcado por el **punto de entrada de guión gráfico** y la flecha de apertura finalizada anterior.

<a name="View-Controllers" />

## <a name="view-controllers"></a>Controladores de vista

Controladores de vista definen las relaciones entre una vista determinada de información dentro de una aplicación de Mac y el modelo de datos que proporciona esa información. Cada escena de nivel superior en el guión gráfico representa un controlador de vista en el código de la aplicación de Xamarin.Mac.

<a name="The-View-Controller-Lifecycle" />

### <a name="the-view-controller-lifecycle"></a>El ciclo de vida del controlador de vista

Se han agregado varios métodos nuevos a la `NSViewController` clase para admitir los guiones gráficos en macOS. Lo más importante son que los métodos de seguimiento se usan para responder al ciclo de vida de la vista que se controla mediante el controlador de vista determinado:

- `ViewDidLoad` : Este método se llama cuando la vista se carga desde el archivo de guión gráfico.
- `ViewWillAppear` : Este método se llama justo antes de que se muestra en pantalla.
- `ViewDidAppear` -Se llama a este método directamente después de la vista se ha mostrado en pantalla.
- `ViewWillDisappear` : Este método se llama justo antes de la vista se quita de la pantalla.
- `ViewDidDisappear` -Se llama a este método directamente después de la vista se ha quitado de la pantalla.
- `UpdateViewConstraints` : Este método se llama cuando las restricciones que definen una vista auto necesidad de posición y el tamaño de diseño se puede actualizar.
- `ViewWillLayout` : Este método se llama justo antes de que se distribuyen las subvistas de esta vista en pantalla.
- `ViewDidLayout` -Se llama a este método directamente después de que se distribuyen las subvistas de vista en pantalla.

<a name="The-Responder-Chain" />

### <a name="the-responder-chain"></a>La cadena de servicio de respuesta

Además, `NSViewControllers` ahora forman parte de la ventana _Respondedor cadena_:

[![](indepth-images/vc01.png "La cadena de servicio de respuesta")](indepth-images/vc01.png#lightbox)

Y por lo tanto son por cable de seguridad para recibir y responder a eventos como cortar, copiar y pegar las selecciones de elementos de menú. Esta conexión View Controller automática-up sólo se produce en las aplicaciones que se ejecuta en macOS Sierra (10.12) y versiones posteriores.

<a name="Containment" />

### <a name="containment"></a>Inclusiones

En los guiones gráficos, ahora puede implementar controladores de vista (por ejemplo, el controlador de vista de división y el controlador de vista de pestañas) _contención_, de modo que puede "contienen" otro sub controladores de vista:

[![](indepth-images/vc02.png "Un ejemplo de contención de controlador de vista")](indepth-images/vc02.png#lightbox)

Controladores de vista secundarios contienen métodos y propiedades para vincularlas hacer una copia en su controlador de vista principal y para trabajar con mostrar o quitar vistas desde la pantalla.

Todos los controladores de vista del contenedor integrado en macOS tiene un diseño específico que Apple recomienda que siga si crear sus propios controladores de vista personalizada de contenedor:

[![](indepth-images/vc03.png "El diseño de controlador de vista")](indepth-images/vc03.png#lightbox)

El controlador de vista de la colección contiene una matriz de elementos de vista de colección, cada uno de los cuales contiene uno o más controladores de vista que contienen sus propias vistas.

<a name="Segues" />

## <a name="segues"></a>Los objetos Segue

Los objetos Segue proporcionan las relaciones entre todas las escenas que definen la interfaz de usuario de la aplicación. Si está familiarizado con los guiones gráficos trabaje en iOS, sabe que los objetos Segue para iOS normalmente definen las transiciones entre las vistas de pantalla completa. Esto difiere de Mac OS, normalmente definen objetos Segue "[contención](#Containment)", donde una escena es el elemento secundario de un elemento primario de escena.

En macOS, mayoría de las aplicaciones tiende a agrupar sus vistas juntos dentro de la misma ventana mediante elementos de interfaz de usuario, como vistas de división y pestañas. A diferencia de iOS, donde se necesitan vistas que se pueden pasar en y fuera de la pantalla, Mostrar espacio debido a la física limitada.

<a name="Presentation-Segues" />

### <a name="presentation-segues"></a>Presentación de los objetos Segue

Dadas las tendencias de macOS hacia la contención, existen situaciones donde _objetos Segue de presentación_ se usan, como Windows Modal, vistas de hoja y Popovers. tipos de macOS proporciona segue integrada la siguiente:

- **Mostrar** -muestra el destino del Segue como una ventana no modal. Por ejemplo, use este tipo de Segue para presentar otra instancia de una ventana de documento en la aplicación.
- **Modal** -presenta el destino del Segue como una ventana modal. Por ejemplo, use este tipo de Segue para presentar la ventana de preferencias de la aplicación.
- **Hoja** -el destino del Segue se presenta como una hoja de cálculo asociada a la ventana primaria. Por ejemplo, utilice este tipo de segue para presentar un buscar y reemplazar la hoja.
- **Elemento flotante** -presenta el destino del Segue como se muestra en una ventana del elemento flotante. Por ejemplo, use este tipo de Segue para presentar opciones cuando se hace clic en un elemento de interfaz de usuario por el usuario.
- **Personalizado** -presenta el destino del Segue utilizando un tipo de Segue personalizada definida por el desarrollador. Consulte la [creación personalizada objetos Segue](#Creating-Custom-Segues) sección para obtener más detalles.

Al usar los objetos Segue de presentación, puede invalidar el `PrepareForSegue` método del controlador de vista principal para su presentación para inicializar y las variables y proporcionar los datos para el controlador de vista que se presentan.

<a name="Triggered-Segues" />

### <a name="triggered-segues"></a>Desencadena los objetos Segue

Objetos Segue desencadenada le permiten especificar objetos Segue con nombre (a través de sus **identificador** propiedad en Interface Builder) y pídale que se desencadena mediante eventos, como el usuario hace clic en un botón o mediante una llamada a la `PerformSegue` método en el código:

```csharp
// Display the Scene defined by the given Segue ID
PerformSegue("MyNamedSegue", this);
``` 

Cuando se diseña la interfaz de usuario de la aplicación, se define el identificador de Segue dentro de Interface Builder de Xcode:

[![](indepth-images/sg02.png "Escriba un nombre de Segue")](indepth-images/sg02.png#lightbox)

En el controlador de vista que actúa como origen de Segue, se debe reemplazar el `PrepareForSegue` se muestra el método y realice cualquier inicialización necesaria antes de ejecuta el objeto Segue y el controlador de vista especificado:

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on Segue ID
    switch (segue.Identifier) {
    case "MyNamedSegue":
        // Prepare for the segue to happen
        ...
        break;
    }
}
```

Si lo desea, puede invalidar el `ShouldPerformSegue` método y si el objeto Segue realmente se ejecuta a través del control C# código. Para los controladores de vista presentada manualmente, llame a su `DismissController` método para quitarlos de la pantalla cuando ya no se necesitan.

<a name="Creating-Custom-Segues" />

### <a name="creating-custom-segues"></a>Crear personalizado a los objetos Segue

Puede que en ocasiones cuando la aplicación requiere un tipo de Segue no proporcionado por los objetos de compilación Segue una definido en macOS. Si este es el caso, puede crear un Segue personalizado que se pueden asignar en Interface Builder de Xcode al diseñar la interfaz de usuario de la aplicación.

Por ejemplo, para crear un nuevo tipo de Segue que reemplaza el controlador de vista actual dentro de una ventana (en lugar de abrir el destino de la escena en una nueva ventana), podemos usar el código siguiente:

```csharp
using System;
using AppKit;
using Foundation;

namespace OnCardMac
{
    [Register("ReplaceViewSeque")]
    public class ReplaceViewSeque : NSStoryboardSegue
    {
        #region Constructors
        public ReplaceViewSeque() {

        }

        public ReplaceViewSeque (string identifier, NSObject sourceController, NSObject destinationController) : base(identifier,sourceController,destinationController) {

        }

        public ReplaceViewSeque (IntPtr handle) : base(handle) {
        }

        public ReplaceViewSeque (NSObjectFlag x) : base(x) {
        }
        #endregion

        #region Override Methods
        public override void Perform ()
        {
            // Cast the source and destination controllers
            var source = SourceController as NSViewController;
            var destination = DestinationController as NSViewController;

            // Swap the controllers
            source.View.Window.ContentViewController = destination;

            // Release memory
            source.RemoveFromParentViewController ();
        }
        #endregion

    }
        
}
```

Un par de cosas a tener en cuenta aquí:

- Usamos el `Register` atributo para exponer esta clase en Objective-C y macOS.
- Vamos a invalidar el `Perform` método para llevar a cabo la acción de nuestro Segue personalizado.
- Sustituiremos la ventana `ContentViewController` controlador con el definido por el destino (destino) de Segue.
- Estamos quitando el controlador de vista original para liberar memoria mediante la `RemoveFromParentViewController` método.

Para usar este nuevo tipo de Segue en Interface Builder de Xcode, se debe compilar en primer lugar, la aplicación, a continuación, cambie a Xcode y agregue un nuevo Segue entre las dos escenas. Establecer el **estilo** a **personalizado** y el **clase Segue** a `ReplaceViewSegue` (el nombre de nuestra clase personalizada de Segue):

[![](indepth-images/sg01.png "Configuración de la clase de Segue")](indepth-images/sg01.png#lightbox)

<a name="Triggered-Segues" />

## <a name="window-controllers"></a>Controladores de ventana

Los controladores de ventana contienen y controlan los distintos tipos de ventana que se puede crear la aplicación de macOS. Para los guiones gráficos, que tienen las siguientes características:

1. Debe proporcionar un controlador de vista de contenido. Este será el mismo controlador de vista de contenido que tiene la ventana secundaria.
2. El `Storyboard` propiedad contendrá el guión gráfico que se cargó el controlador de ventana, de lo contrario, `null` si no se carga desde un guión gráfico.
3. Puede llamar a la `DismissController` método para cerrar la ventana especificada y quitarlo de la vista.

Como controladores de vista, los controladores de ventana implementar el `PerformSegue`, `PrepareForSegue` y `ShouldPerformSegue` métodos y se puede usar como origen de una operación de Segue.

Controlador de ventana es responsable de las siguientes características de una aplicación de macOS:

- Administran una ventana específica.
- Administrar la ventana de la barra de título y barra de herramientas (si está disponible).
- Administran el controlador de vista de contenido para mostrar el contenido de la ventana.

<a name="Gesture-Recognizers" />

## <a name="gesture-recognizers"></a>Reconocedores de gestos

Los reconocedores de gestos para macOS son casi idénticos a sus homólogos en iOS y permiten al desarrollador agregar fácilmente los gestos (por ejemplo, al hacer clic en un botón del mouse) a los elementos de interfaz de usuario de la aplicación.

Sin embargo, donde los gestos en iOS vienen determinados por el diseño de aplicación (por ejemplo, puntear en la pantalla con dos dedos), más hardware dependen de gestos en macOS.

Mediante el uso de los reconocedores de gestos, puede reducir en gran medida la cantidad de código necesario para agregar interacciones personalizadas a un elemento en la interfaz de usuario. Como puede determinar automáticamente entre los clics dobles y simples, haga clic y arrastre los eventos, etcetera.

En lugar de reemplazar el `MouseDown` evento en el controlador de vista, debe usar un reconocedor de movimiento para controlar el evento de entrada de usuario cuando se trabaja con guiones gráficos.

Los reconocedores de gestos siguientes están disponibles en macOS:

- `NSClickGestureRecognizer` -Registrar del mouse hacia abajo y los eventos.
- `NSPanGestureRecognizer` : Registros botón del mouse hacia abajo, arrastre y suelte eventos.
- `NSPressGestureRecognizer` -Registra presionado un botón del mouse durante un período determinado de evento de tiempo.
- `NSMagnificationGestureRecognizer` : Registra un evento de ampliación del hardware trackpad.
- `NSRotationGestureRecognizer` : Registra un evento de rotación del hardware trackpad.

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>Usar referencias a guión gráfico

Una referencia de guión gráfico le permite tomar un diseño de guion gráfico grande y complejo y dividirla en menor guiones gráficos que obtengan hace referencia desde el original, por lo tanto, quitando la complejidad y facilitar los guiones gráficos individuales resultantes diseñar y mantener.

Además, puede proporcionar una referencia de guión gráfico una _delimitador_ a otra escena en el mismo guión gráfico o una escena concreta en uno diferente.

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>Hacer referencia a un guión gráfico externo

Para agregar una referencia a un guión gráfico externo, haga lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en el nombre del proyecto y seleccione **agregar** > **nuevo archivo...**   >  **Mac** > **guión gráfico**. Escriba un **nombre** para el nuevo guion gráfico y haga clic en el **New** botón: 

    [![](indepth-images/ref01.png "Agregar un nuevo guion gráfico")](indepth-images/ref01.png#lightbox)
2. En el **el Explorador de soluciones**, haga doble clic en el nuevo nombre del guión gráfico para abrirlo y editarlo en Interface Builder de Xcode.
3. Definir el diseño de escenas del guión gráfico nuevo como se haría normalmente y guardar los cambios: 

    [![](indepth-images/ref02.png "Diseñar la interfaz")](indepth-images/ref02.png#lightbox)
4. Cambie al guión gráfico que se va a agregar la referencia a en el generador de interfaz.
5. Arrastre un **crear guiones gráficos de referencia** desde el **objeto biblioteca** a la superficie de diseño: 

    [![](indepth-images/ref03.png "Seleccionar una referencia de guión gráfico en la biblioteca")](indepth-images/ref03.png#lightbox)
6. En el **Inspector de atributos**, seleccione el nombre de la **guión gráfico** que creó anteriormente: 

    [![](indepth-images/ref04.png "Configuración de la referencia")](indepth-images/ref04.png#lightbox)
7. Control y haga clic en un Widget de interfaz de usuario (por ejemplo, un botón) en una escena existente y crear un Segue nuevo a la **guión gráfico referencia** que acaba de crear.  En el menú emergente, seleccione **mostrar** para completar el Segue: 

    [![](indepth-images/ref06.png "Establecer el tipo de Segue")](indepth-images/ref06.png#lightbox) 
8. Guarde los cambios en el guión gráfico.
9. Vuelva a Visual Studio para Mac sincronizar los cambios.

Se mostrará cuando se ejecuta la aplicación y el usuario hace clic en el elemento de interfaz de usuario que creó el objeto Segue desde el controlador de ventana inicial desde el guión gráfico externo especificado en la referencia de guión gráfico.

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>Hacer referencia a una escena concreta en un guión gráfico externo

Para agregar una referencia a una escena específica un guión gráfico externo (y no en el controlador de ventana inicial), realice lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en el guión gráfico externo para abrirlo y editarlo en Interface Builder de Xcode.
2. Agregue una nueva escena y diseño como lo haría normalmente: 

    [![](indepth-images/ref07.png "El diseño en Xcode")](indepth-images/ref07.png#lightbox)
3. En el **Inspector de identidad**, escriba un **identificador de guión gráfico** para el controlador de ventana de la escena nueva: 

    [![](indepth-images/ref08.png "Establecer el identificador de guión gráfico")](indepth-images/ref08.png#lightbox)
4. Abra el guión gráfico que se va a agregar la referencia en Interface Builder.
5. Arrastre un **crear guiones gráficos de referencia** desde el **objeto biblioteca** a la superficie de diseño: 

    [![](indepth-images/ref03.png "Seleccionar una referencia de guión gráfico de la biblioteca")](indepth-images/ref03.png#lightbox)
6. En el **Inspector de identidad**, seleccione el nombre de la **guión gráfico** y **Id. de referencia** (identificador de guión gráfico) de la escena que creó anteriormente: 

    [![](indepth-images/ref09.png "Establecer el identificador de referencia")](indepth-images/ref09.png#lightbox)
7. Control y haga clic en un Widget de interfaz de usuario (por ejemplo, un botón) en una escena existente y crear un Segue nuevo a la **guión gráfico referencia** que acaba de crear. En el menú emergente, seleccione **mostrar** para completar el Segue: 

    [![](indepth-images/ref06.png "Establecer el tipo de Segue")](indepth-images/ref06.png#lightbox) 
8. Guarde los cambios en el guión gráfico.
9. Vuelva a Visual Studio para Mac sincronizar los cambios.

Cuando la aplicación es la ejecución y el usuario hace clic en el elemento de interfaz de usuario que ha creado el objeto Segue de la escena con la determinada **identificador de guión gráfico** desde el guión gráfico externo especificado en la referencia de guión gráfico se mostrará.

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>Hacer referencia a una escena concreta en el mismo guión gráfico

Para agregar una referencia a una escena concreta el mismo guión gráfico, haga lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en el guión gráfico para abrirlo y editarlo.
2. Agregue una nueva escena y diseño como lo haría normalmente: 

    [![](indepth-images/ref11.png "Editar el guión gráfico en Xcode")](indepth-images/ref11.png#lightbox)
3. En el **Inspector de identidad**, escriba un **identificador de guión gráfico** para el controlador de ventana de la escena nueva: 

    [![](indepth-images/ref12.png "Establecer el identificador de guión gráfico")](indepth-images/ref12.png#lightbox)
4. Arrastre un **crear guiones gráficos de referencia** desde el **cuadro de herramientas** a la superficie de diseño: 

    [![](indepth-images/ref03.png "Seleccionar una referencia de guión gráfico de la biblioteca")](indepth-images/ref03.png#lightbox)
5. En **Inspector de atributos**, seleccione **Id. de referencia** (identificador de guión gráfico) de la escena que creó anteriormente: 

    [![](indepth-images/ref13.png "Establecer el identificador de referencia")](indepth-images/ref13.png#lightbox)
6. Control y haga clic en un Widget de interfaz de usuario (por ejemplo, un botón) en una escena existente y crear un Segue nuevo a la **guión gráfico referencia** que acaba de crear. En el menú emergente, seleccione **mostrar** para completar el Segue: 

    [![](indepth-images/ref06.png "Seleccionar el tipo de Segue")](indepth-images/ref06.png#lightbox) 
7. Guarde los cambios en el guión gráfico.
8. Vuelva a Visual Studio para Mac sincronizar los cambios.

Cuando la aplicación es la ejecución y el usuario hace clic en el elemento de interfaz de usuario que ha creado el objeto Segue de la escena con la determinada **identificador de guión gráfico** en el mismo guión gráfico especificado en la referencia de guión gráfico se mostrará.

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>Ejemplo de guion gráfico complejo

Para obtener un ejemplo complejo de cómo trabajar con guiones gráficos en una aplicación de Xamarin.Mac, consulte el [aplicación de ejemplo de SourceWriter](https://developer.xamarin.com/samples/mac/SourceWriter/). SourceWriter es un editor de código fuente simple que proporciona compatibilidad con la finalización de código y el resaltado de sintaxis simple.

El código de SourceWriter se ha comentado completamente y, si están disponibles, se han proporcionado vínculos de métodos o tecnologías clave a información relevante en la documentación de las guías de Xamarin.Mac.

## <a name="related-links"></a>Vínculos relacionados

- [MacStoryboard (ejemplo)](https://developer.xamarin.com/samples/mac/MacStoryboard/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabajar con Windows](~/mac/user-interface/window.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
- [Introducción a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
