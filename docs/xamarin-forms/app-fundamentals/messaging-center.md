---
title: " Xamarin.Forms MessagingCenter" Description: "la Xamarin.Forms classe MessagingCenter implementa il modello di pubblicazione-sottoscrizione, consentendo la comunicazione basata su messaggi tra i componenti che non sono pratici per il collegamento in base ai riferimenti a oggetti e tipi".
ms. prod: Novell MS. AssetID: EDFE7B19-C5FD-40D5-816C-FAE56532E885 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/08/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-messagingcenter"></a>Xamarin.FormsMessagingCenter

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)

Il modello di pubblicazione-sottoscrizione è un modello di messaggistica in cui i server di pubblicazione inviano messaggi senza conoscere i ricevitori, noti come sottoscrittori. In modo analogo, i sottoscrittori sono in ascolto di messaggi specifici, senza conoscere i server di pubblicazione.

Gli eventi in .NET implementano il modello di pubblicazione-sottoscrizione e sono l'approccio più semplice e diretto per un livello di comunicazione tra i componenti se non è necessario un accoppiamento libero, ad esempio un controllo e la pagina che lo contiene. Tuttavia, le durate del server di pubblicazione e del Sottoscrittore sono abbinate a riferimenti a oggetti e il tipo di Sottoscrittore deve disporre di un riferimento al tipo di server di pubblicazione. Questo può creare problemi di gestione della memoria, soprattutto quando sono presenti oggetti di breve durata che sottoscrivono un evento di un oggetto statico o di lunga durata. Se il gestore eventi non viene rimosso, il Sottoscrittore verrà mantenuto attivo dal riferimento a esso nel server di pubblicazione e ciò impedirà o ritarderà il Garbage Collection del Sottoscrittore.

La Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe implementa il modello di pubblicazione-sottoscrizione, consentendo la comunicazione basata su messaggi tra i componenti che non sono pratici per il collegamento in base a riferimenti a oggetti e tipi. Questo meccanismo consente ai server di pubblicazione e ai sottoscrittori di comunicare senza avere un riferimento reciproco, contribuendo a ridurre le dipendenze tra di essi.

La [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe fornisce la funzionalità di pubblicazione-sottoscrizione multicast. Ciò significa che possono essere presenti più server di pubblicazione che pubblicano un singolo messaggio e più sottoscrittori in ascolto dello stesso messaggio:

![](messaging-center-images/messaging-center.png "Multicast publish-subscribe functionality")

I Publisher inviano messaggi utilizzando il [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) metodo, mentre i sottoscrittori restano in ascolto dei messaggi utilizzando il [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) metodo. Inoltre, i sottoscrittori possono annullare la sottoscrizione alle sottoscrizioni dei messaggi, se necessario, con il [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) metodo.

> [!IMPORTANT]
> Internamente, la [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe utilizza riferimenti deboli. Ciò significa che non manterrà gli oggetti attivi e consentirà di sottoporli a Garbage Collection, quindi dovrebbe essere necessario annullare la sottoscrizione di un messaggio solo quando una classe non vuole più ricevere il messaggio.

## <a name="publish-a-message"></a>Pubblicare un messaggio

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)i messaggi sono stringhe. Gli autori inviano notifiche ai sottoscrittori di un messaggio con uno degli [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) Overload. L'esempio di codice seguente pubblica un messaggio `Hi`:

```csharp
MessagingCenter.Send<MainPage>(this, "Hi");
```

In questo esempio il [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) metodo specifica un argomento generico che rappresenta il mittente. Per ricevere il messaggio, un sottoscrittore deve anche specificare lo stesso argomento generico, a indicare che è in ascolto di un messaggio proveniente da tale mittente. In questo esempio vengono inoltre specificati due argomenti del metodo:

- Il primo argomento specifica l'istanza del mittente.
- Il secondo argomento specifica il messaggio.

Anche i dati di payload possono essere inviati con un messaggio:

```csharp
MessagingCenter.Send<MainPage, string>(this, "Hi", "John");
```

In questo esempio, il [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) metodo specifica due argomenti generici. Il primo è il tipo che invia il messaggio, mentre il secondo è il tipo dei dati di payload inviati. Per ricevere il messaggio, anche un sottoscrittore deve specificare gli stessi argomenti generici. Ciò consente la presenza di più messaggi che condividono un'identità di messaggio, ma inviano tipi di dati di payload diversi che devono essere ricevuti da sottoscrittori diversi. In questo esempio viene anche specificato un terzo argomento del metodo che rappresenta i dati del payload da inviare al sottoscrittore. In questo caso i dati di payload sono costituiti da un elemento `string`.

Il [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) Metodo pubblicherà il messaggio e i dati di payload usando un approccio Fire-and-Forget. Il messaggio viene quindi inviato anche se non sono presenti sottoscrittori registrati per la ricezione del messaggio. In questa situazione, il messaggio inviato viene ignorato.

## <a name="subscribe-to-a-message"></a>Sottoscrivere un messaggio

I sottoscrittori possono eseguire la registrazione per ricevere un messaggio utilizzando uno degli [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) Overload. Il codice seguente ne è un esempio:

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) =>
{
    // Do something whenever the "Hi" message is received
});
```

In questo esempio, il [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) metodo sottoscrive l' `this` oggetto ai `Hi` messaggi inviati dal `MainPage` tipo ed esegue un delegato di callback in risposta alla ricezione del messaggio. Il delegato di callback, specificato come espressione lambda, potrebbe essere il codice che aggiorna l'interfaccia utente, salva alcuni dati o attiva un'altra operazione.

> [!NOTE]
> Un Sottoscrittore potrebbe non dover gestire ogni istanza di un messaggio pubblicato e questo può essere controllato dagli argomenti di tipo generico specificati nel [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) metodo.

Nell'esempio seguente viene illustrato come sottoscrivere un messaggio contenente dati di payload:

```csharp
MessagingCenter.Subscribe<MainPage, string>(this, "Hi", async (sender, arg) =>
{
    await DisplayAlert("Message received", "arg=" + arg, "OK");
});
```

In questo esempio, il [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) metodo sottoscrive i `Hi` messaggi inviati dal `MainPage` tipo, i cui dati di payload sono `string` . Un delegato di callback viene eseguito in risposta alla ricezione di un messaggio di questo tipo, che visualizza i dati di payload in un avviso.

> [!IMPORTANT]
> Il delegato eseguito dal `Subscribe` metodo verrà eseguito sullo stesso thread che pubblica il messaggio usando il `Send` metodo.

## <a name="unsubscribe-from-a-message"></a>Annullare la sottoscrizione di un messaggio

I sottoscrittori possono annullare la sottoscrizione dei messaggi che non vogliono più ricevere Questa operazione viene eseguita con uno degli [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) Overload:

```csharp
MessagingCenter.Unsubscribe<MainPage>(this, "Hi");
```

In questo esempio, il [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) metodo annulla la sottoscrizione dell' `this` oggetto dal `Hi` messaggio inviato dal `MainPage` tipo.

È necessario annullare la sottoscrizione dei messaggi contenenti dati di payload utilizzando l' [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) Overload di che specifica due argomenti generici:

```csharp
MessagingCenter.Unsubscribe<MainPage, string>(this, "Hi");
```

In questo esempio, il [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) metodo annulla la sottoscrizione dell' `this` oggetto dal `Hi` messaggio inviato dal `MainPage` tipo, i cui dati di payload sono `string` .

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di MessagingCenter](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)
