---
title: Compresión de archivos en Xamarin.iOS
description: Este documento describe cómo trabajar con la API libcompression en Xamarin.iOS. Describe desinflándose, lo que infla, y admiten los distintos algoritmos.
ms.prod: xamarin
ms.assetid: 94D05DAB-01E8-4C62-9CEF-9D6417EEA8EB
ms.technology: xamarin-ios
author: mandel-macaque
ms.author: mandel
ms.date: 03/04/2019
ms.openlocfilehash: f7a1df65047fd8040dd40e9f7f057d6bfe6dea61
ms.sourcegitcommit: 4c97f5d73be7eb2da153a85183be4258b6b11ca6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/20/2019
ms.locfileid: "58290934"
---
# <a name="file-compression-in-xamarinios"></a>Compresión de archivos en Xamarin.iOS

Las aplicaciones de Xamarin que tienen como destino iOS 9.0 o macOS 10.11 (y versiones posteriores) pueden usar el _Framework compresión_ para comprimir (codificar) y descomprimir (descodificación) datos. Xamarin.iOS proporciona este marco de trabajo después de la API de Stream. El marco de compresión permite a los programadores interactuar con el comprimir y descomprime los datos como si fueran secuencias normales sin necesidad de utilizar las devoluciones de llamada o delegados.

El marco de trabajo de compresión proporciona compatibilidad para los algoritmos siguientes:

* LZ4
* LZ4 sin procesar
* Lzfse
* Lzma
* Zlib

Mediante el marco de compresión permite a los desarrolladores realizar las operaciones de compresión sin necesidad de bibliotecas de terceros o paquetes NuGet. Esto reduce las dependencias externas y garantiza que las operaciones de compresión se admitirá en todas las plataformas (siempre y cuando cumplan los requisitos mínimos del sistema operativo).

## <a name="general-file-decompression"></a>Descompresión de archivos generales

El marco de compresión utiliza una secuencia de la API de Xamarin.iOS y Xamarin.Mac. Para comprimir los datos, el programador puede utilizar los patrones normales que se usan en otras API de E/S dentro de .NET significa que esta API. El ejemplo siguiente muestra cómo descomprimir los datos con el marco de compresión, que es similar a la API se encuentra en la `System.IO.Compression.DeflateStream` API:

```csharp
// sample zlib data
static byte [] compressed_data = { 0xf3, 0x48, 0xcd, 0xc9, 0xc9, 0xe7, 0x02, 0x00 };

using (var backing = new MemoryStream (compressed_data)) // compress data to read
using (var decompressing = new CompressionStream (backing, CompressionMode.Decompress, CompressionAlgorithm.Zlib)) // create decompression stream with the correct algorithm
using (var reader = new StreamReader (decompressing))
{
    // perform the required stream operations
    Console.WriteLine (reader.ReadLine ());
}
```

El `CompressionStream` implementa el `IDisposable` interfaz, como otro `System.IO.Streams`, por lo que los desarrolladores deben garantizar que los recursos se liberan una vez que ya no sean necesarios.

## <a name="general-file-compression"></a>Compresión de archivos generales

La API de compresión también permite a los desarrolladores comprimir los datos siguiendo las mismas API. Se pueden comprimir datos con uno de los algoritmos proporcionados se indica en la `CompressionAlgorithm` enumerador.

```csharp
// sample method that copies the data from the source stream to the destination stream
static void CopyStream (Stream src, Stream dest)
{
    byte[] array = new byte[1024];
    int bytes_read;
    bytes_read = src.Read (array, 0, 1024);
    while (bytes_read != 0) {
        dest.Write (array, 0, bytes_read);
        bytes_read = src.Read (array, 0, 1024);
    }
}

static void CompressExample ()
{
    // create some sample data to compress
    byte [] data = new byte[100000];
    for (int i = 0; i < 100000; i++) {
        data[i] = (byte) i;
    }

    using (var dataStream = new MemoryStream (data)) // stream that contains the data to compress
    using (var backing = new MemoryStream ()) // stream in which the compress data will be written
    using (var compressing = new CompressionStream (backing, CompressionMode.Compress, CompressionAlgorithm.Zlib, true))
    {
        // copy the data to the compressing stream
        CopyStream (dataStream, compressing);
        // at this point, the CompressionStream contains all the data from the dataStream but compressed
        // using the zlib algorithm
    }
```

## <a name="async-support"></a>Compatibilidad con Async

El `CompressionStream` es compatible con todas las operaciones asincrónicas que son compatibles con el `System.IO.DeflateStream`, lo que significa que los desarrolladores pueden usar la palabra clave async para realizar las operaciones de compresión y descompresión sin bloquear el subproceso de interfaz de usuario.