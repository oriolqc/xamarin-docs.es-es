---
title: "Localización"
description: Aplicaciones de Xamarin.Forms se pueden localizar con archivos de recursos. NET.
ms.topic: article
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/06/2016
ms.openlocfilehash: ad9129e06f43eea69518c4d876edc7cfd462f4e0
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="localization"></a>Localización

_Aplicaciones de Xamarin.Forms se pueden localizar con archivos de recursos. NET._

## <a name="overview"></a>Información general

El mecanismo integrado para la localización de aplicaciones .NET utiliza [RESX (archivos)](http://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx) y las clases en el `System.Resources` y `System.Globalization` los espacios de nombres. Los archivos RESX que contiene las cadenas traducidas se incrustan en el ensamblado de Xamarin.Forms, junto con una clase generada por el compilador que proporciona acceso fuertemente tipado para las traducciones. A continuación, se puede recuperar el texto de traducción en código.

Este documento contiene las siguientes secciones:

**Globalizar código Xamarin.Forms**

* Agregar y usar recursos de cadena en una aplicación de PCL Xamarin.Forms.
* Habilitar la detección de idioma en cada una de las aplicaciones nativas.

**Localizar XAML**

* Localizar XAML con un `IMarkupExtension`.
* Habilitar la extensión de marcado en las aplicaciones nativas.

**Localizar elementos específicos de la plataforma**

* Localizar las imágenes y el nombre de la aplicación en las aplicaciones nativas.

### <a name="sample-code"></a>Código de ejemplo

Hay dos ejemplos relacionados con este documento:

* [UsingResxLocalization](https://github.com/xamarin/xamarin-forms-samples/tree/master/UsingResxLocalization) es una demostración muy simple de los conceptos explicados. Los fragmentos de código que se muestra a continuación son todas de este ejemplo.
* [TodoLocalized](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized) es una aplicación en funcionamiento básico que usa estas técnicas de localización.

#### <a name="shared-projects-are-not-recommended"></a>No se recomiendan los proyectos compartidos

El ejemplo TodoLocalized incluye un [demostración de proyecto compartido](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized/SharedProject/) sin embargo, debido a las limitaciones del sistema de compilación no obtienen los archivos de recursos una **. designer.cs** archivo generado, lo que interrumpe la capacidad para tener acceso a convertir cadenas [fuertemente tipadas en el código](~/xamarin-forms/app-fundamentals/localization.md).

El resto de este documento se relaciona con proyectos mediante la plantilla de PCL Xamarin.Forms.

## <a name="globalizing-xamarinforms-code"></a>Globalizar código Xamarin.Forms

**Globalizar** una aplicación es el proceso de realización "world ready." Esto significa que escribir código que es capaz de mostrar los diferentes idiomas.

Una de las partes principales de globalización de una aplicación está creando la interfaz de usuario para que no haya ningún *codificado de forma rígida* texto. En su lugar, se debe recuperar nada muestra al usuario de un conjunto de cadenas que se ha traducido en su idioma elegido.

En este documento examinaremos cómo usar RESX (archivos) para almacenar estas cadenas y se recuperan para su presentación según las preferencias del usuario.

Los ejemplos de destino los idiomas inglés, francés, español, alemán, chino, japonés, ruso y portugués (Brasil). Las aplicaciones se pueden traducir en tantos o tan pocos idiomas según sea necesario.

### <a name="adding-resources"></a>Cómo agregar recursos

El primer paso para la globalización de una aplicación de Xamarin.Forms PCL consiste en agregar los archivos de recursos RESX que se usará para almacenar todo el texto que se usa en la aplicación. Es necesario agregar un archivo RESX que contiene el texto predeterminado y, a continuación, agregar archivos RESX adicionales para cada idioma que desee admitir.

#### <a name="base-language-resource"></a>Recurso de idioma base

El archivo de recursos de base (RESX) contendrá las cadenas de idioma predeterminado (los ejemplos se supongan que el idioma predeterminado es inglés). Agregue el archivo al proyecto de código común de Xamarin.Forms, con el botón secundario en el proyecto y elija **Agregar > nuevo archivo...** .

Elija un nombre descriptivo como **AppResources** y presione **Aceptar**.

[ ![Agregar el archivo de recursos](localization-images/resx-new-file-sml.png "nuevo cuadro de diálogo de archivo")](localization-images/resx-new-file.png "nuevo cuadro de diálogo de archivo")

Dos archivos se agregarán al proyecto:

* **AppResources.resx** archivo donde se puede traducir cadenas se almacenan en formato XML.
* **AppResources.designer.cs** archivos que declara una clase parcial para contener referencias a todos los elementos creados en el archivo RESX XML.

El árbol de la solución mostrará los archivos relacionados. El archivo RESX *debe* puede editar para agregar nuevas cadenas traducibles; la **. designer.cs** archivo debería *no* editarse.

![](localization-images/appresources-tree.png "Archivo AppResources.resx")

##### <a name="string-visibility"></a>Visibilidad de la cadena

De forma predeterminada cuando se generan fuertemente tipado referencias a las cadenas, estarán `internal` al ensamblado. Esto es porque la herramienta de compilación predeterminada para los archivos RESX genera el **. designer.cs** de archivos con `internal` propiedades.

Seleccione el **AppResources.resx** de archivos y mostrar la **propiedades** configurar panel para ver dónde está esta herramienta de compilación. La captura de pantalla siguiente muestra la **herramienta personalizada: ResXFileCodeGenerator**.

[[ide name="xs]]

[ ![](localization-images/xs-resx-internal-sml.png "Panel de propiedades para AppResources.Resx")](localization-images/xs-resx-internal.png)

[[/ide]]

[[ide name="vs]]

[ ![](localization-images/vs-resx-internal-sml.png "Ventana de propiedades para AppResources.Resx")](localization-images/vs-resx-internal.png)

[[/ide]]

Para hacer que las propiedades de cadena fuertemente tipado `public`, debe cambiar manualmente la configuración a **herramienta personalizada: PublicResXFileCodeGenerator**, tal y como se muestra en la captura de pantalla siguiente:


[[ide name="xs]]

[ ![](localization-images/xs-resx-public-sml.png "Panel de propiedades para AppResources.Resx")](localization-images/xs-resx-public.png)

[[/ide]]

[[ide name="vs]]

[ ![](localization-images/vs-resx-public-sml.png "Ventana de propiedades para AppResources.Resx")](localization-images/vs-resx-public.png)

[[/ide]]

Este cambio es opcional y solo es necesario si desea hacer referencia a las cadenas localizadas en ensamblados diferentes (por ejemplo, si coloca los archivos RESX en un ensamblado diferente en el código). El ejemplo de este tema deja las cadenas `internal` porque están definidas en el mismo ensamblado Xamarin.Forms PCL que se utilizan.

Basta con establecer la herramienta personalizada en el archivo RESX base tal y como se muestra arriba; no es necesario establecer *cualquier* herramienta de compilación en los archivos RESX específicos del idioma se describe en las secciones siguientes.

##### <a name="editing-the-resx-file"></a>Editar el archivo RESX

Lamentablemente no hay ningún editor RESX integrada en Visual Studio para Mac. Agregar nuevas cadenas traducibles requiere la adición de un nuevo XML `data` (elemento) para cada cadena. Cada `data` elemento puede contener lo siguiente:

* `name` atributo (obligatorio) es la clave para esta cadena se puede traducir. Debe ser un C# nombre de propiedad válido - por lo que no se permiten espacios ni caracteres especiales.
* `value` elemento (obligatorio), que es la cadena real que se muestra en la aplicación.
* `comment` elemento (opcional) puede contener instrucciones para el traductor que explica cómo se utiliza esta cadena.
* `xml:space` (opcional) para controlar la forma de conservar el espacio en la cadena de atributo.

Algunos ejemplos `data` elementos se muestran a continuación:

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

Tal y como se escribe la aplicación, se debe agregar cada fragmento de texto que se muestra al usuario en el archivo de recursos RESX base en una nueva `data` elemento. Se recomienda que incluya `comment`s tanto como sea posible para garantizar una traducción de alta calidad.

> [!NOTE]
> Visual Studio (incluida la edición Community gratis) contiene un editor básico de RESX. Si tiene acceso a un equipo de Windows, que puede ser una manera cómoda de agregar y editar cadenas en RESX (archivos).

#### <a name="language-specific-resources"></a>Recursos específicos del idioma

Por lo general, la traducción real de las cadenas de texto predeterminado no se realizará hasta que se han escrito grandes fragmentos de la aplicación (en cuyo caso el archivo RESX predeterminado contiene una gran cantidad de cadenas). Sigue siendo una buena idea agregar los recursos específicos del idioma al principio del ciclo de desarrollo, opcionalmente, rellenar con texto traducido para ayudar a probar el código de localización.

Se agrega un archivo RESX adicional para cada idioma que desee admitir.
Archivos de recursos específicos del idioma deben seguir una convención de nomenclatura específica: usar el mismo nombre de archivo, como los recursos de base de archivos (p. ej. **AppResources**) seguido por un punto (.) y, a continuación, el código de idioma. Se incluyen ejemplos sencillos:

* **AppResources.fr.resx** -francés las traducciones de idiomas.
* **AppResources.es.resx** -traducciones de español.
* **AppResources.de.resx** -traducciones de idioma alemán.
* **AppResources.ja.resx** -traducciones de idioma japonés.
* **AppResources.zh Hans.resx** - chino (simplificado) las traducciones de idiomas.
* **AppResources.zh Hant.resx** - chino (tradicional) las traducciones de idiomas.
* **AppResources.pt.resx** -las traducciones de idiomas portugués.
* **AppResources.pt BR.resx** -traducciones de portugués (Brasil).

El patrón general consiste en usar códigos de idioma de dos letras, pero hay algunos ejemplos (como el chino) donde se utiliza un formato diferente y otros ejemplos (por ejemplo, portugués de Brasil) donde se requiere un identificador de configuración regional de cuatro caracteres.

Estos archivos de recursos específicos del idioma *no* requieren un **. designer.cs** la clase parcial para que pueda agregar como archivos XML normales, con el **acción de compilación: EmbeddedResource**establecido. Esta captura de pantalla muestra una solución que contiene los archivos de recursos específicos del idioma:

![](localization-images/appresources-langs.png "Archivos de recursos específicos del idioma")

Cuando se desarrolla una aplicación y el archivo RESX base tiene texto agregado, debe enviarla a los traductores que se traducirán cada uno `data` elemento y devuelva un archivo de recursos específicos del idioma (mediante la convención de nomenclatura que se muestra) que se incluirá en la aplicación. A continuación, se muestran algunos ejemplos de 'traducción automática':

**AppResources.es.resx (español)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>Escribir un artículo</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

**AppResources.ja.resx (Japanese)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>新しい項目を追加</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

**AppResources.pt-BR.resx (Brazilian Portuguese)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>adicionar novo item</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

Solo el `value` elemento debe actualizarse el traductor - la `comment` no está pensado para ser traducido. Recuerde: cuando se editan archivos XML como escape caracteres reservados como `<`, `>`, `&` con `&lt;`, `&gt;`, y `&amp;` si aparecen en la `value` o `comment`.

<a name="incode" />

### <a name="using-resources-in-code"></a>Uso de recursos en el código

Las cadenas en los archivos de recursos RESX estará disponibles para su uso en el código de interfaz de usuario utilizando la `AppResources` clase. El `name` asignadas a cada cadena en el RESX archivo se convierte en una propiedad de dicha clase que puede hacer referencia en el código de Xamarin.Forms, tal y como se muestra a continuación:

```csharp
var myLabel = new Label ();
var myEntry = new Entry ();
var myButton = new Button ();
// populate UI with translated text values from resources
myLabel.Text = AppResources.NotesLabel;
myEntry.Placeholder = AppResources.NotesPlaceholder;
myButton.Text = AppResources.AddButton;
```

La interfaz de usuario en iOS y Android, la plataformas de Windows se representa como se esperaría, salvo que ahora es posible traducir la aplicación en varios idiomas porque se está cargando el texto desde un recurso en vez de estar codificado de forma rígida. Esta es una captura de pantalla que muestra la interfaz de usuario en cada plataforma antes de conversión:

![](localization-images/simple-example-english.png "Interfaces de usuario multiplataforma antes de traducción")

### <a name="troubleshooting"></a>Solución de problemas

#### <a name="testing-a-specific-language"></a>Pruebas de un idioma específico

Puede ser complicada cambiar el simulador o en un dispositivo a distintos idiomas, en particular durante el desarrollo cuando desea probar rápidamente referencias culturales diferentes.

Puede forzar un idioma específico que va a cargar estableciendo el `Culture` tal y como se muestra en este fragmento de código:

```csharp
// force a specific culture, useful for quick testing
AppResources.Culture =  new CultureInfo("fr-FR");
```

Este enfoque: establecer la referencia cultural directamente en la `AppResources` clase: también se puede usar para implementar un selector de idioma dentro de la aplicación (en lugar de utilizar la configuración regional del dispositivo).

#### <a name="loading-embedded-resources"></a>Cargar recursos incrustados

El siguiente fragmento de código es útil al intentar depurar problemas con los recursos incrustados (por ejemplo, los archivos RESX). Agregue este código a la aplicación (al principio del ciclo de vida de aplicación) y mostrará una lista de todos los recursos incrustados en el ensamblado, que muestra el identificador de recurso completo:

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

En el **AppResources.Designer.cs** archivo (alrededor de *línea 33*), toda la *el nombre del Administrador de recursos* se especifica (p. ej. `"UsingResxLocalization.Resx.AppResources"`) similar al código siguiente:

```csharp
System.Resources.ResourceManager temp =
        new System.Resources.ResourceManager(
                "UsingResxLocalization.Resx.AppResources",
                typeof(AppResources).GetTypeInfo().Assembly);
```

Compruebe el **salida de la aplicación** para los resultados del código de depuración se muestra arriba, para confirmar la correcta se enumerarán los recursos (es decir `"UsingResxLocalization.Resx.AppResources"`).

Si no es así, la `AppResources` clase será no se puede cargar sus recursos.
Compruebe lo siguiente para resolver los problemas que no se puede encontrar los recursos:

* El espacio de nombres predeterminado del proyecto coincide con el espacio de nombres raíz en el **AppResources.Designer.cs** archivo.
* Si el **AppResources.resx** archivo se encuentra en un subdirectorio, el nombre del subdirectorio debe formar parte del espacio de nombres *y* parte del identificador de recursos.
* El **AppResources.resx** archivo tiene **acción de compilación: EmbeddedResource**.
* El **Project Options > código fuente > .NET Naming Policies > nombres de recursos de estilo de usar Visual Studio** esté marcada. Deje esto si lo prefiere, sin embargo los espacios de nombres que se usa al hacer referencia a los recursos RESX tendrá que actualizarse a lo largo de la aplicación.

#### <a name="doesnt-work-in-debug-mode-android-only"></a>No funciona en modo de depuración (solo Android)

Si las cadenas traducidas están trabajando en las compilaciones de versión Android pero no durante la depuración, haga doble clic en el **proyecto Android** y seleccione **Opciones > compilar > compilar Android** y asegúrese de que el **Rápida implementación de ensamblado** no esté marcada. Esta opción provoca problemas con la carga de recursos y no debe usarse si va a probar aplicaciones localizadas.

### <a name="displaying-the-correct-language"></a>Mostrar el idioma correcto

Hasta ahora hemos examinado cómo escribir código para que pueden proporcionar traducciones, pero no para realmente que aparezcan. Código de Xamarin.Forms puede aprovechar. Recursos de red para cargar las traducciones de idioma correcta, pero es necesario consultar el sistema operativo en cada plataforma que determine el idioma seleccionado por el usuario.

Como parte del código específico de la plataforma es necesarios para obtener la preferencia de idioma del usuario, utilice un [servicio de dependencia](~/xamarin-forms/app-fundamentals/dependency-service/index.md) exponer esa información en la aplicación de Xamarin.Forms e implementar para cada plataforma.

En primer lugar, defina una interfaz para exponer el preferido referencia cultural del usuario, similar al código siguiente:

```csharp
public interface ILocalize
{
    CultureInfo GetCurrentCultureInfo ();
    void SetLocale (CultureInfo ci);
}
```

En segundo lugar, utilice la [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) en el Xamarin.Forms `App` clase para llamar a la interfaz y establezca nuestra cultura de recursos RESX en el valor correcto. Observe que no es necesario establecer manualmente este valor para Windows Phone y la plataforma Universal de Windows desde el marco de trabajo de recursos automáticamente reconoce el idioma seleccionado en estas plataformas.

```csharp
if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
{
    var ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
    Resx.AppResources.Culture = ci; // set the RESX for resource localization
    DependencyService.Get<ILocalize>().SetLocale(ci); // set the Thread for locale-aware methods
}
```

El recurso `Culture` debe establecerse cuando la aplicación carga por primera vez para que se usan las cadenas de idioma correcto. Si lo desea, puede actualizar este valor según los eventos específicos de la plataforma que se pueden generar en iOS o Android si el usuario actualiza sus preferencias de idioma mientras se ejecuta la aplicación.

Las implementaciones para la `ILocalize` interfaz se muestran en la [código específico de la plataforma](#Platform-specific_Code) sección más adelante. Estas implementaciones aprovechar estas características `PlatformCulture` clase auxiliar:

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

El código para detectar qué idioma debe mostrar debe ser específica de la plataforma porque iOS, Android y las plataformas Windows exponen esta información de forma ligeramente diferente. El código para el `ILocalize` servicio de dependencia es proporcionados a continuación para cada plataforma, junto con los requisitos específicos de la plataforma adicionales para garantizar el texto localizado se representa correctamente.

El código específico de la plataforma también debe controlar casos donde el sistema operativo permite al usuario configurar un identificador de configuración regional que no es compatible con. De NET `CultureInfo` clase. En estos casos, se debe escribir código personalizado para detectar configuraciones regionales no compatibles y sustituya la mejor. Configuración regional compatibles con NET.

#### <a name="ios-application-project"></a>Proyecto de aplicación de iOS

los usuarios de iOS seleccionan su idioma preferido por separado de fecha y hora al formato de la referencia cultural. Para cargar los recursos correctos para localizar una aplicación de Xamarin.Forms solo tenemos que consultar la `NSLocale.PreferredLanguages` matriz para el primer elemento.

La siguiente implementación de la `ILocalize` debe estar colocado el servicio de dependencia en el proyecto de aplicación de iOS. Dado que iOS utiliza caracteres de subrayado en lugar de guiones (que es la representación estándar. NET) el código reemplaza el carácter de subrayado antes de crear instancias de la `CultureInfo` clase:

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
                case "ms-SG":   // "Malaysian (Singapore)" not supported .NET culture
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
> El `try/catch` bloques en el `GetCurrentCultureInfo` método imitan el comportamiento de reserva normalmente se utiliza con especificadores de configuración regional: si la coincidencia exacta no se encuentra, busque una coincidencia cerrar simplemente tomando como base el lenguaje (primer bloque de caracteres en la configuración regional).
>
> En el caso de Xamarin.Forms, algunas configuraciones regionales son válidos en iOS, pero no se corresponden con válido `CultureInfo` en .NET y el código anterior intenta controlar esto.
>
> Por ejemplo, el archivo iOS **configuración > General lenguaje &amp; región** pantalla le permite establecer el teléfono **lenguaje** a **inglés** pero la  **Región** a **España**, que da como resultado una cadena de configuración regional de `"en-ES"`. Cuando el `CultureInfo` error en la creación, el código vuelve a utilizar solo las dos primeras letras para seleccionar el idioma para mostrar.
>
> Los desarrolladores deben modificar la `iOSToDotnetLanguage` y `ToDotnetFallbackLanguage` métodos para controlar los casos específicos necesarios para los idiomas admitidos.


Hay algunos elementos de interfaz de usuario definidos por el sistema que se convierten automáticamente por iOS, como el **realiza** situado en el `Picker` control. Para forzar iOS para traducir estos elementos es necesario para indicar qué idiomas se admiten en el **Info.plist** archivo. Puede agregar estos valores a través de **Info.plist > origen** tal y como se muestra aquí:

![Las claves de localización en Info.plist](localization-images/info-plist.png "claves de localización Info.plist")

Como alternativa, abra el **Info.plist** de archivos en un editor XML y editar directamente los valores:

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
> Tenga en cuenta que se trata de Apple portugués manera ligeramente diferente que se esperaría.
> De [sus documentos](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW2): _"usar pt como el identificador de idioma para portugués tal como se utiliza en Brasil y pt-PT como el identificador de idioma para portugués tal como se utiliza en Portugal"_.
> Esto significa que cuando se elige portuguesa en una configuración regional no estándar, el idioma de reserva será portugués brasileño en iOS, a menos que se escribe código para cambiar este comportamiento (como el `ToDotnetFallbackLanguage` anteriormente).

#### <a name="android-application-project"></a>Proyecto de aplicación de Android

Android expone la configuración regional seleccionada actualmente a través de `Java.Util.Locale.Default`y también se utiliza un separador de carácter de subrayado en lugar de un guión (que será sustituido por el código siguiente). Agregue esta implementación de servicio de dependencia para el proyecto de aplicación de Android:

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
> El `try/catch` bloques en el `GetCurrentCultureInfo` método imitan el comportamiento de reserva normalmente se utiliza con especificadores de configuración regional: si la coincidencia exacta no se encuentra, busque una coincidencia cerrar simplemente tomando como base el lenguaje (primer bloque de caracteres en la configuración regional).
>
> En el caso de Xamarin.Forms, algunas configuraciones regionales son válidos en Android pero no se corresponden con válido `CultureInfo` en .NET y el código anterior intenta controlar esto.
>
> Los desarrolladores deben modificar la `iOSToDotnetLanguage` y `ToDotnetFallbackLanguage` métodos para controlar los casos específicos necesarios para los idiomas admitidos.


Cuando este código se haya agregado al proyecto de aplicación de Android, podrá mostrar automáticamente las cadenas traducidas.

> [!NOTE]
>️ **advertencia:** si las cadenas traducidas están trabajando en las compilaciones de versión Android pero no durante la depuración, haga doble clic en el **proyecto Android** y seleccione **Opciones > compilar > Android Generar** y asegúrese de que el **rápida implementación de ensamblado** no esté marcada. Esta opción provoca problemas con la carga de recursos y no debe usarse si va a probar aplicaciones localizadas.

#### <a name="windows-application-projects"></a>Proyectos de aplicación de Windows

Proyectos de Windows 8.1 y plataforma Universal de Windows (UWP) no requieren el servicio de dependencia: estas plataformas establecen automáticamente referencia cultural del recurso correctamente.

Implementar la extensión de marcado XAML se describe más adelante en este documento puede requerir la `ILocalize` implementación se muestra a continuación para Windows Phone.

##### <a name="windows-phone-80"></a>Windows Phone 8.0

Aunque no se utiliza en el `App` de clases, aquí es la implementación de Windows Phone para el `ILocalize` servicio de dependencia. Agregar esta clase al proyecto de aplicación de Windows Phone; será necesario si implementa la extensión de marcado XAML que se describen más adelante:

```csharp
[assembly: Dependency(typeof(UsingResxLocalization.WinPhone.Localize))]

namespace UsingResxLocalization.WinPhone
{
    public class Localize : UsingResxLocalization.ILocalize
    {
        public void SetLocale (CultureInfo ci) { }
        public System.Globalization.CultureInfo GetCurrentCultureInfo ()
        {
            return System.Threading.Thread.CurrentThread.CurrentUICulture;
        }
    }
}

```

Proyectos de Windows Phone 8.0 deben configurarse correctamente para que el texto localizado que se mostrará.
Se deben seleccionar idiomas admitidos en las opciones de proyecto *y* el **WMAppManifest.xml** archivos.
Si esta configuración no se actualiza los recursos localizados de RESX no se cargará.

##### <a name="project-options"></a>Opciones de proyecto

Haga doble clic en el proyecto de Windows Phone y seleccione **propiedades**. En el **aplicación** pestaña graduación el **admite referencias culturales** que admite la aplicación:

[ ![](localization-images/winphone-projectproperties-sml.png "Proyectar las propiedades - referencias culturales admitidas")](localization-images/winphone-projectproperties.png "proyectar las propiedades - referencias culturales admitidas")

##### <a name="wmappmanifestxml"></a>WMAppManifest.xml

Expanda el nodo de propiedades en el proyecto de Windows Phone y haga doble clic en el **WMAppManifest.xml** archivo. Haga clic en el **empaquetado** y a todos los idiomas admitidos por la aplicación de graduación.

[ ![](localization-images/winphone-wmappmanifest-sml.png "Idiomas admitidos de WMAppManifest.xml -")](localization-images/winphone-wmappmanifest.png "WMAppManifest.xml - idiomas admitidos")

##### <a name="assemblyinfocs"></a>AssemblyInfo.cs

Expanda el nodo de propiedades en el proyecto de biblioteca de clases portables (PCL) y haga doble clic en el **AssemblyInfo.cs** archivo. Agregue la siguiente línea al archivo para establecer el lenguaje de ensamblado de recursos neutrales en inglés:

```csharp
[assembly: NeutralResourcesLanguage("en")]
```

Este modo, informa el Administrador de recursos de referencia cultural predeterminada de la aplicación, por lo tanto, lo que asegura que las cadenas definidas en el archivo de idioma neutro RESX (**AppResources.resx**) se mostrará cuando la aplicación ejecuta en una de las configuraciones regionales de inglés.

### <a name="example"></a>Ejemplo

Después de actualizar los proyectos específicos de la plataforma como se muestra anteriores y volver a compilar la aplicación con archivos RESX traducidos, traducciones actualizadas estará disponibles en cada aplicación. Esta es una captura de pantalla desde el código de ejemplo que se traducen en chino simplificado:

![](localization-images/simple-example-hans.png "Interfaces de usuario multiplataforma traducidos a chino simplificado")

## <a name="localizing-xaml"></a>Localizar XAML

Al crear una interfaz de usuario de Xamarin.Forms en XAML en el marcado tendría un aspecto similar al siguiente, con cadenas de incrustarse directamente en el código XML:

```xaml
<Label Text="Notes:" />
<Entry Placeholder="eg. buy milk" />
<Button Text="Add to list" />
```

Idealmente, se pueden traducir los controles de interfaz de usuario directamente en el XAML, lo que podemos hacer mediante la creación de un *extensión de marcado*. Se muestra el código para una extensión de marcado que expone los recursos RESX en XAML. Esta clase debe agregarse en el código común de Xamarin.Forms (junto con las páginas XAML):

```csharp
using System;
using System.Globalization;
using System.Reflection;
using System.Resources;
using Xamarin.Forms;
using Xamarin.Forms.Xaml;

namespace UsingResxLocalization
{
    // You exclude the 'Extension' suffix when using in Xaml markup
    [ContentProperty ("Text")]
    public class TranslateExtension : IMarkupExtension
    {
        readonly CultureInfo ci;
        const string ResourceId = "UsingResxLocalization.Resx.AppResources";

        private static readonly Lazy<ResourceManager> ResMgr = new Lazy<ResourceManager>(()=> new ResourceManager(ResourceId
                                                                                                                  , typeof(TranslateExtension).GetTypeInfo().Assembly));

        public TranslateExtension()
        {
            if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
            {
                ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
            }
        }

        public string Text { get; set; }

        public object ProvideValue (IServiceProvider serviceProvider)
        {
            if (Text == null)
                return "";

            var translation = ResMgr.Value.GetString(Text, ci);

            if (translation == null)
            {
                #if DEBUG
                throw new ArgumentException(
                    String.Format("Key '{0}' was not found in resources '{1}' for culture '{2}'.", Text, ResourceId, ci.Name),
                    "Text");
                #else
                translation = Text; // returns the key, which GETS DISPLAYED TO THE USER
                #endif
            }
            return translation;
        }
    }
}
```

Las viñetas siguientes explican los elementos importantes en el código anterior:

* La clase se denomina `TranslateExtension`, pero por convención, podemos hacer referencia a es como **Translate** en nuestra marcado.
* La clase implementa `IMarkupExtension`, que requiere Xamarin.Forms para funcione.
* `"UsingResxLocalization.Resx.AppResources"` es el identificador de recurso para nuestros recursos RESX. Está compuesto de nuestro espacio de nombres predeterminado, la carpeta donde se encuentran los archivos de recursos y el nombre de archivo predeterminado RESX.
* El `ResourceManager` clase se crea mediante `typeof(TranslateExtension)` para determinar el ensamblado actual para cargar recursos.
* `ci` usa el servicio de dependencia para obtener el idioma del usuario seleccionado desde el sistema operativo nativo.
* `GetString` es el método que recupera la cadena traducida real de los archivos de recursos. En Windows Phone 8.1 y la plataforma Universal de Windows, `ci` será null porque el `ILocalize` interfaz no está implementada en estas plataformas. Esto equivale a llamar a la `GetString` método con solo el primer parámetro. En su lugar, el marco de trabajo de recursos reconocerá automáticamente la configuración regional y recuperará la cadena traducida desde el archivo RESX adecuado.
* Control de errores se ha incluido para facilitar la depuración de recursos que faltan iniciando una excepción (en `DEBUG` sólo en modo).

El fragmento de código XAML siguiente muestra cómo utilizar la extensión de marcado. Hay dos pasos para que funcione:

1. Declarar personalizado `xmlns:i18n` espacio de nombres en el nodo raíz. El `namespace` y `assembly` debe coincidir con la configuración del proyecto exactamente - en este ejemplo se son idénticas, pero podría ser diferentes en el proyecto.
2. Use `{Binding}` sintaxis en los atributos que normalmente se suele contener el texto que se va a llamar a la `Translate` extensión de marcado. La clave de recurso es el único parámetro necesario.

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

Aunque se puede controlar la traducción de la interfaz de usuario en el código de Xamarin.Forms, hay algunos elementos que deben realizarse en cada proyecto específico de la plataforma. Esta sección explica cómo localizar:

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

También se hace referencia a la imagen de marca en el código XAML similar al siguiente:

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

Todas las plataformas resolverá automáticamente las referencias de imagen como los anteriores a las versiones localizadas de las imágenes, siempre y cuando se implementan las estructuras de proyecto que se explica a continuación.

### <a name="ios-application-project"></a>Proyecto de aplicación de iOS

iOS usa una denominación estándar que se denominan proyectos de localización o **.lproj** directorios que contienen recursos de cadena e imagen. Estos directorios pueden contener las versiones localizadas de imágenes que se utilizan en la aplicación así como el **InfoPlist.strings** archivo que se puede utilizar para localizar el nombre de la aplicación.

#### <a name="images"></a>Imágenes

Esta captura de pantalla muestra la aplicación de ejemplo de iOS con específicos del idioma **.lproj** directorios. El directorio español llama **es.lproj**, contiene las versiones localizadas de la imagen predeterminada, así como **flag.png**:

![](localization-images/ios-resources.png "iOS directorios del proyecto de localización")

Cada directorio de lenguaje contiene una copia de **flag.png**localizado para dicho idioma. Si no se proporciona ninguna imagen, el sistema operativo predeterminada será la imagen en el directorio de idioma predeterminado. Para obtener soporte de Retina completa, también debe proporcionar  **@2x**  y  **@3x**  copias de cada imagen.

#### <a name="app-name"></a>Nombre de la aplicación

El contenido de la **InfoPlist.strings** es simplemente un único clave y valor para configurar el nombre de la aplicación:

```csharp
"CFBundleDisplayName" = "ResxEspañol";
```

Cuando se ejecuta la aplicación, el nombre de la aplicación y la imagen son ambos se encuentra:

![](localization-images/ios-imageicon.png "Texto de la aplicación de ejemplo y la localización de la imagen de iOS")

### <a name="android-application-project"></a>Proyecto de aplicación de Android

Android sigue un esquema diferente para almacenar imágenes localizadas diferente **pueden dibujar** y **cadenas** directorios con un sufijo de código de idioma. Cuando un código de configuración regional de cuatro letras se requiere (por ejemplo, zh-TW o pt-BR), tenga en cuenta que requiere más Android **r** siguientes dash/anterior el código de configuración regional (p. ej. zh-rTW o rBR pt).

#### <a name="images"></a>Imágenes

Esta captura de pantalla muestra la Android ejemplo con un algunos localizado drawables y cadenas:

![](localization-images/android-resources.png "Android adaptado Drawables y directorios de cadena")

Tenga en cuenta que Android no usar zh-Hans y zh-Hant códigos simplificado y chino tradicional; en su lugar, solo admite los códigos de país específicos zh-CN y zh-TW.

Para admitir imágenes con una resolución diferente para las pantallas de alta densidad, crear carpetas de idioma adicionales con `-*dpi` sufijos, como **drawables-es-mdpi**, **drawables-es-xdpi**, **drawables-es-xxdpi**, etcetera. Vea [recursos Android alternativa proporcionar](http://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources) para obtener más información.

#### <a name="app-name"></a>Nombre de la aplicación

El contenido de la **strings.xml** es simplemente un único clave y valor para configurar el nombre de la aplicación:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">ResxEspañol</string>
</resources>
```

Actualización de la **MainActivity.cs** en el proyecto de aplicación de Android para que la `Label` hace referencia a las cadenas XML.

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

La aplicación ahora localiza la imagen y el nombre de la aplicación. Esta es una captura de pantalla del resultado (en español):

![](localization-images/android-imageicon.png "Texto de la aplicación de ejemplo de Android y la localización de imagen")

### <a name="windows-phone-80-application-project"></a>Proyecto de aplicación de Windows Phone 8.0

Windows Phone no tiene integrada de manera sencilla de seleccionar una imagen localizada específica ni para localizar el nombre de la aplicación.

#### <a name="images"></a>Imágenes

Para evitar esta limitación en el ejemplo proporciona una sugerencia de cómo se podría implementar adaptado a la carga de imágenes mediante un [representador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) para el `Image` control.

Se muestra el código de representador personalizado - si el origen es un `FileImageSource` , a continuación, se extrae el nombre de archivo y crea una ruta de acceso a una imagen localizado mediante el `CurrentUICulture`. Algunos idiomas requieren un tratamiento especial para que las reservas funcionan según lo esperado; en el ejemplo, el valor predeterminado es usar solo el código de idioma de dos letras excepto en algunos casos especiales:

```csharp
using System.IO;
using Xamarin.Forms;
using Xamarin.Forms.Platform.WinPhone;

[assembly: ExportRenderer(typeof(Image), typeof(UsingResxLocalization.WinPhone.LocalizedImageRenderer))]
namespace UsingResxLocalization.WinPhone
{
    public class LocalizedImageRenderer : ImageRenderer
    {
        protected override void OnElementChanged(ElementChangedEventArgs<Image> e)
        {
            base.OnElementChanged(e);

            if (e.NewElement != null)
            {
                var s = e.NewElement.Source as FileImageSource;
                if (s != null)
                {
                    var fileName = s.File;
                    string ci = System.Threading.Thread.CurrentThread.CurrentUICulture.ToString();
                    // you might need some custom logic here to support particular cultures and fallbacks
                    if (ci == "pt-BR") {
                        // use the complete string 'as is'
                    } else if (ci == "zh-CN") {
                         // we could have named the image directories differently,
                         // but this keeps them consisent with RESX file naming
                        ci = "zh-Hans";
                    } else if (ci == "zh-TW" || ci == "zh-HK") {
                        ci = "zh-Hant";
                    } else {
                        // for all others, just use the two-character language code
                        ci = System.Threading.Thread.CurrentThread.CurrentUICulture.TwoLetterISOLanguageName;
                    }
                    e.NewElement.Source = Path.Combine("Assets/" + ci + "/" + fileName);
                }
            }
        }
    }
}
```

Este código funciona con las imágenes localizadas en la estructura de directorios que se muestra a continuación. Le recomienda para modificar el código para satisfacer sus necesidades de localización específica (por ejemplo, control de configuraciones regionales más específicas y pasar cuando las imágenes no están disponibles):

![](localization-images/winphone-resources.png "Estructura de directorios de imágenes de localizada de Windows Phone")

Ahora, Windows Phone localiza la imagen. Esta es una captura de pantalla del resultado (en español y chino simplificado):

![](localization-images/winphone-image-sml.png "Texto de aplicación de ejemplo de Windows Phone y la localización de imagen")

#### <a name="app-name"></a>Nombre de la aplicación

Consulte la documentación de Microsoft para [localizar el título de la aplicación de Windows Phone 8.0](http://msdn.microsoft.com/library/windows/apps/ff967550(v=vs.105).aspx).

### <a name="windows-phone-81-and-universal-windows-platform-application-projects"></a>Windows Phone 8.1 y Universal Windows proyectos de aplicación de plataforma

La plataforma Universal de Windows y Windows Phone 8.1 ambas poseen una infraestructura de recurso que simplifica la localización de imágenes y el nombre de la aplicación.

#### <a name="images"></a>Imágenes

Las imágenes se pueden localizar colocándolos en una carpeta específica del recurso, como se muestra en la captura de pantalla siguiente:

![](localization-images/uwp-image-folder-structure.png "Windows Phone 8.1 y estructura de carpetas de localización de imagen UWP")

En tiempo de ejecución, la infraestructura de recursos de Windows seleccionará la imagen adecuada en función de la configuración regional del usuario.

#### <a name="app-name"></a>Nombre de la aplicación

Consulte la documentación de Microsoft para [aplicaciones de la tienda de Windows 8.1: localizar la información que describe la aplicación a los usuarios](https://msdn.microsoft.com/library/windows/apps/hh454044.aspx) y [cargar las cadenas del manifiesto de aplicación](https://msdn.microsoft.com/library/windows/apps/xaml/hh965323.aspx#loading_strings_from_the_app_manifest.).

## <a name="summary"></a>Resumen

Aplicaciones de Xamarin.Forms se pueden localizar con RESX (archivos) y las clases de globalización. NET. Además de una pequeña cantidad de código específico de plataforma para detectar el idioma que prefiera el usuario, la mayoría del trabajo de localización está centralizada en el código común.

Por lo general se tratan las imágenes de una manera específica de la plataforma para aprovechar las ventajas de la compatibilidad de la resolución proporcionada en iOS y Android. Windows Phone requiere código personalizado para localizar las imágenes de un modo sencillo de plataforma de entre; código de ejemplo se proporciona para agregar esta funcionalidad.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de localización RESX](https://developer.xamarin.com/samples/xamarin-forms/UsingResxLocalization/)
- [Aplicación de ejemplo de TodoLocalized](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalized/)
- [Localización de multiplataforma](~/cross-platform/app-fundamentals/localization.md)
- [Localización de iOS](~/ios/app-fundamentals/localization/index.md)
- [Localización de Android](~/android/app-fundamentals/localization.md)
- [Uso de la clase CultureInfo (MSDN)](http://msdn.microsoft.com/library/87k6sx8t%28v=vs.90%29.aspx)
- [Buscar y utilizar recursos para una referencia cultural concreta (MSDN)](http://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
