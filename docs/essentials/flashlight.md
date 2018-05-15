---
title: Linterna Xamarin.Essentials
description: La clase linterna tiene la capacidad para activar o desactivar la cámara del dispositivo flash para convertirlos en una linterna.
ms.assetid: 06A03553-D212-43A2-9E6E-C2D2D93EB136
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 0fe07f60502b27322db9486f3382b8f67d50945b
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
---
# <a name="xamarinessentials-flashlight"></a>Linterna Xamarin.Essentials

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **linterna** clase tiene la capacidad para activar o desactivar la cámara del dispositivo flash para convertirlos en una linterna.

## <a name="getting-started"></a>Introducción

Para tener acceso a la **linterna** se requiere la siguiente configuración específica de plataforma de funcionalidad.

# <a name="androidtabandroid"></a>[Android](#tab/android)

Los permisos linterna y cámara son necesarios y deben configurarse en el proyecto de Android. Esto puede agregarse de las maneras siguientes:

Abra la **AssemblyInfo.cs** de archivos en el **propiedades** carpeta y agregar:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Flashlight)]
[assembly: UsesPermission(Android.Manifest.Permission.Camera)]
```

O bien, actualice el manifiesto de Android:

Abra la **AndroidManifest.xml** de archivos en el **propiedades** carpeta y agregue lo siguiente dentro de la **manifiesto** nodo.

```xml
<uses-permission android:name="android.permission.FLASHLIGHT" />
<uses-permission android:name="android.permission.CAMERA" />
```

O haga clic con el botón secundario en el proyecto de Android y abra las propiedades del proyecto. En **manifiesto Android** buscar el **los permisos necesarios:** área y compruebe el **LINTERNA** y **cámara** permisos. Esto actualizará automáticamente el **AndroidManifest.xml** archivo.

Mediante la adición de estos permisos [Google Play automáticamente filtrará los dispositivos](http://developer.android.com/guide/topics/manifest/uses-feature-element.html#permissions-features) sin necesidad de hardware específico. Puede obtener resolver este problema debe agregar lo siguiente al archivo AssemblyInfo.cs en el proyecto de Android:

```csharp
[assembly: UsesFeature("android.hardware.camera", Required = false)]
[assembly: UsesFeature("android.hardware.camera.autofocus", Required = false)]
```

# <a name="iostabios"></a>[iOS](#tab/ios)

No se necesita ninguna instalación adicional.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

No se necesita ninguna instalación adicional.

-----

## <a name="using-flashlight"></a>Usar linterna

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

El linterna puede activar y desactivar a través de la `TurnOnAsync` y `TurnOffAsync` métodos:

```csharp
try
{
    // Turn On
    await Flashlight.TurnOnAsync();

    // Turn Off
    await Flashlight.TurnOffAsync();
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to turn on/off flashlight
}
```

## <a name="platform-implementation-specifics"></a>Detalles de implementación de plataforma

### <a name="androidtabandroid-specifics"></a>[Android](#tab/android-specifics)

La clase linterna ha sido optmized en función de sistema operativo del dispositivo.

#### <a name="api-level-23-and-higher"></a>Nivel de API 23 y versiones posteriores

En los niveles de API más recientes, [antorcha modo](https://developer.android.com/reference/android/hardware/camera2/CameraManager.html#setTorchMode) se usará para activar o desactivar la unidad flash del dispositivo.

#### <a name="api-level-22-and-lower"></a>Nivel de API 22 e inferior

Se crea una textura de la superficie de cámara para activar o desactivar el `FlashMode` de la unidad de la cámara. 

### <a name="iostabios-specifics"></a>[iOS](#tab/ios-specifics)

[AVCaptureDevice](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureDevice/) se utiliza para activar y desactivar la antorcha y modo de Flash del dispositivo.

### <a name="uwptabuwp-specifics"></a>[UWP](#tab/uwp-specifics)

[Luz](https://docs.microsoft.com/en-us/uwp/api/windows.devices.lights.lamp) se utiliza para detectar la luz en la parte posterior del dispositivo para activar o desactivar la primera.

-----

## <a name="api"></a>API

- [Código fuente de linterna](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Flashlight)
- [Documentación de API de linterna](xref:Xamarin.Essentials.Flashlight)
