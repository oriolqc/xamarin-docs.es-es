---
title: Bases de datos Local de Xamarin.Forms
description: Xamarin.Forms es compatible con aplicaciones orientadas a base de datos mediante el motor de base de datos de SQLite, lo que permite cargar y guardar los objetos en el código compartido. Este artículo describe cómo pueden leer y escribir datos en una base de datos local de SQLite mediante SQLite.Net aplicaciones de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: feec4993a0719a083d713e084552b18aead8ee42
ms.sourcegitcommit: eac092f84b603958c761df305f015ff84e0fad44
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/21/2018
ms.locfileid: "36310145"
---
# <a name="xamarinforms-local-databases"></a>Bases de datos Local de Xamarin.Forms

_Xamarin.Forms es compatible con aplicaciones orientadas a base de datos mediante el motor de base de datos de SQLite, lo que permite cargar y guardar los objetos en el código compartido. Este artículo describe cómo pueden leer y escribir datos en una base de datos local de SQLite mediante SQLite.Net aplicaciones de Xamarin.Forms._

## <a name="overview"></a>Información general

Xamarin.Forms las aplicaciones pueden utilizar el [NuGet de PCL SQLite.NET](https://www.nuget.org/packages/sqlite-net-pcl/) paquete para incorporar las operaciones de base de datos en el código compartido haciendo referencia a la `SQLite` las clases que se incluyen en NuGet. Las operaciones de base de datos se pueden definir en el proyecto de biblioteca estándar de .NET de la solución de Xamarin.Forms.

El que acompaña a [aplicación de ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Todo) es una sencilla aplicación de lista de tareas. Las capturas de pantalla siguientes muestran cómo el ejemplo se muestra en cada plataforma:

[![Capturas de pantalla de ejemplo de base de datos de Xamarin.Forms](databases-images/todo-list-sml.png "TodoList primera Page Screenshots")](databases-images/todo-list.png#lightbox "TodoList primera Page Screenshots") [ ![ Capturas de pantalla de ejemplo de base de datos de Xamarin.Forms](databases-images/todo-list-sml.png "TodoList primera Page Screenshots")](databases-images/todo-list.png#lightbox "TodoList primera Page Screenshots")

<a name="Using_SQLite_with_PCL" />

## <a name="using-sqlite"></a>Uso de código

Para agregar compatibilidad de SQLite en una biblioteca de Xamarin.Forms .NET estándar, use la función de búsqueda de NuGet para buscar **sqlite-net-pcl** e instalar el paquete más reciente:

![Agregar paquete de NuGet SQLite.NET PCL](databases-images/vs2017-sqlite-pcl-nuget.png "Agregar paquete de NuGet SQLite.NET PCL")

Hay un número de paquetes de NuGet con nombres similares, el paquete correcto tiene estos atributos:

- **Creado por:** Frank A. Krueger
- **Id.:** sqlite-net-pcl
- **Vínculo de NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> A pesar de que el nombre del paquete, utilice el **sqlite-net-pcl** paquete de NuGet incluso en los proyectos de .NET estándar.

Una vez agregada la referencia, agregue una propiedad a la `App` clase que devuelve una ruta de acceso de archivo local para almacenar la base de datos:

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

El `TodoItemDatabase` constructor, que toma la ruta de acceso para el archivo de base de datos como un argumento, se muestra a continuación:

```csharp
public TodoItemDatabase(string dbPath)
{
  database = new SQLiteAsyncConnection(dbPath);
  database.CreateTableAsync<TodoItem>().Wait();
}
```

La ventaja de exposición de la base de datos sea un singleton que se crea una conexión de base de datos única que se mantiene abierta mientras la aplicación se ejecuta, por lo tanto, evitar los gastos de apertura y cierre el archivo de base de datos cada vez que una operación de base de datos se lleva a cabo.

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

## <a name="summary"></a>Resumen

Xamarin.Forms es compatible con aplicaciones orientadas a base de datos mediante el motor de base de datos de SQLite, lo que permite cargar y guardar los objetos en el código compartido.

En este artículo se centra en **acceso a** una base de datos de SQLite uso de Xamarin.Forms. Para obtener más información sobre cómo trabajar con SQLite.Net propio, consulte el [SQLite.NET en Android](~/android/data-cloud/data-access/using-sqlite-orm.md) o [SQLite.NET en iOS](~/ios/data-cloud/data/using-sqlite-orm.md) documentación.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de lista de tareas](https://developer.xamarin.com/samples/xamarin-forms/Todo/)
- [Xamarin.Forms Samples](https://developer.xamarin.com/samples/xamarin-forms/all/) (Ejemplos de Xamarin.Forms)
- [Libro de la base de datos](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/database/database.workbook)
