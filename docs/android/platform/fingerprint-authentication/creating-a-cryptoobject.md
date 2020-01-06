---
title: Creazione di un CryptoObject
ms.prod: xamarin
ms.assetid: 4D159B80-FFF4-4136-A7F0-F8A5C6B86838
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 871058d1c128b37a0f2e77b43587139efb433de1
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487776"
---
# <a name="creating-a-cryptoobject"></a>Creazione di un CryptoObject

L'integrità dei risultati dell'autenticazione con impronta digitale è importante per un'applicazione &ndash; è il modo in cui l'applicazione conosce l'identità dell'utente. È teoricamente possibile che malware di terze parti possa intercettare e manomettere i risultati restituiti dallo scanner di impronte digitali. In questa sezione verrà illustrata una tecnica per mantenere la validità dei risultati delle impronte digitali. 

Il `FingerprintManager.CryptoObject` è un wrapper per le API di crittografia Java ed è usato dall'`FingerprintManager` per proteggere l'integrità della richiesta di autenticazione. In genere, un oggetto `Javax.Crypto.Cipher` è il meccanismo per la crittografia dei risultati dello scanner di impronte digitali. L'oggetto `Cipher` stesso userà una chiave creata dall'applicazione usando le API dell'archivio chiavi Android.

Per comprendere il funzionamento di queste classi, esaminiamo prima di tutto il codice seguente che illustra come creare un `CryptoObject`e quindi spiegarlo in modo più dettagliato:

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

Il codice di esempio creerà un nuovo `Cipher` per ogni `CryptoObject`, usando una chiave creata dall'applicazione. La chiave viene identificata dalla variabile `KEY_NAME` impostata all'inizio della classe `CryptoObjectHelper`. Il metodo `GetKey` proverà a recuperare la chiave usando le API dell'archivio chiavi Android. Se la chiave non esiste, il metodo `CreateKey` creerà una nuova chiave per l'applicazione.

Viene creata un'istanza della crittografia con una chiamata a `Cipher.GetInstance`, accettando una _trasformazione_ , ovvero un valore stringa che indica alla crittografia come crittografare e decrittografare i dati. La chiamata a `Cipher.Init` completerà l'inizializzazione della crittografia fornendo una chiave dell'applicazione. 

È importante tenere presente che in alcuni casi Android può invalidare la chiave: 

- Una nuova impronta digitale è stata registrata con il dispositivo.
- Non sono presenti impronte digitali registrate con il dispositivo.
- L'utente ha disabilitato il blocco dello schermo.
- L'utente ha modificato il blocco dello schermo, ovvero il tipo di ScreenLock o il PIN o il modello usato.

In tal caso, `Cipher.Init` genererà una [`KeyPermanentlyInvalidatedException`](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html). Il codice di esempio precedente intercetta l'eccezione, Elimina la chiave e quindi ne crea una nuova.

Nella sezione successiva verrà illustrato come creare la chiave e archiviarla nel dispositivo.

## <a name="creating-a-secret-key"></a>Creazione di una chiave privata

La classe `CryptoObjectHelper` USA Android [`KeyGenerator`](xref:Javax.Crypto.KeyGenerator) per creare una chiave e archiviarla nel dispositivo. La classe `KeyGenerator` può creare la chiave, ma richiede alcuni metadati relativi al tipo di chiave da creare. Queste informazioni vengono fornite da un'istanza della classe [`KeyGenParameterSpec`](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html) . 

Viene creata un'istanza di `KeyGenerator` usando il metodo factory `GetInstance`. Il codice di esempio usa il [_Advanced Encryption Standard_](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (_AES_) come algoritmo di crittografia. AES suddivide i dati in blocchi di dimensioni fisse e crittografa ognuno di questi blocchi.

Viene quindi creata una `KeyGenParameterSpec` usando il `KeyGenParameterSpec.Builder`. Il `KeyGenParameterSpec.Builder` include le informazioni seguenti relative alla chiave da creare:

- Nome della chiave.
- La chiave deve essere valida sia per la crittografia che per la decrittografia.
- Nel codice di esempio, il `BLOCK_MODE` è impostato sul _concatenamento dei blocchi crittografici_ (`KeyProperties.BlockModeCbc`), vale a dire che ogni blocco è XORed con il blocco precedente (creando dipendenze tra ogni blocco). 
- Il `CryptoObjectHelper` utilizza [_Public Key Cryptography Standard #7_](https://tools.ietf.org/html/rfc2315) (_PKCS7_) per generare i byte che comporteranno il riempimento dei blocchi per garantire la stessa dimensione.
- `SetUserAuthenticationRequired(true)` significa che è necessaria l'autenticazione dell'utente prima di poter usare la chiave.

Una volta creato, l'`KeyGenParameterSpec` viene usato per inizializzare il `KeyGenerator`, che genera una chiave e la archivia in modo sicuro nel dispositivo. 

## <a name="using-the-cryptoobjecthelper"></a>Uso di CryptoObjectHelper

Ora che il codice di esempio ha incapsulato gran parte della logica per la creazione di un `CryptoWrapper` nella classe `CryptoObjectHelper`, è possibile riesaminare il codice dall'inizio di questa guida e usare il `CryptoObjectHelper` per creare la crittografia e avviare uno scanner di impronta digitale: 

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

Ora che è stato illustrato come creare una `CryptoObject`, è possibile passare a vedere come vengono usati i `FingerprintManager.AuthenticationCallbacks` per trasferire i risultati del servizio scanner di impronte digitali a un'applicazione Android.

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
