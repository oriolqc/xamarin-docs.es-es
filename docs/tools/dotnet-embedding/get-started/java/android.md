---
title: Introducción a Android
description: Este documento describe cómo empezar a usar la inserción de .NET con Android. Describe la instalación de inserción de .NET, crear un proyecto de biblioteca de Android, mediante la salida generada en un proyecto de Android Studio y otras consideraciones.
ms.prod: xamarin
ms.assetid: 870F0C18-A794-4C5D-881B-64CC78759E30
author: lobrien
ms.author: laobri
ms.date: 03/28/2018
ms.openlocfilehash: e60892edfcf73f3e7cada8923e16bcc1be2c203e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121391"
---
# <a name="getting-started-with-android"></a>Introducción a Android

Además de los requisitos de la [Introducción a Java](~/tools/dotnet-embedding/get-started/java/index.md) guía también tendrá que:

* [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/) o posterior
* [Android Studio 3.x](https://developer.android.com/studio/index.html) con Java 1.8

Información general sobre, se hará lo siguiente:

* Crear un C# proyecto de biblioteca de Android
* Instalación de inserción de .NET a través de NuGet
* Ejecutar la inserción de .NET en el ensamblado de biblioteca de Android
* Utilice el archivo AAR generado en un proyecto de Java en Android Studio

## <a name="create-an-android-library-project"></a>Crear un proyecto de biblioteca de Android

Abra Visual Studio para Windows o Mac, cree un nuevo proyecto de biblioteca de clases de Android, asígnele el nombre **hello de csharp**y guárdelo en **~/Projects/hello-from-csharp** o **%USERPROFILE%\ Projects\hello de csharp**.

Agregue una nueva actividad de Android denominado **HelloActivity.cs**, seguido de un diseño de Android en **Resource/layout/hello.axml**.

Agregue un nuevo `TextView` al diseño y cambie el texto a algo divertido.

El origen de diseño debe tener un aspecto similar al siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <TextView
        android:text="Hello from C#!"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center" />
</LinearLayout>
```

En su actividad, asegúrese de que se está llamando a `SetContentView` con el nuevo diseño:

```csharp
[Activity(Label = "HelloActivity"),
    Register("hello_from_csharp.HelloActivity")]
public class HelloActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        SetContentView(Resource.Layout.hello);
    }
}
```

> [!NOTE]
> No olvide el `[Register]` atributo. Para obtener más información, consulte [limitaciones](#current-limitations-on-android).

Compile el proyecto. El ensamblado resultante se guardará en `bin/Debug/hello-from-csharp.dll`.

## <a name="installing-net-embedding-from-nuget"></a>Instalación de inserción de .NET de NuGet

Siga estas [instrucciones](~/tools/dotnet-embedding/get-started/install/install.md) para instalar y configurar la inserción de .NET para el proyecto.

La invocación del comando, debe configurar tendrá este aspecto:

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

```shell
mono '${SolutionDir}/packages/Embeddinator-4000.0.4.0.0/tools/Embeddinator-4000.exe' '${TargetPath}' --gen=Java --platform=Android --outdir='${SolutionDir}/output' -c
```

#### <a name="visual-studio-2017"></a>Visual Studio 2017

```shell
set E4K_OUTPUT="$(SolutionDir)output"
if exist %E4K_OUTPUT% rmdir /S /Q %E4K_OUTPUT%
"$(SolutionDir)packages\Embeddinator-4000.0.2.0.80\tools\Embeddinator-4000.exe" "$(TargetPath)" --gen=Java --platform=Android --outdir=%E4K_OUTPUT% -c
```

## <a name="use-the-generated-output-in-an-android-studio-project"></a>Usar la salida generada en un proyecto de Android Studio

1. Abra Android Studio y cree un nuevo proyecto con un **actividad vacía**.
2. Haga doble clic en su **aplicación** módulo y elija **New > módulo**.
3. Seleccione **importación. JAR /. Paquete AAR**.
4. Utilice el Explorador de directorios para buscar **~/Projects/hello-from-csharp/output/hello_from_csharp.aar** y haga clic en **finalizar**.

![Importación AAR en Android Studio](android-images/androidstudioimport.png)

Esto copiará el archivo AAR en un nuevo módulo denominado **hello_from_csharp**.

![Proyecto de Android Studio](android-images/androidstudioproject.png)

Para usar el nuevo módulo de su **aplicación**, haga clic en y elija **abrir Configuración del módulo**. En el **dependencias** pestaña, agregue un nuevo **dependencias de módulo** y elija **: hello_from_csharp**.

![Dependencias de Android Studio](android-images/androidstudiodependencies.png)

En su actividad, agregue un nuevo `onResume` método y utilizar el siguiente código para iniciar el C# actividad:

```java
import hello_from_csharp.*;

public class MainActivity extends AppCompatActivity {
    //... Other stuff here ...
    @Override
    protected void onResume() {
        super.onResume();

        Intent intent = new Intent(this, HelloActivity.class);
        startActivity(intent);
    }
}
```

### <a name="assembly-compression-important"></a>Compresión de ensamblado (*importante*)

Un cambio adicional es necesario para la inserción de .NET en el proyecto de Android Studio.

Abra la aplicación **build.gradle** archivo y agregue el siguiente cambio:

```groovy
android {
    // ...
    aaptOptions {
        noCompress 'dll'
    }
}
```

Xamarin.Android actualmente carga ensamblados .NET directamente desde el APK, pero requiere que los ensamblados que no se comprimen.

Si no tiene esta configuración, se bloqueará al iniciarse la aplicación e imprimir algo parecido a esto en la consola:

```shell
com.xamarin.hellocsharp A/monodroid: No assemblies found in '(null)' or '<unavailable>'. Assuming this is part of Fast Deployment. Exiting...
```

## <a name="run-the-app"></a>Ejecutar la aplicación

Cuando inicie la aplicación:

![Hola desde C# ejemplo en ejecución en el emulador](android-images/hello-from-csharp-android.png)

Tenga en cuenta qué ha ocurrido aquí:

* Tenemos un C# (clase), `HelloActivity`, que las subclases Java
* Tenemos los archivos de recursos de Android
* Hemos usado desde Java en Android Studio

Para que este ejemplo funcione, todos los archivos siguientes se configuran en el APK final:

* Xamarin.Android se configura al iniciar la aplicación
* Los ensamblados de .NET que se incluye en **activos/ensamblados**
* **AndroidManifest.xml** modificaciones para su C# actividades, etcetera.
* Recursos de Android y activos de bibliotecas de .NET
* [Contenedores invocables Android](~/android/platform/java-integration/android-callable-wrappers.md) para cualquier `Java.Lang.Object` subclase

Si busca un tutorial adicional, consulte el vídeo siguiente, que muestra la incrustación de Charles [demostración de pintura con los dedos](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint/) en un proyecto de Android Studio:

[![Embeddinator-4000 para Android](https://img.youtube.com/vi/ZVcrXUpCNpI/0.jpg)](https://www.youtube.com/watch?v=ZVcrXUpCNpI)

## <a name="using-java-18"></a>Uso de Java 1.8

A partir de este artículo, la mejor opción es usar Android Studio 3.0 ([descargar aquí](https://developer.android.com/studio/index.html)).

Para habilitar Java 1.8 en el módulo de aplicación **build.gradle** archivo:

```groovy
android {
    // ...
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
```

También puede tener un vistazo a un [proyecto de prueba de Android Studio](https://github.com/mono/Embeddinator-4000/blob/master/tests/android/app/build.gradle) para obtener más detalles. 

Si desea usar estable 2.3. x de Android Studio, tendrá que habilitar la cadena de herramientas de Jack en desuso:

```groovy
android {
    // ..
    defaultConfig {
        // ...
        jackOptions.enabled true
    }
}
```

## <a name="current-limitations-on-android"></a>Limitaciones actuales en Android

En este momento, si deriva una subclase `Java.Lang.Object`, Xamarin.Android generará el código auxiliar de Java (Android Callable Wrapper) en lugar de insertar. NET. Por este motivo, debe seguir las mismas reglas para la exportación C# a Java como Xamarin.Android. Por ejemplo:

```csharp
[Register("mono.embeddinator.android.ViewSubclass")]
public class ViewSubclass : TextView
{
    public ViewSubclass(Context context) : base(context) { }

    [Export("apply")]
    public void Apply(string text)
    {
        Text = text;
    }
}
```

* `[Register]` es necesario para asignar a un nombre de paquete de Java deseado
* `[Export]` es necesario para que un método sea visible para Java

Podemos usar `ViewSubclass` en Java de este modo:

```java
import mono.embeddinator.android.ViewSubclass;
//...
ViewSubclass v = new ViewSubclass(this);
v.apply("Hello");
```

Obtenga más información sobre [integración de Java con Xamarin.Android](~/android/platform/java-integration/index.md).

## <a name="multiple-assemblies"></a>Varios ensamblados

Incrustación de un único ensamblado es sencillo; Sin embargo, resulta mucho más probable que tenga más de un C# ensamblado. Muchas veces tendrá dependencias de paquetes de NuGet, como las bibliotecas de compatibilidad con Android o Google Play Services que complican aún más las cosas.

Esto hace que un dilema, puesto que la inserción de .NET debe incluir muchos tipos de archivos en el último AAR como:

* Activos de Android
* Recursos de Android
* Bibliotecas nativas de Android
* Código fuente java de Android

Probablemente no desea incluir estos archivos de la biblioteca de compatibilidad con Android o Google Play Services en su AAR, pero prefiere utilizar la versión oficial de Google en Android Studio.

Este es el enfoque recomendado:

* Pasar la inserción de .NET cualquier ensamblado que posea (dispone de la fuente) y desea llamar desde Java
* Pasar la inserción de .NET cualquier ensamblado que necesite activos de Android, las bibliotecas nativas o recursos desde
* Agregar las dependencias de Java como Android admiten la biblioteca o Google Play Services en Android Studio

Por lo que podría ser el comando:

```shell
mono Embeddinator-4000.exe --gen=Java --platform=Android -c -o output YourMainAssembly.dll YourDependencyA.dll YourDependencyB.dll
```

Debe excluir cualquier cosa desde NuGet, a menos que descubra contiene activos de Android, recursos, etc. que necesitará en el proyecto de Android Studio. También puede omitir las dependencias que no es necesario llamar a desde Java y el vinculador _debe_ incluyen las partes de la biblioteca que se necesitan.

Para agregar las dependencias de Java necesarios en Android Studio, su **build.gradle** archivo podría ser similar a:

```groovy
dependencies {
    // ...
    compile 'com.android.support:appcompat-v7:25.3.1'
    compile 'com.google.android.gms:play-services-games:11.0.4'
    // ...
}
```

## <a name="further-reading"></a>Información adicional

* [Devoluciones de llamada en Android](~/tools/dotnet-embedding/android/callbacks.md)
* [Investigación preliminar de Android](~/tools/dotnet-embedding/android/index.md)
* [Limitaciones de incrustación de .NET](~/tools/dotnet-embedding/limitations.md)
* [En el proyecto de código abierto](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Las descripciones y códigos de error](~/tools/dotnet-embedding/errors.md)

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Weather (Android)](https://github.com/jamesmontemagno/embeddinator-weather)
