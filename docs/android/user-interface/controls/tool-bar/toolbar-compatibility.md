---
title: Compatibilidad de barra de herramientas
ms.prod: xamarin
ms.assetid: A0798CA1-2C7D-43B6-9E91-4435CC7B6683
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 12c19cf1024b78e8be30b7c9f2652019e9854375
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61300362"
---
# <a name="toolbar-compatibility"></a>Compatibilidad de barra de herramientas


## <a name="overview"></a>Información general

En esta sección se explica cómo usar `Toolbar` en versiones de Android anteriores a Android 5.0 Lollipop. Si la aplicación no es compatible con versiones de Android anteriores a Android 5.0, puede omitir esta sección. 

Dado que `Toolbar` forma parte de la biblioteca de compatibilidad de Android v7, puede usarse en dispositivos que ejecutan Android 2.1 (API de nivel 7) y versiones posteriores. Sin embargo, el [biblioteca de compatibilidad de Android v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) NuGet debe estar instalado y el código modificado para que utilice el `Toolbar` implementación proporcionada en esta biblioteca. Esta sección explica cómo instalar este NuGet y modificar el **ToolbarFun** aplicación desde [agregando una segunda barra de herramientas](~/android/user-interface/controls/tool-bar/adding-a-second-toolbar.md) para que se ejecute en las versiones de Android anteriores a 5.0 Lollipop.

Para modificar una aplicación para usar la versión AppCompat de barra de herramientas: 

1.  Establezca las versiones mínima y Android de destino para la aplicación.

2.  Instale el paquete AppCompat NuGet.

3.  Utilizar un tema AppCompat en lugar de un tema de Android integrado.

4.  Modificar `MainActivity` para que lo subclases `AppCompatActivity` lugar `Activity`. 

Cada uno de estos pasos se explica con detalle en las secciones siguientes.



## <a name="set-the-minimum-and-target-android-version"></a>Establezca los valores mínimo y la versión de Android de destino

.NET Framework de destino de la aplicación debe establecerse en el nivel de API 21 o superior o la aplicación no se implementará correctamente. Si un error como **no encontró ningún identificador de recurso para el atributo 'tileModeX' en el paquete 'android'** se ve durante la implementación de la aplicación, esto es porque la plataforma de destino no está establecida en **Android 5.0 (API nivel 21 - círculo)**  o superior. 

Establezca la plataforma de destino nivel a nivel de API 21 o posterior y la configuración del proyecto de nivel de API de Android para la versión Android mínima que la aplicación vaya a admitir. Para obtener más información acerca de cómo establecer los niveles de API de Android, consulte [Understanding Android API niveles](~/android/app-fundamentals/android-api-levels.md). En el `ToolbarFun` ejemplo, la versión mínima de Android se establece en KitKat (4.4 de nivel de API). 


## <a name="install-the-appcompat-nuget-package"></a>Instale el paquete AppCompat NuGet

A continuación, agregue el [biblioteca de compatibilidad de Android v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) paquete al proyecto. En Visual Studio, haga clic en **referencias** y seleccione **administrar paquetes NuGet...** . Haga clic en **examinar** y busque **biblioteca de compatibilidad de Android v7 AppCompat**. Seleccione **Xamarin.Android.Support.v7.AppCompat** y haga clic en **instalar**: 

[![Paquete de la captura de pantalla de V7 Appcompat seleccionado en Administrar paquetes NuGet](toolbar-compatibility-images/01-appcompat-nuget-sml.png)](toolbar-compatibility-images/01-appcompat-nuget.png#lightbox)

Cuando se instala este NuGet, otros paquetes de NuGet también se instalan si no está presente (como **Xamarin.Android.Support.Animated.Vector.Drawable**, **Xamarin.Android.Support.v4**, y **Xamarin.Android.Support.Vector.Drawable**). Para obtener más información acerca de cómo instalar paquetes de NuGet, consulte [Tutorial: Incluir NuGet en el proyecto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough). 


## <a name="use-an-appcompat-theme-and-toolbar"></a>Utilizar un tema de AppCompat y la barra de herramientas

La biblioteca AppCompat incluye varias `Theme.AppCompat` temas que se pueden usar en cualquier versión de Android admitida por la biblioteca de AppCompat. El `ToolbarFun` se deriva de tema de la aplicación de ejemplo `Theme.Material.Light.DarkActionBar`, que no está disponible en versiones Android anteriores a círculo. Por lo tanto, `ToolbarFun` debe adaptarse para su uso el homólogo AppCompat para este tema, `Theme.AppCompat.Light.DarkActionBar`. Además, dado que `Toolbar` es no está disponible en las versiones de Android anteriores a Lollipop, debemos usar la versión AppCompat de `Toolbar`. Por lo tanto, deben usar diseños `android.support.v7.widget.Toolbar` en lugar de `Toolbar`. 


### <a name="update-layouts"></a>Diseños de actualización

Editar **Resources/layout/Main.axml** y reemplace el `Toolbar` elemento con el siguiente código XML: 

```xml
<android.support.v7.widget.Toolbar
    android:id="@+id/edit_toolbar"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorAccent"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

Editar **Resources/layout/toolbar.xml** y reemplace su contenido con el siguiente código XML: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v7.widget.Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"/>
```

Tenga en cuenta que el `?attr` valores ya no tienen el prefijo `android:` (Recuerde que el `?` notación hace referencia a un recurso en el tema actual). Si `?android:attr` todavía se usa en este caso, Android haría referencia el valor del atributo de la plataforma que se está ejecutando en lugar de la biblioteca de AppCompat. Dado que este ejemplo se usa el `actionBarSize` definidos por la biblioteca AppCompat, el `android:` se quita el prefijo. De forma similar, `@android:style` cambia a `@style` para que el `android:theme` atributo está establecido en un tema en la biblioteca de AppCompat &ndash; el `ThemeOverlay.AppCompat.Dark.ActionBar` tema aquí se usa en lugar de `ThemeOverlay.Material.Dark.ActionBar`. 


### <a name="update-the-style"></a>Actualizar el estilo

Editar **Resources/values/styles.xml** y reemplace su contenido con el siguiente código XML: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<resources>
  <style name="MyTheme" parent="MyTheme.Base"> </style>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light.DarkActionBar">
    <item name="windowNoTitle">true</item>
    <item name="windowActionBar">false</item>
    <item name="colorPrimary">#5A8622</item>
    <item name="colorAccent">#A88F2D</item>
  </style>
</resources>
```

Los nombres de elemento y el tema primario en este ejemplo ya no tienen el prefijo con `android:` porque estamos usando la biblioteca AppCompat. Además, se cambia el tema primario a la versión AppCompat de `Light.DarkActionBar`. 



### <a name="update-menus"></a>Menús de la actualización

Para admitir versiones anteriores de Android, la biblioteca de AppCompat usa atributos personalizados que reflejan los atributos de la `android:` espacio de nombres. Sin embargo, algunos atributos (como el `showAsAction` atributo usado en el `<menu>` etiqueta) no existen en el marco de trabajo de Android en dispositivos más antiguos &ndash; `showAsAction` se introdujo en Android 11 API pero no está disponible en Android 7 de API. Por este motivo, debe usarse un espacio de nombres personalizado para prefijar todos los atributos definidos por la biblioteca de soporte técnico. En los archivos de recursos de menú, llama a un espacio de nombres `local` se define como prefijo para el `showAsAction` atributo. 

Editar **Resources/menu/top_menus.xml** y reemplace su contenido con el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:local="http://schemas.android.com/apk/res-auto">
  <item
       android:id="@+id/menu_edit"
       android:icon="@mipmap/ic_action_content_create"
       local:showAsAction="ifRoom"
       android:title="Edit" />
  <item
       android:id="@+id/menu_save"
       android:icon="@mipmap/ic_action_content_save"
       local:showAsAction="ifRoom"
       android:title="Save" />
  <item
       android:id="@+id/menu_preferences"
       local:showAsAction="never"
       android:title="Preferences" />
</menu>
```

El `local` se agrega el espacio de nombres con esta línea:

```xml
xmlns:local="http://schemas.android.com/apk/res-auto">
```

El `showAsAction` atributo se prologa con este `local:` espacio de nombres en lugar de `android:` 

```csharp
local:showAsAction="ifRoom"
```

Editar de forma similar, **Resources/menu/edit_menus.xml** y reemplace su contenido con el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:local="http://schemas.android.com/apk/res-auto">
  <item
       android:id="@+id/menu_cut"
       android:icon="@mipmap/ic_menu_cut_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Cut" />
  <item
       android:id="@+id/menu_copy"
       android:icon="@mipmap/ic_menu_copy_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Copy" />
  <item
       android:id="@+id/menu_paste"
       android:icon="@mipmap/ic_menu_paste_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Paste" />
</menu>
```

¿Cómo este modificador de espacio de nombres proporcionan compatibilidad para la `showAsAction` atributo en las versiones Android anteriores a 11 de nivel de API? El atributo personalizado `showAsAction` y todos sus valores posibles se incluyen en la aplicación cuando se instala el paquete AppCompat NuGet. 


## <a name="subclass-appcompatactivity"></a>Subclase AppCompatActivity

El último paso en la conversión es modificar `MainActivity` para que sea una subclase de `AppCompactActivity`. Editar **MainActivity.cs** y agregue la siguiente `using` instrucciones: 

```csharp
using Android.Support.V7.App;
using Toolbar = Android.Support.V7.Widget.Toolbar;
```

Esto declara `Toolbar` sea la versión AppCompat de `Toolbar`. A continuación, cambie la definición de clase de `MainActivity`: 

```csharp
public class MainActivity : AppCompatActivity
```

Para establecer la barra de acciones a la versión AppCompat de `Toolbar`, sustituya la llamada a `SetActionBar` con `SetSupportActionBar`. En este ejemplo, el título también se cambia para indicar que la versión de AppCompat de `Toolbar` se está usando:

```csharp
SetSupportActionBar (toolbar);
SupportActionBar.Title = "My AppCompat Toolbar";
```

Por último, cambie el nivel mínimo Android en el valor de círculo previo que consiste en que se admiten (por ejemplo, API 19). 

Compilar la aplicación y ejecutarla en un dispositivo de círculo anterior o el emulador de Android. Captura de pantalla siguiente muestra la versión de AppCompat de **ToolbarFun** en un Nexus 4 ejecutando KitKat (API 19): 

[![Captura de pantalla completa de la aplicación que se ejecuta en un dispositivo KitKat, se muestran dos barras de herramientas](toolbar-compatibility-images/02-running-on-kitkat-sml.png)](toolbar-compatibility-images/02-running-on-kitkat.png#lightbox)

Cuando se utiliza la biblioteca AppCompat, temas no deben cambiarse según la versión de Android &ndash; la biblioteca AppCompat hace posible ofrecer una experiencia de usuario coherente en todas las versiones compatibles de Android. 




## <a name="related-links"></a>Vínculos relacionados

- [Barra de herramientas del círculo (ejemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Barra de herramientas de AppCompat (ejemplo)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
