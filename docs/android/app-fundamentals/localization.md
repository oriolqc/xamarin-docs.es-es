---
title: Localización de Android
description: Este documento presenta las características de localización de Android SDK y cómo tener acceso a ellos con Xamarin.
ms.prod: xamarin
ms.assetid: D1277939-A1E8-468E-B136-820D816AF853
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: f6a7f8859340dcc8e48b6a4e6f56847168f4b71e
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67829762"
---
# <a name="android-localization"></a>Localización de Android

_Este documento presenta las características de localización de Android SDK y cómo tener acceso a ellos con Xamarin._

## <a name="android-platform-features"></a>Características de la plataforma Android

En esta sección se describe las características de localización principal de Android. Vaya a la [siguiente sección](#basics) para ver ejemplos y código específico.

### <a name="locale"></a>Configuración regional

Los usuarios elegir su idioma en **configuración > idioma & entrada**. Esta selección controla el idioma que se muestra y la configuración regional utiliza (p ej. para el formato de número y fecha).

La configuración regional actual puede consultarse mediante el contexto actual `Resources`:

```csharp
var lang = Resources.Configuration.Locale; // eg. "es_ES"
```

Este valor será un identificador de configuración regional que contiene un código de idioma y un código de configuración regional, separados por un carácter de subrayado. Como referencia, este es un [lista de configuraciones regionales de Java](https://www.oracle.com/technetwork/java/javase/locales-137662.html) y [Android admite las configuraciones regionales a través de StackOverflow](https://stackoverflow.com/questions/7973023/what-is-the-list-of-supported-languages-locales-on-android).

Estos son algunos ejemplos comunes:

* `en_US` para inglés (Estados Unidos)
* `es_ES` para el idioma de español (España)
* `ja_JP` para japonés (Japón)
* `zh_CN` chino (China)
* `zh_TW` chino (Taiwán)
* `pt_PT` para portugués (Portugal)
* `pt_BR` para portugués (Brasil)

### <a name="localechanged"></a>LOCALE_CHANGED

Android genera `android.intent.action.LOCALE_CHANGED` cuando el usuario cambia su selección de idioma.

Las actividades pueden optar por controlar esto estableciendo el `android:configChanges` atributo en la actividad, similar al siguiente:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
        ConfigurationChanges = ConfigChanges.Locale | ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

<a name="basics" />

## <a name="internationalization-basics-in-android"></a>Conceptos básicos de la internacionalización en Android

Estrategia de localización de Android tiene las siguientes partes principales:

- Carpetas de recursos que contiene cadenas localizadas, imágenes y otros recursos.

- `GetText` método, que se usa para recuperar las cadenas localizadas en el código

- `@string/id` en los archivos AXML, para colocar automáticamente las cadenas localizadas en los diseños.

### <a name="resource-folders"></a>Carpetas de recursos

Las aplicaciones de Android administran gran parte del contenido de carpetas de recursos, tales como:

* **diseño** -contiene archivos de diseño AXML.
* **puede dibujar** -contiene imágenes y otros recursos drawable.
* **valores** -contiene cadenas.
* **RAW** -contiene archivos de datos.

La mayoría de los desarrolladores ya están familiarizados con el uso de **PPP** sufijos en el **drawable** directory para proporcionar varias versiones de una imagen, lo que Android permite elegir la versión correcta para cada dispositivo. El mismo mecanismo se usa para proporcionar varias traducciones de idioma por delante de los directorios de recursos con identificadores de idioma y referencia cultural.

![Captura de pantalla de carpetas/drawable a recursos y los recursos y valores para varios identificadores de referencia culturales](localization-images/resources.png)

> [!NOTE]
> Al especificar un lenguaje de nivel superior como `es` solo dos caracteres son necesarios; sin embargo, al especificar una configuración regional completa, el formato de nombre de directorio requiere un guión y minúscula **r** para separar las dos partes, por ejemplo **pt rBR** o **zh-rCN**. Compare esto con el valor devuelto en el código, que tiene un carácter de subrayado (p ej. `pt_BR`). Ambas opciones son diferentes en el valor .NET `CultureInfo` clase utiliza, que tiene un guión sólo (p ej. `pt-BR`). Tenga en cuenta estas diferencias cuando se trabaja en las plataformas Xamarin.

#### <a name="stringsxml-file-format"></a>Formato de archivo Strings.Xml

Un localizado **valores** directory (p ej. **los valores es-es** o **valores-pt-rBR**) debe contener un archivo denominado **Strings.xml** que contendrá el texto traducido para esa configuración regional.

Cada cadena traducible es un elemento XML con el recurso de Id. especificado como el `name` atributo y la cadena traducida como el valor:

```xml
<string name="app_name">TaskyL10n</string>
```

Deberá escape según las reglas normales de XML y el `name` debe ser un identificador de recurso de Android válido (sin espacios ni guiones). Este es un ejemplo del archivo predeterminado cadenas (en inglés) para el ejemplo:

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

**values-es/Strings.xml**

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

![Captura de pantalla de varios valores de las carpetas, cada una con un archivo Strings.xml](localization-images/values.png)

Con la configuración de los archivos de cadenas, se pueden hacer referencia a los valores traducidos en ambos diseños y código.

### <a name="axml-layout-files"></a>Archivos de diseño AXML

Para hacer referencia a las cadenas localizadas en archivos de diseño, utilice el `@string/id` sintaxis. Este fragmento de código XML de muestra en el ejemplo `text` establecen con localizados (otros atributos se han omitido) de identificadores de recursos:

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

Para recuperar las cadenas traducidas en el código, utilice el `GetText` método y pase el identificador de recurso:

```csharp
var cancelText = Resources.GetText (Resource.String.taskcancel);
```

#### <a name="quantity-strings"></a>Cadenas de cantidad

Recursos de cadena Android también le permiten crear *cadenas cantidad* que permite a los traductores proporcionar traducciones diferentes cantidades diferentes, como:

* "Hay 1 tarea izquierda".
* "Hay 2 tareas que quedan por hacer."

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

Para representar el uso de la cadena completa la `GetQuantityString` método, pasando el identificador de recurso y el valor que se muestre (que se pasan dos veces). El segundo parámetro se usa Android para determinar *que* `quantity` string para usar, el tercer parámetro es el valor realmente se sustituyen en la cadena (ambos son necesarios).

```csharp
var translated = Resources.GetQuantityString (
                    Resource.Plurals.numberOfTasks, taskcount, taskcount);`
```

Válido `quantity` conmutadores son:

* cero
* one
* Dos
* Algunos
* many
* otras

Se describe con más detalle en la [docs Android](https://developer.android.com/guide/topics/resources/string-resource.html#Plurals). Si un idioma determinado no requiere el control "especiales", los `quantity` se pasará por alto las cadenas (por ejemplo, inglés solo usa `one` y `other`; al especificar un `zero` cadena no tiene ningún efecto, no se usará).

### <a name="images"></a>Imágenes

Imágenes localizadas siguen las mismas reglas que los archivos de cadenas: todas las imágenes que se hace referencia en la aplicación deben colocarse en **drawable** directorios por lo que hay una acción de reserva.

Las imágenes específicas de configuración regional, a continuación, se deben colocar en carpetas drawable calificadas como **drawable-es** o **drawable-ja** (también se pueden agregar especificadores PPP).

En esta captura de pantalla, cuatro imágenes se guardan en el **drawable** directorio, pero solo uno, **flag.png**, ha localizado copias en otros directorios.

![Captura de pantalla de varias carpetas drawable, cada una contiene uno o varios archivos .png localizado](localization-images/drawable.png)


#### <a name="other-resource-types"></a>Otros tipos de recursos

También puede proporcionar otros tipos de alternativa, los recursos específicos del idioma incluidos los diseños, animaciones y los archivos sin formato. Esto significa que podría proporcionar un diseño de pantalla específico para uno o varios de los idiomas de destino, por ejemplo podría crear un diseño específicamente para alemán que permite que las etiquetas de texto muy largos.

Android 4.2 introdujo la compatibilidad con [derecha para idiomas de izquierda (RTL)](http://android-developers.blogspot.fr/2013/03/native-rtl-support-in-android-42.html) si establece la configuración de la aplicación `android:supportsRtl="true"`. El calificador de recursos `"ldrtl"` puede incluirse en un nombre de directorio que contiene los diseños personalizados que están diseñados para mostrar de derecha a izquierda.

Para obtener más información sobre los nombres de directorio de recursos y de reserva, consulte la documentación de Android [proporcionar recursos alternativos](https://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources).


### <a name="app-name"></a>Nombre de la aplicación

El nombre de la aplicación es fácil de localizar mediante el uso de un `@string/id` en para el `MainLauncher` actividad:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
    ConfigurationChanges =  ConfigChanges.Orientation | ConfigChanges.Locale)]
```

### <a name="right-to-left-rtl-languages"></a>Idiomas de derecha a izquierda (RTL)

Android 4.2 y versiones más reciente proporciona compatibilidad completa para los diseños de derecha a izquierda, se describe en detalle en la [blog de soporte técnico nativo de derecha a izquierda](http://android-developers.blogspot.dk/2013/03/native-rtl-support-in-android-42.html).

Cuando se usa Android 4.2 (API nivel 17) y la alineación de versiones más reciente, se pueden especificar valores con `start` y `end` en lugar de `left` y `right` (por ejemplo `android:paddingStart`). También hay nuevas API, como `LayoutDirection`, `TextDirection`, y `TextAlignment` para ayudar a crear pantallas que se adaptan para los lectores de derecha a izquierda.

La siguiente captura de pantalla muestra la [localizado **Tasky** ejemplo](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) en árabe:

[![Captura de pantalla de la aplicación Tasky en árabe](localization-images/rtl-ar-sml.png)](localization-images/rtl-ar.png#lightbox) 

La siguiente captura de pantalla muestra la [localizado **Tasky** ejemplo](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) en hebreo:

[![Captura de pantalla de la aplicación Tasky en hebreo](localization-images/rtl-he-sml.png)](localization-images/rtl-he.png#lightbox)

Texto de derecha a izquierda se localiza mediante **Strings.xml** archivos en la misma manera que el texto de izquierda a derecha.

## <a name="testing"></a>Pruebas

Asegúrese de probar exhaustivamente la configuración regional predeterminada. La aplicación se bloqueará si no se puede cargar los recursos predeterminados por algún motivo (es decir, que faltan).

### <a name="emulator-testing"></a>Emulador de pruebas

Hacer referencia a Google [las pruebas en un emulador de Android](https://developer.android.com/guide/topics/resources/localization.html#testing) sección para obtener instrucciones sobre cómo establecer un emulador en una configuración regional específica mediante el shell de ADB.

```shell
adb shell setprop persist.sys.locale fr-CA;stop;sleep 5;start
```

### <a name="device-testing"></a>Pruebas de dispositivos

Para realizar pruebas en un dispositivo, cambiar el idioma en el **configuración** app.

> [!TIP]
> Realizar una nota de los iconos y la ubicación de los elementos de menú para que se puede revertir el lenguaje a la configuración original.


## <a name="summary"></a>Resumen

En este artículo se trata los aspectos básicos de localización de aplicaciones Android mediante la administración de recursos integradas. Puede obtener más información acerca de i18n y L10n para iOS, Android y aplicaciones multiplataforma (incluido Xamarin.Forms) en [esta guía multiplataforma](~/cross-platform/app-fundamentals/localization.md).



## <a name="related-links"></a>Vínculos relacionados

- [Tasky (localizada en el código) (ejemplo)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Localización de recursos de Android](https://developer.android.com/guide/topics/resources/localization.html)
- [Información general de localización de multiplataforma](~/cross-platform/app-fundamentals/localization.md)
- [Localización de Xamarin.Forms](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Localización de iOS](~/ios/app-fundamentals/localization/index.md)
