---
title: ¿Cómo se automatiza un proyecto de prueba de NUnit Android?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/29/2018
ms.openlocfilehash: f63a25f36682038b7fcd85d711d980b9e3ec869d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763776"
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>¿Cómo se automatiza un proyecto de prueba de NUnit Android?

> [!NOTE]
> Esta guía explica cómo automatizar un proyecto de prueba de NUnit Android, no en un proyecto Xamarin.UITest. Encontrará guías Xamarin.UITest [aquí](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

Cuando se crea un **aplicación de prueba de unidad (Android)** proyecto en Visual Studio (o **Android prueba unitaria** proyecto en Visual Studio para Mac), este proyecto se realizará automáticamente las pruebas de forma predeterminada.
Para ejecutar pruebas de NUnit en un dispositivo de destino, puede crear un [Android.App.Instrumentation](https://developer.xamarin.com/api/type/Android.App.Instrumentation/) subclase que se inició mediante el comando siguiente: 

```shell
adb shell am instrument 
```

Los pasos siguientes explica este proceso:

1.  Crear un nuevo archivo denominado **TestInstrumentation.cs**: 

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
    En este archivo, [Xamarin.Android.NUnitLite.TestSuiteInstrumentation](https://developer.xamarin.com/api/type/Xamarin.Android.NUnitLite.TestSuiteInstrumentation/) (de **Xamarin.Android.NUnitLite.dll**) es una subclase para crear `TestInstrumentation`.

2.  Implemente el [TestInstrumentation](https://developer.xamarin.com/api/constructor/Xamarin.Android.NUnitLite.TestSuiteInstrumentation.TestSuiteInstrumentation/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) constructor y el [AddTests](https://developer.xamarin.com/api/member/Xamarin.Android.NUnitLite.TestSuiteInstrumentation.AddTests%28%29) método. El `AddTests` método controla qué pruebas se ejecutan realmente.

3.  Modificar el `.csproj` archivo que desea agregar **TestInstrumentation.cs**. Por ejemplo:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Project DefaultTargets="Build" ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
        ...
        <ItemGroup>
            <Compile Include="TestInstrumentation.cs" />
        </ItemGroup>
        <Target Name="RunTests" DependsOnTargets="_ValidateAndroidPackageProperties">
            <Exec Command="&quot;$(_AndroidPlatformToolsDirectory)adb&quot; $(AdbTarget) $(AdbOptions) shell am instrument -w $(_AndroidPackage)/app.tests.TestInstrumentation" />
        </Target>
        ...
    </Project>
    ```

3.  Utilice el siguiente comando para ejecutar las pruebas unitarias. Reemplace `PACKAGE_NAME` con el nombre del paquete de la aplicación (el nombre del paquete puede encontrarse en la aplicación `/manifest/@package` atributo ubicado en **AndroidManifest.xml**):

    ```shell
    adb shell am instrument -w PACKAGE_NAME/app.tests.TestInstrumentation
    ```

4.  Si lo desea, puede modificar el `.csproj` archivo que desea agregar el `RunTests` destino de MSBuild. Esto permite invocar las pruebas unitarias con un comando similar al siguiente:

    ```shell
    msbuild /t:RunTests Project.csproj
    ```
    (Tenga en cuenta que no es necesario usar este nuevo destino; el anterior `adb` comando se puede usar en lugar de `msbuild`.)

Para obtener más información sobre el uso de la `adb shell am instrument` comando para ejecutar pruebas unitarias, vea el desarrollador Android [ejecutar pruebas con ADB](https://developer.android.com/studio/test/command-line.html#RunTestsDevice) tema.


> [!NOTE]
> Con el [Xamarin.Android 5.0](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming) de la versión, los nombres de paquete predeterminado para Android contenedores CCW se basará la MD5SUM del nombre completo de ensamblado del tipo que se va a exportar. Esto permite que el mismo nombre completo que se proporcionará en ensamblados diferentes y no recibe un error de empaquetado. Por lo tanto, asegúrese de que utiliza el `Name` propiedad en el `Instrumentation` atributo para generar un nombre legible de ACW o la clase.

_El nombre ACW debe usarse en el `adb` comando anterior_.
El cambio de nombre/refactorización de la clase de C#, por tanto, será necesario modificar el `RunTests` comando para usar el nombre ACW correcto.

