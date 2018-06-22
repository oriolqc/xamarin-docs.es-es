---
title: Enlace de una biblioteca de Java
description: La Comunidad de Android tiene muchas bibliotecas de Java que se pueden usar en la aplicación; Esta guía explica cómo incorporar las bibliotecas de Java a la aplicación Xamarin.Android mediante la creación de una biblioteca de enlaces.
ms.prod: xamarin
ms.assetid: B39FF1D5-69C3-8A76-D268-C227A23C9485
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/01/2017
ms.openlocfilehash: 3c2ed92da07519516db94697bbeaac9f328baa22
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30768826"
---
# <a name="binding-a-java-library"></a>Enlace de una biblioteca de Java

_La Comunidad de Android tiene muchas bibliotecas de Java que se pueden usar en la aplicación; Esta guía explica cómo incorporar las bibliotecas de Java a la aplicación Xamarin.Android mediante la creación de una biblioteca de enlaces._

## <a name="overview"></a>Información general

El ecosistema de biblioteca de otro fabricante para Android es masivo. Por este motivo, con frecuencia tiene sentido utilizar una biblioteca de Android existente que to crear uno nuevo. Xamarin.Android ofrece dos maneras de utilizar estas bibliotecas:

-   Crear un *enlaces biblioteca* que ajusta de forma automática la biblioteca con contenedores de C# por lo que puede invocar código de Java a través de llamadas de C#.

-   Use la *Java Native Interface* (*JNI*) para invocar las llamadas en el código de biblioteca de Java directamente. JNI es un marco de programación que permite que el código de Java llamar y ser llamados por aplicaciones nativas o bibliotecas.

Esta guía explica la primera opción: cómo crear un *enlaces biblioteca* que contiene una o más bibliotecas de Java existentes en un ensamblado que se puede vincular a en la aplicación. Para obtener más información acerca del uso de JNI, consulte [trabajar con JNI](~/android/platform/java-integration/working-with-jni.md).

Xamarin.Android implementa enlaces mediante *administrado Callable Wrappers* (*MCW*). MCW es un puente JNI que se utiliza cuando es necesario código administrado para llamar a código de Java. Contenedores CCW administrados también proporcionan compatibilidad para la creación de subclases de tipos de Java y para invalidar métodos virtuales en tipos de Java. Del mismo modo, cada vez que el código en tiempo de ejecución Android (prediseñadas) desea invocar código administrado, lo hace a través de otro puente JNI conocido como contenedores CCW Android (ACW). Esto [arquitectura](~/android/internals/architecture.md) se ilustra en el diagrama siguiente:

[![Arquitectura de puente JNI Android](images/architecture.png)](images/architecture.png#lightbox)

Una biblioteca de enlaces es un ensamblado que contiene los contenedores CCW administrado para los tipos de Java. Por ejemplo, este es un tipo de Java, `MyClass`, que va a incluir en una biblioteca de enlaces:

```java
package com.xamarin.mycode;

public class MyClass
{
    public String myMethod (int i) { ... }
}
```

Después de que se genere una biblioteca de enlaces para la **.jar** que contiene `MyClass`, podemos crear una instancia y llamar a métodos en ella desde C#:

```csharp
var instance = new MyClass ();

string result = instance.MyMethod (42);
```

Para crear esta biblioteca de enlaces, debe utilizar el Xamarin.Android *Java enlaces biblioteca* plantilla. El proyecto de enlace resultante, crea un ensamblado .NET con las clases MCW **.jar** los archivos y recursos para los proyectos de biblioteca de Android incrustados en él. También puede crear bibliotecas de enlaces para archivar Android (. Archivos AAR) y proyectos de biblioteca de Android de Eclipse. Haciendo referencia al ensamblado resultante de la DLL de biblioteca de enlaces, puede volver a usar una biblioteca de Java existente en el proyecto Xamarin.Android.

Al hacer referencia a tipos en la biblioteca de enlace, debe usar el espacio de nombres de la biblioteca de enlace. Normalmente, se agregan un `using` directiva en la parte superior de su origen de C# de archivos, es decir, la versión de espacio de nombres .NET del nombre del paquete de Java. Por ejemplo, si el nombre del paquete de Java para el límite **.jar** es el siguiente:

```csharp
com.company.package
```

A continuación, tendría que poner el siguiente `using` instrucción en la parte superior de los archivos de código fuente de C# para tener acceso a los tipos en el límite **.jar** archivo:

```csharp
using Com.Company.Package;
```


Al enlazar una biblioteca de Android existente, es necesario que tenga en cuenta los puntos siguientes:

* **¿Hay alguna dependencia externa para la biblioteca?** &ndash; Las dependencias de Java requeridas por la biblioteca Android deben incluirse en el proyecto Xamarin.Android como un **ReferenceJar** o como un **EmbeddedReferenceJar**. Los ensamblados nativos deben agregarse al proyecto de enlace como un **EmbeddedNativeLibrary**.  

* **¿Qué versión de la API de Android es ¿es el destino de la biblioteca de Android?** &ndash; No es posible "volver" el nivel de API de Android; Asegúrese de que el proyecto de enlace Xamarin.Android está destinado a la API de mismo nivel (o superior) como la biblioteca de Android.

* **¿Qué versión de JDK se utiliza para compilar la biblioteca?** &ndash; Si la biblioteca Android se compiló con una versión diferente de JDK que en uso por Xamarin.Android, pueden producirse errores de enlace. Si es posible, vuelva a compilar la biblioteca Android con la misma versión de JDK que se usa por la instalación de Xamarin.Android.


## <a name="build-actions"></a>Acciones de compilación

Cuando crea una biblioteca de enlaces, se establecen *acciones de compilación* en el **.jar** o. Archivos AAR que incorporar en el proyecto de biblioteca de enlaces &ndash; cada acción de compilación determina cómo el **.jar** o. Archivo AAR se pueden incrustar en (o al que hace referencia) la biblioteca de enlaces. En la lista siguiente se resume estas acciones de compilación:

* `EmbeddedJar` &ndash; Incrusta el **.jar** en la DLL de biblioteca de enlaces resultante como un recurso incrustado. Esto es la más sencilla y la acción de compilación usados con frecuencia la mayoría. Use esta opción cuando desee que el **.jar** automáticamente compilado en el código de bytes y se han empaquetado en la biblioteca de enlaces.

* `InputJar` &ndash; No incrusta el **.jar** en la biblioteca de enlaces resultante. DLL. La biblioteca de enlaces. DLL tiene una dependencia en el objeto **.jar** en tiempo de ejecución. Use esta opción si no desea incluir el **.jar** en la biblioteca de enlaces (por ejemplo, por motivos de licencias). Si utiliza esta opción, debe asegurarse de que la entrada **.jar** está disponible en el dispositivo que ejecuta la aplicación.

* `LibraryProjectZip` &ndash; Incrusta un. Archivo AAR en la biblioteca de enlaces resultante. DLL. Esto es similar a EmbeddedJar, salvo que puede tener acceso a recursos (así como código) en el límite. Archivos AAR. Use esta opción si desea incrustar una. AAR a la biblioteca de enlaces.

* `ReferenceJar` &ndash; Especifica una referencia **.jar**: una referencia **.jar** es un **.jar** que uno de los enlazados **.jar** o. Archivos AAR depende. Esta referencia **.jar** sólo se utiliza para satisfacer las dependencias de tiempo de compilación. Cuando se usa esta acción de compilación, no se crean enlaces de C# para la referencia de **.jar** y no se incrustan en la biblioteca de enlaces resultante. DLL. Use esta opción si va a realizar una biblioteca de enlaces para la referencia **.jar** pero aún no lo hecho aún. Esta acción de compilación es útil para empaquetar varios **.jar**s (o. AARs) en varias bibliotecas de enlaces interdependientes.

* `EmbeddedReferenceJar` &ndash; Inserta una referencia **.jar** en la biblioteca de enlaces resultante. DLL. Use esta acción de compilación cuando desee crear enlaces de C# para la entrada **.jar** (o). AAR) y todos su referencia **.jar**(s) en la biblioteca de enlaces.

* `EmbeddedNativeLibrary` &ndash; Incrusta nativo **. SO** en el enlace. Esta acción de compilación se utiliza para **. SO** archivos requeridos por la **.jar** archivo que se va a enlazar. Es posible que sea necesario cargar manualmente el **. SO** biblioteca antes de ejecutar el código de la biblioteca de Java. Esto se describe a continuación.

Estas acciones se explican con más detalle en las siguientes guías de compilación.

Además, las siguientes acciones de compilación se usan para importar la documentación de la API de Java y convierten en documentación XML de C#:

* `JavaDocJar` se usa para apuntar al archivo Jar de Javadoc para una biblioteca de Java que se ajusta a un estilo de paquete de Maven (normalmente `FOOBAR-javadoc**.jar**`).
* `JavaDocIndex` se utiliza para señalar a `index.html` archivo dentro de la documentación de referencia de API de HTML.
* `JavaSourceJar` se utiliza para complementar `JavaDocJar`para generar primero JavaDoc de orígenes y, a continuación, tratar los resultados como `JavaDocIndex`, para una biblioteca de Java que se ajusta a un experto en paquete estilo (normalmente `FOOBAR-sources**.jar**`).

La documentación de API debe ser el doclet predeterminado de Java8, Java7 o Java6 SDK (son formato uno distinto), o el estilo DroidDoc.

## <a name="including-a-native-library-in-a-binding"></a>Incluir una biblioteca nativa en un enlace

Es posible que sea necesario incluir una **. SO** biblioteca en un proyecto de enlace Xamarin.Android como parte de una biblioteca de Java de enlace. Cuando se ejecuta el código Java ajustado, se producirá un error Xamarin.Android realizar la llamada JNI y el mensaje de error _java.lang.UnsatisfiedLinkError: no se encontró el método nativo:_ aparecerá en la logcat fuera de la aplicación.

La solución para este problema consiste en cargar manualmente el **.so** biblioteca con una llamada a `Java.Lang.JavaSystem.LoadLibrary`. Por ejemplo si suponemos que un proyecto Xamarin.Android compartió biblioteca **libpocketsphinx_jni.so** incluido en el proyecto de enlace con una acción de compilación de **EmbeddedNativeLibrary**, el siguiente fragmento de código (que se ejecutan antes de usar la biblioteca compartida) se cargará el **. SO** biblioteca:

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="adapting-java-apis-to-ceparsl"></a>Adaptar las API de Java a C&eparsl;

El generador de enlace de Xamarin.Android cambiará algunas expresiones de Java y patrones para que se correspondan con los patrones de. NET. En la lista siguiente describe cómo se asigna Java a C# / .NET:

-   _Métodos de captador y establecedor_ en Java son _propiedades_ en. NET.

-   _Campos_ en Java son _propiedades_ en. NET.

-   _Interfaces de agentes de escucha/Listener_ en Java son _eventos_ en. NET. Los parámetros de métodos de las interfaces de devolución de llamada se representará mediante un `EventArgs` subclase.

-   A _clase anidada estático_ en Java es una _clase anidada_ en. NET.

-   Un _clase interna_ en Java es una _clase anidada_ con un constructor de instancia en C#.



## <a name="binding-scenarios"></a>Escenarios de enlace

Las siguientes guías de escenario de enlace le permite enlazar una biblioteca de Java (o bibliotecas) para la incorporación a la aplicación:

-   [Enlazar un. JAR](~/android/platform/binding-java-library/binding-a-jar.md) es un tutorial para crear bibliotecas de enlaces para **.jar** archivos.

-   [Enlazar un. AAR](~/android/platform/binding-java-library/binding-an-aar.md) es un tutorial para crear bibliotecas de enlaces para. Archivos AAR. Lea este tutorial para obtener información sobre cómo enlazar las bibliotecas de Android Studio.

-   [Enlace de un proyecto de biblioteca de Eclipse](~/android/platform/binding-java-library/binding-a-library-project.md) es un tutorial para crear bibliotecas de enlace de los proyectos de biblioteca de Android. Lea este tutorial para obtener información sobre cómo enlazar los proyectos de biblioteca de Eclipse Android.

-   [Personalizar los enlaces](~/android/platform/binding-java-library/customizing-bindings/index.md) explica cómo realizar modificaciones manuales en el enlace para resolver errores de compilación y dar forma a la API resultante para que resulte más "C#: al igual que".

-   [Solución de problemas de enlaces](~/android/platform/binding-java-library/troubleshooting-bindings.md) se enumeran escenarios comunes de errores de enlace, se explica las causas posibles y ofrece sugerencias para resolver estos errores.


## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con JNI](~/android/platform/java-integration/working-with-jni.md)
- [GAPI Metadata](http://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
- [Uso de bibliotecas nativas](~/android/platform/native-libraries.md)
