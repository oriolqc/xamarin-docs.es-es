---
title: Autenticación de huellas dactilares
description: Esta guía describe cómo agregar la autenticación de huellas digitales, introducida en Android 6.0, a una aplicación Xamarin.Android.
ms.prod: xamarin
ms.assetid: 6742D874-4988-4516-A946-D5C714B20A10
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 1b28b16dfd92ef3a31201ef2e86681a425a58ab8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30764123"
---
# <a name="fingerprint-authentication"></a>Autenticación de huellas dactilares

_Esta guía describe cómo agregar la autenticación de huellas digitales, introducida en Android 6.0, a una aplicación Xamarin.Android._


## <a name="fingerprint-authentication-overview"></a>Introducción a la autenticación mediante huellas digitales

La llegada de los escáneres de huellas digitales en dispositivos Android proporciona aplicaciones con una alternativa al método de usuario tradicionales y la contraseña de autenticación de usuario. El uso de las huellas digitales para autenticar un usuario permite a una aplicación incorporar la seguridad que es menos intrusivo que un nombre de usuario y una contraseña.

Las APIs FingerprintManager dispositivos de destino con un lector de huellas digitales y nivel de API 23 (Android 6.0) se están ejecutando o superior. Las API se encuentran en el `Android.Hardware.Fingerprints` espacio de nombres. La biblioteca de compatibilidad de Android v4 proporciona versiones de la huella digital API diseñadas para versiones anteriores de Android. La compatibilidad de API se encuentran en el `Android.Support.v4.Hardware.Fingerprint` espacio de nombres, se distribuyen a través de la [paquete NuGet de Xamarin.Android.Support.v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).

El [FingerprintManager](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html) (y su homólogo de la biblioteca de compatibilidad, [FingerprintManagerCompat](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)) es la clase principal para el uso de la huella digital del análisis de hardware. Esta clase es un contenedor de SDK de Android en el servicio de nivel de sistema que administra las interacciones con el propio hardware. Es responsable de iniciar el escáner de huellas digitales y para responder a los comentarios del escáner. Esta clase tiene una interfaz bastante sencilla con solo tres miembros:

* **`Authenticate`** &ndash; Este método se inicialice el escáner de hardware e iniciar el servicio en segundo plano, con lo que se espera para que el usuario examinar su huella digital.
* **`EnrolledFingerprints`** &ndash; Esta propiedad devolverá `true` si el usuario ha registrado las huellas digitales de uno o más con el dispositivo.
* **`HardwareDetected`** &ndash; Esta propiedad se utiliza para determinar si el dispositivo admite el análisis de huellas digitales.

El `FingerprintManager.Authenticate` método se usa una aplicación Android para iniciar el analizador de huellas digitales. El fragmento de código siguiente es un ejemplo de cómo invocar mediante la compatibilidad de la biblioteca de compatibilidad con las API:

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

Esta guía describe cómo utilizar el `FingerprintManager` API para mejorar una aplicación Android con la autenticación de huellas digitales. Explica cómo crear una instancia y crear un `CryptoObject` para ayudar a proteger los resultados desde el escáner de huellas digitales. Examinaremos cómo una aplicación debe subclase `FingerprintManager.AuthenticationCallback` y responder a los comentarios desde el escáner de huellas digitales. Por último, veremos cómo inscribir una huella digital en un emulador o dispositivo Android y cómo usar **adb** para simular un examen de huellas digitales.

## <a name="requirements"></a>Requisitos

Autenticación de huellas dactilares requiere Android 6.0 (nivel de API 23) o posterior y un dispositivo con un lector de huellas digitales. 

Una huella digital ya debe estar inscrito con el dispositivo para cada usuario que se autentique. Esto implica configurar un bloqueo de pantalla que utiliza una contraseña, el PIN, el patrón de deslice el dedo o el reconocimiento facial. Es posible simular algunas de las funciones de autenticación de huellas digitales en un emulador de Android.  Para obtener más información sobre estos dos temas, consulte el [inscribir una huella digital](enrolling-fingerprint.md) sección. 






## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo de guía de huellas digitales](https://developer.xamarin.com/samples/monodroid/FingerprintGuide/)
- [Ejemplo de cuadro de diálogo de huellas digitales](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)
- [Solicitar permisos en tiempo de ejecución](http://developer.android.com/training/permissions/requesting.html)
- [android.hardware.fingerprint](http://developer.android.com/reference/android/hardware/fingerprint/package-summary.html)
- [android.support.v4.hardware.fingerprint](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/package-summary.html)
- [Android.Content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [API de huellas digitales y pagos (vídeo)](https://youtu.be/VOn7VrTRlA4)
