---
title: Administración de configuración
description: Este capítulo explica cómo la aplicación móvil de eShopOnContainers implementa la administración de configuración para proporcionar la configuración de la aplicación y la configuración de usuario.
ms.prod: xamarin
ms.assetid: 50d6e780-e768-47f8-9361-3af11e56b87b
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 6f32d8f328232bdfc644da57bdb3201c60010063
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995366"
---
# <a name="configuration-management"></a>Administración de configuración

La configuración permite la separación de datos que se configura el comportamiento de una aplicación desde el código, lo que permite el comportamiento que se puede cambiar sin volver a generar la aplicación. Hay dos tipos de configuración: configuración de la aplicación y la configuración de usuario.

Configuración de la aplicación es datos que una aplicación crea y administra. Puede incluir datos como extremos de servicio web fijos, claves de API y estado en tiempo de ejecución. Configuración de la aplicación está asociada a la existencia de la aplicación y solo es significativa para esa aplicación.

Configuración de usuario es la configuración de una aplicación personalizable que afectan al comportamiento de la aplicación y no requiere el ajuste de volver a frecuentes. Por ejemplo, una aplicación podría permiten al usuario especificar dónde debe recuperar los datos y cómo se mostrará en la pantalla.

Xamarin.Forms incluye un diccionario persistente que se puede usar para almacenar los datos de configuración. Este diccionario se puede acceder mediante el [ `Application.Current.Properties` ](xref:Xamarin.Forms.Application.Properties) propiedad y los datos que se colocan en él se guarda cuando la aplicación entra en un estado de suspensión y se restaura cuando la aplicación se reanuda o se inicie de nuevo. Además, el [ `Application` ](xref:Xamarin.Forms.Application) clase también tiene un [ `SavePropertiesAsync` ](xref:Xamarin.Forms.Application.SavePropertiesAsync) método que permite que una aplicación tenga su configuración guardada cuando sea necesario. Para obtener más información acerca de este diccionario, vea [diccionario de propiedades](~/xamarin-forms/app-fundamentals/application-class.md#Properties_Dictionary).

Un inconveniente de almacenar datos mediante el diccionario persistente Xamarin.Forms es que no es fácilmente datos enlazados a. Por lo tanto, la aplicación móvil de eShopOnContainers utiliza la biblioteca Xam.Plugins.Settings, disponible en [NuGet](https://www.nuget.org/packages/Xam.Plugins.Settings/). Esta biblioteca proporciona un enfoque coherente, segura y multiplataforma para persistir y recuperar la configuración de aplicación y el usuario, al usar la administración de configuración nativa proporcionada por cada plataforma. Además, es fácil de usar el enlace de datos para tener acceso a datos de configuración expuestos por la biblioteca.

> [!NOTE]
> Mientras que la biblioteca Xam.Plugin.Settings puede almacenar la configuración de aplicación y el usuario, hace ninguna distinción entre los dos.

## <a name="creating-a-settings-class"></a>Creación de una clase de configuración

Cuando se usa la biblioteca Xam.Plugins.Settings, debe crearse una única clase estática que contiene la configuración de aplicación y usuario requerida por la aplicación. El ejemplo de código siguiente muestra la clase de configuración en la aplicación móvil de eShopOnContainers:

```csharp
public static class Settings  
{  
    private static ISettings AppSettings  
    {  
        get  
        {  
            return CrossSettings.Current;  
        }  
    }  
    ...  
}
```

Se puede leer y escribir a través de configuración de la `ISettings` API, que proporciona la biblioteca Xam.Plugins.Settings. Esta biblioteca proporciona un singleton que se puede usar para acceder a la API, `CrossSettings.Current`, y la clase de configuración de una aplicación debe exponer este singleton a través de un `ISettings` propiedad.

> [!NOTE]
> Directivas using para los espacios de nombres Plugin.Settings y Plugin.Settings.Abstractions deben agregarse a una clase que requiere acceso a los tipos de biblioteca Xam.Plugins.Settings.

## <a name="adding-a-setting"></a>Adición de un valor

Cada opción de configuración consta de una clave, valor predeterminado y una propiedad. El siguiente ejemplo de código muestra los tres elementos en una configuración de usuario que representa la dirección URL base para los servicios en línea que conecta la aplicación móvil de eShopOnContainers con:

```csharp
public static class Settings  
{  
    ...  
    private const string IdUrlBase = "url_base";  
    private static readonly string UrlBaseDefault = GlobalSetting.Instance.BaseEndpoint;  
    ...  

    public static string UrlBase  
    {  
        get  
        {  
            return AppSettings.GetValueOrDefault<string>(IdUrlBase, UrlBaseDefault);  
        }  
        set  
        {  
            AppSettings.AddOrUpdateValue<string>(IdUrlBase, value);  
        }  
    }  
}
```

La clave siempre es una cadena constante que define el nombre de clave, con el valor predeterminado para la configuración que se va un valor de solo lectura estáticos del tipo requerido. Proporcionar un valor predeterminado, se garantiza que está disponible si se recupera un valor no establecido un valor válido.

El `UrlBase` propiedad estática usa dos métodos desde el `ISettings` API para leer o escribir el valor de configuración. El `ISettings.GetValueOrDefault` método se utiliza para recuperar un valor de configuración de almacenamiento específico de la plataforma. Si no se define ningún valor para la configuración, su valor predeterminado se recupera en su lugar. De forma similar, la `ISettings.AddOrUpdateValue` método se usa para conservar un valor de configuración para almacenamiento específico de la plataforma.

En su lugar que defina un valor predeterminado dentro de la `Settings` (clase), el `UrlBaseDefault` cadena obtiene su valor de la `GlobalSetting` clase. El siguiente ejemplo de código muestra la `BaseEndpoint` propiedad y `UpdateEndpoint` método de esta clase:

```csharp
public class GlobalSetting  
{  
    ...  
    public string BaseEndpoint  
    {  
        get { return _baseEndpoint; }  
        set  
        {  
            _baseEndpoint = value;  
            UpdateEndpoint(_baseEndpoint);  
        }  
    }  
    ...  

    private void UpdateEndpoint(string baseEndpoint)  
    {  
        RegisterWebsite = string.Format("{0}:5105/Account/Register", baseEndpoint);  
        CatalogEndpoint = string.Format("{0}:5101", baseEndpoint);  
        OrdersEndpoint = string.Format("{0}:5102", baseEndpoint);  
        BasketEndpoint = string.Format("{0}:5103", baseEndpoint);  
        IdentityEndpoint = string.Format("{0}:5105/connect/authorize", baseEndpoint);  
        UserInfoEndpoint = string.Format("{0}:5105/connect/userinfo", baseEndpoint);  
        TokenEndpoint = string.Format("{0}:5105/connect/token", baseEndpoint);  
        LogoutEndpoint = string.Format("{0}:5105/connect/endsession", baseEndpoint);  
        IdentityCallback = string.Format("{0}:5105/xamarincallback", baseEndpoint);  
        LogoutCallback = string.Format("{0}:5105/Account/Redirecting", baseEndpoint);  
    }  
}
```

Cada vez que el `BaseEndpoint` propiedad está establecida, el `UpdateEndpoint` se llama al método. Este método actualiza una serie de propiedades, que se basan en el `UrlBase` configuración de usuario proporcionada por el `Settings` clases que representan los distintos puntos de conexión que se conecta la aplicación móvil de eShopOnContainers.

## <a name="data-binding-to-user-settings"></a>Enlace de datos a la configuración de usuario

En la aplicación móvil de eShopOnContainers, el `SettingsView` expone dos configuraciones de usuario. Esta configuración permite que la configuración de si la aplicación debe recuperar los datos de microservicios que se implementan como contenedores de Docker, o si la aplicación debe recuperar los datos de servicios ficticios que no requieren una conexión a internet. Al elegir esta opción recuperar datos de microservicios en contenedores, se debe especificar una dirección URL de punto de conexión base para los microservicios. Figura 7-1 se muestra el `SettingsView` cuando el usuario ha elegido recuperar datos de microservicios en contenedores.

![](configuration-management-images/settings-endpoint.png "Configuración de usuario expuestos por la aplicación móvil de eShopOnContainers")

**Figura 7-1**: configuración de usuario expuestos por la aplicación móvil de eShopOnContainers

Enlace de datos puede usarse para recuperar y establecer la configuración expuesta por el `Settings` clase. Esto se logra mediante los controles en el enlace de vista para ver las propiedades de modelo que a su vez tienen acceso a las propiedades en el `Settings` clase y generar una propiedad notificar los cambios si ha cambiado el valor de configuración. Para obtener información acerca de cómo la aplicación móvil de eShopOnContainers crea la vista de modelos y los asocia a vistas, consulte [creación automática de un modelo de vista con un localizador de modelo de vista](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

El siguiente ejemplo de código muestra la [ `Entry` ](xref:Xamarin.Forms.Entry) controlar desde la `SettingsView` que permite al usuario que escriba una dirección URL de punto de conexión de base de los microservicios en contenedores:

```xaml
<Entry Text="{Binding Endpoint, Mode=TwoWay}" />
```

Esto [ `Entry` ](xref:Xamarin.Forms.Entry) control se enlaza a la `Endpoint` propiedad de la `SettingsViewModel` clase, utilizando un enlace bidireccional. El ejemplo de código siguiente muestra la propiedad de punto de conexión:

```csharp
public string Endpoint  
{  
    get { return _endpoint; }  
    set  
    {  
        _endpoint = value;  

        if(!string.IsNullOrEmpty(_endpoint))  
        {  
            UpdateEndpoint(_endpoint);  
        }  

        RaisePropertyChanged(() => Endpoint);  
    }  
}
```

Cuando el `Endpoint` propiedad está establecida la `UpdateEndpoint` se llama al método, siempre que el valor proporcionado es válido, y cambia la propiedad notificación se genera. El siguiente ejemplo de código muestra la `UpdateEndpoint` método:

```csharp
private void UpdateEndpoint(string endpoint)  
{  
    Settings.UrlBase = endpoint;  
}
```

Este método actualiza el `UrlBase` propiedad en el `Settings` clase con el valor de dirección URL de punto de conexión base especificado por el usuario, lo que hace que se conservan en almacenamiento específico de la plataforma.

Cuando el `SettingsView` se navega a, el `InitializeAsync` método en el `SettingsViewModel` se ejecuta la clase. El ejemplo de código siguiente muestra este método:

```csharp
public override Task InitializeAsync(object navigationData)  
{  
    ...  
    Endpoint = Settings.UrlBase;  
    ...  
}
```

El método establece el `Endpoint` en el valor de la `UrlBase` propiedad en el `Settings` clase. Obtener acceso a la `UrlBase` propiedad hace que la biblioteca Xam.Plugins.Settings recuperar el valor de configuración de almacenamiento específico de la plataforma. Para obtener información acerca de cómo los `InitializeAsync` se invoca el método, consulte [pasar parámetros durante la navegación](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation).

Este mecanismo garantiza que cada vez que un usuario navega a la SettingsView, configuración de usuario se recuperan de almacenamiento específico de la plataforma y presentan mediante enlace de datos. A continuación, si el usuario cambia los valores de configuración, el enlace de datos garantiza que se conservan inmediatamente al almacenamiento específico de la plataforma.

## <a name="summary"></a>Resumen

La configuración permite la separación de datos que se configura el comportamiento de una aplicación desde el código, lo que permite el comportamiento que se puede cambiar sin volver a generar la aplicación. Configuración de la aplicación es datos que una aplicación crea y administra y configuración de usuario es la configuración de una aplicación personalizable que afectan al comportamiento de la aplicación y no requiere el ajuste de volver a frecuentes.

La biblioteca Xam.Plugins.Settings proporciona una coherente, con seguridad de tipos, enfoque multiplataforma para conservar y recuperar la aplicación y la configuración de usuario y el enlace de datos puede utilizarse para tener acceso a la configuración creada con la biblioteca.


## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
