---
title: Administración de configuración
ms.prod: xamarin
ms.assetid: 50d6e780-e768-47f8-9361-3af11e56b87b
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 9318f86077c9cdb98e073e4816dae4fdabbfe568
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="configuration-management"></a>Administración de configuración

Configuración permite la separación de datos que se configura el comportamiento de una aplicación desde el código, lo que permite el comportamiento puede modificarse sin volver a generar la aplicación. Hay dos tipos de configuración: configuración de la aplicación y la configuración de usuario.

Configuración de la aplicación es datos que una aplicación crea y administra. Puede incluir datos como extremos de servicios web fijo, las claves de API y el estado de tiempo de ejecución. Configuración de la aplicación está asociada a la existencia de la aplicación y sólo es significativa para esa aplicación.

Configuración del usuario sea la configuración de una aplicación personalizable que afectan al comportamiento de la aplicación y no requiere frecuente ajuste nuevo. Por ejemplo, una aplicación podría permiten al usuario especificar dónde debe recuperar los datos y cómo se mostrará en la pantalla.

Xamarin.Forms incluye un diccionario persistente que puede usarse para almacenar los datos de configuración. Este diccionario se puede acceder mediante la [ `Application.Current.Properties` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Properties/) propiedad y los datos que se colocan en el se guarda cuando la aplicación entra en un estado de suspensión y se restaura cuando la aplicación se reanuda o vuelve a empezar. Además, el [ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/) clase también tiene un [ `SavePropertiesAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.SavePropertiesAsync()/) método que permite que una aplicación tenga su configuración guardada cuando sea necesario. Para obtener más información acerca de este diccionario, vea [diccionario de propiedades](~/xamarin-forms/app-fundamentals/application-class.md#Properties_Dictionary).

Un inconveniente de almacenar datos mediante el diccionario persistente Xamarin.Forms es que no es fácil enlazado a datos. Por lo tanto, la aplicación móvil eShopOnContainers utiliza la biblioteca de Xam.Plugins.Settings, disponible en [NuGet](https://www.nuget.org/packages/Xam.Plugins.Settings/). Esta biblioteca proporciona un enfoque coherente, seguridad de tipos, multiplataforma para conservar y recuperar la configuración de usuario y la aplicación, durante el uso de la administración de configuración nativo proporcionada por cada plataforma. Además, es fácil de usar enlaces de datos para tener acceso a datos de configuración expuestos por la biblioteca.

> [!NOTE]
> Cuando la biblioteca de Xam.Plugin.Settings puede almacenar la configuración de aplicación y el usuario, no se hace ninguna distinción entre los dos.

## <a name="creating-a-settings-class"></a>Crear una clase de configuración

Cuando se utiliza la biblioteca Xam.Plugins.Settings, debe crearse una sola clase estática que contendrá la configuración de aplicación y el usuario requerida por la aplicación. En el ejemplo de código siguiente se muestra la clase de configuración en la aplicación móvil eShopOnContainers:

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

Configuración puede ser leída y escrita a través de la `ISettings` API, que se proporciona mediante la biblioteca Xam.Plugins.Settings. Esta biblioteca proporciona un singleton que puede utilizarse para tener acceso a la API, `CrossSettings.Current`, y la clase de configuración de una aplicación debe exponer esta singleton a través de un `ISettings` propiedad.

> [!NOTE]
> Directivas using para los espacios de nombres Plugin.Settings y Plugin.Settings.Abstractions deben agregarse a una clase que requiere acceso a los tipos de biblioteca de Xam.Plugins.Settings.

## <a name="adding-a-setting"></a>Agregar una configuración

Cada opción consta de una clave, un valor predeterminado y una propiedad. En el ejemplo de código siguiente se muestra los tres elementos en una configuración de usuario que representa la dirección URL base para los servicios en línea que se conecta la aplicación móvil eShopOnContainers:

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

La clave siempre es una cadena constante que define el nombre de clave, con el valor predeterminado de la configuración que se va un valor de solo lectura estático del tipo requerido. Proporcionar un valor predeterminado se asegura de que está disponible si se recupera un valor sin establecer un valor válido.

El `UrlBase` propiedad estática usa dos métodos de la `ISettings` API para leer o escribir el valor de configuración. El `ISettings.GetValueOrDefault` método se usa para recuperar el valor de una configuración de almacenamiento específicas de la plataforma. Si no se define un valor para la configuración, su valor predeterminado se recupera en su lugar. De forma similar, la `ISettings.AddOrUpdateValue` método se utiliza para conservar un valor de configuración para almacenamiento específico de la plataforma.

En su lugar que definir un valor predeterminado dentro de la `Settings` (clase), el `UrlBaseDefault` cadena obtiene su valor de la `GlobalSetting` clase. El siguiente ejemplo de código muestra la `BaseEndpoint` propiedad y `UpdateEndpoint` método en esta clase:

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

Cada vez que la `BaseEndpoint` propiedad está establecida, el `UpdateEndpoint` se llama al método. Este método actualiza una serie de propiedades, todos ellos se basan en el `UrlBase` configuración de usuario proporcionada por el `Settings` clases que representan puntos de conexión diferentes que se conecta la aplicación móvil eShopOnContainers.

## <a name="data-binding-to-user-settings"></a>Enlace de datos a la configuración de usuario

En la aplicación móvil eShopOnContainers, la `SettingsView` expone dos valores de configuración de usuario. Esta configuración permite que la configuración de si la aplicación debe recuperar los datos de microservicios que se implementan como contenedores de Docker, o si la aplicación debe recuperar los datos de servicios ficticios que no requieren una conexión a internet. Al elegir esta opción recuperar datos de microservicios en contenedores, debe especificarse una dirección URL del extremo base para el microservicios. La figura 7-1 muestra el `SettingsView` cuando el usuario ha elegido recuperar datos de microservicios en contenedores.

![](configuration-management-images/settings-endpoint.png "Configuración de usuario expuestos por la aplicación móvil eShopOnContainers")

**Figura 7-1**: configuración de usuario expuestos por la aplicación móvil eShopOnContainers

Enlace de datos puede utilizarse para recuperar y establecer valores expuestos por la `Settings` clase. Esto se logra mediante controles en el enlace de vista para ver las propiedades de modelo que a su vez tienen acceso a propiedades en la `Settings` clase y cuando se genera una propiedad notificar los cambios si ha cambiado el valor de configuración. Para obtener información sobre cómo la aplicación móvil eShopOnContainers crea la vista de modelos y las asocia a vistas, vea [creación automática de un modelo de vista con un localizador de modelo de vista](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

El siguiente ejemplo de código muestra la [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controlar desde la `SettingsView` que permite al usuario que escriba una dirección URL base del punto de conexión para el microservicios en contenedores:

```xaml
<Entry Text="{Binding Endpoint, Mode=TwoWay}" />
```

Esto [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) control se enlaza a la `Endpoint` propiedad de la `SettingsViewModel` de la clase, utilizando un enlace bidireccional. En el ejemplo de código siguiente se muestra la propiedad de punto de conexión:

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

Cuando el `Endpoint` propiedad está establecida la `UpdateEndpoint` se llama al método, siempre que el valor proporcionado es válido, y cambia la propiedad se notifica. El siguiente ejemplo de código muestra la `UpdateEndpoint` método:

```csharp
private void UpdateEndpoint(string endpoint)  
{  
    Settings.UrlBase = endpoint;  
}
```

Este método actualiza el `UrlBase` propiedad en la `Settings` clase con el valor de dirección URL de extremo base especificado por el usuario, lo que hace que se va a conservar en almacenamiento de información específica de la plataforma.

Cuando el `SettingsView` se navega a, el `InitializeAsync` método en el `SettingsViewModel` se ejecuta la clase. En el ejemplo de código siguiente se muestra este método:

```csharp
public override Task InitializeAsync(object navigationData)  
{  
    ...  
    Endpoint = Settings.UrlBase;  
    ...  
}
```

El método establece la `Endpoint` en el valor de la `UrlBase` propiedad en la `Settings` clase. Obtener acceso a la `UrlBase` esta propiedad hace que la biblioteca de Xam.Plugins.Settings recuperar el valor de la configuración de almacenamiento específicas de la plataforma. Para obtener información acerca de cómo los `InitializeAsync` se invoca el método, consulte [pasar parámetros durante la navegación](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation).

Este mecanismo garantiza que cada vez que un usuario navega a la SettingsView, configuración de usuario se recuperan de almacenamiento específicas de la plataforma y se presentan a través del enlace de datos. A continuación, si el usuario cambia los valores de configuración, enlace de datos garantiza que se conservan inmediatamente al almacenamiento de información específica de la plataforma.

## <a name="summary"></a>Resumen

Configuración permite la separación de datos que se configura el comportamiento de una aplicación desde el código, lo que permite el comportamiento puede modificarse sin volver a generar la aplicación. Configuración de la aplicación es datos que una aplicación crea y administra y configuración del usuario sea la configuración de una aplicación personalizable que afectan al comportamiento de la aplicación y no requiere frecuente ajuste de nuevo.

La biblioteca de Xam.Plugins.Settings proporciona un seguridad de tipos coherente, enfoque multiplataforma para conservar y recuperar la aplicación y la configuración de usuario y el enlace de datos puede utilizarse para tener acceso a la configuración creada con la biblioteca.


## <a name="related-links"></a>Vínculos relacionados

- [Descargar libros electrónicos (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
