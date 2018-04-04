---
title: Utilizzo dei controlli segmentati
description: Questo articolo descrive la progettazione e utilizzo dei controlli segmentata all'interno di un'app Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: 23AD94CC-E93A-40B1-8E2B-ECD21FA355BE
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: d4eac932c7fad628a0a65127bceb641f34ea5d79
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-segmented-controls"></a>Utilizzo dei controlli segmentati

_Questo articolo descrive la progettazione e utilizzo dei controlli segmentata all'interno di un'app Xamarin.tvOS._


Un controllo segmentata fornisce un set di elementi lineari, ognuno dei quali può contenere testo o un'icona e viene utilizzata per fornire un set di opzioni correlate all'utente.

[![](segmented-controls-images/segment01.png "Controlli di segmento di esempio")](segmented-controls-images/segment01.png#lightbox)

Apple ha i seguenti suggerimenti per l'utilizzo di controlli segmentati:

- **Fornire una quantità di spazio** -attenzione dovrebbe essere necessario fornire una quantità di spazio tra altri [elementi con stato attivabile](~/ios/tvos/app-fundamentals/navigation-focus.md) e un controllo segmentati. Un singolo segmento selezionato quando è attivo (non quando viene selezionato) e l'utente può modificare accidentalmente i segmenti quando desiderano effettivamente selezionare un altro elemento con stato attivabile il segmento corrente.
- **Utilizzare le visualizzazioni suddivise per il filtro contenuto** -segmentata controlli non dare per le opzioni appropriate per il filtro come visualizzazioni suddivise sono state progettate per semplificare la navigazione tra il contenuto e i filtri contenuti.
- **Limite al numero massimo di sette segmenti** -è consigliabile ridurre il numero massimo di segmenti di sotto di otto (8) come questo è più semplice analizzare da parte della stanza divano e semplice per spostarsi.
- **Utilizzare coerente dimensioni del segmento di contenuto** : tutti i segmenti hanno la stessa larghezza e, se possibile, è consigliabile mantenere il contenuto in ciascun segmento le stesse dimensioni. Questo non solo rende più visivamente gradevole i controlli di segmento, ma rende più facile da leggere a colpo d'occhio.
- **Evitare di combinare le icone e testo** -ogni singolo segmento può contenere un'icona o testo, ma non entrambi. Sebbene sia possibile combinare le icone e testo nel controllo stesso segmentati, devono essere evitato.

<a name="About-Segment-Icons" />

## <a name="about-segment-icons"></a>Sulle icone di segmento

Apple suggerisce l'utilizzo di immagini semplice, riconoscibile per le icone di segmento, ad esempio una lente di ingrandimento per la ricerca. Icone eccessivamente complesse sono difficili da riconoscere sullo schermo attraverso la chat, pertanto è consigliabile limitare le icone in rappresentazioni di tipo semplice.

Non è possibile combinare testo e icone in un determinato segmento ed evitare la combinazione di icone e testo in un singolo controllo segmentati. Deve essere tutte le icone o tutto il testo.

<a name="Summary" />

## <a name="segment-text"></a>Testo segmento

Apple rende i suggerimenti seguenti per l'utilizzo del segmento di testo:

- **Utilizzare breve, sostantivi significativo** -titolo il segmento deve indicare chiaramente il tipo di contenuto previsto per l'utente quando si seleziona il segmento specificato. Ad esempio: musica o video.
- **Utilizzare l'uso delle maiuscole iniziali maiuscole** -ogni parola del titolo segmenti deve essere in maiuscolo, ad eccezione degli articoli, congiunzioni e preposizioni di caratteri inferiore a quattro (4).
- **Utilizzare breve, con stato attivo titoli** -mantenere i titoli, breve e sono specifici per il tipo di contenuto quando è selezionato il segmento.

Nuovamente, è possibile combinare testo e icone in un determinato segmento ed evitare la combinazione di icone e testo in un singolo controllo segmentati.

<a name="Segment-Controls-and-Storyboards" />

## <a name="segment-controls-and-storyboards"></a>Controlli di segmento e di storyboard

Il modo più semplice per utilizzare i controlli di segmento in un'app Xamarin.tvOS è per aggiungerli all'interfaccia utente dell'applicazione utilizzando la finestra di progettazione iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Nel **soluzione riempimento**, fare doppio clic su di `Main.storyboard` e aprirlo per la modifica.
1. Trascinare un **segmento controllo** dal **della casella degli strumenti** e rilasciarla sulla vista: 

    [![](segmented-controls-images/segment02.png "Un segmento di controllo")](segmented-controls-images/segment02.png#lightbox)
1. Nel **scheda Widget** del **proprietà riempimento**, è possibile modificare diverse proprietà del controllo di segmento, ad esempio il **stile** e **stato**: 

    [![](segmented-controls-images/segment03.png "La scheda Widget")](segmented-controls-images/segment03.png#lightbox)
1. Utilizzare il **segmenti** campo per controllare il numero di segmenti nel controller.
1. Selezionare un determinato segmento dal **elenco a discesa segmento** per modificarne le proprietà singole, ad esempio **titolo** o **immagine** e per controllare se un determinato segmento  **Abilitato** o **selezionati** quando il controllo viene visualizzato.
1. Infine, assegnare **nomi** ai controlli in modo che è possibile rispondere ad essi nel codice c#. Ad esempio: 

    [![](segmented-controls-images/segment04.png "Assegnare un nome")](segmented-controls-images/segment04.png#lightbox)
1. Salvare le modifiche.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. Nel **Esplora**, fare doppio clic su di `Main.storyboard` e aprirlo per la modifica.
1. Trascinare un **segmento controllo** dal **della casella degli strumenti** e rilasciarla sulla vista: 

    [![](segmented-controls-images/segment02-vs.png "Un segmento di controllo")](segmented-controls-images/segment02-vs.png#lightbox)
1. Nel **scheda Widget** del **Esplora proprietà**, è possibile modificare diverse proprietà del controllo di segmento, ad esempio il **stile** e **stato**: 

    [![](segmented-controls-images/segment03-vs.png "La scheda Widget")](segmented-controls-images/segment03-vs.png#lightbox)
1. Utilizzare il **segmenti** campo per controllare il numero di segmenti nel controller.
1. Selezionare un determinato segmento dal **elenco a discesa segmento** per modificarne le proprietà singole, ad esempio **titolo** o **immagine** e per controllare se un determinato segmento  **Abilitato** o **selezionati** quando il controllo viene visualizzato.
1. Infine, assegnare **nomi** ai controlli in modo che è possibile rispondere ad essi nel codice c#. Ad esempio: 

    [![](segmented-controls-images/segment04-vs.png "Assegnare un nome")](segmented-controls-images/segment04-vs.png#lightbox)
1. Salvare le modifiche.
    
-----

Per ulteriori informazioni sull'uso di storyboard, consultare il nostro [Hello, tvOS Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Working-with-Segmented-Controls" />

## <a name="working-with-segmented-controls"></a>Utilizzo dei controlli segmentati

Come descritto in precedenza, s che segmentata controllo fornisce un set di elementi lineari, ognuno dei quali può contenere testo o un'icona e viene utilizzata per fornire un set di opzioni correlate all'utente.

Esistono diversi modi che è possibile utilizzare con i controlli segmentati nell'app Xamarin.tvOS.

<a name="Exposed-as-Outlets-and-Actions" />

## <a name="exposed-as-names-and-events"></a>Esposti come nomi e gli eventi

Se si creata il segmento di controllo nella finestra di progettazione dell'interfaccia e viene esposto come un controllo denominato e un evento è possibile utilizzare il seguente codice per rispondere alla modifica del segmento:

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

Nel caso dell'esempio precedente, il controllo di segmento è stato esposto come un `PlayerCount` nome e un `PlayerCountChanged` azione dell'evento. Per ulteriori informazioni sull'utilizzo dei punti vendita e di azioni, vedere il [scrittura del codice con punti vendita e le azioni](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code) sezione del nostro [Hello, tvOS Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md).

Il `SelectedSegment` proprietà ottiene o imposta l'indice di un segmento selezionato come zero (0) in base. Pertanto se si dispone di segmenti di cinque (5), il primo segmento avrà un indice di zero (0) e l'ultimo indice quattro (4).

<a name="Modifying-Segments" />

## <a name="modifying-segments"></a>Modifica di segmenti

In qualsiasi momento è possibile modificare il numero e il contenuto dei controlli segmentati. Utilizzare il codice seguente per inserire una nuova icona segmento:

```csharp
// Icon Segment
SegmentedControl.InsertSegment(UIImage.FromFile("icon.png"), 0, true);

// Text Segment
SegmentedControl.InsertSegment("New Segment", 0, true);
```

Il secondo parametro definisce il segmento in cui è inserito utilizzando un indice a base zero (0). Se l'ultimo parametro è `true` verrà animata l'inserimento.

Per rimuovere un determinato segmento, utilizzare le operazioni seguenti:

```csharp
SegmentedControl.RemoveSegmentAtIndex(0, true);
```

O per rimuovere tutti i segmenti seguenti:

```csharp
SegmentedControl.RemoveAllSegments();
```

Anche se è l'ultimo parametro `true`, la rimozione verrà animata. Utilizzare il `NumberOfSegments` proprietà per restituire il numero corrente di segmenti.

Per ottenere il **titolo** o **icona** per un determinato segmento, utilizzare la seguente:

```csharp
// Get title
var title = SegmentedControl.TitleAt(0);

// Get icon
var icon = SegmentedControl.ImageAt(0);
```

E per modificare il **titolo** o **icona**, utilizzare la seguente:

```csharp
// Set title
SegmentedControl.SetTitle("New Title", 0);

// Set icon
SegmentedControl.SetImage(UIImage.FromFile("icon.png"), 0);
```

Per verificare se un determinato segmento **abilitato**, utilizzare la seguente:

```csharp
if (SegmentedControl.IsEnabled(0)) {
    // Do something
    ...
}
```

E a **Abilita/Disabilita** un segmento di dati, utilizzare la seguente:

```csharp
SegmentedControl.SetEnabled(false, 0);
```

<a name="Modifying-the-Segmented-Controls-Appearance" />

## <a name="modifying-the-segmented-controls-appearance"></a>Modifica l'aspetto del controllo segmentati

Per cambiare lo sfondo di un determinato segmento di un'immagine, è possibile utilizzare il codice seguente:

```csharp
SegmentedControl.SetBackgroundImage (UIImage.FromFile("background.png"), UIControlState.Normal, UIBarMetrics.Default);
```

Dove `UIControlState` specifica lo stato del controllo che si imposta l'immagine per come:

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

In cui il primo `UIControlState` specifica lo stato del segmento a sinistra del divisore e il secondo `UIControlState` specifica lo stato del segmento a destra.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è descritta la progettazione e l'utilizzo di segmenti di controllo all'interno di un'app Xamarin.tvOS.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Guide interfaccia umana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
