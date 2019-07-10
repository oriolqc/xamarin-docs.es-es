---
title: 'Parte 1: descripción de la plataforma móvil en Xamarin'
description: Este documento describe la plataforma Xamarin en un nivel alto, mirando el proceso de compilación, acceso SDK de plataforma, uso compartido de código, creación de la interfaz de usuario, los diseñadores visuales y más.
ms.prod: xamarin
ms.assetid: FBCEF258-D3D8-A420-79ED-3AAB4A7308E4
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: c7c0f582ac4a7dc8571fbc607dba9b0ad97d49e1
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674844"
---
# <a name="part-1--understanding-the-xamarin-mobile-platform"></a>Parte 1: descripción de la plataforma móvil en Xamarin

La plataforma Xamarin consta de un número de elementos que le permiten desarrollar aplicaciones para iOS y Android:

-   **C#lenguaje** – le permite usar una sintaxis familiar y características sofisticadas, como los genéricos, LINQ y la biblioteca de tareas paralelas.
-   **Mono framework .NET** : proporciona una implementación multiplataforma de las amplias características de Microsoft .NET framework.
-   **Compilador** : según la plataforma, genera una aplicación nativa (p ej. iOS) o una aplicación integrada de .NET y en tiempo de ejecución (p ej. Android). El compilador también realiza muchas optimizaciones para una implementación móvil como vinculación ausente código no utilizado.
-   **Herramientas de IDE** – Visual Studio en Mac y Windows le permite crear, compilar e implementar proyectos de Xamarin.

Además, dado que es el lenguaje subyacente C# con .NET framework, se pueden estructurar los proyectos para compartir el código que también se puede implementar en Windows Phone.

## <a name="under-the-hood"></a>En segundo plano

Aunque Xamarin le permite escribir aplicaciones C#y compartir el mismo código en varias plataformas, la implementación real en cada sistema es muy diferente.

## <a name="compilation"></a>Compilación

El C# origen transforma su forma en una aplicación nativa de maneras muy diferentes en cada plataforma:

-   **iOS** – C# es ahead of time (AOT) compilada en lenguaje ensamblador ARM. .NET framework se incluye, con las clases no usadas se quitan durante la vinculación para reducir el tamaño de la aplicación. Apple no permite la generación de código en tiempo de ejecución en iOS, por lo que algunas características de lenguaje no están disponibles (consulte [Xamarin.iOS limitaciones](~/ios/internals/limitations.md) ).
-   **Android** – C# se compilan en IL y se empaquetan con MonoVM + JIT'ing. Las clases no usadas en el marco de trabajo se eliminan durante la vinculación. La aplicación se ejecuta por en paralelo con Java/ART (runtime de Android) e interactúa con los tipos nativos a través de JNI (consulte [Xamarin.Android limitaciones](~/android/internals/limitations.md) ).
-   **Windows** – C# se compilan en IL y ejecutado por el tiempo de ejecución integrado y no requiere herramientas de Xamarin. Diseño de aplicaciones de Windows una guía de Xamarin siguientes, es más fácil volver a usar el código en iOS y Android.
  Tenga en cuenta que la plataforma Universal de Windows también tiene un **.NET Native** opción que se comporta de forma similar a la compilación de AOT de Xamarin.iOS.


La documentación del vinculador para [Xamarin.iOS](~/ios/deploy-test/linker.md) y [Xamarin.Android](~/android/deploy-test/linker.md) proporciona más información sobre esta parte del proceso de compilación.

Tiempo de ejecución 'compilación': generación de código dinámicamente con `System.Reflection.Emit` – debe evitarse.

Generación de código dinámico en dispositivos iOS impide que el kernel de Apple, por lo tanto, emisión de código sobre la marcha no funcionarán en Xamarin.iOS. Del mismo modo, no se puede usar las características de Dynamic Language Runtime con las herramientas de Xamarin.

Algunas características de reflexión funcionan (p ej. MonoTouch.Dialog lo utiliza para la API de reflexión), pero no la generación de código.

## <a name="platform-sdk-access"></a>Acceso SDK de plataforma

Con Xamarin, las características proporcionadas por el SDK específico de la plataforma fácilmente accesible con familiar C# sintaxis:

-   **iOS** – Xamarin.iOS expone los marcos de CocoaTouch SDK de Apple como espacios de nombres que puede hacer referencia desde C#. Por ejemplo el marco UIKit que contiene todos los controles de interfaz de usuario puede incluirse con un sencillo `using UIKit;` instrucción.
-   **Android** : Xamarin.Android expone el SDK de Android de Google como espacios de nombres, por lo que puede hacer referencia a cualquier parte del SDK compatible con el uso de una instrucción, como `using Android.Views;` para tener acceso a los controles de interfaz de usuario.
-   **Windows** : las aplicaciones de Windows se compilan con Visual Studio en Windows. Tipos de proyecto incluyen Windows Forms, WPF, WinRT y la plataforma Universal de Windows (UWP).

## <a name="seamless-integration-for-developers"></a>Integración perfecta para desarrolladores

La belleza de Xamarin es que a pesar de las diferencias en segundo plano, Xamarin.iOS y Xamarin.Android (junto con los SDK de Windows de Microsoft) ofrecen una experiencia perfecta para escribir en él C# código que se puede volver a usar las tres plataformas.

Lógica de negocios, uso de la base de datos, acceso a la red y otras funciones comunes se pueden escritos una vez y reutilizarse en cada plataforma, proporcionando una base para las interfaces de usuario de específicos de la plataforma que representen y tengan como aplicaciones nativas.

## <a name="integrated-development-environment-ide-availability"></a>Disponibilidad de desarrollo integrado (IDE) de entorno

Desarrollo de Xamarin puede hacerse en Visual Studio en Mac o Windows. El IDE que elija determinará las plataformas que desea tener como destino.

Dado que solo se pueden desarrollar aplicaciones de Windows en Windows, para compilar para iOS, Android, _y_ Windows requiere Visual Studio para Windows. Sin embargo es posible compartir los proyectos y archivos entre equipos de Windows y Mac, por lo que las aplicaciones iOS y Android pueden crearse en un equipo Mac y código compartido podría agregarse más adelante a un proyecto de Windows.

Los requisitos de desarrollo para cada plataforma se analizan con más detalle en la [requisito](~/cross-platform/get-started/requirements.md) guía.

### <a name="ios"></a>iOS

Desarrollo de aplicaciones de iOS requiere un equipo Mac, que ejecutan macOS. También puede usar Visual Studio para escribir e implementar aplicaciones de iOS con Xamarin en Visual Studio. Sin embargo, sigue siendo necesario un equipo Mac para fines de licencias y la compilación.

IDE de Xcode de Apple debe estar instalado para proporcionar el compilador y el simulador para las pruebas. Puede probar en sus propios dispositivos [gratuitamente](~/ios/get-started/installation/device-provisioning/free-provisioning.md), pero para crear aplicaciones para la distribución (p ej. la App Store) debe unirse el programa para desarrolladores de Apple (99 USD al año). Cada vez que envíe o actualiza una aplicación, debe ser revisado y aprobado por Apple antes de que está disponible para los clientes descargar.

Se escribe código con el IDE de Visual Studio y diseños de pantalla pueden ser creados mediante programación o editar con iOS Designer en el IDE de Xamarin.

Hacer referencia a la [Guía de instalación de Xamarin.iOS](~/ios/get-started/installation/index.md) para obtener instrucciones detalladas sobre la configuración.

### <a name="android"></a>Android

Desarrollo de aplicaciones Android requiere Java y Android SDK se deben instalarse. Proporcionan el compilador, el emulador y otras herramientas necesarias para la creación, implementación y prueba. Herramientas de Java, SDK de Android y Xamarin de Google todos se pueden instalar y ejecutar en Windows y macOS. Se recomiendan las siguientes configuraciones:

- Windows 10 con Visual Studio de 2019
- macOS Mojave (10.11 y versiones posteriores) con 2019 de Visual Studio para Mac

Xamarin ofrece a un instalador unificado que configura el sistema con el requisito previo de Java, herramientas de Xamarin y Android (incluido un diseñador visual para los diseños de pantalla). Hacer referencia a la [Guía de instalación de Xamarin.Android](~/android/get-started/installation/index.md) para obtener instrucciones detalladas.

Puede compilar y probar aplicaciones en un dispositivo real sin ninguna licencia de Google, pero para distribuir la aplicación a través de una tienda (como Google Play, Amazon o Barnes &amp; Noble) una cuota de registro puedan aplicarse al operador. Google Play publicará la aplicación al instante, mientras que los demás almacenes tienen un proceso de aprobación similar de Apple.

### <a name="windows"></a>Windows

Aplicaciones de Windows (WinForms, WPF o UWP) se crean con Visual Studio. No usan Xamarin directamente. Sin embargo, C# se puede compartir código entre Windows, iOS y Android.
Visite Microsoft [centro de desarrollo](https://developer.microsoft.com/) para obtener información sobre las herramientas necesarias para el desarrollo de Windows.

## <a name="creating-the-user-interface-ui"></a>Crear la interfaz de usuario (UI)

Una ventaja clave de uso de Xamarin es que la interfaz de usuario de la aplicación usa los controles nativos en cada plataforma, creación de aplicaciones que se pueden distinguir de una aplicación escrita en Objective-C o Java (para iOS y Android respectivamente).

Al crear pantallas en la aplicación, puede organizar los controles en el código o volver a crear pantallas completas con las herramientas de diseño disponibles para cada plataforma.

### <a name="create-controls-programmatically"></a>Crear controles mediante programación

Cada plataforma permite controles de interfaz que se agregarán a una pantalla mediante código de usuario. Esto puede ser muy lenta, ya que puede ser difícil visualizar el diseño finalizado cuando coordenadas de píxel de forma rígida para tamaños y posiciones de control.

Crear mediante programación controles tiene ventajas no obstante, especialmente en iOS para la creación de vistas que cambiar el tamaño o representan de manera diferente a través de los tamaños de pantalla de iPhone y iPad.

### <a name="visual-designer"></a>Diseñador Visual

Cada plataforma tiene un método diferente para diseñar visualmente las pantallas:

- **iOS** : Diseñador de iOS de Xamarin facilita la creación de vistas que usan los campos de propiedad y la funcionalidad de arrastrar y colocar. Colectivamente estas vistas constituyen un guión gráfico y puede obtenerse en la **. Guión gráfico** archivo que se incluye en el proyecto.
- **Android** : Xamarin ofrece un diseñador de interfaz de usuario de arrastrar y colocar Android para Visual Studio. Diseños de pantalla de Android se guardan como **. AXML** archivos al usar las herramientas de Xamarin.
- **Windows** – Microsoft proporciona un diseñador de interfaz de usuario de arrastrar y colocar en Visual Studio y Blend. Los diseños de pantalla se almacenan como. Archivos XAML.

Estas capturas de pantalla muestran los diseñadores de pantalla visual disponibles en cada plataforma:

 [![](understanding-the-xamarin-mobile-platform-images/designer-all1.png "Estas capturas de pantalla muestran los diseñadores de pantalla visual disponibles en cada plataforma")](understanding-the-xamarin-mobile-platform-images/designer-all1.png#lightbox)

En todos los casos se pueden hacer referencia a los elementos que cree visualmente en el código.

### <a name="user-interface-considerations"></a>Consideraciones sobre la interfaz de usuario

Una ventaja clave de uso de Xamarin para compilar aplicaciones multiplataforma es que puede sacar partido de los kits de herramientas de interfaz de usuario nativos para presentar una interfaz conocida para el usuario. La interfaz de usuario también llevan a cabo tan rápido como cualquier otra aplicación nativa.

Algunos metáforas relacionadas con la interfaz de usuario funcionan en diferentes plataformas (por ejemplo, las tres plataformas usan un control de lista desplazamiento similar), pero en orden de la aplicación 'sentirán' la interfaz de usuario debe sacar partido de usuario específicas de plataforma elementos de la interfaz cuando sea adecuado. Ejemplos de metáforas relacionadas con la interfaz de usuario específicas de la plataforma:

- **iOS** – pestañas de navegación jerárquica con el botón Atrás suave, en la parte inferior de la pantalla.
- **Android** : hardware o software del sistema back-botón, el menú Acción, las pestañas en la parte superior de la pantalla.
- **Windows** : las aplicaciones de Windows pueden ejecutar en equipos de escritorio, tabletas (como Microsoft Surface) y teléfonos. Dispositivos Windows 10 pueden tener hardware hacer una copia de botón e iconos dinámicos, por ejemplo.

Se recomienda que lea las instrucciones de diseño relacionadas con las plataformas de que destino:

- **iOS** – [directrices de interfaz humana de Apple](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html)
- **Android** – [directrices de interfaz de usuario de Google](https://developer.android.com/guide/practices/ui_guidelines/index.html)
- **Windows** – [directrices de diseño de la experiencia de usuario de Windows](https://developer.microsoft.com/windows/design)

## <a name="library-and-code-re-use"></a>Biblioteca y la reutilización del código

La plataforma de Xamarin permite volver a utilizar existente C# código en todas las plataformas, así como la integración de bibliotecas escritas de forma nativa para cada plataforma.

### <a name="c-source-and-libraries"></a>C#Origen y bibliotecas

Dado que los productos de Xamarin utilizan C# y .NET framework, una gran cantidad de código fuente existente (ambos y código abierto proyectos internos) pueden volver a usar en proyectos de Xamarin.iOS o Xamarin.Android. A menudo el origen simplemente puede agregarse a una solución de Xamarin y funcionará inmediatamente. Si se ha utilizado una funcionalidad no admitida de marco de trabajo. NET, pueden requerirse algunos ajustes.

Ejemplos de C# incluyen código fuente que se puede usar en Xamarin.iOS o Xamarin.Android: NET de SQLite, NewtonSoft.JSON y SharpZipLib.

### <a name="objective-c-bindings--binding-projects"></a>Enlaces de Objective-C + proyectos de enlace

Xamarin ofrece una herramienta denominada *btouch* que ayuda a crea los enlaces que permiten a las bibliotecas de Objective-C que se usará en los proyectos de Xamarin.iOS. Hacer referencia a la [documentación sobre tipos de enlace de Objective-C](~/cross-platform/macios/binding/binding-types-reference.md) para obtener más información sobre cómo hacerlo.

Ejemplos de bibliotecas de Objective-C que se pueden usar en Xamarin.iOS: Código de barras RedLaser análisis, integración de Google Analytics y PayPal. Enlaces de Xamarin.iOS de código abierto están disponibles en [github](https://github.com/mono/monotouch-bindings).

### <a name="jar-bindings--binding-projects"></a>enlaces .jar + proyectos de enlace

Xamarin admite el uso de bibliotecas de Java existentes en Xamarin.Android. Hacer referencia a la [enlace una documentación de la biblioteca de Java](~/android/platform/binding-java-library/index.md) para obtener más información sobre cómo usar una. Archivo JAR de Xamarin.Android.

Están disponibles en los enlaces de código abierto Xamarin.Android [github](https://github.com/mono/monodroid-bindings).

### <a name="c-via-pinvoke"></a>C a través de PInvoke

Tecnología de "Invocación de plataforma" (P/Invoke) permite que el código administrado (C#) para llamar a métodos en las bibliotecas nativas así como compatibilidad con las bibliotecas nativas para volver a llamar a código administrado.

Por ejemplo, el [SQLite-NET](https://github.com/praeclarum/sqlite-net) biblioteca usa instrucciones similar al siguiente:

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open", CallingConvention=CallingConvention.Cdecl)]
public static extern Result Open (string filename, out IntPtr db);
```

Esto enlaza la implementación nativa de lenguaje C SQLite en iOS y Android.
Los desarrolladores familiarizados con una API de C existente pueden construir un conjunto de C# clases se asignan a la API nativa y usar el código de plataforma existente. Hay documentación para [vincular bibliotecas nativas](~/ios/platform/native-interop.md) en Xamarin.iOS, se aplican los principios similares para Xamarin.Android.

### <a name="c-via-cppsharp"></a>C++ a través de CppSharp

Miguel explica CXXI (ahora denominado [CppSharp](https://github.com/mono/CppSharp)) en su [blog](https://tirania.org/blog/archive/2011/Dec-19.html). Es una alternativa al enlace directamente a una biblioteca de C++ crear un contenedor de C y enlazar a la a través de P/Invoke.
