---
title: Controlli
description: Blocchi predefiniti per la creazione di interfacce utente xamarin
ms.prod: xamarin
ms.assetid: B7A82166-B920-4672-B7A2-20DD5E0B5AEF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 8994a8988c0e32e85aedcd9110e3583195843862
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="controls"></a>Controlli


Xamarin espone tutti i controlli dell'interfaccia utente nativa (widget) forniti da Android. Questi controlli possono essere facilmente aggiunte per App xamarin mediante la finestra di progettazione Android o a livello di programmazione tramite il file di layout di XML. Indipendentemente dal metodo scelto, xamarin espone tutte le proprietà dell'oggetto dell'interfaccia utente e i metodi in c#. Nelle sezioni seguenti introducono i controlli dell'interfaccia utente Android più comuni e viene illustrato come incorporare le app xamarin.

## <a name="action-barandroiduser-interfacecontrolsaction-barmd"></a>[Sulla barra delle azioni](~/android/user-interface/controls/action-bar.md) 

`ActionBar` è una barra degli strumenti che consente di visualizzare il titolo dell'attività, le interfacce di navigazione e altri elementi interattivi. In genere, la barra di azione viene visualizzata nella parte superiore della finestra di un'attività.

![Esempio ActionBar](images/action-bar.png)


## <a name="auto-completeandroiduser-interfacecontrolsauto-completemd"></a>[Completamento automatico](~/android/user-interface/controls/auto-complete.md)

`AutoCompleteTextView` è un elemento di visualizzazione di testo modificabile che consente di visualizzare automaticamente i suggerimenti di completamento mentre l'utente digita. In un elenco a discesa da cui l'utente può scegliere un elemento da sostituire il contenuto della casella di modifica con menu viene visualizzato l'elenco di suggerimenti.

![Esempio di completamento automatico](images/auto-complete.png)


## <a name="buttonsandroiduser-interfacecontrolsbuttonsindexmd"></a>[Pulsanti](~/android/user-interface/controls/buttons/index.md)

I pulsanti sono elementi dell'interfaccia utente che l'utente tocca per eseguire un'azione.

![Pulsanti di esempio](images/buttons.png)


## <a name="calendarandroiduser-interfacecontrolscalendarmd"></a>[Calendario](~/android/user-interface/controls/calendar.md)

La `Calendar` classe viene utilizzata per la conversione di un'istanza specifica nel tempo (un valore in millisecondi che viene spostato dal periodo) per i valori, ad esempio anno, mese, ora, giorno del mese e la data della settimana successiva.
`Calendar` supporta una vasta gamma di opzioni di interazione con i dati di calendario, inclusa la possibilità di leggere e scrivere gli eventi, i partecipanti e i promemoria. Tramite il provider di calendario nell'applicazione, i dati che aggiunti tramite l'API verranno visualizzato nell'app del calendario predefinito che viene fornito con Android.

![Calendario di esempio](images/calendar.png)


## <a name="cardviewandroiduser-interfacecontrolscard-viewmd"></a>[CardView](~/android/user-interface/controls/card-view.md)

`CardView` è un componente dell'interfaccia utente che presenta il contenuto di testo e immagine in viste che sono simili a schede. `CardView` viene implementato come un `FrameLayout` widget con angoli arrotondati e un'ombreggiatura. In genere, un `CardView` viene usato per presentare un elemento di singola riga in un `ListView` o `GridView` gruppo di visualizzazione.

![Visualizzazione di Smart Card di esempio](images/cardview.png)


## <a name="edit-textandroiduser-interfacecontrolsedit-textmd"></a>[Modificare il testo](~/android/user-interface/controls/edit-text.md)

`EditText` è un elemento dell'interfaccia utente che viene utilizzato per l'immissione e modifica del testo.

![Esempio di modifica testo](images/edit-text.png)


## <a name="galleryandroiduser-interfacecontrolsgallerymd"></a>[Gallery](~/android/user-interface/controls/gallery.md)

`Gallery` è un widget di layout che viene utilizzato per visualizzare gli elementi in un elenco di scorrimento orizzontale. la selezione corrente viene posizionato al centro della vista.

![Raccolta di esempio](images/gallery.png)


## <a name="navigation-barandroiduser-interfacecontrolsnavigation-barmd"></a>[Barra di spostamento](~/android/user-interface/controls/navigation-bar.md)

Il *barra di spostamento* fornisce controlli per la navigazione nei dispositivi che non includono i pulsanti hardware **Home**, **nuovamente**, e **Menu**.

![Barra di spostamento di esempio](images/navigation-bar.png)


## <a name="pickersandroiduser-interfacecontrolspickersindexmd"></a>[Selezioni](~/android/user-interface/controls/pickers/index.md)

*Le selezioni* sono elementi dell'interfaccia utente che consentono all'utente di selezionare una data o ora utilizzando le finestre di dialogo fornito da Android.

![Selezione di esempio](images/picker.png)


## <a name="popup-menuandroiduser-interfacecontrolspopup-menumd"></a>[Menu popup](~/android/user-interface/controls/popup-menu.md)

`PopupMenu` viene utilizzato per visualizzare il menu di scelta rapida associati a una vista specifica.

![Menu di scelta rapida di esempio](images/popup-menu.png)


## <a name="spinnerandroiduser-interfacecontrolsspinnermd"></a>[Spinner](~/android/user-interface/controls/spinner.md)

`Spinner` è un elemento dell'interfaccia utente che fornisce un modo rapido per selezionare un valore da un set. È simmilar a un elenco a discesa. 

![Casella di selezione di esempio](images/spinner.png)


## <a name="switchandroiduser-interfacecontrolsswitchmd"></a>[Switch](~/android/user-interface/controls/switch.md)

`Switch` è un elemento dell'interfaccia utente che consente agli utenti di passare tra due stati, ad esempio come in o OFF. Il `Switch` valore predefinito è OFF.

![Opzione di esempio](images/switch.png)


## <a name="textureviewandroiduser-interfacecontrolstexture-viewmd"></a>[TextureView](~/android/user-interface/controls/texture-view.md)

`TextureView` è una vista che utilizza rendering con accelerazione hardware 2D per consentire a un video o un flusso di contenuto OpenGL da visualizzare.

![Visualizzazione di trama di esempio](images/texture-view.png)


## <a name="toolbarandroiduser-interfacecontrolstool-barindexmd"></a>[ToolBar](~/android/user-interface/controls/tool-bar/index.md)

Il `Toolbar` widget, introdotto in Android 5.0 simbolo, può essere considerato come una generalizzazione dell'interfaccia barra azione &ndash; è destinato a sostituire la barra delle azioni. Il `Toolbar` possono essere utilizzate ovunque in un layout dell'app ed è molto più personalizzabile rispetto a una barra di azione.

![Barra degli strumenti di esempio](images/toolbar.png)


## <a name="viewpagerandroiduser-interfacecontrolsview-pagerindexmd"></a>[ViewPager](~/android/user-interface/controls/view-pager/index.md) 

Il `ViewPager` è un gestore di layout che consente all'utente di scorrere left e right le pagine di dati.

![Esempio ViewPager](images/viewpager.png)


## <a name="webviewandroiduser-interfacecontrolsweb-viewmd"></a>[WebView](~/android/user-interface/controls/web-view.md)

`WebView` è un elemento dell'interfaccia utente che consente di creare la propria finestra per la visualizzazione delle pagine web (o anche lo sviluppo di un browser completo).

![Visualizzazione Web di esempio](images/web-view.png)

