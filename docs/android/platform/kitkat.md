---
title: Características KitKat
description: Android 4.4 (KitKat) viene equipada con gran cantidad de características para los usuarios y desarrolladores. Esta guía destaca algunas de estas características y proporciona ejemplos de código y los detalles de implementación que le ayudarán a sacar el máximo provecho KitKat.
ms.prod: xamarin
ms.assetid: D3FDEA1C-F076-406F-BCC3-2A55D2C6ADEE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: f957bd5b361d7287353542186916c7f934ee0490
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827784"
---
# <a name="kitkat-features"></a>Características KitKat

_Android 4.4 (KitKat) viene equipada con gran cantidad de características para los usuarios y desarrolladores. Esta guía destaca algunas de estas características y proporciona ejemplos de código y los detalles de implementación que le ayudarán a sacar el máximo provecho KitKat._

## <a name="overview"></a>Información general

Android 4.4 (API nivel 19), también conocido como "KitKat", se publicó en 2013 en tiempo de ejecución. KitKat ofrece una variedad de nuevas características y mejoras, incluidas:

-  [Experiencia del usuario](#user_experience) &ndash; fácil animaciones con marco de transición, translúcidas barras de estado y la navegación y modo de pantalla completa envolvente ayudan a crear una mejor experiencia del usuario.

-  [Contenido de usuario](#user_content) &ndash; simplificado de administración de archivos de usuario con el marco de trabajo de acceso de almacenamiento; imprimir fotos, sitios web y otro contenido es más fácil con las API de impresión mejorada.

-  [Hardware](#hardware) &ndash; convierta cualquier aplicación en una tarjeta de NFC con NFC basadas en Host emulación de la tarjeta; ejecutar sensores de bajo consumo de energía con el `SensorManager` .

-  [Herramientas de desarrollo](#developer_tools) &ndash; presentación en pantalla aplicaciones en acción con el cliente de Android Debug Bridge, disponible como parte del SDK de Android.


Esta guía proporciona instrucciones para migrar una aplicación existente de Xamarin.Android KitKat, así como una descripción general de KitKat para desarrolladores de Xamarin.Android.

## <a name="requirements"></a>Requisitos

Para desarrollar aplicaciones de Xamarin.Android con KitKat, necesita *Xamarin.Android 4.11.0* o superior y Android 4.4 (API nivel 19) instalado a través de Android SDK Manager, como se muestra en la captura de pantalla siguiente:

[![Seleccionar Android 4.4 en Android SDK Manager](kitkat-images/api19.png)](kitkat-images/api19.png#lightbox)

<a name="Migrating_Your_App_to_KitKat" />

## <a name="migrating-your-app-to-kitkat"></a>Migrar la aplicación a KitKat

Esta sección proporciona algunos elementos de la primera respuesta para ayudar a las aplicaciones existentes de transición para Android 4.4.

### <a name="check-system-version"></a>Compruebe la versión del sistema

Si una aplicación necesita ser compatible con versiones anteriores de Android, asegúrese de ajustar cualquier código específico de KitKat en una comprobación de versión del sistema, como se muestra en el ejemplo de código siguiente:

```csharp
if (Build.VERSION.SdkInt >= BuildVersionCodes.Kitkat) {
    //KitKat only code here
}
```

### <a name="alarm-batching"></a>Procesamiento por lotes de alarma

Android usa servicios de alarma para reactivar una aplicación en segundo plano en un momento determinado. KitKat lleva esto un paso más allá por lotes las alarmas para conservar energía. Esto significa que, en lugar de reactivación cada aplicación en un momento exacto, KitKat prefiere agrupar varias aplicaciones que se registran para reactivar durante el mismo intervalo de tiempo y reactivación de ellos al mismo tiempo.
Para indicar a Android para reactivar una aplicación durante un intervalo de tiempo especificado, llame a `SetWindow` en el [ `AlarmManager` ](https://developer.xamarin.com/api/type/Android.App.AlarmManager/), pasando el mínimo y el tiempo máximo, en milisegundos, que puede transcurrir antes de que se ha activado la aplicación y la operación para realizar en la activación.
El código siguiente proporciona un ejemplo de una aplicación que se deba ser despertado entre una media hora y una hora desde el momento en que se establece la ventana:

```csharp
AlarmManager alarmManager = (AlarmManager)GetSystemService(AlarmService);
alarmManager.SetWindow (AlarmType.Rtc, AlarmManager.IntervalHalfHour, AlarmManager.IntervalHour, pendingIntent);
```

Para continuar la reactivación de una aplicación en un momento exacto, use `SetExact`, pasando el tiempo exacto que debe ser despertada la aplicación y la operación que se va a realizar:

```csharp
alarmManager.SetExact (AlarmType.Rtc, AlarmManager.IntervalDay, pendingIntent);
```

KitKat ya no le permite definir una alarma de repetición exacta. Aplicaciones que usan [`SetRepeating`](https://developer.xamarin.com/api/member/Android.App.AlarmManager.SetRepeating/p/Android.App.AlarmType/System.Int64/System.Int64/Android.App.PendingIntent/)
y necesitan alarmas exactas para trabajar ahora necesarias para desencadenar manualmente cada alarma.

### <a name="external-storage"></a>Almacenamiento externo

Almacenamiento externo ahora se divide en dos tipos: único para la aplicación y los datos compartidos por varias aplicaciones de almacenamiento. Leer y escribir en la ubicación específica de la aplicación en un almacenamiento externo no requieren permisos especiales. Interactuar con los datos en el almacenamiento compartido ahora requiere la `READ_EXTERNAL_STORAGE` o `WRITE_EXTERNAL_STORAGE` permiso. Los dos tipos se pueden clasificar como tal:

-  Si recibe una ruta de acceso de archivo o directorio mediante una llamada a un método en `Context` , por ejemplo, [`GetExternalFilesDir`](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalFilesDir/p/System.String/)
   O [`GetExternalCacheDirs`](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalCacheDirs%28%29/)
   - la aplicación no requiere ningún permiso adicional.

-  Si una ruta de acceso de archivo o directorio está obteniendo acceso a una propiedad o llamar a un método en `Environment` , como [`GetExternalStorageDirectory`](https://developer.xamarin.com/api/property/Android.OS.Environment.ExternalStorageDirectory/)
   O [`GetExternalStoragePublicDirectory`](https://developer.xamarin.com/api/member/Android.OS.Environment.GetExternalStoragePublicDirectory/p/System.String/)
   , la aplicación requiere la `READ_EXTERNAL_STORAGE` o `WRITE_EXTERNAL_STORAGE` permiso.

> [!NOTE]
> `WRITE_EXTERNAL_STORAGE` implica la `READ_EXTERNAL_STORAGE` permiso, por lo que solo deberá establecer un permiso.

### <a name="sms-consolidation"></a>Consolidación de SMS

KitKat simplifica la mensajería para el usuario al agregar todo el contenido SMS en la aplicación de un valor predeterminado seleccionado por el usuario. El desarrollador es responsable de hace que la aplicación puede seleccionar como la aplicación de mensajería de forma predeterminada y su comportamiento adecuadamente en el código y en la vida si la aplicación no está seleccionada. Para obtener más información sobre la transición de la aplicación SMS para KitKat, consulte el [Your SMS aplicaciones preparar para KitKat](http://android-developers.blogspot.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html) Guía de Google.

### <a name="webview-apps"></a>Aplicaciones de WebView

[WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) obtuvo un cambio en KitKat. El cambio más importante es mayor seguridad para cargar contenido en un `WebView`. Aunque la mayoría de las aplicaciones destinadas a versiones anteriores de API debería funcionar según lo previsto, probar aplicaciones que usan el `WebView` clase es muy recomendable. Para obtener más información acerca de afectado APIs WebView hacen referencia a la de Android [migrar a WebView en Android 4.4](https://developer.android.com/guide/webapps/migrating.html) documentación.

<a name="user_experience" />

## <a name="user-experience"></a>Experiencia del usuario

KitKat incluye varias API nuevas para mejorar la experiencia del usuario, incluido el nuevo marco de transición para controlar las animaciones de propiedad y una opción de interfaz de usuario traslúcida de temas. Más adelante se explican estos cambios.

### <a name="transition-framework"></a>Marco de transición

El marco de transición hace que las animaciones más fácil de implementar. KitKat le permite realizar una animación de una propiedad simple con una sola línea de código, o personalizar las transiciones con *escenas*.

#### <a name="simple-property-animation"></a>Animación de propiedades simples

La nueva biblioteca de Android transiciones simplifica el código detrás de las animaciones de propiedad. El marco de trabajo permite realizar animaciones sencillas con un código mínimo. Por ejemplo, se usa el siguiente ejemplo de código [`TransitionManager.BeginDelayedTransition`](https://developer.xamarin.com/api/member/Android.Transitions.TransitionManager.BeginDelayedTransition/p/Android.Views.ViewGroup/Android.Transitions.Transition/)
Para animar mostrando y ocultando un `TextView`:

```csharp
using Android.Transitions;

public class MainActivity : Activity
{
    LinearLayout linear;
    Button button;
    TextView text;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        SetContentView (Resource.Layout.Main);

        linear = FindViewById<LinearLayout> (Resource.Id.linearLayout);
        button = FindViewById<Button> (Resource.Id.button);
        text = FindViewById<TextView> (Resource.Id.textView);

        button.Click += (o, e) => {

            TransitionManager.BeginDelayedTransition (linear);

            if(text.Visibility != ViewStates.Visible)
            {
                text.Visibility = ViewStates.Visible;
            }
            else
            {
                text.Visibility = ViewStates.Invisible;
            }
        };
    }
}
```

El ejemplo anterior usa el marco de transición para crear un automático, la transición predeterminada entre los valores de propiedad cambiantes. Dado que la animación se controla mediante una sola línea de código, puede realizar fácilmente esto compatible con versiones anteriores de Android ajustando el `BeginDelayedTransition` llamar en una comprobación de versión del sistema. Consulte la [migrar su aplicación a KitKat](#Migrating_Your_App_to_KitKat) sección para obtener más información.

La captura de pantalla siguiente muestra la aplicación antes de la animación:

[![Captura de pantalla de aplicación antes de animación](kitkat-images/trans-before.png)](kitkat-images/trans-before.png#lightbox)

La captura de pantalla siguiente muestra la aplicación después de la animación:

[![Captura de pantalla de aplicación una vez completada la animación](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

Puede obtener más control sobre la transición de segundo plano, que se tratan en la sección siguiente.

#### <a name="android-scenes"></a>Segundo plano de Android

[Segundo plano](https://developer.xamarin.com/api/type/Android.Transitions.Scene/) se incluyeron como parte del marco de transición para dar al desarrollador más control sobre las animaciones. Segundo plano crea un área dinámico en la interfaz de usuario: especificar un contenedor y varias versiones o "plano", para el contenido XML dentro del contenedor, y Android encarga del resto del trabajo para animar las transiciones entre bastidores. Segundo plano Android le permite crear animaciones complejas con el mínimo esfuerzo en el lado de desarrollo.

El elemento de interfaz de usuario estático donde se encuentra el contenido dinámico es un denominado un *contenedor* o *escena base*. El ejemplo siguiente usa el Diseñador de Android para crear un `RelativeLayout` llamado `container`:

[![Uso de Android Designer para crear un contenedor RelativeLayout](kitkat-images/container.png)](kitkat-images/container.png#lightbox)

El diseño de ejemplo también define un botón denominado `sceneButton` a continuación el `container`. Este botón desencadenará la transición.

El contenido dinámico dentro del contenedor requiere dos nuevos diseños de Android. Estos diseños especifican solo el código *dentro de* el contenedor.
El ejemplo de código siguiente define un diseño denominado *Scene1* que contiene dos campos de texto leer "Kit" y "Kat", respectivamente, y llama un segundo diseño *Scene2* que contiene los mismos campos de texto que se puede revertidos. El XML es como sigue:

 **Scene1.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView
        android:id="@+id/textA"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Kit"
        android:textSize="35sp" />
    <TextView
        android:id="@+id/textB"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/textA"
        android:text="Kat"
        android:textSize="35sp" />
</merge>
```

 **Scene2.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView
        android:id="@+id/textB"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Kat"
        android:textSize="35sp" />
    <TextView
        android:id="@+id/textA"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/textB"
        android:text="Kit"
        android:textSize="35sp" />
</merge>
```

El ejemplo anterior usa `merge` para hacer que el código de vista más corto y simplificar la jerarquía de vistas. Puede leer más sobre `merge` diseños [aquí](http://android-developers.blogspot.com/2009/03/android-layout-tricks-3-optimize-by.html).

Una escena se crea mediante una llamada a [ `Scene.GetSceneForLayout` ](https://developer.xamarin.com/api/member/Android.Transitions.Scene.GetSceneForLayout/p/Android.Views.ViewGroup/System.Int32/Android.Content.Context/), pasando el objeto contenedor, el identificador de recurso del archivo de diseño de la escena y la actual `Context`, tal y como se muestra en el ejemplo de código siguiente:

```csharp
RelativeLayout container = FindViewById<RelativeLayout> (Resource.Id.container);

Scene scene1 = Scene.GetSceneForLayout(container, Resource.Layout.Scene1, this);
Scene scene2 = Scene.GetSceneForLayout(container, Resource.Layout.Scene2, this);

scene1.Enter();
```

Al hacer clic en el botón voltea entre las dos escenas, que anima Android con los valores de transición predeterminada:

```csharp
sceneButton.Click += (o, e) => {
    Scene temp = scene2;
    scene2 = scene1;
    scene1 = temp;

    TransitionManager.Go (scene1);
};
```

La captura de pantalla siguiente muestra la escena antes de la animación:

[![Captura de pantalla de la aplicación antes de que comience la animación](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

La captura de pantalla siguiente muestra la escena después de la animación:

[![Captura de pantalla de la aplicación una vez completada la animación](kitkat-images/scene.png)](kitkat-images/scene.png#lightbox)


> [!NOTE]
> Hay un [problema conocido](https://code.google.com/p/android/issues/detail?id=62450) en las transiciones Android biblioteca que hace que el segundo plano creado mediante `GetSceneForLayout` para interrumpir cuando un usuario navega a través de una actividad de la segunda vez. Se describe una solución de java [aquí](http://www.doubleencore.com/2013/11/new-transitions-framework/).


##### <a name="custom-transitions-in-scenes"></a>Transiciones personalizadas en segundo plano

Se puede definir una transición personalizada en un archivo de recursos xml en el `transition` directorio bajo `Resources`, tal y como se muestra en la captura de pantalla siguiente:

[![Ubicación del archivo transition.xml bajo el directorio de recursos/transición](kitkat-images/resources.png)](kitkat-images/resources.png#lightbox)

Ejemplo de código siguiente define una transición que anima durante 5 segundos y usa el [pasar por alto los interpolador](https://developer.android.com/reference/android/views/animation/OvershootInterpolator.html):

```xml
<changeBounds
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:duration="5000"
  android:interpolator="@android:anim/overshoot_interpolator" />
```

La transición se crea en la actividad mediante el [TransitionInflater](https://developer.xamarin.com/api/type/Android.Transitions.TransitionInflater/), tal y como se muestra en el código siguiente:

```csharp
Transition transition = TransitionInflater.From(this).InflateTransition(Resource.Transition.transition);
```

A continuación, se agrega la nueva transición a la `Go` llamada que comienza la animación:

```csharp
TransitionManager.Go (scene1, transition);
```

### <a name="translucent-ui"></a>Translucent UI

KitKat proporciona más control sobre los temas de la aplicación con las barras de estado y navegación translúcidas opcionales. Puede cambiar la transparencia de los elementos de interfaz de usuario del sistema en el mismo archivo XML que se usa para definir el tema de Android. KitKat presenta las siguientes propiedades:

-  `windowTranslucentStatus` -Cuando se establece como true, para la barra de estado superior translúcido.

-  `windowTranslucentNavigation` -Cuando se establece como true, para la barra de navegación inferior translúcido.

-  `fitsSystemWindows` -Establecer la barra superior o inferior a transcluent desplaza el contenido en los elementos transparentes de la interfaz de usuario de forma predeterminada. Establecer esta propiedad en `true` es una manera sencilla de evitar que el contenido que se superponen con los elementos de interfaz de usuario del sistema translúcido.


El código siguiente define un tema con las barras de estado y navegación translúcidos:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <style name="KitKatTheme" parent="android:Theme.Holo.Light">
        <item name="android:windowBackground">@color/xamgray</item>
        <item name="android:windowTranslucentStatus">true</item>
        <item name="android:windowTranslucentNavigation">true</item>
        <item name="android:fitsSystemWindows">true</item>
        <item name="android:actionBarStyle">@style/ActionBar.Solid.KitKat</item>
    </style>

    <style name="ActionBar.Solid.KitKat" parent="@android:style/Widget.Holo.Light.ActionBar.Solid">
        <item name="android:background">@color/xampurple</item>
    </style>
</resources>
```

La captura de pantalla siguiente muestra el tema anterior con estado translúcido y barras de navegación:

[![Captura de pantalla de ejemplo de aplicación con las barras de estado y navegación translúcidas](kitkat-images/theme.png)](kitkat-images/theme.png#lightbox)

<a name="user_content" />

## <a name="user-content"></a>Contenido de usuario

### <a name="storage-access-framework"></a>Marco de trabajo de acceso de almacenamiento

El marco de trabajo de acceso de almacenamiento (SAF) es una nueva forma para que los usuarios interactuar con el contenido almacenado como imágenes, vídeos y documentos. En lugar de presentar a los usuarios un cuadro de diálogo para elegir una aplicación para administrar contenido, KitKat abre una nueva interfaz de usuario que permite a los usuarios accedan a sus datos en una ubicación agregada. Una vez que se ha elegido el contenido, se devolverá al usuario a la aplicación que solicitó el contenido y la experiencia de aplicación continuarán con normalidad.

Este cambio requiere dos acciones en el lado del desarrollador: en primer lugar, las aplicaciones que requieren el contenido de proveedores deben actualizarse a una nueva forma de solicitud de contenido. Segundo, las aplicaciones que escriben datos en un `ContentProvider` deben modificarse para usar el nuevo marco. Ambos escenarios dependen de la nueva [`DocumentsProvider`](https://developer.xamarin.com/api/type/Android.Provider.DocumentsProvider/)
API.

#### <a name="documentsprovider"></a>DocumentsProvider

En KitKat, las interacciones con `ContentProviders` se abstraen con el `DocumentsProvider` clase. Esto significa que SAF no le importa dónde están los datos físicamente, siempre que sea accesible a través de la `DocumentsProvider` API. Proveedores locales, cloud services y dispositivos de almacenamiento externo todos utilizan la misma interfaz y se tratan del mismo modo, que proporciona el usuario y el desarrollador con un solo lugar para interactuar con el contenido del usuario.

En esta sección se explica cómo cargar y guardar el contenido con el marco de trabajo de acceso de almacenamiento.

#### <a name="request-content-from-a-provider"></a>Contenido de la solicitud de un proveedor

Vamos a decir KitKat que deseamos seleccionar contenido mediante la UI SAF con el `ActionOpenDocument` intención, lo que significa que queremos conectarnos a todos los proveedores de contenido disponibles para el dispositivo. Puede agregar algún filtrado a esta intención especificando `CategoryOpenable`, lo que significa que sólo el contenido que se puede abrir (es decir, accesible, puede usar contenido) se devolverá. KitKat también permite el filtrado de contenido con el `MimeType`. Por ejemplo, el código siguiente filtros para los resultados de la imagen mediante la especificación de la imagen `MimeType`:

```csharp
Intent intent = new Intent (Intent.ActionOpenDocument);
intent.AddCategory (Intent.CategoryOpenable);
intent.SetType ("image/*");
StartActivityForResult (intent, save_request_code);
```

Una llamada a `StartActivityForResult` inicia la UI SAF, que el usuario, a continuación, puede examinar para elegir una imagen:

[![Captura de pantalla de ejemplo de una aplicación mediante el marco de trabajo de acceso de almacenamiento para la exploración de una imagen](kitkat-images/saf-ui.png)](kitkat-images/saf-ui.png#lightbox)

Después de que el usuario ha elegido una imagen, `OnActivityResult` devuelve el `Android.Net.Uri` del archivo elegido. El ejemplo de código siguiente muestra la selección de imagen del usuario:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    if (resultCode == Result.Ok && data != null && requestCode == save_request_code) {
        imageView = FindViewById<ImageView> (Resource.Id.imageView);
        imageView.SetImageURI (data.Data);
    }
}
```

#### <a name="write-content-to-a-provider"></a>Escribir contenido en un proveedor

Además de cargar el contenido de la UI SAF, KitKat también le permite guardar el contenido a cualquier `ContentProvider` que implementa el `DocumentProvider` API. Guardando contenido usa un `Intent` con `ActionCreateDocument`:

```csharp
Intent intentCreate = new Intent (Intent.ActionCreateDocument);
intentCreate.AddCategory (Intent.CategoryOpenable);
intentCreate.SetType ("text/plain");
intentCreate.PutExtra (Intent.ExtraTitle, "NewDoc");
StartActivityForResult (intentCreate, write_request_code);
```

El ejemplo de código anterior carga la UI SAF, lo que permite al usuario cambiar el nombre de archivo y seleccione un directorio para alojar el nuevo archivo:

[![Captura de pantalla de usuario cambie el nombre de archivo a NewDoc en el directorio de descargas](kitkat-images/saf-save.png)](kitkat-images/saf-save.png#lightbox)

Cuando el usuario presiona **guardar**, `OnActivityResult` se pasa el `Android.Net.Uri` del archivo recién creado, que se puede acceder con `data.Data`. El uri puede usarse para transmitir datos en el nuevo archivo:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    if (resultCode == Result.Ok && data != null && requestCode == write_request_code) {
        using (Stream stream = ContentResolver.OpenOutputStream(data.Data)) {
            Encoding u8 = Encoding.UTF8;
            string content = "Hello, world!";
            stream.Write (u8.GetBytes(content), 0, content.Length);
        }
    }
}
```

Tenga en cuenta que [`ContentResolver.OpenOutputStream(Android.Net.Uri)`](https://developer.xamarin.com/api/member/Android.Content.ContentResolver.OpenOutputStream/(Android.Net.Uri))
Devuelve un `System.IO.Stream`, por lo que se puede escribir todo el proceso de transmisión por secuencias en. NET.

Para obtener más información sobre la carga, crear y editar contenido con el marco de trabajo de acceso de almacenamiento, consulte el [documentación de Android para el marco de trabajo de acceso de almacenamiento](https://developer.android.com/guide/topics/providers/document-provider.html).

### <a name="printing"></a>Impresión

Contenido de impresión se ha simplificado en KitKat con la introducción de la [servicios de impresión](https://developer.xamarin.com/api/namespace/Android.PrintServices/) y `PrintManager`. KitKat también es la primera versión de API para aprovechar completamente la [API del servicio de impresión en la nube de Google](https://developers.google.com/cloud-print/) utilizando el [aplicación de impresión en la nube de Google](https://play.google.com/store/apps/details?id=com.google.android.apps.cloudprint).
La mayoría de los dispositivos que se suministran con KitKat automáticamente descargar la aplicación de impresión en la nube de Google y [complemento de servicio de impresión de HP](https://play.google.com/store/apps/details?id=com.hp.android.printservice)al conectan a Wi-Fi. Un usuario puede comprobar la configuración de impresión de su dispositivo, vaya a **configuración > sistema > Imprimir**:

[![Captura de pantalla de ejemplo de la pantalla de configuración de impresión](kitkat-images/printing.png)](kitkat-images/printing.png#lightbox)


> [!NOTE]
> Aunque las API de impresión se configuran para trabajar con la impresión en la nube de Google de forma predeterminada, Android todavía permite a los desarrolladores preparar el contenido de impresión mediante las nuevas API y enviarlo a otras aplicaciones para controlar la impresión.



#### <a name="printing-html-content"></a>Contenido HTML de impresión

KitKat crea automáticamente un [ `PrintDocumentAdapter` ](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/) para obtener una vista web con `WebView.CreatePrintDocumentAdapter`. Contenido web de impresión es un esfuerzo coordinado entre un [ `WebViewClient` ](https://developer.xamarin.com/api/type/Android.Webkit.WebViewClient/) que espera a que el contenido HTML cargar y permite que la actividad que se sabe que la opción de impresión estén disponibles en el menú de opciones y la actividad, que se espera que el usuario Seleccione la opción de impresión y las llamadas `Print`en el `PrintManager`. Esta sección trata la configuración básica necesaria para imprimir en la pantalla contenido HTML.

Tenga en cuenta que al cargar e imprimir contenido web requieren el permiso de Internet:

[![Establecer permisos de Internet en las opciones de aplicación](kitkat-images/internet.png)](kitkat-images/internet.png#lightbox)

##### <a name="print-menu-item"></a>Elemento de menú de impresión

La opción de impresión normalmente aparecerá en la actividad [menú opciones](https://developer.android.com/guide/topics/ui/menus.html#options-menu).
El menú de opciones permite a los usuarios realizar acciones en una actividad. Se encuentra en la esquina superior derecha de la pantalla y tiene este aspecto:

[![Captura de pantalla de ejemplo de elemento de menú de impresión muestra en la esquina superior derecha de la pantalla](kitkat-images/menu.png)](kitkat-images/menu.png#lightbox)


Se pueden definir elementos de menú adicionales en el *menú*directorio bajo *recursos*. El código siguiente define un ejemplo elemento de menú denominado [impresión](https://developer.xamarin.com/api/type/Android.Print.PrintManager/):

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/menu_print"
        android:title="Print"
        android:showAsAction="never" />
</menu>
```

Interacción con el menú Opciones de la actividad tiene lugar a través del `OnCreateOptionsMenu` y `OnOptionsItemSelected` métodos.
`OnCreateOptionsMenu` es el lugar para agregar nuevos elementos de menú, como la opción de impresión desde el *menú* directorio de recursos.
`OnOptionsItemSelected` realiza escuchas para el usuario selecciona la opción Imprimir del menú y comience la impresión:

```csharp
bool dataLoaded;

public override bool OnCreateOptionsMenu (IMenu menu)
{
    base.OnCreateOptionsMenu (menu);
    if (dataLoaded) {
        MenuInflater.Inflate (Resource.Menu.print, menu);
    }
    return true;
}

public override bool OnOptionsItemSelected (IMenuItem item)
{
    if (item.ItemId == Resource.Id.menu_print) {
        PrintPage ();
        return true;
    }
    return base.OnOptionsItemSelected (item);
}
```

El código anterior también define una variable denominada `dataLoaded` para controlar el estado del contenido HTML. El `WebViewClient` establecerá esta variable en true cuando se ha cargado todo el contenido, para que sepa de la actividad para agregar el elemento de menú de impresión en el menú de opciones.

##### <a name="webviewclient"></a>WebViewClient

El trabajo de la `WebViewClient` consiste en asegurarse de que los datos en el `WebView` está totalmente cargado antes de que aparezca la opción de impresión en el menú, lo que sucede con la `OnPageFinished` método. `OnPageFinished` realiza escuchas para que el contenido web termine de cargarse, e indica la actividad para volver a crear con el menú de opciones `InvalidateOptionsMenu`:

```csharp
class MyWebViewClient : WebViewClient
{
    PrintHtmlActivity caller;

    public MyWebViewClient (PrintHtmlActivity caller)
    {
        this.caller = caller;
    }

    public override void OnPageFinished (WebView view, string url)
    {
        caller.dataLoaded = true;
        caller.InvalidateOptionsMenu ();
    }
}
```

`OnPageFinished` También establece la `dataLoaded` valor `true`, por lo que `OnCreateOptionsMenu` puede volver a crear el menú con la opción de imprimir en su lugar.

##### <a name="printmanager"></a>PrintManager

En el ejemplo de código siguiente se imprime el contenido de un `WebView`:

```csharp
void PrintPage ()
{
    PrintManager printManager = (PrintManager)GetSystemService (Context.PrintService);
    PrintDocumentAdapter printDocumentAdapter = myWebView.CreatePrintDocumentAdapter ();
    printManager.Print ("MyWebPage", printDocumentAdapter, null);
}
```

`Print` toma como argumentos: un nombre para el trabajo de impresión ("MyWebPage" en este ejemplo), un [`PrintDocumentAdapter`](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/)
que genera el documento de impresión a partir del contenido, y [`PrintAttributes`](https://developer.xamarin.com/api/type/Android.Print.PrintAttributes/)
(`null` en el ejemplo anterior). Puede especificar `PrintAttributes` para ayudar a diseñar el contenido en la página impresa, aunque los atributos predeterminados deben controlar la mayoría de los escenarios.

Una llamada a `Print` carga la interfaz de usuario de impresión, que se enumera las opciones del trabajo de impresión. La interfaz de usuario ofrece a los usuarios la opción de imprimir o guardar el contenido HTML a PDF, como se muestra en las capturas de pantalla siguiente:

[![Captura de pantalla de PrintHtmlActivity mostrar el menú de impresión](kitkat-images/print1.png)](kitkat-images/print1.png#lightbox)

[![Captura de pantalla de PrintHtmlActivity mostrar Guardar como menú PDF](kitkat-images/print2.png)](kitkat-images/print2.png#lightbox)

<a name="hardware" />

## <a name="hardware"></a>Hardware

KitKat agrega varias API para dar cabida a nuevas características de dispositivos. Más notables de estos están basadas en Host emulación de la tarjeta y el nuevo `SensorManager`.

### <a name="host-based-card-emulation-in-nfc"></a>Emulación de tarjetas basadas en host en NFC

Emulación de tarjeta basada en host (HCE) permite a las aplicaciones se comportan como las tarjetas de NFC o lectores de tarjetas de NFC sin tener que depender de elemento de proteger su propiedad del transportista. Antes de configurar HCE, asegúrese de que está disponible en el dispositivo con HCE `PackageManager.HasSystemFeature`:

```csharp
bool hceSupport = PackageManager.HasSystemFeature(PackageManager.FeatureNfcHostCardEmulation);
```

HCE requiere que tanto la característica HCE y `Nfc` permiso se ha registrado con la aplicación `AndroidManifest.xml`:

```xml
<uses-feature android:name="android.hardware.nfc.hce" />
```

[![Establecer el permiso de NFC en las opciones de aplicación](kitkat-images/nfc.png)](kitkat-images/nfc.png#lightbox)

Para trabajar, HCE debe ser capaz de ejecutar en segundo plano y debe iniciar cuando el usuario realiza una transacción NFC, incluso si no se está ejecutando la aplicación mediante HCE. Podemos lograr esto al escribir el código HCE como un `Service`. Implementa un servicio HCE el `HostApduService` interfaz, que implementa los métodos siguientes:

-  *ProcessCommandApdu* -una aplicación protocolo datos unidad (APDU) es lo que se envía entre el lector NFC y el servicio HCE. Este método consume un ADPU del lector y devuelve una unidad de datos de respuesta.

-  *OnDeactivated* : la `HostAdpuService` se desactiva cuando el servicio HCE ya no se está comunicando con el lector NFC.


Un servicio HCE también debe estar registrado con el manifiesto de la aplicación y decorado con los permisos adecuados, filtro de intento y metadatos. El código siguiente es un ejemplo de un `HostApduService` registrado con el manifiesto de Android mediante el `Service` atributo (para obtener más información sobre los atributos, consulte el Xamarin [trabajar con el manifiesto de Android](~/android/platform/android-manifest.md) guía):

```csharp
[Service(Exported=true, Permission="android.permissions.BIND_NFC_SERVICE"),
    IntentFilter(new[] {"android.nfc.cardemulation.HOST_APDU_SERVICE"}),
    MetaData("android.nfc.cardemulation.host.apdu_service",
    Resource="@xml/hceservice")]

class HceService : HostApduService
{
    public override byte[] ProcessCommandApdu(byte[] apdu, Bundle extras)
    {
        ...
    }

    public override void OnDeactivated (DeactivationReason reason)
    {
        ...
    }
}
```

El servicio anterior ofrece una manera para el lector NFC interactuar con la aplicación, pero el lector NFC no aún ninguna manera de saber si este servicio está emulando la tarjeta NFC que necesita analizar. Para ayudar a que el lector NFC identificar el servicio, podemos asignar el servicio de un único *Id. de aplicación (AID)* . Se especifica una ayuda, junto con otros metadatos sobre el servicio HCE, en un archivo de recursos xml registrado con el `MetaData` atributo (vea el ejemplo de código anterior). Este archivo de recursos Especifica uno o más filtros ayuda - cadenas de identificador único en formato hexadecimal que corresponden a las ayudas de uno o varios dispositivos del lector de NFC:

```xml
<host-apdu-service xmlns:android="http://schemas.android.com/apk/res/android"
    android:description="@string/hce_service_description"
    android:requireDeviceUnlock="false"
    android:apduServiceBanner="@drawable/service_banner">
    <aid-group android:description="@string/aid_group_description"
                android:category="payment">
        <aid-filter android:name="1111111111111111"/>
        <aid-filter android:name="0123456789012345"/>
    </aid-group>
</host-apdu-service>
```

Además de los filtros de ayuda, el archivo de recursos xml también proporciona una descripción para el usuario del servicio HCE, especifica un grupo de ayuda (aplicación de pago frente a "Sí") y, en el caso de una aplicación de pago, un banner de dp de 260 x 96 para mostrar al usuario.

El programa de instalación que se ha descrito anteriormente proporciona los bloques de creación básicos para una aplicación emular una tarjeta NFC. NFC propio requiere varios pasos más y otras pruebas para configurar. Para obtener más información sobre la emulación de la tarjeta basada en Host, consulte el [portal de documentación de Android](https://developer.android.com/guide/topics/connectivity/nfc/hce.html).
Para obtener más información sobre el uso de NFC con Xamarin, consulte el [ejemplos de Xamarin NFC](https://github.com/xamarin/monodroid-samples/tree/master/NfcSample).

### <a name="sensors"></a>Sensores

KitKat proporciona acceso a los sensores del dispositivo a través de un [ `SensorManager` ](https://developer.xamarin.com/api/type/Android.Hardware.SensorManager/).
El `SensorManager` permite al sistema operativo programar la entrega de información del sensor a una aplicación en lotes, conservando la duración de la batería.

KitKat también se distribuye con dos nuevos tipos de sensor para realizar el seguimiento de los pasos del usuario. Estos se basan en acelerómetro e incluyen:

-  *StepDetector* -App es una notificación/despertado cuando el usuario lleva un paso y el detector proporciona un valor de tiempo para cuando se produjo el paso.

-  *StepCounter* -realiza un seguimiento de la cantidad de pasos que el usuario ha realizado desde que se registró el sensor *hasta el siguiente reinicio de dispositivo*.

La captura de pantalla siguiente muestra el contador de paso en acción:

[![Captura de pantalla de la aplicación SensorsActivity mostrar un contador de paso](kitkat-images/stepcounter.png)](kitkat-images/stepcounter.png#lightbox)

Puede crear un `SensorManager` mediante una llamada a `GetSystemService(SensorService)` y convirtiendo el resultado como un `SensorManager`. Para utilizar el contador de paso, llame a `GetDefaultSensor` en el `SensorManager`. Puede registrar el sensor y escuche los cambios en el recuento de pasos con la Ayuda de la [`ISensorEventListener`](https://developer.xamarin.com/api/type/Android.Hardware.ISensorEventListener/)
interfaz, como se muestra en el ejemplo de código siguiente:

```csharp
public class MainActivity : Activity, ISensorEventListener
{
    float count = 0;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        SetContentView (Resource.Layout.Main);

        SensorManager senMgr = (SensorManager) GetSystemService (SensorService);
        Sensor counter = senMgr.GetDefaultSensor (SensorType.StepCounter);
        if (counter != null) {
            senMgr.RegisterListener(this, counter, SensorDelay.Normal);
        }
    }

    public void OnAccuracyChanged (Sensor sensor, SensorStatus accuracy)
    {
        Log.Info ("SensorManager", "Sensor accuracy changed");
    }

    public void OnSensorChanged (SensorEvent e)
    {
        count = e.Values [0];
    }
}
```

`OnSensorChanged` se llama si el recuento de pasos que se actualiza mientras la aplicación está en primer plano. Si la aplicación entra en el fondo o el dispositivo está suspendido, `OnSensorChanged` no se llamará; sin embargo, seguirá los pasos para contabilizarse hasta `UnregisterListener` se llama.

Tenga en cuenta que *es acumulativo el valor de recuento de paso a través de todas las aplicaciones que registrar el sensor*. Esto significa que aunque desinstale y vuelva a instalar la aplicación e inicializar el `count` variable en 0 al iniciarse la aplicación, el valor notificado por el sensor seguirá siendo el número total de pasos realizados mientras se registraba el sensor, ya sea por su aplicación u otro. Se puede impedir que la aplicación agregando al contador paso mediante una llamada a `UnregisterListener` en el `SensorManager`, como se muestra en el código siguiente:

```csharp
protected override void OnPause()
{
    base.OnPause ();
    senMgr.UnregisterListener(this);
}
```

Reiniciar el dispositivo restablece el recuento de pasos en 0. La aplicación requerirá código adicional para asegurarse de que está informando de un recuento preciso de la aplicación, independientemente de otras aplicaciones con el sensor o el estado del dispositivo.


> [!NOTE]
> Mientras la API para la detección de paso y el recuento se distribuye con KitKat, no todos los teléfonos equipados con el sensor. Puede comprobar si el sensor está disponible mediante la ejecución de `PackageManager.HasSystemFeature(PackageManager.FeatureSensorStepCounter);`, o compruebe el valor devuelto de `GetDefaultSensor` no `null`.


<a name="developer_tools" />

## <a name="developer-tools"></a>Herramientas de desarrollo

### <a name="screen-recording"></a>Grabación de pantalla

KitKat incluye pantalla nueva grabación de capacidades para que los desarrolladores pueden registrar aplicaciones en acción. Grabación de pantalla está disponible a través de la [Android Debug Bridge (ADB)](https://developer.android.com/tools/help/adb.html) cliente, que se puede descargar como parte del SDK de Android.

Para grabar la pantalla, conectar el dispositivo; a continuación, busque la instalación del SDK de Android, vaya a la **platform-tools** directorio y ejecute el **adb** cliente:

```shell
adb shell screenrecord /sdcard/screencast.mp4
```

El comando anterior grabará un vídeo de 3 minutos de forma predeterminada en la resolución predeterminada de 4 Mbps. Para modificar la longitud, agregue el *--plazo* marca.
Para cambiar la resolución, agregue el *: velocidad de bits* marca. El comando siguiente registra un vídeo de minuto con 8 Mbps:

```shell
adb shell screenrecord --bit-rate 8000000 --time-limit 60 /sdcard/screencast.mp4
```

Puede encontrar el vídeo en el dispositivo, aparecerá en la galería una vez completada la grabación.


## <a name="other-kitkat-additions"></a>Otras adiciones KitKat

Además de los cambios que se ha descrito anteriormente, KitKat le permite:

-  *Utilice la pantalla completa* -KitKat introduce un nuevo [modo envolvente](https://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int)) para examinar el contenido, jugar y ejecutan otras aplicaciones que pueden beneficiarse de una experiencia de pantalla completa.

-  *Personalizar las notificaciones* -obtener detalles adicionales sobre las notificaciones del sistema con el [`NotificationListenerService`](https://developer.xamarin.com/api/type/Android.Service.Notification.NotificationListenerService/)
   . Esto le permite presentar la información de forma distinta dentro de la aplicación.

-  *Crear el reflejo de recursos Drawable* -recursos Drawable tienen un nuevo [`autoMirrored`](https://developer.android.com/reference/android/R.attr.html#autoMirrored)
   atributo que indica al sistema cree una versión reflejada para las imágenes que requieren Voltear para diseños de izquierda a derecha.

-  *Pausar las animaciones* -pausar y reanudar animaciones creadas con el [`Animator`](https://developer.xamarin.com/api/type/Android.Animation.Animator/)
   clase.

-  *Leer dinámicamente cambiar texto* -denotan las partes de la interfaz de usuario que actualicen dinámicamente con nuevo texto como "las regiones activas" con la nueva [ `accessibilityLiveRegion`](https://developer.android.com/reference/android/R.attr.html#accessibilityLiveRegion)
   atributo para que el nuevo texto leerá automáticamente en modo de accesibilidad.

-  *Mejorar la experiencia de Audio* -Make realiza un seguimiento más alto con la [`LoudnessEnhancer`](https://developer.xamarin.com/api/type/Android.Media.Audiofx.LoudnessEnhancer/)
   , busque el pico y RMS de una secuencia de audio con el [`Visualizer`](https://developer.xamarin.com/api/field/Android.Media.Audiofx.Visualizer.MeasurementModePeakRms/)
   clase y obtener información desde un [timestamp audio](https://developer.xamarin.com/api/type/Android.Media.AudioTimestamp/) para ayudar con la sincronización de audio y vídeo.

-  *Sincronizar ContentResolver personalizado intervalos* -KitKat agrega cierta variación a la vez que se realiza una solicitud de sincronización. Sincronizar un `ContentResolver` a tiempo personalizado o un intervalo mediante una llamada a `ContentResolver.RequestSync` y pasando un `SyncRequest`.

-  *Distinguir entre los controladores* -en KitKat, los controladores se asignan los identificadores de entero único que pueden obtenerse a través del dispositivo `ControllerNumber` propiedad. Esto facilita indicar a los jugadores más en un juego.

-  *Control remoto* -con unos pocos cambios en el hardware y software de lado, KitKat le permite activar un dispositivo equipado con un transmisor de infrarrojos en un control remoto mediante el `ConsumerIrService`e interactuar con dispositivos periféricos con el nuevo [`RemoteController`](https://developer.xamarin.com/api/type/Android.Media.RemoteController/)
   API.

Para obtener más información sobre los cambios de API anteriores, consulte Google [API de Android 4.4](https://developer.android.com/about/versions/android-4.4.html) información general.


## <a name="summary"></a>Resumen

En este artículo introdujo algunas de las nuevas API disponibles en Android 4.4 (API nivel 19) y trata los procedimientos recomendados al realizar la transición KitKat una aplicación. TI experimentan cambios con contorno en las API de usuario, incluida la *framework transición* y nuevas opciones para *temas*. A continuación, introdujo el *marco de acceso a almacenamiento* y `DocumentsProvider` clase, así como el nuevo *las API de impresión*. Lo explorado *emulación de tarjetas basadas en host NFC* y cómo trabajar con *sensores de bajo consumo de energía*, incluidos dos nuevos sensores para realizar el seguimiento de los pasos del usuario. Por último, se muestra en tiempo real demostraciones de aplicaciones con la captura *grabación de pantalla*y proporciona una lista detallada de la API KitKat cambios e incorporaciones.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de KitKat](https://developer.xamarin.com/samples/monodroid/KitKat/)
- [Android 4.4 API](https://developer.android.com/about/versions/android-4.4.html)
- [KitKat Android](https://developer.android.com/about/versions/kitkat.html)
