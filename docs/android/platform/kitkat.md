---
title: "Características de KitKat"
description: "Android 4.4 (KitKat) viene equipada con gran cantidad de características para los usuarios y desarrolladores. Esta guía resalta algunas de estas características y proporciona ejemplos de código y los detalles de implementación que le ayudarán a sacar el máximo partido KitKat."
ms.topic: article
ms.prod: xamarin
ms.assetid: D3FDEA1C-F076-406F-BCC3-2A55D2C6ADEE
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 8fbb3f73fdc09f953ad5f7134020c1555d000d28
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="kitkat-features"></a>Características de KitKat

_Android 4.4 (KitKat) viene equipada con gran cantidad de características para los usuarios y desarrolladores. Esta guía resalta algunas de estas características y proporciona ejemplos de código y los detalles de implementación que le ayudarán a sacar el máximo partido KitKat._

## <a name="overview"></a>Información general

Android 4.4 (nivel de API 19), también conocido como "KitKat", se publicó en tiempo de ejecución de 2013. KitKat ofrece una serie de nuevas características y mejoras, incluidas:

-  [Experiencia del usuario](#user_experience) &ndash; fácil animaciones con transición framework, translúcidas barras de estado y la navegación y modo de pantalla completa atractiva ayudan a crear una mejor experiencia para el usuario.

-  [Contenido de usuario](#user_content) &ndash; simplificado de administración de archivos de usuario con el marco de trabajo de acceso de almacenamiento; imprimir fotos, sitios web y otro tipo de contenido es más fácil con la API de impresión mejorada.

-  [Hardware](#hardware) &ndash; convertir cualquier aplicación en una tarjeta de NFC con NFC basada en Host emulación de la tarjeta; ejecutar sensores de bajo consumo de energía con el `SensorManager` .

-  [Herramientas de desarrollo](#developer_tools) &ndash; aplicaciones de presentación en pantalla en la acción con el cliente de Android Debug Bridge, disponible como parte del SDK de Android.


Esta guía proporciona instrucciones para migrar una aplicación existente de Xamarin.Android a KitKat, así como una descripción general de KitKat para desarrolladores de Xamarin.Android.

## <a name="requirements"></a>Requisitos

Para desarrollar aplicaciones de Xamarin.Android con KitKat, deberá *Xamarin.Android 4.11.0* o superior y Android 4.4 (nivel de API 19) instalado mediante el Administrador de SDK de Android, como se muestra en la captura de pantalla siguiente:

[![Seleccionar Android 4.4 en el Administrador de SDK de Android](kitkat-images/api19.png)](kitkat-images/api19.png#lightbox)

<a name="Migrating_Your_App_to_KitKat" />

## <a name="migrating-your-app-to-kitkat"></a>Migrar la aplicación a KitKat

Esta sección proporciona algunos elementos de la primera respuesta para ayudar a las aplicaciones existentes de transición a Android 4.4.

### <a name="check-system-version"></a>Compruebe la versión del sistema

Si una aplicación debe ser compatible con versiones anteriores de Android, asegúrese de incluir cualquier código específico de la KitKat en una comprobación de la versión de sistema, como se muestra en el ejemplo de código siguiente:

```csharp
if (Build.VERSION.SdkInt >= BuildVersionCodes.Kitkat) {
    //KitKat only code here
}
```

### <a name="alarm-batching"></a>Procesamiento por lotes de alarma

Android usa servicios de alarma para reactivar una aplicación en segundo plano en un momento determinado. KitKat esto tiene un paso adelante mediante el procesamiento por lotes alarmas para conservar la energía. Esto significa que, en lugar de activar cada aplicación en el momento exacto, KitKat preferida agrupar varias aplicaciones que se registran para reactivar durante el mismo intervalo de tiempo y reactivación de ellos al mismo tiempo.
Para indicar Android para activar una aplicación durante un intervalo de tiempo especificado, llame a `SetWindow` en el [ `AlarmManager` ](https://developer.xamarin.com/api/type/Android.App.AlarmManager/), pasando el mínimo y el tiempo máximo, en milisegundos, que puede transcurrir antes de que se activa la aplicación y la operación para realizar en la activación.
El código siguiente muestra un ejemplo de una aplicación que necesite se hayan activado los entre una media hora y una hora desde el momento en que se establece la ventana:

```csharp
AlarmManager alarmManager = (AlarmManager)GetSystemService(AlarmService);
alarmManager.SetWindow (AlarmType.Rtc, AlarmManager.IntervalHalfHour, AlarmManager.IntervalHour, pendingIntent);
```

Para continuar reactivar una aplicación en el momento exacto, utilice `SetExact`, pasando el momento exacto en que la aplicación debe estar activa y la operación a realizar:

```csharp
alarmManager.SetExact (AlarmType.Rtc, AlarmManager.IntervalDay, pendingIntent);
```

KitKat ya no permite establecer una alarma de repetición exacta. Las aplicaciones que utilizan [ `SetRepeating` ](https://developer.xamarin.com/api/member/Android.App.AlarmManager.SetRepeating/p/Android.App.AlarmType/System.Int64/System.Int64/Android.App.PendingIntent/) y requieren alarmas exactas para trabajar tendrán ahora debe desencadenar manualmente cada alarma.

### <a name="external-storage"></a>Almacenamiento externo

Almacenamiento externo ahora se divide en dos tipos: almacenamiento único para la aplicación y los datos compartidos por varias aplicaciones. Leer y escribir en la ubicación específica de la aplicación en un almacenamiento externo no requieren permisos especiales. Interactuar con los datos en el almacenamiento compartido ahora requiere el `READ_EXTERNAL_STORAGE` o `WRITE_EXTERNAL_STORAGE` permiso. Los dos tipos se pueden clasificar como tales:

-  Si va a recibir una ruta de acceso de archivo o directorio mediante una llamada a un método en `Context` , por ejemplo, [ `GetExternalFilesDir` ](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalFilesDir/p/System.String/) o [`GetExternalCacheDirs`](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalCacheDirs%28%29/)
   - la aplicación no necesita ningún permiso adicional.

-  Si se obtiene acceso a una propiedad o llamar a un método en una ruta de acceso de archivo o directorio `Environment` , como [ `GetExternalStorageDirectory` ](https://developer.xamarin.com/api/property/Android.OS.Environment.ExternalStorageDirectory/) o [ `GetExternalStoragePublicDirectory` ](https://developer.xamarin.com/api/member/Android.OS.Environment.GetExternalStoragePublicDirectory/p/System.String/) , la aplicación necesita la `READ_EXTERNAL_STORAGE` o `WRITE_EXTERNAL_STORAGE` permiso.

> [!NOTE]
> `WRITE_EXTERNAL_STORAGE` implica el `READ_EXTERNAL_STORAGE` permiso, por lo que siempre tendrá que establecer un permiso.

### <a name="sms-consolidation"></a>Consolidación de SMS

KitKat simplifica la mensajería para el usuario al agregar todo el contenido SMS de la aplicación de un valor predeterminado seleccionado por el usuario. El programador es responsable de hace que la aplicación seleccionable que la aplicación de mensajería de manera predeterminada y su comportamiento adecuadamente en el código y en la vida si la aplicación no está seleccionada. Para obtener más información sobre la transición de la aplicación SMS para KitKat, consulte el [Your SMS aplicaciones preparar para KitKat](http://android-developers.blogspot.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html) Guía de Google.

### <a name="webview-apps"></a>Aplicaciones de WebView

[WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) obtenido un makeover en KitKat. El cambio más importante es mayor seguridad para cargar contenido en un `WebView`. Aunque la mayoría de las aplicaciones destinadas a versiones anteriores de API debería funcionar según lo esperado, probar las aplicaciones que utilizan la `WebView` clase es muy recomendable. Para obtener más información acerca de afectados APIs WebView hacen referencia a la Android [migrar a WebView en Android 4.4](http://developer.android.com/guide/webapps/migrating.html) documentación.

<a name="user_experience" />

## <a name="user-experience"></a>Experiencia del usuario

KitKat incluye varias nuevas API para mejorar la experiencia del usuario, incluido el nuevo marco de transición para el tratamiento de las animaciones de propiedad y una opción de interfaz de usuario translúcida de temas. Más adelante se explican estos cambios.

### <a name="transition-framework"></a>Marco de trabajo de transición

El marco de trabajo de transición, animaciones más fácil implementar. KitKat permite realizar una animación de propiedad simple con una única línea de código, o bien personalizar transiciones usando *plano*.

#### <a name="simple-property-animation"></a>Animación de propiedad simple

La nueva biblioteca de transiciones Android simplifica el código detrás de las animaciones de propiedad. El marco de trabajo permite realizar animaciones simples con código mínimo. Por ejemplo, el siguiente código de ejemplo utiliza [ `TransitionManager.BeginDelayedTransition` ](https://developer.xamarin.com/api/member/Android.Transitions.TransitionManager.BeginDelayedTransition/p/Android.Views.ViewGroup/Android.Transitions.Transition/) para animar mostrando y ocultando un `TextView`:

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

El ejemplo anterior utiliza el marco de trabajo de transición para crear un automático, la transición predeterminada entre los valores de propiedad cambiantes. Dado que la animación se controla mediante una sola línea de código, puede fácilmente realizar esto compatible con versiones anteriores de Android ajustando el `BeginDelayedTransition` llamar a en una comprobación de la versión de sistema. Consulte la [migrar la aplicación a KitKat](#Migrating_Your_App_to_KitKat) sección para obtener más información.

La captura de pantalla siguiente muestra la aplicación antes de la animación:

[![Captura de pantalla de aplicación antes de que empiece de animación](kitkat-images/trans-before.png)](kitkat-images/trans-before.png#lightbox)

La captura de pantalla siguiente muestra la aplicación después de la animación:

[![Captura de pantalla de aplicación una vez completada la animación](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

Puede obtener más control sobre la transición con plano, que se tratan en la sección siguiente.

#### <a name="android-scenes"></a>Plano Android

[Plano](https://developer.xamarin.com/api/type/Android.Transitions.Scene/) se incluyeron como parte de la transición .NET framework para proporcionar a los desarrolladores más control sobre las animaciones. Plano crea un área dinámico en la interfaz de usuario: especifica un contenedor y varias versiones o "plano", para el contenido XML dentro del contenedor, y Android no el resto del trabajo para animar las transiciones entre bastidores. Plano Android le permite crear animaciones complejas con el mínimo esfuerzo en el lado de desarrollo.

El elemento de interfaz de usuario estático donde se encuentran el contenido dinámico es una llamada un *contenedor* o *escena base*. El ejemplo siguiente usa el Diseñador de Android para crear un `RelativeLayout` denominado `container`:

[![Uso del Diseñador de Android para crear un contenedor de RelativeLayout](kitkat-images/container.png)](kitkat-images/container.png#lightbox)

El diseño de ejemplo también define un botón denominado `sceneButton` a continuación el `container`. Este botón desencadenará la transición.

El contenido dinámico en el contenedor requiere dos nuevos diseños de Android. Estos diseños especifican solo el código *en* el contenedor.
El ejemplo de código siguiente define un diseño denominado *Scene1* que contiene dos campos de texto leer "Kit" y "Kat" respectivamente, y llama a un segundo diseño *Scene2* que contiene los mismos campos de texto que se invierten. El XML es como sigue:

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

En el ejemplo anterior se usa `merge` para hacer que el código de vista más corto y simplificar la jerarquía de vista. Más información sobre `merge` diseños [aquí](http://android-developers.blogspot.com/2009/03/android-layout-tricks-3-optimize-by.html).

Se crea una escena mediante una llamada a [ `Scene.GetSceneForLayout` ](https://developer.xamarin.com/api/member/Android.Transitions.Scene.GetSceneForLayout/p/Android.Views.ViewGroup/System.Int32/Android.Content.Context/), pasando el objeto de contenedor, el identificador de recurso del archivo de diseño de la escena y actual `Context`, tal y como se muestra en el ejemplo de código siguiente:

```csharp
RelativeLayout container = FindViewById<RelativeLayout> (Resource.Id.container);

Scene scene1 = Scene.GetSceneForLayout(container, Resource.Layout.Scene1, this);
Scene scene2 = Scene.GetSceneForLayout(container, Resource.Layout.Scene2, this);

scene1.Enter();
```

Al hacer clic en el botón voltea entre dos plano, que anima Android con los valores de transición de forma predeterminada:

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
> Hay un [error conocido](https://code.google.com/p/android/issues/detail?id=62450) en las transiciones Android biblioteca que hace plano siguieron `GetSceneForLayout` se interrumpa cuando un usuario navega a través de una actividad de la segunda vez. Se describe una solución de java [aquí](http://www.doubleencore.com/2013/11/new-transitions-framework/).


##### <a name="custom-transitions-in-scenes"></a>Transiciones personalizadas en una escena

Se puede definir una transición personalizada en un archivo de recursos xml en el `transition` directorio bajo `Resources`, tal y como se muestra en la captura de pantalla siguiente:

[![Ubicación del archivo de transition.xml en el directorio de recursos/transición](kitkat-images/resources.png)](kitkat-images/resources.png#lightbox)

El ejemplo de código siguiente define una transición que anima durante 5 segundos y usa el [supera interpolador](http://developer.android.com/reference/android/views/animation/OvershootInterpolator.html):

```xml
<changeBounds
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:duration="5000"
  android:interpolator="@android:anim/overshoot_interpolator" />
```

La transición se crea en la actividad utilizando el [TransitionInflater](https://developer.xamarin.com/api/type/Android.Transitions.TransitionInflater/), tal y como se muestra en el código siguiente:

```csharp
Transition transition = TransitionInflater.From(this).InflateTransition(Resource.Transition.transition);
```

A continuación, se agrega la nueva transición a la `Go` llamada que comienza la animación:

```csharp
TransitionManager.Go (scene1, transition);
```

### <a name="translucent-ui"></a>Interfaz de usuario translúcido

KitKat proporciona más control sobre temas de la aplicación con las barras de estado y la navegación de transclucent opcional. Puede cambiar la transparencia de los elementos de interfaz de usuario del sistema en el mismo archivo XML que se usa para definir el tema Android. KitKat presenta las siguientes propiedades:

-  `windowTranslucentStatus` -Cuando se establece como true, hace que la barra de estado superior translúcido.

-  `windowTranslucentNavigation` -Cuando se establece como true, hace que la barra de navegación inferior translúcido.

-  `fitsSystemWindows` -La configuración de la barra de la parte superior o inferior a transcluent desplaza el contenido en los elementos de interfaz de usuario transparentes de forma predeterminada. Si se establece esta propiedad en `true` es una manera sencilla de evitar que el contenido que se superponen con los elementos de interfaz de usuario del sistema translúcido.


El código siguiente define un tema con translúcido barras de estado y la navegación:

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

La captura de pantalla siguiente muestra el tema anterior con el estado translúcido y barras de navegación:

[![Captura de pantalla de ejemplo de aplicación con translúcido barras de estado y la navegación](kitkat-images/theme.png)](kitkat-images/theme.png#lightbox)

<a name="user_content" />

## <a name="user-content"></a>Contenido de usuario

### <a name="storage-access-framework"></a>Marco de trabajo de acceso de almacenamiento

El marco de trabajo de acceso de almacenamiento (SAF) es una nueva forma a los usuarios interactuar con el contenido almacenado como imágenes, vídeos y documentos. En lugar de presentar a los usuarios con un cuadro de diálogo para elegir una aplicación para administrar contenido, KitKat abre una nueva interfaz de usuario que permite a los usuarios accedan a los datos en una ubicación agregado. Una vez que se ha elegido el contenido, el usuario volverá a la aplicación que solicitó el contenido y la experiencia de aplicación continuará con normalidad.

Este cambio requiere dos acciones en el lado de desarrollador: en primer lugar, las aplicaciones que requieren el contenido de proveedores deben actualizarse a una nueva forma de reqesting contenido. Segundo, las aplicaciones que escriben datos en un `ContentProvider` deba modificarse para usar el nuevo marco. Ambos escenarios dependen de la nueva [ `DocumentsProvider` ](https://developer.xamarin.com/api/type/Android.Provider.DocumentsProvider/) API.

#### <a name="documentsprovider"></a>DocumentsProvider

En KitKat, interacciones con `ContentProviders` se extraen con la `DocumentsProvider` clase. Esto significa que SAF no le importa dónde están los datos físicamente, siempre que sea accesible a través de la `DocumentsProvider` API. Proveedores locales, servicios de nube y dispositivos de almacenamiento externo todos utilizan la misma interfaz y se tratan del mismo modo, proporciona el usuario y el desarrollador con un solo lugar para interactuar con el contenido del usuario.

En esta sección se describe cómo cargar y guardar el contenido con el marco de trabajo de acceso de almacenamiento.

#### <a name="request-content-from-a-provider"></a>Contenido de la solicitud de un proveedor

Podemos indicamos KitKat que deseamos Elegir contenido usando la UI de SAF con el `ActionOpenDocument` intento, lo que significa que queremos conectarnos a todos los proveedores de contenido disponibles para el dispositivo. Puede agregar un filtrado para esta intención especificando `CategoryOpenable`, lo que significa que sólo el contenido que se pueden abrir (es decir, acceder a ella, puede usar contenido) se devolverá. KitKat también permite filtrar de contenido con el `MimeType`. Por ejemplo, el código siguiente filtros para los resultados de la imagen mediante la especificación de la imagen `MimeType`:

```csharp
Intent intent = new Intent (Intent.ActionOpenDocument);
intent.AddCategory (Intent.CategoryOpenable);
intent.SetType ("image/*");
StartActivityForResult (intent, save_request_code);
```

Al llamar a `StartActivityForResult` inicia la UI SAF, que el usuario, a continuación, puede examinar para elegir una imagen:

[![Captura de pantalla de ejemplo de una aplicación con el marco de trabajo de acceso de almacenamiento para ir a una imagen](kitkat-images/saf-ui.png)](kitkat-images/saf-ui.png#lightbox)

Después de que el usuario ha elegido una imagen, `OnActivityResult` devuelve el `Android.Net.Uri` del archivo elegido. El ejemplo de código siguiente muestra la selección del usuario imagen:

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

Además de cargar el contenido de la UI de SAF, KitKat también le permite guardar el contenido a cualquier `ContentProvider` que implementa el `DocumentProvider` API. Guardar contenido usa un `Intent` con `ActionCreateDocument`:

```csharp
Intent intentCreate = new Intent (Intent.ActionCreateDocument);
intentCreate.AddCategory (Intent.CategoryOpenable);
intentCreate.SetType ("text/plain");
intentCreate.PutExtra (Intent.ExtraTitle, "NewDoc");
StartActivityForResult (intentCreate, write_request_code);
```

El ejemplo de código anterior carga la UI SAF, lo que permite al usuario cambiar el nombre de archivo y seleccione un directorio para alojar el nuevo archivo:

[![Captura de pantalla de usuario cambia el nombre de archivo a NewDoc en el directorio de descargas](kitkat-images/saf-save.png)](kitkat-images/saf-save.png#lightbox)

Cuando el usuario presiona **guardar**, `OnActivityResult` se transfiere la `Android.Net.Uri` del archivo recién creado, que se puede acceder con `data.Data`. El uri puede usarse para transmitir datos en el nuevo archivo:

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

Tenga en cuenta que [ `ContentResolver.OpenOutputStream(Android.Net.Uri)` ](https://developer.xamarin.com/api/member/Android.Content.ContentResolver.OpenOutputStream/(Android.Net.Uri)) devuelve un `System.IO.Stream`, por lo que todo el proceso de transmisión por secuencias puede escribirse en. NET.

Para obtener más información sobre la carga, crear y editar contenido con el marco de trabajo de acceso de almacenamiento, consulte el [Android documentación para el marco de trabajo de acceso de almacenamiento](http://developer.android.com/guide/topics/providers/document-provider.html).

### <a name="printing"></a>Impresión

Contenido de impresión se ha simplificado en KitKat con la introducción de la [servicios de impresión](https://developer.xamarin.com/api/namespace/Android.PrintServices/) y `PrintManager`. KitKat también es la primera versión de API para aprovechar completamente la [API del servicio de impresión en la nube de Google](https://developers.google.com/cloud-print/) mediante la [aplicación de impresión de la nube de Google](https://play.google.com/store/apps/details?id=com.google.android.apps.cloudprint).
Mayoría de los dispositivos que se suministran con KitKat automáticamente descarga la aplicación de impresión de la nube de Google y [complemento del servicio de impresión HP](https://play.google.com/store/apps/details?id=com.hp.android.printservice)cuando primero conectarse a la red Wi-Fi. Un usuario puede comprobar la configuración de impresión de su dispositivo, vaya a **configuración > sistema > Imprimir**:

[![Captura de pantalla de ejemplo de la pantalla de configuración de impresión](kitkat-images/printing.png)](kitkat-images/printing.png#lightbox)


> [!NOTE]
> Aunque las API de impresión se configuran para trabajar con la impresión de nube de Google de forma predeterminada, Android todavía permite a los programadores preparar el contenido de impresión mediante las nuevas API y enviarlo a otras aplicaciones para controlar la impresión.



#### <a name="printing-html-content"></a>Contenido HTML de impresión

KitKat crea automáticamente un [ `PrintDocumentAdapter` ](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/) para obtener una vista web con `WebView.CreatePrintDocumentAdapter`. Contenido de web de impresión es un esfuerzo coordinado entre un [ `WebViewClient` ](https://developer.xamarin.com/api/type/Android.Webkit.WebViewClient/) que espera a que el contenido HTML cargar y permite la actividad saber para elegir la opción de impresión disponibles en el menú de opciones y la Actvity, que espera a que el usuario Seleccione la opción de impresión y llamadas `Print`en el `PrintManager`. Esta sección trata sobre la configuración básica necesaria para imprimir en la pantalla contenido HTML.

Tenga en cuenta que cargar e imprimir contenido web requieren el permiso de Internet:

[![Permiso de configuración de Internet en las opciones de aplicación](kitkat-images/internet.png)](kitkat-images/internet.png#lightbox)

##### <a name="print-menu-item"></a>Elemento de menú de impresión

La opción de impresión normalmente aparecerán en la actividad [menú opciones](http://developer.android.com/guide/topics/ui/menus.html#options-menu).
El menú de opciones permite a los usuarios realizar acciones en una actividad. Está en la esquina superior derecha de la pantalla y el siguiente aspecto:

[![Captura de pantalla de ejemplo de muestra de elemento de menú de impresión en la esquina superior derecha de la pantalla](kitkat-images/menu.png)](kitkat-images/menu.png#lightbox)


Elementos de menú adicionales se pueden definir en el *menú*directorio bajo *recursos*. El código siguiente define un ejemplo elemento de menú denominado [impresión](https://developer.xamarin.com/api/type/Android.Print.PrintManager/):

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/menu_print"
        android:title="Print"
        android:showAsAction="never" />
</menu>
```

Interacción con el menú de opciones de la actividad se realiza durante la `OnCreateOptionsMenu` y `OnOptionsItemSelected` métodos.
`OnCreateOptionsMenu` es el lugar para agregar nuevos elementos de menú, como la opción de impresión, desde el *menú* directorio de recursos.
`OnOptionsItemSelected` realiza escuchas para el usuario selecciona la opción de impresión en el menú y empezar a imprimir:

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

El código anterior también define una variable denominada `dataLoaded` para realizar un seguimiento del estado del contenido HTML. El `WebViewClient` establecerá esta variable en true cuando se ha cargado todo el contenido, para que sepa de la actividad para agregar el elemento de menú de impresión en el menú de opciones.

##### <a name="webviewclient"></a>WebViewClient

El trabajo de la `WebViewClient` consiste en asegurarse de datos en el `WebView` se ha cargado completamente antes de que aparezca la opción de impresión en el menú, lo que sucede con la `OnPageFinished` método. `OnPageFinished` realiza escuchas para que el contenido web a que finalice la carga, e indica la actividad que se va a volver a crear con el menú de opciones `InvalidateOptionsMenu`:

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

`OnPageFinished` También establece la `dataLoaded` valor `true`, por lo que `OnCreateOptionsMenu` puede volver a crear el menú con la opción de impresión en su lugar.

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

`Print` toma como argumentos: un nombre para el trabajo de impresión ("MyWebPage" en este ejemplo), un [ `PrintDocumentAdapter` ](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/) que genera el documento de impresión desde el contenido, y [ `PrintAttributes` ](https://developer.xamarin.com/api/type/Android.Print.PrintAttributes/) (`null` en el ejemplos anteriores). Puede especificar `PrintAttributes` para ayudar a diseñar contenido en la página impresa, aunque los atributos predeterminados deben satisfacer scenarions mayoría.

Al llamar a `Print` carga la interfaz de usuario de impresión, que incluye opciones para el trabajo de impresión. La interfaz de usuario proporciona a los usuarios la opción de imprimir o guardar el contenido HTML en formato PDF, como se muestra en las capturas de pantalla siguiente:

[![Captura de pantalla de PrintHtmlActivity mostrar el menú Imprimir](kitkat-images/print1.png)](kitkat-images/print1.png#lightbox)

[![Captura de pantalla de PrintHtmlActivity mostrar la operación de guardar como menús PDF](kitkat-images/print2.png)](kitkat-images/print2.png#lightbox)

<a name="hardware" />

## <a name="hardware"></a>Hardware

KitKat agrega varias API para alojar las nuevas características del dispositivo. El más notable de estas están basadas en Host emulación de la tarjeta y el nuevo `SensorManager`.

### <a name="host-based-card-emulation-in-nfc"></a>Emulación de la tarjeta basada en host en NFC

Emulación de tarjeta basada en host (HCE) permite a las aplicaciones se comportan como tarjetas de NFC o lectores de tarjetas NFC sin tener que depender propietario proteger elemento del transportista. Antes de configurar la HCE, asegúrese de que está disponible en el dispositivo con HCE `PackageManager.HasSystemFeature`:

```csharp
bool hceSupport = PackageManager.HasSystemFeature(PackageManager.FeatureNfcHostCardEmulation);
```

HCE requiere que la característica de HCE y `Nfc` permiso registrarse con la aplicación `AndroidManifest.xml`:

```xml
<uses-feature android:name="android.hardware.nfc.hce" />
```

[![Estableciendo el permiso de NFC en las opciones de aplicación](kitkat-images/nfc.png)](kitkat-images/nfc.png#lightbox)

Para trabajar, HCE debe ser capaz de ejecutar en segundo plano y debe iniciar cuando el usuario realiza una transacción CNC, incluso si no se está ejecutando la aplicación con HCE. Podemos lograr esto al escribir el código HCE como un `Service`. Implementa un servicio HCE el `HostApduService` interfaz, que implementa los métodos siguientes:

-  *ProcessCommandApdu* -una unidad de datos protocolo de aplicación (APDU) es lo que se envían entre el lector NFC y el servicio HCE. Este método consume una ADPU del lector y devuelve una unidad de datos de respuesta.

-  *OnDeactivated* : el `HostAdpuService` se desactiva cuando el servicio de HCE ya no se comunica con el lector CNC.


Un servicio de HCE también debe estar registrado con el manifiesto de la aplicación y decorada con los permisos adecuados, filtro intención y metadatos. El código siguiente es un ejemplo de un `HostApduService` registrado con el manifiesto de Android mediante el `Service` atributo (para obtener más información sobre atributos, consulte el Xamarin [trabajar con manifiestos Android](~/android/platform/android-manifest.md) guía):

```csharp
[Service(Exported=true, Permission="android.permissions.BIND_NFC_SERVICE"),
    IntentFilter(new[] {"android.nfc.cardemulation.HOST_APDU_SERVICE"}),
    MetaData("andorid.nfc.cardemulation.host.apdu_service",
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

El servicio anterior proporciona una manera para que el lector NFC interactuar con la aplicación, pero el lector CNC aún no tiene ninguna manera de saber si este servicio es emular la tarjeta NFC que debe examinar. Para ayudar a que el lector NFC identificar el servicio, podemos asignamos el servicio de un único *Id. de aplicación (AID)*. Se especifica una ayuda, junto con otros metadatos sobre el servicio HCE, en un archivo de recursos xml registrado con el `MetaData` atributo (vea el ejemplo de código anterior). Este archivo de recursos Especifica uno o más filtros ayuda - cadenas de identificador único en formato hexadecimal que corresponden a las ayudas de uno o más dispositivos de lector NFC:

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

Además de los filtros de ayuda, el archivo de recursos xml también proporciona una descripción de cara al usuario del servicio HCE, especifica un grupo de ayuda (aplicación de pago frente a "Sí") y, en el caso de una solicitud de pago, una pancarta de dp de 260 x 96 para mostrar al usuario.

El programa de instalación que se ha descrito anteriormente proporciona los bloques de creación básicos para una aplicación emular una tarjeta CNC. NFC propio requiere varios pasos más y otras pruebas para configurar. Para obtener más información sobre la emulación de la tarjeta basada en Host, consulte la [portal de documentación de Android](https://developer.android.com/guide/topics/connectivity/nfc/hce.html).
Para obtener más información sobre el uso de NFC con Xamarin, consulte el [ejemplos de Xamarin NFC](https://github.com/xamarin/monodroid-samples/tree/master/NfcSample).

### <a name="sensors"></a>Sensores

KitKat proporciona acceso a los sensores del dispositivo a través de un [ `SensorManager` ](https://developer.xamarin.com/api/type/Android.Hardware.SensorManager/).
El `SensorManager` permite que el sistema operativo programar la entrega de información sobre el sensor a una aplicación en lotes, conservar la duración de la batería.

KitKat también se distribuye con dos nuevos tipos de sensor para realizar el seguimiento de los pasos del usuario. Estos se basan en acelerómetro e incluyen:

-  *StepDetector* -aplicación se notifica/activa cuando el usuario realice un paso y el detector proporciona un valor de hora de cuando se produjo el paso.

-  *StepCounter* -realiza un seguimiento de la cantidad de pasos que el usuario ha realizado desde que se registró el sensor *hasta el siguiente reinicio de dispositivo*.

La captura de pantalla siguiente muestra el contador de paso en acción:

[![Captura de pantalla de la aplicación de SensorsActivity mostrar un contador de paso](kitkat-images/stepcounter.png)](kitkat-images/stepcounter.png#lightbox)

Puede crear un `SensorManager` mediante una llamada a `GetSystemService(SensorService)` y convertir el resultado como un `SensorManager`. Para utilizar el contador de paso, llame a `GetDeafultSensor` en el `SensorManager`. Puede registrar el sensor y escuchar a los cambios en el recuento de pasos con la Ayuda de la [ `ISensorEventListener` ](https://developer.xamarin.com/api/type/Android.Hardware.ISensorEventListener/) de la interfaz, como se muestra en el ejemplo de código siguiente:

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

`OnSensorChanged` se llama si el recuento de pasos que se actualiza mientras la aplicación está en primer plano. Si la aplicación entra en el fondo o el dispositivo está suspendido, `OnSensorChanged` no se llamará; sin embargo, sigue los pasos para contabilizarse hasta que `UnregisterListener` se llama.

Tenga en cuenta que *el valor de número de paso es acumulativo para todas las aplicaciones que registran el sensor*. Esto significa que aunque desinstale y vuelva a instalar la aplicación e inicializar el `count` variable en 0 al inicio de la aplicación, el valor notificado por el sensor seguirá siendo el número total de pasos que realiza mientras se registraba el sensor, ya sea por su aplicación u otro. Puede impedir que su aplicación agregando al contador de paso llamando `UnregisterListener` en el `SensorManager`, tal y como se muestra en el código siguiente:

```csharp
protected override void OnPause()
{
    base.OnPause ();
    senMgr.UnregisterListener(this);
}
```

Reiniciar el dispositivo, el recuento de pasos restablece a 0. La aplicación requerirá código adicional para asegurarse de que está informando de un recuento preciso de la aplicación, independientemente de otras aplicaciones que utilicen el sensor o el estado del dispositivo.


> [!NOTE]
> Mientras la API para la detección de paso y el recuento se distribuye con KitKat, no todos los teléfonos equipados con el sensor. También puede comprobar si el sensor está disponible mediante la ejecución de `PackageManager.HasSystemFeature(PackageManager.FeatureSensorStepCounter);`, o compruebe el valor devuelto de `GetDefaultSensor` no `null`.


<a name="developer_tools" />

## <a name="developer-tools"></a>Herramientas de desarrollo

### <a name="screen-recording"></a>Grabación de pantalla

KitKat incluye pantalla nueva grabación capacidades para que los desarrolladores pueden registrar las aplicaciones en acción. Grabación de pantalla está disponible a través de la [Android depuración Bridge (ADB)](http://developer.android.com/tools/help/adb.html) cliente, que se puede descargar como parte del SDK de Android.

Para grabar la pantalla, conecte el dispositivo; a continuación, busque la instalación de SDK de Android, vaya a la **herramientas de la plataforma** directorio y se ejecutan los **adb** cliente:

```shell
adb shell screenrecord /sdcard/screencast.mp4
```

El comando anterior grabará un vídeo de 3 minutos de forma predeterminada en la resolución predeterminada de 4 Mbps. Para modificar la longitud, agregue el *: límite de tiempo* marca.
Para cambiar la resolución, agregue el *: velocidad de bits* marca. El siguiente comando grabará un vídeo largo minuto a 8 Mbps:

```shell
adb shell screenrecord --bit-rate 8000000 --time-limit 60 /sdcard/screencast.mp4
```

Puede encontrar el vídeo en el dispositivo, aparecerá en la Galería de una vez finalizada la grabación.


## <a name="other-kitkat-additions"></a>Otras adiciones KitKat

Además de los cambios que se ha descrito anteriormente, KitKat le permite:

-  *Utilice la pantalla completa* -KitKat introduce un nuevo [modo envolvente](http://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int)) para examinar el contenido, jugar y ejecuta otras aplicaciones que pueden beneficiarse de una experiencia de pantalla completa.

-  *Personalizar las notificaciones* -obtener detalles adicionales sobre las notificaciones del sistema con el [ `NotificationListenerService` ](https://developer.xamarin.com/api/type/Android.Service.Notification.NotificationListenerService/) . Esto le permite presentar la información de una manera diferente dentro de la aplicación.

-  *Reflejar recursos pueden dibujar* -recursos pueden dibujar tienen un nuevo [ `autoMirrored` ](http://developer.android.com/reference/android/R.attr.html#autoMirrored) atributo que indica el sistema cree una versión reflejada para las imágenes que requieren volteo para diseños de izquierda a derecha.

-  *Pausar animaciones* -pausar y reanudar animaciones creadas con la [ `Animator` ](https://developer.xamarin.com/api/type/Android.Animation.Animator/) clase.

-  *Texto de lectura dinámicamente cambiar* -denotan elementos de interfaz de usuario que actualizan dinámicamente con texto nuevo como "regiones en vivo" con el nuevo [ `accesibilityLiveRegion` ](http://developer.android.com/reference/android/R.attr.html#accessibilityLiveRegion) atributo para el nuevo texto se leerán automáticamente en el modo de accesibilidad.

-  *Mejorar la experiencia de Audio* -Asegúrese realiza un seguimiento de más alto con la [ `LoudnessEnhancer` ](https://developer.xamarin.com/api/type/Android.Media.Audiofx.LoudnessEnhancer/) , buscar el pico y el RMS de una secuencia de audio con el [ `Visualizer` ](https://developer.xamarin.com/api/field/Android.Media.Audiofx.Visualizer.MeasurementModePeakRms/) clase y obtener información de un [timestamp audio](https://developer.xamarin.com/api/type/Android.Media.AudioTimestamp/) para ayudar a con la sincronización de audio y vídeo.

-  *Sincronizar ContentResolver personalizado intervalos* -KitKat agrega cierta variación hasta el momento en que se realiza una solicitud de sincronización. Sincronización de un `ContentResolver` a tiempo personalizado o un intervalo mediante una llamada a `ContentResolver.RequestSync` y pasando un `SyncRequest`.

-  *Distinguir entre los controladores de* -en KitKat, los controladores se asignan identificadores de entero único que se pueden tener acceso a través del dispositivo `ControllerNumber` propiedad. Esto facilita indicar reproductores separados en un juego.

-  *Control remoto* -KitKat con unos pocos cambios en el hardware y el software de lado, permite activar un dispositivo equipado con un transmisor de infrarrojos en un control remoto mediante el `ConsumerIrService`e interactuar con dispositivos periféricos con el nuevo [ `RemoteController` ](https://developer.xamarin.com/api/type/Android.Media.RemoteController/) API.

Para obtener más información sobre los cambios anteriores en la API, consulte el Google [API de Android 4.4](http://developer.android.com/about/versions/android-4.4.html) información general.


## <a name="summary"></a>Resumen

En este artículo había presentado algunas de las nuevas API disponibles en Android 4.4 (nivel de API 19) y trata los procedimientos recomendados cuando una aplicación KitKat de transición. TI experimentan cambios se ha descrito en las API que afectan a usuario, incluida la *framework transición* y nuevas opciones para *temas*. A continuación, introdujo el *Framework de acceso de almacenamiento* y `DocumentsProvider` (clase), así como la nueva *imprimir API*. Que explorar *emulación de la tarjeta basada en host de NFC* y cómo trabajar con *sensores de bajo consumo de energía*, incluidos los dos nuevos sensores para realizar el seguimiento de los pasos del usuario. Por último, muestra la captura demostraciones en tiempo real de aplicaciones con *grabación de pantalla*y proporciona una lista detallada de adiciones y cambios en la API de KitKat.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de KitKat](https://developer.xamarin.com/samples/KitKat/)
- [Android 4.4 API](http://developer.android.com/about/versions/android-4.4.html)
- [KitKat Android](http://developer.android.com/about/versions/kitkat.html)
