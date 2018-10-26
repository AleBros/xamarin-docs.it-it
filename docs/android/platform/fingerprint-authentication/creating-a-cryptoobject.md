---
title: Creazione di un CryptoObject
ms.prod: xamarin
ms.assetid: 4D159B80-FFF4-4136-A7F0-F8A5C6B86838
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 1658934bedce11a42701eb023a42fc9e617b654d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113768"
---
# <a name="creating-a-cryptoobject"></a>Creazione di un CryptoObject

L'integrità dei risultati dell'autenticazione tramite impronta digitale è importante per un'applicazione &ndash; è come l'applicazione conosce l'identità dell'utente. È teoricamente possibile antimalware di terze parti per l'intercettazione e manomettere i risultati restituiti dallo scanner di impronta digitale. Questa sezione illustra una tecnica per mantenere la validità dei risultati di impronta digitale. 

Il `FingerprintManager.CryptoObject` è un wrapper per le API di crittografia di Java e viene utilizzato il `FingerprintManager` per proteggere l'integrità della richiesta di autenticazione. In genere, un `Javax.Crypto.Cipher` oggetto è il meccanismo per crittografare i risultati dello scanner di impronta digitale. Il `Cipher` oggetto stesso utilizzerà una chiave che viene creata dall'applicazione usando l'API di archivio chiavi Android.

Per informazioni sull'interagiscono tra queste classi sono tutte, si osserverà il codice seguente che illustra come creare un `CryptoObject`, illustreremo in dettaglio:

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

Il codice di esempio verrà creata una nuova `Cipher` per ogni `CryptoObject`, tramite una chiave che è stata creata dall'applicazione. La chiave è identificata dal `KEY_NAME` variabile che è stata impostata all'inizio del `CryptoObjectHelper` classe. Il metodo `GetKey` tenta di recuperare la chiave usando le APIs Android Keystore. Se la chiave non esiste, quindi il metodo `CreateKey` creerà una nuova chiave per l'applicazione.

La crittografia viene creata un'istanza con una chiamata a `Cipher.GetInstance`, accettando un _trasformazione_ (un valore stringa che spiega la crittografia crittografare e decrittografare i dati). La chiamata a `Cipher.Init` verrà completata l'inizializzazione della crittografia fornendo una chiave dall'applicazione. 

È importante tenere presente che esistono alcune situazioni in cui Android potrebbe invalidare la chiave: 

* Una nuova impronta digitale è stata registrata con il dispositivo.
* Non esistono Nessun impronte digitali registrate con il dispositivo.
* L'utente ha disabilitato il blocco dello schermo.
* L'utente ha modificato il blocco dello schermo (il tipo del screenlock o il PIN/modello usato).

In questo caso `Cipher.Init` genererà un [ `KeyPermanentlyInvalidatedException` ](http://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html). Il codice di esempio sopra riportato sarà intercettare questa eccezione, eliminare la chiave e quindi crearne uno nuovo.

Nella sezione successiva verrà illustrato come creare la chiave e archiviarla nel dispositivo.

## <a name="creating-a-secret-key"></a>Creazione di una chiave privata

Il `CryptoObjectHelper` classe utilizza l'Android [ `KeyGenerator` ](https://developer.xamarin.com/api/type/Javax.Crypto.KeyGenerator/) per creare una chiave e archiviarla nel dispositivo. Il `KeyGenerator` classe può creare la chiave, ma richiede alcuni metadati sul tipo di chiave da creare. Queste informazioni vengono fornite da un'istanza di [ `KeyGenParameterSpec` ](http://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html) classe. 

Oggetto `KeyGenerator` viene creata un'istanza usando il `GetInstance` metodo factory. Il codice di esempio Usa la [ _Advanced Encryption Standard_ ](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (_AES_) come l'algoritmo di crittografia. AES verrà suddividere i dati in blocchi di dimensioni fisse e crittografare ognuno di questi blocchi.

Successivamente, una `KeyGenParameterSpec` viene creato utilizzando il `KeyGenParameterSpec.Builder`. Il `KeyGenParameterSpec.Builder` incapsula le informazioni seguenti sulla chiave che deve essere creato:

* Nome della chiave.
* La chiave deve essere valida per la crittografia e decrittografia.
* Nell'esempio di codice le `BLOCK_MODE` è impostata su _Cipher Block Chaining_ (`KeyProperties.BlockModeCbc`), vale a dire che ogni blocco sia XORed con il blocco precedente (creazione di dipendenze tra ogni blocco). 
* Il `CryptoObjectHelper` viene utilizzato [ _Public Key Cryptography Standard #7 per_ ](https://tools.ietf.org/html/rfc2315) (_PKCS7_) per generare il numero di byte riempirà out i blocchi per assicurarsi che siano tutti della stessa dimensione .
* `SetUserAuthenticationRequired(true)` significa che usano l'autenticazione è necessario prima di poter usare la chiave.

Una volta il `KeyGenParameterSpec` viene creato, viene usato per inizializzare il `KeyGenerator`, che genera una chiave e archiviarlo in modo sicuro nel dispositivo. 

## <a name="using-the-cryptoobjecthelper"></a>Uso di CryptoObjectHelper

Ora che il codice di esempio ha incapsulato gran parte della logica per la creazione di un `CryptoWrapper` nella `CryptoObjectHelper` classe, è possibile rivedere il codice dall'inizio di questa Guida e usare il `CryptoObjectHelper` per creare il pacchetto di crittografia e avviare uno scanner di impronta digitale: 

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

Ora che abbiamo visto come creare un `CryptoObject`, consente di passare a vedere come il `FingerprintManager.AuthenticationCallbacks` vengono usate per trasferire i risultati del servizio scanner di impronta digitale per un'applicazione Android.



## <a name="related-links"></a>Collegamenti correlati

- [Pacchetti di crittografia](https://developer.xamarin.com/api/type/Javax.Crypto.Cipher/)
- [FingerprintManager.CryptoObject](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [KeyGenerator](https://developer.xamarin.com/api/type/Javax.Crypto.KeyGenerator/)
- [KeyGenParameterSpec](http://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)
- [KeyGenParameterSpec.Builder](http://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.Builder.html)
- [KeyPermanentlyInvalidatedException](http://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)
- [KeyProperties](http://developer.android.com/reference/android/security/keystore/KeyProperties.html)
- [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
- [RFC 2315 - PCKS #7](https://tools.ietf.org/html/rfc2315)
