---
title: Visualizzazione popup
description: "Xamarin. Forms fornisce due elementi dell'interfaccia utente pop configurazione simile: un avviso e un foglio di azione. In questo articolo viene illustrato l'utilizzo di API di foglio di avviso e l'azione per chiedere agli utenti semplici domande e per guidare gli utenti attraverso le attività."
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 2e0a5ff433de034da0170e3aa9a19ab50ddc3cb6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="displaying-pop-ups"></a>Visualizzazione popup

_Xamarin. Forms fornisce due elementi dell'interfaccia utente pop configurazione simile: un avviso e un foglio di azione. In questo articolo viene illustrato l'utilizzo di API di foglio di avviso e l'azione per chiedere agli utenti semplici domande e per guidare gli utenti attraverso le attività._

Visualizzazione di un avviso o chiedere all'utente di effettuare una scelta è un'attività dell'interfaccia utente comune. Sono disponibili due metodi xamarin. Forms il [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) classe per l'interazione con l'utente tramite una finestra popup: [ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)/) e [ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])/). Essere sottoposti a rendering con appropriati controlli nativi in ciascuna piattaforma.

## <a name="displaying-an-alert"></a>Visualizzazione di un avviso

Tutte le piattaforme supportate xamarin. Forms dispongono di una finestra popup modale per avvisare l'utente o porre domande semplici di essi. Per visualizzare gli avvisi in xamarin. Forms, utilizzare il [ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)/) metodo su qualsiasi [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/). La riga di codice seguente viene illustrato un semplice messaggio all'utente:

```csharp
DisplayAlert ("Alert", "You have been alerted", "OK");
```

![](pop-ups-images/alert.png "Finestra di dialogo avviso con un pulsante")

In questo esempio non raccoglie informazioni da parte dell'utente. L'avviso viene visualizzato come form modale e una volta annullata l'utente continua a interagire con l'applicazione.

Il [ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)/) metodo può essere utilizzato anche per acquisire il risponde utente presenta due pulsanti e restituendo un `boolean`. Per ottenere una risposta da un avviso, fornire il testo per entrambi i pulsanti e `await` il metodo. Dopo che l'utente seleziona una delle opzioni di risposta verrà restituita al codice. Si noti il `async` e `await` parole chiave nell'esempio di codice riportato di seguito:

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  var answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![DisplayAlert](pop-ups-images/alert2-sml.png "avviso finestra di dialogo con due pulsanti")](pop-ups-images/alert2.png#lightbox "con due pulsanti nella finestra di dialogo di avviso")

## <a name="guiding-users-through-tasks"></a>Guida agli utenti tramite le attività

Il [UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html) è un elemento dell'interfaccia utente comune in iOS. Di xamarin. Forms [ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])/) metodo consente di includere il controllo in applicazioni di piattaforme, il rendering alternative native in Android e Windows Phone.

Per visualizzare un foglio di azione, `await` [ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])/) in qualsiasi [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/), passando il messaggio e pulsante etichette come stringhe. Il metodo restituisce l'etichetta della stringa del pulsante su cui è stato scelto dall'utente. Di seguito è riportato un esempio semplice:

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![](pop-ups-images/action.png "Finestra di dialogo ActionSheet")

Il `destroy` pulsante viene eseguito il rendering in modo diverso rispetto alle altre e può essere lasciato `null` o specificato come terzo parametro di stringa. L'esempio seguente usa il `destroy` pulsante:

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "azione di finestra con il pulsante Elimina")](pop-ups-images/action2.png#lightbox "azione di finestra con pulsante di eliminazione")

## <a name="summary"></a>Riepilogo

Questo articolo sono state illustrate utilizzando il foglio di avviso e l'azione API per richiedere agli utenti semplici domande e per guidare gli utenti attraverso le attività. Sono disponibili due metodi xamarin. Forms il [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) classe per l'interazione con l'utente tramite una finestra popup: [ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)/) e [ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])/), e sono sia sottoposto a rendering con appropriati controlli nativi in ciascuna piattaforma.



## <a name="related-links"></a>Collegamenti correlati

- [PopupsSample](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Pop-ups/)
