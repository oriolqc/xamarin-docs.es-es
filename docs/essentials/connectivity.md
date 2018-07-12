---
title: 'Xamarin.Essentials: conectividad'
description: La clase de conectividad en Xamarin.Essentials le permite supervisar los cambios en las condiciones de red del dispositivo, compruebe el acceso de red actual, y cómo está conectado actualmente.
ms.assetid: E1B1F152-B1D5-4227-965E-C0AEBF528F49
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 54c165e15e725caaecb1573b74cfe295170db141
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2018
ms.locfileid: "38848614"
---
# <a name="xamarinessentials-connectivity"></a>Xamarin.Essentials: conectividad

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **conectividad** permite clase supervisar los cambios en las condiciones de red del dispositivo, compruebe el acceso de red actual, y cómo está conectado actualmente.

## <a name="getting-started"></a>Introducción

Para tener acceso a la **conectividad** se requiere la funcionalidad de la siguiente configuración específica de plataforma.

# <a name="androidtabandroid"></a>[Android](#tab/android)

El `AccessNetworkState` permiso es necesario y debe configurarse en el proyecto de Android. Esto se puede agregar de las maneras siguientes:

Abra el **AssemblyInfo.cs** archivo bajo el **propiedades** carpeta y agregue:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessNetworkState)]
```

O actualice el manifiesto de Android:

Abra el **AndroidManifest.xml** de archivos en el **propiedades** carpeta y agregue lo siguiente dentro de la **manifiesto** nodo.

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

O haga clic con el botón derecho en el proyecto de Android y abra las propiedades del proyecto. En **manifiesto de Android** encontrar el **los permisos necesarios:** área y compruebe el **estado de acceso de red** permiso. Esto actualizará automáticamente el **AndroidManifest.xml** archivo.

# <a name="iostabios"></a>[iOS](#tab/ios)

No se requiere ninguna configuración adicional.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

No se requiere ninguna configuración adicional.

-----

## <a name="using-connectivity"></a>Uso de conectividad

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Comprobar el acceso de red actual:

```csharp
var current = Connectivity.NetworkAccess;

if (current == NetworkAccess.Internet)
{
    // Connection to internet is available
}
```

[Acceso a la red](xref:Xamarin.Essentials.NetworkAccess) se divide en las siguientes categorías:

* **Internet** – acceso Local y en internet.
* **ConstrainedInternet** : limita el acceso a internet. Indica la conectividad del portal cautiva, donde se proporciona acceso local a un portal web, pero el acceso a Internet requiere que se proporcionan credenciales específicas a través de un portal.
* **Local** : Local solo acceso de red.
* **Ninguno** : no hay conectividad está disponible.
* **Desconocido** : no se puede determinar la conectividad a internet.

Puede comprobar qué tipo de [perfil de conexión](xref:Xamarin.Essentials.ConnectionProfile) esté usando activamente el dispositivo:

```csharp
var profiles = Connectivity.Profiles;
if (profiles.Contains(ConnectionProfile.WiFi))
{
    // Active Wi-Fi connection.
}
```

Puede recibir un evento cuando se desencadena cada vez que el perfil de conexión o red acceder a los cambios:

```csharp
public class ConnectivityTest
{
    public ConnectivityTest()
    {
        // Register for connectivity changes, be sure to unsubscribe when finished
        Connectivity.ConnectivityChanged += Connectivity_ConnectivityChanged;
    }

    void Connectivity_ConnectivityChanged(ConnectivityChangedEventArgs  e)
    {
        var access = e.NetworkAccess;
        var profiles = e.Profiles;
    }
}
```

## <a name="limitations"></a>Limitaciones

Es importante tener en cuenta que es posible que `Internet` notificada por `NetworkAccess` pero acceso completo a la web no está disponible. Debido a cómo funciona la conectividad en cada plataforma solo puede garantizar que una conexión está disponible. Por ejemplo, el dispositivo puede estar conectado a una red Wi-Fi, pero el enrutador está desconectado de internet. En esta instancia se puede registrar Internet, pero no está disponible una conexión activa.

## <a name="api"></a>API

* [Código fuente de conectividad](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Connectivity)
* [Documentación de API de conectividad](xref:Xamarin.Essentials.Connectivity)
