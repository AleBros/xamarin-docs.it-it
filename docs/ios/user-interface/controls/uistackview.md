---
title: Viste dello stack in xamarin. IOS
description: In questo articolo viene illustrato l'utilizzo del nuovo controllo UIStackView in un'app xamarin. IOS per gestire un set di sottoviste in entrambi uno stack disposto orizzontalmente o verticalmente.
ms.prod: xamarin
ms.assetid: 20246E87-2A49-438A-9BD7-756A1B50A617
ms.technology: xamarin-ios
ms.custom: xamu-video
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: a894efebe4089adefeb02007bd394c13fc77974c
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790100"
---
# <a name="stack-views-in-xamarinios"></a>Viste dello stack in xamarin. IOS

_In questo articolo viene illustrato l'utilizzo del nuovo controllo UIStackView in un'app xamarin. IOS per gestire un set di sottoviste in entrambi uno stack disposto orizzontalmente o verticalmente._

> [!IMPORTANT]
> Si noti che mentre StackView è supportata nella finestra di progettazione iOS, che possono verificarsi errori di usabilità usando il canale stabile. Cambio di canali Beta o Alpha devono risolvere questo problema. Si è deciso di presentare in questa procedura dettagliata usando Xcode fino a quando non vengono implementate le correzioni necessarie nel canale stabile.

Controllo di visualizzazione dello Stack (`UIStackView`) si avvale di Layout automatico e le classi di dimensioni per gestire una pila di sottoviste, orizzontale o verticale, che rappresenta le risposte in modo dinamico per le dimensioni di schermata e orientamento del dispositivo iOS.

In base alle proprietà di sviluppatore definiti, ad esempio asse, distribuzione, l'allineamento e spaziatura gestire il layout di tutti i sottoviste associata a una visualizzazione dello Stack:

[![](uistackview-images/stacked01.png "Diagramma di layout visualizzazione stack")](uistackview-images/stacked01.png#lightbox)

Quando si utilizza un `UIStackView` in un'app xamarin. IOS, lo sviluppatore può definire sottoviste sia all'interno di uno Storyboard nella finestra di progettazione iOS o aggiungendo e rimuovendo sottoviste nel codice c#.

Questo documento è costituito da due parti: un avvio rapido per visualizzare lo stack prima di implementare e quindi alcuni altri dettagli tecnici sul suo funzionamento.

> [!VIDEO https://youtube.com/embed/p3po6507Ip8]

**UIStackView, da [University Xamarin](https://university.xamarin.com/)**

## <a name="uistackview-quickstart"></a>Guida introduttiva UIStackView

Come una rapida introduzione al `UIStackView` (controllo), occorre creare una semplice interfaccia che consente all'utente di immettere una classificazione da 1 a 5. Verrà usato due visualizzazioni dello Stack: uno per organizzare l'interfaccia verticalmente sullo schermo del dispositivo parti e uno per disporre le icone di valutazione 1-5 orizzontalmente sullo schermo.

### <a name="define-the-ui"></a>Definire l'interfaccia utente

Avviare un nuovo progetto xamarin e modificare il **Main** file in Generatore del Xcode di interfaccia. In primo luogo, trascinare un singolo **visualizzazione Stack verticale** sul **View Controller**:

[![](uistackview-images/quick01.png "Trascinare una singola visualizzazione verticale dello Stack nel Controller di visualizzazione")](uistackview-images/quick01.png#lightbox)

Nel **controllo attributo**, impostare le opzioni seguenti:

[![](uistackview-images/quick02.png "Impostare le opzioni di visualizzazione dello Stack")](uistackview-images/quick02.png#lightbox)

Dove:

- **Asse** – determina se la visualizzazione Stack dispone sia di sottoviste **orizzontalmente** o **verticalmente**.
- **Allineamento** : controlla la modalità di allineamento di sottoviste all'interno della visualizzazione dello Stack.
- **Distribuzione** : controlla la modalità di sottoviste vengono ridimensionate all'interno della visualizzazione dello Stack.
- **Spaziatura** – controlla lo spazio minimo tra ogni visualizzazione secondaria nella visualizzazione dello Stack.
- **Linea di base relativo** : se l'opzione è selezionata, la spaziatura verticale di ogni visualizzazione secondaria verrà derivata dalla linea di base.
- **I margini di layout relativo** – inserisce le sottoviste rispetto ai margini layout standard.

Quando si lavora con una visualizzazione dello Stack, è possibile considerare il **allineamento** come il **X** e **Y** sede la visualizzazione secondaria e **distribuzione** come il **Altezza** e **larghezza**.

> [!IMPORTANT]
> `UIStackView` è progettato come una visualizzazione del contenitore non per il rendering e di conseguenza, non viene disegnato all'area di disegno, ad esempio altri sottoclassi di `UIView`. Pertanto l'impostazione di proprietà, ad esempio `BackgroundColor` o si esegue l'override `DrawRect` non avrà alcun effetto visivo.

Continuare a layout dell'interfaccia dell'app aggiungendo un'etichetta, ImageView, due pulsanti e una visualizzazione Stack orizzontale in modo che risulti simile al seguente:

[![](uistackview-images/quick03.png "Layout dell'interfaccia utente visualizzazione Stack")](uistackview-images/quick03.png#lightbox)

Configurare la visualizzazione Stack orizzontale con le opzioni seguenti:

[![](uistackview-images/quick04.png "Configurare le opzioni di visualizzazione Stack orizzontale")](uistackview-images/quick04.png#lightbox)

Poiché non vogliamo l'icona che rappresenta ogni punto"" nella valutazione estensione quando questo viene aggiunto alla visualizzazione Stack orizzontale, è stato impostato il **allineamento** a **Center** e  **Distribuzione** a **riempimento ugualmente**.

Infine, associare le operazioni seguenti **prese** e **azioni**:

[![](uistackview-images/quick05.png "Le prese visualizzazione Stack e le azioni")](uistackview-images/quick05.png#lightbox)

### <a name="populate-a-uistackview-from-code"></a>Popolare un UIStackView dal codice

Tornare a Visual Studio per Mac e modifica di **ViewController.cs** file e aggiungere il codice seguente:

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

Esaminiamo un alcuni blocchi di codice in modo dettagliato. Innanzitutto, utilizziamo un `if` istruzioni per verificare se esiste più di cinque "stelle" o è minore di zero.

Per aggiungere una nuova "stella" è caricare l'immagine e impostare il relativo **modalità contenuto** a **adatta aspetto**:

```csharp
var icon = new UIImageView (new UIImage("icon.png"));
icon.ContentMode = UIViewContentMode.ScaleAspectFit;
```

Ciò evita che venga distorta quando viene aggiunto alla visualizzazione Stack icona a "stella".

Successivamente si aggiungerà la nuova icona "stella" alla raccolta della visualizzazione dello Stack di sottoviste:

```csharp
RatingView.AddArrangedSubview(icon);
```

Si noterà che è stato aggiunto il `UIImageView` per il `UIStackView`del `ArrangedSubviews` proprietà e non il `SubView`. Aggiungere qualsiasi visualizzazione che si desidera la visualizzazione dello Stack per controllare il layout di `ArrangedSubviews` proprietà.

Per rimuovere una visualizzazione secondaria dalla visualizzazione Stack, innanzitutto si ottiene la visualizzazione secondaria da rimuovere:

```csharp
var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];
```

È necessario rimuoverlo da entrambe le `ArrangedSubviews` raccolta e la visualizzazione con privilegi avanzati:

```csharp
// Remove from stack and screen
RatingView.RemoveArrangedSubview(icon);
icon.RemoveFromSuperview();
```

Rimozione di una visualizzazione secondaria da solo la `ArrangedSubviews` raccolta accetta dal controllo della visualizzazione Stack, ma non rimuovere dalla schermata.

### <a name="testing-the-ui"></a>Test dell'interfaccia utente

Con tutte le necessarie elementi dell'interfaccia utente e codice nella posizione, è ora possibile eseguire e testare l'interfaccia. Quando viene visualizzata l'interfaccia utente, tutti gli elementi nella visualizzazione Stack verticale verrà essere equidistanti dall'alto verso il basso.

Quando l'utente tocca il **aumenta classificazione** pulsante, un altro a "stella" viene aggiunto alla schermata (fino a un massimo di 5):

[![](uistackview-images/intro01.png "Eseguire l'app di esempio")](uistackview-images/intro01.png#lightbox)

Verranno automaticamente centrate e distribuite uniformemente in visualizzazione Stack orizzontale "Stelle". Quando l'utente tocca il **Riduci classificazione** pulsante, a "stella" viene rimosso (fino a quando non sono a sinistra).

## <a name="stack-view-details"></a>Dettagli visualizzazione dello stack

Ora che è disponibile un'idea generale di ciò che il `UIStackView` controllo e il suo funzionamento, è opportuno esaminare alcune delle relative funzioni e i dettagli più approfondita.

### <a name="auto-layout-and-size-classes"></a>Layout automatico e le classi di dimensioni

Come abbiamo visto sopra, quando una visualizzazione secondaria viene aggiunto a una visualizzazione Stack il layout è completamente controllato da tale visualizzazione Stack con Layout automatico e le classi di dimensioni per posizionare e ridimensionare le visualizzazioni disposti.

La visualizzazione Stack verrà _pin_ la visualizzazione secondaria e il cognome nella propria raccolta per il **superiore** e **inferiore** bordi per le visualizzazioni dello Stack verticale o **lasciato**e **destra** bordi per le visualizzazioni dello Stack orizzontale. Se si imposta la `LayoutMarginsRelativeArrangement` proprietà `true`, quindi la vista consente di bloccare le sottoviste ai margini pertinente anziché il bordo.

La visualizzazione Stack utilizza la visualizzazione secondaria `IntrinsicContentSize` proprietà durante il calcolo delle dimensioni sottoviste lungo definito `Axis` (tranne che per il `FillEqually Distribution`). Il `FillEqually Distribution` Ridimensiona sottoviste tutti in modo che siano le stesse dimensioni, pertanto la compilazione la visualizzazione Stack lungo il `Axis`.

Ad eccezione del `Fill Alignment`, la visualizzazione Stack utilizza la visualizzazione secondaria `IntrinsicContentSize` proprietà per il calcolo delle dimensioni della vista perpendicolare il determinato `Axis`. Per il `Fill Alignment`, sottoviste tutti vengono ridimensionati in modo da occupare la visualizzazione Stack perpendicolare il determinato `Axis`.

### <a name="positioning-and-sizing-the-stack-view"></a>Il posizionamento e la visualizzazione dello Stack di ridimensionamento

Mentre la visualizzazione dello Stack ha il controllo totale il layout di qualsiasi visualizzazione secondaria (in base alle proprietà, ad esempio `Axis` e `Distribution`), è necessario posizionare la vista dello Stack (`UIStackView`) all'interno della visualizzazione padre utilizzando le classi di dimensioni e Layout automatico.

In genere, ciò significa almeno due bordi della visualizzazione dello Stack per espandere e comprimere, definendo pertanto la posizione di blocco. Senza eventuali altri vincoli, la visualizzazione Stack verrà ridimensionata automaticamente per adattare tutti relativo sottoviste come indicato di seguito:

 - Le dimensioni lungo il relativo `Axis` sarà la somma di tutte le dimensioni di visualizzazione secondaria oltre lo spazio che è stato definito tra ogni visualizzazione secondaria.
 - Se il `LayoutMarginsRelativeArrangement` proprietà `true`, la dimensione dello Stack viste includerà inoltre spazio per i margini.
 - Le dimensioni perpendicolare il `Axis` verrà impostata per la visualizzazione più grande secondaria nella raccolta.

Inoltre, è possibile specificare vincoli per la visualizzazione di Stack **altezza** e **larghezza**. In questo caso, le sottoviste verranno disposti (dimensioni) per riempire lo spazio specificato per la visualizzazione dello Stack, come determinato dal `Distribution` e `Alignment` proprietà.

Se il `BaselineRelativeArrangement` proprietà `true`, le sottoviste verranno disposti in base a una linea di base del secondaria primo o ultimo, anziché utilizzare il **Top**, **inferiore** o **Center* -  **Y** posizione. Questi vengono calcolati nel contenuto della visualizzazione dello Stack come segue:

 - Una visualizzazione Stack verticale restituirà la prima visualizzazione secondaria per la prima linea di base e l'ultimo per l'ultimo. Se uno di questi sottoviste autonomamente visualizzazioni dello Stack, verrà utilizzato prevista primo o ultimo.
 - Una visualizzazione Stack orizzontale utilizzerà la più alta secondaria per la base e il cognome. Se la vista più alta è anche una visualizzazione dello Stack, utilizzerà la più alta secondaria come linea di base.

> [!IMPORTANT]
> Allineamento della linea di base non funziona sulle dimensioni di visualizzazione secondaria allungato o compresso come verrà calcolata la linea di base in posizione errata. Per l'allineamento della linea di base, verificare che la visualizzazione secondaria **altezza** corrisponde la visualizzazione contenuto intrinseco **altezza**.

### <a name="common-stack-view-uses"></a>Utilizzi comuni di vista dello Stack

Esistono diversi tipi di layout che funzionano bene con controlli di visualizzazione dello Stack. In base a Apple, ecco alcuni degli utilizzi più comuni:

- **Definire dimensioni lungo l'asse** : aggiungendo entrambi lati lungo la visualizzazione di Stack `Axis` e uno dei bordi per impostare la posizione, lo Stack verrà aumento delle dimensioni di visualizzazione lungo l'asse per adattarsi allo spazio definito dal relativo sottoviste adiacenti.
 -  **Definire la posizione di visualizzazione secondaria** – aggiungendo al bordo adiacenti della visualizzazione dello Stack per la visualizzazione padre, la visualizzazione Stack aumenterà in entrambe le dimensioni per il contenitore sottoviste.
- **Definire le dimensioni e la posizione dello Stack** – aggiungendo tutti e quattro i bordi della visualizzazione Stack alla visualizzazione padre, la visualizzazione Stack organizza le sottoviste in base allo spazio definito all'interno della visualizzazione dello Stack.
 -  **Definire dimensioni perpendicolari all'asse** : aggiungendo entrambi perpendicolarmente bordi della visualizzazione Stack `Axis` e uno dei lati lungo l'asse per impostare la posizione, lo Stack di visualizzazione aumenteranno perpendicolari all'asse per adattarsi allo spazio definito dal relativo sottoviste.

### <a name="managing-the-appearance"></a>L'aspetto di gestione

Il `UIStackView` è progettato come una visualizzazione del contenitore non per il rendering e di conseguenza, non viene disegnato nell'area di lavoro come le altre sottoclassi di `UIView`. Impostazione delle proprietà, ad esempio `BackgroundColor` o si esegue l'override `DrawRect` non avrà alcun effetto visivo.

Esistono diverse proprietà che controllano come una visualizzazione Stack organizzerà la raccolta di sottoviste:

- **Asse** – determina se la visualizzazione Stack dispone sia di sottoviste **orizzontalmente** o **verticalmente**.
- **Allineamento** : controlla la modalità di allineamento di sottoviste all'interno della visualizzazione dello Stack.
- **Distribuzione** : controlla la modalità di sottoviste vengono ridimensionate all'interno della visualizzazione dello Stack.
- **Spaziatura** – controlla lo spazio minimo tra ogni visualizzazione secondaria nella visualizzazione dello Stack.
- **Linea di base relativo** : se `true`, la spaziatura verticale di ogni visualizzazione secondaria verrà derivata dalla linea di base.
- **I margini di layout relativo** – inserisce le sottoviste rispetto ai margini layout standard.

In genere si utilizzerà una visualizzazione dello Stack per disporre di un numero ridotto di sottoviste. È possibile creare interfacce utente più complesse mediante l'inserimento di una o più visualizzazioni dello Stack uno in altro (come nel [UIStackView Quickstart](#UIStackView-Quickstart) sopra).

È possibile ottimizzare ulteriormente l'aspetto delle interfacce utente mediante l'aggiunta di vincoli aggiuntivi per le sottoviste (ad esempio al controllo altezza o larghezza). Tuttavia, deve prestare attenzione a non includere i vincoli in conflitto a quelle introdotte dalla visualizzazione Stack stesso.

### <a name="maintaining-arranged-views-and-sub-views-consistency"></a>Gestione disposte le visualizzazioni e la coerenza di viste Sub

Garantisce che la visualizzazione dello Stack relativo `ArrangedSubviews` proprietà è sempre un sottoinsieme della relativa `Subviews` proprietà utilizzando le regole seguenti:

 - Se una visualizzazione secondaria viene aggiunto al `ArrangedSubviews` insieme, verrà automaticamente aggiunto per il `Subviews` insieme (a meno che non sia già parte di tale raccolta).
 - Se una visualizzazione secondaria viene rimosso dal `Subviews` insieme (rimossi dalla visualizzazione), viene rimossa anche dal `ArrangedSubviews` insieme.
 - Rimozione di una visualizzazione secondaria dal `ArrangedSubviews` non comporta la rimozione dall'insieme di `Subviews` insieme. Pertanto non sarà più possibile disposti dalla visualizzazione Stack, ma sarà comunque visibile sullo schermo.

Il `ArrangedSubviews` raccolta è sempre un sottoinsieme del `Subview` insieme, tuttavia l'ordine delle sottoviste singoli all'interno di ogni raccolta è separata e controllato dagli elementi seguenti:

 - L'ordine delle sottoviste all'interno di `ArrangedSubviews` raccolta determinarne l'ordine di visualizzazione all'interno dello stack.
 - L'ordine delle sottoviste all'interno di `Subview` raccolta determina le Z-Order (o dei livelli) all'interno della visualizzazione torna in primo piano.

### <a name="dynamically-changing-content"></a>Modificare dinamicamente il contenuto

Una visualizzazione Stack regolerà automaticamente il layout delle sottoviste ogni volta che una visualizzazione secondaria viene aggiunto, rimosso o nascosta. Il layout verrà regolato anche se qualsiasi proprietà della visualizzazione Stack viene regolata (ad esempio il `Axis`).

Le modifiche di layout possono essere animate inserendoli all'interno di un blocco di animazione, ad esempio:

```csharp
// Animate stack
UIView.Animate(0.25, ()=>{
    // Adjust stack view
    RatingView.LayoutIfNeeded();
});
```

Molte delle proprietà della visualizzazione Stack può essere specificati utilizzando le classi di dimensioni all'interno di uno Storyboard. Tali proprietà saranno automaticamente animato è una risposta alle modifiche di dimensione o orientamento.

## <a name="summary"></a>Riepilogo

In questo articolo è illustrati il nuovo `UIStackView` controllo (per iOS 9) per gestire un set di sottoviste in uno stack disposto orizzontalmente o verticalmente in un'app xamarin. IOS.
Inizia con un semplice esempio di utilizzo delle viste di Stack per creare un'interfaccia utente e terminato con un'analisi più approfondita in visualizzazioni dello Stack e le proprietà e le funzionalità.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 per gli sviluppatori](https://developer.apple.com/ios/pre-release/)
- [Novità in iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Riferimento UIStackView](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIStackView_Class_Reference/)
- [Introduzione a UIStackView (video)](https://university.xamarin.com/lightninglectures/introducing-uistackview-on-ios9)
