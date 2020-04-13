---
title: Risposta ai callback di autenticazione
ms.prod: xamarin
ms.assetid: 6533AFC9-1A1C-4897-A154-4D4ECFE27761
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/06/2017
ms.openlocfilehash: 8dc06740355bd95828e1a1bd8d9d15a2ef37e6b2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027527"
---
# <a name="responding-to-authentication-callbacks"></a>Risposta ai callback di autenticazione

Lo scanner di impronte digitali viene eseguito in background sul proprio thread e al termine `FingerprintManager.AuthenticationCallback` segnalerà i risultati dell'analisi richiamando un metodo di nel thread dell'interfaccia utente. Un'applicazione Android deve fornire il proprio gestore che estende questa classe astratta, implementando tutti i metodi seguenti:An Android application must provide its own handler which extends this abstract class, implementing all the following methods:

- **`OnAuthenticationError(int errorCode, ICharSequence errString)`**&ndash; Chiamato quando si verifica un errore irreversibile. Non c'è niente di più un'applicazione o un utente può fare per correggere la situazione tranne possibilmente riprovare.
- **`OnAuthenticationFailed()`**&ndash; Questo metodo viene richiamato quando un'impronta digitale è stata rilevata ma non riconosciuta dal dispositivo.
- **`OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)`**&ndash; Chiamato quando si verifica un errore recuperabile, ad esempio il dito che viene passato per passare rapidamente allo scanner.
- **`OnAuthenticationSucceeded(FingerprintManagerCompati.AuthenticationResult result)`**&ndash; Viene chiamato quando viene riconosciuta un'impronta digitale.

Se `CryptoObject` è stato `Authenticate`utilizzato un oggetto `Cipher.DoFinal` durante `OnAuthenticationSuccessful`la chiamata a , si consiglia di chiamare in .
`DoFinal`genererà un'eccezione se la crittografia è stata manomessa o inizializzata in modo non corretto, a indicare che il risultato dello scanner di impronte digitali potrebbe essere stato manomesso all'esterno dell'applicazione.

> [!NOTE]
> È consigliabile mantenere la classe di callback relativamente leggera e priva di logica specifica dell'applicazione. I callback devono fungere da "poliziotto del traffico" tra l'applicazione Android e i risultati dello scanner di impronte digitali.

## <a name="a-sample-authentication-callback-handler"></a>Un gestore di callback di autenticazione di esempioA Sample Authentication Callback Handler

La classe seguente è un `FingerprintManager.AuthenticationCallback` esempio di un'implementazione minima:The following class is an example of a minimal implementation: 

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

`OnAuthenticationSucceeded`controlla per vedere `Cipher` se `FingerprintManager` un `Authentication` è stato fornito a quando è stato richiamato. In tal `DoFinal` caso, il metodo viene chiamato sul cifrario. In questo `Cipher`modo si chiude l'oggetto , ripristinandolo allo stato originale. Se si è verificato un `DoFinal` problema con la crittografia, verrà generata un'eccezione e il tentativo di autenticazione deve essere considerato non riuscito.

I `OnAuthenticationError` `OnAuthenticationHelp` callback e ricevono ciascuno un numero intero che indica il problema. Nella sezione seguente vengono illustrati tutti i possibili codici di errore o della Guida. I due callback hanno &ndash; uno scopo simile per informare l'applicazione che l'autenticazione tramite impronta digitale non è riuscita. Come si differenziano è in gravità. `OnAuthenticationHelp`è un errore recuperabile dall'utente, ad esempio facendo scorrere l'impronta digitale troppo velocemente; `OnAuthenticationError` è più un errore grave, ad esempio uno scanner di impronte digitali danneggiato.

Si `OnAuthenticationError` noti che verrà richiamato quando la `CancellationSignal.Cancel()` scansione delle impronte digitali viene annullata tramite il messaggio. Il `errMsgId` valore del parametro`FingerprintState.ErrorCanceled`sarà 5 ( ). A seconda dei requisiti, `AuthenticationCallbacks` un'implementazione del può trattare questa situazione in modo diverso rispetto agli altri errori. 

`OnAuthenticationFailed`viene richiamato quando l'impronta digitale è stata analizzata correttamente ma non corrisponde ad alcuna impronta digitale registrata con il dispositivo. 

## <a name="help-codes-and-error-message-ids"></a>Codici della Guida e ID dei messaggi di errore 

Un elenco e una descrizione dei codici di errore e dei codici della Guida sono disponibili nella [documentazione](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html#FINGERPRINT_ACQUIRED_GOOD) di Android SDK per la classe FingerprintManager. Xamarin.Android rappresenta questi `Android.Hardware.Fingerprints.FingerprintState` valori con l'enumerazione:

- **`AcquiredGood`**&ndash; (valore 0) L'immagine acquisita era buona.

- **`AcquiredImagerDirty`**&ndash; (valore 3) L'immagine dell'impronta digitale era troppo rumorosa a causa dello sporco sospetto o rilevato sul sensore. Ad esempio, è ragionevole restituire questo `AcquiredInsufficient` dopo il rilevamento multiplo o effettivo di sporcizia sul sensore (pixel bloccati, fasce, ecc.). L'utente deve intervenire per pulire il sensore quando questo viene restituito.

- **`AcquiredInsufficient`**&ndash; (valore 2) L'immagine dell'impronta digitale era troppo rumorosa per essere lavorata a `AcquiredImagerDirty`causa di una condizione rilevata (cioè pelle secca) o di un sensore eventualmente sporco (vedere .

- **`AcquiredPartial`**&ndash; (valore 1) È stata rilevata solo un'immagine di impronte digitali parziale. Durante la registrazione, l'utente deve essere informato su ciò che &ldquo;deve accadere per risolvere questo problema, ad esempio, premere con fermezza sul sensore.&rdquo;

- **`AcquiredTooFast`**&ndash; (valore 5) L'immagine dell'impronta digitale era incompleta a causa del movimento rapido. Mentre per lo più appropriato per i sensori di array lineare, questo potrebbe accadere anche se il dito è stato spostato durante l'acquisizione. All'utente dovrebbe essere chiesto di spostare il dito più lentamente (lineare) o di lasciare il dito sul sensore più a lungo.

- **`AcquiredToSlow`**&ndash; (valore 4) L'immagine dell'impronta digitale era illeggibile a causa della mancanza di movimento. Questo è più appropriato per i sensori di array lineari che richiedono un movimento di scorrimento rapido.

- **`ErrorCanceled`**&ndash; (valore 5) L'operazione è stata annullata perché il sensore di impronte digitali non è disponibile. Ad esempio, ciò può verificarsi quando l'utente è commutato, il dispositivo è bloccato o un'altra operazione in sospeso impedisce o disabilita.

- **`ErrorHwUnavailable`**&ndash; (valore 1) L'hardware non è disponibile. Riprovare più tardi.

- **`ErrorLockout`**&ndash; (valore 7) L'operazione è stata annullata perché l'API è bloccata a causa di troppi tentativi.

- **`ErrorNoSpace`**&ndash; (valore 4) Stato di errore restituito per operazioni come la registrazione; Impossibile completare l'operazione perché non è disponibile spazio di archiviazione sufficiente per completare l'operazione.

- **`ErrorTimeout`**&ndash; (valore 3) Stato di errore restituito quando la richiesta corrente è stata eseguita troppo a lungo. Questo ha lo scopo di impedire ai programmi di attendere il sensore di impronte digitali a tempo indeterminato. Il timeout è specifico della piattaforma e del sensore, ma in genere è di circa 30 secondi.

- **`ErrorUnableToProcess`**&ndash; (valore 2) Stato di errore restituito quando il sensore non è stato in grado di elaborare l'immagine corrente.

## <a name="related-links"></a>Collegamenti correlati

- [Cifrario](https://docs.oracle.com/javase/7/docs/api/javax/crypto/Cipher.html)
- [AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [AuthenticationCallback](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.AuthenticationCallback.html)
