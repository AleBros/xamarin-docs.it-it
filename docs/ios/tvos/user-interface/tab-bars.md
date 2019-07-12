---
title: Utilizzo di controller di barra della scheda in Xamarin tvOS
description: Questo documento descrive come usare i controller di barra della scheda in un'app tvOS compilate con Xamarin. Fornisce un alto livello sulla visualizzazione di barre schede e illustra gli elementi delle barra della scheda, l'integrazione di storyboard ed elementi della barra della scheda.
ms.prod: xamarin
ms.assetid: 99A2D7C6-0324-4DE5-B6E9-D39D0BAD8370
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 7ac4d0effc1067b065bad114160dc8648e998dad
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830789"
---
# <a name="working-with-tvos-tab-bar-controllers-in-xamarin"></a>Utilizzo di controller di barra della scheda in Xamarin tvOS

Per molti tipi di App tvOS, esplorazione principale viene presentato come una barra di schede in esecuzione nella parte superiore della schermata. L'utente passa a sinistra e destra attraverso l'elenco di categorie possibili e l'area del contenuto di sotto le modifiche in modo da riflettere la selezione dell'utente.

[![](tab-bars-images/tab01.png "Barra di schede di esempio")](tab-bars-images/tab01.png#lightbox)

La barra delle schede è semitrasparente per impostazione predefinita e viene sempre visualizzata nella parte superiore della schermata. Quando è in stato attivo, una barra di schede di 140 pixel superiore della schermata si occuperà ma rapidamente diapositiva immediatamente quando lo stato attivo passa all'area di contenuto riportato di seguito.

<a name="Tab-Bars-in-tvOS" />

## <a name="tab-bars-in-tvos"></a>Barre schede in tvOS

Il `UITabViewController` funziona in modo simile e ha uno scopo simile su tvOS, come accade in iOS, con le differenze principali seguenti:

- A differenza di barra schede in iOS che viene visualizzata nella parte inferiore della schermata, barre schede in tvOS occupare di 140 pixel superiore dello schermo e sono semitrasparente per impostazione predefinita.
- Quando perde lo stato attivo barra della scheda per l'area del contenuto riportato di seguito, la barra di schede verranno scorrere rapidamente dalla parte superiore della schermata e nascosta. L'utente può toccare il pulsante di Menu una volta o scorrere verso il backup sul [remoti per Siri](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote) per visualizzare più la barra di schede.
- Scorrendo rapidamente verso il basso nel sistema remoto Siri Sposta stato attivo per l'area del contenuto sotto la barra della scheda per il primo [elemento attivabile](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) del contenuto da visualizzare. Anche in questo caso, questo nasconde la barra di schede dopo che lo stato attivo passa.
- Fare clic per selezionare una categoria visualizzata nella barra della scheda passerà a quello della categoria contenuto e lo stato attivo passa al primo elemento attivabile in tale visualizzazione.
- Il numero di categorie visualizzato nella barra della scheda dovrebbe essere risolto e tutte le categorie devono essere accessibili in qualsiasi momento, una categoria specificata non deve mai essere disabilitata.
- Barre schede non supporta la personalizzazione su tvOS. Inoltre, non espongono il **altre** categoria (ad esempio, iOS) se sono presenti più categorie rispetto a può adattarsi alla barra delle schede.

Apple ha i seguenti suggerimenti per l'utilizzo di barre schede:

- **Barre schede utilizzare per organizzare logicamente il contenuto** -usare la barra di schede per organizzare logicamente il contenuto che interrelati con l'app tvOS. Ad esempio, in primo piano, i grafici di inizio, acquistato e ricerca.
- **Aggiungere le notifiche per informare gli utenti del nuovo contenuto** -facoltativamente, è possibile visualizzare una notifica (un oval con un numero bianco o il punto esclamativo rosso) per informare l'utente del nuovo contenuto in una categoria.
- **Utilizzare le notifiche in modo sporadico** : non creare confusione barra della scheda con badge e solo visualizzarli in cui forniscono informazioni critiche all'utente.
- **Limitare il numero di categorie** : per ridurre la complessità e mantenere si app gestibili, non eseguire l'overload di barra schede con le categorie e assicurarsi che tutte le categorie sono visibili e non affollato. Titoli brevi e semplici offrono risultati ottimali.
- **Non disabilitare una categoria** -tutte le schede (categorie) deve essere sempre visibili e abilitati in qualsiasi momento. Se una scheda specificata non presenta alcun contenuto, fornire una spiegazione perché all'utente. Ad esempio, nella scheda acquista sarà vuota se l'utente non ha effettuato alcun acquisti.

<a name="Tab-Bar-Items" />

## <a name="tab-bar-items"></a>Elementi di barra schede

Ogni categoria (scheda) nella barra della scheda è rappresentato da un elemento della barra schede (`UITabBarItem`). Apple ha i seguenti suggerimenti per l'uso di elemento della barra schede:

- **Utilizzare schede basato su testo** -anche se la barra TabItem è in grado di essere rappresentata come un'icona, suggerite da Apple con testo solo perché un titolo breve è più facile da interpretare rispetto a un'icona.
- **Utilizzare a breve, significativo sostantivi o verbi** -elemento della barra di schede A chiaramente deve inoltrare il contenuto che contiene e funziona meglio quando si tratta di un sostantivo semplice (ad esempio foto, film o musica) o verbi (ad esempio ricerca o Play).

<a name="Tab-Bars-and-Storyboards" />

## <a name="tab-bars-and-storyboards"></a>Barre schede e gli storyboard

Il modo più semplice per lavorare con barre schede in un'app xamarin. tvos è per aggiungerli all'interfaccia utente dell'app usando iOS Designer.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)
    
1. Avviare una nuova app xamarin. tvos e selezionare **tvOS** > **App** > **App a schede**: 

    [![](tab-bars-images/tab02.png "Selezionare l'App a schede")](tab-bars-images/tab02.png#lightbox)
1. Seguire tutte le istruzioni per creare una nuova soluzione xamarin. tvos.
1. Nel **riquadro della soluzione**, fare doppio clic il `Main.storyboard` file e aprirlo e modificarlo.
1. Per modificare la **sull'icona** o **titolo** per una determinata categoria, selezionare il **elemento della barra schede** per il **View Controller** nel  **Struttura documento**:

    [![](tab-bars-images/tab03a.png "La barra di schede di elemento per il Controller di visualizzazione nella struttura documento")](tab-bars-images/tab03a.png#lightbox)
1. Quindi impostare le proprietà obbligatorie **scheda Widget** delle **Esplora proprietà**: 

    [![](tab-bars-images/tab03.png "La scheda di Widget")](tab-bars-images/tab03.png#lightbox)
1. Per aggiungere una nuova categoria (scheda), rilasciare un **Controller visualizzazione** l'area di progettazione: 

    [![](tab-bars-images/tab04.png "Un Controller di visualizzazione")](tab-bars-images/tab04.png#lightbox)
1. CTRL + clic e trascinare dal **Controller di visualizzazione della scheda** al nuovo **View Controller**.
1. Finestra popup, selezionare **consente di visualizzare controller** per aggiungere la nuova vista come una scheda (categoria): 

    [![](tab-bars-images/tab05.png "Selezionare scheda")](tab-bars-images/tab05.png#lightbox)
1. Progettare il layout dell'interfaccia utente per ogni area di contenuto Caterogies come di consueto, mediante l'aggiunta di elementi dell'interfaccia utente in iOS Designer.
1. Esporre tutti gli eventi necessari per lavorare con i controlli dell'interfaccia utente in C# codice.
1. Assegnare un nome tutti i controlli dell'interfaccia utente che si desidera esporre C# codice.
1. Salvare le modifiche.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. Avviare una nuova app xamarin. tvos e selezionare **tvOS** > **App** > **App a schede**: 

    [![](tab-bars-images/tab02vs.png "Selezionare l'App a schede")](tab-bars-images/tab02vs.png#lightbox)
1. Seguire tutte le istruzioni per creare una nuova soluzione xamarin. tvos.
1. Nel **Esplora soluzioni**, fare doppio clic il `Main.storyboard` file e aprirlo e modificarlo.
1. Per modificare la **sull'icona** o **titolo** per una determinata categoria, selezionare il **elemento della barra schede** per il **View Controller** nel  **Struttura documento**:

    [![](tab-bars-images/tab03avs.png "Il Controller di visualizzazione nella struttura documento")](tab-bars-images/tab03avs.png#lightbox)
1. Quindi impostare le proprietà obbligatorie **scheda Widget** delle **Esplora proprietà**: 

    [![](tab-bars-images/tab03vs.png "La scheda di Widget")](tab-bars-images/tab03vs.png#lightbox)
1. Per aggiungere una nuova categoria (scheda), trascinare un **Controller di visualizzazione** dalle **della casella degli strumenti** e rilasciarlo nell'area di progettazione: 

    [![](tab-bars-images/tab04vs.png "Un Controller di visualizzazione")](tab-bars-images/tab04vs.png#lightbox)
1. CTRL + clic e trascinare dal **Controller di visualizzazione della scheda** al nuovo **View Controller**.
1. Finestra popup, selezionare **consente di visualizzare controller** per aggiungere la nuova vista come una scheda (categoria): 

    [![](tab-bars-images/tab05vs.png "Selezionare scheda")](tab-bars-images/tab05vs.png#lightbox)
1. Progettare il layout dell'interfaccia utente per ogni area di contenuto Caterogies come di consueto, mediante l'aggiunta di elementi dell'interfaccia utente in iOS Designer.
1. Esporre tutti gli eventi necessari per lavorare con i controlli dell'interfaccia utente in C# codice.
1. Assegnare un nome tutti i controlli dell'interfaccia utente che si desidera esporre C# codice.
1. Salvare le modifiche.
    
-----

> [!IMPORTANT]
> Sebbene sia possibile assegnare gli eventi, ad esempio `TouchUpInside` a un elemento dell'interfaccia utente (ad esempio un `UIButton`) in iOS Designer, non verrà mai chiamato perché non dispone di un tocco Apple TV a schermo intero o supportare gli eventi di tocco. È consigliabile usare sempre la `Primary Action` eventi durante la creazione di gestori eventi per tvOS elementi dell'interfaccia utente.

Per altre informazioni sull'utilizzo degli storyboard, vedere la [App Tvos Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Working-with-Tab-Bars" />

## <a name="working-with-tab-bars"></a>Utilizzo di barre schede

Usare la `Items` proprietà del `UITabBar` per accedere alla raccolta di `UITabBarItems` contiene come matrice indicizzata zero (0). Il `SelectedItem` proprietà restituirà la scheda attualmente selezionata (categoria) come un `UITabBarItem`.


<a name="Working-with-Tab-Bar-Items" />

## <a name="working-with-tab-bar-items"></a>Uso degli elementi di barra schede

Per visualizzare una notifica di una scheda specificata (una forma ovale rossa con testo bianco), usare il codice seguente:

```csharp
// Display a badge
TabBar.Items [2].BadgeValue = "10";
```

Che produrrebbe i risultati seguenti quando si esegue:

[![](tab-bars-images/tab06.png "Un elemento della barra di schede con badge")](tab-bars-images/tab06.png#lightbox)

Usare la `Title` proprietà del `UITabBarItem` per modificare il titolo e il `Image` proprietà per cambiare l'icona.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato la progettazione e l'utilizzo di Controller di barra schede all'interno di un'app xamarin. tvos.




## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [le guide dell'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
