---
title: "Sincronización de datos sin conexión con aplicaciones móviles de Azure"
description: "Sincronización sin conexión permite a los usuarios interactuar con una aplicación móvil, ver, agregar o modificar los datos, incluso cuando no hay una conexión de red. Cambios se almacenan en una base de datos local y una vez que el dispositivo está en línea, se pueden sincronizar los cambios con la instancia de aplicaciones móviles de Azure. En este artículo se explica cómo agregar la funcionalidad de sincronización sin conexión a una aplicación de Xamarin.Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: DBB343B0-2709-4C20-A669-5522B9956D9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/02/2017
ms.openlocfilehash: b7756c63901d3b4fbfea70587b3fdf8e5cf9df72
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="synchronizing-offline-data-with-azure-mobile-apps"></a>Sincronización de datos sin conexión con aplicaciones móviles de Azure

_Sincronización sin conexión permite a los usuarios interactuar con una aplicación móvil, ver, agregar o modificar los datos, incluso cuando no hay una conexión de red. Cambios se almacenan en una base de datos local y una vez que el dispositivo está en línea, se pueden sincronizar los cambios con la instancia de aplicaciones móviles de Azure. En este artículo se explica cómo agregar la funcionalidad de sincronización sin conexión a una aplicación de Xamarin.Forms._

## <a name="overview"></a>Información general

El [SDK de cliente de Azure Mobile](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) proporciona el `IMobileServiceTable` interfaz, que representa las operaciones que se pueden realizar en las tablas almacenadas en la instancia de aplicaciones móviles de Azure. Estas operaciones conectan directamente a la instancia de aplicaciones móviles de Azure y se producirá un error si el dispositivo móvil no tiene una conexión de red.

Para admitir la sincronización sin conexión, es compatible con el SDK de cliente de Azure Mobile *sincronizar tablas*, que se proporcionan con el `IMobileServiceSyncTable` interfaz. Esta interfaz proporciona la misma creación, lectura, actualización, las operaciones de eliminación (CRUD) como el `IMobileServiceTable` interfaz, pero las operaciones de lectura o escritura en un almacén local. El almacén local no se rellena con los nuevos datos de la instancia de aplicaciones móviles de Azure hasta que haya una llamada a *extracción* datos. De forma similar, no envían datos a la instancia de aplicaciones móviles de Azure hasta que haya una llamada a *inserción* cambios locales.

Sincronización sin conexión también incluye compatibilidad para detectar los conflictos cuando ha cambiado el mismo registro en el almacén local de ambos y en la instancia de aplicaciones móviles de Azure y resolución de conflictos personalizada. O bien se pueden administrar conflictos en el almacén local, o en la instancia de aplicaciones móviles de Azure.

Para obtener más información acerca de la sincronización sin conexión, consulte [sincronización de datos sin conexión en aplicaciones móviles de Azure](/azure/app-service-mobile/app-service-mobile-offline-data-sync/) y [habilita la sincronización sin conexión para su aplicación móvil de Xamarin.Forms](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-offline-data/).

## <a name="setup"></a>Programa de instalación

El proceso para integrar la sincronización sin conexión entre una aplicación de Xamarin.Forms y una instancia de aplicaciones móviles de Azure es como sigue:

1. Cree una instancia de aplicaciones móviles de Azure. Para obtener más información, consulte [consumir una aplicación móvil de Azure](~/xamarin-forms/data-cloud/consuming/azure.md).
1. Agregar el [Microsoft.Azure.Mobile.Client.SQLiteStore](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/) paquete NuGet para todos los proyectos de la solución de Xamarin.Forms.
1. (Opcional) Habilitar la autenticación en la instancia de aplicaciones móviles de Azure y la aplicación de Xamarin.Forms. Para obtener más información, consulte [autenticar a los usuarios con aplicaciones de Azure Mobile](~/xamarin-forms/data-cloud/authentication/azure.md).

La siguiente sección proporciona instrucciones de instalación adicionales para configurar los proyectos de plataforma Universal de Windows (UWP) para usar el paquete de Microsoft.Azure.Mobile.Client.SQLiteStore NuGet. Ninguna configuración adicional es necesaria usar el paquete de Microsoft.Azure.Mobile.Client.SQLiteStore NuGet en iOS y Android.

### <a name="universal-windows-platform"></a>Plataforma universal de Windows

Para utilizar código en la plataforma Universal de Windows (UWP), siga estos pasos:

1. Instalar el [SQLite para la plataforma Universal de Windows](http://sqlite.org/2016/sqlite-uwp-3120200.vsix) extensión en el entorno de desarrollo de Visual Studio.
1. En el proyecto de UWP en Visual Studio, haga clic **referencias > Agregar referencia**, vaya a **extensiones** y agregue el **SQLite para la plataforma Universal de Windows** y  **Visual C++ 2015 en tiempo de ejecución para aplicaciones de la plataforma de Windows Universal** extensiones para el proyecto de UWP.

## <a name="initializing-the-local-store"></a>Inicializar el almacén Local

Debe inicializar el almacén local para poder realizar cualquier operación de tabla de sincronización. Esto se logra en el proyecto de biblioteca de clases portables (PCL) de la solución de Xamarin.Forms:

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

Se crea una nueva base de datos local de SQLite el `MobileServiceSQLiteStore` de la clase, siempre que aún no existe. A continuación, la `DefineTable<T>` método crea una tabla en el almacén local que coincida con los campos en el `TodoItem` escribe, siempre que aún no existe.

A *sync-context* está asociado con un `MobileServiceClient` instancia y los cambios de las pistas que se realizan con tablas de sincronización. El contexto de sincronización mantiene una cola que mantiene una lista ordenada de las operaciones Create, Update y Delete (CUD) que se enviará a la instancia de aplicaciones móviles de Azure más adelante. El `IMobileServiceSyncContext.InitializeAsync()` método se utiliza para asociar el almacén local al contexto de sincronización.

El `todoTable` campo es un `IMobileServiceSyncTable`, de modo que todas las operaciones CRUD usan el almacén local.

## <a name="performing-synchronization"></a>Realizando la sincronización

El almacén local se sincroniza con las aplicaciones móviles de Azure instancia cuando la `SyncAsync` se invoca el método:

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

El `IMobileServiceSyncTable.PushAsync` método funciona en el contexto de sincronización, en lugar de una tabla específica y envía todos los cambios CUD desde la última inserción.

Incorporación de cambios se realiza mediante el `IMobileServiceSyncTable.PullAsync` método en una sola tabla. El primer parámetro a la `PullAsync` método es un nombre de consulta que se usa solo en el dispositivo móvil. Proporciona una consulta no null los resultados de nombre en el SDK de cliente de Azure Mobile lleve a cabo una *sincronización incremental*, donde cada vez que una operación de extracción devuelve resultados, la versión más reciente `updatedAt` marca de tiempo de los resultados se almacena en el equipo local tablas del sistema. A continuación, las operaciones de extracción recuperan solo registros después de esa marca de hora. O bien, *sincronización completa* puede lograr pasando `null` como el nombre de la consulta, que da como resultado todos los registros que se va a recuperar en cada operación de extracción. Después de cualquier operación de sincronización, los datos recibidos se insertan en el almacén local.

> [!NOTE]
> **Tenga en cuenta**: si una extracción se ejecuta en una tabla que tiene pendientes las actualizaciones locales, la extracción ejecutará en primer lugar una inserción en el contexto de sincronización. Esto minimiza los conflictos entre los cambios que ya se ponen en cola y los nuevos datos de la instancia de aplicaciones móviles de Azure.

El `SyncAsync` método también incluye una implementación básica para controlar los conflictos cuando se cambia el mismo registro en ambos el almacén local y en la instancia de aplicaciones móviles de Azure. Cuando el conflicto es que los datos se ha actualizado en el almacén local y en la instancia de aplicaciones móviles de Azure, el `SyncAsync` método actualiza los datos en el almacén local de los datos almacenados en la instancia de aplicaciones móviles de Azure. Cuando se produce cualquier otro tipo de conflicto, la `SyncAsync` método descarta el cambio local. Esto controla el escenario donde existe un cambio local para los datos que se han eliminado de la instancia de aplicaciones móviles de Azure.

En una aplicación de producción, los desarrolladores deben escribir un personalizado `IMobileServiceSyncHandler` implementación de control de conflictos que se ajusten a sus casos de uso. Para obtener más información, consulte [usar simultaneidad optimista para la resolución de conflictos](https://azure.microsoft.com/documentation/articles/app-service-mobile-dotnet-how-to-use-client-library/#optimisticconcurrency) en el portal de Azure y [profundización en la compatibilidad sin conexión en el SDK del cliente administrado](https://blogs.msdn.microsoft.com/azuremobile/2014/04/07/deep-dive-on-the-offline-support-in-the-managed-client-sdk/) en blogs de MSDN.

## <a name="purging-data"></a>Purga de datos

Se pueden borrar tablas en el almacén local de datos con el `IMobileServiceSyncTable.PurgeAsync` método. Este método es compatible con escenarios como la eliminación de datos obsoletos que ya no requiere una aplicación. Por ejemplo, la aplicación de ejemplo muestra solo `TodoItem` instancias que no están completadas. Por lo tanto, los elementos completados ya no necesitan estén almacenadas localmente. Purga los elementos completados en el almacén local puede realizarse como sigue:

```csharp
await todoTable.PurgeAsync(todoTable.Where(item => item.Done));
```

Una llamada a `PurgeAsync` también desencadena una operación de inserción. Por lo tanto, todos los elementos que están marcados como completados localmente se enviará a la instancia de aplicaciones móviles de Azure antes de que se ha quitado del almacén local. Sin embargo, si hay operaciones pendientes de sincronización con la instancia de aplicaciones móviles de Azure, se producirá la purga un `InvalidOperationException` a menos que la `force` parámetro está establecido en `true`. Una estrategia alternativa consiste en Examinar el `IMobileServiceSyncContext.PendingOperations` propiedad, que devuelve el número de operaciones que aún no se hayan insertado a la instancia de aplicaciones móviles de Azure y realice únicamente la purga si la propiedad es cero pendientes.

> [!NOTE]
> **Tenga en cuenta**: invocar `PurgeAsync` con el `force` parámetro establecido en `true` los cambios pendientes perderá.

## <a name="initiating-synchronization"></a>Iniciar la sincronización

En la aplicación de ejemplo, el `SyncAsync` método se invoca a través de la `TodoList.OnAppearing` método:

```csharp
protected override async void OnAppearing()
{
  base.OnAppearing();

  // Set syncItems to true to synchronize the data on startup when running in offline mode
  await RefreshItems(true, syncItems: true);
}
```

Esto significa que la aplicación intentará realizar la sincronización con la instancia de aplicaciones móviles de Azure cuando se inicia.

Además, se puede iniciar sincronización en iOS y Android mediante el uso de extracción para actualizar en la lista de datos y en las plataformas de Windows mediante el **sincronización** botón en la interfaz de usuario. Para obtener más información, consulte [de extracción en la actualización](~/xamarin-forms/user-interface/listview/interactivity.md#Pull_to_Refresh).

## <a name="summary"></a>Resumen

Este artículo explica cómo agregar la funcionalidad de sincronización sin conexión a una aplicación de Xamarin.Forms. Sincronización sin conexión permite a los usuarios interactuar con una aplicación móvil, ver, agregar o modificar los datos, incluso cuando no hay una conexión de red. Cambios se almacenan en una base de datos local y una vez que el dispositivo está en línea, se pueden sincronizar los cambios con la instancia de aplicaciones móviles de Azure.


## <a name="related-links"></a>Vínculos relacionados

- [TodoAzureAuthOfflineSync (sample)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthOfflineSync/)
- [Consumir una aplicación móvil de Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Autenticar a los usuarios con aplicaciones móviles de Azure](~/xamarin-forms/data-cloud/authentication/azure.md)
- [Cliente móvil Azure SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
