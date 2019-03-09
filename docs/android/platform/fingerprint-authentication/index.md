---
title: Autenticación de huella digital
description: 'Esta guía describe cómo agregar autenticación con huella digital, introducida en Android 6.0 a una aplicación de Xamarin.Android.'
ms.prod: xamarin
ms.assetid: 6742D874-4988-4516-A946-D5C714B20A10
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
---

# <a name="fingerprint-authentication"></a>Autenticación de huella digital

_Esta guía describe cómo agregar autenticación con huella digital, introducida en Android 6.0 a una aplicación de Xamarin.Android._


## <a name="fingerprint-authentication-overview"></a>Información general sobre la autenticación de huella digital

La llegada de los escáneres de huellas digitales en dispositivos Android proporciona aplicaciones con una alternativa al método tradicional de usuario y contraseña de autenticación de usuario. El uso de las huellas digitales para autenticar un usuario hace posible para una aplicación para incorporar la seguridad que es menos intrusivo que un nombre de usuario y una contraseña.

Las APIs FingerprintManager dispositivos de destino con un escáner de huella digital y se ejecutan a nivel de API 23 (Android 6.0) o superior. Las API se encuentran en el `Android.Hardware.Fingerprints` espacio de nombres. V4 de la biblioteca de compatibilidad de Android proporciona versiones de la huella digital API diseñadas para versiones anteriores de Android. La compatibilidad de API se encuentran en el `Android.Support.v4.Hardware.Fingerprint` espacio de nombres, se distribuyen a través de la [paquete NuGet Xamarin.Android.Support.v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).

El [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html) (y su equivalente de la biblioteca de compatibilidad, [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)) es la clase principal para el uso de la huella digital del examen de hardware. Esta clase es un contenedor de Android SDK del servicio de nivel de sistema que administra las interacciones con el propio hardware. Es responsable de iniciar el escáner de huellas digitales y para responder a los comentarios del escáner. Esta clase tiene una interfaz de un proceso bastante sencillo con solo tres miembros:

* **`Authenticate`** &ndash; Este método se inicialice el analizador de hardware e iniciar el servicio en segundo plano, espera a que el usuario digitalizarán su huella digital.
* **`EnrolledFingerprints`** &ndash; Esta propiedad devolverá `true` si el usuario ha registrado una o varias huellas digitales con el dispositivo.
* **`HardwareDetected`** &ndash; Esta propiedad se utiliza para determinar si el dispositivo es compatible con el examen de huellas digitales.

El `FingerprintManager.Authenticate` método se usa una aplicación Android para iniciar el escáner de huella digital. El fragmento de código siguiente es un ejemplo de cómo invocarlo por medio de la compatibilidad de la biblioteca de compatibilidad con las API:

```csharp
// context is any Android.Content.Context instance, typically the Activity 
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
fingerprintManager.Authenticate(FingerprintManager.CryptoObject crypto,
                                int flags,
                                CancellationSignal cancel,
                                FingerprintManagerCompat.AuthenticationCallback callback,
                                Handler handler
                               );
```

Esta guía describe cómo usar el `FingerprintManager` API para mejorar una aplicación Android con la autenticación con huella digital. Explica cómo crear una instancia y crear un `CryptoObject` para ayudar a proteger los resultados del escáner de huellas digitales. Examinaremos cómo una aplicación debe subclase `FingerprintManager.AuthenticationCallback` y responder a los comentarios del escáner de huellas digitales. Por último, veremos cómo inscribir una huella digital en un emulador o dispositivo Android y cómo usar **adb** para simular un examen de huellas digitales.

## <a name="requirements"></a>Requisitos

Autenticación con huella digital requiere Android 6.0 (nivel de API 23) o superior y un dispositivo con un escáner de huellas digitales. 

Una huella digital ya debe estar inscritos con el dispositivo para cada usuario que debe autenticarse. Esto implica configurar un bloqueo de pantalla que se usa una contraseña, PIN, el patrón de pasar el dedo o el reconocimiento facial. Es posible simular algunas de las funcionalidades de autenticación de huella digital en un emulador de Android.  Para obtener más información sobre estos dos temas, consulte el [inscribir una huella digital](enrolling-fingerprint.md) sección. 






## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo de guía de huellas digitales](https://developer.xamarin.com/samples/monodroid/FingerprintGuide/)
- [Ejemplo de cuadro de diálogo de huellas digitales](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)
- [Solicitar permisos en tiempo de ejecución](https://developer.android.com/training/permissions/requesting.html)
- [android.hardware.fingerprint](https://developer.android.com/reference/android/hardware/fingerprint/package-summary.html)
- [android.support.v4.hardware.fingerprint](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/package-summary.html)
- [Android.Content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [API de huellas digitales y pagos (vídeo)](https://youtu.be/VOn7VrTRlA4)
