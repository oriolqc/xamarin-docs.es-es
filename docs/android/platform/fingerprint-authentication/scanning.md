---
title: Buscando las huellas digitales
ms.prod: xamarin
ms.assetid: 1CDDC096-77E0-47B3-BE0B-8953E2DDACD3
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/23/2016
ms.openlocfilehash: 2b20402fd2cd6782247fb2c62513d7aff43a95a6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30774166"
---
# <a name="scanning-for-fingerprints"></a>Buscando las huellas digitales

Ahora que hemos visto cómo preparar una aplicación de Xamarin.Android para utilizar la autenticación de huellas digitales, vamos a volver a la `FingerprintManager.Authenticate` método y se tratan en su lugar en la autenticación de huellas digitales de Android 6.0. Información general rápida del flujo de trabajo para la autenticación de huellas digitales se describe en esta lista:

1. Invocar `FingerprintManager.Authenticate`, pasando un `CryptoObject` y un `FingerprintManager.AuthenticationCallback` instancia. El `CryptoObject` se utiliza para garantizar que el resultado de la autenticación de huellas digitales no ha sido manipulado. 
2. Subclase la [FingerprintManager.AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html) clase. Una instancia de esta clase se proporcionará a `FingerprintManager` cuando se inicia la autenticación de huellas digitales. Cuando haya finalizado el escáner de huellas digitales, invocará uno de los métodos de devolución de llamada de esta clase.
3. Escribir código para actualizar la interfaz de usuario para que el usuario sepa que el dispositivo ha iniciado el escáner de huellas digitales y está esperando la intervención del usuario. 
4. Cuando se realiza el escáner de huellas digitales, Android devolverá resultados a la aplicación mediante la invocación de un método en el `FingerprintManager.AuthenticationCallback` instancia que se proporcionó en el paso anterior.
5. La aplicación se informar al usuario de los resultados de la autenticación de huellas digitales y reaccionar a los resultados según corresponda. 

El siguiente fragmento de código es un ejemplo de un método en una actividad que se iniciará buscando las huellas digitales:

```csharp
protected void FingerPrintAuthenticationExample()
{
    const int flags = 0; /* always zero (0) */

    // CryptoObjectHelper is described in the previous section.
    CryptoObjectHelper cryptoHelper = new CryptoObjectHelper();    
    
    // cancellationSignal can be used to manually stop the fingerprint scanner. 
    cancellationSignal = new Android.Support.V4.OS.CancellationSignal();
    
    FingerprintManagerCompat fingerPrintManager = FingerprintManagerCompat.From(this);
    
    // AuthenticationCallback is a base class that will be covered later on in this guide.
    FingerprintManagerCompat.AuthenticationCallback authenticationCallback = new MyAuthCallbackSample(this);

    // Start the fingerprint scanner.
    fingerprintManager.Authenticate(cryptoHelper.BuildCryptoObject(), flags, cancellationSignal, authenticationCallback, null);
}
```

Analicemos cada uno de estos parámetros en el `Authenticate` método con un poco más detalle:

* El primer parámetro es un _crypto_ que utilizará el escáner de huellas digitales para autenticar a los resultados de un análisis de huella digital del objeto. Este objeto puede ser `null`, en cuyo caso la aplicación seguirá a ciegas confiar en que no se alteró con los resultados de la huella digital. Se recomienda que un `CryptoObject` se crea una instancia y se proporcionan para el `FingerprintManager` en lugar de null. [Crear un CryptObject](~/android/platform/fingerprint-authentication/creating-a-cryptoobject.md) se explica detalladamente cómo crear una instancia de un `CryptoObject` tomando como base un `Cipher`.
* El segundo parámetro es siempre cero. La documentación de Android lo identifica como conjunto de marcas y es más probable es que se reserva para uso futuro. 
* El tercer parámetro, `cancellationSignal` es un objeto que se usa para desactivar el escáner de huellas digitales y cancelar la solicitud actual. Se trata de un [CancellationSignal Android](http://developer.android.com/reference/android/os/CancellationSignal.html)y no es un tipo de .NET framework.
* El cuarto parámetro es obligatorio y es una clase que cree subclases la `AuthenticationCallback` clase abstracta. Métodos de esta clase se invocará para indicar a los clientes cuando el `FingerprintManager` ha finalizado y cuáles son los resultados. Como hay muchas cosas que saber acerca de cómo implementar el `AuthenticationCallback`, se explicará en [es propia sección](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md).
* El quinto parámetro es una función opcional `Handler` instancia. Si un `Handler` se proporciona el objeto, el `FingerprintManager` utilizará el `Looper` desde el objeto al procesar los mensajes del hardware de huellas digitales. Por lo general, no es necesario proporcionar un `Handler`, usará el FingerprintManager la `Looper` de la aplicación.

## <a name="cancelling-a-fingerprint-scan"></a>Al cancelar un análisis de huellas digitales

Podría ser necesario para el usuario (o la aplicación) cancelar el análisis de huellas digitales después de que se ha iniciado. En esta situación, invocar el [ `IsCancelled` ](http://developer.android.com/reference/android/os/CancellationSignal.html#isCanceled()) método en el [ `CancellationSignal` ](http://developer.android.com/reference/android/os/CancellationSignal.html) que se proporcionó a `FingerprintManager.Authenticate` cuando se invoca para iniciar el análisis de huellas digitales.

Ahora que hemos visto el `Authenticate` método, examinemos algunos de los parámetros más importantes con más detalle. En primer lugar, echemos un vistazo [responde a las devoluciones de llamada de autenticación](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md), que veremos cómo crear subclases de la [FingerprintManager.AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html), permitiendo que una aplicación Android reaccionar ante la resultados proporcionados por el escáner de huellas digitales.




## <a name="related-links"></a>Vínculos relacionados

- [CancellationSignal](http://developer.android.com/reference/android/os/CancellationSignal.html)
- [FingerprintManager.AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [FingerprintManager.CryptoObject](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [FingerprintManager](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
