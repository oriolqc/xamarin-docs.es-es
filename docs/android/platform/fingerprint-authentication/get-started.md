---
title: Introducción a la autenticación de huellas dactilares
ms.prod: xamarin
ms.assetid: 7BACCB36-8E3E-4E5D-B8EF-56A639839FD2
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: 70d27ef3d7518619a246c25aac128b2fd1ed70c5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="getting-started-with-fingerprint-authentication"></a>Introducción a la autenticación de huellas dactilares

Para empezar, vamos a primero incluye información sobre cómo configurar un proyecto de Xamarin.Android para que la aplicación pueda usar la autenticación de huellas digitales:

1. Actualización **AndroidManifest.xml** para declarar los permisos requeridos por las API de huellas digitales.
2. Obtener una referencia a la `FingerprintManager`.
3. Compruebe que el dispositivo es capaz de analizar la huella digital.

## <a name="requesting-permissions-in-the-application-manifest"></a>Solicitar permisos en la aplicación del manifiesto

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Una aplicación Android debe solicitar la `USE_FINGERPRINT` permiso en el manifiesto. Captura de pantalla siguiente muestra cómo agregar este permiso a la aplicación en Visual Studio 2015:

[![Habilitar el uso\_huellas digitales en la pantalla de manifiesto de Android](get-started-images/fingerprint-01-vs.png)](get-started-images/fingerprint-01-vs.png#lightbox) 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Una aplicación Android debe solicitar la `USE_FINGERPRINT` permiso en el manifiesto. Captura de pantalla siguiente muestra cómo agregar este permiso a la aplicación en Visual Studio para Mac:

[![Habilitar UseFingerprint en la pantalla de aplicación de Android](get-started-images/fingerprint-01-xs.png)](get-started-images/fingerprint-01-xs.png#lightbox) 

-----

## <a name="getting-an-instance-of-the-fingerprintmanager"></a>Obtener una instancia de la FingerprintManager

A continuación, la aplicación debe obtener una instancia de la `FingerprintManager` o `FingerprintManagerCompat` clase. Para que sea compatible con versiones anteriores de Android, una aplicación Android debe utilizar la API de compatibilidad se encontró en el paquete de NuGet de compatibilidad con Android v4. El fragmento de código siguiente muestra cómo obtener el objeto adecuado desde el sistema operativo: 

```csharp
// Using the Android Support Library v4
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);

// Using API level 23:
FingerprintManager fingerprintManager = context.GetSystemService(Context.FingerprintService) as FingerprintManager;
```  

En el fragmento de código anterior, el `context` es cualquier Android `Android.Content.Context`. Normalmente, se trata de la actividad que está realizando la autenticación.

## <a name="checking-for-eligibility"></a>Comprobación de idoneidad

Una aplicación debe realizar varias comprobaciones para asegurarse de que es posible utilizar la autenticación de huellas digitales. En total, hay cinco condiciones que utiliza la aplicación para comprobar la idoneidad:  
 

**Nivel de API 23** &ndash; las API de huellas digitales requieren el nivel de API 23 o superior. La `FingerprintManagerCompat` clase ajustará la comprobación de nivel de API para usted. Por este motivo es recomendable utilizar el **biblioteca de compatibilidad de Android v4** y `FingerprintManagerCompat`; esto tendrá en cuenta para una de estas comprobaciones.

**Hardware** &ndash; cuando la aplicación se inicia por primera vez, debe comprobar la presencia de un escáner de huellas digitales:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.IsHardwareDetected)
{
    // Code omitted
}
```
    
**Dispositivo está protegido** &ndash; el usuario debe tener el dispositivo protegido con un bloqueo de pantalla. Si el usuario no ha protegido el dispositivo con un bloqueo de pantalla y la seguridad es importante para la aplicación, a continuación, el usuario se debe notificar que se debe configurar un bloqueo de pantalla. El fragmento de código siguiente muestra cómo comprobar este requiste anterior:

```csharp
KeyguardManager keyguardManager = (KeyguardManager) GetSystemService(KeyguardService);
if (!keyguardManager.IsKeyguardSecure)
{
}
```

**Inscribir las huellas digitales** &ndash; el usuario debe tener al menos una huella digital registrado con el sistema operativo. Esta comprobación de permiso debe producirse antes de cada intento de autenticación:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.HasEnrolledFingerprints)
{
    // Can't use fingerprint authentication - notify the user that they need to
    // enroll at least one fingerprint with the device.
}
```

**Permisos** &ndash; la aplicación debe solicitar permiso del usuario antes de usar la aplicación. Para Android 5.0 e inferior, el usuario concede el permiso como una condición de la instalación de la aplicación. Android 6.0 introdujo un nuevo modelo de permiso que comprueba los permisos en tiempo de ejecución. Este fragmento de código es un ejemplo de cómo comprobar los permisos en Android 6.0:

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
    // http://developer.android.com/training/permissions/requesting.html
}
```

Una aplicación debe comprobar cada vez que se desea utilizar la autenticación de huellas digitales de condiciones 3, 4 y 5. Las dos primeras condiciones se pueden comprobar la primera vez que una aplicación se ejecuta en un dispositivo y guardos los resultados (en shared preferencias por ejemplo).

Para obtener más información sobre cómo solicitar permisos en Android 6.0, consulte la Guía de Android [solicitud de permisos en tiempo de ejecución](http://developer.android.com/training/permissions/requesting.html).



## <a name="related-links"></a>Vínculos relacionados

- [Contexto](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [ContextCompat](https://developer.xamarin.com/api/type/Android.Support.V4.Content.ContextCompat/)
- [KeyguardManager](https://developer.xamarin.com/api/type/Android.App.KeyguardManager/)
- [FingerprintManager](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
- [Solicitar permisos en tiempo de ejecución](http://developer.android.com/training/permissions/requesting.html)
