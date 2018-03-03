---
title: Autenticar un servicio Web de REST
description: "HTTP admite el uso de varios mecanismos de autenticación para controlar el acceso a los recursos. La autenticación básica proporciona acceso a los recursos a solo los clientes que tengan las credenciales correctas. En este artículo se muestra cómo utilizar la autenticación básica para proteger el acceso a los recursos del servicio web RESTful."
ms.topic: article
ms.prod: xamarin
ms.assetid: 7B5FFDC4-F2AA-4B12-A30A-1DACC7FECBF1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 7aea74f95e8738cc415eaac3a5ac4f86b069d0f7
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="authenticating-a-restful-web-service"></a>Autenticar un servicio Web de REST

_HTTP admite el uso de varios mecanismos de autenticación para controlar el acceso a los recursos. La autenticación básica proporciona acceso a los recursos a solo los clientes que tengan las credenciales correctas. En este artículo se muestra cómo utilizar la autenticación básica para proteger el acceso a los recursos del servicio web RESTful._

La aplicación de ejemplo de Xamarin.Forms que lo acompaña consume un servicio hospedado en Xamarin REST que proporciona acceso de solo lectura para el servicio web. Por lo tanto, las operaciones que crean, actualización y eliminan datos no modificará los datos que se consume en la aplicación. Sin embargo, está disponible en una versión del servicio REST hospedable el *TodoRESTService* allí puede encontrar la carpeta en la aplicación de ejemplo e instrucciones sobre cómo configurar el servicio. Esta versión del servicio REST hospedable proporciona completo crear, actualizar, leer y eliminar el acceso a los datos.

> [!NOTE]
> En iOS 9 y versiones posteriores, seguridad de transporte de aplicación (ATS) exige conexiones seguras entre los recursos de internet (por ejemplo, el servidor de back-end de la aplicación) y la aplicación, lo que evita que la divulgación accidental de información confidencial. Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estará sujeto a requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
> Se pueden alta ATS fuera de si no es posible utilizar la `HTTPS` del protocolo y proteger la comunicación para los recursos de internet. Esto puede lograrse mediante la actualización de la aplicación **Info.plist** archivo. Para obtener más información, consulte [seguridad de transporte de la aplicación](~/ios/app-fundamentals/ats.md).

## <a name="authenticating-users-over-http"></a>Autenticar a los usuarios a través de HTTP

La autenticación básica es el mecanismo de autenticación más sencillo compatible con HTTP e implica al cliente que envía el nombre de usuario y la contraseña como texto sin cifrar con codificación de base64. Funciona como se indica a continuación:

- Si un servicio web recibe una solicitud para un recurso protegido, se rechaza la solicitud con un código de estado HTTP 401 (acceso denegado) y establece el encabezado de respuesta WWW-Authenticate, tal como se muestra en el diagrama siguiente:

![](rest-images/basic-authentication-fail.png "Error de autenticación básica")

- Si un servicio web recibe una solicitud para un recurso protegido, con el `Authorization` encabezado correctamente establecido, el web servicio responde con un código de estado HTTP 200, lo que indica que la solicitud es correcta y que la información solicitada está en la respuesta. Este escenario se muestra en el diagrama siguiente:

![](rest-images/basic-authentication-success.png "Éxito de la autenticación básica")

> [!NOTE]
> Sólo se debe utilizar la autenticación básica a través de una conexión HTTPS. Cuando se utiliza en una conexión HTTP, el <code>Authorization</code> encabezado puede descodificar fácilmente si un atacante captura el tráfico HTTP.

## <a name="specifying-basic-authentication-in-a-web-request"></a>Especifica la autenticación básica de una solicitud Web

Uso de la autenticación básica se especifica como sigue:

1. La cadena "Básico" se agrega a la `Authorization` encabezado de la solicitud.
1. El nombre de usuario y la contraseña se combinan en una cadena con el formato "nombreDeUsuario: contraseña", que es, a continuación, codificado en base64 y agrega a la `Authorization` encabezado de la solicitud.

Por lo tanto, con un nombre de usuario de 'XamarinUser' y una contraseña de 'XamarinPassword', el encabezado se convierte en:

```csharp
Authorization: Basic WGFtYXJpblVzZXI6WGFtYXJpblBhc3N3b3Jk
```

El `HttpClient` clase puede establecer el `Authorization` valor del encabezado en la `HttpClient.DefaultRequestHeaders.Authorization` propiedad. Porque el `HttpClient` instancia existe en varias solicitudes, el `Authorization` encabezado solo debe establecerse una vez, en lugar de cuándo realizar cada solicitud, como se muestra en el ejemplo de código siguiente:

```csharp
public class RestService : IRestService
{
  HttpClient client;
  ...

  public RestService ()
  {
    var authData = string.Format ("{0}:{1}", Constants.Username, Constants.Password);
    var authHeaderValue = Convert.ToBase64String (Encoding.UTF8.GetBytes (authData));

    client = new HttpClient ();
    ...
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue ("Basic", authHeaderValue);
  }
  ...
}
```

A continuación, cuando se realiza una solicitud a una operación del servicio web se firma la solicitud con el `Authorization` encabezado, que indica si el usuario tiene permiso para invocar la operación.

> [!NOTE]
> Mientras que el servicio REST de ejemplo almacena las credenciales como constantes, no se deben almacenar en un formato no seguro en una aplicación publicada. El [Xamarith.Auth](https://www.nuget.org/packages/Xamarin.Auth/) NuGet proporciona la funcionalidad para almacenar las credenciales de forma segura. Para obtener más información, consulte [almacenar y recuperar información de cuenta en los dispositivos](~/xamarin-forms/data-cloud/authentication/oauth.md).


## <a name="processing-the-authorization-header-server-side"></a>Procesamiento del lado de servidor de encabezado de autorización

El servicio REST de ejemplo que lo acompaña decora cada acción con el `[BasicAuthentication]` atributo. Este atributo se implementa mediante el `BasicAuthenticationAttribute` clase en la solución y se utiliza para analizar el `Authorization` encabezado y determinar si las credenciales con codificación base64 válidas comparándolos con los valores almacenados en *Web.config*. Aunque este enfoque es adecuado para el servicio de ejemplo, requiere la extensión para un servicio web de acceso público.

En el módulo de autenticación básica utilizadas por IIS, los usuarios se autentican con sus credenciales de Windows. Por lo tanto, los usuarios deben tener cuentas en el dominio del servidor. Sin embargo, el modelo de la autenticación básica se puede configurar para permitir la autenticación personalizada, donde las cuentas de usuario se autentican con un origen externo, como una base de datos. Para obtener más información, consulte [la autenticación básica en ASP.NET Web API](http://www.asp.net/web-api/overview/security/basic-authentication) en el sitio Web ASP.NET.

> [!NOTE]
> La autenticación básica no se diseñó para administrar el registro de. Por lo tanto, el método de autenticación básica estándar para cerrar la sesión es finalizar la sesión.

## <a name="summary"></a>Resumen

Este artículo muestra cómo agregar la autenticación básica para las solicitudes web mediante una aplicación de Xamarin.Forms con el `HttpClient` clase. La autenticación básica proporciona acceso a los recursos a solo los clientes que tengan las credenciales correctas. Para obtener información sobre cómo usar [Xamarin.Auth](https://www.nuget.org/packages/Xamarin.Auth/) para administrar el proceso de autenticación en una aplicación de Xamarin.Forms para que los usuarios puedan compartir un back-end al tiempo que sólo tiene acceso a sus datos, consulte [la autenticación de usuarios con un proveedor de identidades](~/xamarin-forms/data-cloud/authentication/oauth.md).


## <a name="related-links"></a>Vínculos relacionados

- [TodoREST (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/)
- [Consumir un servicio web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
