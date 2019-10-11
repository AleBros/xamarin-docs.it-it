---
title: MessagingCenter di Xamarin.Forms
description: La classe MessagingCenter di Xamarin.Forms implementa il modello di pubblicazione-sottoscrizione, consentendo la comunicazione basata su messaggi tra componenti che non è pratico collegare con riferimenti a oggetti e tipi.
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
ms.openlocfilehash: 8d5de8bac6cc61b0874c978a6443ca4490015457
ms.sourcegitcommit: eb23b7d745d1090376f9def07e0f11cb089494d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170973"
---
# <a name="xamarinforms-messagingcenter"></a>MessagingCenter di Xamarin.Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)

Il modello di pubblicazione-sottoscrizione è un modello di messaggistica in cui i server di pubblicazione inviano messaggi senza conoscere i ricevitori, noti come sottoscrittori. In modo analogo, i sottoscrittori sono in ascolto di messaggi specifici, senza conoscere i server di pubblicazione.

Gli eventi in .NET implementano il modello di pubblicazione-sottoscrizione e sono l'approccio più semplice e diretto per un livello di comunicazione tra i componenti se non è necessario un accoppiamento libero, ad esempio un controllo e la pagina che lo contiene. Tuttavia, le durate del server di pubblicazione e del Sottoscrittore sono abbinate a riferimenti a oggetti e il tipo di Sottoscrittore deve disporre di un riferimento al tipo di server di pubblicazione. Questo può creare problemi di gestione della memoria, soprattutto quando sono presenti oggetti di breve durata che sottoscrivono un evento di un oggetto statico o di lunga durata. Se il gestore eventi non viene rimosso, il Sottoscrittore verrà mantenuto attivo dal riferimento a esso nel server di pubblicazione e ciò impedirà o ritarderà il Garbage Collection del Sottoscrittore.

La classe [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) di Xamarin.Forms implementa il modello di pubblicazione-sottoscrizione, consentendo la comunicazione basata su messaggi tra componenti che non è pratico collegare con riferimenti a oggetti e tipi. Questo meccanismo consente ai server di pubblicazione e ai sottoscrittori di comunicare senza avere un riferimento reciproco, contribuendo a ridurre le dipendenze tra di essi.

La classe [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) fornisce una funzionalità di pubblicazione-sottoscrizione multicast. Ciò significa che possono essere presenti più server di pubblicazione che pubblicano un singolo messaggio e più sottoscrittori in ascolto dello stesso messaggio:

![](messaging-center-images/messaging-center.png "Funzionalità di pubblicazione-sottoscrizione multicast")

I server di pubblicazione inviano i messaggi usando il metodo [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*), mentre i sottoscrittori sono in ascolto dei messaggi usando il metodo [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*). I sottoscrittori possono inoltre annullare la sottoscrizione dei messaggi, se necessario, con il metodo [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*).

> [!IMPORTANT]
> Internamente, la classe [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) usa riferimenti deboli. Ciò significa che non manterrà gli oggetti attivi e consentirà di sottoporli a Garbage Collection, quindi dovrebbe essere necessario annullare la sottoscrizione di un messaggio solo quando una classe non vuole più ricevere il messaggio.

## <a name="publish-a-message"></a>Pubblicare un messaggio

I messaggi di [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) sono stringhe. I server di pubblicazione notificano un messaggio ai sottoscrittori con uno degli overload di [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*). L'esempio di codice seguente pubblica un messaggio `Hi`:

```csharp
MessagingCenter.Send<MainPage>(this, "Hi");
```

In questo esempio il metodo [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) specifica un argomento generico che rappresenta il mittente. Per ricevere il messaggio, un sottoscrittore deve anche specificare lo stesso argomento generico, a indicare che è in ascolto di un messaggio proveniente da tale mittente. In questo esempio vengono inoltre specificati due argomenti del metodo:

- Il primo argomento specifica l'istanza del mittente.
- Il secondo argomento specifica il messaggio.

Anche i dati di payload possono essere inviati con un messaggio:

```csharp
MessagingCenter.Send<MainPage, string>(this, "Hi", "John");
```

In questo esempio il metodo [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) specifica due argomenti generici. Il primo è il tipo che invia il messaggio, mentre il secondo è il tipo dei dati di payload inviati. Per ricevere il messaggio, anche un sottoscrittore deve specificare gli stessi argomenti generici. Ciò consente la presenza di più messaggi che condividono un'identità di messaggio, ma inviano tipi di dati di payload diversi che devono essere ricevuti da sottoscrittori diversi. In questo esempio viene anche specificato un terzo argomento del metodo che rappresenta i dati del payload da inviare al sottoscrittore. In questo caso i dati di payload sono costituiti da un elemento `string`.

Il metodo [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) pubblicherà il messaggio e i dati di payload usando un approccio "fire-and-forget". Il messaggio viene quindi inviato anche se non sono presenti sottoscrittori registrati per la ricezione del messaggio. In questa situazione, il messaggio inviato viene ignorato.

## <a name="subscribe-to-a-message"></a>Sottoscrivere un messaggio

I sottoscrittori possono registrarsi per ricevere un messaggio usando uno degli overload di [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*). Il codice seguente ne è un esempio:

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) =>
{
    // Do something whenever the "Hi" message is received
});
```

In questo esempio il metodo [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) sottoscrive l'oggetto `this` per i messaggi `Hi` inviati dal tipo `MainPage` ed esegue un delegato di callback in risposta alla ricezione del messaggio. Il delegato di callback, specificato come espressione lambda, potrebbe essere il codice che aggiorna l'interfaccia utente, salva alcuni dati o attiva un'altra operazione.

> [!NOTE]
> Tramite gli argomenti di tipo generico specificati nel metodo [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*), è possibile controllare se un sottoscrittore deve gestire ogni istanza di un messaggio pubblicato.

Nell'esempio seguente viene illustrato come sottoscrivere un messaggio contenente dati di payload:

```csharp
MessagingCenter.Subscribe<MainPage, string>(this, "Hi", async (sender, arg) =>
{
    await DisplayAlert("Message received", "arg=" + arg, "OK");
});
```

In questo esempio il metodo [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) sottoscrive i messaggi `Hi` inviati dal tipo `MainPage`, i cui dati di payload sono costituiti da un elemento `string`. Un delegato di callback viene eseguito in risposta alla ricezione di un messaggio di questo tipo, che visualizza i dati di payload in un avviso.

## <a name="unsubscribe-from-a-message"></a>Annullare la sottoscrizione di un messaggio

I sottoscrittori possono annullare la sottoscrizione dei messaggi che non vogliono più ricevere usando uno degli overload di [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*):

```csharp
MessagingCenter.Unsubscribe<MainPage>(this, "Hi");
```

In questo esempio il metodo [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) annulla la sottoscrizione dell'oggetto `this` per il messaggio `Hi` inviato dal tipo `MainPage`.

La sottoscrizione dei messaggi contenenti dati di payload deve essere annullata usando l'overload di [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) che specifica due argomenti generici:

```csharp
MessagingCenter.Unsubscribe<MainPage, string>(this, "Hi");
```

In questo esempio il metodo [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) annulla la sottoscrizione dell'oggetto `this` per il messaggio `Hi` inviato dal tipo `MainPage`, i cui dati di payload sono costituiti da un elemento `string`.

## <a name="related-links"></a>Collegamenti correlati

- [MessagingCenterSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)
