---
title: Proyectos de instalación de Windows
description: Adición de nuevos proyectos de Windows a una solución de Xamarin.Forms existente
ms.prod: xamarin
ms.assetid: A0774D2E-6994-4D91-84E8-DAB66FC92320
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/16/2016
ms.openlocfilehash: 071239abc71a41798a240128f8339687026296f9
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/27/2018
---
# <a name="setup-windows-projects"></a>Proyectos de instalación de Windows

_Adición de nuevos proyectos de Windows a una solución de Xamarin.Forms existente_

Las soluciones anteriores dejen Xamarin.Forms (o las creadas en macOS) no tendrán proyectos de aplicaciones de plataforma Universal de Windows (UWP). Por lo tanto, debe agregar manualmente un proyecto UWP para compilar una aplicación de Windows 10 (UWP).

<a name="pcl" />

## <a name="update-the-pcl-profile"></a>Actualizar el perfil PCL

Si la aplicación existente de Xamarin.Forms utiliza la plantilla de biblioteca de clases portables (PCL), debe actualizar su perfil.

1. **Haga clic en > propiedades** (puede diferir la configuración existente)

  ![](images/targets.png "Destinos PCL")

2. Haga clic en el **cambio...**  botón

3. Asegúrese del **Windows 8** y **Windows Phone 8.1** opciones están seleccionadas (y **Windows Phone Silveright** es *anular la selección*):

  ![](images/pcl.png "Opciones de destino PCL")

4. Presione **Aceptar** y guarde los cambios.

Esto equivale a **perfil 111** si va a configurar su PCL en Visual Studio para Mac mediante la lista desplegable.

  ![](images/pcl-xs.png "Perfil PCL 111")

## <a name="add-a-universal-windows-platform-app"></a>Agregar un Windows Universal aplicación de la plataforma

Deberá ejecutar **2017 de Visual Studio** en **Windows 10** para compilar aplicaciones UWP. Para obtener más información acerca de la plataforma Universal de Windows, vea [Introducción a la plataforma Universal de Windows](/windows/uwp/get-started/universal-application-platform-guide/).

UWP está disponible en Xamarin.Forms 2.1 y versiones posteriores, y se admite Xamarin.Forms.Maps en Xamarin.Forms 2.2 y versiones posteriores.

Compruebe el <a href="#troubleshooting">solución de problemas</a> sección para ver sugerencias útiles.

Siga estas instrucciones para agregar una aplicación para UWP que se ejecutará en teléfonos, tabletas y equipos de escritorio de Windows 10:

 1. Haga doble clic en la solución y seleccione **Agregar > Nuevo proyecto...**  y agregue un **aplicación vacía (Windows Universal)** proyecto:

  ![](universal-images/add-wu.png "Agregar cuadro de diálogo nuevo proyecto")

 2. En el **nuevo proyecto de plataforma Universal de Windows** cuadro de diálogo, seleccione las versiones de mínimo y de destino de Windows 10 que la aplicación se ejecutará en:

  ![](universal-images/target-version.png "Nuevo cuadro de diálogo de proyecto de plataforma Universal de Windows")

 3. Haga doble clic en el proyecto de UWP y seleccione **administrar paquetes de NuGet...**  y agregue el **Xamarin.Forms** paquete. Asegúrese de que los otros proyectos de la solución también se actualizan a la misma versión del paquete de Xamarin.Forms.

 4. Asegúrese de que el nuevo proyecto UWP se compilará el **generar > Configuration Manager** ventana (Esto probablemente no habrá realizado de forma predeterminada). Marca el **generar** y **implementar** cuadros para el proyecto Universal:

  [![](universal-images/configuration-sml.png "Ventana del Administrador de configuración")](universal-images/configuration.png#lightbox "ventana del Administrador de configuración")

 5. Haga doble clic en el proyecto y seleccione **Agregar > referencia** y crear una referencia al proyecto de aplicación Xamarin.Forms (PCL, estándar de .NET o proyecto compartido).

  ![](universal-images/addref-sml.png "Cuadro de diálogo Administrador de referencias")

 6. En el proyecto UWP, editar **App.xaml.cs** para incluir el `Init` llamada a un método dentro de la `OnLaunched` método alrededor de la línea 52:

```csharp
// under this line
rootFrame.NavigationFailed += OnNavigationFailed;
// add this line
Xamarin.Forms.Forms.Init (e); // requires the `e` parameter
```

 7. En el proyecto UWP, editar **MainPage.xaml** quitando el `Grid` dentro de la `Page` elemento.

 8. En **MainPage.xaml**, agregue un nuevo `xmlns` entrada para `Xamarin.Forms.Platform.UWP`:

```csharp
xmlns:forms="using:Xamarin.Forms.Platform.UWP"
```

 9. En **MainPage.xaml**, cambiar la raíz `<Page` elemento a `<forms:WindowsPage`:

```xaml
<forms:WindowsPage
...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
...
</forms:WindowsPage>
```

 10. En el proyecto UWP, editar **MainPage.xaml.cs** para quitar el `: Page` especificador de herencia para el nombre de clase (ya que ahora heredará de `WindowsPage` debido a los cambios realizados en el paso anterior):

```csharp
public sealed partial class MainPage  // REMOVE ": Page"
```

 11. En **MainPage.xaml.cs**, agregue el `LoadApplication` llamar a en el `MainPage` constructor al que se inicie la aplicación Xamarin.Forms:

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

12. Agregue los recursos locales (p. ej. archivos de imagen) de los proyectos existentes de plataforma que son necesarios.

## <a name="troubleshooting"></a>Solución de problemas

<a name="target-invocation-exception" />

### <a name="target-invocation-exception-when-using-compile-with-net-native-tool-chain"></a>"Excepción de invocación de destino" cuando se usa "Compilar con cadena de herramientas nativo. NET"

Si su aplicación para UWP hace referencia a varios ensamblados (por ejemplo un control de terceros bibliotecas, o su propia aplicación se divide en varias bibliotecas), Xamarin.Forms posible que no pueda cargar los objetos de los ensamblados (como representadores personalizados).

Esto puede ocurrir cuando se usa el **compilar con cadena de herramientas .NET Native** que es una opción para aplicaciones UWP en la **Propiedades > compilar > General** ventana para el proyecto.

Puede solucionar este problema mediante el uso de una sobrecarga específica de UWP de la `Forms.Init` llamar a en **App.xaml.cs** tal como se muestra en el código siguiente (debe reemplazar `ClassInOtherAssembly` con una clase real hace referencia el código):

```csharp
// You'll need to add `using System.Reflection;`
List<Assembly> assembliesToInclude = new List<Assembly>();

// Now, add in all the assemblies your app uses
assembliesToInclude.Add(typeof (ClassInOtherAssembly).GetTypeInfo().Assembly);

// Also do this for all your other 3rd party libraries
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// replaces Xamarin.Forms.Forms.Init(e);
```

Agregue una referencia a cada ensamblado al que se hace referencia a la aplicación.

#### <a name="dependency-services-and-net-native-compilation"></a>Servicios de dependencia y .NET compilación nativa

Versiones de lanzamiento con .NET Native puede producirse un error de compilación para resolver los servicios de dependencia que se definen fuera el ejecutable principal de la aplicación (como en un proyecto independiente o una biblioteca).

Use la `DependencyService.Register<T>()` método para registrar manualmente las clases del servicio de dependencia. Según el ejemplo anterior, agregue el método register similar al siguiente:

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
Xamarin.Forms.DependencyService.Register<ClassInOtherAssembly>(); // add this
```
