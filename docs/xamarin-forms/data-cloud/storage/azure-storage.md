---
title: Almacenar y obtener acceso a datos en el almacenamiento de Azure
description: Almacenamiento de Azure es una solución de almacenamiento en nube escalables que puede usarse para almacenar datos estructurados y no estructurados. En este artículo se muestra cómo usar Xamarin.Forms para almacenar datos binarios y texto en el almacenamiento de Azure y cómo obtener acceso a los datos.
ms.prod: xamarin
ms.assetid: 5B10D37B-839B-4CD0-9C65-91014A93F3EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 63afeec81eff350b034e8dd3a13da52801937826
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30789877"
---
# <a name="storing-and-accessing-data-in-azure-storage"></a>Almacenar y obtener acceso a datos en el almacenamiento de Azure

_Almacenamiento de Azure es una solución de almacenamiento en nube escalables que puede usarse para almacenar datos estructurados y no estructurados. En este artículo se muestra cómo usar Xamarin.Forms para almacenar datos binarios y texto en el almacenamiento de Azure y cómo obtener acceso a los datos._

## <a name="overview"></a>Información general

Almacenamiento de Azure proporciona cuatro servicios de almacenamiento:

- Almacenamiento de blobs. Un blob puede ser texto o datos binarios, como las copias de seguridad, las máquinas virtuales, archivos multimedia o documentos.
- Almacenamiento de tabla es un almacén de atributo de clave NoSQL.
- Almacenamiento de cola es un servicio de mensajería para el procesamiento de flujo de trabajo y la comunicación entre servicios en la nube.
- Almacenamiento de archivos proporciona almacenamiento compartido mediante el protocolo SMB.

Hay dos tipos de cuentas de almacenamiento:

- Una cuenta de almacenamiento general proporciona acceso a los servicios de almacenamiento de Azure desde una sola cuenta.
- Una cuenta de almacenamiento de Blob es una cuenta de almacenamiento especializado para almacenar objetos BLOB. Este tipo de cuenta se recomienda cuando necesite almacenar los datos blob.

En este artículo y que acompaña a la aplicación de ejemplo, se muestra cómo cargar archivos de imagen y texto para el almacenamiento de blobs y descargarlos. Además, también se muestra la recuperación de una lista de archivos de almacenamiento de blobs y eliminar archivos.

Para obtener más información sobre el almacenamiento de Azure, consulte [Introducción al almacenamiento](https://azure.microsoft.com/documentation/articles/storage-introduction/).

## <a name="introduction-to-blob-storage"></a>Introducción al almacenamiento de blobs

Almacenamiento de blobs consta de tres componentes, que se muestran en el diagrama siguiente:

![](azure-storage-images/blob-storage.png "Conceptos de almacenamiento de blobs")

Todo el acceso al almacenamiento de Azure es a través de una cuenta de almacenamiento. Una cuenta de almacenamiento puede contener un número ilimitado de contenedores y un contenedor puede almacenar un número ilimitado de blobs, hasta el límite de capacidad de la cuenta de almacenamiento.

Un blob es un archivo de cualquier tipo y tamaño. Almacenamiento de Azure admite tres tipos de blob distinto:

- Blobs en bloques están optimizados para la transmisión por secuencias y almacenar los objetos en la nube y son una buena elección para almacenar las copias de seguridad, archivos multimedia, documentos etcetera. Los blobs en bloques pueden tener hasta 195Gb de tamaño.
- Anexar blobs son similares a los blobs en bloques, pero están optimizados para las operaciones, como el registro de anexión. Anexar blobs pueden tener hasta 195Gb de tamaño.
- Blobs en páginas están optimizados para operaciones de lectura/escritura frecuentes y normalmente se utilizan para almacenar máquinas virtuales y sus discos. Blobs en páginas pueden ser de hasta 1Tb de tamaño.

> [!NOTE]
> Tenga en cuenta que las cuentas de almacenamiento de blobs admiten bloque y anexar blobs, pero no los blobs de página.

Un blob se carga en el almacenamiento de Azure y descargar desde el almacenamiento de Azure, como una secuencia de bytes. Por lo tanto, los archivos se deben convertir en una secuencia de bytes antes de la carga y de vuelta convertido a su representación original después de la descarga.

Cada objeto que se almacena en el almacenamiento de Azure tiene una dirección URL única. El nombre de cuenta de almacenamiento constituye el subdominio de esa dirección y la combinación de formularios de nombre de dominio y subdominio un *extremo* para la cuenta de almacenamiento. Por ejemplo, si la cuenta de almacenamiento se denomina *mystorageaccount*, el extremo de blob predeterminado para la cuenta de almacenamiento es `https://mystorageaccount.blob.core.windows.net`.

La dirección URL para tener acceso a un objeto en una cuenta de almacenamiento se crea mediante la anexión de la ubicación del objeto en la cuenta de almacenamiento para el punto de conexión. Por ejemplo, una dirección del blob tendrá el formato `https://mystorageaccount.blob.core.windows.net/mycontainer/myblob`.

## <a name="setup"></a>Programa de instalación

El proceso para integrar una cuenta de almacenamiento de Azure en una aplicación de Xamarin.Forms es como sigue:

1. Crear una cuenta de almacenamiento. Para obtener más información, consulte [crear una cuenta de almacenamiento](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account).
1. Agregar el [biblioteca de cliente de almacenamiento de Azure](https://www.nuget.org/packages/WindowsAzure.Storage/) a la aplicación de Xamarin.Forms.
1. Configurar la cadena de conexión de almacenamiento. Para obtener más información, consulte [conectarse al almacenamiento de Azure en](#connecting).
1. Agregar `using` directivas para la `Microsoft.WindowsAzure.Storage` y `Microsoft.WindowsAzure.Storage.Blob` los espacios de nombres para las clases que tendrá acceso a almacenamiento de Azure.

> [!NOTE]
> Aunque este ejemplo utiliza un proyecto de acceso compartido, la biblioteca de cliente de almacenamiento de Azure ahora también admite que se va a usar en un proyecto de biblioteca de clases portables (PCL).

<a name="connecting" />

## <a name="connecting-to-azure-storage"></a>Conectarse al almacenamiento de Azure

Cada solicitud realizada en los recursos de la cuenta de almacenamiento debe autenticarse. Aunque los blobs se pueden configurar para admitir la autenticación anónima, hay dos enfoques principales de que una aplicación puede usar para autenticarse con una cuenta de almacenamiento:

- Clave compartida. Este método usa la clave de nombre y la cuenta de la cuenta de almacenamiento de Azure acceder a los servicios de almacenamiento. Una cuenta de almacenamiento se asigna dos claves privadas de creación que puede usarse para la autenticación de clave compartida.
- Firma de acceso compartido. Se trata de un símbolo (token) que se puede anexar a una dirección URL que permite el acceso delegado a un recurso de almacenamiento, con los permisos especifica, durante el período de tiempo que es válido.

Las cadenas de conexión se pueden especificar que incluyen la información de autenticación necesaria para tener acceso a los recursos de almacenamiento de Azure desde una aplicación. Además, una cadena de conexión puede configurarse para conectarse al emulador de almacenamiento de Azure desde Visual Studio.

> [!NOTE]
> Almacenamiento de Azure es compatible con HTTP y HTTPS en una cadena de conexión. Sin embargo, se recomienda usar HTTPS.

### <a name="connecting-to-the-azure-storage-emulator"></a>Conectar con el emulador de almacenamiento de Azure

El emulador de almacenamiento de Azure proporciona un entorno local que emula los servicios de tabla para fines de desarrollo, cola y blobs de Azure.

La siguiente cadena de conexión se debe usar para conectarse al emulador de almacenamiento de Azure:

```csharp
UseDevelopmentStorage=true
```

Para obtener más información sobre el emulador de almacenamiento de Azure, consulte [usar el emulador de almacenamiento de Azure para desarrollo y pruebas](https://azure.microsoft.com/documentation/articles/storage-use-emulator/).

### <a name="connecting-to-azure-storage-using-a-shared-key"></a>Conectarse al almacenamiento de Azure mediante una clave compartida

El siguiente formato de cadena de conexión debe utilizarse para conectarse al almacenamiento de Azure con una clave compartida:

```csharp
DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey
```

`myAccountName` se debe reemplazar por el nombre de la cuenta de almacenamiento y `myAccountKey` debe ser reemplazada por una de las dos claves de acceso de cuenta.

> [!NOTE]
> Uso compartido cuando la autenticación de clave, el nombre de cuenta y la clave de cuenta se distribuirá a cada persona que usa su aplicación, que proporcionará acceso completo de lectura/escritura a la cuenta de almacenamiento. Por lo tanto, utilice la autenticación de clave compartida solo con fines de prueba y nunca distribuir las claves a otros usuarios.

### <a name="connecting-to-azure-storage-using-a-shared-access-signature"></a>Conectarse con el almacenamiento de Azure mediante una firma de acceso compartido

El siguiente formato de cadena de conexión debe utilizarse para conectarse al almacenamiento de Azure con una SAS:

`BlobEndpoint=myBlobEndpoint;SharedAccessSignature=mySharedAccessSignature`

`myBlobEndpoint` debe reemplazarse por la dirección URL de su extremo de blob, y `mySharedAccessSignature` debe reemplazarse con la SAS. Las asociaciones de seguridad proporcionan el protocolo, el extremo de servicio y las credenciales para tener acceso al recurso.

> [!NOTE]
> Se recomienda la autenticación de SAS para aplicaciones de producción. Sin embargo, en una aplicación de producción las asociaciones de seguridad se deben recuperar de un servicio back-end a petición, en lugar de que se incluye con la aplicación.

Para obtener más información sobre las firmas de acceso compartido, consulte [utilizando firmas de acceso compartido (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).

## <a name="creating-a-container"></a>Crear un contenedor

El `GetContainer` método se utiliza para recuperar una referencia a un contenedor con nombre, que, a continuación, se puede usar para recuperar los blobs del contenedor o agregar blobs al contenedor. El siguiente ejemplo de código muestra la `GetContainer` método:

```csharp
static CloudBlobContainer GetContainer(ContainerType containerType)
{
  var account = CloudStorageAccount.Parse(Constants.StorageConnection);
  var client = account.CreateCloudBlobClient();
  return client.GetContainerReference(containerType.ToString().ToLower());
}
```

El `CloudStorageAccount.Parse` método analiza una cadena de conexión y devuelve un `CloudStorageAccount` instancia que representa la cuenta de almacenamiento. A `CloudBlobClient` , a continuación, se crea la instancia, que se utiliza para recuperar los contenedores y blobs, mediante el `CreateCloudBlobClient` método. El `GetContainerReference` método recupera el contenedor especificado como un `CloudBlobContainer` de instancia, antes de que se devuelva al método de llamada. En este ejemplo, el nombre del contenedor es el `ContainerType` valor de enumeración que se convierte en una cadena en minúsculas.

> [!NOTE]
> Los nombres de contenedor deben estar en minúsculas y deben empezar por una letra o un número. Además, solo puede contener letras, números y guiones y debe estar comprendido entre 3 y 63 caracteres.

El `GetContainer` método se invoca como sigue:

```csharp
var container = GetContainer(containerType);
```

El `CloudBlobContainer` instancia, a continuación, se puede usar para crear un contenedor si ya no existe:

```csharp
await container.CreateIfNotExistsAsync();
```

De forma predeterminada, un contenedor recién creado es privado. Esto significa que se debe especificar una clave de acceso de almacenamiento para recuperar blobs del contenedor. Para obtener información acerca de cómo realizar blobs dentro de un contenedor público, consulte [crear un contenedor](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#create-a-container).

## <a name="uploading-data-to-a-container"></a>Cargar los datos en un contenedor

El `UploadFileAsync` método se usa para cargar un flujo de datos de byte en el almacenamiento de blobs y se muestra en el ejemplo de código siguiente:

```csharp
public static async Task<string> UploadFileAsync(ContainerType containerType, Stream stream)
{
  var container = GetContainer(containerType);
  await container.CreateIfNotExistsAsync();

  var name = Guid.NewGuid().ToString();
  var fileBlob = container.GetBlockBlobReference(name);
  await fileBlob.UploadFromStreamAsync(stream);

  return name;
}
```

Después de recuperar una referencia de contenedor, el método crea el contenedor si ya no existe. Un nuevo `Guid` , a continuación, se crea para que actúe como un nombre de blob único, y se recupera una referencia de bloque de blob como un `CloudBlockBlob` instancia. El flujo de datos, a continuación, se carga en el blob mediante la `UploadFromStreamAsync` método, que crea el blob si aún no existe, o lo sobrescribe si existe.

Antes de poder cargar un archivo con este método de almacenamiento de blobs, primero deben convertirse en una secuencia de bytes. Esto último se muestra en el ejemplo de código siguiente:

```csharp
var byteData = Encoding.UTF8.GetBytes(text);
uploadedFilename = await AzureStorage.UploadFileAsync(ContainerType.Text, new MemoryStream(byteData));
```

El `text` datos se convierten en una matriz de bytes, que, a continuación, se incluye como una secuencia que se pasa a la `UploadFileAsync` método.

## <a name="downloading-data-from-a-container"></a>Descarga de datos de un contenedor

El `GetFileAsync` método se utiliza para descargar datos blob del almacenamiento de Azure y se muestra en el ejemplo de código siguiente:

```csharp
public static async Task<byte[]> GetFileAsync(ContainerType containerType, string name)
{
  var container = GetContainer(containerType);

  var blob = container.GetBlobReference(name);
  if (await blob.ExistsAsync())
  {
    await blob.FetchAttributesAsync();
    byte[] blobBytes = new byte[blob.Properties.Length];

    await blob.DownloadToByteArrayAsync(blobBytes, 0);
    return blobBytes;
  }
  return null;
}
```

Después de recuperar una referencia de contenedor, el método recupera una referencia de los datos almacenados. Si existe el blob, sus propiedades se recuperan mediante la `FetchAttributesAsync` método. Se crea una matriz de bytes del tamaño correcto y se descarga el blob como una matriz de bytes que se devuelve al método de llamada.

Después de descargar los datos de blob de bytes, se debe convertir en su representación original. Esto último se muestra en el ejemplo de código siguiente:

```csharp
var byteData = await AzureStorage.GetFileAsync(ContainerType.Text, uploadedFilename);
string text = Encoding.UTF8.GetString(byteData);
```

Se recupera la matriz de bytes desde el almacenamiento de Azure por la `GetFileAsync` método, antes de que se convierta a un UTF8 cadena codificada.

## <a name="listing-data-in-a-container"></a>Mostrar datos en un contenedor

El `GetFilesListAsync` método se utiliza para recuperar una lista de blobs almacenados en un contenedor y se muestra en el ejemplo de código siguiente:

```csharp
public static async Task<IList<string>> GetFilesListAsync(ContainerType containerType)
{
  var container = GetContainer(containerType);

  var allBlobsList = new List<string>();
  BlobContinuationToken token = null;

  do
  {
    var result = await container.ListBlobsSegmentedAsync(token);
    if (result.Results.Count() > 0)
    {
      var blobs = result.Results.Cast<CloudBlockBlob>().Select(b => b.Name);
      allBlobsList.AddRange(blobs);
    }
    token = result.ContinuationToken;
  } while (token != null);

  return allBlobsList;
}
```

Después de recuperar una referencia de contenedor, el método usa el contenedor `ListBlobsSegmentedAsync` método para recuperar las referencias a los blobs dentro del contenedor. Los resultados devueltos por la `ListBlobsSegmentedAsync` se enumeran método mientras el `BlobContinuationToken` instancia no es `null`. Cada blob se convierte desde el valor devuelto `IListBlobItem` a una `CloudBlockBlob` en pedidos acceso a la `Name` propiedad del blob, antes de que sea el valor se agrega a la `allBlobsList` colección. Una vez el `BlobContinuationToken` instancia es `null`, ha devuelto el último nombre de blob y ejecución sale del bucle.

## <a name="deleting-data-from-a-container"></a>Eliminar datos de un contenedor

El `DeleteFileAsync` método se usa para eliminar un blob de un contenedor y se muestra en el ejemplo de código siguiente:

```csharp
public static async Task<bool> DeleteFileAsync(ContainerType containerType, string name)
{
  var container = GetContainer(containerType);
  var blob = container.GetBlobReference(name);
  return await blob.DeleteIfExistsAsync();
}
```

Después de recuperar una referencia de contenedor, el método recupera una referencia de blob para el blob especificado. A continuación, se elimina el blob con el `DeleteIfExistsAsync` método.

## <a name="summary"></a>Resumen

Este artículo muestra cómo usar Xamarin.Forms para almacenar datos binarios y texto en el almacenamiento de Azure y cómo obtener acceso a los datos. Almacenamiento de Azure es una solución de almacenamiento en nube escalables que puede utilizarse para almacenar datos estructurados y no estructurados.


## <a name="related-links"></a>Vínculos relacionados

- [Almacenamiento de Azure (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureStorage/)
- [Introducción al almacenamiento](https://azure.microsoft.com/documentation/articles/storage-introduction/)
- [Introducción al almacenamiento de blobs de Xamarin](https://azure.microsoft.com/documentation/articles/storage-xamarin-blob-storage/)
- [Uso de firmas de acceso compartido (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)
- [Windows almacenamiento de Azure](https://www.nuget.org/packages/WindowsAzure.Storage/)
