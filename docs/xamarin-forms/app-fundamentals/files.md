---
title: Archivos
description: Archivo de control con Xamarin.Forms puede realizarse mediante los recursos insertados o escribir en el sistema de archivos nativo APIs.
ms.prod: xamarin
ms.assetid: 9987C3F6-5F04-403B-BBB4-ECB024EA6CC8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/22/2017
ms.openlocfilehash: 02bea7e2ec927277a92c0732b25f590b5ae6704b
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/27/2018
---
# <a name="files"></a>Archivos

_Archivo de control con Xamarin.Forms puede realizarse mediante los recursos insertados o escribir en el sistema de archivos nativo APIs._

## <a name="overview"></a>Información general

El código de Xamarin.Forms se ejecuta en varias plataformas, cada una con su propio sistema de archivos. Esto significa que leer y escribir archivos estén la más fácilmente lleva a cabo mediante la API de archivos nativos en cada plataforma. Como alternativa, los recursos incrustados son una solución más sencilla para distribuir archivos de datos con una aplicación.

Este documento cubre el siguiente archivo comunes de administración de los escenarios:

-  [ **Archivos que se incrustan como recursos** ](#Loading_Files_Embedded_as_Resources) -archivos pueden enviarse como parte de una aplicación y se cargan mediante la API de reflexión.
-  [ **Guardar y cargar archivos** ](#Loading_and_Saving_Files) -usuario escritura almacenamiento puede ser implementado de forma nativa y, a continuación, acceso mediante el `DependencyService` .


Una llamada a un componente de terceros **PCLStorage** también puede usarse para leer y escribir archivos en almacenamiento usuario accesibles desde código PCL.

Para obtener información sobre cómo administrar los archivos de imagen, consulte el [trabajar con imágenes](~/xamarin-forms/user-interface/images.md) página.

<a name="Loading_Files_Embedded_as_Resources" />

## <a name="loading-files-embedded-as-resources"></a>Carga de archivos que se incrustan como recursos

Para incrustar un archivo en un **PCL** ensamblado, crear o agregar un archivo y asegúrese de que **acción de compilación: EmbeddedResource**.

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

Los ejemplos anteriores se suponen que el archivo está incrustado en la raíz del proyecto PCL, en cuyo caso el identificador de recurso es el formato **Namespace.Filename.Extension**, como `WorkingWithFiles.PCLTextResource.txt` y `WorkingWithFiles.iOS.SharedTextResource.txt`.

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

<a name="Loading_and_Saving_Files" />

## <a name="saving-and-loading-files"></a>Guardar y cargar archivos

Dado que Xamarin.Forms se ejecuta en varias plataformas, cada uno con su propio sistema de archivos, no hay ningún método único para cargar y guardar archivos creados por el usuario. Para demostrar cómo guardar y cargar archivos de texto, que la aplicación de ejemplo incluye una pantalla que guarda y carga alguna entrada del usuario: a continuación se muestra la pantalla terminada:

 [![Guardar y cargar texto](files-images/saveandload-sml.png "guardar y cargar archivos de aplicación")](files-images/saveandload.png#lightbox "guardar y cargar archivos de aplicación")

Cada plataforma tiene una estructura de directorios ligeramente diferentes y capacidades de sistema de archivos diferentes: por ejemplo Xamarin.iOS y Xamarin.Android admiten la mayor parte `System.IO` funcionalidad pero la plataforma Universal de Windows solo admite [ `Windows.Storage` ](/uwp/api/windows.storage/) API.

Para solucionar este problema, la aplicación de ejemplo define una interfaz en la PCL Xamarin.Forms para cargar y guardar archivos. Proporciona una API sencilla para cargar y guardar archivos de texto que se almacenará en el dispositivo.

```csharp
public interface ISaveAndLoad {
    void SaveText (string filename, string text);
    string LoadText (string filename);
}
```

Código de la PCL, a continuación, se utiliza el [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) para obtener una referencia a una implementación nativa de la interfaz. Esto permite que el código portable para delegar la carga y el almacenamiento de archivos de clase escrita en cada uno de los proyectos específicos de la plataforma. En el ejemplo, el **guardar** y **carga** botones se escriben como se muestra:

```csharp
var saveButton = new Button {Text = "Save"};
saveButton.Clicked += (sender, e) => {
    DependencyService.Get<ISaveAndLoad>().SaveText("temp.txt", input.Text);
};
var loadButton = new Button {Text = "Load"};
loadButton.Clicked += (sender, e) => {
    output.Text = DependencyService.Get<ISaveAndLoad>().LoadText("temp.txt");
};
```

Una implementación, a continuación, debe agregarse a cada uno de los proyectos específicos de plataforma antes de que pueden realmente guardar y cargar los archivos.

### <a name="ios-and-android"></a>iOS y Android

La implementación de la interfaz para los proyectos Xamarin.iOS y Xamarin.Android puede ser idéntica. El código se muestra a continuación, incluido el `[assembly: Dependency (typeof (SaveAndLoad))]` atributo que es necesario para la `DependencyService` para que funcione.

```csharp
[assembly: Dependency (typeof (SaveAndLoad))]
namespace WorkingWithFiles {
    public class SaveAndLoad : ISaveAndLoad {
        public void SaveText (string filename, string text) {
            var documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
            var filePath = Path.Combine (documentsPath, filename);
            System.IO.File.WriteAllText (filePath, text);
        }
        public string LoadText (string filename) {
            var documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
            var filePath = Path.Combine (documentsPath, filename);
            return System.IO.File.ReadAllText (filePath);
        }
    }
}
```

### <a name="universal-windows-platform-uwp"></a>Plataforma universal de Windows (UWP)

UWP tiene un sistema de archivos diferentes API: [ `Windows.Storage` ](/windows/uwp/files/quickstart-reading-and-writing-files/) , es decir usado para guardar y cargar archivos.
La `ISaveAndLoad` puede implementar la interfaz tal y como se muestra a continuación:

```csharp
using System;
using System.Threading.Tasks;
using Windows.Storage;
using WinApp;
using WorkingWithFiles;
using Xamarin.Forms;

[assembly: Dependency(typeof(SaveAndLoad_WinApp))]

namespace WindowsApp
{
    // https://msdn.microsoft.com/library/windows/apps/xaml/hh758325.aspx
    public class SaveAndLoad_WinApp : ISaveAndLoad
    {
        public async Task SaveTextAsync(string filename, string text)
        {
            StorageFolder localFolder = ApplicationData.Current.LocalFolder;
            StorageFile sampleFile = await localFolder.CreateFileAsync(filename, CreationCollisionOption.ReplaceExisting);
            await FileIO.WriteTextAsync(sampleFile, text);
        }
        public async Task<string> LoadTextAsync(string filename)
        {
            StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
            StorageFile sampleFile = await storageFolder.GetFileAsync(filename);
            string text = await Windows.Storage.FileIO.ReadTextAsync(sampleFile);
            return text;
        }
    }
}
```

<a name="Saving_and_Loading_in_Shared_Projects" />

### <a name="saving-and-loading-in-shared-projects"></a>Guardar y cargar en los proyectos compartidos

Dado que los proyectos compartidos admiten directivas de compilador es posible incluir todo el código específico de la plataforma en un archivo de clase único dentro del proyecto compartido (sin usar el `DependencyService`).
Una sola `SaveAndLoad` podría escribirse de clase que contiene ambas implementaciones anteriores, se compilan selectivamente en los que hace referencia a proyectos mediante directivas de compilador como `#if WINDOWS_PHONE`, `#if __IOS__`, y `#if __ANDROID__`.

## <a name="additional-information"></a>Información adicional

Los proyectos basados en PCL de Xamarin.Forms también pueden aprovechar las ventajas de la [PCLStorage NuGet](http://www.nuget.org/packages/pclstorage) ([código &amp; documentación](https://pclstorage.codeplex.com/)) para ayudar a implementar las operaciones de archivos de una manera de multiplataforma.


## <a name="summary"></a>Resumen

Este documento han mostrado algunas operaciones de archivo simple para cargar los recursos incrustados y guardar y cargar texto en el dispositivo. Los desarrolladores pueden implementar su propia API de archivo nativo mediante el `DependencyService`, lo que tan complejo como sea necesario para controlar sus requisitos de manipulación de archivos.


## <a name="related-links"></a>Vínculos relacionados

- [FilesSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/)
- [Crear, escribir y leer un archivo (UWP)](/windows/uwp/files/quickstart-reading-and-writing-files/)
- [Xamarin.Forms Samples](https://github.com/xamarin/xamarin-forms-samples) (Ejemplos de Xamarin.Forms)
- [Libro de archivos](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/files/files.workbook)
