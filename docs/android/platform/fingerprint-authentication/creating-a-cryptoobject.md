---
title: Creazione di un CryptoObject
ms.prod: xamarin
ms.assetid: 4D159B80-FFF4-4136-A7F0-F8A5C6B86838
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: f7a8ab7a43c0a3258cf6e737b0d235cbe7a1c747
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="creating-a-cryptoobject"></a>Creazione di un CryptoObject

L'integrità dei risultati dell'autenticazione delle impronte digitali è importante per un'applicazione &ndash; è come l'applicazione conosce l'identità dell'utente. È teoricamente possibile antimalware di terze parti per l'intercettazione e manomettere i risultati restituiti dallo scanner impronta digitale. In questa sezione illustra una tecnica per mantenere la validità dei risultati delle impronte digitali. 

Il `FingerprintManager.CryptoObject` è un wrapper per le API di crittografia di Java e viene utilizzato il `FingerprintManager` per proteggere l'integrità della richiesta di autenticazione. In genere, un `Javax.Crypto.Cipher` oggetto è il meccanismo per crittografare i risultati dello scanner impronta digitale. Il `Cipher` oggetto stesso utilizzerà una chiave che viene creata dall'applicazione utilizzando il file keystore Android API.

Per comprendere la modalità di funzionamento di tutte le classi, innanzitutto esaminiamo il codice seguente che illustra come creare un `CryptoObject`e quindi descrivere in dettaglio:

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

Il codice di esempio verrà creato un nuovo `Cipher` per ogni `CryptoObject`, tramite una chiave che è stata creata dall'applicazione. La chiave è identificata dal `KEY_NAME` variabile che è stata impostata all'inizio della `CryptoObjectHelper` classe. Il metodo `GetKey` tenta di recuperare la chiave usando le APIs Keystore Android. Se la chiave non esiste, quindi il metodo `CreateKey` creerà una nuova chiave per l'applicazione.

La crittografia viene creata con una chiamata a `Cipher.GetInstance`, considerando un _trasformazione_ (un valore stringa che spiega la crittografia crittografare e decrittografare i dati). La chiamata a `Cipher.Init` verrà completata l'inizializzazione della crittografia fornendo una chiave dall'applicazione. 

È importante tenere presente che esistono alcune situazioni in cui Android potrebbe invalidare la chiave: 

* Un nuovo ID che è stato registrato con il dispositivo.
* Non esistono alcun le impronte digitali registrate con il dispositivo.
* L'utente abbia disabilitato il blocco dello schermo.
* L'utente ha modificato il blocco dello schermo (il tipo di screenlock o il PIN o modello utilizzato).

In questo caso, `Cipher.Init` genererà un [ `KeyPermanentlyInvalidatedException` ](http://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html). Il codice di esempio precedente verrà intercettare l'eccezione, eliminare la chiave e quindi crearne uno nuovo.

La sezione successiva verrà illustrato come creare la chiave e archiviarlo sul dispositivo.

## <a name="creating-a-secret-key"></a>Creazione di una chiave segreta

Il `CryptoObjectHelper` classe utilizza il Android [ `KeyGenerator` ](https://developer.xamarin.com/api/type/Javax.Crypto.KeyGenerator/) per creare una chiave e archiviarlo sul dispositivo. La `KeyGenerator` classe può creare la chiave, ma richiede alcuni metadati sul tipo di chiave da creare. Queste informazioni vengono fornite da un'istanza di [ `KeyGenParameterSpec` ](http://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html) classe. 

Oggetto `KeyGenerator` viene creata un'istanza utilizzando il `GetInstance` metodo factory. Il codice di esempio utilizza il [ _Advanced Encryption Standard_ ](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (_AES_) come algoritmo di crittografia. AES verrà suddividere i dati in blocchi di dimensioni fisse e ognuno di tali blocchi della crittografia.

Successivamente, un `KeyGenParameterSpec` viene creato utilizzando il `KeyGenParameterSpec.Builder`. Il `KeyGenParameterSpec.Builder` include le seguenti informazioni sulla chiave che si desidera creare:

* Nome della chiave.
* La chiave deve essere valida per la crittografia e decrittografia.
* Nell'esempio di codice il `BLOCK_MODE` è impostato su _Cipher Block Chaining_ (`KeyProperties.BlockModeCbc`), vale a dire che ogni blocco è XORed con il blocco precedente (creazione di relazioni tra ogni blocco). 
* Il `CryptoObjectHelper` Usa [ _Public Key Cryptography Standard #7 per_ ](https://tools.ietf.org/html/rfc2315) (_PKCS7_) per generare i byte che verranno riempire i blocchi per assicurarsi che siano tutti della stessa dimensione .
* `SetUserAuthenticationRequired(true)` indica che l'autenticazione utente è necessario prima di poter utilizzare la chiave.

Una volta il `KeyGenParameterSpec` viene creato, viene utilizzato per inizializzare il `KeyGenerator`, che genera una chiave e archiviare in modo sicuro nel dispositivo. 

## <a name="using-the-cryptoobjecthelper"></a>Utilizzo di CryptoObjectHelper

Ora che il codice di esempio è incapsulata la logica per la creazione di un `CryptoWrapper` nel `CryptoObjectHelper` classe, rivedere il codice dall'inizio di questa guida si e utilizzare il `CryptoObjectHelper` per creare la crittografia e avviare uno scanner di impronta digitale: 

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

Ora che abbiamo visto come creare un `CryptoObject`, consente di spostarsi vedere come il `FingerprintManager.AuthenticationCallbacks` vengono utilizzati per trasferire i risultati di impronta digitale scanner servizio a un'applicazione Android.



## <a name="related-links"></a>Collegamenti correlati

- [Pacchetto di crittografia](https://developer.xamarin.com/api/type/Javax.Crypto.Cipher/)
- [FingerprintManager.CryptoObject](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [KeyGenerator](https://developer.xamarin.com/api/type/Javax.Crypto.KeyGenerator/)
- [KeyGenParameterSpec](http://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)
- [KeyGenParameterSpec.Builder](http://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.Builder.html)
- [KeyPermanentlyInvalidatedException](http://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)
- [KeyProperties](http://developer.android.com/reference/android/security/keystore/KeyProperties.html)
- [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
- [RFC 2315 - PCKS #7](https://tools.ietf.org/html/rfc2315)
