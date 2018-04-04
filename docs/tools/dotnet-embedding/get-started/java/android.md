---
title: Introducción a Android
ms.prod: xamarin
ms.assetid: 870F0C18-A794-4C5D-881B-64CC78759E30
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/28/2018
ms.openlocfilehash: ed3d6ae3f8537bfae456c6919743e8c9fbfb2009
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="getting-started-with-android"></a>Introducción a Android


Además de los requisitos de nuestro [introducción con Java](~/tools/dotnet-embedding/get-started/java/index.md) guía también necesitará:

* [Xamarin.Android 7.5](https://www.visualstudio.com/xamarin/) o posterior
* [Android Studio 3.x](https://developer.android.com/studio/index.html) con Java 1.8

Como información general, se hará lo siguiente:

* Crear un proyecto de biblioteca de C# Android
* Instalar .NET incrustación a través de NuGet
* Ejecute Embeddinator en el ensamblado de biblioteca de Android
* Utilice el archivo AAR generado en un proyecto de Java en Android Studio

## <a name="create-an-android-library-project"></a>Crear un proyecto de biblioteca de Android

Abra Visual Studio para Windows o Mac, cree un nuevo proyecto de biblioteca de clases de Android, asígnele el nombre `hello-from-csharp`y guárdelo en `~/Projects/hello-from-csharp` o `%USERPROFILE%\Projects\hello-from-csharp`.

Agregar una nueva actividad Android denominado `HelloActivity.cs`, seguido de un diseño Android en `Resource/layout/hello.axml`.

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
*Nota: no se olvide la `[Register]` atributo, ver los detalles de las limitaciones*

Compile el proyecto, se guardará el ensamblado resultante en `bin/Debug/hello-from-csharp.dll`.

## <a name="installing-net-embedding-from-nuget"></a>Instalar .NET incrustación de NuGet

Elija **Agregar > Agregar paquetes de NuGet...**  e instalar `Embeddinator-4000` desde el Administrador de paquetes de NuGet: ![Administrador de paquetes de NuGet](android-images/visualstudionuget.png)

Este modo se instalará `Embeddinator-4000.exe` en el `packages/Embeddinator-4000/tools` directory.

## <a name="run-embeddinator-4000"></a>Run Embeddinator-4000

Se agregará un paso posterior a la compilación para ejecutar Embeddinator y crear un archivo AAR nativo para el ensamblado de proyecto de biblioteca de Android.

En Visual Studio para Mac, vaya a _Project Options > compilar > comandos personalizados_ y agregue un _después de compilar_ paso.

El programa de instalación el commnd siguiente:
```
mono '${SolutionDir}/packages/Embeddinator-4000.0.2.0.80/tools/Embeddinator-4000.exe' '${TargetPath}' --gen=Java --platform=Android --outdir='${SolutionDir}/output' -c
```

> [!NOTE]
> Nota: asegúrese de utilizar el número de versión instalada de NuGet

Si va a hacer el desarrollo en curso en el proyecto de C#, también puede agregar un comando personalizado para limpiar el `output` directorio antes de ejecutar Embeddinator:
```
rm -Rf '${SolutionDir}/output/'
```

![Acción de compilación personalizada](android-images/visualstudiocustombuild.png)

El archivo AAR Android se colocarán en `~/Projects/hello-from-csharp/output/hello_from_csharp.aar`. _Nota: los guiones se reemplazan porque Java no admite nombres de paquete._

### <a name="running-net-embedding-on-windows"></a>Ejecuta .NET incrustar en Windows

Se configurará básicamente lo mismo, pero los menús de Visual Studio son ligeramente distintos en Windows. Los comandos de shell también son ligeramente diferentes.

Vaya a **Project Options > eventos de compilación** y escriba lo siguiente en el **línea de comandos del evento posterior a la compilación** cuadro:
```
set E4K_OUTPUT="$(SolutionDir)output"
if exist %E4K_OUTPUT% rmdir /S /Q %E4K_OUTPUT%
"$(SolutionDir)packages\Embeddinator-4000.0.2.0.80\tools\Embeddinator-4000.exe" "$(TargetPath)" --gen=Java --platform=Android --outdir=%E4K_OUTPUT% -c
```

Por ejemplo, la captura de pantalla siguiente:

![Embeddinator en Windows](android-images/visualstudiowindows.png)

## <a name="use-the-generated-output-in-an-android-studio-project"></a>Usar el resultado generado en un proyecto de Android Studio

1. Abra Android Studio y cree un nuevo proyecto con un **actividad vacía**.
2. Haga doble clic en el `app` módulo y elija **nuevo > módulo**.
3. Seleccione **importación. JAR /. Paquete de AAR**.
4. Utilizar el Explorador de directorios para buscar `~/Projects/hello-from-csharp/output/hello_from_csharp.aar` y haga clic en **finalizar**.

![Importar AAR en Android Studio](android-images/androidstudioimport.png)

Esto copiará el archivo AAR en un nuevo módulo denominado `hello_from_csharp`.

![Proyecto de Android Studio](android-images/androidstudioproject.png)

Para usar el nuevo módulo de su `app`, haga clic en y elija **configuración para abrir el módulo**. En el **dependencias** ficha, agregue un nuevo **módulo dependencia** y elija `:hello_from_csharp`.

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

### <a name="assembly-compression-important"></a>Compresión de ensamblado *importante*

Un cambio más se requiere para la incrustación de .NET en el proyecto de Android Studio.

Abra la aplicación `build.gradle` de archivos y agregue el siguiente cambio:
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

```csharp
com.xamarin.hellocsharp A/monodroid: No assemblies found in '(null)' or '<unavailable>'. Assuming this is part of Fast Deployment. Exiting...
```

## <a name="run-the-app"></a>Ejecutar la aplicación

Tras iniciar la aplicación:

![Hola de ejemplo C# que se ejecuta en el emulador](android-images/hello-from-csharp-android.png)

Tenga en cuenta qué ha ocurrido aquí:

* Tenemos una clase de C#, `HelloActivity`, que las subclases Java
* Contamos con archivos de recursos de Android
* Hemos usado desde Java en Android Studio

Por lo tanto, para que este ejemplo funcione, todos los requisitos siguientes son el programa de instalación en el APK final:

* Xamarin.Android está configurado en el inicio de la aplicación
* Incluido en los ensamblados .NET `assets/assemblies`
* `AndroidManifest.xml` modificaciones de sus actividades de C#, etcetera.
* Android recursos y activos de bibliotecas de .NET
* [Contenedores CCW Android](https://developer.xamarin.com/guides/android/advanced_topics/java_integration_overview/android_callable_wrappers/) para cualquier `Java.Lang.Object` subclase

Si desea obtener un tutorial adicional, consulte este vídeo de incrustación de Petzold [demostración de pintura con los dedos](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint/) en un Android Studio proyecto aquí:

[![Embeddinator-4000 para Android](https://img.youtube.com/vi/ZVcrXUpCNpI/0.jpg)](https://www.youtube.com/watch?v=ZVcrXUpCNpI)

## <a name="using-java-18"></a>Usa Java 1.8

A partir de escribir esto, la mejor opción consiste en usar Android Studio 3.0 ([descargar aquí](https://developer.android.com/studio/index.html)).

Para habilitar Java 1.8 en el módulo de aplicación `build.gradle` archivo:
```groovy
android {
    // ...
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
```
También puede echar un vistazo a nuestro proyecto de prueba de Android Studio [aquí](https://github.com/mono/Embeddinator-4000/blob/master/tests/android/app/build.gradle) para obtener más detalles.

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

Ahora mismo si deriva una subclase de `Java.Lang.Object`, Xamarin.Android generará el código auxiliar de Java (Android Callable Wrapper) en lugar de Embeddinator.

Por lo que debe seguir las mismas reglas para la exportación de C# para Java como Xamarin.Android.

Así, por ejemplo en C#:
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

Esto hace que un dilema, ya que Embeddinator es necesario que incluya muchos tipos de archivos en el final AAR como:
* Android activos
* Recursos de Android
* Android bibliotecas nativas
* Código fuente java Android

Más probable es que no desea incluir estos archivos de la biblioteca de compatibilidad de Android o servicios de Google Play en su AAR, pero en su lugar utilizaría para la versión oficial de Google Android Studio.

Este es el enfoque recomendado:
* Pasar Embeddinator cualquier ensamblado al que usted es el propietario (tiene origen) y desea llamar desde Java
* Pasar Embeddinator cualquier ensamblado al que se necesita Android activos, bibliotecas nativas o recursos de
* Agregar dependencias de Java como el Android admiten la biblioteca o servicios de Google Play en Android Studio

Por lo que podría ser el comando:
```
mono Embeddinator-4000.exe --gen=Java --platform=Android -c -o output YourMainAssembly.dll YourDependencyA.dll YourDependencyB.dll
```
Debe excluir nada de NuGet, a menos que descubra que contiene activos Android, recursos, etc. que necesitará en el proyecto de Android Studio. También puede omitir las dependencias que no es necesario llamar desde Java y el vinculador _debe_ incluyen las partes de la biblioteca que se necesitan.

Para agregar las dependencias de Java necesarias en Android Studio, su `build.gradle` archivo sería:
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
* [Limitaciones de Embeddinator](~/tools/dotnet-embedding/limitations.md)
* [En el proyecto de código abierto](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [Códigos de error y descripciones](~/tools/dotnet-embedding/errors.md)


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de tiempo (Android)](https://github.com/jamesmontemagno/embeddinator-weather)
