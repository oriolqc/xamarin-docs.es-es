---
title: Análisis detallado de Xamarin.Forms
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: d97aa580-1eb9-48b3-b15b-0d7421ea7ae
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/10/2018
ms.openlocfilehash: b51389a7ab6506908cb21f6657820575efdc0615
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2018
ms.locfileid: "34846769"
---
# <a name="xamarinforms-deep-dive"></a>Análisis detallado de Xamarin.Forms

En [Inicio rápido de Xamarin.Forms](~/xamarin-forms/get-started/hello-xamarin-forms/quickstart.md) se creó la aplicación de Phoneword. En este artículo se revisa lo que se ha compilado para comprender los aspectos fundamentales del funcionamiento de las aplicaciones de Xamarin.Forms.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="introduction-to-visual-studio"></a>Introducción a Visual Studio

Visual Studio es un IDE muy completo de Microsoft. Incluye un diseñador visual completamente integrado, un editor de texto completo con herramientas de refactorización, un explorador de ensamblados, integración de código fuente y mucho más. Este artículo se centra en el uso de algunas características básicas de Visual Studio con el complemento de Xamarin.

Visual Studio organiza el código en *soluciones* y *proyectos*. Una solución es un contenedor que puede incluir uno o varios proyectos. Un proyecto puede ser una aplicación, una biblioteca auxiliar o una aplicación de prueba, entre otros. La aplicación de Phoneword consta de una solución que contiene cuatro proyectos, como se muestra en la siguiente captura de pantalla.

![](deepdive-images/vs/solution.png "Explorador de soluciones de Visual Studio")

Los proyectos son:

- Phoneword: este proyecto es el de la biblioteca de .NET Standard que incluye todo el código compartido y la interfaz de usuario compartida.
- Phoneword.Android: este proyecto incluye el código específico de Android y es el punto de entrada de la aplicación Android.
- Phoneword.iOS: este proyecto incluye el código específico de iOS y es el punto de entrada de la aplicación de iOS.
- Phoneword.UWP: este proyecto incluye el código específico de plataforma universal de Windows (UWP) y es el punto de entrada de la aplicación de UWP.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomía de una aplicación de Xamarin.Forms

En la siguiente captura de pantalla se muestra el contenido del proyecto de la biblioteca de .NET Standard en Visual Studio:

![](deepdive-images/vs/net-standard-project.png "Contenido del proyecto de .NET Standard de Phoneword")

El proyecto tiene un nodo **Dependencias** que contiene los nodos **NuGet** y **SDK**. El nodo **NuGet** contiene el paquete NuGet de Xamarin.Forms que se ha agregado al proyecto, y el nodo **SDK** contiene el metapaquete `NETStandard.Library` que hace referencia al conjunto completo de paquetes NuGet que definen .NET Standard.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

## <a name="introduction-to-visual-studio-for-mac"></a>Introducción a Visual Studio para Mac

Visual Studio para Mac es un IDE gratuito y de código abierto similar a Visual Studio. Incluye un diseñador visual completamente integrado, un editor de texto completo con herramientas de refactorización, un explorador de ensamblados, integración de código fuente y mucho más. Para más información sobre Visual Studio para Mac, consulte [Presentación de Visual Studio para Mac](/visualstudio/mac/).

Visual Studio para Mac sigue la práctica de Visual Studio consistente en organizar el código en *soluciones* y *proyectos*. Una solución es un contenedor que puede incluir uno o varios proyectos. Un proyecto puede ser una aplicación, una biblioteca auxiliar o una aplicación de prueba, entre otros. La aplicación de Phoneword consta de una solución que contiene tres proyectos, como se muestra en la siguiente captura de pantalla.

![](deepdive-images/xs/solution.png "Panel de solución de Visual Studio para Mac")

Los proyectos son:

- Phoneword: este proyecto es el de la biblioteca de .NET Standard que incluye todo el código compartido y la interfaz de usuario compartida.
- Phoneword.Droid: este proyecto incluye el código específico de Android y es el punto de entrada de las aplicaciones de Android.
- Phoneword.iOS: este proyecto incluye el código específico de iOS y es el punto de entrada de las aplicaciones de iOS.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomía de una aplicación de Xamarin.Forms

En la siguiente captura de pantalla se muestra el contenido del proyecto de PCL de Phoneword en Visual Studio para Mac:

![](deepdive-images/xs/pcl-project.png "Contenido del proyecto de PCL de Phoneword")

El proyecto consta de tres carpetas:

- **Referencias**: contiene los ensamblados necesarios para compilar y ejecutar la aplicación. Al expandir la carpeta Subconjunto portable de .NET se muestran referencias de ensamblados de .NET como [System](http://msdn.microsoft.com/library/system%28v=vs.110%29.aspx), System.Core y [System.Xml](http://msdn.microsoft.com/library/system.xml%28v=vs.110%29.aspx). Al expandir la carpeta **De paquetes** se muestran referencias a los ensamblados de Xamarin.Forms.
- **Paquetes**: el directorio Paquetes hospeda paquetes de [NuGet](https://www.nuget.org) que simplifican el proceso de uso de bibliotecas de terceros en su aplicación. Estos paquetes pueden actualizarse a las últimas versiones haciendo clic con el botón derecho en la carpeta y seleccionando la opción de actualización en el menú emergente.
- **Propiedades**: contiene **AssemblyInfo.cs**, un archivo de metadatos de ensamblado .NET. Se recomienda que incluya en este archivo información básica sobre la aplicación. Para obtener más información sobre este archivo, consulte [Clase AssemblyInfo](http://msdn.microsoft.com/library/microsoft.visualbasic.applicationservices.assemblyinfo(v=vs.110).aspx) en MSDN.

-----

El proyecto también consta de varios archivos:

- **App.xaml**: el marcado XAML para la clase `App`, que define un diccionario de recursos para la aplicación.
- **App.xaml.cs**: el código subyacente para la clase `App`, que es el responsable de crear instancias de la primera página que se mostrarán mediante la aplicación en cada plataforma, y para controlar los eventos del ciclo de vida de la aplicación.
- **IDialer.cs<**: la interfaz `IDialer`, que especifica que el método `Dial` debe proporcionarse mediante cualquier clase de implementación.
- **MainPage.xaml**: el marcado XAML para la clase `MainPage`, que define la interfaz de usuario para la página que se muestra cuando se inicia la aplicación.
- **MainPage.xaml.cs**: el código subyacente para la clase `MainPage`, que contiene la lógica de negocios que se ejecuta cuando el usuario interactúa con la página.
- **packages.config** (solo Visual Studio para Mac): archivo XML que contiene información sobre los paquetes NuGet que usa el proyecto para hacer un seguimiento de los paquetes necesarios y de sus respectivas versiones. Tanto Visual Studio para Mac como Visual Studio pueden configurarse para restaurar automáticamente cualquier paquete de NuGet que falte al compartir el código fuente con otros usuarios. El contenido de este archivo se controla mediante el administrador del paquete de NuGet y no debe editarse manualmente.
- **PhoneTranslator.cs**: la lógica de negocios responsable de convertir números con letras en un número de teléfono, que se invoca desde **MainPage.xaml.cs**.

Para obtener más información sobre la anatomía de una aplicación de Xamarin.iOS, consulte [Anatomía de una aplicación de Xamarin.iOS](~/ios/get-started/hello-ios/hello-ios-deepdive.md#anatomy). Para obtener más información sobre la anatomía de una aplicación de Xamarin.Android, consulte [Anatomía de una aplicación de Xamarin.Android](~/android/get-started/hello-android/hello-android-deepdive.md#anatomy).

## <a name="architecture-and-application-fundamentals"></a>Arquitectura y elementos fundamentales de la aplicación

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Una aplicación de Xamarin.Forms tiene la misma arquitectura que una aplicación multiplataforma tradicional. El código compartido normalmente se coloca en una biblioteca de .NET Standard, y las aplicaciones específicas de la plataforma consumen el código compartido. En el siguiente diagrama se muestra información general de esta relación para la aplicación de Phoneword:

![](deepdive-images/vs/architecture.png "Arquitectura de Phoneword")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Una aplicación de Xamarin.Forms tiene la misma arquitectura que una aplicación multiplataforma tradicional. El código compartido normalmente se coloca en una biblioteca de .NET Standard, y las aplicaciones específicas de la plataforma consumen el código compartido. En el siguiente diagrama se muestra información general de esta relación para la aplicación de Phoneword:

![](deepdive-images/xs/architecture.png "Arquitectura de Phoneword")

Para obtener más información sobre PCL, consulte [Introducción a las bibliotecas de clases portables](~/cross-platform/app-fundamentals/pcl.md).

-----

Para maximizar la reutilización del código de inicio, las aplicaciones de Xamarin.Forms tienen una clase única denominada `App` que es responsable de crear instancias de la primera página que mostrará la aplicación en cada plataforma, como se muestra en el siguiente ejemplo de código:

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Xaml;

[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
namespace Phoneword
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();
            MainPage = new MainPage();
        }
        ...
    }
}
```

En este código se establece la propiedad `MainPage` de la clase `App` para una nueva instancia de la clase [`MainPage`](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/). Además, el atributo [`XamlCompilation`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationAttribute/) se activa en el compilador XAML, de manera que XAML se compile directamente en lenguaje intermedio. Para obtener más información, consulte [Compilación XAML](~/xamarin-forms/xaml/xamlc.md).

## <a name="launching-the-application-on-each-platform"></a>Iniciar la aplicación en cada plataforma

### <a name="ios"></a>iOS

Para iniciar la página de Xamarin.Forms inicial en iOS, el proyecto Phoneword.iOS incluye la clase `AppDelegate` que hereda de la clase `FormsApplicationDelegate`, como se muestra en el siguiente ejemplo de código:

```csharp
namespace Phoneword.iOS
{
    [Register ("AppDelegate")]
    public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
    {
        public override bool FinishedLaunching (UIApplication app, NSDictionary options)
        {
            global::Xamarin.Forms.Forms.Init ();
            LoadApplication (new App ());
            return base.FinishedLaunching (app, options);
        }
    }
}
```

El reemplazo `FinishedLaunching` inicializa el marco de Xamarin.Forms mediante una llamada al método `Init`. Esto provoca que la implementación específica de iOS de Xamarin.Forms se cargue en la aplicación antes de que se establezca el controlador de vista raíz mediante la llamada al método `LoadApplication`.

### <a name="android"></a>Android

Para iniciar la página de inicio de Xamarin.Forms en Android, el proyecto Phoneword.Droid incluye código para crear un elemento `Activity` con el atributo `MainLauncher` (la actividad se hereda de la clase `FormsApplicationActivity`) como se muestra en el siguiente ejemplo de código:

```csharp
namespace Phoneword.Droid
{
    [Activity(Label = "Phoneword",
              Icon = "@drawable/icon",
              MainLauncher = true,
              ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity
    {
        internal static MainActivity Instance { get; private set; }

        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            Instance = this;
            global::Xamarin.Forms.Forms.Init(this, bundle);
            LoadApplication(new App());
        }
    }
}
```

El reemplazo `OnCreate` inicializa el marco de Xamarin.Forms mediante una llamada al método `Init`. Esto provoca que la implementación específica de Android de Xamarin.Forms se cargue en la aplicación antes de que lo haga la aplicación de Xamarin.Forms. Además, la clase `MainActivity` almacena una referencia a sí misma en la propiedad `Instance`. La propiedad `Instance` se conoce como el contexto local, y se hace referencia a ella en la clase `PhoneDialer`.

## <a name="universal-windows-platform"></a>Plataforma universal de Windows

En las aplicaciones de plataforma universal de Windows (UWP), el método `Init` que inicializa el marco de Xamarin.Forms se invoca desde la clase `App`:

```csharp
Xamarin.Forms.Forms.Init (e);

if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
{
  ...
}
```

Esto provoca que la implementación específica de UWP de Xamarin.Forms se cargue en la aplicación. La página de Xamarin.Forms inicial se inicia mediante la clase `MainPage`, como se muestra en el siguiente ejemplo de código:

```csharp
namespace Phoneword.UWP
{
    public sealed partial class MainPage
    {
        public MainPage()
        {
            this.InitializeComponent();
            this.LoadApplication(new Phoneword.App());
        }
    }
}
```

La aplicación de Xamarin.Forms se carga con el método `LoadApplication`.

> [!NOTE]
> Las aplicaciones de plataforma universal de Windows pueden estar compiladas con Xamarin.Forms, pero usando solo Visual Studio en Windows.

## <a name="user-interface"></a>Interfaz de usuario

Existen cuatro grupos de control principales que se usan para crear la interfaz de usuario de una aplicación de Xamarin.Forms.

1. **Páginas**: las páginas de Xamarin.Forms representan pantallas de aplicaciones móviles multiplataforma. La aplicación de Phoneword usa la clase [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) para mostrar una pantalla única. Para obtener más información sobre las páginas, consulte [Xamarin.Forms Pages](~/xamarin-forms/user-interface/controls/pages.md) (Páginas de Xamarin.Forms).
1. **Diseños**: los diseños de Xamarin.Forms son contenedores que se usan para crear vistas en estructuras lógicas. La aplicación de Phoneword usa la clase [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) para organizar controles en una pila horizontal. Para obtener más información sobre los diseños, consulte [Xamarin.Forms Layouts](~/xamarin-forms/user-interface/controls/layouts.md) (Diseños de Xamarin.Forms).
1. **Vistas**: las vistas de Xamarin.Forms son los controles que se muestran en la interfaz de usuario, como etiquetas, botones y cuadros de entrada de texto. La aplicación de Phoneword usa los controles [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/), [`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) y [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/). Para obtener más información sobre las vistas, consulte [Xamarin.Forms Views](~/xamarin-forms/user-interface/controls/views.md) (Vistas de Xamarin.Forms).
1. **Celdas**: las celdas de Xamarin.Forms son elementos especializados que se usan para los elementos de una lista, y describen cómo debe dibujarse cada elemento de una lista. La aplicación de Phoneword no usa ninguna celda. Para obtener más información sobre las celdas, consulte [Xamarin.Forms Cells](~/xamarin-forms/user-interface/controls/cells.md) (Celdas de Xamarin.Forms).

En tiempo de ejecución, cada control se asignará a su equivalente nativo, que es lo que se representará.

Cuando la aplicación de Phoneword se ejecuta en cualquier plataforma, muestra una pantalla única que corresponde a una [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) en Xamarin.Forms. Una `Page` representa un *Grupo de vista* en Android, un *Controlador de vista* en iOS o una *Página* en la plataforma universal de Windows. La aplicación de Phoneword también crea instancias de un objeto [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) que representa la clase `MainPage`, cuyo marcado XAML se muestra en el siguiente ejemplo de código:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                         xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                         x:Class="Phoneword.MainPage">
        ...
        <StackLayout>
            <Label Text="Enter a Phoneword:" />
            <Entry x:Name="phoneNumberText" Text="1-855-XAMARIN" />
            <Button x:Name="translateButon" Text="Translate" Clicked="OnTranslate" />
            <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
        </StackLayout>
</ContentPage>
```

La clase `MainPage` usa un control [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) para organizar automáticamente los controles de la pantalla, independientemente del tamaño de esta. Cada elemento secundario se coloca uno detrás del otro, verticalmente en el orden en el que se ha agregado. El control `StackLayout` contiene un control [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) para mostrar texto en la página, un control [`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) para aceptar la entrada del usuario textual y dos controles [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) para ejecutar código en respuesta a los eventos de entrada táctil.

Para obtener más información sobre XAML en Xamarin.Forms, consulte [Xamarin.Forms XAML Basics (Datos básicos de XAML en Xamarin.Forms)](~/xamarin-forms/xaml/xaml-basics/index.md).

### <a name="responding-to-user-interaction"></a>Responder a la interacción del usuario

Un objeto que se ha definido en XAML puede desencadenar un evento que se controla en el archivo de código subyacente. En el siguiente ejemplo de código se muestra el método `OnTranslate` en el código subyacente de la clase `MainPage`, que se ejecuta en respuesta al evento [`Clicked`](https://developer.xamarin.com/api/event/Xamarin.Forms.Button.Clicked/) que se desencadena en el botón *Traducir*.

```csharp
void OnTranslate(object sender, EventArgs e)
{
    translatedNumber = Core.PhonewordTranslator.ToNumber (phoneNumberText.Text);
    if (!string.IsNullOrWhiteSpace (translatedNumber)) {
        callButton.IsEnabled = true;
        callButton.Text = "Call " + translatedNumber;
    } else {
        callButton.IsEnabled = false;
        callButton.Text = "Call";
    }
}
```

El método `OnTranslate` traduce los números con letras en su correspondiente número de teléfono y, en respuesta, establece las propiedades del botón de llamada. El archivo de código subyacente de una clase XAML puede tener acceso a un objeto que se ha definido en XAML con el nombre asignado a él con el atributo `x:Name`. El valor que se ha asignado a este atributo tiene las mismas reglas que las variables de C#, ya que debe comenzar con una letra o guion bajo y no contener espacios incrustados.

El cableado del botón de traducción en el método `OnTranslate` se produce en el marcado XAML de la clase `MainPage`:

```xaml
<Button x:Name="translateButon" Text="Translate" Clicked="OnTranslate" />
```

## <a name="additional-concepts-introduced-in-phoneword"></a>Otros conceptos presentados en Phoneword

La aplicación de Phoneword para Xamarin.Forms ha presentado varios conceptos que no se tratan en este artículo. Entre estos conceptos se incluyen los siguientes:

- Habilitar y deshabilitar botones. Un [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) puede activarse o desactivarse cambiando su propiedad [`IsEnabled`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsEnabled/). Por ejemplo, el siguiente ejemplo de código deshabilita el elemento `callButton`:

    ```csharp
    callButton.IsEnabled = false;
    ```

- Mostrar un cuadro de diálogo de alerta. Cuando el usuario pulsa el **botón** de llamada, en la aplicación de Phoneword se muestra un *cuadro de diálogo de alerta* con la opción de realizar o cancelar una llamada. El método [`DisplayAlert`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/System.String/) se usa para crear el cuadro de diálogo, como se muestra en el siguiente ejemplo de código:

    ```csharp
    await this.DisplayAlert (
            "Dial a Number",
            "Would you like to call " + translatedNumber + "?",
            "Yes",
            "No");
    ```

- Acceso a características nativas a través de la clase [`DependencyService`](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/). La aplicación de Phoneword usa la clase `DependencyService` para resolver la interfaz `IDialer` en implementaciones de marcado de teléfono específicas de la plataforma, como se muestra en el siguiente ejemplo de código del proyecto de Phoneword:

    ```csharp
    async void OnCall (object sender, EventArgs e)
    {
        ...
        var dialer = DependencyService.Get<IDialer> ();
        ...
    }
    ```

  Para obtener más información sobre la clase [`DependencyService`](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/), consulte [Acceso a características nativas a través de DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

- Realizar una llamada de teléfono con una dirección URL. La aplicación de Phoneword usa `OpenURL` para iniciar la aplicación de teléfono del sistema. La dirección URL consta de un prefijo `tel:` seguido del número de teléfono al que se va a llamar, como se muestra en el siguiente ejemplo de código del proyecto de iOS:

    ```csharp
    return UIApplication.SharedApplication.OpenUrl (new NSUrl ("tel:" + number));
    ```

- Ajustar el diseño de la plataforma. La clase [`Device`](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/) permite a los desarrolladores personalizar el diseño de la aplicación y la funcionalidad de acuerdo con la plataforma, como se muestra en el siguiente ejemplo de código que usa valores [`Padding`](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) diferentes en distintas plataformas para mostrar correctamente cada página:

    ```xaml
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms" ... >
        <ContentPage.Padding>
            <OnPlatform x:TypeArguments="Thickness">
                <On Platform="iOS" Value="20, 40, 20, 20" />
                <On Platform="Android, UWP" Value="20" />
            </OnPlatform>
        </ContentPage.Padding>
        ...
    </ContentPage>
    ```

  Para obtener más información sobre los ajustes de la plataforma, consulte [Clase Device](~/xamarin-forms/platform/device.md).

## <a name="testing-and-deployment"></a>Prueba e implementación

Tanto Visual Studio para Mac como Visual Studio ofrecen numerosas opciones para probar e implementar una aplicación. Depurar aplicaciones es una parte común del ciclo de vida del desarrollo de la aplicación y ayuda a diagnosticar problemas de código. Para obtener más información, consulte [Set a Breakpoint (Establecer un punto de interrupción)](https://developer.xamarin.com/recipes/cross-platform/ide/debugging/set_a_breakpoint/), [Step Through Code (Recorrer el código paso a paso)](https://developer.xamarin.com/recipes/cross-platform/ide/debugging/step_through_code/) y [Output Information to the Log Window (Información de salida para la ventana Registro)](https://developer.xamarin.com/recipes/cross-platform/ide/debugging/output_information_to_log_window/).

Los simuladores son un buen lugar para comenzar a implementar y probar una aplicación, y cuentan con una funcionalidad que resulta útil a la hora de probar las aplicaciones. Sin embargo, los usuarios no usarán la aplicación final en un simulador, por lo que las aplicaciones deben probarse en dispositivos reales desde el primer momento y con frecuencia. Para obtener más información sobre el aprovisionamiento de dispositivos de iOS, consulte [Aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md). Para obtener más información sobre el aprovisionamiento de dispositivos de Android, consulte [Configurar el dispositivo para el desarrollo](~/android/get-started/installation/set-up-device-for-development.md).

## <a name="summary"></a>Resumen

En este artículo se han examinado los aspectos fundamentales del desarrollo de aplicaciones con Xamarin.Forms. Los temas que se han tratado incluyen la anatomía de una aplicación de Xamarin.Forms, la arquitectura y los elementos fundamentales, y la interfaz de usuario.

En la próxima sección de esta guía, la aplicación se extenderá para incluir varias pantallas a fin de explorar conceptos y arquitecturas de Xamarin.Forms más avanzados.
