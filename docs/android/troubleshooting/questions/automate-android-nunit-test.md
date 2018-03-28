---
title: ¿Cómo se automatiza un proyecto de prueba de NUnit Android?
ms.topic: article
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: acb213e8c73013bc9b2482afb45296c4e1f61ab5
ms.sourcegitcommit: 20ca85ff638dbe3a85e601b5eb09b2f95bda2807
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>¿Cómo se automatiza un proyecto de prueba de NUnit Android?

> [!NOTE]
> Esta guía describen los pasos para configurar un proyecto de prueba de NUnit Android, no en un proyecto Xamarin.UITest. Encontrará guías Xamarin.UITest [aquí](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

Cuando se crea un proyecto de prueba unitaria Android en Visual Studio para Mac o una aplicación de prueba de unidad (Android) en Visual Studio, de forma predeterminada no ejecutará automáticamente las pruebas.
Para ejecutar pruebas de NUnit en un dispositivo de destino, usamos un `Android.App.Instrumentation` subclase, que se pueden crear y ejecutar mediante la `adb shell am instrument` comando.

En primer lugar, creamos la **TestInstrumentation.cs** archivo, que crea una subclase de `Xamarin.Android.NUnitLite.TestSuiteInstrumentation` (declarado en `Xamarin.Android.NUnitLite.dll`). El `TestInstrumentation(IntPtr, JniHandleOwnership)` constructor _debe_ proporcionarse y virtual `AddTests()` método debe invalidarse.
`AddTests()` controla qué pruebas se ejecutan realmente. Este archivo es reutilizable en gran medida.

Después, el `.csproj` debe modificarse para agregar `TestInstrumentation.cs`.

Si lo desea, el `.csproj` puede modificarse para agregar el `RunTests` destino de MSBuild, que permitiría invocar las pruebas unitarias como:

```shell
msbuild /t:RunTests Project.csproj
```

Uso de un nuevo destino no es necesario; el comando adb correspondiente puede utilizarse en su lugar:

```shell
adb shell am instrument -w @PACKAGE_NAME@/app.tests.TestInstrumentation
```

Reemplace `@PACKAGE\_NAME@` según corresponda; es el valor presente en el **AndroidManifest.xml** `/manifest/@package` atributo.


> [!NOTE]
> *Importante*: con la [Xamarin.Android 5.0](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming) de la versión, los nombres de paquete predeterminado para Android contenedores CCW se basará la MD5SUM del nombre completo de ensamblado del tipo que se va a exportar. Esto permite que el mismo nombre completo que se proporcionará en ensamblados diferentes y no recibe un error de empaquetado. Por lo tanto, asegúrese de que utiliza el \`nombre\` propiedad en el \`instrumentación\` encuentra para generar un nombre legible de ACW o la clase.

_El nombre ACW debe usarse en el `adb` comando_. El cambio de nombre/refactorización de la clase de C#, por tanto, será necesario modificar el `RunTests` comando para usar el nombre ACW correcto.

Adiciones al archivo .csproj:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Project DefaultTargets="Build" ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <ItemGroup>
        <Compile Include="TestInstrumentation.cs" />
    </ItemGroup>
    <Target Name="RunTests" DependsOnTargets="_ValidateAndroidPackageProperties">
        <Exec Command="&quot;$(_AndroidPlatformToolsDirectory)adb&quot; $(AdbTarget) $(AdbOptions) shell am instrument -w $(_AndroidPackage)/app.tests.TestInstrumentation" />
    </Target>
</Project>
```

**TestInstruments.cs**:

```cs 
using System;
using System.Reflection;
 
using Android.App;
using Android.Content;
using Android.Runtime;
 
using Xamarin.Android.NUnitLite;
 
namespace App.Tests {
 
    [Instrumentation(Name="app.tests.TestInstrumentation")]
    public class TestInstrumentation : TestSuiteInstrumentation {
 
        public TestInstrumentation (IntPtr handle, JniHandleOwnership transfer) : base (handle, transfer)
        {
        }
 
        protected override void AddTests ()
        {
            AddTest (Assembly.GetExecutingAssembly ());
        }
    }
}
```

