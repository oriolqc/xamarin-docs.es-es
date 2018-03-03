---
title: "Solución de problemas de enlaces"
description: "En este artículo se resume los varios errores comunes que pueden producirse al generar los enlaces, junto con las causas posibles y maneras sugeridas para resolverlos."
ms.topic: article
ms.prod: xamarin
ms.assetid: BB81FCCF-F7BF-4C78-884E-F02C49AA819A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: 84ef87f5ed84fcd0a9aa2504c52a0fec17404e1f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="troubleshooting-bindings"></a>Solución de problemas de enlaces

_En este artículo se resume los varios errores comunes que pueden producirse al generar los enlaces, junto con las causas posibles y maneras sugeridas para resolverlos._

<a name="OVERVIEW" />

## <a name="overview"></a>Información general

Enlace de una biblioteca de Android (un **.AAR y** o un **.jar**) archivo rara vez es un proceso sencillo; normalmente requiere un esfuerzo adicional para mitigar los problemas que son el resultado de las diferencias entre Java y. NET.
Estos problemas se impiden que Xamarin.Android enlace la biblioteca Android y se presentan como mensajes de error en el registro de compilación. Esta guía se proporcionan algunas sugerencias para solucionar los problemas, se muestran algunos de los problemas y escenarios más comunes y proporcionar soluciones posibles para el enlace correcto de la biblioteca de Android.

Al enlazar una biblioteca de Android existente, es necesario tener en cuenta los siguientes puntos:

- **Las dependencias externas para la biblioteca** &ndash; dependencias de Java Any requeridas por la biblioteca Android deben incluirse en el proyecto Xamarin.Android como un **ReferenceJar** o como un  **EmbeddedReferenceJar**.

- **El nivel de API de Android que la biblioteca de Android tiene como destino la** &ndash; no es posible "volver" el nivel de API de Android; Asegúrese de que el proyecto de enlace Xamarin.Android está destinado a la API de mismo nivel (o superior) como la biblioteca de Android.

- **La versión de Android JDK que se usó para empaquetar la biblioteca Android** &ndash; errores de enlace pueden producirse si la biblioteca Android se compiló con una versión diferente de JDK a la utilice por Xamarin.Android. Si es posible, vuelva a compilar la biblioteca Android con la misma versión de JDK que se usa por la instalación de Xamarin.Android.

El primer paso para solucionar problemas con el enlace en una biblioteca de Xamarin.Android consiste en habilitar [resultado del diagnóstico de MSBuild](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).
Después de habilitar la salida del diagnóstico, recompile el proyecto de enlace Xamarin.Android y examine el registro de compilación para encontrar pistas sobre las novedades de la causa del problema.

También puede resultar útil descompilar la biblioteca Android y examinar los tipos y métodos que Xamarin.Android está intentando enlazar. Este tema se trata con más detalle más adelante en esta guía.

<a name="DECOMPILING_AN_ANDROID_LIBRARY" />

## <a name="decompiling-an-android-library"></a>Descompilar una biblioteca de Android

Inspeccionar las clases y métodos de las clases de Java puede proporcionar información valiosa que le ayudará en una biblioteca de enlace.
[Interfaz gráfica de usuario de JD](http://jd.benow.ca/) es una utilidad gráfica que puede mostrar el código fuente Java desde la **clase** archivos contenidos en un archivo JAR. Se puede ejecutar como una aplicación independiente o como un complemento para IntelliJ o Eclipse.

Descompilar biblioteca Android abierto el **. JAR** archivo con el descompilador de Java. Si la biblioteca es un **. AAR** archivo, es necesario extraer el archivo **classes.jar** desde el archivo de almacenamiento. La siguiente es una captura de pantalla de ejemplo del uso de GUI JD para analizar el [Picasso](http://square.github.io/picasso/) JAR:

![Usar el descompilador de Java para analizar cómo picasso 2.5.2.jar](troubleshooting-bindings-images/troubleshoot-bindings-01.png)

Una vez que haya descompilado la biblioteca Android, examine el código fuente. Por lo general, busque:

- **Las clases que tienen las características de ofuscación** &ndash; características de clases ofuscadas incluyen:

    - El nombre de clase incluye una  **$** , es decir, **un .class $**
    - El nombre de clase está completamente en peligro de caracteres en minúsculas, es decir, **a.class**      

- **`import` instrucciones para las bibliotecas** &ndash; identificar la biblioteca sin referencia y agregar estas dependencias para el proyecto de enlace Xamarin.Android con un **acción de compilación** de **ReferenceJar**  o **EmbedddedReferenceJar**.

> [!NOTE]
> **Nota:** descompilar una biblioteca de Java puede prohibida o sujetas a restricciones legales en función de las leyes locales o la licencia en la que se publicó la biblioteca de Java. Si es necesario, dar de alta los servicios de un asesor jurídico antes de intentar descompilar una biblioteca de Java e inspeccionar el código fuente.

<a name="INSPECTING_API_XML" />

## <a name="inspect-apixml"></a>Inspeccionar API. XML

Como parte de la creación de un proyecto de enlace, Xamarin.Android generará un nombre de archivo XML **obj/Debug/api.xml**:

![Api.xml generado en obj/Debug](troubleshooting-bindings-images/troubleshoot-bindings-02.png)

Este archivo proporciona una lista de todas las API de Java que Xamarin.Android está intentando enlazar. El contenido de este archivo puede ayudar a identificar los tipos o métodos que faltan, duplicar el enlace. Aunque la inspección de este archivo es una tarea tediosa y requiere mucho tiempo, puede proporcionar para obtener pistas en lo que podrían estar causando los problemas de enlace. Por ejemplo, **api.xml** podría revelar que una propiedad está devolviendo un tipo apropiado, o que hay dos tipos que comparten el mismo nombre administrado.

<a name="KNOWN_ISSUES" />

## <a name="known-issues"></a>Problemas conocidos

Esta sección enumera algunos de los mensajes de error comunes o síntomas que mi se producen al intentar enlazar una biblioteca de Android.

<a name="PROBLEM_JAVA_VERSION_MISMATCH" />

### <a name="problem-java-version-mismatch"></a>Problema: Discrepancia de versiones de Java

En ocasiones, no se generarán tipos o bloqueos inesperados pueden producirse porque se están usando una versión más reciente o más antigua de Java en comparación con lo que la biblioteca que se compiló con. Vuelva a compilar la biblioteca Android con la misma versión de JDK que está usando el proyecto Xamarin.Android.

<a name="PROBLEM_AT_LEAST_ONE_JAVA_LIBRARY_IS_REQUIRED" />

### <a name="problem-at-least-one-java-library-is-required"></a>Problema: al menos una biblioteca de Java se requiere

Recibe el error "al menos una biblioteca de Java" es necesaria, aunque una. Se ha agregado el archivo JAR.

#### <a name="possible-causes"></a>Causas posibles:

Asegúrese de que la acción de compilación se establece en `EmbeddedJar`. Dado que hay varias acciones de compilación para. Archivos JAR (como `InputJar`, `EmbeddedJar`, `ReferenceJar` y `EmbeddedReferenceJar`), el generador de enlace no puede adivinar automáticamente cuál utilizar de forma predeterminada. Para obtener más información acerca de las acciones de compilación, consulte [acciones de compilación](~/android/platform/binding-java-library/index.md).

<a name="PROBLEM_BINDING_TOOLS_CANNOT_LOAD_THE_JAR_LIBRARY" />

### <a name="problem-binding-tools-cannot-load-the-jar-library"></a>Problema: Enlace de herramientas no se puede cargar el. Biblioteca JAR

El generador de la biblioteca de enlace no puede cargarse el. Biblioteca JAR.

#### <a name="possible-causes"></a>Causas posibles

Algunos. No se puede cargar las bibliotecas JAR que utilizar la ofuscación de código (a través de herramientas como Proguard) con las herramientas de Java. Puesto que nuestra herramienta hace uso de la reflexión de Java y el código de bytes ASM biblioteca de ingeniería, estas herramientas dependientes pueden rechazar las bibliotecas ofuscadas aunque pueden pasar herramientas de Android en tiempo de ejecución. La solución para este problema es enlazar manualmente estas bibliotecas en lugar de utilizar el generador de enlace.


<a name="PROBLEM_MISSING_C_TYPES_IN_GENERATED_OUTPUT_" />

### <a name="problem-missing-c-types-in-generated-output"></a>Problema: Faltan tipos de C# de salida generado.

El enlace **.dll** compilaciones errores pero algunos tipos de Java o el código fuente de C# generado no se compila debido a un error que indica que hay tipos que faltan.

#### <a name="possible-causes"></a>Causas posibles:

Este error puede producirse debido a varias razones, como se muestra a continuación:

-   La biblioteca que se está enlaza puede hacer referencia a una segunda biblioteca de Java. Si la API pública de la biblioteca de enlace utiliza los tipos de la segunda biblioteca, debe hacer referencia a un enlace para la segunda biblioteca administrado.

-   Es posible que una biblioteca insertada debido a la reflexión de Java, similar a la razón del error de carga de biblioteca anterior, haciendo que la carga inesperada de metadatos. Herramientas de Xamarin.Android actualmente no pueden resolver esta situación. En tal caso, la biblioteca debe enlazarse manualmente.

-   Se produjo un error en tiempo de ejecución de .NET 4.0 que no se pudieron cargar los ensamblados debería tener. Este problema se corrigió en el tiempo de ejecución de .NET 4.5.

-   Java permite derivar una clase pública de clase no públicos, pero esto no se admite en. NET. Puesto que el generador de enlace no genera los enlaces para las clases no públicos, las clases derivadas, como no se genera correctamente. Para solucionar este problema, quite la entrada de metadatos de las clases derivadas utilizando el nodo remove en **Metadata.xml**, o corregir los metadatos que está realizando la clase pública no es público. Aunque la última solución creará el enlace para que se compilará el código fuente C#, no debe usarse la clase no público.

    Por ejemplo:

    ```xml
    <attr path="/api/package[@name='com.some.package']/class[@name='SomeClass']"
        name="visibility">public</attr>
    ```

-   Herramientas que ofuscar las bibliotecas de Java pueden interferir con el generador de enlace de Xamarin.Android y su capacidad para generar clases contenedoras de C#. El fragmento de código siguiente muestra cómo actualizar **Metadata.xml** a unobfuscate un nombre de clase:

    ```xml
    <attr path="/api/package[@name='{package_name}']/class[@name='{name}']"
        name="obfuscated">false</attr>
    ```

### <a name="problem-generated-c-source-does-not-build-due-to-parameter-type-mismatch"></a>Problema: Origen de C# generados no se compila debido a la falta de coincidencia de tipo de parámetro

No se compila el código fuente de C# generado. Invalidar parámetro del método tipos no coinciden.

#### <a name="possible-causes"></a>Causas posibles:

Xamarin.Android incluye una serie de campos de Java que se asignan a las enumeraciones en los enlaces de C#. Pueden causar incompatibilidades de tipo en los enlaces generados. Para resolver este problema, las firmas de método creadas por el generador de enlace deben modificarse para usar las enumeraciones. Para más imformation, vea [corregir enumeraciones](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md).

### <a name="problem-noclassdeffounderror-in-packaging"></a>Problema: NoClassDefFoundError de empaquetado

`java.lang.NoClassDefFoundError` se produce en el paso de empaquetado.

#### <a name="possible-causes"></a>Causas posibles:

El motivo más probable de este error es que una biblioteca de Java obligatoria debe agregarse al proyecto de aplicación (**.csproj**). . Archivos JAR no se resuelven automáticamente. Un enlace de la biblioteca de Java no siempre se genera en un ensamblado de usuario que no existe en el dispositivo de destino o el emulador de Windows (como Google Maps **maps.jar**). Esto no es el caso de soporte técnico del proyecto de biblioteca de Android, como la biblioteca. JAR se incrusta en el archivo dll de biblioteca. Por ejemplo: [4288 de error](https://bugzilla.xamarin.com/show_bug.cgi?id=4288)

### <a name="problem-duplicate-custom-eventargs-types"></a>Problema: Duplicar tipos personalizados de EventArgs

Se produce un error en la compilación debido a tipos personalizados de EventArgs duplicados. Se produce un error similar al siguiente:

```shell
error CS0102: The type `Com.Google.Ads.Mediation.DismissScreenEventArgs' already contains a definition for `p0'
```

#### <a name="possible-causes"></a>Causas posibles:

Esto es porque hay algún conflicto entre los tipos de eventos que proceden de más de un tipo de "escucha" de interfaz que comparte los métodos que tienen nombres idénticos. Por ejemplo, si hay dos interfaces de Java tal como se muestra en el ejemplo siguiente, el generador crea `DismissScreenEventArgs` para ambos `MediationBannerListener` y `MediationInterstitialListener`, que puede producir el error.

```java
// Java:
public interface MediationBannerListener {
    void onDismissScreen(MediationBannerAdapter p0);
}
public interface MediationInterstitialListener {
    void onDismissScreen(MediationInterstitialAdapter p0);
}
```

Esto es así por diseño, por lo que se eviten todos los nombres largos en tipos de argumento de evento. Para evitar estos conflictos, se requiere alguna transformación de metadatos. Editar [ **Transforms\Metadata.xml** ](https://github.com/xamarin/monodroid-samples/blob/master/AdMob/AdMob/Transforms/Metadata.xml) y agregue un `argsType` atributo cualquiera de las interfaces (o en el método de interfaz):

```xml
<attr path="/api/package[@name='com.google.ads.mediation']/
        interface[@name='MediationBannerListener']/method[@name='onDismissScreen']"
        name="argsType">BannerDismissScreenEventArgs</attr>

<attr path="/api/package[@name='com.google.ads.mediation']/
        interface[@name='MediationInterstitialListener']/method[@name='onDismissScreen']"
        name="argsType">IntersitionalDismissScreenEventArgs</attr>

<attr path="/api/package[@name='android.content']/
        interface[@name='DialogInterface.OnClickListener']"
        name="argsType">DialogClickEventArgs</attr>
```

### <a name="problem-class-does-not-implement-interface-method"></a>Problema: La clase no implementa el método de interfaz

Se genera un mensaje de error que indica que una clase generada no implementa un método que es necesario para una interfaz que implementa la clase generada. Sin embargo, examinando el código generado, puede ver que el método se implementa.

Este es un ejemplo del error:

```shell
obj\Debug\generated\src\Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.cs(8,23):
error CS0738: 'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter' does not
implement interface member 'Oauth.Signpost.Http.IHttpRequest.Unwrap()'.
'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.Unwrap()' cannot implement
'Oauth.Signpost.Http.IHttpRequest.Unwrap()' because it does not have the matching
return type of 'Java.Lang.Object'
```

#### <a name="possible-causes"></a>Causas posibles:

Se trata de un problema que se produce con enlazar métodos de Java con tipos de valor devuelto covariantes. En este ejemplo, el método `Oauth.Signpost.Http.IHttpRequest.UnWrap()` necesita devolver `Java.Lang.Object`. Sin embargo, el método `Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.UnWrap()` tiene un tipo de valor devuelto de `HttpURLConnection`. Hay dos formas de solucionar este problema:

-   Agregue una declaración de clase parcial para `HttpURLConnectionRequestAdapter` e implemente explícitamente `IHttpRequest.Unwrap()`:

    ```csharp
    namespace Oauth.Signpost.Basic {
        partial class HttpURLConnectionRequestAdapter {
            Java.Lang.Object OauthSignpost.Http.IHttpRequest.Unwrap() {
                return Unwrap();
            }
        }
    }
    ```

-   Quite la covarianza de código de C# generado. Esto implica agregar la transformación siguiente **Transforms\Metadata.xml** lo que hará que el código C# generado a tener un tipo de valor devuelto de `Java.Lang.Object`:

    ```xml
    <attr
        path="/api/package[@name='oauth.signpost.basic']/class[@name='HttpURLConnectionRequestAdapter']/method[@name='unwrap']"
        name="managedReturn">Java.Lang.Object
    </attr>
    ```

### <a name="problem-name-collisions-on-inner-classes--properties"></a>Problema: Conflictos en las clases internas de nombres y propiedades

Visibilidad en conflicto en los objetos heredados.

En Java, no es necesario que una clase derivada tiene la misma visibilidad que su elemento primario. Java simplemente arreglaremos automáticamente. En C#, que tiene que ser explícito, por lo que necesita para asegurarse de que todas las clases de la jerarquía tienen visibilidad adecuado. En el ejemplo siguiente se muestra cómo cambiar un nombre de paquete de Java de `com.evernote.android.job` a `Evernote.AndroidJob`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

### <a name="problem-a-so-library-required-by-the-binding-is-not-loading"></a>Problema: Un **. SO** biblioteca requerido por el enlace no es cargar

Algunos proyectos de enlace también pueden depender de funcionalidad en un **. SO** biblioteca. Es posible que no se cargará automáticamente Xamarin.Android el **. SO** biblioteca. Cuando se ejecuta el código Java ajustado, se producirá un error Xamarin.Android realizar la llamada JNI y el mensaje de error _java.lang.UnsatisfiedLinkError: no se encontró el método nativo:_ aparecerá en la logcat fuera de la aplicación.

La solución para este problema consiste en cargar manualmente el **.so** biblioteca con una llamada a `Java.Lang.JavaSystem.LoadLibrary`. Por ejemplo si suponemos que un proyecto Xamarin.Android compartió biblioteca **libpocketsphinx_jni.so** incluido en el proyecto de enlace con una acción de compilación de **EmbeddedNativeLibrary**, el siguiente fragmento de código (que se ejecutan antes de usar la biblioteca compartida) se cargará el **. SO** biblioteca:

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

<a name=summary />

## <a name="summary"></a>Resumen

En este artículo se enumeran los problemas comunes de solución de problemas asociados con enlaces de Java y explica cómo resolverlos.


## <a name="related-links"></a>Vínculos relacionados

- [Proyectos de biblioteca](http://developer.android.com/tools/projects/index.html#LibraryProjects)
- [Trabajar con JNI](~/android/platform/java-integration/working-with-jni.md)
- [Habilitar la salida de diagnóstico](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)
- [Xamarin para desarrolladores de Android](~/android/get-started/java-developers.md)
- [JD-GUI](http://jd.benow.ca/)
