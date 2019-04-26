---
title: Creación de una esfera del reloj para Android Wear 1.0
description: Esta guía explica cómo implementar un servicio de cara de supervisión personalizada para Android Wear 1.0. Se proporcionan instrucciones paso a paso para la creación de un eliminados servicio cara de inspección digital, seguida de más código para crear un reloj analógico de estilo.
ms.prod: xamarin
ms.assetid: 4D3F9A40-A820-458D-A12A-D784BB11F643
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/23/2018
ms.openlocfilehash: 067a39838fbfe3f1b33ac0d30b5069366b11e407
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61287278"
---
# <a name="creating-a-watch-face"></a>Creación de una esfera del reloj

_Esta guía explica cómo implementar un servicio de cara de supervisión personalizada para Android Wear 1.0. Se proporcionan instrucciones paso a paso para la creación de un eliminados servicio cara de inspección digital, seguida de más código para crear un reloj analógico de estilo._

## <a name="overview"></a>Información general

En este tutorial, se crea un servicio de cara de inspección básica para ilustrar los aspectos básicos de la creación de una esfera del reloj Android Wear 1.0 personalizada.
El servicio de cara de inspección inicial muestra una simple inspección digital que muestra la hora actual en horas y minutos:

[![Esfera del reloj digital](creating-a-watchface-images/01-initial-face.png "captura de pantalla de ejemplo de la pantalla del reloj digital inicial")](creating-a-watchface-images/01-initial-face.png#lightbox)

Después de este reloj digital está desarrollado y probado, se agrega más código para actualizarla a una más sofisticados esfera del reloj analógico con tres manos:

[![Esfera del reloj analógico](creating-a-watchface-images/02-example-watchface.png "captura de pantalla de ejemplo de la pantalla del reloj analógico final")](creating-a-watchface-images/02-example-watchface.png#lightbox)

Servicios de inspección cara agrupados e instalados como parte de una aplicación Wear 1.0. En los ejemplos siguientes, `MainActivity` contiene nada más que el código de la plantilla de aplicación Wear 1.0 para que el servicio de inspección cara puede se empaqueta e implementa en la observación inteligente como parte de la aplicación. De hecho, esta aplicación servirá únicamente como un vehículo para obtener el servicio de cara de inspección cargado en el dispositivo Wear 1.0 (o el emulador) para depurar y probar.

## <a name="requirements"></a>Requisitos

Para implementar un servicio de cara de inspección, se requiere lo siguiente:

-   Android 5.0 (API nivel 21) o superior en el desgaste del dispositivo o emulador.

-   El [Xamarin Android Wear Support Libraries](https://www.nuget.org/packages/Xamarin.Android.Wear) debe agregarse al proyecto de Xamarin.Android.

Aunque Android 5.0 es la API mínima nivel para implementar un servicio de cara de inspección, Android 5.1 o versiones posteriores, se recomienda. Android Wear dispositivos que ejecutan Android 5.1 (API 22) o permite mayor desgaste de aplicaciones controlar lo que se muestra en la pantalla mientras el dispositivo esté en baja energía *ambiente* modo. Cuando sale el dispositivo de bajo consumo de energía *ambiente* modo, se encuentra en *interactivo* modo. Para obtener más información acerca de estos modos, consulte [mantener su aplicación Visible](https://developer.android.com/training/wearables/apps/always-on.html).


## <a name="start-an-app-project"></a>Iniciar un proyecto de aplicación

Cree un nuevo proyecto de Android Wear 1.0 denominado **WatchFace** (para obtener más información acerca de cómo crear nuevos proyectos de Xamarin.Android, consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md)):

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Cuadro de diálogo nuevo proyecto](creating-a-watchface-images/03-wear-project-vs-sml.png "Seleccionar aplicación Wear en el cuadro de diálogo nuevo proyecto")](creating-a-watchface-images/03-wear-project-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Cuadro de diálogo nuevo proyecto](creating-a-watchface-images/03-wear-project-xs-sml.png "Seleccionar aplicación Wear en el cuadro de diálogo nuevo proyecto")](creating-a-watchface-images/03-wear-project-xs.png#lightbox)

-----


Establece el nombre del paquete en `com.xamarin.watchface`:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Valor del nombre del paquete](creating-a-watchface-images/04-package-name-vs.png "establece el nombre del paquete en com.xamarin.watchface")](creating-a-watchface-images/04-package-name-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Valor del nombre del paquete](creating-a-watchface-images/04-package-name-xs.png "establece el nombre del paquete en com.xamarin.watchface")](creating-a-watchface-images/04-package-name-xs.png#lightbox)

-----

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Además, desplácese hacia abajo y habilitar el **INTERNET** y **WAKE_LOCK** permisos:

[![Permisos necesarios](creating-a-watchface-images/05-required-permissions-vs.png "INTERNET habilitar y WAKE_LOCK permisos")](creating-a-watchface-images/05-required-permissions-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Establezca la versión Android mínima en **Android 5.1 (API nivel 22)**.
Además, habilite la **Internet** y **WakeLock** permisos:

[![Permisos necesarios](creating-a-watchface-images/05-required-permissions-xs.png "Internet habilitar y WakeLock permisos")](creating-a-watchface-images/05-required-permissions-xs.png#lightbox)

-----

A continuación, descargue [preview.png](creating-a-watchface-images/preview.png) &ndash; Esto se agregará a la **recursos drawable** carpeta más adelante en este tutorial.


## <a name="add-the-xamarinandroid-wear-package"></a>Agregue el paquete de desgaste de Xamarin.Android

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Iniciar el Administrador de paquetes de NuGet (en Visual Studio, haga clic en **referencias** en el **el Explorador de soluciones** y seleccione **administrar paquetes NuGet...** ). Actualice el proyecto a la última versión estable de **Xamarin.Android.Wear**:

[![Agregar administrador de paquetes de NuGet](creating-a-watchface-images/06-add-wear-pkg-vs-sml.png "agregar el paquete Xamarin.Android.Wear")](creating-a-watchface-images/06-add-wear-pkg-vs.png#lightbox)

A continuación, si **Xamarin.Android.Support.v13** está instalado, desinstálelo:

[![Quitar Administrador de paquetes de NuGet](creating-a-watchface-images/07-uninstall-v13-sml.png "Xamarin.Support.v13 quitar")](creating-a-watchface-images/07-uninstall-v13.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Iniciar el Administrador de paquetes de NuGet (en Visual Studio para Mac, haga clic en **paquetes** en el **panel de solución** y seleccione **agregar paquetes...** ). Actualice el proyecto a la última versión estable de **Xamarin.Android.Wear**:

[![Agregar administrador de paquetes de NuGet](creating-a-watchface-images/06-add-wear-pkg-xs-sml.png "agregar el paquete Xamarin.Android.Wear")](creating-a-watchface-images/06-add-wear-pkg-xs.png#lightbox)

-----


Compilar y ejecutar la aplicación en un emulador o dispositivo Wear (para obtener más información acerca de cómo hacerlo, consulte el [Introducción](~/android/wear/get-started/index.md) guía). Debería ver la siguiente pantalla de la aplicación en el dispositivo Wear:

[![Captura de pantalla de aplicación](creating-a-watchface-images/08-app-screen.png "pantalla de la aplicación en el dispositivo Wear")](creating-a-watchface-images/08-app-screen.png#lightbox)

En este momento, la aplicación básica de desgaste no tiene funcionalidad de cara de inspección porque aún no ofrece una implementación del servicio inspección cara. Este servicio se agregarán a continuación.


## <a name="canvaswatchfaceservice"></a>CanvasWatchFaceService

Android implementa desgaste ver caras a través de la `CanvasWatchFaceService` clase. `CanvasWatchFaceService` se deriva de `WatchFaceService`, que a su vez se deriva `WallpaperService` tal como se muestra en el diagrama siguiente:

[![Diagrama de herencia](creating-a-watchface-images/09-inheritance-diagram-sml.png "CanvasWatchFaceService diagrama de herencia")](creating-a-watchface-images/09-inheritance-diagram.png#lightbox)

`CanvasWatchFaceService` incluye un anidada `CanvasWatchFaceService.Engine`; crea una instancia de un `CanvasWatchFaceService.Engine` objeto que realiza el trabajo real de dibujo de la pantalla del reloj. `CanvasWatchFaceService.Engine` se deriva de `WallpaperService.Engine` tal como se muestra en el diagrama anterior.

No se muestra en este diagrama es una `Canvas` que `CanvasWatchFaceService` usa para dibujar la pantalla del reloj &ndash; esto `Canvas` se pasa a través de la `OnDraw` método tal y como se describe a continuación.

En las secciones siguientes, se creará un servicio de cara de supervisión personalizada siguiendo estos pasos:

1.  Defina una clase denominada `MyWatchFaceService` que se deriva `CanvasWatchFaceService`.

2.  Dentro de `MyWatchFaceService`, cree una clase anidada denominada `MyWatchFaceEngine` que se deriva `CanvasWatchFaceService.Engine`.

3.  En `MyWatchFaceService`, implemente un `CreateEngine` método que crea una instancia `MyWatchFaceEngine` y lo devuelve.

4.  En `MyWatchFaceEngine`, implemente el `OnCreate` método para crear el estilo de la cara de inspección y realizar otras tareas de inicialización.

5.  Implemente el `OnDraw` método `MyWatchFaceEngine`. Este método se llama cada vez que se debe volver a dibujar la pantalla del reloj (es decir, *invalidado*). `OnDraw` es el método que dibuja los elementos de la cara de inspección como hora, minuto y segundo manos (y vuelve a dibujar).

6.  Implemente el `OnTimeTick` método `MyWatchFaceEngine`.
    `OnTimeTick` se llama al menos una vez por minuto (en los modos de ambientales e interactivos) o cuando ha cambiado la fecha y hora.

Para obtener más información acerca de `CanvasWatchFaceService`, vea el Android [CanvasWatchFaceService](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.html) documentación de API.
De forma similar, [CanvasWatchFaceService.Engine](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine.html) explica la implementación real de la pantalla del reloj.


### <a name="add-the-canvaswatchfaceservice"></a>Agregar el CanvasWatchFaceService

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Agregue un nuevo archivo denominado **MyWatchFaceService.cs** (en Visual Studio, haga clic en **WatchFace** en el **el Explorador de soluciones**, haga clic en **Agregar > nuevo elemento...** y seleccione **clase**).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Agregue un nuevo archivo denominado **MyWatchFaceService.cs** (en Visual Studio para Mac, haga clic en el **WatchFace** del proyecto, haga clic en **Agregar > nuevo archivo...** y seleccione **clase vacía**).

-----

Reemplace el contenido de este archivo con el código siguiente:

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

`MyWatchFaceService` (deriva `CanvasWatchFaceService`) es el "programa principal" de la pantalla del reloj. `MyWatchFaceService` implementa un solo método, `OnCreateEngine`, que crea y devuelve un `MyWatchFaceEngine` objeto (`MyWatchFaceEngine` se deriva de `CanvasWatchFaceService.Engine`). Las instancias `MyWatchFaceEngine` objeto debe devolverse como un `WallpaperService.Engine`. La encapsulación `MyWatchFaceService` objeto se pasa al constructor.

`MyWatchFaceEngine` es la implementación de cara real inspección &ndash; contiene el código que se dibuja la pantalla del reloj. También controla los eventos del sistema, como cambios de la pantalla (modos de ambiente interactivo, pantalla si se desactiva, etcetera.).


### <a name="implement-the-engine-oncreate-method"></a>Implemente el método OnCreate de motor

El `OnCreate` método inicializa la pantalla del reloj. Agregue el siguiente campo a `MyWatchFaceEngine`:

```csharp
Paint hoursPaint;
```

Esto `Paint` objeto que se usará para dibujar la hora actual en la pantalla del reloj. A continuación, agregue el método siguiente a `MyWatchFaceEngine`:

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

`OnCreate` se llama a breve tras `MyWatchFaceEngine` se inicia. Configura el `WatchFaceStyle` (que controla cómo el dispositivo Wear interactúa con el usuario) y crea una instancia de la `Paint` objeto que se usará para mostrar la hora.

La llamada a `SetWatchFaceStyle` hace lo siguiente:

1.  Conjuntos de *modo peek* a `PeekModeShort`, lo que hace que las notificaciones que aparezca como tarjetas "peek" pequeño en la pantalla.

2.  Establece la visibilidad en segundo plano `Interruptive`, lo que hace que el fondo de una tarjeta peek se muestren solo brevemente si representa una notificación interrumpen el trabajo.

3.  Deshabilita la hora de la interfaz de usuario predeterminada del sistema desde que se va a dibujar en la pantalla del reloj para que la pantalla del reloj personalizado puede mostrar la hora en su lugar.

Para obtener más información sobre estas y otras opciones de estilo de cara de inspección, vea el Android [WatchFaceStyle.Builder](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceStyle.Builder.html) documentación de API.

Después de `SetWatchFaceStyle` se complete, `OnCreate` crea una instancia de la `Paint` objeto (`hoursPaint`) y establece su color en blanco y su tamaño del texto a 48 píxeles ([TextSize](https://developer.android.com/reference/android/graphics/Paint.html#setTextSize%28float%29) debe especificarse en píxeles).


### <a name="implement-the-engine-ondraw-method"></a>Implementar el motor OnDraw (método)

El `OnDraw` método es más importante quizás `CanvasWatchFaceService.Engine` método &ndash; es el método que realmente dibuja ver elementos de la cara como dígitos y cara manecillas del reloj.
En el ejemplo siguiente, dibuja una cadena de hora en la pantalla del reloj.
Agregue el siguiente método a `MyWatchFaceEngine`:

```csharp
public override void OnDraw (Canvas canvas, Rect frame)
{
    var str = DateTime.Now.ToString ("h:mm tt");
    canvas.DrawText (str,
        (float)(frame.Left + 70),
        (float)(frame.Top  + 80), hoursPaint);
}
```

Cuando llama Android `OnDraw`, pasa un `Canvas` instancia y los límites en el que se puede dibujar la cara. En el ejemplo de código anterior, `DateTime` se usa para calcular la hora actual en horas y minutos (en formato de 12 horas). Se dibuja la cadena de hora resultante en el lienzo mediante el uso de la `Canvas.DrawText` método. La cadena aparecerá 70 píxeles a través de desde el borde izquierdo y 80 píxeles hacia abajo desde el borde superior.

Para obtener más información sobre la `OnDraw` método, consulte el Android [onDraw](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine#ondraw) documentación de API.


### <a name="implement-the-engine-ontimetick-method"></a>Implemente el método motor OnTimeTick

Android llama periódicamente a la `OnTimeTick` método para actualizar el tiempo que se muestra en la pantalla del reloj. Se llama al menos una vez por minuto (en los modos ambientales e interactivos), o cuando ha cambiado la fecha y hora o zona horaria. Agregue el siguiente método a `MyWatchFaceEngine`:

```csharp
public override void OnTimeTick()
{
    Invalidate();
}
```

Esta implementación de `OnTimeTick` simplemente llama a `Invalidate`. El `Invalidate` programaciones método `OnDraw` para volver a dibujar la pantalla del reloj.

Para obtener más información sobre la `OnTimeTick` método, consulte el Android [onTimeTick](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onTimeTick()) documentación de API.


## <a name="register-the-canvaswatchfaceservice"></a>Registrar el CanvasWatchFaceService

`MyWatchFaceService` debe estar registrado en el **AndroidManifest.xml** de la aplicación Wear asociada. Para ello, agregue el siguiente código XML para el `<application>` sección:

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

Este XML hace lo siguiente:

1.  Establece el `android.permission.BIND_WALLPAPER` permiso. Este permiso otorga el permiso de servicio de inspección cara para cambiar el papel tapiz del sistema en el dispositivo. Tenga en cuenta que este permiso se debe establecer en el `<service>` sección en lugar de en el exterior `<application>` sección.

2.  Define un `watch_face` recursos. Este recurso es un archivo XML corto que declara un `wallpaper` recursos (este archivo se creará en la sección siguiente).

3.  Declara una imagen drawable llamada `preview` que se mostrará la pantalla de selección de selector de inspección.

4.  Incluye un `intent-filter` que Android sepan que `MyWatchFaceService` va a mostrar una esfera del reloj.

Que se haya completado el código de las opciones básicas `WatchFace` ejemplo. El siguiente paso es agregar los recursos necesarios.


## <a name="add-resource-files"></a>Agregar archivos de recursos

Antes de poder ejecutar el servicio de inspección, debe agregar el **watch_face** recursos y la imagen de vista previa. En primer lugar, cree un nuevo archivo XML en **Resources/xml/watch_face.xml** y reemplace su contenido con el siguiente código XML:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<wallpaper xmlns:android="http://schemas.android.com/apk/res/android" />
```

Establece la acción de compilación de este archivo en **AndroidResource**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Acción de compilación](creating-a-watchface-images/10-android-resource-vs.png "establecer AndroidResource acción de compilación")](creating-a-watchface-images/10-android-resource-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Acción de compilación](creating-a-watchface-images/10-android-resource-xs.png "establecer AndroidResource acción de compilación")](creating-a-watchface-images/10-android-resource-xs.png#lightbox)

-----

Este archivo de recursos define una sencilla `wallpaper` elemento que se usará para la pantalla del reloj.

Si aún no lo ha hecho, descargue [preview.png](creating-a-watchface-images/preview.png).
Instálelo en **Resources/drawable/preview.png**. No olvide agregar este archivo para el `WatchFace` proyecto. Esta imagen de vista previa se muestra al usuario en el selector de cara de inspección en el dispositivo de desgaste. Para crear una imagen de vista previa para su propio esfera del reloj, puede tomar una captura de pantalla de la pantalla del reloj mientras se está ejecutando. (Para obtener más información sobre cómo obtener las capturas de pantalla de desgaste de los dispositivos, consulte [realizar capturas de pantalla](~/android/wear/deploy-test/debug-on-device.md#screenshots)).


## <a name="try-it"></a>¡Pruébelo!

Compilar e implementar la aplicación en el dispositivo de desgaste. Debería ver la pantalla de la aplicación Wear aparecen como antes. Siga este procedimiento para habilitar la nueva cara de inspección:

1.  Deslice el dedo hacia la derecha hasta que vea el fondo de la pantalla de inspección.

2.  Tocar y mantener en cualquier lugar del fondo de la pantalla durante dos segundos.

3.  Deslice el dedo de izquierda a derecha para ir a través de las caras de inspección distintos.

4.  Seleccione el **Xamarin ejemplo** (se muestra en la parte derecha) de esfera del reloj:

    [![Selector de Watchface](creating-a-watchface-images/11-watchface-picker.png "pasar el dedo para localizar la esfera del reloj de ejemplo de Xamarin")](creating-a-watchface-images/11-watchface-picker.png#lightbox)

5.  Pulse el **Xamarin ejemplo** ver cara para seleccionarlo.

Esto cambia la pantalla del reloj del dispositivo Wear para usar el servicio de cara de supervisión personalizada implementado hasta el momento:

[![Esfera del reloj digital](creating-a-watchface-images/12-digital-watchface.png "inspección digital personalizado que se ejecuta en el dispositivo Wear")](creating-a-watchface-images/12-digital-watchface.png#lightbox)

Se trata de una esfera del reloj relativamente rudimentaria porque la implementación de aplicación es tan mínima (por ejemplo, no incluye un fondo de cara de inspección y no llama a `Paint` suavizados métodos para mejorar el aspecto).
Sin embargo, implementa la funcionalidad básica que se necesita para crear una esfera del reloj personalizado.

En la sección siguiente, se actualizará esta esfera del reloj a una implementación más sofisticada.


## <a name="upgrading-the-watch-face"></a>Actualizar la pantalla del reloj

En el resto de este tutorial, `MyWatchFaceService` se actualiza para mostrar un reloj analógico de estilo y se amplía para admitir más características. Para crear la pantalla del reloj actualizada se agregarán las siguientes funcionalidades:

1.  Indica el tiempo con analógica hora, minuto y segundo manos.

2.  Responde a los cambios en la visibilidad.

3.  Responde a los cambios entre el modo de ambiente y el modo interactivo.

4.  Lee las propiedades del dispositivo Wear subyacente.

5.  Actualiza automáticamente el tiempo cuando realiza un cambio de zona horaria.

Antes de implementar los cambios de código a continuación, descargue [drawable.zip](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/Resources/drawable.zip?raw=true), descomprímalo y mover los archivos .png descomprimido **recursos/drawable** (sobrescribe la anterior **preview.png**). Agregar los nuevos archivos .png en el `WatchFace` proyecto.


### <a name="update-engine-features"></a>Características del motor de actualización

El siguiente paso es actualización **MyWatchFaceService.cs** a una implementación que se dibuja una esfera del reloj analógico y es compatible con las nuevas características. Reemplace el contenido de **MyWatchFaceService.cs** con la versión del código de la cara inspección analógica [MyWatchFaceService.cs](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/WatchFace/MyWatchFaceService.cs) (puede cortar y pegar este código fuente existentes  **MyWatchFaceService.cs**).

Esta versión de **MyWatchFaceService.cs** agrega más código para los métodos existentes e incluye métodos invalidados adicionales para agregar más funcionalidad. Las secciones siguientes proporcionan una visita guiada del código fuente.

#### <a name="oncreate"></a>OnCreate

La actualización **OnCreate** método configura el estilo de cara inspección como antes, pero incluye algunos pasos adicionales:

1.  Establece la imagen de fondo el **xamarin_background** recursos que residen en **Resources/drawable-hdpi/xamarin_background.png**.

2.  Inicializa `Paint` objetos para dibujar la parte de hora, minuto mano y segundero.

3.  Inicializa un `Paint` objeto para dibujar las marcas de hora alrededor del borde de la pantalla del reloj.

4.  Crea un temporizador que llama a la `Invalidate` método (redraw) para que el segundero se volverá a dibujar cada segundo. Tenga en cuenta que este temporizador es necesario porque `OnTimeTick` llamadas `Invalidate` sólo una vez cada minuto.

En este ejemplo incluye sólo un **xamarin_background.png** imagen; sin embargo, es posible que desea crear una imagen de fondo diferente para cada densidad de pantalla que admitirá la esfera del reloj personalizado.

#### <a name="ondraw"></a>OnDraw

La actualización **OnDraw** método dibuja una esfera del reloj de estilo de la señal analógica mediante los siguientes pasos:

1.  Obtiene la hora actual, que ahora se mantiene en un `time` objeto.

2.  Determina los límites de la superficie de dibujo y su centro.

3.  Dibuja el fondo, la escala para ajustarse el dispositivo cuando se dibuja el fondo.

4.  Dibuja doce *tics* alrededor de la cara del reloj (correspondiente a las horas en la cara del reloj).

5.  Calcula el ángulo, rotación y longitud de cada manecilla del reloj.

6.  Dibuja cada mano en la superficie de inspección. Tenga en cuenta que no se dibuja el segundero si el reloj está en modo de ambiente.


#### <a name="onpropertieschanged"></a>OnPropertiesChanged

Este método se llama para informar al `MyWatchFaceEngine` acerca de las propiedades del dispositivo Wear (como modo ambiente baja bits y protección de grabación). En `MyWatchFaceEngine`, este método solo se comprueba para el modo de ambiente de bits baja (en el modo de ambiente de bits baja, la pantalla es compatible con menos bits para cada color).

Para obtener más información acerca de este método, vea el Android [onPropertiesChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onPropertiesChanged%28android.os.Bundle%29) documentación de API.


#### <a name="onambientmodechanged"></a>OnAmbientModeChanged

Este método se llama cuando el dispositivo Wear entra o sale del modo de ambiente. En el `MyWatchFaceEngine` implementación, la pantalla del reloj deshabilita el suavizado de contorno cuando se encuentra en modo de ambiente.

Para obtener más información acerca de este método, vea el Android [onAmbientModeChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onAmbientModeChanged%28boolean%29) documentación de API.


#### <a name="onvisibilitychanged"></a>OnVisibilityChanged

Este método se llama cada vez que el reloj se convierte en visible u oculto. En `MyWatchFaceEngine`, este método registra y anula el registro del receptor de la zona horaria (descrito a continuación), según el estado de visibilidad.

Para obtener más información acerca de este método, vea el Android [onVisibilityChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onVisibilityChanged%28boolean%29) documentación de API.


### <a name="time-zone-feature"></a>Característica de zona horaria

El nuevo **MyWatchFaceService.cs** también incluye la funcionalidad para actualizar la hora actual cada vez que la hora de la zona cambios (por ejemplo mientras viaja en distintas zonas horarias). Cerca del final de **MyWatchFaceService.cs**, un tiempo de cambiar la zona `BroadcastReceiver` se define que controla el cambio de zona horaria objetos Intent:

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

El `RegisterTimezoneReceiver` y `UnregisterTimezoneReceiver` métodos son invocados por el `OnVisibilityChanged` método.
`UnregisterTimezoneReceiver` se llama cuando se cambia el estado de visibilidad de la pantalla del reloj a la oculta. Cuando la pantalla del reloj es visible de nuevo, `RegisterTimezoneReceiver` se denomina (consulte la `OnVisibilityChanged` método).

El motor `RegisterTimezoneReceiver` método declara un controlador para este receptor de la zona horaria `Receive` eventos; este controlador actualiza la `time` objeto con el nuevo tiempo cada vez que se supere una zona horaria:

```csharp
timeZoneReceiver = new TimeZoneReceiver ();
timeZoneReceiver.Receive = (intent) => {
    time.Clear (intent.GetStringExtra ("time-zone"));
    time.SetToNow ();
};
```

Un filtro de intento es creado y registrado para el receptor de la zona horaria:

```csharp
IntentFilter filter = new IntentFilter(Intent.ActionTimezoneChanged);
Application.Context.RegisterReceiver (timeZoneReceiver, filter);
```

El `UnregisterTimezoneReceiver` método anula el registro del receptor de la zona horaria:

```csharp
Application.Context.UnregisterReceiver (timeZoneReceiver);
```

### <a name="run-the-improved-watch-face"></a>Ejecute la pantalla del reloj mejorada

Compilar e implementar la aplicación en el dispositivo de desgaste de nuevo. Seleccione la pantalla del reloj en el selector de cara de inspección como antes. Se muestra la vista previa en el selector de inspección de la izquierda, y se muestra la nueva cara de inspección de la derecha:

[![Esfera del reloj analógico](creating-a-watchface-images/13-analog-watchface.png "mejorado cara analógico en el selector y en dispositivo")](creating-a-watchface-images/13-analog-watchface.png#lightbox)

En esta captura de pantalla, se mueve el segundero una vez por segundo. Al ejecutar este código en un dispositivo Wear, el segundero desaparece cuando el reloj entra en modo de ambiente.


## <a name="summary"></a>Resumen

En este tutorial, un watchface personalizada de Android Wear 1.0 se ha implementado y probado. El `CanvasWatchFaceService` y `CanvasWatchFaceService.Engine` se introdujeron las clases y los métodos esenciales de la clase del motor se implementaron para crear una esfera del reloj digital simple. Esta implementación se actualizó con mayor funcionalidad para crear un reloj analógico, y se implementaron métodos adicionales para controlar los cambios en la visibilidad, el modo de ambiente y las diferencias en las propiedades del dispositivo. Por último, se ha implementado un receptor de difusión de zona horaria para que el reloj actualiza automáticamente el tiempo de cuándo se traspasa una zona horaria.


## <a name="related-links"></a>Vínculos relacionados

- [Creación de esferas de reloj](https://developer.android.com/training/wearables/watch-faces/index.html)
- [Ejemplo de WatchFace](https://developer.xamarin.com/samples/monodroid/wear/WatchFace)
- [WatchFaceService.Engine](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html)
