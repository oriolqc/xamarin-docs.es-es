---
title: Crear Interfaces de usuario de iOS en código
description: 'Xamarin.iOS proporciona dos métodos para crear una interfaz de usuario para la aplicación: con el Diseñador de Xamarin para iOS o en el código. Este artículo examina cómo crear interfaces de usuario de iOS completamente en código. Muestra cómo iniciar desde una plantilla de proyecto para crear una pantalla de aplicación de un controlador mediante la creación de una jerarquía de vistas de UIKit. Luego se explica cómo crear vistas personalizadas que se pueden cargar en un controlador.'
ms.prod: xamarin
ms.assetid: 7CB1FEAE-0BB3-4CDC-9076-5BD555003F1D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 7e8460d2c946159a9869322d6d4944d213d3d801
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="creating-ios-user-interfaces-in-code"></a>Crear Interfaces de usuario de iOS en código

_Xamarin.iOS proporciona dos métodos para crear una interfaz de usuario para la aplicación: con el Diseñador de Xamarin para iOS o en el código. Este artículo examina cómo crear interfaces de usuario de iOS completamente en código. Muestra cómo iniciar desde una plantilla de proyecto para crear una pantalla de aplicación de un controlador mediante la creación de una jerarquía de vistas de UIKit. A continuación, describe cómo crear vistas personalizadas que se pueden cargar en un controlador._

La interfaz de usuario de una aplicación de iOS es similar a una tienda: la aplicación obtiene normalmente una ventana, pero puede llenar la ventana con muchos objetos que necesita y los objetos y las organizaciones se pueden modificar según lo que espera la aplicación mostrar. Los objetos de este escenario, lo que el usuario ve, se denominan "vistas". Para compilar una sola pantalla en una aplicación, las vistas se apilan uno sobre otro en una jerarquía de vista de contenido y la jerarquía está administrada por un único controlador de vista. Las aplicaciones con varias pantallas tienen varias jerarquías de vistas de contenido, cada una con su propio controlador de vistas. La aplicación coloca las vistas en la ventana para crear una jerarquía de vistas de contenido diferente basándose en la pantalla en la que se encuentra el usuario.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

En el diagrama siguiente se muestran las relaciones entre la ventana, las vistas, las subvistas y el controlador de vistas que, de forma conjunta, proporcionan la interfaz de usuario a la pantalla del dispositivo: 

[![](ios-code-only-images/image9.png "Este diagrama muestra las relaciones entre la ventana, vistas, subvistas y View-Controller")](ios-code-only-images/image9.png#lightbox)

Estas jerarquías de vista pueden crearse mediante el [diseñador Xamarin para iOS](~/ios/user-interface/designer/index.md) sin embargo en Visual Studio, es conveniente tener unos conocimientos básicos sobre de cómo trabajar completamente en código. Este artículo le guía a través de algunos puntos básicos para poner en y en ejecución con el desarrollo de la interfaz de usuario de solo código.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

En el diagrama siguiente se muestran las relaciones entre la ventana, las vistas, las subvistas y el controlador de vistas que, de forma conjunta, proporcionan la interfaz de usuario a la pantalla del dispositivo: 

[![](ios-code-only-images/image9.png "Este diagrama muestra las relaciones entre la ventana, vistas, subvistas y View-Controller")](ios-code-only-images/image9.png#lightbox)


Estas jerarquías de vista pueden crearse mediante el [diseñador Xamarin para iOS](~/ios/user-interface/designer/index.md) sin embargo en Visual Studio para Mac, es conveniente tener unos conocimientos básicos sobre de cómo trabajar completamente en código. Este artículo le guía a través de algunos puntos básicos para poner en y en ejecución con el desarrollo de la interfaz de usuario de solo código.


-----

## <a name="creating-a-code-only-project"></a>Crear un proyecto de código

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="ios-blank-project-template"></a>Plantilla de proyecto en blanco de iOS

En primer lugar, cree un proyecto de iOS en Visual Studio usando el iPhone **proyecto en blanco** plantilla, se muestra a continuación, que extenderemos para agregar controladores y vistas.


[![](ios-code-only-images/blankapp-vs.png "Cuadro de diálogo nuevo proyecto")](ios-code-only-images/blankapp-vs.png#lightbox)


La plantilla proyecto vacío de servidor agrega 4 archivos al proyecto:


[![](ios-code-only-images/empty-project.png "Archivos de proyecto")](ios-code-only-images/empty-project.png#lightbox)


1. **AppDelegate.cs** -contiene un `UIApplicationDelegate` subclase `AppDelegate` , que se usa para controlar los eventos de aplicación de iOS. La ventana de la aplicación se crea en el `AppDelegate`del `FinishedLaunching` método.
1. **Main.cs** -contiene el punto de entrada para la aplicación, que especifica la clase para el `AppDelegate` .
1. **Info.plist** -archivo de lista de propiedades que contiene la información de configuración de aplicación.
1. **Entitlements.plist** : archivo de lista de propiedades que contiene información sobre las funciones y los permisos de la aplicación.


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

## <a name="ios-templates"></a>Plantillas de iOS


Visual Studio para Mac no proporciona una plantilla vacía. Todas las plantillas incluyen soporte de guión gráfico, que recomienda Apple como la manera principal para crear una interfaz de usuario. Sin embargo, es posible crear la interfaz de usuario completamente en el código. 

Los siguientes pasos le guiarán en el proceso de quitar el guión gráfico de una aplicación: 


1. Use la plantilla de aplicación de vista única para crear un nuevo proyecto de iOS:
    
    [![](ios-code-only-images/single-view-app.png "Use la plantilla de aplicación de vista única")](ios-code-only-images/single-view-app.png#lightbox)

1. Eliminar el `Main.Storyboard` y `ViewController.cs` archivos. Hacer **no** eliminar el `LaunchScreen.Storyboard`. El controlador de vista debe eliminarse ya que es el código subyacente para el controlador de vista que se crea en el guión gráfico:
1. Asegúrese de seleccionar **eliminar** desde el cuadro de diálogo emergente:
    
    [![](ios-code-only-images/delete.png "Seleccione Eliminar en el cuadro de diálogo emergente")](ios-code-only-images/delete.png#lightbox)

1. En el Info.plist, elimine la información incluida en el **información de implementación > interfaz principal** opción:
    
    [![](ios-code-only-images/main-interface.png "Eliminar la información incluida en la opción de la interfaz principal")](ios-code-only-images/main-interface.png#lightbox)

1. Por último, agregue el código siguiente a su `FinishedLaunching` método en la clase AppDelegate:
        
        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
            // create a new window instance based on the screen size
            window = new UIWindow(UIScreen.MainScreen.Bounds);

            // make the window visible
            window.MakeKeyAndVisible();

            return true;
        }


-----

## <a name="creating-a-window"></a>Creación de una ventana

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

El código que se agregó a la `FinishedLaunching` método en el paso 3 anterior, es la cantidad mínima de código necesario para crear una ventana de la aplicación de iOS.  

-----

aplicaciones de iOS se generan con el [modelo MVC](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md#Model_View_Controller). Se crea la primera pantalla que muestra una aplicación de controlador de vista de la ventana raíz. Consulte la [Hello, iOS Multiscreen](~/ios/get-started/hello-ios-multiscreen/index.md) guía para obtener más detalles sobre MVC propio modelo.

La implementación para el `AppDelegate` agregados por la plantilla crea la ventana de la aplicación, de que existe es solo uno para cada aplicación de iOS y lo hace visible con el código siguiente:

```csharp
public class AppDelegate : UIApplicationDelegate
{
    public override UIWindow Window
            {
                get;
                set;
            }

    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        // create a new window instance based on the screen size
        window = new UIWindow(UIScreen.MainScreen.Bounds);

        // make the window visible
        window.MakeKeyAndVisible();

        return true;
    }
}
```

Si fuera a ejecutar esta aplicación ahora, probablemente obtendría una excepción que indica que `Application windows are expected to have a root view controller at the end of application launch`. Vamos a agregar un controlador y convertirlo en controlador de vista de raíz de la aplicación.

## <a name="adding-a-controller"></a>Agregar un controlador

La aplicación puede contener muchos controladores de la vista, pero debe tener un controlador de vista de raíz para controlar todos los controladores de vista.  Agregar un controlador a la ventana mediante la creación de un `UIViewController` instancia y si se establece como el `window.RootViewController` propiedad:

```csharp
public class AppDelegate : UIApplicationDelegate
{
    // class-level declarations

    public override UIWindow Window
    {
        get;
        set;
    }

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        var controller = new UIViewController();
        controller.View.BackgroundColor = UIColor.LightGray;

        Window.RootViewController = controller;

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
}
```

Cada controlador tiene una vista asociada, que es accesible desde el `View` propiedad. El código anterior cambia la vista `BackgroundColor` propiedad `UIColor.LightGray` para que esté visible, tal y como se muestra a continuación:

 [![](ios-code-only-images/image1.png "Fondo de la vista es un color gris claro visible")](ios-code-only-images/image1.png#lightbox)

Se puede definir cualquier `UIViewController` subclase como el `RootViewController` de esta manera, incluidos los controladores de UIKit, así como los que escribimos nosotros mismos. Por ejemplo, el código siguiente agrega un `UINavigationController` como el `RootViewController`:

```csharp
public class AppDelegate : UIApplicationDelegate
{
    // class-level declarations

    public override UIWindow Window
    {
        get;
        set;
    }

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        var controller = new UIViewController();
        controller.View.BackgroundColor = UIColor.LightGray;
        controller.Title = "My Controller";

        var navController = new UINavigationController(controller);

        Window.RootViewController = navController;

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
}
```

Esto produce el controlador anidado dentro del controlador de navegación, tal y como se muestra a continuación:

 [![](ios-code-only-images/image2.png "El controlador anidado dentro del controlador de navegación")](ios-code-only-images/image2.png#lightbox)

## <a name="creating-a-view-controller"></a>Creación de un controlador de vista

Ahora que hemos visto cómo agregar un controlador como el `RootViewController` de la ventana, veamos cómo crear un controlador de vista personalizada en código.

Agregar una nueva clase denominada `CustomViewController` tal y como se muestra a continuación:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](ios-code-only-images/customviewcontroller.png "Agregar una nueva clase denominada CustomViewController")](ios-code-only-images/customviewcontroller.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](ios-code-only-images/new-file.png "Agregar una nueva clase denominada CustomViewController")](ios-code-only-images/new-file.png#lightbox)

-----

La clase debe heredar de `UIViewController`, que se encuentra en la `UIKit` espacio de nombres, tal como se muestra:

```csharp
using System;
using UIKit;

namespace CodeOnlyDemo
{
    class CustomViewController : UIViewController
    {
    }
}
```

<a name="Initializing_the_View"/>

## <a name="initializing-the-view"></a>Inicialización de la vista

`UIViewController` contiene un método denominado `ViewDidLoad` que se llama cuando el controlador de vista se carga por primera vez en la memoria. Se trata de un lugar adecuado para realizar la inicialización de la vista, por ejemplo, configurar sus propiedades.

Por ejemplo, el código siguiente agrega un botón y un controlador de eventos para insertar un nuevo controlador de vista en la pila de navegación cuando se presiona el botón:

```csharp
using System;
using CoreGraphics;
using UIKit;

namespace CodyOnlyDemo
{
    public class CustomViewController : UIViewController
    {
        public CustomViewController ()
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            View.BackgroundColor = UIColor.White;
            Title = "My Custom View Controller";

            var btn = UIButton.FromType (UIButtonType.System);
            btn.Frame = new CGRect (20, 200, 280, 44);
            btn.SetTitle ("Click Me", UIControlState.Normal);

            var user = new UIViewController ();
            user.View.BackgroundColor = UIColor.Magenta;

            btn.TouchUpInside += (sender, e) => {
                this.NavigationController.PushViewController (user, true);
            };

            View.AddSubview (btn);

        }
    }
}
```

Para cargar este controlador en la aplicación y mostrar el panel de navegación simple, cree una nueva instancia de `CustomViewController`. Crear un nuevo controlador de navegación, pasar la instancia de controlador de vista y establece el nuevo controlador de navegación en la ventana `RootViewController` en el `AppDelegate` como antes:

```csharp
var cvc = new CustomViewController ();

var navController = new UINavigationController (cvc);

Window.RootViewController = navController;
```

Ahora cuando carga la aplicación, el `CustomViewController` se carga dentro de un controlador de navegación:

 [![](ios-code-only-images/customvc.png "El CustomViewController se carga en un controlador de navegación")](ios-code-only-images/customvc.png#lightbox)
 
Haga clic en el botón, le _inserción_ un nuevo controlador de vista en la pila de navegación:

[![](ios-code-only-images/customvca.png "Inserta un nuevo controlador de vista en la pila de navegación")](ios-code-only-images/customvca.png#lightbox)

## <a name="building-the-view-hierarchy"></a>Creación de la jerarquía de vista

En el ejemplo anterior, se comenzó a crear una interfaz de usuario en el código mediante la adición de un botón en el controlador de vista.

interfaces de usuario de iOS se componen de una jerarquía de vista. Vistas adicionales, como etiquetas, botones, controles deslizantes, etc. se agregan como subvistas de algunos vista primaria.

Por ejemplo, vamos a modificar el `CustomViewController` para crear una pantalla de inicio de sesión donde el usuario puede escribir un nombre de usuario y una contraseña. La pantalla constará de dos campos de texto y un botón.

### <a name="adding-the-text-fields"></a>Agregar los campos de texto

En primer lugar, quite el controlador de botón y eventos que se agregó en el [inicializar la vista](#Initializing_the_View) sección. 

Agregar un control para el nombre de usuario al crear e inicializar un `UITextField` y, a continuación, éste se agrega a la jerarquía de la vista, tal y como se muestra a continuación:

```csharp
class CustomViewController : UIViewController
{
    UITextField usernameField;

    public override void ViewDidLoad()
    {
        base.ViewDidLoad();

        View.BackgroundColor = UIColor.Gray;

        nfloat h = 31.0f;
        nfloat w = View.Bounds.Width;

        usernameField = new UITextField
        {
            Placeholder = "Enter your username",
            BorderStyle = UITextBorderStyle.RoundedRect,
            Frame = new CGRect(10, 82, w - 20, h)
        };

        View.AddSubview(usernameField);
    }
}
```

Cuando se crea el `UITextField`, establecemos la `Frame` propiedad para definir la ubicación y el tamaño. En iOS la coordenada 0,0 está en la parte superior izquierda con + x a la derecha y + y hacia abajo. Después de establecer el `Frame` junto con un par de otro propiedades, llamamos a `View.AddSubview` para agregar el `UITextField` a la jerarquía de vista. Esto hace que la `usernameField` una vista secundaria de la `UIView` instancia que la `View` referencias de propiedad. Se agrega una vista secundaria con un orden z es mayor que su vista primaria, por lo que aparece delante de la vista primaria en la pantalla.

La aplicación con el `UITextField` incluidos se muestra a continuación:

 [![](ios-code-only-images/image4.png "La aplicación con el UITextField incluida")](ios-code-only-images/image4.png#lightbox)

Podemos agregar una `UITextField` para la contraseña de un modo similar, pero esta vez establecemos la `SecureTextEntry` propiedad en true, tal y como se muestra a continuación:

```csharp
public class CustomViewController : UIViewController
{
    UITextField usernameField, passwordField;
    public override void ViewDidLoad()
    {
       // keep the code the username UITextField
        passwordField = new UITextField
        {
            Placeholder = "Enter your password",
            BorderStyle = UITextBorderStyle.RoundedRect,
            Frame = new CGRect(10, 114, w - 20, h),
            SecureTextEntry = true
        };

      View.AddSubview(usernameField); 
      View.AddSubview(passwordField);
   }
}

```

Establecer `SecureTextEntry = true` oculta el texto escrito en el `UITextField` por el usuario tal y como se muestra a continuación:

 [![](ios-code-only-images/image4a.png "Establecer SecureTextEntry true oculta el texto escrito por el usuario")](ios-code-only-images/image4a.png#lightbox)

### <a name="adding-the-button"></a>Agregar el botón

A continuación, vamos a agregar un botón, por lo que el usuario puede enviar el nombre de usuario y la contraseña. El botón se agrega a la jerarquía de vista como cualquier otro control, si se pasa como argumento a la vista primaria `AddSubview` nuevo al método.

El código siguiente se agrega el botón y registra un controlador de eventos para el `TouchUpInside` eventos:

```csharp
var submitButton = UIButton.FromType (UIButtonType.RoundedRect);

submitButton.Frame = new CGRect (10, 170, w - 20, 44);
submitButton.SetTitle ("Submit", UIControlState.Normal);

submitButton.TouchUpInside += (sender, e) => {
    Console.WriteLine ("Submit button pressed");
};

View.AddSubview(submitButton);
```

Teniendo esto en su lugar, la pantalla de inicio de sesión aparece ahora como se muestra a continuación:

 [![](ios-code-only-images/image5.png "La pantalla de inicio de sesión")](ios-code-only-images/image5.png#lightbox)

Al contrario que en versiones anteriores de iOS, el fondo predeterminado del botón es transparente. Modificar el botón `BackgroundColor` cambios en las propiedades esto:

```csharp
submitButton.BackgroundColor = UIColor.White;
```

El resultado será un botón cuadrado en lugar de redondearse típica con filo botón. Para obtener el extremo redondeado, utilizar el siguiente fragmento:

```csharp
submitButton.Layer.CornerRadius = 5f;
```

Con estos cambios, la vista tendrá este aspecto:

[![](ios-code-only-images/image6.png "Un ejemplo de ejecución de la vista")](ios-code-only-images/image6.png#lightbox)
 
## <a name="adding-multiple-views-to-the-view-hierarchy"></a>Agregar varias vistas a la jerarquía de vista

iOS proporciona una funcionalidad para agregar varias vistas a la jerarquía de visualización mediante `AddSubviews`.

```csharp
View.AddSubviews(new UIView[] { usernameField, passwordField, submitButton }); 
```

## <a name="adding-button-functionality"></a>Agregar funcionalidad de botón

Cuando se hace clic en un botón, los usuarios esperan ocurra algo. Por ejemplo, se muestra una alerta o se realiza la navegación a otra pantalla. 

Vamos a agregar código para insertar un segundo controlador de vista en la pila de navegación.

En primer lugar, cree el segundo controlador de vista:

```csharp
var loginVC = new UIViewController () { Title = "Login Success!"};
loginVC.View.BackgroundColor = UIColor.Purple;
```

A continuación, agregue la funcionalidad para la `TouchUpInside` eventos:

```csharp
submitButton.TouchUpInside += (sender, e) => {
                this.NavigationController.PushViewController (loginVC, true);
            };
```

El panel de navegación se muestra a continuación:

[![](ios-code-only-images/navigation.png "En este gráfico se muestra el panel de navegación")](ios-code-only-images/navigation.png#lightbox)

Tenga en cuenta que de forma predeterminada, cuando se utiliza un controlador de navegación, iOS ofrece a la aplicación una barra de navegación y un botón Atrás para permitirle desplazarse hacia atrás por la pila.

## <a name="iterating-through-the-view-hierarchy"></a>Recorrer en iteración la jerarquía de vista

Es posible iterar por la jerarquía de la vista secundaria y seleccionar cualquier vista determinada. Por ejemplo, para buscar cada uno de ellos `UIButton` y asigne a ese botón otra `BackgroundColor`, puede utilizarse el siguiente fragmento de código

```csharp
foreach(var subview in View.Subviews)
{
    if (subview is UIButton)
    {
         var btn = subview as UIButton;
         btn.BackgroundColor = UIColor.Green;
    }
}
```

Esto, sin embargo no funcionará si es la vista que se recorre en iteración para un `UIView` como todas las vistas se devolverán como un `UIView` como los objetos agregados a la vista primaria por sí mismos heredan `UIView`.

## <a name="handling-rotation"></a>Control de giro

Si el usuario gira el dispositivo a horizontal, los controles no cambian de tamaño de forma adecuada, como se muestra en la captura de pantalla siguiente:

 [![](ios-code-only-images/image7.png "Si el usuario gira el dispositivo a horizontal, los controles no cambian de tamaño correctamente")](ios-code-only-images/image7.png#lightbox)

Es una manera de solucionar este problema estableciendo la `AutoresizingMask` propiedad en cada vista. En este caso queremos los controles que se va a expandir horizontalmente, por lo que se recomienda establecer cada `AutoresizingMask`. El ejemplo siguiente es para `usernameField`, pero el mismo tendría que se aplicará a cada gadget en la jerarquía de vista.

```csharp
usernameField.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
```

Ahora cuando se gira el dispositivo o el simulador, todo lo que se expande para rellenar el espacio adicional, tal y como se muestra a continuación:

 [![](ios-code-only-images/image8.png "Todos los controles se ajustan para rellenar el espacio adicional")](ios-code-only-images/image8.png#lightbox)

## <a name="creating-custom-views"></a>Creación de vistas personalizadas

Además de utilizar controles que forman parte de UIKit, también se pueden utilizar vistas personalizadas. Puede crearse una vista personalizada mediante la adquisición de `UIView` e invalidar `Draw`. Vamos a crear una vista personalizada y agregarla a la jerarquía de vista para mostrar.

### <a name="inheriting-from-uiview"></a>Heredar UIView

Lo primero que debemos hacer es crear una clase para la vista personalizada. Esto a cabo mediante el **clase** plantilla de Visual Studio para agregar una clase vacía denominada `CircleView`. La clase base debe establecerse en `UIView`, que se recuerde está en el `UIKit` espacio de nombres. También será necesario el `System.Drawing` así el espacio de nombres. Los otros distintos `System.*` dejarán de espacios de nombres utilizados en este ejemplo, por lo que no dude en quitarlos.

La clase debe tener este aspecto:

```csharp
using System;

namespace CodeOnlyDemo
{
    class CircleView : UIView
    {
    }
}
```

### <a name="drawing-in-a-uiview"></a>Dibujar en un UIView

Cada `UIView` tiene un `Draw` método al que se llama por el sistema cuando sea necesario dibujar. `Draw` nunca se debería llamar directamente. Es llamado por el sistema durante el procesamiento de bucle de ejecución. La primera vez en el bucle de ejecución después de agrega una vista a la jerarquía de vista, su `Draw` se llama al método. Las llamadas subsiguientes a `Draw` se producen cuando la vista está marcada como que necesitan dibujar llamando `SetNeedsDisplay` o `SetNeedsDisplayInRect` en la vista.

Podemos agregar código de dibujo a la vista mediante la adición de este código dentro de la invalidado `Draw` método tal como se muestra a continuación:

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    //get graphics context
    using (var g = UIGraphics.GetCurrentContext())
    {
        // set up drawing attributes
        g.SetLineWidth(10.0f);
        UIColor.Green.SetFill();
        UIColor.Blue.SetStroke();

        // create geometry
        var path = new CGPath();
        path.AddArc(Bounds.GetMidX(), Bounds.GetMidY(), 50f, 0, 2.0f * (float)Math.PI, true);

        // add geometry to graphics context and draw
        g.AddPath(path);
        g.DrawPath(CGPathDrawingMode.FillStroke);
    }
}
```

Puesto que `CircleView` es un `UIView`, también podemos establecer `UIView` también las propiedades. Por ejemplo, podemos establecer el `BackgroundColor` en el constructor:

```csharp
public CircleView()
{
    BackgroundColor = UIColor.White;
}
```

Para usar el `CircleView` que acabamos de crear, podemos o bien agregar, como una vista secundaria en la jerarquía de vista de un controlador existente, como hicimos en el `UILabels` y `UIButton` anteriormente, o se puede cargar como la vista de un nuevo controlador. Vamos a hacer esto último.

### <a name="loading-a-view"></a>Cargar una vista

 `UIViewController` tiene un método denominado `LoadView` que se llama por el controlador para crear la vista. Se trata de un lugar adecuado para crear una vista y asignar a del controlador `View` propiedad.

En primer lugar, se necesita un controlador, por lo tanto, crear una nueva clase vacía denominada `CircleController`.

En `CircleController` agregue el código siguiente para establecer el `View` a una `CircleView` (no se debe llamar a la `base` implementación en la invalidación):

```csharp
using UIKit;

namespace CodeOnlyDemo
{
    class CircleController : UIViewController
    {
        CircleView view;

        public override void LoadView()
        {
            view = new CircleView();
            View = view;
        }
    }
}
```

Por último, se necesitan presentar el controlador en tiempo de ejecución. Vamos a ello, agregue un controlador de eventos en el botón de envío que se agregó anteriormente, como sigue:

```csharp
submitButton.TouchUpInside += delegate
{
    Console.WriteLine("Submit button clicked");

    //circleController is declared as class variable
    circleController = new CircleController();
    PresentViewController(circleController, true, null);
};
```

Ahora, cuando se ejecute la aplicación y pulse el botón Enviar, se muestra la nueva vista con un círculo:

 [![](ios-code-only-images/circles.png "Se muestra la nueva vista con un círculo")](ios-code-only-images/circles.png#lightbox)

## <a name="creating-a-launch-screen"></a>Crear una pantalla de inicio

A [pantalla de inicio](~/ios/app-fundamentals/images-icons/launch-screens.md) se muestra cuando la aplicación se inicia como una forma de mostrar a los usuarios que está respondiendo. Dado que una pantalla de inicio se muestra cuando se carga la aplicación, no se puede crear en código como la aplicación todavía se está cargando en memoria. 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Cuando el crear un proyecto en Visual Studio, una pantalla de inicio se proporciona automáticamente en el formulario de un archivo .xib, que puede encontrarse en de iOS la **recursos** carpeta dentro de su proyecto. 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Cuando la cree un proyecto de iOS en Visual Studio para Mac, una pantalla de inicio se proporciona para el usuario en forma de un archivo de guión gráfico. 

-----

Esto puede editarse haciendo doble clic en él y lo abre en el Diseñador de iOS.

Apple recomienda que un archivo de guión gráfico o .xib se utiliza para las aplicaciones dirigidas a iOS 8 o versiones posteriores, cuando inicie cualquier archivo en el Diseñador de iOS, usará las clases de tamaño y diseño automático para adaptar el diseño para que se ve bien y correctamente, se muestra para todos los dispositivos tamaños. Una imagen estática de inicio puede usarse además un .xib o el guión gráfico para permitir la compatibilidad para las aplicaciones destinadas a versiones anteriores.

Para obtener más información acerca de cómo crear una pantalla de inicio, consulte los siguientes documentos:

- [Crear una pantalla de inicio con un .xib](https://developer.xamarin.com/recipes/ios/general/templates/launchscreen-xib/)
- [Administración de las pantallas de inicio con guiones gráficos](~/ios/app-fundamentals/images-icons/launch-screens.md)

> [!IMPORTANT]
> A partir de iOS 9, Apple recomienda que se deben usar guiones gráficos como el principal método de creación de una pantalla de inicio.

### <a name="creating-a-launch-image-for-pre-ios-8-applications"></a>Crear una imagen de inicio para iOS previa 8 aplicaciones

Una imagen estática se puede usar además un .xib o la pantalla de inicio del guión gráfico si su aplicación tiene como destino las versiones anteriores a iOS 8. 

Esta imagen estática se puede establecer en el archivo Info.plist, o como un catálogo de activos (por iOS 7) en la aplicación. Debe proporcionar imágenes independientes para cada tamaño de dispositivo (320 x 480, 640 x 960, 640 x 1136) que puede ejecutar la aplicación. Para obtener más información sobre los tamaños de pantalla de inicio, ver el [imágenes de la pantalla de inicio](~/ios/app-fundamentals/images-icons/launch-screens.md) guía.

> [!IMPORTANT]
> Si la aplicación no tiene ninguna pantalla Iniciar, observará que no caben completamente la pantalla. Si este es el caso, debe asegurarse de que desea incluir, al menos, una imagen de 640 x 1136 denominada `Default-568@2x.png` en su Info.plist. 



## <a name="summary"></a>Resumen

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

En este artículo se describe cómo desarrollar aplicaciones de iOS mediante programación en Visual Studio. Analizamos cómo crear un proyecto de plantilla de proyecto vacía, hablar sobre cómo crear y agregar un controlador de vista de raíz a la ventana. También hemos mostrado cómo usar controles de UIKit para crear una jerarquía de vista dentro de un controlador para desarrollar una pantalla de la aplicación. A continuación se examina cómo dar a conocer las opiniones diseñar adecuadamente en distintas orientaciones y hemos visto cómo crear una vista personalizada mediante subclases `UIView`, así como el modo para cargar la vista dentro de un controlador. Por último, analizamos cómo agregar una pantalla de inicio para una aplicación.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

En este artículo se describe cómo desarrollar aplicaciones de iOS mediante programación en Visual Studio para Mac. Hemos observado cómo compilar un proyecto desde una plantilla de vista única, hablar sobre cómo crear y agregar un controlador de vista de raíz a la ventana. También hemos mostrado cómo usar controles de UIKit para crear una jerarquía de vista dentro de un controlador para desarrollar una pantalla de la aplicación. A continuación se examina cómo dar a conocer las opiniones diseñar adecuadamente en distintas orientaciones y hemos visto cómo crear una vista personalizada mediante subclases `UIView`, así como el modo para cargar la vista dentro de un controlador. Por último, analizamos cómo agregar una pantalla de inicio para una aplicación.

-----




## <a name="related-links"></a>Vínculos relacionados

- [SimpleLogin (ejemplo)](https://developer.xamarin.com/samples/monotouch/SimpleLogin)
