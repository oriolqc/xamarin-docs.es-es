---
title: Reemplazar la barra de acciones
ms.prod: xamarin
ms.assetid: 5341D28E-B203-478D-8464-6FAFDC3A4110
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/27/2018
ms.openlocfilehash: 9e9fa1e2651661670f89baac7fcd438b3d14bfb3
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108228"
---
# <a name="replacing-the-action-bar"></a>Reemplazar la barra de acciones

## <a name="overview"></a>Información general

Uno de los más comunes se utiliza para la `Toolbar` es reemplazar la barra de acción predeterminada por una personalizada `Toolbar` (cuando se crea un nuevo proyecto de Android, usa la barra de acción predeterminada). Porque el `Toolbar` proporciona la capacidad para agregar los logotipos de marca, títulos, los elementos de menú, botones de navegación y vistas personalizadas incluso a la sección de la barra de aplicación de una actividad de la interfaz de usuario, ofrece una actualización importante a través de la barra de acción predeterminada.

Para reemplazar la barra de acción predeterminada de una aplicación con un `Toolbar`: 

1.  Crear un nuevo tema personalizado y modificar las propiedades de la aplicación para que utilice este nuevo tema. 

2.  Deshabilitar la `windowActionBar` de atributo en el tema personalizado y habilitar el `windowNoTitle` atributo.

3.  Definir un diseño para el `Toolbar`.

4.  Incluir el `Toolbar` diseño de la actividad **Main.axml** archivo de diseño. 

5.  Agregue código a la actividad `OnCreate` método para localizar el `Toolbar` y llamar a `SetActionBar` para instalar el `ToolBar` como la barra de acciones.

Las siguientes secciones explican con detalle este proceso. Se crea una aplicación sencilla y su barra de acciones se reemplaza con una personalizada `Toolbar`. 



## <a name="start-an-app-project"></a>Iniciar un proyecto de aplicación

Cree un nuevo proyecto de Android denominado **ToolbarFun** (consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) para obtener más información acerca de cómo crear un nuevo proyecto de Android). Después de crear este proyecto, establecer los niveles de API de Android de destino y mínima **Android 5.0 (API nivel 21 - Lollipop)** o una versión posterior. Para obtener más información acerca de los niveles de versión de Android de configuración, consulte [Understanding Android API niveles](~/android/app-fundamentals/android-api-levels.md). Cuando se compila y se ejecute la aplicación, tal como se muestra en esta captura de pantalla muestra la barra de acción predeterminada:

[![Captura de pantalla de la barra de acción predeterminada](replacing-the-action-bar-images/01-before-sml.png)](replacing-the-action-bar-images/01-before.png#lightbox)



## <a name="create-a-custom-theme"></a>Crear un tema personalizado

Abra el **recursos/valores** directory y crear un nuevo archivo denominado **styles.xml**. Reemplace su contenido con el siguiente código XML: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<resources>
  <style name="MyTheme" parent="@android:style/Theme.Material.Light.DarkActionBar">
    <item name="android:windowNoTitle">true</item>
    <item name="android:windowActionBar">false</item>
    <item name="android:colorPrimary">#5A8622</item>
  </style>
</resources>
```

Este XML define un tema personalizado nuevo llamado **mitema** que se basa en el **Theme.Material.Light.DarkActionBar** tema en el círculo. El `windowNoTitle` atributo está establecido en `true` para ocultar la barra de título: 

```xml
<item name="android:windowNoTitle">true</item>
```

Para mostrar la barra de herramientas personalizada, el valor predeterminado `ActionBar` debe estar deshabilitado: 

```xml
<item name="android:windowActionBar">false</item>
```

Un verde aceituna `colorPrimary` configuración se utiliza para el color de fondo de la barra de herramientas: 
 
```xml
<item name="android:colorPrimary">#5A8622</item>
```

## <a name="apply-the-custom-theme"></a>Aplique el tema personalizado

Editar **Properties/Androidmanifest.XML** y agregue la siguiente `android:theme` atributo a la `<application>` elemento para que la aplicación usa el `MyTheme` tema personalizado: 

```xml
<application android:label="@string/app_name" android:theme="@style/MyTheme"></application>
```

Para obtener más información acerca de cómo aplicar un tema personalizado a una aplicación, consulte [temas personalizados utilizando](~/android/user-interface/material-theme.md#customtheme). 



## <a name="define-a-toolbar-layout"></a>Definir un diseño de la barra de herramientas

En el **y diseño de los recursos** directorio, cree un nuevo archivo denominado **toolbar.xml**. Reemplace su contenido con el siguiente código XML: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:minHeight="?android:attr/actionBarSize"
    android:background="?android:attr/colorPrimary"
    android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"/>
```

Este XML define personalizado `Toolbar` que reemplaza la barra de acción predeterminada. El alto mínimo de la `Toolbar` se establece en el tamaño de la barra de acciones que reemplaza: 

```csharp
android:minHeight="?android:attr/actionBarSize"
```

El color de fondo de la `Toolbar` se establece en el color verde aceituna definido anteriormente en **styles.xml**:

```csharp
android:background="?android:attr/colorPrimary"
```

A partir de círculo, el `android:theme` atributo se puede usar para definir el estilo de una vista individual. El `ThemeOverlay.Material` temas introducidos en círculo hacen posible el valor predeterminado de superposición `Theme.Material` temas, sobrescribiendo los atributos pertinentes para que sean clara u oscura. En este ejemplo, el `Toolbar` usa un tema oscuro, por lo que su contenido es un color claro en: 

```csharp
android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
```

Esta configuración se utiliza para que los elementos de menú se compare con el color de fondo más oscuro.



## <a name="include-the-toolbar-layout"></a>Incluyen el diseño de la barra de herramientas

Editar el archivo de diseño **Resources/layout/Main.axml** y reemplace su contenido con el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <include
        android:id="@+id/toolbar"
        layout="@layout/toolbar" />
</RelativeLayout>
```

Este diseño incluye el `Toolbar` definido en **toolbar.xml** y usa un `RelativeLayout` para especificar que el `Toolbar` debe ponerse en la parte superior de la interfaz de usuario (encima del botón). 



## <a name="find-and-activate-the-toolbar"></a>Encontrar y activar la barra de herramientas

Editar **MainActivity.cs** y agregue la siguiente instrucción using:

```csharp
using Android.Views;
```

Además, agregue las siguientes líneas de código al final de la `OnCreate` método:

```csharp
var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
SetActionBar(toolbar);
ActionBar.Title = "My Toolbar";
```

Este código busca el `Toolbar` y llama a `SetActionBar` para que el `Toolbar` tardará en las características de barra de acción predeterminada. El título de la barra de herramientas se cambia a **mi barra**. Tal como se muestra en este ejemplo de código, el `ToolBar` puede hacer referencia directamente como una barra de acciones. Compilar y ejecutar esta aplicación &ndash; personalizada `Toolbar` se muestra en lugar de la barra de acción predeterminada: 

[![Captura de pantalla de la barra de herramientas personalizada con el esquema de color verde](replacing-the-action-bar-images/02-after-sml.png)](replacing-the-action-bar-images/02-after.png#lightbox)

Tenga en cuenta que el `Toolbar` el estilo se realiza independientemente de la `Theme.Material.Light.DarkActionBar` tema que se aplica al resto de la aplicación. 

Si se produce una excepción mientras se ejecuta la aplicación, consulte el [Troubleshooting](#troubleshooting) sección más adelante.

 
## <a name="add-menu-items"></a>Agregar elementos de menú 

En esta sección, los menús se agregan a la `Toolbar`. La parte superior derecha de la `ToolBar` está reservado para los elementos de menú &ndash; cada elemento de menú (también denominado una *elemento de acción*) puede realizar una acción dentro de la actividad actual o puede realizar una acción en nombre de toda la aplicación. 

Para agregar los menús de la `Toolbar`: 

1.  Agregar iconos de menú (si es necesario) a la `mipmap-` carpetas de proyecto de la aplicación. Google Proporciona un conjunto de iconos del menú libre en el [iconos Material](https://design.google.com/icons/) página. 

2.  Definir el contenido de los elementos de menú mediante la adición de un nuevo archivo de recursos de menú en **recursos o menú**. 

3.  Implemente el `OnCreateOptionsMenu` método de la actividad &ndash; este método aumenta los elementos de menú. 

4.  Implemente el `OnOptionsItemSelected` método de la actividad &ndash; este método realiza una acción cuando se pulsa un elemento de menú. 

Las siguientes secciones muestran este proceso en detalle agregando **editar** y **guardar** elementos de menú personalizada `Toolbar`. 



### <a name="install-menu-icons"></a>Instalar los iconos del menú

Continuar con la `ToolbarFun` aplicación de ejemplo, agregar iconos de menú al proyecto de aplicación. Descargar [iconos de barra de herramientas](https://github.com/xamarin/monodroid-samples/blob/master/Supportv7/AppCompat/Toolbar/Resources/toolbar-icons-plus.zip?raw=true), descomprima y copie el contenido de los datos extraídos *mipmap -* carpetas al proyecto *mipmap -* carpetas bajo **ToolbarFun / Recursos** e incluir cada archivo de icono se agregó en el proyecto.


### <a name="define-a-menu-resource"></a>Definir un recurso de menú

Cree un nuevo **menú** subdirectorio bajo **recursos**. En el **menú** subdirectorio, cree un nuevo archivo de recursos de menú llamado **top_menus.xml** y reemplace su contenido con el siguiente código XML: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item
       android:id="@+id/menu_edit"
       android:icon="@mipmap/ic_action_content_create"
       android:showAsAction="ifRoom"
       android:title="Edit" />
  <item
       android:id="@+id/menu_save"
       android:icon="@mipmap/ic_action_content_save"
       android:showAsAction="ifRoom"
       android:title="Save" />
  <item
       android:id="@+id/menu_preferences"
       android:showAsAction="never"
       android:title="Preferences" />
</menu>
```

Este XML crea tres elementos de menú:

-   Un **editar** elemento de menú que usa el `ic_action_content_create.png` icono (un lápiz). 

-   Un **guardar** elemento de menú que usa el `ic_action_content_save.png` icono (un disquete). 

-   Un **preferencias** elemento de menú que no tiene un icono.

El `showAsAction` los atributos de la **editar** y **guardar** elementos de menú se establecen en `ifRoom` &ndash; esta configuración hace que estos elementos de menú que aparezca en el `Toolbar` si hay espacio suficiente para que se muestren. El **preferencias** conjuntos de elementos de menú `showAsAction` a `never` &ndash; Esto hace que el **preferencias** menú aparezca en el *desbordamiento* menú (tres puntos verticales). 


### <a name="implement-oncreateoptionsmenu"></a>Implementar OnCreateOptionsMenu

Agregue el siguiente método a **MainActivity.cs**:

```csharp
public override bool OnCreateOptionsMenu(IMenu menu)
{
    MenuInflater.Inflate(Resource.Menu.top_menus, menu);
    return base.OnCreateOptionsMenu(menu);
}
```

Android llama el `OnCreateOptionsMenu` método para que la aplicación puede especificar el recurso de menú para una actividad. En este método, el **top_menus.xml** se aumenten los recursos en el pasado `menu`. Este código hace que el nuevo **editar**, **guardar**, y **preferencias** elementos de menú que aparecen en la `Toolbar`. 



### <a name="implement-onoptionsitemselected"></a>Implementar OnOptionsItemSelected

Agregue el siguiente método a **MainActivity.cs**:

```csharp
public override bool OnOptionsItemSelected(IMenuItem item)
{
    Toast.MakeText(this, "Action selected: " + item.TitleFormatted,
        ToastLength.Short).Show();
    return base.OnOptionsItemSelected(item);
}
```

Cuando un usuario puntea un elemento de menú, se llama Android el `OnOptionsItemSelected` método y pasa el elemento de menú que se ha seleccionado. En este ejemplo, la implementación solo muestra una notificación del sistema para indicar qué elemento de menú que se ha punteado. 

Compilar y ejecutar `ToolbarFun` para ver los nuevos elementos de menú en la barra de herramientas. El `Toolbar` ahora muestra tres iconos de menú, como se muestra en esta captura de pantalla: 

[![Diagrama que ilustran las ubicaciones de editar, guardar y los elementos de menú de desbordamiento](replacing-the-action-bar-images/04-menu-items-sml.png)](replacing-the-action-bar-images/04-menu-items.png#lightbox)

Cuando un usuario pulsa el **editar** se muestra el elemento de menú, una notificación del sistema para indicar que el `OnOptionsItemSelected` se llamó al método: 

[![Captura de pantalla de aviso mostrado cuando se pulsa Editar elemento](replacing-the-action-bar-images/05-toast-displayed-sml.png)](replacing-the-action-bar-images/05-toast-displayed.png#lightbox)

Cuando un usuario pulsa el menú de desbordamiento, el **preferencias** se muestra el elemento de menú. Normalmente, se deben colocar las acciones de menos comunes en el menú de desbordamiento &ndash; este ejemplo usa el menú de desbordamiento para **preferencias** porque no se utiliza con tanta frecuencia como **editar** y  **Guardar**: 

[![Elemento de menú de la captura de pantalla de preferencias que aparece en el menú de desbordamiento](replacing-the-action-bar-images/06-preferences-sml.png)](replacing-the-action-bar-images/06-preferences.png#lightbox)

Para obtener más información acerca de los menús de Android, consulte el desarrollador Android [menús](https://developer.android.com/guide/topics/ui/menus.html) tema. 
 

## <a name="troubleshooting"></a>Solución de problemas

Las siguientes sugerencias pueden ayudar a depurar los problemas que pueden producirse mientras se reemplaza la barra de acciones con una barra de herramientas.

### <a name="activity-already-has-an-action-bar"></a>Actividad ya tiene una barra de acciones

Si la aplicación no está correctamente configurada para usar un tema personalizado, como se explica en [aplicar el tema personalizado](#apply-the-custom-theme), la siguiente excepción puede producirse mientras se ejecuta la aplicación:

![Error que se puede producir cuando no se usa el tema personalizado](replacing-the-action-bar-images/03-theme-not-defined.png)

Además, un mensaje de error, como es posible que se produce lo siguiente: _Java.Lang.IllegalStateException: esta actividad ya tiene una barra de acciones proporcionada por la decoración de ventana._ 

Para corregir este error, compruebe que la `android:theme` atributo para el tema personalizado se agrega a `<application>` (en **Properties/Androidmanifest.XML**) como se describió anteriormente en [aplicar el tema personalizado](#apply-the-custom-theme). Además, este error puede producirse si el `Toolbar` diseño o en un tema personalizado no está configurado correctamente.


## <a name="related-links"></a>Vínculos relacionados

- [Barra de herramientas del círculo (ejemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Barra de herramientas de AppCompat (ejemplo)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
