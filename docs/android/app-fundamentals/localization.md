---
title: Localización de Android
description: Este documento presentan las características de localización de Android SDK y cómo tener acceso a ellos con Xamarin.
ms.prod: xamarin
ms.assetid: D1277939-A1E8-468E-B136-820D816AF853
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 076cadd16c3953ee4e06193190b59035ad57f2c1
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33798610"
---
# <a name="android-localization"></a>Localización de Android

_Este documento presentan las características de localización de Android SDK y cómo tener acceso a ellos con Xamarin._

## <a name="android-platform-features"></a>Características de la plataforma Android

Esta sección describen las características principales de localización de Android. Vaya a la [próxima sección](#basics) para ver ejemplos y código específico.

### <a name="locale"></a>Configuración regional

Los usuarios elegir su idioma en **configuración > Language & entrada**. Esta selección controla el idioma que se muestra y la configuración regional utiliza (p. ej. para dar formato a números y fecha).

La configuración regional actual puede consultarse mediante el contexto actual `Resources`:

```csharp
var lang = Resources.Configuration.Locale; // eg. "es_ES"
```

Este valor será un identificador de configuración regional que contiene un código de idioma y un código de configuración regional, separados por un carácter de subrayado. Como referencia, mostramos un [lista de configuraciones regionales de Java](http://www.oracle.com/technetwork/java/javase/locales-137662.html) y [configuraciones regionales admite Android a través de StackOverflow](http://stackoverflow.com/questions/7973023/what-is-the-list-of-supported-languages-locales-on-android).

Estos son algunos ejemplos comunes:

* `en_US` para inglés (Reino Statees)
* `es_ES` para el idioma español (España)
* `ja_JP` para japonés (Japón)
* `zh_CN` para chino (China)
* `zh_TW` para chino (Taiwán)
* `pt_PT` para portugués (Portugal)
* `pt_BR` para portugués (Brasil)

### <a name="localechanged"></a>LOCALE_CHANGED

Android genera `android.intent.action.LOCALE_CHANGED` cuando el usuario cambie su selección de idioma.

Pueden elegir actividades para controlar esto estableciendo el `android:configChanges` atributo en la actividad, como el siguiente:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
        ConfigurationChanges = ConfigChanges.Locale | ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

<a name="basics" />

## <a name="internationalization-basics-in-android"></a>Conceptos básicos de internacionalización en Android

Estrategia de localización de Android tiene los siguientes componentes claves:

- Carpetas de recursos para que contenga las cadenas localizadas, imágenes y otros recursos.

- `GetText` método, que se utiliza para recuperar las cadenas adaptadas en código

- `@string/id` en los archivos AXML, para colocar automáticamente las cadenas localizadas en los diseños.

### <a name="resource-folders"></a>Carpetas de recursos

Aplicaciones Android administran el contenido de la mayoría de carpetas de recursos, como:

* **diseño** -contiene archivos de diseño AXML.
* **puede dibujar** -contiene imágenes y otros recursos con estas características.
* **valores** -contiene cadenas.
* **sin formato** -contiene archivos de datos.

Ya está familiarizada con el uso de la mayoría de los desarrolladores **PPP** sufijos en el **pueden dibujar** directory para proporcionar varias versiones de una imagen, permitiendo Android elegir la versión correcta para cada dispositivo. El mismo mecanismo se usa para proporcionar varias traducciones de lenguaje aplicando el sufijo directorios de recursos con identificadores de idioma y referencia cultural.

![Captura de pantalla de carpetas/pueden dibujar los recursos y de recursos o los valores para varios identificadores de referencia culturales](localization-images/resources.png)

> [!NOTE]
> Al especificar un lenguaje de nivel superior como `es` solo dos caracteres son necesarios; sin embargo, al especificar una configuración regional completa, el formato de nombre de directorio requiere un guión y minúscula **r** para separar las dos partes, por ejemplo **pt rBR** o **zh-rCN**. Compare esto con el valor devuelto en el código, que tiene un carácter de subrayado (p. ej. `pt_BR`). Ambos son diferentes en el valor .NET `CultureInfo` clase utiliza, que tiene un guión solo (p. ej. `pt-BR`). Tenga en cuenta estas diferencias cuando se trabaja en las distintas plataformas de Xamarin.

#### <a name="stringsxml-file-format"></a>Formato de archivo Strings.Xml

Un localizado **valores** directorio (p. ej. **valores es** o **valores-pt-rBR**) debe contener un archivo denominado **Strings.xml** que contendrá el texto traducido para esa configuración regional.

Cada cadena traducible es un elemento XML con el recurso, Id. especificado como el `name` atributo y la cadena traducida como el valor:

```xml
<string name="app_name">TaskyL10n</string>
```

Necesita escape según las reglas normales de XML y el `name` debe ser un identificador de recurso de Android válido (sin espacios ni guiones). Este es un ejemplo del archivo de cadenas (en inglés) predeterminado para el ejemplo:

**values/Strings.xml**

```xml
<resources>
    <string name="app_name">TaskyL10n</string>
    <string name="taskadd">Add Task</string>
    <string name="taskname">Name</string>
    <string name="tasknotes">Notes</string>
    <string name="taskdone">Done</string>
    <string name="taskcancel">Cancel</string>
</resources>
```

El directorio español **valores es-es** contiene un archivo con el mismo nombre (**Strings.xml**) que contiene las traducciones:

**valores-es/Strings.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">TaskyLeon</string>
    <string name="taskadd">agregar tarea</string>
    <string name="taskname">Nombre</string>
    <string name="tasknotes">Notas</string>
    <string name="taskdone">Completo</string>
    <string name="taskcancel">Cancelar</string>
</resources>
```

![Captura de pantalla de varios valores de las carpetas, cada una con un archivo de Strings.xml](localization-images/values.png)

Con la instalación de archivos de cadenas, se pueden hacer referencia a los valores traducidos en diseños y el código.

### <a name="axml-layout-files"></a>Archivos de diseño AXML

Para hacer referencia a las cadenas adaptadas en archivos de diseño, utilice el `@string/id` sintaxis. Este fragmento de código XML se muestra en el ejemplo `text` (otros atributos se han omitido) de identificadores de recursos localizado en propiedades que se va a establecer con:

```xml
<TextView
    android:id="@+id/NameLabel"
    android:text="@string/taskname"
    ... />
<CheckBox
    android:id="@+id/chkDone"
    android:text="@string/taskdone"
    ... />
```

### <a name="gettext-method"></a>GetText (método)

Para recuperar las cadenas traducidas en el código, utilice la `GetText` método y pase el identificador de recurso:

```csharp
var cancelText = Resources.GetText (Resource.String.taskcancel);
```

#### <a name="quantity-strings"></a>Cadenas de cantidad

Recursos de cadena Android también le permiten crear *cadenas cantidad* que permite a los traductores proporcionar traducciones diferentes para distintas cantidades, como:

* "Hay 1 tarea izquierda".
* "Hay 2 tareas todavía para hacer."

(en lugar de un tipo genérico "hay n tareas izquierda").

En el **Strings.xml**

```xml
<plurals name="numberOfTasks">
   <!--
      As a developer, you should always supply "one" and "other"
      strings. Your translators will know which strings are actually
      needed for their language.
    -->
   <item quantity="one">There is %d task left.</item>
   <item quantity="other">There are %d tasks still to do.</item>
 </plurals>
```

Para representar el uso de la cadena completa la `GetQuantityString` método, pasando el identificador de recurso y el valor que se muestre (que se pasa dos veces). El segundo parámetro se usa Android para determinar *que* `quantity` de cadenas para usar, el tercer parámetro es el valor que sustituya realmente en la cadena (ambos son necesarios).

```csharp
var translated = Resources.GetQuantityString (
                    Resource.Plurals.numberOfTasks, taskcount, taskcount);`
```

Válido `quantity` conmutadores son:

* cero
* one
* dos
* Algunos
* many
* otras

Que se describen con más detalle en la [documentos Android](http://developer.android.com/guide/topics/resources/string-resource.html#Plurals). Si un idioma determinado no necesitan controlar 'especiales', los `quantity` se pasará por alto las cadenas (por ejemplo, la versión en inglés solo utiliza `one` y `other`; especificando un `zero` cadena no tiene ningún efecto, no se utilizarán).

### <a name="images"></a>Imágenes

Imágenes localizadas siguen las mismas reglas que los archivos de cadenas: todas las imágenes que se hace referencia en la aplicación deben colocarse en **pueden dibujar** directorios por lo que es una acción de reserva.

Imágenes específicas de la configuración regional, a continuación, se deben colocar en carpetas puede dibujar completas como **puede dibujar es** o **puede dibujar ja** (también pueden agregarse especificadores de PPP).

En esta captura de pantalla, cuatro imágenes se guardan en el **pueden dibujar** directorio, pero solo uno, **flag.png**, se adaptado copias en otros directorios.

![Captura de pantalla de varias carpetas con estas características, cada una contiene uno o más archivos .png localizado](localization-images/drawable.png)


#### <a name="other-resource-types"></a>Otros tipos de recursos

También puede proporcionar otros tipos de alternativa, los recursos específicos del idioma incluidos diseños, animaciones y archivos sin formato. Esto significa que se podría proporcionar un diseño de pantalla específica para uno o varios de los idiomas de destino, por ejemplo podría crear un diseño específicamente para alemán que permite que las etiquetas de texto muy largos.

4.2 Android introdujo la compatibilidad con [de derecha a izquierda idiomas (RTL)](http://android-developers.blogspot.fr/2013/03/native-rtl-support-in-android-42.html) si establece la configuración de aplicación `android:supportsRtl="true"`. El calificador de recurso `"ldrtl"` puede incluirse en un nombre de direcory que contienen diseños personalizados que están diseñados para mostrar de derecha a izquierda.

Para obtener más información sobre los nombres de directorio de recursos y reserva, hacer referencia a los documentos de Android para [proporcionar recursos alternativos](http://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources).


### <a name="app-name"></a>Nombre de la aplicación

El nombre de aplicación es fácil de traducir mediante el uso de un `@string/id` en para el `MainLauncher` actividad:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
    ConfigurationChanges =  ConfigChanges.Orientation | ConfigChanges.Locale)]
```

### <a name="right-to-left-rtl-languages"></a>Idiomas de derecha a izquierda (RTL)

Android 4.2 y versiones más reciente proporciona compatibilidad total con diseños de derecha a izquierda, se describe en detalle en la [blog nativo de derecha a izquierda admiten](http://android-developers.blogspot.dk/2013/03/native-rtl-support-in-android-42.html).

Cuando se usa Android 4.2 (API nivel 17) y alineación versiones más reciente, se pueden especificar valores con `start` y `end` en lugar de `left` y `right` (por ejemplo `android:paddingStart`). También hay nuevas API como `LayoutDirection`, `TextDirection`, y `TextAlignment` como ayuda para crear pantallas que se adapten para los lectores de derecha a izquierda.

La siguiente captura de pantalla muestra la [localizado **Tasky** ejemplo](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) en árabe:

[![Captura de pantalla de aplicación Tasky en árabe](localization-images/rtl-ar-sml.png)](localization-images/rtl-ar.png#lightbox) 

La siguiente captura de pantalla muestra la [localizado **Tasky** ejemplo](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) en hebreo:

[![Captura de pantalla de aplicación Tasky en hebreo](localization-images/rtl-he-sml.png)](localization-images/rtl-he.png#lightbox)

Texto de derecha a izquierda se localiza mediante **Strings.xml** archivos de la misma manera que el texto de izquierda a derecha.

## <a name="testing"></a>Pruebas

Asegúrese de probar exhaustivamente la configuración regional predeterminada. La aplicación se bloqueará si no se puede cargar los recursos predeterminados por algún motivo (es decir, lo están).

### <a name="emulator-testing"></a>Emulador de pruebas

Hacer referencia a Google [pruebas en un emulador de Android](https://developer.android.com/guide/topics/resources/localization.html#testing) sección para obtener instrucciones sobre cómo establecer un emulador con una configuración regional específica mediante el shell de ADB.

```shell
adb shell setprop persist.sys.locale fr-CA;stop;sleep 5;start
```

### <a name="device-testing"></a>Pruebas de dispositivo

Para realizar pruebas en un dispositivo, cambiar el idioma en el **configuración** aplicación.
**Sugerencia:** tome nota de los iconos y ubicación de los elementos de menú para que pueda revertir el idioma a la configuración original.


## <a name="summary"></a>Resumen

Este artículo tratan los conceptos básicos de localizar aplicaciones Android mediante el control de recursos integradas. Puede obtener más información acerca de i18n y L10n para iOS, Android y aplicaciones multiplataforma (incluidas las Xamarin.Forms) en [esta guía de soluciones multiplataforma](~/cross-platform/app-fundamentals/localization.md).



## <a name="related-links"></a>Vínculos relacionados

- [Tasky (localizada en el código) (ejemplo)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Localizar recursos de Android](http://developer.android.com/guide/topics/resources/localization.html)
- [Información general de localización entre plataformas](~/cross-platform/app-fundamentals/localization.md)
- [Localización de Xamarin.Forms](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Localización de iOS](~/ios/app-fundamentals/localization/index.md)
