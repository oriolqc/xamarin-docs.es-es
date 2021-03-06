---
title: Creación de un CryptoObject
ms.prod: xamarin
ms.assetid: 4D159B80-FFF4-4136-A7F0-F8A5C6B86838
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 8e5d4cf50874a0976c1dd10e35e7bd84518f14c4
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511155"
---
# <a name="creating-a-cryptoobject"></a>Creación de un CryptoObject

La integridad de los resultados de la autenticación mediante huellas digitales es &ndash; importante para una aplicación, es cómo la aplicación conoce la identidad del usuario. Teóricamente, es posible que un malware de terceros intercepte y manipule los resultados devueltos por el escáner de huellas digitales. En esta sección se describe una técnica para conservar la validez de los resultados de la huella digital. 

Es un contenedor alrededor de las API de criptografía de Java y lo usa `FingerprintManager` para proteger la integridad de la solicitud de autenticación. `FingerprintManager.CryptoObject` Normalmente, un `Javax.Crypto.Cipher` objeto es el mecanismo de cifrado de los resultados del escáner de huellas digitales. El `Cipher` propio objeto usará una clave creada por la aplicación mediante las API de almacén de claves de Android.

Para comprender cómo funcionan conjuntamente estas clases, echemos un `CryptoObject`vistazo primero al código siguiente, que muestra cómo crear y, a continuación, explicar con más detalle:

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

El código de ejemplo creará un `Cipher` nuevo para `CryptoObject`cada, mediante una clave creada por la aplicación. La clave se identifica mediante la `KEY_NAME` variable que se estableció al principio de la `CryptoObjectHelper` clase. El método `GetKey` probará y recuperará la clave mediante las API de almacén de claves de Android. Si la clave no existe, el método `CreateKey` creará una nueva clave para la aplicación.

Se crea una instancia del cifrado con una `Cipher.GetInstance`llamada a, tomando una _transformación_ (valor de cadena que indica al cifrado cómo cifrar y descifrar los datos). La llamada a `Cipher.Init` completará la inicialización del cifrado proporcionando una clave de la aplicación. 

Es importante saber que hay algunas situaciones en las que Android puede invalidar la clave: 

* Se ha inscrito una nueva huella digital con el dispositivo.
* No hay huellas digitales inscritas con el dispositivo.
* El usuario ha deshabilitado el bloqueo de pantalla.
* El usuario ha cambiado el bloqueo de pantalla (el tipo de screenlock o el PIN o patrón usado).

Cuando esto sucede, `Cipher.Init` producirá una [`KeyPermanentlyInvalidatedException`](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)excepción. En el código de ejemplo anterior se detectará esa excepción, se eliminará la clave y, a continuación, se creará una nueva.

En la siguiente sección se explica cómo crear la clave y almacenarla en el dispositivo.

## <a name="creating-a-secret-key"></a>Creación de una clave secreta

La `CryptoObjectHelper` clase usa Android [`KeyGenerator`](xref:Javax.Crypto.KeyGenerator) para crear una clave y almacenarla en el dispositivo. La `KeyGenerator` clase puede crear la clave, pero necesita algunos metadatos sobre el tipo de clave que se va a crear. Esta información la proporciona una instancia de la [`KeyGenParameterSpec`](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html) clase. 

Se `KeyGenerator` crea una instancia de mediante `GetInstance` el Factory Method. El código de ejemplo utiliza el [_estándar de cifrado avanzado_](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (_AES_) como algoritmo de cifrado. AES dividirá los datos en bloques de tamaño fijo y cifrará cada uno de estos bloques.

A continuación, `KeyGenParameterSpec` se crea una con `KeyGenParameterSpec.Builder`el. `KeyGenParameterSpec.Builder` Incluye la siguiente información sobre la clave que se va a crear:

* Nombre de la clave.
* La clave debe ser válida para el cifrado y el descifrado.
* En el código `BLOCK_MODE` de ejemplo, se establece en encadenamiento de`KeyProperties.BlockModeCbc` _bloques_ de cifrado (), lo que significa que cada bloque es XORed con el bloque anterior (creando dependencias entre cada bloque). 
* Usa `CryptoObjectHelper` el [_estándar de criptografía de clave pública #7_](https://tools.ietf.org/html/rfc2315) (_pkcs7_) para generar los bytes que rellenarán los bloques para asegurarse de que tienen el mismo tamaño.
* `SetUserAuthenticationRequired(true)`significa que se requiere la autenticación del usuario antes de que se pueda usar la clave.

Una vez que `KeyGenerator` secrea,seusaparainicializar,quegeneraráunaclaveylaalmacenarádeformasegura`KeyGenParameterSpec` en el dispositivo. 

## <a name="using-the-cryptoobjecthelper"></a>Usar CryptoObjectHelper

Ahora que el código de ejemplo ha encapsulado gran parte de la lógica para `CryptoWrapper` crear un `CryptoObjectHelper` en la clase, vamos `CryptoObjectHelper` a revisar el código desde el inicio de esta guía y usar para crear el cifrado e iniciar un escáner de huellas digitales: 

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

Ahora que hemos aprendido a crear un `CryptoObject`, vamos a ver `FingerprintManager.AuthenticationCallbacks` cómo se usan para transferir los resultados del servicio de análisis de huellas digitales a una aplicación de Android.



## <a name="related-links"></a>Vínculos relacionados

- [Cifra](xref:Javax.Crypto.Cipher)
- [FingerprintManager.CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [KeyGenerator](xref:Javax.Crypto.KeyGenerator)
- [KeyGenParameterSpec](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)
- [KeyGenParameterSpec.Builder](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.Builder.html)
- [KeyPermanentlyInvalidatedException](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)
- [KeyProperties](https://developer.android.com/reference/android/security/keystore/KeyProperties.html)
- [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
- [RFC 2315-PCKS #7](https://tools.ietf.org/html/rfc2315)
