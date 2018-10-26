---
title: Proyectos de instalación de Windows
description: Las soluciones de Xamarin.Forms anteriores (o aquellos creados en macOS) no tendrán los proyectos de plataforma Universal de Windows y, por lo que este artículo explica cómo agregar un nuevo proyecto UWP a una solución existente de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: A0774D2E-6994-4D91-84E8-DAB66FC92320
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/10/2018
ms.openlocfilehash: fb166b69c76ca4c87746358258d97f1cb81cb301
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123237"
---
# <a name="setup-windows-projects"></a>Proyectos de instalación de Windows

_Adición de nuevos proyectos de Windows a una solución existente de Xamarin.Forms_

Las soluciones de Xamarin.Forms anteriores (o aquellos creados en macOS) no tendrán los proyectos de aplicaciones de plataforma Universal de Windows (UWP). Por lo tanto, deberá agregar manualmente un proyecto UWP para compilar una aplicación de Windows 10 (UWP).

## <a name="add-a-universal-windows-platform-app"></a>Agregar un Universal Windows Platform app

Debe ejecutar **Visual Studio 2017** en **Windows 10** para compilar aplicaciones para UWP. Para obtener más información acerca de la plataforma Universal de Windows, consulte [Introducción a la plataforma Universal de Windows](/windows/uwp/get-started/universal-application-platform-guide/).

UWP está disponible en Xamarin.Forms 2.1 y versiones posteriores, y xamarin.Forms.Maps para que se admite en Xamarin.Forms 2.2 y versiones posteriores.

Compruebe el <a href="#troubleshooting">solución de problemas</a> sección para obtener sugerencias útiles.

Siga estas instrucciones para agregar una aplicación para UWP que se ejecutará en teléfonos, tabletas y equipos de escritorio de Windows 10:

 1. Haga doble clic en la solución y seleccione **Agregar > Nuevo proyecto...**  y agregue un **aplicación vacía (Windows Universal)** proyecto:

  ![](universal-images/add-wu.png "Agregar cuadro de diálogo nuevo proyecto")

 2. En el **nuevo proyecto de plataforma Universal de Windows** cuadro de diálogo, seleccione las versiones mínima y de destino de Windows 10 que se ejecutará la aplicación:

  ![](universal-images/target-version.png "Nuevo cuadro de diálogo de proyecto de plataforma Universal de Windows")

 3. Haga doble clic en el proyecto de UWP y seleccione **administrar paquetes NuGet...**  y agregue el **Xamarin.Forms** paquete. Asegúrese de que los otros proyectos de la solución también se actualizan a la misma versión del paquete de Xamarin.Forms.

 4. Asegúrese de que el nuevo proyecto UWP se compilará el **compilar > Administrador de configuración** ventana (Esto probablemente no habrá sucedido de forma predeterminada). Marca el **compilar** y **implementar** cuadros para el proyecto Universal:

  [![](universal-images/configuration-sml.png "Ventana del Administrador de configuración")](universal-images/configuration.png#lightbox "ventana del Administrador de configuración")

 5. Haga doble clic en el proyecto y seleccione **Agregar > referencia** y crear una referencia al proyecto de aplicación de Xamarin.Forms (.NET Standard o proyectos compartidos).

  ![](universal-images/addref-sml.png "Cuadro de diálogo Administrador de referencias")

 6. En el proyecto UWP, edite **App.xaml.cs** para incluir el `Init` llamada al método dentro de la `OnLaunched` método alrededor de la línea 52:

```csharp
// under this line
rootFrame.NavigationFailed += OnNavigationFailed;
// add this line
Xamarin.Forms.Forms.Init (e); // requires the `e` parameter
```

 7. En el proyecto UWP, edite **MainPage.xaml** quitando el `Grid` dentro de la `Page` elemento.

 8. En **MainPage.xaml**, agregue un nuevo `xmlns` entrada para `Xamarin.Forms.Platform.UWP`:

```csharp
xmlns:forms="using:Xamarin.Forms.Platform.UWP"
```

 9. En **MainPage.xaml**, cambiar la raíz `<Page` elemento `<forms:WindowsPage`:

```xaml
<forms:WindowsPage
...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
...
</forms:WindowsPage>
```

 10. En el proyecto UWP, edite **MainPage.xaml.cs** para quitar el `: Page` especificador de herencia para el nombre de clase (puesto que ahora se heredará desde `WindowsPage` debido a los cambios realizados en el paso anterior):

```csharp
public sealed partial class MainPage  // REMOVE ": Page"
```

 11. En **MainPage.xaml.cs**, agregue el `LoadApplication` llamar el `MainPage` constructor para iniciar la aplicación de Xamarin.Forms:

```csharp
// below this existing line
this.InitializeComponent();
// add this line
LoadApplication(new YOUR_NAMESPACE.App());
```

<!--
11 . Double-click **Package.appxmanifest** to set these capabilities
  that are often required:

  Capabilities set:

  * Internet (Client)
  * Location
-->

12. Agregue los recursos locales (p ej. archivos de imagen) de los proyectos existentes de plataforma que son necesarios.

## <a name="troubleshooting"></a>Solución de problemas

<a name="target-invocation-exception" />

### <a name="target-invocation-exception-when-using-compile-with-net-native-tool-chain"></a>"Excepción de invocación de destino" cuando se usa "Compilación con la cadena de herramientas nativas de .NET"

Si su aplicación para UWP se hace referencia a varios ensamblados (por ejemplo un control de terceros bibliotecas o su propia aplicación se divide en varias bibliotecas), no es posible que pueda Xamarin.Forms cargar los objetos de dichos ensamblados (como representadores personalizados).

Esto puede ocurrir cuando se usa el **compilar con cadena de herramientas .NET Native** que es una opción para aplicaciones UWP en el **Propiedades > compilar > General** ventana para el proyecto.

Puede solucionar este problema mediante una sobrecarga específica de UWP de la `Forms.Init` llamar **App.xaml.cs** tal como se muestra en el código siguiente (debe reemplazar `ClassInOtherAssembly` con una clase real hace referencia su código):

```csharp
// You'll need to add `using System.Reflection;`
List<Assembly> assembliesToInclude = new List<Assembly>();

// Now, add in all the assemblies your app uses
assembliesToInclude.Add(typeof (ClassInOtherAssembly).GetTypeInfo().Assembly);

// Also do this for all your other 3rd party libraries
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// replaces Xamarin.Forms.Forms.Init(e);
```

Agregue una entrada para cada ensamblado que ha agregado como una referencia en el Explorador de soluciones, ya sea a través de una referencia directa o NuGet.

#### <a name="dependency-services-and-net-native-compilation"></a>Compilación nativa de .NET y de servicios de dependencia

Versiones de lanzamiento con .NET Native puede producir un error de compilación para resolver los servicios de dependencia que se definen fuera el ejecutable de aplicación principal (como en un proyecto independiente o biblioteca).

Use el `DependencyService.Register<T>()` método para registrar manualmente clases del servicio de dependencia. Según el ejemplo anterior, agregue el método register similar al siguiente:

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
Xamarin.Forms.DependencyService.Register<ClassInOtherAssembly>(); // add this
```
