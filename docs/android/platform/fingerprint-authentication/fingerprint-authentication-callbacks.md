---
title: "Responde a las devoluciones de llamada de autenticación"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6533AFC9-1A1C-4897-A154-4D4ECFE27761
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/06/2017
ms.openlocfilehash: 37d288cd75f232c8674aece085a78a83ce12d720
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/17/2018
---
# <a name="responding-to-authentication-callbacks"></a>Responde a las devoluciones de llamada de autenticación

El escáner de huellas digitales se ejecuta en segundo plano en su propio subproceso, y cuando haya finalizado notificará los resultados del análisis mediante la invocación de un método de `FingerprintManager.AuthenticationCallback` en el subproceso de interfaz de usuario. Una aplicación de Android debe proporcionar su propio controlador que amplía esta clase abstracta, implementar todos los métodos siguientes:

* **`OnAuthenticationError(int errorCode, ICharSequence errString)`** &ndash; Se llama cuando se produce un error irrecuperable. No hay nada más que una aplicación o un usuario pueda hacer para corregir la situación salvo posiblemente vuelva a intentarlo.
* **`OnAuthenticationFailed()`** &ndash; Este método se invoca cuando se detectó una huella digital pero no reconoce el dispositivo.
* **`OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)`** &ndash; Se llama cuando se produce un error recuperable, como el dedo está pasado a rápido sobre el escáner.
* **`OnAuthenticationSucceeded(FingerprintManagerCompati.AuthenticationResult result)`** &ndash; Esto se llama cuando se ha identificado una huella digital.

Si un `CryptoObject` se usó cuando se llama a `Authenticate`, se recomienda llamar a `Cipher.DoFinal` en `OnAuthenticationSuccessful`.
`DoFinal` se iniciará una excepción si el cifrado se ha alterado o inicializado de forma inadecuada, que indica que el resultado del escáner de huellas digitales que han sido alterado fuera de la aplicación.


> [!NOTE]
> Se recomienda mantener la devolución de llamada clase relativamente ligero y sin necesidad de lógica específica de la aplicación. Las devoluciones de llamada deben actuar como un "administrador del tráfico" entre la aplicación Android y los resultados del analizador de huellas digitales.

## <a name="a-sample-authentication-callback-handler"></a>Un controlador de devolución de llamada de autenticación de ejemplo

La clase siguiente es un ejemplo de un número mínimo `FingerprintManager.AuthenticationCallback` implementación: 

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

`OnAuthenticationSucceeded` comprueba si un `Cipher` ha proporcionado al `FingerprintManager` cuando `Authentication` se invocó. Si es así, la `DoFinal` método se llama en el cifrado. Esto cierra el `Cipher`, restaurar a su estado original. Si se produjo un problema con el cifrado, a continuación, `DoFinal` se iniciará una excepción y se debe considerar el intento de autenticación ha sufrido un error.

El `OnAuthenticationError` y `OnAuthenticationHelp` las devoluciones de llamada cada recepción un entero que indica cuál fue el problema. En la siguiente sección se explica cada una de las posibles ayuda o códigos de error. Las devoluciones de dos llamada finalidades similar &ndash; para informar a la aplicación de error de autenticación mediante huellas digitales. ¿Cómo se diferencian es según su gravedad. `OnAuthenticationHelp` es un error recuperable de usuario, por ejemplo, pasar la huella digital demasiado rápida; `OnAuthenticationError` es más un error grave, como un escáner de huellas digitales está dañado.

Tenga en cuenta que `OnAuthenticationError` se invocará cuando se cancela el examen de huellas digitales a través de la `CancellationSignal.Cancel()` mensaje. El `errMsgId` parámetro tendrá el valor 5 (`FingerprintState.ErrorCanceled`). Según los requisitos, una implementación de la `AuthenticationCallbacks` puede tratar esta situación de forma diferente a los demás errores. 

`OnAuthenticationFailed` se invoca cuando la huella digital se analizó correctamente pero no coincidía con ninguna huella digital inscrito con el dispositivo. 

## <a name="help-codes-and-error-message-ids"></a>Ayuda de códigos y los identificadores de mensaje de Error 

Encontrará una lista y una descripción de los códigos de error y ayuda en la [documentación del SDK de Android](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html#FINGERPRINT_ACQUIRED_GOOD) para la clase FingerprintManager. Xamarin.Android representa estos valores con el `Android.Hardware.Fingerprints.FingerprintState` enum:


-   **`AcquiredGood`** &ndash; (valor 0) La imagen adquirida ha sido buena.


-   **`AcquiredImagerDirty`** &ndash; (valor 3) La imagen de la huella digital es produce demasiado ruidosa debido a sucio sospechosa o detectado por el sensor. Por ejemplo, resulta razonable devolver esto después de varios `AcquiredInsufficient` o detección real de sucio en el sensor (píxeles atascados, franjas, etcetera). El usuario se espera que tome medidas para limpiar el sensor cuando se devuelve.


-   **`AcquiredInsufficient`** &ndash; (valor 2) La imagen de la huella digital es produce demasiado ruidosa procesar debido a una condición detectada (es decir, la máscara de seca) o a un sensor posiblemente desfasado (vea `AcquiredImagerDirty`.



-   **`AcquiredPartial`** &ndash; (valor 1) Se detectó una imagen de la huella digital parcial. Durante la inscripción, el usuario debe ser informado sobre lo que debe ocurrir resolver este problema, p. ej., &ldquo;Presione firmemente en sensor.&rdquo;



-   **`AcquiredTooFast`** &ndash; (valor 5) La imagen de huellas digitales estaba incompleta debido al movimiento rápido. Mientras más adecuado para sensores de la matriz lineal, esto también podría ocurrir si se ha movido el dedo durante la adquisición. El usuario se debe plantear para mover el dedo más lento (lineal) o dejar el dedo en el sensor más tiempo.




-   **`AcquiredToSlow`** &ndash; (valor 4) La imagen de la huella digital es ilegible debido a falta de movimiento. Esto es más adecuado para los sensores de matriz lineal que requieren un movimiento deslice el dedo.



-   **`ErrorCanceled`** &ndash; (valor 5) Se canceló la operación porque el sensor de huellas digitales no está disponible. Por ejemplo, esto puede ocurrir cuando se cambia el usuario, el dispositivo está bloqueado, u otra operación pendiente impide o lo deshabilita.



-   **`ErrorHwUnavailable`** &ndash; (valor 1) El hardware no está disponible. Inténtelo de nuevo más tarde.




-   **`ErrorLockout`** &ndash; (valor 7) Se canceló la operación porque la API se bloquea debido a demasiados intentos incorrectos.




-   **`ErrorNoSpace`** &ndash; (valor 4) Estado de error devuelto para operaciones como la inscripción; no se puede completar la operación porque no hay suficiente espacio restante para completar la operación.



-   **`ErrorTimeout`** &ndash; (valor 3) Estado de error se devuelve cuando la solicitud actual se ha estado ejecutando demasiado larga. Está diseñado para evitar que los programas esperando indefinidamente el sensor de huellas digitales. El tiempo de espera es específico de sensor y plataforma, pero suele ser unos 30 segundos.



-   **`ErrorUnableToProcess`** &ndash; (valor 2) Estado de error se devuelve cuando el sensor no pudo procesar la imagen actual.



## <a name="related-links"></a>Vínculos relacionados

- [Cipher](https://docs.oracle.com/javase/7/docs/api/javax/crypto/Cipher.html)
- [AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [AuthenticationCallback](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.AuthenticationCallback.html)
