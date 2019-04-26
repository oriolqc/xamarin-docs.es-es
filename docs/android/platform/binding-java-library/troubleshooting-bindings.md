---
title: Solución de problemas de enlaces
description: En este artículo se resume a varios errores comunes que pueden producirse al generar los enlaces, junto con las causas posibles y sugiere formas de resolverlos.
ms.prod: xamarin
ms.assetid: BB81FCCF-F7BF-4C78-884E-F02C49AA819A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: b0bb7cbb6160865af5b1e40d40c7b999a8bd5ebc
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60956546"
---
# <a name="troubleshooting-bindings"></a>Solución de problemas de enlaces

_En este artículo se resume a varios errores comunes que pueden producirse al generar los enlaces, junto con las causas posibles y sugiere formas de resolverlos._


## <a name="overview"></a>Información general

Enlace de una biblioteca de Android (un **.aar** o un **.jar**) archivo rara vez es un proceso sencillo; normalmente requiere un esfuerzo adicional para mitigar los problemas que se derivan de las diferencias entre Java y. NET.
Estos problemas impedirá que el enlace de la biblioteca de Android Xamarin.Android y presentan como mensajes de error en el registro de compilación. Esta guía se proporcionan algunas sugerencias para solucionar los problemas, se muestran algunos de los problemas más comunes o escenarios y proporcionar soluciones posibles para el enlace correcto de la biblioteca de Android.

Al enlazar una biblioteca de Android existente, es necesario tener en cuenta los siguientes puntos:

- **Las dependencias externas de la biblioteca** &ndash; dependencias de Java Any requeridas por la biblioteca de Android deben incluirse en el proyecto Xamarin.Android como una **ReferenceJar** o como un  **EmbeddedReferenceJar**.

- **El nivel de API de Android que la biblioteca de Android tiene como destino** &ndash; no es posible "cambiar" el nivel de API Android; Asegúrese de que el proyecto Xamarin.Android de enlace está destinado a la API de misma nivel (o posterior) como la biblioteca de Android.

- **La versión de JDK Android que se usó para empaquetar la biblioteca de Android** &ndash; errores de enlace pueden producirse si la biblioteca de Android compilada con una versión diferente de JDK en uso en Xamarin.Android. Si es posible, vuelva a compilar la biblioteca de Android con la misma versión de JDK que se usa por la instalación de Xamarin.Android.

Es el primer paso para solucionar problemas relacionados con el enlace de una biblioteca de Xamarin.Android habilitar [salida de diagnóstico de MSBuild](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).
Después de habilitar la salida del diagnóstico, recompile el proyecto de enlace de Xamarin.Android y examine el registro de compilación para buscar pistas sobre lo que es la causa del problema.

También pueden resultar útil descompilar la biblioteca de Android y examinar los tipos y métodos que Xamarin.Android está intentando enlazar. Este tema se trata con más detalle más adelante en esta guía.


## <a name="decompiling-an-android-library"></a>Descompilación una biblioteca de Android

Inspeccionar las clases y métodos de las clases de Java puede proporcionar información valiosa que le ayudará en una biblioteca de enlace.
[Interfaz gráfica de usuario de JD](http://jd.benow.ca/) es una utilidad gráfica que se puede mostrar el código fuente de Java desde el **clase** archivos contenidos en un archivo JAR. Se puede ejecutar como una aplicación independiente o como un complemento de IntelliJ o Eclipse.

Para descompilar biblioteca Android abierto el **. JAR** archivo con el descompilador de Java. Si la biblioteca es un **. AAR** archivo, es necesario extraer el archivo **classes.jar** del archivo de almacenamiento. El siguiente es una captura de pantalla de ejemplo de uso JD GUI para analizar el [Picasso](http://square.github.io/picasso/) JAR:

![Usar el descompilador de Java para analizar cómo picasso 2.5.2.jar](troubleshooting-bindings-images/troubleshoot-bindings-01.png)

Una vez que haya descompilado la biblioteca de Android, examine el código fuente. Por lo general, busque:

- **Las clases que tienen las características de ofuscación** &ndash; características de ofuscación clases incluyen:

    - El nombre de clase incluye un **$**, es decir, **un .class $**
    - El nombre de clase completamente consta de caracteres en minúsculas, es decir, **a.class**      

- **`import` instrucciones para las bibliotecas sin referencia** &ndash; identificar la biblioteca sin referencia y agregará esas dependencias al proyecto de enlace de Xamarin.Android con un **acción de compilación** de **ReferenceJar**  o **EmbedddedReferenceJar**.

> [!NOTE]
> Descompilación una biblioteca de Java puede prohibida o sujetas a restricciones legales según las leyes locales o de la licencia en la que se publicó la biblioteca de Java. Si es necesario, dar de alta los servicios de un asesor jurídico antes de intentar descompilar una biblioteca de Java e inspeccionar el código fuente.


## <a name="inspect-apixml"></a>Inspeccione la API. XML

Como parte de la creación de un proyecto de enlace, Xamarin.Android generará un nombre de archivo XML **obj/Debug/api.xml**:

![Api.xml generado en obj/Debug](troubleshooting-bindings-images/troubleshoot-bindings-02.png)

Este archivo proporciona una lista de todas las API de Java que Xamarin.Android está tratando de enlace. El contenido de este archivo puede ayudar a identificar los tipos o métodos que faltan, enlace duplicado. Aunque la inspección de este archivo es tediosa y larga, puede proporcionar para obtener pistas sobre lo que podrían estar causando problemas de enlace. Por ejemplo, **api.xml** podría revelar que devuelve una propiedad de tipo inadecuado o que hay dos tipos que comparten el mismo nombre administrado.


## <a name="known-issues"></a>Problemas conocidos

Esta sección enumera algunos de los mensajes de error comunes o los síntomas que mi se producen al intentar enlazar una biblioteca de Android.


### <a name="problem-java-version-mismatch"></a>Problema: Discrepancia de versiones de Java

A veces no se generarán tipos o bloqueos imprevistos pueden producirse porque está usando ya sea una versión posterior o anterior de Java en comparación con lo que la biblioteca que se compiló con. Vuelva a compilar la biblioteca de Android con la misma versión de JDK que está usando el proyecto de Xamarin.Android.


### <a name="problem-at-least-one-java-library-is-required"></a>Problema: Se requiere al menos una biblioteca de Java

Recibe el error "al menos una biblioteca de Java es obligatoria,", aunque una. Se ha agregado el archivo JAR.

#### <a name="possible-causes"></a>Causas posibles:

Asegúrese de que la acción de compilación se establece en `EmbeddedJar`. Dado que hay varias acciones de compilación para. Archivos JAR (como `InputJar`, `EmbeddedJar`, `ReferenceJar` y `EmbeddedReferenceJar`), el generador de enlace no puede adivinar automáticamente cuál utilizar de forma predeterminada. Para obtener más información acerca de las acciones de compilación, véase [acciones de compilación](~/android/platform/binding-java-library/index.md).


### <a name="problem-binding-tools-cannot-load-the-jar-library"></a>Problema: Herramientas de enlace no se puede cargar el. Biblioteca JAR

El generador de biblioteca de enlace no se puede cargar el. Biblioteca JAR.

#### <a name="possible-causes"></a>Causas posibles

Algunos. No se puede cargar las bibliotecas JAR que utilizar la ofuscación de código (mediante herramientas como Proguard) mediante las herramientas de Java. Dado que nuestra herramienta hace uso de reflexión de Java y el código de bytes ASM biblioteca de ingeniería, esas herramientas dependientes pueden rechazar las bibliotecas de ofuscación aunque pueden pasar las herramientas de Android en tiempo de ejecución. Para solucionar esto es enlazar manualmente estas bibliotecas en lugar de usar el generador de enlace.



### <a name="problem-missing-c-types-in-generated-output"></a>Problema: Falta C# tipos en la salida generada.

El enlace **.dll** compilaciones, pero pierde algunos tipos de Java o generado C# origen no se compila debido a un error que indica que hay tipos que faltan.

#### <a name="possible-causes"></a>Causas posibles:

Este error puede producirse debido a varias razones, como se muestra a continuación:

-   La biblioteca que se está enlazaba puede hacer referencia a una biblioteca de Java de segundo. Si la API pública de la biblioteca dependiente usa tipos de la biblioteca de segundo, debe hacer referencia a un enlace para la segunda biblioteca administrado.

-   Es posible que una biblioteca insertada debido a la reflexión de Java, similar a la razón del error de carga de biblioteca anterior, causando la carga de metadatos inesperada. Las herramientas de Xamarin.Android actualmente no pueden resolver esta situación. En tal caso, la biblioteca debe enlazarse manualmente.

-   Se produjo un error en tiempo de ejecución de .NET 4.0 que no se pudieron cargar los ensamblados debe tener. Este problema se corrigió en el tiempo de ejecución de .NET 4.5.

-   Java permite derivar una clase pública de clase no pública, pero esto no se admite en. NET. Dado que el generador de enlace no generan enlaces para las clases que no son públicos, las clases derivadas, como no se generaron correctamente. Para solucionar este problema, quite la entrada de metadatos para las clases derivadas utilizando el nodo remove en **Metadata.xml**, o corregir los metadatos que está realizando la clase pública que no son públicos. Aunque la última solución creará el enlace para que el C# compilará el código fuente, no se debe usar la clase no pública.

    Por ejemplo:

    ```xml
    <attr path="/api/package[@name='com.some.package']/class[@name='SomeClass']"
        name="visibility">public</attr>
    ```

-   Las herramientas que ofuscar las bibliotecas de Java pueden interferir con el generador de enlace de Xamarin.Android y su capacidad para generar C# clases contenedoras. El fragmento de código siguiente muestra cómo actualizar **Metadata.xml** a unobfuscate un nombre de clase:

    ```xml
    <attr path="/api/package[@name='{package_name}']/class[@name='{name}']"
        name="obfuscated">false</attr>
    ```

### <a name="problem-generated-c-source-does-not-build-due-to-parameter-type-mismatch"></a>Problema: Genera C# origen no se compila debido a falta de coincidencia de tipo de parámetro

Generado C# no genera código fuente. Invalidar parámetro del método tipos no coinciden.

#### <a name="possible-causes"></a>Causas posibles:

Xamarin.Android incluye una serie de campos de Java que se asignan a las enumeraciones en el C# enlaces. Pueden causar incompatibilidades de tipo en los enlaces generados. Para resolver este problema, las firmas de método creadas desde el generador de enlace deben modificarse para usar las enumeraciones. Para obtener más información, consulte [corregir enumeraciones](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md).

### <a name="problem-noclassdeffounderror-in-packaging"></a>Problema: NoClassDefFoundError de empaquetado

`java.lang.NoClassDefFoundError` se produce en el paso de empaquetado.

#### <a name="possible-causes"></a>Causas posibles:

El motivo más probable de este error es que una biblioteca de Java obligatoria debe agregarse al proyecto de aplicación (**.csproj**). . Archivos JAR no se resuelven automáticamente. Un enlace de la biblioteca de Java no siempre se genera en un ensamblado de usuario que no existe en el emulador o dispositivo de destino (como Google Maps **maps.jar**). Esto no es el caso de soporte técnico del proyecto de biblioteca de Android, como la biblioteca. Archivo JAR se incrusta en el archivo dll de biblioteca. Por ejemplo: [Error 4288](https://bugzilla.xamarin.com/show_bug.cgi?id=4288)

### <a name="problem-duplicate-custom-eventargs-types"></a>Problema: Tipos de EventArgs personalizados duplicados

Se produce un error en la compilación debido a los tipos de EventArgs personalizados duplicados. Se produce un error similar al siguiente:

```shell
error CS0102: The type `Com.Google.Ads.Mediation.DismissScreenEventArgs' already contains a definition for `p0'
```

#### <a name="possible-causes"></a>Causas posibles:

Esto es porque hay algún conflicto entre los tipos de eventos que proceden de más de un tipo de "escucha" de interfaz que comparte los métodos que tienen nombres idénticos. Por ejemplo, si hay dos interfaces de Java, tal como se muestra en el ejemplo siguiente, el generador crea `DismissScreenEventArgs` para ambos `MediationBannerListener` y `MediationInterstitialListener`, lo que provocará el error.

```java
// Java:
public interface MediationBannerListener {
    void onDismissScreen(MediationBannerAdapter p0);
}
public interface MediationInterstitialListener {
    void onDismissScreen(MediationInterstitialAdapter p0);
}
```

Esto es así por diseño para que se evitan los nombres largos en tipos de argumento de evento. Para evitar estos conflictos, se requiere alguna transformación de metadatos. Editar [ **Transforms\Metadata.xml** ](https://github.com/xamarin/monodroid-samples/blob/master/AdMob/AdMob/Transforms/Metadata.xml) y agregue un `argsType` atributo en cualquiera de las interfaces (o en el método de interfaz):

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

Se produce un mensaje de error que indica que una clase generada no implementa un método que se requiere para una interfaz que implementa la clase generada. Sin embargo, examinar el código generado, puede ver que se implementa el método.

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

Se trata de un problema que ocurre con los métodos de Java de enlace con tipos de valor devuelto covariante. En este ejemplo, el método `Oauth.Signpost.Http.IHttpRequest.UnWrap()` necesita devolver `Java.Lang.Object`. Sin embargo, el método `Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.UnWrap()` tiene un tipo de valor devuelto de `HttpURLConnection`. Hay dos formas de solucionar este problema:

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

-   Quitar la covarianza de generado C# código. Esto implica agregar la siguiente transformación a **Transforms\Metadata.xml** lo que hará que el generado C# código para tener un tipo de valor devuelto de `Java.Lang.Object`:

    ```xml
    <attr
        path="/api/package[@name='oauth.signpost.basic']/class[@name='HttpURLConnectionRequestAdapter']/method[@name='unwrap']"
        name="managedReturn">Java.Lang.Object
    </attr>
    ```

### <a name="problem-name-collisions-on-inner-classes--properties"></a>Problema: Conflictos en las clases internas de nombres y propiedades

Visibilidad en conflicto en los objetos heredados.

En Java, no es necesario que una clase derivada tiene la misma visibilidad que su elemento primario. Java soluciona justo para usted. En C#, que tiene que ser explícito, por lo que deberá asegurarse de que todas las clases de la jerarquía tienen visibilidad adecuada. El ejemplo siguiente muestra cómo cambiar el nombre de un paquete de Java de `com.evernote.android.job` a `Evernote.AndroidJob`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

### <a name="problem-a-so-library-required-by-the-binding-is-not-loading"></a>Problema: Un **.so** biblioteca requerido por el enlace está cargando no

Algunos proyectos de enlace también pueden depender de funcionalidad en un **.so** biblioteca. Es posible que Xamarin.Android no se cargará automáticamente la **.so** biblioteca. Cuando se ejecuta el código Java ajustado, se producirá un error en Xamarin.Android para que el mensaje de error y de la llamada JNI _java.lang.UnsatisfiedLinkError: No se encontró el método nativo:_ aparecerá en el logcat fuera de la aplicación.

Corregir este problema consiste en cargar manualmente el **.so** biblioteca con una llamada a `Java.Lang.JavaSystem.LoadLibrary`. Por ejemplo suponiendo que la biblioteca compartida por un proyecto de Xamarin.Android **libpocketsphinx_jni.so** incluido en el proyecto de enlace con una acción de compilación **EmbeddedNativeLibrary**, el siguiente fragmento de código (se ejecutará antes de usar la biblioteca compartida) se cargará el **.so** biblioteca:

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="summary"></a>Resumen

En este artículo, se enumeran problemas comunes de solución de problemas asociadas con los enlaces de Java y se explica cómo resolverlos.


## <a name="related-links"></a>Vínculos relacionados

- [Proyectos de biblioteca](https://developer.android.com/tools/projects/index.html#LibraryProjects)
- [Trabajo con JNI](~/android/platform/java-integration/working-with-jni.md)
- [Habilitar la salida de diagnóstico](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)
- [Xamarin para desarrolladores de Android](~/android/get-started/java-developers.md)
- [JD-GUI](http://jd.benow.ca/)
