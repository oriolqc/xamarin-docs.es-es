---
title: Respuesta a las devoluciones de llamada de autenticación
ms.prod: xamarin
ms.assetid: 6533AFC9-1A1C-4897-A154-4D4ECFE27761
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/06/2017
ms.openlocfilehash: c720a30a59eea8f1ed74033da8d1c045a1fb9109
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57666871"
---
# <a name="responding-to-authentication-callbacks"></a>Respuesta a las devoluciones de llamada de autenticación

El escáner de huella digital se ejecuta en segundo plano en su propio subproceso, y cuando haya finalizado notificará los resultados del análisis mediante la invocación de un método de `FingerprintManager.AuthenticationCallback` en el subproceso de interfaz de usuario. Una aplicación Android debe proporcionar su propio controlador que amplía esta clase abstracta, implementar todos los métodos siguientes:

* **`OnAuthenticationError(int errorCode, ICharSequence errString)`** &ndash; Se llama cuando se produce un error irrecuperable. No hay nada más que una aplicación o un usuario pueda hacer para corregir la situación, salvo que, posiblemente, vuelva a intentarlo.
* **`OnAuthenticationFailed()`** &ndash; Este método se invoca cuando se detectó una huella digital pero no reconoce el dispositivo.
* **`OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)`** &ndash; Se llama cuando hay un error recuperable, por ejemplo, el dedo que se va a pasar a rápido sobre el analizador.
* **`OnAuthenticationSucceeded(FingerprintManagerCompati.AuthenticationResult result)`** &ndash; Se llama cuando se le ha reconocido una huella digital.

Si un `CryptoObject` se usó cuando se llama a `Authenticate`, se recomienda llamar a `Cipher.DoFinal` en `OnAuthenticationSuccessful`.
`DoFinal` se iniciará una excepción si el cifrado se ha manipulado o inicializado incorrectamente, lo que indica que el resultado del escáner de huellas digitales puede haber sido alterado con fuera de la aplicación.


> [!NOTE]
> Se recomienda mantener la devolución de llamada clase relativamente ligera y gratuita de lógica específica de aplicación. Las devoluciones de llamada deben actuar como un "administrador del tráfico" entre la aplicación Android y los resultados del escáner de huellas digitales.

## <a name="a-sample-authentication-callback-handler"></a>Un ejemplo de controlador de devolución de llamada de autenticación

La clase siguiente es un ejemplo de un mínimo `FingerprintManager.AuthenticationCallback` implementación: 

```csharp
class MyAuthCallbackSample : FingerprintManagerCompat.AuthenticationCallback
{
    // Can be any byte array, keep unique to application.
    static readonly byte[] SECRET_BYTES = {1, 2, 3, 4, 5, 6, 7, 8, 9};
    // The TAG can be any string, this one is for demonstration.
    static readonly string TAG = "X:" + typeof (SimpleAuthCallbacks).Name;

    public MyAuthCallbackSample()
    {
    }

    public override void OnAuthenticationSucceeded(FingerprintManagerCompat.AuthenticationResult result)
    {
        if (result.CryptoObject.Cipher != null) 
        {
            try
            {
                // Calling DoFinal on the Cipher ensures that the encryption worked.
                byte[] doFinalResult = result.CryptoObject.Cipher.DoFinal(SECRET_BYTES);
    
                // No errors occurred, trust the results.              
            }
            catch (BadPaddingException bpe)
            {
                // Can't really trust the results.
                Log.Error(TAG, "Failed to encrypt the data with the generated key." + bpe);
            }
            catch (IllegalBlockSizeException ibse)
            {
                // Can't really trust the results.
                Log.Error(TAG, "Failed to encrypt the data with the generated key." + ibse);
            }
        }
        else
        {
            // No cipher used, assume that everything went well and trust the results.
        }
    }

    public override void OnAuthenticationError(int errMsgId, ICharSequence errString)
    {
        // Report the error to the user. Note that if the user canceled the scan,
        // this method will be called and the errMsgId will be FingerprintState.ErrorCanceled.
    }

    public override void OnAuthenticationFailed()
    {
        // Tell the user that the fingerprint was not recognized.
    }

    public override void OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)
    {
        // Notify the user that the scan failed and display the provided hint.
    }
}
```

`OnAuthenticationSucceeded` comprueba si un `Cipher` proporcionó `FingerprintManager` cuando `Authentication` se invocó. Si es así, el `DoFinal` se llama al método en el cifrado. Esto cierra el `Cipher`, restaurándola a su estado original. Si se produjo un problema con el cifrado, a continuación, `DoFinal` generará una excepción y se debe considerar el intento de autenticación que ha sido erróneo.

El `OnAuthenticationError` y `OnAuthenticationHelp` devoluciones de llamada cada recepción un entero que indica cuál fue el problema. La siguiente sección explica cada uno de los códigos de error o ayuda posible. Las dos devoluciones de llamada sirven a propósitos semejantes &ndash; para informar a la aplicación de error de autenticación mediante huella digital. Qué se diferencian es la gravedad. `OnAuthenticationHelp` es un error recuperable de usuario, por ejemplo, deslice el dedo hacia la huella digital demasiado rápida; `OnAuthenticationError` es más un error grave, como un escáner de huellas digitales está dañado.

Tenga en cuenta que `OnAuthenticationError` se invocará cuando se cancela el examen de huellas digitales a través de la `CancellationSignal.Cancel()` mensaje. El `errMsgId` parámetro tendrá el valor de 5 (`FingerprintState.ErrorCanceled`). Según los requisitos, una implementación de la `AuthenticationCallbacks` puede tratar esta situación de forma diferente a los demás errores. 

`OnAuthenticationFailed` se invoca cuando la huella digital se realizó correctamente pero no coincide con ninguna huella digital inscrito con el dispositivo. 

## <a name="help-codes-and-error-message-ids"></a>Ayuda de códigos y los identificadores de mensaje de Error 

Pueden encontrar una lista y una descripción de los códigos de error y ayuda en la [documentación del SDK de Android](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html#FINGERPRINT_ACQUIRED_GOOD) para la clase FingerprintManager. Xamarin.Android representa estos valores con el `Android.Hardware.Fingerprints.FingerprintState` enum:


-   **`AcquiredGood`** &ndash; (valor 0) La imagen que se adquirió ha sido buena.


-   **`AcquiredImagerDirty`** &ndash; (valor 3) La imagen de la huella digital era con demasiado ruido debido a la suciedad de sospecha o detectado en el sensor. Por ejemplo, es razonable devolver esto después de varios `AcquiredInsufficient` o detección de suciedad en el sensor (píxeles bloqueadas, franjas, etcetera.). Se espera que el usuario tome medidas para limpiar el sensor cuando se devuelve.


-   **`AcquiredInsufficient`** &ndash; (valor 2) La imagen de la huella digital es produce demasiado ruidosa procesar debido a una condición detectada (es decir, la máscara de seca) o un sensor posiblemente desfasado (consulte `AcquiredImagerDirty`.



-   **`AcquiredPartial`** &ndash; (valor 1) Se ha detectado solo una imagen parcial de huellas digitales. Durante la inscripción, el usuario debe estar informado sobre lo que debe suceder resolver este problema, por ejemplo, &ldquo;Presione firmemente en el sensor.&rdquo;



-   **`AcquiredTooFast`** &ndash; (valor 5) La imagen de la huella digital estaba incompleta debido al movimiento rápido. Mientras principalmente adecuado para los sensores de matriz lineal, esto también podría ocurrir si se ha movido el dedo durante la adquisición. El usuario debe se le pida que mover el dedo más lento (lineal) o dejar el dedo en el sensor más tiempo.




-   **`AcquiredToSlow`** &ndash; (valor 4) La imagen de la huella digital es ilegible debido a falta de movimiento. Esto es más adecuado para los sensores de matriz lineal que requieren un movimiento del dedo.



-   **`ErrorCanceled`** &ndash; (valor 5) Se canceló la operación porque el sensor de huellas digitales no está disponible. Por ejemplo, esto puede ocurrir cuando se cambia el usuario, el dispositivo está bloqueado, u otra operación pendiente impide o lo deshabilita.



-   **`ErrorHwUnavailable`** &ndash; (valor 1) El hardware no está disponible. Vuelva a intentarlo más tarde.




-   **`ErrorLockout`** &ndash; (valor 7) Se canceló la operación porque la API está bloqueada debido a demasiados intentos.




-   **`ErrorNoSpace`** &ndash; (valor 4) Estado de error devuelto para operaciones como la inscripción; no se puede completar la operación porque no hay suficiente espacio restante para completar la operación de almacenamiento.



-   **`ErrorTimeout`** &ndash; (valor 3) Estado de error devuelto cuando la solicitud actual se ejecuta durante mucho tiempo. Esto está pensado para evitar que programas indefinidamente en espera para el sensor de huellas digitales. El tiempo de espera es específico para el sensor y plataforma, pero suele ser aproximadamente 30 segundos.



-   **`ErrorUnableToProcess`** &ndash; (valor 2) Estado de error se devuelve cuando el sensor no pudo procesar la imagen actual.



## <a name="related-links"></a>Vínculos relacionados

- [Cifrado](https://docs.oracle.com/javase/7/docs/api/javax/crypto/Cipher.html)
- [AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [AuthenticationCallback](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.AuthenticationCallback.html)
