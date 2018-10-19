---
title: Controlli Android (widget)
description: Blocchi predefiniti per la creazione di interfacce utente di xamarin. Android
ms.prod: xamarin
ms.assetid: B7A82166-B920-4672-B7A2-20DD5E0B5AEF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: 842fb1df2c9cc1aaf1a106687179a3730c2503bd
ms.sourcegitcommit: 7e4070bc104d612b6754ea35dd5a49c5c3d45f4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2018
ms.locfileid: "32436713"
---
# <a name="android-controls-widgets"></a>Controlli Android (widget)

Xamarin. Android espone tutti i controlli dell'interfaccia utente nativa (widget) forniti da Android. Tali controlli possono essere aggiunti facilmente alle App xamarin. Android con Android Designer o a livello di codice tramite i file di layout XML. Indipendentemente dal metodo scelto, xamarin. Android espone tutte le proprietà dell'oggetto dell'interfaccia utente e i metodi in c#. Le sezioni seguenti introducono i controlli dell'interfaccia utente Android più comuni e illustrano come incorporarli in App xamarin. Android.

## <a name="action-barandroiduser-interfacecontrolsaction-barmd"></a>[Sulla barra delle azioni](~/android/user-interface/controls/action-bar.md) 

`ActionBar` è una barra degli strumenti che consente di visualizzare il titolo dell'attività, le interfacce di navigazione e altri elementi interattivi. In genere, la barra delle azioni viene visualizzato nella parte superiore della finestra dell'attività.

![Esempio ActionBar](images/action-bar.png)


## <a name="auto-completeandroiduser-interfacecontrolsauto-completemd"></a>[Completamento automatico](~/android/user-interface/controls/auto-complete.md)

`AutoCompleteTextView` è un elemento di visualizzazione di testo modificabile che Mostra suggerimenti di completamento automaticamente mentre l'utente sta digitando. Viene visualizzato l'elenco di suggerimenti in un elenco a discesa da cui l'utente può scegliere di sostituire il contenuto della casella di modifica con un elemento.

![Esempio di completamento automatico](images/auto-complete.png)


## <a name="buttonsandroiduser-interfacecontrolsbuttonsindexmd"></a>[Pulsanti](~/android/user-interface/controls/buttons/index.md)

I pulsanti sono elementi dell'interfaccia utente che l'utente tocca per eseguire un'azione.

![Pulsanti di esempio](images/buttons.png)


## <a name="calendarandroiduser-interfacecontrolscalendarmd"></a>[Calendario](~/android/user-interface/controls/calendar.md)

Il `Calendar` classe viene utilizzata per la conversione di un'istanza specifica nel tempo (un valore in millisecondi che viene spostato dal periodo) per i valori, ad esempio anno, mese, ora, giorno del mese e la data della settimana successiva.
`Calendar` supporta un'ampia gamma di opzioni di interazione con i dati di calendario, inclusa la possibilità di leggere e scrivere i promemoria, partecipanti ed eventi. Tramite il provider di calendario nell'applicazione, verranno visualizzati i dati che aggiunti tramite l'API nell'app del calendario predefinito fornito con Android.

![Esempio Calendar](images/calendar.png)


## <a name="cardviewandroiduser-interfacecontrolscard-viewmd"></a>[CardView](~/android/user-interface/controls/card-view.md)

`CardView` è un componente dell'interfaccia utente che presenta il contenuto di testo e immagine in viste che sono simili a schede. `CardView` viene implementato come un `FrameLayout` widget con angoli arrotondati e un'ombreggiatura. In genere, un `CardView` viene usato per presentare un elemento di riga singola in un `ListView` o `GridView` gruppo della visualizzazione.

![Visualizzazione di Smart Card di esempio](images/cardview.png)


## <a name="edit-textandroiduser-interfacecontrolsedit-textmd"></a>[Modificare il testo](~/android/user-interface/controls/edit-text.md)

`EditText` è un elemento dell'interfaccia utente che viene usato per l'immissione e modifica di testo.

![Testo di modifica di esempio](images/edit-text.png)


## <a name="galleryandroiduser-interfacecontrolsgallerymd"></a>[Gallery](~/android/user-interface/controls/gallery.md)

`Gallery` è un widget di layout che consente di visualizzare gli elementi in un elenco di scorrimento orizzontale; Posiziona la selezione corrente al centro della visualizzazione.

![Raccolta di esempi](images/gallery.png)


## <a name="navigation-barandroiduser-interfacecontrolsnavigation-barmd"></a>[Barra di spostamento](~/android/user-interface/controls/navigation-bar.md)

Il *barra di spostamento* offre controlli di spostamento nei dispositivi che non includono i pulsanti hardware per **Home**, **Indietro**, e **Menu**.

![Barra di spostamento di esempio](images/navigation-bar.png)


## <a name="pickersandroiduser-interfacecontrolspickersindexmd"></a>[Selezioni](~/android/user-interface/controls/pickers/index.md)

*Controlli di selezione* sono elementi dell'interfaccia utente che consentono all'utente di selezionare una data o ora utilizzando le finestre di dialogo sono disponibili da Android.

![Selezione di esempio](images/picker.png)


## <a name="popup-menuandroiduser-interfacecontrolspopup-menumd"></a>[Menu popup](~/android/user-interface/controls/popup-menu.md)

`PopupMenu` viene usato per la visualizzazione di menu di scelta rapida associati a una vista specifica.

![Menu di scelta rapida di esempio](images/popup-menu.png)


## <a name="ratingbarandroiduser-interfacecontrolsratingbarmd"></a>[RatingBar](~/android/user-interface/controls/ratingbar.md)

Oggetto `RatingBar` è un elemento dell'interfaccia utente che visualizza una classificazione a stelle.

![Esempio di un RatingBar](ratingbar-images/01-ratingbar.png)


## <a name="spinnerandroiduser-interfacecontrolsspinnermd"></a>[Spinner](~/android/user-interface/controls/spinner.md)

`Spinner` è un elemento dell'interfaccia utente che fornisce un modo rapido per selezionare un valore da un set. È simmilar a un elenco di riepilogo a discesa. 

![Casella di selezione di esempio](images/spinner.png)


## <a name="switchandroiduser-interfacecontrolsswitchmd"></a>[Switch](~/android/user-interface/controls/switch.md)

`Switch` è un elemento dell'interfaccia utente che consente all'utente di alternare tra due stati, ad esempio in data o su OFF. Il `Switch` valore predefinito è OFF.

![Opzione di esempio](images/switch.png)


## <a name="textureviewandroiduser-interfacecontrolstexture-viewmd"></a>[TextureView](~/android/user-interface/controls/texture-view.md)

`TextureView` è una vista che utilizza con accelerazione hardware rendering 2D per consentire a un video o un flusso di contenuto OpenGL da visualizzare.

![Visualizzazione di trama di esempio](images/texture-view.png)


## <a name="toolbarandroiduser-interfacecontrolstool-barindexmd"></a>[ToolBar](~/android/user-interface/controls/tool-bar/index.md)

Il `Toolbar` widget (introdotta in Android 5.0 Lollipop) può essere considerato una generalizzazione dell'interfaccia barra azione &ndash; destinato a sostituire la barra delle azioni. Il `Toolbar` possono essere utilizzati ovunque in un layout dell'app ed è molto più personalizzabile rispetto a una barra delle azioni.

![Sulla barra degli strumenti di esempio](images/toolbar.png)


## <a name="viewpagerandroiduser-interfacecontrolsview-pagerindexmd"></a>[ViewPager](~/android/user-interface/controls/view-pager/index.md) 

Il `ViewPager` è un gestore di layout che consente di capovolgere left e right tramite pagine di dati.

![ViewPager di esempio](images/viewpager.png)


## <a name="webviewandroiduser-interfacecontrolsweb-viewmd"></a>[WebView](~/android/user-interface/controls/web-view.md)

`WebView` è un elemento dell'interfaccia utente che consente di creare la propria finestra per la visualizzazione pagine web (o anche lo sviluppo di un browser completato).

![Visualizzazione Web di esempio](images/web-view.png)

