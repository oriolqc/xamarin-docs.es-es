---
title: Pantalla de presentación
description: Una aplicación Android tarda algún tiempo en iniciarse, especialmente cuando la aplicación se inició por primera vez en un dispositivo. Una pantalla de presentación puede mostrar el inicio hasta el usuario o para indicar la personalización de marca de progreso.
ms.prod: xamarin
ms.assetid: 26480465-CE19-71CD-FC7D-69D0990D05DE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/06/2018
ms.openlocfilehash: 1a9856a5b6257c82b2072e9eb44be481373abfcf
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61308229"
---
# <a name="splash-screen"></a>Pantalla de presentación

_Una aplicación Android tarda algún tiempo en iniciarse, especialmente cuando la aplicación se inició por primera vez en un dispositivo. Una pantalla de presentación puede mostrar el inicio hasta el usuario o para indicar la personalización de marca de progreso._


## <a name="overview"></a>Información general

Una aplicación Android tarda algún tiempo en iniciarse, especialmente durante la primera vez que se ejecuta la aplicación en un dispositivo (en ocasiones, esto se conoce como un _frío_). Es posible que muestre la pantalla de presentación iniciarse progreso al usuario, o puede mostrar información de personalización de marca para identificar y promover la aplicación.

Esta guía explica una técnica para implementar una pantalla de presentación en una aplicación de Android. Se tratan los pasos siguientes:

1.  Creación de un recurso puede dibujar en la pantalla de presentación.

2.  Definir un nuevo tema que se mostrará el recurso puede dibujar.

3.  Agregar una nueva actividad a la aplicación que se usará como la pantalla de presentación definida por el tema que creó en el paso anterior.

[![Pantalla de presentación de ejemplo Xamarin logotipo seguido por la pantalla de la aplicación](splash-screen-images/splashscreen-01-sml.png)](splash-screen-images/splashscreen-01.png#lightbox)


## <a name="requirements"></a>Requisitos

Esta guía se da por supuesto que la aplicación está destinada a nivel de API de Android (Android 4.0.3) 15 o superior. La aplicación también debe tener la **Xamarin.Android.Support.v4** y **Xamarin.Android.Support.v7.AppCompat** paquetes de NuGet agregados al proyecto.

Todo el código y XML en esta guía se puede encontrar en el [SplashScreen](https://developer.xamarin.com/samples/monodroid/SplashScreen) proyecto de ejemplo para esta guía.


## <a name="implementing-a-splash-screen"></a>Implementación de una pantalla de presentación

La forma más rápida para representar y mostrar la pantalla de presentación es crear un tema personalizado y aplicarlo a una actividad que se comporta de la pantalla de presentación. Cuando se representa la actividad, carga el tema y se aplica a los recursos drawable (al que hace referencia el tema) al fondo de la actividad. Este enfoque evita la necesidad de crear un archivo de diseño.

La pantalla de presentación se implementa como una actividad que muestra la marca drawable, realiza las inicializaciones y las tareas se inicia. Una vez que se arranca la aplicación, la actividad de la pantalla de presentación inicia la actividad principal y se elimina de la pila de retroceso de la aplicación.


### <a name="creating-a-drawable-for-the-splash-screen"></a>Creación de un Drawable para la pantalla de presentación

La pantalla de presentación mostrará un pueden dibujar en el fondo de la actividad de la pantalla de presentación XML. Es necesario utilizar una imagen de mapa de bits (como PNG o JPG) para la imagen para mostrar.

En esta guía, usamos un [lista capa](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) para centrar la imagen de pantalla de bienvenida en la aplicación. El fragmento de código siguiente es un ejemplo de un `drawable` de recursos mediante un `layer-list`:

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

Esto `layer-list` se centrar la imagen de bienvenida **splash.png** en segundo plano especificado por el `@color/splash_background` recursos. Coloque este archivo XML en el **recursos/drawable** carpeta (por ejemplo, **Resources/drawable/splash_screen.xml**).

Una vez creada la pantalla de presentación con estas características, el siguiente paso es crear un tema de la pantalla de presentación.


### <a name="implementing-a-theme"></a>Implementación de un tema

Para crear un tema personalizado para la actividad de la pantalla de presentación, editar (o agregue) el archivo **values/styles.xml** y cree un nuevo `style` (elemento) para la pantalla de presentación. Un ejemplo **values/style.xml** archivo se muestra a continuación con un `style` denominado **MyTheme.Splash**:

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

**MyTheme.Splash** es muy spartan &ndash; declara el fondo de la ventana, explícitamente quita de la barra de título de la ventana y declara que es la pantalla completa. Si desea crear una pantalla de presentación que emula la interfaz de usuario de la aplicación antes de la actividad aumenta el primer diseño, puede usar `windowContentOverlay` lugar `windowBackground` en la definición de estilo. En este caso, también debe modificar el **splash_screen.xml** drawable para que se muestre una emulación de la interfaz de usuario.


### <a name="create-a-splash-activity"></a>Crear una actividad de presentación

Ahora tenemos una nueva actividad de Android iniciar que tiene la imagen de presentación y realiza cualquier tarea de inicio. El código siguiente es un ejemplo de una implementación de la pantalla de presentación completa:

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

`SplashActivity` usa explícitamente el tema que se creó en la sección anterior, reemplazando el tema predeterminado de la aplicación.
No es necesario cargar un diseño en `OnCreate` como el tema declara un drawable como fondo.

Es importante establecer el `NoHistory=true` atributo para que la actividad se quita de la pila de retroceso. Para evitar que el botón Atrás Cancelar el proceso de inicio, también puede invalidar `OnBackPressed` y hacer que no hacen nada:

```csharp
public override void OnBackPressed() { }
```

El trabajo de inicio se realiza de forma asincrónica en `OnResume`. Esto es necesario para que el trabajo de inicio no ralentiza o retrasar la apariencia de la pantalla de inicio. Cuando haya finalizado el trabajo, `SplashActivity` iniciará `MainActivity` y el usuario puede empezar a interactuar con la aplicación.

Esta nueva `SplashActivity` se establece como la actividad del iniciador de la aplicación estableciendo el `MainLauncher` atributo `true`. Dado que `SplashActivity` es ahora la actividad del iniciador, debe editar `MainActivity.cs`y quite el `MainLauncher` de atributo de `MainActivity`:

```csharp
[Activity(Label = "@string/ApplicationName")]
public class MainActivity : AppCompatActivity
{
    // Code omitted for brevity
}
```

## <a name="landscape-mode"></a>Modo horizontal

La pantalla de presentación que se implementa en los pasos anteriores se mostrará correctamente en modo vertical y horizontal. Sin embargo, en algunos casos es necesario disponer de las pantallas de presentación independiente para los modos vertical y horizontal (por ejemplo, si la imagen de bienvenida es la pantalla completa).

Para agregar una pantalla de presentación para el modo horizontal, siga estos pasos:

1. En el **recursos/drawable** carpeta, agregue la versión horizontal de la imagen de pantalla de presentación que desee utilizar. En este ejemplo, **splash_logo_land.png** es la versión del panorama del logotipo que se usó en los ejemplos anteriores (usa letras en blanco en lugar de azul).

2. En el **recursos/drawable** carpeta, cree una versión del panorama de la `layer-list` drawable que se definió anteriormente (por ejemplo, **splash_screen_land.xml**). En este archivo, establezca la ruta de acceso de mapa de bits a la versión del panorama de la imagen de pantalla de presentación. En el ejemplo siguiente, **splash_screen_land.xml** usa **splash_logo_land.png**:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <layer-list xmlns:android="http://schemas.android.com/apk/res/android">
      <item>
        <color android:color="@color/splash_background"/>
      </item>
      <item>
        <bitmap
            android:src="@drawable/splash_logo_land"
            android:tileMode="disabled"
            android:gravity="center"/>
      </item>
    </layer-list>
    ```

3.  Crear el **recursos, los valores-land** carpeta si aún no existe.

4.  Agregue los archivos **colors.xml** y **style.xml** a **valores land** (estos se pueden copiar y modificar desde existente **values/colors.xml**y **values/style.xml** archivos).

5.  Modificar **valores-land/style.xml** para que use la versión horizontal de la drawable para `windowBackground`. En este ejemplo, **splash_screen_land.xml** se usa:

    ```xml
    <resources>
      <style name="MyTheme.Base" parent="Theme.AppCompat.Light">
      </style>
        <style name="MyTheme" parent="MyTheme.Base">
      </style>
      <style name="MyTheme.Splash" parent ="Theme.AppCompat.Light.NoActionBar">
        <item name="android:windowBackground">@drawable/splash_screen_land</item>
        <item name="android:windowNoTitle">true</item>  
        <item name="android:windowFullscreen">true</item>  
        <item name="android:windowContentOverlay">@null</item>  
        <item name="android:windowActionBar">true</item>  
      </style>
    </resources>
    ```

6.  Modificar **valores-land/colors.xml** para configurar los colores que desee usar para la versión del panorama de la pantalla de presentación. En este ejemplo, se cambia el color de fondo de presentación a azul para modo horizontal:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <resources>
      <color name="primary">#2196F3</color>
      <color name="primaryDark">#1976D2</color>
      <color name="accent">#FFC107</color>
      <color name="window_background">#F5F5F5</color>
      <color name="splash_background">#3498DB</color>
    </resources>
    ```

7.  Compile y vuelva a ejecutar la aplicación. Girar el dispositivo para el modo horizontal mientras todavía se muestra la pantalla de presentación. La pantalla de presentación cambia a la versión horizontal:

    [![Rotación de pantalla de presentación en modo horizontal](splash-screen-images/landscape-splash-sml.png)](splash-screen-images/landscape-splash.png#lightbox)


Tenga en cuenta que el uso de una pantalla de presentación del modo de horizontal no siempre proporciona una experiencia sin problemas. De forma predeterminada, Android inicia la aplicación en modo vertical y realiza la transición para el modo horizontal, incluso si el dispositivo ya está en modo horizontal. Como resultado, si se inicia la aplicación mientras el dispositivo está en modo horizontal, el dispositivo presenta brevemente la pantalla de presentación vertical y, a continuación, anima la rotación de la vertical a la pantalla de presentación horizontal. Lamentablemente, esta transición de vertical a horizontal inicial tiene lugar incluso cuando `ScreenOrientation = Android.Content.PM.ScreenOrientation.Landscape` se especifica en las marcas de la actividad de presentación. La mejor manera de evitar esta limitación es crear una imagen de pantalla de presentación único que representa correctamente en los modos vertical y horizontal.


## <a name="summary"></a>Resumen

Esta guía describe una forma de implementar una pantalla de presentación en una aplicación de Xamarin.Android; es decir, aplicar un tema personalizado para la actividad de inicio.


## <a name="related-links"></a>Vínculos relacionados

- [SplashScreen (ejemplo)](https://developer.xamarin.com/samples/monodroid/SplashScreen)
- [lista de capas Drawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList)
- [ Patrones de diseño de materiales: las pantallas de inicio](https://material.io/design/communication/launch-screen.html#usage)
