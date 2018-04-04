---
title: Crear un CryptoObject
ms.prod: xamarin
ms.assetid: 4D159B80-FFF4-4136-A7F0-F8A5C6B86838
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: f7a8ab7a43c0a3258cf6e737b0d235cbe7a1c747
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="creating-a-cryptoobject"></a>Crear un CryptoObject

La integridad de los resultados de la autenticación de huellas digitales es importante para una aplicación &ndash; es cómo la aplicación conoce la identidad del usuario. Teóricamente, es posible en busca de malware de terceros interceptar y alterar los resultados devueltos por el analizador de huellas digitales. En esta sección trataremos una de estas técnicas para conservar la validez de los resultados de la huella digital. 

El `FingerprintManager.CryptoObject` es un contenedor alrededor de las API de criptografía de Java y es utilizada por el `FingerprintManager` para proteger la integridad de la solicitud de autenticación. Normalmente, un `Javax.Crypto.Cipher` objeto es el mecanismo para cifrar los resultados del escáner de huellas digitales. El `Cipher` propio objeto usará una clave que se crea según la aplicación mediante la API de almacén de claves Android.

Para entender cómo funcionan conjuntamente todas estas clases, veamos primero el código siguiente que se muestra cómo crear un `CryptoObject`y, a continuación, se explica con más detalle:

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

El código de ejemplo creará un nuevo `Cipher` para cada `CryptoObject`, utilizando una clave que se creó con la aplicación. La clave se identifica mediante el `KEY_NAME` variable que se estableció al principio de la `CryptoObjectHelper` clase. El método `GetKey` se intente y recuperar la clave mediante las APIs Keystore Android. Si la clave no existe, a continuación, el método `CreateKey` creará una nueva clave para la aplicación.

El cifrado se crea una instancia con una llamada a `Cipher.GetInstance`, teniendo un _transformación_ (un valor de cadena que indica el cifrado cómo cifrar y descifrar los datos). La llamada a `Cipher.Init` se completará la inicialización del cifrado proporcionando una clave de la aplicación. 

Es importante tener en cuenta que existen algunas situaciones donde Android puede invalidar la clave: 

* Una huella digital del nuevo se ha inscrito con el dispositivo.
* No hay ningún las huellas digitales que están inscritos con el dispositivo.
* El usuario ha deshabilitado el bloqueo de pantalla.
* El usuario ha cambiado el bloqueo de pantalla (el tipo de la screenlock o el PIN o patrón que se usa).

Cuando esto sucede, `Cipher.Init` producirá un [ `KeyPermanentlyInvalidatedException` ](http://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html). El código de ejemplo anterior se intercepta esa excepción, elimine la clave y, a continuación, cree uno nuevo.

La siguiente sección veremos cómo crear la clave y almacenar en el dispositivo.

## <a name="creating-a-secret-key"></a>Crear una clave secreta

El `CryptoObjectHelper` clase usa la Android [ `KeyGenerator` ](https://developer.xamarin.com/api/type/Javax.Crypto.KeyGenerator/) para crear una clave y almacenarlo en el dispositivo. La `KeyGenerator` clase puede crear la clave, pero necesita algunos metadatos sobre el tipo de clave que se va a crear. Esta información se proporciona una instancia de la [ `KeyGenParameterSpec` ](http://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html) clase. 

A `KeyGenerator` se crea una instancia mediante el `GetInstance` método de fábrica. El código de ejemplo utiliza la [ _Advanced Encryption Standard_ ](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (_AES_) como el algoritmo de cifrado. AES se dividir los datos en bloques de tamaño fijo y se cifrarán cada uno de los bloques.

Después, un `KeyGenParameterSpec` se crea utilizando el `KeyGenParameterSpec.Builder`. El `KeyGenParameterSpec.Builder` contiene la siguiente información acerca de la clave que se pueden crear:

* Nombre de la clave.
* La clave debe ser válida para el cifrado y descifrado.
* En el código de ejemplo del `BLOCK_MODE` está establecido en _encadenamiento de bloques de cifrado_ (`KeyProperties.BlockModeCbc`), lo que significa que cada bloque se compara mediante XOR con el bloque anterior (creación de dependencias entre cada bloque). 
* El `CryptoObjectHelper` utiliza [ _pública clave de criptografía estándar #7_ ](https://tools.ietf.org/html/rfc2315) (_PKCS7_) para generar los bytes que rellenará fuera de los bloques para asegurarse de que son todas del mismo tamaño .
* `SetUserAuthenticationRequired(true)` significa que la autenticación usuario es necesaria para poder usar la clave.

Una vez el `KeyGenParameterSpec` está creado, se utiliza para inicializar el `KeyGenerator`, lo que generará una clave y almacénelas en el dispositivo. 

## <a name="using-the-cryptoobjecthelper"></a>Uso de la CryptoObjectHelper

Ahora que el código de ejemplo encapsula gran parte de la lógica para crear un `CryptoWrapper` en el `CryptoObjectHelper` clase, vamos a revisar el código desde el principio de esta guía y usar el `CryptoObjectHelper` para crear el cifrado e iniciar un escáner de huellas digitales: 

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

Ahora que hemos visto cómo crear un `CryptoObject`, permite pasar para ver cómo la `FingerprintManager.AuthenticationCallbacks` se usan para transferir los resultados del servicio de escáner de huellas digitales para una aplicación Android.



## <a name="related-links"></a>Vínculos relacionados

- [Cipher](https://developer.xamarin.com/api/type/Javax.Crypto.Cipher/)
- [FingerprintManager.CryptoObject](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [KeyGenerator](https://developer.xamarin.com/api/type/Javax.Crypto.KeyGenerator/)
- [KeyGenParameterSpec](http://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)
- [KeyGenParameterSpec.Builder](http://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.Builder.html)
- [KeyPermanentlyInvalidatedException](http://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)
- [KeyProperties](http://developer.android.com/reference/android/security/keystore/KeyProperties.html)
- [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
- [RFC 2315 - PCKS #7](https://tools.ietf.org/html/rfc2315)
