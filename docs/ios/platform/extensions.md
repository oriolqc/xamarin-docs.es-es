---
title: Extensiones de iOS
description: "Se introdujo en iOS 8, las extensiones son widgets que presentan los iOS en contextos estándares, como en el centro de notificaciones, cuando el usuario solicita un teclado personalizado o cuando están foto de edición. Todas las extensiones se instalan junto con una aplicación de contenedor y se activan desde un punto de extensión concreto en una aplicación Host."
ms.topic: article
ms.prod: xamarin
ms.assetid: 3DEB3D43-3E4A-4099-8331-93C1E7A77095
ms.technology: xamarin-ios
ms.custom: xamu-video
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: ccd83f4b31da555aa9c8ec34501a8656ce0a5032
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/22/2018
---
# <a name="ios-extensions"></a>Extensiones de iOS

_Se introdujo en iOS 8, las extensiones son widgets que presentan los iOS en contextos estándares, como en el centro de notificaciones, cuando el usuario solicita un teclado personalizado o cuando están foto de edición. Todas las extensiones se instalan junto con una aplicación de contenedor y se activan desde un punto de extensión concreto en una aplicación Host._

> [!VIDEO https://youtube.com/embed/Sd0-ch9Udmk]

**Crear extensiones de iOS, por [Universidad de Xamarin](https://university.xamarin.com/)**

Extensiones, según se presentó en iOS 8, están especializadas `UIViewControllers` que presentan los iOS en contextos estándares tal como en el **centro de notificaciones**, como tipos de teclado personalizados solicitados por el usuario para realizar especializados entrada u otros contextos, como modificar una foto en la extensión puede proporcionar filtros de efecto especial.

Todas las extensiones se instalan junto con una aplicación de contenedor (con dos elementos que se escriben mediante las API unificada de 64 bits) y se activan desde un punto de extensión concreto en una aplicación Host. Y puesto que se utilizará como suplementos a funciones del sistema existentes, deben ser alto rendimiento, eficiente y sólida. 

En este artículo se tratan los siguientes temas:

- [Puntos de extensión](#Extension-Points) -muestra el tipo de puntos de extensión disponibles y el tipo de extensión que se pueden crear para cada punto.
- [Limitaciones](#Limitations) -extensiones tienen una serie de limitaciones, algunos de los cuales son universales a todos los tipos, mientras que otros tipos de extensión pueden tener limitaciones específicas de su uso.
- [Distribuir, instalar y ejecuta las extensiones](#Distributing-Installing-and-Running-Extensions) -extensiones se distribuyen desde dentro de una aplicación de contenedor, que, a su vez se envía y se distribuyen a través de la tienda de aplicaciones. Las extensiones que se distribuye con la aplicación se instalan en ese momento, pero el usuario debe habilitar explícitamente cada extensión. Los diferentes tipos de extensiones están habilitados de maneras diferentes.
- [Ciclo de vida de extensión](#Extension-Lifecycle) : una extensión de `UIViewController` se conectan y comenzar el ciclo de vida de controlador de la vista normal. Sin embargo, a diferencia de una aplicación normal, las extensiones de carga, ejecuta y, a continuación, finaliza repetidamente.
- [Crear una extensión](#Creating-an-Extension) -al desarrollar una extensión, las soluciones contendrá al menos dos proyectos: proyecto de la aplicación de contenedor y uno para cada extensión el contenedor proporciona.
- [Tutorial](#Walkthrough) : trata la creación de una sencilla **hoy** widget de extensión que proporciona la interfaz de usuario usando un guión gráfico o en el código, se instala la extensión y probar en el simulador de iOS.
- [Comunicarse con la aplicación Host](#Communicating-with-the-Host-App) -describe brevemente comunicarse con la aplicación Host de una extensión.
- [Comunicarse con la aplicación primaria](#Communicating-with-the-Parent-App) -describe brevemente comunicarse con la aplicación primaria de una extensión.
- [Precauciones y consideraciones](#Precautions-and-Considerations) -algunos saben precauciones y consideraciones que deben tenerse en cuenta al diseñar e implementar una extensión de lista.
 

<a name="Extension-Points" />

## <a name="extension-points"></a>Puntos de extensión

|Tipo|Descripción|Punto de extensión|Aplicación de host|
|--- |--- |--- |--- |
|Acción|Editor especializado o el Visor para un tipo de medio determinado|`com.apple.ui-services`|Cualquiera|
|Proveedor de documento|Permite que la aplicación usar un almacén de documentos de forma remota|`com.apple.fileprovider-ui`|Aplicaciones que usan un [UIDocumentPickerViewController](https://developer.xamarin.com/api/type/UIKit.UIDocumentPickerViewController/)|
|Teclado|Teclados alternativos|`com.apple.keyboard-service`|Cualquiera|
|Edición de fotografías.|Manipulación de fotografías y editar|`com.apple.photo-editing`|Editor de Photos.app|
|Compartir|Comparte datos con redes sociales, servicios, etcetera de mensajería.|`com.apple.share-services`|Cualquiera|
|Hoy|"Widgets" que aparecen en la pantalla hoy o el centro de notificaciones|`com.apple.widget-extensions`|Hoy en día y centro de notificaciones|

[Puntos de extensión adicionales](~/ios/platform/introduction-to-ios10/index.md#app-extensions) se agregaron en iOS 10.

<a name="Limitations" />

## <a name="limitations"></a>Limitaciones

Extensiones tienen una serie de limitaciones, algunos de los cuales son comunes a todos los tipos (por instancia, ningún tipo de extensión puede tener acceso a la cámara o micrófonos) mientras que otros tipos de extensión pueden tener limitaciones específicas de su uso (por ejemplo, personalizados teclados no se puede usar para los campos de entrada de proteger los datos como contraseñas). 

Las limitaciones universales son:

- El [Kit Health](~/ios/platform/healthkit.md) y [evento Kit UI](~/ios/platform/eventkit.md) marcos no están disponibles
- No se pueden utilizar extensiones [extendidos modos en segundo plano](http://developer.xamarin.com/guides/cross-platform/application_fundamentals/backgrounding/part_3_ios_backgrounding_techniques/registering_applications_to_run_in_background/)
- Las extensiones no pueden tener acceso el dispositivo cámaras o micrófonos (aunque pueden tener acceso a los archivos multimedia existentes)
- Las extensiones no pueden recibir datos de aire Drop (aunque puede transmitir datos a través de aire Drop)
- [UIActionSheet](https://developer.xamarin.com/api/type/UIKit.UIActionSheet/) y [UIAlertView](https://developer.xamarin.com/api/type/UIKit.UIAlertView/) no están disponibles; deben usar las extensiones de [UIAlertController](https://developer.xamarin.com/api/type/UIKit.UIAlertController/)
- Varios miembros de [UIApplication](https://developer.xamarin.com/api/type/UIKit.UIApplication/) no están disponibles: [UIApplication.SharedApplication](https://developer.xamarin.com/api/property/UIKit.UIApplication.SharedApplication/), `UIApplication.OpenURL`, `UIApplication.BeginIgnoringInteractionEvents` y `UIApplication.EndIgnoringInteractionEvents`
- iOS impone un límite de uso de memoria de 16 MB en las extensiones de hoy en día.
- De forma predeterminada, las extensiones de teclado no tienen acceso a la red. Esto afecta a la depuración en el dispositivo (no se aplica la restricción en el simulador), debido a que Xamarin.iOS requiere acceso a la red para que funcione la depuración. Es posible solicitar acceso de red estableciendo el `Requests Open Access` valor Info.plist del proyecto a `Yes`. Vea de Apple [Guía de teclado personalizadas](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/CustomKeyboard.html) para obtener más información acerca de las limitaciones de la extensión de teclado.

Para conocer las limitaciones individuales, vea de Apple [Guía de programación de la extensión en la aplicación](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/).

<a name="Distributing-Installing-and-Running-Extensions" />

## <a name="distributing-installing-and-running-extensions"></a>Distribuir, instalar y ejecutar las extensiones

Las extensiones se distribuyen desde dentro de una aplicación de contenedor, que, a su vez se envía y se distribuyen a través de la tienda de aplicaciones. Las extensiones que se distribuye con la aplicación se instalan en ese momento, pero el usuario debe habilitar explícitamente cada extensión. Los diferentes tipos de extensiones están habilitados de maneras diferentes; algunos requieren que el usuario navegar hasta la **configuración** aplicación y habilitarlas desde allí. Mientras que otros están habilitados en el momento de uso, como habilitar una extensión de uso compartido al enviar una foto. 

La aplicación en el que se utiliza la extensión (donde el usuario encuentra el punto de extensión) se conoce como el **aplicación Host**, ya que se trata de la aplicación que hospeda la extensión cuando se ejecuta. La aplicación que instala la extensión es la **aplicación de contenedor**, porque es la aplicación que contiene la extensión cuando se instaló.  

Normalmente, la aplicación de contenedor describe la extensión y guía al usuario a través del proceso de habilitarla.

<a name="Extension-Lifecycle" />

## <a name="extension-lifecycle"></a>Ciclo de vida de extensión

Una extensión puede ser tan simple como una sola [UIViewController](https://developer.xamarin.com/api/type/UIKit.UIViewController/) o extensiones más complejas que presentan varias pantallas de interfaz de usuario. Cuando el usuario se encuentra un _puntos de extensión_ (como al uso compartido de una imagen), tendrán la oportunidad de elegir entre las extensiones registradas para ese punto de extensión. 

Si elige uno de la aplicación de extensiones, su `UIViewController` se conectan y comenzar el ciclo de vida de controlador de la vista normal. Sin embargo, a diferencia de una aplicación normal, que está suspendido, pero generalmente no se finalizan cuando el usuario termina de interactuar con ellos, las extensiones de carga, ejecuta y, a continuación, finaliza repetidamente.

Las extensiones pueden comunicarse con sus aplicaciones de Host a través de un [NSExtensionContext](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/) objeto. Algunas extensiones tienen las operaciones que reciben las devoluciones de llamada asincrónicas con los resultados. Estas devoluciones de llamada se ejecutará en subprocesos en segundo plano y la extensión debe tener esto en cuenta; Por ejemplo, mediante el uso de [NSObject.InvokeOnMainThread](https://developer.xamarin.com/api/member/Foundation.NSObject.InvokeOnMainThread/) si desean actualizar la interfaz de usuario. Consulte la [comunicarse con la aplicación Host](#Communicating-with-the-Host-App) sección para obtener más detalles.

De forma predeterminada, las extensiones y sus aplicaciones de contenedor no pueden comunicarse, a pesar de que se instalan conjuntamente. En algunos casos, la aplicación de contenedor es básicamente un contenedor "envío" vacío cuyo propósito es servir una vez que se instala la extensión. Sin embargo, si así requieran circunstancias, la aplicación de contenedor y la extensión pueden compartir recursos de un área común. Además, un **hoy extensión** puede solicitar su aplicación de contenedor para abrir una dirección URL. Este comportamiento se muestra en el [evolucionar Widget de cuenta atrás](http://github.com/xamarin/monotouch-samples/tree/master/ExtensionsDemo).

<a name="Creating-an-Extension" />

## <a name="creating-an-extension"></a>Crear una extensión

Extensiones (y sus aplicaciones de contenedor) deben ser los archivos binarios de 64 bits y creados con la Xamarin.iOS [API unificada](http://developer.xamarin.com/guides/cross-platform/macios/unified). Al desarrollar una extensión, las soluciones contendrá al menos dos proyectos: proyecto de la aplicación de contenedor y uno para cada extensión el contenedor proporciona. 

<a name="Container-App-Project-Requirements" />

### <a name="container-app-project-requirements"></a>Requisitos de proyecto de aplicación de contenedor

La aplicación de contenedor que se usa para instalar la extensión tiene los siguientes requisitos:

- Debe mantener una referencia al proyecto de extensión.   
- Debe ser una aplicación completa (debe ser capaz de iniciar y ejecutar correctamente) incluso si no hace nada más que proporcionan una manera de instalar una extensión. 
- Debe tener un identificador de paquete que es la base para el identificador de paquete de la extensión de proyecto (vea la sección siguiente para obtener más detalles).

<a name="Extension-Project-Requirements" />

### <a name="extension-project-requirements"></a>Requisitos del proyecto de extensión

Además, el proyecto de la extensión tiene los siguientes requisitos:

- Debe tener un identificador de paquete que comience con el identificador de paquete de la aplicación de su contenedor. Por ejemplo, si la aplicación de contenedor tiene un identificador de paquete de `com.myCompany.ContainerApp`, identificador de la extensión podría ser `com.myCompany.ContainerApp.MyExtension`: 

    ![](extensions-images/bundleidentifiers.png) 
- Debe definir la clave `NSExtensionPointIdentifier`, con un valor adecuado (como `com.apple.widget-extension` para un **hoy** widget del centro de notificaciones), en su `Info.plist` archivo.
- También debe definir *cualquier* el `NSExtensionMainStoryboard` clave o el `NSPrincipalClass` clave en su `Info.plist` archivo con un valor adecuado:
    - Use la `NSExtensionMainStoryboard` clave para especificar el nombre del guión gráfico que presenta la interfaz de usuario principal de la extensión (menos `.storyboard`). Por ejemplo, `Main` para el `Main.storyboard` archivo.
    - Use la `NSPrincipalClass` clave para especificar la clase que se inicializará cuando se inicia la extensión. El valor debe coincidir con el **registrar** valor de la `UIViewController`: 

    ![](extensions-images/registerandprincipalclass.png)

Tipos específicos de las extensiones pueden tener requisitos adicionales. Por ejemplo, un **hoy** o **centro de notificaciones** debe implementar la clase de entidad de seguridad de la extensión de [INCWidgetProviding](https://developer.xamarin.com/api/type/NotificationCenter.INCWidgetProviding/).

> [!IMPORTANT]
> Si inicia el proyecto mediante una de las plantillas de extensiones proporcionadas por Visual Studio para Mac, mayoría (si no todos) estos requisitos se proporcionan y se cumple para automáticamente mediante la plantilla.

<a name="Walkthrough" />

## <a name="walkthrough"></a>Tutorial 

En el siguiente tutorial, aprenderá a crear un ejemplo **hoy** widget que calcula el día y el número de días que quedan en el año:

[![](extensions-images/carpediemscreenshot-sm.png "Un widget de hoy en día de ejemplo que calcula el día y el número de días que quedan en el año")](extensions-images/carpediemscreenshot.png#lightbox)

<a name="Creating-the-Solution" />

### <a name="creating-the-solution"></a>Crear la solución

Para crear la solución necesaria, haga lo siguiente:

1. En primer lugar, cree un nuevo iOS, **ver solo la aplicación** del proyecto y haga clic en el **siguiente** botón: 

    [![](extensions-images/today01.png "En primer lugar, cree un nuevo iOS, el proyecto de aplicación de vista única y haga clic en el botón siguiente")](extensions-images/today01.png#lightbox)
2. Asigne al proyecto el `TodayContainer` y haga clic en el **siguiente** botón: 

    [![](extensions-images/today02.png "Asigne al proyecto el TodayContainer y haga clic en el botón siguiente")](extensions-images/today02.png#lightbox)
3. Compruebe el **nombre del proyecto** y **nombresolución** y haga clic en el **crear** botón para crear la solución: 

    [![](extensions-images/today03.png "Compruebe el nombre del proyecto y SolutionName y haga clic en el botón Crear para crear la solución")](extensions-images/today03.png#lightbox)
4. Después, en la **el Explorador de soluciones**, haga doble clic en la solución y agregue un nuevo **iOS extensión** proyecto desde el **extensión hoy en día** plantilla: 

    [![](extensions-images/today04.png "A continuación, en el Explorador de soluciones, haga doble clic en la solución y agregue un nuevo proyecto de extensión de iOS de la plantilla de hoy en día extensión")](extensions-images/today04.png#lightbox)
5. Asigne al proyecto el `DaysRemaining` y haga clic en el **siguiente** botón: 

    [![](extensions-images/today05.png "Asigne al proyecto el DaysRemaining y haga clic en el botón siguiente")](extensions-images/today05.png#lightbox)
6. Revise el proyecto y haga clic en el **crear** botón para crearla: 

    [![](extensions-images/today06.png "Revise el proyecto y haga clic en el botón Crear para crearlo")](extensions-images/today06.png#lightbox)

La solución resultante ahora debería tener dos proyectos, como se muestra aquí:

[![](extensions-images/today07.png "La solución resultante ahora debería tener dos proyectos, como se muestra aquí")](extensions-images/today07.png#lightbox)

<a name="Creating-the-Extension-User-Interface" />

### <a name="creating-the-extension-user-interface"></a>Creación de la interfaz de usuario de extensión

A continuación, deberá diseñar la interfaz de su **hoy** widget. Esto puede hacerse ya sea mediante un guión gráfico o creando la interfaz de usuario en el código. Ambos métodos se tratará a continuación en detalle.

<a name="Using-Storyboards" />

#### <a name="using-storyboards"></a>Uso de guiones gráficos

Para compilar la interfaz de usuario con un guión gráfico, haga lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en el proyecto de extensión `Main.storyboard` archivo para abrirlo y editarlo: 

    [![](extensions-images/today08.png "Haga doble clic en el archivo de Main.storyboard de proyectos de extensión para abrirlo y editarlo")](extensions-images/today08.png#lightbox)
2. Seleccione la etiqueta que se agregó automáticamente a la interfaz de usuario mediante la plantilla y asígnele el **nombre** `TodayMessage` en el **Widget** pestaña de la **el Explorador de propiedades**: 

    [![](extensions-images/today09.png "Seleccione la etiqueta que se agregó automáticamente a la interfaz de usuario mediante la plantilla y asígnele el nombre TodayMessage en la ficha del Widget del explorador de propiedades")](extensions-images/today09.png#lightbox)
3. Guarde los cambios en el guión gráfico.

<a name="Using-Code" />

#### <a name="using-code"></a>Mediante código

Para compilar la interfaz de usuario en el código, realice lo siguiente: 

1. En el **el Explorador de soluciones**, seleccione la **DaysRemaining** del proyecto, agregue una nueva clase y llámelo `CodeBasedViewController`: 

    [![](extensions-images/code01.png "Aelect el proyecto DaysRemaining, agregue una nueva clase y llámelo CodeBasedViewController")](extensions-images/code01.png#lightbox)
2. Una vez más, en la **el Explorador de soluciones**, haga doble clic en la extensión `Info.plist` archivo para abrirlo y editarlo: 

    [![](extensions-images/code02.png "Haga doble clic en el archivo Info.plist de extensiones para abrirlo y editarlo")](extensions-images/code02.png#lightbox)
3. Seleccione el **vista del origen de** (desde la parte inferior de la pantalla) y abra el `NSExtension` nodo: 

    [![](extensions-images/code03.png "Seleccione la vista del origen de la parte inferior de la pantalla y abra el nodo NSExtension")](extensions-images/code03.png#lightbox)
4. Quitar el `NSExtensionMainStoryboard` clave y agregue un `NSPrincipalClass` con el valor `CodeBasedViewController`: 

    [![](extensions-images/code04.png "Quitar la clave de NSExtensionMainStoryboard y agregue un NSPrincipalClass con el valor CodeBasedViewController")](extensions-images/code04.png#lightbox)
5. Guarde los cambios.

A continuación, edite la `CodeBasedViewController.cs` de archivos y darle un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using UIKit;
using NotificationCenter;
using CoreGraphics;

namespace DaysRemaining
{
    [Register("CodeBasedViewContoller")]
    public class CodeBasedViewController : UIViewController, INCWidgetProviding
    {
        public CodeBasedViewController ()
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Add label to view
            var TodayMessage = new UILabel (new CGRect (0, 0, View.Frame.Width, View.Frame.Height)) {
                TextAlignment = UITextAlignment.Center
            };

            View.AddSubview (TodayMessage);
            
            // Insert code to power extension here...

        }
    }
}
```

Tenga en cuenta que la `[Register("CodeBasedViewContoller")]` coincide con el valor especificado para el `NSPrincipalClass` anteriormente.

<a name="Coding-the-Extension" />

### <a name="coding-the-extension"></a>La extensión de codificación

Con la interfaz de usuario que se creó, abra la `TodayViewController.cs` o `CodeBasedViewController.cs` archivo (en función del método utilizado para crear la interfaz de usuario anterior), el cambio de la **ViewDidLoad** método y darle un aspecto similar al siguiente:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Calculate the values
    var dayOfYear = DateTime.Now.DayOfYear;
    var leapYearExtra = DateTime.IsLeapYear (DateTime.Now.Year) ? 1 : 0;
    var daysRemaining = 365 + leapYearExtra - dayOfYear;

    // Display the message
    if (daysRemaining == 1) {
        TodayMessage.Text = String.Format ("Today is day {0}. There is one day remaining in the year.", dayOfYear);
    } else {
        TodayMessage.Text = String.Format ("Today is day {0}. There are {1} days remaining in the year.", dayOfYear, daysRemaining);
    }
}
```

Si con el código según el método de interfaz de usuario, reemplace el `// Insert code to power extension here...` comentario con el nuevo código desde arriba. Después de llamar a la implementación base (e insertar una etiqueta para la versión de código según), este código realiza un cálculo simple para obtener el día del año y durante cuántos días quedan. A continuación, muestra el mensaje en la etiqueta (`TodayMessage`) que creó en el diseño de interfaz de usuario.

Tenga en cuenta la similitud este proceso es el proceso normal de la escritura de una aplicación. Una extensión `UIViewController` tiene el mismo ciclo de vida como un controlador de vista en una aplicación, salvo las extensiones no tienen modos en segundo plano y no se suspenderán cuando el usuario ha terminado con ellos. En su lugar, las extensiones se repetidamente inicializadas y desasigne según sea necesario.

<a name="Creating-the-Container-App-User-Interface" />

### <a name="creating-the-container-app-user-interface"></a>Creación de la interfaz de usuario de la aplicación de contenedor

En este tutorial, la aplicación de contenedor simplemente se usa como un método para distribuir e instalar la extensión y no proporciona ninguna funcionalidad de su propio. Editar la TodayContainer `Main.storyboard` de archivos y agregar texto de definición de función de la extensión y cómo instalarlo:

[![](extensions-images/today10.png "Edite el archivo TodayContainers Main.storyboard y agregar algún texto que define la función de extensiones y cómo instalarlo")](extensions-images/today10.png#lightbox)

Guarde los cambios en el guión gráfico.

<a name="Testing-the-Extension" />

### <a name="testing-the-extension"></a>Probar la extensión

Para probar la extensión en el simulador de iOS, ejecute el **TodayContainer** aplicación. Se mostrará la vista principal del contenedor:

[![](extensions-images/run01.png "Se mostrará la vista principal de contenedores")](extensions-images/run01.png#lightbox)

A continuación, llamadas el **inicio** botón en el simulador, deslice el dedo hacia abajo desde la parte superior de la pantalla para abrir el **centro de notificaciones**, seleccione el **hoy en día** ficha y haga clic en el **Editar** botón:

[![](extensions-images/run02.png "Pulse el botón de inicio en el simulador, deslice el dedo hacia abajo desde la parte superior de la pantalla para abrir el centro de notificaciones, seleccione la pestaña de hoy y haga clic en el botón Editar")](extensions-images/run02.png#lightbox)

Agregar el **DaysRemaining** extensión a la **hoy** ver y haga clic en el **realiza** botón:

[![](extensions-images/run03.png "Agregar la DaysRemaining extensión a la vista de hoy y haga clic en el botón Listo")](extensions-images/run03.png#lightbox)

El widget nueva se agregará a la **hoy** y ver los resultados se mostrará:

[![](extensions-images/run04.png "El widget nueva se agregará a la vista de hoy y los resultados se mostrarán")](extensions-images/run04.png#lightbox)

<a name="Communicating-with-the-Host-App" />

## <a name="communicating-with-the-host-app"></a>Comunicarse con la aplicación Host

El ejemplo de extensión de hoy en día que creó anteriormente no se comunica con su aplicación de host (el **hoy** pantalla). Si lo hiciera, utilizaría el [ExtensionContext](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/) propiedad de la `TodayViewController` o `CodeBasedViewController` clases. 

Para las extensiones que van a recibir datos desde sus aplicaciones de host, los datos están en el formulario de una matriz de [NSExtensionItem](https://developer.xamarin.com/api/type/Foundation.NSExtensionItem/) objetos almacenados en el [InputItems](https://developer.xamarin.com/api/property/Foundation.NSExtensionContext.InputItems/) propiedad de la [ExtensionContext ](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/) de la extensión `UIViewController`.

Otra extensión, como las extensiones de edición fotográfica, puede distinguir entre el usuario completar o cancelar el uso. Esto se señalará a la aplicación host a través de la [CompleteRequest](https://developer.xamarin.com/api/member/Foundation.NSExtensionContext.CompleteRequest/) y [CancelRequest](https://developer.xamarin.com/api/member/Foundation.NSExtensionContext.CancelRequest/) métodos de [ExtensionContext](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/) propiedad.

Para obtener más información, vea de Apple [Guía de programación de la extensión en la aplicación](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW1).

<a name="Communicating-with-the-Parent-App" />

## <a name="communicating-with-the-parent-app"></a>Comunicarse con la aplicación principal

Un grupo de aplicaciones permite que aplicaciones diferentes (o una aplicación y sus extensiones) tengan acceso a una ubicación de almacenamiento de archivos compartidos. Los grupos de aplicaciones se pueden usar para datos como:

- [Configuración de Apple Watch](~/ios/watchos/app-fundamentals/settings.md).
- [Valores NSUserDefaults compartidos](~/ios/app-fundamentals/user-defaults.md).
- [Archivos compartidos](~/ios/watchos/app-fundamentals/parent-app.md#files).

Para obtener más información, consulte el [grupos de aplicaciones](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) sección de nuestro **trabajar con capacidades** documentación.

<a name="MobileCoreServices" />

## <a name="mobilecoreservices"></a>MobileCoreServices

Cuando se trabaja con las extensiones, use un identificador uniforme de tipo (UTI) para crear y manipular los datos que se intercambian entre la aplicación, otras aplicaciones o servicios.

El `MobileCoreServices.UTType` clase estática define las siguientes propiedades de aplicación auxiliar que se relacionan con de Apple `kUTType...` definiciones:

- `kUTTypeAlembic` - `Alembic`
- `kUTTypeAliasFile` - `AliasFile`
- `kUTTypeAliasRecord` - `AliasRecord`
- `kUTTypeAppleICNS` - `AppleICNS`
- `kUTTypeAppleProtectedMPEG4Audio` - `AppleProtectedMPEG4Audio`
- `kUTTypeAppleProtectedMPEG4Video` - `AppleProtectedMPEG4Video`
- `kUTTypeAppleScript` - `AppleScript`
- `kUTTypeApplication` - `Application`
- `kUTTypeApplicationBundle` - `ApplicationBundle`
- `kUTTypeApplicationFile` - `ApplicationFile`
- `kUTTypeArchive` - `Archive`
- `kUTTypeAssemblyLanguageSource` - `AssemblyLanguageSource`
- `kUTTypeAudio` - `Audio`
- `kUTTypeAudioInterchangeFileFormat` - `AudioInterchangeFileFormat`
- `kUTTypeAudiovisualContent` - `AudiovisualContent`
- `kUTTypeAVIMovie` - `AVIMovie`
- `kUTTypeBinaryPropertyList` - `BinaryPropertyList`
- `kUTTypeBMP` - `BMP`
- `kUTTypeBookmark` - `Bookmark`
- `kUTTypeBundle` - `Bundle`
- `kUTTypeBzip2Archive` - `Bzip2Archive`
- `kUTTypeCalendarEvent` - `CalendarEvent`
- `kUTTypeCHeader` - `CHeader`
- `kUTTypeCommaSeparatedText` - `CommaSeparatedText`
- `kUTTypeCompositeContent` - `CompositeContent`
- `kUTTypeConformsToKey` - `ConformsToKey`
- `kUTTypeContact` - `Contact`
- `kUTTypeContent` - `Content`
- `kUTTypeCPlusPlusHeader` - `CPlusPlusHeader`
- `kUTTypeCPlusPlusSource` - `CPlusPlusSource`
- `kUTTypeCSource` - `CSource`
- `kUTTypeData` - `Database`
- `kUTTypeDelimitedText` - `DelimitedText`
- `kUTTypeDescriptionKey` - `DescriptionKey`
- `kUTTypeDirectory` - `Directory`
- `kUTTypeDiskImage` - `DiskImage`
- `kUTTypeElectronicPublication` - `ElectronicPublication`
- `kUTTypeEmailMessage` - `EmailMessage`
- `kUTTypeExecutable` - `Executable`
- `kUTExportedTypeDeclarationsKey` - `ExportedTypeDeclarationsKey`
- `kUTTypeFileURL` - `FileURL`
- `kUTTypeFlatRTFD` - `FlatRTFD`
- `kUTTypeFolder` - `Folder`
- `kUTTypeFont` - `Font`
- `kUTTypeFramework` - `Framework`
- `kUTTypeGIF` - `GIF`
- `kUTTypeGNUZipArchive` - `GNUZipArchive` 
- `kUTTypeHTML` - `HTML`
- `kUTTypeICO` - `ICO`
- `kUTTypeIconFileKey` - `IconFileKey`
- `kUTTypeIdentifierKey` - `IdentifierKey`
- `kUTTypeImage` - `Image`
- `kUTImportedTypeDeclarationsKey` - `ImportedTypeDeclarationsKey`
- `kUTTypeInkText` - `InkText`
- `kUTTypeInternetLocation` - `InternetLocation`
- `kUTTypeItem` - `Item`
- `kUTTypeJavaArchive` - `JavaArchive`
- `kUTTypeJavaClass` - `JavaClass`
- `kUTTypeJavaScript` - `JavaScript`
- `kUTTypeJavaSource` - `JavaSource`
- `kUTTypeJPEG` - `JPEG`
- `kUTTypeJPEG2000` - `JPEG2000`
- `kUTTypeJSON` - `JSON`
- `kUTType3dObject` - `k3dObject`
- `kUTTypeLivePhoto` - `LivePhoto`
- `kUTTypeLog` - `Log` 
- `kUTTypeM3UPlaylist` - `M3UPlaylist`
- `kUTTypeMessage` - `Message`
- `kUTTypeMIDIAudio` - `MIDIAudio`
- `kUTTypeMountPoint` - `MountPoint`
- `kUTTypeMovie` - `Movie`
- `kUTTypeMP3` - `MP3`
- `kUTTypeMPEG` - `MPEG`
- `kUTTypeMPEG2TransportStream` - `MPEG2TransportStream`
- `kUTTypeMPEG2Video` - `MPEG2Video`
- `kUTTypeMPEG4` - `MPEG4`
- `kUTTypeMPEG4Audio` - `MPEG4Audio`
- `kUTTypeObjectiveCPlusPlusSource` - `ObjectiveCPlusPlusSource`
- `kUTTypeObjectiveCSource` - `ObjectiveCSource`
- `kUTTypeOSAScript` - `OSAScript`
- `kUTTypeOSAScriptBundle` - `OSAScriptBundle`
- `kUTTypePackage` - `Package`
- `kUTTypePDF` - `PDF`
- `kUTTypePerlScript` - `PerlScript`
- `kUTTypePHPScript` - `PHPScript`
- `kUTTypePICT` - `PICT`
- `kUTTypePKCS12` - `PKCS12`
- `kUTTypePlainText` - `PlainText`
- `kUTTypePlaylist` - `Playlist`
- `kUTTypePluginBundle` - `PluginBundle`
- `kUTTypePNG` - `PNG`
- `kUTTypePolygon` - `Polygon`
- `kUTTypePresentation` - `Presentation`
- `kUTTypePropertyList` - `PropertyList`
- `kUTTypePythonScript` - `PythonScript`
- `kUTTypeQuickLookGenerator` - `QuickLookGenerator`
- `kUTTypeQuickTimeImage` - `QuickTimeImage`
- `kUTTypeQuickTimeMovie` - `QuickTimeMovie` 
- `kUTTypeRawImage` - `RawImage`
- `kUTTypeReferenceURLKey` - `ReferenceURLKey`
- `kUTTypeResolvable` - `Resolvable`
- `kUTTypeRTF` - `RTF`
- `kUTTypeRTFD` - `RTFD`
- `kUTTypeRubyScript` - `RubyScript`
- `kUTTypeScalableVectorGraphics` - `ScalableVectorGraphics`
- `kUTTypeScript` - `Script`
- `kUTTypeShellScript` - `ShellScript`
- `kUTTypeSourceCode` - `SourceCode`
- `kUTTypeSpotlightImporter` - `SpotlightImporter`
- `kUTTypeSpreadsheet` - `Spreadsheet`
- `kUTTypeStereolithography` - `Stereolithography`
- `kUTTypeSwiftSource` - `SwiftSource`
- `kUTTypeSymLink` - `SymLink`
- `kUTTypeSystemPreferencesPane` - `SystemPreferencesPane`
- `kUTTypeTabSeparatedText` - `TabSeparatedText`
- `kUTTagClassFilenameExtension` - `TagClassFilenameExtension`
- `kUTTagClassMIMEType` - `TagClassMIMEType`
- `kUTTypeTagSpecificationKey` - `TagSpecificationKey`
- `kUTTypeText` - `Text`
- `kUTType3DContent` - `ThreeDContent`
- `kUTTypeTIFF` - `TIFF`
- `kUTTypeToDoItem` - `ToDoItem`
- `kUTTypeTXNTextAndMultimediaData` - `TXNTextAndMultimediaData`
- `kUTTypeUniversalSceneDescription` - `UniversalSceneDescription`
- `kUTTypeUnixExecutable` - `UnixExecutable`
- `kUTTypeURL` - `URL` 
- `kUTTypeURLBookmarkData` - `URLBookmarkData`
- `kUTTypeUTF16ExternalPlainText` - `UTF16ExternalPlainText`
- `kUTTypeUTF16PlainText` - `UTF16PlainText`
- `kUTTypeUTF8PlainText` - `UTF8PlainText`
- `kUTTypeUTF8TabSeparatedText` - `UTF8TabSeparatedText`
- `kUTTypeVCard` - `VCard`
- `kUTTypeVersionKey` - `VersionKey` 
- `kUTTypeVideo` - `Video` 
- `kUTTypeVolume` - `Volume` 
- `kUTTypeWaveformAudio` - `WaveformAudio`
- `kUTTypeWebArchive` - `WebArchive`
- `kUTTypeWindowsExecutable` - `WindowsExecutable`
- `kUTTypeX509Certificate` - `X509Certificate`
- `kUTTypeXML` - `XML`
- `kUTTypeXMLPropertyList` - `XMLPropertyList`
- `kUTTypeXPCService` - `XPCService`
- `kUTTypeZipArchive` - `ZipArchive`

Vea el ejemplo siguiente:

```csharp
using MobileCoreServices;
...

NSItemProvider itemProvider = new NSItemProvider ();
itemProvider.LoadItem(UTType.PropertyList ,null, (item, err) => {
    if (err == null) {
        NSDictionary results = (NSDictionary )item;
        NSString baseURI =
results.ObjectForKey("NSExtensionJavaScriptPreprocessingResultsKey");
    }
});
```

Para obtener más información, consulte el [grupos de aplicaciones](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) sección de nuestro **trabajar con capacidades** documentación.


<a name="Precautions-and-Considerations" />

## <a name="precautions-and-considerations"></a>Precauciones y consideraciones

Extensiones tienen mucha menos memoria disponible a ellos de hacer las aplicaciones. Deberán realizar rápidamente y con la mínima intrusión al usuario y la aplicación que se hospedan en. Sin embargo, una extensión también debe proporcionar una función útil distintivo a la aplicación de consumo con una interfaz de usuario con la marca que permiten al usuario identificar el desarrollador de la extensión o una aplicación de contenedor al que pertenecen.

Dados estos requisitos estrictos, solo se deben implementar las extensiones que se han probado exhaustivamente y optimizado para rendimiento y el consumo de memoria. 

<a name="Summary" />

## <a name="summary"></a>Resumen

Este documento trata extensiones, ¿qué son, el tipo de puntos de extensión y las limitaciones conocidas que se impone en una extensión de iOS. También se han descrito crear, distribuir, instalar y ejecutar las extensiones y el ciclo de vida de la extensión. Proporciona un tutorial para crear un sencillo **hoy** widget que muestra dos maneras de crear la interfaz de usuario del widget con guiones gráficos o código. Ha explicado cómo probar una extensión en el simulador de iOS. Por último, describe brevemente comunicarse con la aplicación Host y algunas precauciones y consideraciones que deben tenerse en cuenta al desarrollar una extensión. 


## <a name="related-links"></a>Vínculos relacionados

- [ContainerApp (ejemplo)](https://developer.xamarin.com/samples/monotouch/intro-to-extensions)
- [Crear extensiones de Xamarin.iOS (vídeo)](https://university.xamarin.com/lightninglectures/creating-extensions-in-ios)
