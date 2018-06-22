---
title: Tema de materiales
description: Cómo crear un tema su aplicación Xamarin.Android con Material de tema
ms.prod: xamarin
ms.assetid: DC4CDBD0-3DF9-4B7E-B876-29128985E2A7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: a3b5f908330833a38aad9e329835a4a437fc29f0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30771325"
---
# <a name="material-theme"></a>Tema de materiales

*Tema material* es un estilo de la interfaz de usuario que determina la apariencia y funcionamiento de vistas y actividades a partir de Android 5.0 (círculo). Tema material está integrado en Android 5.0, por lo que se utiliza la interfaz de usuario del sistema, así como las aplicaciones. Tema de material no es un "tema" en el sentido de una opción de apariencia de todo el sistema que un usuario puede elegir dinámicamente desde un menú de configuración. En su lugar, el tema de Material puede considerarse como un conjunto de relacionados estilos base integrados que puede usar para personalizar la apariencia y funcionamiento de la aplicación.

Android proporciona tres tipos de Material tema:

-  `Theme.Material` &ndash; Versión oscuro del tema de Material; Este es el tipo predeterminado en Android 5.0.

-  `Theme.Material.Light` &ndash; Versión "Light" del tema de Material.

-  `Theme.Material.Light.DarkActionBar` &ndash; Versión "Light" del tema de Material, pero con una barra de acción oscuro.

Aquí se muestran algunos ejemplos de estos tipos de Material tema:

[![Capturas de pantalla de ejemplo del tema oscuro, el tema claro y tema oscuro barra de acciones](material-theme-images/three-flavors-sml.png)](material-theme-images/three-flavors.png#lightbox)

Puede derivar de tema de Material para crear su propio tema, reemplazar algunos o todos los atributos de color. Por ejemplo, puede crear un tema que se deriva de `Theme.Material.Light`, pero reemplaza el color de la barra de aplicación para que coincida con el color de la marca. También puede aplicar estilo vistas individuales; Por ejemplo, puede crear un estilo para [CardView](~/android/user-interface/controls/card-view.md) que tiene más esquinas redondeadas y utiliza un color de fondo más oscuro.

Puede utilizar un tema único para toda una aplicación, o puede usar distintos temas para las diferentes pantallas (actividades) de una aplicación. En las capturas de pantalla anterior, por ejemplo, una única aplicación usa un tema diferente para cada actividad para mostrar las combinaciones de colores integrada. Botones de radio cambie la aplicación a las distintas actividades y, como resultado, mostrar temas diferentes.

Dado que el tema de Material se admite únicamente en Android 5.0 y versiones posteriores, no puede utilizar (o a un tema personalizado derivado de tema de Material) al tema la aplicación para ejecutar en versiones anteriores de Android. Sin embargo, puede configurar la aplicación para utilizar el tema de Material en dispositivos Android 5.0 y correctamente revertir a un tema anterior cuando se ejecuta en las versiones anteriores de Android (vea la [compatibilidad](#compatibility) sección de este artículo para obtener más información).


## <a name="requirements"></a>Requisitos

Para usar las nuevas características de tema de Material de Android 5.0 en aplicaciones basadas en Xamarin, es necesario lo siguiente:

-  **Xamarin.Android** &ndash; Xamarin.Android 4.20 o posterior debe instalarse y configurarse con Visual Studio o Visual Studio para Mac. 

-  **SDK de Android** &ndash; Android 5.0 (API de 21) o posterior debe estar instalado mediante el Administrador de SDK de Android.

-  **Java JDK 1.8** &ndash; JDK 1.7 puede usarse si se está específicamente como destino la API de nivel 23 y versiones anterior. 1.8 de JDK está disponible en [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Para obtener información sobre cómo configurar un proyecto de aplicación de Android 5.0, consulte [configuración de un proyecto de Android 5.0](~/android/platform/lollipop.md).


## <a name="using-the-built-in-themes"></a>Uso de los temas integrados

Es la manera más fácil de utilizar Material de tema configurar la aplicación para que utilice un tema integrado sin personalización. Si no desea configurar explícitamente un tema, la aplicación predeterminada será `Theme.Material` (el tema oscuro). Si su aplicación tiene sólo una actividad, puede configurar un tema en el nivel de aplicación. Si la aplicación tiene varias actividades, puede configurar un tema en el nivel de aplicación para que usa el mismo tema en todas las actividades, o puede asignar distintos temas a las distintas actividades. En las siguientes secciones se explican cómo configurar los temas en el nivel de aplicación y en el nivel de actividad.


### <a name="theming-an-application"></a>Una aplicación de temas

Para configurar una aplicación completa para usar un tipo de tema de Material, establezca el `android:theme` atributo del nodo de aplicación en **AndroidManifest.xml** a uno de los siguientes:

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

Cuando se establece el tema de la aplicación en `@android:style/Theme.Material.Light`, todas las actividades en *MyApp* se mostrará mediante `Theme.Material.Light`.


### <a name="theming-an-activity"></a>Una actividad de temas

Tema una actividad, agregue un `Theme` si se establece en el `[Activity]` atributo por encima de la declaración de actividad y asignar `Theme` para el tipo de tema de Material que va a utilizar. Los siguientes temas de ejemplo una actividad con `Theme.Material.Light`:

```C#
[Activity(Theme = "@android:style/Theme.Material.Light",
          Label = "MyApp", MainLauncher = true, Icon = "@drawable/icon")]  
```

Otras actividades en esta aplicación usará el valor predeterminado `Theme.Material` combinación de colores oscuro (o, si ha configurado, la configuración de tema de la aplicación).

<a name="customtheme" />

## <a name="using-custom-themes"></a>Uso de temas personalizados

Puede mejorar su marca mediante la creación de un tema personalizado estilos su aplicación con su marca&rsquo;colores s. Para crear un tema personalizado, defina un nuevo estilo que se deriva de un tipo de tema de Material integrado, reemplazando los atributos de color que desea cambiar. Por ejemplo, puede definir un tema personalizado que deriva de `Theme.Material.Light.DarkActionBar` y cambia el color de fondo de pantalla en beige en lugar de blanco.

Tema material expone los siguientes atributos de diseño para la personalización:

-  `colorPrimary` &ndash; El color de la barra de la aplicación.

-  `colorPrimaryDark` &ndash; El color de la barra de estado y barras de la aplicación contextual; Esto normalmente es una versión oscura de `colorPrimary`.

-  `colorAccent` &ndash; El color de los controles de interfaz de usuario, como casillas y botones de radio, cuadros de texto de edición.

-  `windowBackground` &ndash; El color de fondo de pantalla.

-  `textColorPrimary` &ndash; El color del texto de la interfaz de usuario en la barra de la aplicación.

-  `statusBarColor` &ndash; El color de la barra de estado.

-  `navigationBarColor` &ndash; El color de la barra de navegación.

Estas áreas de la pantalla se etiquetan en el diagrama siguiente:

[![Diagrama de atributos y sus áreas de la pantalla asociada](material-theme-images/screen-attributes-sml.png)](material-theme-images/screen-attributes.png#lightbox)

De forma predeterminada, `statusBarColor` se establece en el valor de `colorPrimaryDark`. Puede establecer `statusBarColor` en un color sólido, o puede establecerlo en `@android:color/transparent` para que la barra de estado sea transparente. La barra de navegación pueden hacer transparente estableciendo `navigationBarColor` a `@android:color/transparent`.


### <a name="creating-a-custom-app-theme"></a>Crear un tema de la aplicación personalizada

Puede crear un tema de la aplicación personalizada mediante la creación y modificación de los archivos en el **recursos** carpeta de su proyecto de aplicación. Para aplicar estilo a la aplicación con un tema personalizado, siga estos pasos:

-   Crear un **colors.xml** en el archivo **recursos/valores** &mdash; utilizar este archivo para definir los colores de tema personalizado. Por ejemplo, puede pegar el código siguiente en **colors.xml** que le ayudarán a empezar a trabajar:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <color name="my_blue">#3498DB</color>
    <color name="my_green">#77D065</color>
    <color name="my_purple">#B455B6</color>
    <color name="my_gray">#738182</color>
</resources>
```

-   Modificar este archivo de ejemplo para definir los nombres y códigos de color para los recursos de color que va a utilizar en el tema personalizado.

-   Crear un **recursos/valores-v21** carpeta. En esta carpeta, cree un **styles.xml** archivo:

    [![Ubicación de styles.xml en la carpeta de recursos/valores-21.xml](material-theme-images/values-v21-sml.png)](material-theme-images/values-v21.png#lightbox)

    Tenga en cuenta que **recursos/valores-v21** es específico de Android 5.0 &ndash; versiones anteriores de Android no leerá archivos en esta carpeta.

-   Agregar un `resources` nodo **styles.xml** y definir una `style` nodo con el nombre del tema personalizado. Por ejemplo, mostramos un **styles.xml** archivo que define *MyCustomTheme* (derivado de los integrados `Theme.Material.Light` estilo de tema):

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Customizations go here -->
    </style>
</resources>
```

-   En este punto, una aplicación que utiliza *MyCustomTheme* mostrará las existencias `Theme.Material.Light` tema sin personalizaciones:

    [![Apariencia de tema personalizado antes de las personalizaciones](material-theme-images/custom-theme-before-sml.png)](material-theme-images/custom-theme-before.png#lightbox)

-   Agregar personalizaciones de color a **styles.xml** , defina los colores de los atributos de diseño que desea cambiar. Por ejemplo, para cambiar el color de la barra de aplicación para `my_blue` y cambiar el color de los controles de interfaz de usuario `my_purple`, agregar el color de las invalidaciones **styles.xml** que hacen referencia a los recursos de color configurados en **colors.xml**:

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

[![Apariencia de tema personalizado después de las personalizaciones](material-theme-images/custom-theme-after-sml.png)](material-theme-images/custom-theme-after.png#lightbox)

En este ejemplo, *MyCustomTheme* toma prestado colores de `Theme.Material.Light` para el fondo del color, barra de estado y los colores de texto, pero se cambia el color de la barra de aplicación para `my_blue` y establece el color del botón de radio a `my_purple`.

<a name="customview" />

### <a name="creating-a-custom-view-style"></a>Crear un estilo de vista personalizada

Android 5.0 también hace posible para que pueda aplicar estilo a una vista individual. Después de crear **colors.xml** y **styles.xml** (tal y como se describe en la sección anterior), puede agregar un estilo de vista para **styles.xml**.
Para aplicar estilo a una vista individual, siga estos pasos:

-   Editar **Resources/values-v21/styles.xml** y agregue un `style` nodo con el nombre de su estilo de vista personalizada. Establecer los atributos de color personalizado para la vista dentro de este `style` nodo. Por ejemplo, para crear un personalizado [CardView](~/android/user-interface/controls/card-view.md) estilo que tiene más esquinas redondeadas y utiliza `my_blue` como el color de fondo de la tarjeta, agregue un `style` nodo **styles.xml** (dentro de la `resources`nodo) y configurar el radio de esquina y de color de fondo:

```xml
<!-- Theme an individual view: -->
<style name="CardView.MyBlue">

    <!-- Change the background color to Xamarin blue: -->
    <item name="cardBackgroundColor">@color/my_blue</item>

    <!-- Make the corners very round: -->
    <item name="cardCornerRadius">18dp</item>
</style>
```

-   En el diseño, establezca la `style` atributo para esa vista para que coincida con el nombre de estilo personalizado que eligió en el paso anterior. Por ejemplo:

```xml
<android.support.v7.widget.CardView
    style="@style/CardView.MyBlue"
    android:layout_width="200dp"
    android:layout_height="100dp"
    android:layout_gravity="center_horizontal">
```

Captura de pantalla siguiente muestra un ejemplo del valor predeterminado `CardView` (se muestra a la izquierda) en comparación con un `CardView` que se ha aplicado el estilo con personalizado `CardView.MyBlue` tema (se muestra a la derecha):

[![Ejemplos de CardView personalizados y predeterminados CardView](material-theme-images/custom-cardview-sml.png)](material-theme-images/custom-cardview.png#lightbox)

En este ejemplo, la opción de instalación `CardView` se muestra con el color de fondo `my_blue` y un radio de redondeo 18dp.


## <a name="compatibility"></a>Compatibilidad

Para aplicar estilo a la aplicación para que se usa el tema de Material en Android 5.0, pero se revierte automáticamente a un estilo hacia abajo compatible en las versiones anteriores de Android, siga estos pasos:

-   Definir un tema personalizado en **Resources/values-v21/styles.xml** que se deriva de un estilo de tema de Material. Por ejemplo:

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

-   Definir un tema personalizado en **Resources/values/styles.xml** que se deriva de un tema anterior, pero utiliza el mismo nombre del tema anterior. Por ejemplo:

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

Si el tema utiliza colores definidos en un **colors.xml** de archivos, asegúrese de colocar este archivo en **recursos/valores** (en lugar de **recursos/valores-v21**) para que ambas versiones de el tema personalizado puede tener acceso a las definiciones de color.

Cuando la aplicación se ejecuta en un dispositivo Android 5.0, usará la definición del tema especificada en **Resources/values-v21/styles.xml**. Cuando esta aplicación se ejecuta en dispositivos Android anteriores, automáticamente recurra a la definición del tema especificada en **Resources/values/styles.xml**.

Para obtener más información sobre la compatibilidad de tema con las versiones anteriores de Android, consulte [recursos alternativos](~/android/app-fundamentals/resources-in-android/alternate-resources.md).

## <a name="summary"></a>Resumen

En este artículo se introdujo el nuevo estilo de interfaz de usuario de tema de Material incluido en Android 5.0 (círculo). Describen los tres tipos de Material tema integrados que puede usar para diseñar la aplicación, ha explicado cómo crear un tema personalizado para la aplicación de personalización de marca y proporciona un ejemplo de cómo crear un tema de una vista individual. Por último, en este artículo se explica cómo utilizar el tema de Material en la aplicación mientras se mantiene la compatibilidad con versiones anteriores de Android de forma descendente.



## <a name="related-links"></a>Vínculos relacionados

- [ThemeSwitcher (ejemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/ThemeSwitcher)
- [Introducción a círculo](~/android/platform/lollipop.md)
- [CardView](~/android/user-interface/controls/card-view.md)
- [Recursos alternativos](~/android/app-fundamentals/resources-in-android/alternate-resources.md)
- [Vista previa de desarrollador de Android L](http://developer.android.com/preview/index.html)
- [Diseño de materiales](http://developer.android.com/preview/material/index.html)
- [Principios de diseño de materiales](http://static.googleusercontent.com/media/www.google.com/en/us/design/material-design.pdf)
- [Mantiene la compatibilidad con](http://developer.android.com/preview/material/compatibility.html)
