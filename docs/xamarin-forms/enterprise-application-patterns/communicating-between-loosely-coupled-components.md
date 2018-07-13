---
title: Comunicazioni tra componenti poco accoppiati
description: "Questo capitolo viene illustrato il modo in cui l'app per dispositivi mobili di eShopOnContainers implementa la pubblicazione-sottoscrizione modello, che consente la comunicazione basata su messaggi tra i componenti che sono poco pratici di collegamento dai riferimenti oggetto e il tipo "
ms.prod: xamarin
ms.assetid: 1194af33-8a91-48d2-88b5-b84d77f2ce69
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: ddc33d28aad4e00c9259893c0f8e7a1ab40ee429
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998544"
---
# <a name="communicating-between-loosely-coupled-components"></a>Comunicazioni tra componenti poco accoppiati

La pubblicazione-sottoscrizione pattern è un modello di messaggistica in cui i server di pubblicazione inviano messaggi senza che sia a conoscenza di eventuali destinatari, noto come sottoscrittori. Allo stesso modo, i sottoscrittori in ascolto di messaggi specifici, senza che sia a conoscenza di alcun server di pubblicazione.

Gli eventi in .NET implementano la pubblicazione-sottoscrizione pattern e sono più semplice e diretti approccio per un livello di comunicazione tra i componenti se regime di controllo libero non è richiesto, ad esempio un controllo e la pagina che lo contiene. Tuttavia, la durata di pubblicazione e nel Sottoscrittore è collegata da riferimenti a oggetti tra loro e il tipo di Sottoscrittore deve includere un riferimento al tipo di server di pubblicazione. Ciò può creare problemi di gestione della memoria in particolare quando sono presenti oggetti temporanei che sottoscrivono un evento di un oggetto statico o di lunga durato. Se il gestore dell'evento non viene rimosso, verrà mantenuto attivo al sottoscrittore per il riferimento a esso nel server di pubblicazione e questo verrà impedire o ritardare la garbage collection del sottoscrittore.

## <a name="introduction-to-messagingcenter"></a>Introduzione a MessagingCenter

Xamarin. Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe implementa la pubblicazione-sottoscrizione modello, che consente la comunicazione basata su messaggi tra i componenti che sono poco pratici di collegamento dai riferimenti oggetto e il tipo. Questo meccanismo consente i server di pubblicazione e sottoscrittori di comunicare senza che sia un riferimento tra loro, contribuendo a ridurre le dipendenze tra componenti, consentendo anche di componenti a essere sviluppate e testate in modo indipendente.

Il [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe fornisce funzionalità di pubblicazione-sottoscrizione multicast. Ciò significa che possono essere presenti più server di pubblicazione che pubblicano un singolo messaggio e possono essere presenti più sottoscrittori in attesa per lo stesso messaggio. Figura 4-1 illustra questa relazione:

![](communicating-between-loosely-coupled-components-images/messagingcenter.png "Funzionalità di pubblicazione-sottoscrizione multicast")

**Figura 4-1:** funzionalità di pubblicazione-sottoscrizione Multicast

I server di pubblicazione inviano messaggi usando la [ `MessagingCenter.Send` ](xref:Xamarin.Forms.MessagingCenter.Send*) (metodo), mentre i sottoscrittori in ascolto dei messaggi utilizzando la [ `MessagingCenter.Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*) (metodo). Inoltre, i sottoscrittori possono anche annullare la sottoscrizione dalle sottoscrizioni di messaggio, se necessario, con la [ `MessagingCenter.Unsubscribe` ](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) (metodo).

Internamente, il [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe utilizza riferimenti deboli. Ciò significa che sarà non mantenere gli oggetti attivi e consentirà loro di essere sottoposto a garbage collection. Pertanto, deve solo essere necessario annullare la sottoscrizione a un messaggio quando non è più una classe desidera ricevere il messaggio.

L'app per dispositivi mobili di eShopOnContainers Usa il [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe per la comunicazione ad accoppiamento tra componenti poco accoppiati. L'app definisce tre messaggi:

-   Il `AddProduct` messaggio viene pubblicato dal `CatalogViewModel` classe quando un elemento viene aggiunto al carrello degli acquisti. In cambio il `BasketViewModel` classe sottoscrive il messaggio e incrementa il numero di elementi nel carrello acquisti nella risposta. Inoltre, il `BasketViewModel` classe Annulla la sottoscrizione anche da questo messaggio.
-   Il `Filter` messaggio viene pubblicato dal `CatalogViewModel` classe quando l'utente viene applicato un filtro del marchio o un tipo per gli elementi visualizzati nel catalogo. In cambio il `CatalogView` classe sottoscrive il messaggio e aggiorna l'interfaccia utente in modo che solo gli elementi che soddisfano i criteri di filtro vengono visualizzati.
-   Il `ChangeTab` messaggio viene pubblicato dal `MainViewModel` quando viene chiamato il `CheckoutViewModel` passa al `MainViewModel` dopo la creazione ha esito positivo e l'invio di un nuovo ordine. In cambio, la `MainView` classe sottoscrive il messaggio e gli aggiornamenti dell'interfaccia utente in modo che il **profilo personale** scheda è attiva, per visualizzare gli ordini dell'utente.

> [!NOTE]
> Mentre il [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe consente la comunicazione tra le classi di accoppiamento, non offre la soluzione architetturale solo a questo problema. Ad esempio, la comunicazione tra un modello di visualizzazione e una visualizzazione anche ottenibile tramite il motore di binding e le notifiche di modifica di proprietà. Inoltre, la comunicazione tra i due modelli di visualizzazione possa essere ottenuta anche eseguendo il passaggio di dati durante la navigazione.

Nell'app per dispositivi mobili di eShopOnContainers[ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) viene usato per aggiornare nell'interfaccia utente in risposta a un'azione che si verificano in un'altra classe. Di conseguenza, i messaggi vengono pubblicati nel thread dell'interfaccia utente, con gli abbonati che ricevono il messaggio sullo stesso thread.

> [!TIP]
> Effettuare il marshalling nel thread dell'interfaccia utente durante l'esecuzione dell'interfaccia utente di aggiornamenti. Se è necessario aggiornare l'interfaccia utente di un messaggio che viene inviato da un thread in background, elaborare il messaggio sul thread dell'interfaccia utente nel Sottoscrittore richiamando il [ `Device.BeginInvokeOnMainThread` ](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) (metodo).

Per altre informazioni sulle [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter), vedere [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md).

## <a name="defining-a-message"></a>Definizione di un messaggio

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) i messaggi sono stringhe che vengono usate per identificare i messaggi. Esempio di codice seguente mostra i messaggi definiti all'interno dell'app per dispositivi mobili di eShopOnContainers:

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

In questo esempio, i messaggi vengono definiti usando le costanti. Il vantaggio di questo approccio è che fornisce l'indipendenza dai tipi in fase di compilazione e supporto per il refactoring.

## <a name="publishing-a-message"></a>Pubblicazione di un messaggio

I server di pubblicazione notificare ai sottoscrittori di un messaggio con uno dei [ `MessagingCenter.Send` ](xref:Xamarin.Forms.MessagingCenter.Send*) overload. L'esempio di codice seguente illustra la pubblicazione di `AddProduct` messaggio:

```csharp
MessagingCenter.Send(this, MessengerKeys.AddProduct, catalogItem);
```

In questo esempio, il [ `Send` ](xref:Xamarin.Forms.MessagingCenter.Send*) metodo specifica tre argomenti:

-   Il primo argomento specifica la classe mittente. La classe mittente deve essere specificata per i sottoscrittori che desiderano ricevere il messaggio.
-   Il secondo argomento specifica il messaggio.
-   Il terzo argomento specifica i dati di payload da inviare al sottoscrittore. In questo caso i dati di payload sono un `CatalogItem` istanza.

Il [ `Send` ](xref:Xamarin.Forms.MessagingCenter.Send*) metodo verrà pubblicato il messaggio e i relativi dati di payload, usando un approccio fire-and-forget. Pertanto, il messaggio viene inviato anche se non sono presenti sottoscrittori registrati per ricevere il messaggio. In questo caso, il messaggio inviato viene ignorato.

> [!NOTE]
> Il [ `MessagingCenter.Send` ](xref:Xamarin.Forms.MessagingCenter.Send*) metodo può usare i parametri generici per controllare la modalità di recapito dei messaggi. Pertanto, più i messaggi che condividono un'identità del messaggio, ma inviare tipi di dati di payload diversi possono essere ricevuti da sottoscrittori diversi.

## <a name="subscribing-to-a-message"></a>Sottoscrive un messaggio

I sottoscrittori possono registrarsi per ricevere un messaggio utilizzando uno dei [ `MessagingCenter.Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*) overload. Esempio di codice seguente viene illustrato come l'app per dispositivi mobili di eShopOnContainers sottoscrive e processi, il `AddProduct` messaggio:

```csharp
MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
    this, MessageKeys.AddProduct, async (sender, arg) =>  
{  
    BadgeCount++;  

    await AddCatalogItemAsync(arg);  
});
```

In questo esempio, il [ `Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*) metodo sottoscrive il `AddProduct` del messaggio ed esegue un delegato di callback in risposta alla ricezione del messaggio. Il delegato di callback, specificato come un'espressione lambda, esegue il codice che aggiorna l'interfaccia utente.

> [!TIP]
> È consigliabile usare i dati di payload non modificabile. Non provare a modificare i dati di payload all'interno di un delegato di callback perché diversi thread è stato possibile accedere i dati ricevuti simultaneamente. In questo scenario, i dati di payload devono essere modificabili per evitare errori di concorrenza.

Un sottoscrittore potrebbe non essere necessario gestire tutte le istanze di un messaggio pubblicato e può essere controllato dagli argomenti tipo generico sono specificati nel [ `Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*) (metodo). In questo esempio, il sottoscrittore riceverà solo `AddProduct` i messaggi inviati dal `CatalogViewModel` (classe), dati il cui payload sono un `CatalogItem` istanza.

## <a name="unsubscribing-from-a-message"></a>Annullamento della sottoscrizione da un messaggio

I sottoscrittori possono annullare la sottoscrizione che non vuoi più ricevere messaggi. Questo risultato viene ottenuto con uno dei [ `MessagingCenter.Unsubscribe` ](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) esegue l'overload, come illustrato nell'esempio di codice seguente:

```csharp
MessagingCenter.Unsubscribe<CatalogViewModel, CatalogItem>(this, MessengerKeys.AddProduct);
```

In questo esempio, il [ `Unsubscribe` ](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) la sintassi del metodo riflette gli argomenti di tipo specificati per la sottoscrizione per ricevere il `AddProduct` messaggio.

## <a name="summary"></a>Riepilogo

Xamarin. Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe implementa la pubblicazione-sottoscrizione modello, che consente la comunicazione basata su messaggi tra i componenti che sono poco pratici di collegamento dai riferimenti oggetto e il tipo. Questo meccanismo consente i server di pubblicazione e sottoscrittori di comunicare senza che sia un riferimento tra loro, contribuendo a ridurre le dipendenze tra componenti, consentendo anche di componenti a essere sviluppate e testate in modo indipendente.


## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
