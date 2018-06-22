---
title: Control de archivos de Xamarin.Forms
description: Archivo de control con Xamarin.Forms se puede lograr utilizando código en una biblioteca de .NET estándar, o mediante el uso de recursos incrustados.
ms.prod: xamarin
ms.assetid: 9987C3F6-5F04-403B-BBB4-ECB024EA6CC8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: 0be441a7be9777698212e690aca95fdd75e5050f
ms.sourcegitcommit: eac092f84b603958c761df305f015ff84e0fad44
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/21/2018
ms.locfileid: "36310158"
---
# <a name="file-handling-in-xamarinforms"></a>Control de archivos de Xamarin.Forms

_Archivo de control con Xamarin.Forms se puede lograr utilizando código en una biblioteca de .NET estándar, o mediante el uso de recursos incrustados._

## <a name="overview"></a>Información general

El código de Xamarin.Forms se ejecuta en varias plataformas, cada una con su propio sistema de archivos. Anteriormente, esto significaba que realiza la que leer y escribir archivos era más fácilmente mediante la API de archivos nativos en cada plataforma. Como alternativa, los recursos incrustados son una solución más sencilla para distribuir archivos de datos con una aplicación. Sin embargo, con .NET 2.0 estándar es posible compartir código de acceso de archivo en las bibliotecas de .NET estándar.

Para obtener información sobre cómo administrar los archivos de imagen, consulte el [trabajar con imágenes](~/xamarin-forms/user-interface/images.md) página.

<a name="Loading_and_Saving_Files" />

## <a name="saving-and-loading-files"></a>Guardar y cargar archivos

El `System.IO` clases se pueden utilizar para tener acceso al sistema de archivos en cada plataforma. El `File` clase le permite crear, eliminar y leer archivos y el `Directory` clase le permite crear, eliminar o enumerar el contenido de los directorios. También puede usar el `Stream` subclases, lo que pueden proporcionar un mayor grado de control sobre las operaciones de archivo (por ejemplo, búsqueda de compresión o posición dentro de un archivo).

Un archivo de texto se puede escribir con el `File.WriteAllText` método:

```csharp
File.WriteAllText(fileName, text);
```

Se puede leer un archivo de texto utilizando la `File.ReadAllText` método:

```csharp
string text = File.ReadAllText(fileName);
```

Además, la `File.Exists` método determina si existe el archivo especificado:

```csharp
bool doesExist = File.Exists(fileName);
```

Se puede determinar la ruta de acceso del archivo en cada plataforma de una biblioteca de .NET estándar con un valor de la [ `Environment.SpecialFolder` ](xref:System.Environment.SpecialFolder) enumeración como el primer argumento para el `Environment.GetFolderPath` método. A continuación, se puede combinar con un nombre de archivo con el `Path.Combine` método:

```csharp
string fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "temp.txt");
```

Estas operaciones se muestran en la aplicación de ejemplo, que incluye una página que guarde y cargue el texto:

[![Guardar y cargar texto](files-images/saveandload-sml.png "guardar y cargar archivos de aplicación")](files-images/saveandload.png#lightbox "guardar y cargar archivos de aplicación")

<a name="Loading_Files_Embedded_as_Resources" />

## <a name="loading-files-embedded-as-resources"></a>Carga de archivos que se incrustan como recursos

Para incrustar un archivo en un **.NET estándar** ensamblado, crear o agregar un archivo y asegúrese de que **acción de compilación: EmbeddedResource**.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Configurar incrusta la acción de compilación de recurso](files-images/vs-embeddedresource-sml.png "configuración EmbeddedResource BuildAction")](files-images/vs-embeddedresource.png#lightbox "configuración EmbeddedResource BuildAction")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Archivo de texto incrustado en la PCL, configurar la acción de compilación de recurso incrustado](files-images/xs-embeddedresource-sml.png "configuración EmbeddedResource BuildAction")](files-images/xs-embeddedresource.png#lightbox "configuración EmbeddedResource BuildAction")

-----

`GetManifestResourceStream` se utiliza para tener acceso al archivo incrustado utilizando su **Id. de recurso**. De forma predeterminada, el identificador de recurso es el nombre de archivo como precedida del espacio de nombres predeterminado para el proyecto se incrusta en - en este caso es el ensamblado **WorkingWithFiles** y el nombre de archivo es **PCLTextResource.txt**, por lo que es el identificador de recurso `WorkingWithFiles.PCLTextResource.txt`.

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLTextResource.txt");
string text = "";
using (var reader = new System.IO.StreamReader (stream)) {
    text = reader.ReadToEnd ();
}
```

El `text` variable, a continuación, puede utilizarse para mostrar el texto o en caso contrario, se usa en el código. Esta captura de pantalla de la [aplicación de ejemplo](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/) muestra el texto que se representan en un `Label` control.

 [![Archivo de texto incrustado en PCL](files-images/pcltext-sml.png "archivo de texto incrustado en PCL mostrado en aplicación")](files-images/pcltext.png#lightbox "archivo de texto incrustado en PCL que se muestran en la aplicación")

Cargar y deserializar un documento XML es igual de sencillo. El código siguiente muestra un archivo XML que se está cargado y deserializa desde un recurso y, a continuación, enlaza a un `ListView` para su presentación. El archivo XML contiene una matriz de `Monkey` objetos (la clase se define en el código de ejemplo).

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLXmlResource.xml");
List<Monkey> monkeys;
using (var reader = new System.IO.StreamReader (stream)) {
    var serializer = new XmlSerializer(typeof(List<Monkey>));
    monkeys = (List<Monkey>)serializer.Deserialize(reader);
}
var listView = new ListView ();
listView.ItemsSource = monkeys;
```

 [![Archivo XML se incrusta en la PCL, que se muestran en ListView](files-images/pclxml-sml.png "archivo XML incrustado en PCL que se muestran en ListView")](files-images/pclxml.png#lightbox "archivo XML incrustado en PCL muestra de ListView")

<a name="Embedding_in_Shared_Projects" />

### <a name="embedding-in-shared-projects"></a>Incrustar en los proyectos compartidos

Los proyectos compartidos también pueden contener archivos como recursos incrustados, sin embargo, dado que el contenido de un proyecto compartido se compila en los proyectos de referencia, el prefijo usado para incrustado puede cambiar el Id. de recurso de archivo. Esto significa que el identificador de recurso para cada archivo incrustado puede ser diferente para cada plataforma.

Hay dos posibles soluciones a este problema con los proyectos compartidos:

-  **Sincronizar los proyectos** -editar las propiedades del proyecto para cada plataforma usar el **mismo** ensamblado predeterminada y el nombre de espacio de nombres. Este valor puede ser, a continuación, "codificado" como prefijo para los identificadores en el proyecto compartido de recursos incrustado.
-  **directivas de compilador #if** -usar las directivas de compilador para establecer el prefijo de Id. de recurso correcto y utilizar ese valor para construir dinámicamente el identificador de recurso correcto.


A continuación se muestra código que ilustra la segunda opción. Directivas de compilador se usan para seleccionar el prefijo de recurso codificado de forma rígida (que normalmente es el mismo que el espacio de nombres predeterminado para el proyecto que hace referencia). El `resourcePrefix` se usa para crear un identificador de recurso válido mediante la concatenación con el nombre de archivo de recursos incrustado.

```csharp
#if __IOS__
var resourcePrefix = "WorkingWithFiles.iOS.";
#endif
#if __ANDROID__
var resourcePrefix = "WorkingWithFiles.Droid.";
#endif

Debug.WriteLine("Using this resource prefix: " + resourcePrefix);
// note that the prefix includes the trailing period '.' that is required
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(SharedPage)).Assembly;
Stream stream = assembly.GetManifestResourceStream
    (resourcePrefix + "SharedTextResource.txt");
```

<a name="Organizing_Resources" />

### <a name="organizing-resources"></a>Organizar los recursos

Los ejemplos anteriores se suponen que el archivo está incrustado en la raíz del proyecto de biblioteca estándar. NET, en cuyo caso el identificador de recurso es el formato **Namespace.Filename.Extension**, como `WorkingWithFiles.PCLTextResource.txt` y `WorkingWithFiles.iOS.SharedTextResource.txt`.

Es posible organizar los recursos incrustados en las carpetas. Cuando se coloca un recurso incrustado en una carpeta, el nombre de la carpeta pasa a formar parte del identificador de recurso (separados por puntos), por lo que se convierte en el formato de Id. de recurso **Namespace.Folder.Filename.Extension**. Colocar los archivos que se usan en la aplicación de ejemplo en una carpeta **MiCarpeta** haría que el Id. de recurso correspondiente `WorkingWithFiles.MyFolder.PCLTextResource.txt` y `WorkingWithFiles.iOS.MyFolder.SharedTextResource.txt`.

<a name="Debugging_Embedded_Resources" />

### <a name="debugging-embedded-resources"></a>Depuración de recursos incrustados

Dado que a veces es difícil de entender por qué no se puede cargar un recurso determinado, el siguiente código de depuración puede agregarse temporalmente a una aplicación para comprobar que los recursos están configurados correctamente. Dará como resultado conocidos todos los recursos incrustados en el ensamblado especificado para el **errores** panel para ayudar a depurar problemas de carga de recursos.

```csharp
using System.Reflection;
// ...
// use for debugging, not in released app code!
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(SharedPage)).Assembly;
foreach (var res in assembly.GetManifestResourceNames()) {
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

## <a name="summary"></a>Resumen

En este artículo se muestra algunas operaciones de archivo simple para guardar y cargar texto en el dispositivo y para cargar los recursos incrustados. Con .NET 2.0 estándar es posible compartir código de acceso de archivo en las bibliotecas de .NET estándar.

## <a name="related-links"></a>Vínculos relacionados

- [FilesSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/)
- [Xamarin.Forms Samples](https://github.com/xamarin/xamarin-forms-samples) (Ejemplos de Xamarin.Forms)
- [Trabajar con el sistema de archivos en Xamarin.iOS](~/ios/app-fundamentals/file-system.md)
- [Libro de archivos](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/files/files.workbook)
