---
title: Hola, tvOS Guía de inicio rápido
description: Esta guía le guía por la creación de su primera aplicación Xamarin.tvOS y su cadena de herramientas de desarrollo. También presenta al diseñador de Xamarin, que expone los controles de interfaz de usuario al código y se muestra cómo crear, ejecutar y probar una aplicación Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: 6E0AFE58-A13B-492F-861E-D5D73EB1C4A3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 02/02/2018
ms.openlocfilehash: 0adf6e326dd29db15b6bd90626f424b803dc0bc9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30785323"
---
# <a name="hello-tvos-quick-start-guide"></a>Hola, tvOS Guía de inicio rápido

_Esta guía le guía por la creación de su primera aplicación Xamarin.tvOS y su cadena de herramientas de desarrollo. También presenta al diseñador de Xamarin, que expone los controles de interfaz de usuario al código y se muestra cómo crear, ejecutar y probar una aplicación Xamarin.tvOS._

Apple ha lanzado el 5 de generación de la Apple TV, Apple TV 4K, que se ejecuta tvOS 11.

La plataforma de TV de Apple está abierta a los desarrolladores, lo que les permite crear aplicaciones enriquecidas y atractiva y liberarlos a través de la tienda de aplicaciones integradas de TV de Apple.

Si está familiarizado con el desarrollo de Xamarin.iOS, debe buscar la transición a tvOS bastante simple. La mayoría de las API y características es los mismos, sin embargo, no están disponibles (por ejemplo, WebKit) muchas API comunes. Además, trabajar con el con el control remoto Siri presenta algunos desafíos de diseño que no están presentes en los dispositivos de iOS en función de pantalla táctil.

Esta guía le proporcionará una introducción a trabajar con tvOS en una aplicación de Xamarin. Para obtener más información sobre tvOS, vea de Apple [preparar para Apple TV 4K](https://developer.apple.com/tvos/) documentación.

## <a name="overview"></a>Información general

Xamarin.tvOS le permite desarrollar aplicaciones de Apple TV totalmente nativas en C# y .NET con las mismas bibliotecas de OS X y controles de interfaz que se usan para el desarrollo en *Swift* (o *Objective-C*) y *Xcode*.

Además, puesto que las aplicaciones Xamarin.tvOS están escritas en C# y. NET, es común, código de back-end puede compartirse con aplicaciones Xamarin.iOS y Xamarin.Android, Xamarin.Mac; todos los al tiempo que ofrece una experiencia nativa en cada plataforma.

En este artículo le presentará los conceptos básicos necesarios para crear una aplicación de TV de Apple con Xamarin.tvOS y Visual Studio, le guía a través del proceso de creación básico **Hola tvOS** tiene un botón de aplicación que cuente el número de veces ha hecho clic:

[![](hello-tvos-images/run05.png "Aplicación de ejemplo que se ejecute")](hello-tvos-images/run05.png#lightbox)

Hablaremos sobre los conceptos siguientes:

-  **Visual Studio para Mac** : Introducción a Visual Studio para Mac y cómo crear aplicaciones de Xamarin.tvOS con él.
-  **Anatomía de una Xamarin.tvOS App** – Xamarin.tvOS a qué aplicación está compuesta por.
-  **Crear una interfaz de usuario** : cómo usar al diseñador de Xamarin para iOS para crear una interfaz de usuario.
-  **Implementación y pruebas** : cómo ejecutar y probar la aplicación en el simulador tvOS y en hardware real tvOS.

## <a name="starting-a-new-xamarintvos-app-in-visual-studio-for-mac"></a>A partir de un nuevo App Xamarin.tvOS en Visual Studio para Mac

Como se mencionó anteriormente, vamos a crear una aplicación de Apple TV denominada `Hello-tvOS` que agrega un solo botón y una etiqueta a la pantalla principal. Cuando se hace clic en el botón, la etiqueta mostrará el número de veces que se ha hecho clic en él.

Para empezar, vamos a hacer lo siguiente:

1. Inicie Visual Studio para Mac:

    [![](hello-tvos-images/setup01.png "Visual Studio para Mac")](hello-tvos-images/setup01.png#lightbox)
2. Haga clic en el **nueva solución...**  vínculo en la esquina superior izquierda de la pantalla para abrir el **nuevo proyecto** cuadro de diálogo.
3. Seleccione **tvOS** > **aplicación** > **ver solo la aplicación** y haga clic en el **siguiente** botón:

    [![](hello-tvos-images/setup02.png "Seleccione la aplicación de la vista única")](hello-tvos-images/setup02.png#lightbox)
4. Escriba `Hello, tvOS` para el **nombre de la aplicación**, escriba su **identificador de la organización** y haga clic en el **siguiente** botón:

    [![](hello-tvos-images/setup04.png "Escriba Hola tvOS")](hello-tvos-images/setup04.png#lightbox)
5. Escriba `Hello_tvOS` para el **nombre del proyecto** y haga clic en el **crear** botón:

    [![](hello-tvos-images/setup03.png "Escriba HellotvOS")](hello-tvos-images/setup03.png#lightbox)

Visual Studio para Mac creará la nueva aplicación de Xamarin.tvOS y mostrar los archivos predeterminados que se agregan a la solución de la aplicación:

 [![](hello-tvos-images/project01.png "Ver los archivos predeterminados")](hello-tvos-images/project01.png#lightbox)

Visual Studio para usos Mac **soluciones** y **proyectos**, de la misma manera que Visual Studio realiza. Una solución es un contenedor que puede contener uno o varios proyectos; los proyectos pueden incluir aplicaciones, bibliotecas auxiliares, aplicaciones de prueba, etc. En este caso, Visual Studio para Mac ha creado una solución y un proyecto de aplicación para usted.

Si desea, puede crear código de uno o más proyectos de biblioteca que contienen código común y compartido. Estos proyectos de biblioteca pueden ser utilizados por el proyecto de aplicación o compartirse con otros proyectos de aplicación Xamarin.tvOS (o Xamarin.iOS y Xamarin.Android, Xamarin.Mac según el tipo de código), como lo haría si estuviese compilando una aplicación de .NET estándar.

## <a name="anatomy-of-a-xamarintvos-app"></a>Anatomía de una aplicación Xamarin.tvOS

Si está familiarizado con la programación de iOS, observará muchas similitudes aquí. De hecho, tvOS 9 es un subconjunto de iOS 9, por lo que muchos de los conceptos de cruce aquí.

¡Eche un vistazo a los archivos en el proyecto:

-   `Main.cs`: contiene el punto de entrada principal de la aplicación. Cuando se inicia la aplicación, contiene la primera clase y método que se ejecutan.
-   `AppDelegate.cs` – Este archivo contiene la clase de aplicación principal que es responsable de escuchar los eventos del sistema operativo.
-   `Info.plist` – Este archivo contiene propiedades de la aplicación, como el nombre de la aplicación, iconos, etcetera.
-   `ViewController.cs` – Ésta es la clase que representa la ventana principal y controla el ciclo de vida del mismo.
-   `ViewController.designer.cs` – Este archivo contiene código de establecimiento que le ayuda a integrar con la interfaz de usuario de la pantalla principal.
-  `Main.storyboard` : La interfaz de usuario para la ventana principal. Este archivo se puede crear y mantenido por el Diseñador de Xamarin para iOS.

En las siguientes secciones, echaremos un vistazo rápido a través de algunos de estos archivos. Exploraremos ellos con más detalle más adelante, pero es una buena idea para comprender los conceptos básicos de ahora.

### <a name="maincs"></a>Main.cs

El `Main.cs` archivo contiene una variable static `Main` método que crea una nueva instancia de la aplicación Xamarin.tvOS y pasa el nombre de la clase que controla los eventos de sistema operativo, que en este caso es la `AppDelegate` clase:

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

El `AppDelegate.cs` archivo contiene nuestro `AppDelegate` (clase), que es responsable de crear la ventana y escuchar los eventos de sistema operativo:

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

Este código es probablemente familiarizado, a menos que haya creado una aplicación de iOS antes, pero resulta bastante sencilla. Vamos a examinar las líneas importantes.

En primer lugar, echemos un vistazo a la declaración de variable de nivel de clase:

```csharp
public override UIWindow Window {
            get;
            set;
        }

```

El `Window` propiedad proporciona acceso a la ventana principal. tvOS utiliza lo que se conoce como el *Model View Controller* modelo (MVC). Por lo general, para cada ventana que se crea (y muchas otras cosas dentro de windows), hay un controlador, que es responsable de ciclo de vida de la ventana, como mostrarlo, agregar nuevas vistas (controles) para, etcetera.

A continuación, tenemos la `FinishedLaunching` método. Este método se ejecuta después de que se ha creado una instancia de la aplicación y es responsable de realmente crear la ventana de la aplicación y comenzar el proceso de presentación de la vista en ella. Dado que nuestra aplicación utiliza un guión gráfico para definir su interfaz de usuario, aquí se requiere ningún código adicional.

Existen muchos otros métodos que se proporcionan en la plantilla como `DidEnterBackground` y `WillEnterForeground`. Se pueden quitar sin ningún riesgo si los eventos de aplicación no se utilizan en la aplicación.

### <a name="viewcontrollercs"></a>ViewController.cs

La `ViewController` clase es controlador de la ventana principal. Eso significa que es responsable el ciclo de vida de la ventana principal. Vamos para examinarlo esto detalladamente más adelante, por ahora simplemente Echemos un vistazo:

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

El archivo del diseñador para la clase de ventana principal está vacío en este momento, pero se rellenará automáticamente con Visual Studio para Mac tal y como se cree la interfaz de usuario con el Diseñador de iOS:

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

Hemos no se refiere normalmente con archivos de diseñador, como simplemente automáticamente está administrados por Visual Studio para Mac y basta con que proporcione el código de establecimiento de requisitos que permite el acceso a los controles que agregamos a cualquier ventana o ver en nuestra aplicación.

Ahora que hemos creado nuestra aplicación Xamarin.tvOS y disponemos de un conocimiento básico de sus componentes, echemos un vistazo a la creación de la interfaz de usuario.

<a name="Creating-the-User-Interface" />

## <a name="creating-the-user-interface"></a>Crear la interfaz de usuario

No es necesario utilizar el Diseñador de Xamarin para iOS para crear la interfaz de usuario de la aplicación Xamarin.tvOS, la interfaz de usuario puede crearse directamente desde el código C# pero que queda fuera del ámbito de este artículo. Por simplicidad, utilizaremos el Diseñador de iOS para crear la interfaz de usuario en el resto de este tutorial.

Para empezar a crear la interfaz de usuario, vamos a haga doble clic en el `Main.storyboard` un archivo en el **el Explorador de soluciones** para abrirlo y editarlo en el Diseñador de iOS:

[![](hello-tvos-images/designer01.png "Archivo Main.storyboard en el Explorador de soluciones")](hello-tvos-images/designer01.png#lightbox)

Esto debería iniciar el diseñador y el siguiente aspecto:

[![](hello-tvos-images/designer02.png "El diseñador")](hello-tvos-images/designer02.png#lightbox)

Para obtener más información sobre el Diseñador de iOS y cómo funciona, consulte la [Introducción al diseñador de Xamarin para iOS](~/ios/user-interface/designer/introduction.md) guía.

Ahora podemos comenzar agregando controles a la superficie de diseño de nuestra aplicación Xamarin.tvOS.

Haga lo siguiente:

1. Busque la **cuadro de herramientas**, que debe estar a la derecha de la superficie de diseño:

    [![](hello-tvos-images/designer03.png "El cuadro de herramientas")](hello-tvos-images/designer03.png#lightbox)

    Si no se encuentra aquí, vaya a **Vista > rellena > cuadro de herramientas** para verlo.
2. Arrastre un **etiqueta** desde el **cuadro de herramientas** a la superficie de diseño:

    [![](hello-tvos-images/designer04.png "Arrastre una etiqueta desde el cuadro de herramientas")](hello-tvos-images/designer04.png#lightbox)
3. Haga clic en el **título** propiedad en el **controlador de propiedad** y cambie el título del botón a `Hello, tvOS` y establezca el **el tamaño de fuente** a 128:

    [![](hello-tvos-images/designer05.png "Establecer el título a saludos, tvOS y establezca el tamaño de fuente a 128")](hello-tvos-images/designer05.png#lightbox)
4. Cambiar el tamaño de la etiqueta para que todas las palabras están visibles y colóquelo centrado en la parte superior de la ventana:

    [![](hello-tvos-images/designer06.png "Cambiar el tamaño y la etiqueta del centro")](hello-tvos-images/designer06.png#lightbox)
5. La etiqueta ahora deberá estar restringido a su posición, para que aparezca como se espera. independientemente del tamaño de pantalla. Para ello, haga clic en la etiqueta hasta la *con forma de T* aparece:

    [![](hello-tvos-images/designer07.png "El identificador en forma de T")](hello-tvos-images/designer07.png#lightbox)
6. Para restringir la etiqueta horizontalmente, seleccione el cuadrado central y arrástrelo a la línea vertical discontinua:

    [![](hello-tvos-images/designer08.png "Seleccione el cuadrado central")](hello-tvos-images/designer08zoom.png#lightbox)

     La etiqueta debería cambiar a naranja.
7. Seleccione el identificador de T en la parte superior de la etiqueta y arrástrelo hasta el borde superior de la ventana:

    [![](hello-tvos-images/designer09.png "Arrastre el controlador hasta el borde superior de la ventana")](hello-tvos-images/designer09.png#lightbox)
8. A continuación, haga clic en el ancho y, a continuación, el alto *identificador hueso* tal como se muestra a continuación:

    [![](hello-tvos-images/designer10.png "El ancho y el alto ósea identificadores")](hello-tvos-images/designer10.png#lightbox)

     Cuando cada *identificador hueso* es hacer clic en, seleccione tanto ancho y alto respectivamente para establecer dimensiones fijas.
9. Cuando haya completado, las restricciones de deben ser similares a las de la pestaña diseño del panel de propiedades:

    [![](hello-tvos-images/designer11.png "Un ejemplo de restricción")](hello-tvos-images/designer11.png#lightbox)
8. Arrastre un **botón** desde el **cuadro de herramientas** y colóquelo en la etiqueta.
9. Haga clic en el **título** propiedad en el **controlador de propiedad** y cambie el título del botón a `Click Me`:

    [![](hello-tvos-images/designer12.png "Cambie el título de botones a Click Me")](hello-tvos-images/designer12.png#lightbox)
10. Repita los pasos 5 a 8 anterior para restringir el botón en la ventana de tvOS. Sin embargo, en lugar de arrastrar el identificador de T a la parte superior de la ventana (como en el paso &#7;), arrástrelo hasta la parte inferior de la etiqueta:

    [![](hello-tvos-images/designer14.png "Restringir el botón")](hello-tvos-images/designer14.png#lightbox)
11. Arrastre otra etiqueta en el botón, cambiar su tamaño para que sea el mismo ancho que la primera etiqueta y establezca su **alineación** a **Center**:

    [![](hello-tvos-images/designer15.png "Arrastre otra etiqueta debajo del botón, cambio de tamaño para que tenga el mismo ancho que la primera etiqueta y establecer su alineación al centro")](hello-tvos-images/designer15.png#lightbox)
12. Al igual que la primera etiqueta y botón, establecer esta etiqueta para centrarse y anclar en la ubicación y el tamaño:

    [![](hello-tvos-images/designer16.png "Anclar la etiqueta en la ubicación y el tamaño")](hello-tvos-images/designer16.png#lightbox)
13. Guarde los cambios en la interfaz de usuario.

Tal y como estaban cambiando el tamaño y mueva los controles en, se debe que haya observado que el diseñador proporciona sugerencias útiles de complemento que se basan en [directrices de interfaz humana de Apple TV](https://developer.apple.com/tvos/human-interface-guidelines/). Estas instrucciones le ayudará a crear aplicaciones de alta calidad que tendrán una apariencia familiar para los usuarios de Apple TV.

Si observa el **esquema del documento** sección, tenga en cuenta cómo se muestran el diseño y la jerarquía de los elementos que componen la interfaz de usuario:

[![](hello-tvos-images/designer17.png "La sección de esquema del documento")](hello-tvos-images/designer17.png#lightbox)

Desde aquí puede seleccionar elementos para editar o arrastre para reordenar los elementos de interfaz de usuario si es necesario. Por ejemplo, si un elemento de interfaz de usuario que se está cubierto por otro elemento, puede arrastrar a la parte inferior de la lista para que sea el elemento de nivel superior en la ventana.

Ahora que tenemos que crear la interfaz de usuario, es necesario exponer los elementos de interfaz de usuario para que Xamarin.tvOS puede obtener acceso e interactuar con ellos en código C#.

### <a name="accessing-the-controls-in-the-code-behind"></a>Obtener acceso a los controles en el código subyacente

Hay dos formas principales para tener acceso a los controles que ha agregado en el Diseñador de iOS desde el código:

* Crear un controlador de eventos en un control.
* Da el control de un nombre, por lo que podemos más adelante hacer referencia a él.

Cuando uno de ellos se agregan, la clase parcial en la `ViewController.designer.cs` se actualizará para reflejar los cambios. Esto le permitirá tener acceso a los controles en el controlador de vista.

### <a name="creating-an-event-handler"></a>Crear un controlador de eventos

En esta aplicación de ejemplo, cuando se hace clic en el botón que deseamos _algo_ a ocurrir, por lo que un controlador de eventos debe agregarse a un evento concreto en el botón. Para configurar esta opción, haga lo siguiente:

1. En el Diseñador de iOS de Xamarin, seleccione el botón en el controlador de vista.
2. En el panel Propiedades, seleccione la **eventos** ficha:

    [![](hello-tvos-images/event1.png "La ficha de eventos")](hello-tvos-images/event1.png#lightbox)
3. Busque el evento TouchUpInside y asígnele un controlador de eventos denominado `Clicked`:

    [![](hello-tvos-images/event2.png "El evento TouchUpInside")](hello-tvos-images/event2.png#lightbox)
4. Cuando se presiona **ENTRAR**, **ViewController**se abrirá el archivo. cs, lo que sugiere ubicaciones para el controlador de eventos en el código. Use las teclas de dirección en el teclado para establecer la ubicación:

    [![](hello-tvos-images/event3.png "Configuración de la ubicación")](hello-tvos-images/event3.png#lightbox)
5. Esto creará un método parcial, tal y como se muestra a continuación:

    [![](hello-tvos-images/event4.png "El método parcial")](hello-tvos-images/event4.png#lightbox)

Ahora estamos preparados empezar a agregar código para permitir que el botón de función.

### <a name="naming-a-control"></a>Un control de nomenclatura

Cuando se hace clic en el botón, debe actualizar la etiqueta en función del número de clics. Para ello, se necesita acceso a la etiqueta en el código. Para ello, dándole un nombre. Haga lo siguiente:

1. Abra el guión gráfico y seleccione la etiqueta en la parte inferior del controlador de vista.
2. En el panel Propiedades, seleccione la **Widget** ficha:

    [![](hello-tvos-images/name1.png "Seleccione la pestaña de Widget")](hello-tvos-images/name1.png#lightbox)
3. En **identidad > nombre**, agregar `ClickedLabel`:

    [![](hello-tvos-images/name2.png "Establecer ClickedLabel")](hello-tvos-images/name2.png#lightbox)

Ahora estamos preparados para iniciar la actualización de la etiqueta.

### <a name="how-controls-are-accessed"></a>¿Cómo se obtiene acceso a controles

Si selecciona el `ViewController.designer.cs` en el **el Explorador de soluciones** , podrá ver cómo la `ClickedLabel` etiqueta y la `Clicked` controlador de eventos se han asignado a un **toma** y  **Acción** en C#:

[![](hello-tvos-images/accesscontrol.png "Distribuidores y acciones")](hello-tvos-images/accesscontrol.png#lightbox)

También puede observar que `ViewController.designer.cs` es una clase parcial, para que Visual Studio para Mac no tiene que modificar `ViewController.cs` que sobrescribirá los cambios que hemos realizado en la clase.

Exponer los elementos de interfaz de usuario de este modo, le permite tener acceso a ellos en el controlador de vista.

Normalmente nunca debe abrir el `ViewController.designer.cs` usted mismo, se presenta aquí únicamente con fines formativos.

<a name="Writing-the-Code" />

## <a name="writing-the-code"></a>Escritura del código

Con la interfaz de usuario creado y sus elementos de interfaz de usuario expuestas al código a través de **tomas** y **acciones**, estamos llegado el momento de escribir el código para proporcionar la funcionalidad del programa.

En nuestra aplicación, cada vez que se hace clic en el primer botón, vamos a actualizar la etiqueta para mostrar cuántas veces se ha hecho clic el botón. Para lograr esto, es necesario abrir el `ViewController.cs` archivo para su edición haciendo doble clic en él en el **solución panel**:

[![](hello-tvos-images/code01.png "El panel de la solución")](hello-tvos-images/code01.png#lightbox)

En primer lugar, es necesario crear una variable de nivel de clase en nuestro `ViewController` clase para realizar un seguimiento del número de clics que se han producido. Edite la definición de clase y dele un aspecto similar al siguiente:

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

Después, en la misma clase (`ViewController`), es necesario invalidar el **ViewDidLoad** método y agregue algo de código para establecer el mensaje inicial para la etiqueta:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Set the initial value for the label
    ClickedLabel.Text = "Button has not been clicked yet.";
}
```

Que debemos usar `ViewDidLoad `, en lugar de otro método, como `Initialize`, porque `ViewDidLoad ` se denomina *después* ha cargado el sistema operativo y se crea una instancia de la interfaz de usuario de la `.storyboard` archivo. Si se intentó obtener acceso al control de etiqueta antes de la `.storyboard` archivo se ha cargado y crea una instancia totalmente, se obtendría un `NullReferenceException` error porque todavía no se crearán el control de etiqueta.

A continuación, necesitamos agregar el código para responder al usuario hacer clic en el botón. Agregue lo siguiente al parcial de clase para que hemos creado:

```csharp
partial void Clicked (UIButton sender)
{
    ClickedLabel.Text = string.Format("The button has been clicked {0} time{1}.", ++numberOfTimesClicked, (numberOfTimesClicked
```

Este código se llamará siempre que el usuario hace clic en el botón.

Con todo el contenido en su lugar, ahora estamos preparados para compilar y probar la aplicación Xamarin.tvOS.

## <a name="testing-the-application"></a>Probar la aplicación

Es el momento de compilar y ejecutar la aplicación para asegurarse de que se ejecuta según lo previsto. Podemos compilar y ejecutar en un solo paso, o podemos crear, sin tener que ejecutar.

Cada vez que se compila una aplicación, podemos elegir qué tipo de compilación que deseamos:

-   **Depurar** – una compilación de depuración se compila en un "archivo (aplicación) con metadatos adicionales que nos permite depurar lo que sucede mientras se ejecuta la aplicación.
-   **La versión** – también crea una versión de lanzamiento un "archivo, pero no incluye información de depuración, por lo que es más pequeño y se ejecuta con mayor rapidez.  

Puede seleccionar el tipo de compilación de la **configuración Selector** en la esquina superior izquierda de Visual Studio para la pantalla de Mac:

[![](hello-tvos-images/run01.png "Seleccione el tipo de compilación")](hello-tvos-images/run01.png#lightbox)

### <a name="building-the-application"></a>Compilar la aplicación

En nuestro caso, nos intención es una compilación de depuración, así que vamos a Asegúrese de que **depurar** está seleccionada. Vamos a crear nuestra aplicación primero si presiona **⌘B**, o desde el **generar** menú, elija **compilar todos los**.

Si no había errores, verá un **se compiló correctamente** mensaje en Visual Studio para la barra de estado de Mac. Si se produjeron errores, revise el proyecto y asegúrese de que ha seguido los pasos correctamente. Empiece por confirmar que el código (tanto en Xcode como en Visual Studio para Mac) coincide con el código en el tutorial.

### <a name="running-the-application"></a>Ejecutar la aplicación

Para ejecutar la aplicación, tenemos tres opciones:

-  Presione **⌘+Entrar**.
-  En el menú **Ejecutar**, elija **Depurar**.
-  Haga clic en el botón **Reproducir** de la barra de herramientas de Visual Studio para Mac (justo encima del **Explorador de soluciones**).

La aplicación se compilará (si todavía no ha creado ya), inicio en modo de depuración, el simulador tvOS se iniciará y la aplicación se inicie y mostrar su ventana principal de interfaz:

[![La pantalla principal de aplicación de ejemplo](hello-tvos-images/run03.png)](hello-tvos-images/run03.png#lightbox)

Desde el **Hardware** menú, seleccione **mostrar Apple TV remoto** para poder controlar el simulador.

[![](hello-tvos-images/run04.png "Seleccione Mostrar Apple TV remoto")](hello-tvos-images/run04.png#lightbox)

Usar control remoto del simulador, si hace clic en el botón varias veces en la etiqueta debe actualizarse con el recuento:

[![](hello-tvos-images/run05.png "La etiqueta con un recuento actualizado")](hello-tvos-images/run05.png#lightbox)

¡Enhorabuena! Hemos aprendido mucha información aquí, pero si sigue este tutorial de principio a fin, ahora debería tener un conocimiento sólido de los componentes de una aplicación Xamarin.tvOS, así como las herramientas utilizadas para crearlos.

## <a name="where-to-next"></a>¿Dónde siguiente?

Desarrollar presenta aplicaciones de Apple TV algunos desafíos debido a la desconexión entre el usuario y la interfaz (no en la misma habitación no está en la parte del usuario) y la tvOS limitaciones coloca en el almacenamiento y el tamaño de la aplicación.

Como resultado, se recomienda altamente que leyendo los siguientes documentan antes de pasar al diseño de la aplicación de Xamarin.tvOS:

- [Introducción a tvOS 9](~/ios/tvos/platform/tvos9.md) : este artículo detallan todas las características disponibles en tvOS 9 y las API nuevas y modificadas para desarrolladores de Xamarin.tvOS.
- [Trabajar con navegación y foco](~/ios/tvos/app-fundamentals/navigation-focus.md) : los usuarios de la aplicación Xamarin.tvOS no será su interacción con su interfaz directamente como con iOS que tocan imágenes en la pantalla del dispositivo, pero indirectamente de toda la habitación mediante el mando a distancia Siri. Este artículo explican los conceptos de foco y cómo se utiliza para controlar la navegación en la interfaz de usuario de la aplicación de Xamarin.tvOS.
- [Siri remoto y controladores de Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) : la forma principal que los usuarios será su interacción con la televisión de Apple y la aplicación Xamarin.tvOS, es a través de la instancia remota de Siri incluye. Si la aplicación es un juego, puede, opcionalmente, crear en la compatibilidad de entidad 3ª, realizados para dispositivos de juego de iOS (MFI) Bluetooth en así la aplicación. Este artículo tratan admitir los nuevos dispositivos de juego Siri remoto y Bluetooth en sus aplicaciones Xamarin.tvOS.
- [Almacenamiento de datos y recursos](~/ios/tvos/app-fundamentals/resources-data-storage.md) : a diferencia de los dispositivos iOS, el nuevo Televisor de Apple no proporciona un almacenamiento persistente y local para las aplicaciones de tvOS. Como resultado, si la aplicación Xamarin.tvOS necesita para conservar información (por ejemplo, las preferencias del usuario) debe almacenar y recuperar los datos de iCloud. Este artículo explica cómo trabajar con recursos y el almacenamiento de datos persistentes en una aplicación Xamarin.tvOS.
- [Trabajar con imágenes de iconos y](~/ios/tvos/app-fundamentals/icons-images.md) : crear atractivas iconos e imágenes son una parte fundamental del desarrollo de una experiencia de usuario atractiva para las aplicaciones de TV de Apple. Esta guía tratará los pasos necesarios para crear e incluir los activos gráficos necesarios para las aplicaciones de Xamarin.tvOS.
- [Interfaz de usuario](~/ios/tvos/user-interface/index.md) – cobertura de experiencia de usuario General (UX), incluidos los controles de interfaz de usuario (UI), utilice el generador de interfaz de Xcode y principios de diseño UX cuando trabaje con Xamarin.tvOS.
- [Implementación y pruebas](~/ios/tvos/deploy-test/index.md) : en esta sección se trata temas que se usa para probar una aplicación y cómo distribuir la aplicación. Temas aquí expuestos incluyen elementos como herramientas que se usan para la depuración, la implementación para evaluadores y cómo publicar una aplicación en la tienda de aplicaciones de TV de Apple.

Si experimenta problemas al trabajar con Xamarin.tvOS, visite nuestro [solución de problemas](~/ios/tvos/troubleshooting.md) saber de documentación para obtener una lista de problemas y soluciones.

## <a name="summary"></a>Resumen

Este artículo ha proporcionado un tutorial para desarrollar aplicaciones tvos con Visual Studio para Mac mediante la creación de un simple Hello, tvOS aplicación. Tratan los aspectos básicos del dispositivo de tvOS aprovisionamiento, creación de una interfaz, codificar tvos y realizar pruebas en el simulador tvOS.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guías de interfaz humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones tvos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [Compilar aplicaciones tvos con Xamarin (vídeo)](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
