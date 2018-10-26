---
title: ¿Cómo instalar manualmente las bibliotecas de compatibilidad con Android requeridas por los paquetes de Xamarin.Android.Support?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A9CB8CA8-8A6D-405E-B84C-A16CE452C0F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 84ee33fe174c01656144e55bc3cbba7c773950fd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120650"
---
# <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>¿Cómo instalar manualmente las bibliotecas de compatibilidad con Android requeridas por los paquetes de Xamarin.Android.Support?

## <a name="example-steps-for-xamarinandroidsupportv4"></a>Pasos de ejemplo para Xamarin.Android.Support.v4 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Descargue el paquete Xamarin.Android.Support NuGet deseado (por ejemplo, puede instalarlo con el Administrador de paquetes de NuGet).

Use `ildasm` para comprobar qué versión de **android_m2repository.zip** el paquete NuGet necesita:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr SourceUrl
```
Salida del ejemplo:

```cmd
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
```

Descargar **android\_m2repository.zip** de Google con la dirección URL devuelta desde **ildasm**. Como alternativa, puede comprobar qué versión de la _Android Support Repository_ actualmente instalada en Android SDK Manager:

!["Se muestra Android Support Repository versión 32 instalado android SDK Manager"](install-android-support-library-images/sdk-extras.png)

Si la versión coincida con el que necesita para el paquete de NuGet, no debe descargar nada nuevo. Puede en su lugar, volver a comprimir a existente **m2repository** directorio que se encuentra en **extras\\android** en el _ruta de acceso del SDK_ (como se muestra la parte superior de Android Ventana de SDK Manager).

Calcular el hash MD5 de la dirección URL devuelta **ildasm**. Dar formato a la cadena resultante para utilizar todas las letras en mayúsculas y sin espacios en blanco. Por ejemplo, ajustar el `$url` variable como sea necesario y, a continuación, ejecute las siguientes 2 líneas (según [original C# código de Xamarin.Android](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)) en PowerShell:

```powershell
$url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"
(([System.Security.Cryptography.MD5]::Create()).ComputeHash([System.Text.Encoding]::UTF8.GetBytes($url)) | %{ $_.ToString("X02") }) -join ""
```
Salida del ejemplo:

```powershell
F16A3455987DBAE5783F058F19F7FCDF
```

Copia **android\_m2repository.zip** en el **% LOCALAPPDATA %\\Xamarin\\zips\\**  carpeta. Cambie el nombre del archivo para utilizar el hash MD5 del hash MD5 anterior paso de cálculo. Por ejemplo:

**% LOCALAPPDATA %\\Xamarin\\zips\\F16A3455987DBAE5783F058F19F7FCDF.zip**

(Opcional) Descomprima el archivo en **% LOCALAPPDATA %\\Xamarin\\Xamarin.Android.Support.v4\\23.4.0.0\\contenido\\**  (creación de un **contenido\\m2repository** subdirectorio). Si omite este paso, la primera compilación que usa la biblioteca se tardará un poco más tiempo porque necesitará completar este paso.
El número de versión del subdirectorio (**23.4.0.0** en este ejemplo) no es exactamente lo mismo que la versión del paquete NuGet. Puede usar `ildasm` para buscar el número de versión correcto:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr /C:"string 'Version'"
```
Salida del ejemplo:

```cmd
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
```

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Descargue el paquete Xamarin.Android.Support NuGet deseado (por ejemplo, puede instalarlo con el Administrador de paquetes de NuGet).

Haga doble clic en el _Xamarin.Android.Support.v4_ ensamblado bajo el _referencias_ sección del proyecto Android en Visual Studio para Mac abrir el ensamblado en el Explorador de ensamblados. Asegúrese de que el _lenguaje_ desplegable se establece en _C#_ y seleccione el nivel superior _Xamarin.Android.Support.v4_ ensamblado desde el árbol de navegación del explorador de ensamblados . Busque el `SourceUrl` propiedad en uno de los `IncludeAndroidResourcesFrom` o `JavaLibraryReference` atributos:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

Descargar **android\_m2repository.zip** desde Google mediante el `SourceUrl` devuelto desde **ildasm**. Como alternativa, puede comprobar qué versión de la _Android Support Repository_ actualmente instalada en Android SDK Manager:

!["Se muestra Android Support Repository versión 32 instalado android SDK Manager"](install-android-support-library-images/sdk-extras.png)

Si la versión coincida con el que necesita para el paquete de NuGet, no debe descargar nada nuevo. Puede en su lugar, volver a comprimir a existente **m2repository** directorio que se encuentra en **extras/android** en el _ruta de acceso del SDK_ (como se muestra en la parte superior de la ventana del Administrador de SDK de Android) .

Calcular el hash MD5 de la dirección URL devuelta **ildasm**. Dar formato a la cadena resultante para utilizar todas las letras en mayúsculas y sin espacios en blanco. Por ejemplo, ajustar la cadena de dirección URL según sea necesario y, a continuación, ejecute el siguiente comando un **Terminal.app** símbolo del sistema:

```bash
echo -n "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip" | md5 | tr '[:lower:]' '[:upper:]'
```

Otra opción consiste en usar el `csharp` intérprete para ejecutar [el mismo C# código que usa Xamarin.Android propia](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208).
Para ello, ajuste el `url` variable como sea necesario y, a continuación, ejecute el siguiente comando un **Terminal.app** símbolo del sistema:

```bash
csharp -e 'var url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"; string.Concat((System.Security.Cryptography.MD5.Create().ComputeHash(System.Text.Encoding.UTF8.GetBytes(url))).Select(b => b.ToString("X02")))'
```
Salida del ejemplo:

```bash
F16A3455987DBAE5783F058F19F7FCDF
```

Copia **android\_m2repository.zip** a la **$HOME/.local/share/Xamarin/zips/** carpeta. Cambie el nombre del archivo para utilizar el hash MD5 del hash MD5 anterior paso de cálculo. Por ejemplo:

**$HOME/.local/share/Xamarin/zips/F16A3455987DBAE5783F058F19F7FCDF.zip**

(Opcional) Descomprima el archivo en: 

**$HOME/.local/share/Xamarin/Xamarin.Android.Support.v4/23.4.0.0/content/**

(crear un **contenido/m2repository** subdirectorio). Si omite este paso, la primera compilación que usa la biblioteca se tardará un poco más tiempo porque necesitará completar este paso.

El número de versión del subdirectorio (**23.4.0.0** en este ejemplo) no es exactamente lo mismo que la versión del paquete NuGet. Como en el **ildasm** el paso anterior, puede usar el Explorador de ensamblado en Visual Studio para Mac para buscar el número de versión correcto. Busque el `Version` propiedad en uno de los `IncludeAndroidResourcesFrom` o `JavaLibraryReference` atributos:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

-----


## <a name="additional-references"></a>Referencias adicionales

- [Error 43245](https://bugzilla.xamarin.com/show_bug.cgi?id=43245) – inexacto "no se pudo descargar. Descargue {0} y colóquelo la {1} directorio. " y "instale el paquete: '{0}' disponibles en el instalador del SDK de" mensajes de error relacionados con paquetes de Xamarin.Android.Support

### <a name="next-steps"></a>Pasos siguientes

Este documento describe el comportamiento actual a partir de agosto de 2016. La técnica descrita en este documento no forma parte de la serie de pruebas estable para Xamarin, por lo que podría dejar de hacerlo en el futuro.

Para obtener más ayuda, póngase en contacto con nosotros, o si este problema permanece incluso después de la utilización de la información anterior, consulte la sección [qué opciones de soporte técnico están disponibles para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obtener información sobre las opciones de contacto, sugerencias, así como cómo archivar un error nuevo si es necesario.

