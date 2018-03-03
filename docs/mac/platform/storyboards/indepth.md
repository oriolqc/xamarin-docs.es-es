---
title: "Trabajar con guiones gráficos"
description: "Crear interfaces de usuario de Mac OS con guiones gráficos con Xcode."
ms.topic: article
ms.prod: xamarin
ms.assetid: DF4DF7C2-DDD7-4A32-B375-5C5446301EC5
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: effa527b330fb6ca75800392e557289a326f17aa
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="storyboards"></a>Guiones gráficos

Un guión gráfico define todos de la interfaz de usuario para una aplicación determinada que se divide en una introducción a las funciones de sus controladores de la vista. En el generador de interfaz de Xcode, cada uno de estos controladores reside en su propio escena.

[ ![](indepth-images/intro01.png "Un guión gráfico en el generador de interfaz de Xcode")](indepth-images/intro01.png)

El guión gráfico es un archivo de recursos (con las extensiones de `.storyboard`) que obtiene incluido en el paquete de la aplicación Xamarin.Mac cuando se compila y se envía. Para definir el guión gráfico inicial para la aplicación, editarlo del `Info.plist` de archivo y seleccione la **interfaz principal** en el cuadro de lista desplegable: 

[ ![](indepth-images/sb01.png "El editor de Info.plist")](indepth-images/sb01.png)

<a name="Loading-from-Code" />

## <a name="loading-from-code"></a>Cargar desde el código

Puede haber ocasiones cuando necesite cargar un guión gráfico específico desde el código y crear manualmente un controlador de vista. Puede usar el siguiente código para realizar esta acción:

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);
```

El `FromName` carga el archivo del guión gráfico con el nombre especificado que se ha incluido en el paquete de la aplicación. El `InstantiateControllerWithIdentifier` crea una instancia del controlador de vista con la identidad especificada. Establecer la identidad en el generador de interfaz de Xcode al diseñar la interfaz de usuario:

[ ![](indepth-images/sb02.png "Establecer el identificador de guión gráfico")](indepth-images/sb02.png)

Si lo desea, puede usar el `InstantiateInitialController` método para cargar el controlador de vista que se ha asignado el controlador inicial en el generador de interfaz:

[ ![](indepth-images/sb03.png "Establecimiento del controlador inicial")](indepth-images/sb03.png)

Está marcado por el **punto de entrada de guión gráfico** y la flecha de abiertas anterior.

<a name="View-Controllers" />

## <a name="view-controllers"></a>Controladores de la vista

Ver controladores definen las relaciones entre una vista determinada de información dentro de una aplicación de Mac y el modelo de datos que proporciona dicha información. Cada escena de nivel superior en el guión gráfico representa un controlador de vista en el código de la aplicación Xamarin.Mac.

<a name="The-View-Controller-Lifecycle" />

### <a name="the-view-controller-lifecycle"></a>El ciclo de vida del controlador de vista

Varios métodos nuevos se agregaron a la `NSViewController` clase para admitir los guiones gráficos en macOS. Lo más importante son los métodos de seguimiento que se utilizan para responder al ciclo de vida de la vista que se controla por el controlador de vista determinado:

- `ViewDidLoad` : Este método se llama cuando la vista se carga desde el archivo del guión gráfico.
- `ViewWillAppear` : Este método se llama justo antes de que la vista se muestra en pantalla.
- `ViewDidAppear` : Este método se llama directamente después de la vista se ha mostrado en pantalla.
- `ViewWillDisappear` : Este método se llama justo antes de que la vista se quita de la pantalla.
- `ViewDidDisappear` : Este método se llama directamente después de la vista se ha quitado de la pantalla.
- `UpdateViewConstraints` : Este método se llama cuando las restricciones que definen una vista automática tenga de posición y el tamaño de diseño que actualizarse.
- `ViewWillLayout` : Este método se llama justo antes de que se distribuyen las subvistas de esta vista en pantalla.
- `ViewDidLayout` : Este método se llama directamente después de que se distribuyen las subvistas de ver en pantalla.

<a name="The-Responder-Chain" />

### <a name="the-responder-chain"></a>La cadena de respuesta

Además, `NSViewControllers` ahora forman parte de la ventana _Respondedor cadena_:

[ ![](indepth-images/vc01.png "La cadena de respuesta")](indepth-images/vc01.png)

Y por lo tanto son por cable telefónico para recibir y responder a eventos, como cortar, copiar y pegar las selecciones de elementos de menú. Este automática View Controller cable telefónico sólo se produce en aplicaciones que se ejecutan en Mac OS Sierra (10.12) y versiones posteriores.

<a name="Containment" />

### <a name="containment"></a>Inclusiones

En los guiones gráficos, ahora puede implementar controladores de la vista (por ejemplo, el controlador de vista de división y el controlador de vista de la ficha) _contención_, por ejemplo, que puede "contienen" otro controladores de la vista de sub:

[ ![](indepth-images/vc02.png "Un ejemplo de contención de controlador de vista")](indepth-images/vc02.png)

Ver controladores de secundarios contienen métodos y propiedades que vincularlas nuevo a su controlador de vista de elemento primario y para trabajar con mostrar y quitar las vistas de la pantalla.

Todos los controladores de vista de contenedor integran macOS tienen un diseño concreto que Apple recomienda que siga si crear sus propios controladores personalizados de la vista de contenedor:

[ ![](indepth-images/vc03.png "El diseño del controlador de vista")](indepth-images/vc03.png)

El controlador de vista de colección contiene una matriz de elementos de vista de colección, cada uno de los cuales contiene uno o más controladores de vista que contienen sus propias vistas.

<a name="Segues" />

## <a name="segues"></a>Segues

Segues proporcionan las relaciones entre todas las escenas que definen la interfaz de usuario de la aplicación. Si está familiarizado con trabajar en guiones gráficos en iOS, sabrá que Segues para iOS normalmente definen las transiciones entre las vistas de pantalla completa. Esto difiere macOS, normalmente definen Segues "[contención](#Containment)", donde una escena es el elemento secundario de un elemento primario escena.

En Mac OS, mayoría de las aplicaciones tiende a agrupar sus vistas en la misma ventana usando los elementos de interfaz de usuario, como vistas de división y pestañas. A diferencia de iOS, donde vistas deben pasarse en y fuera de la pantalla, debido a limitadas física muestra espacio.

<a name="Presentation-Segues" />

### <a name="presentation-segues"></a>Presentación Segues

Proporciona tendencias del macOS hacia contención, hay situaciones donde _Segues presentación_ está acostumbrado, como ventanas modales, vistas de hoja y Popovers. tipos de macOS proporciona desplazará tranquilamente integrada la siguiente:

- **Mostrar** -muestra el destino de la Segue como una ventana no modal. Por ejemplo, use este tipo de Segue para presentar otra instancia de una ventana de documento en la aplicación.
- **Modal** -presenta el destino de la Segue como una ventana modal. Por ejemplo, use este tipo de Segue para presentar la ventana de preferencias para la aplicación.
- **Hoja de** -presenta el destino de la Segue como una hoja asociada a la ventana primaria. Por ejemplo, utilice este tipo de desplazará tranquilamente para presentar un buscar y reemplazar hoja.
- **Popover** -presenta el destino de la Segue como en una ventana de popover. Por ejemplo, puede usar este tipo de Segue para presentar opciones cuando se hace clic en un elemento de interfaz de usuario por el usuario.
- **Personalizado** -presenta el destino de la Segue mediante desplazará tranquilamente tipo personalizado definido por el desarrollador. Consulte la [crear personalizado Segues](#Creating-Custom-Segues) sección para obtener más detalles.

Al utilizar Segues de presentación, puede invalidar el `PrepareForSegue` método del controlador de vista de elemento primario para su presentación para inicializar y las variables y proporcionar los datos a la controladora de vista que se presentan.

<a name="Triggered-Segues" />

### <a name="triggered-segues"></a>Desencadena Segues

Segues desencadenadas le permiten especificar Segues con nombre (a través de sus **identificador** propiedad en el generador de interfaz) y pídale que desencadenada por eventos, como el usuario hace clic en un botón o mediante una llamada a la `PerformSegue` método en el código:

```csharp
// Display the Scene defined by the given Segue ID
PerformSegue("MyNamedSegue", this);
``` 

El identificador desplazará tranquilamente se define dentro interfaz generador del Xcode cuando se diseña la interfaz de usuario de la aplicación:

[ ![](indepth-images/sg02.png "Escribir un desplazará tranquilamente nombre")](indepth-images/sg02.png)

En el controlador de vista que actúa como el origen de la Segue, se debe reemplazar el `PrepareForSegue` se muestra el método y realice cualquier inicialización necesaria antes de que se ejecute el Segue y el controlador de vista especificado:

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

Si lo desea, puede invalidar el `ShouldPerfromSegue` método y el control o no el Segue realmente se ejecuta a través de código de C#. Para ver controladores de presentado manualmente, llame a su `DismissController` método para quitarlos de la presentación cuando ya no son necesarios.

<a name="Creating-Custom-Segues" />

### <a name="creating-custom-segues"></a>Crear personalizado Segues

Puede haber ocasiones cuando la aplicación requiere un tipo de Segue no proporcionado por el Segues de compilación definidas en macOS. Si este es el caso, puede crear un desplazará personalizado y tranquilamente que se pueden asignar en el generador de interfaz de Xcode cuando diseña la interfaz de usuario de la aplicación.

Por ejemplo, para crear un nuevo tipo de Segue que reemplaza el controlador de vista actual dentro de una ventana (en lugar de abrir el destino de escena en una nueva ventana), podemos utilizar el siguiente código:

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

Un par de cosas que destacar aquí:

- Usamos el `Register` atributo para exponer esta clase a objetivo-C/macOS.
- Nos vamos a reemplazar el `Perform` método para llevar a cabo la acción de nuestro Segue personalizado.
- Sustituiremos la ventana `ContentViewController` controlador con el definido por el destino (destino) de la Segue.
- Estamos quitando el controlador de vista original para liberar la memoria con el `RemoveFromParentViewController` método.

Para usar este nuevo tipo de Segue en el generador de interfaz de Xcode, es necesario compilar la aplicación en primer lugar, a continuación, cambie a Xcode y agregue un nuevo Segue entre dos escenas. Establecer el **estilo** a **personalizado** y **desplazará tranquilamente clase** a `ReplaceViewSegue` (el nombre de la clase personalizada de Segue):

[ ![](indepth-images/sg01.png "Configuración de la clase Segue")](indepth-images/sg01.png)

<a name="Triggered-Segues" />

## <a name="window-controllers"></a>Controladores de ventana

Controladores de ventana contienen y controlan los distintos tipos de ventana que puede crear la aplicación de Mac OS. Para los guiones gráficos, tienen las siguientes características:

1. Debe proporcionar un controlador de vista de contenido. Esto será el mismo controlador de vista de contenido que tiene el elemento secundario de ventana.
2. El `Storyboard` propiedad contendrá el guión gráfico que se cargó el controlador de ventana, else `null` si no se carga desde un guión gráfico.
3. Puede llamar a la `DismissController` método para cerrar la ventana especificada y quitarlo de la vista.

Al igual que los controladores de la vista, implementar controladores de ventana el `PerformSegue`, `PrepareForSegue` y el `ShouldPerfromSegue` métodos y puede utilizarse como el origen de una operación de Segue.

Controlador de la ventana son responsables de las siguientes características de una aplicación de Mac OS:

- Administran a una ventana específica.
- Administrar la ventana de la barra de título y barra de herramientas (si está disponible).
- Administran el controlador de vista de contenido para mostrar el contenido de la ventana.

<a name="Gesture-Recognizers" />

## <a name="gesture-recognizers"></a>Identificadores de gestos

Identificadores de gestos para macOS son casi idénticas a sus homólogos en iOS y permiten al desarrollador agregar fácilmente movimientos (por ejemplo, al hacer clic en un botón del mouse) a los elementos de interfaz de usuario de la aplicación.

Sin embargo, donde gestos en iOS se determinan por cuestiones de diseño de la aplicación (por ejemplo, al puntear en la pantalla con dos dedos), más movimientos en macOS dependen de hardware.

Mediante el uso de identificadores de gestos, puede reducir considerablemente la cantidad de código necesario para agregar interacciones personalizadas a un elemento en la interfaz de usuario. Tal y como se puede determinar automáticamente entre los clics de dobles y simples, haga clic y arrastre los eventos, etcetera.

En lugar de reemplazar el `MouseDown` evento en el controlador de vista, debe usar el reconocedor de gestos para controlar el evento de entrada de usuario cuando se trabaja con guiones gráficos.

Los siguientes identificadores de movimiento están disponibles en Mac OS:

- `NSClickGestureRecognizer` -Registrar del mouse hacia abajo y eventos.
- `NSPanGestureRecognizer` -Registros de presionar el botón del mouse, arrastre y suelte eventos.
- `NSPressGestureRecognizer` -Registra presionado un botón del mouse durante un período determinado de evento de tiempo.
- `NSMagnificationGestureRecognizer` : Registra un evento de ampliación del hardware del panel de seguimiento.
- `NSRotationGestureRecognizer` : Registra un evento de rotación del hardware del panel de seguimiento.

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>Usar referencias de guión gráfico

Una referencia de guión gráfico le permite tomar un diseño de guión gráfico grande y complejo y dividirla en guiones gráficos más pequeños que obtengan referencia de los originales, por lo tanto quitar quitar complejidad y realizar el individuales guiones gráficos más fácilmente al diseño y mantener.

Además, puede proporcionar una referencia de guión gráfico una _delimitador_ a otra escena en el mismo guión gráfico o una escena específica en uno diferente.

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>Hacer referencia a un guión gráfico externo

Para agregar una referencia a un guión gráfico externo, haga lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en el nombre del proyecto y seleccione **agregar** > **nuevo archivo...**   >  **Mac** > **guión gráfico**. Escriba un **nombre** para el nuevo guión gráfico y haga clic en el **New** botón: 

    [ ![](indepth-images/ref01.png "Agregar un nuevo guión gráfico")](indepth-images/ref01.png)
2. En el **el Explorador de soluciones**, haga doble clic en el nuevo nombre de guión gráfico para abrirlo y editarlo en el generador de interfaz de Xcode.
2. Definir el diseño de escenas del guión gráfico nuevo como se haría normalmente y guardar los cambios: 

    [ ![](indepth-images/ref02.png "Diseñar la interfaz")](indepth-images/ref02.png)
3. Cambie al guión gráfico que se va a agregar la referencia en el generador de interfaz.
4. Arrastre un **referencia de guión gráfico** desde el **objeto biblioteca** a la superficie de diseño: 

    [ ![](indepth-images/ref03.png "Al seleccionar una referencia de guión gráfico en la biblioteca")](indepth-images/ref03.png)
5. En el **atributo Inspector**, seleccione el nombre de la **guión gráfico** que haya creado anteriormente: 

    [ ![](indepth-images/ref04.png "Configuración de la referencia")](indepth-images/ref04.png)
6. Control y haga clic en un Widget de interfaz de usuario (por ejemplo, un botón) en una escena existente y crear un nuevo Segue a la **referencia de guión gráfico** que acaba de crear.  En el menú emergente seleccione **mostrar** para completar la Segue: 

    [ ![](indepth-images/ref06.png "Establecer el tipo de Segue")](indepth-images/ref06.png) 
8. Guarde los cambios en el guión gráfico.
9. Vuelva a Visual Studio para Mac sincronizar los cambios.

Se mostrará cuando se ejecuta la aplicación y el usuario hace clic en el elemento de interfaz de usuario que creó el Segue desde el controlador de ventana inicial desde el guión gráfico externo especificado en la referencia de guión gráfico.

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>Hacer referencia a una escena específica en un guión gráfico externo

Para agregar una referencia a una escena específica un guión gráfico externo (y no el controlador de ventana inicial), haga lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en el guión gráfico externo para abrirlo y editarlo en el generador de interfaz de Xcode.
2. Agregar una nueva escena y se diseñan como lo haría normalmente: 

    [ ![](indepth-images/ref07.png "Definir el diseño en Xcode.")](indepth-images/ref07.png)
3. En el **identidad Inspector**, escriba un **Id. de guión gráfico** para controlador de ventana de la escena nueva: 

    [ ![](indepth-images/ref08.png "Establecer el identificador de guión gráfico")](indepth-images/ref08.png)
3. Abra el guión gráfico que se va a agregar la referencia en el generador de interfaz.
4. Arrastre un **referencia de guión gráfico** desde el **objeto biblioteca** a la superficie de diseño: 

    [ ![](indepth-images/ref03.png "Seleccionar una referencia de guión gráfico de la biblioteca")](indepth-images/ref03.png)
5. En el **identidad Inspector**, seleccione el nombre de la **guión gráfico** y **identificador de referencia** (Id. de guión gráfico) de la escena que haya creado anteriormente: 

    [ ![](indepth-images/ref09.png "Establecer el identificador de referencia")](indepth-images/ref09.png)
6. Control y haga clic en un Widget de interfaz de usuario (por ejemplo, un botón) en una escena existente y crear un nuevo Segue a la **referencia de guión gráfico** que acaba de crear. En el menú emergente seleccione **mostrar** para completar la Segue: 

    [ ![](indepth-images/ref06.png "Establecer el tipo de Segue")](indepth-images/ref06.png) 
8. Guarde los cambios en el guión gráfico.
9. Vuelva a Visual Studio para Mac sincronizar los cambios.

Cuando la aplicación esté ejecución y el usuario hace clic en el elemento de interfaz de usuario que crea el Segue de la escena con la determinada **Id. de guión gráfico** desde el guión gráfico externo especificado en la referencia del guión gráfico se mostrará.

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>Hacer referencia a una escena específica en el mismo guión gráfico

Para agregar una referencia a una escena específica el mismo guión gráfico, haga lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en el guión gráfico para abrirlo y editarlo.
2. Agregar una nueva escena y se diseñan como lo haría normalmente: 

    [ ![](indepth-images/ref11.png "Editar el guión gráfico en Xcode.")](indepth-images/ref11.png)
3. En el **identidad Inspector**, escriba un **Id. de guión gráfico** para controlador de ventana de la escena nueva: 

    [ ![](indepth-images/ref12.png "Establecer el identificador de guión gráfico")](indepth-images/ref12.png)
3. Arrastre un **referencia de guión gráfico** desde el **cuadro de herramientas** a la superficie de diseño: 

    [ ![](indepth-images/ref03.png "Seleccionar una referencia de guión gráfico de la biblioteca")](indepth-images/ref03.png)
5. En **atributo Inspector**, seleccione **identificador de referencia** (Id. de guión gráfico) de la escena que haya creado anteriormente: 

    [ ![](indepth-images/ref13.png "Establecer el identificador de referencia")](indepth-images/ref13.png)
6. Control y haga clic en un Widget de interfaz de usuario (por ejemplo, un botón) en una escena existente y crear un nuevo Segue a la **referencia de guión gráfico** que acaba de crear. En el menú emergente seleccione **mostrar** para completar la Segue: 

    [ ![](indepth-images/ref06.png "Seleccionar el tipo de Segue")](indepth-images/ref06.png) 
8. Guarde los cambios en el guión gráfico.
9. Vuelva a Visual Studio para Mac sincronizar los cambios.

Cuando la aplicación esté ejecución y el usuario hace clic en el elemento de interfaz de usuario que crea el Segue de la escena con la determinada **Id. de guión gráfico** en el mismo guión gráfico especificado en la referencia del guión gráfico se mostrará.

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>Ejemplo de guión gráfico complejo

Para obtener un ejemplo complejo de trabajar con guiones gráficos en una aplicación Xamarin.Mac, consulte el [aplicación de ejemplo de SourceWriter](https://developer.xamarin.com/samples/mac/SourceWriter/). SourceWriter es un editor de código fuente simple que proporciona compatibilidad con la finalización de código y el resaltado de sintaxis simple.

El código de SourceWriter se ha comentado completamente y, si están disponibles, se han proporcionado vínculos de métodos o tecnologías clave a información relevante en la documentación de las guías de Xamarin.Mac.

## <a name="related-links"></a>Vínculos relacionados

- [MacStoryboard (ejemplo)](https://developer.xamarin.com/samples/mac/MacStoryboard/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabajar con ventanas](~/mac/user-interface/window.md)
- [Directrices de interfaz de sistema operativo X humanos](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introducción a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
