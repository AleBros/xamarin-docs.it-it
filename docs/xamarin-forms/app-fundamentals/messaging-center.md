---
title: Xamarin. Forms MessagingCenter
description: Questo articolo illustra come usare il MessagingCenter xamarin. Forms per inviare e ricevere messaggi, per ridurre l'accoppiamento tra classi, ad esempio i modelli di visualizzazione.
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: 7fef4443cacba0fa8bdb8d5df070c4244730b4f5
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675175"
---
# <a name="xamarinforms-messagingcenter"></a>Xamarin. Forms MessagingCenter

_Xamarin. Forms include un semplice servizio di messaggistica per inviare e ricevere messaggi._

<a name="Overview" />

## <a name="overview"></a>Panoramica

Xamarin. Forms `MessagingCenter` consente di visualizzare i modelli e altri componenti per comunicare con senza dover conoscere tra loro oltre a un semplice contratto di messaggio.

<a name="How_the_MessagingCenter_Works" />

## <a name="how-the-messagingcenter-works"></a>Come funziona il MessagingCenter

Esistono due parti per `MessagingCenter`:

-  **La sottoscrizione** : ascolto dei messaggi con una determinata firma e di eseguire un'azione quando vengono ricevuti. Possono essere in ascolto di più sottoscrittori per il messaggio stesso.
-  **Inviare** -pubblicazione di un messaggio per i listener su cui intervenire. Se nessun listener hanno sottoscritto il messaggio viene ignorato.


Il `MessagingService` è una classe statica con `Subscribe` e `Send` metodi usati nella soluzione.

I messaggi hanno una stringa `message` parametro che viene usato come modo per *indirizzo* messaggi. Il `Subscribe` e `Send` metodi usano parametri generici per controllare ulteriormente la modalità di recapito dei messaggi - due messaggi con lo stesso `message` testo ma gli argomenti di tipo generico diversi non verranno recapitati al sottoscrittore stesso.

L'API per `MessagingCenter` è semplice:

- `Subscribe<TSender> (object subscriber, string message, Action<TSender> callback, TSender source = null)`
- `Subscribe<TSender, TArgs> (object subscriber, string message, Action<TSender, TArgs> callback, TSender source = null)`
- `Send<TSender> (TSender sender, string message)`
- `Send<TSender, TArgs> (TSender sender, string message, TArgs args)`
- `Unsubscribe<TSender, TArgs> (object subscriber, string message)`
- `Unsubscribe<TSender> (object subscriber, string message)`

Questi metodi sono spiegati di seguito.

<a name="Using_the_MessagingCenter" />

## <a name="using-the-messagingcenter"></a>Uso di MessagingCenter

È possibile inviare i messaggi in seguito all'interazione dell'utente (ad esempio, fare clic su un pulsante), un evento di sistema (ad esempio, i controlli di modifica dello stato) o alcuni altri eventi imprevisti (ad esempio, un completamento di download asincrone). I sottoscrittori potrebbero essere in ascolto per modificare l'aspetto dell'interfaccia utente, salvare i dati o attivare un'altra operazione.

### <a name="simple-string-message"></a>Messaggio stringa semplice

Il messaggio più semplice contiene solo una stringa nel `message` parametro. Oggetto `Subscribe` metodo che *è in attesa* per una semplice stringa di messaggio verrà visualizzata sotto - si noti che il tipo generico che specifica il mittente che dovrà essere di tipo `MainPage`. Tutte le classi della soluzione possono sottoscrivere il messaggio utilizzando la seguente sintassi:

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) => {
    // do something whenever the "Hi" message is sent
});
```

Nel `MainPage` il codice seguente della classe *Invia* il messaggio. Il `this` parametro è un'istanza di `MainPage`.

```csharp
MessagingCenter.Send<MainPage> (this, "Hi");
```

La stringa non cambia, indica la *tipo di messaggio* e viene usato per determinare quali sottoscrittori per inviare una notifica. Questo tipo di messaggio viene utilizzato per indicare che si verifica un evento, ad esempio "caricamento è stato completato", in cui non è necessaria alcuna informazione.

### <a name="passing-an-argument"></a>Passaggio di un argomento

Per passare un argomento con il messaggio, specificare il tipo di argomento in di `Subscribe` argomenti generici e della firma di azione.

```csharp
MessagingCenter.Subscribe<MainPage, string> (this, "Hi", (sender, arg) => {
    // do something whenever the "Hi" message is sent
    // using the 'arg' parameter which is a string
});
```

Per inviare il messaggio con argomento, includere il parametro di tipo generico e il valore dell'argomento in di `Send` chiamata al metodo.

```csharp
MessagingCenter.Send<MainPage, string> (this, "Hi", "John");
```

Questo semplice esempio Usa un' `string` , ma qualsiasi argomento C# oggetto può essere passato.

### <a name="unsubscribe"></a>Annullare la sottoscrizione

Un oggetto può annullare la sottoscrizione di una firma del messaggio in modo che nessun futura dei messaggi recapitata. Il `Unsubscribe` la sintassi del metodo deve riflettere la firma del messaggio (quindi, potrebbe essere necessario includere il parametro di tipo generico per l'argomento del messaggio).

```csharp
MessagingCenter.Unsubscribe<MainPage> (this, "Hi");
MessagingCenter.Unsubscribe<MainPage, string> (this, "Hi");
```

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Il MessagingCenter è un modo semplice per ridurre l'accoppiamento, in particolare tra i modelli di visualizzazione. Può essere utilizzato per inviare e ricevere semplici messaggi o passare un argomento tra classi. Le classi devono annullare la sottoscrizione che non desideri più ricevere messaggi.


## <a name="related-links"></a>Collegamenti correlati

- [MessagingCenterSample](https://developer.xamarin.com/samples/UsingMessagingCenter)
- [Esempi di Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
