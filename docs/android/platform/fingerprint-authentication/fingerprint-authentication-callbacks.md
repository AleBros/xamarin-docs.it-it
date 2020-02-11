---
title: Risposta ai callback di autenticazione
ms.prod: xamarin
ms.assetid: 6533AFC9-1A1C-4897-A154-4D4ECFE27761
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/06/2017
ms.openlocfilehash: 8dc06740355bd95828e1a1bd8d9d15a2ef37e6b2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027527"
---
# <a name="responding-to-authentication-callbacks"></a>Risposta ai callback di autenticazione

Lo scanner di impronte digitali viene eseguito in background sul proprio thread e, al termine, segnalerà i risultati dell'analisi richiamando un metodo di `FingerprintManager.AuthenticationCallback` sul thread UI. Un'applicazione Android deve fornire il proprio gestore che estende questa classe astratta, implementando tutti i metodi seguenti:

- **`OnAuthenticationError(int errorCode, ICharSequence errString)`** &ndash; chiamato quando si verifica un errore irreversibile. Per risolvere il problema non è più possibile eseguire un'applicazione o un utente, ad eccezione di riprovare.
- **`OnAuthenticationFailed()`** &ndash; questo metodo viene richiamato quando un'impronta digitale è stata rilevata ma non riconosciuta dal dispositivo.
- **`OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)`** &ndash; chiamato quando si verifica un errore reversibile, ad esempio quando il dito viene sottoposto a scorrimento rapido sullo scanner.
- **`OnAuthenticationSucceeded(FingerprintManagerCompati.AuthenticationResult result)`** &ndash; questa operazione viene chiamata quando è stata riconosciuta un'impronta digitale.

Se è stato usato un `CryptoObject` durante la chiamata di `Authenticate`, è consigliabile chiamare `Cipher.DoFinal` in `OnAuthenticationSuccessful`.
`DoFinal` genererà un'eccezione se la crittografia è stata manomessa o inizializzata in modo errato, a indicare che il risultato dello scanner di impronta digitale potrebbe essere stato manomesso all'esterno dell'applicazione.

> [!NOTE]
> È consigliabile lasciare la classe di callback relativamente leggero e priva di logica specifica dell'applicazione. I callback devono fungere da "Traffic Cop" tra l'applicazione Android e i risultati dello scanner di impronte digitali.

## <a name="a-sample-authentication-callback-handler"></a>Gestore di callback di autenticazione di esempio

La classe seguente è un esempio di un'implementazione di `FingerprintManager.AuthenticationCallback` minima: 

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

`OnAuthenticationSucceeded` verifica se è stato fornito un `Cipher` per `FingerprintManager` quando è stato richiamato `Authentication`. In tal caso, viene chiamato il metodo `DoFinal` sulla crittografia. In questo modo, il `Cipher`viene chiuso, ripristinando lo stato originale. Se si è verificato un problema con la crittografia, `DoFinal` genererà un'eccezione e il tentativo di autenticazione verrà considerato non riuscito.

I callback `OnAuthenticationError` e `OnAuthenticationHelp` ricevono ciascuno un valore integer che indica il problema. Nella sezione seguente vengono illustrati i possibili codici di errore o di guida. I due callback servono a scopi simili &ndash; per informare l'applicazione che l'autenticazione con impronta digitale ha avuto esito negativo. Il modo in cui si differenziano è in gravità. `OnAuthenticationHelp` è un errore reversibile da parte dell'utente, ad esempio un rapido scorrimento dell'impronta digitale; `OnAuthenticationError` è più un errore grave, ad esempio uno scanner di impronte digitali danneggiato.

Si noti che `OnAuthenticationError` verrà richiamato quando l'analisi dell'impronta digitale viene annullata tramite il messaggio di `CancellationSignal.Cancel()`. Il valore del parametro `errMsgId` sarà 5 (`FingerprintState.ErrorCanceled`). A seconda dei requisiti, un'implementazione del `AuthenticationCallbacks` può considerare questa situazione in modo diverso rispetto agli altri errori. 

`OnAuthenticationFailed` viene richiamato quando l'impronta digitale è stata analizzata correttamente ma non corrisponde ad alcuna impronta digitale registrata con il dispositivo. 

## <a name="help-codes-and-error-message-ids"></a>Codici della guida e ID del messaggio di errore 

È possibile trovare un elenco e una descrizione dei codici di errore e dei codici della guida nella [documentazione di Android SDK](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html#FINGERPRINT_ACQUIRED_GOOD) per la classe FingerprintManager. Xamarin.Android rappresenta questi valori con il `Android.Hardware.Fingerprints.FingerprintState` enum:

- **`AcquiredGood`** &ndash; (valore 0) l'immagine acquisita è stata corretta.

- **`AcquiredImagerDirty`** &ndash; (valore 3) l'immagine dell'impronta digitale è troppo rumorosa a causa di una sporcizia sospetta o rilevata nel sensore. Ad esempio, è ragionevole restituire questo risultato dopo più `AcquiredInsufficient` o il rilevamento effettivo di sporcizia sul sensore (pixel bloccati, fasce e così via). L'utente deve intervenire per pulire il sensore quando viene restituito.

- **`AcquiredInsufficient`** &ndash; (valore 2) l'immagine dell'impronta digitale è troppo rumorosa per essere elaborata a causa di una condizione rilevata (ad esempio, un'interfaccia a secco) o di un sensore eventualmente modificato (vedere `AcquiredImagerDirty`.

- **`AcquiredPartial`** &ndash; (valore 1) è stata rilevata solo un'immagine di impronta digitale parziale. Durante la registrazione, l'utente deve essere informato sulle operazioni da eseguire per risolvere il problema, ad esempio &ldquo;premere saldamente sul sensore.&rdquo;

- **`AcquiredTooFast`** &ndash; (valore 5) l'immagine dell'impronta digitale è incompleta a causa del movimento rapido. Sebbene sia particolarmente appropriato per i sensori di matrici lineari, questo problema può verificarsi anche se il dito è stato spostato durante l'acquisizione. All'utente deve essere richiesto di spostare il dito più lentamente (lineare) o lasciare il dito sul sensore più a lungo.

- **`AcquiredToSlow`** &ndash; (valore 4) l'immagine dell'impronta digitale è stata illeggibile a causa della mancanza di movimento. Questa operazione è più appropriata per i sensori di matrici lineari che richiedono un movimento di scorrimento.

- **`ErrorCanceled`** &ndash; (valore 5) l'operazione è stata annullata perché il sensore di impronta digitale non è disponibile. Ad esempio, questo problema può verificarsi quando l'utente è cambiato, il dispositivo è bloccato o un'altra operazione in sospeso ne impedisce o Disabilita.

- **`ErrorHwUnavailable`** &ndash; (valore 1) l'hardware non è disponibile. Riprovare più tardi.

- **`ErrorLockout`** &ndash; (valore 7) l'operazione è stata annullata perché l'API è bloccata a causa di un numero eccessivo di tentativi.

- viene restituito lo stato di errore **`ErrorNoSpace`** &ndash; (valore 4) per operazioni come la registrazione; non è possibile completare l'operazione perché rimane disponibile spazio di archiviazione sufficiente per completare l'operazione.

- **`ErrorTimeout`** &ndash; (valore 3) lo stato di errore restituito quando la richiesta corrente è stata eseguita troppo a lungo. Questa operazione è destinata a impedire che i programmi attendano il sensore di impronte digitali a tempo indefinito. Il timeout è specifico della piattaforma e del sensore, ma in genere è di circa 30 secondi.

- viene restituito lo stato di errore **`ErrorUnableToProcess`** &ndash; (valore 2) quando il sensore non è stato in grado di elaborare l'immagine corrente.

## <a name="related-links"></a>Collegamenti correlati

- [Crittografia](https://docs.oracle.com/javase/7/docs/api/javax/crypto/Cipher.html)
- [AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [AuthenticationCallback](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.AuthenticationCallback.html)
