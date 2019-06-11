---
title: Acceso al sistema de archivos de Xamarin.iOS
description: Este documento describe cómo trabajar con el sistema de archivos de Xamarin.iOS. Describe los directorios, leer los archivos, la serialización XML y JSON, el espacio aislado de aplicación, uso compartido de archivos a través de iTunes y mucho más.
ms.prod: xamarin
ms.assetid: 37DF2F38-901E-8F8E-269A-5EE0CCD28C08
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/12/2018
ms.openlocfilehash: 10f4f53e71a47521076538bf9eb12b86c1e478a6
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827510"
---
# <a name="file-system-access-in-xamarinios"></a>Acceso al sistema de archivos de Xamarin.iOS

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/monotouch/FileSystemSampleCode/)

Puede usar Xamarin.iOS y `System.IO` clases en el *biblioteca de clases Base (BCL) de .NET* para tener acceso al sistema de archivos de iOS. La clase `File` le permite crear, eliminar y leer archivos, mientras que la clase `Directory` le permite crear, eliminar o enumerar el contenido de directorios. También puede usar `Stream` subclases, lo que pueden proporcionar un mayor grado de control sobre las operaciones de archivo (por ejemplo, la búsqueda de compresión o posición dentro de un archivo).

iOS impone algunas restricciones sobre lo que puede hacer una aplicación con el sistema de archivos para conservar la seguridad de datos de la aplicación y para proteger a los usuarios de aplicaciones malignos. Estas restricciones forman parte de la *espacio aislado de la aplicación* : un conjunto de reglas que limita el acceso de la aplicación a los archivos, las preferencias, los recursos de red, hardware, etcetera. Una aplicación se limita a leer y escribir archivos en su directorio particular (ubicación de instalación); no se puede tener acceso a archivos de la otra aplicación.

iOS también tiene algunas características específicas del sistema de archivos: ciertos directorios requieren un tratamiento especial con respecto a las copias de seguridad y las actualizaciones y las aplicaciones también pueden compartir archivos entre sí y el **archivos** app (desde iOS 11) y a través de iTunes.

En este artículo se describe las características y restricciones de iOS sistema de archivos e incluye una aplicación de ejemplo que muestra cómo usar Xamarin.iOS para ejecutar algunas operaciones de sistema de archivos simple:

[![Un ejemplo de ejecución de algunas operaciones de sistema de archivos simple de iOS](file-system-images/01-sampleapp-sml.png)](file-system-images/01-sampleapp.png#lightbox)

## <a name="general-file-access"></a>Acceso de archivos generales

Xamarin.iOS permite usar .NET `System.IO` clases para las operaciones de sistema de archivos en iOS.

Los fragmentos de código siguientes muestran algunas operaciones de archivo comunes. Encontrará ellas todo a continuación, en el **SampleCode.cs** archivo, en la aplicación de ejemplo para este artículo.

### <a name="working-with-directories"></a>Trabajar con directorios

Este código enumera los subdirectorios del directorio actual (especificado por el ". /" parámetro), que es la ubicación del archivo ejecutable de aplicación.
El resultado será una lista de todos los archivos y carpetas que se implementan con la aplicación (se muestra en la ventana de consola durante la depuración).

```csharp
var directories = Directory.EnumerateDirectories("./");
foreach (var directory in directories) {
      Console.WriteLine(directory);
}
```

### <a name="reading-files"></a>Leer archivos

Para leer un archivo de texto, solo se necesita una sola línea de código. En este ejemplo se mostrará el contenido de un archivo de texto en la ventana de salida de la aplicación.

```csharp
var text = File.ReadAllText("TestData/ReadMe.txt");
Console.WriteLine(text);
```

### <a name="xml-serialization"></a>serialización XML

Aunque trabajar con la completa `System.Xml` espacio de nombres está fuera del ámbito de este artículo, fácilmente puede deserializar un documento XML desde el sistema de archivos mediante el uso de StreamReader como este fragmento de código:

```csharp
using (TextReader reader = new StreamReader("./TestData/test.xml")) {
      XmlSerializer serializer = new XmlSerializer(typeof(MyObject));
      var xml = (MyObject)serializer.Deserialize(reader);
}
```

Para obtener más información, consulte la documentación de [System.Xml](xref:System.Xml) y [serialización](xref:System.Xml.Serialization). Consulte la [Xamarin.iOS documentación](~/ios/deploy-test/linker.md) en el vinculador: a menudo necesitará agregar la `[Preserve]` atributo a clases que se va a serializar.

### <a name="creating-files-and-directories"></a>Crear archivos y directorios

En este ejemplo se muestra cómo usar el `Environment` clase para tener acceso a la carpeta documentos donde podemos crear archivos y directorios.

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments); 
var filename = Path.Combine (documents, "Write.txt");
File.WriteAllText(filename, "Write this text into a file");
```

Creación de un directorio es un proceso similar:

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var directoryname = Path.Combine (documents, "NewDirectory");
Directory.CreateDirectory(directoryname);
```

Para obtener más información, consulte el [referencia de API de System.IO](xref:System.IO).

### <a name="serializing-json"></a>Serialización de JSON

[Json.NET](http://www.newtonsoft.com/json) es un marco JSON de alto rendimiento que funciona con Xamarin.iOS y está disponible en NuGet. Agregue el paquete NuGet para la aplicación del proyecto, mediante **agregar NuGet** en Visual Studio para Mac:

[![](file-system-images/json01.png "Agregar el paquete NuGet al proyecto de aplicaciones")](file-system-images/json01.png#lightbox)

A continuación, agregue una clase para que actúe como el modelo de datos para la serialización/deserialización (en este caso `Account.cs`):

```csharp
using System;
using System.Collections.Generic;
using Foundation; // for Preserve attribute, which helps serialization with Linking enabled

namespace FileSystem
{
    [Preserve]
    public class Account
    {
        public string Email { get; set; }
        public bool Active { get; set; }
        public DateTime CreatedDate { get; set; }
        public List<string> Roles { get; set; }

        public Account() {
        }
    }
}
```

Por último, cree una instancia de la `Account` clase, serializarlo en datos json y escribirlo en un archivo:

```csharp
// Create a new record
var account = new Account(){
    Email = "monkey@xamarin.com",
    Active = true,
    CreatedDate = new DateTime(2015, 5, 27, 0, 0, 0, DateTimeKind.Utc),
    Roles = new List<string> {"User", "Admin"}
};

// Serialize object
var json = JsonConvert.SerializeObject(account, Newtonsoft.Json.Formatting.Indented);

// Save to file
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "account.json");
File.WriteAllText(filename, json);
```

Para obtener más información sobre cómo trabajar con datos json en una aplicación. NET, consulte la sección Json .NET [documentación](http://www.newtonsoft.com/json/help).

## <a name="special-considerations"></a>Consideraciones especiales

A pesar de las similitudes entre las operaciones de archivos de Xamarin.iOS y. NET, iOS y Xamarin.iOS difieren de .NET en algunos aspectos importantes.

### <a name="making-project-files-accessible-at-runtime"></a>Hacer que los archivos de proyecto puede tener acceso en tiempo de ejecución

De forma predeterminada, si agrega un archivo al proyecto, no se incluirán en el ensamblado final y, por tanto, no estará disponible para su aplicación. Con el fin de incluir un archivo en el ensamblado, se debe marcar con una acción de compilación especial, denominada contenido.

Para marcar un archivo de inclusión, haga doble clic en los archivos y elija **acción de compilación &gt; contenido** en Visual Studio para Mac. También puede cambiar el **acción de compilación** en el archivo **propiedades** hoja.

### <a name="case-sensitivity"></a>Distinción de mayúsculas y minúsculas

Es importante comprender que el sistema de archivos iOS *distingue mayúsculas de minúsculas*. Minúsculas significa que los nombres de archivo y directorio deben coincidir exactamente – **README.txt** y **readme.txt** se consideraría distintos nombres de archivo.

Esto podría resultar confuso para los desarrolladores de .NET que están más familiarizados con el sistema de archivos de Windows, que es *entre mayúsculas y minúsculas* – **archivos**, **archivos**, y  **archivos** haría referencia al mismo directorio.

> [!WARNING]
> IOS Simulator no es distingue mayúsculas de minúsculas.
> Si el nombre de archivo de mayúsculas y minúsculas es diferente entre el propio archivo y las referencias a él en el código, el código todavía podría funcionar en el simulador, pero se producirá un error en un dispositivo real. Esta es una de las razones por las es importante implementar y probar en un dispositivo real temprana y con frecuencia durante el desarrollo de iOS.

### <a name="path-separator"></a>Separador de ruta de acceso

iOS usa la barra diagonal '/' como separador de ruta de acceso (que es diferente de Windows, que usa la barra diagonal inversa ' \').

Debido a esta diferencia confusa, es recomendable utilizar el `System.IO.Path.Combine` método, que se ajusta para la plataforma actual, en lugar de codificar un separador de ruta de acceso determinada. Se trata de un simple paso por el código más portable a otras plataformas.

## <a name="application-sandbox"></a>Espacio aislado de la aplicación

Acceso de la aplicación para el sistema de archivos (y otros recursos como las características de hardware y red) está limitado por motivos de seguridad. Esta restricción se conoce como el *espacio aislado de la aplicación*. En el sistema de archivos, términos de la aplicación se limita a crear y eliminar archivos y directorios en su directorio particular.

El directorio particular es una ubicación única en el sistema de archivos donde se almacenan la aplicación y todos sus datos. No puede elegir (ni cambiar) la ubicación del directorio principal para su aplicación; Sin embargo iOS y Xamarin.iOS proporcionan propiedades y métodos para administrar los archivos y directorios dentro.

## <a name="the-application-bundle"></a>El paquete de aplicación

El *agrupación de aplicaciones* es la carpeta que contiene la aplicación.
Se diferencia de otras carpetas al tener el sufijo de archivo .app agregado al nombre de directorio. El paquete de aplicación contiene el archivo ejecutable y todo el contenido (archivos, imágenes, etc.) necesario para el proyecto.

Cuando vaya a la agrupación de aplicaciones en Mac OS, aparece con un icono diferente que verá en otros directorios (y el **.app** sufijo está oculto); sin embargo, es simplemente un directorio normal que muestra el sistema operativo de forma diferente.

Para ver el paquete de aplicación para el código de ejemplo, haga doble clic en el proyecto en **Visual Studio para Mac** y seleccione **mostrar en Finder**. A continuación, navegue hasta el **bin /** directorio donde debe encontrar un icono de aplicación (similar a la siguiente captura de pantalla).

![Navegar por el directorio bin para encontrar un icono de aplicación similar a esta captura de pantalla](file-system-images/40-bundle.png)

Haga doble clic en este icono y elija **mostrar contenido del paquete** para examinar el contenido del directorio de la agrupación de aplicaciones. El contenido aparece igual que el contenido de un directorio regular, como se muestra aquí:

[![El contenido del lote de aplicaciones](file-system-images/45-bundle-sml.png)](file-system-images/45-bundle.png#lightbox)

El paquete de aplicación es lo que está instalado en el simulador o en el dispositivo durante las pruebas y, en última instancia, es lo que se envía a Apple para su inclusión en la aplicación de Store.

## <a name="application-directories"></a>Directorios de la aplicación

Cuando se instala la aplicación en un dispositivo, el sistema operativo crea un directorio principal para la aplicación y crea un número de directorios en el directorio raíz de aplicación que están disponibles para su uso. Desde iOS 8, los directorios accesibles para el usuario son [no encuentra](https://developer.apple.com/library/ios/technotes/tn2406/_index.html) dentro de la raíz de la aplicación, por lo que no se puede derivar las rutas de acceso para el paquete de aplicación desde los directorios de usuario, o viceversa.

Estos directorios, cómo determinar su ruta de acceso y sus fines se enumeran a continuación:

&nbsp;

|Directorio|Descripción|
|---|---|
|[ApplicationName].app/|**En iOS 7 y versiones anteriores**, se trata la `ApplicationBundle` directorio donde se almacena el archivo ejecutable de la aplicación. La estructura de directorios que se crea en la aplicación existe en este directorio (por ejemplo, imágenes y otros tipos de archivo que se han marcado como recursos en Visual Studio para proyectos de Mac).<br /><br />Si necesita tener acceso a los archivos de contenido dentro de la agrupación de aplicaciones, la ruta de acceso a este directorio está disponible a través de la `NSBundle.MainBundle.BundlePath` propiedad.|
|Documentos /|Utilice este directorio para almacenar documentos de usuario y archivos de datos de aplicación.<br /><br />El contenido de este directorio puede estar disponible para el usuario a través de iTunes de archivos compartidos (aunque esta opción está deshabilitada de forma predeterminada). Agregar un `UIFileSharingEnabled` clave booleana en el archivo Info.plist para permitir que los usuarios accedan a estos archivos.<br /><br />Incluso si una aplicación inmediatamente no permite el uso compartido de archivos, debe evitar colocar los archivos que deben ocultarse de los usuarios en este directorio (como los archivos de base de datos, a menos que desee compartirlos). Siempre que permanezcan ocultos archivos confidenciales, estos archivos no se exponen (y potencialmente movidos, modificadas o eliminadas por iTunes) si el uso compartido de archivos está habilitado en una versión futura.<br /><br /> Puede usar el `Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments)` método para obtener la ruta de acceso al directorio de documentos para la aplicación.<br /><br />El contenido de este directorio estén respaldado por iTunes.|
|Biblioteca /|El directorio de bibliotecas es un buen lugar para almacenar los archivos que no se crean directamente por el usuario, como las bases de datos u otros archivos generados por la aplicación. El contenido de este directorio nunca se expone al usuario a través de iTunes.<br /><br />Puede crear sus propios subdirectorios en la biblioteca; Sin embargo, hay ya algunos creado por el sistema directorios aquí que debe ser consciente de, incluidas las preferencias y las memorias caché.<br /><br />El contenido de este directorio (excepto el subdirectorio cachés) estén respaldado por iTunes. Se copiarán los directorios personalizados que cree en la biblioteca.|
|Biblioteca/Preferencias /|Archivos de preferencias específicas de la aplicación se almacenan en este directorio. No cree directamente estos archivos. En su lugar, use la `NSUserDefaults` clase.<br /><br />El contenido de este directorio estén respaldado por iTunes.|
|Cachés/biblioteca /|El directorio de las memorias caché es un buen lugar para almacenar archivos de datos que pueden ayudar a la aplicación se ejecute, pero se puede volver a crear fácilmente. La aplicación debe crear y eliminar estos archivos según sea necesario y poder volver a crear estos archivos si es necesario. iOS 5 también puede eliminar estos archivos (en situaciones de almacenamiento baja), pero no lo hará mientras se ejecuta la aplicación.<br /><br />El contenido de este directorio no estén respaldado por iTunes, lo que significa que no estará presentes si el usuario restaura un dispositivo, y que no estén presentes después de instala una versión actualizada de la aplicación.<br /><br />Por ejemplo, en caso de que la aplicación no se puede conectar a la red, podría usar el directorio de caché para almacenar archivos de datos o para proporcionar una buena experiencia sin conexión. La aplicación puede guardar y recuperar estos datos rápidamente mientras espera respuestas de red, pero no necesita una copia de seguridad y puede fácilmente puede recuperar o volver a crear después de actualizar una versión o restauración.|
|tmp/|Las aplicaciones pueden almacenar archivos temporales que solo son necesarios durante un breve período en este directorio. Para ahorrar espacio, los archivos deben eliminarse cuando ya no sean necesarios. El sistema operativo también puede eliminar archivos desde este directorio cuando no se está ejecutando una aplicación.<br /><br />El contenido de este directorio no estén respaldado por iTunes.<br /><br />Por ejemplo, se puede usar el directorio tmp para almacenar archivos temporales que se descargan para mostrar al usuario (por ejemplo, Twitter avatares o datos adjuntos de correo electrónico), pero que se puede eliminar una vez que han se ha visto (y volver a descargarse si son necesarios en el futuro) .|

Esta captura de pantalla muestra la estructura de directorios en una ventana del Finder:

[![](file-system-images/08-library-directory.png "Esta captura de pantalla muestra la estructura de directorios en una ventana de Finder")](file-system-images/08-library-directory.png#lightbox)

### <a name="accessing-other-directories-programmatically"></a>Acceso a otros directorios mediante programación

Los ejemplos anteriores de archivos y directorios que se tiene acceso a la `Documents` directory. Para escribir en otro directorio, debe construir una ruta de acceso con el ".." sintaxis como se muestra aquí:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var filename = Path.Combine (library, "WriteToLibrary.txt");
File.WriteAllText(filename, "Write this text into a file in Library");
```

Creación de un directorio es similar:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var directoryname = Path.Combine (library, "NewLibraryDirectory");
Directory.CreateDirectory(directoryname);
```

Rutas de acceso a la `Caches` y `tmp` directorios se pueden construir similar al siguiente:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var cache = Path.Combine (documents, "..", "Library", "Caches");
var tmp = Path.Combine (documents, "..", "tmp");
```

## <a name="sharing-with-the-files-app"></a>Uso compartido con la aplicación de archivos

iOS 11 introducidas el **archivos** app - un explorador de archivos para iOS que permite al usuario ver e interactuar con sus archivos en iCloud y también se almacenan en cualquier aplicación que lo admite. Para permitir al usuario obtener acceso directo a los archivos de la aplicación, cree una nueva clave booleana en el **Info.plist** archivo `LSSupportsOpeningDocumentsInPlace` y establézcalo en `true`, como aquí:

![Establecer LSSupportsOpeningDocumentsInPlace en Info.plist](file-system-images/51-supports-opening.png)

La aplicación **documentos** directorio ahora estarán disponible para examinarlos en el **archivos** app. En el **archivos** aplicación, vaya a **en mi iPhone** cada aplicación con archivos compartidos que estarán visible. Las capturas de pantalla siguiente muestran lo que el [aplicación de ejemplo de sistema de archivos](https://developer.xamarin.com/samples/monotouch/FileSystemSampleCode/) el siguiente aspecto:

![aplicación de iOS 11 archivos](file-system-images/50-files-app-1-sml.png) ![Examinar archivos de mi iPhone](file-system-images/50-files-app-2-sml.png) ![Archivos de aplicación de ejemplo](file-system-images/50-files-app-3-sml.png)

## <a name="sharing-files-with-the-user-through-itunes"></a>Uso compartido de archivos con el usuario a través de iTunes

Los usuarios pueden acceder a los archivos en el directorio de documentos de la aplicación mediante la edición de `Info.plist` y la creación de un **aplicación que admite el uso compartido de iTunes** (`UIFileSharingEnabled`) entrada en el **origen** Ver, como se muestra aquí:

[![Al agregar la aplicación es compatible con la propiedad de uso compartido de iTunes](file-system-images/09-uifilesharingenabled-plist-sml.png)](file-system-images/09-uifilesharingenabled-plist.png#lightbox)

Estos archivos pueden tener acceso en iTunes cuando el dispositivo está conectado y el usuario elige el `Apps` ficha. Por ejemplo, la captura de pantalla siguiente muestra los archivos en la aplicación seleccionada compartido a través de iTunes:

[![Esta captura de pantalla muestra los archivos de la aplicación seleccionada compartido a través de iTunes](file-system-images/10-itunes-file-sharing-sml.png)](file-system-images/10-itunes-file-sharing.png#lightbox)

Los usuarios solo pueden tener acceso a los elementos de nivel superior de este directorio a través de iTunes. No se verán el contenido de todos los subdirectorios (aunque puede copiarlos en su equipo o eliminarlos). Por ejemplo, con GoodReader, archivos PDF y EPUB pueden compartirse con la aplicación para que los usuarios pueden leer en sus dispositivos iOS.

Los usuarios que modifican el contenido de su carpeta de documentos pueden causar problemas si no tienen cuidadosas. La aplicación debe tener esto en cuenta y ser resistente a destructivas actualizaciones de la carpeta documentos.

El código de ejemplo para este artículo crea un archivo y una carpeta en la carpeta documentos (en **SampleCode.cs**) y permite compartir archivos de la **Info.plist** archivo. Esta captura de pantalla muestra cómo aparecen en iTunes:

[![Esta captura de pantalla muestra cómo aparecen los archivos de iTunes](file-system-images/15-itunes-file-sharing-example-sml.png)](file-system-images/15-itunes-file-sharing-example.png#lightbox)

Hacer referencia a la [trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md) artículo para obtener información acerca de cómo establecer los iconos de la aplicación y para cualquier tipo de documento personalizadas que cree.

Si el `UIFileSharingEnabled` clave es false o no está presente, a continuación, uso compartido de archivos de forma predeterminada, deshabilitado, y los usuarios no podrán interactuar con su directorio de documentos.

## <a name="backup-and-restore"></a>Copia de seguridad y restauración

Cuando se copia un dispositivo mediante iTunes, todos los directorios que creó en el directorio principal de la aplicación se guardará, excepto los siguientes directorios:

- **[ApplicationName] .app** : no se escriben en este directorio, desde que se firmaron y por lo que deben permanecer sin cambios tras la instalación. Puede contener recursos que tiene acceso desde el código, pero no requieren copias de seguridad ya que se restauraría por volver a descargar la aplicación.
- **Biblioteca/cachés** : el directorio de caché está pensado para los archivos de trabajo que no es necesario hacer copias de seguridad.
- **TMP** : este directorio se usa para los archivos temporales que se crean y eliminan cuando ya no es necesario, o para los archivos que iOS elimina cuando necesita espacio.

Copia una gran cantidad de datos puede tardar mucho tiempo. Si decide que necesita realizar copias de seguridad de cualquier documento en particular o datos, la aplicación debe usar los documentos y carpetas de la biblioteca. Para datos transitorios o archivos que se pueden recuperar fácilmente desde la red, utilice las memorias caché o el directorio tmp.

> [!NOTE]
> iOS 'limpiará' el sistema de archivos cuando un dispositivo se ejecuta dispone de suficiente espacio en disco.
> Este proceso eliminarán todos los archivos de la biblioteca y las memorias caché y tmp carpeta de aplicaciones que no se están ejecutando actualmente.

## <a name="complying-with-ios-5-icloud-backup-restrictions"></a>Cumplir con las restricciones de copia de seguridad de iCloud 5 de iOS

> [!NOTE]
> Aunque esta directiva fue la primera introducidos con iOS 5 (que parece ser hace mucho tiempo) la guía sigue siendo pertinente para las aplicaciones hoy mismo.

Apple introducida *iCloud Backup* funcionalidad con iOS 5. Cuando se habilita la copia de seguridad de iCloud, todos los archivos en el directorio principal de la aplicación (excluyendo los directorios no normalmente con copia de seguridad, por ejemplo, el lote de aplicaciones, `Caches`, y `tmp`) se copia de seguridad a los servidores de iCloud. Esta característica proporciona al usuario con una copia de seguridad completa en caso de su dispositivo es pérdido, robo o dañado.

Debido a iCloud proporciona solo 5 Gb de espacio 'free' para cada usuario y para evitar el uso innecesariamente ancho de banda, Apple espera que las aplicaciones realizar solo copias de seguridad esencial generados por el usuario. Para cumplir con las directrices de almacenamiento de datos de iOS, debe limitar la cantidad de datos que obtiene una copia de seguridad cumple con los siguientes elementos:

- Solo se almacenan los datos generados por el usuario o datos que en caso contrario, no se puede volver a crearse, en el directorio de documentos (que es la copia de seguridad).
- Store cualquier otro dato que fácilmente se puede volver a crearse o volver a descargar en `Library/Caches` o `tmp` (que no es una copia de seguridad y se ha podido 'Limpiar').
- Si tiene archivos que sean adecuados para el `Library/Caches` o `tmp` carpeta pero no quiere ' Limpiar ' out, almacenarlas en otra parte (como `Library/YourData`) y aplique el ' no hacer una copia de seguridad ' atributo para evitar que los archivos de uso de la copia de seguridad de iCloud espacio de almacenamiento y ancho de banda. Estos datos sigue usan espacio en el dispositivo, por lo que debe administrar con cuidado y eliminarlo siempre que sea posible.

El "no hacer una copia de seguridad ' atributo se establece mediante la `NSFileManager` clase. Asegúrese de que su clase está `using Foundation` y llamar a `SetSkipBackupAttribute` similar al siguiente:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "LocalOnly.txt");
File.WriteAllText(filename, "This file will never get backed-up. It would need to be re-created after a restore or re-install");
NSFileManager.SetSkipBackupAttribute (filename, true); // backup will be skipped for this file
```

Cuando `SetSkipBackupAttribute` es `true` el archivo no será la copia de seguridad, independientemente del directorio se almacena en (incluso la `Documents` directorio). Puede consultar el atributo utilizando el `GetSkipBackupAttribute` método y se puede restablecer mediante una llamada a la `SetSkipBackupAttribute` método con `false`, similar al siguiente:

```csharp
NSFileManager.SetSkipBackupAttribute (filename, false); // file will be backed-up
```

## <a name="sharing-data-between-ios-apps-and-app-extensions"></a>Compartir datos entre aplicaciones iOS y extensiones de aplicación

Puesto que las extensiones de aplicación se ejecuta como parte de una aplicación de host (a diferencia de su aplicación contenedora), el uso compartido de datos no es automático incluidos por lo que se requiere trabajo adicional. Grupos de aplicaciones son el mecanismo de iOS que se utiliza para permitir que aplicaciones diferentes compartir datos. Si las aplicaciones se han configurado correctamente con los derechos correctos y aprovisionamiento, pueden tener acceso a un directorio compartido fuera de su espacio aislado de iOS normales.

### <a name="configure-an-app-group"></a>Configurar un grupo de aplicaciones

La ubicación compartida se configura mediante un [grupo de aplicaciones](https://developer.apple.com/library/archive/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19), que se configura en la sección **Certificates, Identifiers & Profiles** (Certificados, identificadores y perfiles) del [Centro para desarrolladores de iOS](https://developer.apple.com/devcenter/ios/). También se debe hacer referencia a este valor en cada archivo **Entitlements.plist** del proyecto.

Para obtener información sobre cómo crear y configurar un grupo de aplicaciones, consulte el [las capacidades de grupos de aplicación](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) guía.

### <a name="files"></a>Archivos

La aplicación de iOS y la extensión también pueden compartir archivos con una ruta de acceso de archivo comunes (dado se han configurado con los derechos correctos y aprovisionamiento):

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =FileManager.GetContainerUrl ("group.com.xamarin.WatchSettings");
var appGroupContainerPath = appGroupContainer.Path

Console.WriteLine ("Group Path: " + appGroupContainerPath);

// use the path to create and update files
...
```

> [!IMPORTANT]
> Si se devuelve la ruta de acceso de grupo es `null`, compruebe la configuración de los derechos y el perfil de aprovisionamiento y asegúrese de que sean correctos.

## <a name="application-version-updates"></a>Actualizaciones de versión de aplicación

Cuando se descarga una nueva versión de la aplicación, iOS crea un nuevo directorio particular y almacena el nuevo paquete de aplicación en ella. iOS, a continuación, mueve las siguientes carpetas de la versión anterior de la agrupación de aplicaciones en el nuevo directorio principal:

- **Documentos**
- **Library**

Otros directorios también pueden copiarse más allá y colocar en el nuevo directorio principal, pero no están garantizados va a copiar, por lo que la aplicación no debe confiar en este comportamiento del sistema.

## <a name="summary"></a>Resumen

Este artículo se muestra que las operaciones de sistema de archivos con Xamarin.iOS son similares a cualquier otra aplicación. NET. También introdujo el espacio aislado de la aplicación y examinar las implicaciones de seguridad que hace. A continuación, se han examinado el concepto de un conjunto de aplicaciones. Por último, enumerar los directorios especializados disponibles para su aplicación y explica los roles asignados durante las copias de seguridad y las actualizaciones de aplicaciones.

## <a name="related-links"></a>Vínculos relacionados

- [Código de ejemplo de sistema de archivos](https://developer.xamarin.com/samples/monotouch/FileSystemSampleCode/)
- [Guía de programación de sistema de archivos](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/FileSystemProgrammingGUide/Introduction/Introduction.html)
- [Registra el archivo de tipos que admite la aplicación](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html#/apple_ref/doc/uid/TP40010411-SW1)
