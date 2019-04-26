---
title: Sincronizar datos sin conexión con Azure Mobile Apps
description: En este artículo se explica cómo agregar la funcionalidad de sincronización sin conexión a una aplicación de Xamarin.Forms que consume un back-end de Azure Mobile Apps.
ms.prod: xamarin
ms.assetid: DBB343B0-2709-4C20-A669-5522B9956D9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/02/2017
ms.openlocfilehash: 6080b4dc152558d6f532399cee7424670c588c28
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61319583"
---
# <a name="synchronizing-offline-data-with-azure-mobile-apps"></a>Sincronizar datos sin conexión con Azure Mobile Apps

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthOfflineSync/)

_Sincronización sin conexión permite a los usuarios interactuar con una aplicación móvil, ver, agregar o modificar datos, incluso cuando no hay una conexión de red. Los cambios se almacenan en una base de datos local y una vez que el dispositivo está en línea, se pueden sincronizar los cambios con la instancia de Azure Mobile Apps. En este artículo se explica cómo agregar la funcionalidad de sincronización sin conexión a una aplicación de Xamarin.Forms._

## <a name="overview"></a>Información general

El [SDK de Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) proporciona el `IMobileServiceTable` interfaz, que representa las operaciones que se pueden realizar en las tablas almacenadas en la instancia de Azure Mobile Apps. Estas operaciones conexión directamente a la instancia de Azure Mobile Apps y se producirá un error si el dispositivo móvil no tiene una conexión de red.

Para admitir la sincronización sin conexión, es compatible con el SDK de Azure Mobile Client *sincronizar tablas*, que proporciona el `IMobileServiceSyncTable` interfaz. Esta interfaz proporciona la misma creación, lectura, actualización, las operaciones de eliminación (CRUD) como el `IMobileServiceTable` interfaz, pero las operaciones de leer o escribir en un almacén local. El almacén local no se rellena con datos nuevos de la instancia de Azure Mobile Apps hasta que haya una llamada a *extracción* datos. De forma similar, los datos no se enviarán a la instancia de Azure Mobile Apps hasta que haya una llamada a *inserción* cambios locales.

Sincronización sin conexión también incluye compatibilidad para detectar los conflictos cuando ha cambiado el mismo registro en ambos el almacén local y en la instancia de Azure Mobile Apps y resolución de conflictos personalizado. O bien se pueden controlar los conflictos en el almacén local o en la instancia de Azure Mobile Apps.

Para obtener más información acerca de la sincronización sin conexión, consulte [sincronización sin conexión de datos en Azure Mobile Apps](/azure/app-service-mobile/app-service-mobile-offline-data-sync/) y [habilitar la sincronización sin conexión para la aplicación móvil Xamarin.Forms](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-offline-data/).

## <a name="setup"></a>Programa de instalación

El proceso para la integración de la sincronización sin conexión entre una aplicación de Xamarin.Forms y una instancia de Azure Mobile Apps es como sigue:

1. Cree una instancia de Azure Mobile Apps. Para obtener más información, consulte [consumir una aplicación móvil de Azure](~/xamarin-forms/data-cloud/consuming/azure.md).
1. Agregar el [Microsoft.Azure.Mobile.Client.SQLiteStore](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/) paquete NuGet para todos los proyectos de la solución de Xamarin.Forms.
1. (Opcional) Habilitar la autenticación en la instancia de Azure Mobile Apps y la aplicación de Xamarin.Forms. Para obtener más información, consulte [la autenticación de usuarios con Azure Mobile Apps](~/xamarin-forms/data-cloud/authentication/azure.md).

La siguiente sección proporciona instrucciones de instalación adicionales para configurar los proyectos de plataforma Universal de Windows (UWP) para usar el paquete Microsoft.Azure.Mobile.Client.SQLiteStore NuGet. Ninguna configuración adicional es necesaria usar el paquete de Microsoft.Azure.Mobile.Client.SQLiteStore NuGet en iOS y Android.

### <a name="universal-windows-platform"></a>Plataforma universal de Windows

Para usar SQLite en la plataforma Universal de Windows (UWP), siga estos pasos:

1. Instalar el [SQLite para la plataforma Universal de Windows](http://sqlite.org/2016/sqlite-uwp-3120200.vsix) extensión en el entorno de desarrollo de Visual Studio.
1. Haga clic en el proyecto UWP en Visual Studio, **referencias > Agregar referencia**, vaya a **extensiones** y agregue el **SQLite para plataforma Universal de Windows** y  **Visual C++ 2015 Runtime para aplicaciones de la plataforma Windows Universal** extensiones para el proyecto UWP.

## <a name="initializing-the-local-store"></a>Inicializando el Store Local

El almacén local debe inicializarse antes de poder realizar cualquier operación de tabla de sincronización. Esto se logra en el proyecto de biblioteca de clases portables (PCL) de la solución de Xamarin.Forms:

```csharp
using Microsoft.WindowsAzure.MobileServices;
using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
using Microsoft.WindowsAzure.MobileServices.Sync;

namespace TodoAzure
{
    public partial class TodoItemManager
    {
        static TodoItemManager defaultInstance = new TodoItemManager();
        IMobileServiceClient client;
        IMobileServiceSyncTable<TodoItem> todoTable;

        private TodoItemManager()
        {
            this.client = new MobileServiceClient(Constants.ApplicationURL);
            var store = new MobileServiceSQLiteStore("localstore.db");
            store.DefineTable<TodoItem>();
            this.client.SyncContext.InitializeAsync(store);
            this.todoTable = client.GetSyncTable<TodoItem>();
        }
        ...
  }
}
```

Se crea una nueva base de datos SQLite local mediante la `MobileServiceSQLiteStore` clase, siempre que aún no existe. A continuación, la `DefineTable<T>` método crea una tabla en el almacén local que coincide con los campos en el `TodoItem` tipo, siempre que aún no existe.

Un *contexto de sincronización* está asociado con un `MobileServiceClient` instancia y los cambios de las pistas que se realizan con las tablas de sincronización. El contexto de sincronización mantiene una cola que mantiene una lista ordenada de operaciones de creación, actualización y eliminación (CUD) que se enviarán a la instancia de Azure Mobile Apps más adelante. El `IMobileServiceSyncContext.InitializeAsync()` método se utiliza para asociar el almacén local con el contexto de sincronización.

El `todoTable` campo es un `IMobileServiceSyncTable`, por lo que todas las operaciones de CRUD utilizan el almacén local.

## <a name="performing-synchronization"></a>Realizando la sincronización

El almacén local se sincroniza con Azure Mobile Apps instancia cuando la `SyncAsync` se invoca el método:

```csharp
public async Task SyncAsync()
{
  ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

  try
  {
    await this.client.SyncContext.PushAsync();

    // The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
    // Use a different query name for each unique query in your program.
    await this.todoTable.PullAsync("allTodoItems", this.todoTable.CreateQuery());
  }
  catch (MobileServicePushFailedException exc)
  {
    if (exc.PushResult != null)
    {
      syncErrors = exc.PushResult.Errors;
    }
  }

  // Simple error/conflict handling.
  if (syncErrors != null)
  {
    foreach (var error in syncErrors)
    {
      if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
      {
        // Update failed, revert to server's copy
        await error.CancelAndUpdateItemAsync(error.Result);
      }
      else
      {
        // Discard local change
        await error.CancelAndDiscardItemAsync();
      }

      Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
    }
  }
}
```

El `IMobileServiceSyncTable.PushAsync` método funciona en el contexto de sincronización, en lugar de una tabla específica y envía todos los cambios CUD realizados desde la última inserción.

Incorporación de cambios se realiza mediante el `IMobileServiceSyncTable.PullAsync` método en una sola tabla. El primer parámetro para el `PullAsync` método es un nombre de la consulta que se usa solo en el dispositivo móvil. Proporciona una consulta que no sean null los resultados de nombre en el SDK de Azure Mobile Client lleve a cabo una *sincronización incremental*, donde cada vez que una operación de extracción devuelve resultados, la última `updatedAt` marca de tiempo en los resultados se almacena en el equipo local tablas del sistema. Operaciones de extracción posteriores luego sólo necesitamos recuperar registros después de esa marca de tiempo. Como alternativa, *sincronización completa* se puede lograr pasando `null` como el nombre de la consulta, lo que da como resultado todos los registros que se va a recuperar en cada operación de extracción. Después de cualquier operación de sincronización, se insertan datos recibidos en el almacén local.

> [!NOTE]
> Si se ejecuta una extracción en una tabla que tiene actualizaciones locales pendientes, la extracción ejecutará primero una inserción en el contexto de sincronización. Esto minimiza los conflictos entre los cambios que ya están en cola y los nuevos datos de la instancia de Azure Mobile Apps.

El `SyncAsync` método también incluye una implementación básica para controlar los conflictos cuando ha cambiado el mismo registro en ambos el almacén local y en la instancia de Azure Mobile Apps. Cuando el conflicto es que los datos se ha actualizado en el almacén local y en la instancia de Azure Mobile Apps, el `SyncAsync` método actualiza los datos en el almacén local de los datos almacenados en la instancia de Azure Mobile Apps. Cuando se produce cualquier otro tipo de conflicto, la `SyncAsync` método descarta el cambio local. Esto controla el escenario donde existe un cambio local para los datos que se ha eliminado de la instancia de Azure Mobile Apps.

En una aplicación de producción, los desarrolladores deberían escribir personalizada `IMobileServiceSyncHandler` implementación del control de conflictos que se adapte a sus casos de uso. Para obtener más información, consulte [usar simultaneidad optimista para la resolución de conflictos](https://azure.microsoft.com/documentation/articles/app-service-mobile-dotnet-how-to-use-client-library/#optimisticconcurrency) en el portal de Azure, y [profundización en la compatibilidad sin conexión en el SDK de cliente administrado](https://blogs.msdn.microsoft.com/azuremobile/2014/04/07/deep-dive-on-the-offline-support-in-the-managed-client-sdk/) en los blogs de MSDN.

## <a name="purging-data"></a>Purga de datos

Se pueden borrar las tablas del almacén local de datos con el `IMobileServiceSyncTable.PurgeAsync` método. Este método es compatible con escenarios como la eliminación de datos obsoletos que ya no requiere una aplicación. Por ejemplo, la aplicación de ejemplo solo muestra `TodoItem` instancias que no se completen. Por lo tanto, los elementos completados ya no necesitan almacenarse localmente. Purga los elementos completados desde el almacén local puede realizarse como sigue:

```csharp
await todoTable.PurgeAsync(todoTable.Where(item => item.Done));
```

Una llamada a `PurgeAsync` también desencadena una operación de inserción. Por lo tanto, todos los elementos que se marcan como completados localmente se enviarán a la instancia de Azure Mobile Apps antes de eliminarse del almacén local. Sin embargo, si hay operaciones pendientes de sincronización con la instancia de Azure Mobile Apps, se producirá la purga un `InvalidOperationException` a menos que el `force` parámetro está establecido en `true`. Una estrategia alternativa consiste en Examinar el `IMobileServiceSyncContext.PendingOperations` propiedad, que devuelve el número de operaciones que aún no se han insertado en la instancia de Azure Mobile Apps y solo realizar la depuración si la propiedad es cero pendientes.

> [!NOTE]
> Invocar `PurgeAsync` con el `force` parámetro establecido en `true` los cambios pendientes perderá.

## <a name="initiating-synchronization"></a>Inicia la sincronización

En la aplicación de ejemplo, el `SyncAsync` método se invoca a través de la `TodoList.OnAppearing` método:

```csharp
protected override async void OnAppearing()
{
  base.OnAppearing();

  // Set syncItems to true to synchronize the data on startup when running in offline mode
  await RefreshItems(true, syncItems: true);
}
```

Esto significa que la aplicación intentará sincronizarse con la instancia de Azure Mobile Apps cuando se inicia.

Además, puede iniciar la sincronización en iOS y Android mediante el uso de incorporación de cambios para actualizar en la lista de datos y en las plataformas de Windows mediante el uso de la **sincronización** botón en la interfaz de usuario. Para obtener más información, consulte [Deslizar para actualizar](~/xamarin-forms/user-interface/listview/interactivity.md#Pull_to_Refresh).

## <a name="summary"></a>Resumen

En este artículo se explica cómo agregar la funcionalidad de sincronización sin conexión a una aplicación de Xamarin.Forms. Sincronización sin conexión permite a los usuarios interactuar con una aplicación móvil, ver, agregar o modificar datos, incluso cuando no hay una conexión de red. Los cambios se almacenan en una base de datos local y una vez que el dispositivo está en línea, se pueden sincronizar los cambios con la instancia de Azure Mobile Apps.


## <a name="related-links"></a>Vínculos relacionados

- [TodoAzureAuthOfflineSync (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthOfflineSync/)
- [Consumir una aplicación móvil de Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Autenticar a los usuarios con Azure Mobile Apps](~/xamarin-forms/data-cloud/authentication/azure.md)
- [SDK de Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
