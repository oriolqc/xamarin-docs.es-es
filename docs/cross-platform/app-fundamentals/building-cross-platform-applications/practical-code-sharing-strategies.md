---
title: 'Parte 5: prácticas de uso compartido estrategias de código'
ms.prod: xamarin
ms.assetid: 328D042A-FF78-A7B6-1574-B5AF49A1AADB
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 234f6399a163572538755c41e4c58cf0a80e0d3e
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="part-5---practical-code-sharing-strategies"></a>Parte 5: prácticas de uso compartido estrategias de código

Esta sección proporciona ejemplos de cómo compartir código para escenarios comunes de aplicación.



## <a name="data-layer"></a>Capa de datos

La capa de datos consta de un motor de almacenamiento y métodos para leer y escribir información. Por el código de compatibilidad de rendimiento, flexibilidad y multiplataforma motor de base de datos se recomienda para las aplicaciones de Xamarin y multiplataforma.
Se ejecuta en una amplia variedad de plataformas, como Windows, Android, iOS y Mac.


### <a name="sqlite"></a>SQLite

SQLite es una implementación de base de datos de código abierto. La documentación y el origen pueden encontrarse en [SQLite.org](http://www.sqlite.org/). Soporte de SQLite está disponible en cada plataforma móvil:

-  **iOS** – integrada en el sistema operativo.
- **Android** – integrada en el sistema operativo desde Android 2.2 (API nivel 10).
- **Windows** : consulte la [SQLite para extensión de la plataforma Universal de Windows](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936).


Incluso con el motor de base de datos disponible en todas las plataformas, los métodos nativos para tener acceso a la base de datos son diferentes. Tanto iOS y Android proporcionan API integradas para tener acceso a código que podría ser usada de Xamarin.iOS o Xamarin.Android, sin embargo el uso de los métodos nativos de SDK no ofrece ninguna capacidad de compartir código (excepto quizás las consultas SQL por sí mismos, suponiendo que se almacenan como cadenas) . Para obtener más información acerca de la búsqueda de la funcionalidad de base de datos nativa para `CoreData` en de Android o iOS `SQLiteOpenHelper` clase; dado que estas opciones no son multiplataforma quedan fuera del ámbito de este documento.



### <a name="adonet"></a>ADO.NET

Compatibilidad con Xamarin.iOS y Xamarin.Android `System.Data` y `Mono.Data.Sqlite` (consulte la Xamarin.iOS [documentación](~/ios/data-cloud/system.data.md) para obtener más información).
Usar estos espacios de nombres, podrá escribir código ADO.NET que funcione en ambas plataformas. Editar las referencias del proyecto para incluir `System.Data.dll` y `Mono.Data.Sqlite.dll` y agregue estas instrucciones using a su código:

```csharp
using System.Data;
using Mono.Data.Sqlite;
```

A continuación, el código de ejemplo siguiente funcionará:

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "items.db3");
bool exists = File.Exists (dbPath);
if (!exists)
    SqliteConnection.CreateFile (dbPath);
var connection = new SqliteConnection ("Data Source=" + dbPath);
connection.Open ();
if (!exists) {
    // This is the first time the app has run and/or that we need the DB.
    // Copy a "template" DB from your assets, or programmatically create one like this:
    var commands = new[]{
        "CREATE TABLE [Items] (Key ntext, Value ntext);",
        "INSERT INTO [Items] ([Key], [Value]) VALUES ('sample', 'text')"
    };
    foreach (var command in commands) {
        using (var c = connection.CreateCommand ()) {
            c.CommandText = command;
            c.ExecuteNonQuery ();
        }
    }
}
// use `connection`... here, we'll just append the contents to a TextView
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT [Key], [Value] from [Items]";
    var r = contents.ExecuteReader ();
    while (r.Read ())
        Console.Write("\n\tKey={0}; Value={1}",
                r ["Key"].ToString (),
                r ["Value"].ToString ());
}
connection.Close ();
```

Las implementaciones reales de ADO.NET obviamente se dividirá entre los distintos métodos y clases (en este ejemplo es solo con fines de demostración).



### <a name="sqlite-net--cross-platform-orm"></a>SQLite-NET-ORM multiplataforma

Un ORM (o un asignador relacional de objetos) intenta simplificar el almacenamiento de datos que se modela en clases. En lugar de escribir manualmente las consultas SQL que crear tablas o seleccione, campos de datos de INSERCIÓN y eliminación que se extraen manualmente desde la clase y propiedades, un ORM agrega una capa de código que lo hace automáticamente. Use la reflexión para examinar la estructura de las clases, un ORM puede crear automáticamente tablas y columnas que coinciden con una clase y generan consultas para leer y escribir los datos. Esto permite que el código de aplicación para enviar y recuperar las instancias de objeto para el ORM, que se encarga de todas las operaciones de SQL bajo el paraguas de simplemente.

SQLite NET actúa como un simple ORM que le permitirá guardar y recuperar las clases en código. Oculta la complejidad de cross platform SQLite acceso con una combinación de directivas de compilador y otros trucos.

Características de red de SQLite:

-  Las tablas se definen mediante la adición de atributos a las clases del modelo.
-  Una instancia de base de datos se representa mediante una subclase de `SQLiteConnection` , la clase principal de la biblioteca de red de SQLite.
-  Pueden insertar datos, consultar y eliminarse mediante objetos. Ninguna de las instrucciones SQL es necesaria (aunque puede escribir instrucciones SQL si es necesario).
-  Consultas básicas de Linq pueden realizarse en las colecciones devueltas por la red de SQLite.


Está disponible en el código fuente y la documentación de SQLite NET [Net SQLite en github](https://github.com/praeclarum/sqlite-net) y se ha implementado en ambos casos de estudio. Un ejemplo sencillo de código de SQLite NET (desde el *Tasky Pro* caso práctico) se muestra a continuación.

En primer lugar, la `TodoItem` clase usa atributos para definir un campo para que sea una clave principal de la base de datos:

```csharp
public class TodoItem : IBusinessEntity
{
    public TodoItem () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

Esto permite un `TodoItem` tabla que se creará con la siguiente línea de código (y no hay instrucciones SQL) en un `SQLiteConnection` instancia:

```csharp
CreateTable<TodoItem> ();
```

Datos de la tabla también se pueden manipular con otros métodos en el `SQLiteConnection` (de nuevo, sin necesidad de instrucciones SQL):

```csharp
Insert (TodoItem); // 'task' is an instance with data populated in its properties
Update (TodoItem); // Primary Key field must be populated for Update to work
Table<TodoItem>.ToList(); // returns all rows in a collection
```

Vea el código de origen del caso práctico para obtener ejemplos completos.



## <a name="file-access"></a>Acceso a archivos

Acceso de archivo tiene la certeza de que una parte fundamental de cualquier aplicación. Ejemplos comunes de archivos que pueden formar parte de una aplicación se incluyen:

-  Archivos de base de datos de SQLite.
-  Generado por el usuario datos (texto, imágenes, audio, vídeo).
-  Datos descargados para almacenar en caché (imágenes, html o archivos PDF).




### <a name="systemio-direct-access"></a>Acceso directo de System.IO

Xamarin.iOS y Xamarin.Android permitirán el acceso de sistema de archivos mediante clases en el `System.IO` espacio de nombres.

Cada plataforma tiene restricciones de acceso diferentes que deben considerar con detenimiento:

-  ejecutan aplicaciones de iOS en un espacio aislado con acceso de sistema de archivos muy restringido. Apple más dicta cómo deben utilizar el sistema de archivos mediante la especificación de ciertas ubicaciones de copia de seguridad (y otras no). Hacer referencia a la [trabajar con el sistema de archivos en Xamarin.iOS](~/ios/app-fundamentals/file-system.md) guía para obtener más detalles.
-  Android también restringe el acceso a ciertos directorios relacionados con la aplicación, pero también admite medios externos (p. ej. Las tarjetas SD) y obtener acceso a datos compartidos.
-  Windows Phone 8 (Silverlight) no permiten el acceso de archivo directa: solo se pueden manipular archivos utilizando `IsolatedStorage`.
-  Proyectos de WinRT de Windows 8.1 y Windows 10 UWP solo ofrecen operaciones de archivo asincrónicas a través de `Windows.Storage` API, que son diferentes de las otras plataformas.

#### <a name="example-for-ios-and-android"></a>Ejemplo de iOS y Android

Un ejemplo trivial que escribe y lee un archivo de texto se muestra a continuación.
Usar `Environment.GetFolderPath` permite que el mismo código que se ejecuta en iOS y Android, que devuelven un directorio válido según sus convenciones del sistema de archivos.

```csharp
string filePath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "MyFile.txt");
System.IO.File.WriteAllText (filePath, "Contents of text file");
Console.WriteLine (System.IO.ReadAllText (filePath));
```

Hacer referencia a la Xamarin.iOS [trabajar con el sistema de archivos](~/ios/app-fundamentals/file-system.md) documento para obtener más información sobre las funciones de sistema de archivos específicos de iOS. Al escribir código de acceso de archivo entre plataformas, recuerde que algunos sistemas de archivos distinguen mayúsculas de minúsculas y tienen separadores de directorio diferentes. Es recomendable que utilice siempre la misma grafía para nombres de archivo y la `Path.Combine()` método al construir rutas de acceso de archivo o directorio.



### <a name="windowsstorage-for-windows-8-and-windows-10"></a>Windows.Storage para Windows 8 y Windows 10

El *crear aplicaciones móviles con Xamarin.Forms* [libreta](https://developer.xamarin.com/r/xamarin-forms/book/)
[capítulo 20. Async y E/S de archivos](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf) incluye [ejemplos para Windows 8.1 y Windows 10](https://github.com/xamarin/xamarin-forms-book-preview-2/tree/master/Chapter20).

Con un [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md) es posible leer y archivos en estas plataformas a través de las API admitidas del archivo:

```csharp
StorageFolder localFolder = ApplicationData.Current.LocalFolder;
IStorageFile storageFile = await localFolder.CreateFileAsync("MyFile.txt",
                                        CreationCollisionOption.ReplaceExisting);
await FileIO.WriteTextAsync(storageFile, "Contents of text file");
```

Hacer referencia a la [capítulo del libro](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf) para obtener más detalles.


<a name="Isolated_Storage" />

### <a name="isolated-storage-on-windows-phone-7--8-silverlight"></a>Almacenamiento aislado en Windows Phone 7 y 8 (Silverlight)

El almacenamiento aislado es una API común para guardar y cargar archivos a través de todas las plataformas antiguas de Windows Phone, Android y iOS.

Es el mecanismo predeterminado para el acceso de archivos en Windows Phone (Silverlight) que se ha implementado en Xamarin.iOS y Xamarin.Android para permitir que el código de acceso a archivos comunes que se va a escribir. El `System.IO.IsolatedStorage` clase puede hacer referencia a través de las tres plataformas en un [proyecto compartido](~/cross-platform/app-fundamentals/shared-projects.md).

Hacer referencia a la [las información general de almacenamiento aislado para Windows Phone](http://msdn.microsoft.com/library/windowsphone/develop/ff402541(v=vs.105).aspx) para obtener más información.

Las API de almacenamiento aislado no están disponibles en [bibliotecas de clases Portable](~/cross-platform/app-fundamentals/pcl.md). Es una alternativa para PCL el [PCLStorage NuGet](https://pclstorage.codeplex.com/)



### <a name="cross-platform-file-access-in-pcls"></a>Acceso a los archivos de multiplataforma en PCLs

También hay un Nuget de PCL compatible: [PCLStorage](https://www.nuget.org/packages/PCLStorage/) – ese acceso a los archivos de multiplataforma instalaciones para plataformas admitidas de Xamarin y las API de Windows más reciente.


## <a name="network-operations"></a>Operaciones de red

Mayoría de aplicaciones móviles tendrá componente de red, por ejemplo:

-  Descargar imágenes, audio y vídeo (p. ej. vistas en miniatura, fotos, música).
-  Descarga de documentos (p. ej. HTML, PDF).
-  Cargando los datos de usuario (por ejemplo, fotos o texto).
-  Acceso a servicios web o entidad 3rd API (incluidos SOAP, XML o JSON).


.NET Framework proporciona algunas clases diferentes para tener acceso a recursos de red: `HttpClient`, `WebClient`, y `HttpWebRequest`.

### <a name="httpclient"></a>HttpClient

El `HttpClient` clase en el `System.Net.Http` espacio de nombres está disponible en la mayoría de las plataformas Windows, Xamarin.iOS y Xamarin.Android. Hay un [Nuget de biblioteca de cliente HTTP de Microsoft](https://www.nuget.org/packages/Microsoft.Net.Http/) que puede usarse para poner esta API en bibliotecas de clases Portable (y Silverlight de Windows Phone 8).

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get, "https://xamarin.com");
var response = await myClient.SendAsync(request);
```

### <a name="webclient"></a>WebClient

La `WebClient` clase proporciona una API sencilla para recuperar datos remotos desde servidores remotos.

Operaciones de Windows Platofrm universales *debe* ser asincrónico, aunque Xamarin.iOS y Xamarin.Android admiten las operaciones sincrónicas (que se pueden efectuar en subprocesos en segundo plano).

El código de un simple asincrónico `WebClient` operación es:

```csharp
var webClient = new WebClient ();
webClient.DownloadStringCompleted += (sender, e) =>
{
    var resultString = e.Result;
    // do something with downloaded string, do UI interaction on main thread
};
webClient.Encoding = System.Text.Encoding.UTF8;
webClient.DownloadStringAsync (new Uri ("http://some-server.com/file.xml"));
```

 `WebClient` También tiene `DownloadFileCompleted` y `DownloadFileAsync` para recuperar datos binarios.

<a name="HttpWebRequest" />

### <a name="httpwebrequest"></a>HttpWebRequest

`HttpWebRequest` ofrece personalización más que `WebClient` y así requiere más código para usar.

El código de un sencillo sincrónico `HttpWebRequest` operación es:

```csharp
var request = HttpWebRequest.Create(@"http://some-server.com/file.xml ");
request.ContentType = "text/xml";
request.Method = "GET";
using (HttpWebResponse response = request.GetResponse() as HttpWebResponse)
{
    if (response.StatusCode != HttpStatusCode.OK)
        Console.WriteLine("Error fetching data. Server returned status code: {0}", response.StatusCode);
    using (StreamReader reader = new StreamReader(response.GetResponseStream()))
    {
        var content = reader.ReadToEnd();
        // do something with downloaded string, do UI interaction on main thread
    }
}
```

Hay un ejemplo en nuestra [documentación de servicios Web](~/cross-platform/data-cloud/web-services/index.md).

 <a name="Reachability" />


### <a name="reachability"></a>Accesibilidad

Dispositivos móviles funcionan en una variedad de condiciones de red de Wi-Fi rápida o las conexiones de 4G a áreas de recepción deficiente y vínculos de datos de borde lentos. Por este motivo, es recomendable para detectar si la red está disponible y si es así, ¿qué tipo de red está disponible, antes de intentar conectarse a servidores remotos.

Las acciones que una aplicación móvil puede tardar en estos casos son:

-  Si la red no está disponible, avisar al usuario. Si se ha deshabilitado (p. ej manualmente. Modo de avión o desactivar Wi-Fi), a continuación, puede resolver el problema.
-  Si la conexión es 3G, las aplicaciones pueden comportarse de manera diferente (por ejemplo, Apple no permitir aplicaciones más de 20Mb para descargarse más de 3G). Las aplicaciones pudieron utilizar esta información para advertir al usuario sobre la descarga excesiva el tiempo de espera al recuperar los archivos de gran tamaño.
-  Incluso si la red está disponible, es recomendable comprobar la conectividad con el servidor de destino antes de iniciar otras solicitudes. Esto se impiden operaciones de red de la aplicación el tiempo de espera repetidamente y también permite un mensaje de error más informativo que se mostrará al usuario.


Hay un [Xamarin.iOS ejemplo](https://github.com/xamarin/monotouch-samples/tree/master/ReachabilitySample) disponibles (que se basa en de Apple [código de ejemplo de accesibilidad](http://developer.apple.com/library/ios/#samplecode/Reachability/Introduction/Intro.html) ) para ayudar a detectar la disponibilidad de la red.


## <a name="webservices"></a>Servicios Web

Consulte la documentación en [trabajar con servicios Web](~/cross-platform/data-cloud/web-services/index.md), donde abordan las obtiene acceso a REST, los extremos SOAP y WCF que usan Xamarin.iOS. Es posible para solicitudes de servicio web de mano elaborar y analizar las respuestas, sin embargo, hay disponibles para hacer esto mucho más simples, como Azure, RestSharp y ServiceStack de bibliotecas. Pueden tener acceso a operaciones de WCF incluso básicas en aplicaciones de Xamarin.

### <a name="azure"></a>Azure

Microsoft Azure es una plataforma de nube que proporciona una gran variedad de servicios para aplicaciones móviles, incluido el almacenamiento de datos, sincronización y las notificaciones de inserción.

Visite [azure.microsoft.com](https://azure.microsoft.com/) para probarlo de forma gratuita.

### <a name="restsharp"></a>RestSharp

RestSharp es una biblioteca de .NET que puede incluirse en las aplicaciones móviles para proporcionar a un cliente REST que simplifica el acceso a servicios web. Ayuda a proporcionando una API sencilla para solicitar datos y analizar la respuesta REST. Puede ser útil RestSharp

El [sitio Web de RestSharp](http://restsharp.org/) contiene [documentación](https://github.com/restsharp/RestSharp/wiki) acerca de cómo implementar un cliente REST con RestSharp.
RestSharp se proporcionan ejemplos de Xamarin.iOS y Xamarin.Android en [github](https://github.com/restsharp/RestSharp/).

También hay un fragmento de código Xamarin.iOS nuestro [documentación de servicios Web](~/cross-platform/data-cloud/web-services/index.md).

 <a name="ServiceStack" />


### <a name="servicestack"></a>ServiceStack

A diferencia de RestSharp, ServiceStack es tanto una solución de servidor para hospedar un servicio web, así como una biblioteca de cliente que se pueden implementar en las aplicaciones móviles para tener acceso a esos servicios.

El [sitio Web de ServiceStack](http://servicestack.net/) Explique el propósito del proyecto y vínculos a documentos y ejemplos de código. Los ejemplos incluyen una implementación de servidor completa de un servicio web, así como diversas aplicaciones del lado cliente que pueden tener acceso a él.

Hay un [Xamarin.iOS ejemplo](http://www.servicestack.net/monotouch/remote-info/) en el sitio Web ServiceStack y un fragmento de código en nuestro [documentación de servicios Web](~/cross-platform/data-cloud/web-services/index.md).


### <a name="wcf"></a>WCF

Herramientas de Xamarin pueden ayudarle a utilizar algunos servicios de Windows Communication Foundation (WCF). En general, Xamarin es compatible con el mismo subconjunto de cliente de WCF que se suministra con el tiempo de ejecución de Silverlight. Esto incluye las implementaciones más comunes de codificación y protocolo de WCF: texto codificado de mensajes SOAP a través de HTTP utilizando el protocolo de transporte la `BasicHttpBinding`.

Debido al tamaño y complejidad del marco de trabajo WCF, es posible que haya implementaciones de servicio actuales y futuros que pertenecen fuera del ámbito admitido por dominio de subconjunto de cliente de Xamarin. Además, la compatibilidad WCF requiere el uso de herramientas solo está disponibles en un entorno de Windows para generar al proxy.

 <a name="Threading" />


## <a name="threading"></a>Subprocesos

La capacidad de respuesta de la aplicación es importante para las aplicaciones móviles: los usuarios esperan que las aplicaciones para cargar y realizar rápidamente. Una pantalla "inmovilizada" que va a aparecer deja de aceptar la entrada del usuario para indicar que la aplicación se ha bloqueado, por lo que es importante no atar el subproceso de interfaz de usuario con llamadas de bloqueo de larga duración, como las solicitudes de red o las operaciones locales lentas (por ejemplo, descomprimir un archivo). En concreto, el proceso de inicio no debe contener tareas de larga duración: todas las plataformas móviles elimina una aplicación que tarda demasiado tiempo en cargarse.

Esto significa que la interfaz de usuario debe implementar un indicador de progreso de' ' o de lo contrario 'utilizable' interfaz de usuario que es más rápido mostrar y las tareas asincrónicas para llevar a cabo operaciones en segundo plano. Ejecutar tareas en segundo plano, requiere el uso de subprocesos, lo que significa que las necesidades de las tareas de segundo plano una manera para comunicarse de vuelta con el subproceso principal para indicar el progreso o cuando ha completado.

 <a name="Parallel_Task_Library" />


### <a name="parallel-task-library"></a>Biblioteca de tareas paralelas

Las tareas creadas con la biblioteca de tareas de procesamiento paralelo pueden ejecutar de forma asincrónica y devolver en su subproceso de llamada, que sean muy útiles para la activación de las operaciones de ejecución prolongada sin bloquear la interfaz de usuario.

Una operación de tareas paralelas simples podría tener este aspecto:

```csharp
using System.Threading.Tasks;
void MainThreadMethod ()
{
    Task.Factory.StartNew (() => wc.DownloadString ("http://...")).ContinueWith (
        t => label.Text = t.Result, TaskScheduler.FromCurrentSynchronizationContext()
    );
}
```

La clave es `TaskScheduler.FromCurrentSynchronizationContext()` que va a reutilizar el SynchronizationContext.Current del subproceso llamando al método (aquí el subproceso principal que se está ejecutando `MainThreadMethod`) como una manera de calcular las referencias de llamadas posteriores a ese subproceso. Esto significa que si se llama al método en el subproceso de interfaz de usuario, se ejecutará la `ContinueWith` operación en el subproceso de interfaz de usuario.

Si el código está iniciando las tareas de otros subprocesos, utilice el modelo siguiente para crear una referencia al subproceso de interfaz de usuario y la tarea puede seguir llamando a volver a él:

```csharp
static Context uiContext = TaskScheduler.FromCurrentSynchronizationContext();
```

 <a name="Invoking_on_the_UI_Thread" />


### <a name="invoking-on-the-ui-thread"></a>Invocar en el subproceso de interfaz de usuario

Para el código que no utiliza la biblioteca de tareas paralelas, cada plataforma tiene su propia sintaxis para las operaciones de cálculo de referencias para el subproceso de interfaz de usuario:

-  **iOS** : `owner.BeginInvokeOnMainThread(new NSAction(action))`
-  **Android** : `owner.RunOnUiThread(action)`
-  **Xamarin.Forms** : `Device.BeginInvokeOnMainThread(action)`
-  **Windows** : `Deployment.Current.Dispatcher.BeginInvoke(action)`



IOS y Android sintaxis requiere una clase 'context' esté disponible, lo que significa que el código debe pasar este objeto a cualquier método que requiera una devolución de llamada en el subproceso de interfaz de usuario.

Para realizar las llamadas de subprocesos de interfaz de usuario en el código compartido, siga el [IDispatchOnUIThread ejemplo](http://www.slideshare.net/follesoe/cross-platform-mobile-apps-using-net) (cortesía de [ @follesoe ](http://jonas.follesoe.no/)). Declarar y un programa para un `IDispatchOnUIThread` de interfaz en el código compartido y, a continuación, implementar las clases específicas de plataforma como se muestra aquí:

```csharp
// program to the interface in shared code
public interface IDispatchOnUIThread {
    void Invoke (Action action);
}
// iOS
public class DispatchAdapter : IDispatchOnUIThread {
    public readonly NSObject owner;
    public DispatchAdapter (NSObject owner) {
        this.owner = owner;
    }
    public void Invoke (Action action) {
        owner.BeginInvokeOnMainThread(new NSAction(action));
    }
}
// Android
public class DispatchAdapter : IDispatchOnUIThread {
    public readonly Activity owner;
    public DispatchAdapter (Activity owner) {
        this.owner = owner;
    }
    public void Invoke (Action action) {
        owner.RunOnUiThread(action);
    }
}
// WP7
public class DispatchAdapter : IDispatchOnUIThread {
    public void Invoke (Action action) {
        Deployment.Current.Dispatcher.BeginInvoke(action);
    }
}
```

Deben usar los desarrolladores de Xamarin.Forms [ `Device.BeginInvokeOnMainThread` ](~/xamarin-forms/platform/device.md#Device_BeginInvokeOnMainThread) en el código común (proyectos compartidos o PCL).

 <a name="Platform_and_Device_Capabilities_and_Degradation" />


## <a name="platform-and-device-capabilities-and-degradation"></a>Plataforma y las capacidades del dispositivo y degradación

Más ejemplos específicos de trabajar con distintas capacidades se proporcionan en la documentación de capacidades de la plataforma. Ocupa de detectar distintas capacidades y cómo degradar una aplicación para proporcionar una buena experiencia del usuario, incluso cuando la aplicación no puede funcionar en todo su potencial de forma correcta.
