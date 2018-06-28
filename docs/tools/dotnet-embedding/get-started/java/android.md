---
title: Introducción a Android
description: Este documento describe cómo empezar a usar la incrustación de .NET con Android. Se trata de instalar .NET incrustar, crear un proyecto de biblioteca de Android, con el resultado generado en un proyecto de Android Studio y otras consideraciones.
ms.prod: xamarin
ms.assetid: 870F0C18-A794-4C5D-881B-64CC78759E30
author: topgenorth
ms.author: toopge
ms.date: 03/28/2018
ms.openlocfilehash: 0bb27df901d306e97151cfeaad5c26130508e5b1
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066486"
---
# <a name="getting-started-with-android"></a>Introducción a Android

Además de los requisitos de la [introducción con Java](~/tools/dotnet-embedding/get-started/java/index.md) guía también necesitará:

* [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/) o posterior
* [Android Studio 3.x](https://developer.android.com/studio/index.html) con Java 1.8

Como información general, se hará lo siguiente:

* Crear un proyecto de biblioteca de C# Android
* Instalar .NET incrustación a través de NuGet
* Ejecute .NET incrustar en el ensamblado de biblioteca de Android
* Utilice el archivo AAR generado en un proyecto de Java en Android Studio

## <a name="create-an-android-library-project"></a>Crear un proyecto de biblioteca de Android

Abra Visual Studio para Windows o Mac, cree un nuevo proyecto de biblioteca de clases de Android, asígnele el nombre **Hola de csharp**y guárdelo en **~/Projects/hello-from-csharp** o **%USERPROFILE%\ Projects\hello de csharp**.

Agregar una nueva actividad Android denominado **HelloActivity.cs**, seguido de un diseño Android en **Resource/layout/hello.axml**.

Agregue un nuevo `TextView` al diseño y cambiar el texto a algo divertido.

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

En la actividad, asegúrese de que se está llamando a `SetContentView` con el nuevo diseño:

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
> No olvide la `[Register]` atributo. Para obtener más información, consulte [limitaciones](#current-limitations-on-android).

Compile el proyecto. Se guardará el ensamblado resultante en `bin/Debug/hello-from-csharp.dll`.

## <a name="installing-net-embedding-from-nuget"></a>Instalar .NET incrustación de NuGet

Siga estas [instrucciones](~/tools/dotnet-embedding/get-started/install/install.md) para instalar y configurar la incrustación de .NET para el proyecto.

La invocación de comando que se debe configurar tendrá un aspecto similar al siguiente:

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

## <a name="use-the-generated-output-in-an-android-studio-project"></a>Usar el resultado generado en un proyecto de Android Studio

1. Abra Android Studio y cree un nuevo proyecto con un **actividad vacía**.
2. Haga doble clic en el **aplicación** módulo y elija **nuevo > módulo**.
3. Seleccione **importación. JAR /. Paquete de AAR**.
4. Utilizar el Explorador de directorios para buscar **~/Projects/hello-from-csharp/output/hello_from_csharp.aar** y haga clic en **finalizar**.

![Importar AAR en Android Studio](android-images/androidstudioimport.png)

Esto copiará el archivo AAR en un nuevo módulo denominado **hello_from_csharp**.

![Proyecto de Android Studio](android-images/androidstudioproject.png)

Para usar el nuevo módulo de su **aplicación**, haga clic en y elija **configuración para abrir el módulo**. En el **dependencias** ficha, agregue un nuevo **módulo dependencia** y elija **: hello_from_csharp**.

![Android Studio dependencias](android-images/androidstudiodependencies.png)

En la actividad, agregue un nuevo `onResume` método y utilice el código siguiente para iniciar la actividad de C#:

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

Un cambio más se requiere para la incrustación de .NET en el proyecto de Android Studio.

Abra la aplicación **build.gradle** de archivos y agregue el siguiente cambio:

```groovy
android {
    // ...
    aaptOptions {
        noCompress 'dll'
    }
}
```

Xamarin.Android actualmente carga ensamblados .NET directamente desde el APK, pero requiere que los ensamblados no se comprimirá.

Si no tiene este programa de instalación, la aplicación se bloqueará al iniciarse e imprimir algo parecido a esto en la consola:

```shell
com.xamarin.hellocsharp A/monodroid: No assemblies found in '(null)' or '<unavailable>'. Assuming this is part of Fast Deployment. Exiting...
```

## <a name="run-the-app"></a>Ejecutar la aplicación

Tras iniciar la aplicación:

![Hola de ejemplo C# que se ejecuta en el emulador](android-images/hello-from-csharp-android.png)

Tenga en cuenta qué ha ocurrido aquí:

* Tenemos una clase de C#, `HelloActivity`, que las subclases Java
* Contamos con archivos de recursos de Android
* Hemos usado desde Java en Android Studio

Para que este ejemplo funcione, todos los archivos siguientes se configuran en el APK final:

* Xamarin.Android está configurado en el inicio de la aplicación
* Ensamblados de .NET de **activos/ensamblados**
* **AndroidManifest.xml** modificaciones para sus actividades de C#, etcetera.
* Android recursos y activos de bibliotecas de .NET
* [Contenedores CCW Android](~/android/platform/java-integration/android-callable-wrappers.md) para cualquier `Java.Lang.Object` subclase

Si desea obtener un tutorial adicional, consulte el vídeo siguiente, que muestra la incrustación de Petzold [demostración de pintura con los dedos](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint/) en un proyecto de Android Studio:

[![Embeddinator-4000 para Android](https://img.youtube.com/vi/ZVcrXUpCNpI/0.jpg)](https://www.youtube.com/watch?v=ZVcrXUpCNpI)

## <a name="using-java-18"></a>Usa Java 1.8

A partir de escribir esto, la mejor opción consiste en usar Android Studio 3.0 ([descargar aquí](https://developer.android.com/studio/index.html)).

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

También puede echar un vistazo a una [proyecto de prueba de Android Studio](https://github.com/mono/Embeddinator-4000/blob/master/tests/android/app/build.gradle) para obtener más detalles. 

Si se desean usar estable de Studio Android 2.3. x, tendrá que habilitar la cadena de herramientas de conector en desuso:

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

En este momento, si deriva una subclase de `Java.Lang.Object`, Xamarin.Android generará el código auxiliar de Java (Android Callable Wrapper) en lugar de incrustar. NET. Por este motivo, debe seguir las mismas reglas para la exportación de C# para Java como Xamarin.Android. Por ejemplo:

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

* `[Register]` es necesario asignar a un nombre de paquete de Java deseado
* `[Export]` es necesario para que un método visible para Java

Podemos usar `ViewSubclass` en Java así:

```java
import mono.embeddinator.android.ViewSubclass;
//...
ViewSubclass v = new ViewSubclass(this);
v.apply("Hello");
```

Obtenga más información sobre [integración de Java con Xamarin.Android](~/android/platform/java-integration/index.md).

## <a name="multiple-assemblies"></a>Varios ensamblados

Incrustar un único ensamblado es sencillo; Sin embargo, es mucho más probable que tenga más de C# un ensamblado. Muchas veces tendrá dependencias en los paquetes de NuGet, como las bibliotecas de compatibilidad con Android o servicios de Google Play que complican aún más.

Esto hace que un dilema, ya que incrustar .NET necesita incluir muchos tipos de archivos en el final AAR como:

* Android activos
* Recursos de Android
* Android bibliotecas nativas
* Código fuente java Android

Más probable es que no desea incluir estos archivos de la biblioteca de compatibilidad de Android o servicios de Google Play en su AAR, pero en su lugar utilizaría para la versión oficial de Google Android Studio.

Este es el enfoque recomendado:

* Pasar .NET incrustar cualquier ensamblado al que usted es el propietario (tiene origen) y desea llamar desde Java
* Pasar .NET incrustar cualquier ensamblado al que se necesita Android activos, bibliotecas nativas o recursos de
* Agregar dependencias de Java como el Android admiten la biblioteca o servicios de Google Play en Android Studio

Por lo que podría ser el comando:

```shell
mono Embeddinator-4000.exe --gen=Java --platform=Android -c -o output YourMainAssembly.dll YourDependencyA.dll YourDependencyB.dll
```

Debe excluir nada de NuGet, a menos que descubra que contiene activos Android, recursos, etc. que necesitará en el proyecto de Android Studio. También puede omitir las dependencias que no es necesario llamar desde Java y el vinculador _debe_ incluyen las partes de la biblioteca que se necesitan.

Para agregar las dependencias de Java necesarias en Android Studio, el **build.gradle** archivo sería:

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
* [Investigación de Android preliminar](~/tools/dotnet-embedding/android/index.md)
* [Limitaciones de incrustación de .NET](~/tools/dotnet-embedding/limitations.md)
* [En el proyecto de código abierto](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Códigos de error y descripciones](~/tools/dotnet-embedding/errors.md)

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de tiempo (Android)](https://github.com/jamesmontemagno/embeddinator-weather)
