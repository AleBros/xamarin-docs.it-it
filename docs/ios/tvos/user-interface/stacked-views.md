---
title: Uso di viste in pila tvOS in Novell
description: Questo documento descrive come usare le viste in pila tvOS in un'app compilata con Novell. Fornisce una panoramica generale delle visualizzazioni in pila e illustra il layout automatico, il posizionamento e il ridimensionamento di una visualizzazione in pila, usi comuni, integrazione con storyboard e altro ancora.
ms.prod: xamarin
ms.assetid: 00B07F85-F30B-4DD4-8664-A61D0A1CDB0E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 9dbcecf40c742de6e9f3a5c8458dcae3f347501d
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120734"
---
# <a name="working-with-tvos-stacked-views-in-xamarin"></a>Uso di viste in pila tvOS in Novell

Il controllo di visualizzazione Stack`UIStackView`() sfrutta la potenza del layout automatico e delle classi di dimensioni per gestire uno stack di visualizzazioni, orizzontalmente o verticalmente, che risponde dinamicamente alle modifiche del contenuto e alle dimensioni dello schermo del dispositivo Apple TV.

Il layout di tutte le sottoviste associate a una visualizzazione dello stack viene gestito da esso in base alle proprietà definite dallo sviluppatore, ad esempio asse, distribuzione, allineamento e spaziatura:

[![](stacked-views-images/stacked01.png "Diagramma di layout della visualizzazione secondaria")](stacked-views-images/stacked01.png#lightbox)

Quando si usa `UIStackView` un oggetto in un'app Novell. tvOS, lo sviluppatore può definire le sottoviste all'interno di uno storyboard in iOS designer o aggiungere e rimuovere le visualizzazioni in C# codice.

## <a name="about-stacked-view-controls"></a>Informazioni sui controlli visualizzazione in pila 

È progettato come una visualizzazione del contenitore non di rendering e, di conseguenza, non viene disegnato nell'area di disegno come le altre sottoclassi `UIView`di. `UIStackView` L'impostazione di `BackgroundColor` proprietà come o l' `DrawRect` override non avrà alcun effetto visivo.

Sono disponibili diverse proprietà che consentono di controllare il modo in cui una visualizzazione stack organizzerà la raccolta di visualizzazioni.

- **Axis** : determina se la visualizzazione dello stack dispone le sottoviste orizzontalmente o verticalmente.
- **Alignment** : controlla il modo in cui le visualizzazioni sono allineate nella visualizzazione stack.
- **Distribution** : controlla il modo in cui le visualizzazioni vengono ridimensionate all'interno della visualizzazione stack.
- **Spaziatura** : controlla lo spazio minimo tra le singole visualizzazioni nella visualizzazione stack.
- **Relativa** alla baseline: `true`se, la spaziatura verticale di ogni sottovista sarà derivata dalla relativa Baseline.
- **Layout margini relativi** : inserisce le visualizzazioni subordinate rispetto ai margini di layout standard.

In genere si utilizzerà una visualizzazione stack per disporre un numero ridotto di visualizzazioni. È possibile creare interfacce utente più complesse annidando una o più visualizzazioni dello stack l'una all'altra.

È possibile ottimizzare ulteriormente l'aspetto dell'utente aggiungendo vincoli aggiuntivi alle visualizzazioni (ad esempio per controllare l'altezza o la larghezza). Tuttavia, prestare attenzione a non includere vincoli in conflitto con quelli introdotti dalla visualizzazione stack.

<a name="Auto-Layout-and-Size-Classes" />

## <a name="auto-layout-and-size-classes"></a>Classi layout e dimensioni automatiche

Quando una sottovista viene aggiunta a una visualizzazione stack, il layout è completamente controllato da tale visualizzazione dello stack utilizzando il layout automatico e le classi di dimensioni per posizionare e ridimensionare le visualizzazioni disposte.

La visualizzazione dello stack consente di _aggiungere_ la prima e l'ultima visualizzazione della raccolta ai bordi **superiore** e **inferiore** per le visualizzazioni dello stack verticali o i bordi **sinistro** e **destro** per le visualizzazioni dello stack orizzontale. Se si imposta la `LayoutMarginsRelativeArrangement` proprietà su `true`, la visualizzazione aggiunge le sottoviste ai margini rilevanti anziché al bordo.

La visualizzazione dello stack usa la proprietà della `IntrinsicContentSize` Sottovisualizzazione quando si calcolano le dimensioni delle visualizzazioni di `Axis` visualizzazione lungo l'oggetto `FillEqually Distribution`definito (ad eccezione di). Ridimensiona tutte le sottoviste in modo che abbiano le stesse dimensioni, riempiendo così la visualizzazione dello stack `Axis`lungo. `FillEqually Distribution`

Ad eccezione di `Fill Alignment`, la visualizzazione dello stack utilizza la `IntrinsicContentSize` proprietà della Sottovisualizzazione per calcolare la dimensione della visualizzazione perpendicolare all'oggetto specificato `Axis`. Per, tutte le sottoviste vengono ridimensionate in modo da riempire la visualizzazione dello stack perpendicolare all' `Axis`oggetto specificato. `Fill Alignment`

<a name="Positioning-and-Sizing-the-Stack-View" />

## <a name="positioning-and-sizing-the-stack-view"></a>Posizionamento e ridimensionamento della visualizzazione dello stack

Mentre la visualizzazione dello stack dispone del controllo totale sul layout di qualsiasi vista (in base alle proprietà, `Axis` ad `Distribution`esempio e), è comunque necessario posizionare la visualizzazione dello`UIStackView`stack () all'interno della visualizzazione padre usando le classi di layout e dimensioni automatiche.

In genere, ciò significa bloccare almeno due bordi della visualizzazione dello stack per espanderli e configurarli, definendone la posizione. Senza vincoli aggiuntivi, la visualizzazione dello stack verrà ridimensionata automaticamente per adattarsi a tutte le relative visualizzazioni, come indicato di seguito:

- La dimensione lungo il `Axis` suo sarà la somma di tutte le dimensioni della vista, oltre a qualsiasi spazio definito tra le singole sottoviste.
- Se la `LayoutMarginsRelativeArrangement` proprietà è `true`, nella dimensione delle visualizzazioni dello stack sarà inclusa anche la stanza per i margini.
- La dimensione perpendicolare all' `Axis` oggetto verrà impostata sulla Sottovisualizzazione più grande nella raccolta.

Inoltre, è possibile specificare vincoli per l' **altezza** e la **larghezza**della visualizzazione stack. In questo caso, le sottoviste verranno disposte (ridimensionate) in modo da riempire lo spazio specificato dalla visualizzazione dello stack in base `Distribution` a `Alignment` quanto determinato dalle proprietà e.

Se la `BaselineRelativeArrangement` proprietà è `true`, le visualizzazioni subordinate verranno disposte in base alla linea di base della prima o dell'ultima Sottovisualizzazione, anziché utilizzare la posizione **Top**, **Bottom** o **Center*- **Y** . Tali informazioni vengono calcolate nel contenuto della visualizzazione stack come indicato di seguito:

- Una visualizzazione dello stack verticale restituirà la prima Sottovisualizzazione per la prima linea di base e l'ultima per l'ultima. Se una di queste visualizzazioni è a loro volta viste dello stack, verrà usata la prima o l'ultima Baseline.
- Una visualizzazione Stack orizzontale utilizzerà la relativa Sottovisualizzazione più alta per la prima e l'ultima Baseline. Se anche la visualizzazione più alta è una visualizzazione dello stack, verrà usata come linea di base la visualizzazione più alta.

> [!IMPORTANT]
> L'allineamento della linea di base non funziona sulle dimensioni delle sottoviste estese o compresse perché la linea di base verrà calcolata in una posizione errata. Per l'allineamento della linea di base, assicurarsi che l' **altezza** della Sottovisualizzazione corrisponda all' **Altezza**della visualizzazione contenuto intrinseca.




<a name="Common-Stack-View-Uses" />

## <a name="common-stack-view-uses"></a>Usi comuni della visualizzazione stack

Sono disponibili diversi tipi di layout che funzionano bene con i controlli di visualizzazione stack. In base a Apple, di seguito sono riportati alcuni degli usi più comuni:

- **Definisci le dimensioni lungo l'asse** , bloccando entrambi i bordi lungo la visualizzazione `Axis` stack e uno dei bordi adiacenti per impostare la posizione, la visualizzazione dello stack aumenterà lungo l'asse per adattarla allo spazio definito dalle relative viste sottostanti.
- **Definire la posizione della Sottovisualizzazione** : l'aggiunta ai bordi adiacenti della visualizzazione dello stack alla visualizzazione padre, la visualizzazione dello stack aumenterà in entrambe le dimensioni per adattarla alle sottoviste che la contengono.
- **Definire le dimensioni e la posizione dello stack** : aggiungendo tutti i quattro bordi della visualizzazione stack alla visualizzazione padre, la visualizzazione dello stack dispone le visualizzazioni in base allo spazio definito nella visualizzazione dello stack.
- **Definire la dimensione perpendicolare all'asse** , bloccando entrambi i bordi perpendicolari alla visualizzazione `Axis` dello stack e uno dei bordi lungo l'asse per impostare la posizione, la visualizzazione dello stack aumenterà perpendicolarmente all'asse per adattarla allo spazio definito dalle relative visualizzazioni.

<a name="Stack-Views-and-Storyboards" />

## <a name="stack-views-and-storyboards"></a>Visualizzazioni dello stack e storyboard

Il modo più semplice per usare le visualizzazioni dello stack in un'app Novell. tvOS consiste nell'aggiungerle all'interfaccia utente dell'app tramite iOS designer.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Nella **riquadro della soluzione**fare doppio clic sul `Main.storyboard` file e aprirlo per la modifica.
1. Progettare il layout dei singoli elementi che si intende aggiungere alla visualizzazione dello stack: 

    [![](stacked-views-images/layout01.png "Esempio di layout degli elementi")](stacked-views-images/layout01.png#lightbox)
1. Aggiungere tutti i vincoli obbligatori agli elementi per garantirne la scalabilità corretta. Questo passaggio è importante quando l'elemento viene aggiunto alla visualizzazione stack.
1. Apportare il numero di copie necessario (quattro in questo caso): 

    [![](stacked-views-images/layout02.png "Numero di copie necessario")](stacked-views-images/layout02.png#lightbox)
1. Trascinare una **visualizzazione stack** dalla **casella degli strumenti** e rilasciarla nella visualizzazione: 

    [![](stacked-views-images/layout03.png "Visualizzazione stack")](stacked-views-images/layout03.png#lightbox)
1. Selezionare la visualizzazione dello stack, nella **scheda Widget** del **riquadro delle proprietà** Selezionare **riempimento** per l' **allineamento**, **compilare ugualmente** per la **distribuzione** e immettere `25` per la **spaziatura**: 

    [![](stacked-views-images/layout04.png "Scheda widget")](stacked-views-images/layout04.png#lightbox)
1. Posizionare la visualizzazione dello stack sullo schermo in cui si desidera e aggiungere vincoli per mantenerla nella posizione richiesta.
1. Selezionare i singoli elementi e trascinarli nella visualizzazione dello stack: 

    [![](stacked-views-images/layout05.png "Singoli elementi nella visualizzazione dello stack")](stacked-views-images/layout05.png#lightbox)
1. Il layout verrà regolato e gli elementi verranno disposti nella visualizzazione dello stack in base agli attributi impostati in precedenza.
1. Assegnare i **nomi** nella **scheda Widget** di **Esplora proprietà** per usare i controlli dell'interfaccia utente nel C# codice.
1. Salvare le modifiche.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Nella **Esplora soluzioni**fare doppio clic sul `Main.storyboard` file e aprirlo per la modifica.
1. Progettare il layout dei singoli elementi che si intende aggiungere alla visualizzazione dello stack: 

    [![](stacked-views-images/layout01.png "Esempio di layout degli elementi")](stacked-views-images/layout01.png#lightbox)
1. Aggiungere tutti i vincoli obbligatori agli elementi per garantirne la scalabilità corretta. Questo passaggio è importante quando l'elemento viene aggiunto alla visualizzazione stack.
1. Apportare il numero di copie necessario (quattro in questo caso): 

    [![](stacked-views-images/layout02.png "Numero di copie necessario")](stacked-views-images/layout02.png#lightbox)
1. Trascinare una **visualizzazione stack** dalla **casella degli strumenti** e rilasciarla nella visualizzazione: 

    [![](stacked-views-images/layout03-vs.png "Visualizzazione stack")](stacked-views-images/layout03-vs.png#lightbox)
1. Selezionare la visualizzazione dello stack, nella **scheda Widget** di **Esplora proprietà** Selezionare **riempimento** per l' **allineamento**, **compilare ugualmente** per la **distribuzione** e immettere `25` per la **spaziatura**: 

    [![](stacked-views-images/layout04-vs.png "Scheda widget")](stacked-views-images/layout04-vs.png#lightbox)
1. Posizionare la visualizzazione dello stack sullo schermo in cui si desidera e aggiungere vincoli per mantenerla nella posizione richiesta.
1. Selezionare i singoli elementi e trascinarli nella visualizzazione dello stack: 

    [![](stacked-views-images/layout05-vs.png "Singoli elementi nella visualizzazione dello stack")](stacked-views-images/layout05-vs.png#lightbox)
1. Il layout verrà regolato e gli elementi verranno disposti nella visualizzazione dello stack in base agli attributi impostati in precedenza.
1. Assegnare i **nomi** nella **scheda Widget** di **Esplora proprietà** per usare i controlli dell'interfaccia utente nel C# codice.
1. Salvare le modifiche.

-----

> [!IMPORTANT]
> Sebbene sia possibile assegnare azioni come `TouchUpInside` a un elemento dell'interfaccia utente (ad esempio `UIButton`,) in iOS designer durante la creazione di un gestore eventi, non verrà mai chiamato perché Apple TV non ha un touchscreen o supporta gli eventi di tocco. Quando si creano azioni per gli `Action Type` elementi dell'interfaccia utente di tvOS, è consigliabile usare sempre il valore predefinito.

Per ulteriori informazioni sull'utilizzo degli storyboard, vedere la [Guida introduttiva Hello, tvOS](~/ios/tvos/get-started/hello-tvos.md).

Nel caso dell'esempio, abbiamo esposto un Outlet e un'azione per il controllo Segment e un outlet per ogni "Player Card". Nel codice viene nascosto e mostrato il lettore in base al segmento corrente. Ad esempio:

```csharp
partial void PlayerCountChanged (Foundation.NSObject sender) {

    // Take Action based on the segment
    switch(PlayerCount.SelectedSegment) {
    case 0:
        Player1.Hidden = false;
        Player2.Hidden = true;
        Player3.Hidden = true;
        Player4.Hidden = true;
        break;
    case 1:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = true;
        Player4.Hidden = true;
        break;
    case 2:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = false;
        Player4.Hidden = true;
        break;
    case 3:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = false;
        Player4.Hidden = false;
        break;
    }
}
```

Quando si esegue l'app, i quattro elementi verranno distribuiti equamente nella visualizzazione dello stack:

[![](stacked-views-images/layout06.png "Quando si esegue l'app, i quattro elementi verranno distribuiti equamente nella visualizzazione stack")](stacked-views-images/layout06.png#lightbox)

Se il numero di giocatori viene ridotto, le visualizzazioni inutilizzate vengono nascoste e la visualizzazione dello stack regola il layout in base alle esigenze:

[![](stacked-views-images/layout07.png "Se il numero di giocatori viene ridotto, le visualizzazioni inutilizzate vengono nascoste e la visualizzazione dello stack regola il layout in base alle esigenze")](stacked-views-images/layout07.png#lightbox)

<a name="Populate-a-Stack-View-from-Code" />

### <a name="populate-a-stack-view-from-code"></a>Popolare una visualizzazione stack dal codice

Oltre a definire completamente il contenuto e il layout di una visualizzazione stack in iOS designer, è possibile crearlo e rimuoverlo dinamicamente dal C# codice.

Nell'esempio seguente viene usata una visualizzazione stack per gestire le "stelle" in una revisione (da 1 a 5):

```csharp
public int Rating { get; set;} = 0;
...

partial void IncreaseRating (Foundation.NSObject sender) {

    // Maximum of 5 "stars"
    if (++Rating > 5 ) {
        // Abort
        Rating = 5;
        return;
    }

    // Create new rating icon and add it to stack
    var icon = new UIImageView (new UIImage("icon.png"));
    icon.ContentMode = UIViewContentMode.ScaleAspectFit;
    RatingView.AddArrangedSubview(icon);

    // Animate stack
    UIView.Animate(0.25, ()=>{
        // Adjust stack view
        RatingView.LayoutIfNeeded();
    });

}

partial void DecreaseRating (Foundation.NSObject sender) {

    // Minimum of zero "stars"
    if (--Rating < 0) {
        // Abort
        Rating =0;
        return;
    }

    // Get the last subview added
    var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];

    // Remove from stack and screen
    RatingView.RemoveArrangedSubview(icon);
    icon.RemoveFromSuperview();

    // Animate stack
    UIView.Animate(0.25, ()=>{
        // Adjust stack view
        RatingView.LayoutIfNeeded();
    });
}
```

Verranno ora esaminati in dettaglio alcune parti di questo codice. In primo luogo, vengono `if` usate istruzioni per verificare che non siano presenti più di cinque "stelle" o minori di zero.

Per aggiungere una nuova "stella", caricare la relativa immagine e impostarne la **modalità di contenuto** per la scalabilità dell' **aspetto**:

```csharp
var icon = new UIImageView (new UIImage("icon.png"));
icon.ContentMode = UIViewContentMode.ScaleAspectFit;
```

In questo modo l'icona "stella" viene distorta quando viene aggiunta alla visualizzazione stack.

Si aggiunge quindi la nuova icona "Star" alla raccolta di visualizzazioni di visualizzazione dello stack:

```csharp
RatingView.AddArrangedSubview(icon);
```

Si noterà `UIImageView` che è stato aggiunto `UIStackView` `ArrangedSubviews` all'oggetto della proprietà e non a `SubView`. Qualsiasi visualizzazione che si desidera venga controllata dalla visualizzazione dello stack per il layout deve essere aggiunta `ArrangedSubviews` alla proprietà.

Per rimuovere una Sottovisualizzazione da una visualizzazione stack, è prima di tutto necessario rimuovere la visualizzazione:

```csharp
var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];
```

Quindi è necessario rimuoverlo dalla `ArrangedSubviews` raccolta e dalla visualizzazione Super:

```csharp
// Remove from stack and screen
RatingView.RemoveArrangedSubview(icon);
icon.RemoveFromSuperview();
```

La rimozione di una Sottovisualizzazione solo `ArrangedSubviews` dalla raccolta lo estrae dal controllo della visualizzazione stack, ma non dalla schermata.

<a name="Dynamically-Changing-Content" />

## <a name="dynamically-changing-content"></a>Modifica dinamica del contenuto

Una visualizzazione stack regola automaticamente il layout delle visualizzazioni subordinate ogni volta che una vista viene aggiunta, rimossa o nascosta. Il layout verrà regolato anche se una proprietà della visualizzazione dello stack viene regolata, ad esempio `Axis`.

Le modifiche al layout possono essere animate inserendole in un blocco di animazione, ad esempio:

```csharp
// Animate stack
UIView.Animate(0.25, ()=>{
    // Adjust stack view
    RatingView.LayoutIfNeeded();
});
```

Molte delle proprietà della visualizzazione stack possono essere specificate utilizzando le classi di dimensioni all'interno di uno storyboard. Queste proprietà verranno automaticamente animate in risposta alle modifiche alle dimensioni o all'orientamento.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è stata illustrata la progettazione e l'utilizzo di una vista in pila all'interno di un'app Novell. tvOS.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guide all'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione delle app per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
