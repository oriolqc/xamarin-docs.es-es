---
title: Control de archivos en Xamarin.Forms
description: Archivo de control con Xamarin.Forms puede lograrse mediante código en una biblioteca .NET Standard, o mediante el uso de recursos incrustados.
ms.prod: xamarin
ms.assetid: 9987C3F6-5F04-403B-BBB4-ECB024EA6CC8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: 87084a0ccc2970f56e7ef7a6d2f4c59c49032aa0
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2018
ms.locfileid: "51527370"
---
# <a name="file-handling-in-xamarinforms"></a>Control de archivos en Xamarin.Forms

_Archivo de control con Xamarin.Forms puede lograrse mediante código en una biblioteca .NET Standard, o mediante el uso de recursos incrustados._

## <a name="overview"></a>Información general

El código de Xamarin.Forms se ejecuta en varias plataformas, cada una con su propio sistema de archivos. Anteriormente, esto significaba que leer y escribir archivos se realizan más fácilmente mediante la API de archivo nativo en cada plataforma. Como alternativa, los recursos incrustados son una solución más sencilla para distribuir los archivos de datos con una aplicación. Sin embargo, con .NET Standard 2.0 es posible compartir código de acceso de archivo en bibliotecas de .NET Standard.

Para obtener información sobre cómo administrar archivos de imagen, consulte el [trabajar con imágenes](~/xamarin-forms/user-interface/images.md) página.

<a name="Loading_and_Saving_Files" />

## <a name="saving-and-loading-files"></a>Guardar y cargar archivos

El `System.IO` clases pueden utilizarse para tener acceso el sistema de archivos en cada plataforma. El `File` clase le permite crear, eliminar y leer archivos y el `Directory` clase le permite crear, eliminar o enumerar el contenido de los directorios. También puede usar el `Stream` subclases, lo que pueden proporcionar un mayor grado de control sobre las operaciones de archivo (por ejemplo, la búsqueda de compresión o posición dentro de un archivo).

Se puede escribir un archivo de texto mediante el `File.WriteAllText` método:

```csharp
File.WriteAllText(fileName, text);
```

Se puede leer un archivo de texto utilizando la `File.ReadAllText` método:

```csharp
string text = File.ReadAllText(fileName);
```

Además, el `File.Exists` método determina si existe el archivo especificado:

```csharp
bool doesExist = File.Exists(fileName);
```

La ruta de acceso del archivo en cada plataforma puede determinarse a partir de una biblioteca .NET Standard con un valor de la [ `Environment.SpecialFolder` ](xref:System.Environment.SpecialFolder) enumeración como el primer argumento para el `Environment.GetFolderPath` método. A continuación, se pueden combinar con un nombre de archivo con el `Path.Combine` método:

```csharp
string fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "temp.txt");
```

Estas operaciones se muestran en la aplicación de ejemplo, que incluye una página que se guarda y carga de texto:

[![Guardar y cargar texto](files-images/saveandload-sml.png "guardar y cargar archivos de aplicación")](files-images/saveandload.png#lightbox "guardar y cargar archivos de aplicación")

<a name="Loading_Files_Embedded_as_Resources" />

## <a name="loading-files-embedded-as-resources"></a>Cargar archivos incrustados como recursos

Para incrustar un archivo en un **.NET Standard** ensamblado, crear o agregar un archivo y asegúrese de que **acción de compilación: EmbeddedResource**.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Configurar incrustados la acción de compilación de recursos](files-images/vs-embeddedresource-sml.png "configuración EmbeddedResource BuildAction")](files-images/vs-embeddedresource.png#lightbox "configuración EmbeddedResource BuildAction")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Archivo de texto incrustado en PCL, configurar acción de compilación de recurso incrustado](files-images/xs-embeddedresource-sml.png "configuración EmbeddedResource BuildAction")](files-images/xs-embeddedresource.png#lightbox "configuración EmbeddedResource BuildAction")

-----

`GetManifestResourceStream` se usa para tener acceso al archivo incrustado utilizando su **Id. de recurso**. De forma predeterminada, el identificador de recurso es el nombre de archivo como precedida del espacio de nombres predeterminado para el proyecto está incrustado en - en este caso es el ensamblado **WorkingWithFiles** y es el nombre de archivo **PCLTextResource.txt**, por lo que es el identificador de recurso `WorkingWithFiles.PCLTextResource.txt`.

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLTextResource.txt");
string text = "";
using (var reader = new System.IO.StreamReader (stream)) {
    text = reader.ReadToEnd ();
}
```

El `text` variable, a continuación, puede utilizarse para mostrar el texto o en caso contrario, se usa en el código. Esta captura de pantalla de la [aplicación de ejemplo](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/) muestra el texto representado en un `Label` control.

 [![Archivo de texto incrustado en PCL](files-images/pcltext-sml.png "archivo de texto incrustado en PCL que se muestra en la aplicación")](files-images/pcltext.png#lightbox "archivo de texto incrustado en PCL que se muestra en la aplicación")

Cargar y deserializar un documento XML es igualmente sencilla. El código siguiente muestra un archivo XML que se está cargado y deserializarse desde un recurso y, a continuación, enlaza a un `ListView` para su presentación. El archivo XML contiene una matriz de `Monkey` objetos (la clase se define en el código de ejemplo).

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

 [![Archivo XML incrustado en PCL, que se muestran en ListView](files-images/pclxml-sml.png "archivo XML incrustado en PCL que se muestran en ListView")](files-images/pclxml.png#lightbox "archivo XML incrustado en PCL que se muestran en ListView")

<a name="Embedding_in_Shared_Projects" />

## <a name="embedding-in-shared-projects"></a>Incrustar en los proyectos compartidos

Proyectos compartidos también pueden contener archivos como recursos incrustados, sin embargo, dado que el contenido de un proyecto compartido se compila en los proyectos de referencia, el prefijo usado para incrustado pueden cambiar los identificadores de recursos de archivo. Esto significa que el identificador de recurso para cada archivo incrustado puede ser diferente para cada plataforma.

Hay dos soluciones para este problema con los proyectos compartidos:

-  **Sincronizar los proyectos** -editar las propiedades del proyecto para cada plataforma para usar el **mismo** espacio de nombres predeterminada y el nombre de ensamblado. Este valor, a continuación, puede ser "codificados" como prefijo para los identificadores en el proyecto compartido de recursos incrustado.
-  **directivas de compilador #if** -usar directivas de compilador que establezca el prefijo de Id. de recurso correcto y use ese valor para construir dinámicamente el identificador de recurso correcto.


A continuación se muestra código que ilustra la segunda opción. Directivas de compilador se usan para seleccionar el prefijo de recurso codificado de forma rígida (que normalmente es el mismo que el espacio de nombres predeterminado para el proyecto que hace referencia). El `resourcePrefix` variable, a continuación, se usa para crear un identificador de recurso válido concatenando el nombre de archivo del recurso incrustado.

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

Los ejemplos anteriores se supone que el archivo está incrustado en la raíz del proyecto de biblioteca estándar. NET, en cuyo caso el identificador de recurso es la forma **Namespace.Filename.Extension**, tales como `WorkingWithFiles.PCLTextResource.txt` y `WorkingWithFiles.iOS.SharedTextResource.txt`.

Es posible organizar los recursos incrustados en las carpetas. Cuando se coloca un recurso incrustado en una carpeta, el nombre de carpeta se convierte en parte del identificador del recurso (separado por puntos), por lo que se convierte en el formato de Id. de recurso **Namespace.Folder.Filename.Extension**. Colocación de los archivos utilizados en la aplicación de ejemplo en una carpeta **MiCarpeta** haría que los identificadores de recurso correspondiente `WorkingWithFiles.MyFolder.PCLTextResource.txt` y `WorkingWithFiles.iOS.MyFolder.SharedTextResource.txt`.

<a name="Debugging_Embedded_Resources" />

### <a name="debugging-embedded-resources"></a>Depuración de recursos incrustados

Dado que a veces es difícil de entender por qué no se puede cargar un recurso determinado, el siguiente código de depuración se puede agregar temporalmente a una aplicación para ayudarle a confirmar que los recursos están configurados correctamente. Dará como resultado conocidos todos los recursos incrustados en el ensamblado especificado para el **errores** pad para ayudar a depurar problemas de carga de recursos.

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

Este artículo le haya mostrado algunas operaciones de archivos simple para guardar y cargar texto en el dispositivo y para cargar recursos incrustados. Es posible compartir código de acceso de archivo en bibliotecas de .NET Standard con .NET Standard 2.0.

## <a name="related-links"></a>Vínculos relacionados

- [FilesSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/)
- [Xamarin.Forms Samples](https://github.com/xamarin/xamarin-forms-samples) (Ejemplos de Xamarin.Forms)
- [Trabajar con el sistema de archivos de Xamarin.iOS](~/ios/app-fundamentals/file-system.md)

