---
title: "Pantalla de presentación"
description: "Una aplicación Android tarda algún tiempo en iniciarse, especialmente cuando la aplicación se inició por primera vez en un dispositivo. Una pantalla de presentación puede mostrar Inicio progreso al usuario o para indicar la personalización de marca una copia de seguridad."
ms.topic: article
ms.prod: xamarin
ms.assetid: 26480465-CE19-71CD-FC7D-69D0990D05DE
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: 9acb1ad6ab1425edb98b938e8c03edc3704f50ae
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="splash-screen"></a>Pantalla de presentación

_Una aplicación Android tarda algún tiempo en iniciarse, especialmente cuando la aplicación se inició por primera vez en un dispositivo. Una pantalla de presentación puede mostrar Inicio progreso al usuario o para indicar la personalización de marca una copia de seguridad._

<a name="overview" />

## <a name="overview"></a>Información general

Una aplicación Android tarda algún tiempo para iniciarse, especialmente durante la primera vez que se ejecute la aplicación en un dispositivo (a veces, esto se conoce como un _arranque en frío_). Puede mostrar la pantalla de presentación iniciarse progreso al usuario o puede mostrar información de personalización de marca para identificar y promover la aplicación.

Esta guía describe una técnica para implementar una pantalla de presentación en una aplicación de Android. Tratan los siguientes pasos:

1.  Crear un recurso puede dibujar en la pantalla de presentación.

2.  Definir un nuevo tema que se mostrará el recurso puede dibujar.

3.  Agregar una nueva actividad a la aplicación que se utilizará como la pantalla de presentación definida por el tema que creó en el paso anterior.

[![Pantalla de presentación de ejemplo Xamarin logotipo seguido de pantalla de la aplicación](splash-screen-images/splashscreen-01-sml.png)](splash-screen-images/splashscreen-01.png)


<a name="requirements" />

## <a name="requirements"></a>Requisitos

Esta guía se asume que la aplicación está destinada a nivel de API de Android 15 (Android 4.0.3) o superior. La aplicación también debe tener la **Xamarin.Android.Support.v4** y **Xamarin.Android.Support.v7.AppCompat** paquetes de NuGet que se agrega al proyecto.

Todo el código y XML en esta guía pueden encontrarse en el [SplashScreen](https://developer.xamarin.com/samples/monodroid/SplashScreen) proyecto de ejemplo de esta guía.

<a name="implement" />

## <a name="implementing-a-splash-screen"></a>Implementar una pantalla de presentación

La forma más rápida para procesar y mostrar la pantalla de presentación es crear un tema personalizado y aplicarlo a una actividad que muestra la pantalla de presentación. Cuando se representa la actividad, carga un tema y se aplica el recurso puede dibujar (al que hace referencia el tema) para el fondo de la actividad. Este enfoque evita la necesidad de crear un archivo de diseño.

La pantalla de presentación se implementa como una actividad que muestra la marca puede dibujar, realiza las inicializaciones y las tareas se inicia. Una vez se iniciar la aplicación, la actividad de la pantalla de presentación, iniciará la actividad principal y se elimina de la pila de retroceso de la aplicación.

<a name="drawable" />

### <a name="creating-a-drawable-for-the-splash-screen"></a>Crear una con estas características para la pantalla de presentación

La pantalla de presentación mostrará un XML que se pueden dibujar en el fondo de la actividad de la pantalla de presentación. Es necesario utilizar una imagen de mapa de bits (por ejemplo, PNG o JPG) de la imagen para mostrar.

En esta guía, se utiliza un [lista capa](http://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) para centrar la imagen de la pantalla de presentación en la aplicación. El fragmento de código siguiente es un ejemplo de un `drawable` de recursos utilizando una `layer-list`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
  <item>
    <color android:color="@color/splash_background"/>
  </item>
  <item>
    <bitmap
        android:src="@drawable/splash"
        android:tileMode="disabled"
        android:gravity="center"/>
  </item>
</layer-list>
```

Esto `layer-list` se centrar la imagen de la pantalla de presentación **splash.png** sobre un fondo especificado por el `@color/splash_background` recursos.
Colocar este archivo en el **/puede dibujar recursos** carpeta (por ejemplo, **Resources/drawable/splash_screen.xml**).

Una vez creada la pantalla de presentación con estas características, el siguiente paso es crear un tema de la pantalla de presentación.

<a name="theme" />

### <a name="implementing-a-theme"></a>Implementación de un tema

Para crear un tema personalizado para la actividad de la pantalla de presentación, editar (o agregar) el archivo **values/styles.xml** y crear un nuevo `style` , elemento de la pantalla de presentación. Un ejemplo de **values/style.xml** archivo se muestra a continuación con un `style` denominado **MyTheme.Splash**:

```xml
<resources>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light">
  </style>

  <style name="MyTheme" parent="MyTheme.Base">
  </style>

  <style name="MyTheme.Splash" parent ="Theme.AppCompat.Light.NoActionBar">
    <item name="android:windowBackground">@drawable/splash_screen</item>
    <item name="android:windowNoTitle">true</item>
    <item name="android:windowFullscreen">true</item>
  </style>
</resources>
```

**MyTheme.Splash** es muy spartan &ndash; declara el fondo de la ventana, explícitamente quita la barra de título de la ventana y declara que es la pantalla completa. Si desea crear una pantalla de presentación que emula la interfaz de usuario de la aplicación antes de que la actividad aumenta la primera distribución, puede usar `windowContentOverlay` en lugar de `windowBackground` en la definición de estilo. En este caso, también debe modificar el **splash_screen.xml** con estas características para que se muestre una emulación de la interfaz de usuario.

<a name="activity" />

### <a name="create-a-splash-activity"></a>Crear una actividad de presentación

Ahora tenemos una nueva actividad para Android iniciar que tiene la imagen a la presentación y realiza cualquier tarea de inicio. El código siguiente es un ejemplo de una implementación de la pantalla de presentación completa:

```csharp
[Activity(Theme = "@style/MyTheme.Splash", MainLauncher = true, NoHistory = true)]
public class SplashActivity : AppCompatActivity
{
    static readonly string TAG = "X:" + typeof(SplashActivity).Name;

    public override void OnCreate(Bundle savedInstanceState, PersistableBundle persistentState)
    {
        base.OnCreate(savedInstanceState, persistentState);
        Log.Debug(TAG, "SplashActivity.OnCreate");
    }

    // Launches the startup task
    protected override void OnResume()
    {
        base.OnResume();
        Task startupWork = new Task(() => { SimulateStartup(); });
        startupWork.Start();
    }

    // Simulates background work that happens behind the splash screen
    async void SimulateStartup ()
    {
        Log.Debug(TAG, "Performing some startup work that takes a bit of time.");
        await Task.Delay (8000); // Simulate a bit of startup work.
        Log.Debug(TAG, "Startup work is finished - starting MainActivity.");
        StartActivity(new Intent(Application.Context, typeof (MainActivity)));
    }
}
```

`SplashActivity` utiliza de forma explícita el tema que se creó en la sección anterior, reemplazar el tema predeterminado de la aplicación.
No es necesario cargar un diseño en `OnCreate` como el tema declara un puede dibujar como fondo.

Es importante establecer el `NoHistory=true` atributo para que la actividad se quita de la pila de retroceso. Para evitar que el botón Atrás Cancelar el proceso de inicio, también puede invalidar `OnBackPressed` y para que no hacen nada:

```csharp
public override void OnBackPressed() { }
```

El trabajo de inicio se realiza de forma asincrónica en `OnResume`. Esto es necesario para que el trabajo de inicio no se ralentiza o retrasar la apariencia de la pantalla de inicio. Cuando haya finalizado el trabajo, `SplashActivity` iniciará `MainActivity` y el usuario puede empezar a interactuar con la aplicación.

Esta nueva `SplashActivity` se establece como la actividad del iniciador de la aplicación estableciendo el `MainLauncher` atribuir a `true`. Dado que `SplashActivity` es ahora la actividad del iniciador, debe editar `MainActivity.cs`y quitar el `MainLauncher` de atributo de `MainActivity`:

```csharp
[Activity(Label = "@string/ApplicationName")]
public class MainActivity : AppCompatActivity
{
    // Code omitted for brevity
}
```

<a name="summary" />

## <a name="summary"></a>Resumen

Esta guía describe una manera de implementar una pantalla de presentación en una aplicación Xamarin.Android; es decir, aplicar un tema personalizado a la actividad de inicio.


## <a name="related-links"></a>Vínculos relacionados

- [Pantalla de presentación (ejemplo)](https://developer.xamarin.com/samples/monodroid/SplashScreen)
- [lista de capas Drawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList)
- [ Patrones de diseño material - pantallas de inicio](https://www.google.com/design/spec/patterns/launch-screens.html)
