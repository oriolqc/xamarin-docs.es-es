---
title: Acceso a archivos en un almacenamiento externo con Xamarin.Android
description: Esta guía describirá el acceso a archivos en un almacenamiento externo en Xamarin.Android
ms.prod: xamarin
ms.assetid: 40da10b2-a207-4f9c-a2dd-165d9b662f33
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/23/2018
ms.openlocfilehash: 78051fce44239eea86948988a4d19ac37c5ea0d5
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60953243"
---
# <a name="external-storage"></a>Almacenamiento externo

Almacenamiento externo hace referencia al almacenamiento de archivos que no en el almacenamiento interno y no exclusivamente accesible para la aplicación que se encarga del archivo. Es el propósito principal de almacenamiento externo proporcionar un lugar para colocar los archivos que están diseñados para compartirse entre aplicaciones o que son demasiado grandes para caber en el almacenamiento interno.

Históricamente en términos de almacenamiento externo hace referencia a una partición de disco en un medio extraíble, como una tarjeta SD (también conocido como _almacenamiento portátil_). Esta distinción ya no es tan relevante como dispositivos Android han evolucionado y muchos dispositivos Android ya no admiten el almacenamiento extraíble. En su lugar, algunos dispositivos asignará algunas de su memoria no volátil interna que Android para llevar a cabo los mismos medios extraíbles de función. Esto se conoce como _emulado_ almacenamiento y todavía se considera un almacenamiento externo. Como alternativa, algunos dispositivos Android pueden tener varias particiones de almacenamiento externo. Por ejemplo, un Android Tablet PC (además de su almacenamiento interno) es posible que haya emula almacenamiento y ranuras de uno o más de una tarjeta SD. Android todas estas particiones se tratan como almacenamiento externo.

En los dispositivos que tienen varios usuarios, cada usuario tendrá un directorio dedicado en la partición principal de almacenamiento externo para su almacenamiento externo. Aplicaciones que se ejecutan como un usuario no tendrá acceso a los archivos de otro usuario en el dispositivo. Los archivos de todos los usuarios están todavía de lectura y escritura mundo; Sin embargo, Android superará el espacio aislado de cada perfil de usuario de los demás.

Leer y escribir en archivos son casi idéntico en Xamarin.Android y sirve para cualquier otra aplicación. NET. La aplicación de Xamarin.Android determina la ruta de acceso al archivo que se manipulará, a continuación, usa estándar .NET expresiones de acceso a archivos. Dado que las rutas de acceso reales a almacenamiento interno y externo pueden variar de un dispositivo a otro o desde la versión de Android para la versión de Android, no se recomienda codificar de forma rígida la ruta de acceso a los archivos. En su lugar, Xamarin.Android expone las API nativas de Android que le ayudarán a determinar la ruta de acceso a los archivos de almacenamiento interno y externo.

Esta guía tratan los conceptos y las API de Android que son específicos de almacenamiento externo.

## <a name="public-and-private-files-on-external-storage"></a>Archivos pública y privada en un almacenamiento externo

Hay dos tipos diferentes de los archivos que es posible que tenga una aplicación en un almacenamiento externo:

* **Privada** archivos &ndash; archivos privados son archivos que son específicos de la aplicación (pero son todavía de lectura y escritura). Android espera que los archivos privados se almacenan en un directorio específico en un almacenamiento externo. Aunque los archivos se denominan "privados", son aún visibles y accesibles para otras aplicaciones en el dispositivo, no ofrezcan una protección especial por Android.

* **Pública** archivos &ndash; son archivos que no se consideran específico para la aplicación y están diseñados para compartirse libremente.

Las diferencias entre estos archivos sea principalmente conceptual. Archivos privados son privados en el sentido de que se consideran como parte de la aplicación, mientras que los archivos públicos son los otros archivos que existen en un almacenamiento externo. Android proporciona dos API distintas para resolver las rutas de acceso a los archivos públicos y privados, pero en caso contrario, se usan las mismas API de .NET para leer y escribir en estos archivos. Estas son las mismas API que se describen en la sección en [leer y escribir](~/android/platform/files/index.md#reading-or-writing-to-files-on-internal-storage).

### <a name="private-external-files"></a>Archivos externos privados

Privada archivos externos se consideran específicos para una aplicación (similar a los archivos internos), pero se están realizando en un almacenamiento externo para una serie de motivos (por ejemplo, era demasiado grande para el almacenamiento interno). Al igual que los archivos internos, estos archivos se eliminarán cuando se desinstala la aplicación por el usuario.

Se encuentra la ubicación principal para archivos externos privadas llamando al método `Android.Content.Context.GetExternalFilesDir(string type)`. Este método devolverá un `Java.IO.File` objeto que representa el directorio de almacenamiento externa privada para la aplicación. Pasar `null` a este método devolverá la ruta de acceso al directorio de almacenamiento del usuario para la aplicación. Por ejemplo, para una aplicación con el nombre del paquete `com.companyname.app`, sería el directorio "raíz" de los archivos externos privados:

```bash
/storage/emulated/0/Android/data/com.companyname.app/files/
```

Este documento se hará referencia en el directorio de almacenamiento para archivos privados en un almacenamiento externo como _privada\_externo\_almacenamiento_.


El parámetro `GetExternalFilesDir()` es una cadena que especifica un _directorio de la aplicación_. Este es un directorio diseñado para proporcionar una ubicación estándar para una organización lógica de archivos. Los valores de cadena están disponibles a través de las constantes en el `Android.OS.Environment` clase:

| `Android.OS.Environment` | Directorio |
|-|-|
| DirectoryAlarms | **_PRIVATE\_EXTERNAL\_STORAGE_/Alarms** |
| DirectoryDcim | **_PRIVATE\_EXTERNAL\_STORAGE_/DCIM** |
| DirectoryDownloads | **_PRIVATE\_EXTERNAL\_STORAGE_/Download** |
| DirectoryDocuments | **_PRIVATE\_EXTERNAL\_STORAGE_/Documents** |
| DirectoryMovies | **_PRIVATE\_EXTERNAL\_STORAGE_/Movies** |
| DirectoryMusic | **_PRIVATE\_EXTERNAL\_STORAGE_/Music** |
| DirectoryNotifications | **_PRIVATE\_EXTERNAL\_STORAGE_/Notifications** |
| DirectoryPodcasts | **_PRIVATE\_EXTERNAL\_STORAGE_/Podcasts** |
| DirectoryRingtones | **_PRIVATE\_EXTERNAL\_STORAGE_/Ringtones** |
| DirectoryPictures | **_PRIVATE\_EXTERNAL\_STORAGE_/Pictures** |

Para dispositivos que tienen varias particiones de almacenamiento externo, cada partición tendrá un directorio que está pensado para archivos privados. El método `Android.Content.Context.GetExternalFilesDirs(string type)` devolverá una matriz de `Java.IO.Files`. Cada objeto representa un directorio específico de la aplicación privado en todos los dispositivos de almacenamiento compartido y externos en la aplicación puede colocar los archivos posee.

> [!IMPORTANT]
> La ruta de acceso exacta en el directorio de almacenamiento externa privada puede variar de dispositivo a dispositivo y entre las versiones de Android. Por este motivo, las aplicaciones deben duro no la ruta de acceso a este directorio de código y en su lugar, use las APIs Xamarin.Android, como `Android.Content.Context.GetExternalFilesDir()`.

### <a name="public-external-files"></a>Archivos externos públicos

Archivos públicos son archivos que existen en un almacenamiento externo que no se almacenan en el directorio de Android que se asigna para archivos privados. Archivos públicos no se eliminarán cuando se desinstala la aplicación. Aplicaciones de Android deben tener el permiso antes de que pueden leer o escribir los archivos públicos. Es posible que los archivos públicos que exista en cualquier lugar en un almacenamiento externo, pero por convención Android espera archivos públicos que existe en el directorio identificado por la propiedad `Android.OS.Environment.ExternalStorageDirectory`. Esta propiedad devolverá un `Java.IO.File` objeto que representa el directorio principal de almacenamiento externo. Por ejemplo, `Android.OS.Environment.ExternalStorageDirectory` puede hacer referencia al directorio siguiente:

```bash
/storage/emulated/0/
```

Este documento se hará referencia en el directorio de almacenamiento para archivos públicos en un almacenamiento externo como _pública\_externo\_almacenamiento_.


Android admite también el concepto de directorios de la aplicación en _pública\_externo\_almacenamiento_. Estos directorios son exactamente iguales que los directorios de aplicación para `_PRIVATE\_EXTERNAL\_STORAGE_` y se describen en la tabla en la sección anterior. El método `Android.OS.Environment.GetExternalStoragePublicDirectory(string directoryType)` devolverá un `Java.IO.File` objetos que corresponden a un directorio de aplicaciones pública. El `directoryType` parámetro es un parámetro obligatorio y no puede ser `null`.

Por ejemplo, al llamar a `Environment.GetExternalStoragePublicDirectory(Environment.DirectoryDocuments).AbsolutePath` devolverá una cadena que será similar:

```bash
/storage/emulated/0/Documents
```

> [!IMPORTANT]
> La ruta de acceso exacta en el directorio de almacenamiento externo público puede variar de dispositivo a dispositivo y entre las versiones de Android. Por este motivo, las aplicaciones deben duro no la ruta de acceso a este directorio de código y en su lugar, use las APIs Xamarin.Android, como `Android.OS.Environment.ExternalStorageDirectory`.

## <a name="working-with-external-storage"></a>Trabajar con almacenamiento externo

Una vez que una aplicación de Xamarin.Android ha obtenido la ruta de acceso completa a un archivo, debe utilizar cualquiera de las API estándar de .NET para crear, leer, escribir o eliminar archivos. Esto maximiza la cantidad de entre el código de plataforma compatible para una aplicación. Sin embargo, antes de intentar tener acceso a un archivo de una aplicación de Xamarin.Android debe asegurarse de que es posible tener acceso a ese archivo.

1. **Comprobar el almacenamiento externo** &ndash; según la naturaleza del almacenamiento externo, es posible que no se puede montar y puede usar la aplicación. Todas las aplicaciones deben comprobar el estado de almacenamiento externo antes de intentar utilizarlo.
2. **Realizar una comprobación de permisos en tiempo de ejecución** &ndash; An Android app debe solicitar permiso al usuario para tener acceso a almacenamiento externo. Esto significa que una solicitud de permiso debe realizarse antes de cualquier acceso a los archivos de tiempo de ejecución. La Guía de [permisos en Xamarin.Android](~/android/app-fundamentals/permissions.md) contiene más detalles sobre los permisos de Android.

Cada una de estas dos tareas se describen a continuación.

### <a name="verifying-that-external-storage-is-available"></a>Comprobando que el almacenamiento externo está disponible

Es el primer paso antes de escribir en un almacenamiento externo comprobar que es leer o escribir. El `Android.OS.Environment.ExternalStorageState` propiedad contiene una cadena que identifica el estado de almacenamiento externo. Esta propiedad devolverá una cadena que representa el estado. Esta tabla es una lista de los `ExternalStorageState` valores que pueden devolver `Environment.ExternalStorageState`:

| ExternalStorageState | Descripción  |
|----------------------|---|
| MediaBadRemoval      | Los medios repentinamente se haya quitado sin que se desmontó correctamente. |
| MediaChecking        | El medio está presente pero está llevando a cabo un disco a comprobar.  |
| MediaEjecting        | Medio está en el proceso que se va a desmontar y expulsado.  |
| MediaMounted         | Media está montado y se puede leer o escribir en.  |
| MediaMountedReadOnly | Media está montado, pero solo se puede leer desde. |
| MediaNofs            | Media está presente pero no contiene un sistema de archivos adecuado para Android. |
| MediaRemoved         | No hay ningún medio presente. |
| MediaShared          | Media está presente, pero no está montada. Que se comparten a través de USB con otro dispositivo.|
| MediaUnknown         | Android no reconoce el estado de los medios. |
| MediaUnmountable     | El medio está presente pero no se puede montar en Android. |
| MediaUnmounted       | El medio está presente pero no está montado. |


La mayoría de las aplicaciones Android solo necesitará comprobar si se monta almacenamiento externo. El fragmento de código siguiente muestra cómo comprobar que el almacenamiento externo está montado para acceso de solo lectura o acceso de lectura y escritura:

```csharp
bool isReadonly = Environment.MediaMountedReadOnly.Equals(Environment.ExternalStorageState);
bool isWriteable = Environment.MediaMounted.Equals(Environment.ExternalStorageState);
```

## <a name="external-storage-permissions"></a>Permisos de almacenamiento externo

Android tiene en cuenta al acceder al almacenamiento externo para que sea un _permiso peligroso_, que normalmente requiere que el usuario conceder su permiso para acceder al recurso. El usuario puede revocar este permiso en cualquier momento.  Esto significa que una solicitud de permiso debe realizarse antes de cualquier acceso a los archivos de tiempo de ejecución. Las aplicaciones se conceden automáticamente permisos para leer y escribir sus propios archivos privados. Es posible que las aplicaciones leer y escribir los archivos privados que pertenecen a otras aplicaciones después de que se va a [permiso](~/android/app-fundamentals/permissions.md) por el usuario.

Todas las aplicaciones de Android deben declarar uno de los dos permisos para el almacenamiento externo en el **AndroidManifest.xml** . Para identificar los permisos, uno de los dos siguientes `uses-permission` se debe agregar elementos a **AndroidManifest.xml**:

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

> [!NOTE]
> Si el usuario concede `WRITE_EXTERNAL_STORAGE`, a continuación, `READ_EXTERNAL_STORAGE` también es implícitamente concedido. No es necesario solicitar ambos permisos en **AndroidManifest.xml**.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

También se pueden agregar los permisos mediante la **manifiesto de Android** pestaña de la **propiedades de la solución**:

![Explorador de soluciones - permisos necesarios para Visual Studio](./images/required-permissions.w157.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

También se pueden agregar los permisos mediante la **manifiesto de Android** pestaña de la **panel de propiedades de solución**:

[![Panel de solución, los permisos necesarios para Visual Studio para Mac](./images/required-permissions.m752-sml.png)](./images/required-permissions.m752.png#lightbox)

-----

Por lo general, todos los permisos peligrosos deben ser aprobados por el usuario. Los permisos para el almacenamiento externo son una anomalía en que existen excepciones a esta regla, según la versión de Android que se ejecuta la aplicación:

![Diagrama de flujo de las comprobaciones de permisos de almacenamiento externo](./images/external-permission-check-flowchart.png)

Para obtener más información sobre la realización de las solicitudes de permiso en tiempo de ejecución, consulte la guía [permisos en Xamarin.Android](~/android/app-fundamentals/permissions.md). El **monodroid ejemplo** [archivosLocales](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles) también muestra una manera de llevar a cabo comprobaciones de permisos en tiempo de ejecución.

#### <a name="granting-and-revoking-permissions-with-adb"></a>Conceder y revocar permisos con ADB

Durante el desarrollo de una aplicación de Android, puede ser necesario conceder y revocar permisos a los distintos flujos de trabajo relacionados con las comprobaciones de permisos en tiempo de ejecución de pruebas. Es posible hacer esto en el símbolo del sistema ADB de uso. Los siguientes fragmentos de línea de comandos muestran cómo conceder o revocar permisos con ADB para una aplicación Android cuyo nombre del paquete es **com.companyname.app**:

```bash
$ adb shell pm grant com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE

$ adb shell pm revoke com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE
```

## <a name="deleting-files"></a>Eliminación de archivos

Cualquiera de las API de C# puede usarse para eliminar un archivo de almacenamiento externo, como el estándar de [ `System.IO.File.Delete` ](xref:System.IO.File.Delete*). También es posible usar las API de Java a costa de la portabilidad del código. Por ejemplo:

```csharp
System.IO.File.Delete("/storage/emulated/0/Android/data/com.companyname.app/files/count.txt");
```

## <a name="related-links"></a>Vínculos relacionados

* [Archivos locales de Xamarin.Android de ejemplo en **monodroid-samples**](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles)
* [Permisos de Xamarin.Android](~/android/app-fundamentals/permissions.md)
