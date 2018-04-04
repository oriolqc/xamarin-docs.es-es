---
title: Visual Basic.NET en Xamarin iOS y Android
description: XEste tutorial muestra cómo compilar aplicaciones nativas de Xamarin.iOS y Xamarin.Android que emplean la lógica de negocios escrita en Visual Basic.NET.
ms.prod: xamarin
ms.assetid: 455fda67-3879-4299-8036-b12840e6a498
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: c922102d20eb103eb265d8a7bcb418e5187296a5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="visual-basicnet-in-xamarin-ios-and-android"></a>Visual Basic.NET en Xamarin iOS y Android

El [TaskyPortable](/samples/mobile/VisualBasic/TaskyPortableVB/) aplicación de ejemplo muestra cómo se puede utilizar código de Visual Basic que se compila en una biblioteca de clases Portable con Xamarin. Estas son algunas capturas de pantalla de las aplicaciones resultantes que se ejecutan en iOS, Android y Windows Phone:

 [![](native-apps-images/image5.png "iOS, Android y Windows teléfonos ejecuta una aplicación compilada con Visual Basic")](native-apps-images/image5.png#lightbox)

IOS, Android y Windows Phone proyectos en el ejemplo se escriben en C#. La interfaz de usuario para cada aplicación se compila con las tecnologías nativas (guiones gráficos, Xml y Xaml respectivamente), mientras que la `TodoItem` administración es proporcionada por la biblioteca de clases Portable de Visual Basic con un `IXmlStorage` implementación proporcionada por el proyecto nativo.

## <a name="sample-walkthrough"></a>Ejemplo de tutorial

Esta guía describe cómo Visual Basic se ha implementado en el [TaskyPortableVB](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB) muestra de Xamarin para iOS y Android.

> [!NOTE]
> Revisa la información de [PCLs de Visual Basic.NET](/guides/cross-platform/application_fundamentals/pcl/portable_visual_basic_net/) antes de continuar con esta guía.

## <a name="visualbasicportablelibrary"></a>VisualBasicPortableLibrary

Solo se pueden crear bibliotecas de clases Portable de Visual Basic en Visual Studio.
La biblioteca de ejemplo contiene los conceptos básicos de la aplicación en cuatro archivos de Visual Basic:

-  IXmlStorage.vb
-  TodoItem.vb
-  TodoItemManager.vb
-  TodoItemRepositoryXML.vb


### <a name="ixmlstoragevb"></a>IXmlStorage.vb

Dado que los comportamientos de acceso de archivo varían en gran medida entre plataformas, no proporciona bibliotecas de clases Portable `System.IO` en ningún perfil de las API de almacenamiento de archivos. Esto significa que si desea interactuar directamente con el sistema de archivos en el código portable, es necesario devolver la llamada a nuestro proyectos nativos en cada plataforma.  Si escribe el código de Visual Basic en una interfaz simple que se puede implementar en C# en cada plataforma, podemos tenemos que se pueda compartir código de Visual Basic que todavía tiene acceso al sistema de archivos.

El código de ejemplo usa esta interfaz muy simple que contiene dos métodos: para leer y escribir un archivo Xml serializado.

```vb
Public Interface IXmlStorage
    Function ReadXml(filename As String) As List(Of TodoItem)
    Sub WriteXml(tasks As List(Of TodoItem), filename As String)
End Interface
```

iOS, Android y Windows Phone implementaciones para esta interfaz se mostrará más adelante en la guía.

### <a name="todoitemvb"></a>TodoItem.vb

Esta clase contiene el objeto de negocios que se usará en toda la aplicación. Se definirán en Visual Basic y proyectos que se escriben en C# compartidos con iOS, Android y Windows Phone.

La definición de clase se muestra aquí:

```vb
Public Class TodoItem
    Property ID() As Integer
    Property Name() As String
    Property Notes() As String
    Property Done() As Boolean
End Class
```

El ejemplo usa la serialización XML y la deserialización para cargar y guardar los objetos TodoItem.

### <a name="todoitemmanagervb"></a>TodoItemManager.vb

La clase Manager presenta la API de' ' para el código portable. Proporciona operaciones CRUD básicas para la `TodoItem` clase, pero ninguna implementación de esas operaciones.

```vb
Public Class TodoItemManager
    Private _repository As TodoItemRepositoryXML
    Public Sub New(filename As String, storage As IXmlStorage)
        _repository = New TodoItemRepositoryXML(filename, storage)
    End Sub
    Public Function GetTask(id As Integer) As TodoItem
        Return _repository.GetTask(id)
    End Function
    Public Function GetTasks() As List(Of TodoItem)
        Return New List(Of TodoItem)(_repository.GetTasks())
    End Function
    Public Function SaveTask(item As TodoItem) As Integer
        Return _repository.SaveTask(item)
    End Function
    Public Function DeleteTask(item As TodoItem) As Integer
        Return _repository.DeleteTask(item.ID)
    End Function
End Class
```

El constructor toma una instancia de IXmlStorage como un parámetro. Esto permite que cada plataforma para proporcionar su propia implementación de trabajo mientras se sigue permitiendo que el código portable describen otras funciones que se pueden compartir.

### <a name="todoitemrepositoryvb"></a>TodoItemRepository.vb

La clase de repositorio contiene la lógica para administrar la lista de objetos de TodoItem. El código completo se muestra a continuación: la lógica existe principalmente para administrar un valor de identificador único a través de la TodoItems tal y como se agregan y quitan de la colección.

```vb
Public Class TodoItemRepositoryXML
    Private _filename As String
    Private _storage As IXmlStorage
    Private _tasks As List(Of TodoItem)

    ''' <summary>Constructor</summary>
    Public Sub New(filename As String, storage As IXmlStorage)
        _filename = filename
        _storage = storage
        _tasks = _storage.ReadXml(filename)
    End Sub
    ''' <summary>Inefficient search for a Task by ID</summary>
    Public Function GetTask(id As Integer) As TodoItem
        For t As Integer = 0 To _tasks.Count - 1
            If _tasks(t).ID = id Then
                Return _tasks(t)
            End If
        Next
        Return New TodoItem() With {.ID = id}
    End Function
    ''' <summary>List all the Tasks</summary>
    Public Function GetTasks() As IEnumerable(Of TodoItem)
        Return _tasks
    End Function
    ''' <summary>Save a Task to the Xml file
    ''' Calculates the ID as the max of existing IDs</summary>
    Public Function SaveTask(item As TodoItem) As Integer
        Dim max As Integer = 0
        If _tasks.Count > 0 Then
            max = _tasks.Max(Function(t As TodoItem) t.ID)
        End If
        If item.ID = 0 Then
            item.ID = ++max
            _tasks.Add(item)
        Else
            Dim j = _tasks.Where(Function(t) t.ID = item.ID).First()
            j = item
        End If
        _storage.WriteXml(_tasks, _filename)
        Return max
    End Function
    ''' <summary>Removes the task from the XMl file</summary>
    Public Function DeleteTask(id As Integer) As Integer
        For t As Integer = 0 To _tasks.Count - 1
            If _tasks(t).ID = id Then
                _tasks.RemoveAt(t)
                _storage.WriteXml(_tasks, _filename)
                Return 1
            End If
        Next
        Return -1
    End Function
End Class
```

> [!NOTE]
> Este código es un ejemplo de un mecanismo de almacenamiento de datos muy básica.
> Se proporciona para mostrar cómo puede codificar una biblioteca de clases Portable en una interfaz para tener acceso a funcionalidad específica de la plataforma (en este caso, cargar y guardar un archivo Xml). No pensado para ser una alternativa de base de datos de calidad de producción.

## <a name="ios-android-and-windows-phone-application-projects"></a>iOS, Android y proyectos de aplicación de Windows Phone

Esta sección contiene las implementaciones específicas de la plataforma para la interfaz de IXmlStorage y muestra cómo se utiliza en cada aplicación. Los proyectos de aplicación se escriben en C#.

### <a name="ios-and-android-ixmlstorage"></a>iOS y Android IXmlStorage

Xamarin.iOS y Xamarin.Android proporcionan completa `System.IO` funcionalidad para fácilmente pueda cargar y guardar el archivo Xml mediante la clase siguiente:

```csharp
public class XmlStorageImplementation : IXmlStorage
{
    public XmlStorageImplementation(){}
    public List<TodoItem> ReadXml(string filename)
    {
        if (File.Exists(filename))
        {
            var serializer = new XmlSerializer(typeof(List<TodoItem>));
            using (var stream = new FileStream(filename, FileMode.Open))
            {
                return (List<TodoItem>)serializer.Deserialize(stream);
            }
        }
        return new List<TodoItem>();
    }
    public void WriteXml(List<TodoItem> tasks, string filename)
    {
        var serializer = new XmlSerializer(typeof(List<TodoItem>));
        using (var writer = new StreamWriter(filename))
        {
            serializer.Serialize(writer, tasks);
        }
    }
}
```

En la aplicación de iOS la `TodoItemManager` y `XmlStorageImplementation` se crean en el **AppDelegate.cs** archivo tal como se muestra en este fragmento de código. Las cuatro primeras líneas solo crea la ruta de acceso al archivo donde se almacenarán los datos; las dos últimas líneas muestran las dos clases que se va a crear una instancia.

```csharp
var xmlFilename = "TodoList.xml";
string documentsPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine(documentsPath, "..", "Library"); // Library folder
var path = Path.Combine(libraryPath, xmlFilename);
var xmlStorage = new XmlStorageImplementation();
TaskMgr = new TodoItemManager(path, xmlStorage);
```

En la aplicación Android el `TodoItemManager` y `XmlStorageImplementation` se crean en el **Application.cs** archivo tal como se muestra en este fragmento de código. Las tres primeras líneas solo crea la ruta de acceso al archivo donde se almacenarán los datos; las dos últimas líneas muestran las dos clases que se va a crear una instancia.

```csharp
var xmlFilename = "TodoList.xml";
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
var path = Path.Combine(libraryPath, xmlFilename);
var xmlStorage = new AndroidTodo.XmlStorageImplementation();
TaskMgr = new TodoItemManager(path, xmlStorage);
```

El resto del código de aplicación principalmente se preocupa de la interfaz de usuario y que estás usando la `TaskMgr` clase para cargar y guardar `TodoItem` clases.

### <a name="windows-phone-ixmlstorage"></a>Windows Phone IXmlStorage

Windows Phone no proporciona acceso completo al sistema de archivos del dispositivo, en su lugar exponer la API de IsolatedStorage. El `IXmlStorage` implementación para Windows Phone tiene el siguiente aspecto:

```csharp
public class XmlStorageImplementation : IXmlStorage
{
    public XmlStorageImplementation(){}
    public List<TodoItem> ReadXml(string filename)
    {
        IsolatedStorageFile fileStorage = IsolatedStorageFile.GetUserStoreForApplication();
        if (fileStorage.FileExists(filename))
        {
            var serializer = new XmlSerializer(typeof(List<TodoItem>));
            using (var stream = new StreamReader(new IsolatedStorageFileStream(filename, FileMode.Open, fileStorage)))
            {
                return (List<TodoItem>)serializer.Deserialize(stream);
            }
        }
        return new List<TodoItem>();
    }
    public void WriteXml(List<TodoItem> tasks, string filename)
    {
        IsolatedStorageFile fileStorage = IsolatedStorageFile.GetUserStoreForApplication();
        var serializer = new XmlSerializer(typeof(List<TodoItem>));
        using (var writer = new StreamWriter(new IsolatedStorageFileStream(filename, FileMode.OpenOrCreate, fileStorage)))
        {
            serializer.Serialize(writer, tasks);
        }
    }
}
```

El `TodoItemManager` y `XmlStorageImplementation` se crean en el **App.xaml.cs** archivo tal como se muestra en este fragmento de código.

```csharp
var filename = "TodoList.xml";
var xmlStorage = new XmlStorageImplementation();
TodoMgr = new TodoItemManager(filename, xmlStorage);
```

El resto de la aplicación de Windows Phone consta de Xaml y C# para crear la interfaz de usuario y usar el `TodoMgr` clase para cargar y guardar `TodoItem` objetos.

## <a name="visual-basic-pcl-in-visual-studio-for-mac"></a>Visual Basic PCL en Visual Studio para Mac

Visual Studio para Mac no es compatible con el lenguaje Visual Basic: no se puede crear o compilar proyectos de Visual Basic con Visual Studio para Mac.

Visual Studio para la compatibilidad de Mac de bibliotecas de clases Portable significa que puede hacer referencia a ensamblados PCL que se crearon desde Visual Basic.

Esta sección explica cómo compilar un ensamblado PCL en Visual Studio y, a continuación, asegúrese de que se almacena en un sistema de control de versiones y se hace referencia a otros proyectos.

### <a name="keeping-the-pcl-output-from-visual-studio"></a>Mantener el resultado PCL desde Visual Studio

De forma predeterminada la mayoría sistemas de control de versión (incluidos TFS y Git) estará configurados para pasar por alto la **/bin/** directory, que significa que el ensamblado compilado de PCL no se almacenarán. Esto significa que se necesitaría copiarlo manualmente en cualquier equipo que ejecute Visual Studio para Mac agregar una referencia a él.

Para asegurarse de que el sistema de control de versiones puede almacenar el resultado del ensamblado PCL, puede crear un script posterior a la compilación que se copia en la raíz del proyecto. Este paso posterior a la compilación ayuda a garantizar el ensamblado se puede agregar al control de código fuente y compartirse con otros proyectos fácilmente.

#### <a name="visual-studio-2017"></a>Visual Studio 2017

1. Haga doble clic en el proyecto y elija la **Propiedades > eventos de compilación** sección.

2. Agregar un _posterior a la compilación_ script que copiara la DLL de salida de este proyecto en el directorio raíz del proyecto (que está fuera de **/bin/**). Según la configuración de control de versión, el archivo DLL ahora podrá agregarse al control de código fuente.

  [![](native-apps-images/image6-vs-sml.png "Script de compilación posterior para copiar VB DLL de eventos de compilación")](native-apps-images/image6-vs.png#lightbox)

#### <a name="visual-studio-2015"></a>Visual Studio 2015

1.  Haga doble clic en el proyecto y elija **Propiedades > compilar** , a continuación, asegúrese de todas las configuraciones se selecciona en el cuadro de combinación superior izquierda. Haga clic en el **eventos de compilación...**  botón en la parte inferior derecha.

    [![](native-apps-images/image6.png "La sección de compilación de propiedades de proyecto")](native-apps-images/image6.png#lightbox)

1.  Agregar un script posterior a la compilación que copia la DLL de salida de este proyecto en el directorio raíz del proyecto (que está fuera de **/bin/** ). Según la configuración de control de versión, el archivo DLL ahora podrá agregarse al control de código fuente.

    [![](native-apps-images/image7.png "Ventana de eventos de compilación")](native-apps-images/image7.png#lightbox)

#### <a name="all-versions"></a>Todas las versiones

Próxima vez que compile el proyecto, el ensamblado de biblioteca de clases Portable se copiarán en la raíz del proyecto y cuándo comprobación en/commit/inserción de los cambios del archivo DLL será almacenado (por lo que se pueden descargar en un equipo Mac con Visual Studio para Mac).

  [![](native-apps-images/image8-sml.png "Ubicación del archivo de Visual Basic del ensamblado de salida")](native-apps-images/image8.png#lightbox)


Este ensamblado, a continuación, se agregará a los proyectos de Xamarin en Visual Studio para Mac, aunque el propio lenguaje de Visual Basic no se admite en proyectos de Android o iOS de Xamarin.

### <a name="referencing-the-pcl-in-visual-studio-for-mac"></a>Hacer referencia a la PCL en Visual Studio para Mac

Porque Xamarin no es compatible con Visual Basic no se puede cargar el proyecto PCL (ni la aplicación de Windows Phone) como se muestra en esta captura de pantalla:

 [![](native-apps-images/image9.png "Visual Studio para la solución de Mac")](native-apps-images/image9.png#lightbox)

Podemos todavía incluimos la DLL del ensamblado PCL de Visual Basic en los proyectos Xamarin.iOS y Xamarin.Android:

1.  Haga doble clic en el **referencias** nodo y seleccione **editar referencias...**

    [![](native-apps-images/image10.png "Menú de referencias de proyecto editar")](native-apps-images/image10.png#lightbox)

1.  Seleccione el **.Net ensamblado** pestaña y navegue a la salida de la DLL en el directorio del proyecto de Visual Basic. Aunque Visual Studio para Mac no se puede abrir el proyecto, todos los archivos debería existir de control de código fuente. Haga clic en **agregar** , a continuación, **Aceptar** para agregar este ensamblado a las aplicaciones Android y iOS.

    [![](native-apps-images/image11-sml.png "Haga clic en Agregar y en Aceptar para agregar este ensamblado a las aplicaciones Android y iOS")](native-apps-images/image11.png#lightbox)

1.  IOS y Android aplicaciones ahora pueden incluir la lógica de aplicación proporcionada por la biblioteca de clases Portable de Visual Basic. Esta captura de pantalla muestra una aplicación de iOS que hace referencia la PCL de Visual Basic y tiene código que usa la funcionalidad de esa biblioteca.

    [![](native-apps-images/image12-sml.png "Editar referencias Agregar ventana de ensamblado de .NET")](native-apps-images/image12.png#lightbox)


Si se realizan cambios en el proyecto de Visual Basic en Visual Studio recordar compilar el proyecto, almacenar el archivo DLL del ensamblado resultante en el control de código fuente y, a continuación, extraer ese archivo DLL nuevo desde el control de código fuente en el equipo Mac para que genera Visual Studio para Mac contiene la versión más reciente funcionalidad.


## <a name="summary"></a>Resumen

Este artículo se demuestra cómo utilizar código de Visual Basic en aplicaciones de Xamarin con Visual Studio y bibliotecas de clases Portable. Aunque Xamarin no es compatible directamente con Visual Basic, la compilación de Visual Basic en una PCL permite al código escrito con Visual Basic para incluirse en aplicaciones iOS y Android.

## <a name="related-links"></a>Vínculos relacionados

- [TaskyPortableVB (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB)
- [El desarrollo multiplataforma con .NET Framework (Microsoft)](http://msdn.microsoft.com/en-us/library/gg597391(v=vs.110).aspx)
