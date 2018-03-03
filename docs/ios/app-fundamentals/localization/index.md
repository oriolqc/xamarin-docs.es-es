---
title: "Localización de iOS"
description: "Este documento tratan las características de localización del SDK de iOS y cómo tener acceso a ellos con Xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: DFD9EB4A-E536-18E4-C8FD-679BA9C836D8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/28/2017
ms.openlocfilehash: ea91dbcf7148651cb5d10acae4ada8bb6758c39e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="ios-localization"></a>Localización de iOS

_Este documento tratan las características de localización del SDK de iOS y cómo tener acceso a ellos con Xamarin._

Hacer referencia a la [codificaciones de internacionalización](encodings.md) para obtener instrucciones sobre cómo incluir páginas de códigos o conjuntos de caracteres en aplicaciones que se deben procesar datos no Unicode.

## <a name="ios-platform-features"></a>Características de la plataforma de iOS

En esta sección se describe algunas de las características de localización de iOS. Vaya a la [próxima sección](#basics) para ver ejemplos y código específico.

### <a name="language"></a>Lenguaje

Los usuarios elegir su idioma en la **configuración** aplicación. Esta configuración afecta a las cadenas de idioma e imágenes mostradas por el sistema operativo, así como las aplicaciones que detectan la configuración de idioma.

Esto es donde se deciden a los usuarios si desean ver inglés, español, japonés, francés u otro lenguaje que se muestran en sus aplicaciones.

La lista real de los idiomas admitidos en iOS 7 es: inglés (Estados Unidos), inglés (Reino Unido), chino (simplificado), chino (tradicional), francés, alemán, italiano, japonés, coreano, español, árabe, catalán, croata, checo, danés, neerlandés, finés, griego, hebreo, Húngaro, indonesio, malayo, noruego, polaco, portugués, portugués (Brasil), rumano, ruso, eslovaco, sueco, tailandés, turco, ucraniano y vietnamita, inglés (Australia), español (México).

El idioma actual se puede consultar mediante el acceso al primer elemento de la `PreferredLanguages` matriz:

```csharp
var lang = NSBundle.MainBundle.PreferredLocalizations[0];
```

Este valor será un código de idioma como `en` para inglés, `es` para español, `ja` para japonés, etcetera. _El valor devuelto está restringido a una de las adaptaciones admitidas por la aplicación (mediante reglas de reserva para determinar a la mejor coincidencia)._

Código de aplicación no necesita siempre comprobar si este valor - Xamarin y iOS ambos proporcionan características que ayudan a proporcionar automáticamente la cadena correcta o el recurso para el idioma del usuario. Estas características se describen en el resto de este documento.

> [!NOTE]
> **Nota:** antes de iOS 9, el código recomendado era `var lang = NSLocale.PreferredLanguages [0];`.
>
> Vea los resultados devueltos por este código cambia en iOS 9 - [TN2418 de nota técnica](https://developer.apple.com/library/content/technotes/tn2418/_index.html) para obtener más información.
>
> Aún puede usar `NSLocale.PreferredLanguages [0]` para determinar los valores reales seleccionados por el usuario (sin tener en cuenta las adaptaciones que admita la aplicación).

### <a name="locale"></a>Configuración regional

Los usuarios elegir su configuración regional en el **configuración** aplicación. Esta configuración afecta a la manera en que las fechas, horas, números y moneda con formato.

Esto permite a los usuarios elegir si ver los formatos de hora de 12 horas o 24 horas, si su separador decimal es una coma o un punto y el orden del día, mes y año de presentación de la fecha.

Con Xamarin tendrá acceso a las clases de ambos Apple iOS (`NSNumberFormatter`), así como las clases de .NET Framework en System.Globalization. Los desarrolladores deben evaluar que mejor se adapta a sus necesidades, ya que hay distintas características disponibles en cada uno. En concreto, si va a recuperar y mostrar los precios de compras desde la aplicación en uso StoreKit definitivamente debe utilizar clases de formato de Apple para obtener la información de precios que se devuelve.

Puede consultar la configuración regional actual, ya sea:

- `NSLocale.CurrentLocale.LocaleIdentifier`
- `NSLocale.AutoUpdatingCurrentLocale.LocaleIdentifier`

El primer valor puede almacenarse en caché por el sistema operativo y por lo que podría no siempre refleja el idioma del usuario actualmente seleccionado. Utilice el valor de segundo para obtener la configuración regional seleccionada actualmente.


### <a name="nscurrentlocaledidchangenotification"></a>NSCurrentLocaleDidChangeNotification

iOS genera un `NSCurrentLocaleDidChangeNotification` cuando el usuario actualiza su configuración regional. Las aplicaciones pueden escuchar para esta notificación mientras se está ejecutando y realizan los cambios apropiados en la interfaz de usuario.

<a name="basics" />

## <a name="localization-basics-in-ios"></a>Conceptos básicos de localización en iOS

Las siguientes características de operaciones de E/s se aprovechan fácilmente en Xamarin para proporcionar recursos localizados para mostrar al usuario. Hacer referencia a la [TaskyL10n ejemplo](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) para ver cómo implementar estas ideas.

### <a name="infoplist"></a>Info.plist

Antes de empezar, configure la **Info.plist** archivo con las claves siguientes:

- `CFBundleDevelopmentRegion` -el idioma predeterminado para la aplicación (normalmente el idioma, pronunciado por los desarrolladores de software y en los guiones gráficos y recursos de cadena e imagen). En el ejemplo siguiente, **en** se especifica (en inglés).
- `CFBundleLocalizations` -una matriz de otras adaptaciones admitida por la aplicación, también mediante los códigos de idioma como **es** (español) y **pt-PT** (portugués se habla en Portugal).

```xml
<key>CFBundleDevelopmentRegion</key>
<string>en</string>
<key>CFBundleLocalizations</key>
<array>
  <string>de</string>
  <string>es</string>
  <string>ja</string>
  ...
</array>
```

### <a name="localizedstring-method"></a>LocalizedString (método)

El `NSBundle.MainBundle.LocalizedString` método busca el texto localizado que se ha almacenado en **.strings** archivos en el proyecto. Estos archivos se organizan según el lenguaje en directorios especiales con nombre con un **.lproj** sufijo.

#### <a name="strings-file-locations"></a>ubicaciones de archivos de .strings

- **Base.lproj** es el directorio que contiene los recursos para el idioma predeterminado.
  A menudo se encuentra en la raíz del proyecto (pero también se puede colocar en el **recursos** carpeta).
- **<language>.lproj** directorios se crean para cada idioma admitido, normalmente en el **recursos** carpeta.

Puede haber un número de diferentes **.strings** archivos en cada directorio de idioma:

- **Localizable.Strings** -la lista principal de texto localizado.
- **InfoPlist.strings** : determinadas teclas específicas se permiten en este archivo para traducir los elementos tales como el nombre de la aplicación.
- **< nombre del guión gráfico > .strings** -archivo opcional que contiene las traducciones de elementos de la interfaz de usuario en un guión gráfico.

El **acción de compilación** para estos archivos deben estar **agrupación de recursos**.

#### <a name="strings-file-format"></a>formato de archivo .strings

La sintaxis para los valores de cadena localizada es:

```csharp
/* comment */
"key"="localized-value";
```

Se deben omitir los siguientes caracteres en cadenas:

* `\"`  comillas
* `\\`  Barra diagonal inversa
* `\n`  Nueva línea

Este es un ejemplo **es/Localizable.strings** (Internet Explorer. Archivo de español) del ejemplo:

```csharp
"<new task>" = "<new task>";
"Task Details" = "Detalles de la tarea";
"Name" = "Nombre";
"task name" = "nombre de la tarea";
"Notes" = "Notas";
"other task info"= "otra información de tarea";
"Done" = "Completo";
"Save" = "Guardar";
"Delete" = "Eliminar";
```

### <a name="images"></a>Imágenes

Para localizar una imagen en iOS:

1. Hace referencia a la imagen en el código, por ejemplo:

  ```csharp
  UIImage.FromBundle("flag");
  ```

2. Coloque el archivo de imagen predeterminado **flag.png** en **Base.lproj** (el directorio de lenguaje de desarrollo nativo).

3. Si lo desea colocar las versiones localizadas de la imagen en **.lproj** carpetas para cada idioma (p. ej. **es.lproj**, **ja.lproj**). Usar el mismo nombre de archivo **flag.png** en cada directorio de idioma.

Si no hay una imagen para un idioma determinado, iOS recurra a la carpeta de lenguaje nativo de forma predeterminada y cargar la imagen desde allí.


#### <a name="launch-images"></a>Iniciar imágenes

Utilizar las convenciones de nomenclatura estándares para las imágenes de inicio (y el XIB o el guión gráfico para modelos de iPhone 6) cuando se coloquen en la **.lproj** directorios para cada idioma.

```csharp
Default.png
Default@2x.png
Default-568h@2x.png
LaunchScreen.xib
```

### <a name="app-name"></a>Nombre de la aplicación

Colocar un **InfoPlist.strings** un archivo en un **.lproj** directorio le permite invalidar algunos valores de la aplicación **Info.plist**, incluido el nombre de aplicación:

```csharp
"CFBundleDisplayName" = "LeónTodo";
```

Otras teclas que puede usar para [localizar cadenas específicas de la aplicación](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW21) son:

- CFBundleName
- CFBundleShortVersionString
- NSHumanReadableCopyright


<!--
## App icon

Does not seem to be possible (although it definitely used to be!).
-->

### <a name="localization-native-development-region"></a>Región de desarrollo nativo de localización

Las cadenas predeterminadas (ubicado en el **Base.lproj** carpeta) se asume que el idioma de 'reserva'. Esto significa que si una traducción se solicita en el código y no se encuentra en un el idioma actual, la **Base.lproj** carpeta se buscará la cadena predeterminada utilizar (si se encuentra ninguna coincidencia, la propia cadena de identificador de traducción es se muestra).

Los desarrolladores pueden elegir un idioma diferente en la reserva si establece la clave de plist `CFBundleDevelopmentRegionKey`. Su valor debe establecerse en el código de idioma para el lenguaje de desarrollo nativo. Esta captura de pantalla muestra que un plist de Xamarin Studio con la región de desarrollo nativo establecido en español (es):

![](images/cfbundledevelopmentregion.png "Propiedad InfoPList.strings")

Si el idioma predeterminado usado en guiones gráficos y en todo el código no es el inglés, establezca este valor para reflejar el lenguaje nativo que se va a utilizar en todo el código de aplicación.

### <a name="dates-and-times"></a>Las fechas y horas

Aunque es posible utilizar las funciones integradas de fecha y hora de .NET (junto con la actual `CultureInfo`) para dar formato a fechas y horas para una configuración regional, omitiría específica de la configuración regional: configuración de usuario (que puede establecerse por separado de lenguaje).

Use el iOS `NSDateFormatter` para generar la salida que coincida con las preferencias del usuario Configuración regional. El código de ejemplo siguiente muestra la fecha básico y opciones de formato de hora:

```csharp
var date = NSDate.Now;
var df = new NSDateFormatter ();
df.DateStyle = NSDateFormatterStyle.Full;
df.TimeStyle = NSDateFormatterStyle.Long;
Debug.WriteLine ("Full,Long: " + df.StringFor(date));
df.DateStyle = NSDateFormatterStyle.Short;
df.TimeStyle = NSDateFormatterStyle.Short;
Debug.WriteLine ("Short,Short: " + df.StringFor(date));
df.DateStyle = NSDateFormatterStyle.Medium;
df.TimeStyle = NSDateFormatterStyle.None;
Debug.WriteLine ("Medium,None: " + df.StringFor(date));
```

Resultados para inglés de Estados Unidos:

```csharp
Full,Long: Friday, August 7, 2015 at 10:29:32 AM PDT
Short,Short: 8/7/15, 10:29 AM
Medium,None: Aug 7, 2015
```

Resultados de español de España:

```csharp
Full,Long: viernes, 7 de agosto de 2015, 10:26:58 GMT-7
Short,Short: 7/8/15 10:26
Medium,None: 7/8/2015
```

Hacer referencia a la manzana [fecha formateadores](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html) documentación para obtener más información. Al probar la configuración regional y formatos de fecha y hora, compruebe las **iPhone lenguaje** y **región** configuración.

<a name="rtl" />

### <a name="right-to-left-rtl-layout"></a>Diseño de derecha a izquierda (RTL)

iOS proporciona una serie de características que ayudan en la creación de aplicaciones con reconocimiento de derecha a izquierda:

* Use **diseño automático** `leading` y `trailing` atributos para la alineación del control (que corresponde a *izquierdo* y *derecho* para inglés, por ejemplo, pero es ha invertido para idiomas de derecha a izquierda).
  El [ `UIStackView` ](~/ios/user-interface/controls/uistackview.md) control es especialmente útil para distribuir los controles para tener en cuenta de derecha a izquierda.
* Use `TextAlignment = UITextAlignment.Natural` para la alineación del texto (que será *izquierdo* para la mayoría de los idiomas, pero *derecho* de derecha a izquierda).
* `UINavigationController` Voltea el botón Atrás e invierte deslice el dedo dirección automáticamente.

El siguientes capturas de pantalla se muestra la [localizado **Tasky** ejemplo](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) en árabe y hebreo (aunque inglés se ha especificado en los campos):

[ ![](images/rtl-ar-sml.png "Localización en árabe")](images/rtl-ar.png "Arabic") 

[ ![](images/rtl-he-sml.png "Localización en hebreo")](images/rtl-he.png "Hebrew")

iOS invierte automáticamente el `UINavigationController`, y los demás controles se colocan dentro de `UIStackView` o alineado con diseño automático.
Texto de derecha a izquierda se localiza mediante **.strings** archivos de la misma manera que el texto de izquierda a derecha.


<a name="code"/>

## <a name="localizing-the-ui-in-code"></a>Adaptar la interfaz de usuario en el código

El [Tasky (localizada en el código)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) ejemplo muestra cómo adaptar una aplicación que se compila la interfaz de usuario en el código (en lugar de XIBs o guiones gráficos).

### <a name="project-structure"></a>Estructura del proyecto



![](images/solution-code.png "Árbol de recursos")

### <a name="localizablestrings-file"></a>Archivo localizable.Strings

Como se describió anteriormente, el **Localizable.strings** formato de archivo consta de pares clave-valor, donde la clave es una cadena seleccionados por el usuario que indica

El español (**es**) adaptaciones para el ejemplo se muestran a continuación:

```csharp
"<new task>" = "<new task>";
"Task Details" = "Detalles de la tarea";
"Name" = "Nombre";
"task name" = "nombre de la tarea";
"Notes" = "Notas";
"other task info"= "otra información de tarea";
"Done" = "Completo";
"Save" = "Guardar";
"Delete" = "Eliminar";
```

### <a name="performing-the-localization"></a>Realizar la localización

En el código de aplicación, siempre que se establece el texto de presentación de la interfaz de usuario (si existe texto de una etiqueta, o marcador de posición de una entrada, etcetera), el código usa el archivo iOS `LocalizedString` función para recuperar la traducción adecuada para mostrar:

```csharp
var localizedString = NSBundle.MainBundle.LocalizedString ("key", "optional");
someControl.Text = localizedString;
```

<a name="storyboard"/>

## <a name="localizing-storyboard-uis"></a>Localizar interfaces de usuario de guión gráfico

El ejemplo [Tasky (guión gráfico localizado)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard) muestra cómo adaptar el texto de los controles en un guión gráfico.

### <a name="project-structure"></a>Estructura del proyecto

El **Base.lproj** directorio contiene el guión gráfico y también debe contener las imágenes que se utilizan en la aplicación.

Contienen los directorios de otros idiomas un **Localizable.strings** archivo para cualquier recurso de cadena al que hace referencia en el código, así como un **MainStoryboard.strings** archivo que contiene las traducciones de texto en el guión gráfico.

![](images/solution-storyboard.png "Árbol de recursos")

Los directorios de idioma deben contener una copia de las imágenes que se han localizado, para reemplazar el uno presente en **Base.lproj**.

### <a name="object-id--localization-id"></a>Id. de objeto o identificador de localización

Al crear y modificar controles en un guión gráfico, seleccione cada control y comprobar el identificador que se va a usar para la localización:

* en Visual Studio para Mac, se ha ubicado en el panel de propiedades y llama a **identificador de localización**.
* en Xcode, se llama **Id. de objeto**.

Es un valor de cadena que tiene a menudo un formulario como **"NF3-h8-xmR"**:

![](images/xs-designer-localization-id.png "Vista de Xcode de localización del guión gráfico")

Este valor se utiliza en el **.strings** archivo para asignar texto traducido automáticamente a cada control.

### <a name="mainstoryboardstrings"></a>MainStoryboard.strings

El formato del archivo de traducción de guión gráfico es similar a la **Localizable.strings** de archivos, salvo que la clave (el valor de la izquierda) no puede ser definido por el usuario, pero en su lugar, debe tener un formato muy específico: `ObjectID.property`.

En el ejemplo **Mainstoryboard.strings** a continuación puede ver `UITextField`tienen un `placeholder` text (propiedad) que se pueden localizar; `UILabel`tienen un `text` propiedad; y `UIButton`texto de s predeterminado se establece mediante `normalTitle`:

```csharp
"SXg-TT-IwM.placeholder" = "nombre de la tarea";
"Pqa-aa-ury.placeholder"= "otra información de tarea";
"zwR-D9-hM1.text" = "Detalles de la tarea";
"bAM-2j-Rzw.text" = "Notas";           /* Notes */
"NF3-h8-xmR.text" = "Completo";        /* Done */
"MWt-Ya-pMf.normalTitle" = "Guardar";  /* Save */
"IGr-pR-05L.normalTitle" = "Eliminar"; /* Delete */
```

> [!IMPORTANT]
> **Uso de un guión gráfico con clases de tamaño** puede dar lugar a que las traducciones no aparecen. Esto probablemente se relaciona con [este problema](http://stackoverflow.com/questions/24989208/xcode-6-does-not-localize-interface-builder) donde aparece la documentación de Apple: localización un guión gráfico o XIB no traduce correctamente si se cumplen todas las tres condiciones siguientes: el guión gráfico o XIB usa las clases de tamaño. La localización de base y el destino build se establecen en Universal. IOS 7.0 dirige a la compilación.
La solución consiste en duplicar el archivo de cadenas de guión gráfico en dos archivos idénticos: **MainStoryboard~iphone.strings** y **MainStoryboard~ipad.strings**:

> ![](images/xs-dup-strings.png "Archivos de cadenas")


<!--
# Native Formatting

Xamarin.iOS applications can take advantage of the .NET framework's formatting options for localizing
numbers and dates.

### Date string formatting

https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html#//apple_ref/doc/uid/TP40002369-SW1


NSDateFormatter formatter = new NSDateFormatter ();
formatter.DateFormat = "MMMM/dd/yyyy";
NSString dateString = new NSString (formatter.ToString (d));


### Number formatting

https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfNumberFormatting10_4.html#//apple_ref/doc/uid/TP40002368-SW1

-->

<a name="appstore" />

## <a name="app-store-listing"></a>Lista de la tienda de aplicaciones

Sigue P+F de Apple [localización de almacén de aplicación](https://itunespartner.apple.com/en/apps/faq/App%20Store_Localization) para especificar traducciones para cada país la aplicación está a la venta. Tenga en cuenta su advertencia que las traducciones sólo aparecerá si la aplicación también contiene un localizado **.lproj** directorio para el idioma.

<!--

Once you’ve entered your application into iTunes Connect the default language
metadata and screenshots will appear as shown:

![]( "itunes connect 1")

Use the language list on the right to select other languages to provide
translated application name, description, search keywords, URLs and screenshots.
The complete list of languages is shown in this screenshot:

![]( "itunes connect 2")
-->

## <a name="summary"></a>Resumen

Este artículo tratan los conceptos básicos de localizar aplicaciones de iOS con las características de control y el guión gráfico de recursos integradas.

Puede obtener más información acerca de i18n y L10n para aplicaciones de iOS, Android y multiplataforma (incluida la Xamarin.Forms) en [esta guía de soluciones multiplataforma](~/cross-platform/app-fundamentals/localization.md).


## <a name="related-links"></a>Vínculos relacionados

- [Tasky (localizada en el código) (ejemplo)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Tasky (guión gráfico localizado) (ejemplo)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)
- [Guía de localización de Apple](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)
- [Información general de localización entre plataformas](~/cross-platform/app-fundamentals/localization.md)
- [Localización de Xamarin.Forms](~/xamarin-forms/app-fundamentals/localization.md)
- [Localización de Android](~/android/app-fundamentals/localization.md)
