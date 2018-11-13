---
title: Introducción a guiones gráficos en Xamarin.iOS
description: Este documento proporciona una introducción a guiones gráficos en Xamarin.iOS. Describe cómo un guión gráfico se usa para definir una interfaz de usuario, los objetos Segue y cómo usar el Diseñador de iOS para editar archivos de guión gráfico.
ms.prod: xamarin
ms.assetid: A3339BD2-9F56-7965-25F5-4B7C991EB775
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 554c96cdb46041b75a48523cc2952f05d2a7b1db
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2018
ms.locfileid: "51564010"
---
# <a name="introduction-to-storyboards-in-xamarinios"></a>Introducción a guiones gráficos en Xamarin.iOS

En esta guía se explica qué un guión gráfico es y examine algunos de los componentes claves: por ejemplo, los objetos Segue. Veremos cómo se pueden crear y usar, los guiones gráficos y qué ventajas tienen un desarrollador.

Antes de que el formato de archivo de guión gráfico se introdujo por Apple como una representación visual de la interfaz de usuario de una aplicación de iOS, los desarrolladores crean los archivos XIB por cada controlador de vista y programar manualmente la navegación entre cada vista.  Utilizar un guión gráfico permite al desarrollador definir controladores de vista y la navegación entre ellos en una superficie de diseño y ofrece una edición WYSIWYG de interfaz de usuario de la aplicación.

Un guión gráfico se puede crear, abrir y editar con el Diseñador de iOS de Xamarin. Esta guía también será tutorial cómo usar el diseñador para crear guiones gráficos al usar C# para programar el panel de navegación.


## <a name="requirements"></a>Requisitos

Los guiones gráficos se pueden usar con iOS Designer en Visual Studio para Mac o Visual Studio 2015 y 2017 con las cargas de trabajo de Xamarin instalados.

## <a name="what-is-a-storyboard"></a>¿Qué es un guión gráfico?

Un guión gráfico es la representación visual de todas las pantallas en una aplicación. Contiene una secuencia de segundo plano, con cada escena que representa un *View Controller* y su *vistas*. Estas vistas pueden contener objetos y [controles](~/ios/user-interface/controls/index.md) que permitirá que el usuario interactuar con la aplicación. Esta colección de vistas y los controles (o *subvistas*) se conoce como un *jerarquía de vistas de contenido*. Segundo plano está conectado por segue de objetos que representan una transición entre controladores de vista. Normalmente, esto se logra mediante la creación de un objeto segue entre un objeto en la vista inicial y la vista de la conexión. En la imagen siguiente se muestran las relaciones en la superficie de diseño:

 [![](images/storyboardsview.png "Se ilustran las relaciones en la superficie de diseño en esta imagen")](images/storyboardsview.png#lightbox)

Como se muestra, el guión gráfico disposición de cada uno de sus escenas con contenido que ya se hayan procesado y muestra las conexiones entre ellos.  Es importante destacar en este momento, que, cuando hablamos acerca de las escenas en un iPhone, es seguro suponer que una *escena* en el guión gráfico es igual a uno *pantalla* de contenido en el dispositivo. Sin embargo, con un iPad que es posible tener varias escenas aparecen a la vez: por ejemplo, mediante un controlador de vista de elemento flotante.

Hay muchas ventajas al uso de guiones gráficos para crear la interfaz de usuario de la aplicación, especialmente cuando se usa Xamarin. En primer lugar, es una representación visual de la interfaz de usuario, como todos los objetos, incluidos [controles personalizados](~/ios/user-interface/designer/ios-designable-controls-overview.md) – se representan en tiempo de diseño. Esto significa que, antes de compilar o implementar la aplicación puede visualizar su apariencia y el flujo. Veamos la imagen anterior, por ejemplo. Nos podemos deducir a partir de un vistazo rápido al diseño de la superficies existe escenas cuántos son, el diseño de cada vista y cómo todo lo que se relaciona. Esto es lo que hace que los guiones gráficos tan eficaz.

Los eventos son más fáciles de administrar con guiones gráficos, especialmente cuando se usa el Diseñador de iOS. La mayoría de los controles de interfaz de usuario tendrá una lista de posibles eventos en el panel de propiedades. El controlador de eventos se puede agregar aquí y completa en un método parcial de la clase de controladores de vista...

El contenido de un guión gráfico se almacena como un archivo XML. En el tiempo, de compilación cualquier `.storyboard` archivos se compilan en archivos binarios que se conoce como nibs. En tiempo de ejecución, estos nibs se inicializa y crear instancias para crear nuevas vistas.

## <a name="segues"></a>Los objetos Segue

Un *Segue*, o *objeto Segue*, se utiliza en el desarrollo de iOS para representar una transición entre bastidores. Para crear un segue, mantenga presionada la **Ctrl** clave y haga clic y arrastre desde una escena a otro. Como se arrastra el mouse, aparece un conector azul, que indica donde llevará el segue como se muestra en la imagen siguiente:

 [![](images/createsegue.png "Aparece un conector azul, que indica donde llevará el segue como se muestra en esta imagen")](images/createsegue.png#lightbox)

En el mouse hacia arriba, aparecerá un menú lo que permite elegir la acción para nuestro segue. Puede ser similar a las imágenes siguientes: 

**Clases de previa iOS 8 y tamaño**:

[![](images/segue1.png "La lista desplegable de Segue de acción sin las clases de tamaño")](images/segue1.png#lightbox)

**Al usar las clases de tamaño y objetos Segue adaptable**:

[![](images/16new.png "La lista desplegable de Segue de acción con las clases de tamaño")](images/16new.png#lightbox)

> [!IMPORTANT]
> Si usa VMWare para la máquina Virtual de Windows, pulse Ctrl se asigna como el _haga_ botón del mouse de forma predeterminada. Para crear un Segue, edite sus preferencias de teclado a través de **preferencias** > **teclado y Mouse** > **accesos directos de Mouse** y reasignar el **Botón secundario** tal como se muestra a continuación:
> 
> [![](images/image22.png "Configuración de preferencias de teclado y Mouse")](images/image22.png#lightbox)
> 
> Ahora podrá agregar un objeto segue entre los controladores de vista de forma habitual.

Hay diferentes tipos de transiciones, cada control lo sobre cómo se presenta un nuevo controlador de vista al usuario y cómo interactúa con otros controladores de vista en el guión gráfico. Estos se explican a continuación. También es posible crear subclases de un objeto segue para implementar una transición personalizada:

-  **Mostrar / Push** : una inserción segue agrega el controlador de vista a la pila de navegación. Se supone que el controlador de vista que se origina la inserción es parte del mismo controlador de navegación como el controlador de vista que se va a agregar a la pila. Esto hace lo mismo que `pushViewController` y se utiliza generalmente cuando hay alguna relación entre los datos en las pantallas. Mediante la inserción de segue le ofrece el lujo de tener una barra de navegación con un botón Atrás y título agregado a cada vista en la pila, lo que permite explorar en profundidad la navegación a través de la jerarquía de vistas.
-  **Modal** : un segue modal crea una relación entre los controladores de vista de dos en el proyecto, con la opción de una transición animada que se muestran. El controlador de vista secundarios ocultará completamente el controlador de vista principal cuando se incluyen en la vista. A diferencia de una inserción de segue, que agrega un botón Atrás para nosotros; Cuando uso modal segue `DismissViewController` debe usarse con el fin de volver a la anterior controlador de vista.
-  **Personalizado** : cualquier custom segue puede crearse como una subclase de ` UIStoryboardSegue`.
-  **Desenredo** : un desenredo segue puede usarse para navegar hacia atrás por una inserción o modal segue: por ejemplo, descartando el controlador de vista presenta de forma modal. Además, puede desenredar a través de no solo uno, pero una serie de inserción y modal objetos Segue y volver atrás varios pasos en la jerarquía de navegación con una sola acción de desenredo. Para aprender a usar una operación de desenredo segue en iOS, lea el [crear desenredo objetos Segue](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/unwind_segue) receta.
-  **Sourceless** : un sourceless segue indica la escena que contiene el controlador de vista inicial y, por tanto, la vista donde el usuario verán en primer lugar. Se representa mediante el objeto segue que se muestra a continuación:  

    [![](images/sourcelesssegue.png "Un objeto sourceless segue")](images/sourcelesssegue.png#lightbox)

### <a name="adaptive-segue-types"></a>Tipos de Segue adaptable

 iOS 8 introducidas [las clases de tamaño](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes) para permitir que un archivo de guión gráfico de iOS para que funcione con todos los tamaños de pantalla disponibles, lo que permite a los desarrolladores crear una interfaz de usuario para todos los dispositivos iOS. De forma predeterminada, todas las nuevas aplicaciones de Xamarin.iOS usará las clases de tamaño. Para usar las clases de tamaño de un proyecto anterior, consulte el [Introducción a guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) guía. 
 
Cualquier aplicación que use las clases de tamaño también van a usar el nuevo [ *objetos Segue adaptable*](~/ios/user-interface/storyboards/unified-storyboards.md). Al utilizar las clases de tamaño, recuerde que no especificar directamente si se va a estamos usando un iPhone o iPad. En otras palabras, vamos a crear una interfaz de usuario que siempre será el mismo, independientemente de cuánto inmobiliaria tiene que trabajar con. Trabajo de objetos Segue adaptable juzgar por el entorno y determinar cuál es la mejor presentar el contenido. Los objetos Segue adaptable se muestran a continuación: 

[![](images/adaptivesegue.png "La lista desplegable de objetos Segue adaptable")](images/adaptivesegue.png#lightbox)

|Segue|Descripción|
|--- |--- |
|Mostrar|Esto es muy similar a una inserción de segue, pero tiene en cuenta el contenido de la pantalla.|
|Mostrar detalles|Si la aplicación muestra una vista de maestro y detalles (por ejemplo, en un controlador de vista dividida en un iPad), el contenido reemplazará a la vista de detalle. Si la aplicación muestra el patrón o el detalle, el contenido reemplazará a la parte superior de la pila de controlador de vista.|
|Presentación|Esto es similar a la segue Modal y permite la selección de los estilos de presentación y la transición.|
|Presentación del elemento flotante|Esto presenta el contenido como un elemento flotante|

### <a name="transferring-data-with-segues"></a>Transferencia de datos con los objetos Segue

Las ventajas de un segue no terminen con las transiciones. También puede usarse para administrar a la transferencia de datos entre controladores de vista. Esto se logra reemplazando el `PrepareForSegue` método en el controlador de vista inicial y controlar los datos de nosotros mismos. Cuando se desencadene el segue: por ejemplo, con presionar un botón: la aplicación llamará a este método, que proporciona una oportunidad para preparar el nuevo controlador de vista *antes* se produce ninguna navegación. El código siguiente, desde el [Phoneword](https://developer.xamarin.com/samples/monotouch/Hello_iOS/) de ejemplo, se muestra cómo hacerlo: 


```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, 
NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    var callHistoryController = segue.DestinationViewController 
                                  as CallHistoryController;

    if (callHistoryController != null) {
        callHistoryController.PhoneNumbers = PhoneNumbers;
    }
}
```

En este ejemplo, el `PrepareForSegue` método se llamará cuando se desencadene el segue por el usuario. En primer lugar, tenemos que crear una instancia del controlador de vista "receptor" y establézcalo como destino del segue View Controller. Esto se realiza mediante la línea de código siguiente:

```csharp
var callHistoryController = segue.DestinationViewController as CallHistoryController;
```

El método ahora tiene la capacidad de establecer las propiedades de la `DestinationViewController`. En este ejemplo, hemos aprovechado de esto, pase una lista denominada `PhoneNumbers` a la `CallHistoryController` y asignarla a un objeto del mismo nombre:

```csharp
if (callHistoryController != null) {
        callHistoryController.PhoneNumbers = PhoneNumbers;
    }
```

Una vez finalizada la transición, el usuario verá la `CallHistoryController` con la lista rellenada.

## <a name="adding-a-storyboard-to-a-non-storyboard-project"></a>Adición de un guión gráfico a un proyecto sin guiones gráficos

En ocasiones es posible que deba agregar un guion gráfico a un archivo previamente sin guiones gráficos. Una vez hacerlo en Visual Studio para Mac se puede optimizar siguiendo los pasos siguientes:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Cree un nuevo archivo de guión gráfico, vaya a **archivo > nuevo archivo > iOS > guión gráfico**, tal y como se muestra a continuación: 
    
    [![](images/new-storyboard-xs.png "El cuadro de diálogo nuevo archivo")](images/new-storyboard-xs.png#lightbox)

2. Agregue el nombre del guion gráfico a la **interfaz principal** sección de la **Info.plist**, tal y como se muestra a continuación:
    
    [![](images/infoplist.png "El editor Info.plist")](images/infoplist.png#lightbox)
    
    Esto hace que el equivalente de crear instancias del controlador de vista inicial en el `FinishedLaunching` método dentro del delegado de aplicación. Con esta opción establecida, la aplicación crea una instancia de una ventana (ver abajo), carga el guión gráfico principal y le asigna una instancia de controlador de vista inicial del guión gráfico (el uno al lado de sourceless Segue) como el `RootViewController` propiedad de la ventana y, a continuación, hace la ventana visible en la pantalla.

3. En el `AppDelegate`, invalidar el valor predeterminado `Window` método con el código siguiente para implementar la propiedad window:
        
        public override UIWindow Window {
            get;
            set;
            }
            
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Cree un nuevo archivo de guión gráfico con el botón secundario en el proyecto para **Agregar > nuevo archivo > iOS > guion gráfico vacío**, tal y como se muestra a continuación: 
    
    [![](images/new-storyboard-vs.png "El cuadro de diálogo nuevo elemento")](images/new-storyboard-vs.png#lightbox)

2. Agregue el nombre del guion gráfico a la **interfaz principal** sección de la aplicación, tal y como se muestra a continuación de iOS:
    
    [![](images/ios-app.png "El editor Info.plist")](images/ios-app.png#lightbox)
    
    Esto hace que el equivalente de crear instancias del controlador de vista inicial en el `FinishedLaunching` método dentro del delegado de aplicación. Con esta opción establecida, la aplicación crea una instancia de una ventana (ver abajo), carga el guión gráfico principal y le asigna una instancia de controlador de vista inicial del guión gráfico (el uno al lado de sourceless Segue) como el `RootViewController` propiedad de la ventana y, a continuación, hace la ventana visible en la pantalla.

3. En el `AppDelegate`, invalidar el valor predeterminado `Window` método con el código siguiente para implementar la propiedad window:

        public override UIWindow Window {
            get;
            set;
            }
            
-----

## <a name="creating-a-storyboard-with-the-ios-designer"></a>Creación de un guión gráfico con el Diseñador de iOS

Un guión gráfico se puede crear mediante el Diseñador de Xamarin para iOS, que se ha integrado perfectamente con Visual Studio para Mac y Visual Studio.

Para comenzar a usar el Diseñador de iOS para crear guiones gráficos, siga el [Hello, iOS Multiscreen](~/ios/get-started/hello-ios-multiscreen/index.md) guía. En este tutorial explorará la navegación entre controladores de vista mediante objetos Segue y cómo controlar eventos en los controles.

## <a name="instantiate-storyboards-manually"></a>Crear manualmente una instancia de guiones gráficos

Guiones gráficos reemplazan completamente los archivos XIB individuales en el proyecto, sin embargo, los controladores de vista individuales en un guión gráfico todavía se pueden crear instancias mediante `Storyboard.InstantiateViewController`.

A veces, las aplicaciones tienen requisitos especiales que no se pueden controlar con las transiciones de guiones gráficos integrados proporcionadas por el diseñador. Por ejemplo, si tuviéramos que crear una aplicación que inicia las pantallas de diferentes desde el mismo botón, según el estado actual de una aplicación, nos conviene crear manualmente una instancia de los controladores de vista y la transición de programas nosotros mismos.

La captura de pantalla siguiente muestra dos controladores de vista en la superficie de diseño con n segue entre ellos. La siguiente sección le guiará a través de cómo configurar esa transición en el código.

 [![](images/viewcontrollerspink.png "Esta captura de pantalla muestra dos controladores de vista en la superficie de diseño con n segue entre ellos")](images/viewcontrollerspink.png#lightbox)

1. Agregar un _guión gráfico de iPhone vacía_ a un proyecto existente del proyecto:
    
    [![](images/add-storyboard1.png "Agregar a guión gráfico")](images/add-storyboard1.png#lightbox)

2. Haga doble clic en el guión gráfico recién creado para abrirlo y agregue un nuevo **controlador de navegación** a la superficie de diseño. Como el controlador de navegación es menor de la interfaz de usuario, de forma predeterminada viene acompañado de un controlador de vista raíz, como se muestra a continuación:

    [![](images/uinavigationcontroller.png "Controladores de vista con los objetos Segue")](images/uinavigationcontroller.png#lightbox)

3. Seleccione el _View Controller_ haciendo clic en la barra negra situada en la parte inferior. En el diseñador **panel propiedad**, en **identidad** podemos especificar una clase personalizada, así como un identificador único para el controlador de vista. Establecer el **nombre de la clase** y **identificador de guión gráfico** a `MainViewController`.

    [![](images/identitypanelnew.png "Especifique la clase personalizada")](images/identitypanelnew.png#lightbox)

4. Más adelante, necesitamos crear una instancia de nuestros controladores de vista desde el guión gráfico y usará el identificador de guión gráfico para hacer referencia a ellos en nuestro código. Establecer el identificador de restauración para que coincida con el identificador de guión gráfico, se garantiza que obtiene a volver a crear correctamente el controlador de vista si es necesario restaurar el estado.

5. Actualmente, sólo tiene un controlador de vista. Arrastre otro controlador de vista a la superficie de diseño. En el **panel propiedad**, bajo la identidad, establezca la clase y el identificador de guión gráfico para `PinkViewController`, tal y como se muestra a continuación:

    [![](images/pinkvcnew.png "El panel de propiedades")](images/pinkvcnew.png#lightbox)
    
    El IDE creará estas clases personalizadas para los controladores de vista. Estos se pueden ver en el **panel de solución**, tal y como se muestra en la captura de pantalla siguiente:
    
    [![](images/solution-pad.png "Panel de solución")](images/solution-pad.png#lightbox)

6. En el `PinkViewController`, seleccione la vista haciendo hacia el centro del marco del controlador. En el panel de propiedades en la vista, cambiar el **en segundo plano** fucsia:
    
    [![](images/pinkcontroller.png "Establecer el color de fondo")](images/pinkcontroller.png#lightbox)

7. Por último, arrastre un botón desde la **cuadro de herramientas** hasta el `MainViewController`. En el panel de propiedades, asígnele el nombre `PinkButton` y GoToPink el título, como se muestra a continuación:

    [![](images/pinkbutton.png "Nombre del conjunto de botón")](images/pinkbutton.png#lightbox)

El guión gráfico está completado, pero si se implementa el proyecto ahora, obtenemos una pantalla en blanco. Eso es porque todavía es necesario indicarle al IDE para usar nuestro guión gráfico y para configurar un controlador de vista raíz para que actúe como la primera vista. Normalmente esto puede hacerse a través de nuestras opciones de proyecto, como se indicó anteriormente. Sin embargo en este ejemplo vamos a lograrán el mismo resultado en el código, debe agregar lo siguiente a la **AppDelegate**:

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

Es una gran cantidad de código, pero sólo unas pocas líneas no están familiarizadas. Primero, registramos nuestro guión gráfico con el **AppDelegate** pasando en nombre del guión gráfico, **MainStoryboard**. A continuación, se informa al crear una instancia de un controlador de vista inicial desde el guión gráfico mediante una llamada a la aplicación `InstantiateInitialViewController` en el guión gráfico, y se establezca ese controlador de vista como controlador de vista raíz de la aplicación. Este método determina la primera pantalla que ve el usuario, y crea una nueva instancia de ese controlador de vista.

Observe que en el panel de solución que ha creado el IDE una `MainViewcontroller.cs` (clase) y su `corresponding designer.cs` cuando se agrega el nombre de clase en el panel de propiedades en el paso 4. Podemos ver esta clase crea un constructor especial que incluye una clase base:

```csharp
public MainViewController (IntPtr handle) : base (handle) 
{
}
```


Cuando se crea un guión gráfico mediante el diseñador, el IDE agrega automáticamente el [[registrar]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/) atributo en la parte superior de la `designer.cs` clase y pasar un identificador de cadena, que es idéntico al identificador de guión gráfico especificado en el paso anterior. Esta opción vinculará el C# a la escena en el guión gráfico relevante.

En algún momento desea agregar una clase existente que fue **no** creado en el diseñador. En este caso, podría registrar esta clase como normal:

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

Para obtener más información sobre cómo registrar las clases y métodos, consulte el [tipo registrador](http://docs.xamarin.com/guides/ios/advanced_topics/registrar/) documentación.

El último paso de esta clase es para conectar el botón y la transición al controlador de vista de color rosa. Se deberá crear una instancia de la `PinkViewController` desde el guión gráfico; a continuación, se programará una inserción segue con `PushViewController`, tal y como se muestra en el ejemplo de código siguiente:

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

Ejecutar la aplicación genera una aplicación de pantalla de 2:

![](images/finishedstoryboard.png "Las pantallas de ejecución de la aplicación de ejemplo")

## <a name="conditional-segues"></a>Los objetos Segue condicional

A menudo, pasar de un controlador de vista al siguiente depende de una determinada condición. Por ejemplo, si vamos a realizar una pantalla de inicio de sesión simple sería sólo queremos pasar a la siguiente pantalla *si* había verificados el nombre de usuario y la contraseña.

En el ejemplo siguiente se agregará un campo de contraseña en el ejemplo anterior. El usuario sólo podrá obtener acceso a la *PinkViewController* si se especifica la contraseña correcta, en caso contrario, se mostrará un error.

Antes de comenzar, siga los pasos 1 a 8 anteriores. En estos pasos se crea el guión gráfico, empezar a crear la interfaz de usuario e indicar qué controlador de vista que se usará a nuestro delegado de la aplicación sea RootViewController.

1. Ahora, vamos a compilar nuestra interfaz de usuario y agregar las vistas adicionales enumeradas en la `MainViewController` para darle un aspecto similar en la captura de pantalla siguiente:

    - Interfaz de usuario
        - Nombre: PasswordTextField
        - Marcador de posición: 'escribir la contraseña secreta'
    - UILabel
        - Texto: "Error: incorrecto de contraseña. No se deben pasar!'
        - Colores: rojo
        - Alineación: Centro
        - Líneas: 2
        - Casilla 'Hidden' activada 
        
    [![](images/passwordvc.png "Líneas de referencia")](images/passwordvc.png#lightbox)
    
2. Crear un objeto Segue entre el botón Ir a rosa y el controlador de vista por Ctrl y arrastrar desde el *PinkButton* a la *PinkViewController*y seleccionando **Push** de mouse hacia arriba . 

3. Haga clic en el objeto Segue y asígnele el *identificador* `SegueToPink`:

    [![](images/namesegue.png "Haga clic en el objeto Segue y asígnele el identificador de SegueToPink")](images/namesegue.png#lightbox)  
    

4. Por último, agregue el siguiente método ShouldPerformSegue a la `MainViewController` clase:

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

En este código nos hemos ajustado el segueIdentifier a nuestro `SegueToPink` segue, por lo que, a continuación, podemos probar una condición; una contraseña válida en este caso. Si la condición devuelve `true`, llevará a cabo el Segue y presentará el `PinkViewController`. Si `false`, no se mostrará el nuevo controlador de vista.

Podemos aplicar este enfoque para cualquier objeto Segue en este controlador de vista activando el argumento segueIdentifier al método ShouldPerformSegue. En este caso, solo tiene un identificador de Segue: `SegueToPink`.

Hacer referencia a la solución Storyboards.Conditional en el [ejemplo guiones gráficos Manual](https://developer.xamarin.com/samples/monotouch/ManualStoryboard/) para obtener un ejemplo ilustrativo.

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>Usar referencias a guión gráfico

Una referencia de guión gráfico le permite tomar un diseño de guion gráfico grande y complejo y dividirla en menor guiones gráficos que obtengan hace referencia desde el original, por lo tanto, quitando la complejidad y facilitar los guiones gráficos individuales resultantes diseñar y mantener.

Además, puede proporcionar una referencia de guión gráfico una _delimitador_ a otra escena en el mismo guión gráfico o una escena concreta en uno diferente.

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>Hacer referencia a un guión gráfico externo

Para agregar una referencia a un guión gráfico externo, haga lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en el nombre del proyecto y seleccione **agregar** > **nuevo archivo...**   >  **iOS** > **guión gráfico**. Escriba un **nombre** para el nuevo guion gráfico y haga clic en el **New** botón:
    
    [![](images/ref01.png "El cuadro de diálogo nuevo archivo")](images/ref01.png#lightbox)
    
2. Definir el diseño de escenas del guión gráfico nuevo como se haría normalmente y guardar los cambios: 
    
    [![](images/ref02.png "El diseño de la nueva escena")](images/ref02.png#lightbox)
    
3. Abra el guión gráfico que se va a agregar la referencia a en el Diseñador de iOS.

4. Arrastre un **crear guiones gráficos de referencia** desde el **cuadro de herramientas** a la superficie de diseño: 
    
    [![](images/ref03.png "Una referencia de guión gráfico")](images/ref03.png#lightbox)
    
5. En el **Widget** pestaña de la **Explorador de propiedades**, seleccione el nombre de la **guión gráfico** que creó anteriormente: 

    [![](images/ref04.png "La pestaña de Widget")](images/ref04.png#lightbox)
    
6. Control y haga clic en un Widget de interfaz de usuario (por ejemplo, un botón) en una escena existente y crear un Segue nuevo a la **guión gráfico referencia** que acaba de crear: 

    [![](images/ref05.png "Creación de un segue")](images/ref05.png#lightbox) 
    
7. En el menú emergente, seleccione **mostrar** para completar el Segue: 

    [![](images/ref06.png "Seleccione Mostrar para completar el Segue")](images/ref06.png#lightbox) 
    
8. Guarde los cambios en el guión gráfico.

Se mostrará cuando se ejecuta la aplicación y el usuario hace clic en el elemento de interfaz de usuario que creó el objeto Segue desde el controlador de vista inicial desde el guión gráfico externo especificado en la referencia de guión gráfico.

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>Hacer referencia a una escena concreta en un guión gráfico externo

Para agregar una referencia a una escena específica un guión gráfico externo (y no en el controlador de vista inicial), realice lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en el guión gráfico externo para abrirlo y editarlo.

2. Agregue una nueva escena y diseño como lo haría normalmente: 

    [![](images/ref07.png "El nuevo diseño de la escena")](images/ref07.png#lightbox)
    
3. En el **Widget** pestaña de la **Explorador de propiedades**, escriba un **identificador de guión gráfico** para controlador de vista de la escena nueva: 

    [![](images/ref08.png "Escriba un identificador de guión gráfico para el nuevo controlador de vista de segundo plano")](images/ref08.png#lightbox)
    
3. Abra el guión gráfico que se va a agregar la referencia a en el Diseñador de iOS.

4. Arrastre un **crear guiones gráficos de referencia** desde el **cuadro de herramientas** a la superficie de diseño: 

    [![](images/ref03.png "Una referencia de guión gráfico")](images/ref03.png#lightbox)
    
5. En el **Widget** pestaña de la **Explorador de propiedades**, seleccione el nombre de la **guión gráfico** y el **Id. de referencia** (identificador de guión gráfico) de la Escena que creó anteriormente: 

    [![](images/ref09.png "La pestaña de Widget ")](images/ref09.png#lightbox)
    
6. Control y haga clic en un Widget de interfaz de usuario (por ejemplo, un botón) en una escena existente y crear un Segue nuevo a la **guión gráfico referencia** que acaba de crear: 

    [![](images/ref10.png "Creación de un segue")](images/ref10.png#lightbox) 
    
7. En el menú emergente, seleccione **mostrar** para completar el Segue: 

    [![](images/ref06.png "Seleccione Mostrar para completar el Segue")](images/ref06.png#lightbox) 
    
8. Guarde los cambios en el guión gráfico.

Cuando la aplicación es la ejecución y el usuario hace clic en el elemento de interfaz de usuario que ha creado el objeto Segue de la escena con la determinada **identificador de guión gráfico** desde el guión gráfico externo especificado en la referencia de guión gráfico se mostrará.

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>Hacer referencia a una escena concreta en el mismo guión gráfico

Para agregar una referencia a una escena concreta el mismo guión gráfico, haga lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en el guión gráfico para abrirlo y editarlo.

2. Agregue una nueva escena y diseño como lo haría normalmente: 

    [![](images/ref11.png "El nuevo diseño de la escena")](images/ref11.png#lightbox)

3. En el **Widget** pestaña de la **Explorador de propiedades**, escriba un **identificador de guión gráfico** para controlador de vista de la escena nueva: 

    [![](images/ref12.png "La pestaña de Widget")](images/ref12.png#lightbox)
    
3. Arrastre un **crear guiones gráficos de referencia** desde el **cuadro de herramientas** a la superficie de diseño: 

    [![](images/ref03.png "Una referencia de guión gráfico")](images/ref03.png#lightbox)
    
5. En el **Widget** pestaña de la **Explorador de propiedades**, seleccione **Id. de referencia** (identificador de guión gráfico) de la escena que creó anteriormente: 

    [![](images/ref13.png "La pestaña de Widget")](images/ref13.png#lightbox)
    
6. Control y haga clic en un Widget de interfaz de usuario (por ejemplo, un botón) en una escena existente y crear un Segue nuevo a la **guión gráfico referencia** que acaba de crear: 

    [![](images/ref14.png "Creación de un segue")](images/ref14.png#lightbox) 
    
7. En el menú emergente, seleccione **mostrar** para completar el Segue: 

    [![](images/ref06.png "Seleccione Mostrar para completar el Segue")](images/ref06.png#lightbox) 
    
8. Guarde los cambios en el guión gráfico.

Cuando la aplicación es la ejecución y el usuario hace clic en el elemento de interfaz de usuario que ha creado el objeto Segue de la escena con la determinada **identificador de guión gráfico** en el mismo guión gráfico especificado en la referencia de guión gráfico se mostrará.

## <a name="summary"></a>Resumen

En este artículo se presenta el concepto de guiones gráficos y cómo pueden ser beneficiosos en el desarrollo de aplicaciones de iOS. Describe escenas, los controladores de vista, vistas y las jerarquías de vista y cómo se vinculan escenas junto con diferentes tipos de objetos Segue.  También se exploran crear instancias de los controladores de vista manualmente desde un guión gráfico y crear objetos Segue condicional.



## <a name="related-links"></a>Vínculos relacionados

- [Guión gráfico manual (ejemplo)](https://developer.xamarin.com/samples/ManualStoryboard/)
- [Introducción a iOS Designer](~/ios/user-interface/designer/introduction.md)
- [Convertir a guiones gráficos](http://developer.apple.com/library/ios/#releasenotes/Miscellaneous/RN-AdoptingStoryboards/)
- [Referencia de clase UIStoryboard](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIStoryboard_Class/Reference/Reference.html)
