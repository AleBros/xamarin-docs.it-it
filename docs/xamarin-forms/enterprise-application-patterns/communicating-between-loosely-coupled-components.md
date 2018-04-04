---
title: Le comunicazioni tra Loosely Coupled componenti
ms.prod: xamarin
ms.assetid: 1194af33-8a91-48d2-88b5-b84d77f2ce69
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 01669573f215c5a13bb918c9f9ba80aa5ca528c9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="communicating-between-loosely-coupled-components"></a>Le comunicazioni tra Loosely Coupled componenti

La pubblicazione-sottoscrizione modello è un modello di messaggistica in cui i server di pubblicazione inviare i messaggi senza disporre di conoscenze di qualsiasi ricevitori, noto come sottoscrittori. Analogamente, i sottoscrittori ascolto di messaggi specifici, senza disporre di conoscenze di alcun server di pubblicazione.

La pubblicazione di implementare gli eventi in .NET-modello di sottoscrizione e sono più semplice e diretto approccio per un livello di comunicazione tra i componenti se regime di controllo non è richiesto, ad esempio un controllo e la pagina che lo contiene. Tuttavia, la durata di pubblicazione e nel Sottoscrittore è collegata da riferimenti a oggetti tra loro e il tipo di Sottoscrittore deve includere un riferimento al tipo di server di pubblicazione. Questo può creare problemi di gestione della memoria soprattutto quando sono presenti oggetti temporanei che sottoscrivono un evento di un oggetto statico o di lunga durato. Se il gestore dell'evento non è stato rimosso, verrà mantenuto attivo nel Sottoscrittore per il riferimento a esso nel server di pubblicazione e verrà impedire o ritardare la garbage collection del server di sottoscrizione.

## <a name="introduction-to-messagingcenter"></a>Introduzione a MessagingCenter

Di xamarin. Forms [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) classe implementa la pubblicazione-modello, consentendo la comunicazione basata su messaggi tra i componenti che sono poco pratico da collegare con riferimenti di oggetto e il tipo di sottoscrizione. Questo meccanismo consente i server di pubblicazione e sottoscrittori di comunicare senza un riferimento a altro, contribuendo a ridurre le dipendenze tra componenti, consentendo anche componenti sviluppati e testati in modo indipendente.

Il [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) classe fornisce funzionalità di pubblicazione-sottoscrizione multicast. Ciò significa che possono essere presenti più server di pubblicazione che pubblicano un singolo messaggio e può essere presente più sottoscrittori in attesa per lo stesso messaggio. Figura 4-1 viene illustrata questa relazione:

![](communicating-between-loosely-coupled-components-images/messagingcenter.png "Funzionalità di pubblicazione-sottoscrizione multicast")

**Figura 4-1:** funzionalità di pubblicazione-sottoscrizione Multicast

I server di pubblicazione invia messaggi tramite il [ `MessagingCenter.Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender%7D/p/TSender/System.String/) (metodo), mentre i sottoscrittori ascolto dei messaggi che utilizzano il [ `MessagingCenter.Subscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Subscribe%7BTSender%7D/p/System.Object/System.String/System.Action%7BTSender%7D/TSender/) metodo. Inoltre, i sottoscrittori possono anche annullare la sottoscrizione di sottoscrizioni di messaggio, se necessario, con la [ `MessagingCenter.Unsubscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Unsubscribe%7BTSender%7D/p/System.Object/System.String/) metodo.

Internamente, il [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) classe utilizza riferimenti deboli. Ciò significa che verrà conserva gli oggetti e consentire loro di essere sottoposto a garbage collection. Pertanto, devono solo essere necessario annullare la sottoscrizione di un messaggio quando una classe non desidera ricevere il messaggio.

Gli utilizzi di app per dispositivi mobili eShopOnContainers il [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) classe per la comunicazione tra loosely coupled componenti. L'app definisce tre messaggi:

-   Il `AddProduct` messaggio viene pubblicato per la `CatalogViewModel` classe quando viene aggiunto un elemento al carrello degli acquisti. In cambio, la `BasketViewModel` classe sottoscrive il messaggio e incrementa il numero di elementi nel carrello acquisti in risposta. Inoltre, la `BasketViewModel` classe Annulla la sottoscrizione anche da questo messaggio.
-   Il `Filter` messaggio viene pubblicato per la `CatalogViewModel` classe quando l'utente si applica un filtro di tipo o del marchio per gli elementi visualizzati nel catalogo. In cambio, la `CatalogView` classe sottoscrive il messaggio e aggiorna l'interfaccia utente in modo che solo gli elementi che soddisfano i criteri di filtro vengono visualizzati.
-   Il `ChangeTab` messaggio viene pubblicato per la `MainViewModel` classe quando il `CheckoutViewModel` passa al `MainViewModel` dopo la creazione ha esito positivo e l'invio di un nuovo ordine. In cambio, la `MainView` classe sottoscrive il messaggio e gli aggiornamenti dell'interfaccia utente in modo che il **profilo personale** scheda è attiva, per visualizzare gli ordini dell'utente.

> [!NOTE]
> Mentre il [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) classe consente la comunicazione tra le classi ad accoppiamento debole, non offre la soluzione architetturale solo a tale problema. Ad esempio, la comunicazione tra un modello di visualizzazione e una vista può essere ottenuta anche tramite il motore di associazione e le notifiche di modifica delle proprietà. Inoltre, la comunicazione tra i due modelli di visualizzazione può essere visualizzata anche tramite il passaggio di dati durante la navigazione.

Nell'app mobile eShopOnContainers,[ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) viene utilizzato per aggiornare nell'interfaccia utente in risposta a un'azione che si verificano in un'altra classe. Di conseguenza, i messaggi vengono pubblicati sul thread dell'interfaccia utente, con i sottoscrittori che riceve il messaggio sullo stesso thread.

> [!TIP]
> Effettuare il marshalling al thread dell'interfaccia utente durante l'esecuzione dell'interfaccia utente di aggiornamenti. Se un messaggio inviato da un thread in background è necessario aggiornare l'interfaccia utente, è possibile elaborare il messaggio sul thread dell'interfaccia utente nel server di sottoscrizione richiamando il [ `Device.BeginInvokeOnMainThread` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.BeginInvokeOnMainThread/p/System.Action/) metodo.

Per ulteriori informazioni su [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/), vedere [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md).

## <a name="defining-a-message"></a>Definizione di un messaggio

[`MessagingCenter`](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) i messaggi sono stringhe che vengono utilizzate per identificare i messaggi. Esempio di codice seguente mostra i messaggi definiti all'interno dell'app mobile eShopOnContainers:

```csharp
public class MessengerKeys  
{  
    // Add product to basket  
    public const string AddProduct = "AddProduct";  

    // Filter  
    public const string Filter = "Filter";  

    // Change selected Tab programmatically  
    public const string ChangeTab = "ChangeTab";  
}
```

In questo esempio vengono definiti i messaggi con costanti. Il vantaggio di questo approccio è che fornisce l'indipendenza dai tipi in fase di compilazione e il refactoring del supporto.

## <a name="publishing-a-message"></a>Pubblicazione di un messaggio

I server di pubblicazione notificare ai sottoscrittori di un messaggio con uno del [ `MessagingCenter.Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender,TArgs%7D/p/TSender/System.String/TArgs/) overload. Esempio di codice seguente viene illustrata la pubblicazione di `AddProduct` messaggio:

```csharp
MessagingCenter.Send(this, MessengerKeys.AddProduct, catalogItem);
```

In questo esempio, il [ `Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender,TArgs%7D/p/TSender/System.String/TArgs/) metodo specifica tre argomenti:

-   Il primo argomento specifica la classe mittente. La classe mittente deve essere specificata da tutti i sottoscrittori che desiderano ricevere il messaggio.
-   Il secondo argomento specifica il messaggio.
-   Il terzo argomento specifica i dati di payload da inviare al sottoscrittore. In questo caso i dati di payload sono un `CatalogItem` istanza.

Il [ `Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender,TArgs%7D/p/TSender/System.String/TArgs/) metodo pubblicherà il messaggio e i relativi dati di payload, utilizzando un approccio fire and forget. Pertanto, il messaggio viene inviato anche se non sono presenti sottoscrittori registrati per ricevere il messaggio. In questo caso, il messaggio inviato viene ignorato.

> [!NOTE]
> Il [ `MessagingCenter.Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender,TArgs%7D/p/TSender/System.String/TArgs/) metodo può utilizzare parametri generici per controllare la modalità di recapito messaggi. Pertanto, più i messaggi che condividono un'identità del messaggio ma invia i tipi di dati di payload diversi possono essere ricevuti da diversi sottoscrittori.

## <a name="subscribing-to-a-message"></a>La sottoscrizione a un messaggio

I sottoscrittori possono registrarsi per ricevere un messaggio utilizzando uno del [ `MessagingCenter.Subscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Subscribe%7BTSender%7D/p/System.Object/System.String/System.Action%7BTSender%7D/TSender/) overload. Esempio di codice seguente viene illustrato come l'app mobile eShopOnContainers sottoscrive e processi, il `AddProduct` messaggio:

```csharp
MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
    this, MessageKeys.AddProduct, async (sender, arg) =>  
{  
    BadgeCount++;  

    await AddCatalogItemAsync(arg);  
});
```

In questo esempio, il [ `Subscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Subscribe%7BTSender%7D/p/System.Object/System.String/System.Action%7BTSender%7D/TSender/) metodo sottoscrive il `AddProduct` messaggio ed esegue un delegato di callback in risposta alla ricezione del messaggio. Il delegato di callback, specificato come un'espressione lambda, esegue il codice che aggiorna l'interfaccia utente.

> [!TIP]
> È consigliabile utilizzare i dati di payload non modificabile. Non tentare di modificare i dati del payload all'interno di un delegato di callback, poiché più thread può accedere i dati ricevuti contemporaneamente. In questo scenario, i dati di payload devono essere non modificabili per evitare errori di concorrenza.

Un sottoscrittore potrebbe non essere necessario gestire tutte le istanze di un messaggio pubblicato, e questo può essere controllato dagli argomenti di tipo generico specificati nel [ `Subscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Subscribe%7BTSender%7D/p/System.Object/System.String/System.Action%7BTSender%7D/TSender/) metodo. In questo esempio, il sottoscrittore riceverà solo `AddProduct` i messaggi inviati dal `CatalogViewModel` (classe), dati i cui payload sono un `CatalogItem` istanza.

## <a name="unsubscribing-from-a-message"></a>Annullamento della sottoscrizione di un messaggio

I sottoscrittori possono annullare la sottoscrizione non è più che desiderano ricevere messaggi. Questo risultato viene ottenuto con uno del [ `MessagingCenter.Unsubscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Unsubscribe%7BTSender,TArgs%7D/p/System.Object/System.String/) esegue l'overload, come illustrato nell'esempio di codice seguente:

```csharp
MessagingCenter.Unsubscribe<CatalogViewModel, CatalogItem>(this, MessengerKeys.AddProduct);
```

In questo esempio, il [ `Unsubscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Unsubscribe%7BTSender,TArgs%7D/p/System.Object/System.String/) la sintassi del metodo riflette gli argomenti di tipo specificati per la sottoscrizione per ricevere il `AddProduct` messaggio.

## <a name="summary"></a>Riepilogo

Di xamarin. Forms [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) classe implementa la pubblicazione-modello, consentendo la comunicazione basata su messaggi tra i componenti che sono poco pratico da collegare con riferimenti di oggetto e il tipo di sottoscrizione. Questo meccanismo consente i server di pubblicazione e sottoscrittori di comunicare senza un riferimento a altro, contribuendo a ridurre le dipendenze tra componenti, consentendo anche componenti sviluppati e testati in modo indipendente.


## <a name="related-links"></a>Collegamenti correlati

- [Scaricare eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
