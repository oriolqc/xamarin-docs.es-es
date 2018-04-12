---
title: Consumir un servicio de SimpleDB de Amazon
description: Amazon SimpleDB es un servicio web que proporciona la capacidad de almacenar y consultar datos en la nube de Amazon. En este artículo se explica cómo usar el SDK de AWS para .NET para consultar, crear, reemplazar y eliminar los datos almacenados en un servicio de SimpleDB.
ms.prod: xamarin
ms.assetid: 823819AA-15F9-4144-B355-78A10AD37513
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 1602319dbf5a5d00ac5de75f2d438b9aea692699
ms.sourcegitcommit: 271d3f7ea4abfcf87734d2c747a68cb8114d743c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/12/2018
---
# <a name="consuming-an-amazon-simpledb-service"></a>Consumir un servicio de SimpleDB de Amazon

_Amazon SimpleDB es un servicio web que proporciona la capacidad de almacenar y consultar datos en la nube de Amazon. En este artículo se explica cómo usar el SDK de AWS para .NET para consultar, crear, reemplazar y eliminar los datos almacenados en un servicio de SimpleDB._

Servicios de SimpleDB utilizan un modelo de solicitud y respuesta resultará familiar a los consumidores de servicios REST. Las operaciones se invocan en el servicio de SimpleDB mediante el envío de una solicitud, que puede contener datos. Después de procesar la solicitud, el servicio SimpleDB devuelve una respuesta que contiene los resultados. Un servicio de SimpleDB debe crearse mediante programación y no se puede crear a través de la [consola de AWS](https://aws.amazon.com). Sin embargo, una cuenta AWS es necesaria para crear y tener acceso a cualquier servicio web de Amazon.

En un servicio SimpleDB, los datos se organizan en dominios, dentro del cual se pueden colocar los datos y ejecutarán consultas en los datos. Dominios constan de elementos que se describen mediante pares de nombre / valor de atributo. Dominios pueden considerarse como algo similar a las tablas, con algo similar a elementos es similar a las filas y columnas de atributos. Para obtener más información sobre el modelo de datos de SimpleDB, consulte [modelo de datos](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/DataModel.html) en el sitio Web de Amazon.

Las instrucciones sobre cómo configurar los servicios de Amazon necesarios se pueden encontrar en el archivo Léame que acompaña a la aplicación de ejemplo. Cuando se ejecuta la aplicación de ejemplo, se conectará a un grupo de identidad Cognito de Amazon para autorizar el acceso al servicio SimpleDB, como se muestra en la captura de pantalla siguiente:

![](aws-images/portal.png "Aplicación de ejemplo")

> [!NOTE]
> En iOS 9 y versiones posteriores, seguridad de transporte de aplicación (ATS) exige conexiones seguras entre los recursos de internet (por ejemplo, el servidor de back-end de la aplicación) y la aplicación, lo que evita que la divulgación accidental de información confidencial. Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estará sujeto a requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
> Se pueden alta ATS fuera de si no es posible utilizar la `HTTPS` del protocolo y proteger la comunicación para los recursos de internet. Esto puede lograrse mediante la actualización de la aplicación **Info.plist** archivo. Para obtener más información, consulte [seguridad de transporte de la aplicación](~/ios/app-fundamentals/ats.md).

## <a name="consuming-a-simpledb-service"></a>Consumir un servicio SimpleDB

Identidad de Amazon Cognito permite a los servicios AWS, como SimpleDB, que se debe invocar desde una aplicación sin credenciales AWS codificar de forma rígida en la aplicación. En su lugar, se crea un grupo de identidad única en el [Amazon Cognito consola](https://console.aws.amazon.com/cognito/home). El grupo de identidad contiene las identidades que utilizan funciones para especificar los recursos, como SimpleDB, que puede tener acceso la identidad.

El [AWS SDK para .NET](https://www.nuget.org/packages?q=Tags%3A%22aws-sdk-v3%22) proporciona el `CognitoAWSCredentials` y `AmazonSimpleDBClient` las clases, que se utilizan por una aplicación de Xamarin.Forms para tener acceso al servicio SimpleDB, tal como se muestra en el ejemplo de código siguiente:

```csharp
AmazonSimpleDBClient client;
...

public SimpleDBStorage ()
{
  var credentials = new CognitoAWSCredentials (
                      Constants.CognitoIdentityPoolId,
                      RegionEndpoint.USEast1);
  var config = new AmazonSimpleDBConfig ();
  config.RegionEndpoint = RegionEndpoint.USWest2;
  client = new AmazonSimpleDBClient (credentials, config);
  ...
}
```

Una nueva instancia de la `CognitoAWSCredentials` se crea la clase proporcionando el identificador del grupo de identidad única y la región de la cuenta de identidad de Cognito. En el momento de escribir este artículo, Cognito identidad sólo está disponible en las regiones USEast1 y EUWest1. Sin embargo, puede comunicarse con servicios de Amazon fuera de esas regiones.

Cuando el `AmazonSimpleDBClient` se crea la instancia, el `CognitoAWSCredentials` instancia debe proporcionarse, junto con un `AmazonSimpleDBConfig` instancia que especifica la región geográfica donde reside el servicio SimpleDB. El `CognitoAWSCredentials` instancia garantiza que el servicio de SimpleDB que se obtiene acceso es el asociado con la cuenta AWS en el que se creó el grupo de identidad, evitando la necesidad de incorporar una tecla de acceso AWS y la clave secreta en la aplicación.

Se crea un dominio de servicio de SimpleDB mediante una llamada a la `AmazonSimpleDBClient.CreateDomainAsync` método, tal como se muestra en el ejemplo de código siguiente:

```csharp
string tableName = "Todo";
...

async Task CreateDomain ()
{
  ...
  await client.CreateDomainAsync (new CreateDomainRequest { DomainName = tableName });
  ...
}
```

El `CreateDomainAsync` método requiere un `CreateDomainRequest` instancia como un parámetro. El `CreateDomainRequest` instancia inicializa el `DomainName` valor para la propiedad que se usará para identificar el dominio. Para crear el dominio, este valor debe ser único entre los dominios asociados a la cuenta AWS. En caso contrario, no se creará el dominio y no se enviará ninguna respuesta de error para indicar que. Cualquier operación con el nombre de dominio, a continuación, se producirá en el dominio existente, en lugar de un dominio recién creado.

### <a name="creating-simpledb-objects"></a>Crear objetos de SimpleDB

La aplicación de ejemplo utiliza la `TodoItem` clase para modelar los datos. Para almacenar un `TodoItem` perteneciente a un servicio de SimpleDB debe convertirse primero en un `List` de `ReplaceableAttribute` objetos. Esto se logra mediante la `ToSimpleDBReplaceableAttributes` método, tal como se muestra en el ejemplo de código siguiente:

```csharp
List<ReplaceableAttribute> ToSimpleDBReplaceableAttributes (TodoItem item)
{
  return new List<ReplaceableAttribute> () {
    new ReplaceableAttribute () {
      Name = "Name",
      Value = item.Name,
      Replace = true
    },
    new ReplaceableAttribute () {
      Name = "Notes",
      Value = item.Notes,
      Replace = true
    },
    new ReplaceableAttribute () {
      Name = "Done",
      Value = item.Done.ToString (),
      Replace = true
    }
  };
}
```

Este método crea un `List` de nueva `ReplaceableAttribute` instancias, con el `List` que representa una sola `TodoItem` instancia. Cada `ReplaceableAttribute` instancia representa una única propiedad de la `TodoItem` instancia. Para obtener más información sobre la `ReplaceableAttribute` de clases, consulte [ReplaceableAttribute clase](http://docs.aws.amazon.com/sdkfornet1/latest/apidocs/html/T_Amazon_SimpleDB_Model_ReplaceableAttribute.htm) en el sitio Web de Amazon.

De forma similar, cuando se recuperan datos desde el servicio de SimpleDB, se debe convertir de un `List` de `Attribute` instancias de un `TodoItem` instancia. Esto se consigue con el `FromSimpleDBAttributes` método, tal como se muestra en el ejemplo de código siguiente:

```csharp
TodoItem FromSimpleDBAttributes (List<Amazon.SimpleDB.Model.Attribute> attributeList, string id)
{
  var todoItem = new TodoItem ();
  todoItem.ID = id;
  todoItem.Name = attributeList.Where (attr => attr.Name == "Name").FirstOrDefault ().Value;
  todoItem.Notes = attributeList.Where (attr => attr.Name == "Notes").FirstOrDefault ().Value;
  todoItem.Done = Convert.ToBoolean (attributeList.Where (attr => attr.Name == "Done").FirstOrDefault ().Value);
  return todoItem;
}
```

Este método simplemente recupera cada `Attribute` instancia desde el `List` y establece recién creado `TodoItem` instancia.

Para obtener más información sobre la `Attribute` de clases, consulte [clase de atributo](http://docs.aws.amazon.com/sdkfornet1/latest/apidocs/html/T_Amazon_SimpleDB_Model_Attribute.htm) en el sitio Web de Amazon.

### <a name="querying-data"></a>Consulta de datos

Se puede recuperar el contenido de un dominio mediante una llamada a la `AmazonSimpleDBClient.SelectAsync` método, tal como se muestra en el ejemplo de código siguiente:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var request = new SelectRequest () {
      SelectExpression = string.Format ("SELECT * from {0}", tableName)
  };
  var response = await client.SelectAsync (request);
  foreach (var item in response.Items) {
    Items.Add (FromSimpleDBAttributes (item.Attributes, item.Name));
  }
  ...
}
```

El `SelectAsync` método acepta un `SelectRequest` instancia como un parámetro, que especifica un `Select` su expresión de consulta `SelectExpression` propiedad. El formato de la expresión de consulta es similar al formato de la instrucción SQL estándar `SELECT` instrucción. Para obtener más información acerca de la expresión de consulta, vea [utilizando Seleccione esta opción para crear consultas de Amazon SimpleDB](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/UsingSelect.html) en el sitio Web de Amazon.

> [!NOTE]
> Asegúrese de seguir las reglas de entrecomillado al construir la expresión de consulta. Para obtener más información, consulte [seleccionar reglas de entrecomillar](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/QuotingRulesSelect.html) en el sitio Web de Amazon.

El `SelectAsync` método devuelve una respuesta que contiene una colección de elementos y atributos asociados que coinciden con la expresión de consulta. Esta colección se convierte a un `List` de `TodoItem` instancias para su presentación.

### <a name="creating-and-replacing-data"></a>Crear y sustituir datos

El `AmazonSimpleDBClient.PutAttributesAsync` método se utiliza para crear y reemplazar datos en el dominio de servicio SimpleDB, tal como se muestra en el ejemplo de código siguiente:

```csharp
public async Task SaveTodoItemAsync (TodoItem todoItem)
{
  ...
  var attributeList = ToSimpleDBReplaceableAttributes (todoItem);
  var request = new PutAttributesRequest () {
      DomainName = tableName,
      ItemName = todoItem.ID,
      Attributes = attributeList
  };
  await client.PutAttributesAsync (request);
  ...
}
```

El `PutAttributesAsync` método acepta un `PutAttributesRequest` instancia como un parámetro. El `PutAttributesRequest` instancia especifica los pares de nombre-valor de atributo que se crea como un elemento nuevo, o se reemplazará en un elemento existente. El `List` de `ReplaceableAttribute` instancias se genera mediante el `ToSimpleDBReplaceableAttributes` método. Este método también establece la `Replace` propiedad de cada `ReplaceableAttribute` a `true`. Esto hará que el nuevo valor de atributo reemplazar un valor de atributo existente si se va a reemplazar los datos. Sin embargo, no se producirá intentar reemplazar valores de atributo que no existen en una respuesta de error.

El valor de la `PutAttributesRequest.ItemName` propiedad controla si un nuevo elemento se agregará al dominio o si se sustituirán un elemento existente. Cuando la aplicación crea un nuevo elemento, Establece la `TodoItem.ID` propiedad a una nueva `Guid`. Esto garantiza que cada `TodoItem` instancia tiene un identificador único. Por lo tanto, si la `PutAttributesRequest.ItemName` propiedad se establece en un valor que no existe en el dominio, el servicio SimpleDB creará un nuevo elemento que contiene los pares de nombre y valor de atributo especificado. Si el `PutAttributesRequest.ItemName` propiedad se establece en un valor que ya existe en el dominio, el servicio SimpleDB actualizará el elemento con los pares de nombre y valor de atributo especificado.

### <a name="deleting-data"></a>Eliminar datos

El `AmazonSimpleDBClient.DeleteAttributesAsync` método se utiliza para eliminar datos desde el dominio del servicio SimpleDB, tal como se muestra en el ejemplo de código siguiente:

```csharp
public async Task DeleteTodoItemAsync (TodoItem todoItem)
{
  ...
  var attributeList = ToSimpleDBAttributes (todoItem);
  var request = new DeleteAttributesRequest () {
      DomainName = tableName,
      ItemName = todoItem.ID,
      Attributes = attributeList
  };
  await client.DeleteAttributesAsync (request);
  ...
}
```

El `DeleteAttributesAsync` método acepta un `DeleteAttributesRequest` instancia como un parámetro.  El `DeleteAttributesRequest` instancia especifica los atributos que se va a eliminar desde el elemento con la `List` de `Attribute` instancias que para se va a eliminar está generando por la `ToSimpleDBAttributes` método. Se elimina el elemento siempre que se eliminan todos los atributos del elemento.

## <a name="summary"></a>Resumen

Este artículo explica cómo usar el SDK de AWS para .NET para consultar, crear y reemplazar y eliminar los datos almacenados en un servicio de SimpleDB. Este SDK proporciona el `CognitoAWSCredentials` y `AmazonSimpleDBClient` las clases que se utilizan por una aplicación de Xamarin.Forms para tener acceso al servicio SimpleDB.


## <a name="related-links"></a>Vínculos relacionados

- [TodoAWS (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAWS/)
- [Guía para desarrolladores de Xamarin del SDK de servicios Web de Amazon](http://docs.aws.amazon.com/mobile/sdkforxamarin/developerguide/)
- [Identidad de Cognito de Amazon](http://docs.aws.amazon.com/cognito/devguide/identity/)
- [Documentación para desarrolladores de SimpleDB Amazon](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/Welcome.html)
- [Clase AmazonSimpleDBClient](http://docs.aws.amazon.com/sdkfornet1/latest/apidocs/html/T_Amazon_SimpleDB_AmazonSimpleDBClient.htm)
- [Servicios Web de Amazon SDK para .NET](https://www.nuget.org/packages?q=Tags%3A%22aws-sdk-v3%22)
