---
ms.assetid: EA2D979E-9151-4CE9-9289-13B6A979838B
title: Uso de bibliotecas de C o C++ con Xamarin
description: Visual Studio para Mac puede utilizarse para crear e integrar código de C o C++ multiplataforma en aplicaciones móviles para iOS y Android con Xamarin y C#. En este artículo se explica cómo configurar y depurar un proyecto de C++ en una aplicación Xamarin.
author: mikeparker104
ms.author: miparker
ms.date: 12/17/2018
ms.openlocfilehash: a235a24d544e938d4bf29e6569564aface2f6972
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2019
ms.locfileid: "58866389"
---
# <a name="use-cc-libraries-with-xamarin"></a>Uso de bibliotecas de C o C++ con Xamarin

## <a name="overview"></a>Información general

Xamarin permite a los desarrolladores crear aplicaciones móviles nativas de desarrollo multiplataforma con Visual Studio. Por lo general, C# enlaces se utilizan para exponer componentes existentes de la plataforma para los desarrolladores. Sin embargo, hay veces cuando las bases de código Xamarin que las aplicaciones necesitan para trabajar con los existentes. A veces los equipos que no tienen el tiempo, presupuesto o recursos al puerto una grande, bien probado y altamente optimizada codebase a C#.

[Visual C++ para desarrollo móvil multiplataforma](https://docs.microsoft.com/visualstudio/cross-platform/visual-cpp-for-cross-platform-mobile-development) permite la C/C++ y C# código que se crea como parte de la misma solución que ofrece muchas ventajas, incluida una experiencia de depuración unificada. Microsoft ha utilizado en este modo para entregar aplicaciones, como C/C ++ y Xamarin [Hyperlapse Mobile](https://www.microsoft.com/p/hyperlapse-mobile/9wzdncrd1prw) y [Pix cámara](https://www.microsoft.com/microsoftpix).

Sin embargo, en algunos casos hay un deseo (o requisito) para mantener las herramientas de C o C++ y los procesos en su lugar y para mantener el código de biblioteca que se separa de la aplicación, se trata de la biblioteca como si fuese similar a un componente de terceros existentes. En estas situaciones, el desafío no sólo expone los miembros pertinentes a C# , pero la administración de la biblioteca como una dependencia. Y, por supuesto, automatizar gran parte de este proceso como sea posible.  

Esta entrada describe un enfoque de alto nivel para este escenario y le guía a través de un ejemplo sencillo.

## <a name="background"></a>Fondo

C o C++ se considera un lenguaje de desarrollo multiplataforma, pero debe tener mucho cuidado para asegurarse de que el código fuente es efectivamente entre plataformas, mediante C o C++ compatible con todos los compiladores de destino y que contiene poca o ninguna plataforma incluidos condicionalmente solo o código específico del compilador.

En última instancia, el código debe compilar y ejecutar correctamente en todas las plataformas de destino, por tanto, esto se reduce a la similitud entre las plataformas (y compiladores) usando como objetivo. Todavía pueden surgir problemas de ligeras diferencias entre los compiladores y así realizar pruebas exhaustivas (preferiblemente automatizada) en cada destino de plataforma se vuelve cada vez más importante.  

## <a name="high-level-approach"></a>Enfoque de alto nivel

La ilustración siguiente representa el enfoque de cuatro fases usado para transformar el código fuente de C o C++ en una biblioteca de Xamarin multiplataforma que se comparte a través de NuGet y, a continuación, se usan en una aplicación de Xamarin.Forms.
 

![Enfoque de alto nivel para el uso de C o C++ con Xamarin](images/cpp-steps.jpg)

Las 4 fases son:

1.  Compilar el código fuente de C o C++ en bibliotecas nativas específicas de la plataforma.
2.  Ajuste las bibliotecas nativas con una solución de Visual Studio.
3.  El empaquetado y la inserción de un paquete de NuGet para el contenedor. NET.
4.  Consumiendo el paquete NuGet desde una aplicación de Xamarin.

### <a name="stage-1-compiling-the-cc-source-code-into-platform-specific-native-libraries"></a>Fase 1: Compilar el código fuente de C o C++ en bibliotecas nativas específicas de la plataforma

El objetivo de esta fase es crear bibliotecas nativas que pueden llamarse mediante el C# contenedor. Esto puede o no puede ser relevante según su situación. Las herramientas y procesos que se pueden poner a tener en este escenario habitual de muchos están fuera del ámbito de este artículo. Las consideraciones clave son mantener el C o C++ codebase en sincronización con cualquier código nativo de contenedor, suficientes pruebas unitarias, y automatización de la compilación. 

Las bibliotecas en el tutorial se crearon con Visual Studio Code con un script de shell que lo acompaña. Una versión extendida de este tutorial puede encontrarse en el [repositorio de GitHub de CAT Mobile](https://github.com/xamarin/mobcat/blob/dev/samples/cppwithxamarin/README.md) que explica esta parte del ejemplo en mayor profundidad. Las bibliotecas nativas se que se tratan como una dependencia de terceros en este caso sin embargo, este escenario se muestra para el contexto.


Por motivos de simplicidad, el tutorial dirige a solo un subconjunto de las arquitecturas. Para iOS, usa la utilidad lipo para crear una única fat binaria de los archivos binarios específicos de la arquitectura de individuales. Android usará los archivos binarios dinámicos con una extensión. SO y iOS usará binaria fat estática con una extensión .a. 

### <a name="stage-2-wrapping-the-native-libraries-with-a-visual-studio-solution"></a>Fase 2: Ajuste las bibliotecas nativas con una solución de Visual Studio

La siguiente fase consiste en ajustar las bibliotecas nativas para que se usan con facilidad desde. NET. Esto se hace con una solución de Visual Studio con cuatro proyectos. Un proyecto compartido contiene el código común. Los proyectos destinados a cada uno de Xamarin.Android, Xamarin.iOS y .NET Standard permiten a la biblioteca que se haga referencia de forma independiente de la plataforma.

Los usos de contenedor[el truco gancho](https://log.paulbetts.org/the-bait-and-switch-pcl-trick/),' descrito por Paul Betts. Esto no es la única manera, pero facilita la referencia a la biblioteca y evita la necesidad de administrar explícitamente implementaciones específicas de la plataforma en la misma aplicación que lo consume. El truco esencialmente es asegurarse de que los destinos (.NET Standard, Android, iOS) comparten el mismo espacio de nombres, el nombre del ensamblado y la estructura de clase. Dado que NuGet siempre preferirá una biblioteca específica de la plataforma, nunca se utiliza la versión de .NET Standard en tiempo de ejecución.

La mayoría del trabajo en este paso se centrará en mediante P/Invoke para llamar a los métodos de la biblioteca nativa y administrar las referencias a los objetos subyacentes. El objetivo es exponer la funcionalidad de la biblioteca al consumidor al abstraer cualquier complejidad. Los desarrolladores de Xamarin.Forms no es necesario tener conocimientos prácticos sobre el funcionamiento interno de la biblioteca no administrada. Debe sentir como cualquier otro administrados usan C# biblioteca.

En última instancia, el resultado de esta fase es un conjunto de bibliotecas. NET, uno por cada destino, junto con un documento de nuspec que contiene la información necesaria para crear el paquete en el paso siguiente.

**Fase 3: El empaquetado y la inserción de un paquete de NuGet para el contenedor de .NET**

La tercera fase es crear un paquete NuGet con los artefactos de compilación en el paso anterior. El resultado de este paso es un paquete de NuGet que se puede consumir desde una aplicación de Xamarin. El tutorial utiliza un directorio local para que actúe como la fuente de NuGet. En producción, este paso debe publicar un paquete para un público o fuente de NuGet privado y debería estar completamente automatizada.

**Fase 4: Consumir el paquete NuGet desde una aplicación de Xamarin.Forms**

El último paso es hacer referencia y utilizar el paquete de NuGet desde una aplicación de Xamarin.Forms. Esto requiere configurar el paquete NuGet de fuente en Visual Studio para usar la fuente definida en el paso anterior.

Una vez configurada la fuente, el paquete debe hacer referencia a cada proyecto de la aplicación de Xamarin.Forms multiplataforma. 'El truco bait-and-switch' proporciona interfaces idénticas, por lo que la funcionalidad de la biblioteca nativa se puede llamar mediante código definido en una sola ubicación.

El repositorio de código fuente contiene un [lista de lectura adicional](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin#wrapping-up) que incluye artículos sobre cómo configurar una fuente de DevOps de Azure privado de NuGet y cómo insertar el paquete en esa fuente. Mientras que requieren un poco más el tiempo de instalación de un directorio local, este tipo de fuente es mejor en un entorno de desarrollo de equipo.

## <a name="walk-through"></a>Ejemplo paso a paso

Los pasos proporcionados son específicos de **Visual Studio para Mac**, pero funciona de la estructura de **Visual Studio 2017** también.

### <a name="prerequisites"></a>Requisitos previos

Para seguir el tutorial, el desarrollador debe:

-   [Línea de comandos (CLI) de NuGet](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)

-   [*Visual Studio* *para Mac*](https://visualstudio.microsoft.com/downloads)

> [!NOTE]
> Cuando se activa [ **cuenta de desarrollador de Apple** ](https://developer.apple.com/) es necesario para implementar aplicaciones en un iPhone.

## <a name="creating-the-native-libraries-stage-1"></a>Creación de las bibliotecas nativas (fase 1)

La funcionalidad de la biblioteca nativa se basa en el ejemplo de [Tutorial: Crear y utilizar una biblioteca estática (C++)](https://docs.microsoft.com/cpp/windows/walkthrough-creating-and-using-a-static-library-cpp?view=vs-2017).

En este tutorial, se omite la primera fase, creación de las bibliotecas nativas, puesto que la biblioteca se proporciona como una dependencia de terceros en este escenario. Se incluyen las bibliotecas nativas precompiladas junto con el [código de ejemplo](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin) o puede ser [descargado](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin/Sample/Artefacts) directamente.

### <a name="working-with-the-native-library"></a>Trabajar con la biblioteca nativa

La versión original *MathFuncsLib* ejemplo incluye una sola clase denominada MyMathFuncs con la siguiente definición: 

```cpp
namespace MathFuncs
{
    class MyMathFuncs
    {
    public:
        double Add(double a, double b);
        double Subtract(double a, double b);
        double Multiply(double a, double b);
        double Divide(double a, double b);
    };
}
```

Una clase adicional define las funciones de contenedor que permiten un consumidor de .NET crear, eliminar e interactuar con la clase MyMathFuncs nativa subyacente.

```cpp
#include "MyMathFuncs.h"
using namespace MathFuncs;

extern "C" {
    MyMathFuncs* CreateMyMathFuncsClass();
    void DisposeMyMathFuncsClass(MyMathFuncs* ptr);
    double MyMathFuncsAdd(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsSubtract(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsMultiply(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsDivide(MyMathFuncs *ptr, double a, double b);
}
```

Será estas funciones de contenedor que se usan en el [Xamarin](https://visualstudio.microsoft.com/xamarin/) lado.

## <a name="wrapping-the-native-library-stage-2"></a>Ajuste la biblioteca nativa (fase 2)

Este escenario requiere la [precompilado bibliotecas](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin/Sample/Artefacts) se describe en el [sección anterior](https://docs.microsoft.com/xamarin/cross-platform/cpp/index).

### <a name="creating-the-visual-studio-solution"></a>Creación de la solución de Visual Studio

1. En **Visual Studio para Mac**, haga clic en **nuevo proyecto** (desde el *página de bienvenida*) o **nueva solución** (desde el *archivo* menú).
2. Desde el **nuevo proyecto** ventana, elija **proyecto compartido** (desde *Multiplatform > biblioteca*) y, a continuación, haga clic en **siguiente**.
3. Actualice los campos siguientes, a continuación, haga clic en **crear**:

    - **Nombre del proyecto:** MathFuncs.Shared  
    - **Nombre de la solución:** MathFuncs  
    - **Ubicación:** Use el valor predeterminado, ubicación de almacenamiento (o elegir una alternativa)   
    - **Crear un proyecto desde el directorio de la solución:** Establezca esta opción para comprobar
4. Desde **el Explorador de soluciones**, haga doble clic en el **MathFuncs.Shared** del proyecto y vaya a **configuración principal del**.
5. Quitar **. Compartido** desde el **Default Namespace** por lo que se establece en **MathFuncs** solo, a continuación, haga clic en **Aceptar**.
6. Abra **MyClass.cs** (creados por la plantilla), a continuación, cambie el nombre de la clase y el nombre de archivo **MyMathFuncsWrapper** y cambie el espacio de nombres **MathFuncs**.
7. **CONTROL + clic** en la solución **MathFuncs**, a continuación, elija **Agregar nuevo proyecto...**  desde el **agregar** menú.
8. Desde el **nuevo proyecto** ventana, elija **biblioteca .NET Standard** (desde *Multiplatform > biblioteca*) y, a continuación, haga clic en **siguiente**.
9. Elija **.NET Standard 2.0** y, a continuación, haga clic en **siguiente**.
10. Actualice los campos siguientes, a continuación, haga clic en **crear**:

    - **Nombre del proyecto:** MathFuncs.Standard  
    - **Ubicación:** Use la misma ubicación que el proyecto compartido de almacenamiento   

11. Desde **el Explorador de soluciones**, haga doble clic en el **MathFuncs.Standard** proyecto.
12. Vaya a **configuración principal del**, a continuación, actualice **Default Namespace** a **MathFuncs**.
13. Navegue hasta la **salida** configuración, a continuación, actualice **nombre del ensamblado** a **MathFuncs**.
14. Navegue hasta la **compilador** cambie la configuración, el **configuración** a **versión**, estableciendo **información de depuración** a  **Solo símbolos** , a continuación, haga clic en **Aceptar**.
15. Eliminar **Class1.cs/Getting iniciado** desde el proyecto (si uno de ellos se ha incluido como parte de la plantilla).
16. **CONTROL + clic** en el proyecto **dependencias/referencias** carpeta, a continuación, elija **editar referencias**.
17. Seleccione **MathFuncs.Shared** desde el **proyectos** y, después, haga clic en **Aceptar**.
18. Repita los pasos 7-17 (paso 9 y pasa por alto) mediante las siguientes configuraciones:

    | **NOMBRE DEL PROYECTO**  | **NOMBRE DE LA PLANTILLA**   | **MENÚ NUEVO PROYECTO**   |
    |-------------------| --------------------| -----------------------|
    | MathFuncs.Android | Biblioteca de clases       | Android > biblioteca      |
    | MathFuncs.iOS     | Biblioteca de enlace     | iOS > biblioteca          |

19. Desde **el Explorador de soluciones**, haga doble clic en el **MathFuncs.Android** del proyecto y, después, vaya a la **compilador** configuración.

20. Con el **configuración** establecido en **depurar**, editar **definir símbolos** para incluir **Android;**.

21. Cambiar el **configuración** a **versión**, a continuación, edite **definir símbolos** para incluir también **Android;**.

22. Repita los pasos 19 – 20 para **MathFuncs.iOS**, edición **definir símbolos** para incluir **iOS;** en lugar de **Android;** en ambos casos.

23. Compile la solución **versión** configuration (**CONTROL + comando + B**) y validar que todos los ensamblados de salida de tres (Android, iOS, .NET Standard) (en las carpetas de proyecto respectivos bin) comparten el mismo nombre **MathFuncs.dll**.

En esta fase, la solución debe tener tres destinos, uno para cada una de Android, iOS y .NET Standard y un proyecto compartido que se hace referencia a cada uno de los tres destinos. Se debe configurar para usar los mismos ensamblados de salida y el espacio de nombres predeterminado con el mismo nombre. Esto es necesario para el enfoque de "gancho" se ha mencionado anteriormente.

### <a name="adding-the-native-libraries"></a>Agregar las bibliotecas nativas

El proceso de agregar las bibliotecas nativas para la solución de contenedor varía ligeramente entre iOS y Android.

#### <a name="native-references-for-mathfuncsandroid"></a>Referencias nativas para MathFuncs.Android

1. **CONTROL + clic** en el **MathFuncs.Android** del proyecto y luego elija **nueva carpeta** desde el **agregar** menú asignándole **libs**.

2. Para cada **ABI** (interfaz binaria de aplicación), **CONTROL + clic** en el **libs** carpeta, a continuación, elija **nueva carpeta** desde el  **Agregar** menú, asígnele el nombre después de eso respectivos **ABI**. En este caso:

    - arm64-v8a
    - armeabi-v7a
    - x86
    - x86_64  

    > [!NOTE]
    > Para obtener información más detallada, consulte el [arquitecturas y CPU](https://developer.android.com/ndk/guides/arch) tema desde el [Guía del desarrollador NDK](https://developer.android.com/ndk/guides/), específicamente la sección sobre direccionamiento [código nativo en paquetes de aplicación](https://developer.android.com/ndk/guides/abis#native-code-in-app-packages) .

3. Compruebe la estructura de carpetas:  

    ```
    - lib
        - arm64-v8a
        - armeabi-v7a
        - x86
        - x86_64
    ```

4. Agregar las correspondientes **. SO** bibliotecas a cada uno de los **ABI** carpetas según la asignación siguiente:

    **arm64-v8a:** libs/Android/arm64

    **armeabi-v7a:** libs/Android/arm  

    **x86:** libs/Android/x86

    **x86_64:** libs/Android/x86_64

    > [!NOTE]
    > Para agregar archivos, **CONTROL + clic** en la carpeta que representa los respectivos **ABI**, a continuación, elija **agregar archivos...**  desde el **agregar** menú. Elija la biblioteca adecuada (desde el **PrecompiledLibs** directorio), a continuación, haga clic en **abierto** y, a continuación, haga clic en **Aceptar** dejar la opción predeterminada para *copia el archivo en el directorio*.

5. Para cada uno de los **.so** archivos, **CONTROL + clic** , a continuación, elija el **EmbeddedNativeLibrary** opción desde el **acción de compilación** menú.

Ahora el **libs** carpeta debería aparecer como sigue:

```bash
- lib
    - arm64-v8a
        - libMathFuncs.so
    - armeabi-v7a
        - libMathFuncs.so
    - x86 
        - libMathFuncs.so
    - x86_64 
        - libMathFuncs.so
```

#### <a name="native-references-for-mathfuncsios"></a>Referencias nativas para MathFuncs.iOS

1. **CONTROL + clic** en el **MathFuncs.iOS** del proyecto y luego elija **Agregar referencia nativa** desde el **agregar** menú. 
2. Elija la **libMathFuncs.a** biblioteca (desde bibliotecas/ios en el **PrecompiledLibs** directorio), a continuación, haga clic en **abierto** 
3. **CONTROL + clic** en el **libMathFuncs** archivo (dentro de la **las referencias nativas** carpeta, a continuación, elija el **propiedades** opción del menú  
4. Configurar la **referencia nativa** propiedades, por lo que se protegen (mostrando un icono de marca) en el **propiedades** panel:
        
    - Carga de fuerza
    - Es de C++
    - Conexión inteligente 

    > [!NOTE]
    > Uso de un tipo de proyecto de biblioteca de enlace junto con un [referencia nativa](https://docs.microsoft.com/xamarin/cross-platform/macios/native-references) incrusta la biblioteca estática y le permite que se vinculará automáticamente a la aplicación de Xamarin.iOS que hace referencia a él (incluso cuando se incluye a través de un paquete de NuGet). 

5. Abra **ApiDefinition.cs**, eliminando la plantilla de código comentado (lo que solo deja el **MathFuncs** espacio de nombres), a continuación, realizar el mismo paso para **Structs.cs** 

    > [!NOTE]
    > Un proyecto de biblioteca de enlace requiere estos archivos (con el *ObjCBindingApiDefinition* y *ObjCBindingCoreSource* acciones de compilación) con el fin de crear. Sin embargo, se escribirá el código para llamar a nuestra biblioteca nativa, fuera de estos archivos de forma que se pueden compartir entre los destinos de biblioteca de iOS y Android mediante P/Invoke estándar.

### <a name="writing-the-managed-library-code"></a>Escribir el código de biblioteca administrada

Ahora, escribir el C# código para llamar a la biblioteca nativa. El objetivo es ocultar cualquier complejidad subyacente. El consumidor no debería necesitar ningún conocimiento práctico de los aspectos internos de biblioteca nativa o de los conceptos de P/Invoke.  

#### <a name="creating-a-safehandle"></a>Creación de un SafeHandle

1. **CONTROL + clic** en el **MathFuncs.Shared** del proyecto y luego elija **Agregar archivo...**  desde el **agregar** menú. 
2. Elija **clase vacía** desde el **nuevo archivo** ventana, asígnele el nombre **MyMathFuncsSafeHandle** y, a continuación, haga clic en **nuevo**
3. Implemente el **MyMathFuncsSafeHandle** clase:

    ```csharp
    using System;
    using Microsoft.Win32.SafeHandles;

    namespace MathFuncs
    {
        internal class MyMathFuncsSafeHandle : SafeHandleZeroOrMinusOneIsInvalid
        {
            public MyMathFuncsSafeHandle() : base(true) { }

            public IntPtr Ptr => this.handle;

            protected override bool ReleaseHandle()
            {
                // TODO: Release the handle here
                return true;
            }
        }
    }
    ```

    > [!NOTE]
    > Un [SafeHandle](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.safehandle?view=netframework-4.7.2) es la mejor manera de trabajar con recursos no administrados en código administrado. Esto abstrae mucho código reutilizable relacionados con fundamental del ciclo de vida de objeto y de finalización. El propietario de este identificador puede posteriormente tratarlo como cualquier otro recurso administrado y no tendrá que implementar el completo [patrón desechable](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose). 

#### <a name="creating-the-internal-wrapper-class"></a>Creación de la clase de contenedor interna

1. Abra **MyMathFuncsWrapper.cs**, cambiar a una clase estática interna

    ```csharp
    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
        }
    }
    ```

2. En el mismo archivo, agregue la siguiente instrucción condicional a la clase:

    ```csharp
    #if Android
        const string DllName = "libMathFuncs.so";
    #else
        const string DllName = "__Internal";
    #endif
    ```

    > [!NOTE]
    > Esto establece la **DllName** valor constante en función de si se va a compilar la biblioteca para **Android** o **iOS**. Esto es para abordar las diferentes convenciones de nomenclatura utilizadas por cada plataforma respectiva, sino también del tipo de biblioteca que se va a usar en este caso. Android usa una biblioteca dinámica y por lo que espera un nombre de archivo incluye la extensión. Para iOS, '*__Internal*' es necesario puesto que estamos usando una biblioteca estática.

3. Agregue una referencia a **System.Runtime.InteropServices** en la parte superior de la **MyMathFuncsWrapper.cs** archivo

    ```csharp
    using System.Runtime.InteropServices;
    ```

4. Agregue los métodos de contenedor para controlar la creación y eliminación de la **MyMathFuncs** clase:

    ```csharp
    [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
    internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

    [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
    internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);
    ```

    > [!NOTE]
    > Pasamos la constante **DllName** a la **DllImport** atributo junto con el **EntryPoint** que explícitamente indica el tiempo de ejecución .NET el nombre de la función para llamar a dentro de esa biblioteca. Técnicamente, no necesitamos proporcionar el **EntryPoint** valor si los nombres de nuestro método administrado fueran idénticos a la no administrada. Si no se proporciona, se usará el nombre del método administrado como el **EntryPoint** en su lugar. Sin embargo, es mejor ser explícito.  

5. Agregue los métodos de contenedor para que podamos trabajar con el **MyMathFuncs** clase utilizando el código siguiente:

    ```csharp
    [DllImport(DllName, EntryPoint = "MyMathFuncsAdd")]
    internal static extern double Add(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsSubtract")]
    internal static extern double Subtract(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsMultiply")]
    internal static extern double Multiply(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsDivide")]
    internal static extern double Divide(MyMathFuncsSafeHandle ptr, double a, double b);
    ```

    > [!NOTE]
    > Vamos a usar tipos simples para los parámetros en este ejemplo. Dado que el cálculo de referencias es una copia bit a bit en este caso no requiere ningún trabajo adicional por nuestra parte. Observe también el uso de la **MyMathFuncsSafeHandle** clase en lugar del estándar **IntPtr**. El **IntPtr** se asignan automáticamente a la **SafeHandle** como parte del proceso de cálculo de referencias.

6. Compruebe que la termine **MyMathFuncsWrapper** clase aparece como a continuación:

    ```csharp
    using System.Runtime.InteropServices;

    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
            #if Android
                const string DllName = "libMathFuncs.so";
            #else
                const string DllName = "__Internal";
            #endif

            [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
            internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

            [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
            internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);

            [DllImport(DllName, EntryPoint = "MyMathFuncsAdd")]
            internal static extern double Add(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsSubtract")]
            internal static extern double Subtract(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsMultiply")]
            internal static extern double Multiply(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsDivide")]
            internal static extern double Divide(MyMathFuncsSafeHandle ptr, double a, double b);
        }
    }
    ```

#### <a name="completing-the-mymathfuncssafehandle-class"></a>Finalización de la clase MyMathFuncsSafeHandle
1. Abra el **MyMathFuncsSafeHandle** class, navegue hasta el marcador de posición **TODO** comentario dentro de la **ReleaseHandle** método:
    ```csharp
    // TODO: Release the handle here
    ```
2. Reemplace el **TODO** línea:

    ```csharp
    MyMathFuncsWrapper.DisposeMyMathFuncs(this);
    ```

#### <a name="writing-the-mymathfuncs-class"></a>Escribir la clase MyMathFuncs

Ahora que ha finalizado el contenedor, cree una clase de MyMathFuncs que administrará la referencia al objeto MyMathFuncs C++ no administrado.  

1. **CONTROL + clic** en el **MathFuncs.Shared** del proyecto y luego elija **Agregar archivo...**  desde el **agregar** menú. 
2. Elija **clase vacía** desde el **nuevo archivo** ventana, asígnele el nombre **MyMathFuncs** y, a continuación, haga clic en **nuevo**
3. Agregue los siguientes miembros para el **MyMathFuncs** clase:

    ```csharp
    readonly MyMathFuncsSafeHandle handle;
    ```

4. Implemente el constructor para la clase, por lo que crea y almacena un identificador nativo **MyMathFuncs** cuando se crea una instancia de la clase de objeto:

    ```csharp
    public MyMathFuncs()
    {
        handle = MyMathFuncsWrapper.CreateMyMathFuncs();
    }
    ```

5. Implemente el **IDisposable** interfaz con el código siguiente:

    ```csharp
    public class MyMathFuncs : IDisposable
    {
        ...

        protected virtual void Dispose(bool disposing)
        {
            if (handle != null && !handle.IsInvalid)
                handle.Dispose();
        }

        public void Dispose()
        {
            Dispose(true);
            GC.SuppressFinalize(this);
        }

        // ...
    }
    ```

6. Implemente el **MyMathFuncs** métodos mediante la **MyMathFuncsWrapper** clase para realizar el trabajo real en segundo plano al pasar el puntero nos hemos almacenado para el objeto no administrado subyacente. El código debería ser como sigue:

    ```csharp
    public double Add(double a, double b)
    {
        return MyMathFuncsWrapper.Add(handle, a, b);
    }

    public double Subtract(double a, double b)
    {
        return MyMathFuncsWrapper.Subtract(handle, a, b);
    }

    public double Multiply(double a, double b)
    {
        return MyMathFuncsWrapper.Multiply(handle, a, b);
    }

    public double Divide(double a, double b)
    {
        return MyMathFuncsWrapper.Divide(handle, a, b);
    }
    ```

#### <a name="creating-the-nuspec"></a>Crear el archivo nuspec

Para disponer de la biblioteca que se empaquetan y distribuyen a través de NuGet, la solución necesita una **nuspec** archivo. Esto identificará cuál de los ensamblados resultantes se incluirán para cada plataforma compatible.

1.  **CONTROL + clic** en la solución **MathFuncs**, a continuación, elija **Agregar carpeta de solución** desde el **agregar** menú asignándole **SolutionItems**.
2.  **CONTROL + clic** en el **SolutionItems** carpeta, a continuación, elija **nuevo archivo...**  desde el **agregar** menú.
3.  Elija **archivo XML vacío** desde el **nuevo archivo** ventana, asígnele el nombre **MathFuncs.nuspec** y, a continuación, haga clic en **New**.
4.  Actualización **MathFuncs.nuspec** con los metadatos del paquete básico que se mostrará a los **NuGet** consumidor. Por ejemplo:


    ```xml
    <?xml version="1.0"?>
    <package>
        <metadata>
            <id>MathFuncs</id>
            <version>$version$</version>
            <authors>Microsoft Mobile Customer Advisory Team</authors>
            <description>Sample C++ Wrapper Library</description>
            <requireLicenseAcceptance>false</requireLicenseAcceptance>
            <copyright>Copyright 2018</copyright>
        </metadata>
    </package>
    ```

    > [!NOTE]
    >  Consulte la [referencia de nuspec](https://docs.microsoft.com/nuget/reference/nuspec) documento para obtener información detallada sobre el esquema utilizado para este manifiesto.

5. Agregar un `<files>` como elemento secundario de la `<package>` elemento (justo debajo `<metadata>`), identificando cada archivo con otro `<file>` elemento:

    ```xml
    <files>

        <!-- Android -->

        <!-- iOS -->        

        <!-- netstandard2.0 -->

    </files>
    ```

    > [!NOTE]
    > Cuando se instala un paquete en un proyecto y donde hay varios ensamblados especificados por el mismo nombre, NuGet eficazmente elegirá el ensamblado que es más específico a la plataforma determinada.

6. Agregar el `<file>` elementos para el **Android** ensamblados:

    ```xml
    <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
    <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
    ```

7. Agregar el `<file>` elementos para el **iOS** ensamblados:

    ```xml
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
    ```

8. Agregar el `<file>` elementos para el **netstandard2.0** ensamblados:

    ```xml
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />
    ```

9. Compruebe el **nuspec** manifiesto:

    ```xml
    <?xml version="1.0"?>
    <package>
    <metadata>
        <id>MathFuncs</id>
        <version>$version$</version>
        <authors>Microsoft Mobile Customer Advisory Team</authors>
        <description>Sample C++ Wrapper Library</description>
        <requireLicenseAcceptance>false</requireLicenseAcceptance>
        <copyright>Copyright 2018</copyright>
    </metadata>
    <files>
    
        <!-- Android -->
        <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
        <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
        
        <!-- iOS -->
        <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
        <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
        
        <!-- netstandard2.0 -->
        <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
        <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />

    </files>
    </package>
    ```

    > [!NOTE]
    > Este archivo especifica las rutas de acceso de salida de ensamblado desde un **versión** de compilación, así que asegúrese de compilar la solución con esa configuración.

En este momento, la solución contiene 3 ensamblados .NET y apoyo **nuspec** manifiesto.

## <a name="distributing-the-net-wrapper-with-nuget"></a>Distribuir el contenedor de .NET con NuGet

El siguiente paso consiste en empaquetar y distribuir el paquete NuGet para ser consumido por la aplicación y administran como una dependencia de fácilmente. El ajuste y el consumo podrían realizar dentro de una única solución, pero la distribución de la biblioteca a través de NuGet ayuda a desacoplar y permite administrar estos códigos base por separado.

### <a name="preparing-a-local-packages-directory"></a>Preparación de un directorio local de paquetes

La forma más sencilla de fuente NuGet es un directorio local:

1.  En **buscador**, vaya a un directorio adecuado. Por ejemplo, **/Users**.
2.  Elija **nueva carpeta** desde el **archivo** menú, proporcionar un nombre significativo, como **fuente de nuget local**.

### <a name="creating-the-package"></a>Creación del paquete

1.  Establecer el **configuración de compilación** a **versión**y ejecutar una compilación con **comando + B**.
2.  Abra **Terminal** y cambie el directorio a la carpeta que contiene el **nuspec** archivo.
3.  En **Terminal**, ejecutar el **nuget pack** comando especificando el **nuspec** archivo, el **versión** (por ejemplo, 1.0.0) y el  **OutputDirectory** con la carpeta creada en el [paso anterior](https://docs.microsoft.com/xamarin/cross-platform/cpp/index#creating-a-local-nuget-feed), es decir, **fuente de nuget local**. Por ejemplo:

    ```bash
    nuget pack MathFuncs.nuspec -Version 1.0.0 -OutputDirectory ~/local-nuget-feed
    ```

4. **Confirmar** que **MathFuncs.1.0.0.nupkg** se ha creado en el **fuente de nuget local** directory.

### <a name="optional-using-a-private-nuget-feed-with-azure-devops"></a>[OPCIONAL] Fuente de NuGet con una privada con Azure DevOps

Se describe en una técnica más sólida [empezar a trabajar con paquetes de NuGet de Azure DevOps](https://docs.microsoft.com/azure/devops/artifacts/get-started-nuget?view=vsts&tabs=new-nav#publish-a-package), que muestra cómo crear una fuente privada e insertar el paquete (generado en el paso anterior) para esa fuente.

Es ideal para que este flujo de trabajo completamente automatizada, por ejemplo mediante [canalizaciones de Azure](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts). Para obtener más información, consulte [empezar a trabajar con las canalizaciones de Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=vsts).

## <a name="consuming-the-net-wrapper-from-a-xamarinforms-app"></a>Consumo del contenedor .NET desde una aplicación de Xamarin.Forms
Para completar el tutorial, cree un **Xamarin.Forms** aplicación para usar solo el paquete publicado local **NuGet** fuentes de distribución.

### <a name="creating-the-xamarinforms-project"></a>Crear el **Xamarin.Forms** proyecto

1. Abra una nueva instancia de **Visual Studio para Mac**. Esto puede hacerse desde **Terminal**:

    ```bash
    open -n -a "Visual Studio"
    ```

2. En **Visual Studio para Mac**, haga clic en **nuevo proyecto** (desde el *página de bienvenida*) o **nueva solución** (desde el *archivo* menú).
3. Desde el **nuevo proyecto** ventana, elija **aplicación de Forms en blanco** (desde *multiplataforma > aplicación*) y, a continuación, haga clic en **siguiente**.
4. Actualice los campos siguientes, a continuación, haga clic en **siguiente**:

    - **Nombre de la aplicación:** MathFuncsApp.
    - **Identificador de la organización:** Usar un espacio de nombres inverso, por ejemplo, _com. {your_org}_.
    - **Plataformas de destino:** Utilice el valor predeterminado (destinos iOS y Android).
    - **Código compartido:** Establezca esta opción en .NET Standard (una solución de "Biblioteca compartida" es posible, pero fuera del ámbito de este tutorial).

5. Actualice los campos siguientes, a continuación, haga clic en **crear**:

    - **Nombre del proyecto:** MathFuncsApp.
    - **Nombre de la solución:** MathFuncsApp.  
    - **Ubicación:** Use el valor predeterminado, ubicación de almacenamiento (o seleccionar una alternativa).

6. En **el Explorador de soluciones**, **CONTROL + clic** en el destino (**MathFuncsApp.Android** o **MathFuncs.iOS**) para las pruebas iniciales, a continuación, Elija **establecer como proyecto de inicio**.
7. Elija el preferido **dispositivo** o **simulador**/**emulador**. 
8. Ejecutar la solución (**comando + retorno**) para validar que la plantilla **Xamarin.Forms** proyecto se compila y se ejecuta bien. 

    > [!NOTE]
    > **iOS** (específicamente el simulador) suele tener el tiempo de compilación e implementación más rápido.

### <a name="adding-the-local-nuget-feed-to-the-nuget-configuration"></a>Adición del paquete de NuGet local de la fuente en la configuración de NuGet

1. En **Visual Studio**, elija **preferencias** (desde el **Visual Studio** menú).
2. Elija **orígenes** desde el **NuGet** sección y, después, haga clic en **agregar**.
3. Actualice los campos siguientes, a continuación, haga clic en **agregar origen**:

    - **Nombre:** Proporcione un nombre descriptivo, por ejemplo, los paquetes Local.  
    - **Ubicación:** Especifique el **fuente de nuget local** carpeta creada en el [paso anterior](#preparing-a-local-packages-directory).

    > [!NOTE]
    > En este caso, no hay ninguna necesidad de especificar un **Username** y **contraseña**. 

4. Haga clic en **Aceptar**.

### <a name="referencing-the-package"></a>Hacer referencia al paquete

Repita los pasos siguientes para cada proyecto (**MathFuncsApp**, **MathFuncsApp.Android**, y **MathFuncsApp.iOS**).

1. **CONTROL + clic** en el proyecto, a continuación, elija **agregar paquetes NuGet...**  desde el **agregar** menú.
2. Busque **MathFuncs**. 
3. Compruebe el **versión** del paquete es **1.0.0** y los demás detalles aparecen según lo esperado, como el **título** y **descripción**, es decir , *MathFuncs* y *biblioteca de contenedor de C++ de ejemplo*. 
4. Seleccione el **MathFuncs** del paquete y, después, haga clic en **Agregar paquete**.

### <a name="using-the-library-functions"></a>Uso de las funciones de biblioteca

Ahora, con una referencia a la **MathFuncs** paquete en cada uno de los proyectos, las funciones están disponibles para el C# código.

1.  Abra **MainPage.xaml.cs** desde el **MathFuncsApp** comunes **Xamarin.Forms**proyecto (al que hace referencia tanto **MathFuncsApp.Android**y **MathFuncsApp.iOS**).
2.  Agregar **mediante** instrucciones para **System.Diagnostics** y **MathFuncs** en la parte superior del archivo:

    ```csharp
    using System.Diagnostics;
    using MathFuncs;
    ```

3. Declare una instancia de la `MyMathFuncs` clase en la parte superior de la `MainPage` clase:

    ```csharp
    MyMathFuncs myMathFuncs;
    ```

4. Invalidar el `OnAppearing` y `OnDisappearing` métodos desde el `ContentPage` clase base:

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
    }
    ```

5. Actualización de la `OnAppearing` método para inicializar el `myMathFuncs` variable declarada anteriormente:

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
        myMathFuncs = new MyMathFuncs();
    }
    ```

6. Actualización de la `OnDisappearing` método para llamar a la `Dispose` método en `myMathFuncs`:

    ```csharp
    protected override void OnDisappearing()
    {
        base.OnAppearing();
        myMathFuncs.Dispose();
    }
    ```

7. Implementar un método privado denominado **TestMathFuncs** como sigue:

    ```csharp
    private void TestMathFuncs()
    {
        var numberA = 1;
        var numberB = 2;

        // Test Add function
        var addResult = myMathFuncs.Add(numberA, numberB);

        // Test Subtract function
        var subtractResult = myMathFuncs.Subtract(numberA, numberB);

        // Test Multiply function
        var multiplyResult = myMathFuncs.Multiply(numberA, numberB);

        // Test Divide function
        var divideResult = myMathFuncs.Divide(numberA, numberB);

        // Output results
        Debug.WriteLine($"{numberA} + {numberB} = {addResult}");
        Debug.WriteLine($"{numberA} - {numberB} = {subtractResult}");
        Debug.WriteLine($"{numberA} * {numberB} = {multiplyResult}");
        Debug.WriteLine($"{numberA} / {numberB} = {divideResult}");
    }
    ```

8. Por último, llame a `TestMathFuncs` al final de la `OnAppearing` método:

    ```csharp
    TestMathFuncs();
    ```

9. Ejecute la aplicación en cada plataforma de destino y validar el resultado en el **resultado de la aplicación** panel aparece como sigue:

    ```csharp
    1 + 2 = 3
    1 - 2 = -1
    1 * 2 = 2
    1 / 2 = 0.5
    ```

    > [!NOTE]
    > Si se produce un '*DLLNotFoundException*' cuando las pruebas en Android o un error de compilación en iOS, asegúrese de comprobar que la arquitectura de CPU del dispositivo/emulador o simulador usa es compatible con el subconjunto que se ha optado por soporte técnico. 

## <a name="summary"></a>Resumen

En este artículo se explica cómo crear una aplicación de Xamarin.Forms que usa las bibliotecas nativas a través de un contenedor común de .NET distribuidas a través de un paquete de NuGet. El ejemplo proporcionado en este tutorial es deliberadamente muy simplista para mostrar más fácilmente el enfoque. Una aplicación real, tendrá que tratar con complejidades como control de excepciones, las devoluciones de llamada, el cálculo de referencias de tipos más complejos y vincular con otras bibliotecas de dependencia. Una consideración clave es el proceso mediante el cual la evolución del código de C++ es coordinada y sincronizada con las aplicaciones cliente y el contenedor. Este proceso puede variar dependiendo de si uno o ambos de esos problemas son responsabilidad de un único equipo. En cualquier caso, la automatización es una ventaja real. A continuación se muestran algunos recursos que proporciona información adicional en torno a algunos de los conceptos claves junto con las descargas relevantes. 

### <a name="downloads"></a>Descargas

- [Herramientas de línea de comandos (CLI de NuGet)](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)
- [Visual Studio](https://visualstudio.microsoft.com/vs)

### <a name="examples"></a>Ejemplos

- [Desarrollo para dispositivos móviles multiplataforma Hyperlapse con C++](https://blogs.msdn.microsoft.com/vcblog/2015/06/26/hyperlapse-cross-platform-mobile-development-with-visual-c-and-xamarin/)
- [Microsoft Pix (C++ y Xamarin)](https://blog.xamarin.com/microsoft-research-ships-intelligent-apps-with-the-power-of-c-and-ai/)
- [Mono San Ángeles ejemplo puerto](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/)

### <a name="further-reading"></a>Información adicional

[Artículos relacionados con el contenido de esta publicación](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin#wrapping-up)
