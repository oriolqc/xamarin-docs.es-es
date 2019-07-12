---
title: Hola, Guía de inicio rápido de tvOS
description: Esta guía se describe cómo crear su primera aplicación Xamarin.tvOS y su cadena de herramientas de desarrollo. También presenta al diseñador de Xamarin, que expone controles de interfaz de usuario al código y se muestra cómo compilar, ejecutar y probar una aplicación Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: 6E0AFE58-A13B-492F-861E-D5D73EB1C4A3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 02/02/2018
ms.openlocfilehash: 6b404d7c27fba38c53ff07207003849445d51118
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832480"
---
# <a name="hello-tvos-quick-start-guide"></a>Hola, Guía de inicio rápido de tvOS

_Esta guía se describe cómo crear su primera aplicación Xamarin.tvOS y su cadena de herramientas de desarrollo. También presenta al diseñador de Xamarin, que expone controles de interfaz de usuario al código y se muestra cómo compilar, ejecutar y probar una aplicación Xamarin.tvOS._

Apple ha lanzado la generación del 5 de Apple TV, Apple TV 4K, que se ejecuta tvOS 11.

La plataforma de Apple TV está abierta a los desarrolladores, permitiéndoles crear aplicaciones sofisticadas y envolventes y liberarlos a través el Store de Apple TV integrada aplicación.

Si está familiarizado con el desarrollo de Xamarin.iOS, debería encontrar la transición a tvOS bastante simple. La mayoría de las API y características es los mismos, sin embargo, muchas API comunes están disponibles (por ejemplo, WebKit). Además, trabajar con el con el elemento remoto Siri plantea algunos desafíos de diseño que no están presentes en los dispositivos de pantalla táctil en función de iOS.

Esta guía le proporcionará una introducción a trabajar con tvOS en una aplicación Xamarin. Para obtener más información sobre tvOS, consulte Apple [Prepárese para Apple TV 4K](https://developer.apple.com/tvos/) documentación.

## <a name="overview"></a>Información general

Xamarin.tvOS le permite desarrollar aplicaciones de Apple TV completamente nativas en C# y .NET con las mismas bibliotecas de OS X y los controles de interfaz que se usan al desarrollar en *Swift* (o *Objective-C*) y  *Xcode*.

Además, desde Xamarin.tvOS las aplicaciones se escriben en C# y se puede compartir código común, de back-end. NET, con aplicaciones de Xamarin.iOS, Xamarin.Android y Xamarin.Mac; al mismo tiempo que ofrece una experiencia nativa en cada plataforma.

En este artículo se presentan los conceptos básicos necesarios para crear una aplicación de TV de Apple con Xamarin.tvOS y Visual Studio, le guía a través del proceso de creación básico **Hello, tvOS** tiene un botón de aplicación que cuente el número de veces se ha hecho clic:

[![](hello-tvos-images/run05.png "Ejecución de la aplicación de ejemplo")](hello-tvos-images/run05.png#lightbox)

Hablaremos sobre los conceptos siguientes:

-  **Visual Studio para Mac** : Introducción a Visual Studio para Mac y cómo crear aplicaciones Xamarin.tvOS con él.
-  **Anatomía de una App Xamarin.tvOS** – Xamarin.tvOS unos qué aplicación está compuesta por.
-  **Creación de una interfaz de usuario** : cómo usar al diseñador de Xamarin para iOS para crear una interfaz de usuario.
-  **Implementación y pruebas** : cómo ejecutar y probar la aplicación en el simulador de tvOS y en hardware real tvOS.

## <a name="starting-a-new-xamarintvos-app-in-visual-studio-for-mac"></a>A partir de un nuevo App Xamarin.tvOS Visual Studio para Mac

Como se indicó anteriormente, vamos a crear una aplicación de Apple TV llamada `Hello-tvOS` que agrega un solo botón y una etiqueta a la pantalla principal. Cuando se hace clic en el botón, la etiqueta mostrará el número de veces que se ha hecho clic en él.

Para empezar, vamos a hacer lo siguiente:

1. Inicie Visual Studio para Mac:

    [![](hello-tvos-images/setup01.png "Visual Studio para Mac")](hello-tvos-images/setup01.png#lightbox)
2. Haga clic en el **nueva solución...**  vínculo en la esquina superior izquierda de la pantalla para abrir el **nuevo proyecto** cuadro de diálogo.
3. Seleccione **tvOS** > **aplicación** > **aplicación de vista única** y haga clic en el **siguiente** botón:

    [![](hello-tvos-images/setup02.png "Seleccione la aplicación de una vista")](hello-tvos-images/setup02.png#lightbox)
4. Escriba `Hello, tvOS` para el **nombre de la aplicación**, escriba su **identificador de la organización** y haga clic en el **siguiente** botón:

    [![](hello-tvos-images/setup04.png "Escriba Hello, tvOS")](hello-tvos-images/setup04.png#lightbox)
5. Escriba `Hello_tvOS` para el **nombre del proyecto** y haga clic en el **crear** botón:

    [![](hello-tvos-images/setup03.png "Escriba HellotvOS")](hello-tvos-images/setup03.png#lightbox)

Visual Studio para Mac creará la nueva aplicación Xamarin.tvOS y mostrará los archivos predeterminados que se agregan a la solución de la aplicación:

 [![](hello-tvos-images/project01.png "La vista de archivos predeterminado")](hello-tvos-images/project01.png#lightbox)

Visual Studio para Mac usa **soluciones** y **proyectos**, de la misma manera que Visual Studio. Una solución es un contenedor que puede contener uno o varios proyectos; los proyectos pueden incluir aplicaciones, bibliotecas auxiliares, aplicaciones de prueba, etc. En este caso, Visual Studio para Mac ha creado una solución y un proyecto de aplicación por usted.

Si deseara, podría crear código de uno o varios proyectos de biblioteca que contengan código común y compartido. Estos proyectos de biblioteca se pueden utilizados por el proyecto de aplicación o compartir con otros proyectos de aplicación Xamarin.tvOS (o Xamarin.iOS, Xamarin.Android y Xamarin.Mac según el tipo de código), como lo haría si estuviese compilando una aplicación de .NET estándar.

## <a name="anatomy-of-a-xamarintvos-app"></a>Anatomía de una aplicación Xamarin.tvOS

Si está familiarizado con la programación de iOS, observará muchas semejanzas aquí. De hecho, tvOS 9 es un subconjunto de iOS 9, por lo que muchos de los conceptos se cruzan aquí.

Echemos un vistazo a los archivos en el proyecto:

-   `Main.cs`: contiene el punto de entrada principal de la aplicación. Cuando se inicia la aplicación, contiene la primera clase y método que se ejecutan.
-   `AppDelegate.cs` : Este archivo contiene la clase de aplicación principal que es responsable de escuchar los eventos del sistema operativo.
-   `Info.plist` : Este archivo contiene las propiedades de la aplicación, como el nombre de la aplicación, iconos, etcetera.
-   `ViewController.cs` : Es la clase que representa la ventana principal y controla el ciclo de vida del mismo.
-   `ViewController.designer.cs` : Este archivo contiene código estructural que ayuda a integrar con la interfaz de usuario de la pantalla principal.
-  `Main.storyboard` : La interfaz de usuario de la ventana principal. Este archivo se puede crear y mantenido por el Diseñador de Xamarin para iOS.

En las secciones siguientes, echaremos un vistazo rápido a través de algunos de estos archivos. Exploraremos ellos con más detalle más adelante, pero es una buena idea entender sus conceptos básicos ahora.

### <a name="maincs"></a>Main.cs

El `Main.cs` archivo contiene una variable static `Main` método que crea una nueva instancia de la aplicación Xamarin.tvOS y pasa el nombre de la clase que controla los eventos del sistema operativo, que en nuestro caso es el `AppDelegate` clase:

```csharp
using UIKit;

namespace Hello_tvOS
{
    public class Application
    {
        // This is the main entry point of the application.
        static void Main (string[] args)
        {
            // if you want to use a different Application Delegate class from "AppDelegate"
            // you can specify it here.
            UIApplication.Main (args, null, "AppDelegate");
        }
    }
}
```

### <a name="appdelegatecs"></a>AppDelegate.cs

El `AppDelegate.cs` archivo contiene nuestra `AppDelegate` (clase), que es responsable de crear la ventana y escuchar los eventos del sistema operativo:

```csharp
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    // The UIApplicationDelegate for the application. This class is responsible for launching the
    // User Interface of the application, as well as listening (and optionally responding) to application events from iOS.
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        // class-level declarations

        public override UIWindow Window {
            get;
            set;
        }

        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Override point for customization after application launch.
            // If not required for your application you can safely delete this method

            return true;
        }

        public override void OnResignActivation (UIApplication application)
        {
            // Invoked when the application is about to move from active to inactive state.
            // This can occur for certain types of temporary interruptions (such as an incoming phone call or SMS message)
            // or when the user quits the application and it begins the transition to the background state.
            // Games should use this method to pause the game.
        }

        public override void DidEnterBackground (UIApplication application)
        {
            // Use this method to release shared resources, save user data, invalidate timers and store the application state.
            // If your application supports background execution this method is called instead of WillTerminate when the user quits.
        }

        public override void WillEnterForeground (UIApplication application)
        {
            // Called as part of the transition from background to active state.
            // Here you can undo many of the changes made on entering the background.
        }

        public override void OnActivated (UIApplication application)
        {
            // Restart any tasks that were paused (or not yet started) while the application was inactive.
            // If the application was previously in the background, optionally refresh the user interface.
        }

        public override void WillTerminate (UIApplication application)
        {
            // Called when the application is about to terminate. Save data, if needed. See also DidEnterBackground.
        }
    }
}
```

Este código es probablemente familiarizado, a menos que se ha creado una aplicación de iOS antes, pero es bastante sencillo. Vamos a examinar las líneas importantes.

En primer lugar, echemos un vistazo a la declaración de variable de nivel de clase:

```csharp
public override UIWindow Window {
            get;
            set;
        }

```

El `Window` propiedad proporciona acceso a la ventana principal. tvOS usa lo que se conoce como el *Model View Controller* modelo (MVC). Por lo general, para cada ventana que se crea (y para muchas otras cosas dentro de windows), hay un controlador, que es responsable del ciclo de vida de la ventana, como mostrarla, agregarle nuevas vistas (controles), etcetera.

A continuación, tenemos la `FinishedLaunching` método. Este método se ejecuta después de que se ha creado una instancia de la aplicación y es responsable para realmente crear la ventana de la aplicación y comenzar el proceso de mostrar la vista en ella. Dado que nuestra aplicación usa un guión gráfico para definir su interfaz de usuario, aquí se requiere ningún código adicional.

Existen muchos otros métodos que se proporcionan en la plantilla como `DidEnterBackground` y `WillEnterForeground`. Se pueden eliminar con seguridad si no se utilizan los eventos de aplicación en la aplicación.

### <a name="viewcontrollercs"></a>ViewController.cs

La `ViewController` clase es el controlador de la ventana principal. Eso significa que es responsable del ciclo de vida de la ventana principal. Vamos para examinar esto con detalle más adelante, por ahora simplemente Echemos un vistazo rápido a él:

```csharp
using System;
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    public partial class ViewController : UIViewController
    {
        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
    }
}
```

#### <a name="viewcontrollerdesignercs"></a>ViewController.Designer.cs

El archivo del Diseñador de la clase de ventana principal ahora está vacío, pero se rellenará automáticamente por Visual Studio para Mac como creamos nuestra interfaz de usuario con el Diseñador de iOS:

```csharp
using Foundation;

namespace HellotvOS
{
    [Register ("ViewController")]
    partial class ViewController
    {
        void ReleaseDesignerOutlets ()
        {
        }
    }
}
```

Hemos normalmente no relacionadas con los archivos de diseñador, ya que solo estén administrados por Visual Studio para Mac y basta con que proporcione el código estructural necesario que permita el acceso a los controles que agregue a cualquier ventana o ver en nuestra aplicación.

Ahora que hemos creado nuestra aplicación Xamarin.tvOS y tenemos un conocimiento básico de sus componentes, echemos un vistazo a la creación de la interfaz de usuario.

<a name="Creating-the-User-Interface" />

## <a name="creating-the-user-interface"></a>Crear la interfaz de usuario

No es necesario utilizar el Diseñador de Xamarin para iOS para crear la interfaz de usuario para la aplicación Xamarin.tvOS, la interfaz de usuario se puede crear directamente desde C# código pero que está fuera del ámbito de este artículo. Por simplicidad, vamos a usar el Diseñador de iOS para crear la interfaz de usuario en el resto de este tutorial.

Para empezar a crear la interfaz de usuario, vamos a haga doble clic en el `Main.storyboard` de archivos en el **el Explorador de soluciones** para abrirlo y editarlo en el Diseñador de iOS:

[![](hello-tvos-images/designer01.png "Archivo Main.storyboard en el Explorador de soluciones")](hello-tvos-images/designer01.png#lightbox)

Esto debería iniciar el diseñador y el siguiente aspecto:

[![](hello-tvos-images/designer02.png "El diseñador")](hello-tvos-images/designer02.png#lightbox)

Para obtener más información sobre el Diseñador de iOS y su funcionamiento, consulte el [Introducción al diseñador de Xamarin para iOS](~/ios/user-interface/designer/introduction.md) guía.

Ahora podemos comenzar a agregar controles a la superficie de diseño de nuestra aplicación Xamarin.tvOS.

Haga lo siguiente:

1. Busque el **cuadro de herramientas**, que debe estar a la derecha de la superficie de diseño:

    [![](hello-tvos-images/designer03.png "El cuadro de herramientas")](hello-tvos-images/designer03.png#lightbox)

    Si no puede encontrar aquí, vaya a **Vista > paneles > cuadro de herramientas** para verlo.
2. Arrastre un **etiqueta** desde el **cuadro de herramientas** a la superficie de diseño:

    [![](hello-tvos-images/designer04.png "Arrastre una etiqueta desde el cuadro de herramientas")](hello-tvos-images/designer04.png#lightbox)
3. Haga clic en el **título** propiedad en el **panel propiedad** y cambie el título del botón a `Hello, tvOS` y establezca el **Font Size** a 128:

    [![](hello-tvos-images/designer05.png "Establece el título a Hello, tvOS y establece el tamaño de fuente en 128")](hello-tvos-images/designer05.png#lightbox)
4. Cambiar el tamaño de la etiqueta para que todas las palabras están visibles y colocan centrado en la parte superior de la ventana:

    [![](hello-tvos-images/designer06.png "Cambiar el tamaño y del centro de la etiqueta")](hello-tvos-images/designer06.png#lightbox)
5. La etiqueta ahora deberá estar restringido a su posición, para que aparezca según lo previsto. independientemente del tamaño de la pantalla. Para ello, haga clic en la etiqueta hasta la *indicador con forma de T* aparece:

    [![](hello-tvos-images/designer07.png "El indicador con forma de T")](hello-tvos-images/designer07.png#lightbox)
6. Para restringir la etiqueta horizontalmente, seleccione el cuadrado central y arrástrelo a la línea vertical discontinua:

    [![](hello-tvos-images/designer08.png "Seleccione el cuadrado central")](hello-tvos-images/designer08zoom.png#lightbox)

     La etiqueta debería cambiar a naranja.
7. Seleccione el identificador de T en la parte superior de la etiqueta y arrástrelo hasta el borde superior de la ventana:

    [![](hello-tvos-images/designer09.png "Arrastre el borde superior de la ventana")](hello-tvos-images/designer09.png#lightbox)
8. A continuación, haga clic en el ancho y, a continuación, el alto *identificador ósea* tal como se muestra a continuación:

    [![](hello-tvos-images/designer10.png "El ancho y alto ósea identificadores")](hello-tvos-images/designer10.png#lightbox)

     Cuando cada *identificador ósea* es hacer clic en, seleccione ancho y alto respectivamente para establecer las dimensiones fijas.
9. Cuando haya completado, un aspecto similares a los de la pestaña diseño del panel de propiedades de las restricciones:

    [![](hello-tvos-images/designer11.png "Restricciones de ejemplo")](hello-tvos-images/designer11.png#lightbox)
8. Arrastre un **botón** desde el **cuadro de herramientas** y colóquelo debajo de la etiqueta.
9. Haga clic en el **título** propiedad en el **panel propiedad** y cambie el título del botón a `Click Me`:

    [![](hello-tvos-images/designer12.png "Cambie el título de botones, haga clic en Me")](hello-tvos-images/designer12.png#lightbox)
10. Repita los pasos 5 a 8 anteriores para restringir el botón en la ventana de tvOS. Sin embargo, en lugar de arrastrar el controlador de T a la parte superior de la ventana (como en el paso #7), arrástrelo hasta la parte inferior de la etiqueta:

    [![](hello-tvos-images/designer14.png "Restringir el botón")](hello-tvos-images/designer14.png#lightbox)
11. Arrastre otra etiqueta en el botón, el tamaño para que sea el mismo ancho que la primera etiqueta y establezca su **alineación** a **Center**:

    [![](hello-tvos-images/designer15.png "Arrastre otra etiqueta debajo del botón, de tamaño para ser el mismo ancho que la primera etiqueta y establezca su alineación al centro")](hello-tvos-images/designer15.png#lightbox)
12. Al igual que la primera etiqueta y el botón, establezca esta etiqueta para centrar y anclarlo a la ubicación y el tamaño:

    [![](hello-tvos-images/designer16.png "Anclar la etiqueta a la ubicación y tamaño")](hello-tvos-images/designer16.png#lightbox)
13. Guardar los cambios en la interfaz de usuario.

Tal como eran el cambio de tamaño y mover controles, se debe que haya observado que el diseñador le ofrece sugerencias de ajuste útiles basadas en [directrices de interfaz humana de Apple TV](https://developer.apple.com/tvos/human-interface-guidelines/). Estas instrucciones le ayudará a crear aplicaciones de alta calidad que tendrán una apariencia familiar para los usuarios de Apple TV.

Si observa la **esquema del documento** sección, tenga en cuenta cómo se muestran el diseño y la jerarquía de los elementos que componen la interfaz de usuario:

[![](hello-tvos-images/designer17.png "La sección de esquema del documento")](hello-tvos-images/designer17.png#lightbox)

Desde aquí puede seleccionar elementos para editarlos o arrastrarlos para volver a ordenar los elementos de interfaz de usuario si es necesario. Por ejemplo, si un elemento de interfaz de usuario que se estaba cubierto por otro elemento, podría arrastrarlo a la parte inferior de la lista para convertirlo en el elemento superior en la ventana.

Ahora que tenemos nuestra interfaz de usuario creada, es necesario exponer los elementos de interfaz de usuario, por lo que puede obtener acceso e interactuar con ellos en Xamarin.tvOS C# código.

### <a name="accessing-the-controls-in-the-code-behind"></a>Obtener acceso a los controles en el código subyacente

Hay dos formas principales para tener acceso a los controles que agregó en el Diseñador de iOS desde el código:

* Creación de un controlador de eventos en un control.
* Lo que proporciona el control de un nombre, por lo que nos podemos consultarla más tarde.

Cuando cualquiera de estos se agregan, la clase parcial dentro de la `ViewController.designer.cs` se actualizará para reflejar los cambios. Esto le permitirá posteriormente tener acceso a los controles en el controlador de vista.

### <a name="creating-an-event-handler"></a>Creación de un controlador de eventos

En esta aplicación de ejemplo, cuando se hace clic en el botón queremos _algo_ a ocurrir, por lo que un controlador de eventos debe agregarse a un evento concreto en el botón. Para configurar esta opción, haga lo siguiente:

1. En el Diseñador de Xamarin iOS, seleccione el botón en el controlador de vista.
2. En el panel de propiedades, seleccione la **eventos** pestaña:

    [![](hello-tvos-images/event1.png "La ficha de eventos")](hello-tvos-images/event1.png#lightbox)
3. Busque el evento TouchUpInside y asígnele un controlador de eventos denominado `Clicked`:

    [![](hello-tvos-images/event2.png "El evento TouchUpInside")](hello-tvos-images/event2.png#lightbox)
4. Al presionar **ENTRAR**, **ViewController**se abrirá el archivo. cs, sugerir las ubicaciones para el controlador de eventos en el código. Use las teclas de flecha del teclado para establecer la ubicación:

    [![](hello-tvos-images/event3.png "Configuración de la ubicación")](hello-tvos-images/event3.png#lightbox)
5. Esto creará un método parcial, tal como se muestra a continuación:

    [![](hello-tvos-images/event4.png "El método parcial")](hello-tvos-images/event4.png#lightbox)

Ahora estamos listos empezar a agregar algo de código para permitir que el botón de función.

### <a name="naming-a-control"></a>Un control de nomenclatura

Cuando se hace clic en el botón, debe actualizar la etiqueta en función del número de clics. Para ello, necesitamos tener acceso a la etiqueta en el código. Para ello, dándole un nombre. Haga lo siguiente:

1. Abra el guion gráfico y seleccione la etiqueta en la parte inferior del controlador de vista.
2. En el panel de propiedades, seleccione la **Widget** pestaña:

    [![](hello-tvos-images/name1.png "Seleccione la pestaña de Widget")](hello-tvos-images/name1.png#lightbox)
3. En **identidad > nombre**, agregar `ClickedLabel`:

    [![](hello-tvos-images/name2.png "Establecer ClickedLabel")](hello-tvos-images/name2.png#lightbox)

Ahora estamos preparados para iniciar la actualización de la etiqueta.

### <a name="how-controls-are-accessed"></a>¿Cómo se obtiene acceso a los controles

Si selecciona el `ViewController.designer.cs` en el **el Explorador de soluciones** , podrá ver cómo el `ClickedLabel` etiqueta y el `Clicked` controlador de eventos se han asignado a un **toma** y **Acción** en C#:

[![](hello-tvos-images/accesscontrol.png "Salidas y acciones")](hello-tvos-images/accesscontrol.png#lightbox)

También puede observar que `ViewController.designer.cs` es una clase parcial, para que Visual Studio para Mac no tiene que modificar `ViewController.cs` que sobrescribiría los cambios que hemos realizado en la clase.

Exponer los elementos de interfaz de usuario de este modo, le permite tener acceso a ellos en el controlador de vista.

Normalmente nunca tendrá que abrir el `ViewController.designer.cs` usted mismo, se ha presentado aquí solo con fines educativos.

<a name="Writing-the-Code" />

## <a name="writing-the-code"></a>Escritura del código

Con nuestra interfaz de usuario creada y sus elementos de interfaz de usuario expuestos al código mediante **salidas** y **acciones**, estamos preparados para escribir el código para asignar a la funcionalidad del programa.

En nuestra aplicación, cada vez que se hace clic en el primer botón, vamos a actualizar la etiqueta para mostrar cuántas veces se ha hecho clic el botón. Para lograr esto, es necesario abrir el `ViewController.cs` archivo para modificarlo, haga doble clic en el **panel de solución**:

[![](hello-tvos-images/code01.png "El panel de solución")](hello-tvos-images/code01.png#lightbox)

En primer lugar, necesitamos crear una variable de nivel de clase en nuestra `ViewController` clase para realizar un seguimiento del número de clics que se han producido. Edite la definición de clase y dele un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    public partial class ViewController : UIViewController
    {
        private int numberOfTimesClicked = 0;
        ...
```

A continuación, en la misma clase (`ViewController`), es necesario reemplazar el **ViewDidLoad** método y agregue código para establecer el mensaje inicial de la etiqueta:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Set the initial value for the label
    ClickedLabel.Text = "Button has not been clicked yet.";
}
```

Debemos usar `ViewDidLoad`, en lugar de otro método como `Initialize`, ya que `ViewDidLoad` se denomina *después* haya cargado el sistema operativo y se crea una instancia de la interfaz de usuario desde el `.storyboard` archivo. Si se ha intentado obtener acceso al control de etiqueta antes de la `.storyboard` archivo ha sido totalmente cargado y crea una instancia, obtendríamos un `NullReferenceException` error porque todavía no se crearía el control de etiqueta.

A continuación, necesitamos agregar el código para responder al usuario hacer clic en el botón. Agregue lo siguiente al parcial de clase para que se ha creado:

```csharp
partial void Clicked (UIButton sender)
{
    ClickedLabel.Text = string.Format("The button has been clicked {0} time{1}.", ++numberOfTimesClicked, (numberOfTimesClicked
```

Este código se llamará siempre que el usuario hace clic en el botón.

Con todo lo que en su lugar, ahora estamos preparados compilar y probar nuestra aplicación Xamarin.tvOS.

## <a name="testing-the-application"></a>Probar la aplicación

Es el momento de compilar y ejecutar la aplicación para asegurarse de que se ejecuta según lo previsto. Podemos crear y ejecutar todo en un paso o podamos compilarlo sin ejecutarla.

Cada vez que se compila una aplicación, podemos elegir el tipo de compilación desee:

-   **Depurar** : una compilación de depuración se compila en un "archivo (aplicación) con metadatos adicionales que nos permite depurar lo que sucede mientras se ejecuta la aplicación.
-   **Versión** : una compilación de versión crea también un '' archivo, pero no incluye información de depuración, por lo que es menor y se ejecuta más rápido.  

Puede seleccionar el tipo de compilación desde el **Selector de configuración** en la esquina superior izquierda de Visual Studio para la pantalla de Mac:

[![](hello-tvos-images/run01.png "Seleccione el tipo de compilación")](hello-tvos-images/run01.png#lightbox)

### <a name="building-the-application"></a>Compilar la aplicación

En nuestro caso, nos tan solo quiere una compilación de depuración, así que vamos a Asegúrese de que **depurar** está seleccionada. Vamos a crear nuestra aplicación en primer lugar presionando **⌘B**, o desde el **compilar** menú, elija **compilar todo**.

Si habido ningún error, verá un **compilación correcta** mensaje en Visual Studio para la barra de estado del equipo Mac. Si hay algún error, revise el proyecto y asegúrese de que ha seguido los pasos correctamente. Empiece por confirmar que el código (tanto en Xcode como en Visual Studio para Mac) coincide con el código en el tutorial.

### <a name="running-the-application"></a>Ejecutar la aplicación

Para ejecutar la aplicación, tenemos tres opciones:

-  Presione **⌘+Entrar**.
-  En el menú **Ejecutar**, elija **Depurar**.
-  Haga clic en el botón **Reproducir** de la barra de herramientas de Visual Studio para Mac (justo encima del **Explorador de soluciones**).

La aplicación se compilará (si aún no se ha compilado ya), se iniciará el inicio en modo de depuración, el simulador de tvOS e iniciará la aplicación y mostrar su ventana de interfaz principal:

[![La pantalla de inicio de aplicación de ejemplo](hello-tvos-images/run03.png)](hello-tvos-images/run03.png#lightbox)

Desde el **Hardware** menú, seleccione **mostrar Apple TV remoto** para que pueda controlar el simulador.

[![](hello-tvos-images/run04.png "Seleccione Mostrar Apple TV remoto")](hello-tvos-images/run04.png#lightbox)

Uso remoto del simulador, si hace clic en el botón varias veces en la etiqueta debe actualizarse con el recuento:

[![](hello-tvos-images/run05.png "La etiqueta con un recuento actualizado")](hello-tvos-images/run05.png#lightbox)

Felicidades. Hemos abarcado mucha información en este tema, pero si ha seguido este tutorial de principio a fin, ahora debería tener un conocimiento sólido de los componentes de una aplicación de Xamarin.tvOS, así como las herramientas utilizadas para crearlos.

## <a name="where-to-next"></a>¿Qué hacer después?

Desarrollar presenta las aplicaciones de Apple TV algunos desafíos debido a la desconexión entre el usuario y la interfaz (está en el espacio no en la mano del usuario) y las limitaciones de tvOS se coloca en el almacenamiento y tamaño de la aplicación.

Como resultado, se recomienda encarecidamente que leyendo los siguientes documentan antes de pasar a un Xamarin.tvOS diseño de aplicación:

- [Introducción a tvOS 9](~/ios/tvos/platform/tvos9.md) : este artículo presentan todas las características disponibles en tvOS 9 y las API nuevas y modificadas para desarrolladores de Xamarin.tvOS.
- [Trabajo con navegación y foco](~/ios/tvos/app-fundamentals/navigation-focus.md) : usuarios de su aplicación Xamarin.tvOS no interactúa con su interfaz directamente como con iOS donde pulsar imágenes en la pantalla del dispositivo, pero indirectamente de toda la sala mediante el control remoto de Siri. Este artículo describe el concepto de foco y cómo se usa para controlar la navegación en la interfaz de usuario de una aplicación Xamarin.tvOS.
- [Siri Remote y Bluetooth controladores](~/ios/tvos/platform/remote-bluetooth.md) : la forma principal en que los usuarios será su interacción con Apple TV y la aplicación Xamarin.tvOS, es a través de Siri remoto incluido. Si la aplicación es un juego, puede, opcionalmente, crear en la compatibilidad de parte 3ª, realizados para dispositivos de juego de iOS (MFI) Bluetooth en la aplicación también. Este artículo trata sobre la compatibilidad con los nuevos dispositivos de juego de Siri Remote y Bluetooth en sus aplicaciones Xamarin.tvOS.
- [Almacenamiento de datos y recursos](~/ios/tvos/app-fundamentals/resources-data-storage.md) : a diferencia de los dispositivos iOS, el nuevo Televisor Apple no proporciona un almacenamiento persistente y local para las aplicaciones de tvOS. Como resultado, si su aplicación Xamarin.tvOS necesita conservar la información (por ejemplo, las preferencias del usuario) debe almacenar y recuperar los datos de iCloud. En este artículo se explica cómo trabajar con recursos y almacenamiento de datos persistente en una aplicación Xamarin.tvOS.
- [Trabajar con iconos e imágenes](~/ios/tvos/app-fundamentals/icons-images.md) : crear atractivas iconos e imágenes son una parte fundamental de desarrollar una experiencia de usuario amplias para las aplicaciones de Apple TV. Esta guía describen los pasos necesarios para crear e incluir los activos gráficos necesarios para las aplicaciones Xamarin.tvOS.
- [Interfaz de usuario](~/ios/tvos/user-interface/index.md) : cobertura de experiencia de usuario General (UX), incluidos los controles de interfaz de usuario (UI), usar Interface Builder de Xcode y los principios de diseño de experiencia de usuario cuando se trabaja con Xamarin.tvOS.
- [Implementación y pruebas](~/ios/tvos/deploy-test/index.md) : en esta sección se trata temas que se usa para probar una aplicación, además de cómo distribuir la aplicación. Estos temas incluyen aspectos como las herramientas usadas para la depuración, implementación en evaluadores y cómo publicar una aplicación en el Store de Apple TV App.

Si experimenta problemas al trabajar con Xamarin.tvOS, consulte nuestra [Troubleshooting](~/ios/tvos/troubleshooting.md) documentación para obtener una lista de problemas conocidos y soluciones.

## <a name="summary"></a>Resumen

En este artículo se proporciona un inicio rápido para desarrollar aplicaciones para tvOS con Visual Studio para Mac mediante la creación de un sencillo aplicación Hello, tvOS. Tratan los aspectos básicos del dispositivo de tvOS aprovisionamiento, la creación de una interfaz, de codificación para tvOS y pruebas en el simulador de tvOS.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicación de tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [Creación de aplicaciones para tvOS con Xamarin (vídeo)](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
