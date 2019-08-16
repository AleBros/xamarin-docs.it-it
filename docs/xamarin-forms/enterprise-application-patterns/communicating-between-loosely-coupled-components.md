---
title: Comunicazioni tra componenti ad accoppiamento debole
description: "Questo capitolo illustra il modo in cui l'app per dispositivi mobili eShopOnContainers implementa il modello di pubblicazione-sottoscrizione, consentendo la comunicazione basata su messaggi tra i componenti che non sono pratici per il collegamento per riferimento a oggetti e tipi "
ms.prod: xamarin
ms.assetid: 1194af33-8a91-48d2-88b5-b84d77f2ce69
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 4763e73f0eafa24cc4a2fc42a6d69b457d863b8d
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69529158"
---
# <a name="communicating-between-loosely-coupled-components"></a>Comunicazioni tra componenti ad accoppiamento debole

Il modello di pubblicazione-sottoscrizione è un modello di messaggistica in cui i server di pubblicazione inviano messaggi senza conoscere i ricevitori, noti come sottoscrittori. In modo analogo, i sottoscrittori sono in ascolto di messaggi specifici, senza conoscere i server di pubblicazione.

Gli eventi in .NET implementano il modello di pubblicazione-sottoscrizione e sono l'approccio più semplice e diretto per un livello di comunicazione tra i componenti se non è necessario un accoppiamento libero, ad esempio un controllo e la pagina che lo contiene. Tuttavia, le durate del server di pubblicazione e del Sottoscrittore sono abbinate a riferimenti a oggetti e il tipo di Sottoscrittore deve disporre di un riferimento al tipo di server di pubblicazione. Questo può creare problemi di gestione della memoria, soprattutto quando sono presenti oggetti di breve durata che sottoscrivono un evento di un oggetto statico o di lunga durata. Se il gestore eventi non viene rimosso, il Sottoscrittore verrà mantenuto attivo dal riferimento a esso nel server di pubblicazione e ciò impedirà o ritarderà il Garbage Collection del Sottoscrittore.

## <a name="introduction-to-messagingcenter"></a>Introduzione a MessagingCenter

La classe [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) di Xamarin.Forms implementa il modello di pubblicazione-sottoscrizione, consentendo la comunicazione basata su messaggi tra componenti che non è pratico collegare con riferimenti a oggetti e tipi. Questo meccanismo consente agli editori e ai sottoscrittori di comunicare senza avere un riferimento reciproco, contribuendo a ridurre le dipendenze tra i componenti, consentendo allo stesso tempo di sviluppare e testare i componenti in modo indipendente.

La classe [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) fornisce una funzionalità di pubblicazione-sottoscrizione multicast. Ciò significa che possono essere presenti più editori che pubblicano un singolo messaggio e che possono essere presenti più Sottoscrittori in ascolto dello stesso messaggio. La figura 4-1 illustra questa relazione:

![](communicating-between-loosely-coupled-components-images/messagingcenter.png "Funzionalità di pubblicazione-sottoscrizione multicast")

**Figura 4-1:** Funzionalità di pubblicazione-sottoscrizione multicast

I server di pubblicazione inviano i messaggi usando il metodo [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*), mentre i sottoscrittori sono in ascolto dei messaggi usando il metodo [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*). I sottoscrittori possono inoltre annullare la sottoscrizione dei messaggi, se necessario, con il metodo [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*).

Internamente, la classe [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) usa riferimenti deboli. Ciò significa che non manterrà gli oggetti attivi e consentirà di sottoporli a Garbage Collection, quindi dovrebbe essere necessario annullare la sottoscrizione di un messaggio solo quando una classe non vuole più ricevere il messaggio.

L'app per dispositivi mobili eShopOnContainers [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) usa la classe per la comunicazione tra componenti a regime di controllo libero. L'app definisce tre messaggi:

- Il `AddProduct` messaggio viene pubblicato `CatalogViewModel` dalla classe quando un elemento viene aggiunto al carrello acquisti. In return `BasketViewModel` la classe sottoscrive il messaggio e incrementa il numero di elementi nel carrello acquisti in risposta. Inoltre, la classe `BasketViewModel` Annulla la sottoscrizione da questo messaggio.
- Il `Filter` messaggio viene pubblicato `CatalogViewModel` dalla classe quando l'utente applica un filtro del marchio o del tipo agli elementi visualizzati nel catalogo. In return `CatalogView` la classe sottoscrive il messaggio e aggiorna l'interfaccia utente in modo che vengano visualizzati solo gli elementi che corrispondono ai criteri di filtro.
- Il `ChangeTab` messaggio viene pubblicato `MainViewModel` dalla classe quando `MainViewModel` il `CheckoutViewModel` passa a dopo la creazione e l'invio di un nuovo ordine. In return `MainView` la classe sottoscrive il messaggio e aggiorna l'interfaccia utente in modo che la scheda **profilo personale** sia attiva per visualizzare gli ordini dell'utente.

> [!NOTE]
> Sebbene la [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe consenta la comunicazione tra le classi a regime di controllo libero, non offre l'unica soluzione di architettura a questo problema. La comunicazione tra un modello di visualizzazione e una vista, ad esempio, può essere eseguita anche dal motore di binding e tramite le notifiche di modifica delle proprietà. Inoltre, la comunicazione tra due modelli di visualizzazione può essere eseguita anche passando i dati durante la navigazione.

Nell'app per dispositivi mobili eShopOnContainers [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) , viene usato per aggiornare nell'interfaccia utente in risposta a un'azione che si verifica in un'altra classe. Pertanto, i messaggi vengono pubblicati nel thread dell'interfaccia utente, con i Sottoscrittori che ricevono il messaggio sullo stesso thread.

> [!TIP]
> Eseguire il marshalling al thread UI durante l'esecuzione degli aggiornamenti dell'interfaccia utente. Se è necessario un messaggio inviato da un thread in background per aggiornare l'interfaccia utente, elaborare il messaggio nel thread UI del Sottoscrittore richiamando il [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) metodo.

Per ulteriori informazioni su [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter), vedere [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md).

## <a name="defining-a-message"></a>Definizione di un messaggio

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)i messaggi sono stringhe utilizzate per identificare i messaggi. L'esempio di codice seguente illustra i messaggi definiti nell'app per dispositivi mobili eShopOnContainers:

```csharp
public class MessengerKeys  
{  
    // Add product to basket  
    public const string AddProduct = "AddProduct";  

    // Filter  
    public const string Filter = "Filter";  

    // Change selected Tab programmatically  
    public const string ChangeTab = "ChangeTab";  
}
```

In questo esempio i messaggi vengono definiti tramite costanti. Il vantaggio di questo approccio consiste nel fatto che fornisce il supporto per il refactoring e l'indipendenza dai tipi in fase di compilazione.

## <a name="publishing-a-message"></a>Pubblicazione di un messaggio

I server di pubblicazione notificano un messaggio ai sottoscrittori con uno degli overload di [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*). Nell'esempio di codice riportato di seguito `AddProduct` viene illustrata la pubblicazione del messaggio:

```csharp
MessagingCenter.Send(this, MessengerKeys.AddProduct, catalogItem);
```

In questo esempio, il [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) metodo specifica tre argomenti:

- Il primo argomento specifica la classe sender. La classe sender deve essere specificata da tutti i Sottoscrittori che desiderano ricevere il messaggio.
- Il secondo argomento specifica il messaggio.
- Il terzo argomento specifica i dati del payload da inviare al Sottoscrittore. In questo caso i dati del payload sono `CatalogItem` un'istanza di.

Il [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) metodo pubblicherà il messaggio e i dati di payload usando un approccio Fire-and-Forget. Il messaggio viene quindi inviato anche se non sono presenti sottoscrittori registrati per la ricezione del messaggio. In questa situazione, il messaggio inviato viene ignorato.

> [!NOTE]
> Il [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) metodo può utilizzare parametri generici per controllare il modo in cui vengono recapitati i messaggi. Pertanto, più messaggi che condividono un'identità del messaggio, ma inviano tipi di dati di payload diversi possono essere ricevuti da Sottoscrittori diversi.

## <a name="subscribing-to-a-message"></a>Sottoscrizione di un messaggio

I sottoscrittori possono registrarsi per ricevere un messaggio usando uno degli overload di [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*). Nell'esempio di codice seguente viene illustrato il modo in cui l'app per dispositivi mobili eShopOnContainers sottoscrive `AddProduct` e elabora il messaggio:

```csharp
MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
    this, MessageKeys.AddProduct, async (sender, arg) =>  
{  
    BadgeCount++;  

    await AddCatalogItemAsync(arg);  
});
```

In questo esempio il [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) metodo sottoscrive il `AddProduct` messaggio ed esegue un delegato di callback in risposta alla ricezione del messaggio. Questo delegato di callback, specificato come espressione lambda, esegue il codice che aggiorna l'interfaccia utente.

> [!TIP]
> Si consiglia di utilizzare dati di payload non modificabili. Non tentare di modificare i dati del payload da un delegato di callback perché più thread potrebbero accedere contemporaneamente ai dati ricevuti. In questo scenario, i dati di payload non devono essere modificabili per evitare errori di concorrenza.

Tramite gli argomenti di tipo generico specificati nel metodo [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*), è possibile controllare se un sottoscrittore deve gestire ogni istanza di un messaggio pubblicato. In questo esempio, il sottoscrittore riceverà `AddProduct` solo i messaggi inviati `CatalogViewModel` dalla classe, i cui dati di payload `CatalogItem` sono un'istanza.

## <a name="unsubscribing-from-a-message"></a>Annullamento della sottoscrizione da un messaggio

I sottoscrittori possono annullare la sottoscrizione dei messaggi che non vogliono più ricevere Questa operazione viene eseguita con uno degli [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) overload, come illustrato nell'esempio di codice seguente:

```csharp
MessagingCenter.Unsubscribe<CatalogViewModel, CatalogItem>(this, MessengerKeys.AddProduct);
```

In questo esempio, la [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) sintassi del metodo riflette gli argomenti di tipo specificati quando si sottoscrive la ricezione `AddProduct` del messaggio.

## <a name="summary"></a>Riepilogo

La classe [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) di Xamarin.Forms implementa il modello di pubblicazione-sottoscrizione, consentendo la comunicazione basata su messaggi tra componenti che non è pratico collegare con riferimenti a oggetti e tipi. Questo meccanismo consente agli editori e ai sottoscrittori di comunicare senza avere un riferimento reciproco, contribuendo a ridurre le dipendenze tra i componenti, consentendo allo stesso tempo di sviluppare e testare i componenti in modo indipendente.


## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
