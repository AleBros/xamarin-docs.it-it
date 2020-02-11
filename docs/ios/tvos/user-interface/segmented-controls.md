---
title: Uso di controlli segmentati tvOS in Xamarin
description: Questo documento descrive come usare i controlli segmentati tvOS in un'app compilata con Xamarin. Illustra le icone del segmento e il testo, gli eventi, la modifica dell'aspetto del controllo e altro ancora.
ms.prod: xamarin
ms.assetid: 23AD94CC-E93A-40B1-8E2B-ECD21FA355BE
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 0bedd898232b39898e6079a204bd7409587cde82
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030403"
---
# <a name="working-with-tvos-segmented-controls-in-xamarin"></a>Uso di controlli segmentati tvOS in Xamarin

Un controllo segmentato fornisce un set di elementi lineari, ognuno dei quali può contenere un'icona o un testo, e viene usato per fornire un set di scelte correlate all'utente.

[![](segmented-controls-images/segment01.png "Sample segment controls")](segmented-controls-images/segment01.png#lightbox)

Apple presenta i suggerimenti seguenti per l'utilizzo dei controlli segmentati:

- **Fornire un'ampia** quantità di spazio: prestare spazio tra [gli altri elementi attivabili](~/ios/tvos/app-fundamentals/navigation-focus.md) e un controllo segmentato. Un singolo segmento viene selezionato quando è attivo (non quando viene fatto clic) e l'utente può modificare accidentalmente i segmenti quando vogliono selezionare un altro elemento attivabile nel segmento corrente.
- **Usare le visualizzazioni divise per il filtro del contenuto** . i controlli segmentati non consentono di scegliere tra le opzioni di filtro dei contenuti perché le visualizzazioni suddivise sono state progettate per semplificare la navigazione tra il contenuto e i filtri.
- **Limite massimo di sette segmenti** : è consigliabile provare a tenere il numero massimo di segmenti inferiori a otto (8), in quanto questa operazione è più semplice da analizzare da tutta la stanza sul divano e più facile da esplorare.
- **Usa dimensioni del contenuto del segmento coerenti** : tutti i segmenti hanno la stessa larghezza e, se possibile, è consigliabile provare a tenere il contenuto di ogni segmento con le stesse dimensioni. In questo modo non solo i controlli del segmento sono più gradevoli da visualizzare, ma semplificano la lettura a colpo d'occhio.
- **Evitare di combinare icone e testo** . ogni singolo segmento può contenere un'icona o un testo, ma non entrambi. Sebbene sia possibile combinare icone e testo nello stesso controllo segmentato, questa operazione deve essere evitata.

<a name="About-Segment-Icons" />

## <a name="about-segment-icons"></a>Informazioni sulle icone del segmento

Apple suggerisce di usare immagini semplici e riconoscibili per le icone del segmento, ad esempio una lente di ingrandimento per la ricerca. Le icone eccessivamente complesse sono difficili da riconoscere in uno schermo della TV, quindi è consigliabile limitare le icone alle rappresentazioni semplici.

Non è possibile combinare testo e icone in un determinato segmento ed evitare di combinare icone e testo in un singolo controllo segmentato. Deve essere costituito da tutte le icone o tutto il testo.

<a name="Summary" />

## <a name="segment-text"></a>Testo segmento

Apple apporta i suggerimenti seguenti per l'uso del testo del segmento:

- **Utilizzare sostantivi brevi e significativi** : il titolo del segmento deve indicare chiaramente il tipo di contenuto che l'utente deve aspettarsi durante la selezione del segmento specificato. Ad esempio, musica o video.
- **Usare le maiuscole/minuscole per maiuscole e minuscole** : ogni parola del titolo del segmento deve essere in maiuscolo, ad eccezione di articoli, conjunction e preposizioni di meno di quattro (4) caratteri.
- **Usa titoli brevi e mirati** : Mantieni i titoli, brevi e incentrati sul tipo di contenuto da prevedere quando viene selezionato il segmento.

Anche in questo caso non è possibile combinare testo e icone in un determinato segmento ed evitare di combinare icone e testo in un singolo controllo segmentato.

<a name="Segment-Controls-and-Storyboards" />

## <a name="segment-controls-and-storyboards"></a>Controlli e storyboard del segmento

Il modo più semplice per usare i controlli dei segmenti in un'app Xamarin.tvOS consiste nell'aggiungerli all'interfaccia utente dell'app tramite iOS designer.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Nella **riquadro della soluzione**fare doppio clic sul file di `Main.storyboard` e aprirlo per la modifica.
1. Trascinare un **controllo Segment** dalla **casella degli strumenti** e rilasciarlo nella visualizzazione: 

    [![](segmented-controls-images/segment02.png "A Segment Control")](segmented-controls-images/segment02.png#lightbox)
1. Nella **scheda Widget** del **riquadro Proprietà**è possibile modificare diverse proprietà del controllo Segment, ad esempio **lo stile** e **lo stato**: 

    [![](segmented-controls-images/segment03.png "The Widget Tab")](segmented-controls-images/segment03.png#lightbox)
1. Usare il campo **segmenti** per controllare il numero di segmenti nel controller.
1. Selezionare un segmento specifico dall' **elenco a discesa segmento** per modificare le singole proprietà, ad esempio **titolo** o **immagine** , e controllare se un determinato segmento è **abilitato** o **selezionato** quando viene visualizzato il controllo.
1. Infine, assegnare i **nomi** ai controlli in modo che sia possibile rispondere a tali C# controlli nel codice. Esempio: 

    [![](segmented-controls-images/segment04.png "Assign a Name")](segmented-controls-images/segment04.png#lightbox)
1. Salvare le modifiche.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Nella **Esplora soluzioni**fare doppio clic sul file di `Main.storyboard` e aprirlo per la modifica.
1. Trascinare un **controllo Segment** dalla **casella degli strumenti** e rilasciarlo nella visualizzazione: 

    [![](segmented-controls-images/segment02-vs.png "A Segment Control")](segmented-controls-images/segment02-vs.png#lightbox)
1. Nella **scheda Widget** di **Esplora proprietà**è possibile modificare diverse proprietà del controllo segmento, ad esempio **lo stile** e **lo stato**: 

    [![](segmented-controls-images/segment03-vs.png "The Widget Tab")](segmented-controls-images/segment03-vs.png#lightbox)
1. Usare il campo **segmenti** per controllare il numero di segmenti nel controller.
1. Selezionare un segmento specifico dall' **elenco a discesa segmento** per modificare le singole proprietà, ad esempio **titolo** o **immagine** , e controllare se un determinato segmento è **abilitato** o **selezionato** quando viene visualizzato il controllo.
1. Infine, assegnare i **nomi** ai controlli in modo che sia possibile rispondere a tali C# controlli nel codice. Esempio: 

    [![](segmented-controls-images/segment04-vs.png "Assign a Name")](segmented-controls-images/segment04-vs.png#lightbox)
1. Salvare le modifiche.

-----

Per ulteriori informazioni sull'utilizzo degli storyboard, vedere la [Guida introduttiva Hello, tvOS](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Working-with-Segmented-Controls" />

## <a name="working-with-segmented-controls"></a>Utilizzo di controlli segmentati

Come indicato in precedenza, il controllo segmentato fornisce un set di elementi lineari, ognuno dei quali può contenere un'icona o un testo, e viene usato per fornire un set di scelte correlate all'utente.

Esistono diversi modi in cui è possibile usare i controlli segmentati nell'app Xamarin.tvOS.

<a name="Exposed-as-Outlets-and-Actions" />

## <a name="exposed-as-names-and-events"></a>Esposto come nomi ed eventi

Se il controllo segmento è stato creato in progettazione interfacce ed esposto come controllo denominato e un evento, è possibile usare il codice seguente per rispondere alla modifica del segmento:

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

Nel caso dell'esempio precedente, il controllo segmento è stato esposto come nome `PlayerCount` e un'azione `PlayerCountChanged` evento. Per altre informazioni sull'uso di azioni e Outlet, vedere la sezione [scrittura del codice con Outlet e azioni](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code) di [Hello, tvOS Guida introduttiva](~/ios/tvos/get-started/hello-tvos.md).

Il `SelectedSegment` proprietà Ottiene o imposta il segmento attualmente selezionato come indice in base zero (0). Quindi, se si dispone di cinque (5) segmenti, il primo segmento avrà un indice pari a zero (0) e l'ultimo indice di quattro (4).

<a name="Modifying-Segments" />

## <a name="modifying-segments"></a>Modifica di segmenti

È possibile modificare il numero e il contenuto dei controlli segmentati in qualsiasi momento. Usare il codice seguente per inserire un nuovo segmento di icona:

```csharp
// Icon Segment
SegmentedControl.InsertSegment(UIImage.FromFile("icon.png"), 0, true);

// Text Segment
SegmentedControl.InsertSegment("New Segment", 0, true);
```

Il secondo parametro definisce la posizione in cui verrà inserito il segmento utilizzando un indice in base zero (0). Se l'ultimo parametro è `true` l'inserimento verrà animato.

Per rimuovere un determinato segmento, utilizzare quanto segue:

```csharp
SegmentedControl.RemoveSegmentAtIndex(0, true);
```

Oppure il codice seguente per rimuovere tutti i segmenti:

```csharp
SegmentedControl.RemoveAllSegments();
```

Anche in questo caso, se l'ultimo parametro è `true`, la rimozione verrà animata. Utilizzare la proprietà `NumberOfSegments` per restituire il numero corrente di segmenti.

Per ottenere il **titolo** o l' **icona** per un determinato segmento, utilizzare quanto segue:

```csharp
// Get title
var title = SegmentedControl.TitleAt(0);

// Get icon
var icon = SegmentedControl.ImageAt(0);
```

Per modificare il **titolo** o l' **icona**, utilizzare quanto segue:

```csharp
// Set title
SegmentedControl.SetTitle("New Title", 0);

// Set icon
SegmentedControl.SetImage(UIImage.FromFile("icon.png"), 0);
```

Per verificare se un determinato segmento è **abilitato**, usare quanto segue:

```csharp
if (SegmentedControl.IsEnabled(0)) {
    // Do something
    ...
}
```

Per **abilitare o disabilitare** un determinato segmento, utilizzare quanto segue:

```csharp
SegmentedControl.SetEnabled(false, 0);
```

<a name="Modifying-the-Segmented-Controls-Appearance" />

## <a name="modifying-the-segmented-controls-appearance"></a>Modifica dell'aspetto del controllo segmentato

Per modificare lo sfondo di un determinato segmento in un'immagine, è possibile usare il codice seguente:

```csharp
SegmentedControl.SetBackgroundImage (UIImage.FromFile("background.png"), UIControlState.Normal, UIBarMetrics.Default);
```

Dove `UIControlState` specifica lo stato del controllo per il quale si sta impostando l'immagine:

- Normale
- Evidenziato
- Disabilitato
- Selezionati
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

Dove il primo `UIControlState` specifica lo stato del segmento a sinistra del divisore e la seconda `UIControlState` specifica lo stato del segmento a destra.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha trattato la progettazione e l'uso del controllo segmentato all'interno di un'app Xamarin.tvOS.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guide all'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione delle app per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
