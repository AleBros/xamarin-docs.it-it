---
title: Creazione di un CryptoObject
ms.prod: xamarin
ms.assetid: 4D159B80-FFF4-4136-A7F0-F8A5C6B86838
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 03796af880aaef74c2d4b54007ac34ef1c5dc180
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119831"
---
# <a name="creating-a-cryptoobject"></a>Creazione di un CryptoObject

L'integrità dei risultati dell'autenticazione con impronta digitale è importante per &ndash; un'applicazione. si tratta del modo in cui l'applicazione riconosce l'identità dell'utente. È teoricamente possibile che malware di terze parti possa intercettare e manomettere i risultati restituiti dallo scanner di impronte digitali. In questa sezione verrà illustrata una tecnica per mantenere la validità dei risultati delle impronte digitali. 

È un wrapper `FingerprintManager` per le API di crittografia Java e viene usato da per proteggere l'integrità della richiesta di autenticazione. `FingerprintManager.CryptoObject` In genere, `Javax.Crypto.Cipher` un oggetto è il meccanismo per la crittografia dei risultati dello scanner di impronte digitali. L' `Cipher` oggetto stesso userà una chiave creata dall'applicazione usando le API dell'archivio chiavi Android.

Per comprendere il funzionamento di queste classi, esaminiamo prima di tutto il codice seguente che illustra come creare un oggetto `CryptoObject`e quindi spiegarlo in modo più dettagliato:

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

Il codice di esempio creerà un nuovo `Cipher` per ogni `CryptoObject`, usando una chiave creata dall'applicazione. La chiave viene identificata dalla `KEY_NAME` variabile impostata all'inizio `CryptoObjectHelper` della classe. Il metodo `GetKey` proverà a recuperare la chiave usando le API dell'archivio chiavi Android. Se la chiave non esiste, il metodo `CreateKey` creerà una nuova chiave per l'applicazione.

Viene creata un'istanza della crittografia con una chiamata `Cipher.GetInstance`a, accettando una _trasformazione_ , ovvero un valore stringa che indica alla crittografia come crittografare e decrittografare i dati. La chiamata a `Cipher.Init` comporterà il completamento dell'inizializzazione della crittografia fornendo una chiave dall'applicazione. 

È importante tenere presente che in alcuni casi Android può invalidare la chiave: 

- Una nuova impronta digitale è stata registrata con il dispositivo.
- Non sono presenti impronte digitali registrate con il dispositivo.
- L'utente ha disabilitato il blocco dello schermo.
- L'utente ha modificato il blocco dello schermo, ovvero il tipo di ScreenLock o il PIN o il modello usato.

In tal caso, `Cipher.Init` genererà un' [`KeyPermanentlyInvalidatedException`](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)operazione. Il codice di esempio precedente intercetta l'eccezione, Elimina la chiave e quindi ne crea una nuova.

Nella sezione successiva verrà illustrato come creare la chiave e archiviarla nel dispositivo.

## <a name="creating-a-secret-key"></a>Creazione di una chiave privata

La `CryptoObjectHelper` classe USA Android [`KeyGenerator`](xref:Javax.Crypto.KeyGenerator) per creare una chiave e archiviarla nel dispositivo. La `KeyGenerator` classe può creare la chiave, ma richiede alcuni metadati sul tipo di chiave da creare. Queste informazioni vengono fornite da un'istanza della [`KeyGenParameterSpec`](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html) classe. 

Viene `KeyGenerator` creata un'istanza di un `GetInstance` oggetto usando il metodo factory. Il codice di esempio usa il [_Advanced Encryption Standard_](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (_AES_) come algoritmo di crittografia. AES suddivide i dati in blocchi di dimensioni fisse e crittografa ognuno di questi blocchi.

Viene quindi creato `KeyGenParameterSpec` un oggetto `KeyGenParameterSpec.Builder`utilizzando. `KeyGenParameterSpec.Builder` Esegue il wrapping delle informazioni seguenti sulla chiave da creare:

- Nome della chiave.
- La chiave deve essere valida sia per la crittografia che per la decrittografia.
- Nel codice `BLOCK_MODE` di esempio è impostato su _Cipher Block Chaining_ (`KeyProperties.BlockModeCbc`), vale a dire che ogni blocco è XORed con il blocco precedente (creando dipendenze tra ogni blocco). 
- USA Public [_Key Cryptography Standard #7_](https://tools.ietf.org/html/rfc2315) (PKCS7) per generare i byte che comporteranno il riempimento dei blocchi per garantire la stessa dimensione. `CryptoObjectHelper`
- `SetUserAuthenticationRequired(true)`indica che è necessaria l'autenticazione utente prima di poter usare la chiave.

Una volta creato, viene usato per inizializzare la `KeyGenerator`, che genera una chiave e la archivia in modo sicuro nel dispositivo. `KeyGenParameterSpec` 

## <a name="using-the-cryptoobjecthelper"></a>Uso di CryptoObjectHelper

Ora che il codice di esempio ha incapsulato gran parte della logica per `CryptoWrapper` la creazione `CryptoObjectHelper` di un oggetto nella classe, è `CryptoObjectHelper` possibile riesaminare il codice dall'inizio di questa guida e usare per creare la crittografia e avviare uno scanner di impronta digitale: 

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

Ora che è stato illustrato come creare un `CryptoObject`, `FingerprintManager.AuthenticationCallbacks` è possibile passare a vedere come vengono usati per trasferire i risultati del servizio scanner di impronte digitali a un'applicazione Android.



## <a name="related-links"></a>Collegamenti correlati

- [Crittografia](xref:Javax.Crypto.Cipher)
- [FingerprintManager.CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [KeyGenerator](xref:Javax.Crypto.KeyGenerator)
- [KeyGenParameterSpec](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)
- [KeyGenParameterSpec.Builder](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.Builder.html)
- [KeyPermanentlyInvalidatedException](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)
- [KeyProperties](https://developer.android.com/reference/android/security/keystore/KeyProperties.html)
- [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
- [RFC 2315-PCKS #7](https://tools.ietf.org/html/rfc2315)
