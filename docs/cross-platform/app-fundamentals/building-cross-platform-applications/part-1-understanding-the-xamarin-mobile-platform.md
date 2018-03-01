---
title: "Parte 1: descripción de la plataforma Mobile Xamarin"
ms.topic: article
ms.prod: xamarin
ms.assetid: FBCEF258-D3D8-A420-79ED-3AAB4A7308E4
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 8b3908bcbf13063daced19c9d85b95bcd0a69c37
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="part-1--understanding-the-xamarin-mobile-platform"></a>Parte 1: descripción de la plataforma Mobile Xamarin

La plataforma Xamarin consta de un número de elementos que le permiten desarrollar aplicaciones para iOS y Android:

-   **Lenguaje C#** – le permite usar una sintaxis familiar y sus características sofisticadas como genéricos, LINQ y la biblioteca de tareas de procesamiento paralelo.
-   **El marco de trabajo de mono .NET** : proporciona una implementación entre plataformas de las amplias características de Microsoft .NET framework.
-   **Compilador** : según la plataforma, genera una aplicación nativa (p. ej. iOS) o una aplicación .NET integrada y en tiempo de ejecución (p. ej. Android). El compilador también realiza muchas optimizaciones para la implementación de dispositivos móvil como vinculación alejada código no utilizado.
-   **Herramientas del IDE** : Visual Studio en Mac y Windows le permite crear, compilar e implementar proyectos de Xamarin.


Además, dado que el lenguaje subyacente es C# con .NET framework, los proyectos se pueden estructurar para compartir el código que también se puede implementar en Windows Phone.

 <a name="Under_the_Hood" />


## <a name="under-the-hood"></a>En segundo plano

Aunque Xamarin le permite escribir aplicaciones en C# y compartir el mismo código en varias plataformas, la implementación real en cada sistema es muy diferente.

 <a name="Compilation" />


## <a name="compilation"></a>Compilación

El código fuente C#, su forma convierte en una aplicación nativa de maneras muy diferentes en cada plataforma:

-   **iOS** – C# es de anticipado (AOT) compilado en el lenguaje de ensamblado ARM. .NET framework se incluye, sin usar clases que se elimina durante la vinculación para reducir el tamaño de la aplicación. Apple no permite la generación de código en tiempo de ejecución en iOS, por lo que no están disponibles algunas características del lenguaje (vea [Xamarin.iOS limitaciones](~/ios/internals/limitations.md) ).
-   **Android** – C# se compilan en IL y empaquetado con MonoVM + JIT'ing. Clases no utilizadas en el marco de trabajo se eliminan durante la vinculación. La aplicación se ejecuta por paralelo con Java/prediseñadas (Android en tiempo de ejecución) e interactúa con los tipos nativos a través de JNI (vea [Xamarin.Android limitaciones](~/android/internals/limitations.md) ).
-   **Windows** : C# se compilan en IL y ejecutados por el tiempo de ejecución integrado y no requiere herramientas de Xamarin. Diseñar aplicaciones de Windows guía de Xamarin siguiente, es más fácil volver a usar el código de iOS y Android.
  Tenga en cuenta que la plataforma Universal de Windows también tiene un **.NET Native** opción que se comporta de forma similar a la compilación de Xamarin.iOS AOT.


La documentación del vinculador para [Xamarin.iOS](~/ios/deploy-test/linker.md) y [Xamarin.Android](~/android/deploy-test/linker.md) proporciona más información sobre esta parte del proceso de compilación.

En tiempo de ejecución 'compilation': generar código dinámicamente con `System.Reflection.Emit` – debería evitarse.

Generación de código dinámico en dispositivos iOS impedirá que el kernel de Apple, por lo tanto, emisión de código sobre la marcha no funcionarán en Xamarin.iOS. Asimismo, las características de Dynamic Language Runtime no se puede usar con herramientas de Xamarin.

Algunas características de reflexión se funcionen (p. ej. MonoTouch.Dialog lo utiliza para la API de reflexión), pero no la generación de código.

 <a name="Platform_SDK_Access" />


## <a name="platform-sdk-access"></a>Acceso SDK de plataforma

Xamarin hace que las características proporcionadas por el SDK de específico de la plataforma fácilmente accesible con la conocida sintaxis de C#:

-   **iOS** – Xamarin.iOS expone los marcos de trabajo de Apple CocoaTouch SDK como espacios de nombres que puede hacer referencia de C#. Por ejemplo el marco de trabajo de UIKit que contiene todos los controles de interfaz de usuario puede incluirse con un sencillo `using UIKit;` instrucción.
-   **Android** – Xamarin.Android expone Android SDK de Google como espacios de nombres, por lo que puede hacer referencia a cualquier parte del SDK compatible con el uso de una instrucción, como `using Android.Views;` para tener acceso a los controles de interfaz de usuario.
-   **Windows** : aplicaciones de Windows están compiladas con Visual Studio en Windows. Tipos de proyecto incluyen formularios Windows Forms, WPF, WinRT y la plataforma Universal de Windows (UWP).


 <a name="Seamless_Integration_for_Developers" />


## <a name="seamless-integration-for-developers"></a>Integración sin problemas para desarrolladores

La belleza de Xamarin es que a pesar de las diferencias en segundo plano, Xamarin.iOS y Xamarin.Android (junto con los Windows SDK de Microsoft) ofrecen una experiencia perfecta para escribir código C# que se puede volver a usar en las tres plataformas.

Lógica de negocios, el uso de base de datos, el acceso a la red y otras funciones comunes se pueden escritos una vez y volver a utilizar en cada plataforma, proporcionar una base para las interfaces de usuario específica de la plataforma que buscan y realizan como aplicaciones nativas.

 <a name="Integrated_Development_Environment_(IDE)_Availability" />


## <a name="integrated-development-environment-ide-availability"></a>Disponibilidad de desarrollo integrado (IDE) de entorno

Desarrollo de Xamarin puede hacerse en Visual Studio en Windows o Mac. El IDE elige vendrá determinado por las plataformas que se va a tener como destino.

Dado que solo se pueden desarrollar aplicaciones de Windows en Windows, para compilar para iOS, Android, _y_ Windows requiere que Visual Studio para Windows. Sin embargo es posible compartir archivos entre equipos de Windows y Mac, por lo que se pueden crear aplicaciones iOS y Android en un equipo Mac y más adelante se puede agregar un código compartido en un proyecto de Windows y proyectos.

Los requisitos de desarrollo para cada plataforma se tratan con más detalle en la [requisito](~/cross-platform/get-started/requirements.md) guía.


<a name="iOS" />

### <a name="ios"></a>iOS

Desarrollo de aplicaciones de iOS requiere un equipo Mac, ejecutando macOS. También puede usar Visual Studio para escribir e implementar aplicaciones de iOS con Xamarin en Visual Studio. Sin embargo, sigue siendo necesario un equipo Mac para fines de licencias y compilación.

Debe instalarse Xcode IDE de Apple para proporcionar el compilador y el simulador para las pruebas. Puede probar en sus propios dispositivos [gratuitamente](~/ios/get-started/installation/device-provisioning/free-provisioning.md), pero para compilar aplicaciones para su distribución (p. ej. la tienda de aplicaciones), debe unir programa para desarrolladores de Apple (99 dólares al año). Cada vez que envíe o actualiza una aplicación, se debe revisado y aprobado por Apple antes de que se pone a disposición de los clientes descargar.

Se escribe código con el IDE de Visual Studio y diseños de pantalla se pueden crea mediante programación o editar con iOS de Xamarin diseñador en el IDE.

Hacer referencia a la [Xamarin.iOS Installation Guide](~/ios/get-started/installation/index.md) para obtener instrucciones detalladas sobre la configuración.

<a name="Android" />

### <a name="android"></a>Android

Desarrollo de aplicaciones Android requiere los Java y los SDK de Android para instalarse. Proporcionan el compilador, el emulador y otras herramientas necesarias para la creación, implementación y prueba. Herramientas de Java, de Google Android SDK y de Xamarin pueden todos instalarse y ejecutarse en las siguientes configuraciones:

-  Mac OS X El capitán y versiones posteriores (10.11 +) con Visual Studio para Mac
-  Windows 7 & anterior con Visual Studio 2015 o de 2017


Xamarin ofrece a un instalador unificado que va a configurar el sistema con el requisito previo de Java, herramientas de Xamarin y Android (incluido un diseñador visual para los diseños de pantalla). Hacer referencia a la [Xamarin.Android Installation Guide](~/android/get-started/installation/index.md) para obtener instrucciones detalladas.

Puede compilar y probar las aplicaciones en un dispositivo real sin ningún tipo de licencia de Google, sin embargo para distribuir la aplicación a través de una tienda (como Google Play, Amazon o Barnes &amp; Noble) una cuota de registro puedan aplicarse al operador. Google Play publicará la aplicación al instante, mientras que los demás almacenes tienen un proceso de aprobación similar de Apple.

 <a name="Windows_Phone" />


### <a name="windows"></a>Windows

Aplicaciones de Windows (formularios Windows Forms, WPF o UWP) se crean con Visual Studio. No utilizan Xamarin directamente. Sin embargo, el código de C# puede compartirse a través de Windows, iOS y Android.
Visite de Microsoft [centro de desarrollo](https://developer.microsoft.com/) para obtener información acerca de las herramientas necesarias para el desarrollo de Windows.

 <a name="Creating_the_User_Interface_(UI)" />


## <a name="creating-the-user-interface-ui"></a>Crear la interfaz de usuario (UI)

Una ventaja clave del uso de Xamarin es que la interfaz de usuario de la aplicación usa los controles nativos en cada plataforma, crear aplicaciones que no están posible distinguir desde una aplicación escrita en Objective-C o Java (iOS y Android respectivamente).

Al crear pantallas en la aplicación, puede organizar los controles en el código o crear pantallas completadas utilizando las herramientas de diseño disponibles para cada plataforma.

 <a name="Programmatically_Create_Controls" />


### <a name="create-controls-programmatically"></a>Crear controles mediante programación

Cada plataforma permite controles de interfaz de agregarse a una pantalla mediante código de usuario. Esto puede resultar muy lento como puede ser difícil de visualizar el diseño terminado cuando coordenadas de píxel codificar de forma rígida para tamaños y posiciones de los controles.

Crear controles mediante programación tiene ventajas, especialmente en iOS para la creación de vistas que representan de forma diferente en los tamaños de pantalla de iPhone y iPad o cambie su tamaño.

 <a name="Visual_Designer" />


### <a name="visual-designer"></a>Diseñador Visual

Cada plataforma tiene un método diferente para diseñar visualmente las pantallas:

-   **iOS** : iOS de Xamarin diseñador facilita la creación de vistas que utilizan los campos de propiedad y la funcionalidad de arrastrar y colocar. Colectivamente estas vistas constituyen un guión gráfico y son accesibles en el **. Guión gráfico** archivo que se incluye en el proyecto.
-   **Android** – Xamarin proporciona un diseñador de interfaz de usuario de arrastrar y colocar Android para Visual Studio. Diseños de pantalla Android se guardan como **. AXML** archivos cuando se utilizan herramientas de Xamarin.
-   **Windows** – Microsoft proporciona un diseñador de la interfaz de usuario de arrastrar y colocar en Visual Studio y Blend. Los diseños de pantalla se almacenan como. Archivos XAML.


Estas capturas de pantalla muestran los diseñadores de visual pantalla disponibles en cada plataforma:

 [ ![](part-1-understanding-the-xamarin-mobile-platform-images/designer-all1.png "Estas capturas de pantalla muestran los diseñadores de visual pantalla disponibles en cada plataforma")](part-1-understanding-the-xamarin-mobile-platform-images/designer-all1.png)

En todos los casos se pueden hacer referencia a los elementos que cree visualmente en el código.

 <a name="User_Interface_Considerations" />


### <a name="user-interface-considerations"></a>Consideraciones sobre la interfaz de usuario

Una ventaja clave del uso de Xamarin para crear aplicaciones multiplataforma es que pueden aprovechar las ventajas de nativo kits de herramientas de interfaz de usuario para presentar una interfaz conocida para el usuario. La interfaz de usuario también realizará tan rápido como cualquier otra aplicación nativa.

Algunos metáforas relacionadas con la interfaz de usuario pueden funcionar en varias plataformas (por ejemplo, las tres plataformas usan un control de lista desplazamiento similar) pero en orden de la aplicación para 'sentirán' la interfaz de usuario debe sacar provecho de usuario específico de la plataforma de elementos de la interfaz cuando sea apropiado. Ejemplos de metáforas de interfaz de usuario específica de la plataforma:

-   **iOS** – pestañas de navegación jerárquica con el botón Atrás suave, en la parte inferior de la pantalla.
-   **Android** : hardware/software sistema back-botón, el menú Acción, las pestañas en la parte superior de la pantalla.
-   **Windows** : aplicaciones de Windows se pueden ejecutar en equipos de escritorio, tabletas (como Microsoft Surface) y teléfonos. Dispositivos Windows 10 pueden tener hardware hacer copia de botón e iconos dinámicos, por ejemplo.


Se recomienda que lea las instrucciones de diseño relacionadas con las plataformas de que destino:

-   **iOS** – [directrices de interfaz humana de Apple](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html)
-   **Android** – [directrices de interfaz de usuario de Google](http://developer.android.com/guide/practices/ui_guidelines/index.html)
-   **Windows** – [instrucciones de diseño de la experiencia de usuario de Windows](https://developer.microsoft.com/en-us/windows/design)


 <a name="Library_and_Code_Re-use" />


## <a name="library-and-code-re-use"></a>Biblioteca y reutilización de código

La plataforma Xamarin permite la reutilización de código C# existente en todas las plataformas, así como la integración de bibliotecas escritos de forma nativa para cada plataforma.

 <a name="C#_Source_and_Libraries" />


### <a name="c-source-and-libraries"></a>Bibliotecas y código fuente de C#

Como productos de Xamarin usan C# y .NET framework, una gran cantidad de código fuente existente (ambos Abrir origen y proyectos internos) puede volver a usar en proyectos de Xamarin.iOS o Xamarin.Android. A menudo el origen simplemente puede agregarse a una solución de Xamarin y funcionará inmediatamente. Si se ha utilizado una función de marco .NET no compatible, pueden requerirse algunos ajustes.

Ejemplos de código fuente C# que puede usarse en Xamarin.iOS o Xamarin.Android: NET de SQLite, NewtonSoft.JSON y SharpZipLib.

 <a name="Objective-C_Bindings_+_Binding_Projects" />


### <a name="objective-c-bindings--binding-projects"></a>Enlaces de Objective-C + enlace proyectos

Xamarin ofrece una herramienta denominada *btouch* que le ayuda a crear los enlaces que permiten a las bibliotecas de Objective-C para su uso en proyectos de Xamarin.iOS. Hacer referencia a la [documentación sobre tipos de Objective-C de enlace](~/cross-platform/macios/binding/binding-types-reference.md) para obtener más información sobre cómo hacerlo.

Ejemplos de bibliotecas de Objective-C que pueden usarse en Xamarin.iOS: código de barras RedLaser análisis, integración de Google Analytics y PayPal. Están disponibles en enlaces de código abierto Xamarin.iOS [github](https://github.com/mono/monotouch-bindings).

 <a name=".jar_Bindings_+_Binding_Projects" />


### <a name="jar-bindings--binding-projects"></a>enlaces de .jar + enlace proyectos

Xamarin admite el uso de las bibliotecas existentes de Java en Xamarin.Android. Hacer referencia a la [enlace una documentación de la biblioteca de Java](~/android/platform/binding-java-library/index.md) para obtener más información sobre cómo usar una. Archivo JAR de Xamarin.Android.

Están disponibles en enlaces de código abierto Xamarin.Android [github](https://github.com/mono/monodroid-bindings).

 <a name="C_via_PInvoke" />


### <a name="c-via-pinvoke"></a>C mediante PInvoke

Tecnología de "Invocación de plataforma" (P/Invoke) permite código administrado (C#) para llamar a métodos de bibliotecas nativas así como compatibilidad con bibliotecas nativas llamar en código administrado.

Por ejemplo, el [SQLite NET](https://github.com/praeclarum/sqlite-net) biblioteca utiliza instrucciones similar al siguiente:

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open", CallingConvention=CallingConvention.Cdecl)]
public static extern Result Open (string filename, out IntPtr db);
```

Esto enlaza a la implementación de código de lenguaje C nativo en iOS y Android.
Los programadores familiarizados con una API de C existente pueden construir un conjunto de clases de C# se asignan a la API nativa y utilizar el código de plataforma existente. Hay documentación para [vincular bibliotecas nativas](~/ios/platform/native-interop.md) en Xamarin.iOS, se aplican a Xamarin.Android principios similares.

 <a name="C++_via_Cxxi" />


### <a name="c-via-cppsharp"></a>C++ a través de CppSharp

Miguel explica CXXI (ahora denominado [CppSharp](https://github.com/mono/CppSharp)) en su [blog](http://tirania.org/blog/archive/2011/Dec-19.html). Alternativa al enlace directamente en una biblioteca de C++ consiste en crear un contenedor de C y enlazar a la a través de P/Invoke.

