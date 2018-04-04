---
title: Bases de datos locales
description: Xamarin.Forms es compatible con aplicaciones orientadas a base de datos mediante el motor de base de datos de SQLite, lo que permite cargar y guardar los objetos en el código compartido. Este artículo describe cómo pueden leer y escribir datos en una base de datos local de SQLite mediante SQLite.Net aplicaciones de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/23/2017
ms.openlocfilehash: 95c5f482e1bf3e55fa4c6fef18b1dbe6274f33e8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="local-databases"></a>Bases de datos locales

_Xamarin.Forms es compatible con aplicaciones orientadas a base de datos mediante el motor de base de datos de SQLite, lo que permite cargar y guardar los objetos en el código compartido. Este artículo describe cómo pueden leer y escribir datos en una base de datos local de SQLite mediante SQLite.Net aplicaciones de Xamarin.Forms._

## <a name="overview"></a>Información general

Xamarin.Forms las aplicaciones pueden utilizar el [NuGet de PCL SQLite.NET](https://www.nuget.org/packages/sqlite-net-pcl/) paquete para incorporar las operaciones de base de datos en el código compartido haciendo referencia a la `SQLite` las clases que se incluyen en NuGet. Las operaciones de base de datos se pueden definir en el proyecto de biblioteca de clases portables (PCL) de la solución de Xamarin.Forms, con los proyectos específicos de plataforma devuelve una ruta de acceso a donde se almacenará la base de datos.

El que acompaña a [aplicación de ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Todo) es una sencilla aplicación de lista de tareas. Las capturas de pantalla siguientes muestran cómo el ejemplo se muestra en cada plataforma:

[![Capturas de pantalla de ejemplo de base de datos de Xamarin.Forms](databases-images/todo-list-sml.png "TodoList primera Page Screenshots")](databases-images/todo-list.png#lightbox "TodoList primera Page Screenshots") [ ![ Capturas de pantalla de ejemplo de base de datos de Xamarin.Forms](databases-images/todo-list-sml.png "TodoList primera Page Screenshots")](databases-images/todo-list.png#lightbox "TodoList primera Page Screenshots")

<a name="Using_SQLite_with_PCL" />

## <a name="using-sqlite"></a>Uso de código

Esta sección muestra cómo agregar paquetes de SQLite.Net NuGet a una solución Xamarin.Forms, escribir métodos para realizar operaciones de base de datos y usar el [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md) para determinar una ubicación para almacenar la base de datos en cada plataforma.

<a name="XamarinForms_PCL_Project" />

### <a name="xamarinsforms-pcl-project"></a>PCL Xamarins.Forms proyecto

Para agregar compatibilidad de código a un proyecto de Xamarin.Forms PCL, use la función de búsqueda de NuGet para buscar **sqlite-net-pcl** e instale el paquete:

![](databases-images/vs2017-sqlite-pcl-nuget.png "Agregar paquete de NuGet SQLite.NET PCL")

Hay un número de paquetes de NuGet con nombres similares, el paquete correcto tiene estos atributos:

- **Creado por:** Frank A. Krueger
- **Id.:** sqlite-net-pcl
- **Vínculo de NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

Una vez agregada la referencia, escribir una interfaz para abstraer la funcionalidad específica de la plataforma, que se usa para determinar la ubicación del archivo de base de datos. La interfaz utilizada en el ejemplo define un método único:

```csharp
public interface IFileHelper
{
  string GetLocalFilePath(string filename);
}
```

Una vez que se ha definido la interfaz, use la [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md) para obtener una implementación y obtener una ruta de acceso de archivo local (tenga en cuenta que esta interfaz no se ha implementado todavía). El código siguiente obtiene una implementación de la `App.Database` propiedad:

```csharp
static TodoItemDatabase database;

public static TodoItemDatabase Database
{
  get
  {
    if (database == null)
    {
      database = new TodoItemDatabase(DependencyService.Get<IFileHelper>().GetLocalFilePath("TodoSQLite.db3"));
    }
    return database;
  }
}
```

El `TodoItemDatabase` constructor se muestra a continuación:

```csharp
public TodoItemDatabase(string dbPath)
{
  database = new SQLiteAsyncConnection(dbPath);
  database.CreateTableAsync<TodoItem>().Wait();
}
```

Este método crea una conexión de base de datos única que se mantiene abierta mientras se ejecuta la aplicación, por lo tanto, evitar los gastos de apertura y cierre el archivo de base de datos cada vez que se realiza una operación de base de datos.

El resto de la `TodoItemDatabase` clase contiene consultas de código que se ejecutan multiplataforma. Código de la consulta de ejemplo se muestra a continuación (más detalles sobre la sintaxis pueden encontrarse en el [SQLite.NET utilizando](~/cross-platform/app-fundamentals/index.md) artículo):

```csharp
public Task<List<TodoItem>> GetItemsAsync()
{
  return database.Table<TodoItem>().ToListAsync();
}

public Task<List<TodoItem>> GetItemsNotDoneAsync()
{
  return database.QueryAsync<TodoItem>("SELECT * FROM [TodoItem] WHERE [Done] = 0");
}

public Task<TodoItem> GetItemAsync(int id)
{
  return database.Table<TodoItem>().Where(i => i.ID == id).FirstOrDefaultAsync();
}

public Task<int> SaveItemAsync(TodoItem item)
{
  if (item.ID != 0)
  {
    return database.UpdateAsync(item);
  }
  else {
    return database.InsertAsync(item);
  }
}

public Task<int> DeleteItemAsync(TodoItem item)
{
  return database.DeleteAsync(item);
}
```

> [!NOTE]
> La ventaja de usar la API de SQLite.Net asincrónica es esa base de datos se mueven las operaciones en subprocesos en segundo plano. Además, no hay ninguna necesidad de escribir simultaneidad adicional que el control de código porque la API se encarga de ello.

Todo el código de acceso de datos se escribe en el proyecto PCL para compartirse en todas las plataformas. Solo una ruta de acceso de archivo local para la base de datos se requiere código específico de la plataforma, tal como se describe en las secciones siguientes.

<a name="PCL_iOS" />

### <a name="ios-project"></a>Proyecto de iOS

Para configurar la aplicación de iOS, agregue el mismo paquete de NuGet al proyecto de iOS mediante el *NuGet* ventana:

![](databases-images/vsmac-sqlite-nuget.png "Agregar paquete de NuGet SQLite.NET PCL")

Es el único código que requiere el `IFileHelper` implementación que determina la ruta de acceso del archivo de datos. El siguiente código coloca el archivo de base de datos de SQLite en el **/bases de datos de biblioteca** carpeta dentro de espacio aislado de la aplicación. Consulte la [iOS trabajar con el sistema de archivos](~/ios/app-fundamentals/file-system.md) documentación para obtener más información sobre los distintos directorios que están disponibles para el almacenamiento.

```csharp
[assembly: Dependency(typeof(FileHelper))]
namespace Todo.iOS
{
    public class FileHelper : IFileHelper
    {
        public string GetLocalFilePath(string filename)
        {
            string docFolder = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
            string libFolder = Path.Combine(docFolder, "..", "Library", "Databases");

            if (!Directory.Exists(libFolder))
            {
                Directory.CreateDirectory(libFolder);
            }

            return Path.Combine(libFolder, filename);
        }
    }
}
```

Tenga en cuenta que el código incluye el `assembly:Dependency` atributo para que esta implementación es reconocible por el `DependencyService`.

<a name="PCL_Android" />

### <a name="android-project"></a>Proyecto de Android

Para configurar la aplicación Android, agregue el mismo paquete de NuGet al proyecto de Android mediante el *NuGet* ventana:

![](databases-images/vsmac-sqlite-nuget.png "Agregar paquete de NuGet SQLite.NET PCL")

Una vez que se ha agregado esta referencia, es el único código que requiere el `IFileHelper` implementación que determina la ruta de acceso del archivo de datos.

```csharp
[assembly: Dependency(typeof(FileHelper))]
namespace Todo.Droid
{
    public class FileHelper : IFileHelper
    {
        public string GetLocalFilePath(string filename)
        {
            string path = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
            return Path.Combine(path, filename);
        }
    }
}
```

<a name="PCL_UWP" />

### <a name="windows-10-universal-windows-platform-uwp"></a>Plataforma universal de Windows (UWP) de Windows 10

Para configurar la aplicación de UWP, agregue el mismo paquete de NuGet al proyecto de UWP mediante la *NuGet* ventana:

![](databases-images/vs2017-sqlite-uwp-nuget.png "Agregar paquete de NuGet SQLite.NET PCL")

Una vez agregada la referencia, implementar la `IFileHelper` interfaz mediante la específica de la plataforma `Windows.Storage` API para determinar la ruta de acceso del archivo de datos.

```csharp
using Windows.Storage;
...

[assembly: Dependency(typeof(FileHelper))]
namespace Todo.UWP
{
    public class FileHelper : IFileHelper
    {
        public string GetLocalFilePath(string filename)
        {
            return Path.Combine(ApplicationData.Current.LocalFolder.Path, filename);
        }
    }
}

```

## <a name="summary"></a>Resumen

Xamarin.Forms es compatible con aplicaciones orientadas a base de datos mediante el motor de base de datos de SQLite, lo que permite cargar y guardar los objetos en el código compartido.

En este artículo se centra en **acceso a** una base de datos de SQLite uso de Xamarin.Forms. Para obtener más información sobre cómo trabajar con SQLite.Net propio, consulte el [acceso a datos: SQLite.NET utilizando](~/cross-platform/app-fundamentals/index.md) documentación. La mayoría del código SQLite.Net es puede compartirse en todas las plataformas; sólo para configurar la ubicación del archivo de base de datos de SQLite necesario funcionalidad específica de la plataforma.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de lista de tareas](https://developer.xamarin.com/samples/xamarin-forms/Todo/)
- [Xamarin.Forms Samples](https://developer.xamarin.com/samples/xamarin-forms/all/) (Ejemplos de Xamarin.Forms)
- [Libro de la base de datos](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/database/database.workbook)
