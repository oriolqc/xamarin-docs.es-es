---
title: Consumir una aplicación móvil de Azure
description: En este artículo, que sólo es aplicable a las aplicaciones móviles de Azure que usan un back-end de Node.js, explica cómo consultar, insertar, actualizar y eliminar los datos almacenados en una tabla en una instancia de aplicaciones móviles de Azure.
ms.prod: xamarin
ms.assetid: 2B3EFD0A-2922-437D-B151-4B4DE46E2095
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 73d74b59ef6e59028eec7cad19feec21908b6329
ms.sourcegitcommit: d70fcc6380834127fdc58595aace55b7821f9098
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2018
ms.locfileid: "36269049"
---
# <a name="consuming-an-azure-mobile-app"></a>Consumir una aplicación móvil de Azure

_Aplicaciones móviles de Azure le permiten desarrollar aplicaciones con back-ends de escalable hospedado en el servicio de aplicación de Azure, con compatibilidad para la autenticación de dispositivos móvil, sincronización sin conexión y las notificaciones de inserción. En este artículo, que sólo es aplicable a las aplicaciones móviles de Azure que usan un back-end de Node.js, explica cómo consultar, insertar, actualizar y eliminar los datos almacenados en una tabla en una instancia de aplicaciones móviles de Azure._

> [!NOTE]
> A partir del 30 de junio de, todas las aplicaciones móviles de Azure nuevo se creará con TLS 1.2 de forma predeterminada. Además, se recomienda también existentes aplicaciones móviles de Azure a configurarse para usar TLS 1.2. Para obtener información sobre cómo exigir TLS 1.2 en una aplicación móvil de Azure, consulte [exigir TLS 1.2](/azure/app-service/app-service-web-tutorial-custom-ssl#enforce-tls-1112). Para obtener información sobre cómo configurar proyectos de Xamarin para usar TLS 1.2, consulte [seguridad de capa de transporte (TLS) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md).

Para obtener información sobre cómo crear una instancia de aplicaciones móviles de Azure que puede ser utilizada por Xamarin.Forms, consulte [crear una aplicación de Xamarin.Forms](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/). Después de seguir estas instrucciones, la aplicación de ejemplo descargable puede configurarse para usar la instancia de aplicaciones móviles de Azure estableciendo la `Constants.ApplicationURL` a la dirección URL de la instancia de aplicaciones móviles de Azure. A continuación, cuando se ejecuta la aplicación de ejemplo se conectará a la instancia de aplicaciones móviles de Azure, como se muestra en la captura de pantalla siguiente:

![](azure-images/portal.png "Aplicación de ejemplo")

Acceso a las aplicaciones móviles de Azure es a través de la [SDK de cliente de Azure Mobile](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/), y todas las conexiones de la aplicación de ejemplo de Xamarin.Forms para Azure se realizan a través de HTTPS.

> [!NOTE]
> En iOS 9 y versiones posteriores, seguridad de transporte de aplicación (ATS) exige conexiones seguras entre los recursos de internet (por ejemplo, el servidor de back-end de la aplicación) y la aplicación, lo que evita que la divulgación accidental de información confidencial. Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estará sujeto a requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
> Se pueden alta ATS fuera de si no es posible utilizar la `HTTPS` del protocolo y proteger la comunicación para los recursos de internet. Esto puede lograrse mediante la actualización de la aplicación **Info.plist** archivo. Para obtener más información, consulte [seguridad de transporte de la aplicación](~/ios/app-fundamentals/ats.md).

## <a name="consuming-an-azure-mobile-app-instance"></a>Consumir una instancia de la aplicación móvil de Azure

El [SDK de cliente de Azure Mobile](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) proporciona el `MobileServiceClient` (clase), que se utiliza una aplicación de Xamarin.Forms para tener acceso a la instancia de aplicaciones móviles de Azure, tal como se muestra en el ejemplo de código siguiente:

```csharp
IMobileServiceTable<TodoItem> todoTable;
MobileServiceClient client;

public TodoItemManager ()
{
  client = new MobileServiceClient (Constants.ApplicationURL);
  todoTable = client.GetTable<TodoItem> ();
}
```

Cuando el `MobileServiceClient` se crea la instancia, se debe especificar una URL de aplicación para identificar la instancia de aplicaciones móviles de Azure. Este valor se puede obtener desde el panel de la aplicación móvil en el [Portal de Microsoft Azure](https://portal.azure.com/).

Una referencia a la `TodoItem` tabla almacenada en la instancia de aplicaciones móviles de Azure debe obtenerse para pueden realizar operaciones en la tabla. Esto se logra mediante una llamada a la `GetTable` método en el `MobileServiceClient` de instancia, que devuelve un `IMobileServiceTable<TodoItem>` referencia.

### <a name="querying-data"></a>Consulta de datos

Se puede recuperar el contenido de una tabla mediante una llamada a la `IMobileServiceTable.ToEnumerableAsync` método que se evalúa como la consulta y devuelve los resultados de forma asincrónica. Datos también se pueden filtrar del servidor mediante la inclusión de un `Where` cláusula de la consulta. El `Where` cláusula aplica a una fila de filtrado de predicado de la consulta en la tabla, tal como se muestra en el ejemplo de código siguiente:

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

Esta consulta devuelve todos los elementos de la `TodoItem` tabla cuyos `Done` propiedad es igual a `false`. Los resultados de la consulta, a continuación, se colocan en un `ObservableCollection` para su presentación.

### <a name="inserting-data"></a>Insertar datos

Al insertar datos en la instancia de aplicaciones móviles de Azure, automáticamente se genera nuevas columnas en la tabla según sea necesario, proporciona ese esquema dinámico está habilitado en la instancia de aplicaciones móviles de Azure. El `IMobileServiceTable.InsertAsync` método se utiliza para insertar una nueva fila de datos en la tabla especificada, tal como se muestra en el ejemplo de código siguiente:

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.InsertAsync (item);
  ...
}
```

Al realizar una solicitud de inserción, no se especifica un identificador en los datos que se pasan a la instancia de aplicaciones móviles de Azure. Si la solicitud de inserción contiene un identificador de un `MobileServiceInvalidOperationException` se iniciará.

Después de la `InsertAsync` método finaliza, se rellenará el Id. de los datos de la instancia de aplicaciones móviles de Azure en la `TodoItem` perteneciente a la aplicación de Xamarin.Forms.

### <a name="updating-data"></a>Actualizar datos

Al actualizar datos en la instancia de aplicaciones móviles de Azure, automáticamente se genera nuevas columnas en la tabla según sea necesario, proporciona ese esquema dinámico está habilitado en la instancia de aplicaciones móviles de Azure. El `IMobileServiceTable.UpdateAsync` método se usa para actualizar los datos existentes con información nueva, como se muestra en el ejemplo de código siguiente:

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.UpdateAsync (item);
  ...
}
```

Al realizar una solicitud de actualización, debe especificarse un Id. para que la instancia de aplicaciones móviles de Azure puede identificar los datos que se va a actualizar. Valor de este identificador se almacena en la `TodoItem.ID` propiedad. Si la solicitud de actualización no contiene un Id. no hay ninguna manera de actualizar a la instancia de aplicaciones móviles de Azure para determinar los datos por lo que un `MobileServiceInvalidOperationException` se iniciará.

### <a name="deleting-data"></a>Eliminar datos

El `IMobileServiceTable.DeleteAsync` método se usa para eliminar datos de una tabla de aplicaciones móviles de Azure, tal como se muestra en el ejemplo de código siguiente:

```csharp
public async Task DeleteTaskAsync (TodoItem item)
{
  ...
  await todoTable.DeleteAsync(item);
  ...
}
```

Al realizar una solicitud de eliminación, debe especificarse un Id. para que la aplicación móvil de Azure sinstance pueda identificar los datos que se va a eliminar. Valor de este identificador se almacena en la `TodoItem.ID` propiedad. Si la solicitud de eliminación no contiene un identificador, no hay ninguna manera de la instancia de aplicaciones móviles de Azure determinar los datos que va a eliminar de modo que un `MobileServiceInvalidOperationException` se iniciará.

## <a name="summary"></a>Resumen

Este artículo explica cómo utilizar el [SDK de cliente de Azure Mobile](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) para consultar, insertar, actualizar y eliminar los datos almacenados en una tabla en una instancia de aplicaciones móviles de Azure. El SDK proporciona el `MobileServiceClient` clase que se usa una aplicación de Xamarin.Forms para tener acceso a la instancia de aplicaciones móviles de Azure.


## <a name="related-links"></a>Vínculos relacionados

- [TodoAzure (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure/)
- [Crear una aplicación de Xamarin.Forms](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/)
- [Cliente móvil Azure SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
