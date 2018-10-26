---
title: Acceso a archivos con Xamarin.Android
description: Esta guía explica el acceso a los archivos en Xamarin.Android
ms.prod: xamarin
ms.assetid: FC1CFC58-B799-4DD6-8ED1-DE36B0E56856
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/23/2018
ms.openlocfilehash: 476f1c50a2f1a4199dfaf1996fc9c16615b40598
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116802"
---
# <a name="file-storage-and-access-with-xamarinandroid"></a>Almacenamiento de archivos y acceso con Xamarin.Android

Es un requisito común para aplicaciones Android manipular archivos &ndash; guardado de imágenes, descarga de documentos o exportar datos para compartir con otros programas. Android (que se basa en Linux) es compatible con esto proporcionando espacio para almacenamiento de archivos. Android agrupa el sistema de archivos en dos tipos diferentes de almacenamiento:

* **Almacenamiento interno** &ndash; se trata de una parte del sistema de archivos que solo puede tener acceso la aplicación o el sistema operativo.
* **Almacenamiento externo** &ndash; se trata de una partición para el almacenamiento de archivos que sea accesible por todas las aplicaciones, el usuario y, posiblemente, otros dispositivos. En algunos dispositivos, almacenamiento externo puede ser extraíble (por ejemplo, una tarjeta SD).

Estas agrupaciones son conceptuales sólo y necesariamente no hacen referencia a una sola partición o el directorio en el dispositivo. Un dispositivo Android siempre proporcionará la partición para almacenamiento interno y externo. Es posible que algunos dispositivos pueden tener varias particiones que se consideran almacenamiento externo. Independientemente de la partición de las API para leer, escribir o creación de archivos es el mismo. Hay dos conjuntos de API que puede usar una aplicación de Xamarin.Android para el acceso de archivo:

1. **Las API de .NET (proporcionado por Mono y ajustado por Xamarin.Android)** &ndash; entre ellos se incluyen la [aplicaciones auxiliares de sistema de archivos](~/essentials/file-system-helpers.md?context=xamarin/android) proporcionada por [Xamarin.Essentials](~/essentials/index.md?context=xamarin/android). Las API de .NET proporcionan la mejor compatibilidad multiplataforma y por lo tanto se incluirá el objetivo de esta guía en estas API.
1. **El acceso a archivos de Java native API (proporcionado por Java y ajustado por Xamarin.Android)** &ndash; Java proporciona sus propias API para leer y escribir archivos. Estas son una alternativa a las API. NET, completamente aceptable, pero son específicas de Android y no son adecuadas para las aplicaciones que están diseñadas para ser multiplataforma.

Leer y escribir en archivos son casi idéntico en Xamarin.Android y sirve para cualquier otra aplicación. NET. La aplicación de Xamarin.Android determina la ruta de acceso al archivo que se manipulará, a continuación, usa estándar .NET expresiones de acceso a archivos. Dado que las rutas de acceso reales a almacenamiento interno y externo pueden variar de un dispositivo a otro o desde la versión de Android para la versión de Android, no se recomienda codificar de forma rígida la ruta de acceso a los archivos. En su lugar, utilice las APIs Xamarin.Android para determinar la ruta de acceso a archivos. De este modo, las API de .NET para leer y escribir archivos expone las API nativas de Android que le ayudarán a determinar la ruta de acceso a los archivos de almacenamiento interno y externo.

Antes de tratar las API implicadas con acceso de archivo, es importante comprender algunos de los detalles relacionados con el almacenamiento interno y externo. Esto se explicará en la sección siguiente.

## <a name="internal-vs-external-storage"></a>Almacenamiento externo de vs interno

Conceptualmente, el almacenamiento interno y externo son muy similares &ndash; son ambos lugares en que una aplicación de Xamarin.Android puede guardar archivos. Esta similitud puede resultar confusa para los desarrolladores que no están familiarizados con Android ya no está claro cuando una aplicación debe usar el almacenamiento externo de vs de almacenamiento interno.

Almacenamiento interno que hace referencia a la memoria no volátil Android asigna al sistema operativo, el Apk y para las aplicaciones individuales. Este espacio no es accesible, excepto por el sistema operativo o las aplicaciones. Android asignará un directorio en la partición del almacenamiento interno para cada aplicación. Cuando se desinstala la aplicación, también se eliminarán todos los archivos que se mantienen en almacenamiento interno en ese directorio. Almacenamiento interno es ideal para los archivos que solo son accesibles para la aplicación y que no se comparta con otras aplicaciones o tendrá muy poco valor una vez que la aplicación se desinstala. En Android 6.0 o posterior, copia archivos de almacenamiento interno pueden ser automáticamente mediante el uso de Google el [característica de copia de seguridad automática de Android 6.0](https://developer.android.com/guide/topics/data/autobackup). Almacenamiento interno tiene las siguientes desventajas:

* No se puede compartir archivos.
* Los archivos se eliminarán cuando se desinstala la aplicación.
* El espacio disponible en almacenamiento interno quizás limitado.

Almacenamiento externo hace referencia al almacenamiento de archivos que no es de almacenamiento interno y no está accesible a una aplicación de forma exclusiva. Es el propósito principal de almacenamiento externo proporcionar un lugar para colocar los archivos que están diseñados para compartirse entre aplicaciones o que son demasiado grandes para caber en el almacenamiento interno. La ventaja de almacenamiento externo es que normalmente tiene mucho más espacio para los archivos que el almacenamiento interno. Sin embargo, el almacenamiento externo no se garantiza que siempre estén presentes en un dispositivo y puede requerir un permiso especial del usuario para acceder a él.

> [!NOTE]
> Para los dispositivos que admiten varios usuarios, Android proporciona a cada usuario su propio directorio de almacenamiento interno y externo. Este directorio no es accesible a otros usuarios en el dispositivo. Esta separación es invisible para las aplicaciones siempre y cuando lo hacen no codificar las rutas de acceso a los archivos de almacenamiento interno o externo.

Como regla general, las aplicaciones de Xamarin.Android deben prefiere guardar los archivos en almacenamiento interno cuando resulta razonable y se basan en un almacenamiento externo cuando es necesario compartir con otras aplicaciones, son muy grandes o deberían conservarse, incluso si la aplicación se desinstala los archivos. Por ejemplo, un archivo de configuración es más adecuado para un almacenamiento interno porque no tiene ninguna importancia, excepto para la aplicación que lo crea.  En cambio, las fotos son buenos candidatos para el almacenamiento externo. Pueden ser muy grandes y en muchos casos el usuario desee compartirlos o acceder a ellos incluso si se desinstala la aplicación.

Esta guía se centra en el almacenamiento interno. Consulte la guía [almacenamiento externo](~/android/platform/files/external-storage.md) para obtener más información sobre el uso de almacenamiento externo en una aplicación de Xamarin.Android.

## <a name="working-with-internal-storage"></a>Trabajar con el almacenamiento interno

El directorio de almacenamiento interno para una aplicación viene determinada por el sistema operativo y se expone a aplicaciones de Android mediante el `Android.Content.Context.FilesDir` propiedad. Esto devolverá un `Java.IO.File` objeto que representa el directorio que ha dedicado Android exclusivamente para la aplicación.  Por ejemplo, una aplicación con el nombre del paquete **com.companyname** el directorio de almacenamiento interno podría ser:

```bash
/data/user/0/com.companyname/files
```

Este documento se hará referencia en el directorio de almacenamiento interno como _interno\_almacenamiento_.

> [!IMPORTANT]
> La ruta de acceso exacta en el directorio de almacenamiento interno puede variar de dispositivo a dispositivo y entre las versiones de Android. Por este motivo, las aplicaciones deben no estricta la ruta de acceso en el directorio de almacenamiento interno de los archivos de código y en su lugar, use las APIs Xamarin.Android, como `System.Environment.GetFolderPath()`.

Para maximizar el uso compartido de código, deben usar las aplicaciones de Xamarin.Android (o aplicaciones de Xamarin.Forms para Xamarin.Android) la [ `System.Environment.GetFolderPath()` ](xref:System.Environment.GetFolderPath*) método. En Xamarin.Android, este método devolverá una cadena para un directorio que es la misma ubicación que `Android.Content.Context.FilesDir`. Este método toma una enumeración, `System.Environment.SpecialFolder`, que se usa para identificar un conjunto de constantes enumeradas que representan las rutas de acceso de carpetas especiales usadas por el sistema operativo. No todos los `System.Environment.SpecialFolder` asignarán valores a un directorio válido en Xamarin.Android. La siguiente tabla describe qué ruta de acceso se puede esperar un determinado valor de `System.Environment.SpecialFolder`:

| System.Environment.SpecialFolder | Ruta de acceso  |
|----------------------|---|
| `ApplicationData` | **_INTERNO\_almacenamiento_/.config** |
| `Desktop` | **_INTERNO\_almacenamiento_  /escritorio** |
| `LocalApplicationData` | **_INTERNO\_almacenamiento_/.local/share** |
| `MyComputer` | **_INTERNO\_almacenamiento_/.local/share** |
| `MyDocuments` | **_INTERNO\_ALMACENAMIENTO_** |
| `MyMusic` | **_INTERNO\_almacenamiento_/Music** |
| `MyPictures` | **_INTERNO\_almacenamiento_/Music** |
| `MyVideos` | **_INTERNO\_almacenamiento_/Videos** |
| `Personal` | **_INTERNO\_ALMACENAMIENTO_** |


### <a name="reading-or-writing-to-files-on-internal-storage"></a>Leer o escribir en archivos en almacenamiento interno

Cualquiera de los [ C# API para escribir en él](https://docs.microsoft.com/dotnet/csharp/programming-guide/file-system/how-to-write-to-a-text-file) a un archivo son suficientes; todo lo necesario es obtener la ruta de acceso al archivo que se encuentra en el directorio asignado a la aplicación. Se recomienda encarecidamente que async se usan versiones de las API de .NET para minimizar los problemas que pueden asociar a bloquear el subproceso principal de acceso de archivo.

Este fragmento de código es un ejemplo de escritura de un entero en un archivo de texto UTF-8 en el directorio de almacenamiento interno de una aplicación:

```csharp
public async Task SaveCountAsync(int count)
{
    var backingFile = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), "count.txt");
    using (var writer = File.CreateText(backingFile))
    {
        await writer.WriteLineAsync(count.ToString());
    }
}
```

El siguiente fragmento de código proporciona una manera de leer un valor entero que se almacenó en un archivo de texto:

```csharp
public async Task<int> ReadCountAsync()
{
    var backingFile = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), "count.txt");

    if (backingFile == null || !File.Exists(backingFile))
    {
        return 0;
    }

    var count = 0;
    using (var reader = new StreamReader(backingFile, true))
    {
        string line;
        while ((line = await reader.ReadLineAsync()) != null)
        {
            if (int.TryParse(line, out var newcount))
            {
                count = newcount;
            }
        }
    }

    return count;
}
```

## <a name="using--xamarinessentials-ndash-file-system-helpers"></a>Usar Xamarin.Essentials &ndash; aplicaciones auxiliares de sistema de archivos

[Xamarin.Essentials](~/essentials/file-system-helpers.md?context=xamarin/android) es un conjunto de API para escribir código compatible entre plataformas. El [aplicaciones auxiliares de sistema de archivos](~/essentials/file-system-helpers.md?context=xamarin/android) es una clase que contiene una serie de aplicaciones auxiliares para simplificar la búsqueda de los directorios de caché y los datos de la aplicación. Este fragmento de código proporciona un ejemplo de cómo encontrar el directorio de almacenamiento interno y el directorio de caché para una aplicación:

```csharp
// Get the path to a file on internal storage
var backingFile = Path.Combine(Xamarin.Essentials.FileSystem.AppDataDirectory, "count.txt");

// Get the path to a file in the cache directory
var cacheFile = Path.Combine(Xamarin.Essentials.FileSystem.CacheDirectory, "count.txt");
```

## <a name="hiding-files-from-the-mediastore"></a>Ocultar archivos desde el `MediaStore`

El `MediaStore` es un componente de Android que recopila metadatos acerca de los archivos multimedia (como vídeos, música, imágenes) en un dispositivo Android. Su objetivo es simplificar el uso compartido de estos archivos en todas las aplicaciones Android en el dispositivo.

Archivos privados no se mostrarán como un medio que se pueda compartir. Por ejemplo, si una aplicación guarda una imagen para su almacenamiento externo privada, a continuación, ese archivo no seleccionará el analizador de medios (`MediaStore`).

Archivos públicos se recogerán mediante `MediaStore`. Directorios que tienen un nombre de archivo cero bytes **. NOMEDIA** no se examinarán por `MediaStore`.

## <a name="related-links"></a>Vínculos relacionados

* [Almacenamiento externo](~/android/platform/files/external-storage.md)
* [Guardar archivos en el almacenamiento del dispositivo](https://developer.android.com/training/data-storage/files)
* [Aplicaciones auxiliares de sistema de archivos Xamarin.Essentials](~/essentials/file-system-helpers.md?context=xamarin/android)
* [Datos de usuario de copia de seguridad con copia de seguridad automática](https://developer.android.com/guide/topics/data/autobackup)
* [Almacenamiento de forma](https://source.android.com/devices/storage/adoptable)
