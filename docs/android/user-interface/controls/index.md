---
title: Controlli Android (widget)
description: Blocchi predefiniti per la creazione di interfacce utente Novell. Android
ms.prod: xamarin
ms.assetid: B7A82166-B920-4672-B7A2-20DD5E0B5AEF
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 25e33ee2d77501b3913a3598ef7855714f0b9bc1
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2020
ms.locfileid: "78293075"
---
# <a name="xamarinandroid-controls-widgets"></a>Controlli Novell. Android (widget)

Novell. Android espone tutti i controlli dell'interfaccia utente (widget) nativi forniti da Android. Questi controlli possono essere facilmente aggiunti alle app Novell. Android usando il Android Designer o a livello tramite i file di layout XML. Indipendentemente dal metodo scelto, Novell. Android espone tutti i metodi e le proprietà degli oggetti dell'interfaccia utente in C#. Le sezioni seguenti introducono i controlli più comuni dell'interfaccia utente di Android e spiegano come incorporarli nelle app Novell. Android.

## <a name="action-bar"></a>[Barra delle azioni](~/android/user-interface/controls/action-bar.md) 

`ActionBar` è una barra degli strumenti che Visualizza il titolo dell'attività, le interfacce di navigazione e altri elementi interattivi. In genere, la barra delle azioni viene visualizzata nella parte superiore della finestra di un'attività.

![ActionBar di esempio](images/action-bar.png)

## <a name="auto-complete"></a>[Completamento automatico](~/android/user-interface/controls/auto-complete.md)

`AutoCompleteTextView` è un elemento di visualizzazione di testo modificabile che mostra automaticamente i suggerimenti di completamento mentre l'utente sta digitando. L'elenco di suggerimenti viene visualizzato in un menu a discesa da cui l'utente può scegliere un elemento in cui sostituire il contenuto della casella di modifica.

![Esempio di completamento automatico](images/auto-complete.png)

## <a name="buttons"></a>[Pulsanti](~/android/user-interface/controls/buttons/index.md)

I pulsanti sono elementi dell'interfaccia utente che vengono toccati dall'utente per eseguire un'azione.

![Pulsanti di esempio](images/buttons.png)

## <a name="calendar"></a>[Calendarioio](~/android/user-interface/controls/calendar.md)

La classe `Calendar` viene utilizzata per la conversione di un'istanza specifica nel tempo (un valore in millisecondi offset rispetto al periodo) a valori quali anno, mese, ora, giorno del mese e data della settimana successiva.
`Calendar` supporta un'ampia gamma di opzioni di interazione con i dati del calendario, inclusa la possibilità di leggere e scrivere eventi, partecipanti e promemoria. Usando il provider di calendario nell'applicazione, i dati aggiunti tramite l'API verranno visualizzati nell'app Calendar incorporata fornita con Android.

![Calendario di esempio](images/calendar.png)

## <a name="cardview"></a>[CardView](~/android/user-interface/controls/card-view.md)

`CardView` è un componente dell'interfaccia utente che presenta il contenuto di testo e immagini in visualizzazioni simili a schede. `CardView` viene implementato come un widget `FrameLayout` con angoli arrotondati e un'ombreggiatura. In genere, viene utilizzato un `CardView` per presentare un singolo elemento riga in un gruppo di `ListView` o `GridView` visualizzazione.

![Visualizzazione scheda di esempio](images/cardview.png)

## <a name="edit-text"></a>[Modifica testo](~/android/user-interface/controls/edit-text.md)

`EditText` è un elemento dell'interfaccia utente utilizzato per l'immissione e la modifica di testo.

![Testo di modifica di esempio](images/edit-text.png)

## <a name="gallery"></a>[Raccolta](~/android/user-interface/controls/gallery.md)

`Gallery` è un widget di layout usato per visualizzare gli elementi in un elenco a scorrimento orizzontale; posiziona la selezione corrente al centro della visualizzazione.

![Raccolta di esempio](images/gallery.png)

## <a name="navigation-bar"></a>[Barra di spostamento](~/android/user-interface/controls/navigation-bar.md)

La *barra di spostamento* fornisce controlli di spostamento nei dispositivi che non includono i pulsanti hardware per **Home**, **back**e **menu**.

![Barra di spostamento di esempio](images/navigation-bar.png)

## <a name="pickers"></a>[Selezioni](~/android/user-interface/controls/pickers/index.md)

I *selezionatori* sono elementi dell'interfaccia utente che consentono all'utente di selezionare una data o un'ora usando le finestre di dialogo fornite da Android.

![Selezione di esempio](images/picker.png)

## <a name="popup-menu"></a>[Menu popup](~/android/user-interface/controls/popup-menu.md)

`PopupMenu` viene utilizzato per visualizzare i menu popup collegati a una visualizzazione specifica.

![Menu di scelta rapida di esempio](images/popup-menu.png)

## <a name="ratingbar"></a>[RatingBar](~/android/user-interface/controls/ratingbar.md)

Un `RatingBar` è un elemento dell'interfaccia utente che visualizza una classificazione in stelle.

![Esempio di RatingBar](ratingbar-images/01-ratingbar.png)

## <a name="spinner"></a>[Spinner](~/android/user-interface/controls/spinner.md)

`Spinner` è un elemento dell'interfaccia utente che fornisce un modo rapido per selezionare un valore da un set. È simile a un elenco a discesa. 

![Casella di selezione di esempio](images/spinner.png)

## <a name="switch"></a>[Switch](~/android/user-interface/controls/switch.md)

`Switch` è un elemento dell'interfaccia utente che consente a un utente di passare tra due Stati, ad esempio ON o OFF. Il valore predefinito `Switch` è disattivato.

![Switch di esempio](images/switch.png)

## <a name="textureview"></a>[TextureView](~/android/user-interface/controls/texture-view.md)

`TextureView` è una vista che usa il rendering 2D con accelerazione hardware per consentire la visualizzazione di un flusso di contenuto video o OpenGL.

![Esempio di visualizzazione trama](images/texture-view.png)

## <a name="toolbar"></a>[ToolBar](~/android/user-interface/controls/tool-bar/index.md)

Il widget `Toolbar` (introdotto in Android 5,0 Lollipop) può essere considerato come una generalizzazione dell'interfaccia della barra delle azioni &ndash; si intende sostituire la barra delle azioni. Il `Toolbar` può essere usato in qualsiasi punto del layout dell'app ed è molto più personalizzabile rispetto a una barra delle azioni.

![Barra degli strumenti di esempio](images/toolbar.png)

## <a name="viewpager"></a>[ViewPager](~/android/user-interface/controls/view-pager/index.md) 

Il `ViewPager` è un gestore di layout che consente all'utente di capovolgere verso sinistra e verso destra le pagine di dati.

![ViewPager di esempio](images/viewpager.png)

## <a name="webview"></a>[WebView](~/android/user-interface/controls/web-view.md)

`WebView` è un elemento dell'interfaccia utente che consente di creare una finestra personalizzata per la visualizzazione di pagine Web o persino per lo sviluppo di un browser completo.

![Visualizzazione Web di esempio](images/web-view.png)
