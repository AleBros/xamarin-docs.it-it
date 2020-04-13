---
title: MessagingCenter di Xamarin.Forms
description: La classe MessagingCenter di Xamarin.Forms implementa il modello di pubblicazione-sottoscrizione, consentendo la comunicazione basata su messaggi tra componenti che non è pratico collegare con riferimenti a oggetti e tipi.
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
ms.openlocfilehash: 0e5fd88678becd7becfcb1c43e14b1e33aad72de
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "75489880"
---
# <a name="xamarinforms-messagingcenter"></a>MessagingCenter di Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)

Il modello di pubblicazione-sottoscrizione è un modello di messaggistica in cui i server di pubblicazione inviano messaggi senza conoscere i ricevitori, noti come sottoscrittori. In modo analogo, i sottoscrittori sono in ascolto di messaggi specifici, senza conoscere i server di pubblicazione.

Gli eventi in .NET implementano il modello di pubblicazione-sottoscrizione e sono l'approccio più semplice e diretto per un livello di comunicazione tra i componenti se non è necessario un accoppiamento libero, ad esempio un controllo e la pagina che lo contiene. Tuttavia, le durate del server di pubblicazione e del sottoscrittore sono accoppiate tra loro da riferimenti a oggetti e il tipo di sottoscrittore deve avere un riferimento al tipo di server di pubblicazione. Ciò può creare problemi di gestione della memoria, soprattutto quando sono presenti oggetti di breve durata che sottoscrivono un evento di un oggetto statico o di lunga durata. Se il gestore eventi non viene rimosso, il sottoscrittore verrà mantenuto attivo dal riferimento a esso nel server di pubblicazione e ciò impedirà o ritarderà la procedura di Garbage Collection del sottoscrittore.

La classe Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) implementa il modello di pubblicazione-sottoscrizione, consentendo la comunicazione basata su messaggi tra componenti scomodi per il collegamento in base ai riferimenti a oggetti e tipi. Questo meccanismo consente ai server di pubblicazione e ai sottoscrittori di comunicare senza avere un riferimento reciproco, contribuendo a ridurre le dipendenze tra di essi.

La [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe fornisce funzionalità di pubblicazione/sottoscrizione multicast. Ciò significa che possono essere presenti più server di pubblicazione che pubblicano un singolo messaggio e più sottoscrittori in ascolto dello stesso messaggio:

![](messaging-center-images/messaging-center.png "Multicast publish-subscribe functionality")

I server di [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) pubblicazione inviano messaggi utilizzando il [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) metodo , mentre i sottoscrittori ascoltano i messaggi utilizzando il metodo . Inoltre, i sottoscrittori possono anche annullare la [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) sottoscrizione alle sottoscrizioni dei messaggi, se necessario, con il metodo .

> [!IMPORTANT]
> Internamente, [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) la classe utilizza riferimenti deboli. Ciò significa che non manterrà gli oggetti attivi e consentirà di sottoporli a Garbage Collection, quindi dovrebbe essere necessario annullare la sottoscrizione di un messaggio solo quando una classe non vuole più ricevere il messaggio.

## <a name="publish-a-message"></a>Pubblicare un messaggio

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)i messaggi sono stringhe. I server di pubblicazione notificano ai sottoscrittori un messaggio con uno degli [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) overload. L'esempio di codice seguente pubblica un messaggio `Hi`:

```csharp
MessagingCenter.Send<MainPage>(this, "Hi");
```

In questo [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) esempio il metodo specifica un argomento generico che rappresenta il mittente. Per ricevere il messaggio, un sottoscrittore deve anche specificare lo stesso argomento generico, a indicare che è in ascolto di un messaggio proveniente da tale mittente. In questo esempio vengono inoltre specificati due argomenti del metodo:

- Il primo argomento specifica l'istanza del mittente.
- Il secondo argomento specifica il messaggio.

Anche i dati di payload possono essere inviati con un messaggio:

```csharp
MessagingCenter.Send<MainPage, string>(this, "Hi", "John");
```

In questo esempio, il [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) metodo specifica due argomenti generici. Il primo è il tipo che invia il messaggio, mentre il secondo è il tipo dei dati di payload inviati. Per ricevere il messaggio, anche un sottoscrittore deve specificare gli stessi argomenti generici. Ciò consente la presenza di più messaggi che condividono un'identità di messaggio, ma inviano tipi di dati di payload diversi che devono essere ricevuti da sottoscrittori diversi. In questo esempio viene anche specificato un terzo argomento del metodo che rappresenta i dati del payload da inviare al sottoscrittore. In questo caso i dati di payload sono costituiti da un elemento `string`.

Il [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) metodo pubblicherà il messaggio ed eventuali dati di payload, usando un approccio fire-and-forget. Il messaggio viene quindi inviato anche se non sono presenti sottoscrittori registrati per la ricezione del messaggio. In questa situazione, il messaggio inviato viene ignorato.

## <a name="subscribe-to-a-message"></a>Sottoscrivere un messaggio

I sottoscrittori possono registrarsi per [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) ricevere un messaggio utilizzando uno degli overload. Il codice seguente ne è un esempio:

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) =>
{
    // Do something whenever the "Hi" message is received
});
```

In questo esempio, [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) il `this` metodo `Hi` sottoscrive l'oggetto `MainPage` ai messaggi inviati dal tipo ed esegue un delegato di callback in risposta alla ricezione del messaggio. Il delegato di callback, specificato come espressione lambda, potrebbe essere il codice che aggiorna l'interfaccia utente, salva alcuni dati o attiva un'altra operazione.

> [!NOTE]
> Un sottoscrittore potrebbe non essere necessario gestire ogni istanza di un messaggio pubblicato e [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) questo può essere controllato dagli argomenti di tipo generico specificati nel metodo.

Nell'esempio seguente viene illustrato come sottoscrivere un messaggio contenente dati di payload:

```csharp
MessagingCenter.Subscribe<MainPage, string>(this, "Hi", async (sender, arg) =>
{
    await DisplayAlert("Message received", "arg=" + arg, "OK");
});
```

In questo esempio, [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) il `Hi` metodo sottoscrive i `MainPage` messaggi inviati dal `string`tipo, i cui dati di payload sono un oggetto . Un delegato di callback viene eseguito in risposta alla ricezione di un messaggio di questo tipo, che visualizza i dati di payload in un avviso.

> [!IMPORTANT]
> Il delegato eseguito dal `Subscribe` metodo verrà eseguito sullo stesso thread che pubblica `Send` il messaggio utilizzando il metodo .

## <a name="unsubscribe-from-a-message"></a>Annullare la sottoscrizione di un messaggio

I sottoscrittori possono annullare la sottoscrizione dei messaggi che non vogliono più ricevere Questo si ottiene con [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) uno degli overload:

```csharp
MessagingCenter.Unsubscribe<MainPage>(this, "Hi");
```

In questo esempio, [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) il metodo `this` annulla `Hi` la sottoscrizione `MainPage` dell'oggetto dal messaggio inviato dal tipo.

I messaggi contenenti dati di payload [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) devono essere annullati dall'utilizzo dell'overload che specifica due argomenti generici:

```csharp
MessagingCenter.Unsubscribe<MainPage, string>(this, "Hi");
```

In questo esempio, il [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) metodo `this` annulla `Hi` la sottoscrizione `MainPage` dell'oggetto dal messaggio `string`inviato dal tipo, i cui dati di payload sono un oggetto .

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di MessagingCenter](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)
