---
title: Agregar AppCompat y diseño Material
description: En este artículo se explica cómo convertir las aplicaciones existentes de Xamarin.Forms Android para usar AppCompat y Material de diseño.
ms.prod: xamarin
ms.assetid: 045FBCDF-4D45-48BB-9911-BD3938C87D58
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2017
ms.openlocfilehash: c2eed44a7c684b91ceed4493a83ff3b4e1578b5f
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242926"
---
# <a name="adding-appcompat-and-material-design"></a>Agregar AppCompat y diseño Material

_Siga estos pasos para convertir las aplicaciones existentes de Xamarin.Forms Android para usar AppCompat y Material de diseño_

<!-- source https://gist.github.com/jassmith/a3b2a543f99126782936
https://blog.xamarin.com/material-design-for-your-xamarin-forms-android-apps/ -->

## <a name="overview"></a>Información general

Estas instrucciones explica cómo actualizar las aplicaciones de Xamarin.Forms Android existentes para usar la biblioteca AppCompat y habilita el Material de diseño en la versión de Android de las aplicaciones de Xamarin.Forms.

### <a name="1-update-xamarinforms"></a>1. Actualizar Xamarin.Forms

Asegúrese de que la solución está usando Xamarin.Forms 2.0 o posterior. Si es necesario, actualice el paquete Xamarin.Forms Nuget a 2.0.

### <a name="2-check-android-version"></a>2. Comprobar la versión de Android

Asegúrese de que .NET framework de destino del proyecto Android es Android 6.0 (Marshmallow). Compruebe el **proyecto Android > Opciones > compilar > General** configuración garantiza que el marco de trabajo corrent está seleccionado:

 ![](appcompat-images/target-android-6-sml.png "Configuración de compilación de Android General")

### <a name="3-add-new-themes-to-support-material-design"></a>3. Agregar nuevos temas para admitir el Material de diseño

Cree los tres archivos siguientes en el proyecto de Android y pegue el contenido siguiente. Google Proporciona una [Guía de estilo](http://www.google.com/design/spec/style/color.html#color-color-palette) y un [generador de la paleta de colores](http://www.materialpalette.com/) para ayudarle a elegir una combinación de colores alternativos a la especificada.

**Resources/Values/Colors.Xml**

```xml
<resources>
  <color name="primary">#2196F3</color>
  <color name="primaryDark">#1976D2</color>
  <color name="accent">#FFC107</color>
  <color name="window_background">#F5F5F5</color>
</resources>
```

**Resources/Values/Style.Xml**

```xml
<resources>
  <style name="MyTheme" parent="MyTheme.Base">
  </style>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light.NoActionBar">
    <item name="colorPrimary">@color/primary</item>
    <item name="colorPrimaryDark">@color/primaryDark</item>
    <item name="colorAccent">@color/accent</item>
    <item name="android:windowBackground">@color/window_background</item>
    <item name="windowActionModeOverlay">true</item>
  </style>
</resources>
```

Un estilo adicionales debe incluirse en la **v21 valores** carpeta para aplicar propiedades específicas cuando se ejecuta en el círculo Android y versiones más recientes.

**Resources/Values-v21/Style.Xml**

```xml
<resources>
  <style name="MyTheme" parent="MyTheme.Base">
    <!--If you are using MasterDetailPage you will want to set these, else you can leave them out-->
    <!--<item name="android:windowDrawsSystemBarBackgrounds">true</item>
    <item name="android:statusBarColor">@android:color/transparent</item>-->
  </style>
</resources>
```

### <a name="4-update-androidmanifestxml"></a>4. Actualizar AndroidManifest.xml

Para garantizar el nuevo tema información sea utilizado, establezca el tema en el **AndroidManifest** archivo agregando `android:theme="@style/MyTheme"` (deje el resto de XML tal como estaba).

**Properties/AndroidManifest.xml**

```xml
...
<application android:label="AppName" android:icon="@drawable/icon"
  android:theme="@style/MyTheme">
...
```

### <a name="5-provide-toolbar-and-tab-layouts"></a>5. Proporcionar diseños de pestaña y barra de herramientas

Crear **Tabbar.axml** y **Toolbar.axml** archivos en el **recursos/diseño** directorio y péguelo en su contenido desde abajo:

**Resources/layout/Tabbar.axml**

```xml
<android.support.design.widget.TabLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/sliding_tabs"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    app:tabIndicatorColor="@android:color/white"
    app:tabGravity="fill"
    app:tabMode="fixed" />
```

Algunas propiedades para las pestañas se han establecido incluidos la gravedad de la pestaña para `fill` y el modo para `fixed`.
Si tiene una gran cantidad de pestañas puede cambiar esto para desplazable - lea el Android [TabLayout documentación](http://developer.android.com/reference/android/support/design/widget/TabLayout.html) para obtener más información.

**Resources/layout/Toolbar.axml**

```xml
<android.support.v7.widget.Toolbar
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    app:popupTheme="@style/ThemeOverlay.AppCompat.Light"
    app:layout_scrollFlags="scroll|enterAlways" />
```

En estos archivos estamos creando el tema específico de la barra de herramientas que puede variar en la aplicación.
Hacer referencia a la [barra de herramientas Hello](https://blog.xamarin.com/android-tips-hello-toolbar-goodbye-action-bar/) entrada de blog para obtener más información.


### <a name="6-update-the-mainactivity"></a>6. Actualización de la `MainActivity`

En las aplicaciones existentes de Xamarin.Forms el **MainActivity.cs** clase heredará de `FormsApplicationActivity`. Debe sustituirse con `FormsAppCompatActivity` para habilitar la funcionalidad nueva.

**MainActivity.cs**

```csharp
public class MainActivity : FormsAppCompatActivity  // was FormsApplicationActivity
```

Por último, "conectar" los diseños de nuevo del paso 5 en el `OnCreate` método, como se muestra aquí:

```csharp
protected override void OnCreate(Bundle bundle)
{
  // set the layout resources first
  FormsAppCompatActivity.ToolbarResource = Resource.Layout.Toolbar;
  FormsAppCompatActivity.TabLayoutResource = Resource.Layout.Tabbar;

  // then call base.OnCreate and the Xamarin.Forms methods
  base.OnCreate(bundle);
  Forms.Init(this, bundle);
  LoadApplication(new App());
}
```
