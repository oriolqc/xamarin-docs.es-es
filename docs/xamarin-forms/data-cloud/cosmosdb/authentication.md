---
title: Autenticar a los usuarios con una base de datos del documento de Cosmos Azure DB
description: Bases de datos de Azure DB Cosmos documento admiten colecciones con particiones, que pueden abarcar varios servidores y particiones, que la ejecución de rendimiento y almacenamiento ilimitado. Este artículo explica cómo combinar el control de acceso con las colecciones particionadas, para que un usuario sólo puede tener acceso a sus propios documentos en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 11ED4A4C-0F05-40B2-AB06-5A0F2188EF3D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 0132f849a066976896295da8b8b67ed8bf3c3e8d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="authenticating-users-with-an-azure-cosmos-db-document-database"></a>Autenticar a los usuarios con una base de datos del documento de Cosmos Azure DB

_Bases de datos de Azure DB Cosmos documento admiten colecciones con particiones, que pueden abarcar varios servidores y particiones, que la ejecución de rendimiento y almacenamiento ilimitado. Este artículo explica cómo combinar el control de acceso con las colecciones particionadas, para que un usuario sólo puede tener acceso a sus propios documentos en una aplicación de Xamarin.Forms._

## <a name="overview"></a>Información general

Una clave de partición debe especificarse al crear una colección con particiones y documentos con la misma clave de partición se almacenarán en la misma partición. Por lo tanto, se producirá una colección con particiones que se van a almacenar documentos para que el usuario sólo si se especifica la identidad del usuario como clave de partición. Esto también garantiza que la base de datos de documentos de base de datos de Azure Cosmos escalará como el número de usuarios y se incrementan de elementos.

Se debe conceder acceso a cualquier colección, y el modelo de control de acceso de API de SQL define dos tipos de construcciones de acceso:

- **Las claves maestras** habilitar acceso administrativo completo a todos los recursos en una cuenta de base de datos de Cosmos y se crean cuando se crea una cuenta de base de datos de Cosmos.
- **Tokens de recursos** capturan la relación entre el usuario de una base de datos y el permiso que tiene el usuario para un recurso de base de datos de Cosmos específico, como una colección o un documento.

Exponer una clave maestra, abre una cuenta de base de datos de Cosmos a la posibilidad de uso malintencionado o por negligencia. Sin embargo, los tokens de recursos de base de datos de Azure Cosmos proporcionan un mecanismo seguro para permitir que los clientes leer, escribir y eliminar recursos específicos de una cuenta de base de datos de Azure Cosmos según los permisos concedidos.

Un enfoque típico para solicitar, generar y entregar tokens de recursos a una aplicación móvil es usar a un agente de token de recurso. El siguiente diagrama muestra una descripción general del modo en que la aplicación de ejemplo utiliza a un agente de símbolo (token) de recursos para administrar el acceso a los datos de la base de datos de documento:

![](authentication-images/documentdb-authentication.png "Proceso de autenticación de base de datos de documento")

El agente de token de recurso es un servicio de API Web de nivel intermedio, hospedado en el servicio de aplicaciones de Azure, que posee la clave maestra de la cuenta de base de datos de Cosmos. La aplicación de ejemplo utiliza al agente de símbolo (token) de recursos para administrar el acceso a los datos de base de datos del documento como se indica a continuación:

1. Inicio de sesión, la aplicación de Xamarin.Forms se pone en contacto con el servicio de aplicaciones de Azure para iniciar un flujo de autenticación.
1. Servicio de aplicaciones de Azure realiza un flujo de autenticación de OAuth con Facebook. Una vez completado el flujo de autenticación, la aplicación de Xamarin.Forms recibe un token de acceso.
1. La aplicación de Xamarin.Forms utiliza el token de acceso para solicitar un token de recurso desde el agente de token de recurso.
1. El agente de token de recurso usa el token de acceso para solicitar la identidad del usuario de Facebook. La identidad del usuario, a continuación, se usa para solicitar un token de recurso de base de datos de Cosmos, que se utiliza para conceder acceso de lectura/escritura a la colección con particiones del usuario autenticado.
1. La aplicación de Xamarin.Forms utiliza el token de recurso para acceder directamente a los recursos de base de datos de Cosmos con los permisos definidos por el token de recurso.

> [!NOTE]
> Cuando expira el token de recurso, las solicitudes de base de datos de documentos posteriores recibirá una excepción 401 no autorizado. En este momento, las aplicaciones de Xamarin.Forms deben volver a establecer la identidad y solicitar un nuevo token de recurso.

Para obtener más información acerca de las particiones de base de datos de Cosmos, consulte [cómo crear particiones y escala en la base de datos de Azure Cosmos](/azure/cosmos-db/partition-data/). Para obtener más información acerca del control de acceso de base de datos de Cosmos, consulte [proteger el acceso a los datos de la base de datos de Cosmos](/azure/cosmos-db/secure-access-to-data/) y [control de acceso en la API de SQL](/rest/api/documentdb/access-control-on-documentdb-resources/).

## <a name="setup"></a>Programa de instalación

El proceso para integrar al agente de token de recurso en una aplicación de Xamarin.Forms es como sigue:

1. Cree una cuenta de base de datos de Cosmos que va a usar el control de acceso. Para obtener más información, consulte [configuración de base de datos de Cosmos](#cosmosdb_configuration).
1. Crear un servicio de aplicaciones de Azure para hospedar al broker de token de recurso. Para obtener más información, consulte [configuración de servicio de aplicaciones de Azure](#app_service_configuration).
1. Cree una aplicación de Facebook para realizar la autenticación. Para obtener más información, consulte [Facebook App Configuration](#facebook_configuration).
1. Configurar el servicio de aplicación de Azure para llevar a cabo la autenticación fácil con Facebook. Para obtener más información, consulte [configuración de autenticación del servicio de aplicación de Azure](#app_service_authentication_configuration).
1. Configure la aplicación de ejemplo de Xamarin.Forms para comunicarse con el servicio de aplicaciones de Azure y base de datos de Cosmos. Para obtener más información, consulte [configuración de la aplicación de Xamarin.Forms](#forms_application_configuration).

<a name="cosmosdb_configuration" />

### <a name="azure-cosmos-db-configuration"></a>Configuración de base de datos de Azure Cosmos

El proceso de creación de una cuenta de base de datos de Cosmos que va a usar el control de acceso es como sigue:

1. Cree una cuenta de base de datos de Cosmos. Para obtener más información, consulte [crear una cuenta de base de datos de Azure Cosmos](/azure/cosmos-db/sql-api-dotnetcore-get-started#step-1-create-an-azure-cosmos-db-account).
1. En la cuenta de base de datos de Cosmos, cree una nueva colección denominada `UserItems`, especifica una clave de partición de `/userid`.

<a name="app_service_configuration" />

### <a name="azure-app-service-configuration"></a>Configuración de servicio de aplicaciones de Azure

El proceso para hospedar al broker de símbolo (token) de recursos de servicio de aplicaciones de Azure es como sigue:

1. En el portal de Azure, cree una nueva aplicación web de servicio de aplicaciones. Para obtener más información, consulte [crear una aplicación web en un entorno de servicio de aplicaciones](/azure/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase/).
1. En el portal de Azure, abra la hoja de configuración de la aplicación de la aplicación web y agregue la siguiente configuración:
    - `accountUrl` : el valor debe ser la dirección URL de cuenta de base de datos de Cosmos desde la hoja de claves de la cuenta de base de datos de Cosmos.
    - `accountKey` : el valor debe ser la clave maestra de base de datos de Cosmos (principal o secundaria) en la hoja de claves de la cuenta de base de datos de Cosmos.
    - `databaseId` : el valor debe ser el nombre de la base de datos de la base de datos de Cosmos.
    - `collectionId` : el valor debe ser el nombre de la colección de la base de datos de Cosmos (en este caso, `UserItems`).
    - `hostUrl` : el valor debe ser la dirección URL de la aplicación web en la hoja de información general de la cuenta de servicio de aplicaciones.

    Captura de pantalla siguiente muestra esta configuración:

    [![](authentication-images/azure-web-app-settings.png "Configuración de la aplicación de servicio de aplicaciones Web")](authentication-images/azure-web-app-settings-large.png#lightbox "configuración de la aplicación de servicio de aplicaciones Web")

1. Publicar la solución de broker de token de recurso a la aplicación web de servicio de aplicaciones de Azure.

<a name="facebook_configuration" />

### <a name="facebook-app-configuration"></a>Configuración de la aplicación de Facebook

El proceso de creación de una aplicación de Facebook para realizar la autenticación es el siguiente:

1. Crear una aplicación de Facebook. Para obtener más información, consulte [registrar y configurar una aplicación](https://developers.facebook.com/docs/apps/register) en el Centro para desarrolladores de Facebook.
1. Agregue el producto de inicio de sesión de Facebook a la aplicación. Para obtener más información, consulte [agregar inicio de sesión de Facebook para la aplicación o sitio Web](https://developers.facebook.com/docs/facebook-login) en el Centro para desarrolladores de Facebook.
1. Configurar inicio de sesión de Facebook como sigue:
  - Habilitar el inicio de sesión de cliente OAuth.
  - Habilitar inicio de sesión de OAuth de Web.
  - Establece el URI para el URI de la aplicación de servicio de aplicaciones web, de redirección de OAuth válido con `/.auth/login/facebook/callback` anexado.

  Captura de pantalla siguiente muestra esta configuración:

  ![](authentication-images/facebook-oauth-settings.png "Configuración de OAuth de inicio de sesión de Facebook")

Para obtener más información, consulte [registrar la aplicación con Facebook](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-nameregister-aregister-your-application-with-facebook).

<a name="app_service_authentication_configuration" />

### <a name="azure-app-service-authentication-configuration"></a>Configuración de autenticación de servicio de aplicaciones de Azure

El proceso para configurar la autenticación de servicio de aplicaciones fácil es como sigue:

1. En el Portal de Azure, vaya a la aplicación de servicio de aplicaciones web.
1. En el Portal de Azure, abra la autenticación / hoja de autorización y realizar la configuración siguiente:
  - Autenticación de servicio de aplicaciones debe estar activada.
  - La acción a realizar cuando no se autentica una solicitud debe establecerse en **inicio de sesión con Facebook**.

  Captura de pantalla siguiente muestra esta configuración:

  [![](authentication-images/app-service-authentication-settings.png "Configuración de autenticación de aplicación de servicio de aplicaciones Web")](authentication-images/app-service-authentication-settings-large.png#lightbox "configuración de autenticación de aplicación de servicio de aplicaciones Web")

También se debería configurar la aplicación de servicio de aplicaciones web para comunicarse con la aplicación de Facebook para habilitar el flujo de autenticación. Esto puede realizarse al seleccionar el proveedor de identidades de Facebook, y especificando el **Id. de aplicación** y **secreto de la aplicación** valores de la configuración de la aplicación de Facebook en el Centro para desarrolladores de Facebook. Para obtener más información, consulte [Facebook agregar información a la aplicación](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-namesecrets-aadd-facebook-information-to-your-application).

<a name="forms_application_configuration" />

### <a name="xamarinforms-application-configuration"></a>Configuración de la aplicación de Xamarin.Forms

El proceso para configurar la aplicación de ejemplo de Xamarin.Forms es como sigue:

1. Abra la solución de Xamarin.Forms.
1. Abra `Constants.cs` y actualizar los valores de las constantes siguientes:
  - `EndpointUri` : el valor debe ser la dirección URL de cuenta de base de datos de Cosmos desde la hoja de claves de la cuenta de base de datos de Cosmos.
  - `DatabaseName` : el valor debe ser el nombre de la base de datos del documento.
  - `CollectionName` : el valor debe ser el nombre de la colección de la base de datos de documentos (en este caso, `UserItems`).
  - `ResourceTokenBrokerUrl` : el valor debe ser la dirección URL de la aplicación web de broker token de recurso en la hoja de información general de la cuenta de servicio de aplicaciones.

## <a name="initiating-login"></a>Inicio de sesión de inicio

La aplicación de ejemplo inicia el proceso de inicio de sesión mediante el uso de Xamarin.Auth para redirigir un explorador a una URL de proveedor de identidades, como se muestra en el ejemplo de código siguiente:

```csharp
var auth = new Xamarin.Auth.WebRedirectAuthenticator(
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/facebook"),
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/done"));
```

Esto hace que un flujo de autenticación de OAuth que se inicie entre el servicio de aplicaciones de Azure y Facebook, que muestra la página de inicio de sesión de Facebook:

![](authentication-images/login.png "Inicio de sesión de Facebook")

Se puede cancelar el inicio de sesión presionando el **cancelar** botón de iOS o presionando el **volver** botón en Android, en cuyo caso el usuario continúa sin autenticar y la interfaz de usuario del proveedor de identidad es Quita de la pantalla.

Para obtener más información sobre Xamarin.Auth, consulte [autenticar a los usuarios con un proveedor de identidades](~/xamarin-forms/data-cloud/authentication/oauth.md).

## <a name="obtaining-a-resource-token"></a>Obtener un Token de recurso

Después de una autenticación correcta, el `WebRedirectAuthenticator.Completed` desencadena el evento. En el ejemplo de código siguiente se muestra cómo controlar este evento:

```csharp
auth.Completed += async (sender, e) =>
{
  if (e.IsAuthenticated && e.Account.Properties.ContainsKey("token"))
  {
    var easyAuthResponseJson = JsonConvert.DeserializeObject<JObject>(e.Account.Properties["token"]);
    var easyAuthToken = easyAuthResponseJson.GetValue("authenticationToken").ToString();

    // Call the ResourceBroker to get the resource token
    using (var httpClient = new HttpClient())
    {
      httpClient.DefaultRequestHeaders.Add("x-zumo-auth", easyAuthToken);
      var response = await httpClient.GetAsync(Constants.ResourceTokenBrokerUrl + "/api/resourcetoken/");
      var jsonString = await response.Content.ReadAsStringAsync();
      var tokenJson = JsonConvert.DeserializeObject<JObject>(jsonString);
      resourceToken = tokenJson.GetValue("token").ToString();
      UserId = tokenJson.GetValue("userid").ToString();

      if (!string.IsNullOrWhiteSpace(resourceToken))
      {
        client = new DocumentClient(new Uri(Constants.EndpointUri), resourceToken);
        ...
      }
      ...
    }
  }
};
```

El resultado de una autenticación correcta es un token de acceso, que está disponible `AuthenticatorCompletedEventArgs.Account` propiedad. El token de acceso se extrae y se utiliza en una solicitud GET para el agente token de recurso `resourcetoken` API.

El `resourcetoken` API usa el token de acceso para solicitar la identidad del usuario de Facebook, que a su vez, se usa para solicitar un token de recurso de base de datos de Cosmos. Si ya existe un documento de permiso válido para el usuario en la base de datos del documento, se recupera y se devuelve un documento JSON que contiene el token de recurso a la aplicación de Xamarin.Forms. Si no existe un documento de permiso válido para el usuario, se crea un usuario y un permiso en la base de datos de documento y el token de recurso se extraen del documento de permiso y se devuelven a la aplicación de Xamarin.Forms en un documento JSON.

> [!NOTE]
> Un usuario de base de datos de documento es un recurso asociado a una base de datos de documento y cada base de datos puede contener cero o más usuarios. Un permiso de base de datos de documento es un recurso asociado a un usuario de base de datos de documento y cada usuario puede contener cero o varios permisos. Un recurso de permiso proporciona acceso a un token de seguridad que requiere que el usuario al intentar obtener acceso a un recurso como un documento.

Si el `resourcetoken` API se completa correctamente, enviará el código de estado HTTP 200 (OK) en la respuesta, junto con un documento JSON que contiene el token de recurso. Los siguientes datos JSON muestran un mensaje de respuesta correcta típico:

```csharp
{
  "id": "John Smithpermission",
  "token": "type=resource&ver=1&sig=zx6k2zzxqktzvuzuku4b7y==;a74aukk99qtwk8v5rxfrfz7ay7zzqfkbfkremrwtaapvavw2mrvia4umbi/7iiwkrrq+buqqrzkaq4pp15y6bki1u//zf7p9x/aefbvqvq3tjjqiffurfx+vexa1xarxkkv9rbua9ypfzr47xpp5vmxuvzbekkwq6txme0xxxbjhzaxbkvzaji+iru3xqjp05amvq1r1q2k+qrarurhmjzah/ha0evixazkve2xk1zu9u/jpyf1xrwbkxqpzebvqwma+hyyaazemr6qx9uz9be==;",
  "expires": 4035948,
  "userid": "John Smith"
}
```

El `WebRedirectAuthenticator.Completed` controlador de eventos lee la respuesta de la `resourcetoken` API y extrae el token de recurso y el identificador de usuario. El token de recurso, a continuación, se pasa como argumento a la `DocumentClient` constructor, que encapsula el punto de conexión, las credenciales y directiva de conexión utilizada para tener acceso a la base de datos de Cosmos y se usa para configurar y ejecutar solicitudes en la base de datos de Cosmos. El token de recurso se envía con cada solicitud para acceder directamente a un recurso e indica que se concede acceso de lectura/escritura a la colección con particiones de los usuarios autenticados.

## <a name="retrieving-documents"></a>Recuperación de documentos

Recuperación de documentos que solo pertenecen al usuario autenticado puede lograrse mediante la creación de una consulta de documento que incluye el identificador del usuario como clave de partición y se muestra en el ejemplo de código siguiente:

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink,
                        new FeedOptions
                        {
                          MaxItemCount = -1,
                          PartitionKey = new PartitionKey(UserId)
                        })
          .Where(item => !item.Id.Contains("permission"))
          .AsDocumentQuery();
while (query.HasMoreResults)
{
  Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
}
```

La consulta de forma asincrónica recupera todos los documentos que pertenecen al usuario autenticado, de la colección especificada y las coloca en una `List<TodoItem>` colección para su presentación.

El `CreateDocumentQuery<T>` método especifica un `Uri` argumento que representa la colección que se debe consultar documentos, y un `FeedOptions` objeto. La `FeedOptions` objeto especifica que un número ilimitado de elementos puede ser devuelto por la consulta y el identificador del usuario como clave de partición. Esto garantiza que únicamente los documentos en la colección del usuario con particiones se devuelven en el resultado.

> [!NOTE]
> Tenga en cuenta que los documentos de permiso, que se crean por el agente de token de recurso, se almacenan en la misma colección de documentos que los documentos creados por la aplicación de Xamarin.Forms. Por lo tanto, la consulta de documento contiene un `Where` cláusula que se aplica un predicado de filtrado a la consulta en la colección de documentos. Esta cláusula se asegura de que no se devuelven documentos de permiso de la colección de documentos.

Para obtener más información acerca de cómo recuperar los documentos de una colección de documentos, consulte [recuperar documentos de colecciones de documento](~/xamarin-forms/data-cloud/cosmosdb/consuming.md#document_query).

## <a name="inserting-documents"></a>Inserción de documentos

Antes de insertar un documento en una colección de documentos, la `TodoItem.UserId` propiedad debe actualizarse con el valor utilizado como clave de partición, como se muestra en el ejemplo de código siguiente:

```csharp
item.UserId = UserId;
await client.CreateDocumentAsync(collectionLink, item);
```

Esto garantiza que el documento se insertará en la colección del usuario con particiones.

Para obtener más información acerca de cómo insertar un documento en una colección de documentos, consulte [insertar un documento en una colección de documentos](~/xamarin-forms/data-cloud/cosmosdb/consuming.md#inserting_document).

## <a name="deleting-documents"></a>Eliminación de documentos

El valor de clave de partición debe especificarse al eliminar un documento de una colección con particiones, como se muestra en el ejemplo de código siguiente:

```csharp
await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id),
                 new RequestOptions
                 {
                   PartitionKey = new PartitionKey(UserId)
                 });
```

Esto garantiza que Cosmos DB sabe que particiones a eliminar el documento de la colección.

Para obtener más información sobre cómo eliminar un documento de una colección de documentos, consulte [eliminar un documento de una colección de documentos](~/xamarin-forms/data-cloud/cosmosdb/consuming.md#deleting_document).

## <a name="summary"></a>Resumen

Este artículo explica cómo combinar el control de acceso con las colecciones particionadas, para que un usuario sólo puede tener acceso a sus propios documentos de base de datos del documento en una aplicación de Xamarin.Forms. Especificar la identidad del usuario como una clave de partición garantiza que una colección con particiones solo puede almacenar documentos de ese usuario.


## <a name="related-links"></a>Vínculos relacionados

- [Autenticación de base de datos de lista de tareas Azure Cosmos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoDocumentDBAuth/)
- [Consumo de una base de datos de documentos de Cosmos Azure DB](~/xamarin-forms/data-cloud/cosmosdb/consuming.md)
- [Proteger el acceso a los datos de la base de datos de Azure Cosmos](/azure/cosmos-db/secure-access-to-data/)
- [Control de acceso en la API de SQL](/rest/api/documentdb/access-control-on-documentdb-resources/).
- [Cómo crear particiones y escala en la base de datos de Azure Cosmos](/azure/cosmos-db/partition-data/)
- [Biblioteca de cliente de base de datos de Azure Cosmos](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API de base de datos de Azure Cosmos](https://msdn.microsoft.com/library/azure/dn948556.aspx)
