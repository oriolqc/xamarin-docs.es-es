---
title: 'Xamarin.Essentials: Aplicaciones auxiliares de sistema de archivos'
description: La clase FileSystem de Xamarin.Essentials contiene una serie de aplicaciones auxiliares para buscar la caché y los directorios de datos de la aplicación y abrir archivos dentro del paquete de aplicación.
ms.assetid: B3EC2DE0-EFC0-410C-AF71-7410AE84CF84
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 37a4fdbdc3f7e1ee309ee9d49c7ad67374035ab4
ms.sourcegitcommit: 6be6374664cd96a7d924c2e0c37aeec4adf8be13
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617532"
---
# <a name="xamarinessentials-file-system-helpers"></a>Xamarin.Essentials: Aplicaciones auxiliares de sistema de archivos

![Versión preliminar de NuGet](~/media/shared/pre-release.png)

La clase **FileSystem** contiene una serie de aplicaciones auxiliares para buscar la caché y los directorios de datos de la aplicación y abrir archivos dentro del paquete de aplicación.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-file-system-helpers"></a>Uso de aplicaciones auxiliares de sistema de archivos

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Para obtener el directorio de la aplicación para almacenar **datos en caché**. Los datos en caché se pueden usar para cualquier dato que se tenga que conservar más tiempo que los datos temporales, pero no deben ser datos necesarios para que el funcionamiento sea correcto.

```csharp
var cacheDir = FileSystem.CacheDirectory;
```

Para obtener el directorio de nivel superior de la aplicación para todos los archivos que no son archivos de datos de usuario. Se realiza una copia de seguridad de estos archivos con el marco de sincronización del sistema operativo. Vea Detalles de implementación de la plataforma a continuación.

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

## <a name="platform-implementation-specifics"></a>Detalles de implementación de la plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

- **CacheDirectory**: devuelve el [CacheDir](https://developer.android.com/reference/android/content/Context.html#getCacheDir) del contexto actual.
- **AppDataDirectory**: devuelve el [FilesDir](https://developer.android.com/reference/android/content/Context.html#getFilesDir) del contexto actual y se realiza una copia de seguridad mediante [Copia de seguridad automática](https://developer.android.com/guide/topics/data/autobackup.html) a partir de la API 23 y versiones posteriores.

Agregue un archivo a la carpeta **Activos** del proyecto de Android y marque la acción de compilación como **AndroidAsset** para usarla con `OpenAppPackageFileAsync`.

# <a name="iostabios"></a>[iOS](#tab/ios)

- **CacheDirectory**: devuelve el directorio [Library/Caches](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).
- **AppDataDirectory**: devuelve el directorio [Library](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html) del que iTunes e iCloud realizan una copia de seguridad.

Agregue cualquier archivo a la carpeta **Activos** del proyecto de iOS y marque la acción de compilación como **BundledResource** para usarla con `OpenAppPackageFileAsync`.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

- **CacheDirectory**: devuelve el directorio [LocalCacheFolder](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.localcachefolder#Windows_Storage_ApplicationData_LocalCacheFolder).
- **AppDataDirectory**: devuelve el directorio [LocalFolder](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.localfolder#Windows_Storage_ApplicationData_LocalFolder) del que se realiza una copia de seguridad en la nube.

Agregue cualquier archivo a la raíz del proyecto de UWP y marque la acción de compilación como **Content** para usarla con `OpenAppPackageFileAsync`.

--------------

## <a name="api"></a>API

- [Código fuente de las aplicaciones auxiliares de sistema de archivos](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/FileSystem)
- [Documentación de API para FileSystem](xref:Xamarin.Essentials.FileSystem)
