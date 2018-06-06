---
title: 'Xamarin.Essentials: Almacenamiento seguro'
description: Este documento describe la clase SecureStorage en Xamarin.Essentials, lo que ayuda a almacenar de forma segura los pares clave/valor simple. Explica cómo utilizar la clase, detalles de implementación de plataforma y limitaciones.
ms.assetid: 78856C0D-76BB-406E-A880-D5A3987B7D64
author: redth
ms.author: jodick
ms.date: 05/04/2018
ms.openlocfilehash: d9fd5b5fd0d4dc29f4d2531521370618f97e3846
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783163"
---
# <a name="xamarinessentials-secure-storage"></a>Xamarin.Essentials: Almacenamiento seguro

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **SecureStorage** clase le ayuda a almacenar de forma segura los pares clave/valor simple.

## <a name="getting-started"></a>Introducción

Para tener acceso a la **SecureStorage** la siguiente configuración específica de la plataforma de función, se necesita:

# <a name="androidtabandroid"></a>[Android](#tab/android)

No se necesita ninguna instalación adicional.

# <a name="iostabios"></a>[iOS](#tab/ios)

Al desarrollar en el simulador de iOS, habilite la **llaveros** derechos y agregar un grupo de acceso de cadena de claves para el identificador de paquete de la aplicación.

Abra la **Entitlements.plist** en el proyecto de iOS y buscar el **llaveros** derechos y habilitarla. Esto agregará automáticamente el identificador de la aplicación como un grupo.

En las propiedades del proyecto, en **agrupación de firma de iOS** establecer el **personalizado derechos** a **Entitlements.plist**.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

No se necesita ninguna instalación adicional.

-----

## <a name="using-secure-storage"></a>Uso de almacenamiento seguro

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

Para guardar un valor para una determinada _clave_ en un almacenamiento seguro:

```csharp
await SecureStorage.SetAsync("oauth_token", "secret-oauth-token-value");
```

Para recuperar un valor de almacenamiento seguro:

```csharp
var oauthToken = await SecureStorage.GetAsync("oauth_token");
```

## <a name="platform-implementation-specifics"></a>Detalles de implementación de plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

El [KeyStore Android](https://developer.android.com/training/articles/keystore.html) se utiliza para almacenar la clave de cifrado utilizada para cifrar el valor antes de guardarlos en un [preferencias compartidas](https://developer.android.com/training/data-storage/shared-preferences.html) con un nombre de archivo de **.xamarinessentials [YOUR-paquete-identificador de la aplicación]** .  La clave usada en el archivo de preferencias compartidas es una _Hash MD5_ de la clave pasada en el `SecureStorage` la API.

## <a name="api-level-23-and-higher"></a>Nivel de API 23 y versiones posteriores

En los niveles de API más recientes, una **AES** clave se obtiene desde el almacén de claves Android y utilizar con un **AES/GCM/NoPadding** cifrado para cifrar el valor antes de almacenarse en el archivo de preferencias compartidas.

## <a name="api-level-22-and-lower"></a>Nivel de API 22 e inferior

En los niveles anteriores de API, el almacén de claves Android sólo admite almacenar **RSA** claves, que se usa con un **RSA/ECB/PKCS1Padding** cifrado para cifrar un **AES** (aleatoriamente de clave se genera en tiempo de ejecución) y se almacenan en el archivo de preferencias compartidas en la clave _SecureStorageKey_, si aún no ha generado una.

Cuando la aplicación se desinstala del dispositivo, se quitarán todos los valores cifrados.

# <a name="iostabios"></a>[iOS](#tab/ios)

[Cadena de claves](https://developer.xamarin.com/api/type/Android.Security.KeyChain/) se utiliza para almacenar valores de forma segura en dispositivos iOS.  El `SecRecord` utilizado para almacenar el valor tiene un `Service` valor establecido en **.xamarinessentials [YOUR-aplicaciones-identificador de paquete]**.

En algunos casos, datos de cadena de claves se sincronizan con iCloud y desinstalar la aplicación no debe eliminar los valores seguros de iCloud y otros dispositivos del usuario.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[DataProtectionProvider](https://docs.microsoft.com/en-us/uwp/api/windows.security.cryptography.dataprotection.dataprotectionprovider) se utiliza para valores de encryped forma segura en dispositivos UWP.

Encryped valores se almacenan en `ApplicationData.Current.LocalSettings`, dentro de un contenedor con el nombre de **.xamarinessentials [identificador de aplicación de su]**.

Desinstalar la aplicación hará que la _LocalSettings_y cifrados todos los valores que se quitará también.

-----

## <a name="limitations"></a>Limitaciones

Esta API se ha diseñado para almacenar pequeñas cantidades de texto.  Rendimiento puede ser lento si intenta utilizarlo para almacenar grandes cantidades de texto.

## <a name="api"></a>API

- [Código fuente de SecureStorage](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/SecureStorage)
- [Documentación de la API de SecureStorage](xref:Xamarin.Essentials.SecureStorage)
