---
title: Utilizzo di Controller barra delle schede
description: Questo articolo descrive la progettazione e l'utilizzo di Controller barra scheda all'interno di un'app Xamarin.tvOS.
ms.topic: article
ms.prod: xamarin
ms.assetid: 99A2D7C6-0324-4DE5-B6E9-D39D0BAD8370
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 7b3a7a2347ed93aff5cddc6f15e25028c61a53d8
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2018
---
# <a name="working-with-tab-bar-controller"></a>Utilizzo di Controller barra delle schede

_Questo articolo descrive la progettazione e l'utilizzo di Controller barra scheda all'interno di un'app Xamarin.tvOS._

Per molti tipi di App tvOS, navigazione primario viene presentato come una barra delle schede in esecuzione nella parte superiore della schermata. L'utente passa a sinistra e destra tra l'elenco di categorie possibili e l'area del contenuto di sotto le modifiche in modo da riflettere la selezione dell'utente.

[![](tab-bars-images/tab01.png "Barra delle schede di esempio")](tab-bars-images/tab01.png#lightbox)

Barra della scheda è trasparente per impostazione predefinita e viene sempre visualizzata nella parte superiore della schermata. Quando è in stato attivo, una barra delle schede verranno illustrate le 140 pixel superiore dello schermo ma rapidamente diapositiva immediatamente quando lo stato attivo passa all'area del contenuto.

<a name="Tab-Bars-in-tvOS" />

## <a name="tab-bars-in-tvos"></a>Scheda barre tvOS

Il `UITabViewController` funziona in modo simile e serve tvOS uno scopo simile a quanto accade in iOS, con le differenze principali seguenti:

- A differenza di barra della scheda in iOS che viene visualizzata nella parte inferiore della schermata, scheda barre tvOS occupano di 140 pixel superiore dello schermo e sono semitrasparente per impostazione predefinita.
- Quando la barra delle schede per l'area di contenuto seguente perde lo stato attivo, barra della scheda verrà rapidamente diapositiva dalla parte superiore dello schermo e nascosta. L'utente può toccare il pulsante di Menu, una volta o scorrere rapidamente sul [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote) per mostrare la barra delle schede.
- Passaggio verso il basso in remoto Siri Sposta stato attivo per l'area del contenuto sotto la barra di scheda al primo [elemento con stato attivabile](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) del contenuto da visualizzare. Nuovamente, questo verrà nasconde la barra di scheda quando riceve lo stato attivo.
- Fare clic per selezionare una categoria visualizzata nella barra della scheda passerà a quella della categoria contenuto e lo stato attivo passa al primo elemento con stato attivabile in tale visualizzazione.
- Il numero di categorie visualizzato nella barra della scheda deve essere corretto e tutte le categorie devono essere accessibile in qualsiasi momento, non deve mai essere disabilitata una determinata categoria.
- Barre di scheda non supporta la personalizzazione in tvOS. Inoltre, non vengono visualizzati il **più** categoria (ad esempio, iOS) se sono presenti più categorie di dimensioni barra della scheda.

Apple ha i seguenti suggerimenti per l'utilizzo di barre scheda:

- **Utilizzare le barre di scheda a organizzare logicamente il contenuto** -utilizzare la barra della scheda per organizzare logicamente il contenuto che l'app tvOS funziona con. Ad esempio, in primo piano, i grafici di inizio, acquistato e ricerca.
- **Aggiungere le notifiche per informare gli utenti del nuovo contenuto** -, è possibile visualizzare un Badge (oval rosso con un punto esclamativo bianco numero) per informare l'utente del nuovo contenuto in una categoria.
- **Utilizzare le notifiche in modo sporadico** : non creare confusione nella barra della scheda con badge e solo visualizzarli in forniscono informazioni critiche per l'utente.
- **Limitare il numero di categorie** : per ridurre la complessità e consentono di app gestibile, non l'overload la barra delle schede con categorie e assicurarsi che tutte le categorie sono visibili e non talmente. Titoli breve e semplici la scelta ottimale.
- **Non disabilitare una categoria** -tutte le schede (categorie) deve essere sempre visibili e abilitati in qualsiasi momento. Se una scheda specificata non ha contenuto, fornire una spiegazione perché all'utente. Ad esempio, nella scheda acquista sarà vuota se l'utente non ha effettuato alcun gli acquisti.

<a name="Tab-Bar-Items" />

## <a name="tab-bar-items"></a>Elementi della barra della scheda

Ogni categoria (scheda) nella barra della scheda è rappresentato da un elemento della barra della scheda (`UITabBarItem`). Apple ha i seguenti suggerimenti per lavorare con gli elementi della barra della scheda:

- **Utilizzare le schede basato su testo** -mentre la barra TabItem è in grado di essere rappresentato da un'icona, Apple consiglia di usare testo solo perché un titolo breve è più facile da interpretare rispetto a un'icona.
- **Utilizzare nomi significativi, a breve o verbi** -elemento barra di una scheda chiaramente deve inoltrare il contenuto che contiene e risulta particolarmente utile quando un sostantivo semplice (ad esempio, foto, film o musica) o dei verbi (ad esempio ricerca o Play).

<a name="Tab-Bars-and-Storyboards" />

## <a name="tab-bars-and-storyboards"></a>Scheda barre e gli storyboard

Il modo più semplice per utilizzare le barre di scheda in un'app Xamarin.tvOS è per aggiungerli all'interfaccia utente dell'applicazione utilizzando la finestra di progettazione iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)
    
1. Avviare una nuova app Xamarin.tvOS e selezionare **tvOS** > **App** > **schede App**: 

    [![](tab-bars-images/tab02.png "Selezionare l'App a schede")](tab-bars-images/tab02.png#lightbox)
1. Seguire tutti i prompt per creare una nuova soluzione Xamarin.tvOS.
1. Nel **soluzione riempimento**, fare doppio clic su di `Main.storyboard` e aprirlo per la modifica.
1. Per modificare il **icona** o **titolo** per una determinata categoria, selezionare il **barra TabItem** per il **View Controller** nel  **Struttura documento**:

    [![](tab-bars-images/tab03a.png "La barra delle schede di elemento per il Controller di visualizzazione nella struttura del documento")](tab-bars-images/tab03a.png#lightbox)
1. Impostare quindi le proprietà obbligatorie **scheda Widget** del **Esplora proprietà**: 

    [![](tab-bars-images/tab03.png "La scheda Widget")](tab-bars-images/tab03.png#lightbox)
1. Per aggiungere una nuova categoria (scheda), eliminare un **View Controller** nell'area di progettazione: 

    [![](tab-bars-images/tab04.png "Un Controller di visualizzazione")](tab-bars-images/tab04.png#lightbox)
1. CTRL + clic e trascinare il **scheda View Controller** al nuovo **View Controller**.
1. Selezionare la finestra popup, **visualizzare controller** per aggiungere la nuova vista come una scheda (categoria): 

    [![](tab-bars-images/tab05.png "Seleziona il tasto Tab")](tab-bars-images/tab05.png#lightbox)
1. Progettare il layout dell'interfaccia utente per ogni area del contenuto Caterogies normalmente, tramite l'aggiunta di elementi dell'interfaccia utente nella finestra di progettazione iOS.
1. Esporre gli eventi necessari per lavorare con i controlli dell'interfaccia utente in codice c#.
1. Nome di tutti i controlli dell'interfaccia utente che si desidera esporre nel codice c#.
1. Salvare le modifiche.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. Avviare una nuova app Xamarin.tvOS e selezionare **tvOS** > **App** > **schede App**: 

    [![](tab-bars-images/tab02vs.png "Selezionare l'App a schede")](tab-bars-images/tab02vs.png#lightbox)
1. Seguire tutti i prompt per creare una nuova soluzione Xamarin.tvOS.
1. Nel **Esplora**, fare doppio clic su di `Main.storyboard` e aprirlo per la modifica.
1. Per modificare il **icona** o **titolo** per una determinata categoria, selezionare il **barra TabItem** per il **View Controller** nel  **Struttura documento**:

    [![](tab-bars-images/tab03avs.png "Il Controller di visualizzazione nella struttura del documento")](tab-bars-images/tab03avs.png#lightbox)
1. Impostare quindi le proprietà obbligatorie **scheda Widget** del **Esplora proprietà**: 

    [![](tab-bars-images/tab03vs.png "La scheda Widget")](tab-bars-images/tab03vs.png#lightbox)
1. Per aggiungere una nuova categoria (scheda), trascinare un **View Controller** dal **della casella degli strumenti** e rilasciarlo nell'area di progettazione: 

    [![](tab-bars-images/tab04vs.png "Un Controller di visualizzazione")](tab-bars-images/tab04vs.png#lightbox)
1. CTRL + clic e trascinare il **scheda View Controller** al nuovo **View Controller**.
1. Selezionare la finestra popup, **visualizzare controller** per aggiungere la nuova vista come una scheda (categoria): 

    [![](tab-bars-images/tab05vs.png "Seleziona il tasto Tab")](tab-bars-images/tab05vs.png#lightbox)
1. Progettare il layout dell'interfaccia utente per ogni area del contenuto Caterogies normalmente, tramite l'aggiunta di elementi dell'interfaccia utente nella finestra di progettazione iOS.
1. Esporre gli eventi necessari per lavorare con i controlli dell'interfaccia utente in codice c#.
1. Nome di tutti i controlli dell'interfaccia utente che si desidera esporre nel codice c#.
1. Salvare le modifiche.
    
-----

> [!IMPORTANT]
> Sebbene sia possibile assegnare gli eventi, ad esempio `TouchUpInside` a un elemento dell'interfaccia utente (ad esempio un `UIButton`) nella finestra di progettazione iOS, non verrà mai chiamato perché Apple TV non ha un tocco supportano eventi tocco o sullo schermo. È consigliabile utilizzare sempre il `Primary Action ` eventi durante la creazione di gestori eventi per tvOS elementi dell'interfaccia utente.

Per ulteriori informazioni sull'uso di storyboard, consultare il nostro [Hello, tvOS Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Working-with-Tab-Bars" />

## <a name="working-with-tab-bars"></a>Utilizzo di barre di scheda

Utilizzare il `Items` proprietà del `UITabBar` per accedere alla raccolta di `UITabBarItems` contiene come matrice indicizzata zero (0). Il `SelectedItem` proprietà restituirà la scheda attualmente selezionata (categoria) come un `UITabBarItem`.


<a name="Working-with-Tab-Bar-Items" />

## <a name="working-with-tab-bar-items"></a>Utilizzo di elementi della barra della scheda

Per visualizzare un Badge di una scheda specificata (oval rosso con testo bianco), utilizzare il codice seguente:

```csharp
// Display a badge
TabBar.Items [2].BadgeValue = "10";
```

Che produca i risultati seguenti quando esegue:

[![](tab-bars-images/tab06.png "Un elemento della barra della scheda con badge")](tab-bars-images/tab06.png#lightbox)

Utilizzare il `Title` proprietà del `UITabBarItem` per modificare il titolo e `Image` proprietà per modificare l'icona.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è descritta la progettazione e l'utilizzo di Controller barra scheda all'interno di un'app Xamarin.tvOS.




## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Guide interfaccia umana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
