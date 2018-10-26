---
title: Cadena y la localización de imagen
description: Las aplicaciones de Xamarin.Forms se pueden localizar con archivos de recursos. NET.
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/06/2016
ms.openlocfilehash: 09fe3587e4e435383822e50bd12616747b807f82
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108462"
---
# <a name="localization"></a>Localización

_Las aplicaciones de Xamarin.Forms se pueden localizar con archivos de recursos. NET._

## <a name="overview"></a>Información general

El mecanismo integrado para la localización de aplicaciones de .NET utiliza [archivos RESX](http://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx) y las clases en el `System.Resources` y `System.Globalization` espacios de nombres. Los archivos RESX, que contiene las cadenas traducidas se incrustan en el ensamblado de Xamarin.Forms, junto con una clase generada por el compilador que proporciona acceso fuertemente tipado para las traducciones. A continuación, se puede recuperar el texto traducido en código.

### <a name="sample-code"></a>Código de ejemplo

Hay dos ejemplos relacionados con este documento:

* [UsingResxLocalization](https://github.com/xamarin/xamarin-forms-samples/tree/master/UsingResxLocalization) es una demostración muy simple de los conceptos explicados. Los fragmentos de código que se muestra a continuación son todas de este ejemplo.
* [TodoLocalized](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized) es una aplicación básica de trabajo que usa estas técnicas de localización.

#### <a name="shared-projects-are-not-recommended"></a>No se recomiendan los proyectos compartidos

El ejemplo TodoLocalized incluye un [demostración del proyecto compartido](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized/SharedProject/) sin embargo, debido a las limitaciones del sistema de compilación no se obtienen los archivos de recursos un **. designer.cs** archivo generado, lo que interrumpe la capacidad de tener acceso a cadenas traducidas fuertemente tipadas en el código.

El resto de este documento se relaciona con los proyectos con la plantilla de biblioteca estándar de .NET de Xamarin.Forms.

## <a name="globalizing-xamarinforms-code"></a>Globalizar el código de Xamarin.Forms

**Globalizar** una aplicación es el proceso de lo que "world ready". Esto significa que escribir código que es capaz de mostrar diferentes idiomas.

Una de las partes principales de globalización de una aplicación está creando la interfaz de usuario para que no haya ningún *rígida* texto. Nada se muestra al usuario en su lugar, se debe recuperar de un conjunto de cadenas que se han traducido a su idioma elegido.

En este documento, examinaremos cómo usar los archivos RESX para almacenar estas cadenas y recuperarlos para su presentación según la preferencia del usuario.

Los ejemplos de destino los idiomas inglés, francés, español, alemán, chino, japonés, ruso y portugués (Brasil). Las aplicaciones se pueden traducir a tantos o tan pocos idiomas según sea necesario.

> [!NOTE]
> En la plataforma Universal de Windows, los archivos RESW deben usarse para la localización de notificación de inserción, en lugar de los archivos RESX. Para obtener más información, consulte [UWP localización](/windows/uwp/design/globalizing/globalizing-portal/).

### <a name="adding-resources"></a>Adición de recursos

El primer paso para la globalización de una aplicación de la biblioteca Xamarin.Forms .NET Standard es agregar los archivos de recursos RESX que se usará para almacenar todo el texto que se usa en la aplicación. Necesitamos agregar un archivo RESX que contiene el texto predeterminado y, a continuación, agregue los archivos RESX adicionales para cada idioma que desee admitir.

#### <a name="base-language-resource"></a>Recurso de idioma base

El archivo de recursos base (RESX) contendrá las cadenas de idioma predeterminada (los ejemplos se asume que el idioma predeterminado es inglés). Agregue el archivo al proyecto de código común de Xamarin.Forms, con el botón secundario en el proyecto y elija **Agregar > nuevo archivo...** .

Elija un nombre descriptivo, como **AppResources** y presione **Aceptar**.

[![Agregar archivo de recursos](text-images/resx-new-file-sml.png "cuadro de diálogo nuevo archivo")](text-images/resx-new-file.png#lightbox "nuevo cuadro de diálogo de archivo")

Se agregarán dos archivos al proyecto:

* **AppResources.resx** archivo donde se almacenan cadenas traducibles en formato XML.
* **AppResources.designer.cs** archivo que se declara una clase parcial que contiene referencias a todos los elementos creados en el archivo RESX XML.

El árbol de la solución mostrará los archivos relacionados. El archivo RESX *debe* modificarla para agregar nuevas cadenas traducibles; el **. designer.cs** archivo debería *no* se puede editar.

![](text-images/appresources-tree.png "Archivo AppResources.resx")

##### <a name="string-visibility"></a>Visibilidad de la cadena

De forma predeterminada cuando se generan referencias fuertemente tipado para las cadenas, estarán `internal` al ensamblado. Esto es porque la herramienta de compilación predeterminada para los archivos RESX genera el **. designer.cs** de archivos con `internal` propiedades.

Seleccione el **AppResources.resx** de archivo y mostrar el **propiedades** configurar panel para ver dónde está esta herramienta de compilación. La captura de pantalla siguiente muestra la **herramienta personalizada: ResXFileCodeGenerator**.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-internal-sml.png "Ventana de propiedades para AppResources.Resx")](text-images/vs-resx-internal.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "Panel de propiedades para AppResources.Resx")](text-images/xs-resx-internal.png#lightbox)

-----

Hacer que las propiedades de cadena fuertemente tipado `public`, debe cambiar manualmente la configuración a **herramienta personalizada: PublicResXFileCodeGenerator**, tal y como se muestra en la captura de pantalla siguiente:


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-public-sml.png "Ventana de propiedades para AppResources.Resx")](text-images/vs-resx-public.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "Panel de propiedades para AppResources.Resx")](text-images/xs-resx-internal.png#lightbox)


[![](text-images/xs-resx-public-sml.png "Panel de propiedades para AppResources.Resx")](text-images/xs-resx-public.png#lightbox)

-----

Este cambio es opcional y solo es necesario si desea hacer referencia a las cadenas localizadas en ensamblados diferentes (por ejemplo, si coloca los archivos RESX en un ensamblado diferente en el código). El ejemplo de este tema abandona las cadenas `internal` porque ya están definidas en el mismo ensamblado de biblioteca estándar de .NET de Xamarin.Forms en que se usen.

Basta con establecer la herramienta personalizada en el archivo RESX base tal como se muestra anteriormente. no es necesario establecer *cualquier* herramienta de compilación en los archivos RESX específicos del idioma se describe en las secciones siguientes.

##### <a name="editing-the-resx-file"></a>Edición del archivo RESX

Desafortunadamente, hay ningún editor de RESX integrada en Visual Studio para Mac. Adición de nuevas cadenas traducibles requiere la adición de un archivo XML nuevo `data` (elemento) para cada cadena. Cada `data` elemento puede contener lo siguiente:

* `name` atributo (obligatorio) es la clave para esta cadena traducible. Debe ser válido C# nombre de la propiedad - por lo que no se permiten espacios ni caracteres especiales.
* `value` elemento (obligatorio), que es la cadena real que se muestra en la aplicación.
* `comment` elemento (opcional) puede contener instrucciones para el traductor que explica cómo se utiliza esta cadena.
* `xml:space` (opcional) para controlar cómo se conserva el espacio en la cadena de atributo.

Algunos ejemplos de `data` elementos se muestran aquí:

```xml
<data name="NotesLabel" xml:space="preserve">
    <value>Notes:</value>
    <comment>label for input field</comment>
</data>
<data name="NotesPlaceholder" xml:space="preserve">
    <value>eg. buy milk</value>
    <comment>example input for notes field</comment>
</data>
<data name="AddButton" xml:space="preserve">
    <value>Add new item</value>
</data>
```

Tal como se escribe la aplicación, cada fragmento de texto que se muestra al usuario debe agregarse al archivo de recursos RESX base en una nueva `data` elemento. Se recomienda que incluya `comment`s tanto como sea posible para garantizar una traducción de alta calidad.

> [!NOTE]
> Visual Studio (incluida la edición Community gratis) contiene un editor de RESX básico. Si tiene acceso a un equipo Windows, que puede ser una manera cómoda de agregar y editar las cadenas en archivos RESX.

#### <a name="language-specific-resources"></a>Recursos específicos del idioma

Normalmente, la traducción real de las cadenas de texto predeterminado no será posible hasta que se han escrito grandes fragmentos de la aplicación (en cuyo caso el archivo RESX predeterminado contendrá una gran cantidad de cadenas). Sigue siendo una buena idea agregar los recursos específicos del idioma al principio del ciclo de desarrollo, opcionalmente, rellenar con texto traducido para ayudar a probar el código de localización.

Se agrega un archivo RESX adicional para cada idioma que desee admitir.
Archivos de recursos específicos del idioma deben seguir una convención de nomenclatura específica: usar el mismo nombre de archivo, como los recursos de bases de archivo (p ej. **AppResources**) seguido por un punto (.) y, a continuación, el código de idioma. Se incluyen ejemplos sencillos:

* **AppResources.fr.resx** -francés las traducciones de idiomas.
* **AppResources.es.resx** -traducciones de idioma español.
* **AppResources.de.resx** -traducciones de idioma alemán.
* **AppResources.ja.resx** -traducciones de idioma japonés.
* **AppResources.zh Hans.resx** - chino (simplificado) las traducciones de idiomas.
* **AppResources.zh Hant.resx** - chino (tradicional) las traducciones de idiomas.
* **AppResources.pt.resx** -traducciones de idioma portugués.
* **AppResources.pt BR.resx** -traducciones portugués (Brasil).

El patrón general consiste en usar los códigos de idioma de dos letras, pero hay algunos ejemplos (por ejemplo, chino) donde se usa un formato diferente y otros ejemplos (por ejemplo, portugués de Brasil) donde se requiere un identificador de configuración regional de cuatro caracteres.

Estos archivos de recursos específicos del idioma *no* requieren un **. designer.cs** la clase parcial para que pueda agregar como archivos XML normales, con el **acción de compilación: EmbeddedResource**establecido. Esta captura de pantalla muestra una solución que contiene los archivos de recursos específicos del idioma:

![](text-images/appresources-langs.png "Archivos de recursos específicos del idioma")

Cuando se desarrolla una aplicación y el archivo RESX base tiene texto agregado, debe enviarla a los traductores que se traducirán cada `data` elemento y devuelva un archivo de recursos específicos del idioma (mediante la convención de nomenclatura que se muestra) que se incluirán en la aplicación. A continuación, se muestran algunos ejemplos de 'traducidos':

**AppResources.es.resx (español)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>Escribir un artículo</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

**AppResources.ja.resx (japonés)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>新しい項目を追加</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

**AppResources.pt-BR.resx (portugués de Brasil)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>adicionar novo item</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

Solo el `value` elemento debe actualizarse el traductor - la `comment` no está pensado para traducir. Recuerde: al editar archivos XML para escapar caracteres reservados como `<`, `>`, `&` con `&lt;`, `&gt;`, y `&amp;` si aparecen en la `value` o `comment`.

<a name="incode" />

### <a name="using-resources-in-code"></a>Uso de recursos en el código

Las cadenas en los archivos de recursos RESX estará disponibles para su uso en el código de interfaz de usuario utilizando el `AppResources` clase. El `name` asignadas a cada cadena en el RESX archivo pasa a ser una propiedad de esa clase que puede hacer referencia en el código de Xamarin.Forms, tal como se muestra a continuación:

```csharp
var myLabel = new Label ();
var myEntry = new Entry ();
var myButton = new Button ();
// populate UI with translated text values from resources
myLabel.Text = AppResources.NotesLabel;
myEntry.Placeholder = AppResources.NotesPlaceholder;
myButton.Text = AppResources.AddButton;
```

La interfaz de usuario en iOS, Android y la plataforma Universal de Windows (UWP) se representa como se esperaría, salvo que ahora es posible convertir la aplicación en varios idiomas porque el texto que se carga desde un recurso en vez de estar codificado de forma rígida. Esta es una captura de pantalla que muestra la interfaz de usuario en cada plataforma antes de traducción:

![](text-images/simple-example-english.png "Interfaces de usuario multiplataforma antes de la traducción")

### <a name="troubleshooting"></a>Solución de problemas

#### <a name="testing-a-specific-language"></a>Un lenguaje específico de pruebas

Puede ser difícil de cambiar el simulador o en un dispositivo a distintos idiomas, especialmente durante el desarrollo cuando desea probar rápidamente las distintas referencias culturales.

Puede forzar un idioma específico que va a cargar estableciendo el `Culture` tal como se muestra en este fragmento de código:

```csharp
// force a specific culture, useful for quick testing
AppResources.Culture =  new CultureInfo("fr-FR");
```

Este enfoque: establecer la referencia cultural directamente en el `AppResources` clase: también se puede usar para implementar un selector de idioma dentro de la aplicación (en lugar de utilizar la configuración regional del dispositivo).

#### <a name="loading-embedded-resources"></a>Cargando recursos incrustados

El siguiente fragmento de código es útil cuando se intenta depurar problemas con los recursos incrustados (por ejemplo, los archivos RESX). Agregue este código a la aplicación (al principio del ciclo de vida de aplicación) y obtendrá una lista de todos los recursos incrustados en el ensamblado, que muestra el identificador de recurso completo:

```csharp
using System.Reflection;
// ...
// NOTE: use for debugging, not in released app code!
var assembly = typeof(EmbeddedImages).GetTypeInfo().Assembly; // "EmbeddedImages" should be a class in your app
foreach (var res in assembly.GetManifestResourceNames())
{
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

En el **AppResources.Designer.cs** archivo (en torno a *línea 33*), el completo *nombre del Administrador de recursos* se especifica (p ej. `"UsingResxLocalization.Resx.AppResources"`) similar al código siguiente:

```csharp
System.Resources.ResourceManager temp =
        new System.Resources.ResourceManager(
                "UsingResxLocalization.Resx.AppResources",
                typeof(AppResources).GetTypeInfo().Assembly);
```

Compruebe el **resultado de la aplicación** para los resultados del código de depuración que se muestra arriba, para confirmar los recursos correctos aparecen (es decir. `"UsingResxLocalization.Resx.AppResources"`).

Si no es así, el `AppResources` clase no se puede cargar sus recursos.
Compruebe lo siguiente para resolver problemas que no se puede encontrar los recursos:

* El espacio de nombres predeterminado para el proyecto coincide con el espacio de nombres raíz en el **AppResources.Designer.cs** archivo.
* Si el **AppResources.resx** archivo se encuentra en un subdirectorio, el nombre del subdirectorio debe formar parte del espacio de nombres *y* parte del identificador de recursos.
* El **AppResources.resx** archivo tiene **acción de compilación: EmbeddedResource**.
* El **opciones de proyecto > código fuente > directivas de nomenclatura .NET > nombres de recursos de estilo de uso Visual Studio** está activada. Sin embargo los espacios de nombres que se usa al hacer referencia a los recursos RESX tendrán que actualiza a lo largo de la aplicación, deje esto si lo prefiere.

#### <a name="doesnt-work-in-debug-mode-android-only"></a>No funciona en modo de depuración (solo Android)

Si las cadenas traducidas son trabajar en las compilaciones de versión Android pero no durante la depuración, haga doble clic en el **proyecto Android** y seleccione **Opciones > compilar > compilación de Android** y asegúrese de que el **Implementación rápida de ensamblados** no está activada. Esta opción provoca problemas con la carga de recursos y no debe usarse si va a probar aplicaciones localizadas.

### <a name="displaying-the-correct-language"></a>Mostrar el idioma correcto

Hasta ahora hemos examinado cómo escribir código para que se pueden proporcionar traducciones, pero no para realmente hacer que aparezca. El código de Xamarin.Forms puede aprovechar. Recursos de la red para cargar las traducciones de idioma correcta, pero necesitamos consultar el sistema operativo en cada plataforma para determinar el idioma que el usuario ha seleccionado.

Dado que algún código específico de la plataforma es necesaria para obtener la preferencia de idioma del usuario, utilice un [servicio de dependencia](~/xamarin-forms/app-fundamentals/dependency-service/index.md) exponer esa información en la aplicación de Xamarin.Forms e implementarlo para cada plataforma.

En primer lugar, defina una interfaz para exponer el preferido referencia cultural del usuario, similar al código siguiente:

```csharp
public interface ILocalize
{
    CultureInfo GetCurrentCultureInfo ();
    void SetLocale (CultureInfo ci);
}
```

En segundo lugar, utilice el [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) en Xamarin.Forms `App` clase para llamar a la interfaz y establezca nuestra cultura de recursos RESX en el valor correcto. Tenga en cuenta que no es necesario establecer manualmente este valor para la plataforma Universal de Windows, desde el marco de trabajo de los recursos automáticamente reconoce el idioma seleccionado en estas plataformas.

```csharp
if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
{
    var ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
    Resx.AppResources.Culture = ci; // set the RESX for resource localization
    DependencyService.Get<ILocalize>().SetLocale(ci); // set the Thread for locale-aware methods
}
```

El recurso `Culture` debe establecerse cuando la aplicación se carga por primera vez para que se usan las cadenas de idioma correcto. Opcionalmente, puede actualizar este valor según los eventos específicos de la plataforma que se generen en iOS o Android si el usuario actualiza sus preferencias de idioma mientras se ejecuta la aplicación.

Las implementaciones para el `ILocalize` interfaz se muestra en el [código específico de plataforma](#Platform-specific_Code) sección más adelante. Estas implementaciones aprovechar esta `PlatformCulture` clase auxiliar:

```csharp
public class PlatformCulture
{
    public PlatformCulture (string platformCultureString)
    {
        if (String.IsNullOrEmpty(platformCultureString))
        {
            throw new ArgumentException("Expected culture identifier", "platformCultureString"); // in C# 6 use nameof(platformCultureString)
        }
        PlatformString = platformCultureString.Replace("_", "-"); // .NET expects dash, not underscore
        var dashIndex = PlatformString.IndexOf("-", StringComparison.Ordinal);
        if (dashIndex > 0)
        {
            var parts = PlatformString.Split('-');
            LanguageCode = parts[0];
            LocaleCode = parts[1];
        }
        else
        {
            LanguageCode = PlatformString;
            LocaleCode = "";
        }
    }
    public string PlatformString { get; private set; }
    public string LanguageCode { get; private set; }
    public string LocaleCode { get; private set; }
    public override string ToString()
    {
        return PlatformString;
    }
}
```

<a name="Platform-specific_Code" />

### <a name="platform-specific-code"></a>Código específico de plataforma

El código para detectar el idioma para mostrar debe ser específica de la plataforma porque iOS, Android y UWP exponen esta información de forma ligeramente distinta. El código para el `ILocalize` servicio de dependencia es proporcionada a continuación para cada plataforma, junto con los requisitos específicos de la plataforma adicionales para garantizar el texto localizado se represente correctamente.

El código específico de la plataforma también debe controlar los casos donde el sistema operativo permite al usuario configurar un identificador de configuración regional que no es compatible con. La red `CultureInfo` clase. En estos casos se debe escribir código personalizado para detectar configuraciones regionales no compatibles y sustituya el mejor. Configuración regional compatible con NET.

#### <a name="ios-application-project"></a>Proyecto de aplicación de iOS

los usuarios de iOS seleccionan su idioma preferido por separado desde el formato de referencia cultural de fecha y hora. Para cargar los recursos correctos para localizar una aplicación de Xamarin.Forms, basta con consultar la `NSLocale.PreferredLanguages` matriz para el primer elemento.

La siguiente implementación de la `ILocalize` servicio de dependencia se debe colocar en el proyecto de aplicación de iOS. Porque iOS usa caracteres de subrayado en lugar de guiones (que es la representación estándar. NET) el código reemplaza el carácter de subrayado antes de crear instancias del `CultureInfo` clase:

```csharp
[assembly:Dependency(typeof(UsingResxLocalization.iOS.Localize))]

namespace UsingResxLocalization.iOS
{
public class Localize : UsingResxLocalization.ILocalize
    {
        public void SetLocale (CultureInfo ci)
        {
            Thread.CurrentThread.CurrentCulture = ci;
            Thread.CurrentThread.CurrentUICulture = ci;
        }
        public CultureInfo GetCurrentCultureInfo ()
        {
            var netLanguage = "en";
            if (NSLocale.PreferredLanguages.Length > 0)
            {
                var pref = NSLocale.PreferredLanguages [0];
                netLanguage = iOSToDotnetLanguage(pref);
            }
            // this gets called a lot - try/catch can be expensive so consider caching or something
            System.Globalization.CultureInfo ci = null;
            try
            {
                ci = new System.Globalization.CultureInfo(netLanguage);
            }
            catch (CultureNotFoundException e1)
            {
                // iOS locale not valid .NET culture (eg. "en-ES" : English in Spain)
                // fallback to first characters, in this case "en"
                try
                {
                    var fallback = ToDotnetFallbackLanguage(new PlatformCulture(netLanguage));
                    ci = new System.Globalization.CultureInfo(fallback);
                }
                catch (CultureNotFoundException e2)
                {
                    // iOS language not valid .NET culture, falling back to English
                    ci = new System.Globalization.CultureInfo("en");
                }
            }
            return ci;
        }
        string iOSToDotnetLanguage(string iOSLanguage)
        {
            var netLanguage = iOSLanguage;
            //certain languages need to be converted to CultureInfo equivalent
            switch (iOSLanguage)
            {
                case "ms-MY":   // "Malaysian (Malaysia)" not supported .NET culture
                case "ms-SG":    // "Malaysian (Singapore)" not supported .NET culture
                    netLanguage = "ms"; // closest supported
                    break;
                case "gsw-CH":  // "Schwiizertüütsch (Swiss German)" not supported .NET culture
                    netLanguage = "de-CH"; // closest supported
                    break;
                // add more application-specific cases here (if required)
                // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
        string ToDotnetFallbackLanguage (PlatformCulture platCulture)
        {
            var netLanguage = platCulture.LanguageCode; // use the first part of the identifier (two chars, usually);
            switch (platCulture.LanguageCode)
            {
                case "pt":
                    netLanguage = "pt-PT"; // fallback to Portuguese (Portugal)
                    break;
                case "gsw":
                    netLanguage = "de-CH"; // equivalent to German (Switzerland) for this app
                    break;
                // add more application-specific cases here (if required)
                // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
    }
}
```

> [!NOTE]
> El `try/catch` bloques en el `GetCurrentCultureInfo` método imitar el comportamiento de reserva que se utiliza normalmente con los especificadores de configuración regional: si la coincidencia exacta no se encuentra, busque una coincidencia cerrar basándose solo en el lenguaje (primer bloque de caracteres en la configuración regional).
>
> En el caso de Xamarin.Forms, algunas configuraciones regionales son válidos en iOS, pero no corresponden a válido `CultureInfo` en. NET y el código anterior intenta controlar esto.
>
> Por ejemplo, iOS **configuración > General lenguaje &amp; región** pantalla le permite establecer el teléfono **lenguaje** a **inglés** pero la  **Región** a **España**, que da como resultado una cadena de configuración regional de `"en-ES"`. Cuando el `CultureInfo` error en la creación, el código vuelve a utilizar solo las dos primeras letras para seleccionar el idioma para mostrar.
>
> Los desarrolladores modifiquen la `iOSToDotnetLanguage` y `ToDotnetFallbackLanguage` métodos para controlar los casos específicos necesarios para los idiomas admitidos.


Hay algunos elementos de interfaz de usuario definido por el sistema que se traducen automáticamente mediante iOS, como el **realiza** situado en el `Picker` control. Para forzar iOS para traducir estos elementos se debe indicar qué idiomas se admiten en el **Info.plist** archivo. Puede agregar estos valores a través de **Info.plist > origen** como se muestra aquí:

![Las claves de localización en Info.plist](text-images/info-plist.png "localización de claves de Info.plist")

Como alternativa, abra el **Info.plist** de archivos en un editor XML y edite los valores directamente:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>de</string>
    <string>es</string>
    <string>fr</string>
    <string>ja</string>
    <string>pt</string> <!-- Brazil -->
    <string>pt-PT</string> <!-- Portugal -->
    <string>ru</string>
    <string>zh-Hans</string>
    <string>zh-Hant</string>
</array>
<key>CFBundleDevelopmentRegion</key>
<string>en</string>
```

Una vez que haya implementado el servicio de dependencia y actualiza **Info.plist**, la aplicación de iOS podrá mostrar texto traducido.

> [!NOTE]
> Tenga en cuenta que se trata de Apple portugués ligeramente diferente de lo que cabría esperar.
> Desde [sus docs](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW2): _"usar pt como el identificador de idioma para portugués tal como se utiliza en Brasil y pt-PT como el identificador de idioma para portugués tal como se utiliza en Portugal"_.
> Esto significa que cuando se elige portuguesa en una configuración regional no estándar, el idioma de reserva será portugués (Brasil) en iOS, a menos que se escribe código para cambiar este comportamiento (como el `ToDotnetFallbackLanguage` anteriormente).

Para obtener más información sobre la localización de iOS, consulte [iOS localización](~/ios/app-fundamentals/localization/index.md).

#### <a name="android-application-project"></a>Proyecto de aplicación de Android

Android expone la configuración regional seleccionada actualmente a través de `Java.Util.Locale.Default`y también usa un separador de un carácter de subrayado en lugar de un guión (que será sustituido por el código siguiente). Agregue esta implementación de servicio de dependencia al proyecto de aplicación de Android:

```csharp
[assembly:Dependency(typeof(UsingResxLocalization.Android.Localize))]

namespace UsingResxLocalization.Android
{
    public class Localize : UsingResxLocalization.ILocalize
    {
        public void SetLocale(CultureInfo ci)
        {
            Thread.CurrentThread.CurrentCulture = ci;
            Thread.CurrentThread.CurrentUICulture = ci;
        }
        public CultureInfo GetCurrentCultureInfo()
        {
            var netLanguage = "en";
            var androidLocale = Java.Util.Locale.Default;
            netLanguage = AndroidToDotnetLanguage(androidLocale.ToString().Replace("_", "-"));
            // this gets called a lot - try/catch can be expensive so consider caching or something
            System.Globalization.CultureInfo ci = null;
            try
            {
                ci = new System.Globalization.CultureInfo(netLanguage);
            }
            catch (CultureNotFoundException e1)
            {
                // iOS locale not valid .NET culture (eg. "en-ES" : English in Spain)
                // fallback to first characters, in this case "en"
                try
                {
                    var fallback = ToDotnetFallbackLanguage(new PlatformCulture(netLanguage));
                    ci = new System.Globalization.CultureInfo(fallback);
                }
                catch (CultureNotFoundException e2)
                {
                    // iOS language not valid .NET culture, falling back to English
                    ci = new System.Globalization.CultureInfo("en");
                }
            }
            return ci;
        }
        string AndroidToDotnetLanguage(string androidLanguage)
        {
            var netLanguage = androidLanguage;
            //certain languages need to be converted to CultureInfo equivalent
            switch (androidLanguage)
            {
                case "ms-BN":   // "Malaysian (Brunei)" not supported .NET culture
                case "ms-MY":   // "Malaysian (Malaysia)" not supported .NET culture
                case "ms-SG":   // "Malaysian (Singapore)" not supported .NET culture
                    netLanguage = "ms"; // closest supported
                    break;
                case "in-ID":  // "Indonesian (Indonesia)" has different code in  .NET
                    netLanguage = "id-ID"; // correct code for .NET
                    break;
                case "gsw-CH":  // "Schwiizertüütsch (Swiss German)" not supported .NET culture
                    netLanguage = "de-CH"; // closest supported
                    break;
                    // add more application-specific cases here (if required)
                    // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
        string ToDotnetFallbackLanguage(PlatformCulture platCulture)
        {
            var netLanguage = platCulture.LanguageCode; // use the first part of the identifier (two chars, usually);
            switch (platCulture.LanguageCode)
            {
                case "gsw":
                    netLanguage = "de-CH"; // equivalent to German (Switzerland) for this app
                    break;
                    // add more application-specific cases here (if required)
                    // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
    }
}
```

> [!NOTE]
> El `try/catch` bloques en el `GetCurrentCultureInfo` método imitar el comportamiento de reserva que se utiliza normalmente con los especificadores de configuración regional: si la coincidencia exacta no se encuentra, busque una coincidencia cerrar basándose solo en el lenguaje (primer bloque de caracteres en la configuración regional).
>
> En el caso de Xamarin.Forms, algunas configuraciones regionales son válidos en Android, pero no corresponden a válido `CultureInfo` en. NET y el código anterior intenta controlar esto.
>
> Los desarrolladores modifiquen la `iOSToDotnetLanguage` y `ToDotnetFallbackLanguage` métodos para controlar los casos específicos necesarios para los idiomas admitidos.

Una vez que este código se haya agregado al proyecto de aplicación de Android, podrá mostrar automáticamente las cadenas traducidas.

> [!NOTE]
>️ **advertencia:** si las cadenas traducidas son trabajar en las compilaciones de versión Android pero no durante la depuración, haga doble clic en el **proyecto Android** y seleccione **Opciones > compilar > Android Compilar** y asegúrese de que el **implementación rápida de ensamblados** no está activada. Esta opción provoca problemas con la carga de recursos y no debe usarse si va a probar aplicaciones localizadas.

Para obtener más información acerca de la localización de Android, consulte [localización Android](~/android/app-fundamentals/localization.md).

#### <a name="universal-windows-platform"></a>Plataforma universal de Windows

Proyectos de universal Windows Platform (UWP) no requieren el servicio de dependencia. En su lugar, esta plataforma establece automáticamente referencia cultural del recurso correctamente.

##### <a name="assemblyinfocs"></a>AssemblyInfo.cs

Expanda el nodo de propiedades en el proyecto de biblioteca estándar de .NET y haga doble clic en el **AssemblyInfo.cs** archivo. Agregue la siguiente línea al archivo para establecer el lenguaje de ensamblado de recursos independientes del idioma para inglés:

```csharp
[assembly: NeutralResourcesLanguage("en")]
```

Esto informa al administrador de recursos de referencia cultural predeterminada de la aplicación, por lo tanto, lo que garantiza que las cadenas definidas en el archivo RESX neutral de idioma (**AppResources.resx**) se mostrará cuando la aplicación ejecuta en una de las configuraciones regionales de inglés.

### <a name="example"></a>Ejemplo

Después de actualizar los proyectos específicos de plataforma como se muestra anteriores y volver a compilar la aplicación con archivos RESX traducidos, traducciones actualizadas estará disponibles en cada aplicación. Esta es una captura de pantalla desde el código de ejemplo que se traduce en chino simplificado:

![](text-images/simple-example-hans.png "Interfaces de usuario multiplataforma traducidos a chino simplificado")

Para obtener más información acerca de la localización de UWP, consulte [UWP localización](/windows/uwp/design/globalizing/globalizing-portal/).

## <a name="localizing-xaml"></a>Localizar XAML

Al compilar una interfaz de usuario de Xamarin.Forms en XAML en el marcado tendría un aspecto similar al siguiente, con cadenas de incrustados directamente en el código XML:

```xaml
<Label Text="Notes:" />
<Entry Placeholder="eg. buy milk" />
<Button Text="Add to list" />
```

Idealmente, nos podríamos traducir los controles de interfaz de usuario directamente en el XAML, lo que podemos hacer mediante la creación de un *extensión de marcado*. El código para una extensión de marcado que expone los recursos RESX en XAML se muestra a continuación. Esta clase se debe agregar al código común de Xamarin.Forms (junto con las páginas XAML):

```csharp
using System;
using System.Globalization;
using System.Reflection;
using System.Resources;
using Xamarin.Forms;
using Xamarin.Forms.Xaml;

namespace UsingResxLocalization
{
    // You exclude the 'Extension' suffix when using in XAML
    [ContentProperty("Text")]
    public class TranslateExtension : IMarkupExtension
    {
        readonly CultureInfo ci = null;
        const string ResourceId = "UsingResxLocalization.Resx.AppResources";

        static readonly Lazy<ResourceManager> ResMgr = new Lazy<ResourceManager>(
            () => new ResourceManager(ResourceId, IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly));

        public string Text { get; set; }

        public TranslateExtension()
        {
            if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
            {
                ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
            }
        }

        public object ProvideValue(IServiceProvider serviceProvider)
        {
            if (Text == null)
                return string.Empty;

            var translation = ResMgr.Value.GetString(Text, ci);
            if (translation == null)
            {
#if DEBUG
                throw new ArgumentException(
                    string.Format("Key '{0}' was not found in resources '{1}' for culture '{2}'.", Text, ResourceId, ci.Name),
                    "Text");
#else
                translation = Text; // HACK: returns the key, which GETS DISPLAYED TO THE USER
#endif
            }
            return translation;
        }
    }
}
```

Las viñetas siguientes explican los elementos importantes en el código anterior:

* La clase se denomina `TranslateExtension`, pero por convención, podemos referirnos a es como **Translate** en nuestro marcado.
* La clase implementa `IMarkupExtension`, que es necesario para Xamarin.Forms para funcione.
* `"UsingResxLocalization.Resx.AppResources"` es el identificador de recurso para nuestros recursos RESX. Consta de nuestro espacio de nombres predeterminado, la carpeta donde se encuentran los archivos de recursos y el nombre del archivo RESX predeterminado.
* El `ResourceManager` clase se crea mediante `IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly)` para determinar el ensamblado actual para cargar los recursos y en memoria caché en estático `ResMgr` campo. Se crea como un `Lazy` escriba por lo que su creación se aplaza hasta que se usa en primer lugar en el `ProvideValue` método.
* `ci` usa el servicio de dependencia para obtener el idioma del usuario seleccionado desde el sistema operativo nativo.
* `GetString` es el método que recupera la cadena traducida real de los archivos de recursos. En la plataforma Universal de Windows, `ci` será null porque el `ILocalize` interfaz no está implementada en estas plataformas. Esto equivale a llamar a la `GetString` método con solo el primer parámetro. En su lugar, el marco de trabajo de los recursos reconocerá automáticamente la configuración regional y recuperará la cadena traducida desde el archivo RESX correspondiente.
* Control de errores se ha incluido para ayudar a depurar los recursos que faltan iniciando una excepción (en `DEBUG` solo en modo).

El fragmento de código XAML siguiente muestra cómo usar la extensión de marcado. Hay dos pasos para que funcione:

1. Declarar personalizado `xmlns:i18n` espacio de nombres en el nodo raíz. El `namespace` y `assembly` debe coincidir con la configuración del proyecto exactamente - en este ejemplo que son idénticos, pero podrían ser diferentes en el proyecto.
2. Use `{Binding}` sintaxis en los atributos que normalmente se debería contener el texto para llamar a la `Translate` extensión de marcado. La clave de recurso es el único parámetro necesario.

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        x:Class="UsingResxLocalization.FirstPageXaml"
        xmlns:i18n="clr-namespace:UsingResxLocalization;assembly=UsingResxLocalization">
    <StackLayout Padding="0, 20, 0, 0">
        <Label Text="{i18n:Translate NotesLabel}" />
        <Entry Placeholder="{i18n:Translate NotesPlaceholder}" />
        <Button Text="{i18n:Translate AddButton}" />
    </StackLayout>
</ContentPage>
```

La siguiente sintaxis más detallada también es válida para la extensión de marcado:

```xaml
<Button Text="{i18n:TranslateExtension Text=AddButton}" />
```

## <a name="localizing-platform-specific-elements"></a>Localizar elementos específicos de la plataforma

Aunque podemos administrar la traducción de la interfaz de usuario en el código de Xamarin.Forms, hay algunos elementos que se deben realizar en cada proyecto específico de plataforma. Esta sección explica cómo localizar:

* Nombre de la aplicación
* Imágenes

El proyecto de ejemplo incluye una imagen localizada denominada **flag.png**, que se hace referencia en C# como sigue:

```csharp
var flag = new Image();
switch (Device.RuntimePlatform)
{
    case Device.iOS:
    case Device.Android:
        flag.Source = ImageSource.FromFile("flag.png");
        break;
    case Device.UWP:
        flag.Source = ImageSource.FromFile("Assets/Images/flag.png");
        break;
}
```

También se hace referencia a la imagen de marca en el XAML similar al siguiente:

```xaml
<Image>
  <Image.Source>
    <OnPlatform x:TypeArguments="ImageSource">
        <On Platform="iOS, Android" Value="flag.png" />
        <On Platform="UWP" Value="Assets/Images/flag.png" />
    </OnPlatform>
  </Image.Source>
</Image>
```

Todas las plataformas resolverán automáticamente las referencias de imagen como éstas, que las versiones localizadas de las imágenes, siempre y cuando se implementan las estructuras de proyecto se explica más adelante.

### <a name="ios-application-project"></a>Proyecto de aplicación de iOS

iOS usa una denominación estándar que se denominan proyectos de localización o **.lproj** directorios que contienen recursos de cadena e imagen. Estos directorios pueden contener las versiones localizadas de imágenes que se usan en la aplicación y también el **InfoPlist.strings** archivo que se puede usar para localizar el nombre de la aplicación. Para obtener más información sobre la localización de iOS, consulte [iOS localización](~/ios/app-fundamentals/localization/index.md).

#### <a name="images"></a>Imágenes

Esta captura de pantalla muestra la aplicación de ejemplo de iOS con específicos del idioma **.lproj** directorios. El directorio español denominado **es.lproj**, contiene las versiones localizadas de la imagen predeterminada, así como **flag.png**:

![](text-images/ios-resources.png "Localización de directorios del proyecto de iOS")

Cada directorio del lenguaje contiene una copia de **flag.png**y adaptadas para dicho idioma. Si no se proporciona ninguna imagen, el sistema operativo será la imagen en el directorio de idioma predeterminado. Para obtener compatibilidad total con pantalla Retina, debería proporcionar **@2x** y **@3x** copias de cada imagen.

#### <a name="app-name"></a>Nombre de la aplicación

El contenido de la **InfoPlist.strings** es simplemente un único valor de clave para configurar el nombre de la aplicación:

```csharp
"CFBundleDisplayName" = "ResxEspañol";
```

Cuando se ejecuta la aplicación, el nombre de la aplicación y la imagen son ambos traducidos:

![](text-images/ios-imageicon.png "Localización de imagen y texto de la aplicación de ejemplo de iOS")

### <a name="android-application-project"></a>Proyecto de aplicación de Android

Android sigue un esquema diferente para almacenar imágenes localizadas mediante diferentes **drawable** y **cadenas** directorios con un sufijo de código de idioma. Cuando un código de configuración regional de cuatro letras es necesario (por ejemplo, zh-TW o pt-BR), tenga en cuenta que Android requiere más **r** siguientes dash/anterior de la configuración regional del código (p ej. pt rBR o zh-rTW). Para obtener más información acerca de la localización de Android, consulte [localización Android](~/android/app-fundamentals/localization.md).

#### <a name="images"></a>Imágenes

Esta captura de pantalla muestra el Android localizada de ejemplo con un algunos recursos drawable y cadenas:

![](text-images/android-resources.png "Android localizar recursos Drawable y directorios de cadena")

Tenga en cuenta que Android no usa zh-Hans y zh-Hant códigos para simplificado y chino tradicional; en su lugar, solo admite los códigos de país específicos zh-CN y zh-TW.

Para admitir las imágenes de una resolución distinta para pantallas de alta densidad, crear carpetas de idioma adicionales con `-*dpi` sufijos, tales como **recursos drawable-es-mdpi**, **recursos drawable-es-xdpi**, **recursos drawable-es-xxdpi**, etcetera. Consulte [proporcionar recursos de Android alternativa](http://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources) para obtener más información.

#### <a name="app-name"></a>Nombre de la aplicación

El contenido de la **strings.xml** es simplemente un único valor de clave para configurar el nombre de la aplicación:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">ResxEspañol</string>
</resources>
```

Actualización de la **MainActivity.cs** en el proyecto de aplicación de Android para que el `Label` hace referencia a las cadenas XML.

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

La aplicación ahora localiza la imagen y el nombre de la aplicación. Aquí es una captura de pantalla del resultado (en español):

![](text-images/android-imageicon.png "Localización de imagen y texto de la aplicación de ejemplo de Android")

### <a name="universal-windows-platform-application-projects"></a>Proyectos de aplicación de plataforma universal de Windows

La plataforma Universal de Windows posee una infraestructura de recursos que simplifica la localización de las imágenes y el nombre de la aplicación. Para obtener más información acerca de la localización de UWP, consulte [UWP localización](/windows/uwp/design/globalizing/globalizing-portal/).

#### <a name="images"></a>Imágenes

Las imágenes se pueden localizar al colocarlos en una carpeta específica del recurso, como se muestra en la captura de pantalla siguiente:

![](text-images/uwp-image-folder-structure.png "Estructura de carpetas de localización de imagen UWP")

En tiempo de ejecución, la infraestructura de recursos de Windows seleccionará la imagen adecuada según la configuración regional del usuario.

## <a name="summary"></a>Resumen

Las aplicaciones de Xamarin.Forms se pueden localizar con clases de globalización de .NET y de los archivos RESX. Además de una pequeña cantidad de código específico de plataforma para detectar el idioma que prefiere el usuario, la mayoría de los esfuerzos de localización se centraliza en el código común.

Por lo general se tratan las imágenes de una manera específica de la plataforma para aprovechar la compatibilidad con varias resoluciones proporcionada en iOS y Android.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de localización de RESX](https://developer.xamarin.com/samples/xamarin-forms/UsingResxLocalization/)
- [Aplicación de ejemplo TodoLocalized](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalized/)
- [Localización de multiplataforma](~/cross-platform/app-fundamentals/localization.md)
- [Localización de iOS](~/ios/app-fundamentals/localization/index.md)
- [Localización de Android](~/android/app-fundamentals/localization.md)
- [Localización de UWP](/windows/uwp/design/globalizing/globalizing-portal/)
- [Uso de la clase CultureInfo (MSDN)](http://msdn.microsoft.com/library/87k6sx8t%28v=vs.90%29.aspx)
- [Buscar y utilizar recursos para una referencia cultural concreta (MSDN)](http://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
