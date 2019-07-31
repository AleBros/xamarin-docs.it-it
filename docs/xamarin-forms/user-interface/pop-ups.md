---
title: Visualizza popup
description: "Xamarin.Forms offre due elementi dell'interfaccia utente di tipo popup: un avviso e una finestra delle azioni. Questo articolo illustra l'uso delle API di avviso e foglio di azione per visualizzare le finestre di dialogo che consentono agli utenti di semplificare le domande e guidare gli utenti attraverso le attività."
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 3b6b2ea2d497c9fdce2c323c7f7a793a4186aa4f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656102"
---
# <a name="display-pop-ups"></a>Visualizza popup

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)

_Xamarin.Forms offre due elementi dell'interfaccia utente di tipo popup: un avviso e una finestra delle azioni. Questo articolo illustra l'uso delle API di avviso e foglio di azione per visualizzare le finestre di dialogo che consentono agli utenti di semplificare le domande e guidare gli utenti attraverso le attività._

Visualizzare un avviso o rivolgere una domanda a un utente perché prenda una decisione è un'attività comune dell'interfaccia utente. In Xamarin.Forms sono disponibili due metodi nella classe [`Page`](xref:Xamarin.Forms.Page) che consentono di interagire con l'utente usando un popup: [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) e [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*). Il rendering di questi metodi viene eseguito con i controlli nativi di ogni piattaforma.

## <a name="display-an-alert"></a>Visualizzare un avviso

In tutte le piattaforme supportate da Xamarin.Forms è disponibile un popup modale che consente di inviare un avviso o rivolgere semplici domande a un utente. Per visualizzare questi avvisi in Xamarin.Forms, usare il metodo [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) in qualsiasi classe [`Page`](xref:Xamarin.Forms.Page). La riga di codice seguente illustra un semplice messaggio per l'utente:

```csharp
DisplayAlert ("Alert", "You have been alerted", "OK");
```

![](pop-ups-images/alert.png "Finestra di dialogo dell'avviso con un pulsante")

In questo esempio non vengono raccolte informazioni dall'utente. L'avviso viene visualizzato come modale. Quando viene ignorato, l'utente continua a interagire con l'applicazione.

Il metodo [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) può essere usato anche per acquisire una risposta dell'utente, visualizzando due pulsanti e restituendo un valore `boolean`. Per ottenere una risposta da un avviso, specificare un testo per i due pulsanti e il metodo `await`. Dopo aver selezionato una delle opzioni, la risposta dell'utente sarà restituita al codice. Si notino le parole chiave `async` e `await` nell'esempio di codice seguente:

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  bool answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![DisplayAlert](pop-ups-images/alert2-sml.png "Finestra di avviso con due pulsanti")](pop-ups-images/alert2.png#lightbox "Finestra di avviso con due pulsanti")

## <a name="guide-users-through-tasks"></a>Guida per gli utenti attraverso le attività

[UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html) è un elemento comune dell'interfaccia utente in iOS. Il metodo [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) di Xamarin.Forms consente di includere questo controllo nelle app multipiattaforma, eseguendo il rendering delle alternative native in Android e nella piattaforma UWP.

Per visualizzare un foglio delle azioni, impostare `await` [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) in qualsiasi oggetto [`Page`](xref:Xamarin.Forms.Page), passando il messaggio e le etichette dei pulsanti come stringhe. Il metodo restituisce l'etichetta di stringa del pulsante che è stato selezionato dall'utente. Di seguito è riportato un semplice esempio:

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![](pop-ups-images/action.png "Finestra di dialogo ActionSheet")

Il rendering del pulsante `destroy` viene eseguito diversamente rispetto agli altri pulsanti. È possibile lasciare il valore `null` o è possibile specificarlo come il terzo parametro di stringa. Nell'esempio seguente viene usato il pulsante `destroy`:

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "Finestra di dialogo Action Sheet con pulsante Destroy")](pop-ups-images/action2.png#lightbox "Finestra di dialogo Action Sheet con pulsante Destroy")

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di popup](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)
