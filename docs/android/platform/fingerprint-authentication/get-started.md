---
title: Introducción a la autenticación con huella digital
ms.prod: xamarin
ms.assetid: 7BACCB36-8E3E-4E5D-B8EF-56A639839FD2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/17/2018
ms.openlocfilehash: 731aeaf0ad89a44211072962bf9891851a44ffcc
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667729"
---
# <a name="getting-started-with-fingerprint-authentication"></a>Introducción a la autenticación con huella digital

Para empezar, vamos a examinar primero cómo configurar un proyecto de Xamarin.Android para que la aplicación pueda usar la autenticación con huella digital:

1. Actualización **AndroidManifest.xml** para declarar los permisos que requieren las API de huellas digitales.
2. Obtener una referencia a la `FingerprintManager`.
3. Compruebe que el dispositivo es capaz de analizar la huella digital.

## <a name="requesting-permissions-in-the-application-manifest"></a>Manifiesto de la solicitud de permisos en la aplicación

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Una aplicación Android debe solicitar el `USE_FINGERPRINT` permiso en el manifiesto. Captura de pantalla siguiente muestra cómo agregar este permiso a la aplicación en Visual Studio 2015:

[![Habilitar uso\_huellas digitales en la pantalla de manifiesto de Android](get-started-images/fingerprint-01-vs.png)](get-started-images/fingerprint-01-vs.png#lightbox) 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Una aplicación Android debe solicitar el `USE_FINGERPRINT` permiso en el manifiesto. Captura de pantalla siguiente muestra cómo agregar este permiso a la aplicación en Visual Studio para Mac:

[![Habilitar UseFingerprint en la pantalla de la aplicación de Android](get-started-images/fingerprint-01-xs.png)](get-started-images/fingerprint-01-xs.png#lightbox) 

-----

## <a name="getting-an-instance-of-the-fingerprintmanager"></a>Obtener una instancia de la FingerprintManager

A continuación, la aplicación debe obtener una instancia de la `FingerprintManager` o `FingerprintManagerCompat` clase. Para ser compatible con versiones anteriores de Android, una aplicación Android debe utilizar la API de compatibilidad se han encontrado en el paquete de NuGet de compatibilidad de Android v4. El fragmento de código siguiente muestra cómo obtener el objeto adecuado desde el sistema operativo: 

```csharp
// Using the Android Support Library v4
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);

// Using API level 23:
FingerprintManager fingerprintManager = context.GetSystemService(Context.FingerprintService) as FingerprintManager;
```  

En el fragmento de código anterior, el `context` es cualquier Android `Android.Content.Context`. Normalmente se trata de la actividad que está realizando la autenticación.

## <a name="checking-for-eligibility"></a>Comprobar elegibilidad

Una aplicación debe realizar varias comprobaciones para asegurarse de que es posible usar la autenticación con huella digital. En total, hay cinco condiciones que utiliza la aplicación para comprobar la idoneidad:  

**Nivel de API 23** &ndash; las huellas digitales API requieren el nivel de API 23 o superior. La `FingerprintManagerCompat` clase ajustará la comprobación de nivel de API para usted. Por este motivo es recomendable usar el **biblioteca de compatibilidad de Android v4** y `FingerprintManagerCompat`; Esto se encargará de uno de estas comprobaciones.

**Hardware** &ndash; cuando la aplicación se inicia por primera vez, debe comprobar la presencia de un escáner de huella digital:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.IsHardwareDetected)
{
    // Code omitted
}
```

**Dispositivo esté protegido** &ndash; el usuario debe tener el dispositivo protegido con un bloqueo de pantalla. Si el usuario no ha protegido el dispositivo con un bloqueo de pantalla y la seguridad es importante para la aplicación, a continuación, el usuario se debe notificar que se debe configurar un bloqueo de pantalla. El fragmento de código siguiente muestra cómo comprobar este requisito previo:

```csharp
KeyguardManager keyguardManager = (KeyguardManager) GetSystemService(KeyguardService);
if (!keyguardManager.IsKeyguardSecure)
{
}
```

**Las huellas digitales de inscrito** &ndash; el usuario debe tener al menos una huella digital registrado con el sistema operativo. Esta comprobación de permiso debe producirse antes de cada intento de autenticación:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.HasEnrolledFingerprints)
{
    // Can't use fingerprint authentication - notify the user that they need to
    // enroll at least one fingerprint with the device.
}
```

**Permisos** &ndash; la aplicación debe solicitar permiso al usuario antes de usar la aplicación. Para Android 5.0 e inferior, el usuario concede el permiso como una condición de la instalación de la aplicación. Android 6.0 introdujo un nuevo modelo de permiso que comprueba los permisos en tiempo de ejecución. Este fragmento de código es un ejemplo de cómo comprobar los permisos en Android 6.0:

```csharp
// The context is typically a reference to the current activity.
Android.Content.PM.Permission permissionResult = ContextCompat.CheckSelfPermission(context, Manifest.Permission.UseFingerprint);
if (permissionResult == Android.Content.PM.Permission.Granted)
{
    // Permission granted - go ahead and start the fingerprint scanner.
}
else
{
    // No permission. Go and ask for permissions and don't start the scanner. See
    // https://developer.android.com/training/permissions/requesting.html
}
```

Comprobación de todas estas condiciones cada vez que la aplicación ofrece opciones de autenticación garantizará que el usuario obtiene la mejor experiencia de usuario. Los cambios o actualizaciones a su dispositivo o sistema operativo pueden afectar a la disponibilidad de la autenticación con huella digital. Si decide almacenar en caché los resultados de cualquiera de estas comprobaciones, asegúrese de cumplir los requisitos de escenarios de actualización.

Para obtener más información sobre cómo solicitar permisos en Android 6.0, consulte la Guía de Android [solicitar permisos en tiempo de ejecución](https://developer.android.com/training/permissions/requesting.html).

## <a name="related-links"></a>Vínculos relacionados

- [Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [ContextCompat](https://developer.xamarin.com/api/type/Android.Support.V4.Content.ContextCompat/)
- [KeyguardManager](https://developer.xamarin.com/api/type/Android.App.KeyguardManager/)
- [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
- [Solicitar permisos en tiempo de ejecución](https://developer.android.com/training/permissions/requesting.html)
