---
title: Uso dei controller della barra schede tvOS in Novell
description: Questo documento descrive come usare i controller della barra schede in un'app tvOS compilata con Novell. Offre una visualizzazione di alto livello delle barre delle schede e illustra gli elementi della barra delle schede, l'integrazione dello storyboard e gli elementi della barra delle schede.
ms.prod: xamarin
ms.assetid: 99A2D7C6-0324-4DE5-B6E9-D39D0BAD8370
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: a4adc692680c0556a3cfb07b1a9f45963a81d199
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022131"
---
# <a name="working-with-tvos-tab-bar-controllers-in-xamarin"></a>Uso dei controller della barra schede tvOS in Novell

Per molti tipi di app tvOS, la navigazione principale viene visualizzata come barra scheda in esecuzione nella parte superiore della schermata. L'utente scorre verso sinistra e verso destra nell'elenco delle categorie possibili e l'area di contenuto sotto le modifiche per riflettere la selezione dell'utente.

[![](tab-bars-images/tab01.png "Sample Tab Bar")](tab-bars-images/tab01.png#lightbox)

Per impostazione predefinita, la barra delle schede è traslucida e viene sempre visualizzata nella parte superiore della schermata. Quando si è in stato attivo, una barra delle schede coprirà i primi 140 pixel della schermata, ma viene rapidamente spostata quando lo stato attivo passa all'area di contenuto seguente.

<a name="Tab-Bars-in-tvOS" />

## <a name="tab-bars-in-tvos"></a>Barre delle schede in tvOS

Il `UITabViewController` funziona in modo simile e ha uno scopo analogo in tvOS come in iOS, con le seguenti differenze principali:

- A differenza della barra scheda in iOS visualizzata nella parte inferiore della schermata, le barre delle schede in tvOS occupano i primi 140 pixel dello schermo e sono trasparenti per impostazione predefinita.
- Quando lo stato attivo esce dalla barra delle schede per l'area del contenuto, la barra della scheda si sposta rapidamente dalla parte superiore dello schermo e viene nascosta. L'utente può toccare il pulsante di menu una volta o scorrere verso l'alto nella finestra di [Siri Remote](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote) per visualizzare di nuovo la barra delle schede.
- Se si scorre verso il basso sul Siri remoto, lo stato attivo viene spostato nell'area del contenuto sotto la barra della scheda fino alla prima [voce attivabile](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) nel contenuto visualizzato. Anche in questo caso, la barra delle schede viene nascosta dopo lo spostamento dello stato attivo.
- Facendo clic per selezionare una categoria visualizzata nella barra delle schede, si passerà al contenuto di tale categoria e lo stato attivo verrà impostato sul primo elemento attivabile in tale visualizzazione.
- Il numero di categorie visualizzate nella barra delle schede dovrebbe essere fisso e tutte le categorie devono essere accessibili in qualsiasi momento, una determinata categoria non deve mai essere disabilitata.
- Le barre delle schede non supportano la personalizzazione in tvOS. Inoltre, non visualizzano la categoria **più** (ad esempio iOS) se sono presenti più categorie di quelle che è possibile inserire nella barra delle schede.

Apple presenta i suggerimenti seguenti per l'utilizzo delle barre delle schede:

- **Usare le barre delle schede per organizzare logicamente il contenuto** : usare la barra delle schede per organizzare logicamente il contenuto con cui funziona l'app tvOS. Ad esempio, in primo piano, in primo piano, acquistato e ricerca.
- **Aggiungere notifiche per informare gli utenti del nuovo contenuto** . Facoltativamente, è possibile visualizzare una notifica (un ovale rosso con un numero bianco o un punto esclamativo) per informare l'utente del nuovo contenuto in una categoria.
- **Usare le notifiche in modo sporadico** . non ingombrare la barra delle schede con le notifiche e visualizzarle solo quando forniscono informazioni critiche all'utente.
- **Limitare il numero di categorie** : per ridurre la complessità e mantenere gestibili le app, non sovraccaricare la barra delle schede con le categorie e assicurarsi che tutte le categorie siano visibili e non affollate. I titoli semplici e brevi funzionano meglio.
- **Non disabilitare una categoria** : tutte le schede (categorie) devono essere sempre visibili e abilitate in qualsiasi momento. Se una scheda specificata non contiene contenuto, fornire una spiegazione per l'utente. La scheda acquisti, ad esempio, sarà vuota se l'utente non ha effettuato acquisti.

<a name="Tab-Bar-Items" />

## <a name="tab-bar-items"></a>Elementi della barra scheda

Ogni categoria (scheda) nella barra delle schede è rappresentata da un elemento della barra scheda (`UITabBarItem`). Apple presenta i suggerimenti seguenti per l'utilizzo degli elementi della barra schede:

- **Usare schede basate su testo** : mentre l'elemento della barra schede può essere rappresentato come un'icona, Apple suggerisce di usare solo testo perché un titolo conciso è più semplice da interpretare rispetto a un'icona.
- **Usare sostantivi o verbi brevi e significativi** : un elemento della barra schede deve inoltrare chiaramente il contenuto che contiene e funziona meglio quando è un sostantivo semplice (ad esempio foto, filmati o musica) o verbi (ad esempio, ricerca o riproduzione).

<a name="Tab-Bars-and-Storyboards" />

## <a name="tab-bars-and-storyboards"></a>Barre di tabulazione e storyboard

Il modo più semplice per usare le barre delle schede in un'app Novell. tvOS consiste nell'aggiungerle all'interfaccia utente dell'app tramite iOS designer.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Avviare una nuova app Novell. tvOS e selezionare **tvOS** > **app** > **app a schede**: 

    [![](tab-bars-images/tab02.png "Select Tabbed App")](tab-bars-images/tab02.png#lightbox)
1. Seguire tutte le istruzioni per creare una nuova soluzione Novell. tvOS.
1. Nella **riquadro della soluzione**fare doppio clic sul file di `Main.storyboard` e aprirlo per la modifica.
1. Per modificare l' **icona** o il **titolo** di una determinata categoria, selezionare l' **elemento della barra schede** per il **controller di visualizzazione** nella struttura del **documento**:

    [![](tab-bars-images/tab03a.png "The Tab Bar Item for the View Controller in the Document Outline")](tab-bars-images/tab03a.png#lightbox)
1. Impostare quindi le proprietà necessarie nella **scheda Widget** di **Esplora proprietà**: 

    [![](tab-bars-images/tab03.png "The Widget Tab")](tab-bars-images/tab03.png#lightbox)
1. Per aggiungere una nuova categoria (scheda), trascinare un **controller di visualizzazione** nell'area di progettazione: 

    [![](tab-bars-images/tab04.png "A View Controller")](tab-bars-images/tab04.png#lightbox)
1. Controllare: fare clic e trascinare dal **controller di visualizzazione a schede** al nuovo **controller di visualizzazione**.
1. Nella finestra popup selezionare **Visualizza controller** per aggiungere la nuova visualizzazione come scheda (categoria): 

    [![](tab-bars-images/tab05.png "Select Tab")](tab-bars-images/tab05.png#lightbox)
1. Progettare il layout dell'interfaccia utente per ogni area di contenuto di Caterogies come di consueto, aggiungendo elementi dell'interfaccia utente in iOS designer.
1. Esporre tutti gli eventi necessari per lavorare con i controlli dell' C# interfaccia utente nel codice.
1. Assegnare un nome a tutti i controlli dell'interfaccia utente C# che si desidera esporre nel codice.
1. Salvare le modifiche.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Avviare una nuova app Novell. tvOS e selezionare **tvOS** > **app** > **app a schede**: 

    [![](tab-bars-images/tab02vs.png "Select Tabbed App")](tab-bars-images/tab02vs.png#lightbox)
1. Seguire tutte le istruzioni per creare una nuova soluzione Novell. tvOS.
1. Nella **Esplora soluzioni**fare doppio clic sul file di `Main.storyboard` e aprirlo per la modifica.
1. Per modificare l' **icona** o il **titolo** di una determinata categoria, selezionare l' **elemento della barra schede** per il **controller di visualizzazione** nella struttura del **documento**:

    [![](tab-bars-images/tab03avs.png "The View Controller in the Document Outline")](tab-bars-images/tab03avs.png#lightbox)
1. Impostare quindi le proprietà necessarie nella **scheda Widget** di **Esplora proprietà**: 

    [![](tab-bars-images/tab03vs.png "The Widget Tab")](tab-bars-images/tab03vs.png#lightbox)
1. Per aggiungere una nuova categoria (scheda), trascinare un **controller di visualizzazione** dalla **casella degli strumenti** e rilasciarlo nell'area di progettazione: 

    [![](tab-bars-images/tab04vs.png "A View Controller")](tab-bars-images/tab04vs.png#lightbox)
1. Controllare: fare clic e trascinare dal **controller di visualizzazione a schede** al nuovo **controller di visualizzazione**.
1. Nella finestra popup selezionare **Visualizza controller** per aggiungere la nuova visualizzazione come scheda (categoria): 

    [![](tab-bars-images/tab05vs.png "Select Tab")](tab-bars-images/tab05vs.png#lightbox)
1. Progettare il layout dell'interfaccia utente per ogni area di contenuto di Caterogies come di consueto, aggiungendo elementi dell'interfaccia utente in iOS designer.
1. Esporre tutti gli eventi necessari per lavorare con i controlli dell' C# interfaccia utente nel codice.
1. Assegnare un nome a tutti i controlli dell'interfaccia utente C# che si desidera esporre nel codice.
1. Salvare le modifiche.

-----

> [!IMPORTANT]
> Sebbene sia possibile assegnare eventi come `TouchUpInside` a un elemento dell'interfaccia utente, ad esempio un `UIButton`, nella finestra di progettazione di iOS, non verrà mai chiamato perché Apple TV non ha un touch screen o supporta gli eventi di tocco. È consigliabile usare sempre l'evento `Primary Action` durante la creazione di gestori eventi per gli elementi dell'interfaccia utente tvOS.

Per ulteriori informazioni sull'utilizzo degli storyboard, vedere la [Guida introduttiva Hello, tvOS](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Working-with-Tab-Bars" />

## <a name="working-with-tab-bars"></a>Utilizzo delle barre delle schede

Utilizzare la proprietà `Items` della `UITabBar` per accedere alla raccolta di `UITabBarItems` che contiene come matrice indicizzata zero (0). La proprietà `SelectedItem` restituirà la scheda attualmente selezionata (categoria) come `UITabBarItem`.

<a name="Working-with-Tab-Bar-Items" />

## <a name="working-with-tab-bar-items"></a>Utilizzo degli elementi della barra schede

Per visualizzare una notifica in una scheda specificata (un ovale rosso con testo bianco), usare il codice seguente:

```csharp
// Display a badge
TabBar.Items [2].BadgeValue = "10";
```

Che produrrebbe i risultati seguenti quando vengono eseguiti:

[![](tab-bars-images/tab06.png "A Tab Bar Item with badge")](tab-bars-images/tab06.png#lightbox)

Utilizzare la proprietà `Title` della `UITabBarItem` per modificare il titolo e la proprietà `Image` per modificare l'icona.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha trattato la progettazione e l'uso del controller della barra schede all'interno di un'app Novell. tvOS.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guide all'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione delle app per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
