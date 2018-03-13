---
title: "Creación de una cara de inspección"
description: "Esta guía explica cómo implementar un servicio de cara de inspección personalizado para desgaste Android. Se proporcionan instrucciones paso a paso para crear un eliminada servicio de cara de inspección digital, seguida de más código para crear una pantalla de estilo de señales analógicas watch."
ms.topic: article
ms.prod: xamarin
ms.assetid: 4D3F9A40-A820-458D-A12A-D784BB11F643
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: fb3a2a9e60bda2a99a719bf75d23c29d42a94bdb
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="creating-a-watch-face"></a>Creación de una cara de inspección

_Esta guía explica cómo implementar un servicio de cara de inspección personalizado para desgaste Android. Se proporcionan instrucciones paso a paso para crear un eliminada servicio de cara de inspección digital, seguida de más código para crear una pantalla de estilo de señales analógicas watch._

## <a name="overview"></a>Información general 

En este tutorial, se crea un servicio de cara de inspección básica para ilustrar las operaciones básicas de creación de una imagen personalizada de Android desgaste inspección. El servicio de cara de inspección inicial muestra una simple inspección digital que muestra la hora actual en horas y minutos: 

[![Pantalla digital watch](creating-a-watchface-images/01-initial-face.png "captura de pantalla de ejemplo de la pantalla inicial watch digital")](creating-a-watchface-images/01-initial-face.png#lightbox)

Después de esta pantalla del reloj digital ha sido desarrollado y probado, se agregará más código actualizar a una más sofisticado pantalla del reloj analógico con tres manos: 

[![Pantalla del reloj analógico](creating-a-watchface-images/02-example-watchface.png "captura de pantalla de ejemplo de la pantalla del reloj analógico final")](creating-a-watchface-images/02-example-watchface.png#lightbox)

Ver los servicios de cara son agrupados y se instala como parte de una aplicación de uso. En los ejemplos siguientes, `MainActivity` contiene nada más que el código de la plantilla de aplicación de uso para que el servicio de cara de inspección puede empaquetar e implementar a la observación inteligente como parte de la aplicación. De hecho, esta aplicación servirá únicamente como vehículo para obtener el servicio de cara de inspección cargado en el dispositivo de uso (o el emulador) para depurar y probar. 

## <a name="requirements"></a>Requisitos

Para implementar un servicio de cara de inspección, se requiere lo siguiente:

-   Android 5.0 (API nivel 21) o superior en el emulador o dispositivo de uso.

-   El [Xamarin Android desgaste compatibilidad con bibliotecas](https://www.nuget.org/packages/Xamarin.Android.Wear) debe agregarse al proyecto Xamarin.Android. 

Aunque Android 5.0 es la API mínima nivel para implementar un servicio de cara de inspección, Android 5.1 o versiones posteriores, se recomienda. Android usan dispositivos que ejecutan Android 5.1 (API 22) o superior permitir que las aplicaciones de uso controlar lo que se muestra en la pantalla mientras el dispositivo está en bajo consumo de energía *ambiente* modo. Cuando el dispositivo deja bajo consumo de energía *ambiente* modo, se encuentra en *interactivo* modo. Para obtener más información acerca de estos modos, consulte [mantener su aplicación Visible](https://developer.android.com/training/wearables/apps/always-on.html). 


## <a name="start-an-app-project"></a>Iniciar un proyecto de aplicación

Crear un nuevo proyecto de Android desgaste denominado **WatchFace** (para obtener más información acerca de cómo crear nuevos proyectos de Xamarin.Android, consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md)):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Cuadro de diálogo nuevo proyecto](creating-a-watchface-images/03-wear-project-vs-sml.png "Seleccionar aplicación usan en el cuadro de diálogo nuevo proyecto")](creating-a-watchface-images/03-wear-project-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Cuadro de diálogo nuevo proyecto](creating-a-watchface-images/03-wear-project-xs-sml.png "Seleccionar aplicación usan en el cuadro de diálogo nuevo proyecto")](creating-a-watchface-images/03-wear-project-xs.png#lightbox)

-----


Establece el nombre del paquete en `com.xamarin.watchface`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Valor del nombre del paquete](creating-a-watchface-images/04-package-name-vs.png "establece el nombre del paquete en com.xamarin.watchface")](creating-a-watchface-images/04-package-name-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Valor del nombre del paquete](creating-a-watchface-images/04-package-name-xs.png "establece el nombre del paquete en com.xamarin.watchface")](creating-a-watchface-images/04-package-name-xs.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Además, desplácese hacia abajo y habilite la **INTERNET** y **WAKE_LOCK** permisos: 

[![Los permisos necesarios](creating-a-watchface-images/05-required-permissions-vs.png "permisos de INTERNET habilitar y WAKE_LOCK")](creating-a-watchface-images/05-required-permissions-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Establezca la versión mínima Android en **Android 5.1 (API nivel 22)**. Además, habilite la **Internet** y **WakeLock** permisos:

[![Los permisos necesarios](creating-a-watchface-images/05-required-permissions-xs.png "permisos de Internet habilitar y WakeLock")](creating-a-watchface-images/05-required-permissions-xs.png#lightbox)

-----

A continuación, descargar [preview.png](creating-a-watchface-images/preview.png) &ndash; Esto se agregará a la **drawables** carpeta más adelante en este tutorial.


## <a name="add-the-xamarin-android-wear-package"></a>Agregue el paquete de uso de Xamarin para Android

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Iniciar el Administrador de paquetes de NuGet (en Visual Studio, haga clic en **referencias** en el **el Explorador de soluciones** y seleccione **administrar paquetes de NuGet...** ). Actualice el proyecto a la versión estable más reciente de **Xamarin.Android.Wear**: 

[![Agregar administrador de paquetes de NuGet](creating-a-watchface-images/06-add-wear-pkg-vs-sml.png "agregar el paquete Xamarin.Android.Wear")](creating-a-watchface-images/06-add-wear-pkg-vs.png#lightbox)

A continuación, si **Xamarin.Android.Support.v13** está instalado, desinstálelo:

[![Quitar Administrador de paquetes de NuGet](creating-a-watchface-images/07-uninstall-v13-sml.png "Xamarin.Support.v13 quitar")](creating-a-watchface-images/07-uninstall-v13.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Iniciar el Administrador de paquetes de NuGet (en Visual Studio para Mac, haga clic en **paquetes** en el **panel de soluciones** y seleccione **agregar paquetes** ). Actualice el proyecto a la versión estable más reciente de **Xamarin.Android.Wear**: 

[![Agregar administrador de paquetes de NuGet](creating-a-watchface-images/06-add-wear-pkg-xs-sml.png "agregar el paquete Xamarin.Android.Wear")](creating-a-watchface-images/06-add-wear-pkg-xs.png#lightbox)

-----


Compilar y ejecutar la aplicación en un emulador o dispositivo de desgaste (para obtener más información acerca de cómo hacerlo, consulte la [Introducción](~/android/wear/get-started/index.md) guía). Debería ver la siguiente pantalla de aplicación en el dispositivo de uso:

[![Captura de pantalla de aplicación](creating-a-watchface-images/08-app-screen.png "pantalla de la aplicación en el dispositivo de uso")](creating-a-watchface-images/08-app-screen.png#lightbox)

En este punto, la aplicación de uso básica no tiene la funcionalidad de cara de inspección porque todavía no proporciona una implementación del servicio de cara de inspección. Este servicio se agregará a continuación. 

 
## <a name="canvaswatchfaceservice"></a>CanvasWatchFaceService

Android implementa desgaste ver caras a través de la `CanvasWatchFaceService` clase. `CanvasWatchFaceService` se deriva de `WatchFaceService`, que a su vez se deriva de `WallpaperService` tal como se muestra en el diagrama siguiente: 

[![Diagrama de herencia](creating-a-watchface-images/09-inheritance-diagram-sml.png "CanvasWatchFaceService diagrama de herencia")](creating-a-watchface-images/09-inheritance-diagram.png#lightbox)

`CanvasWatchFaceService` incluye un anidada `CanvasWatchFaceService.Engine`; crea una instancia de un `CanvasWatchFaceService.Engine` objeto que realiza el trabajo real de la pantalla del reloj de dibujo. `CanvasWatchFaceService.Engine` se deriva de `WallpaperService.Engine` tal y como se muestra en el diagrama anterior. 

No se muestra en este diagrama es una `Canvas` que `CanvasWatchFaceService` utiliza para dibujar la pantalla del reloj &ndash; esto `Canvas` se pasa a través de la `OnDraw` método tal como se describe a continuación. 

En las secciones siguientes, se creará un servicio de cara de inspección personalizado, siga estos pasos: 

1.  Define una clase denominada `MyWatchFaceService` que se deriva de `CanvasWatchFaceService`. 

2.  Dentro de `MyWatchFaceService`, cree una clase anidada denominada `MyWatchFaceEngine` que se deriva de `CanvasWatchFaceService.Engine`. 

3.  En `MyWatchFaceService`, implementar un `CreateEngine` método que crea una instancia `MyWatchFaceEngine` y lo devuelve.

4.  En `MyWatchFaceEngine`, implementar la `OnCreate` método para crear el estilo de fuente de inspección y realizar otras tareas de inicialización. 

5.  Implemente el `OnDraw` método `MyWatchFaceEngine`. Se llama a este método siempre que sea necesario volver a dibujar la pantalla del reloj (es decir, *invalida*). `OnDraw` es el método que dibuja (y vuelve a dibujarse) inspección cara elementos como la hora, minuto y segundo manos. 

6.  Implemente el `OnTimeTick` método `MyWatchFaceEngine`. 
    `OnTimeTick` se llama al menos una vez por minuto (en los modos de ambiente e interactivos) o cuando ha cambiado la fecha y hora. 

Para obtener más información acerca de `CanvasWatchFaceService`, vea el Android [CanvasWatchFaceService](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.html) la documentación de API.
De forma similar, [CanvasWatchFaceService.Engine](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine.html) explica la implementación real de la pantalla del reloj.


### <a name="add-the-canvaswatchfaceservice"></a>Agregar el CanvasWatchFaceService

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Agregar un nuevo archivo denominado **MyWatchFaceService.cs** (en Visual Studio, haga clic en **WatchFace** en el **el Explorador de soluciones**, haga clic en **Agregar > nuevo elemento...** y seleccione **clase**).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Agregar un nuevo archivo denominado **MyWatchFaceService.cs** (en Visual Studio para Mac, haga clic en el **WatchFace** proyecto de equipo y haga clic en **Agregar > nuevo archivo...** y seleccione **clase vacía**). 

-----

Reemplace el contenido de este archivo por el código siguiente: 

```csharp
using System;
using Android.Views;
using Android.Support.Wearable.Watchface;
using Android.Service.Wallpaper;
using Android.Graphics;

namespace WatchFace
{
    class MyWatchFaceService : CanvasWatchFaceService
    {
        public override WallpaperService.Engine OnCreateEngine()
        {
            return new MyWatchFaceEngine(this);
        }

        public class MyWatchFaceEngine : CanvasWatchFaceService.Engine
        {
            CanvasWatchFaceService owner;
            public MyWatchFaceEngine (CanvasWatchFaceService owner) : base(owner)
            {
                this.owner = owner;
            }
        }
    }
}
```

`MyWatchFaceService` (derivado de `CanvasWatchFaceService`) es el "programa principal" de la pantalla del reloj. `MyWatchFaceService` implementa un solo método, `OnCreateEngine`, que crea y devuelve un `MyWatchFaceEngine` objeto (`MyWatchFaceEngine` se deriva de `CanvasWatchFaceService.Engine`). La creación de instancias `MyWatchFaceEngine` objeto debe devolverse como un `WallpaperService.Engine`. La encapsulación `MyWatchFaceService` objeto se pasa al constructor. 

`MyWatchFaceEngine` es la implementación de la cara de inspección real &ndash; contiene el código que se dibuja la pantalla del reloj. También controla los eventos del sistema, como los cambios de la pantalla (modos ambiente interactivo, pantalla si se desactiva, etcetera.). 

 
### <a name="implement-the-engine-oncreate-method"></a>Implemente el método de OnCreate de motor

El `OnCreate` método inicializa la pantalla del reloj. Agregue el siguiente campo a `MyWatchFaceEngine`: 

```csharp
Paint hoursPaint;
```

Esto `Paint` objeto se utilizará para dibujar la hora actual en la pantalla del reloj. A continuación, agregue el método siguiente a `MyWatchFaceEngine`: 

```csharp
public override void OnCreate(ISurfaceHolder holder)
{
    base.OnCreate (holder);

    SetWatchFaceStyle (new WatchFaceStyle.Builder(owner)
        .SetCardPeekMode (WatchFaceStyle.PeekModeShort)
        .SetBackgroundVisibility (WatchFaceStyle.BackgroundVisibilityInterruptive)
        .SetShowSystemUiTime (false)
        .Build ());

    hoursPaint = new Paint();
    hoursPaint.Color = Color.White;
    hoursPaint.TextSize = 48f;
}
```

`OnCreate` se llama en breve después `MyWatchFaceEngine` se inicia. Configura la `WatchFaceStyle` (que controla cómo el dispositivo desgaste interactúa con el usuario) y crea una instancia de la `Paint` objeto que se usará para mostrar la hora. 

La llamada a `SetWatchFaceStyle` hace lo siguiente:

1.  Conjuntos de *modo peek* a `PeekModeShort`, lo que hace que las notificaciones para que aparezca como tarjetas de pequeña "rápida" en la pantalla. 

2.  Establece la visibilidad de fondo en `Interruptive`, lo que hace que el fondo de una tarjeta de información para mostrar solo brevemente si representa una notificación molestos.

3.  Deshabilita la hora de interfaz de usuario del sistema predeterminada desde la que se va a dibujar en la pantalla del reloj para que la pantalla del reloj personalizado puede mostrar la hora en su lugar.

Para obtener más información sobre estas y otras opciones de estilo de fuente de inspección, vea el Android [WatchFaceStyle.Builder](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceStyle.Builder.html) la documentación de API.

Después de `SetWatchFaceStyle` completa, `OnCreate` crea una instancia de la `Paint` objeto (`hoursPaint`) y establece su color en blanco y el tamaño del texto a 48 píxeles ([TextSize](https://developer.android.com/reference/android/graphics/Paint.html#setTextSize%28float%29) debe especificarse en píxeles). 


### <a name="implement-the-engine-ondraw-method"></a>Implemente el método OnDraw de motor

El `OnDraw` método es el más importante quizás `CanvasWatchFaceService.Engine` método &ndash; es el método que realmente dibuja ver elementos de cara como dígitos y cara manecillas del reloj. En el ejemplo siguiente, dibuja una cadena de hora en la pantalla del reloj.
Agregue el método siguiente a `MyWatchFaceEngine`:

```csharp
public override void OnDraw (Canvas canvas, Rect frame)
{
    var str = DateTime.Now.ToString ("h:mm tt");
    canvas.DrawText (str, 
        (float)(frame.Left + 70), 
        (float)(frame.Top  + 80), hoursPaint);
}
```

Cuando se llama Android `OnDraw`, pasa un valor en un `Canvas` instancia y los límites en el que se puede dibujar la imagen. En el ejemplo de código anterior, `DateTime` se usa para calcular la hora actual en horas y minutos (en formato de 12 horas). La cadena de hora resultante se dibuja en el lienzo utilizando el `Canvas.DrawText` método. La cadena aparecerá 70 píxeles sobre desde el borde izquierdo y 80 píxeles hacia abajo desde el borde superior. 

Para obtener más información sobre la `OnDraw` método, vea el Android [onDraw](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine.html#onDraw(android.graphics.Canvas, android.graphics.Rect)) documentación de API.


### <a name="implement-the-engine-ontimetick-method"></a>Implemente el método OnTimeTick de motor

Android llama periódicamente a la `OnTimeTick` método para actualizar la hora en que se muestra en la pantalla del reloj. Se llama al menos una vez por minuto (en los modos ambiente e interactivos), o cuando la fecha y hora o zona horaria han cambiado. Agregue el método siguiente a `MyWatchFaceEngine`: 

```csharp
public override void OnTimeTick()
{
    Invalidate();
}
```

Esta implementación de `OnTimeTick` simplemente llama `Invalidate`. El `Invalidate` programaciones de método `OnDraw` para volver a dibujar la pantalla del reloj. 

Para obtener más información sobre la `OnTimeTick` método, vea el Android [onTimeTick](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onTimeTick()) la documentación de API.

 
## <a name="register-the-canvaswatchfaceservice"></a>Registrar el CanvasWatchFaceService

`MyWatchFaceService` debe estar registrado en el **AndroidManifest.xml** de la aplicación de uso asociada. Para ello, agregue el siguiente código XML para el `<application>` sección: 

```xml
<service 
    android:name="watchface.MyWatchFaceService" 
    android:label="Xamarin Sample" 
    android:allowEmbedded="true" 
    android:taskAffinity="" 
    android:permission="android.permission.BIND_WALLPAPER">
    <meta-data 
        android:name="android.service.wallpaper" 
        android:resource="@xml/watch_face" />
    <meta-data 
        android:name="com.google.android.wearable.watchface.preview" 
        android:resource="@drawable/preview" />
    <intent-filter>
        <action android:name="android.service.wallpaper.WallpaperService" />
        <category android:name="com.google.android.wearable.watchface.category.WATCH_FACE" />
    </intent-filter>
</service>
```

Este código XML hace lo siguiente:

1.  Establece el `android.permission.BIND_WALLPAPER` permiso. Este permiso concede el permiso de servicio de cara de inspección para cambiar el papel tapiz del sistema en el dispositivo. Tenga en cuenta que este permiso debe establecerse en el `<service>` sección en lugar de en el exterior `<application>` sección. 

2.  Define un `watch_face` recursos. Este recurso es un archivo XML corto que declara un `wallpaper` recursos (este archivo se creará en la siguiente sección). 

3.  Declara una imagen puede dibujar llamada `preview` que se mostrará de la pantalla de selección del selector de inspección. 

4.  Incluye un `intent-filter` para informar a Android que `MyWatchFaceSevice` va a mostrar una pantalla del reloj. 

Que se haya completado el código de las opciones básicas `WatchFace` ejemplo. El siguiente paso es agregar los recursos necesarios.

 
## <a name="add-resource-files"></a>Agregar archivos de recursos

Antes de poder ejecutar el servicio de inspección, debe agregar el **watch_face** recursos y la imagen de vista previa. En primer lugar, cree un nuevo archivo XML en **Resources/xml/watch_face.xml** y reemplazar su contenido con el siguiente código XML: 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<wallpaper xmlns:android="http://schemas.android.com/apk/res/android" />
```

Establece la acción de compilación de este archivo en **AndroidResource**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Acción de compilación](creating-a-watchface-images/10-android-resource-vs.png "conjunto acción a AndroidResource de compilación")](creating-a-watchface-images/10-android-resource-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Acción de compilación](creating-a-watchface-images/10-android-resource-xs.png "conjunto acción a AndroidResource de compilación")](creating-a-watchface-images/10-android-resource-xs.png#lightbox)

-----

Este archivo de recursos define un sencillo `wallpaper` elemento que se usará para la pantalla del reloj. 

Si no lo ha hecho todavía, descargue [preview.png](creating-a-watchface-images/preview.png). Instalar en **Resources/drawable/preview.png**. No olvide agregar este archivo a la `WatchFace` proyecto. Esta imagen de vista previa se muestra al usuario en el selector de cara de inspección en el dispositivo de uso. Para crear una imagen de vista previa para su propia pantalla watch, puede tomar una captura de pantalla de la pantalla del reloj mientras se está ejecutando. (Para obtener más información sobre cómo obtener capturas de pantalla de los dispositivos de uso, consulte [tomar capturas de pantalla](~/android/wear/deploy-test/debug-on-device.md#screenshots)). 


## <a name="try-it"></a>¡Inténtelo!

Compilar e implementar la aplicación en el dispositivo de uso. Debe aparecer la pantalla de la aplicación de uso como antes. Siga este procedimiento para habilitar la pantalla del reloj nueva: 

1.  Deslice el dedo hacia la derecha hasta ver el fondo de la pantalla de inspección. 

2.  Tocar y mantener en cualquier lugar en el fondo de la pantalla durante dos segundos.

3.  Deslice el dedo de izquierda a derecha para ir a través de las caras de inspección distintos. 

4.  Seleccione el **Xamarin ejemplo** ver cara (se muestra a la derecha): 

    [![Selector de Watchface](creating-a-watchface-images/11-watchface-picker.png "deslice el dedo para localizar la pantalla del reloj de ejemplo de Xamarin")](creating-a-watchface-images/11-watchface-picker.png#lightbox)

5.  Pulse la **Xamarin ejemplo** ver cara para seleccionarlo. 

Esto cambia la pantalla del reloj del dispositivo desgaste para usar el servicio de cara de inspección personalizado implementado hasta el momento: 

[![Pantalla digital watch](creating-a-watchface-images/12-digital-watchface.png "inspección digital personalizado con el dispositivo de uso")](creating-a-watchface-images/12-digital-watchface.png#lightbox)

Esto es una cara de inspección relativamente tosco porque la implementación de aplicación es lo mínima (por ejemplo, no incluye un fondo de cara de inspección y no llama a `Paint` métodos de suavizado para mejorar el aspecto). Sin embargo, para implementar la funcionalidad básica que se requiere para crear una pantalla de watch personalizado. 

En la sección siguiente, esta pantalla del reloj se actualiza a una implementación más sofisticada. 

 
## <a name="upgrading-the-watch-face"></a>Actualizar la pantalla del reloj

En el resto de este tutorial, `MyWatchFaceService` se actualiza para mostrar una pantalla de estilo de señales analógicas watch y se amplía para admitir más características. Para crear la pantalla del reloj actualizado se agregarán las siguientes capacidades: 

1.  Indica el tiempo con analógica hora, minuto y segundo manos.

2.  Responde a los cambios en la visibilidad.

3.  Responda a los cambios entre el modo de ambiente y el modo interactivo. 

4.  Lee las propiedades del dispositivo desgaste subyacente.

5.  Actualiza automáticamente el tiempo cuando realiza un cambio de zona horaria. 

Antes de implementar los cambios de código a continuación, descargar [drawable.zip](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/Resources/drawable.zip?raw=true), descomprímalo y mover los archivos .png descomprimida **/puede dibujar recursos** (sobrescribir anterior **preview.png**). Agregar los nuevos archivos .png en el `WatchFace` proyecto.


### <a name="update-engine-features"></a>Características del motor de actualización

El siguiente paso es actualización **MyWatchFaceService.cs** a una implementación que dibuja una pantalla del reloj analógico y admite las nuevas características. Reemplace el contenido de **MyWatchFaceService.cs** con la versión del código de cara de inspección de analógica [MyWatchFaceService.cs](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/WatchFace/MyWatchFaceService.cs) (puede cortar y pegar este origen existentes  **MyWatchFaceService.cs**). 

Esta versión de **MyWatchFaceService.cs** agrega más código a los métodos existentes e incluye métodos invalidados adicionales para agregar más funcionalidad. Las secciones siguientes proporcionan una visita guiada del código fuente.

#### <a name="oncreate"></a>OnCreate

La actualización **OnCreate** método configura el estilo de fuente de inspección que antes, pero incluye algunos pasos adicionales: 

1.  Establece la imagen de fondo el **xamarin_background** recursos que se encuentra en **Resources/drawable-hdpi/xamarin_background.png**. 

2.  Inicializa `Paint` objetos para dibujar la manecilla de las horas, la manecilla de los minutos y la manecilla de los segundos.

3.  Inicializa un `Paint` objeto para dibujar las marcas de hora alrededor del borde de la pantalla del reloj. 

4.  Crea un temporizador que llama el `Invalidate` método (renegociación) para que la manecilla de los segundos se volverá a dibujar cada segundo. Tenga en cuenta que este temporizador es necesario porque `OnTimeTick` llamadas `Invalidate` sólo una vez cada minuto.

Este ejemplo incluye sólo un **xamarin_background.png** imagen; sin embargo, puede que desee crear una imagen de fondo diferente para cada densidad de pantalla que admitirá la pantalla del reloj personalizado. 

#### <a name="ondraw"></a>OnDraw

La actualización **OnDraw** método dibuja una cara de inspección de estilo de señales analógicas siguiendo estos pasos: 

1.  Obtiene la hora actual, que ahora se mantiene en una `time` objeto. 

2.  Determina los límites de su centro y la superficie de dibujo.

3.  Dibuja el fondo, escalado para ajustarse a los dispositivos cuando se dibuja el fondo.

4.  Dibuja doce *tics* alrededor de la cara del reloj (correspondiente a las horas en la cara del reloj). 

5.  Calcula el ángulo, la rotación y la longitud de cada manecilla de inspección.

6.  Dibuja cada parte en la superficie de inspección. Tenga en cuenta que no se dibuja la manecilla de los segundos si el reloj está en modo de ambiente. 

 
#### <a name="onpropertieschanged"></a>OnPropertiesChanged

Se llama a este método para informar a `MyWatchFaceEngine` acerca de las propiedades del dispositivo desgaste (como el modo de ambiente de bits baja y grabación de protección). En `MyWatchFaceEngine`, este método comprueba solo para modo ambiente de bits baja (en modo de ambiente de bits baja, la pantalla es compatible con menos bits para cada color). 

Para obtener más información acerca de este método, vea el Android [onPropertiesChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onPropertiesChanged%28android.os.Bundle%29) la documentación de API.


#### <a name="onambientmodechanged"></a>OnAmbientModeChanged

Se llama a este método cuando el dispositivo desgaste entra o sale del modo de ambiente. En el `MyWatchFaceEngine` implementación, la pantalla del reloj deshabilita el suavizado cuando se encuentra en modo de ambiente. 

Para obtener más información acerca de este método, vea el Android [onAmbientModeChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onAmbientModeChanged%28boolean%29) la documentación de API.


#### <a name="onvisibilitychanged"></a>OnVisibilityChanged

Este método se llama cada vez que se convierte en el reloj visible u oculto. En `MyWatchFaceEngine`, este método registra o anula el registro del receptor de zona horaria (descrito a continuación) según el estado de visibilidad. 

Para obtener más información acerca de este método, vea el Android [onVisibilityChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onVisibilityChanged%28boolean%29) la documentación de API.

 
### <a name="time-zone-feature"></a>Función de zona horaria

El nuevo **MyWatchFaceService.cs** también incluye funcionalidad para actualizar la hora actual cada vez que la hora de la zona cambios (por ejemplo, mientras viaja en distintas zonas horarias). Cerca del final de **MyWatchFaceService.cs**, una vez Cambiar zona `BroadcastReceiver` corresponde a los que administra objetos por intención Cambiar zona horaria: 

```csharp
public class TimeZoneReceiver: BroadcastReceiver
{
    public Action<Intent> Receive { get; set; }
    public override void OnReceive (Context context, Intent intent)
    {
        if (Receive != null)
            Receive (intent);
    }
}
```

El `RegisterTimezoneReceiver` y `UnregisterTimezoneReceiver` métodos son llamados por el `OnVisibilityChanged` método. 
`UnregisterTimezoneReceiver` se llama cuando se cambia el estado de visibilidad de la pantalla del reloj a la oculto. Cuando la pantalla del reloj es visible de nuevo, `RegisterTimezoneReceiver` se denomina (vea la `OnVisibilityChanged` método). 

El motor de `RegisterTimezoneReceiver` método declara un controlador para este receptor de zona horaria `Receive` eventos; este controlador actualiza la `time` objeto con el nuevo tiempo cada vez que se traspasa una zona horaria: 

```csharp
timeZoneReceiver = new TimeZoneReceiver ();
timeZoneReceiver.Receive = (intent) => {
    time.Clear (intent.GetStringExtra ("time-zone"));
    time.SetToNow ();
};
```

Se crea un filtro de intención y se registra para el receptor de zona horaria:

```csharp
IntentFilter filter = new IntentFilter(Intent.ActionTimezoneChanged);
Application.Context.RegisterReceiver (timeZoneReceiver, filter);
```

El `UnregisterTimezoneReceiver` método anula el registro del receptor de zona horaria:

```csharp
Application.Context.UnregisterReceiver (timeZoneReceiver);
```

### <a name="run-the-improved-watch-face"></a>Ejecute la pantalla del reloj mejorada

Compilar e implementar la aplicación en el dispositivo de desgaste nuevo. Seleccione la pantalla del reloj en el selector de cara de inspección como antes. Se muestra la vista previa en el selector de inspección de la izquierda, y se muestra la pantalla del reloj nuevo a la derecha:

[![Pantalla del reloj analógico](creating-a-watchface-images/13-analog-watchface.png "mejorado cara analógico en el selector y en el dispositivo")](creating-a-watchface-images/13-analog-watchface.png#lightbox)

En esta captura de pantalla, se refiere a la manecilla de los segundos una vez por segundo. Cuando se ejecuta este código en un dispositivo de uso, la manecilla de los segundos desaparece cuando el reloj entra en modo de ambiente.

 
## <a name="summary"></a>Resumen

En este tutorial, un watchface personalizado desgaste Android se ha implementado y probado. El `CanvasWatchFaceService` y `CanvasWatchFaceService.Engine` se introdujeron las clases y los métodos esenciales de la clase del motor se implementaron para crear una cara simple inspección digital. Esta implementación se actualizó con más funcionalidad para crear una pantalla del reloj analógico y se implementaron métodos adicionales para controlar los cambios en la visibilidad, el modo de ambiente y las diferencias en las propiedades del dispositivo. Por último, se implementa un receptor de difusión de zona horaria para que el reloj automáticamente actualiza la hora de cuándo se traspasa una zona horaria. 


## <a name="related-links"></a>Vínculos relacionados

- [Creación de caras de inspección](https://developer.android.com/training/wearables/watch-faces/index.html)
- [Ejemplo de WatchFace](https://developer.xamarin.com/samples/monodroid/wear/WatchFace)
- [WatchFaceService.Engine](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html)
