---
title: Control de giro
description: "En este tema se describe cómo controlar los cambios de orientación de dispositivo en Xamarin.Android. Se describe cómo trabajar con el sistema Android recursos para cargar automáticamente los recursos para una orientación de dispositivo en particular, así como el modo controlar mediante programación la orientación cambia."
ms.topic: article
ms.prod: xamarin
ms.assetid: 6D33ADF7-ED81-0256-479D-D9E3787A76B0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: eb310b13a97e345bab68bf4e878f81a6187da691
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="handling-rotation"></a>Control de giro

_En este tema se describe cómo controlar los cambios de orientación de dispositivo en Xamarin.Android. Se describe cómo trabajar con el sistema Android recursos para cargar automáticamente los recursos para una orientación de dispositivo en particular, así como el modo controlar mediante programación la orientación cambia._

<a name="Overview" />

## <a name="overview"></a>Información general

Dado que los dispositivos móviles rotan fácilmente, giro integrado es una característica estándar en sistemas operativos móviles. Android proporciona un marco sofisticado para tratar con rotación dentro de las aplicaciones, si la interfaz de usuario se crea mediante declaración en XML o mediante programación en código. Al procesar automáticamente los cambios de diseño declarativo en un dispositivo girado, una aplicación puede beneficiarse de la estrecha integración con el sistema de recursos Android. Para el diseño mediante programación, se deben realizar cambios manualmente. Esto permite un control más preciso en tiempo de ejecución, pero a costa de más trabajo para el programador. Una aplicación puede elegir no participar en el reinicio de la actividad y tomar el control manual de los cambios de orientación.

Esta guía examina los siguientes temas de orientación:

-   **Rotación de diseño declarativo** &ndash; cómo usar el sistema Android recursos para compilar aplicaciones compatibles con orientación, incluyendo cómo cargar diseños y drawables para las orientaciones determinadas.

-   **Rotación de diseño mediante programación** &ndash; cómo agregar controles mediante programación, así como cómo controlar manualmente los cambios de orientación.

<a name="Handling_Rotation_Declaratively_with_Layouts" />

## <a name="handling-rotation-declaratively-with-layouts"></a>Control de giro mediante declaración con diseños

Al incluir archivos en carpetas que siguen las convenciones de nomenclatura, Android carga automáticamente los archivos adecuados cuando cambia la orientación.
Esto incluye compatibilidad para:

-   *Recursos de diseño* &ndash; especificar qué archivos de diseño se aumenten para cada orientación.

-   *Puede dibujar recursos* &ndash; especifica qué drawables se cargan para cada orientación.

<a name="Layout_Resources" />

### <a name="layout-resources"></a>Recursos de diseño

De forma predeterminada, los archivos XML Android (AXML) se incluyen en el **recursos/diseño** carpeta se usan para las vistas de representación para una actividad. Recursos de esta carpeta se usan para la orientación vertical y horizontal si no hay recursos adicionales de diseño se proporcionan específicamente para el entorno. Tenga en cuenta la estructura de proyecto creada por la plantilla de proyecto predeterminada:

[ ![Estructura de plantilla de proyecto predeterminada](handling-rotation-images/00.png)](handling-rotation-images/00.png)

Este proyecto crea una sola **Main.axml** un archivo en el **recursos/diseño** carpeta. Cuando la actividad `OnCreate` se llama al método, aumenta la vista definida en **Main.axml,** que declara un botón, como se muestra en el código XML siguiente:

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

Si el dispositivo se gira a la orientación horizontal, la actividad del `OnCreate` se vuelve a llamar al método y el mismo **Main.axml** se aumenta el archivo, como se muestra en la captura de pantalla siguiente:

[ ![Mismo pero en la pantalla con orientación horizontal](handling-rotation-images/01-sml.png)](handling-rotation-images/01.png)

<a name="Orientation-Specific_Layouts" />

#### <a name="orientation-specific-layouts"></a>Diseños de orientación específica

Además de la carpeta de diseño (que tiene como valor predeterminado en vertical y también se puede llamar explícitamente *diseño-port* mediante la inclusión de una carpeta denominada `layout-land`), una aplicación puede definir las vistas que necesita en horizontal sin ningún código cambios.

Suponga que el **Main.axml** archivo contiene el siguiente código XML:

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

Si una carpeta denominada tierra de diseño que contiene más **Main.axml** archivo se agrega al proyecto, ahora dará como resultado Android cargar recién agregada que infla el diseño en horizontal **Main.axml.** Tenga en cuenta la versión horizontal de la **Main.axml** archivo que contiene el código siguiente (por motivos de simplicidad, este código XML es similar a la versión de vertical predeterminada del código, pero usa una cadena diferente en el `TextView`):

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

Ejecutar este código y girar el dispositivo de vertical a horizontal muestran la nueva carga de XML, tal y como se muestra a continuación:

[ ![Capturas de pantalla vertical y horizontal imprimir el modo vertical](handling-rotation-images/02.png)](handling-rotation-images/02.png)

<a name="Drawable_Resources" />

### <a name="drawable-resources"></a>Puede dibujar recursos

Durante la rotación, Android trata recursos pueden dibujar de forma similar a los recursos de diseño. En este caso, el sistema obtiene la drawables desde la **/puede dibujar recursos** y **recursos, puede dibujar-terrenos** carpetas, respectivamente.

Por ejemplo, el proyecto incluye una imagen denominada Monkey.png en el **/puede dibujar recursos** carpeta, donde la puede dibujar se hace referencia desde una `ImageView` en XML similar al siguiente:

```xml
<ImageView
  android:layout_height="wrap_content"
  android:layout_width="wrap_content"
  android:src="@drawable/monkey"
  android:layout_centerVertical="true"
  android:layout_centerHorizontal="true" />
```

Supongamos además que una versión diferente de **Monkey.png** se incluye en **recursos, puede dibujar-terrenos**. Igual que con los archivos de diseño, cuando el dispositivo está había girado, los cambios con estas características para la orientación determinada, tal y como se muestra a continuación:

[ ![Versión diferente de Monkey.png se muestra en los modos vertical y horizontal](handling-rotation-images/03.png)](handling-rotation-images/03.png)

<a name="Handling_Rotation_Programmatically" />

## <a name="handling-rotation-programmatically"></a>Rotación de control mediante programación

A veces se definen diseños en el código. Esto puede ocurrir por diversos motivos, incluyendo las limitaciones técnicas, preferencias de desarrollador, etcetera. Cuando se agregan controles mediante programación, debe tener una aplicación manualmente en cuenta para la orientación del dispositivo, que se controla automáticamente cuando se usan los recursos XML.

<a name="Adding_Controls_in_Code" />

### <a name="adding-controls-in-code"></a>Agregar controles en el código

Para agregar controles mediante programación, una aplicación tiene que realizar los pasos siguientes:

-  Crear un diseño.
-  Establecer parámetros de diseño.
-  Crear controles.
-  Establecer parámetros de diseño del control.
-  Agregar controles al diseño.
-  Establecer el diseño como la vista de contenido.

Por ejemplo, considere la posibilidad de una interfaz de usuario que se compone de una sola `TextView` control agregado a un `RelativeLayout`, tal y como se muestra en el código siguiente.

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

Este código crea una instancia de un `RelativeLayout` clase y se establece su `LayoutParameters` propiedad. La `LayoutParams` clase es modo de Android de encapsulación de cómo se colocan los controles de una manera reutilizable. Una vez que se crea una instancia de un diseño, los controles se pueden crear y agregar a ella. Los controles también tienen `LayoutParameters`, como el `TextView` en este ejemplo. Después de la `TextView` se crea, éste se agrega a la `RelativeLayout` y estableciendo el `RelativeLayout` como los resultados de la vista de contenido en la aplicación mostrando el `TextView` tal como se muestra:

[ ![Botón de contador de incremento que se muestra en los modos vertical y horizontal](handling-rotation-images/04.png)](handling-rotation-images/04.png)

<a name="Detecting_Orientation_in_Code" />

### <a name="detecting-orientation-in-code"></a>Detectando orientación en código

Si una aplicación intenta cargar una interfaz de usuario diferente para cada orientación cuando `OnCreate` se denomina (Esto ocurre cada vez que un dispositivo se gira), debe detectar la orientación y, a continuación, cargue el código de la interfaz de usuario deseado. El sistema Android tiene una clase denominada el `WindowManager`, que puede utilizarse para determinar la rotación del dispositivo actual a través de la `WindowManager.DefaultDisplay.Rotation` propiedad, tal y como se muestra a continuación:

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

Este código establece la `TextView` sea posicionadas 100 píxeles desde la parte superior izquierda de la pantalla, animar automáticamente al nuevo diseño, cuando gira horizontal, como se muestra aquí:

[ ![Estado de vista se conserva en los modos vertical y horizontal](handling-rotation-images/05.png)](handling-rotation-images/05.png)

<a name="Preventing_Activity_Restart" />

### <a name="preventing-activity-restart"></a>Evitar el reinicio de la actividad

Además de administrar todo el contenido de `OnCreate`, una aplicación también puede evitar que una actividad se reinicia cuando cambia la orientación estableciendo `ConfigurationChanges` en el `ActivityAttribute` como se indica a continuación:

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
```
Ahora cuando se gira el dispositivo, la actividad no se reinicia. Con el fin de controlar manualmente el cambio de orientación en este caso, una actividad puede reemplazar la `OnConfigurationChanged` método y determinar la orientación de la `Configuration` objeto que se pasa, como se muestra en la nueva implementación de la actividad siguiente:

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

Aquí el `TextView's` parámetros de diseño se inicializan para horizontal y vertical. Las variables de clase contienen los parámetros, junto con el `TextView` propio, ya que la actividad no se pueden volver a crear cuando cambia la orientación. El código sigue usando el `surfaceOrientartion` en `OnCreate` para establecer el diseño inicial para la `TextView`. Después de eso, `OnConfigurationChanged` controla todos los cambios de diseño posteriores.

Cuando se ejecuta la aplicación, Android carga los cambios de la interfaz de usuario como la rotación de dispositivos se produce y no reinicia la actividad.

<a name="Preventing_Activity_Restart_for_Declarative_Layouts" />

## <a name="preventing-activity-restart-for-declarative-layouts"></a>Evitar el reinicio de actividad para diseños declarativos

También pueden evitar que los reinicios de actividad ocasionados por la rotación de dispositivos si se define el diseño en XML. Por ejemplo, podemos usar este enfoque si desea impedir el reinicio de una actividad (por motivos de rendimiento, quizás) y no es necesario cargar nuevos recursos para las orientaciones diferentes.

Para ello, se siga el mismo procedimiento que se usa con un diseño mediante programación. Basta con establecer `ConfigurationChanges` en el `ActivityAttribute`, como hicimos el `CodeLayoutActivity` anteriormente. Cualquier código que deba ejecutar de nuevo se puede implementar el cambio de orientación en el `OnConfigurationChanged` método.

<a name="Maintaining_State_During_Orientation_Changes" />

## <a name="maintaining-state-during-orientation-changes"></a>Mantener el estado durante los cambios de orientación

Si el control de giro de forma declarativa o mediante programación, todas las aplicaciones de Android deben implementar las mismas técnicas para administrar el estado cuando cambia la orientación del dispositivo. Administrar el estado es importante porque el sistema reinicia una actividad en ejecución cuando se gira un dispositivo Android. Android hace esto para que resulte sencillo cargar recursos alternativos, como diseños y drawables que están diseñadas específicamente para una orientación concreta. Cuando se reinicia, la actividad pierde cualquier estado transitorio, es posible que haya almacenado en variables de clase local. Por lo tanto, si una actividad dependen del estado, debe conservar su estado en el nivel de aplicación. Una aplicación deba controlar guardar y restaurar el estado de la aplicación que se va a conservar en todos los cambios de orientación.

Para obtener más información sobre mantener el estado en Android, consulte el [ciclo de vida de actividad](~/android/app-fundamentals/activity-lifecycle/index.md) guía.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se trata cómo usar las características integradas de Android para trabajar con rotación. En primer lugar, explica cómo usar el sistema Android recursos para crear aplicaciones con reconocimiento de orientación. A continuación, se han mostrado cómo agregar controles en el código, así como cómo controlar manualmente los cambios de orientación.



## <a name="related-links"></a>Vínculos relacionados

- [Demostración de rotación (ejemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/RotationDemo/)
- [Ciclo de vida de actividad](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Gestionar los cambios en tiempo de ejecución](http://developer.android.com/guide/topics/resources/runtime-changes.html)
- [Cambio de orientación de pantalla rápida](http://android-developers.blogspot.com/2009/02/faster-screen-orientation-change.html)
