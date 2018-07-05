---
title: 'Xamarin.Essentials: Almacenamiento seguro'
description: Este documento describe la clase SecureStorage en Xamarin.Essentials, lo que ayuda a almacenar de forma segura los pares clave/valor simple. Describe cómo usar la clase, especificaciones de implementación de la plataforma y limitaciones.
ms.assetid: 78856C0D-76BB-406E-A880-D5A3987B7D64
author: redth
ms.author: jodick
ms.date: 05/04/2018
ms.openlocfilehash: fae5f5f0f15d80e2f3bdce26b8beb5f6fae2f81f
ms.sourcegitcommit: 081a2d094774c6f75437d28b71d22607e33aae71
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/03/2018
ms.locfileid: "37403447"
---
# <a name="xamarinessentials-secure-storage"></a>Xamarin.Essentials: Almacenamiento seguro

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **SecureStorage** clase ayuda a almacenar de forma segura los pares clave/valor simple.

## <a name="getting-started"></a>Introducción

Para tener acceso a la **SecureStorage** funcionalidad, se requiere la siguiente configuración específica de la plataforma:

# <a name="androidtabandroid"></a>[Android](#tab/android)

No se requiere ninguna configuración adicional.

# <a name="iostabios"></a>[iOS](#tab/ios)

Al desarrollar en el simulador de iOS, habilite la **llaves** derechos y agregue un grupo de acceso de la cadena de claves para el identificador de paquete de la aplicación.

Abra el **Entitlements.plist** en el proyecto de iOS y busque el **llaves** derechos y habilitarla. Esto agregará automáticamente el identificador de la aplicación como un grupo.

En las propiedades del proyecto, bajo **firma de lote de iOS** establecer el **derechos personalizados** a **Entitlements.plist**.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

No se requiere ninguna configuración adicional.

-----

## <a name="using-secure-storage"></a>Uso de almacenamiento seguro

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Para guardar un valor para un determinado _clave_ en un almacenamiento seguro:

```csharp
await SecureStorage.SetAsync("oauth_token", "secret-oauth-token-value");
```

Para recuperar un valor de almacenamiento seguro:

```csharp
var oauthToken = await SecureStorage.GetAsync("oauth_token");
```

> [!NOTE]
> Si no hay ningún valor asociado con la clave solicitada, `GetAsync` devolverá `null`.

Para quitar una clave específica, llame al:

```csharp
SecureStorage.Remove("oauth_token");
```

Para quitar todas las claves, llamar:

```csharp
SecureStorage.RemoveAll();
```


## <a name="platform-implementation-specifics"></a>Detalles de implementación de plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

El [almacén de claves Android](https://developer.android.com/training/articles/keystore.html) se usa para almacenar la clave de cifrado utilizada para cifrar el valor antes de guardarlo en un [preferencias compartidas](https://developer.android.com/training/data-storage/shared-preferences.html) con un nombre de archivo de **.xamarinessentials [YOUR-APP-ID de paquete]** .  La clave usada en el archivo de preferencias compartido es un _Hash MD5_ de la clave pasada en el `SecureStorage` la API.

## <a name="api-level-23-and-higher"></a>Nivel de API 23 o superior

En los niveles de API más recientes, una **AES** clave se obtiene desde el almacén de claves Android y utilizar con un **AES/GCM/NoPadding** cifrado para cifrar el valor antes de almacenarse en el archivo de preferencias compartido.

## <a name="api-level-22-and-lower"></a>Nivel de API 22 e inferior

En los niveles de API anteriores, el almacén de claves Android admite solo almacenamiento **RSA** claves, que se usa con un **ECB/RSA/PKCS1Padding** cifrado para cifrar un **AES** (aleatoriamente de clave se genera en tiempo de ejecución) y se almacenan en el archivo de preferencias compartidas en la clave _SecureStorageKey_, si aún no ha generado una.

Todos los valores cifrados, se eliminará cuando la aplicación se desinstala del dispositivo.

# <a name="iostabios"></a>[iOS](#tab/ios)

[Cadena de claves](https://developer.xamarin.com/api/type/Security.SecKeyChain/) se usa para almacenar los valores de forma segura en dispositivos iOS.  El `SecRecord` utilizado para almacenar el valor tiene un `Service` valor establecido en **.xamarinessentials [YOUR-APP-BUNDLE-ID]**.

En algunos casos, los datos de cadena de claves está sincronizados con iCloud y desinstalar la aplicación no puede eliminar los valores de seguridad de iCloud y otros dispositivos del usuario.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[DataProtectionProvider](https://docs.microsoft.com/uwp/api/windows.security.cryptography.dataprotection.dataprotectionprovider) se utiliza para valores de cifrado de forma segura en dispositivos UWP.

Los valores de cifrado se almacenan en `ApplicationData.Current.LocalSettings`, dentro de un contenedor con el nombre de **.xamarinessentials [YOUR-APP-ID]**.

Desinstalar la aplicación hará que el _LocalSettings_y valores de todos los cifrados que también se puede quitar.

-----

## <a name="limitations"></a>Limitaciones

Esta API está destinada a almacenar pequeñas cantidades de texto.  Rendimiento puede ser lento si se intenta utilizar para almacenar grandes cantidades de texto.

## <a name="api"></a>API

- [Código fuente de SecureStorage](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/SecureStorage)
- [Documentación de API SecureStorage](xref:Xamarin.Essentials.SecureStorage)
