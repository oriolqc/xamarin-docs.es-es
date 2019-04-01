---
title: Localización de Xamarin.iOS
description: 'Este documento describe las características de localización de iOS y cómo usar estas características en aplicaciones de Xamarin.iOS. Describe el idioma, configuración regional, los archivos de cadenas, imágenes de inicio y más.'
ms.prod: xamarin
ms.assetid: DFD9EB4A-E536-18E4-C8FD-679BA9C836D8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/28/2017
---

# <a name="localization-in-xamarinios"></a>Localización de Xamarin.iOS

_Este documento describe las características de localización del SDK de iOS y cómo tener acceso a ellos con Xamarin._

Hacer referencia a la [codificaciones de internacionalización](encodings.md) para obtener instrucciones sobre cómo incluir páginas de código y conjuntos de caracteres en las aplicaciones que se deben procesar datos no Unicode.

## <a name="ios-platform-features"></a>características de la plataforma de iOS

Esta sección describen algunas de las características de localización en iOS. Vaya a la [siguiente sección](#localization-basics-in-ios) para ver ejemplos y código específico.

### <a name="language"></a>Lenguaje

Los usuarios elegir su idioma en el **configuración** app. Esta configuración afecta a las cadenas de idioma y las imágenes mostradas por el sistema operativo y en aplicaciones.

Para determinar el idioma que se usa en una aplicación, obtener el primer elemento de `NSBundle.MainBundle.PreferredLocalizations`:

```csharp
var lang = NSBundle.MainBundle.PreferredLocalizations[0];
```

Este valor será un código de idioma como `en` para inglés, `es` en español, `ja` para japonés, etcetera. El valor devuelto está restringido a una de las localizaciones admitidas por la aplicación (mediante las reglas de reserva para determinar a la mejor coincidencia).

Código de la aplicación siempre es necesario comprobar este valor: Xamarin y iOS ambos proporcionan características que ayudan a proporcionar automáticamente la cadena correcta o el recurso para el idioma del usuario. Estas características se describen en el resto de este documento.

> [!NOTE]
> Use `NSLocale.PreferredLanguages` para determinar las preferencias de idioma del usuario, independientemente de las adaptaciones que son compatibles con la aplicación. Los valores devueltos por este método puede cambiado en iOS 9; consulte [TN2418 de nota técnica](https://developer.apple.com/library/content/technotes/tn2418/_index.html) para obtener más información.

### <a name="locale"></a>Configuración regional

Los usuarios elegir su configuración regional en el **configuración** app. Esta configuración afecta a la manera en que las fechas, horas, números y moneda con formato.

Esto permite a los usuarios elegir si ven formatos de hora 12 o 24 horas, si su separador decimal es una coma o un punto y el orden del día, mes y año de presentación de la fecha.

Tiene acceso a las clases de ambos Apple iOS con Xamarin (`NSNumberFormatter`), así como las clases de .NET en System.Globalization. Los desarrolladores deben evaluar que mejor se adapta a sus necesidades, ya que hay distintas características disponibles en cada uno. En concreto, si está recuperando y mostrar los precios de compra en la aplicación mediante StoreKit debe usar clases de formato de Apple para obtener la información de precios que se devuelve.

La configuración regional actual puede ser consultada por cualquiera de estas dos maneras:

- `NSLocale.CurrentLocale.LocaleIdentifier`
- `NSLocale.AutoUpdatingCurrentLocale.LocaleIdentifier`

El primer valor puede almacenarse en caché por el sistema operativo y por lo que es posible que no siempre refleja el idioma del usuario actualmente seleccionado. Utilice el valor de segundo para obtener la configuración regional actualmente seleccionada.

> [!NOTE]
> Mono (runtime de .NET en la que se basa Xamarin.iOS) y las API de iOS de Apple no admiten conjuntos idénticos de combinaciones de idioma y región.
> Por este motivo, es posible seleccionar una combinación de idioma y región en iOS **configuración** aplicación que no se asigna a un valor válido en Mono. Por ejemplo, establecer idioma de un iPhone al inglés y su región en España hará que las siguientes API obtener valores distintos:
>
> - `CurrentThead.CurrentCulture`: en-US (API de Mono)
> - `CurrentThread.CurrentUICulture`: en-US (API de Mono)
> - `NSLocale.CurrentLocale.LocaleIdentifier`: en_ES (API de Apple)
>
> Puesto que usa Mono `CurrentThread.CurrentUICulture` para seleccionar los recursos y `CurrentThread.CurrentCulture` para dar formato a fechas y divisas, localización basada en Mono (por ejemplo, con archivos .resx) no puede arrojar resultados esperados para estas combinaciones de idioma y región. En estas situaciones, se basan en las API de Apple para localizar según sea necesario.

### <a name="nscurrentlocaledidchangenotification"></a>NSCurrentLocaleDidChangeNotification

iOS genera un `NSCurrentLocaleDidChangeNotification` cuando el usuario actualiza la configuración regional. Las aplicaciones pueden escuchar para esta notificación mientras se está ejecutando y pueden realizar los cambios apropiados en la interfaz de usuario.

## <a name="localization-basics-in-ios"></a>Conceptos básicos de localización en iOS

Las siguientes características de iOS se aprovechan fácilmente en Xamarin para proporcionar recursos localizados para mostrar al usuario. Hacer referencia a la [TaskyL10n ejemplo](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) para ver cómo implementar estas ideas.

### <a name="specifying-default-and-supported-languages-in-infoplist"></a>Especificar predeterminados y los idiomas admitidos en Info.plist

En [un QA1828 & de preguntas y respuestas técnicas: Modo en que determina el idioma para la aplicación iOS](https://developer.apple.com/library/content/qa/qa1828/_index.html), Apple, describe cómo selecciona un idioma que se usará en una aplicación iOS. Los siguientes factores afectan el idioma que se muestra:

- Idiomas preferida del usuario (se encuentra en la **configuración** app)
- Las localizaciones integradas en la aplicación (.lproj carpetas)
- `CFBundleDevelopmentRegion` (**Info.plist** valor que especifica el idioma predeterminado para la aplicación)
- `CFBundleLocalizations` (**Info.plist** matriz especificando localizaciones todas las admitidas)

Como se indica en la técnica de preguntas y respuestas, `CFBundleDevelopmentRegion` representa la región predeterminada y el idioma de una aplicación. Si la aplicación no admite explícitamente cualquiera de los lenguajes preferidos del usuario, usará el idioma especificado por este campo.

> [!IMPORTANT]
> iOS 11 aplica este mecanismo de selección de idioma de forma más estricta ocurría en las versiones anteriores del sistema operativo. Por este motivo, cualquier aplicación de iOS 11 que no declaran explícitamente sus localizaciones compatibles, incluidas carpetas .lproj o estableciendo un valor para `CFBundleLocalizations` – puede mostrar un idioma diferente en iOS 11 que se hacía en iOS 10.

Si `CFBundleDevelopmentRegion` no se ha especificado en el **Info.plist** archivo, las herramientas de compilación de Xamarin.iOS utilizan actualmente un valor predeterminado de `en_US`. Aunque esto podría cambiar en futuras versiones, significa que el idioma predeterminado es inglés.

Para asegurarse de que la aplicación selecciona un idioma esperado, realice los pasos siguientes:

- Especificar un idioma predeterminado. Abra **Info.plist** y usar el **origen** vista para establecer un valor para el `CFBundleDevelopmentRegion` clave; en XML, debe ser similar al siguiente:

```xml
<key>CFBundleDevelopmentRegion</key>
<string>es</string>
```

Este ejemplo utiliza "es" para especificar que cuando ninguno de un usuario preferido se admiten idiomas, español de forma predeterminada.

- Declarar localizaciones todas las admitidas. En **Info.plist**, utilice el **origen** vista para establecer una matriz para el `CFBundleLocalizations` clave; en XML, debe ser similar al siguiente:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>es</string>
    ...
</array>
```

Las aplicaciones de Xamarin.iOS que se han localizado mediante mecanismos. NET, como archivos .resx deben proporcionarles estos **Info.plist** también con los valores.

Para obtener más información acerca de estos **Info.plist** claves, eche un vistazo a Apple [referencia de clave de lista de propiedades de información](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html).

### <a name="getlocalizedstring-method"></a>Método GetLocalizedString

El `NSBundle.MainBundle.GetLocalizedString` método busca un texto localizado que se ha almacenado en **.strings** los archivos del proyecto. Estos archivos están organizados por lenguaje en directorios especialmente denominados con una **.lproj** sufijo (tenga en cuenta que la primera letra de la extensión es una letra minúscula "L").

#### <a name="strings-file-locations"></a>ubicaciones del archivo .strings

- **Base.lproj** es el directorio que contiene recursos para el idioma predeterminado.
  A menudo se encuentra en la raíz del proyecto (pero también se pueden colocar en el **recursos** carpeta).
- **&lt;lenguaje&gt;.lproj** los directorios se crean para cada idioma admitido, normalmente en el **recursos** carpeta.

Puede haber diversas **.strings** archivos en cada directorio de idioma:

- **Localizable.Strings** : la lista principal del texto localizado.
- **InfoPlist.strings** : determinadas claves específicas se permiten en este archivo para traducir las cosas, como el nombre de la aplicación.
- **< nombre del guión gráfico > .strings** : archivo opcional que contiene las traducciones de los elementos de interfaz de usuario en un guión gráfico.

El **acción de compilación** para estos archivos deben estar **agrupación de recursos**.

#### <a name="strings-file-format"></a>formato de archivo .strings

La sintaxis de los valores de cadena localizada es:

```console
/* comment */
"key"="localized-value";
```

Debe escape los caracteres en las cadenas siguientes:

* `\"` oferta
* `\\` barra diagonal inversa
* `\n` nueva línea

Este es un ejemplo **es/Localizable.strings** (es decir. Archivo español) del ejemplo:

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

1. Consulte la imagen en el código, por ejemplo:

    ```csharp
    UIImage.FromBundle("flag");
    ```

2. Coloque el archivo de imagen predeterminado **flag.png** en **Base.lproj** (el directorio del lenguaje de desarrollo nativo).

3. Si lo desea colocar las versiones localizadas de la imagen en **.lproj** carpetas para cada idioma (p ej. **es.lproj**, **ja.lproj**). Usar el mismo nombre de archivo **flag.png** en cada directorio del lenguaje.

Si no hay una imagen para un idioma determinado, iOS se revertirá a la carpeta de lenguaje nativo de forma predeterminada y cargar la imagen desde allí.

#### <a name="launch-images"></a>Las imágenes de inicio

Usar las convenciones de nomenclatura estándares para las imágenes de inicio (y el XIB o guion gráfico para los modelos de iPhone 6) al incluirlos en el **.lproj** directorios para cada idioma.

```console
Default.png
Default@2x.png
Default-568h@2x.png
LaunchScreen.xib
```

### <a name="app-name"></a>Nombre de la aplicación

Colocar un **InfoPlist.strings** de archivos en un **.lproj** directory le permite invalidar algunos valores de la aplicación **Info.plist**, incluido el nombre de aplicación:

```console
"CFBundleDisplayName" = "LeónTodo";
```

Otras claves que puede usar para [localizar cadenas específicas de la aplicación](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW21) son:

- CFBundleName
- CFBundleShortVersionString
- NSHumanReadableCopyright

### <a name="dates-and-times"></a>Las fechas y horas

Aunque es posible usar las funciones integradas de fecha y hora en .NET (junto con la actual `CultureInfo`) para dar formato a fechas y horas para una configuración regional, omitiría específicas de configuración regional-configuración de usuario (que puede establecerse por separado de lenguaje).

Usar iOS `NSDateFormatter` para producir el resultado que coincida con la preferencia de configuración regional del usuario. El código de ejemplo siguiente muestra básica fecha y hora en las opciones de formato:

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

Resultados para español de España:

```console
Full,Long: viernes, 7 de agosto de 2015, 10:26:58 GMT-7
Short,Short: 7/8/15 10:26
Medium,None: 7/8/2015
```

Hacer referencia a la manzana [fecha formateadores](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html) documentación para obtener más información. Al probar la configuración regional y formato de fecha y hora, comprobar ambos **iPhone lenguaje** y **región** configuración.

<a name="rtl" />

### <a name="right-to-left-rtl-layout"></a>Diseño de derecha a izquierda (RTL)

iOS proporciona una serie de características que ayudan en la creación de aplicaciones con reconocimiento de derecha a izquierda:

- Diseño automático de uso `leading` y `trailing` atributos para la alineación del control (que corresponde a la izquierda y derecha para inglés, pero se ha invertido para idiomas de derecha a izquierda).
  El [ `UIStackView` ](~/ios/user-interface/controls/uistackview.md) control es especialmente útil para disponer los controles para tener conciencia de derecha a izquierda.
- Use `TextAlignment = UITextAlignment.Natural` para la alineación del texto (que se dejarán para la mayoría de los lenguajes pero adecuado para RTL).
- `UINavigationController` Voltea el botón Atrás y pasar el dedo retrocederá automáticamente.

Capturas de pantalla siguientes se muestra el [ejemplo Tasky localizadas](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) en árabe y hebreo (aunque se ha especificado el inglés en los campos):

[![](images/rtl-ar-sml.png "Localización de árabe")](images/rtl-ar.png#lightbox "Arabic")

[![](images/rtl-he-sml.png "Localización en hebreo")](images/rtl-he.png#lightbox "Hebrew")

iOS se invierte automáticamente el `UINavigationController`, y los demás controles se colocan dentro de `UIStackView` o está alineado con el diseño automático.
Texto de derecha a izquierda se localiza mediante **.strings** archivos en la misma manera que el texto de izquierda a derecha.

<a name="code"/>

## <a name="localizing-the-ui-in-code"></a>Localización de la interfaz de usuario en el código

El [Tasky (localizada en el código)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) ejemplo muestra cómo localizar una aplicación donde se crea la interfaz de usuario en el código (en lugar de Xib o guiones gráficos).

### <a name="project-structure"></a>Estructura del proyecto

![](images/solution-code.png "Árbol de recursos")

### <a name="localizablestrings-file"></a>Archivo localizable.Strings

Como se describió anteriormente, el **Localizable.strings** formato de archivo consta de pares clave-valor. La clave describe la intención de la cadena y el valor es el texto traducido que se usará en la aplicación.

El español (**es**) localizaciones para el ejemplo se muestran a continuación:

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

En el código de aplicación, siempre que se establece el texto para mostrar de la interfaz de usuario (ya sea texto de la etiqueta, o marcador de posición de entrada, etcetera), el código usa iOS `GetLocalizedString` función para recuperar la traducción adecuada para mostrar:

```csharp
var localizedString = NSBundle.MainBundle.GetLocalizedString ("key", "optional");
someControl.Text = localizedString;
```

<a name="storyboard"/>

## <a name="localizing-storyboard-uis"></a>Localización de interfaces de usuario de guión gráfico

El ejemplo [Tasky (guión gráfico localizado)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard) se muestra cómo localizar texto en los controles en un guión gráfico.

### <a name="project-structure"></a>Estructura del proyecto

El **Base.lproj** directorio contiene el guión gráfico y también debe contener todas las imágenes utilizadas en la aplicación.

Contienen los directorios de otros idiomas un **Localizable.strings** archivo para todos los recursos de cadena al que hace referencia en el código, así como un **MainStoryboard.strings** archivo que contiene las traducciones de texto en el guión gráfico.

![](images/solution-storyboard.png "Árbol de recursos")

Los directorios de idioma deben contener una copia de las imágenes que se han localizado, para reemplazar el uno presente en **Base.lproj**.

### <a name="object-id--localization-id"></a>Id. de objeto o identificador de localización

Al crear y modificar controles en un guión gráfico, seleccione cada control y compruebe el identificador que se usará para la localización:

- En Visual Studio para Mac, se encuentra en la **panel de propiedades** y se denomina **identificador de localización**.
- En Xcode, se llama a **Id. de objeto**.

A menudo, este valor de cadena tiene un formulario, como "NF3-h8-xmR", como se muestra en la siguiente captura de pantalla:

![](images/xs-designer-localization-id.png "Vista de Xcode de localización de guión gráfico")

Este valor se utiliza en el **.strings** archivo para asignar texto traducido automáticamente a cada control.

### <a name="mainstoryboardstrings"></a>MainStoryboard.strings

El formato del archivo de traducción de guión gráfico es similar a la **Localizable.strings** de archivos, excepto en que la clave (el valor de la izquierda), no puede ser definido por el usuario pero en su lugar, debe tener un formato muy específico: `ObjectID.property`.

En el ejemplo **Mainstoryboard.strings** a continuación puede ver `UITextField`tienen un `placeholder` propiedad de texto que se pueda localizar; `UILabel`tienen un `text` propiedad; y `UIButton`texto de s predeterminado se establece mediante `normalTitle`:

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
> Utilizar un guión gráfico con las clases de tamaño puede producir en las conversiones que no aparecen en la aplicación. [Notas de la versión Xcode de Apple](https://developer.apple.com/library/content/releasenotes/DeveloperTools/RN-Xcode/Chapters/Introduction.html) indican que un guión gráfico o XIB a no localizar correctamente si se cumplen estas tres cosas: usa las clases de tamaño, la localización de base y el destino de compilación se establecen en Universal y la compilación tiene como destino iOS 7.0. La solución consiste en duplicar el archivo de guión gráfico cadenas en dos archivos idénticos: **MainStoryboard~iphone.strings** y **MainStoryboard~ipad.strings**, tal y como se muestra en la captura de pantalla siguiente:
>
> ![](images/xs-dup-strings.png "Archivos de cadenas")

<a name="appstore" />

## <a name="app-store-listing"></a>Lista de App Store

Sigue las preguntas más frecuentes de Apple [App Store localización](https://itunespartner.apple.com/en/apps/faq/App%20Store_Localization) para especificar traducciones para cada país es la aplicación a la venta. Tenga en cuenta su advertencia de que las traducciones solo aparecerá si la aplicación también contiene un texto localizado **.lproj** directorio para el idioma.

## <a name="summary"></a>Resumen

En este artículo se trata los aspectos básicos de localización de aplicaciones de iOS con las características de control y el guión gráfico de recursos integradas.

Puede aprender más acerca de i18n y L10n para aplicaciones de iOS, Android y multiplataforma (incluido Xamarin.Forms) en [esta guía multiplataforma](~/cross-platform/app-fundamentals/localization.md).

## <a name="related-links"></a>Vínculos relacionados

- [Tasky (localizada en el código) (ejemplo)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Tasky (guión gráfico localizado) (ejemplo)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)
- [Guía de localización de Apple](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)
- [Información general de localización de multiplataforma](~/cross-platform/app-fundamentals/localization.md)
- [Localización de Xamarin.Forms](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Localización de Android](~/android/app-fundamentals/localization.md)
