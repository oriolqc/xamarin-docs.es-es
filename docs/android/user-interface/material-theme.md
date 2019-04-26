---
title: Tema de materiales
description: Cómo crear un tema con el tema de Material de la aplicación de Xamarin.Android
ms.prod: xamarin
ms.assetid: DC4CDBD0-3DF9-4B7E-B876-29128985E2A7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 512775864f5ad55ddfedd53b83dd02d7b0e1d1f8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61309136"
---
# <a name="material-theme"></a>Tema de materiales

*Tema de materiales* es un estilo de la interfaz de usuario que determina la apariencia de las vistas y actividades a partir de Android 5.0 (Lollipop). Tema de materiales está integrado en Android 5.0, por lo que se usa la interfaz de usuario del sistema, así como las aplicaciones. Tema de materiales no es un "tema" en el sentido de una opción de la apariencia de todo el sistema que un usuario puede elegir dinámicamente desde un menú de configuración. En su lugar, Material de tema puede considerarse como un conjunto de relacionados estilos bases integrados que puede usar para personalizar la apariencia de la aplicación.

Android proporciona tres tipos de Material tema:

-  `Theme.Material` &ndash; Versión oscuro del tema de Material; Este es el tipo predeterminado en Android 5.0.

-  `Theme.Material.Light` &ndash; Versión ligera de Material de tema.

-  `Theme.Material.Light.DarkActionBar` &ndash; Versión ligera de tema de Material, pero con una barra de acción oscuro.

Aquí se muestran algunos ejemplos de estos tipos de Material tema:

[![Capturas de pantalla de ejemplo del tema oscuro, claro y tema oscuro barra de acciones](material-theme-images/three-flavors-sml.png)](material-theme-images/three-flavors.png#lightbox)

Puede derivar del Material de tema para crear su propio tema, reemplazar algunos o todos los atributos de color. Por ejemplo, puede crear un tema que se deriva de `Theme.Material.Light`, pero reemplaza el color de la barra de aplicación para que coincida con el color de su marca. También puede aplicar estilos a las vistas individuales; Por ejemplo, puede crear un estilo para [CardView](~/android/user-interface/controls/card-view.md) que más tiene esquinas redondeadas y utiliza un color de fondo más oscuro.

Puede usar un tema único para toda una aplicación, o puede usar los diferentes temas para diferentes pantallas (actividades) en una aplicación. En las capturas de pantalla anterior, por ejemplo, una sola aplicación usa un tema diferente para cada actividad para mostrar las combinaciones de colores integrada. Botones de radio cambie la aplicación a diferentes actividades y, como resultado, muestran los diferentes temas.

Como Material de tema es compatible solo en Android 5.0 y versiones posteriores, no puede usar (o a un tema personalizado que deriva el Material de tema) al tema de la aplicación para que se ejecutan en versiones anteriores de Android. Sin embargo, puede configurar la aplicación para utilizar el tema de Material en los dispositivos Android 5.0 y correctamente recurrir a un tema anterior cuando se ejecuta en versiones anteriores de Android (consulte la [compatibilidad](#compatibility) sección de este artículo para obtener más información).


## <a name="requirements"></a>Requisitos

Para usar las nuevas características de tema de materiales de Android 5.0 en aplicaciones basadas en Xamarin, se requiere lo siguiente:

-  **Xamarin.Android** &ndash; Xamarin.Android 4.20 o posterior debe estar instalado y configurado con Visual Studio o Visual Studio para Mac. 

-  **SDK de Android** &ndash; Android 5.0 (API 21) o posterior debe estar instalado a través de Android SDK Manager.

-  **Java JDK 1.8** &ndash; JDK 1.7 puede usarse si se está específicamente dirigidos a API nivel 23 y versiones anteriores. Está disponible en JDK 1.8 [Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Para obtener información sobre cómo configurar un proyecto de aplicación de Android 5.0, consulte [configuración de un proyecto de Android 5.0](~/android/platform/lollipop.md).


## <a name="using-the-built-in-themes"></a>Uso de los temas integrados

Es la manera más fácil de usar Material de tema configurar la aplicación para usar un tema integrado sin personalización. Si no desea configurar explícitamente un tema, la aplicación predeterminada será `Theme.Material` (el tema oscuro). Si la aplicación tiene sólo una actividad, puede configurar un tema en el nivel de actividad. Si la aplicación tiene varias actividades, puede configurar un tema en el nivel de aplicación para que usa el mismo tema en todas las actividades, o puede asignar diferentes temas para actividades diferentes. Las secciones siguientes explican cómo configurar los temas en el nivel de aplicación y en el nivel de actividad.


### <a name="theming-an-application"></a>Una aplicación de creación de temas

Para configurar una aplicación completa para usar un tipo de tema de Material, establezca el `android:theme` atributo del nodo de la aplicación en **AndroidManifest.xml** a uno de los siguientes:

-  `@android:style/Theme.Material` &ndash; Tema oscuro.

-  `@android:style/Theme.Material.Light` &ndash; Tema claro.

-  `@android:style/Theme.Material.Light.DarkActionBar` &ndash; Tema claro con la barra de acciones oscuro.

En el ejemplo siguiente se configura la aplicación *MyApp* para utilizar el tema claro:

```xml
<application android:label="MyApp" 
             android:theme="@android:style/Theme.Material.Light">
</application>
```

Como alternativa, puede establecer la aplicación `Theme` atributo **AssemblyInfo.cs** (o **Properties.cs**). Por ejemplo:

```C#
[assembly: Application(Theme="@android:style/Theme.Material.Light")]
```

Cuando se establece el tema de la aplicación en `@android:style/Theme.Material.Light`, todas las actividades en *MyApp* se mostrará usando `Theme.Material.Light`.


### <a name="theming-an-activity"></a>Una actividad de creación de temas

Tema una actividad, agrega un `Theme` si se establece en el `[Activity]` atributo por encima de la declaración de la actividad y asignar `Theme` para el tipo de tema de Material que desee usar. Los siguientes temas de ejemplo una actividad con `Theme.Material.Light`:

```C#
[Activity(Theme = "@android:style/Theme.Material.Light",
          Label = "MyApp", MainLauncher = true, Icon = "@drawable/icon")]  
```

Otras actividades en esta aplicación usará el valor predeterminado `Theme.Material` combinación de colores oscuros (o, si ha configurado, la configuración de tema de la aplicación).

<a name="customtheme" />

## <a name="using-custom-themes"></a>Uso de temas personalizados

Puede mejorar su marca mediante la creación de un tema personalizado que diseña la aplicación con su marca&rsquo;colores s. Para crear un tema personalizado, defina un nuevo estilo que se deriva de un tipo de tema de Material integrado, reemplazando los atributos de color que desee cambiar. Por ejemplo, puede definir un tema personalizado que se derive de `Theme.Material.Light.DarkActionBar` y cambia el color de fondo de pantalla para beige en lugar de en blanco.

Tema de materiales expone los siguientes atributos de diseño para la personalización:

-  `colorPrimary` &ndash; El color de la barra de la aplicación.

-  `colorPrimaryDark` &ndash; El color de la barra de estado y las barras de la aplicación contextuales; Esto normalmente es una versión oscura de `colorPrimary`.

-  `colorAccent` &ndash; El color de los controles de interfaz de usuario, como cuadros de edición de texto, botones de radio y casillas de verificación.

-  `windowBackground` &ndash; El color de fondo de la pantalla.

-  `textColorPrimary` &ndash; El color del texto de la interfaz de usuario en la barra de la aplicación.

-  `statusBarColor` &ndash; El color de la barra de estado.

-  `navigationBarColor` &ndash; El color de la barra de navegación.

Estas áreas de la pantalla se etiquetan en el diagrama siguiente:

[![Diagrama de atributos y sus áreas de la pantalla asociada](material-theme-images/screen-attributes-sml.png)](material-theme-images/screen-attributes.png#lightbox)

De forma predeterminada, `statusBarColor` se establece en el valor de `colorPrimaryDark`. Puede establecer `statusBarColor` a un color sólido, o puede establecerlo en `@android:color/transparent` a hacer transparente la barra de estado. La barra de navegación también se pueden hacer transparente estableciendo `navigationBarColor` a `@android:color/transparent`.


### <a name="creating-a-custom-app-theme"></a>Creación de un tema de la aplicación personalizada

Puede crear un tema de la aplicación personalizada mediante la creación y modificación de los archivos en el **recursos** carpeta del proyecto de aplicación. Para aplicar estilo a la aplicación con un tema personalizado, siga estos pasos:

-   Crear un **colors.xml** archivo **recursos/valores** &mdash; usar este archivo para definir los colores del tema personalizado. Por ejemplo, puede pegar el código siguiente en **colors.xml** que le ayudarán a empezar a trabajar:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <color name="my_blue">#3498DB</color>
    <color name="my_green">#77D065</color>
    <color name="my_purple">#B455B6</color>
    <color name="my_gray">#738182</color>
</resources>
```

-   Modifique este archivo de ejemplo para definir los nombres y códigos de color para los recursos de color que va a utilizar en el tema personalizado.

-   Crear un **recursos, los valores-v21** carpeta. En esta carpeta, cree un **styles.xml** archivo:

    [![Ubicación de styles.xml en la carpeta de recursos, los valores-21.xml](material-theme-images/values-v21-sml.png)](material-theme-images/values-v21.png#lightbox)

    Tenga en cuenta que **recursos, los valores-v21** es específico de Android 5.0 &ndash; versiones anteriores de Android no leerá archivos en esta carpeta.

-   Agregar un `resources` nodo **styles.xml** y definir un `style` nodo con el nombre del tema personalizado. Por ejemplo, este es un **styles.xml** archivo que define *MyCustomTheme* (derivado de integrado `Theme.Material.Light` estilo de tema):

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Customizations go here -->
    </style>
</resources>
```

-   En este momento, una aplicación que utiliza *MyCustomTheme* mostrará las existencias `Theme.Material.Light` tema sin personalizaciones:

    [![Apariencia del tema personalizado antes de las personalizaciones](material-theme-images/custom-theme-before-sml.png)](material-theme-images/custom-theme-before.png#lightbox)

-   Agregar personalizaciones del color para **styles.xml** definiendo los colores de los atributos de diseño que desee cambiar. Por ejemplo, para cambiar el color de la barra de aplicación para `my_blue` y cambiar el color de los controles de interfaz de usuario a `my_purple`, agregar las invalidaciones de color para **styles.xml** que hacen referencia a recursos de color configurados en **colors.xml**:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Override the app bar color -->
        <item name="android:colorPrimary">@color/my_blue</item>
        <!-- Override the color of UI controls -->
        <item name="android:colorAccent">@color/my_purple</item>
    </style>
</resources>
```

Con estos cambios en su lugar, una aplicación que usa *MyCustomTheme* mostrará un color de la barra de aplicación en `my_blue` y controles de IU en `my_purple`, pero usar el `Theme.Material.Light` combinación de colores en los demás lugares:

[![Apariencia del tema personalizado después de las personalizaciones](material-theme-images/custom-theme-after-sml.png)](material-theme-images/custom-theme-after.png#lightbox)

En este ejemplo, *MyCustomTheme* adopta los colores de `Theme.Material.Light` para el fondo de color, barra de estado y los colores de texto, pero se cambia el color de la barra de aplicaciones `my_blue` y establece el color del botón de radio para `my_purple`.

<a name="customview" />

### <a name="creating-a-custom-view-style"></a>Creación de un estilo de vista personalizada

Android 5.0 también hace posible para que pueda aplicar estilo a una vista individual. Después de crear **colors.xml** y **styles.xml** (como se describe en la sección anterior), puede agregar un estilo de vista **styles.xml**.
Para aplicar estilo a una vista individual, siga estos pasos:

-   Editar **Resources/values-v21/styles.xml** y agregue un `style` nodo con el nombre del estilo de vista personalizada. Establezca los atributos de color personalizado para la vista dentro de este `style` nodo. Por ejemplo, para crear un personalizado [CardView](~/android/user-interface/controls/card-view.md) estilo que más tiene esquinas redondeadas y usos `my_blue` como el color de fondo de la tarjeta, agregue un `style` nodo **styles.xml** (dentro de la `resources`nodo) y configurar el radio de rincón y color de fondo:

```xml
<!-- Theme an individual view: -->
<style name="CardView.MyBlue">

    <!-- Change the background color to Xamarin blue: -->
    <item name="cardBackgroundColor">@color/my_blue</item>

    <!-- Make the corners very round: -->
    <item name="cardCornerRadius">18dp</item>
</style>
```

-   En el diseño, establezca el `style` atributo para esa vista para que coincida con el nombre de estilo personalizado que eligió en el paso anterior. Por ejemplo:

```xml
<android.support.v7.widget.CardView
    style="@style/CardView.MyBlue"
    android:layout_width="200dp"
    android:layout_height="100dp"
    android:layout_gravity="center_horizontal">
```

Captura de pantalla siguiente proporciona un ejemplo del valor predeterminado `CardView` (se muestra en la parte izquierda) en comparación con un `CardView` que se ha aplicado el estilo con personalizado `CardView.MyBlue` tema (se muestra en la parte derecha):

[![Ejemplos de predeterminado CardView y CardView personalizado](material-theme-images/custom-cardview-sml.png)](material-theme-images/custom-cardview.png#lightbox)

En este ejemplo, personalizado `CardView` se muestra con el color de fondo `my_blue` y un radio de redondeo 18dp.


## <a name="compatibility"></a>Compatibilidad

Para aplicar estilo a la aplicación para que se utiliza el tema de Material en Android 5.0, pero se revierte automáticamente a un estilo hacia abajo compatible en versiones anteriores de Android, use los pasos siguientes:

-   Definir un tema personalizado en **Resources/values-v21/styles.xml** que se deriva de un estilo de tema del Material. Por ejemplo:

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

-   Definir un tema personalizado en **Resources/values/styles.xml** que deriva de un tema anterior, pero usa el mismo nombre del tema anterior. Por ejemplo:

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Holo.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

-   En **AndroidManifest.xml**, configure la aplicación con el nombre de tema personalizado. 
    Por ejemplo:

```xml
<application android:label="MyApp" 
             android:theme="@style/MyCustomTheme">
</application>
```

-   Como alternativa, puedes aplicar estilo a una actividad específica mediante su tema personalizado:

```C#
[Activity(Label = "MyActivity", Theme = "@style/MyCustomTheme")]
```

Si el tema que usa colores definidos en un **colors.xml** de archivos, asegúrese de colocar este archivo en **recursos/valores** (en lugar de **recursos, los valores-v21**) para que ambas versiones de el tema personalizado puede tener acceso a las definiciones de color.

Cuando la aplicación se ejecuta en un dispositivo Android 5.0, usará la definición del tema especificada en **Resources/values-v21/styles.xml**. Cuando esta aplicación se ejecuta en dispositivos Android más antiguos, automáticamente volverá a la definición del tema especificada en **Resources/values/styles.xml**.

Para obtener más información sobre la compatibilidad con versiones anteriores de Android del tema, consulte [recursos alternativos](~/android/app-fundamentals/resources-in-android/alternate-resources.md).

## <a name="summary"></a>Resumen

Este artículo presenta el nuevo estilo de interfaz de usuario de tema de Material incluido en Android 5.0 (Lollipop). Describen las tres variantes de Material tema integradas que puede usar para diseñar la aplicación, se ha explicado cómo crear un tema personalizado para la personalización de marca de la aplicación y proporciona un ejemplo de cómo crear un tema una vista individual. Por último, en este artículo se explica cómo utilizar el tema de Material en la aplicación al tiempo que mantiene la compatibilidad con versiones anteriores de Android de forma descendente.



## <a name="related-links"></a>Vínculos relacionados

- [ThemeSwitcher (ejemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/ThemeSwitcher)
- [Introducción al círculo](../platform/lollipop.md)
- [CardView](controls/card-view.md)
- [Recursos alternativos](../app-fundamentals/resources-in-android/alternate-resources.md)
- [Android Lollipop](https://developer.android.com/about/versions/lollipop)
- [Para desarrolladores de Android circular](https://developer.android.com/about/versions/pie/)
- [Diseño de materiales](https://developer.android.com/guide/topics/ui/look-and-feel/)
- [Principios de diseño de materiales](https://material.io/design/)
- [Mantener la compatibilidad](https://developer.android.com/training/backward-compatible-ui/)
