---
title: MessagingCenter di Xamarin.Forms
description: Questo articolo illustra come usare MessagingCenter di Xamarin.Forms per inviare e ricevere messaggi per ridurre l'accoppiamento tra le classi, ad esempio i modelli di visualizzazione.
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: b40617dc9ed2054540ce04d5527fae8de6e2285b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644903"
---
# <a name="xamarinforms-messagingcenter"></a>MessagingCenter di Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)

_Xamarin.Forms include un servizio di messaggistica semplice per l'invio e la ricezione di messaggi._

<a name="Overview" />

## <a name="overview"></a>Panoramica

`MessagingCenter` di Xamarin.Forms abilita modelli di visualizzazione e altri componenti per comunicare senza che sia necessaria una conoscenza reciproca. È sufficiente un contratto di messaggio.

<a name="How_the_MessagingCenter_Works" />

## <a name="how-the-messagingcenter-works"></a>Come funziona MessagingCenter

`MessagingCenter` si compone di due parti:

-  **Subscribe**: ascolto dei messaggi con una determinata firma ed esecuzione di un'azione alla ricezione. Più sottoscrittori possono essere in ascolto dello stesso messaggio.
-  **Send**: pubblicazione di un messaggio che richiede un'azione da parte dei listener. Se nessun listener ha effettuato la sottoscrizione, il messaggio viene ignorato.

`MessagingCenter` è una classe statica con i metodi `Subscribe` e `Send` che vengono usati in tutta la soluzione.

I messaggi includono un parametro `message` di stringa che viene usato per *indirizzare* i messaggi. I metodi `Subscribe` e `Send` usano parametri generici per controllare ulteriormente le modalità di recapito dei messaggi: due messaggi con lo stesso testo `message`, ma con argomenti di tipo generico diversi non verranno recapitati allo stesso sottoscrittore.

L'API per `MessagingCenter` è semplice:

- `Subscribe<TSender> (object subscriber, string message, Action<TSender> callback, TSender source = null)`
- `Subscribe<TSender, TArgs> (object subscriber, string message, Action<TSender, TArgs> callback, TSender source = null)`
- `Send<TSender> (TSender sender, string message)`
- `Send<TSender, TArgs> (TSender sender, string message, TArgs args)`
- `Unsubscribe<TSender, TArgs> (object subscriber, string message)`
- `Unsubscribe<TSender> (object subscriber, string message)`

Questi metodi sono illustrati di seguito.

<a name="Using_the_MessagingCenter" />

## <a name="using-the-messagingcenter"></a>Uso di MessagingCenter

I messaggi possono essere inviati in seguito all'interazione dell'utente (come la selezione di un pulsante), a un evento di sistema (come la modifica dello stato dei controlli) o ad altri eventi imprevisti (come il completamento di un download asincrono). I sottoscrittori potrebbero essere in ascolto per cambiare l'aspetto dell'interfaccia utente, salvare dati o attivare altre operazioni.

Per altre informazioni sull'uso della classe `MessagingCenter`, vedere [Comunicazioni tra componenti ad accoppiamento debole](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md).

### <a name="simple-string-message"></a>Messaggio stringa semplice

Il messaggio più semplice contiene una sola stringa nel parametro `message`. Di seguito viene illustrato un metodo `Subscribe` in *ascolto* di un messaggio stringa semplice. Si noti che il tipo generico che specifica il mittente deve essere di tipo `MainPage`. Tutte le classi della soluzione possono sottoscrivere il messaggio usando questa sintassi:

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) => {
    // do something whenever the "Hi" message is sent
});
```

Nella classe `MainPage` il codice seguente *invia* il messaggio. Il parametro `this` è un'istanza di `MainPage`.

```csharp
MessagingCenter.Send<MainPage> (this, "Hi");
```

La stringa non cambia: indica il *tipo di messaggio* ed è usata per determinare i sottoscrittori a cui inviare una notifica. Questo tipo di messaggio viene usato per indicare che si è verificato un evento, ad esempio "Caricamento completato", in cui non sono necessarie altre informazioni.

### <a name="passing-an-argument"></a>Passare un argomento

Per passare un argomento con il messaggio, specificare il tipo di argomento negli argomenti generici `Subscribe` e nella firma dell'azione.

```csharp
MessagingCenter.Subscribe<MainPage, string> (this, "Hi", (sender, arg) => {
    // do something whenever the "Hi" message is sent
    // using the 'arg' parameter which is a string
});
```

Per inviare il messaggio con un argomento, includere il parametro generico del tipo e il valore dell'argomento nella chiamata al metodo `Send`.

```csharp
MessagingCenter.Send<MainPage, string> (this, "Hi", "John");
```

Questo semplice esempio usa un argomento `string`, ma è possibile passare qualsiasi oggetto C#.

### <a name="unsubscribe"></a>Annullare la sottoscrizione

Un oggetto può annullare la sottoscrizione alla firma di un messaggio in modo che i futuri messaggi non vengano recapitati. La sintassi del metodo `Unsubscribe` deve riflettere la firma del messaggio e può quindi essere necessario includere il parametro del tipo generico per l'argomento del messaggio.

```csharp
MessagingCenter.Unsubscribe<MainPage> (this, "Hi");
MessagingCenter.Unsubscribe<MainPage, string> (this, "Hi");
```

<a name="Summary" />

## <a name="summary"></a>Riepilogo

MessagingCenter è un modo semplice per ridurre l'accoppiamento, in particolare tra i modelli di visualizzazione. Può essere usato per inviare e ricevere messaggi semplici o passare un argomento tra le classi. Le classi devono annullare la sottoscrizione ai messaggi che non desiderano ricevere più.


## <a name="related-links"></a>Collegamenti correlati

- [Esempio di MessagingCenter](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)
- [Esempi di Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
- [Comunicazioni tra componenti poco accoppiati](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md)
