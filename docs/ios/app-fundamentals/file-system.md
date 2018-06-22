---
title: Trabajar con el sistema de archivos en Xamarin.iOS
description: Este documento describe cómo trabajar con el sistema de archivos en Xamarin.iOS. Se trata de directorios, leer archivos, serialización XML y JSON, el espacio aislado de aplicación, uso compartido de archivos a través de iTunes y mucho más.
ms.prod: xamarin
ms.assetid: 37DF2F38-901E-8F8E-269A-5EE0CCD28C08
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 5bcfac7911ac589e08cd6c5db8c8ea15a3497eca
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784246"
---
# <a name="working-with-the-file-system-in-xamarinios"></a>Trabajar con el sistema de archivos en Xamarin.iOS

Puede utilizar Xamarin.iOS y `System.IO` clases en el *biblioteca de clases Base (BCL) de .NET* para tener acceso al sistema de archivos de iOS. El `File` clase le permite crear, eliminar y leer archivos y el `Directory` clase le permite crear, eliminar o enumerar el contenido de los directorios. También puede usar `Stream` subclases, lo que pueden proporcionar un mayor grado de control sobre las operaciones de archivo (por ejemplo, búsqueda de compresión o posición dentro de un archivo).

iOS impone algunas restricciones en lo que puede hacer una aplicación con el sistema de archivos para conservar la seguridad de datos de una aplicación y para proteger a los usuarios de aplicaciones malignos. Estas restricciones forman parte de la *espacio aislado de la aplicación* : un conjunto de reglas que limita el acceso de la aplicación a los archivos, las preferencias, los recursos de red, hardware, etcetera. Una aplicación se limita a leer y escribir en archivos dentro de su directorio particular (ubicación de la instalación); no se puede tener acceso a archivos de la otra aplicación.

iOS también tiene algunas características específicas del sistema de archivos: ciertos directorios requieren un tratamiento especial con respecto a las copias de seguridad y las actualizaciones y aplicaciones también pueden compartir archivos a través de iTunes.

En este artículo se describe las características y restricciones del iOS sistema de archivos en detalle e incluye una aplicación de ejemplo que muestra cómo utilizar Xamarin.iOS para ejecutar algunas operaciones de sistema de archivos simple:

 [![](file-system-images/05-sampleapp.png "Un ejemplo de ejecución de algunas operaciones de sistema de archivos simple de iOS")](file-system-images/05-sampleapp.png#lightbox)

 <a name="General_File_Access" />

## <a name="general-file-access"></a>Acceso a los archivos general

Xamarin.iOS le permite usar .NET `System.IO` clases para las operaciones de sistema de archivos en iOS.

Los fragmentos de código siguientes muestran algunas operaciones de archivo comunes. Encontrará ellas todas por debajo en la `SampleCode.cs` el archivo, en la aplicación de ejemplo de este artículo.

<a name="Working_with_directories" />

### <a name="working-with-directories"></a>Trabajar con directorios

Este código enumera los subdirectorios del directorio actual (especificado por el ". /" parámetro), que es la ubicación de su archivo ejecutable de la aplicación.
El resultado será una lista de todos los archivos y carpetas que se implementan con la aplicación (que se muestran en la ventana de consola durante la depuración).

```csharp
var directories = Directory.EnumerateDirectories("./");
foreach (var directory in directories) {
      Console.WriteLine(directory);
}
```

 <a name="Reading_files" />


### <a name="reading-files"></a>Leer archivos

Para leer un archivo de texto, solo necesita una sola línea de código. En este ejemplo se mostrará el contenido de un archivo de texto en la ventana de salida de la aplicación.

```csharp
var text = File.ReadAllText("TestData/ReadMe.txt");
Console.WriteLine(text);
```

 <a name="XML_Serialization" />


### <a name="xml-serialization"></a>Serialización XML

Aunque trabajar con la sección completa `System.Xml` espacio de nombres está fuera del ámbito de este artículo, fácilmente puede deserializar un documento XML desde el sistema de archivos mediante el uso de un StreamReader similar al siguiente:

```csharp
using (TextReader reader = new StreamReader("./TestData/test.xml")) {
      XmlSerializer serializer = new XmlSerializer(typeof(MyObject));
      var xml = (MyObject)serializer.Deserialize(reader);
}
```

Consulte la documentación de MSDN para la [System.Xml](http://msdn.microsoft.com/library/system.xml.aspx) espacio de nombres para obtener más información acerca de [serialización](http://msdn.microsoft.com/library/system.xml.serialization.aspx). También debe revisar la [Xamarin.iOS documentación](~/ios/deploy-test/linker.md) en el vinculador: por lo general, deberá agregar el `[Preserve]` atributo a las clases que se va a serializar.

 <a name="Creating_Files_and_Directories" />


### <a name="creating-files-and-directories"></a>Crear archivos y directorios

Este ejemplo muestra cómo utilizar la `Environment` clase para tener acceso a la carpeta documentos donde podemos crear archivos y directorios.

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments); 
var filename = Path.Combine (documents, "Write.txt");
File.WriteAllText(filename, "Write this text into a file");
```

Crear un directorio es un proceso muy similar:

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var directoryname = Path.Combine (documents, "NewDirectory");
Directory.CreateDirectory(directoryname);
```

Para obtener más información sobre el espacio de nombres System.IO, consulte el [la documentación de MSDN](http://msdn.microsoft.com/library/system.io.aspx).


### <a name="serializing-json"></a>Serialización de Json

Trabajar con Json datos en una aplicación de Xamarin.iOS están muy fáciles mediante la [Json.NET](http://www.newtonsoft.com/json) framework JSON de alto rendimiento para el paquete de NuGet. NET. Basta con agregar el paquete NuGet al proyecto de la aplicación: 

[![](file-system-images/json01.png "Agregar el paquete de NuGet para el proyecto de aplicaciones")](file-system-images/json01.png#lightbox)

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
        #region Computed Properties
        public string Email { get; set; }
        public bool Active { get; set; }
        public DateTime CreatedDate { get; set; }
        public List<string> Roles { get; set; }
        #endregion

        #region Constructors
        public Account() {

        }
        #endregion
    }
}
```

Por último, cree una instancia de la `Account` clase, serializar a datos json y escribir en un archivo:

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
Hacer referencia a Json .NET [documentación](http://www.newtonsoft.com/json/help) para obtener más información sobre cómo trabajar con datos json en una aplicación. NET.

<a name="Special_Considerations" />

## <a name="special-considerations"></a>Consideraciones especiales

A pesar de las similitudes entre las operaciones de archivo Xamarin.iOS y. NET, iOS y Xamarin.iOS difieren de .NET en algunos aspectos importantes.

 <a name="runtimeaccessible" />


### <a name="making-project-files-accessible-at-runtime"></a>Hacer que los archivos de proyecto puede tener acceso en tiempo de ejecución

De forma predeterminada, si agrega un archivo al proyecto, no se incluirán en el ensamblado final y, por tanto, no estarán disponible para la aplicación. Para incluir un archivo en el ensamblado, se debe marcar con una acción de compilación especial, denominada contenido.

Para marcar un archivo de inclusión, haga doble clic en los archivos y elija **acción de compilación &gt; contenido** en Visual Studio para Mac. También puede cambiar la **acción de compilación** en el archivo **propiedades** hoja.

 <a name="Case_Sensitivity" />


### <a name="case-sensitivity"></a>Distinción de mayúsculas y minúsculas

Es importante entender que el sistema de archivos iOS es *entre mayúsculas y minúsculas*. Esto significa que los nombres de archivo y directorio deben coincidir exactamente, README.txt y readme.txt se consideraría distintos nombres de archivo.

Esto puede resultar confuso para los desarrolladores de .NET que están más familiarizados con el sistema de archivos de Windows, que es *entre mayúsculas y minúsculas*: "Archivos", "Archivos" y "archivos" haría referencia al mismo directorio.

Por lo tanto, aunque los dispositivos iOS distinguen mayúsculas de minúsculas y el código debe escribirse con la presente, el simulador es de iOS no distinguir mayúsculas de minúsculas de forma predeterminada. Esto significa que si el nombre de archivo de mayúsculas y minúsculas es diferente entre el propio archivo y las referencias a él en el código, el código todavía funcionen en el simulador, pero que produciría un error en un dispositivo real. Este es uno de los motivos por qué es importante implementar en un dispositivo real temprano y con frecuencia durante el desarrollo de iOS.

 <a name="Path_Separator" />


### <a name="path-separator"></a>Separador de ruta de acceso

iOS usa la barra diagonal '/' como separador de ruta de acceso (que es diferente de Windows, que usa la barra diagonal inversa ' \').

Debido a esta diferencia confusa, es recomendable utilizar el `System.IO.Path.Combine` método, que se ajusta a la plataforma actual, en lugar de codificar un separador de ruta de acceso determinada. Se trata de un paso simple que hace que el código sea más portátil para otras plataformas.

 <a name="Application_Sandbox" />


## <a name="application-sandbox"></a>Espacio aislado de la aplicación

Acceso de la aplicación para el sistema de archivos (y otros recursos, como las características de hardware y de red) está limitado por motivos de seguridad. Esta restricción se conoce como el *espacio aislado de la aplicación*. En términos del sistema de archivos, la aplicación se limita a crear y eliminar archivos y directorios en su directorio particular.

El directorio particular es una ubicación única en el sistema de archivos donde se almacenan la aplicación y todos sus datos. No puede elegir (o cambiar) la ubicación del directorio particular de la aplicación; Sin embargo iOS y Xamarin.iOS proporcionan propiedades y métodos para administrar los archivos y directorios dentro.

 <a name="The_Application_Bundle" />


## <a name="the-application-bundle"></a>La agrupación de aplicaciones

El *agrupación de aplicaciones* es la carpeta que contiene la aplicación.
Se diferencia de otras carpetas al tener el sufijo .app agregado para el nombre del directorio. El paquete de aplicación contiene el archivo ejecutable y todo el contenido (archivos, imágenes, etc.) necesario para el proyecto.

Al ir a la agrupación de aplicaciones de Mac OS, aparece con un icono diferente que se ven en otros directorios (y el sufijo .app está oculto); Sin embargo, es simplemente un directorio normal que el sistema operativo se muestra de forma diferente.

Para ver la agrupación de aplicaciones para el código de ejemplo, haga doble clic en el proyecto en Visual Studio para Mac y seleccione **Abrir carpeta contenedora**. A continuación, vaya a **bin/Debug/** donde debería encontrar un icono de la aplicación (similar a la captura de pantalla siguiente).

 [![](file-system-images/40-bundle.png "Desplácese para bin/Debug para encontrar el icono de una aplicación similar a esta captura de pantalla")](file-system-images/40-bundle.png#lightbox)

Haga doble clic en este icono y elija **ver contenido de paquete** para examinar el contenido del directorio de la agrupación de aplicaciones. El contenido aparece al igual que el contenido de un directorio regular, como se muestra aquí:

 [![](file-system-images/45-bundle.png "El contenido de la agrupación de aplicaciones")](file-system-images/45-bundle.png#lightbox)

La agrupación de aplicaciones es lo que esté instalado en el simulador o en el dispositivo durante las pruebas y, en última instancia es lo que se envía a Apple para su inclusión en la tienda de aplicaciones.

 <a name="Application_Directories" />


## <a name="application-directories"></a>Directorios de la aplicación

Cuando la aplicación se instala en un dispositivo, el sistema operativo crea su directorio particular y se coloca dentro de la agrupación de aplicaciones. El código puede tener acceso a la agrupación de aplicaciones para leer los datos, pero nada debería escribirse en ese directorio raíz, tal y como está firmado y todas las modificaciones se invalidan la aplicación y evitar que se inicie.

Por lo tanto, aunque nada debería escribirse en el directorio raíz, <b>en iOS 7 y versiones anteriores</b> crea una serie de directorios en el directorio raíz de la aplicación que están disponibles para su uso. <b>En iOS 8 son los directorios accesibles a los usuarios <a href="https://developer.apple.com/library/ios/technotes/tn2406/_index.html" target="_blank">no se encuentra</a> dentro de la raíz de la aplicación</b>.

Estos directorios y sus fines se enumeran a continuación:

&nbsp;

|Directorio|Descripción|
|---|---|
|[NombreAplicación] .app /|**En iOS 7 y versiones anteriores** trata el `ApplicationBundle` directorio donde se almacena el archivo ejecutable de la aplicación. La estructura de directorios que se crea en la aplicación existe en este directorio (por ejemplo, imágenes y otros tipos de archivo que se han marcado como recursos en la Visual Studio para proyectos de Mac).<br /><br />Si necesita tener acceso a los archivos de contenido dentro de la agrupación de aplicaciones, la ruta de acceso a este directorio está disponible a través de la `NSBundle.MainBundle.BundlePath` propiedad.|
|Documentos /|Utilice este directorio para almacenar documentos de usuario y archivos de datos de aplicación.<br /><br />El contenido de este directorio puede estar disponible para el usuario a través de los archivos de iTunes compartidos (aunque esto está deshabilitada de forma predeterminada). Agregar un `UIFileSharingEnabled` booleano clave en el archivo Info.plist para permitir que los usuarios accedan a estos archivos.<br /><br />Incluso si una aplicación inmediatamente no habilita el uso compartido de archivos, debe evitar colocar los archivos que deben ocultarse de los usuarios en este directorio (por ejemplo, archivos de base de datos, a menos que desee compartirlos). Siempre y cuando los archivos confidenciales permanecen ocultos, estos archivos no se exponen (y potencialmente mueve, modificado o eliminado por iTunes) si el uso compartido de archivos está habilitado en una versión futura.<br /><br /> Puede usar el `Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments)` método para obtener la ruta de acceso al directorio de documentos para la aplicación.<br /><br />El contenido de este directorio estén respaldado por iTunes.|
|Biblioteca /|El directorio de bibliotecas es un buen lugar para almacenar los archivos que no se crean directamente por el usuario, como las bases de datos u otros archivos generados por la aplicación. El contenido de este directorio nunca se expone al usuario a través de iTunes.<br /><br />Puede crear sus propios subdirectorios en la biblioteca; Sin embargo, ya hay algunos creado por el sistema directorios aquí que debe tener en cuenta, incluyendo las preferencias y las memorias caché.<br /><br />El contenido de este directorio (excepto el subdirectorio cachés) estén respaldado por iTunes. Se copiarán los directorios personalizados que se crean en la biblioteca.|
|Preferencias de la biblioteca / /|Archivos de preferencia específica de la aplicación se almacenan en este directorio. No cree directamente estos archivos. En su lugar, use la `NSUserDefaults` clase.<br /><br />El contenido de este directorio estén respaldado por iTunes.|
|Biblioteca/cachés /|El directorio de caché es un buen lugar para almacenar archivos de datos que pueden ayudar a la aplicación se ejecute, pero que pueden volver a crear fácilmente si es necesario. La aplicación debe crear y eliminar estos archivos según sea necesario y poder volver a crear estos archivos si es necesario. iOS 5 también puede eliminar estos archivos (en situaciones de almacenamiento muy baja), sin embargo, no se realizará mientras se ejecuta la aplicación.<br /><br />El contenido de este directorio no está respaldado por iTunes, lo que significa que no estará presentes si el usuario restaura un dispositivo, y que no estén presentes después de instala una versión actualizada de la aplicación.<br /><br />Por ejemplo, en caso de que la aplicación no se puede conectar a la red, podría utilizar el directorio de caché para almacenar archivos de datos o para proporcionar una buena experiencia sin conexión. La aplicación puede guardar y recuperar estos datos rápidamente mientras se espera para las respuestas de la red, pero no es necesario realizar copias de seguridad y se puede fácilmente recuperar o volver a crear después de actualizar una versión o restauración.|
|tmp /|Las aplicaciones pueden almacenar archivos temporales que solo son necesarios durante un breve período en este directorio. Para ahorrar espacio, los archivos se deben eliminar cuando ya no son necesarios. El sistema operativo también puede eliminar archivos desde este directorio cuando una aplicación no se está ejecutando.<br /><br />El contenido de este directorio no está respaldado por iTunes.<br /><br />Por ejemplo, el directorio tmp podría utilizarse para almacenar archivos temporales que se descargan para mostrar al usuario (por ejemplo, Twitter avatares o datos adjuntos de correo electrónico), pero que se puede eliminar una vez que hayan sido ver (y descargarla de nuevo si es necesarios en el futuro ).|

Esta captura de pantalla muestra la estructura de directorios en una ventana de herramienta de búsqueda:

 [![](file-system-images/08-library-directory.png "Esta captura de pantalla muestra la estructura de directorios en una ventana de buscador")](file-system-images/08-library-directory.png#lightbox)

 <a name="Accessing_Other_Directories_Programmatically" />

### <a name="accessing-other-directories-programmatically"></a>Obtener acceso a otros directorios mediante programación

Los ejemplos anteriores de archivos y directorios que se tiene acceso a la `Documents` directory. Para escribir en otro directorio, debe construir una ruta mediante la ".." sintaxis, tal como se muestra aquí:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var filename = Path.Combine (library, "WriteToLibrary.txt");
File.WriteAllText(filename, "Write this text into a file in Library");
```

Crear un directorio es muy similar:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var directoryname = Path.Combine (library, "NewLibraryDirectory");
Directory.CreateDirectory(directoryname);
```

Rutas de acceso a la `Caches` y `tmp` se pueden construir directorios similar al siguiente:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var cache = Path.Combine (documents, "..", "Library", "Caches");
var tmp = Path.Combine (documents, "..", "tmp");
```

 <a name="Sharing_Files_with_the_User_through_iTunes" />


## <a name="sharing-files-with-the-user-through-itunes"></a>Uso compartido de archivos con el usuario a través de iTunes

Los usuarios pueden acceder a los archivos en el directorio de documentos de su aplicación mediante la edición de `Info.plist` y la creación de un **aplicación admite el uso compartido de iTunes** (`UIFileSharingEnabled`) entrada en el **origen** vista, como se muestra aquí:

 [![](file-system-images/09-uifilesharingenabled-plist.png "Al agregar la aplicación es compatible con la propiedad de uso compartido de iTunes")](file-system-images/09-uifilesharingenabled-plist.png#lightbox)

Estos archivos son accesibles en iTunes cuando el dispositivo está conectado y el usuario elige el `Apps` ficha. Por ejemplo, la captura de pantalla siguiente muestra los archivos de aplicación seleccionada compartido mediante iTunes:

 [![](file-system-images/10-itunes-file-sharing.png "Esta captura de pantalla muestra los archivos de aplicación seleccionada que se comparten a través de iTunes")](file-system-images/10-itunes-file-sharing.png#lightbox)

Los usuarios solo pueden tener acceso a los elementos de nivel superior en este directorio a través de iTunes. No se vean el contenido de todos los subdirectorios (aunque puede copiarlos en su equipo o eliminarlos). Por ejemplo, con GoodReader, archivos PDF y EPUB pueden compartirse con la aplicación para que los usuarios pueden leer en sus dispositivos iOS.

Los usuarios que modificar el contenido de su carpeta de documentos pueden causar problemas si no lo es cuidadosas. La aplicación debe tener esto en cuenta y sean resistentes a destructivas actualizaciones de la carpeta documentos.

El código de ejemplo de este artículo crea un archivo y una carpeta en la carpeta documentos (en **SampleCode.cs**) y permite compartir archivos de la **Info.plist** archivo. Esta captura de pantalla muestra cómo aparecen en iTunes:

 [![](file-system-images/15-itunes-file-sharing-example.png "Esta captura de pantalla muestra la aparecen de los archivos de iTunes")](file-system-images/15-itunes-file-sharing-example.png#lightbox)

Hacer referencia a la [trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md) artículo para obtener información sobre cómo establecer iconos para la aplicación y para cualquier tipo de documento personalizadas que crear.

Si el `UIFileSharingEnabled` clave es false o no está presente, a continuación, uso compartido de archivos es, de forma predeterminada, deshabilitado y los usuarios no podrán interactuar con su Documentsdirectory.

 <a name="Backup_and_Restore" />

## <a name="backup-and-restore"></a>Copias de seguridad y restauración

Cuando un dispositivo está respaldado por iTunes, todos los directorios que creó en el directorio particular de la aplicación se guardará excepto los siguientes:

-   **[NombreAplicación] .app** : el paquete de aplicación *does* obtener copia de seguridad, pero solo cuando el paquete ha cambiado (cuando se instala una actualización de la aplicación, por ejemplo). No debe modificar este directorio de todas formas, desde que se firmaron y por lo que debe permanecer sin cambios tras la instalación. 
-   **Biblioteca/cachés** : el directorio de caché está pensado para los archivos de trabajo que no es necesario realizar copias de seguridad. 
-   **TMP** : este directorio se utiliza para los archivos temporales que se crean y se eliminan cuando ya no es necesario, o para los archivos que iOS elimina cuando necesita espacio. 


Copia de seguridad de una gran cantidad de datos puede tardar mucho tiempo. Si decide que necesita hacer copia de seguridad de cualquier documento en particular o datos, la aplicación debe utilizar únicamente los documentos y la biblioteca de carpetas de este. Para datos transitorios o archivos que se pueden recuperar fácilmente desde la red, utilice las memorias caché o el directorio tmp.

Tenga en cuenta que iOS 'limpiará' el sistema de archivos cuando un dispositivo se ejecuta dispone de suficiente espacio en disco. Este proceso eliminarán todos los archivos de la biblioteca y las memorias caché y tmp carpeta de aplicaciones que no se están ejecutando actualmente.

 <a name="Complying_with_iOS5_iCloud_Backup_Restrictions" />

## <a name="complying-with-ios5-icloud-backup-restrictions"></a>Cumplimiento de iCloud iOS5 restricciones de copia de seguridad

Apple introducida *iCloud copia de seguridad* funcionalidad con iOS 5. Cuando se habilita la copia de seguridad de iCloud, todos los archivos en el directorio particular de la aplicación (excluyendo los directorios no normalmente con copia de seguridad, por ejemplo, el grupo de aplicaciones, `Caches` y `tmp`) son copias de seguridad para servidores de iCloud. Esta característica proporciona al usuario con una copia de seguridad completa en caso de que su dispositivo se pérdida, robo o dañado.

Debido a que iCloud solo proporciona 5Gb de espacio "free" para cada usuario y evitar innecesariamente usando ancho de banda, Apple espera que las aplicaciones solo copia de seguridad generado por el usuario datos esenciales. Para cumplir con las directrices de almacenamiento de datos de iOS debe limitar la cantidad de datos que obtiene copia de seguridad mediante la adhesión a los elementos siguientes:

-  Solo se almacenan datos generado por el usuario o datos que en caso contrario, no se pueden volver a crearse, en el directorio de documentos (que es copia de seguridad). 
-  Almacenar cualquier otro dato que fácilmente se puede volver a crear o volver a descargar en `Library/Caches` o `tmp` (que no es una copia de seguridad y se ha podido 'Limpiar'). 
-  Si tiene archivos que podrían ser apropiados para el `Library/Caches` o `tmp` carpeta pero no desea 'Limpiar' out, almacenarlas en otro lugar (como `Library/YourData` ) y aplicar el ' no copia la ' atributo para impedir que los archivos ocupando ba de copia de seguridad de iCloud ndwidth y espacio de almacenamiento. Estos datos sigue usan espacio en el dispositivo, por lo que debe administrar cuidadosamente y eliminarlo siempre que sea posible. 

El ' no copia de seguridad ' atributo se establece mediante el `NSFileManager` clase. Asegúrese de que la clase es `using Foundation` y llame a `SetSkipBackupAttribute` similar a la siguiente:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "LocalOnly.txt");
File.WriteAllText(filename, "This file will never get backed-up. It would need to be re-created after a restore or re-install");
NSFileManager.SetSkipBackupAttribute (filename, true); // backup will be skipped for this file
```

Cuando `SetSkipBackupAttribute` es `true` el archivo no será la copia de seguridad, sin tener en cuenta el directorio se almacena en (incluso el `Documents` directorio). Puede consultar el atributo utilizando el `GetSkipBackupAttribute` método y se puede restablecer mediante una llamada a la `SetSkipBackupAttribute` método con `false`, similar a la siguiente:

```csharp
NSFileManager.SetSkipBackupAttribute (filename, false); // file will be backed-up
```

## <a name="sharing-data-between-ios-apps-and-app-extensions"></a>Compartir datos entre iOS aplicaciones y extensiones de aplicación

Dado que las extensiones de aplicación se ejecuta como parte de una aplicación de host (en lugar de la aplicación que lo contiene), el uso compartido de datos no es automático incluyen, lo que requiere trabajo adicional. Grupos de aplicaciones son el mecanismo de iOS que se utiliza para permitir que las diferentes aplicaciones compartir datos. Si las aplicaciones se han configurado correctamente con los derechos correctos y el aprovisionamiento, puede tener acceso a un directorio compartido fuera de su espacio aislado iOS normal.

### <a name="configure-an-app-group"></a>Configurar un grupo de aplicaciones

La ubicación compartida se configura mediante un [grupo de aplicaciones](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19), que se configura en la sección **Certificates, Identifiers & Profiles** (Certificados, identificadores y perfiles) del [Centro para desarrolladores de iOS](https://developer.apple.com/devcenter/ios/). También se debe hacer referencia a este valor en cada archivo **Entitlements.plist** del proyecto.

Para obtener información sobre cómo crear y configurar un grupo de aplicaciones, consulte el [las capacidades de grupo de aplicaciones](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) guía.

### <a name="files"></a>Archivos

La aplicación de iOS y la extensión también pueden compartir archivos mediante una ruta de acceso de archivo común (dado se han configurado con los derechos correctos y aprovisionamiento):

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =FileManager.GetContainerUrl ("group.com.xamarin.WatchSettings");
var appGroupContainerPath = appGroupContainer.Path

Console.WriteLine ("Group Path: " + appGroupContainerPath);

// use the path to create and update files
...
```
> [!IMPORTANT]
> Si la ruta de acceso de grupo devuelto es `null`, compruebe la configuración de los derechos y el perfil de aprovisionamiento y asegúrese de que son correctos.


<a name="Application_Version_Updates" />

## <a name="application-version-updates"></a>Actualizaciones de la versión de aplicación

Cuando se descarga una nueva versión de la aplicación, iOS crea un nuevo directorio particular y almacena el nuevo paquete de aplicación en ella. iOS, a continuación, mueve las carpetas siguientes desde la versión anterior de la agrupación de aplicaciones a su nuevo directorio particular:

-  **Documentos**
-  **Library**


Otros directorios también se copian a través de y poner bajo su nuevo directorio principal, pero no le garantiza que se copian, por lo que su aplicación no debería confiar en este comportamiento del sistema.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se ha mostrado que las operaciones de sistema de archivos son tan simples con Xamarin.iOS al igual que con cualquier otra aplicación. NET. También introdujo el espacio aislado de la aplicación y examinar las implicaciones de seguridad que lo hace. A continuación, se han examinado el concepto de un paquete de aplicación. Por último, enumeran los directorios especializados disponibles para la aplicación y la explicación de sus roles durante las copias de seguridad y las actualizaciones de aplicaciones.


## <a name="related-links"></a>Vínculos relacionados

- [FileSystemSampleCode](https://developer.xamarin.com/samples/FileSystemSampleCode/)
- [Guía de programación de sistema de archivos](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/FileSystemProgrammingGUide/Introduction/Introduction.html)
- [Registra el archivo de tipos de la aplicación, es compatible con](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html#/apple_ref/doc/uid/TP40010411-SW1)
