---
title: Consumir una aplicación móvil de Azure
description: En este artículo, que solo es aplicable a Azure Mobile Apps que use un back-end de Node.js, explica cómo consultar, insertar, actualizar y eliminar datos almacenados en una tabla en una instancia de Azure Mobile Apps.
ms.prod: xamarin
ms.assetid: 2B3EFD0A-2922-437D-B151-4B4DE46E2095
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 8bd77ec4975fae3cc7245c5adc2b5ef18568b9e1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61328393"
---
# <a name="consuming-an-azure-mobile-app"></a>Consumir una aplicación móvil de Azure

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure/)

_Azure Mobile Apps le permiten desarrollar aplicaciones con escalables back-ends hospedados en Azure App Service, con compatibilidad con autenticación móvil, sincronización sin conexión y las notificaciones de inserción. En este artículo, que solo es aplicable a Azure Mobile Apps que use un back-end de Node.js, explica cómo consultar, insertar, actualizar y eliminar datos almacenados en una tabla en una instancia de Azure Mobile Apps._

> [!NOTE]
> A partir del 30 de junio, se crearán nuevos todos los Azure Mobile Apps con TLS 1.2 de forma predeterminada. Además, también se recomienda existentes se vuelve a configurar Azure Mobile Apps para usar TLS 1.2. Para obtener información sobre cómo aplicar TLS 1.2 en una aplicación móvil de Azure, consulte [versiones exigir TLS](/azure/app-service/app-service-web-tutorial-custom-ssl#enforce-tls-versions). Para obtener información sobre cómo configurar proyectos de Xamarin para usar TLS 1.2, consulte [seguridad de capa de transporte (TLS) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md).

Para obtener información sobre cómo crear una instancia de Azure Mobile Apps que puede usarse con Xamarin.Forms, consulte [crear una aplicación de Xamarin.Forms](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/). Después de seguir estas instrucciones, la aplicación de ejemplo descargable puede configurarse para usar la instancia de Azure Mobile Apps estableciendo el `Constants.ApplicationURL` a la dirección URL de la instancia de Azure Mobile Apps. A continuación, cuando se ejecuta la aplicación de ejemplo se conectará a la instancia de Azure Mobile Apps, como se muestra en la captura de pantalla siguiente:

![](azure-images/portal.png "Aplicación de ejemplo")

Acceso a Azure Mobile Apps es a través de la [SDK de Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/), y todas las conexiones de la aplicación de ejemplo de Xamarin.Forms en Azure se realizan a través de HTTPS.

> [!NOTE]
> En iOS 9 y versiones posteriores, App Transport Security (ATS) exige que las conexiones seguras entre los recursos de internet (por ejemplo, el servidor back-end de la aplicación) y la aplicación, lo que impide la divulgación accidental de información confidencial. Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estará sujeto a los requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
> Se puede optar por en ATS de si no es posible usar la `HTTPS` del protocolo y proteger la comunicación de los recursos de internet. Esto puede lograrse mediante la actualización de la aplicación **Info.plist** archivo. Para obtener más información, consulte [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="consuming-an-azure-mobile-app-instance"></a>Consumo de una instancia de la aplicación móvil de Azure

El [SDK de Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) proporciona el `MobileServiceClient` (clase), que se usa una aplicación de Xamarin.Forms para tener acceso a la instancia de Azure Mobile Apps, como se muestra en el ejemplo de código siguiente:

```csharp
IMobileServiceTable<TodoItem> todoTable;
MobileServiceClient client;

public TodoItemManager ()
{
  client = new MobileServiceClient (Constants.ApplicationURL);
  todoTable = client.GetTable<TodoItem> ();
}
```

Cuando el `MobileServiceClient` se crea la instancia, se debe especificar una dirección URL de la aplicación para identificar la instancia de Azure Mobile Apps. Este valor puede obtenerse desde el panel de la aplicación móvil en el [Microsoft Azure Portal](https://portal.azure.com/).

Una referencia a la `TodoItem` debe obtenerse la tabla almacenada en la instancia de Azure Mobile Apps antes de que se pueden realizar operaciones en esa tabla. Esto se logra mediante una llamada a la `GetTable` método en el `MobileServiceClient` de instancia, que devuelve un `IMobileServiceTable<TodoItem>` referencia.

### <a name="querying-data"></a>Consulta de datos

Se puede recuperar el contenido de una tabla mediante una llamada a la `IMobileServiceTable.ToEnumerableAsync` método que se evalúa como la consulta y devuelve los resultados de forma asincrónica. También pueden ser datos filtrados en el servidor mediante la inclusión de un `Where` cláusula de la consulta. El `Where` cláusula aplica un predicado a la consulta en la tabla, el filtrado de filas tal como se muestra en el ejemplo de código siguiente:

```csharp
public async Task<ObservableCollection<TodoItem>> GetTodoItemsAsync (bool syncItems = false)
{
  ...
  IEnumerable<TodoItem> items = await todoTable
              .Where (todoItem => !todoItem.Done)
              .ToEnumerableAsync ();

  return new ObservableCollection<TodoItem> (items);
}
```

Esta consulta devuelve todos los elementos de la `TodoItem` tabla cuyos `Done` es igual a la propiedad `false`. Los resultados de consulta, a continuación, se colocan en un `ObservableCollection` para su presentación.

### <a name="inserting-data"></a>Insertar datos

Al insertar datos en la instancia de Azure Mobile Apps, automáticamente se generarán nuevas columnas en la tabla según sea necesario, proporciona ese esquema dinámico está habilitado en la instancia de Azure Mobile Apps. El `IMobileServiceTable.InsertAsync` método se utiliza para insertar una nueva fila de datos en la tabla especificada, tal como se muestra en el ejemplo de código siguiente:

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.InsertAsync (item);
  ...
}
```

Al realizar una solicitud de inserción, no se especifica un identificador en los datos que se pasan a la instancia de Azure Mobile Apps. Si la solicitud de inserción contiene un identificador de un `MobileServiceInvalidOperationException` se iniciará.

Después de la `InsertAsync` método finaliza, el Id. de los datos de la instancia de Azure Mobile Apps se rellenarán en la `TodoItem` instancia de la aplicación de Xamarin.Forms.

### <a name="updating-data"></a>Actualizar datos

Al actualizar datos en la instancia de Azure Mobile Apps, automáticamente se generarán nuevas columnas en la tabla según sea necesario, proporciona ese esquema dinámico está habilitado en la instancia de Azure Mobile Apps. El `IMobileServiceTable.UpdateAsync` método se usa para actualizar los datos existentes con nueva información, como se muestra en el ejemplo de código siguiente:

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.UpdateAsync (item);
  ...
}
```

Al realizar una solicitud de actualización, debe especificar un identificador para que la instancia de Azure Mobile Apps puede identificar los datos que se va a actualizarse. Este valor de identificador se almacena en el `TodoItem.ID` propiedad. Si la solicitud de actualización no contiene un Id. no hay ninguna manera para la instancia de Azure Mobile Apps determinar los datos se puede actualizar por lo que un `MobileServiceInvalidOperationException` se iniciará.

### <a name="deleting-data"></a>Eliminar datos

El `IMobileServiceTable.DeleteAsync` método se usa para eliminar datos de una tabla de Azure Mobile Apps, tal como se muestra en el ejemplo de código siguiente:

```csharp
public async Task DeleteTaskAsync (TodoItem item)
{
  ...
  await todoTable.DeleteAsync(item);
  ...
}
```

Al realizar una solicitud de eliminación, debe especificar un identificador para que la aplicación móvil de Azure de sinstance pueda identificar los datos que se va a eliminar. Este valor de identificador se almacena en el `TodoItem.ID` propiedad. Si la solicitud de eliminación no contiene un identificador, no hay ninguna manera para la instancia de Azure Mobile Apps determinar los datos se eliminarán de modo que un `MobileServiceInvalidOperationException` se iniciará.

## <a name="summary"></a>Resumen

En este artículo se explica cómo usar el [SDK de Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) para consultar, insertar, actualizar y eliminar datos almacenados en una tabla en una instancia de Azure Mobile apps. El SDK proporciona la `MobileServiceClient` clase que es utilizada por una aplicación de Xamarin.Forms para tener acceso a la instancia de Azure Mobile Apps.


## <a name="related-links"></a>Vínculos relacionados

- [TodoAzure (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure/)
- [Creación de una aplicación de Xamarin.Forms](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/)
- [SDK de Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
