---
title: Extensiones de Xamarin.iOS de iOS
description: Este documento describe las extensiones, que son widgets presentadas por iOS en contexto estándar, como en el centro de notificaciones. Describe cómo crear una extensión y comunicarse con él desde la aplicación primaria.
ms.prod: xamarin
ms.assetid: 3DEB3D43-3E4A-4099-8331-93C1E7A77095
ms.technology: xamarin-ios
ms.custom: xamu-video
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: b21bf4da7cf862bd32e71708f9e3657f577682c2
ms.sourcegitcommit: 946ce514fd6575aa6b93ff24181e02a60b24b106
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/30/2019
ms.locfileid: "58677929"
---
# <a name="ios-extensions-in-xamarinios"></a>extensiones de iOS de Xamarin.iOS

> [!VIDEO https://youtube.com/embed/Sd0-ch9Udmk]

**Creación de extensiones en iOS, por [Xamarin University](https://university.xamarin.com/)**

Las extensiones, como se mencionó en iOS 8, están especializadas en `UIViewControllers` que se presentan por iOS en contextos estándares tal como en el **centro de notificaciones**, como tipos de teclado personalizado solicitados por el usuario para realizar especializados entrada o en otros contextos, como modificar una foto donde la extensión puede proporcionar filtros de efectos especiales.

Todas las extensiones se instalan junto con una aplicación de contenedor (con ambos elementos escritos mediante la API unificadas de 64 bits) y se activan desde un punto de extensión determinado en una aplicación Host. Y dado que se usará como complementos de funciones del sistema existente, deben ser eficiente y sólida de alto rendimiento. 

## <a name="extension-points"></a>Puntos de extensión

|Tipo|Descripción|Punto de extensión|Aplicación de host|
|--- |--- |--- |--- |
|Acción|Editor especializado o un visor para un tipo de medio determinado|`com.apple.ui-services`|Cualquiera|
|Proveedor de documentos|Permite que la aplicación usar un almacén de documentos de forma remota|`com.apple.fileprovider-ui`|Aplicaciones que usan un [UIDocumentPickerViewController](xref:UIKit.UIDocumentPickerViewController)|
|Teclado|Teclados alternativos|`com.apple.keyboard-service`|Cualquiera|
|Edición de fotografías.|Manipulación de fotografías y edición|`com.apple.photo-editing`|Editor de Photos.app|
|Compartir|Comparte datos con las redes sociales, mensajería, servicios, etcetera.|`com.apple.share-services`|Cualquiera|
|Hoy|"Widgets" que aparecen en la pantalla hoy o el centro de notificaciones|`com.apple.widget-extensions`|Hoy en día y el centro de notificaciones|

[Puntos de extensión adicionales](~/ios/platform/introduction-to-ios10/index.md#app-extensions) se agregaron en iOS 10.

## <a name="limitations"></a>Limitaciones

Las extensiones tienen una serie de limitaciones, algunos de los cuales son comunes a todos los tipos (por instancia, ningún tipo de extensión puede tener acceso a las cámaras o micrófonos) mientras que otros tipos de extensión pueden tener limitaciones específicas de su uso (por ejemplo, personalizados teclados no se puede usar para los campos de entrada de proteger los datos como contraseñas). 

Las limitaciones universales son:

- El [Kit Health](~/ios/platform/healthkit.md) y [la interfaz de usuario del Kit de evento](~/ios/platform/eventkit.md) marcos de trabajo no están disponibles
- No se pueden usar las extensiones [extendidos modos en segundo plano](https://developer.xamarin.com/guides/cross-platform/application_fundamentals/backgrounding/part_3_ios_backgrounding_techniques/registering_applications_to_run_in_background/)
- Las extensiones no pueden acceder a los micrófonos o cámaras del dispositivo (aunque es posible que acceden a los archivos multimedia existentes)
- Las extensiones no pueden recibir datos de aire Drop (aunque pueden transmitir datos a través de aire Drop)
- [UIActionSheet](xref:UIKit.UIActionSheet) y [UIAlertView](xref:UIKit.UIAlertView) no están disponibles; deben usar las extensiones [UIAlertController](xref:UIKit.UIAlertController)
- Varios miembros de [UIApplication](xref:UIKit.UIApplication) no están disponibles: [UIApplication.SharedApplication](xref:UIKit.UIApplication.SharedApplication), [UIApplication.OpenUrl](xref:UIKit.UIApplication.OpenUrl(Foundation.NSUrl)), [UIApplication.BeginIgnoringInteractionEvents](xref:UIKit.UIApplication.BeginIgnoringInteractionEvents) and [UIApplication.EndIgnoringInteractionEvents](xref:UIKit.UIApplication.EndIgnoringInteractionEvents)
- iOS impone un límite de uso de memoria de 16 MB en extensiones de hoy en día.
- De forma predeterminada, las extensiones de teclado no tienen acceso a la red. Esto afecta a la depuración en el dispositivo (no se aplica la restricción en el simulador), ya que Xamarin.iOS requiere el acceso a la red para que funcione la depuración. Es posible solicitar acceso de red estableciendo el `Requests Open Access` valor en Info.plist del proyecto a `Yes`. Consulte Apple [Guía de teclado personalizadas](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/CustomKeyboard.html) para obtener más información acerca de las limitaciones de extensión de teclado.

Para conocer las limitaciones individuales, consulte Apple [Guía de programación de extensión de aplicación](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/).

## <a name="distributing-installing-and-running-extensions"></a>Distribuir, instalar y ejecutar extensiones

Las extensiones se distribuyen desde dentro de una aplicación de contenedor, que a su vez se envía y se distribuyen a través de la aplicación de Store. Las extensiones que se distribuye con la aplicación se instalan en ese momento, pero el usuario debe habilitar cada extensión de forma explícita. Se habilitan los distintos tipos de extensiones de maneras diferentes; algunos requieren que el usuario ir a la **configuración** app y habilitarlas desde allí. Mientras que otros están habilitados en el momento de uso, como la habilitación de una extensión de uso compartido al enviar una foto. 

La aplicación en el que se utiliza la extensión (donde el usuario encuentra el punto de extensión) se conoce como el **aplicación Host**, ya que es la aplicación que hospeda la extensión cuando se ejecuta. La aplicación que instala la extensión es la **aplicación contenedora**, porque es la aplicación que contiene la extensión cuando se instaló.  

Normalmente, la aplicación contenedora describe la extensión y guía al usuario a través del proceso de habilitarlo.

## <a name="extension-lifecycle"></a>Ciclo de vida de extensión

Una extensión puede ser tan simple como una sola [UIViewController](xref:UIKit.UIViewController) o extensiones más complejas que presentan varias pantallas de interfaz de usuario. Cuando el usuario encuentra un _puntos de extensión_ (por ejemplo, cuando uso compartido de una imagen), tendrán la oportunidad de elegir entre las extensiones registradas de ese punto de extensión. 

Si elige una de la aplicación de extensiones, su `UIViewController` se creará una instancia y comenzar el ciclo de vida de controlador de vista normal. Sin embargo, a diferencia de una aplicación normal, que se suspende, pero generalmente no termina cuando el usuario termina de interactuar con ellos, las extensiones de carga, ejecutar y después termina de forma repetida.

Las extensiones pueden comunicarse con sus aplicaciones de Host a través de un [NSExtensionContext](xref:Foundation.NSExtensionContext) objeto. Algunas extensiones tienen las operaciones que reciben las devoluciones de llamada asincrónicas con los resultados. Estas devoluciones de llamada se ejecutará en subprocesos en segundo plano y la extensión debe tener esto en cuenta; Por ejemplo, mediante el uso de [NSObject.InvokeOnMainThread](xref:Foundation.NSObject.InvokeOnMainThread*) si desean actualizar la interfaz de usuario. Consulte la [comunicarse con la aplicación Host](#communicating-with-the-host-app) sección para obtener más detalles.

De forma predeterminada, las extensiones y sus aplicaciones de contenedor no pueden comunicarse, a pesar de que se instalan conjuntamente. En algunos casos, la aplicación de contenedor es básicamente un contenedor "shipping" vacío se sirve cuyo propósito una vez que la extensión está instalada. Sin embargo, si las circunstancias, la aplicación de contenedor y la extensión pueden compartir los recursos de un área común. Además, un **hoy extensión** puede solicitar su aplicación de contenedor para abrir una dirección URL. Este comportamiento se muestra en el [evolucionar Countdown Widget](https://github.com/xamarin/monotouch-samples/tree/master/ExtensionsDemo).

## <a name="creating-an-extension"></a>Creación de una extensión

Extensiones (y sus aplicaciones de contenedor) deben ser archivos binarios de 64 bits y compiladas con Xamarin.iOS [API unificadas](https://developer.xamarin.com/guides/cross-platform/macios/unified). Al desarrollar una extensión, las soluciones contendrá al menos dos proyectos: la aplicación de contenedor y un proyecto para cada contenedor de la extensión proporciona. 

### <a name="container-app-project-requirements"></a>Requisitos del proyecto de aplicación de contenedor

La aplicación de contenedor que se usa para instalar la extensión tiene los siguientes requisitos:

- Debe mantener una referencia al proyecto de extensión.   
- Debe ser una aplicación completa (debe ser capaz de iniciar y ejecutar correctamente) incluso si no hace nada más que proporcionan una manera de instalar una extensión. 
- Debe tener un identificador de paquete que constituye la base para el identificador de paquete de la extensión de proyecto (vea la sección siguiente para obtener más detalles).

### <a name="extension-project-requirements"></a>Requisitos del proyecto de extensión

Además, el proyecto de la extensión tiene los siguientes requisitos:

- Debe tener un identificador de lote que empieza por el identificador de paquete de la aplicación de su contenedor. Por ejemplo, si la aplicación de contenedor tiene un identificador de lote de `com.myCompany.ContainerApp`, podría ser el identificador de la extensión `com.myCompany.ContainerApp.MyExtension`: 

    ![](extensions-images/bundleidentifiers.png) 
- Debe definir la clave `NSExtensionPointIdentifier`, con un valor adecuado (como `com.apple.widget-extension` para un **hoy** widget del centro de notificaciones), en su `Info.plist` archivo.
- También debe definir *cualquier* el `NSExtensionMainStoryboard` clave o el `NSExtensionPrincipalClass` clave en su `Info.plist` archivo con un valor adecuado:
    - Use la `NSExtensionMainStoryboard` clave para especificar el nombre del guión gráfico que presenta la interfaz de usuario principal de la extensión (menos `.storyboard`). Por ejemplo, `Main` para el `Main.storyboard` archivo.
    - Use la `NSExtensionPrincipalClass` clave para especificar la clase que se inicializará cuando se inicia la extensión. El valor debe coincidir con el **registrar** valor de su `UIViewController`: 

    ![](extensions-images/registerandprincipalclass.png)

Determinados tipos de extensiones pueden tener requisitos adicionales. Por ejemplo, un **hoy** o **centro de notificaciones** debe implementar la clase de entidad de seguridad de la extensión [INCWidgetProviding](https://developer.xamarin.com/api/type/NotificationCenter.INCWidgetProviding/).

> [!IMPORTANT]
> Si inicia el proyecto con una de las plantillas de las extensiones proporcionadas por Visual Studio para Mac, mayoría (si no todos) de estos requisitos se proporcionan y cumple por usted automáticamente mediante la plantilla.

## <a name="walkthrough"></a>Tutorial 

En el siguiente tutorial, creará un ejemplo **hoy** widget que calcula el día y el número de días que quedan en el año:

[![](extensions-images/carpediemscreenshot-sm.png "Un widget de hoy de ejemplo que calcula el día y el número de días que quedan en el año")](extensions-images/carpediemscreenshot.png#lightbox)

### <a name="creating-the-solution"></a>Creación de la solución

Para crear la solución necesaria, haga lo siguiente:

1. En primer lugar, cree una nueva iOS **aplicación de vista única** del proyecto y haga clic en el **siguiente** botón: 

    [![](extensions-images/today01.png "En primer lugar, cree un proyecto de aplicación de vista única iOS nueva y haga clic en el botón siguiente")](extensions-images/today01.png#lightbox)
2. Llame al proyecto `TodayContainer` y haga clic en el **siguiente** botón: 

    [![](extensions-images/today02.png "Llame al proyecto TodayContainer y haga clic en el botón siguiente")](extensions-images/today02.png#lightbox)
3. Compruebe el **nombre del proyecto** y **SolutionName** y haga clic en el **crear** botón para crear la solución: 

    [![](extensions-images/today03.png "Compruebe el nombre del proyecto y SolutionName y haga clic en el botón Crear para crear la solución")](extensions-images/today03.png#lightbox)
4. A continuación, en el **el Explorador de soluciones**, haga doble clic en la solución y agregue un nuevo **iOS extensión** proyecto desde el **hoy extensión** plantilla: 

    [![](extensions-images/today04.png "A continuación, en el Explorador de soluciones, haga doble clic en la solución y agregue un nuevo proyecto de extensión de iOS de la plantilla de extensión de hoy en día")](extensions-images/today04.png#lightbox)
5. Llame al proyecto `DaysRemaining` y haga clic en el **siguiente** botón: 

    [![](extensions-images/today05.png "Llame al proyecto DaysRemaining y haga clic en el botón siguiente")](extensions-images/today05.png#lightbox)
6. Revise el proyecto y haga clic en el **crear** botón para crearlo: 

    [![](extensions-images/today06.png "Revise el proyecto y haga clic en el botón Crear para crearla")](extensions-images/today06.png#lightbox)

La solución resultante ahora debería tener dos proyectos, como se muestra aquí:

[![](extensions-images/today07.png "La solución resultante ahora debería tener dos proyectos, como se muestra aquí")](extensions-images/today07.png#lightbox)

### <a name="creating-the-extension-user-interface"></a>Creación de la interfaz de usuario de extensión

A continuación, debe diseñar la interfaz para su **hoy** widget. Esto puede hacerse ya sea utilizando un guión gráfico o la interfaz de usuario mediante la creación de código. Ambos métodos se explicará a continuación en detalle.

#### <a name="using-storyboards"></a>Uso de guiones gráficos

Para compilar la interfaz de usuario con un guión gráfico, haga lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en el proyecto de extensión `Main.storyboard` archivo para abrirlo y editarlo: 

    [![](extensions-images/today08.png "Haga doble clic en el archivo de extensión proyectos Main.storyboard para abrirlo y editarlo")](extensions-images/today08.png#lightbox)
2. Seleccione la etiqueta que se agregó automáticamente a la interfaz de usuario mediante la plantilla y asígnele el **nombre** `TodayMessage` en el **Widget** pestaña de la **Explorador de propiedades**: 

    [![](extensions-images/today09.png "Seleccione la etiqueta que se agregó automáticamente a la interfaz de usuario mediante la plantilla y asígnele el nombre de TodayMessage en la pestaña de Widget del explorador de propiedades")](extensions-images/today09.png#lightbox)
3. Guarde los cambios en el guión gráfico.

#### <a name="using-code"></a>Mediante código

Para compilar la interfaz de usuario en el código, realice lo siguiente: 

1. En el **el Explorador de soluciones**, seleccione el **DaysRemaining** del proyecto, agregue una nueva clase y llámelo `CodeBasedViewController`: 

    [![](extensions-images/code01.png "Aelect el proyecto DaysRemaining, agregue una nueva clase y llámelo CodeBasedViewController")](extensions-images/code01.png#lightbox)
2. De nuevo, en el **el Explorador de soluciones**, haga doble clic en la extensión `Info.plist` archivo para abrirlo y editarlo: 

    [![](extensions-images/code02.png "Haga doble clic en el archivo de extensiones Info.plist para abrirlo y editarlo")](extensions-images/code02.png#lightbox)
3. Seleccione el **vista del origen de** (desde la parte inferior de la pantalla) y abra el `NSExtension` nodo: 

    [![](extensions-images/code03.png "Seleccione la vista del origen de la parte inferior de la pantalla y abra el nodo NSExtension")](extensions-images/code03.png#lightbox)
4. Quitar el `NSExtensionMainStoryboard` clave y agregue un `NSExtensionPrincipalClass` con el valor `CodeBasedViewController`: 

    [![](extensions-images/code04.png "Quitar la clave NSExtensionMainStoryboard y agregue un NSExtensionPrincipalClass con el valor CodeBasedViewController")](extensions-images/code04.png#lightbox)
5. Guarde los cambios.

A continuación, edite el `CodeBasedViewController.cs` de archivo y dele un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using UIKit;
using NotificationCenter;
using CoreGraphics;

namespace DaysRemaining
{
    [Register("CodeBasedViewController")]
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

Tenga en cuenta que el `[Register("CodeBasedViewController")]` coincide con el valor que especificó para el `NSExtensionPrincipalClass` anteriormente.

### <a name="coding-the-extension"></a>La extensión de codificación

Con la interfaz de usuario creada, abra el `TodayViewController.cs` o `CodeBasedViewController.cs` archivo (en función del método utilizado para crear la interfaz de usuario anterior), el cambio de la **ViewDidLoad** método y darle un aspecto similar al siguiente:

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

Si con el código según el método de interfaz de usuario, reemplace el `// Insert code to power extension here...` comentario con el nuevo código anterior. Después de llamar a la implementación base (e insertar una etiqueta para la versión del código que se basa), este código realiza un cálculo simple para obtener el día del año y durante cuántos días quedan. A continuación, muestra el mensaje en la etiqueta (`TodayMessage`) que creó en el diseño de interfaz de usuario.

Tenga en cuenta cómo similar este proceso es el proceso normal de escribir una aplicación. Una extensión `UIViewController` tiene el mismo ciclo de vida como un controlador de vista en una aplicación, excepto las extensiones no tienen modos en segundo plano y no se suspenderán cuando el usuario ha terminado con ellos. En su lugar, las extensiones repetidamente se inicializa y se desasignan según sea necesario.

### <a name="creating-the-container-app-user-interface"></a>Creación de la interfaz de usuario de la aplicación de contenedor

Para este tutorial, la aplicación de contenedor se usa simplemente como un método para distribuir e instalar la extensión y no proporciona ninguna funcionalidad propia. Editar el TodayContainer `Main.storyboard` archivo y agregue algún texto en la definición de función de la extensión y cómo instalarlo:

[![](extensions-images/today10.png "Edite el archivo TodayContainers Main.storyboard y agregar texto de definición de la función de las extensiones y cómo instalarlo")](extensions-images/today10.png#lightbox)

Guarde los cambios en el guión gráfico.

### <a name="testing-the-extension"></a>La extensión de pruebas

Para probar la extensión en el simulador de iOS, ejecute el **TodayContainer** app. Se mostrará la vista principal del contenedor:

[![](extensions-images/run01.png "Se mostrará la vista principal de contenedores")](extensions-images/run01.png#lightbox)

A continuación, presione el **inicio** botón en el simulador, deslice el dedo hacia abajo desde la parte superior de la pantalla para abrir el **centro de notificaciones**, seleccione el **hoy** ficha y haga clic en el **Editar** botón:

[![](extensions-images/run02.png "Presione el botón de inicio en el simulador, deslice el dedo hacia abajo desde la parte superior de la pantalla para abrir el centro de notificaciones, seleccione la pestaña de hoy y haga clic en el botón Editar")](extensions-images/run02.png#lightbox)

Agregar el **DaysRemaining** extensión a la **hoy** ver y haga clic en el **realiza** botón:

[![](extensions-images/run03.png "Agregar la DaysRemaining extensión a la vista hoy y haga clic en el botón Listo")](extensions-images/run03.png#lightbox)

El nuevo widget se agregará a la **hoy** vista y los resultados se mostrarán:

[![](extensions-images/run04.png "El nuevo widget se agregará a la vista hoy y los resultados se mostrarán")](extensions-images/run04.png#lightbox)

## <a name="communicating-with-the-host-app"></a>Comunicación con la aplicación host

El ejemplo de extensión de hoy en día que creó anteriormente no se comunica con su aplicación de host (el **hoy** pantalla). Si lo hiciera, usaría el [ExtensionContext](xref:Foundation.NSExtensionContext) propiedad de la `TodayViewController` o `CodeBasedViewController` clases. 

Para las extensiones que van a recibir datos desde sus aplicaciones host, los datos están en el formulario de una matriz de [NSExtensionItem](xref:Foundation.NSExtensionItem) objetos almacenados en el [InputItems](xref:Foundation.NSExtensionContext.InputItems) propiedad de la [ExtensionContext ](xref:Foundation.NSExtensionContext) de la extensión `UIViewController`.

Otra extensión, como las extensiones de edición de fotos, puede distinguir entre el usuario completar o cancelar el uso. Esto señalará a la aplicación host a través de la [CompleteRequest](xref:Foundation.NSExtensionContext.CompleteRequest*) y [CancelRequest](xref:Foundation.NSExtensionContext.CancelRequest*) métodos de [ExtensionContext](xref:Foundation.NSExtensionContext) propiedad.

Para obtener más información, consulte Apple [Guía de programación de extensión de aplicación](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW1).

## <a name="communicating-with-the-parent-app"></a>Comunicación con la aplicación primaria

Un grupo de aplicaciones permite que aplicaciones diferentes (o una aplicación y sus extensiones) tengan acceso a una ubicación de almacenamiento de archivos compartidos. Los grupos de aplicaciones se pueden usar para datos como:

- [Configuración de Apple Watch](~/ios/watchos/app-fundamentals/settings.md).
- [Valores NSUserDefaults compartidos](~/ios/app-fundamentals/user-defaults.md).
- [Archivos compartidos](~/ios/watchos/app-fundamentals/parent-app.md#files).

Para obtener más información, consulte el [grupos de aplicaciones](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) sección de nuestra **trabajar con capacidades** documentación.

## <a name="mobilecoreservices"></a>MobileCoreServices

Cuando se trabaja con las extensiones, use un identificador uniforme de tipo (UTI) para crear y manipular los datos que se intercambian entre la aplicación, otras aplicaciones o servicios.

El `MobileCoreServices.UTType` clase estática define las siguientes propiedades de la aplicación auxiliar que se relacionan con Apple `kUTType...` definiciones:

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

Consulte el ejemplo siguiente:

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

Para obtener más información, consulte el [grupos de aplicaciones](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) sección de nuestra **trabajar con capacidades** documentación.

## <a name="precautions-and-considerations"></a>Precauciones y consideraciones

Las extensiones tienen significativamente menos memoria a su disposición de las aplicaciones lo hacen. Se espera para realizar rápidamente y con intrusiones mínima para el usuario y la aplicación se hospedan en. Sin embargo, una extensión también debe proporcionar una función distintiva y útil a la aplicación con una interfaz de usuario con marca que permiten al usuario identificar el desarrollador de la extensión que lo consume o una aplicación de contenedor que pertenecen.

Dadas estas requisito estricto, solo se deben implementar las extensiones que se han probado exhaustivamente y optimizado para rendimiento y consumo de memoria. 

## <a name="summary"></a>Resumen

Este documento ha descrito las extensiones, qué son y el tipo de puntos de extensión y las limitaciones conocidas que impone una extensión de iOS. Tratan de crear, distribuir, instalar y ejecutar las extensiones y el ciclo de vida de la extensión. Proporciona un tutorial sobre la creación de una sencilla **hoy** widget que muestra dos maneras de crear la interfaz de usuario del widget mediante guiones gráficos o código. Se ha mostrado cómo probar una extensión en el simulador de iOS. Finalmente, analizan brevemente comunicarse con la aplicación Host y algunas precauciones y consideraciones que deben realizarse al desarrollar una extensión. 

## <a name="related-links"></a>Vínculos relacionados

- [ContainerApp (ejemplo)](https://developer.xamarin.com/samples/monotouch/intro-to-extensions)
- [Creación de extensiones en Xamarin.iOS (vídeo)](https://university.xamarin.com/lightninglectures/creating-extensions-in-ios)
