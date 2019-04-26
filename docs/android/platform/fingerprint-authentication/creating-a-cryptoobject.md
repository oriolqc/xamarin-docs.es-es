---
title: Creación de un CryptoObject
ms.prod: xamarin
ms.assetid: 4D159B80-FFF4-4136-A7F0-F8A5C6B86838
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 1305a8a1f39d34b5e91e478a769750911afb2b3e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60953193"
---
# <a name="creating-a-cryptoobject"></a>Creación de un CryptoObject

La integridad de los resultados de la autenticación de huella digital es importante para una aplicación &ndash; es cómo la aplicación conoce la identidad del usuario. Es posible, en teoría, en busca de malware de terceros interceptar y alterar los resultados devueltos por el escáner de huella digital. Esta sección describe una técnica para conservar la validez de los resultados de la huella digital. 

El `FingerprintManager.CryptoObject` es un contenedor en torno a la API de criptografía de Java y utilizan el `FingerprintManager` para proteger la integridad de la solicitud de autenticación. Normalmente, un `Javax.Crypto.Cipher` objeto es el mecanismo para cifrar los resultados del escáner de huellas digitales. El `Cipher` propio objeto usará una clave que se crea mediante la aplicación con el almacén de claves Android API.

Para entender cómo funcionan juntas todas estas clases, veamos primero el código siguiente que muestra cómo crear un `CryptoObject`y, a continuación, se explica con más detalle:

```csharp
public class CryptoObjectHelper
{
    // This can be key name you want. Should be unique for the app.
    static readonly string KEY_NAME = "com.xamarin.android.sample.fingerprint_authentication_key";

    // We always use this keystore on Android.
    static readonly string KEYSTORE_NAME = "AndroidKeyStore";

    // Should be no need to change these values.
    static readonly string KEY_ALGORITHM = KeyProperties.KeyAlgorithmAes;
    static readonly string BLOCK_MODE = KeyProperties.BlockModeCbc;
    static readonly string ENCRYPTION_PADDING = KeyProperties.EncryptionPaddingPkcs7;
    static readonly string TRANSFORMATION = KEY_ALGORITHM + "/" +
                                            BLOCK_MODE + "/" +
                                            ENCRYPTION_PADDING;
    readonly KeyStore _keystore;

    public CryptoObjectHelper()
    {
        _keystore = KeyStore.GetInstance(KEYSTORE_NAME);
        _keystore.Load(null);
    }

    public FingerprintManagerCompat.CryptoObject BuildCryptoObject()
    {
        Cipher cipher = CreateCipher();
        return new FingerprintManagerCompat.CryptoObject(cipher);
    }

    Cipher CreateCipher(bool retry = true)
    {
        IKey key = GetKey();
        Cipher cipher = Cipher.GetInstance(TRANSFORMATION);
        try
        {
            cipher.Init(CipherMode.EncryptMode | CipherMode.DecryptMode, key);
        } catch(KeyPermanentlyInvalidatedException e)
        {
            _keystore.DeleteEntry(KEY_NAME);
            if(retry)
            {
                CreateCipher(false);
            } else
            {
                throw new Exception("Could not create the cipher for fingerprint authentication.", e);
            }
        }
        return cipher;
    }

    IKey GetKey()
    {
        IKey secretKey;
        if(!_keystore.IsKeyEntry(KEY_NAME))
        {
            CreateKey();
        }

        secretKey = _keystore.GetKey(KEY_NAME, null);
        return secretKey;
    }

    void CreateKey()
    {
        KeyGenerator keyGen = KeyGenerator.GetInstance(KeyProperties.KeyAlgorithmAes, KEYSTORE_NAME);
        KeyGenParameterSpec keyGenSpec =
            new KeyGenParameterSpec.Builder(KEY_NAME, KeyStorePurpose.Encrypt | KeyStorePurpose.Decrypt)
                .SetBlockModes(BLOCK_MODE)
                .SetEncryptionPaddings(ENCRYPTION_PADDING)
                .SetUserAuthenticationRequired(true)
                .Build();
        keyGen.Init(keyGenSpec);
        keyGen.GenerateKey();
    }
}
```

El código de ejemplo creará una nueva `Cipher` para cada `CryptoObject`, mediante una clave que se creó con la aplicación. La clave se identifica mediante el `KEY_NAME` variable que se ha establecido al principio de la `CryptoObjectHelper` clase. El método `GetKey` se pruebe y recuperar la clave mediante las APIs Keystore Android. Si la clave no existe, a continuación, el método `CreateKey` creará una nueva clave para la aplicación.

El cifrado se crea una instancia con una llamada a `Cipher.GetInstance`, teniendo un _transformación_ (un valor de cadena que indica el cifrado para cifrar y descifrar los datos). La llamada a `Cipher.Init` se completará la inicialización del cifrado proporcionando una clave de la aplicación. 

Es importante tener en cuenta que existen algunas situaciones donde Android puede invalidar la clave: 

* Una huella digital del nuevo se ha inscrito con el dispositivo.
* No hay ningún huellas digitales inscritos con el dispositivo.
* El usuario ha deshabilitado el bloqueo de pantalla.
* El usuario ha cambiado el bloqueo de pantalla (el tipo de la screenlock o el PIN o el patrón utilizado).

Cuando esto sucede, `Cipher.Init` producirá un [ `KeyPermanentlyInvalidatedException` ](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html). El código de ejemplo anterior se intercepta esa excepción, elimine la clave y, a continuación, cree una nueva.

La siguiente sección describe cómo crear la clave y almacenarla en el dispositivo.

## <a name="creating-a-secret-key"></a>Creación de una clave secreta

El `CryptoObjectHelper` clase usa el Android [ `KeyGenerator` ](https://developer.xamarin.com/api/type/Javax.Crypto.KeyGenerator/) para crear una clave y almacenarla en el dispositivo. La `KeyGenerator` clase puede crear la clave, pero necesita algunos metadatos sobre el tipo de clave que se va a crear. Esta información se proporciona por una instancia de la [ `KeyGenParameterSpec` ](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html) clase. 

Un `KeyGenerator` se crea una instancia mediante el `GetInstance` método de fábrica. El código de ejemplo usa el [ _estándar de cifrado avanzado_ ](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (_AES_) como el algoritmo de cifrado. AES se dividir los datos en bloques de un tamaño fijo y se cifrarán cada uno de los bloques.

A continuación, un `KeyGenParameterSpec` se crea utilizando el `KeyGenParameterSpec.Builder`. El `KeyGenParameterSpec.Builder` ajusta la información siguiente acerca de la clave que se va a crearse:

* Nombre de la clave.
* La clave debe ser válida para el cifrado y descifrado.
* En el código de ejemplo la `BLOCK_MODE` está establecido en _encadenamiento de bloques de cifrado_ (`KeyProperties.BlockModeCbc`), lo que significa que cada bloque se compara mediante XOR con el bloque anterior (creación de dependencias entre cada bloque). 
* El `CryptoObjectHelper` usa [ _pública clave de criptografía estándar #7_ ](https://tools.ietf.org/html/rfc2315) (_PKCS7_) para generar los bytes que rellenará los los bloques para asegurarse de que son todas del mismo tamaño .
* `SetUserAuthenticationRequired(true)` significa que se requiere autenticación del usuario antes de que se puede usar la clave.

Una vez el `KeyGenParameterSpec` está creado, se usa para inicializar el `KeyGenerator`, que se genere una clave y almacenarla de forma segura en el dispositivo. 

## <a name="using-the-cryptoobjecthelper"></a>Uso de la CryptoObjectHelper

Ahora que el código de ejemplo encapsula gran parte de la lógica para crear un `CryptoWrapper` en el `CryptoObjectHelper` clase, vamos a revisar el código desde el principio de esta guía y usar el `CryptoObjectHelper` para el cifrado de crear e iniciar un escáner de huella digital: 

```csharp
protected void FingerPrintAuthenticationExample()
{
    const int flags = 0; /* always zero (0) */
    
    CryptoObjectHelper cryptoHelper = new CryptoObjectHelper();
    cancellationSignal = new Android.Support.V4.OS.CancellationSignal();
    
    // Using the Support Library classes for maximum reach
    FingerprintManagerCompat fingerPrintManager = FingerprintManagerCompat.From(this);
    
    // AuthCallbacks is a C# class defined elsewhere in code.
    FingerprintManagerCompat.AuthenticationCallback authenticationCallback = new MyAuthCallbackSample(this);

    // Here is where the CryptoObjectHelper builds the CryptoObject. 
    fingerprintManager.Authenticate(cryptohelper.BuildCryptoObject(), flags, cancellationSignal, authenticationCallback, null);
}
```

Ahora que hemos visto cómo crear un `CryptoObject`, permite pasar a ver cómo el `FingerprintManager.AuthenticationCallbacks` se usan para transferir los resultados del servicio de escáner de huellas digitales para una aplicación de Android.



## <a name="related-links"></a>Vínculos relacionados

- [Cifrado](https://developer.xamarin.com/api/type/Javax.Crypto.Cipher/)
- [FingerprintManager.CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [KeyGenerator](https://developer.xamarin.com/api/type/Javax.Crypto.KeyGenerator/)
- [KeyGenParameterSpec](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)
- [KeyGenParameterSpec.Builder](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.Builder.html)
- [KeyPermanentlyInvalidatedException](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)
- [KeyProperties](https://developer.android.com/reference/android/security/keystore/KeyProperties.html)
- [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
- [RFC 2315 - PCKS #7](https://tools.ietf.org/html/rfc2315)
