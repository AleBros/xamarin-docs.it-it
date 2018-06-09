---
title: Xamarin. Forms MessagingCenter
description: In questo articolo viene illustrato come utilizzare il MessagingCenter xamarin. Forms per inviare e ricevere messaggi, per ridurre l'accoppiamento tra classi, quali modelli di visualizzazione.
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: 71f526f87a2536110a6d2292cd66a0f4d81c0bfc
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240337"
---
# <a name="xamarinforms-messagingcenter"></a>Xamarin. Forms MessagingCenter

_Xamarin. Forms include un semplice servizio di messaggistica per inviare e ricevere messaggi._

<a name="Overview" />

## <a name="overview"></a>Panoramica

Xamarin. Forms `MessagingCenter` consente di visualizzare modelli e altri componenti per comunicare con senza dover conoscere tra loro oltre a un semplice contratto di messaggio.

<a name="How_the_MessagingCenter_Works" />

## <a name="how-the-messagingcenter-works"></a>Funzionamento di MessagingCenter

Esistono due parti per `MessagingCenter`:

-  **La sottoscrizione** : ascolto dei messaggi con una determinata firma e di eseguire un'azione quando vengono ricevuti. Possono essere in ascolto di più sottoscrittori per lo stesso messaggio.
-  **Inviare** -pubblicazione di un messaggio per i listener possono eseguire operazioni. Se nessun listener hanno sottoscritto il messaggio viene ignorato.


Il `MessagingService` è una classe statica con `Subscribe` e `Send` metodi utilizzati in tutta la soluzione.

I messaggi hanno una stringa `message` parametro viene utilizzato come modalità per *indirizzo* messaggi. Il `Subscribe` e `Send` metodi usano parametri generici per controllare ulteriormente la modalità di recapito messaggi, due messaggi con lo stesso `message` testo senza argomenti di tipo generico diversi non verranno recapitati al sottoscrittore stesso.

L'API per `MessagingCenter` è semplice:

-  La sottoscrizione&lt;TSender > (sottoscrittore di oggetto, stringa di messaggio, azione&lt;TSender > callback, origine TSender = null)
-  La sottoscrizione&lt;TSender, TArgs > (sottoscrittore di oggetto, stringa di messaggio, azione&lt;TSender, TArgs > callback, origine TSender = null)
-  Inviare&lt;TSender > (TSender mittente, messaggio stringa)
-  Inviare&lt;TSender, TArgs > (mittente TSender messaggio stringa, TArgs args)
-  Annullare la sottoscrizione&lt;TSender, TArgs > (sottoscrittore di oggetto, stringa di messaggio)
-  Annullare la sottoscrizione&lt;TSender > (sottoscrittore di oggetto, stringa di messaggio)


Questi metodi sono illustrati di seguito.

<a name="Using_the_MessagingCenter" />

## <a name="using-the-messagingcenter"></a>Utilizzo di MessagingCenter

I messaggi possono essere inviati in seguito all'interazione dell'utente (ad esempio, fare clic su un pulsante), un evento di sistema (ad esempio i controlli di modifica dello stato) o alcuni altri eventi imprevisti (ad esempio, un completamento del download asincrono). I sottoscrittori potrebbero essere in ascolto per modificare l'aspetto dell'interfaccia utente, salvare i dati o attivare un'altra operazione.

### <a name="simple-string-message"></a>Messaggio stringa semplice

Il messaggio più semplice contiene una stringa nel `message` parametro. Oggetto `Subscribe` metodo che *è in ascolto* di seguito è riportata una semplice stringa di messaggio - si noti il tipo generico che specifica il mittente deve essere di tipo `MainPage`. Tutte le classi nella soluzione possono sottoscrivere il messaggio utilizzando questa sintassi:

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) => {
    // do something whenever the "Hi" message is sent
});
```

Nel `MainPage` classe nel codice seguente *Invia* il messaggio. Il `this` parametro è un'istanza di `MainPage`.

```csharp
MessagingCenter.Send<MainPage> (this, "Hi");
```

Non modifica la stringa - indica il *tipo di messaggio* e viene utilizzato per stabilire quali sottoscrittori per notificare. Questo tipo di messaggio viene utilizzato per indicare che un evento si è verificato, ad esempio "caricamento completato", in cui nessuna ulteriore informazione è necessario.

### <a name="passing-an-argument"></a>Passaggio di un argomento

Per passare un argomento con il messaggio, specificare il tipo di argomento in di `Subscribe` argomenti generici e la firma dell'azione.

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

Questo semplice esempio viene utilizzato un `string` argomento ma qualsiasi oggetto c# può essere passato.

### <a name="unsubscribe"></a>annullare la sottoscrizione

Un oggetto può annullare la sottoscrizione di una firma del messaggio in modo che nessun recapito future. Il `Unsubscribe` la sintassi del metodo deve riflettere la firma del messaggio (quindi potrebbe essere necessario includere il parametro di tipo generico per l'argomento di messaggio).

```csharp
MessagingCenter.Unsubscribe<MainPage> (this, "Hi");
MessagingCenter.Unsubscribe<MainPage, string> (this, "Hi");
```

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Il MessagingCenter è un modo semplice per ridurre la correlazione, in particolare tra modelli di visualizzazione. E può essere utilizzato per inviare e ricevere messaggi semplici o passare un argomento tra classi. Classi devono annullare la sottoscrizione non è più che desiderano ricevere messaggi.


## <a name="related-links"></a>Collegamenti correlati

- [MessagingCenterSample](https://developer.xamarin.com/samples/UsingMessagingCenter)
- [Esempi di Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
