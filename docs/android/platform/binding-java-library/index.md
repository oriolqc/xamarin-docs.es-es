---
title: Enlace de una biblioteca Java
description: La Comunidad de Android tiene muchas bibliotecas de Java que puede usar en la aplicación; Esta guía explica cómo incorporar bibliotecas de Java en su aplicación de Xamarin.Android mediante la creación de una biblioteca de enlaces.
ms.prod: xamarin
ms.assetid: B39FF1D5-69C3-8A76-D268-C227A23C9485
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/01/2017
ms.openlocfilehash: 016ac7269f334f6df7fba9635897b9608f459284
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970218"
---
# <a name="binding-a-java-library"></a>Enlace de una biblioteca Java

_La Comunidad de Android tiene muchas bibliotecas de Java que puede usar en la aplicación; Esta guía explica cómo incorporar bibliotecas de Java en su aplicación de Xamarin.Android mediante la creación de una biblioteca de enlaces._

## <a name="overview"></a>Información general

El ecosistema de bibliotecas de terceros para Android es masivo. Por este motivo, con frecuencia tiene sentido usar una biblioteca de Android existente que to crear uno nuevo. Xamarin.Android ofrece dos formas de usar estas bibliotecas:

-   Crear un *biblioteca de enlaces* que ajusta automáticamente la biblioteca con C# de código a través de contenedores, por lo que puede invocar Java C# llamadas.

-   Use la *Java Native Interface* (*JNI*) para invocar directamente las llamadas en el código de biblioteca de Java. JNI es un marco de programación que permite que el código de Java llamar y ser llamados por las aplicaciones nativas o bibliotecas.

Esta guía explica la primera opción: cómo crear un *biblioteca de enlaces* que contiene uno o más bibliotecas de Java existentes en un ensamblado que se puede vincular a en la aplicación. Para obtener más información sobre el uso de JNI, consulte [trabajo con JNI](~/android/platform/java-integration/working-with-jni.md).

Xamarin.Android implementa enlaces mediante *administrados contenedores RCW* (*MCW*). MCW es un puente JNI que sirve al código administrado necesita invocar código de Java. Contenedores invocables administrados también proporcionan compatibilidad para la creación de subclases de tipos de Java y para invalidar los métodos virtuales en tipos de Java. Del mismo modo, siempre que el código de tiempo de ejecución de Android (ART) desea invocar código administrado, lo hace a través de otro puente JNI conocido como contenedores RCW Android (ACW). Esto [arquitectura](~/android/internals/architecture.md) se ilustra en el diagrama siguiente:

[![Arquitectura de puente JNI de Android](images/architecture.png)](images/architecture.png#lightbox)

Una biblioteca de enlaces es un ensamblado que contiene los contenedores administrados para tipos Java. Por ejemplo, este es un tipo de Java, `MyClass`, que va a incluir en una biblioteca de enlaces:

```java
package com.xamarin.mycode;

public class MyClass
{
    public String myMethod (int i) { ... }
}
```

Después de que se genere una biblioteca de enlaces para el **.jar** que contiene `MyClass`, podemos crear una instancia y llamar a métodos en ella desde C#:

```csharp
var instance = new MyClass ();

string result = instance.MyMethod (42);
```

Para crear esta biblioteca de enlaces, usa el Xamarin.Android *biblioteca de enlaces de Java* plantilla. El proyecto resultante de enlace crea un ensamblado .NET con las clases MCW, **.jar** los archivos y recursos para los proyectos de biblioteca de Android incrustados en él. También puede crear bibliotecas de enlaces para el archivo Android (. Archivos AAR) y proyectos de biblioteca de Android de Eclipse. Haciendo referencia al ensamblado de biblioteca de enlaces DLL resultante, puede volver a usar una biblioteca de Java existente en el proyecto de Xamarin.Android.

Cuando se hace referencia a tipos en la biblioteca de enlace, debe usar el espacio de nombres de la biblioteca de enlace. Normalmente, se agrega un `using` la directiva en la parte superior de su C# archivos de origen que es la versión de espacio de nombres .NET del nombre del paquete de Java. Por ejemplo, si el nombre del paquete de Java para su límite **.jar** es el siguiente:

```csharp
com.company.package
```

A continuación, tendría que poner el siguiente `using` instrucción en la parte superior de su C# archivos de origen a tipos en el límite de acceso **.jar** archivo:

```csharp
using Com.Company.Package;
```


Al enlazar una biblioteca de Android existente, es necesario que tenga en cuenta los siguientes puntos:

* **¿Existen dependencias externas para la biblioteca?** &ndash; Las dependencias de Java necesarias en la biblioteca de Android deben incluirse en el proyecto Xamarin.Android como una **ReferenceJar** o como un **EmbeddedReferenceJar**. Los ensamblados nativos deben agregarse al proyecto de enlace como un **EmbeddedNativeLibrary**.  

* **¿Qué versión de la API de Android no Android destino de la biblioteca?** &ndash; No es posible "cambiar" el nivel de API de Android; Asegúrese de que el proyecto Xamarin.Android de enlace está destinado a la API de misma nivel (o posterior) como la biblioteca de Android.

* **¿Qué versión de JDK se usó para compilar la biblioteca?** &ndash; Errores de enlace pueden producirse si la biblioteca de Android se compiló con una versión diferente de JDK que usa xamarin.Android. Si es posible, vuelva a compilar la biblioteca de Android con la misma versión de JDK que se usa por la instalación de Xamarin.Android.


## <a name="build-actions"></a>Acciones de compilación

Cuando se crea una biblioteca de enlaces, establece *acciones de compilación* en el **.jar** o. Archivos AAR que incorporar a su proyecto de biblioteca de enlaces &ndash; cada acción de compilación determina cómo el **.jar** o. Archivo AAR se incrusta en (o al que hace referencia) la biblioteca de enlaces. En la lista siguiente se resume estas acciones de compilación:

* `EmbeddedJar` &ndash; Incrusta el **.jar** en la DLL de biblioteca de enlaces resultante como un recurso incrustado. Esto es el más sencillo y acción de compilación usadas con frecuencia la mayoría. Utilice esta opción cuando desee la **.jar** automáticamente compilan en código de bytes y empaquetado en la biblioteca de enlaces.

* `InputJar` &ndash; No incrusta el **.jar** en la biblioteca de enlaces resultante. ARCHIVO DLL. La biblioteca de enlaces. Archivo DLL tiene una dependencia en esta **.jar** en tiempo de ejecución. Use esta opción si no desea incluir el **.jar** en la biblioteca de enlaces (por ejemplo, por motivos de licencias). Si usa esta opción, debe asegurarse de que la entrada **.jar** está disponible en el dispositivo que ejecuta la aplicación.

* `LibraryProjectZip` &ndash; Incrusta un. Archivo AAR en la biblioteca de enlaces resultante. ARCHIVO DLL. Esto es similar a EmbeddedJar, salvo que puede tener acceso a los recursos (así como código) en el límite. Archivo AAR. Use esta opción si desea incrustar una. AAR a la biblioteca de enlaces.

* `ReferenceJar` &ndash; Especifica una referencia **.jar**: una referencia **.jar** es un **.jar** que uno de su límite **.jar** o. Archivos AAR depende. Esta referencia **.jar** sólo se utiliza para satisfacer las dependencias de tiempo de compilación. Cuando se usa esta acción de compilación, C# enlaces no se crean para la referencia **.jar** y no se incrustan en la biblioteca de enlaces resultante. ARCHIVO DLL. Use esta opción si va a realizar una biblioteca de enlaces para la referencia **.jar** pero aún no lo hecho aún. Esta acción de compilación es útil para empaquetar varios **.jar**s (o. AARs) en varias bibliotecas de enlaces interdependientes.

* `EmbeddedReferenceJar` &ndash; Inserta una referencia **.jar** en la biblioteca de enlaces resultante. ARCHIVO DLL. Use esta acción de compilación cuando desea crear C# enlaces para la entrada **.jar** (o). AAR) y todos su referencia **.jar**(s) de la biblioteca de enlaces.

* `EmbeddedNativeLibrary` &ndash; Incrusta nativo **.so** en el enlace. Esta acción de compilación se utiliza para **.so** archivos requeridos por la **.jar** archivo que se está enlazando. Puede que sea necesario cargar manualmente el **.so** biblioteca antes de ejecutar el código de la biblioteca de Java. Esto se describe a continuación.

Estas acciones se explican con más detalle en las siguientes guías de compilación.

Además, se utilizan las siguientes acciones de compilación para ayudar a importar la documentación de la API de Java y convertirlos en C# documentación XML:

* `JavaDocJar` se usa para que apunte al archivo Jar de Javadoc para una biblioteca de Java que se ajusta a un estilo de paquete de Maven (normalmente `FOOBAR-javadoc**.jar**`).
* `JavaDocIndex` se usa para apuntar a `index.html` archivo dentro de la documentación de referencia de API HTML.
* `JavaSourceJar` se utiliza para complementar `JavaDocJar`para generar primero JavaDoc de orígenes y, a continuación, tratar los resultados como `JavaDocIndex`, para una biblioteca de Java que se ajusta a un experto en paquete estilo (normalmente `FOOBAR-sources**.jar**`).

La documentación de API debe ser el doclet predeterminado desde Java8, Java7 o Java6 SDK (son todos diferente formato), o el estilo DroidDoc.

## <a name="including-a-native-library-in-a-binding"></a>Incluir una biblioteca nativa en un enlace

Puede ser necesario incluir un **.so** biblioteca en un proyecto Xamarin.Android de enlace como parte de una biblioteca de Java de enlace. Cuando se ejecuta el código Java ajustado, se producirá un error en Xamarin.Android para que el mensaje de error y de la llamada JNI _java.lang.UnsatisfiedLinkError: No se encontró el método nativo:_ aparecerá en el logcat fuera de la aplicación.

Corregir este problema consiste en cargar manualmente el **.so** biblioteca con una llamada a `Java.Lang.JavaSystem.LoadLibrary`. Por ejemplo suponiendo que la biblioteca compartida por un proyecto de Xamarin.Android **libpocketsphinx_jni.so** incluido en el proyecto de enlace con una acción de compilación **EmbeddedNativeLibrary**, el siguiente fragmento de código (se ejecutará antes de usar la biblioteca compartida) se cargará el **.so** biblioteca:

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="adapting-java-apis-to-ceparsl"></a>Adaptar las API de Java a C&eparsl;

El generador de enlace de Xamarin.Android cambiará algunas expresiones de Java y patrones para que coincida con los patrones. NET. La lista siguiente describe cómo Java se asigna a C#/.NET:

-   _Métodos de captador o establecedor_ en Java son _propiedades_ en. NET.

-   _Campos_ en Java son _propiedades_ en. NET.

-   _Interfaces de los agentes de escucha/Listener_ en Java son _eventos_ en. NET. Los parámetros de métodos en las interfaces de devolución de llamada se representarán mediante un `EventArgs` subclase.

-   Un _clases anidadas estáticas_ en Java es una _clase anidada_ en. NET.

-   Un _clase interna_ en Java es una _clase anidada_ con un constructor de instancia en C#.



## <a name="binding-scenarios"></a>Escenarios de enlace

Las siguientes guías de escenario de enlace le permite enlazar una biblioteca de Java (o bibliotecas) para su incorporación a la aplicación:

-   [Enlace un. JAR](~/android/platform/binding-java-library/binding-a-jar.md) es un tutorial para crear bibliotecas de enlaces para **.jar** archivos.

-   [Enlace un. AAR](~/android/platform/binding-java-library/binding-an-aar.md) es un tutorial para crear bibliotecas de enlaces para. Archivos AAR. Leer este tutorial para aprender a enlazar las bibliotecas de Android Studio.

-   [Enlace de un proyecto de biblioteca de Eclipse](~/android/platform/binding-java-library/binding-a-library-project.md) es un tutorial para crear bibliotecas de enlace de proyectos de biblioteca de Android. Leer este tutorial para aprender a enlazar los proyectos de biblioteca de Android de Eclipse.

-   [Personalización de enlaces](~/android/platform/binding-java-library/customizing-bindings/index.md) se explica cómo realizar modificaciones manuales en el enlace para resolver errores de compilación y dar forma a la API resultante para que resulte más "C#-como".

-   [Solución de problemas de enlaces](~/android/platform/binding-java-library/troubleshooting-bindings.md) encontrará escenarios comunes de error de enlace, se explican las causas posibles y ofrece sugerencias para resolver estos errores.


## <a name="related-links"></a>Vínculos relacionados

- [Trabajo con JNI](~/android/platform/java-integration/working-with-jni.md)
- [Metadatos GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
- [Uso de bibliotecas nativas](~/android/platform/native-libraries.md)
