---
title: Creazione di un oggetto CryptoObjectCreating a CryptoObject
ms.prod: xamarin
ms.assetid: 4D159B80-FFF4-4136-A7F0-F8A5C6B86838
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 871058d1c128b37a0f2e77b43587139efb433de1
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "75487776"
---
# <a name="creating-a-cryptoobject"></a>Creazione di un oggetto CryptoObjectCreating a CryptoObject

L'integrità dei risultati dell'autenticazione &ndash; delle impronte digitali è importante per un'applicazione, ma il modo in cui l'applicazione conosce l'identità dell'utente. È teoricamente possibile per il malware di terze parti per intercettare e manomettere i risultati restituiti dallo scanner di impronte digitali. In questa sezione verrà illustrata una tecnica per preservare la validità dei risultati delle impronte digitali. 

Il `FingerprintManager.CryptoObject` è un wrapper per le API di crittografia `FingerprintManager` Java e viene utilizzato da per proteggere l'integrità della richiesta di autenticazione. In genere, un `Javax.Crypto.Cipher` oggetto è il meccanismo per crittografare i risultati dello scanner di impronte digitali. L'oggetto stesso utilizzerà una chiave creata dall'applicazione utilizzando le API keystore Android.The `Cipher` object itself will use a key that is created by the application using the Android keystore APIs.

Per comprendere il modo in cui queste classi funzionano tutte insieme, `CryptoObject`esaminiamo innanzitutto il codice seguente che illustra come creare un oggetto , quindi spiegare in modo più dettagliato:

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
            cipher.Init(CipherMode.EncryptMode, key);
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

Il codice di esempio `Cipher` creerà un nuovo per ogni `CryptoObject`, utilizzando una chiave creata dall'applicazione. La chiave è `KEY_NAME` identificata dalla variabile impostata `CryptoObjectHelper` all'inizio della classe. Il `GetKey` metodo tenterà di recuperare la chiave utilizzando le API di Android Keystore.The method will try to retrieve the key using the Android Keystore APIs. Se la chiave non esiste, `CreateKey` il metodo creerà una nuova chiave per l'applicazione.

Viene creata un'istanza della `Cipher.GetInstance`crittografia con una chiamata a , che richiede una _trasformazione_ (un valore stringa che indica alla crittografia come crittografare e decrittografare i dati). La chiamata `Cipher.Init` a completerà l'inizializzazione della crittografia fornendo una chiave dall'applicazione. 

È importante rendersi conto che ci sono alcune situazioni in cui Android può invalidare la chiave: 

- Una nuova impronta digitale è stata registrata con il dispositivo.
- Non ci sono impronte digitali registrate con il dispositivo.
- L'utente ha disattivato il blocco schermo.
- L'utente ha modificato il blocco schermo (il tipo di screenlock o il PIN/modello utilizzato).

Quando questo `Cipher.Init` accade, [`KeyPermanentlyInvalidatedException`](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)lancerà un . Il codice di esempio precedente trappingrà tale eccezione, eliminerà la chiave e ne creerà una nuova.

Nella sezione successiva verrà illustrato come creare la chiave e archiviarla nel dispositivo.

## <a name="creating-a-secret-key"></a>Creazione di una chiave segretaCreating a Secret Key

La `CryptoObjectHelper` classe utilizza [`KeyGenerator`](xref:Javax.Crypto.KeyGenerator) Android per creare una chiave e archiviarla nel dispositivo. La `KeyGenerator` classe può creare la chiave, ma richiede alcuni metadati sul tipo di chiave da creare. Queste informazioni vengono fornite da [`KeyGenParameterSpec`](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html) un'istanza della classe. 

Viene `KeyGenerator` creata un'istanza di Un oggetto utilizzando il `GetInstance` metodo factory. Il codice di esempio utilizza [_Advanced Encryption Standard_](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (_AES_) come algoritmo di crittografia. AES suddividerà i dati in blocchi di dimensioni fisse e crittograferà ciascuno di questi blocchi.

Successivamente, `KeyGenParameterSpec` viene creato `KeyGenParameterSpec.Builder`un oggetto utilizzando il file . Il `KeyGenParameterSpec.Builder` esegue il wrapping delle seguenti informazioni sulla chiave che deve essere creata:

- Nome della chiave.
- La chiave deve essere valida sia per la crittografia che per la decrittografia.
- Nel codice di `BLOCK_MODE` esempio l'oggetto è`KeyProperties.BlockModeCbc`impostato su Cipher Block _Chaining_ ( ), il che significa che ogni blocco è XORed con il blocco precedente (creando dipendenze tra ogni blocco). 
- Utilizza `CryptoObjectHelper` [_Public Key Cryptography Standard #7_](https://tools.ietf.org/html/rfc2315) (_PKCS7_) per generare i byte che riempiranno i blocchi per garantire che siano tutti della stessa dimensione.
- `SetUserAuthenticationRequired(true)`significa che è necessaria l'autenticazione utente prima di poter utilizzare la chiave.

`KeyGenParameterSpec` Una volta creato, viene utilizzato `KeyGenerator`per inizializzare il , che genererà una chiave e archivierà in modo sicuro sul dispositivo. 

## <a name="using-the-cryptoobjecthelper"></a>Utilizzo di CryptoObjectHelperUsing the CryptoObjectHelper

Ora che il codice di esempio ha incapsulato `CryptoObjectHelper` gran parte della logica per la creazione di un `CryptoObjectHelper` `CryptoWrapper` nella classe, è possibile rivedere il codice dall'inizio di questa guida e utilizzare il per creare il Cipher e avviare uno scanner di impronte digitali: 

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

Ora che abbiamo visto come `CryptoObject`creare un , consente `FingerprintManager.AuthenticationCallbacks` di passare a vedere come vengono utilizzati per trasferire i risultati del servizio di scanner di impronte digitali a un'applicazione Android.

## <a name="related-links"></a>Collegamenti correlati

- [Cifrario](xref:Javax.Crypto.Cipher)
- [FingerprintManager.CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [Generatore di tasti](xref:Javax.Crypto.KeyGenerator)
- [KeyGenParameterSpec](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)
- [KeyGenParameterSpec.Builder](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.Builder.html)
- [KeyPermanentlyInvalidatedException (eccezione)KeyPermanentlyInvalidatedException](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)
- [Proprietà chiave](https://developer.android.com/reference/android/security/keystore/KeyProperties.html)
- [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
- [RFC 2315 - #7 PCKS](https://tools.ietf.org/html/rfc2315)
