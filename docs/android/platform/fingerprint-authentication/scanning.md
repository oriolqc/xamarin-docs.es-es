---
title: Examen de huellas digitales
ms.prod: xamarin
ms.assetid: 1CDDC096-77E0-47B3-BE0B-8953E2DDACD3
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/23/2016
ms.openlocfilehash: 372fe4c7844448e7fb3cbc768f16feb3a5cc7791
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023462"
---
# <a name="scanning-for-fingerprints"></a>Examen de huellas digitales

Ahora que hemos visto cómo preparar una aplicación de Xamarin.Android para usar la autenticación con huella digital, vamos a volver a la `FingerprintManager.Authenticate` método y analizar su lugar en la autenticación de huella digital de Android 6.0. Información general del flujo de trabajo de autenticación con huella digital se describe en esta lista:

1. Invocar `FingerprintManager.Authenticate`, pasando un `CryptoObject` y un `FingerprintManager.AuthenticationCallback` instancia. El `CryptoObject` se utiliza para garantizar que no se ha alterado el resultado de la autenticación de huella digital. 
2. Subclase la [FingerprintManager.AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html) clase. Una instancia de esta clase se ofrecerá en `FingerprintManager` cuando se inicia la autenticación de huella digital. Cuando finalice el escáner de huella digital, invocará uno de los métodos de devolución de llamada en esta clase.
3. Escribir código para actualizar la interfaz de usuario para que el usuario sepa que el dispositivo ha iniciado el escáner de huellas digitales y está esperando la interacción del usuario. 
4. Cuando se realiza el escáner de huella digital, Android devolverá resultados a la aplicación mediante la invocación de un método en el `FingerprintManager.AuthenticationCallback` instancia que se proporcionó en el paso anterior.
5. La aplicación de informar al usuario de los resultados de la autenticación de huella digital y reaccionar a los resultados según corresponda. 

El fragmento de código siguiente es un ejemplo de un método en una actividad que se iniciará el examen de huellas digitales:

```csharp
protected void FingerPrintAuthenticationExample()
{
    const int flags = 0; /* always zero (0) */

    // CryptoObjectHelper is described in the previous section.
    CryptoObjectHelper cryptoHelper = new CryptoObjectHelper();    
    
    // cancellationSignal can be used to manually stop the fingerprint scanner. 
    cancellationSignal = new Android.Support.V4.OS.CancellationSignal();
    
    FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(this);
    
    // AuthenticationCallback is a base class that will be covered later on in this guide.
    FingerprintManagerCompat.AuthenticationCallback authenticationCallback = new MyAuthCallbackSample(this);

    // Start the fingerprint scanner.
    fingerprintManager.Authenticate(cryptoHelper.BuildCryptoObject(), flags, cancellationSignal, authenticationCallback, null);
}
```

Tratemos cada uno de estos parámetros en el `Authenticate` método un poco más detalladamente:

* El primer parámetro es un _crypto_ que usará el escáner de huellas digitales para autenticar a los resultados de un examen de huellas digitales de objeto. Este objeto puede ser `null`, en cuyo caso en que la aplicación tiene que confían ciegamente en la que nada ha alterado los resultados de la huella digital. Se recomienda que un `CryptoObject` se crea una instancia y se proporcionan para el `FingerprintManager` en lugar de null. [Creación de un CryptObject](~/android/platform/fingerprint-authentication/creating-a-cryptoobject.md) se explica detalladamente cómo crear una instancia de un `CryptoObject` según un `Cipher`.
* El segundo parámetro siempre es cero. La documentación de Android lo identifica como conjunto de marcas y es más probable es que se reserva para uso futuro. 
* El tercer parámetro, `cancellationSignal` es un objeto utilizado para desactivar el escáner de huellas digitales y cancelar la solicitud actual. Se trata de un [CancellationSignal Android](https://developer.android.com/reference/android/os/CancellationSignal.html)y no es un tipo de .NET framework.
* El cuarto parámetro es obligatorio y es una clase que cree subclases el `AuthenticationCallback` clase abstracta. Los métodos de esta clase se invocará para indicar a los clientes cuando el `FingerprintManager` ha finalizado y cuáles son los resultados. Como hay mucho que comprender acerca de cómo implementar la `AuthenticationCallback`, se explicará en [es propia sección](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md).
* El quinto parámetro es opcional `Handler` instancia. Si un `Handler` se proporciona el objeto, el `FingerprintManager` usará el `Looper` desde ese objeto al procesar los mensajes desde el hardware de huella digital. Normalmente, no es necesario proporcionar un `Handler`, usará el FingerprintManager el `Looper` desde la aplicación.

## <a name="cancelling-a-fingerprint-scan"></a>Al cancelar un examen de huellas digitales

Podría ser necesario para el usuario (o la aplicación) cancelar el examen de huellas digitales después de que se ha iniciado. En esta situación, invocar el [ `IsCancelled` ](https://developer.android.com/reference/android/os/CancellationSignal.html#isCanceled()) método en el [ `CancellationSignal` ](https://developer.android.com/reference/android/os/CancellationSignal.html) que proporcionó `FingerprintManager.Authenticate` cuando se invoca para iniciar el examen de huellas digitales.

Ahora que hemos visto la `Authenticate` método, vamos a examinar algunos de los parámetros más importantes con más detalle. En primer lugar, examinaremos [responde a las devoluciones de llamada de autenticación](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md), que se describe cómo crear subclases de la [FingerprintManager.AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html), habilitando una aplicación Android para reaccionar a la resultados proporcionados por el escáner de huella digital.




## <a name="related-links"></a>Vínculos relacionados

- [CancellationSignal](https://developer.android.com/reference/android/os/CancellationSignal.html)
- [FingerprintManager.AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [FingerprintManager.CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
