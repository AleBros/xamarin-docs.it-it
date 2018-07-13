---
title: Visualizzazione di popup
description: "Xamarin. Forms fornisce due elementi dell'interfaccia utente a comparsa-iscrizione: un avviso e un foglio di azione. Questo articolo viene illustrato come utilizzare il foglio di avviso e l'azione API semplici domande di utenti e per guidare gli utenti tramite le attività."
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 156c2f9dca47a7755d4f810d7921a05662388ded
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996714"
---
# <a name="displaying-pop-ups"></a>Visualizzazione di popup

_Xamarin. Forms fornisce due elementi dell'interfaccia utente a comparsa-iscrizione: un avviso e un foglio di azione. Questo articolo viene illustrato come utilizzare il foglio di avviso e l'azione API semplici domande di utenti e per guidare gli utenti tramite le attività._

Visualizzazione di un avviso o chiedere all'utente di effettuare una scelta è un'attività dell'interfaccia utente comune. Xamarin. Forms sono disponibili due metodi [ `Page` ](xref:Xamarin.Forms.Page) classe per l'interazione con l'utente tramite una finestra popup: [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*) e [ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet*). Essi vengono visualizzati con controlli nativi appropriati in ogni piattaforma.

## <a name="displaying-an-alert"></a>Visualizzazione di un avviso

Tutte le piattaforme supportate da xamarin. Forms hanno una finestra popup modale per avvisare l'utente o porre semplici domande di essi. Per visualizzare gli avvisi di xamarin. Forms, usare il [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*) metodo su qualsiasi [ `Page` ](xref:Xamarin.Forms.Page). La riga di codice seguente viene illustrato un semplice messaggio all'utente:

```csharp
DisplayAlert ("Alert", "You have been alerted", "OK");
```

![](pop-ups-images/alert.png "Finestra di dialogo avviso con un pulsante")

In questo esempio non raccoglie informazioni da parte dell'utente. L'avviso viene visualizzato come form modale e una volta chiusa l'utente continua a interagire con l'applicazione.

Il [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*) metodo può essere utilizzato anche per l'acquisizione di risposta dell'utente presentando due pulsanti e restituendo un `boolean`. Per ottenere una risposta da un avviso, fornire un testo per entrambi i pulsanti e `await` il metodo. Dopo che l'utente ha selezionato una delle opzioni di risposta verrà restituita al codice. Si noti il `async` e `await` parole chiave nel codice di esempio riportato di seguito:

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  var answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![DisplayAlert](pop-ups-images/alert2-sml.png "finestra di dialogo con due pulsanti di avviso")](pop-ups-images/alert2.png#lightbox "finestra di dialogo con due pulsanti di avviso")

## <a name="guiding-users-through-tasks"></a>Guida agli utenti tramite le attività

Il [UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html) è un elemento dell'interfaccia utente comune in iOS. Xamarin. Forms [ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet*) metodo consente di includere questo controllo nelle App multipiattaforma, per il rendering alternative native per Android e UWP.

Per visualizzare un foglio di azione `await` [ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet*) in qualsiasi [ `Page` ](xref:Xamarin.Forms.Page), passando il messaggio e sul pulsante delle etichette come stringhe. Il metodo restituisce l'etichetta di stringa del pulsante su cui è stato scelto dall'utente. Di seguito è riportato un esempio semplice:

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![](pop-ups-images/action.png "Finestra di dialogo ActionSheet")

Il `destroy` pulsante viene eseguito il rendering in modo diverso rispetto agli altri e può essere lasciato `null` o è specificato come terzo parametro di stringa. L'esempio seguente usa il `destroy` pulsante:

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "azione di finestra con il pulsante di eliminazione")](pop-ups-images/action2.png#lightbox "azione di finestra con il pulsante di eliminazione")

## <a name="summary"></a>Riepilogo

Questo articolo illustrato tramite il foglio di avviso e l'azione API semplici domande di utenti e per guidare gli utenti tramite le attività. Xamarin. Forms sono disponibili due metodi [ `Page` ](xref:Xamarin.Forms.Page) classe per l'interazione con l'utente tramite una finestra popup: [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*) e [ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet*), e sono entrambi sottoposti a rendering con controlli nativi appropriati in ogni piattaforma.



## <a name="related-links"></a>Collegamenti correlati

- [PopupsSample](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Pop-ups/)
