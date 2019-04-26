---
title: Uso di controlli segmentati tvOS in Xamarin
description: Questo documento descrive come usare tvOS controlli segmentati in un'app compilata con Xamarin. Vengono illustrati le icone di segmento e il testo, eventi, modificare l'aspetto del controllo e altro ancora.
ms.prod: xamarin
ms.assetid: 23AD94CC-E93A-40B1-8E2B-ECD21FA355BE
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 98a770d05014e0498b805ed9ffa0c84314efc765
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61374997"
---
# <a name="working-with-tvos-segmented-controls-in-xamarin"></a>Uso di controlli segmentati tvOS in Xamarin

Un controllo Segmented fornisce un set di elementi lineari, ognuno dei quali può contenere un'icona o testo e viene usato per fornire un set di opzioni correlate all'utente.

[![](segmented-controls-images/segment01.png "Controlli di segmento di esempio")](segmented-controls-images/segment01.png#lightbox)

Apple ha i seguenti suggerimenti per l'uso di controlli segmentati:

- **Forniscono un ampio spazio** -attenzione deve essere take per fornire uno spazio maggiore tra loro [gli elementi con stato attivabile](~/ios/tvos/app-fundamentals/navigation-focus.md) e un controllo Segmented. Un singolo segmento risulterà selezionato quando è messa a fuoco (ma non quando viene fatto clic) e l'utente può modificare accidentalmente segmenti quando vogliono effettivamente selezionare un altro elemento attivabile nel segmento corrente.
- **Usare le visualizzazioni di divisione per il filtro contenuto** -controlli segmentati non fare la scelta per il contenuto filtrando come doppie visualizzazioni sono state progettate per semplificare la navigazione tra il contenuto e i filtri.
- **Limite al numero massimo di segmenti di sette** -è consigliabile provare a mantenere il numero massimo di segmenti di sotto di otto (8) come questo è più semplice analizzare da attraversare la stanza da usare sul divano e semplice per spostarsi.
- **Usare dimensioni del contenuto segmento coerente** : tutti i segmenti hanno la stessa larghezza e, se possibile, è consigliabile mantenere il contenuto in ciascun segmento le stesse dimensioni. Ciò non solo rende più accattivante i controlli di segmento, ma rende più facile da leggere in modo immediato.
- **Evitare di combinare le icone e testo** -ogni singolo segmento può contenere un'icona o testo, ma non entrambi. Sebbene sia possibile combinare le icone e testo nel controllo segmentato stesso, questo deve essere evitato.

<a name="About-Segment-Icons" />

## <a name="about-segment-icons"></a>Sulle icone nel segmento

Uso delle immagini di semplice, riconoscibile per le icone di segmento, ad esempio una lente di ingrandimento per la ricerca suggerite da Apple. Icone eccessivamente complesse sono difficili da riconoscere in uno schermo TV tutta la stanza, pertanto è consigliabile limitare le icone alle rappresentazioni semplice.

È possibile combinare testo e icone in un dato segmento ed è consigliabile evitare di combinare le icone e testo in un singolo controllo segmentato. Deve essere tutte le icone o tutto il testo.

<a name="Summary" />

## <a name="segment-text"></a>Testo di segmento

Apple rende i suggerimenti seguenti per l'utilizzo del segmento di testo:

- **Usare breve, i sostantivi significativo** -titolo il segmento indicherà chiaramente il tipo di contenuto che l'utente dovrebbe aspettarsi quando si seleziona il segmento specificato. Ad esempio: Musica o video.
- **Uso di maiuscole/minuscole di tutte le iniziali maiuscole** -tutte le parole del titolo segmenti devono essere in maiuscolo, ad eccezione degli articoli, congiunzioni e preposizioni di meno di quattro (4) caratteri.
- **Usare breve, con stato attivo titoli** -mantenere i titoli brevi e con lo stato attivo del tipo di contenuto previsto quando è selezionato il segmento.

Anche in questo caso, non è possibile combinare testo e icone in un dato segmento ed è consigliabile evitare di combinare le icone e testo in un singolo controllo segmentato.

<a name="Segment-Controls-and-Storyboards" />

## <a name="segment-controls-and-storyboards"></a>I controlli di segmento e storyboard

Il modo più semplice per usare i controlli di segmento in un'app xamarin. tvos è per aggiungerli all'interfaccia utente dell'app usando iOS Designer.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Nel **riquadro della soluzione**, fare doppio clic il `Main.storyboard` file e aprirlo e modificarlo.
1. Trascinare un **controllo di segmento** dal **della casella degli strumenti** e rilasciarlo nella vista: 

    [![](segmented-controls-images/segment02.png "Un segmento di controllo")](segmented-controls-images/segment02.png#lightbox)
1. Nel **scheda Widget** del **riquadro della proprietà**, è possibile modificare diverse proprietà del segmento di controllo, ad esempio relativi **stile** e **stato**: 

    [![](segmented-controls-images/segment03.png "La scheda di Widget")](segmented-controls-images/segment03.png#lightbox)
1. Usare la **segmenti** campo per controllare il numero di segmenti nel controller.
1. Selezionare un segmento specificato dal **elenco a discesa segmento** per modificare le relative proprietà singole, ad esempio **titolo** o **immagine** e controllare se è un dato segmento  **Abilitata** oppure **selezionati** quando il controllo viene visualizzato.
1. Infine, assegnare **nomi** ai controlli in modo da poter rispondere ad essi in C# codice. Ad esempio: 

    [![](segmented-controls-images/segment04.png "Assegnare un nome")](segmented-controls-images/segment04.png#lightbox)
1. Salvare le modifiche.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. Nel **Esplora soluzioni**, fare doppio clic il `Main.storyboard` file e aprirlo e modificarlo.
1. Trascinare un **controllo di segmento** dal **della casella degli strumenti** e rilasciarlo nella vista: 

    [![](segmented-controls-images/segment02-vs.png "Un segmento di controllo")](segmented-controls-images/segment02-vs.png#lightbox)
1. Nel **scheda Widget** del **Esplora proprietà**, è possibile modificare diverse proprietà del segmento di controllo, ad esempio relativi **Style** e **stato**: 

    [![](segmented-controls-images/segment03-vs.png "La scheda di Widget")](segmented-controls-images/segment03-vs.png#lightbox)
1. Usare la **segmenti** campo per controllare il numero di segmenti nel controller.
1. Selezionare un segmento specificato dal **elenco a discesa segmento** per modificare le relative proprietà singole, ad esempio **titolo** o **immagine** e controllare se è un dato segmento  **Abilitata** oppure **selezionati** quando il controllo viene visualizzato.
1. Infine, assegnare **nomi** ai controlli in modo da poter rispondere ad essi in C# codice. Ad esempio: 

    [![](segmented-controls-images/segment04-vs.png "Assegnare un nome")](segmented-controls-images/segment04-vs.png#lightbox)
1. Salvare le modifiche.
    
-----

Per altre informazioni sull'utilizzo degli storyboard, vedere la [App Tvos Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Working-with-Segmented-Controls" />

## <a name="working-with-segmented-controls"></a>Utilizzo di controlli segmentati

Come indicato in precedenza, s che controllo Segmented fornisce un set di elementi lineari, ognuno dei quali può contenere un'icona o testo e viene usato per fornire un set di opzioni correlate all'utente.

Esistono diversi modi disponibili per lavorare con i controlli segmentati nell'app xamarin. tvos.

<a name="Exposed-as-Outlets-and-Actions" />

## <a name="exposed-as-names-and-events"></a>Esposti come nomi e gli eventi

Se è stato creato il segmento di controllo nella finestra di progettazione dell'interfaccia viene esposta come un evento e un controllo denominato è possibile usare il codice riportato di seguito per rispondere alla modifica del segmento:

```csharp
partial void PlayerCountChanged (Foundation.NSObject sender) {

    // Take action based on the number of players selected
    switch(PlayerCount.SelectedSegment) {
    case 0:
        // Do something if the segment is selected
        ...
        break;
    case 1:
        // Do something if the segment is selected
        ...
        break;
    case 2:
        // Do something if the segment is selected
        ...
        break;
    }
}
```

Nel caso dell'esempio precedente, il segmento di controllo è stata esposta come un `PlayerCount` nome e un `PlayerCountChanged` azione dell'evento. Per altre informazioni sull'uso dei Outlet e azioni, vedere la [scrittura del codice con Outlet e azioni](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code) sezione del nostro [App Tvos Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md).

Il `SelectedSegment` proprietà ottiene o imposta l'indice del segmento attualmente selezionata come uno zero (0) in base. Quindi se si dispone di segmenti di cinque (5), il primo segmento sarà necessario un indice di zero (0) e l'ultimo un indice di quattro (4).

<a name="Modifying-Segments" />

## <a name="modifying-segments"></a>Modifica di segmenti

In qualsiasi momento è possibile modificare il numero e il contenuto dei controlli segmentati. Usare il codice seguente per inserire una nuova icona segmento:

```csharp
// Icon Segment
SegmentedControl.InsertSegment(UIImage.FromFile("icon.png"), 0, true);

// Text Segment
SegmentedControl.InsertSegment("New Segment", 0, true);
```

Il secondo parametro definisce il segmento in cui è inserito usando un indice a base zero (0). Se l'ultimo parametro è `true` verrà animato dell'inserimento.

Per rimuovere un determinato segmento, usare il comando seguente:

```csharp
SegmentedControl.RemoveSegmentAtIndex(0, true);
```

O per rimuovere tutti i segmenti seguenti:

```csharp
SegmentedControl.RemoveAllSegments();
```

Anche in questo caso, se l'ultimo parametro è `true`, verrà animata la rimozione. Usare il `NumberOfSegments` proprietà per restituire il numero corrente di segmenti.

Per ottenere il **Title** oppure **icona** per un dato segmento, usare il comando seguente:

```csharp
// Get title
var title = SegmentedControl.TitleAt(0);

// Get icon
var icon = SegmentedControl.ImageAt(0);
```

E per modificare la **Title** oppure **icona**, usare il comando seguente:

```csharp
// Set title
SegmentedControl.SetTitle("New Title", 0);

// Set icon
SegmentedControl.SetImage(UIImage.FromFile("icon.png"), 0);
```

Per verificare se un dato segmento **abilitato**, usare il comando seguente:

```csharp
if (SegmentedControl.IsEnabled(0)) {
    // Do something
    ...
}
```

E di ottenere **Abilita/Disabilita** un dato segmento, usare il comando seguente:

```csharp
SegmentedControl.SetEnabled(false, 0);
```

<a name="Modifying-the-Segmented-Controls-Appearance" />

## <a name="modifying-the-segmented-controls-appearance"></a>Modifica l'aspetto del controllo segmentato

È possibile usare il codice seguente per cambiare lo sfondo di un segmento specificato di un'immagine:

```csharp
SegmentedControl.SetBackgroundImage (UIImage.FromFile("background.png"), UIControlState.Normal, UIBarMetrics.Default);
```

In cui `UIControlState` specifica lo stato del controllo che si sta impostando l'immagine per come:

- Normale
- evidenziato
- Disabilitato
- Selezionato
- Con stato attivo

E `UIBarMetrics` specifica le metriche da usare come:

- Impostazione predefinita
- Compact
- DefaultPrompt
- CompactPrompt

Inoltre, è possibile impostare il divisore tra i segmenti utilizzando:

```csharp
SegmentedControl.SetDividerImage (UIImage.FromFile("divider.png"), UIControlState.Normal, UIControlState.Normal, UIBarMetrics.Default);
```

Dove il primo `UIControlState` specifica lo stato del segmento a sinistra del divisore e il secondo `UIControlState` specifica lo stato del segmento a destra.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato la progettazione e l'utilizzo di controllo Segmented all'interno di un'app xamarin. tvos.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [le guide dell'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
