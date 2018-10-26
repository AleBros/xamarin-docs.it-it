---
title: Risposta ai callback di autenticazione
ms.prod: xamarin
ms.assetid: 6533AFC9-1A1C-4897-A154-4D4ECFE27761
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/06/2017
ms.openlocfilehash: 17a1c94ad3b9bde67537ea7113352f0fc10d2a08
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110121"
---
# <a name="responding-to-authentication-callbacks"></a>Risposta ai callback di autenticazione

Lo scanner di impronta digitale viene eseguito in background nel relativo thread e al termine segnala i risultati dell'analisi richiamando un metodo per `FingerprintManager.AuthenticationCallback` sul thread UI. Un'applicazione Android è necessario fornire il proprio gestore che estende questa classe astratta, che implementa i metodi seguenti:

* **`OnAuthenticationError(int errorCode, ICharSequence errString)`** &ndash; Chiamato quando si verifica un errore irreversibile. Non è semplicemente che un'applicazione o un utente può fare per correggere la situazione con la differenza eventualmente ripetere l'operazione.
* **`OnAuthenticationFailed()`** &ndash; Questo metodo viene richiamato quando un'impronta digitale è stata rilevata ma non è riconosciuta dal dispositivo.
* **`OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)`** &ndash; Chiamato quando si verifica un errore irreversibile, ad esempio il dito viene passato a veloce tramite lo scanner.
* **`OnAuthenticationSucceeded(FingerprintManagerCompati.AuthenticationResult result)`** &ndash; Viene chiamato quando un'impronta digitale è stata riconosciuta.

Se un `CryptoObject` è stato usato quando si chiama `Authenticate`, è consigliabile chiamare `Cipher.DoFinal` in `OnAuthenticationSuccessful`.
`DoFinal` verrà generata un'eccezione se la crittografia è stato manomesso o inizializzata in modo non corretto, che indica che il risultato dello scanner di impronta digitale può sono stati manomessi all'esterno dell'applicazione.


> [!NOTE]
> È consigliabile mantenere il callback classe relativamente leggero e gratuito di logica specifica dell'applicazione. I metodi di callback deve agire come un "vigile" tra l'applicazione Android e i risultati dallo scanner di impronta digitale.

## <a name="a-sample-authentication-callback-handler"></a>Un gestore di Callback di autenticazione di esempio

La classe seguente è riportato un esempio di un minimo `FingerprintManager.AuthenticationCallback` implementazione: 

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

`OnAuthenticationSucceeded` verifica se un `Cipher` sono state fornite `FingerprintManager` quando `Authentication` è stata richiamata. In questo caso, il `DoFinal` metodo viene chiamato per la crittografia. Consente di chiudere il `Cipher`, ripristinandone lo stato originale. Se si è verificato un problema con la crittografia, quindi `DoFinal` verrà generata un'eccezione e il tentativo di autenticazione deve essere considerato come non riuscito.

Il `OnAuthenticationError` e `OnAuthenticationHelp` ogni callback ricevono un numero intero che indica qual era il problema. La sezione seguente illustra ognuna delle possibili della Guida in linea o codici di errore. I due callback hanno scopi simili &ndash; per informare l'applicazione che l'autenticazione tramite impronta digitale non è riuscita. Le differenze è nel livello di gravità. `OnAuthenticationHelp` è un errore reversibile di utente, ad esempio scorrendo rapidamente l'impronta digitale troppo veloce; `OnAuthenticationError` è più un errore grave, ad esempio un sensore di impronte danneggiato.

Si noti che `OnAuthenticationError` verrà richiamato quando l'analisi di impronte digitali è stata annullata tramite la `CancellationSignal.Cancel()` messaggio. Il `errMsgId` parametro avrà il valore pari a 5 (`FingerprintState.ErrorCanceled`). A seconda dei requisiti, un'implementazione del `AuthenticationCallbacks` possono gestire questa situazione in modo diverso rispetto gli altri errori. 

`OnAuthenticationFailed` viene richiamato quando l'impronta digitale è stato analizzato correttamente, ma non corrisponde a qualsiasi impronta digitale registrati con il dispositivo. 

## <a name="help-codes-and-error-message-ids"></a>I codici di Guida e gli ID di messaggio di errore 

Un elenco e una descrizione dei codici di Guida e i codici di errore, vedere la [documentazione SDK Android](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html#FINGERPRINT_ACQUIRED_GOOD) per la classe FingerprintManager. Xamarin. Android rappresenta questi valori con il `Android.Hardware.Fingerprints.FingerprintState` enum:


-   **`AcquiredGood`** &ndash; (valore 0) L'immagine acquisita è valida.


-   **`AcquiredImagerDirty`** &ndash; (valore 3) L'immagine di impronte digitali era troppo disturbato a causa di sporco sospetta o rilevata sul sensore. Ad esempio, è ragionevole restituire questo dopo più `AcquiredInsufficient` o il rilevamento effettivo dei sporco sul sensore (pixel bloccati, quantità e così via). L'utente dovrà intervenire per pulire il sensore quando questa proprietà viene restituita.


-   **`AcquiredInsufficient`** &ndash; (valore 2) L'immagine di impronte digitali è troppo disturbato elaborare a causa di una condizione (vale a dire dry skin) o un sensore possibilmente dirty (vedere `AcquiredImagerDirty`.



-   **`AcquiredPartial`** &ndash; (valore 1) È stata rilevata solo un'immagine di impronte digitali parziale. Durante la registrazione, è consigliabile informare l'utente su ciò che è necessario eseguire per risolvere questo problema, ad esempio, &ldquo;premere saldamente sul sensore.&rdquo;



-   **`AcquiredTooFast`** &ndash; (valore 5) L'immagine di impronte digitali è incompleto a causa di un rapido movimento. Mentre principalmente appropriato per i sensori di matrice lineare, ciò può verificarsi anche se il dito è stato spostato durante l'acquisizione. All'utente deve essere richiesto di muovere il dito più lento (lineare) oppure lasciare il dito sul sensore più lungo.




-   **`AcquiredToSlow`** &ndash; (valore 4) L'immagine di impronte digitali è illeggibile a causa della mancanza di movimento. Ciò è particolarmente appropriato per i sensori di matrice lineare che richiedono un movimento scorrimento rapido.



-   **`ErrorCanceled`** &ndash; (valore 5) L'operazione è stata annullata perché il sensore di impronta digitale non è disponibile. Ad esempio, ciò può accadere quando si passa l'utente, il dispositivo è bloccato o un'altra operazione in sospeso impedisce o lo disabilita.



-   **`ErrorHwUnavailable`** &ndash; (valore 1) L'hardware non è disponibile. Riprovare più tardi.




-   **`ErrorLockout`** &ndash; (valore 7) L'operazione è stata annullata perché l'API è bloccato a causa di troppi tentativi di accesso.




-   **`ErrorNoSpace`** &ndash; (valore 4) Stato dell'errore restituito per le operazioni, come registrazione; non è possibile completare l'operazione perché non è presente spazio di archiviazione sufficiente rimanente per completare l'operazione.



-   **`ErrorTimeout`** &ndash; (valore 3) Stato di errore restituito quando la richiesta corrente è rimasto in esecuzione troppo lungo. Ciò serve a impedire che i programmi in attesa per il sensore di impronta digitale per un periodo illimitato. Il timeout è specifico del sensore e piattaforma, ma è in genere circa 30 secondi.



-   **`ErrorUnableToProcess`** &ndash; (valore 2) Stato dell'errore restituito quando il sensore non è riuscito a elaborare l'immagine corrente.



## <a name="related-links"></a>Collegamenti correlati

- [Pacchetti di crittografia](https://docs.oracle.com/javase/7/docs/api/javax/crypto/Cipher.html)
- [AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [AuthenticationCallback](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.AuthenticationCallback.html)
