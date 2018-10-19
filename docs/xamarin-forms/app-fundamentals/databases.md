---
title: Bases de datos Local de Xamarin.Forms
description: Xamarin.Forms es compatible con aplicaciones basadas en la base de datos con el motor de base de datos de SQLite, lo que permite cargar y guardar los objetos en código compartido. En este artículo se describe cómo pueden leer y escribir datos en una base de datos SQLite local mediante SQLite.Net las aplicaciones de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: 05c77c4c47841a897d0d1de5c3ba004db524ea2d
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2018
ms.locfileid: "36310145"
---
# <a name="xamarinforms-local-databases"></a>Bases de datos Local de Xamarin.Forms

_Xamarin.Forms es compatible con aplicaciones basadas en la base de datos con el motor de base de datos de SQLite, lo que permite cargar y guardar los objetos en código compartido. En este artículo se describe cómo pueden leer y escribir datos en una base de datos SQLite local mediante SQLite.Net las aplicaciones de Xamarin.Forms._

## <a name="overview"></a>Información general

Las aplicaciones de Xamarin.Forms pueden usar el [NuGet de PCL de SQLite.NET](https://www.nuget.org/packages/sqlite-net-pcl/) paquete para incorporar las operaciones de base de datos en el código compartido haciendo referencia a la `SQLite` las clases que se incluyen en el paquete NuGet. Las operaciones de base de datos se pueden definir en el proyecto de biblioteca estándar de .NET de la solución de Xamarin.Forms.

Que la acompaña [aplicación de ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Todo) es una sencilla aplicación de lista de tareas. Las capturas de pantalla siguientes muestran cómo el ejemplo aparece en cada plataforma:

[![Capturas de pantalla de ejemplo de base de datos de Xamarin.Forms](databases-images/todo-list-sml.png "TodoList primera Page Screenshots")](databases-images/todo-list.png#lightbox "TodoList primera Page Screenshots") [ ![ Capturas de pantalla de ejemplo de base de datos de Xamarin.Forms](databases-images/todo-list-sml.png "TodoList primera Page Screenshots")](databases-images/todo-list.png#lightbox "TodoList primera Page Screenshots")

<a name="Using_SQLite_with_PCL" />

## <a name="using-sqlite"></a>Usar SQLite

Para agregar compatibilidad de SQLite en una biblioteca de Xamarin.Forms .NET Standard, use la función de búsqueda de NuGet para buscar **sqlite-net-pcl** e instalar el paquete más reciente:

![Agregar paquete de NuGet SQLite.NET PCL](databases-images/vs2017-sqlite-pcl-nuget.png "Agregar paquete de NuGet SQLite.NET PCL")

Hay una serie de paquetes de NuGet con nombres similares, el paquete correcto tiene estos atributos:

- **Creado por:** Frank A. Krueger
- **Id.:** sqlite-net-pcl
- **Vínculo de NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> A pesar de que el nombre del paquete, utilice el **sqlite-net-pcl** paquete NuGet incluso en los proyectos de .NET Standard.

Una vez agregada la referencia, agregue una propiedad a la `App` clase que devuelve una ruta de acceso local para almacenar la base de datos:

```csharp
static TodoItemDatabase database;

public static TodoItemDatabase Database
{
  get
  {
    if (database == null)
    {
      database = new TodoItemDatabase(
        Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "TodoSQLite.db3"));
    }
    return database;
  }
}
```

El `TodoItemDatabase` constructor, que toma la ruta de acceso del archivo de base de datos como un argumento, se muestra a continuación:

```csharp
public TodoItemDatabase(string dbPath)
{
  database = new SQLiteAsyncConnection(dbPath);
  database.CreateTableAsync<TodoItem>().Wait();
}
```

La ventaja de exposición de la base de datos como un singleton es que se crea una conexión de base de datos única que se mantiene abierta mientras la aplicación se ejecuta, por lo tanto, evitar el gasto de apertura y cierre el archivo de base de datos cada vez que una operación de base de datos se lleva a cabo.

El resto de la `TodoItemDatabase` clase contiene consultas de SQLite que se ejecutan entre plataformas. A continuación se muestra el código de ejemplo de consulta (se puede encontrar más información sobre la sintaxis en [uso de SQLite.NET con Xamarin.iOS](~/ios/data-cloud/data/using-sqlite-orm.md).

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
> La ventaja de usar la API de SQLite.Net asincrónica es esa base de datos se mueven las operaciones a subprocesos en segundo plano. Además, no hay ninguna necesidad de escribir más simultaneidad control de código porque la API se encarga de ello.

## <a name="summary"></a>Resumen

Xamarin.Forms es compatible con aplicaciones basadas en la base de datos con el motor de base de datos de SQLite, lo que permite cargar y guardar los objetos en código compartido.

En este artículo se centra en **acceso** una base de datos SQLite mediante Xamarin.Forms. Para obtener más información sobre cómo trabajar con SQLite.Net propio, consulte el [SQLite.NET en Android](~/android/data-cloud/data-access/using-sqlite-orm.md) o [SQLite.NET en iOS](~/ios/data-cloud/data/using-sqlite-orm.md) documentación.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo "todo"](https://developer.xamarin.com/samples/xamarin-forms/Todo/)
- [Xamarin.Forms Samples](https://developer.xamarin.com/samples/xamarin-forms/all/) (Ejemplos de Xamarin.Forms)

