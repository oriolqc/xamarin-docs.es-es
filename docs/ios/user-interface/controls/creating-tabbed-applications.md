---
title: Barras de pestaña y controladores de la barra de pestañas en Xamarin.iOS
description: Este documento describe los controladores de barra de pestaña de iOS y cómo utilizarlos con Xamarin.iOS. Muestra cómo configurar un UITabBarController, trabajar con imágenes, establezca los valores distintivos, trabajar con eventos y mucho más.
ms.prod: xamarin
ms.assetid: 7C772899-2900-F139-D642-F3C4F3F14DDC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: d8b096774e60ec0e0b69e109fa5da53c25e66d25
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789763"
---
# <a name="tab-bars-and-tab-bar-controllers-in-xamarinios"></a>Barras de pestaña y controladores de la barra de pestañas en Xamarin.iOS

Aplicaciones con pestañas se utilizan en iOS para admitir las interfaces de usuario donde se pueden tener acceso a varias pantallas en ningún orden concreto. A través de la `UITabBarController` (clase), las aplicaciones pueden incluir fácilmente compatibilidad para estos escenarios de la pantallas. `UITabBarController` se encarga de la administración de la pantalla, lo que permite al desarrollador de la aplicación para centrarse en los detalles de cada pantalla.

Normalmente, se generan las aplicaciones con pestañas con el `UITabBarController` que se va a la `RootViewController` de la ventana principal. Sin embargo, con un poco de código adicional, las aplicaciones con pestañas también se usan en sucesión a alguna otra pantalla inicial, como el escenario donde una aplicación, en primer lugar, presenta una pantalla de inicio de sesión, seguida de la interfaz con pestañas.

Examinaremos mediante pestañas aquí trabajar a través de un tutorial de una aplicación sencilla. A continuación, analizaremos cómo trabajar con pestañas en no es `RootViewController` escenario.

## <a name="introducing-uitabbarcontroller"></a>Introducción a UITabBarController

El `UITabBarController` admite por pestañas de desarrollo de aplicaciones por el texto siguiente:

-  Permitir que varios controladores para agregarse a ella.
-  Proporcionar una interfaz de usuario con pestañas, a través de la `UITabBar` (clase), para permitir que un usuario cambiar entre los controladores y sus vistas. 


Los controladores se agregan a la `UITabBarController` a través de su `ViewControllers` propiedad, que es un `UIViewController` matriz. El `UITabBarController` propio controla cargar el controlador adecuado y presenta su vista en función de la pestaña seleccionada.

Las pestañas son instancias de la `UITabBarItem` (clase), que se encuentran en un `UITabBar` instancia. Cada `UITabBar` instancia es accesible a través del `TabBarItem` propiedad del controlador en cada ficha.

Para obtener una descripción de cómo trabajar con la `UITabBarController`, pasemos a la creación de una aplicación simple que usa uno.

## <a name="tabbed-application-walkthrough"></a>Tutorial de la aplicación con pestañas

En este tutorial vamos a crear la siguiente aplicación:

[![](creating-tabbed-applications-images/00-app.png "Aplicación de ejemplo con pestañas")](creating-tabbed-applications-images/00-app.png#lightbox)

Aunque ya hay una plantilla de aplicación con pestañas disponibles en Visual Studio para Mac, en este ejemplo, vamos a trabajar desde un proyecto vacío para obtener una mejor comprensión de cómo está construida la aplicación.

 <a name="Creating_the_Application" />


### <a name="creating-the-application"></a>Crear la aplicación

Empecemos creando una nueva aplicación.

Seleccione el **archivo > Nuevo > solución** elemento de menú en Visual Studio para Mac y seleccione un **iOS > aplicaciones > proyecto vacío** plantilla, asigne al proyecto el `TabbedApplication`, tal y como se muestra a continuación:

[![](creating-tabbed-applications-images/newsolution1.png "Seleccione la plantilla proyecto vacío")](creating-tabbed-applications-images/newsolution1.png#lightbox)

[![](creating-tabbed-applications-images/newsolution2.png "Denomine el proyecto TabbedApplication")](creating-tabbed-applications-images/newsolution2.png#lightbox)



### <a name="adding-the-uitabbarcontroller"></a>Agregar el UITabBarController

A continuación, agregue una clase vacía seleccionando **archivo > nuevo archivo** y eligiendo la **General: clase vacía** plantilla. Asignar nombre al archivo `TabController` tal y como se muestra a continuación:

[![](creating-tabbed-applications-images/02-newclass.png "Agregue la clase TabController")](creating-tabbed-applications-images/02-newclass.png#lightbox)

El `TabController` clase contendrá la implementación de la `UITabBarController` que va a administrar una matriz de `UIViewControllers`. Cuando el usuario selecciona una ficha, el `UITabBarController` se encargará de presentar la vista para el controlador de vista adecuado.

Para implementar el `UITabBarController` debemos hacer lo siguiente:

1.  Establece la clase base del `TabController` a `UITabBarController` . 
1.  Crear `UIViewController` instancias para agregar a la `TabController` . 
1.  Agregar el `UIViewController` instancias en una matriz asignada a la `ViewControllers` propiedad de la `TabController` . 


Agregue el código siguiente a la `TabController` clase para conseguir estos pasos:

```csharp
using System;
using UIKit;

namespace TabbedApplication {
        public class TabController : UITabBarController {

                UIViewController tab1, tab2, tab3;

                public TabController ()
                {
                        tab1 = new UIViewController();
                        tab1.Title = "Green";
                        tab1.View.BackgroundColor = UIColor.Green;

                        tab2 = new UIViewController();
                        tab2.Title = "Orange";
                        tab2.View.BackgroundColor = UIColor.Orange;

                        tab3 = new UIViewController();
                        tab3.Title = "Red";
                        tab3.View.BackgroundColor = UIColor.Red;

                        var tabs = new UIViewController[] {
                                tab1, tab2, tab3
                        };

                        ViewControllers = tabs;
                }
        }
}
```

Tenga en cuenta que para cada `UIViewController` instancia, establecemos la `Title` propiedad de la `UIViewController`. Cuando los controladores se agregan a la `UITabBarController`, el `UITabBarController` leerá el `Title` para cada controlador y mostrarlo en la etiqueta de ficha asociado tal y como se muestra a continuación:

[![](creating-tabbed-applications-images/00-app.png "Ejecutar la aplicación de ejemplo")](creating-tabbed-applications-images/00-app.png#lightbox)

#### <a name="setting-the-tabcontroller-as-the-rootviewcontroller"></a>Establecer el TabController como la RootViewController

El orden en que los controladores se colocan en las pestañas se corresponde con el orden en que se agregan a la `ViewControllers` matriz.

Para obtener la `UITabController` para cargar como la primera pantalla, necesitamos asegurarnos de la ventana `RootViewController`, tal y como se muestra en el siguiente código para el `AppDelegate`:

```csharp
[Register ("AppDelegate")]
        public partial class AppDelegate : UIApplicationDelegate
        {
                UIWindow window;
                TabController tabController;

                public override bool FinishedLaunching (UIApplication app, NSDictionary options)
                {
                        window = new UIWindow (UIScreen.MainScreen.Bounds);

                        var tabController = new TabController ();
                        window.RootViewController = tabController;

                        window.MakeKeyAndVisible ();
            
                        return true;
                }
        }
```

Si se ejecuta la aplicación ahora, el `UITabBarController` se cargará con la primera pestaña seleccionada de forma predeterminada. Si se selecciona cualquiera de las otras pestañas ver los resultados en el controlador asociado que se presenta la `UITabBarController,` tal y como se muestra a continuación donde el usuario final ha seleccionado la segunda pestaña:

[![](creating-tabbed-applications-images/03-secondtab.png "La segunda pestaña que se muestra")](creating-tabbed-applications-images/03-secondtab.png#lightbox)

 <a name="Modifying_TabBarItems" />


### <a name="modifying-tabbaritems"></a>Modificar TabBarItems

Ahora que tenemos un ejecución pestaña aplicación, vamos a modificar el `TabBarItem` para cambiar la imagen y texto que se muestra, así como para agregar un distintivo a una de las pestañas.

 <a name="Setting_a_System_Item" />


#### <a name="setting-a-system-item"></a>Establecer un elemento del sistema

En primer lugar, vamos a establecer la primera ficha para utilizar un elemento del sistema. En el constructor de la `TabController`, quite la línea que establece el controlador `Title` para el `tab1` instancia y reemplácelo por el código siguiente para establecer el controlador `TabBarItem` propiedad:

```csharp
tab1.TabBarItem = new UITabBarItem (UITabBarSystemItem.Favorites, 0);
```

Al crear el `UITabBarItem` con un `UITabBarSystemItem`, el título y la imagen se proporcionan automáticamente con iOS, tal como se muestra en la captura de pantalla siguiente muestra la **favoritos** icono y el título en la primera pestaña:

 ![](creating-tabbed-applications-images/04a-tabimage.png "La primera pestaña con un icono de estrella")

 <a name="Setting_the_Title_and_Image" />


#### <a name="setting-the-title-and-image"></a>Establecer el título y la imagen

Además de utilizar un elemento del sistema, el título y la imagen de un `UITabBarItem` puede establecerse en valores personalizados. Por ejemplo, cambiar el código que establece la `TabBarItem` propiedad del controlador denominado `tab2` como se indica a continuación:

```csharp
tab2 = new UIViewController ();
tab2.TabBarItem = new UITabBarItem ();
tab2.TabBarItem.Image = UIImage.FromFile ("second.png");
tab2.TabBarItem.Title = "Second";
tab2.View.BackgroundColor = UIColor.Orange;
```

El código anterior supone una imagen denominada `second.png` se ha agregado a la raíz del proyecto en Visual Studio para Mac. Hemos agregado realmente tres imágenes para el proyecto, para cubrir todas las resoluciones de dispositivo, tal y como se muestra a continuación:

 [![](creating-tabbed-applications-images/tabbedimages7new.png "Las imágenes que se agrega al proyecto")](creating-tabbed-applications-images/tabbedimages7new.png#lightbox)

La imagen de la ficha debe ser un archivo png de 30 x 30 con transparencia para la resolución normal, 60 x 60 para alta resolución y 90 x 90 para iPhone 6 Plus resolución. En el código, solo es necesario cargar el archivo denominado `second.png` y iOS cargará automáticamente la alta resolución en dispositivos con una pantalla Retina display. Puede leer más sobre esto en la [trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md) guías. De forma predeterminada elementos de la barra de pestaña son grises, con un tono azul cuando se selecciona.

**Nota:**

Las imágenes anteriores también podrían agregarse a la **recursos** directorio, que es un directorio especial cuyo contenido se copiará automáticamente a la raíz de la agrupación de aplicaciones:

[![](creating-tabbed-applications-images/tabbedapplication8.png "Las imágenes como recursos")](creating-tabbed-applications-images/tabbedapplication8.png#lightbox)

Además, cuando se establece la `Title` propiedad directamente en el `TabBarItem`, invalidaría cualquier valor establecido para `Title` en el controlador.

Cuando se ejecuta la aplicación ahora, la segunda pestaña muestra el título personalizado e imagen tal y como se muestra a continuación:

[![](creating-tabbed-applications-images/05-customtab.png "La segunda pestaña con un icono cuadrado")](creating-tabbed-applications-images/05-customtab.png#lightbox)

 <a name="Setting_the_Badge_Value" />


#### <a name="setting-the-badge-value"></a>Establecer el valor del distintivo

Una pestaña también puede mostrar un distintivo. Por ejemplo, agregue la siguiente línea de código para establecer un distintivo en la tercera ficha:

```csharp
tab3.TabBarItem.BadgeValue = "Hi";
```

Si se ejecuta resulta en una etiqueta de color rojo con la cadena "Hola" en la esquina superior izquierda de la pestaña tal y como se muestra a continuación:

[![](creating-tabbed-applications-images/06-badge.png "La segunda pestaña con un distintivo Hi")](creating-tabbed-applications-images/06-badge.png#lightbox)

El distintivo a menudo se usa para mostrar una indicación numérica no leída, nuevos elementos. Para quitar el distintivo, establezca el `BadgeValue` en null como se muestra a continuación:

```csharp
tab3.TabBarItem.BadgeValue = null;
```

 <a name="Tabs_in_Non-RootViewController_Scenarios" />


## <a name="tabs-in-non-rootviewcontroller-scenarios"></a>Pestañas en escenarios no RootViewController

En el ejemplo anterior, mostramos cómo trabajar con un `UITabBarController` cuando es el `RootViewController` de la ventana. En este ejemplo examinaremos cómo usar un `UITabBarController` cuando no es la `RootViewController` y mostrar cómo se crea usar guiones gráficos.

 <a name="Initial_Screen_Example" />


### <a name="initial-screen-example"></a>Ejemplo de la pantalla inicial

En este escenario, la pantalla inicial se carga desde un controlador que no es un `UITabBarController`. Cuando el usuario interactúa con la pantalla punteando un botón, se cargará el mismo controlador de vista en un `UITabBarController`, que, a continuación, se presenta al usuario. Captura de pantalla siguiente muestra el flujo de la aplicación:

[![](creating-tabbed-applications-images/inital-screen-application.png "Esta captura de pantalla muestra el flujo de la aplicación")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)

Vamos a iniciar una nueva aplicación para este ejemplo. Una vez más, vamos a usar la **iPhone > aplicaciones > proyecto vacío (C#)** plantilla, esta vez el proyecto de nomenclatura `InitialScreenDemo`.


En este ejemplo se necesitará un guión gráfico para mantener los controladores de la vista. Para agregar un guión gráfico:

- Haga doble clic en el nombre del proyecto y seleccione **Agregar > nuevo archivo**.

- Cuando aparezca el cuadro de diálogo nuevo archivo, vaya a **iOS > vacía iPhone guión gráfico**.

Vamos a llamar a este guión gráfico nuevo **MainStoryboard** , como se muestra a continuación: 

[![](creating-tabbed-applications-images/new-file-dialog.png "Agregue un archivo MainStoryboard al proyecto")](creating-tabbed-applications-images/new-file-dialog.png#lightbox)

Hay unos pocos pasos importantes tener en cuenta al agregar un guion gráfico a un archivo previamente sin guiones gráficos, que se tratan en la [Introducción a guiones gráficos](~/ios/user-interface/storyboards/index.md) guía. Estos son:

 
1. Agregar el nombre del guión gráfico a la **interfaz principal** sección de la `Info.plist`:

    [![](creating-tabbed-applications-images/project-options.png "Establece la interfaz principal en MainStoryboard")](creating-tabbed-applications-images/project-options.png#lightbox)
1. En su `App Delegate`, invalide el método de ventana, con el código siguiente:

    ```csharp
    public override UIWindow Window {
      get;
      set;
    }
    ```

Vamos a necesita tres controladores de la vista para este ejemplo. Uno, denominado `ViewController1`, se usará como el controlador de vista inicial y en la primera pestaña. Los otros dos, denominados `ViewController2` y `ViewController3`, que se utilizarán en las pestañas de la segunda y terceros respectivamente.

Abra el diseñador haciendo doble clic en el archivo MainStoryboard.storyboard y arrastre tres controladores de la vista en la superficie de diseño. Queremos que cada uno de estos controladores de la vista tiene su propia clase que corresponde al nombre anterior, por lo tanto, en **identidad > clase**, escriba su nombre, como se muestra en la captura de pantalla siguiente:

[![](creating-tabbed-applications-images/class-name.png "Defina la clase como ViewController1")](creating-tabbed-applications-images/class-name.png#lightbox)

Visual Studio para Mac generará automáticamente las clases y los archivos de diseñador que sea necesario, esto puede verse en el panel de la solución, como se muestra a continuación:

[![](creating-tabbed-applications-images/solution-pad2.png "Archivos generados automáticamente en el proyecto")](creating-tabbed-applications-images/solution-pad2.png#lightbox)

 <a name="Creating_the_UI" />


#### <a name="creating-the-ui"></a>Crear la interfaz de usuario

A continuación, vamos a crear una interfaz de usuario sencilla para cada una de las vistas del ViewController, usar el Diseñador de iOS de Xamarin.

Desea arrastrar un `Label` y un `Button` en ViewController1 desde el **cuadro de herramientas** en el lado derecho. A continuación vamos a usar el panel de propiedades para editar el nombre y el texto de los controles a lo siguiente:

-  **Etiqueta** : `Text`  =  **uno**
-  **Botón** : `Title`  =  **usuario realice alguna acción inicial**


Se puede controlar la visibilidad de nuestro botón en un `TouchUpInside` eventos y tenemos que hacer referencia a él en el código subyacente. Vamos a identificarlo con la **nombre** `aButton` en el panel de propiedades, como se describe en la siguiente captura de pantalla:

[![](creating-tabbed-applications-images/abutton-properties.png "Establezca el nombre en aButton en el panel de propiedades")](creating-tabbed-applications-images/abutton-properties.png#lightbox)

La superficie de diseño ahora debe ser similar a la captura de pantalla siguiente:

[![](creating-tabbed-applications-images/design-surface1.png "La superficie de diseño ahora debe ser similar a esta captura de pantalla")](creating-tabbed-applications-images/design-surface1.png#lightbox)

Vamos a agregar un poco más detalle a `ViewController2` y `ViewController3`, agregue una etiqueta a cada uno de ellos y cambiando el texto 'Dos' y 'Tres' respectivamente. Esto resalta al usuario que estamos examinando de pestaña o vista.

#### <a name="wiring-up-the-button"></a>El botón de cableado

Vamos a cargar `ViewController1` cuando la aplicación inicia por primera vez. Cuando el usuario puntea el botón, se podrá ocultar el botón y cargar un `UITabBarController` con el `ViewController1` perteneciente a la primera pestaña.

Cuando el usuario suelta el `aButton`, queremos que se desencadena un evento de TouchUpInside. Vamos a seleccionar el botón y en el **ficha eventos** del panel Propiedades, declare el controlador de eventos: `InitialActionCompleted` : por lo que puede hacer referencia en el código. Esto se muestra en la captura de pantalla siguiente:

[![](creating-tabbed-applications-images/event-handler.png "Cuando el usuario suelta el aButton, desencadenar un evento TouchUpInside")](creating-tabbed-applications-images/event-handler.png#lightbox)

Ahora necesitamos saber el controlador de vista para ocultar el botón cuando se desencadene el evento `InitialActionCompleted`. En `ViewController1`, agregue el método parcial siguiente:

```csharp
partial void InitialActionCompleted (UIButton sender)
    {
      aButton.Hidden = true;  
    }
```

Guarde el archivo y ejecutar la aplicación. Debemos vemos de pantalla aparecerá uno y el botón desaparecer en Retocar.

#### <a name="adding-the-tab-bar-controller"></a>Agregar la barra de pestañas Controller

Ahora tenemos nuestro punto de vista inicial funciona según lo esperado. A continuación, queremos que agregue a un `UITabBarController`, junto con vistas 2 y 3. Vamos a abrir el guión gráfico en el diseñador.

En el **cuadro de herramientas**, busque la **ficha barra controlador** en controladores de & objetos y arrástrelo hasta la superficie de diseño. Como puede ver en la captura de pantalla siguiente, el controlador de la barra de ficha es menor de la interfaz de usuario y, por tanto, pone dos controladores de la vista con él de forma predeterminada:

[![](creating-tabbed-applications-images/tabbarcontroller.png "Agregar un controlador de la barra de ficha al diseño del")](creating-tabbed-applications-images/tabbarcontroller.png#lightbox)

Eliminar estos nuevos controladores de vista, seleccione la barra de color negra en la parte inferior y presionando la tecla SUPR.

En el guión gráfico, podemos usar Segues para controlar las transiciones entre el TabBarController y nuestros controladores de la vista. Después de interactuar con la vista inicial, queremos cargar en el TabBarController presenta al usuario. Vamos a configurar esto en el diseñador.

**CTRL-clic** y **arrastre** desde el botón para el TabBarController. En el mouse (ratón), aparecerá un menú contextual. Desea usar un segue modal. 
 
Para configurar cada una de nuestras pestañas, **Ctrl-clic** desde el TabBarController a cada uno de nuestros controladores de la vista en el orden de uno a tres y seleccione la relación **ficha** en el menú contextual, como se muestra a continuación:

[![](creating-tabbed-applications-images/context-menu.png "Seleccione la ficha relación")](creating-tabbed-applications-images/context-menu.png#lightbox)

El guión gráfico debe ser similar a la captura de pantalla siguiente:

[![](creating-tabbed-applications-images/segue-layout.png "El guión gráfico debe ser similar a esta captura de pantalla")](creating-tabbed-applications-images/segue-layout.png#lightbox)

Si se hace clic en uno de los elementos de barra de la pestaña y explorar el panel de propiedades, puede ver una serie de opciones diferentes, como se muestra a continuación:

[![](creating-tabbed-applications-images/properties-panel.png "Establecer las opciones de la pestaña en el Explorador de propiedades")](creating-tabbed-applications-images/properties-panel.png#lightbox)

Podemos utilizar esto para editar determinados atributos, como el distintivo, el título y el archivo iOS [identificador](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/UIKitUICatalog/TabBarItem.html), entre otros

Si se guarde y ejecute la aplicación ahora, buscaremos que el botón vuelve a aparecer cuando la instancia de ViewController1 se cargan en el TabBarController. Vamos a solucionar este problema y comprueba si la vista actual tiene un controlador de vista de un elemento primario. Si es así, sabemos que se encuentren dentro de la TabBarController, y, por tanto, se debe ocultar el botón. Vamos a agregar el código siguiente a la clase ViewController1:

```csharp
public override void ViewDidLoad ()
{
     if (ParentViewController != null){
       aButton.Hidden = true;
     }

}
```

Cuando se ejecuta la aplicación y el usuario puntee en el botón en la primera pantalla, el UITabBarController se ha cargado, con la vista de la primera pantalla que se coloca en la primera ficha, tal y como se muestra a continuación:

[![](creating-tabbed-applications-images/first-view.png "La salida de la aplicación de ejemplo")](creating-tabbed-applications-images/first-view.png#lightbox)

<!--Save the files and run the application:

[![](creating-tabbed-applications-images/inital-screen-application.png "Save the files and run the application")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)-->

## <a name="summary"></a>Resumen

En este artículo se trata cómo usar un `UITabBarController` en una aplicación. Hemos visto cómo cargar controladores en cada pestaña, así como cómo establecer propiedades en las pestañas tales el título, imágenes y distintivos. A continuación, examinamos, con guiones gráficos, cómo cargar un `UITabBarController` en tiempo de ejecución cuando no es la `RootViewController` de la ventana.


## <a name="related-links"></a>Vínculos relacionados

- [Creación de aplicaciones por pestañas (ejemplo)](https://developer.xamarin.com/samples/monotouch/CreatingTabbedApplications/)
- [Images.zip](https://github.com/xamarin/ios-samples/blob/master/CreatingTabbedApplications/Resources/images.zip?raw=true)
- [Referencia de clase UITabBarController](http://developer.apple.com/library/ios/#documentation/uikit/reference/UITabBarController_Class/Reference/Reference.html)
