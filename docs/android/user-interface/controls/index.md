---
title: Controlli Android (widget)
description: Blocchi predefiniti per la creazione di interfacce utente Xamarin.Android
ms.prod: xamarin
ms.assetid: B7A82166-B920-4672-B7A2-20DD5E0B5AEF
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 8c0a2dffbe312cb25258cd2738b661ded2df8d7d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029267"
---
# <a name="xamarinandroid-controls-widgets"></a>Controlli Xamarin.Android (widget)

Xamarin.Android espone tutti i controlli dell'interfaccia utente (widget) nativi forniti da Android. Questi controlli possono essere facilmente aggiunti alle app Xamarin.Android usando il Android Designer o a livello tramite i file di layout XML. Indipendentemente dal metodo scelto, Xamarin.Android espone tutti i metodi e le proprietà degli oggetti dell'interfaccia utente in C#. Le sezioni seguenti introducono i controlli più comuni dell'interfaccia utente di Android e spiegano come incorporarli nelle app Xamarin.Android.

## <a name="action-barandroiduser-interfacecontrolsaction-barmd"></a>[Barra delle azioni](~/android/user-interface/controls/action-bar.md) 

`ActionBar` è una barra degli strumenti che Visualizza il titolo dell'attività, le interfacce di navigazione e altri elementi interattivi. In genere, la barra delle azioni viene visualizzata nella parte superiore della finestra di un'attività.

![ActionBar di esempio](images/action-bar.png)

## <a name="auto-completeandroiduser-interfacecontrolsauto-completemd"></a>[Completamento automatico](~/android/user-interface/controls/auto-complete.md)

`AutoCompleteTextView` è un elemento di visualizzazione di testo modificabile che mostra automaticamente i suggerimenti di completamento mentre l'utente sta digitando. L'elenco di suggerimenti viene visualizzato in un menu a discesa da cui l'utente può scegliere un elemento in cui sostituire il contenuto della casella di modifica.

![Esempio di completamento automatico](images/auto-complete.png)

## <a name="buttonsandroiduser-interfacecontrolsbuttonsindexmd"></a>[Pulsanti](~/android/user-interface/controls/buttons/index.md)

I pulsanti sono elementi dell'interfaccia utente che vengono toccati dall'utente per eseguire un'azione.

![Pulsanti di esempio](images/buttons.png)

## <a name="calendarandroiduser-interfacecontrolscalendarmd"></a>[Calendario](~/android/user-interface/controls/calendar.md)

La classe `Calendar` viene utilizzata per la conversione di un'istanza specifica nel tempo (un valore in millisecondi offset rispetto al periodo) a valori quali anno, mese, ora, giorno del mese e data della settimana successiva.
`Calendar` supporta un'ampia gamma di opzioni di interazione con i dati del calendario, inclusa la possibilità di leggere e scrivere eventi, partecipanti e promemoria. Usando il provider di calendario nell'applicazione, i dati aggiunti tramite l'API verranno visualizzati nell'app Calendar incorporata fornita con Android.

![Calendario di esempio](images/calendar.png)

## <a name="cardviewandroiduser-interfacecontrolscard-viewmd"></a>[CardView](~/android/user-interface/controls/card-view.md)

`CardView` è un componente dell'interfaccia utente che presenta il contenuto di testo e immagini in visualizzazioni simili a schede. `CardView` viene implementato come un widget `FrameLayout` con angoli arrotondati e un'ombreggiatura. In genere, viene utilizzato un `CardView` per presentare un singolo elemento riga in un gruppo di `ListView` o `GridView` visualizzazione.

![Visualizzazione scheda di esempio](images/cardview.png)

## <a name="edit-textandroiduser-interfacecontrolsedit-textmd"></a>[Modifica testo](~/android/user-interface/controls/edit-text.md)

`EditText` è un elemento dell'interfaccia utente utilizzato per l'immissione e la modifica di testo.

![Testo di modifica di esempio](images/edit-text.png)

## <a name="galleryandroiduser-interfacecontrolsgallerymd"></a>[Gallery](~/android/user-interface/controls/gallery.md)

`Gallery` è un widget di layout usato per visualizzare gli elementi in un elenco a scorrimento orizzontale; posiziona la selezione corrente al centro della visualizzazione.

![Raccolta di esempio](images/gallery.png)

## <a name="navigation-barandroiduser-interfacecontrolsnavigation-barmd"></a>[Barra di spostamento](~/android/user-interface/controls/navigation-bar.md)

La *barra di spostamento* fornisce controlli di spostamento nei dispositivi che non includono i pulsanti hardware per **Home**, **back**e **menu**.

![Barra di spostamento di esempio](images/navigation-bar.png)

## <a name="pickersandroiduser-interfacecontrolspickersindexmd"></a>[Selezioni](~/android/user-interface/controls/pickers/index.md)

I *selezionatori* sono elementi dell'interfaccia utente che consentono all'utente di selezionare una data o un'ora usando le finestre di dialogo fornite da Android.

![Selezione di esempio](images/picker.png)

## <a name="popup-menuandroiduser-interfacecontrolspopup-menumd"></a>[Menu popup](~/android/user-interface/controls/popup-menu.md)

`PopupMenu` viene utilizzato per visualizzare i menu popup collegati a una visualizzazione specifica.

![Menu di scelta rapida di esempio](images/popup-menu.png)

## <a name="ratingbarandroiduser-interfacecontrolsratingbarmd"></a>[RatingBar](~/android/user-interface/controls/ratingbar.md)

Un `RatingBar` è un elemento dell'interfaccia utente che visualizza una classificazione in stelle.

![Esempio di RatingBar](ratingbar-images/01-ratingbar.png)

## <a name="spinnerandroiduser-interfacecontrolsspinnermd"></a>[Spinner](~/android/user-interface/controls/spinner.md)

`Spinner` è un elemento dell'interfaccia utente che fornisce un modo rapido per selezionare un valore da un set. È simile a un elenco a discesa. 

![Casella di selezione di esempio](images/spinner.png)

## <a name="switchandroiduser-interfacecontrolsswitchmd"></a>[Switch](~/android/user-interface/controls/switch.md)

`Switch` è un elemento dell'interfaccia utente che consente a un utente di passare tra due Stati, ad esempio ON o OFF. Il valore predefinito `Switch` è disattivato.

![Switch di esempio](images/switch.png)

## <a name="textureviewandroiduser-interfacecontrolstexture-viewmd"></a>[TextureView](~/android/user-interface/controls/texture-view.md)

`TextureView` è una vista che usa il rendering 2D con accelerazione hardware per consentire la visualizzazione di un flusso di contenuto video o OpenGL.

![Esempio di visualizzazione trama](images/texture-view.png)

## <a name="toolbarandroiduser-interfacecontrolstool-barindexmd"></a>[ToolBar](~/android/user-interface/controls/tool-bar/index.md)

Il widget `Toolbar` (introdotto in Android 5,0 Lollipop) può essere considerato come una generalizzazione dell'interfaccia della barra delle azioni &ndash; si intende sostituire la barra delle azioni. Il `Toolbar` può essere usato in qualsiasi punto del layout dell'app ed è molto più personalizzabile rispetto a una barra delle azioni.

![Barra degli strumenti di esempio](images/toolbar.png)

## <a name="viewpagerandroiduser-interfacecontrolsview-pagerindexmd"></a>[ViewPager](~/android/user-interface/controls/view-pager/index.md) 

Il `ViewPager` è un gestore di layout che consente all'utente di capovolgere verso sinistra e verso destra le pagine di dati.

![ViewPager di esempio](images/viewpager.png)

## <a name="webviewandroiduser-interfacecontrolsweb-viewmd"></a>[WebView](~/android/user-interface/controls/web-view.md)

`WebView` è un elemento dell'interfaccia utente che consente di creare una finestra personalizzata per la visualizzazione di pagine Web o persino per lo sviluppo di un browser completo.

![Visualizzazione Web di esempio](images/web-view.png)
