---
title: Viste dello stack in xamarin. IOS
description: Questo articolo illustra l'uso del nuovo controllo UIStackView in un'app xamarin. IOS per gestire un set di visualizzazioni secondarie in uno uno stack disposto orizzontalmente o verticalmente.
ms.prod: xamarin
ms.assetid: 20246E87-2A49-438A-9BD7-756A1B50A617
ms.technology: xamarin-ios
ms.custom: xamu-video
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: d04ce6827f6695163c7d9a8d34acc3891d854c8d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122673"
---
# <a name="stack-views-in-xamarinios"></a>Viste dello stack in xamarin. IOS

_Questo articolo illustra l'uso del nuovo controllo UIStackView in un'app xamarin. IOS per gestire un set di visualizzazioni secondarie in uno uno stack disposto orizzontalmente o verticalmente._

> [!IMPORTANT]
> Si noti che mentre StackView è supportata in iOS Designer, possono insorgere usabilità bug quando si usa il canale stabile. I canali Beta o alfa di commutazione allevierà il problema. Si è deciso di presentare questa procedura dettagliata usando Xcode fino a quando non le correzioni necessarie vengono implementate nel canale stabile.

Il controllo di visualizzazione dello Stack (`UIStackView`) sfrutta la potenza del Layout automatico e le classi di dimensioni per gestire una pila di visualizzazioni secondarie, orizzontalmente o verticalmente, che risponde dinamicamente alle dimensioni dello schermo e l'orientamento del dispositivo iOS.

In base alle proprietà definite per gli sviluppatori, ad esempio asse, distribuzione, allineamento e spaziatura gestire il layout di tutte le visualizzazioni secondarie associata a una visualizzazione dello Stack:

[![](uistackview-images/stacked01.png "Diagramma del layout visualizzazione stack")](uistackview-images/stacked01.png#lightbox)

Quando si usa un `UIStackView` in un'app xamarin. IOS, lo sviluppatore può definire le visualizzazioni secondarie sia all'interno di uno Storyboard in iOS Designer, o aggiungendo e rimuovendo le visualizzazioni secondarie nel codice c#.

Questo documento è costituito da due parti: una Guida introduttiva che consentono di visualizzare lo stack prima di implementare e quindi alcune informazioni tecniche più dettagliate sul funzionamento.

> [!VIDEO https://youtube.com/embed/p3po6507Ip8]

**UIStackView, da [Xamarin University](https://university.xamarin.com/)**

## <a name="uistackview-quickstart"></a>Guida introduttiva UIStackView

Come una rapida introduzione al `UIStackView` (controllo), si intende creare un'interfaccia semplice che consente all'utente di immettere una classificazione da 1 a 5. Verrà usato due visualizzazioni dello Stack: uno per disporre l'interfaccia verticalmente sullo schermo del dispositivo parti e uno per disporre le icone di classificazione da 1 a 5 in senso orizzontale sullo schermo.

### <a name="define-the-ui"></a>Definire l'interfaccia utente

Avviare un nuovo progetto xamarin. IOS e modificare il **Main. Storyboard** file in Interface Builder di Xcode. In primo luogo, trascinare un singolo **visualizzazione Stack verticale** nel **View Controller**:

[![](uistackview-images/quick01.png "Trascinare una singola visualizzazione verticale dello Stack nel Controller di visualizzazione")](uistackview-images/quick01.png#lightbox)

Nel **Inspector attributo**, impostare le opzioni seguenti:

[![](uistackview-images/quick02.png "Impostare le opzioni di visualizzazione Stack")](uistackview-images/quick02.png#lightbox)

Dove:

- **Asse** : determina se la visualizzazione dello Stack vengono disposti sia le visualizzazioni secondarie **orizzontalmente** oppure **verticalmente**.
- **Allineamento** : controlla la modalità di allineamento le visualizzazioni secondarie all'interno della visualizzazione dello Stack.
- **Distribuzione** – controlla come vengono ridimensionate le visualizzazioni secondarie all'interno della visualizzazione dello Stack.
- **Spaziatura** – controlla lo spazio minimo tra ogni visualizzazione secondaria nella visualizzazione dello Stack.
- **Linea di base relativo** – se selezionata, la spaziatura verticale di ogni visualizzazione secondaria verrà derivata dal relativo della linea di base.
- **Layout dei margini relativo** – inserisce le visualizzazioni secondarie relativo ai margini layout standard.

Quando si lavora con una visualizzazione dello Stack, è possibile considerare il **allineamento** come il **X** e **Y** posizione della visualizzazione secondaria e il **distribuzione** come il **Altezza** e **larghezza**.

> [!IMPORTANT]
> `UIStackView` è progettata come una visualizzazione del contenitore non relative al rendering e di conseguenza, non viene disegnato all'area di disegno, ad esempio altre sottoclassi di `UIView`. Quindi, impostare proprietà quali `BackgroundColor` o si esegue l'override `DrawRect` non avranno alcun effetto visivo.

Continuare al layout dell'interfaccia dell'app mediante l'aggiunta di un'etichetta, ImageView, due pulsanti e una visualizzazione Stack orizzontale in modo che risulti simile al seguente:

[![](uistackview-images/quick03.png "Definizione del layout dell'interfaccia utente visualizzazione Stack")](uistackview-images/quick03.png#lightbox)

Configurare la visualizzazione dello Stack orizzontale con le opzioni seguenti:

[![](uistackview-images/quick04.png "Configurare le opzioni di visualizzazione dello Stack orizzontale")](uistackview-images/quick04.png#lightbox)

Poiché non vogliamo l'icona che rappresenta ogni punto di"" la classificazione per estenderla quando viene aggiunto alla visualizzazione Stack orizzontale, è stato impostato il **allineamento** al **Center** e il  **Distribuzione** al **riempire ugualmente**.

Infine, collegare i seguenti **Outlet** e **azioni**:

[![](uistackview-images/quick05.png "Le Stack visualizzazione Outlet e azioni")](uistackview-images/quick05.png#lightbox)

### <a name="populate-a-uistackview-from-code"></a>Popolare un UIStackView dal codice

Tornare a Visual Studio per Mac e modificare il **ViewController.cs** file e aggiungere il codice seguente:

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

Diamo un'occhiata alcune parti di questo codice in modo dettagliato. Innanzitutto, utilizziamo un `if` istruzioni per verificare che non vi è più di cinque "stelle" o è minore di zero.

Per aggiungere un nuovo "star" è caricare la propria immagine e impostare relativi **modalità a contenuti** al **aspetto adatta**:

```csharp
var icon = new UIImageView (new UIImage("icon.png"));
icon.ContentMode = UIViewContentMode.ScaleAspectFit;
```

In tal modo l'icona "a stella" possono essere distorto quando viene aggiunto alla visualizzazione dello Stack.

Successivamente, aggiungiamo la nuova icona "a stella" alla raccolta della visualizzazione dello Stack delle visualizzazioni secondarie:

```csharp
RatingView.AddArrangedSubview(icon);
```

Si noterà che è stato aggiunto il `UIImageView` per il `UIStackView`del `ArrangedSubviews` proprietà e non sul `SubView`. Qualsiasi visualizzazione che si desidera che la visualizzazione dello Stack per controllare il layout deve essere aggiunto al `ArrangedSubviews` proprietà.

Per rimuovere una visualizzazione secondaria da una visualizzazione dello Stack, si ottiene prima di tutto la visualizzazione secondaria da rimuovere:

```csharp
var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];
```

È necessario rimuoverlo da entrambi i `ArrangedSubviews` raccolta e la visualizzazione con privilegi elevati:

```csharp
// Remove from stack and screen
RatingView.RemoveArrangedSubview(icon);
icon.RemoveFromSuperview();
```

Rimozione di una visualizzazione secondaria da solo la `ArrangedSubviews` raccolta lo accetta dal controllo della visualizzazione dello Stack, ma non rimuoverla dalla schermata.

### <a name="testing-the-ui"></a>Test dell'interfaccia utente

Con tutti i necessari elementi dell'interfaccia utente e codice nella posizione, è ora possibile eseguire e testare l'interfaccia. Quando viene visualizzata l'interfaccia utente, tutti gli elementi nella visualizzazione Stack verticale sarà essere equidistanti dall'alto verso il basso.

Quando l'utente tocca il **aumenta classificazione** pulsante, un altro "star" viene aggiunto alla schermata (fino a un massimo di 5):

[![](uistackview-images/intro01.png "Eseguire l'app di esempio")](uistackview-images/intro01.png#lightbox)

Le stelle"" verranno automaticamente centrate e distribuite uniformemente in visualizzazione Stack orizzontale. Quando l'utente tocca il **Riduci classificazione** pulsante, una "stella" viene rimosso (fino a quando non sono a sinistra).

## <a name="stack-view-details"></a>Dettagli visualizzazione dello stack

Ora che abbiamo un'idea generale di ciò che il `UIStackView` il controllo è e come funziona, diamo un'occhiata ad alcune delle relative funzionalità e i dettagli più approfondita.

### <a name="auto-layout-and-size-classes"></a>Layout automatico e le classi di dimensioni

Come descritto in precedenza, quando una visualizzazione secondaria viene aggiunto a una visualizzazione dello Stack del layout è completamente controllato dalla visualizzazione Stack con Layout automatico e le classi di dimensioni per posizionare e ridimensionare le visualizzazioni disposte.

La visualizzazione dello Stack verranno _pin_ la visualizzazione e il cognome secondaria nella propria raccolta per il **Top** e **inferiore** bordi per le visualizzazioni dello Stack verticale o **asinistra**e **destra** bordi per le visualizzazioni dello Stack orizzontale. Se si impostano i `LayoutMarginsRelativeArrangement` proprietà `true`, quindi la vista blocca le visualizzazioni secondarie margini rilevanti anziché il bordo.

La visualizzazione dello Stack Usa la visualizzazione secondaria `IntrinsicContentSize` proprietà quando si calcolano le dimensioni di visualizzazioni secondarie lungo definiti `Axis` (tranne che per il `FillEqually Distribution`). Il `FillEqually Distribution` ridimensiona tutte le visualizzazioni secondarie in modo che siano le stesse dimensioni, pertanto la compilazione di visualizzazione dello Stack lungo il `Axis`.

Ad eccezione dei `Fill Alignment`, la visualizzazione dello Stack Usa la visualizzazione secondaria `IntrinsicContentSize` proprietà per il calcolo delle dimensioni della visualizzazione perpendicolare al determinato `Axis`. Per il `Fill Alignment`, tutte le visualizzazioni secondarie vengono ridimensionate in modo da occupare la visualizzazione dello Stack perpendicolare al determinato `Axis`.

### <a name="positioning-and-sizing-the-stack-view"></a>Posizionamento e ridimensionamento la visualizzazione dello Stack

Durante la visualizzazione dello Stack ha il controllo completo sul layout dei qualsiasi visualizzazione secondaria (in base alle proprietà, ad esempio `Axis` e `Distribution`), è comunque necessario posizionare la visualizzazione dello Stack (`UIStackView`) all'interno della visualizzazione padre con Layout automatico e le classi di dimensioni.

In genere, ciò comporta l'aggiunta di almeno due bordi della vista dello Stack per espandere e comprimere, definendo pertanto la posizione. Senza i vincoli aggiuntivi, la visualizzazione dello Stack verrà ridimensionata automaticamente per adattare l'intero le visualizzazioni secondarie come indicato di seguito:

 - Le dimensioni lungo relativo `Axis` sarà costituito dalla somma di tutte le dimensioni di visualizzazione secondaria e quindi qualsiasi spazio che è stata definita tra ogni visualizzazione secondaria.
 - Se il `LayoutMarginsRelativeArrangement` è di proprietà `true`, la dimensione dello Stack viste includerà anche spazio per i margini.
 - Le dimensioni perpendicolare al `Axis` verrà impostato per la visualizzazione più grande secondaria nella raccolta.

Inoltre, è possibile specificare vincoli per la visualizzazione di Stack **altezza** e **larghezza**. In questo caso, le visualizzazioni secondarie verranno disposte (dimensioni) per riempire lo spazio specificato dalla visualizzazione Stack come determinato dal `Distribution` e `Alignment` proprietà.

Se il `BaselineRelativeArrangement` proprietà è `true`, le visualizzazioni secondarie verranno disposte in base della linea di base, della prima o nell'ultima visualizzazione secondaria invece di usare i **Top**, **inferiore** o **Center** -  **Y** posizione. Questi vengono calcolati come indicato di seguito sul contenuto della visualizzazione dello Stack:

 - Una visualizzazione Stack verticale restituirà la prima visualizzazione secondaria per la prima linea di base e l'ultimo per l'ultimo. Se una di queste visualizzazioni secondarie fanno visualizzazioni dello Stack, quindi verrà utilizzato il primo o ultimo della linea di base.
 - Una visualizzazione Stack orizzontale userà la più alta secondaria per la previsione e il cognome. Se la vista più alta è anche una visualizzazione dello Stack, è più alta secondaria userà come linea di base.

> [!IMPORTANT]
> Allineamento della linea di base non funziona sulle dimensioni di visualizzazione secondaria allungata o compressa come verrà calcolato la linea di base alla posizione corretta. Per l'allineamento della linea di base, verificare che la visualizzazione secondaria **altezza** corrisponda alla visualizzazione del contenuto intrinseco **altezza**.

### <a name="common-stack-view-uses"></a>Utilizzi comuni di vista dello Stack

Esistono diversi tipi di layout che funzionano bene con controlli di visualizzazione dello Stack. In base a Apple, ecco alcuni degli usi più comuni:

- **Definire dimensioni lungo l'asse** : per l'aggiunta di entrambi i bordi lungo della visualizzazione Stack `Axis` e uno dei lati adiacenti per impostare la posizione, lo Stack di dimensioni aumentano visualizzazione lungo l'asse per adattarsi allo spazio definito dalle visualizzazioni secondarie.
 -  **Definire la posizione della visualizzazione secondaria** – aggiungendo ai bordi adiacenti della visualizzazione Stack alla relativa visualizzazione padre, la visualizzazione dello Stack aumenteranno in entrambe le dimensioni in base alle relative visualizzazioni secondarie che lo contiene.
- **Definire le dimensioni e la posizione dello Stack** – aggiungendo le quattro contorni della visualizzazione dello Stack per la visualizzazione padre, la visualizzazione dello Stack organizza le visualizzazioni secondarie basate allo spazio definito all'interno della visualizzazione dello Stack.
 -  **Definire le dimensioni perpendicolari all'asse** : per l'aggiunta di entrambi perpendicolarmente bordi della visualizzazione Stack `Axis` e uno dei bordi lungo l'asse di impostare la posizione, lo Stack di visualizzazione aumenterà perpendicolare all'asse per adattarsi allo spazio definito da relativo visualizzazioni secondarie.

### <a name="managing-the-appearance"></a>La gestione dell'aspetto

Il `UIStackView` è progettata come una visualizzazione del contenitore non relative al rendering e di conseguenza, non viene disegnato all'area di disegno, ad esempio altre sottoclassi di `UIView`. Impostazione delle proprietà, ad esempio `BackgroundColor` o si esegue l'override `DrawRect` non avranno alcun effetto visivo.

Esistono diverse proprietà che controllano il modo in cui una visualizzazione dello Stack verranno organizzati relativa raccolta di visualizzazioni secondarie:

- **Asse** : determina se la visualizzazione dello Stack vengono disposti sia le visualizzazioni secondarie **orizzontalmente** oppure **verticalmente**.
- **Allineamento** : controlla la modalità di allineamento le visualizzazioni secondarie all'interno della visualizzazione dello Stack.
- **Distribuzione** – controlla come vengono ridimensionate le visualizzazioni secondarie all'interno della visualizzazione dello Stack.
- **Spaziatura** – controlla lo spazio minimo tra ogni visualizzazione secondaria nella visualizzazione dello Stack.
- **Linea di base relativi** : se `true`, la spaziatura verticale di ogni visualizzazione secondaria verrà derivata dal relativo della linea di base.
- **Layout dei margini relativo** – inserisce le visualizzazioni secondarie relativo ai margini layout standard.

In genere si utilizzerà una visualizzazione dello Stack per disporre di un numero ridotto di visualizzazioni secondarie. Interfacce utente più complesse possono essere create da una o più visualizzazioni dello Stack all'interno di altre di annidamento (come abbiamo fatto [UIStackView Quickstart](#UIStackView-Quickstart) sopra).

È possibile ottimizzare ulteriormente l'aspetto delle interfacce utente mediante l'aggiunta di vincoli aggiuntivi per le visualizzazioni secondarie (ad esempio al controllo larghezza o all'altezza). Tuttavia, necessario fare attenzione a non includere i vincoli in conflitto a quelle introdotte dalla visualizzazione Stack se stesso.

### <a name="maintaining-arranged-views-and-sub-views-consistency"></a>Gestione disposte le visualizzazioni e coerenza visualizzazioni secondarie

Fa sì che la visualizzazione dello Stack relativi `ArrangedSubviews` proprietà è sempre un sottoinsieme di relativo `Subviews` proprietà usando le regole seguenti:

 - Se una visualizzazione secondaria viene aggiunto al `ArrangedSubviews` raccolta, verrà automaticamente aggiunto per il `Subviews` raccolta (a meno che non fa già parte di tale raccolta).
 - Se una visualizzazione secondaria viene rimosso dal `Subviews` collection (rimosso dalla visualizzazione), viene rimossa anche dal `ArrangedSubviews` raccolta.
 - Rimozione di una visualizzazione secondaria dal `ArrangedSubviews` non comporta la rimozione dall'insieme la `Subviews` raccolta. Quindi non è più essere disposto da parte della vista dello Stack, ma sarà comunque visibile sullo schermo.

Il `ArrangedSubviews` raccolta è sempre un sottoinsieme del `Subview` raccolta, tuttavia l'ordine delle singole visualizzazioni secondarie all'interno di ogni raccolta è separata e controllato dagli elementi seguenti:

 - L'ordine delle visualizzazioni secondarie all'interno di `ArrangedSubviews` raccolta determinarne l'ordine di visualizzazione all'interno dello stack.
 - L'ordine delle visualizzazioni secondarie all'interno di `Subview` raccolta determina loro ordine Z (o dei livelli) all'interno della visualizzazione torna in primo piano.

### <a name="dynamically-changing-content"></a>Modifica dinamica del contenuto

Una visualizzazione Stack regolerà automaticamente il layout delle visualizzazioni secondarie ogni volta che una visualizzazione secondaria viene aggiunto, rimosso o nascosta. Il layout verrà regolato anche se le proprietà della visualizzazione Stack viene regolata (ad esempio relativi `Axis`).

Le modifiche del layout possono essere animate, posizionandoli all'interno di un blocco di animazione, ad esempio:

```csharp
// Animate stack
UIView.Animate(0.25, ()=>{
    // Adjust stack view
    RatingView.LayoutIfNeeded();
});
```

Molte delle proprietà della visualizzazione Stack può essere specificati usando le classi di dimensioni all'interno di uno Storyboard. Queste proprietà verrà automaticamente aggiunta un'animazione è risposta alle modifiche di dimensione o orientamento.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato il nuovo `UIStackView` controllare (per iOS 9) per gestire un set di visualizzazioni secondarie in uno stack disposta orizzontalmente o verticalmente in un'app xamarin. IOS.
Si è iniziato con un semplice esempio dell'uso delle visualizzazioni dello Stack per creare un'interfaccia utente e terminato con un'analisi più approfondita in visualizzazioni dello Stack e le relative proprietà e le funzionalità.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [per gli sviluppatori iOS 9](https://developer.apple.com/ios/pre-release/)
- [What ' s New in iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Riferimento UIStackView](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIStackView_Class_Reference/)
- [Introduzione a UIStackView (video)](https://university.xamarin.com/lightninglectures/introducing-uistackview-on-ios9)
