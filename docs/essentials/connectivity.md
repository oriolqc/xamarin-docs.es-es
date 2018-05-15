---
title: Conectividad de Xamarin.Essentials
description: La clase de conectividad le permite supervisar los cambios en las condiciones de red del dispositivo, compruebe el acceso de red actual y cómo está conectado actualmente.
ms.assetid: E1B1F152-B1D5-4227-965E-C0AEBF528F49
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: d2184f1e9817b473eac1d0b69a7637bc862de4cf
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
---
# <a name="xamarinessentials-connectivity"></a>Conectividad de Xamarin.Essentials

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **conectividad** permite clase supervisar cambios en las condiciones de red del dispositivo, compruebe el acceso de red actual y cómo está conectado actualmente.

## <a name="getting-started"></a>Introducción

Para tener acceso a la **conectividad** se requiere la siguiente configuración específica de plataforma de funcionalidad.

# <a name="androidtabandroid"></a>[Android](#tab/android)

El `AccessNetworkState` permiso es necesario y debe configurarse en el proyecto de Android. Esto puede agregarse de las maneras siguientes:

Abra la **AssemblyInfo.cs** de archivos en el **propiedades** carpeta y agregar:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessNetworkState)]
```

O bien, actualice el manifiesto de Android:

Abra la **AndroidManifest.xml** de archivos en el **propiedades** carpeta y agregue lo siguiente dentro de la **manifiesto** nodo.

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

O haga clic con el botón secundario en el proyecto de Android y abra las propiedades del proyecto. En **manifiesto Android** buscar el **los permisos necesarios:** área y compruebe el **estado de acceso de red** permiso. Esto actualizará automáticamente el **AndroidManifest.xml** archivo.

# <a name="iostabios"></a>[iOS](#tab/ios)

No se necesita ninguna instalación adicional.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

No se necesita ninguna instalación adicional.

-----

## <a name="using-connectivity"></a>Uso de conectividad

Agregue una referencia a Xamarin.Essentials en la clase:

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

[Acceso a la red](xref:Xamarin.Essentials.NetworkAccess) se divide en las categorías siguientes:

* **Internet** – acceso Local y a internet.
* **ConstrainedInternet** – limitado el acceso a internet. Indica la conectividad del portal capturada, donde se proporciona acceso local a un portal web, pero el acceso a Internet requiere que se proporcionan credenciales específicas a través de un portal.
* **Local** : Local solo acceso a la red.
* **Ninguno** – conectividad no está disponible.
* **Desconocido** : no se puede determinar la conectividad a internet.

Puede comprobar qué tipo de [perfil de conexión](xref:Xamarin.Essentials.ConnectionProfile) esté usando activamente el dispositivo:

```csharp
var profiles = Connectivity.Profiles;
if (profiles.Contains(ConnectionProfile.WiFi))
{
    // Active Wi-Fi connection.
}
```

Cada vez que el perfil de conexión o red acceso cambios puede recibir un evento cuando activa:

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

Es importante tener en cuenta que es posible que `Internet` notifica `NetworkAccess` pero no está disponible el acceso completo a la web. Debido a cómo funciona la conectividad en cada plataforma sólo puede garantizar que haya disponible una conexión. Por ejemplo, el dispositivo puede estar conectado a una red Wi-Fi, pero el enrutador está desconectado de internet. En esta instancia se puede notificar Internet, pero no está disponible una conexión activa.

## <a name="api"></a>API

* [Código fuente de conectividad](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Connectivity)
* [Documentación de API de conectividad](xref:Xamarin.Essentials.Connectivity)
