---
title: Control de giro
description: Este tema describe cómo controlar los cambios de orientación del dispositivo en Xamarin.Android. Se describe cómo trabajar con el sistema de recursos de Android para cargar automáticamente los recursos para una orientación de dispositivo determinado, así cómo controlar mediante programación la orientación cambia.
ms.prod: xamarin
ms.assetid: 6D33ADF7-ED81-0256-479D-D9E3787A76B0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: b2361c04ae627dd68d98f9a6bca1238f1694aaa1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118791"
---
# <a name="handling-rotation"></a>Control de giro

_Este tema describe cómo controlar los cambios de orientación del dispositivo en Xamarin.Android. Se describe cómo trabajar con el sistema de recursos de Android para cargar automáticamente los recursos para una orientación de dispositivo determinado, así cómo controlar mediante programación la orientación cambia._


## <a name="overview"></a>Información general

Dado que fácilmente se giran los dispositivos móviles, giro integrado es una característica estándar en los sistemas operativos móviles. Android proporciona un marco sofisticado para tratar con una rotación de las aplicaciones, si la interfaz de usuario se crea mediante declaración en XML o mediante programación en código. Cuando se administran automáticamente los cambios de diseño declarativo en un dispositivo girado, una aplicación puede beneficiarse de la estrecha integración con el sistema de recursos de Android. Para el diseño mediante programación, se deben realizar los cambios manualmente. Esto permite un control más preciso en tiempo de ejecución, pero a costa de más trabajo para el desarrollador. Una aplicación también puede dejar de participar en el reinicio de la actividad y tome el control manual de los cambios de orientación.

Esta guía examina los siguientes temas de orientación:

-   **Rotación de diseño declarativo** &ndash; cómo usar el sistema de recursos de Android para compilar aplicaciones basadas en la orientación, incluyendo cómo cargar los diseños y recursos drawable para orientaciones determinadas.

-   **Rotación de diseño mediante programación** &ndash; cómo agregar controles mediante programación, así como cómo controlar manualmente los cambios de orientación.


## <a name="handling-rotation-declaratively-with-layouts"></a>Control de giro mediante declaración con diseños

Mediante la inclusión de archivos en carpetas que siguen las convenciones de nomenclatura, Android carga automáticamente los archivos adecuados cuando cambia la orientación.
Esto incluye compatibilidad con:

-   *Recursos de diseño* &ndash; especificar qué archivos de diseño se inflan para cada orientación.

-   *Recursos drawable* &ndash; especifica qué recursos drawable se carga para cada orientación.


### <a name="layout-resources"></a>Recursos de diseño

De forma predeterminada, los archivos XML de Android (AXML) se incluyen en el **y diseño de los recursos** carpeta se usa para representar vistas para una actividad. Recursos de esta carpeta se usan para la orientación vertical y horizontal si no hay recursos de diseño adicionales se proporcionan específicamente para el entorno de. Tenga en cuenta la estructura de proyecto creada por la plantilla de proyecto predeterminada:

[![Estructura de plantilla de proyecto predeterminada](handling-rotation-images/00.png)](handling-rotation-images/00.png#lightbox)

Este proyecto crea una sola **Main.axml** de archivos en el **y diseño de los recursos** carpeta. Cuando la actividad `OnCreate` se llama al método, aumenta la vista definida en **Main.axml,** que declara un botón, como se muestra en el XML siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:orientation="vertical"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
<Button  
  android:id="@+id/myButton"
  android:layout_width="fill_parent" 
  android:layout_height="wrap_content" 
  android:text="@string/hello"/>
</LinearLayout>
```

Si el dispositivo se gira a la orientación horizontal, la actividad del `OnCreate` se llama al método nuevo y el mismo **Main.axml** se aumenta el archivo, como se muestra en la captura de pantalla siguiente:

[![Mismo, pero en la pantalla con orientación horizontal](handling-rotation-images/01-sml.png)](handling-rotation-images/01.png#lightbox)


#### <a name="orientation-specific-layouts"></a>Diseños de orientación específica

Además de la carpeta de diseño (que el valor predeterminado es vertical y también se puede llamar explícitamente *diseño-port* mediante la inclusión de una carpeta denominada `layout-land`), una aplicación puede definir las vistas que necesita en el panorama sin código cambios.

Supongamos que el **Main.axml** archivo contenía el código XML siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
  <TextView
    android:text="This is portrait"
    android:layout_height="wrap_content"
    android:layout_width="fill_parent" />
</RelativeLayout>
```

Si una carpeta denominada el terreno de diseño que contiene más **Main.axml** archivo se agrega al proyecto, se infle el diseño en el panorama producirá ahora Android cargando recién agregada **Main.axml.** Tenga en cuenta la versión del panorama de la **Main.axml** archivo que contiene el código siguiente (por motivos de simplicidad, este código XML es similar a la versión de vertical predeterminada del código, pero usa una cadena diferente en el `TextView`):

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
  <TextView
    android:text="This is landscape"
    android:layout_height="wrap_content"
    android:layout_width="fill_parent" />
</RelativeLayout>
```

Ejecutar este código y girar el dispositivo de vertical a horizontal muestra la nueva carga de XML, tal como se muestra a continuación:

[![Capturas de pantalla vertical y horizontal, imprimir el modo vertical](handling-rotation-images/02.png)](handling-rotation-images/02.png#lightbox)


### <a name="drawable-resources"></a>Recursos drawable

Durante la rotación, Android trata los recursos pueden dibujables de forma similar a los recursos de diseño. En este caso, el sistema obtiene lo recursos drawable desde el **recursos/drawable** y **recursos/drawable-land** carpetas, respectivamente.

Por ejemplo, supongamos que el proyecto incluye una imagen denominada Monkey.png en el **recursos/drawable** carpeta, donde el drawable se hace referencia desde un `ImageView` en XML similar al siguiente:

```xml
<ImageView
  android:layout_height="wrap_content"
  android:layout_width="wrap_content"
  android:src="@drawable/monkey"
  android:layout_centerVertical="true"
  android:layout_centerHorizontal="true" />
```

Supongamos además que una versión diferente de **Monkey.png** se incluye en **recursos/drawable-land**. Al igual que con los archivos de diseño, cuando el dispositivo está había girado, los cambios pueden dibujables para la orientación determinada, tal como se muestra a continuación:

[![Versión diferente de Monkey.png se muestra en los modos vertical y horizontal](handling-rotation-images/03.png)](handling-rotation-images/03.png#lightbox)


## <a name="handling-rotation-programmatically"></a>Control de giro mediante programación

A veces, definimos los diseños en el código. Esto puede ocurrir por diversos motivos, incluyendo las limitaciones técnicas, preferencias de desarrollador, etcetera. Cuando se agregan controles mediante programación, una aplicación manualmente tener en cuenta para la orientación del dispositivo, que se controla automáticamente cuando se usan recursos XML.


### <a name="adding-controls-in-code"></a>Agregar controles en el código

Para agregar controles mediante programación, una aplicación debe realizar los pasos siguientes:

-  Crear un diseño.
-  Establecer parámetros de diseño.
-  Creación de controles.
-  Establecer parámetros de diseño del control.
-  Agregar controles al diseño.
-  Establecer el diseño como la vista de contenido.

Por ejemplo, considere la posibilidad de una interfaz de usuario que consta de una sola `TextView` control agregado a un `RelativeLayout`, tal y como se muestra en el código siguiente.

```csharp
protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);
                        
  // create a layout
  var rl = new RelativeLayout (this);

  // set layout parameters
  var layoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.FillParent);
  rl.LayoutParameters = layoutParams;
        
  // create TextView control
  var tv = new TextView (this);

  // set TextView's LayoutParameters
  tv.LayoutParameters = layoutParams;
  tv.Text = "Programmatic layout";

  // add TextView to the layout
  rl.AddView (tv);
        
  // set the layout as the content view
  SetContentView (rl);
}
```

Este código crea una instancia de un `RelativeLayout` clase y establece su `LayoutParameters` propiedad. La `LayoutParams` clase es la manera de Android de encapsulación de cómo los controles se colocan de forma reutilizable. Una vez que se crea una instancia de un diseño, se pueden crear y le agrega controles. Los controles tienen también `LayoutParameters`, como el `TextView` en este ejemplo. Después de la `TextView` se crea, éste se agrega a la `RelativeLayout` y estableciendo el `RelativeLayout` como los resultados de la vista de contenido en la aplicación se muestre el `TextView` tal como se muestra:

[![Botón de contador de incremento que se muestra en los modos vertical y horizontal](handling-rotation-images/04.png)](handling-rotation-images/04.png#lightbox)


### <a name="detecting-orientation-in-code"></a>Detectar la orientación en el código

Si una aplicación intenta cargar una interfaz de usuario diferente para cada orientación cuando `OnCreate` se llama (Esto ocurre cada vez que se gira un dispositivo), debe detectar la orientación y, a continuación, cargue el código de interfaz de usuario deseado. Android tiene una clase denominada el `WindowManager`, que puede usarse para determinar la rotación de dispositivo actual a través de la `WindowManager.DefaultDisplay.Rotation` propiedad, como se muestra a continuación:

```csharp
protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);
                        
  // create a layout
  var rl = new RelativeLayout (this);

  // set layout parameters
  var layoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.FillParent);
  rl.LayoutParameters = layoutParams;
                        
  // get the initial orientation
  var surfaceOrientation = WindowManager.DefaultDisplay.Rotation;
  // create layout based upon orientation
  RelativeLayout.LayoutParams tvLayoutParams;
                
  if (surfaceOrientation == SurfaceOrientation.Rotation0 || surfaceOrientation == SurfaceOrientation.Rotation180) {
    tvLayoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
  } else {
    tvLayoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
    tvLayoutParams.LeftMargin = 100;
    tvLayoutParams.TopMargin = 100;
  }
                        
  // create TextView control
  var tv = new TextView (this);
  tv.LayoutParameters = tvLayoutParams;
  tv.Text = "Programmatic layout";
        
  // add TextView to the layout
  rl.AddView (tv);
        
  // set the layout as the content view
  SetContentView (rl);
}
```

Este código establece el `TextView` para ser colocado 100 píxeles de la parte superior izquierda de la pantalla, animar automáticamente al nuevo diseño, cuando gira horizontal, como se muestra aquí:

[![Estado de vista se conserva entre los modos vertical y horizontal](handling-rotation-images/05.png)](handling-rotation-images/05.png#lightbox)


### <a name="preventing-activity-restart"></a>Evitar el reinicio de la actividad

Además de controlar todo el contenido de `OnCreate`, una aplicación también puede evitar que una actividad que se reinicia cuando cambia la orientación estableciendo `ConfigurationChanges` en el `ActivityAttribute` como sigue:

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
```
Ahora cuando se gira el dispositivo, la actividad no se reinicia. Con el fin de controlar manualmente el cambio de orientación en este caso, una actividad puede invalidar el `OnConfigurationChanged` método y determinar la orientación de la `Configuration` objeto que se pasa, como se muestra en la nueva implementación de la actividad siguiente:

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
public class CodeLayoutActivity : Activity
{
  TextView _tv;
  RelativeLayout.LayoutParams _layoutParamsPortrait;
  RelativeLayout.LayoutParams _layoutParamsLandscape;
                
  protected override void OnCreate (Bundle bundle)
  {
    // create a layout
    // set layout parameters
    // get the initial orientation

    // create portrait and landscape layout for the TextView
    _layoutParamsPortrait = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
                
    _layoutParamsLandscape = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
    _layoutParamsLandscape.LeftMargin = 100;
    _layoutParamsLandscape.TopMargin = 100;
                        
    _tv = new TextView (this);
                        
    if (surfaceOrientation == SurfaceOrientation.Rotation0 || surfaceOrientation == SurfaceOrientation.Rotation180) {
      _tv.LayoutParameters = _layoutParamsPortrait;
    } else {
      _tv.LayoutParameters = _layoutParamsLandscape;
    }
                        
    _tv.Text = "Programmatic layout";
    rl.AddView (_tv);
    SetContentView (rl);
  }
                
  public override void OnConfigurationChanged (Android.Content.Res.Configuration newConfig)
  {
    base.OnConfigurationChanged (newConfig);
                        
    if (newConfig.Orientation == Android.Content.Res.Orientation.Portrait) {
      _tv.LayoutParameters = _layoutParamsPortrait;
      _tv.Text = "Changed to portrait";
    } else if (newConfig.Orientation == Android.Content.Res.Orientation.Landscape) {
      _tv.LayoutParameters = _layoutParamsLandscape;
      _tv.Text = "Changed to landscape";
    }
  }
}
```

Aquí el `TextView's` se inicializan los parámetros de diseño para horizontal y vertical. Las variables de clase contienen los parámetros, junto con el `TextView` Sí, ya que la actividad no se creará volver a cuando cambia la orientación. El código sigue usando el `surfaceOrientartion` en `OnCreate` para establecer el diseño inicial para la `TextView`. Después de eso, `OnConfigurationChanged` controla todos los cambios de diseño posterior.

Cuando ejecutemos la aplicación, Android carga los cambios de la interfaz de usuario como la rotación de dispositivos se produce y no reinicia la actividad.


## <a name="preventing-activity-restart-for-declarative-layouts"></a>Evitar el reinicio de actividad declarativas diseños

También se pueden evitar reinicios de actividad causados por la rotación de dispositivos si se define el diseño en XML. Por ejemplo, podemos usar este enfoque si desea evitar un reinicio de la actividad (por motivos de rendimiento, quizás) y no es necesario cargar nuevos recursos para orientaciones diferentes.

Para ello, se sigue el mismo procedimiento que se usa con un diseño mediante programación. Basta con establecer `ConfigurationChanges` en el `ActivityAttribute`, tal y como hicimos en el `CodeLayoutActivity` anteriormente. Cualquier código que deba ejecutar de nuevo se puede implementar el cambio de orientación de la `OnConfigurationChanged` método.


## <a name="maintaining-state-during-orientation-changes"></a>Mantiene el estado durante los cambios de orientación

Si el control de giro de forma declarativa o mediante programación, todas las aplicaciones de Android deben implementar las mismas técnicas para administrar el estado cuando se cambia la orientación del dispositivo. Administrar el estado es importante porque el sistema reinicia una actividad en ejecución cuando se gira un dispositivo Android. Android hace esto para que sea fácil cargar recursos alternativos, como diseños y recursos drawable que está diseñada específicamente para una orientación determinada. Cuando se reinicia, la actividad pierde cualquier estado transitorio, es posible que haya almacenado en variables de clase local. Por lo tanto, si una actividad es que dependen del estado, debe conservar su estado en el nivel de aplicación. Una aplicación necesita manejar guardado y restauración de estado de la aplicación que desea conservar en todos los cambios de orientación.

Para obtener más información de estado de persistencia en Android, consulte el [ciclo de vida de actividad](~/android/app-fundamentals/activity-lifecycle/index.md) guía.


## <a name="summary"></a>Resumen

En este artículo trata cómo usar las capacidades integradas de Android para trabajar con una rotación. En primer lugar, explica cómo usar el sistema de recursos de Android para crear aplicaciones de orientación. A continuación, se han mostrado cómo agregar controles en el código, así como cómo controlar manualmente los cambios de orientación.



## <a name="related-links"></a>Vínculos relacionados

- [Demostración de giro (ejemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/RotationDemo/)
- [Ciclo de vida de la actividad](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Controlar los cambios en tiempo de ejecución](http://developer.android.com/guide/topics/resources/runtime-changes.html)
- [Cambio de orientación de pantalla rápida](http://android-developers.blogspot.com/2009/02/faster-screen-orientation-change.html)
