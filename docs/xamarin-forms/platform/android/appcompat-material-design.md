---
title: Adición de AppCompat y Material Design
description: En este artículo se explica cómo convertir aplicaciones de Xamarin.Forms Android existentes para usar AppCompat y Material Design.
ms.prod: xamarin
ms.assetid: 045FBCDF-4D45-48BB-9911-BD3938C87D58
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2017
ms.openlocfilehash: cade72aaad60c30993f6b11e98704addd218ffae
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293468"
---
# <a name="adding-appcompat-and-material-design"></a>Adición de AppCompat y Material Design

_Siga estos pasos para convertir aplicaciones de Xamarin.Forms Android existentes para usar AppCompat y Material Design_

<!-- source https://gist.github.com/jassmith/a3b2a543f99126782936
https://blog.xamarin.com/material-design-for-your-xamarin-forms-android-apps/ -->

## <a name="overview"></a>Información general

Estas instrucciones explican cómo actualizar las aplicaciones de Xamarin.Forms Android existentes para usar la biblioteca AppCompat y habilitar el Material de diseño en la versión de Android de sus aplicaciones de Xamarin.Forms.

### <a name="1-update-xamarinforms"></a>1. Actualización de Xamarin.Forms

Asegúrese de que la solución está usando Xamarin.Forms 2.0 o posterior. Si es necesario, actualice el paquete de Xamarin.Forms Nuget a 2.0.

### <a name="2-check-android-version"></a>2. Comprobar la versión de Android

Asegúrese de que .NET framework de destino del proyecto Android es Android 6.0 (Marshmallow). Compruebe el **proyecto Android > Opciones > compilar > General** configuración garantiza que el marco de trabajo corrent está seleccionado:

 ![](appcompat-images/target-android-6-sml.png "Configuración de compilación de Android generales")

### <a name="3-add-new-themes-to-support-material-design"></a>3. Agregar nuevos temas para admitir Material Design

Cree los tres archivos siguientes en el proyecto de Android y pegue el contenido siguiente. Google Proporciona una [Guía de estilo](http://www.google.com/design/spec/style/color.html#color-color-palette) y un [generador de la paleta de colores](http://www.materialpalette.com/) para ayudarle a elegir una combinación de colores alternativos a la especificada.

**Resources/values/colors.xml**

```xml
<resources>
  <color name="primary">#2196F3</color>
  <color name="primaryDark">#1976D2</color>
  <color name="accent">#FFC107</color>
  <color name="window_background">#F5F5F5</color>
</resources>
```

**Resources/values/style.xml**

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

Un estilo adicionales debe incluirse en el **valores v21** carpeta para aplicar propiedades específicas cuando se ejecuta en Android Lollipop y versiones más recientes.

**Resources/values-v21/style.xml**

```xml
<resources>
  <style name="MyTheme" parent="MyTheme.Base">
    <!--If you are using MasterDetailPage you will want to set these, else you can leave them out-->
    <!--<item name="android:windowDrawsSystemBarBackgrounds">true</item>
    <item name="android:statusBarColor">@android:color/transparent</item>-->
  </style>
</resources>
```

### <a name="4-update-androidmanifestxml"></a>4. Update AndroidManifest.xml

Para garantizar este nuevo tema información sea utilizado, establezca el tema en el **AndroidManifest** archivo agregando `android:theme="@style/MyTheme"` (deje el resto del código XML tal como estaba).

**Properties/AndroidManifest.xml**

```xml
...
<application android:label="AppName" android:icon="@drawable/icon"
  android:theme="@style/MyTheme">
...
```

### <a name="5-provide-toolbar-and-tab-layouts"></a>5. Proporcionar diseños de pestaña y la barra de herramientas

Crear **Tabbar.axml** y **Toolbar.axml** archivos en el **y diseño de los recursos** directorio y péguelo en su contenido desde abajo:

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

Algunas propiedades de las pestañas se han establecido como la gravedad de la ficha `fill` y modo a `fixed`.
Si tiene muchas pestañas desea cambiarlo a desplazable - lea el Android [TabLayout documentación](https://developer.android.com/reference/android/support/design/widget/TabLayout.html) para obtener más información.

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

En estos archivos estamos creando un tema específico de la barra de herramientas que puede variar para la aplicación.
Hacer referencia a la [barra de herramientas Hello](https://blog.xamarin.com/android-tips-hello-toolbar-goodbye-action-bar/) del blog para obtener más información.


### <a name="6-update-the-mainactivity"></a>6. Actualización de la `MainActivity`

En las aplicaciones existentes de Xamarin.Forms el **MainActivity.cs** clase heredará de `FormsApplicationActivity`. Esto se debe reemplazar con `FormsAppCompatActivity` para habilitar la funcionalidad nueva.

**MainActivity.cs**

```csharp
public class MainActivity : FormsAppCompatActivity  // was FormsApplicationActivity
```

Por último, "conectar" los diseños del nuevo del paso 5 en el `OnCreate` método, como se muestra aquí:

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
