---
title: Risposta ai callback di autenticazione
ms.topic: article
ms.prod: xamarin
ms.assetid: 6533AFC9-1A1C-4897-A154-4D4ECFE27761
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/06/2017
ms.openlocfilehash: 371ffae8e14a630cb548f4a9ee2bf0bd06f7284c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="responding-to-authentication-callbacks"></a>Risposta ai callback di autenticazione

Lo scanner di impronta digitale viene eseguito in background nel proprio thread e una volta terminato segnalerà anche i risultati dell'analisi richiamando un metodo di `FingerprintManager.AuthenticationCallback` nel thread UI. Un'applicazione Android è necessario fornire il proprio gestore che estende questa classe astratta che implementa i metodi seguenti:

* **`OnAuthenticationError(int errorCode, ICharSequence errString)`** &ndash; Chiamata eseguita quando si verifica un errore irreversibile. Non è più che possibile eseguire un'applicazione o un utente per risolvere il problema, ad eccezione del fatto eventualmente ripetere l'operazione.
* **`OnAuthenticationFailed()`** &ndash; Questo metodo viene richiamato quando viene rilevata ma non riconosciuta dal dispositivo tramite impronta digitale.
* **`OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)`** &ndash; Chiamata eseguita quando si verifica un errore irreversibile, ad esempio il dito viene passato a veloce tramite lo scanner.
* **`OnAuthenticationSucceeded(FingerprintManagerCompati.AuthenticationResult result)`** &ndash; Viene chiamato quando un'impronta digitale è stata riconosciuta.

Se un `CryptoObject` è stato utilizzato durante la chiamata `Authenticate`, si consiglia di chiamare `Cipher.DoFinal` in `OnAuthenticationSuccessful`.
`DoFinal` verrà generata un'eccezione se la crittografia è stato manomesso o inizializzata in modo non corretto, che indica che il risultato dello scanner impronta digitale potrebbe essere stato alterato all'esterno dell'applicazione.


> [!NOTE]
> **Nota:** è consigliabile mantenere il callback classe relativamente leggero e privo di una logica specifica dell'applicazione. Il callback deve agire come un "traffico cop" tra l'applicazione di Android e i risultati dallo scanner impronta digitale.

## <a name="a-sample-authentication-callback-handler"></a>Un gestore di Callback di autenticazione di esempio

La classe seguente è riportato un esempio di un numero minimo `FingerprintManager.AuthenticationCallback` implementazione: 

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

`OnAuthenticationSucceeded` Controlla se un `Cipher` forniti `FingerprintManager` quando `Authentication` è stato richiamato. In questo caso, il `DoFinal` metodo viene chiamato per la crittografia. Consente di chiudere il `Cipher`, eseguire il ripristino allo stato originale. Se si è verificato un problema con la crittografia, quindi `DoFinal` genererà un'eccezione e il tentativo di autenticazione deve essere considerato non riuscito.

Il `OnAuthenticationError` e `OnAuthenticationHelp` callback ogni ricezione di un numero intero che indica il problema è. Nella sezione seguente viene spiegata ciascuna delle possibili Guida o codici di errore. I callback di due scopi simili &ndash; per informare l'applicazione di impronta digitale autenticazione non riuscita. Le differenze è in base alla gravità. `OnAuthenticationHelp` è un errore reversibile utente, ad esempio scorrimento rapido l'impronta digitale troppo veloce. `OnAuthenticationError` è più un errore grave, ad esempio uno scanner di impronta digitale danneggiato.

Si noti che `OnAuthenticationError` verrà richiamato quando l'analisi delle impronte digitali è stata annullata tramite il `CancellationSignal.Cancel()` messaggio. Il `errMsgId` parametro avrà il valore 5 (`FingerprintState.ErrorCanceled`). A seconda dei requisiti, un'implementazione del `AuthenticationCallbacks` può gestire questa situazione in modo diverso da altri errori. 

`OnAuthenticationFailed` viene richiamato quando l'impronta digitale è stato eseguito correttamente l'analisi, ma non corrisponde a qualsiasi impronta digitale registrati con il dispositivo. 

## <a name="help-codes-and-error-message-ids"></a>Codici di Guida e gli ID di messaggio di errore 

Sono disponibili in un elenco e una descrizione dei codici di Guida e i codici di errore di [documentazione SDK Android](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html#FINGERPRINT_ACQUIRED_GOOD) per la classe FingerprintManager. Xamarin rappresenta questi valori con il `Android.Hardware.Fingerprints.FingerprintState` enum:


-   **`AcquiredGood`** &ndash; (valore 0) L'immagine acquisita è valido.


-   **`AcquiredImagerDirty`** &ndash; (valore 3) L'immagine di impronte digitali è troppo disturbato a causa di terra sospetto o rilevato nel sensore. Ad esempio, è ragionevole restituire questo dopo più `AcquiredInsufficient` o rilevamento effettivo della terra in sensore (pixel bloccati, quantità e così via). L'utente deve intervenire per pulire i sensori di rilevamento quando viene restituito.


-   **`AcquiredInsufficient`** &ndash; (valore 2) L'immagine di impronte digitali è troppo disturbato elaborare a causa di una condizione (ad esempio interfaccia secca) o un sensore probabilmente dirty (vedere `AcquiredImagerDirty`.



-   **`AcquiredPartial`** &ndash; (valore 1) È stata rilevata un'immagine di impronta digitale parziale. Durante la registrazione, è consigliabile informare l'utente su ciò che deve essere eseguita per risolvere questo problema, ad esempio, &ldquo;premere solidamente sul sensore.&rdquo;



-   **`AcquiredTooFast`** &ndash; (valore 5) L'immagine di impronte digitali è incompleto a causa di movimento rapido. Mentre per lo più appropriato per i sensori di matrice lineare, ciò può verificarsi anche se si è stato spostato durante l'acquisizione. All'utente deve essere richiesto per spostare il dito più lento (lineare) o lasciare il dito sul sensore più lungo.




-   **`AcquiredToSlow`** &ndash; (valore 4) L'immagine di impronte digitali è illeggibile a causa della mancanza di movimento. Questo comportamento è più appropriato per i sensori di matrice lineare che richiedono un movimento scorrere.



-   **`ErrorCanceled`** &ndash; (valore 5) L'operazione è stata annullata perché non è disponibile il sensore di impronta digitale. Ad esempio, ciò può verificarsi quando l'utente è impostato, il dispositivo è bloccato o un'altra operazione in sospeso impedisce o disabilitarlo.



-   **`ErrorHwUnavailable`** &ndash; (valore 1) L'hardware non è disponibile. Riprovare più tardi.




-   **`ErrorLockout`** &ndash; (valore 7) L'operazione è stata annullata perché l'API è bloccato a causa di troppi tentativi di accesso.




-   **`ErrorNoSpace`** &ndash; (valore 4) Stato dell'errore restituito per operazioni quali la registrazione; Impossibile completare l'operazione perché non vi è spazio sufficiente per completare l'operazione.



-   **`ErrorTimeout`** &ndash; (valore 3) Stato di errore restituito quando la richiesta corrente è stato eseguito troppo lungo. Questo consente di impedire ai programmi di attendere indefinitamente sensore di impronta digitale. Il timeout è di piattaforma e specifico del sensore, ma è in genere circa 30 secondi.



-   **`ErrorUnableToProcess`** &ndash; (valore 2) Stato di errore restituito quando il sensore non è riuscito a elaborare l'immagine corrente.



## <a name="related-links"></a>Collegamenti correlati

- [Pacchetto di crittografia](https://docs.oracle.com/javase/7/docshttps://developer.xamarin.com/api/javax/crypto/Cipher.html)
- [AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [AuthenticationCallback](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.AuthenticationCallback.html)
