---
title: Localización en Xamarin.iOS
description: Este documento describe las características de localización de iOS y cómo usar estas características en aplicaciones de Xamarin.iOS. Se trata de idioma, configuración regional, archivos de cadenas, imágenes de inicio y mucho más.
ms.prod: xamarin
ms.assetid: DFD9EB4A-E536-18E4-C8FD-679BA9C836D8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/28/2017
ms.openlocfilehash: 7f05243196a9b916ac5c7b73df957262604ccb11
ms.sourcegitcommit: d70fcc6380834127fdc58595aace55b7821f9098
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268815"
---
# <a name="localization-in-xamarinios"></a>Localización en Xamarin.iOS

_Este documento tratan las características de localización del SDK de iOS y cómo tener acceso a ellos con Xamarin._

Hacer referencia a la [codificaciones de internacionalización](encodings.md) para obtener instrucciones sobre cómo incluir páginas de códigos o conjuntos de caracteres en aplicaciones que se deben procesar datos no Unicode.

## <a name="ios-platform-features"></a>Características de la plataforma iOS

En esta sección se describe algunas de las características de localización de iOS. Vaya a la [próxima sección](#basics) para ver ejemplos y código específico.

### <a name="language"></a>Lenguaje

Los usuarios elegir su idioma en la **configuración** aplicación. Esta configuración afecta a las cadenas de idioma e imágenes mostradas por el sistema operativo y de aplicaciones. 

Para determinar el idioma que se usa en una aplicación, obtener el primer elemento de `NSBundle.MainBundle.PreferredLocalizations`:

```csharp
var lang = NSBundle.MainBundle.PreferredLocalizations[0];
```

Este valor será un código de idioma como `en` para inglés, `es` para español, `ja` para japonés, etcetera. El valor devuelto está restringido a una de las adaptaciones admitidas por la aplicación (mediante reglas de reserva para determinar a la mejor coincidencia).

Código de aplicación no necesita siempre comprobar si este valor: Xamarin y iOS ambos proporcionan características que ayudan a proporcionar automáticamente la cadena correcta o el recurso para el idioma del usuario. Estas características se describen en el resto de este documento.

> [!NOTE]
> Utilice `NSLocale.PreferredLanguages` para determinar las preferencias de idioma del usuario, sin tener en cuenta las adaptaciones admitidas por la aplicación. Los valores devueltos por este método cambiado en iOS 9; vea [TN2418 de nota técnica](https://developer.apple.com/library/content/technotes/tn2418/_index.html) para obtener más información.

### <a name="locale"></a>Configuración regional

Los usuarios elegir su configuración regional en el **configuración** aplicación. Esta configuración afecta a la manera en que las fechas, horas, números y moneda con formato.

Esto permite a los usuarios elegir si ver los formatos de hora de 12 horas o 24 horas, si su separador decimal es una coma o un punto y el orden del día, mes y año de presentación de la fecha.

Con Xamarin tendrá acceso a las clases de ambos Apple iOS (`NSNumberFormatter`), así como las clases de .NET Framework en System.Globalization. Los desarrolladores deben evaluar que mejor se adapta a sus necesidades, ya que hay distintas características disponibles en cada uno. En concreto, si va a recuperar y mostrar los precios de compra de la aplicación mediante StoreKit debe utilizar clases de formato de Apple para obtener la información de precios que se devuelve.

La configuración regional actual puede ser consultada por cualquiera de estas dos maneras:

- `NSLocale.CurrentLocale.LocaleIdentifier`
- `NSLocale.AutoUpdatingCurrentLocale.LocaleIdentifier`

El primer valor puede almacenarse en caché por el sistema operativo y por lo que podría no siempre refleja el idioma del usuario actualmente seleccionado. Utilice el valor de segundo para obtener la configuración regional seleccionada actualmente.

> [!NOTE]
> Mono (el runtime de .NET en el que se basa Xamarin.iOS) y las API de iOS de Apple son compatibles con conjuntos idénticos de combinaciones de idioma y región.
> Por este motivo, es posible seleccionar una combinación de idioma y región en el archivo iOS **configuración** aplicación que no se asigna a un valor válido en Mono. Por ejemplo, establecer idioma del iPhone en inglés y su región en España hará que las API siguientes obtener valores distintos:
> 
> - `CurrentThead.CurrentCulture`: en-US (API de Mono)
> - `CurrentThread.CurrentUICulture`: en-US (API de Mono)
> - `NSLocale.CurrentLocale.LocaleIdentifier`: en_ES (API de Apple)
>
> Debido a que utiliza Mono `CurrentThread.CurrentUICulture` para seleccionar los recursos y `CurrentThread.CurrentCulture` para dar formato a fechas y monedas, localización basada en Mono (por ejemplo, con archivos .resx) no puede producir resultados esperados para estas combinaciones de idioma y región. En estas situaciones, se basan en las API de Apple para adaptar según sea necesario.

### <a name="nscurrentlocaledidchangenotification"></a>NSCurrentLocaleDidChangeNotification

iOS genera un `NSCurrentLocaleDidChangeNotification` cuando el usuario actualiza su configuración regional. Las aplicaciones pueden escuchar para esta notificación mientras se está ejecutando y pueden realizar los cambios apropiados en la interfaz de usuario.

<a name="basics" />

## <a name="localization-basics-in-ios"></a>Conceptos básicos de localización en iOS

Las siguientes características de operaciones de E/s se aprovechan fácilmente en Xamarin para proporcionar recursos localizados para mostrar al usuario. Hacer referencia a la [TaskyL10n ejemplo](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) para ver cómo implementar estas ideas.

### <a name="specifying-default-and-supported-languages-in-infoplist"></a>Especificar predeterminados y los lenguajes admitidos en Info.plist

En [Technical Q & A QA1828: cómo iOS determina el idioma de la aplicación](https://developer.apple.com/library/content/qa/qa1828/_index.html), Apple describe cómo iOS selecciona un idioma que se usará en una aplicación. Los siguientes factores afectan el idioma que se muestra:

- El usuario preferido idiomas (se encuentra en la **configuración** aplicación)
- Las adaptaciones incluidas en la aplicación (.lproj carpetas)
- `CFBundleDevelopmentRegion` (**Info.plist** valor que especifica el idioma predeterminado para la aplicación)
- `CFBundleLocalizations` (**Info.plist** matriz especificando adaptaciones todos los admitidos)

Como se indica en la técnica de preguntas y respuestas, `CFBundleDevelopmentRegion` representa la región predeterminada y el idioma de una aplicación. Si la aplicación no admite explícitamente cualquier idioma preferido del usuario, usará el idioma especificado por este campo. 

> [!IMPORTANT]
> iOS 11 aplica este mecanismo de selección de idioma de forma más estricta que ocurría en versiones anteriores del sistema operativo. Por este motivo, cualquier aplicación de iOS 11 que no declara explícitamente sus adaptaciones compatibles: por incluidas carpetas .lproj o establecer un valor para `CFBundleLocalizations` : puede mostrar un idioma diferente en iOS 11 del que devolvía en iOS 10.

Si `CFBundleDevelopmentRegion` no se ha especificado en el **Info.plist** archivo, las herramientas de compilación de Xamarin.iOS actualmente utilizan un valor predeterminado de `en_US`. Aunque esto podría cambiar en futuras versiones, significa que el idioma predeterminado es el inglés.

Para asegurarse de que la aplicación selecciona un idioma esperado, siga estos pasos:

- Especifique un idioma predeterminado. Abra **Info.plist** y usar el **origen** vista diagrama para establecer un valor para el `CFBundleDevelopmentRegion` de la clave; en XML, debe ser similar al siguiente:

```xml
<key>CFBundleDevelopmentRegion</key>
<string>es</string>
```

Este ejemplo utiliza "es" para especificar que cuando ninguno de un usuario preferido idiomas son compatibles, español de forma predeterminada.

- Declarar adaptaciones todos los admitidos. En **Info.plist**, use la **origen** vista diagrama para establecer una matriz para el `CFBundleLocalizations` de la clave; en XML, debe ser similar al siguiente:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>es</string>
    ...
</array>
```

Aplicaciones de Xamarin.iOS que se han localizado mediante mecanismos. NET, como archivos .resx deben proporcionar estas **Info.plist** también con los valores.

Para obtener más información sobre estas **Info.plist** claves, eche un vistazo a de Apple [referencia de clave de lista de información de propiedad](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html).

### <a name="localizedstring-method"></a>LocalizedString (método)

El `NSBundle.MainBundle.LocalizedString` método busca el texto localizado que se ha almacenado en **.strings** archivos en el proyecto. Estos archivos se organizan según el lenguaje en directorios especiales con nombre con un **.lproj** sufijo.

#### <a name="strings-file-locations"></a>ubicaciones de archivos de .strings

- **Base.lproj** es el directorio que contiene los recursos para el idioma predeterminado.
  A menudo se encuentra en la raíz del proyecto (pero también se puede colocar en el **recursos** carpeta).
- **<language>.lproj** directorios se crean para cada idioma admitido, normalmente en el **recursos** carpeta.

Puede haber un número de diferentes **.strings** archivos en cada directorio de idioma:

- **Localizable.Strings** : la lista principal de texto localizado.
- **InfoPlist.strings** : determinadas teclas específicas se permiten en este archivo para traducir los elementos como el nombre de la aplicación.
- **< nombre del guión gráfico > .strings** – archivo opcional que contiene las traducciones de elementos de la interfaz de usuario en un guión gráfico.

El **acción de compilación** para estos archivos deben estar **agrupación de recursos**.

#### <a name="strings-file-format"></a>formato de archivo .strings

La sintaxis para los valores de cadena localizada es:

```console
/* comment */
"key"="localized-value";
```

Se deben omitir los siguientes caracteres en cadenas:

* `\"`  comillas
* `\\`  Barra diagonal inversa
* `\n`  Nueva línea

Este es un ejemplo **es/Localizable.strings** (Internet Explorer. Archivo de español) del ejemplo:

```console
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

```console
Default.png
Default@2x.png
Default-568h@2x.png
LaunchScreen.xib
```

### <a name="app-name"></a>Nombre de la aplicación

Colocar un **InfoPlist.strings** un archivo en un **.lproj** directorio le permite invalidar algunos valores de la aplicación **Info.plist**, incluido el nombre de aplicación:

```console
"CFBundleDisplayName" = "LeónTodo";
```

Otras teclas que puede usar para [localizar cadenas específicas de la aplicación](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW21) son:

- CFBundleName
- CFBundleShortVersionString
- NSHumanReadableCopyright

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

```console
Full,Long: Friday, August 7, 2015 at 10:29:32 AM PDT
Short,Short: 8/7/15, 10:29 AM
Medium,None: Aug 7, 2015
```

Resultados de español de España:

```console
Full,Long: viernes, 7 de agosto de 2015, 10:26:58 GMT-7
Short,Short: 7/8/15 10:26
Medium,None: 7/8/2015
```

Hacer referencia a la manzana [fecha formateadores](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html) documentación para obtener más información. Al probar la configuración regional y formatos de fecha y hora, compruebe las **iPhone lenguaje** y **región** configuración.

<a name="rtl" />

### <a name="right-to-left-rtl-layout"></a>Diseño de derecha a izquierda (RTL)

iOS proporciona una serie de características que ayudan en la creación de aplicaciones con reconocimiento de derecha a izquierda:

* Diseño automático de uso `leading` y `trailing` atributos de alineación de control (que corresponde a la izquierda y derecha para inglés, pero se ha invertido para idiomas de derecha a izquierda).
  El [ `UIStackView` ](~/ios/user-interface/controls/uistackview.md) control es especialmente útil para distribuir los controles para tener en cuenta de derecha a izquierda.
* Use `TextAlignment = UITextAlignment.Natural` para la alineación del texto (que se dejarán para la mayoría de los lenguajes pero adecuado para RTL).
* `UINavigationController` Voltea el botón Atrás e invierte deslice el dedo dirección automáticamente.

El siguientes capturas de pantalla se muestra la [ejemplo Tasky localizadas](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) en árabe y hebreo (aunque inglés se ha especificado en los campos):

[![](images/rtl-ar-sml.png "Localización en árabe")](images/rtl-ar.png#lightbox "Arabic") 

[![](images/rtl-he-sml.png "Localización en hebreo")](images/rtl-he.png#lightbox "Hebrew")

iOS invierte automáticamente el `UINavigationController`, y los demás controles se colocan dentro de `UIStackView` o alineado con diseño automático.
Texto de derecha a izquierda se localiza mediante **.strings** archivos de la misma manera que el texto de izquierda a derecha.

<a name="code"/>

## <a name="localizing-the-ui-in-code"></a>Adaptar la interfaz de usuario en el código

El [Tasky (localizada en el código)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) ejemplo muestra cómo adaptar una aplicación que se compila la interfaz de usuario en el código (en lugar de XIBs o guiones gráficos).

### <a name="project-structure"></a>Estructura del proyecto

![](images/solution-code.png "Árbol de recursos")

### <a name="localizablestrings-file"></a>Archivo localizable.Strings

Como se describió anteriormente, el **Localizable.strings** formato de archivo consta de pares de clave y valor. La clave describe el propósito de la cadena y el valor es el texto traducido que se usará en la aplicación.

El español (**es**) adaptaciones para el ejemplo se muestran a continuación:

```console
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

* En Visual Studio para Mac, se encuentra en la **panel de propiedades** y se denomina **identificador de localización**.
* en Xcode, se llama **Id. de objeto**.

Este valor de cadena a menudo tiene un formato como "NF3-h8-xmR", como se muestra en la captura de pantalla siguiente:

![](images/xs-designer-localization-id.png "Vista de Xcode de localización del guión gráfico")

Este valor se utiliza en el **.strings** archivo para asignar texto traducido automáticamente a cada control.

### <a name="mainstoryboardstrings"></a>MainStoryboard.strings

El formato del archivo de traducción de guión gráfico es similar a la **Localizable.strings** de archivos, salvo que la clave (el valor de la izquierda) no puede ser definido por el usuario, pero en su lugar, debe tener un formato muy específico: `ObjectID.property`.

En el ejemplo **Mainstoryboard.strings** a continuación puede ver `UITextField`tienen un `placeholder` text (propiedad) que se pueden localizar; `UILabel`tienen un `text` propiedad; y `UIButton`texto de s predeterminado se establece mediante `normalTitle`:

```console
"SXg-TT-IwM.placeholder" = "nombre de la tarea";
"Pqa-aa-ury.placeholder"= "otra información de tarea";
"zwR-D9-hM1.text" = "Detalles de la tarea";
"bAM-2j-Rzw.text" = "Notas";           /* Notes */
"NF3-h8-xmR.text" = "Completo";        /* Done */
"MWt-Ya-pMf.normalTitle" = "Guardar";  /* Save */
"IGr-pR-05L.normalTitle" = "Eliminar"; /* Delete */
```

> [!IMPORTANT]
> Uso de un guión gráfico con clases de tamaño puede producir en las traducciones que no aparecen en la aplicación. [Notas de la versión de Apple Xcode](https://developer.apple.com/library/content/releasenotes/DeveloperTools/RN-Xcode/Chapters/Introduction.html) indican que un guión gráfico o XIB no traduce correctamente si se cumplen estas tres cosas: utiliza las clases de tamaño, la localización de base y el destino build se establecen en Universal y la compilación tiene como destino iOS 7.0. La solución consiste en duplicar el archivo de cadenas de guión gráfico en dos archivos idénticos: **MainStoryboard~iphone.strings** y **MainStoryboard~ipad.strings**, tal y como se muestra en la captura de pantalla siguiente:
> 
> ![](images/xs-dup-strings.png "Archivos de cadenas")

<a name="appstore" />

## <a name="app-store-listing"></a>Lista de la tienda de aplicaciones

Sigue P+F de Apple [localización de almacén de aplicación](https://itunespartner.apple.com/en/apps/faq/App%20Store_Localization) para especificar traducciones para cada país la aplicación está a la venta. Tenga en cuenta su advertencia que las traducciones sólo aparecerá si la aplicación también contiene un localizado **.lproj** directorio para el idioma.

## <a name="summary"></a>Resumen

Este artículo tratan los conceptos básicos de localizar aplicaciones de iOS con las características de control y el guión gráfico de recursos integradas.

Puede obtener más información acerca de i18n y L10n para aplicaciones de iOS, Android y multiplataforma (incluida la Xamarin.Forms) en [esta guía de soluciones multiplataforma](~/cross-platform/app-fundamentals/localization.md).

## <a name="related-links"></a>Vínculos relacionados

- [Tasky (localizada en el código) (ejemplo)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Tasky (guión gráfico localizado) (ejemplo)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)
- [Guía de localización de Apple](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)
- [Información general de localización entre plataformas](~/cross-platform/app-fundamentals/localization.md)
- [Localización de Xamarin.Forms](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Localización de Android](~/android/app-fundamentals/localization.md)
