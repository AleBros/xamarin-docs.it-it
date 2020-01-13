---
title: Visualizzazioni dello stack in Xamarin.iOS
description: Questo articolo illustra l'uso del nuovo controllo UIStackView in un'app Xamarin.iOS per gestire un set di visualizzazioni in uno stack disposto orizzontalmente o verticalmente.
ms.prod: xamarin
ms.assetid: 20246E87-2A49-438A-9BD7-756A1B50A617
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: b4a8507d4d1497964f6b60307622ca3e1dc4cd90
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021797"
---
# <a name="stack-views-in-xamarinios"></a>Visualizzazioni dello stack in Xamarin.iOS

_Questo articolo illustra l'uso del nuovo controllo UIStackView in un'app Xamarin.iOS per gestire un set di visualizzazioni in uno stack disposto orizzontalmente o verticalmente._

> [!IMPORTANT]
> Si noti che, mentre StackView è supportato in iOS designer, è possibile che si verifichino bug di usabilità quando si usa il canale stabile. Il cambio dei canali Beta o alfa dovrebbe risolvere questo problema. Si è deciso di presentare questa procedura dettagliata con Xcode fino a quando non sono state implementate le correzioni necessarie nel canale stabile.

Il controllo di visualizzazione stack (`UIStackView`) sfrutta la potenza del layout automatico e delle classi di dimensioni per gestire uno stack di visualizzazioni, orizzontalmente o verticalmente, che risponde dinamicamente all'orientamento e alle dimensioni dello schermo del dispositivo iOS.

Il layout di tutte le sottoviste associate a una visualizzazione dello stack viene gestito da esso in base alle proprietà definite dallo sviluppatore, ad esempio asse, distribuzione, allineamento e spaziatura:

[![](uistackview-images/stacked01.png "Stack View layout diagram")](uistackview-images/stacked01.png#lightbox)

Quando si usa un `UIStackView` in un'app Xamarin.iOS, lo sviluppatore può definire le sottoviste all'interno di uno storyboard in iOS designer o aggiungere e rimuovere le visualizzazioni in C# codice.

Questo documento è costituito da due parti: una guida introduttiva che consente di implementare la prima visualizzazione dello stack e altri dettagli tecnici sul funzionamento.

> [!VIDEO https://youtube.com/embed/p3po6507Ip8]

**Video di UIStackView**

## <a name="uistackview-quickstart"></a>Guida introduttiva a UIStackView

Come breve introduzione al controllo `UIStackView`, verrà creata un'interfaccia semplice che consente all'utente di immettere una valutazione da 1 a 5. Verranno usate due visualizzazioni dello stack, una per disporre verticalmente l'interfaccia sullo schermo del dispositivo e l'altra per disporre orizzontalmente le icone di valutazione di 1-5 sullo schermo.

### <a name="define-the-ui"></a>Definire l'interfaccia utente

Avviare un nuovo progetto Xamarin.iOS e modificare il file **Main. Storyboard** nel Interface Builder di Xcode. Per prima cosa, trascinare una singola **visualizzazione dello stack verticale** sul **controller di visualizzazione**:

[![](uistackview-images/quick01.png "Drag a single Vertical Stack View on the View Controller")](uistackview-images/quick01.png#lightbox)

In **attribute Inspector**impostare le opzioni seguenti:

[![](uistackview-images/quick02.png "Set the Stack View options")](uistackview-images/quick02.png#lightbox)

Dove:

- **Axis** : determina se la visualizzazione dello stack dispone le sottoviste **orizzontalmente** o **verticalmente**.
- **Alignment** : controlla il modo in cui le visualizzazioni sono allineate nella visualizzazione stack.
- **Distribution** : controlla il modo in cui le visualizzazioni vengono ridimensionate all'interno della visualizzazione stack.
- **Spaziatura** : controlla lo spazio minimo tra le singole visualizzazioni nella visualizzazione stack.
- **Relativa Baseline** : se questa opzione è selezionata, la spaziatura verticale di ogni sottovista sarà derivata dalla relativa Baseline.
- **Layout margini relativi** : inserisce le visualizzazioni subordinate rispetto ai margini di layout standard.

Quando si utilizza una visualizzazione stack, è possibile considerare l' **allineamento** come la posizione **X** e **Y** della Sottovisualizzazione e la **distribuzione** come **altezza** e **larghezza**.

> [!IMPORTANT]
> `UIStackView` è progettato come una visualizzazione del contenitore senza rendering e, di conseguenza, non viene disegnato nell'area di disegno come le altre sottoclassi di `UIView`. Di conseguenza, l'impostazione di proprietà quali `BackgroundColor` o l'override di `DrawRect` non avrà alcun effetto visivo.

Continuare a eseguire il layout dell'interfaccia dell'app aggiungendo un'etichetta, ImageView, due pulsanti e una visualizzazione dello stack orizzontale in modo che sia simile a quanto segue:

[![](uistackview-images/quick03.png "Laying out the Stack View UI")](uistackview-images/quick03.png#lightbox)

Configurare la visualizzazione dello stack orizzontale con le opzioni seguenti:

[![](uistackview-images/quick04.png "Configure the Horizontal Stack View options")](uistackview-images/quick04.png#lightbox)

Poiché non si vuole che l'icona che rappresenta ogni "punto" della classificazione venga allungata quando viene aggiunta alla visualizzazione dello stack orizzontale, l' **allineamento** è stato impostato su **Center** e la **distribuzione** in modo che venga **riempita allo stesso**modo.

Infine, collegare gli **Outlet** e le **azioni**seguenti:

[![](uistackview-images/quick05.png "The Stack View Outlets and Actions")](uistackview-images/quick05.png#lightbox)

### <a name="populate-a-uistackview-from-code"></a>Popola un UIStackView dal codice

Tornare a Visual Studio per Mac e modificare il file **ViewController.cs** e aggiungere il codice seguente:

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

Verranno ora esaminati in dettaglio alcune parti di questo codice. In primo luogo, si usa un `if` istruzioni per verificare che non siano presenti più di cinque "stelle" o minori di zero.

Per aggiungere una nuova "stella", caricare la relativa immagine e impostarne la **modalità di contenuto** per la **scalabilità dell'aspetto**:

```csharp
var icon = new UIImageView (new UIImage("icon.png"));
icon.ContentMode = UIViewContentMode.ScaleAspectFit;
```

In questo modo l'icona "stella" viene distorta quando viene aggiunta alla visualizzazione stack.

Si aggiunge quindi la nuova icona "Star" alla raccolta di visualizzazioni di visualizzazione dello stack:

```csharp
RatingView.AddArrangedSubview(icon);
```

Si noterà che è stato aggiunto il `UIImageView` alla proprietà `ArrangedSubviews` della `UIStackView`e non alla `SubView`. Tutte le visualizzazioni che si desidera che la visualizzazione dello stack controlli il layout devono essere aggiunte alla proprietà `ArrangedSubviews`.

Per rimuovere una Sottovisualizzazione da una visualizzazione stack, è prima di tutto necessario rimuovere la visualizzazione:

```csharp
var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];
```

Quindi, è necessario rimuoverlo dalla raccolta di `ArrangedSubviews` e dalla visualizzazione Super:

```csharp
// Remove from stack and screen
RatingView.RemoveArrangedSubview(icon);
icon.RemoveFromSuperview();
```

La rimozione di una Sottovisualizzazione solo dalla raccolta `ArrangedSubviews` lo estrae dal controllo della visualizzazione stack, ma non lo rimuove dalla schermata.

### <a name="testing-the-ui"></a>Test dell'interfaccia utente

Con tutti gli elementi dell'interfaccia utente e il codice necessari, è ora possibile eseguire e testare l'interfaccia. Quando viene visualizzata l'interfaccia utente, tutti gli elementi nella visualizzazione dello stack verticale saranno equidistanti dall'alto verso il basso.

Quando l'utente tocca il pulsante **Aumenta classificazione** , alla schermata viene aggiunto un altro asterisco (fino a un massimo di 5):

[![](uistackview-images/intro01.png "The sample app run")](uistackview-images/intro01.png#lightbox)

Le "stelle" verranno automaticamente centrate ed equamente distribuite nella visualizzazione dello stack orizzontale. Quando l'utente tocca il pulsante **Diminuisci valutazione** , viene rimossa una "stella" (finché non ne viene lasciato nessuno).

## <a name="stack-view-details"></a>Dettagli visualizzazione stack

Ora che è stata rilevata un'idea generale del controllo `UIStackView` e del relativo funzionamento, verranno esaminate alcune delle funzionalità e dei dettagli.

### <a name="auto-layout-and-size-classes"></a>Classi layout e dimensioni automatiche

Come illustrato in precedenza, quando una sottovista viene aggiunta a una visualizzazione stack, il layout è completamente controllato da tale visualizzazione dello stack usando il layout automatico e le classi di dimensioni per posizionare e ridimensionare le visualizzazioni disposte.

La visualizzazione dello stack consente di _aggiungere_ la prima e l'ultima visualizzazione della raccolta ai bordi **superiore** e **inferiore** per le visualizzazioni dello stack verticali o i bordi **sinistro** e **destro** per le visualizzazioni dello stack orizzontale. Se si imposta la proprietà `LayoutMarginsRelativeArrangement` su `true`, la visualizzazione aggiunge le sottoviste ai margini rilevanti anziché al bordo.

La visualizzazione dello stack utilizza la proprietà `IntrinsicContentSize` della Sottovisualizzazione durante il calcolo delle dimensioni delle visualizzazioni di visualizzazione lungo il `Axis` definito (ad eccezione del `FillEqually Distribution`). Il `FillEqually Distribution` ridimensiona tutte le sottoviste in modo che abbiano le stesse dimensioni, riempiendo così la visualizzazione dello stack lungo l'`Axis`.

Fatta eccezione per il `Fill Alignment`, la visualizzazione dello stack usa la proprietà `IntrinsicContentSize` della Sottovisualizzazione per calcolare la dimensione della visualizzazione perpendicolare al `Axis`specificato. Per il `Fill Alignment`, tutte le sottoviste vengono ridimensionate in modo da riempire la visualizzazione dello stack perpendicolare alla `Axis`specificata.

### <a name="positioning-and-sizing-the-stack-view"></a>Posizionamento e ridimensionamento della visualizzazione dello stack

Mentre la visualizzazione dello stack dispone del controllo totale sul layout di qualsiasi sottovista (in base alle proprietà, ad esempio `Axis` e `Distribution`), è comunque necessario posizionare la visualizzazione dello stack (`UIStackView`) nella visualizzazione padre utilizzando il layout automatico e le classi di dimensioni.

In genere, ciò significa bloccare almeno due bordi della visualizzazione dello stack per espanderli e configurarli, definendone la posizione. Senza vincoli aggiuntivi, la visualizzazione dello stack verrà ridimensionata automaticamente per adattarsi a tutte le relative visualizzazioni, come indicato di seguito:

- La dimensione lungo il `Axis` sarà la somma di tutte le dimensioni della vista, oltre a qualsiasi spazio definito tra le singole sottoviste.
- Se la proprietà `LayoutMarginsRelativeArrangement` è `true`, la dimensione delle visualizzazioni dello stack includerà anche lo spazio per i margini.
- Le dimensioni perpendicolari al `Axis` verranno impostate sulla Sottovisualizzazione più grande nella raccolta.

Inoltre, è possibile specificare vincoli per l' **altezza** e la **larghezza**della visualizzazione stack. In questo caso, le sottoviste verranno disposte (ridimensionate) in modo da riempire lo spazio specificato dalla visualizzazione dello stack in base a quanto determinato dalle proprietà `Distribution` e `Alignment`.

Se la proprietà `BaselineRelativeArrangement` è `true`, le sottoviste verranno disposte in base alla linea di base della prima o dell'ultima visualizzazione, anziché utilizzare la posizione **superiore**, **inferiore** o **centrale**- **Y** . Tali informazioni vengono calcolate nel contenuto della visualizzazione stack come indicato di seguito:

- Una visualizzazione dello stack verticale restituirà la prima Sottovisualizzazione per la prima linea di base e l'ultima per l'ultima. Se una di queste visualizzazioni è a loro volta viste dello stack, verrà usata la prima o l'ultima Baseline.
- Una visualizzazione Stack orizzontale utilizzerà la relativa Sottovisualizzazione più alta per la prima e l'ultima Baseline. Se anche la visualizzazione più alta è una visualizzazione dello stack, verrà usata come linea di base la visualizzazione più alta.

> [!IMPORTANT]
> L'allineamento della linea di base non funziona sulle dimensioni delle sottoviste estese o compresse perché la linea di base verrà calcolata in una posizione errata. Per l'allineamento della linea di base, assicurarsi che l' **altezza** della Sottovisualizzazione corrisponda all' **Altezza**della visualizzazione contenuto intrinseca.

### <a name="common-stack-view-uses"></a>Usi comuni della visualizzazione stack

Sono disponibili diversi tipi di layout che funzionano bene con i controlli di visualizzazione stack. In base a Apple, di seguito sono riportati alcuni degli usi più comuni:

- **Definisci le dimensioni lungo l'asse** , bloccando entrambi i bordi lungo il `Axis` della visualizzazione stack e uno dei bordi adiacenti per impostare la posizione, la visualizzazione dello stack si espande lungo l'asse per adattarla allo spazio definito dalle relative visualizzazioni.
- **Definire la posizione della Sottovisualizzazione** : l'aggiunta ai bordi adiacenti della visualizzazione dello stack alla visualizzazione padre, la visualizzazione dello stack aumenterà in entrambe le dimensioni per adattarla alle sottoviste che la contengono.
- **Definire le dimensioni e la posizione dello stack** : aggiungendo tutti i quattro bordi della visualizzazione stack alla visualizzazione padre, la visualizzazione dello stack dispone le visualizzazioni in base allo spazio definito nella visualizzazione dello stack.
- **Definire la dimensione perpendicolare all'asse** : aggiungendo entrambi i bordi perpendicolari al `Axis` della visualizzazione stack e a uno dei bordi lungo l'asse per impostare la posizione, la visualizzazione dello stack aumenterà perpendicolarmente all'asse per adattarla allo spazio definito dalle relative visualizzazioni.

### <a name="managing-the-appearance"></a>Gestione dell'aspetto

Il `UIStackView` è progettato come una visualizzazione del contenitore non di rendering e, di conseguenza, non viene disegnato nell'area di disegno come le altre sottoclassi di `UIView`. L'impostazione di proprietà quali `BackgroundColor` o l'override di `DrawRect` non avrà alcun effetto visivo.

Sono disponibili diverse proprietà che consentono di controllare il modo in cui una visualizzazione stack organizzerà la raccolta di visualizzazioni.

- **Axis** : determina se la visualizzazione dello stack dispone le sottoviste **orizzontalmente** o **verticalmente**.
- **Alignment** : controlla il modo in cui le visualizzazioni sono allineate nella visualizzazione stack.
- **Distribution** : controlla il modo in cui le visualizzazioni vengono ridimensionate all'interno della visualizzazione stack.
- **Spaziatura** : controlla lo spazio minimo tra le singole visualizzazioni nella visualizzazione stack.
- **Relativa alla baseline** : se `true`, la spaziatura verticale di ogni sottovista sarà derivata dalla relativa Baseline.
- **Layout margini relativi** : inserisce le visualizzazioni subordinate rispetto ai margini di layout standard.

In genere si utilizzerà una visualizzazione stack per disporre un numero ridotto di visualizzazioni. È possibile creare interfacce utente più complesse annidando una o più visualizzazioni dello stack l'una all'altra (come è stato fatto nella [Guida introduttiva di UIStackView](#uistackview-quickstart) precedente).

È possibile ottimizzare ulteriormente l'aspetto dell'utente aggiungendo vincoli aggiuntivi alle visualizzazioni (ad esempio per controllare l'altezza o la larghezza). Tuttavia, prestare attenzione a non includere vincoli in conflitto con quelli introdotti dalla visualizzazione stack.

### <a name="maintaining-arranged-views-and-sub-views-consistency"></a>Mantenimento della coerenza delle viste e delle visualizzazioni disposte

La visualizzazione dello stack garantisce che la proprietà `ArrangedSubviews` sia sempre un subset della relativa proprietà `Subviews` utilizzando le regole seguenti:

- Se una sottovista viene aggiunta alla raccolta di `ArrangedSubviews`, verrà automaticamente aggiunta alla raccolta `Subviews`, a meno che non sia già inclusa in tale raccolta.
- Se una visualizzazione subview viene rimossa dalla raccolta di `Subviews` (rimosso dalla visualizzazione), viene anche rimossa dalla raccolta di `ArrangedSubviews`.
- La rimozione di una Sottovisualizzazione dalla raccolta di `ArrangedSubviews` non lo rimuove dalla raccolta di `Subviews`. Quindi, non sarà più disposto dalla visualizzazione stack, ma sarà comunque visibile sullo schermo.

La raccolta `ArrangedSubviews` è sempre un subset della raccolta `Subview`, tuttavia l'ordine delle singole visualizzazioni all'interno di ogni raccolta è separato e controllato da quanto segue:

- L'ordine delle sottoviste all'interno della raccolta di `ArrangedSubviews` determina il relativo ordine di visualizzazione nello stack.
- L'ordine delle visualizzazioni subordinate all'interno della raccolta di `Subview` determina il rispettivo ordine Z (o Layer) all'interno della visualizzazione.

### <a name="dynamically-changing-content"></a>Modifica dinamica del contenuto

Una visualizzazione stack regola automaticamente il layout delle visualizzazioni subordinate ogni volta che una vista viene aggiunta, rimossa o nascosta. Il layout verrà regolato anche se una proprietà della visualizzazione dello stack viene regolata, ad esempio la `Axis`.

Le modifiche al layout possono essere animate inserendole in un blocco di animazione, ad esempio:

```csharp
// Animate stack
UIView.Animate(0.25, ()=>{
    // Adjust stack view
    RatingView.LayoutIfNeeded();
});
```

Molte delle proprietà della visualizzazione stack possono essere specificate utilizzando le classi di dimensioni all'interno di uno storyboard. Queste proprietà verranno automaticamente animate in risposta alle modifiche alle dimensioni o all'orientamento.

## <a name="summary"></a>Riepilogo

Questo articolo ha trattato il nuovo controllo di `UIStackView` (per iOS 9) per gestire un set di visualizzazioni in uno stack disposto orizzontalmente o verticalmente in un'app Xamarin.iOS.
Inizia con un semplice esempio di utilizzo delle visualizzazioni dello stack per creare un'interfaccia utente e completata con un aspetto più dettagliato delle visualizzazioni dello stack e delle relative proprietà e funzionalità.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 per sviluppatori](https://developer.apple.com/ios/pre-release/)
- [Novità di iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Riferimento a UIStackView](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIStackView_Class_Reference/)
- [Introduzione a UIStackView (video)](https://university.xamarin.com/lightninglectures/introducing-uistackview-on-ios9)
