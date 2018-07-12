---
title: 'Xamarin.Essentials: Aplicaciones auxiliares de sistema de archivos'
description: La clase del sistema de archivos en Xamarin.Essentials contiene una serie de aplicaciones auxiliares para encontrar la memoria caché de la aplicación y los directorios de datos y abrir archivos dentro del paquete de aplicación.
ms.assetid: B3EC2DE0-EFC0-410C-AF71-7410AE84CF84
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 13293ec05261cbdc1e70fd278002d1af18654851
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815623"
---
# <a name="xamarinessentials-file-system-helpers"></a>Xamarin.Essentials: Aplicaciones auxiliares de sistema de archivos

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **FileSystem** clase contiene una serie de aplicaciones auxiliares para encontrar los directorios de caché y los datos de la aplicación y abrir archivos dentro del paquete de aplicación.

## <a name="using-file-system-helpers"></a>Usar aplicaciones auxiliares de sistema de archivos

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Para obtener el directorio de la aplicación para almacenar **almacenar en caché datos**. Almacenar datos en caché pueden usarse para cualquier dato que deba conservarse durante más de los datos temporales, pero no debe ser datos necesarios para que funcione correctamente.

```csharp
var cacheDir = FileSystem.CacheDirectory;
```

Para obtener el directorio de nivel superior de la aplicación para todos los archivos que no son archivos de datos de usuario. Estos archivos se copian con el sistema operativo, sincronización de framework. Ver detalles de implementación de plataforma a continuación.

```csharp
var mainDir = FileSystem.AppDataDirectory;
```

Para abrir un archivo que se incluye en el paquete de aplicación:

```csharp
 using (var stream = await FileSystem.OpenAppPackageFileAsync(templateFileName))
 {
    using (var reader = new StreamReader(stream))
    {
        var fileContents = await reader.ReadToEndAsync();
    }
 }
```

## <a name="platform-implementation-specifics"></a>Detalles de implementación de plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

- **CacheDirectory** : devuelve el [CacheDir](https://developer.android.com/reference/android/content/Context.html#getCacheDir) del contexto actual.
- **AppDataDirectory** : devuelve el [FilesDir](https://developer.android.com/reference/android/content/Context.html#getFilesDir) del contexto actual y son una copia de seguridad mediante [copia de seguridad automática](https://developer.android.com/guide/topics/data/autobackup.html) a partir de API 23 y versiones posteriores.

Agregue un archivo en el **activos** carpeta Android del proyecto y marcar la acción de compilación como **AndroidAsset** para usarlo con `OpenAppPackageFileAsync`.

# <a name="iostabios"></a>[iOS](#tab/ios)

- **CacheDirectory** : devuelve el [biblioteca/cachés](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html) directory.
- **AppDataDirectory** : devuelve el [biblioteca](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html) directorio que está respaldada por iTunes e iCloud.

Agregue un archivo en el **recursos** carpeta en iOS del proyecto y marcar la acción de compilación como **BundledResource** para usarlo con `OpenAppPackageFileAsync`.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

- **CacheDirectory** : devuelve el [LocalCacheFolder](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdata.localcachefolder#Windows_Storage_ApplicationData_LocalCacheFolder) directorio...
- **AppDataDirectory** : devuelve el [LocalFolder](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdata.localfolder#Windows_Storage_ApplicationData_LocalFolder) directorio que se copia en la nube.

Agregue un archivo en la raíz del proyecto de UWP y marcar la acción de compilación como **contenido** para usarlo con `OpenAppPackageFileAsync`.

--------------

## <a name="api"></a>API

- [Código fuente de las aplicaciones auxiliares de sistema de archivos](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/FileSystem)
- [Documentación de API del sistema de archivos](xref:Xamarin.Essentials.FileSystem)
