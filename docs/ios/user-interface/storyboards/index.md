---
title: Introducción a guiones gráficos
description: Un guión gráfico es una representación visual de la apariencia y el flujo de la aplicación. Xamarin presenta un diseñador para permitir que las aplicaciones de Xamarin.iOS aprovechar las ventajas de guiones gráficos, para que pueda diseñar visualmente la pantalla de la aplicación y tener acceso a las vistas, controladores y segues con C# para tener más control.
ms.prod: xamarin
ms.assetid: A3339BD2-9F56-7965-25F5-4B7C991EB775
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 647bd7d339dc56978752f7ab29de30cf8acb7e07
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-storyboards"></a>Introducción a guiones gráficos

_Un guión gráfico es una representación visual de la apariencia y el flujo de la aplicación. Xamarin presenta un diseñador para permitir que las aplicaciones de Xamarin.iOS aprovechar las ventajas de guiones gráficos, para que pueda diseñar visualmente la pantalla de la aplicación y tener acceso a las vistas, controladores y segues con C# para tener más control._

En esta guía se explicará qué un guión gráfico es y examinar algunos de los componentes claves: como Segues. Veremos cómo se pueden crear y utilizar, guiones gráficos y qué ventajas que tienen para un desarrollador.

Antes de que el formato de archivo del guión gráfico se introdujo por Apple como una representación visual de la interfaz de usuario de una aplicación de iOS, los programadores crean archivos XIB para cada controlador de vista y programar manualmente la navegación entre cada vista.  Uso de un guión gráfico permite que un desarrollador definir controladores de la vista y la navegación entre ellos en una superficie de diseño y ofrece una edición WYSIWYG de interfaz de usuario de la aplicación.

Un guión gráfico puede crear, abrir y editar con el Diseñador de iOS de Xamarin. Esta guía le tutorial también cómo usar el diseñador para crear guiones gráficos al usar C# para programar el panel de navegación.


## <a name="requirements"></a>Requisitos

Guiones gráficos se pueden utilizar con el Diseñador de Visual Studio para Mac de iOS o con Visual Studio 2015 y 2017 con las cargas de trabajo de Xamarin instalados.

## <a name="what-is-a-storyboard"></a>¿Qué es un guión gráfico?

Un guión gráfico es la representación visual de todas las pantallas en una aplicación. Contiene una secuencia de plano, con cada escena que representa un *View Controller* y su *vistas*. Estas vistas pueden contener objetos y [controles](~/ios/user-interface/controls/index.md) que permitirá que el usuario interactuar con la aplicación. Esta colección de controles y vistas (o *subvistas*) se conoce como un *jerarquía de vista de contenido*. Plano está conectado por desplazará tranquilamente objetos que representan una transición entre los controladores de la vista. Normalmente, esto se logra mediante la creación de un segue entre un objeto en la vista inicial y la vista de la conexión. En la imagen siguiente se muestran las relaciones en la superficie de diseño:

 [![](images/storyboardsview.png "Se ilustran las relaciones en la superficie de diseño en esta imagen")](images/storyboardsview.png#lightbox)

Como se muestra, el guión gráfico disposición de cada una de la escena con contenido que ya se hayan procesado y muestra las conexiones entre ellos.  Cabe mencionar en este punto, que cuando hablemos sobre plano en un iPhone, resulta seguro presuponer que uno *escena* en el guión gráfico es igual a uno *pantalla* de contenido en el dispositivo. Sin embargo, con un iPad que es posible tener varias escenas aparecen a la vez: por ejemplo, mediante un controlador de vista Popover.

Hay muchas ventajas en el uso de guiones gráficos para crear la interfaz de usuario de la aplicación, especialmente cuando se usa Xamarin. En primer lugar, es una representación visual de la interfaz de usuario, como todos los objetos, incluidos los [controles personalizados](~/ios/user-interface/designer/ios-designable-controls-overview.md) – se representan en tiempo de diseño. Esto significa que antes de compilar o implementar la aplicación, puede ver su apariencia y el flujo. Tomemos como ejemplo la imagen anterior. Le podemos deducir a partir de un vistazo el diseño son escenas cuántos expuestas no existe, el diseño de cada vista y cómo todo lo que está relacionada con. Esto es lo que hace tan eficaz a guiones gráficos.

Los eventos son más fáciles de administrar con guiones gráficos, especialmente cuando se utiliza el Diseñador de iOS. La mayoría de los controles de interfaz de usuario tendrá una lista de posibles eventos en el panel de propiedades. El controlador de eventos se puede agregar aquí y completado en un método parcial en la clase de controladores de la vista...

El contenido de un guión gráfico se almacena como un archivo XML. Hora, la compilación en cualquier `.storyboard` archivos se compilan en archivos binarios que se conoce como nibs. En tiempo de ejecución, estos nibs se inicializan y se crea una instancia para crear nuevas vistas.

## <a name="segues"></a>Segues

A *Segue*, o *objeto desplazará tranquilamente*, se utiliza en el desarrollo de iOS para representar una transición entre bastidores. Para crear un segue, mantenga presionada la **Ctrl** clave y haga clic y arrastre de una escena a otra. Tal y como se arrastra el mouse, aparece un conector azul, que indica donde llevará el segue como se muestra en la imagen siguiente:

 [![](images/createsegue.png "Aparece un conector azul, que indica donde llevará el segue como se muestra en esta imagen")](images/createsegue.png#lightbox)

En el mouse (ratón), aparecerá un menú que nos permite elegir la acción para nuestro segue. Puede ser similar a las siguientes imágenes: 

**Clases de 8 y el tamaño de iOS anterior**:

[![](images/segue1.png "La lista desplegable acción desplazará tranquilamente sin clases de tamaño")](images/segue1.png#lightbox)

**Cuando se utilizan las clases de tamaño y adaptable Segues**:

[![](images/16new.png "La lista desplegable acción desplazará tranquilamente con clases de tamaño")](images/16new.png#lightbox)

> [!IMPORTANT]
> Si usas VMWare para la máquina Virtual de Windows, pulse la tecla Ctrl se asigna como el _haga_ botón del mouse (ratón) de forma predeterminada. Para crear un Segue, editar sus preferencias de teclado a través de **preferencias** > **teclado y mouse (ratón)** > **métodos abreviados de Mouse** y reasignar el **Botón secundario** tal como se muestra a continuación:
> 
> [![](images/image22.png "Configuración de preferencias de teclado y mouse (ratón)")](images/image22.png#lightbox)
> 
> Ahora podrá agregar un segue entre los controladores de vista de forma habitual.

Hay diferentes tipos de transiciones, cada control sobre cómo se presenta un nuevo controlador de vista para el usuario y cómo interactúa con otros controladores de la vista en el guión gráfico determinado. A continuación se explican estos. También es posible crear subclases de un objeto de segue para implementar una transición personalizada:

-  **Mostrar / Push** : una inserción desplazará tranquilamente agrega el controlador de vista a la pila de navegación. Se supone que el controlador de vista que se origina la inserción de forma parte del mismo controlador de navegación como el controlador de vista que se va a agregar a la pila. Esto hace lo mismo que `pushViewController` y se utiliza generalmente cuando hay alguna relación entre los datos en las pantallas. Mediante la inserción desplazará tranquilamente ofrece el lujo de tener una barra de navegación con un botón Atrás y el título que se agrega a cada vista en la pila, lo que permite explorar en profundidad la navegación a través de la jerarquía de vista.
-  **Modal** : segue modal crea una relación entre los controladores de la vista de dos en el proyecto, con la opción de una transición animada está mostrando. El controlador de vista secundario ocultará completamente el controlador de vista principal cuando se incorporan a la vista. A diferencia de una inserción desplazará tranquilamente, que agrega un botón Atrás para que podamos; al usar modal desplazará tranquilamente `DismissViewController` debe utilizarse para devolver para el controlador de vista anterior.
-  **Personalizado** : cualquier personalizado segue puede crearse como una subclase de ` UIStoryboardSegue`.
-  **Desenredado** : desenredado desplazará tranquilamente puede utilizarse para navegar hacia atrás por una inserción o modal desplazará tranquilamente: por ejemplo, descartando el controlador de vista presenta de forma modal. Además, puede desenredar a través de no solo uno, pero una serie de inserción y modal segues y volver atrás varios pasos en la jerarquía de navegación con una sola acción de desenredado. Para aprender a usar un desenredado desplazará tranquilamente en iOS, lea la [crear desenredo Segues](https://developer.xamarin.com/recipes/ios/general/storyboard/unwind_segue/) receta.
-  **Sourceless** : un segue sourceless indica la escena que contiene el controlador de vista inicial y, por tanto, la vista donde el usuario verá en primer lugar. Se representa mediante el segue que se muestra a continuación:  

    [![](images/sourcelesssegue.png "Un segue sourceless")](images/sourcelesssegue.png#lightbox)

### <a name="adaptive-segue-types"></a>Adaptable desplazará tranquilamente tipos

 iOS 8 introducidas [clases de tamaño](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes) para permitir que un archivo de guión gráfico de iOS para que funcione con todos los tamaños de pantalla disponible, permitiendo a los programadores crear una interfaz de usuario para todos los dispositivos iOS. De forma predeterminada, todas las nuevas aplicaciones de Xamarin.iOS usará las clases de tamaño. Para usar las clases de tamaño de un proyecto anterior, consulte la [Introducción a guiones gráficos unificado](~/ios/user-interface/storyboards/unified-storyboards.md) guía. 
 
Las aplicaciones que utilizan las clases de tamaño también usarán el nuevo [ *Segues adaptable*](~/ios/user-interface/storyboards/unified-storyboards.md). Cuando use las clases de tamaño, recuerde que no estamos especificar directamente si se va a estamos usando un iPhone o iPad. En otras palabras, vamos a crear una interfaz de usuario que siempre será el mismo, independientemente de cuánto espacio real tiene que trabajar con. Trabajo Segues Adaptive juzgar el entorno y determinar cuál es la mejor presentar el contenido. A continuación se muestra el Segues adaptable: 

[![](images/adaptivesegue.png "La lista desplegable Segues adaptable")](images/adaptivesegue.png#lightbox)

|Desplazará tranquilamente|Descripción|
|--- |--- |
|Mostrar|Esto es muy similar a una inserción desplazará tranquilamente, pero tiene en cuenta el contenido de la pantalla.|
|Mostrar detalles|Si la aplicación muestra una vista maestra y de detalles (por ejemplo, en un controlador de vista de división en un iPad), el contenido reemplazará la vista de detalle. Si la aplicación solo muestra el patrón o detalles, el contenido reemplazará la parte superior de la pila de controlador de vista.|
|Presentación|Esto es similar a la segue Modal y permite la selección de los estilos de presentación y transición.|
|Presentación de popover|Esto presenta el contenido como un popover|

### <a name="transferring-data-with-segues"></a>Transferencia de datos con Segues

Las ventajas de un segue no terminen con las transiciones. También puede utilizar para administrar a la transferencia de datos entre los controladores de la vista. Esto se logra mediante la invalidación del `PrepareForSegue` método en el controlador de vista inicial y controlar los datos de nosotros mismos. Cuando se desencadene el segue: por ejemplo, con presionar un botón, la aplicación llamará este método, que proporciona una oportunidad para preparar el nuevo controlador de vista *antes de* se produce ninguna navegación. El código siguiente, desde la [Phoneword](https://developer.xamarin.com/samples/monotouch/Hello_iOS/) de ejemplo, se muestra cómo hacerlo: 


```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, 
NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    var callHistoryContoller = segue.DestinationViewController 
                                  as CallHistoryController;

    if (callHistoryContoller != null) {
        callHistoryContoller.PhoneNumbers = PhoneNumbers;
    }
}
```

En este ejemplo, el `PrepareForSegue` se llamará al método cuando se desencadene el segue por el usuario. En primer lugar, tenemos que crear una instancia del controlador de vista 'recepción' y configúrelo como destino de segue View Controller. Esto se realiza mediante la línea de código siguiente:

```csharp
var callHistoryContoller = segue.DestinationViewController as CallHistoryController;
```

El método tiene la capacidad de establecer las propiedades de la `DestinationViewController`. En este ejemplo, hemos aprovechado de esto, pase una lista denominada `PhoneNumbers` a la `CallHistoryController` y asignarlo a un objeto del mismo nombre:

```csharp
if (callHistoryContoller != null) {
        callHistoryContoller.PhoneNumbers = PhoneNumbers;
    }
```

Una vez que haya finalizado la transición, el usuario verá la `CallHistoryController` con la lista rellenada.

## <a name="adding-a-storyboard-to-a-non-storyboard-project"></a>Agregar un guion gráfico a un proyecto sin guiones gráficos

En ocasiones necesitará agregar un guion gráfico a un archivo previamente sin guiones gráficos. Es posible simplificar la hacerlo una vez en Visual Studio para Mac, siga los pasos siguientes:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Crear un nuevo archivo de guión gráfico examinando **archivo > nuevo archivo > iOS > guión gráfico**, como se muestra a continuación: 
    
    [![](images/new-storyboard-xs.png "El cuadro de diálogo de archivo nuevo")](images/new-storyboard-xs.png#lightbox)

2. Agregar el nombre del guión gráfico a la **interfaz principal** sección de la **Info.plist**, tal y como se muestra a continuación:
    
    [![](images/infoplist.png "El editor de Info.plist")](images/infoplist.png#lightbox)
    
    Esto realiza el equivalente de una instancia el controlador de vista inicial en el `FinishedLaunching` método dentro del delegado de la aplicación. Con esta opción activada, la aplicación crea una instancia de una ventana (ver abajo), carga el guión gráfico principal y asigna una instancia de controlador de vista inicial del guión gráfico (el uno al lado de la Segue sourceless) como el `RootViewController` propiedad de la ventana y, a continuación, hace la ventana visible en la pantalla.

3. En el `AppDelegate`, invalidar el valor predeterminado `Window` método, con el código siguiente para implementar la propiedad de la ventana:
        
        public override UIWindow Window {
            get;
            set;
            }
            
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Crear un nuevo archivo de guión gráfico con el botón secundario en el proyecto para **Agregar > nuevo archivo > iOS > guión gráfico vacío**, como se muestra a continuación: 
    
    [![](images/new-storyboard-vs.png "El cuadro de diálogo nuevo elemento")](images/new-storyboard-vs.png#lightbox)

2. Agregar el nombre del guión gráfico a la **interfaz principal** sección de la aplicación, tal y como se muestra a continuación de iOS:
    
    [![](images/ios-app.png "El editor de Info.plist")](images/ios-app.png#lightbox)
    
    Esto realiza el equivalente de una instancia el controlador de vista inicial en el `FinishedLaunching` método dentro del delegado de la aplicación. Con esta opción activada, la aplicación crea una instancia de una ventana (ver abajo), carga el guión gráfico principal y asigna una instancia de controlador de vista inicial del guión gráfico (el uno al lado de la Segue sourceless) como el `RootViewController` propiedad de la ventana y, a continuación, hace la ventana visible en la pantalla.

3. En el `AppDelegate`, invalidar el valor predeterminado `Window` método, con el código siguiente para implementar la propiedad de la ventana:

        public override UIWindow Window {
            get;
            set;
            }
            
-----

## <a name="creating-a-storyboard-with-the-ios-designer"></a>Creación de un guión gráfico con el Diseñador de iOS

Un guión gráfico se puede crear mediante el Diseñador de Xamarin para iOS, que se ha integrado perfectamente con Visual Studio para Mac y Visual Studio.

Para empezar a usar el Diseñador de iOS para crear guiones gráficos, siga el [Hello, iOS Multiscreen](~/ios/get-started/hello-ios-multiscreen/index.md) guía. En este tutorial explorará la navegación entre los controladores de vista usando Segues y cómo controlar eventos en los controles.

## <a name="instantiate-storyboards-manually"></a>Crear manualmente una instancia de guiones gráficos

Guiones gráficos reemplazar completamente archivos XIB individuales en el proyecto, sin embargo, los controladores de vista individuales en un guión gráfico todavía se pueden crear instancias mediante `Storyboard.InstantiateViewController`.

A veces, las aplicaciones tienen requisitos especiales que no se pueden administrar con las transiciones de guión gráfico integrados proporcionadas por el diseñador. Por ejemplo, si se fuera a crear una aplicación que inicia distintas pantallas en el mismo botón, según el estado actual de una aplicación, podemos queremos crear manualmente una instancia de los controladores de vista y la transición de programas nosotros mismos.

La captura de pantalla siguiente muestra dos controladores de la vista en la superficie de diseño con n desplazará tranquilamente entre ellos. La siguiente sección le guiará a través de cómo configurar esa transición en el código.

 [![](images/viewcontrollerspink.png "Esta captura de pantalla muestra dos controladores de la vista en la superficie de diseño con n desplazará tranquilamente entre ellos")](images/viewcontrollerspink.png#lightbox)

1. Agregar un _vacía iPhone guión gráfico_ a un proyecto existente de proyecto:
    
    [![](images/add-storyboard1.png "Agregar a guión gráfico")](images/add-storyboard1.png#lightbox)

2. Haga doble clic en el guión gráfico recién creado para abrirlo y agregue un nuevo **controlador navegación** a la superficie de diseño. Como el controlador de navegación es menor de interfaz de usuario, de forma predeterminada, vienen con un controlador de vista raíz, como se muestra a continuación:

    [![](images/uinavigationcontroller.png "Ver controladores con Segues")](images/uinavigationcontroller.png#lightbox)

3. Seleccione el _View Controller_ haciendo clic en la barra de color negra en la parte inferior. En el diseñador **propiedad panel**, en **identidad** se puede especificar una clase personalizada, así como un identificador único para el controlador de vista. Establecer el **nombre de la clase** y **Id. de guión gráfico** a `MainViewController`.

    [![](images/identitypanelnew.png "Especifique la clase personalizada")](images/identitypanelnew.png#lightbox)

4. Más adelante, tendrá que crear una instancia de nuestro controladores de la vista de guión gráfico y usará el identificador de guión gráfico para hacer referencia a ellos en el código. Establecer el identificador de restauración para que coincida con el identificador de guión gráfico, se garantiza que obtiene a volver a crear correctamente el controlador de vista si el estado debe restaurarse.

5. Actualmente solo tenemos un controlador de vista. Arrastre otro controlador de vista en la superficie de diseño. En el **propiedad panel**, bajo la identidad, establezca la clase y el Id. de guión gráfico para `PinkViewController`, como se muestra a continuación:

    [![](images/pinkvcnew.png "El panel de propiedades")](images/pinkvcnew.png#lightbox)
    
    El IDE creará estas clases personalizadas para los controladores de vista. Estos se pueden ver en la **solución Pad**, tal y como se muestra en la captura de pantalla siguiente:
    
    [![](images/solution-pad.png "Panel de soluciones")](images/solution-pad.png#lightbox)

6. En el `PinkViewController`, seleccione la vista haciendo clic en hacia el centro del marco del controlador. En el panel Propiedades, en la vista cambie la **fondo** fucsia:
    
    [![](images/pinkcontroller.png "Establecer el color de fondo")](images/pinkcontroller.png#lightbox)

7. Por último, arrastre un botón de la **cuadro de herramientas** en el `MainViewController`. En el panel Propiedades, asígnele el nombre `PinkButton` y GoToPink de título, como se muestra a continuación:

    [![](images/pinkbutton.png "Nombre del conjunto de botón")](images/pinkbutton.png#lightbox)

El guión gráfico es completando, pero si se implementa el proyecto ahora, obtenemos una pantalla en blanco. Eso es porque necesitamos indicarle al IDE que se va a utilizar el guión gráfico como configurar un controlador de vista de raíz para que actúe como la primera vista. Normalmente esto puede hacerse a través de nuestras opciones de proyecto, como se indicó anteriormente. Sin embargo en este ejemplo se conseguirá el mismo resultado en el código, debe agregar lo siguiente a la **AppDelegate**:

```csharp
public partial class AppDelegate : UIApplicationDelegate
    {
        UIWindow window;
        public static UIStoryboard Storyboard = UIStoryboard.FromName ("MainStoryboard", null);
        public static UIViewController initialViewController;

        public override bool FinishedLaunching (UIApplication app, NSDictionary options)
        {
            window = new UIWindow (UIScreen.MainScreen.Bounds);

            initialViewController = Storyboard.InstantiateInitialViewController () as UIViewController;

            window.RootViewController = initialViewController;
            window.MakeKeyAndVisible ();
            return true;
        }

    }
```

Es una gran cantidad de código, pero solo unas pocas líneas están familiarizadas. En primer lugar, registramos el guión gráfico con la **AppDelegate** pasando en nombre del guión gráfico, **MainStoryboard**. A continuación, le indicamos a la aplicación para crear instancias de un controlador de vista inicial desde el guión gráfico mediante una llamada a `InstantiateInitialViewController` en el guión gráfico, y se establece ese controlador de vista como controlador de vista de raíz de la aplicación. Este método determina la primera pantalla que ve el usuario, y crea una nueva instancia de ese controlador de vista.

Observe que en el panel de la solución que ha creado el IDE una `MainViewcontroller.cs` (clase) y su `corresponding designer.cs` cuando se agrega el nombre de clase en el panel de propiedades en el paso 4. Podemos ver esta clase crea un constructor especial que incluye una clase base:

```csharp
public MainViewController (IntPtr handle) : base (handle) 
{
}
```


Cuando se crea un guión gráfico mediante el diseñador, el IDE agrega automáticamente el [[registrar]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/) atributo en la parte superior de la `designer.cs` clase y pasar un identificador de cadena, que es idéntico para el identificador de guión gráfico especificado en el paso anterior. Esto vinculará el C# a la escena relevante en el guión gráfico.

En algún momento desea agregar una clase existente que era **no** creado en el diseñador. En este caso, se registra esta clase como normal:

```csharp
[Register ("MainViewController")]
public partial class MainViewController : UIViewController
{
public MainViewController (IntPtr handle) : base (handle) 
{
}

...
}
```

Para obtener más información sobre cómo registrar clases y métodos, consulte el [registrador de tipo](http://docs.xamarin.com/guides/ios/advanced_topics/registrar/) documentación.

El último paso de esta clase es para conectarlo el botón y la transición para el controlador de vista de color rosa. Se deberá crear una instancia de la `PinkViewController` desde el guión gráfico; a continuación, se programará una inserción desplazará tranquilamente con `PushViewController`, tal y como se muestra en el ejemplo de código siguiente:

```csharp
public partial class MainViewController : UIViewController
{
    UIViewController pinkViewController;

    public MainViewController (IntPtr handle) : base (handle)
    {

    }

    public override void AwakeFromNib ()
    {
    // Called when loaded from xib or storyboard.

    this.Initialize ();
    }

    public void Initialize(){

    //Instantiating View Controller with Storyboard ID 'PinkViewController'
    pinkViewController = Storyboard.InstantiateViewController ("PinkViewController") as PinkViewController;
    }

    public override void ViewDidLoad ()
    {
    base.ViewDidLoad ();

    //When we push the button, we will push the pinkViewController onto our current Navigation Stack
    PinkButton.TouchUpInside += (o, e) =&gt; {
        this.NavigationController.PushViewController (pinkViewController, true);
    };
    }

}
```

Ejecutar la aplicación, genera una aplicación de pantalla de 2:

![](images/finishedstoryboard.png "Aplicación de ejemplo ejecutar pantallas")

## <a name="conditional-segues"></a>Segues condicional

A menudo, mover desde un controlador de vista a la siguiente es depende de una determinada condición. Por ejemplo, si se estábamos realizando una pantalla de inicio de sesión simple sería aconsejable solo pasar a la pantalla siguiente *si* tenían comprobó el nombre de usuario y la contraseña.

En el ejemplo siguiente, vamos a agregar un campo de contraseña para el ejemplo anterior. El usuario solo podrá tener acceso a la *PinkViewController* si escribe la contraseña correcta, en caso contrario, se mostrará un error.

Antes de comenzar, siga realizando los pasos del 1 al 8 anterior. En estos pasos se crea el guión gráfico, empieza a crear la interfaz de usuario y saber qué controlador de vista de nuestro delegado de aplicación sea RootViewController.

1. Ahora, vamos a crear la interfaz de usuario y agregar las vistas adicionales que aparecen en la `MainViewController` para darle un aspecto similar en la captura de pantalla siguiente:

    - UITextField
        - Nombre: PasswordTextField
        - Marcador de posición: "escriba la contraseña secreta"
    - UILabel
        - Texto: ' Error: incorrecto de contraseña. No se deben pasar!'
        - Colores: rojo
        - Alineación: Center
        - Líneas: 2
        - Casilla de verificación 'Hidden' activada 
        
    [![](images/passwordvc.png "Líneas de referencia")](images/passwordvc.png#lightbox)
    
2. Crear un Segue entre el botón Ir a rosa y el controlador de vista arrastrando Ctrl-desde el *PinkButton* a la *PinkViewController*y seleccionando **Push** en mouse hacia arriba . 

3. Haga clic en el Segue y asígnele el *identificador* `SegueToPink`:

    [![](images/namesegue.png "Haga clic en el Segue y asígnele el identificador SegueToPink")](images/namesegue.png#lightbox)  
    

4. Por último, agregue el siguiente método de ShouldPerformSegue a la `MainViewController` clase:

    ```csharp
    public override bool ShouldPerformSegue (string segueIdentifier, NSObject sender)
    {
        
        if(segueIdentifier == "SegueToPink"){
            if (PasswordTextField.Text == "password") {
                PasswordTextField.ResignFirstResponder ();
                return true;
            }
            else{
                ErrorLabel.Hidden = false;
                return false;
            }
        }
        return base.ShouldPerformSegue (segueIdentifier, sender);
    }
    ```

En este código hemos ajustado el segueIdentifier a nuestro `SegueToPink` desplazará tranquilamente, por lo que, a continuación, podemos probar una condición; una contraseña válida en este caso. Si la condición devuelve `true`, llevará a cabo la Segue y presentará el `PinkViewController`. Si `false`, no se presentará el nuevo controlador de vista.

Podemos aplicar este enfoque para cualquier Segue en este controlador de vista activando el argumento segueIdentifier al método ShouldPerformSegue. En este caso sólo se tiene un identificador de Segue: `SegueToPink`.

Hacer referencia a la solución Storyboards.Conditional en el [ejemplo guiones gráficos Manual](https://developer.xamarin.com/samples/monotouch/ManualStoryboard/) para obtener un ejemplo de trabajo.

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>Usar referencias de guión gráfico

Una referencia de guión gráfico le permite tomar un diseño de guión gráfico grande y complejo y dividirla en guiones gráficos más pequeños que obtengan referencia de los originales, por lo tanto quitar quitar complejidad y realizar el individuales guiones gráficos más fácilmente al diseño y mantener.

Además, puede proporcionar una referencia de guión gráfico una _delimitador_ a otra escena en el mismo guión gráfico o una escena específica en uno diferente.

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>Hacer referencia a un guión gráfico externo

Para agregar una referencia a un guión gráfico externo, haga lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en el nombre del proyecto y seleccione **agregar** > **nuevo archivo...**   >  **iOS** > **guión gráfico**. Escriba un **nombre** para el nuevo guión gráfico y haga clic en el **New** botón:
    
    [![](images/ref01.png "El cuadro de diálogo de archivo nuevo")](images/ref01.png#lightbox)
    
2. Definir el diseño de escenas del guión gráfico nuevo como se haría normalmente y guardar los cambios: 
    
    [![](images/ref02.png "El diseño de la escena nueva")](images/ref02.png#lightbox)
    
3. Abra el guión gráfico que se va a agregar la referencia en el Diseñador de iOS.

4. Arrastre un **referencia de guión gráfico** desde el **cuadro de herramientas** a la superficie de diseño: 
    
    [![](images/ref03.png "Una referencia de guión gráfico")](images/ref03.png#lightbox)
    
5. En el **Widget** pestaña de la **el Explorador de propiedades**, seleccione el nombre de la **guión gráfico** que haya creado anteriormente: 

    [![](images/ref04.png "La pestaña de Widget")](images/ref04.png#lightbox)
    
6. Control y haga clic en un Widget de interfaz de usuario (por ejemplo, un botón) en una escena existente y crear un nuevo Segue a la **referencia de guión gráfico** que acaba de crear: 

    [![](images/ref05.png "Crear un segue")](images/ref05.png#lightbox) 
    
7. En el menú emergente seleccione **mostrar** para completar la Segue: 

    [![](images/ref06.png "Seleccione Mostrar para completar la Segue")](images/ref06.png#lightbox) 
    
8. Guarde los cambios en el guión gráfico.

Se mostrará cuando se ejecuta la aplicación y el usuario hace clic en el elemento de interfaz de usuario que creó el Segue desde el controlador de vista inicial desde el guión gráfico externo especificado en la referencia de guión gráfico.

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>Hacer referencia a una escena específica en un guión gráfico externo

Para agregar una referencia a una escena específica un guión gráfico externo (y no el controlador de vista inicial), haga lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en el guión gráfico externo para abrirlo y editarlo.

2. Agregar una nueva escena y se diseñan como lo haría normalmente: 

    [![](images/ref07.png "El nuevo diseño de escena")](images/ref07.png#lightbox)
    
3. En el **Widget** pestaña de la **el Explorador de propiedades**, escriba un **Id. de guión gráfico** para controlador de vista de la escena nueva: 

    [![](images/ref08.png "Escriba un identificador de guión gráfico para el nuevo controlador de vista de plano")](images/ref08.png#lightbox)
    
3. Abra el guión gráfico que se va a agregar la referencia en el Diseñador de iOS.

4. Arrastre un **referencia de guión gráfico** desde el **cuadro de herramientas** a la superficie de diseño: 

    [![](images/ref03.png "Una referencia de guión gráfico")](images/ref03.png#lightbox)
    
5. En el **Widget** pestaña de la **el Explorador de propiedades**, seleccione el nombre de la **guión gráfico** y la **Id. de referencia** (Id. de guión gráfico) de la Escena que haya creado anteriormente: 

    [![](images/ref09.png "La pestaña de Widget ")](images/ref09.png#lightbox)
    
6. Control y haga clic en un Widget de interfaz de usuario (por ejemplo, un botón) en una escena existente y crear un nuevo Segue a la **referencia de guión gráfico** que acaba de crear: 

    [![](images/ref10.png "Crear un segue")](images/ref10.png#lightbox) 
    
7. En el menú emergente seleccione **mostrar** para completar la Segue: 

    [![](images/ref06.png "Seleccione Mostrar para completar la Segue")](images/ref06.png#lightbox) 
    
8. Guarde los cambios en el guión gráfico.

Cuando la aplicación esté ejecución y el usuario hace clic en el elemento de interfaz de usuario que crea el Segue de la escena con la determinada **Id. de guión gráfico** desde el guión gráfico externo especificado en la referencia del guión gráfico se mostrará.

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>Hacer referencia a una escena específica en el mismo guión gráfico

Para agregar una referencia a una escena específica el mismo guión gráfico, haga lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en el guión gráfico para abrirlo y editarlo.

2. Agregar una nueva escena y se diseñan como lo haría normalmente: 

    [![](images/ref11.png "El nuevo diseño de escena")](images/ref11.png#lightbox)

3. En el **Widget** pestaña de la **el Explorador de propiedades**, escriba un **Id. de guión gráfico** para controlador de vista de la escena nueva: 

    [![](images/ref12.png "La pestaña de Widget")](images/ref12.png#lightbox)
    
3. Arrastre un **referencia de guión gráfico** desde el **cuadro de herramientas** a la superficie de diseño: 

    [![](images/ref03.png "Una referencia de guión gráfico")](images/ref03.png#lightbox)
    
5. En el **Widget** pestaña de la **el Explorador de propiedades**, seleccione **identificador de referencia** (Id. de guión gráfico) de la escena que haya creado anteriormente: 

    [![](images/ref13.png "La pestaña de Widget")](images/ref13.png#lightbox)
    
6. Control y haga clic en un Widget de interfaz de usuario (por ejemplo, un botón) en una escena existente y crear un nuevo Segue a la **referencia de guión gráfico** que acaba de crear: 

    [![](images/ref14.png "Crear un segue")](images/ref14.png#lightbox) 
    
7. En el menú emergente seleccione **mostrar** para completar la Segue: 

    [![](images/ref06.png "Seleccione Mostrar para completar la Segue")](images/ref06.png#lightbox) 
    
8. Guarde los cambios en el guión gráfico.

Cuando la aplicación esté ejecución y el usuario hace clic en el elemento de interfaz de usuario que crea el Segue de la escena con la determinada **Id. de guión gráfico** en el mismo guión gráfico especificado en la referencia del guión gráfico se mostrará.

## <a name="summary"></a>Resumen

En este artículo se presenta el concepto de guiones gráficos y cómo pueden ser beneficiosos en el desarrollo de aplicaciones de iOS. Se trata de escenas, controladores de la vista, vistas y las jerarquías de vista y cómo está vinculada plano junto con diferentes tipos de Segues.  También se exploran controladores crear instancias de la vista manualmente desde un guión gráfico y crear Segues condicional.



## <a name="related-links"></a>Vínculos relacionados

- [Guión gráfico manual (ejemplo)](https://developer.xamarin.com/samples/ManualStoryboard/)
- [Introducción al diseñador de iOS](~/ios/user-interface/designer/introduction.md)
- [Convertir a guiones gráficos](http://developer.apple.com/library/ios/#releasenotes/Miscellaneous/RN-AdoptingStoryboards/)
- [Referencia de clase UIStoryboard](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIStoryboard_Class/Reference/Reference.html)
