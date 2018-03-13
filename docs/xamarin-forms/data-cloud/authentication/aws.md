---
title: Autenticar a los usuarios con un servicio de SimpleDB de Amazon
description: "Amazon SimpleDB no proporciona su propio sistema de permisos basada en recursos. En su lugar, la autenticación con un proveedor de identidades puede usarse para asegurarse de que los usuarios sólo tienen acceso a sus propios datos en el dominio SimpleDB. En este artículo se explica cómo restringir el acceso de los usuarios a sus propios datos SimpleDB."
ms.topic: article
ms.prod: xamarin
ms.assetid: 797C91A5-9720-4DAC-89D8-5C85996584C8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: ac4c788b4bd48991d7628d892ad1ece3d2451228
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="authenticating-users-with-an-amazon-simpledb-service"></a>Autenticar a los usuarios con un servicio de SimpleDB de Amazon

_Amazon SimpleDB no proporciona su propio sistema de permisos basada en recursos. En su lugar, la autenticación con un proveedor de identidades puede usarse para asegurarse de que los usuarios sólo tienen acceso a sus propios datos en el dominio SimpleDB. En este artículo se explica cómo restringir el acceso de los usuarios a sus propios datos SimpleDB._

[Xamarin.Auth](https://github.com/xamarin/Xamarin.Auth) se utiliza en la aplicación de ejemplo para administrar el proceso de autenticación de usuario y almacenar de forma segura los detalles de la cuenta de los usuarios en el dispositivo. Para obtener más información, consulte [autenticar a los usuarios con un proveedor de identidades](~/xamarin-forms/data-cloud/authentication/oauth.md).

## <a name="allowing-an-authenticated-user-access-to-simpledb-domain-data"></a>Permitir que un usuario autenticado de acceso a los datos de dominio de SimpleDB

La aplicación de ejemplo utiliza la `TodoItem` clase para modelar los datos. Para almacenar un `TodoItem` perteneciente a un servicio de SimpleDB debe convertirse primero en un `List` de `ReplaceableAttribute` objetos. Para obtener más información, consulte [SimpleDB crear objetos](~/xamarin-forms/data-cloud/consuming/aws.md).

> [!NOTE]
> En iOS 9 y versiones posteriores, seguridad de transporte de aplicación (ATS) exige conexiones seguras entre los recursos de internet (por ejemplo, el servidor de back-end de la aplicación) y la aplicación, lo que evita que la divulgación accidental de información confidencial. Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estará sujeto a requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
> Se pueden alta ATS fuera de si no es posible utilizar la `HTTPS` del protocolo y proteger la comunicación para los recursos de internet. Esto puede lograrse mediante la actualización de la aplicación **Info.plist** archivo. Para obtener más información, consulte [seguridad de transporte de la aplicación](~/ios/app-fundamentals/ats.md).

Para asegurarse de que los usuarios tienen acceso solo a sus propios datos en el dominio SimpleDB, la `ToSimpleDBReplaceableAttributes` método almacena un atributo adicional para un `TodoItem` instancia, tal como se muestra en el ejemplo de código siguiente:

```csharp
List<ReplaceableAttribute> ToSimpleDBReplaceableAttributes (TodoItem item)
{
  return new List<ReplaceableAttribute> () {
    ...
    new ReplaceableAttribute () {
      Name = "User",
      Value = App.User.Email,
      Replace = true
    },
  };
}
```

Este atributo garantiza que cada elemento almacenado en el dominio SimpleDB tiene una dirección de correo electrónico asociada para un usuario, que se utiliza para identificar al usuario que pertenecen los datos a. Cuando se recupera el contenido de un dominio mediante una llamada a la `AmazonSimpleDBClient.SelectAsync` método, la expresión de consulta se asegura de que se recuperan sólo los elementos del usuario autenticado, como se muestra en el ejemplo de código siguiente:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var request = new SelectRequest () {
    SelectExpression = string.Format ("SELECT * from {0} WHERE User = '{1}'", tableName, App.User.Email)
  };
  var response = await client.SelectAsync (request);
  ...
}
```

El `SelectAsync` método devuelve una respuesta que contiene una colección de elementos y atributos asociados que coinciden con la expresión de consulta. La expresión de consulta se asegura de que se recuperarán sólo los elementos que coinciden con la dirección de correo electrónico del usuario. Para obtener más información acerca de las expresiones de consulta, vea [utilizando Seleccione esta opción para crear consultas de Amazon SimpleDB](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/UsingSelect.html) en el sitio Web de Amazon.

> [!NOTE]
> Asegúrese de seguir las reglas de entrecomillado al construir la expresión de consulta. Para obtener más información, consulte [seleccionar reglas de entrecomillar](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/QuotingRulesSelect.html) en el sitio Web de Amazon.

## <a name="summary"></a>Resumen

Este artículo explica cómo restringir el acceso de los usuarios a sus propios datos SimpleDB. Amazon SimpleDB no proporciona su propio sistema de permisos basada en recursos. En su lugar, la autenticación con un proveedor de identidades puede usarse para asegurarse de que los usuarios tienen acceso solo a sus propios datos en el dominio SimpleDB.


## <a name="related-links"></a>Vínculos relacionados

- [TodoAWSAuth (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAWSAuth/)
- [Consumir un servicio de SimpleDB de Amazon](~/xamarin-forms/data-cloud/consuming/aws.md)
- [Autenticar a los usuarios con un proveedor de identidades](~/xamarin-forms/data-cloud/authentication/oauth.md)
- [Identidad de Cognito de Amazon](http://docs.aws.amazon.com/cognito/devguide/identity/)
- [Documentación para desarrolladores de SimpleDB Amazon](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/Welcome.html)
- [Servicios Web de Amazon SDK para .NET](https://www.nuget.org/packages?q=Tags%3A%22aws-sdk-v3%22)
