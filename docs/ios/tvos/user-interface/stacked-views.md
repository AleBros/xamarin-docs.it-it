---
title: Utilizzo delle visualizzazioni in pila tvOS in Xamarin
description: Questo documento descrive come ha collaborato con tvOS in pila di visualizzazioni in un'app compilata con Xamarin. Fornisce una panoramica generale delle viste in pila e viene illustrato il Layout automatico, posizionamento e ridimensionamento di una visualizzazione in pila, comuni Usa, integrazione con gli storyboard e altro ancora.
ms.prod: xamarin
ms.assetid: 00B07F85-F30B-4DD4-8664-A61D0A1CDB0E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: f51ed3d6dbbfc8a7e430c2949485838a7471e545
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61161469"
---
# <a name="working-with-tvos-stacked-views-in-xamarin"></a>Utilizzo delle visualizzazioni in pila tvOS in Xamarin

Il controllo di visualizzazione dello Stack (`UIStackView`) sfrutta la potenza del Layout automatico e le classi di dimensioni per gestire una pila di visualizzazioni secondarie, orizzontalmente o verticalmente, che risponde dinamicamente alle modifiche del contenuto e dimensioni dello schermo del dispositivo Apple TV.

In base alle proprietà definite per gli sviluppatori, ad esempio asse, distribuzione, allineamento e spaziatura gestire il layout di tutte le visualizzazioni secondarie associata a una visualizzazione dello Stack:

[![](stacked-views-images/stacked01.png "Diagramma del layout visualizzazione secondaria")](stacked-views-images/stacked01.png#lightbox)

Quando si usa un' `UIStackView` in un'app xamarin. tvos, lo sviluppatore può definire le visualizzazioni secondarie sia all'interno di uno Storyboard in iOS Designer, o aggiungendo e rimuovendo le visualizzazioni secondarie in C# codice.

## <a name="about-stacked-view-controls"></a>Informazioni sui controlli di visualizzazione in pila 

Il `UIStackView` è progettata come una visualizzazione del contenitore non relative al rendering e di conseguenza, non viene disegnato all'area di disegno, ad esempio altre sottoclassi di `UIView`. Impostazione delle proprietà, ad esempio `BackgroundColor` o si esegue l'override `DrawRect` non avranno alcun effetto visivo.

Esistono diverse proprietà che controllano il modo in cui una visualizzazione dello Stack verranno organizzati relativa raccolta di visualizzazioni secondarie:

- **Asse** : determina se la visualizzazione dello Stack vengono disposti sia le visualizzazioni secondarie **orizzontalmente** oppure **verticalmente**.
- **Allineamento** : controlla la modalità di allineamento le visualizzazioni secondarie all'interno della visualizzazione dello Stack.
- **Distribuzione** – controlla come vengono ridimensionate le visualizzazioni secondarie all'interno della visualizzazione dello Stack.
- **Spaziatura** – controlla lo spazio minimo tra ogni visualizzazione secondaria nella visualizzazione dello Stack.
- **Linea di base relativi** : se `true`, la spaziatura verticale di ogni visualizzazione secondaria verrà derivata dal relativo della linea di base.
- **Layout dei margini relativo** – inserisce le visualizzazioni secondarie relativo ai margini layout standard.

In genere si utilizzerà una visualizzazione dello Stack per disporre di un numero ridotto di visualizzazioni secondarie. È possibile creare interfacce utente più complesse mediante l'inserimento di una o più visualizzazioni dello Stack all'interno di altre.

È possibile ottimizzare ulteriormente l'aspetto delle interfacce utente mediante l'aggiunta di vincoli aggiuntivi per le visualizzazioni secondarie (ad esempio al controllo larghezza o all'altezza). Tuttavia, necessario fare attenzione a non includere i vincoli in conflitto a quelle introdotte dalla visualizzazione Stack se stesso.

<a name="Auto-Layout-and-Size-Classes" />

## <a name="auto-layout-and-size-classes"></a>Layout automatico e le classi di dimensioni

Quando una visualizzazione secondaria viene aggiunto a una visualizzazione Stack completamente il layout viene controllato da tale visualizzazione Stack con Layout automatico e le classi di dimensioni per posizionare e ridimensionare le visualizzazioni disposte.

La visualizzazione dello Stack verranno _pin_ la visualizzazione e il cognome secondaria nella propria raccolta per il **Top** e **inferiore** bordi per le visualizzazioni dello Stack verticale o **asinistra**e **destra** bordi per le visualizzazioni dello Stack orizzontale. Se si impostano i `LayoutMarginsRelativeArrangement` proprietà `true`, quindi la vista blocca le visualizzazioni secondarie margini rilevanti anziché il bordo.

La visualizzazione dello Stack Usa la visualizzazione secondaria `IntrinsicContentSize` proprietà quando si calcolano le dimensioni di visualizzazioni secondarie lungo definiti `Axis` (tranne che per il `FillEqually Distribution`). Il `FillEqually Distribution` ridimensiona tutte le visualizzazioni secondarie in modo che siano le stesse dimensioni, pertanto la compilazione di visualizzazione dello Stack lungo il `Axis`.

Ad eccezione dei `Fill Alignment`, la visualizzazione dello Stack Usa la visualizzazione secondaria `IntrinsicContentSize` proprietà per il calcolo delle dimensioni della visualizzazione perpendicolare al determinato `Axis`. Per il `Fill Alignment`, tutte le visualizzazioni secondarie vengono ridimensionate in modo da occupare la visualizzazione dello Stack perpendicolare al determinato `Axis`.

<a name="Positioning-and-Sizing-the-Stack-View" />

## <a name="positioning-and-sizing-the-stack-view"></a>Posizionamento e ridimensionamento la visualizzazione dello Stack

Durante la visualizzazione dello Stack ha il controllo completo sul layout dei qualsiasi visualizzazione secondaria (in base alle proprietà, ad esempio `Axis` e `Distribution`), è comunque necessario posizionare la visualizzazione dello Stack (`UIStackView`) all'interno della visualizzazione padre con Layout automatico e le classi di dimensioni.

In genere, ciò comporta l'aggiunta di almeno due bordi della vista dello Stack per espandere e comprimere, definendo pertanto la posizione. Senza i vincoli aggiuntivi, la visualizzazione dello Stack verrà ridimensionata automaticamente per adattare l'intero le visualizzazioni secondarie come indicato di seguito:

* Le dimensioni lungo relativo `Axis` sarà costituito dalla somma di tutte le dimensioni di visualizzazione secondaria e quindi qualsiasi spazio che è stata definita tra ogni visualizzazione secondaria.
* Se il `LayoutMarginsRelativeArrangement` è di proprietà `true`, la dimensione dello Stack viste includerà anche spazio per i margini.
* Le dimensioni perpendicolare al `Axis` verrà impostato per la visualizzazione più grande secondaria nella raccolta.

Inoltre, è possibile specificare vincoli per la visualizzazione di Stack **altezza** e **larghezza**. In questo caso, le visualizzazioni secondarie verranno disposte (dimensioni) per riempire lo spazio specificato dalla visualizzazione Stack come determinato dal `Distribution` e `Alignment` proprietà.

Se il `BaselineRelativeArrangement` proprietà è `true`, le visualizzazioni secondarie verranno disposte in base della linea di base, della prima o nell'ultima visualizzazione secondaria invece di usare i **Top**, **inferiore** o **Center* -  **Y** posizione. Questi vengono calcolati come indicato di seguito sul contenuto della visualizzazione dello Stack:

* Una visualizzazione Stack verticale restituirà la prima visualizzazione secondaria per la prima linea di base e l'ultimo per l'ultimo. Se una di queste visualizzazioni secondarie fanno visualizzazioni dello Stack, quindi verrà utilizzato il primo o ultimo della linea di base.
* Una visualizzazione Stack orizzontale userà la più alta secondaria per la previsione e il cognome. Se la vista più alta è anche una visualizzazione dello Stack, è più alta secondaria userà come linea di base.

> [!IMPORTANT]
> Allineamento della linea di base non funziona sulle dimensioni di visualizzazione secondaria allungata o compressa come verrà calcolato la linea di base alla posizione corretta. Per l'allineamento della linea di base, verificare che la visualizzazione secondaria **altezza** corrisponda alla visualizzazione del contenuto intrinseco **altezza**.




<a name="Common-Stack-View-Uses" />

## <a name="common-stack-view-uses"></a>Utilizzi comuni di vista dello Stack

Esistono diversi tipi di layout che funzionano bene con controlli di visualizzazione dello Stack. In base a Apple, ecco alcuni degli usi più comuni:

- **Definire dimensioni lungo l'asse** : per l'aggiunta di entrambi i bordi lungo della visualizzazione Stack `Axis` e uno dei lati adiacenti per impostare la posizione, lo Stack di dimensioni aumentano visualizzazione lungo l'asse per adattarsi allo spazio definito dalle visualizzazioni secondarie.
*  **Definire la posizione della visualizzazione secondaria** – aggiungendo ai bordi adiacenti della visualizzazione Stack alla relativa visualizzazione padre, la visualizzazione dello Stack aumenteranno in entrambe le dimensioni in base alle relative visualizzazioni secondarie che lo contiene.
- **Definire le dimensioni e la posizione dello Stack** – aggiungendo le quattro contorni della visualizzazione dello Stack per la visualizzazione padre, la visualizzazione dello Stack organizza le visualizzazioni secondarie basate allo spazio definito all'interno della visualizzazione dello Stack.
*  **Definire le dimensioni perpendicolari all'asse** : per l'aggiunta di entrambi perpendicolarmente bordi della visualizzazione Stack `Axis` e uno dei bordi lungo l'asse di impostare la posizione, lo Stack di visualizzazione aumenterà perpendicolare all'asse per adattarsi allo spazio definito da relativo visualizzazioni secondarie.

<a name="Stack-Views-and-Storyboards" />

## <a name="stack-views-and-storyboards"></a>Le visualizzazioni dello stack e storyboard

Il modo più semplice per lavorare con le visualizzazioni dello Stack in un'app xamarin. tvos è per aggiungerli all'interfaccia utente dell'app usando iOS Designer.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Nel **riquadro della soluzione**, facendo doppio clic su di `Main.storyboard` file e aprirlo e modificarlo.
1. Progettare il layout dei singoli elementi che si desidera aggiungere alla visualizzazione dello Stack: 

    [![](stacked-views-images/layout01.png "Esempio di layout elemento")](stacked-views-images/layout01.png#lightbox)
1. Aggiungere eventuali vincoli necessari per gli elementi per garantire una che scalabilità in modo corretto. Questo passaggio è importante dopo l'elemento viene aggiunto alla visualizzazione dello Stack.
1. Verificare il numero necessario di copie (quattro in questo caso): 

    [![](stacked-views-images/layout02.png "Il numero necessario di copie")](stacked-views-images/layout02.png#lightbox)
1. Trascinare un **visualizzazione Stack** dalle **della casella degli strumenti** e rilasciarla sulla vista: 

    [![](stacked-views-images/layout03.png "Una visualizzazione Stack")](stacked-views-images/layout03.png#lightbox)
1. Selezionare la visualizzazione dello Stack, nelle **scheda Widget** del **riquadro delle proprietà** selezionare **riempire** per il **allineamento**, **riempimento Ugualmente** per il **distribuzione** e immettere `25` per il **spaziatura**: 

    [![](stacked-views-images/layout04.png "La scheda di Widget")](stacked-views-images/layout04.png#lightbox)
1. Posizionare la visualizzazione dello Stack sullo schermo in cui desiderato e aggiungere i vincoli per mantenere i dati nella posizione desiderata.
1. Selezionare i singoli elementi e trascinarli nella visualizzazione dello Stack: 

    [![](stacked-views-images/layout05.png "I singoli elementi nella visualizzazione Stack")](stacked-views-images/layout05.png#lightbox)
1. Il layout verrà regolato e gli elementi verranno disposte nella visualizzazione Stack basati sugli attributi che è impostato in precedenza.
1. Assegnare **nomi** nel **scheda Widget** del **Esplora proprietà** per lavorare con i controlli dell'interfaccia utente in C# codice.
1. Salvare le modifiche.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Nel **Esplora soluzioni**, facendo doppio clic su di `Main.storyboard` file e aprirlo e modificarlo.
1. Progettare il layout dei singoli elementi che si desidera aggiungere alla visualizzazione dello Stack: 

    [![](stacked-views-images/layout01.png "Esempio di layout elemento")](stacked-views-images/layout01.png#lightbox)
1. Aggiungere eventuali vincoli necessari per gli elementi per garantire una che scalabilità in modo corretto. Questo passaggio è importante dopo l'elemento viene aggiunto alla visualizzazione dello Stack.
1. Verificare il numero necessario di copie (quattro in questo caso): 

    [![](stacked-views-images/layout02.png "Il numero necessario di copie")](stacked-views-images/layout02.png#lightbox)
1. Trascinare un **visualizzazione Stack** dalle **della casella degli strumenti** e rilasciarla sulla vista: 

    [![](stacked-views-images/layout03-vs.png "Una visualizzazione Stack")](stacked-views-images/layout03-vs.png#lightbox)
1. Selezionare la visualizzazione dello Stack, nel **scheda Widget** del **riquadro delle proprietà** selezionare **riempire** per il **allineamento**, **riempimento Ugualmente** per il **distribuzione** e immettere `25` per il **spaziatura**: 

    [![](stacked-views-images/layout04-vs.png "La scheda di Widget")](stacked-views-images/layout04-vs.png#lightbox)
1. Posizionare la visualizzazione dello Stack sullo schermo in cui desiderato e aggiungere i vincoli per mantenere i dati nella posizione desiderata.
1. Selezionare i singoli elementi e trascinarli nella visualizzazione dello Stack: 

    [![](stacked-views-images/layout05-vs.png "I singoli elementi nella visualizzazione Stack")](stacked-views-images/layout05-vs.png#lightbox)
1. Il layout verrà regolato e gli elementi verranno disposte nella visualizzazione Stack basati sugli attributi che è impostato in precedenza.
1. Assegnare **nomi** nel **scheda Widget** del **Esplora proprietà** per lavorare con i controlli dell'interfaccia utente in C# codice.
1. Salvare le modifiche.

-----

> [!IMPORTANT]
> Sebbene sia possibile assegnare le azioni, ad esempio `TouchUpInside` a un elemento dell'interfaccia utente (ad esempio un `UIButton`) in iOS Designer durante la creazione di un gestore eventi, non verrà mai chiamato perché non dispone di un tocco Apple TV a schermo intero o supportare gli eventi di tocco. È consigliabile usare sempre il valore predefinito `Action Type` durante la creazione di azioni per tvOS elementi dell'interfaccia utente.

Per altre informazioni sull'utilizzo degli storyboard, vedere la [App Tvos Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md).

Nel caso di questo esempio, è stata esposta un Outlet e azioni per il controllo di segmento e una presa per ogni scheda di Windows Media player"". Nel codice, è nascondere e mostrare player basata il segmento corrente. Ad esempio:

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

Quando viene eseguita l'app, i quattro elementi verranno distribuiti in modo uniforme nella nostra vista dello Stack:

[![](stacked-views-images/layout06.png "Quando viene eseguita l'app, i quattro elementi verranno distribuiti in modo uniforme nella nostra vista dello Stack")](stacked-views-images/layout06.png#lightbox)

Se è diminuito il numero di giocatori, vengono nascoste le viste inutilizzate e la visualizzazione dello Stack modificare il layout in base alle:

[![](stacked-views-images/layout07.png "Se è diminuito il numero di giocatori, le viste non utilizzate sono nascoste e la visualizzazione dello Stack modificare il layout per adattarlo")](stacked-views-images/layout07.png#lightbox)

<a name="Populate-a-Stack-View-from-Code" />

### <a name="populate-a-stack-view-from-code"></a>Popolare una visualizzazione Stack dal codice

Oltre a definire completamente il contenuto e il layout di una visualizzazione dello Stack in iOS Designer, è possibile creare e rimuovere l'oggetto in modo dinamico da C# codice.

Eseguire l'esempio seguente che usa una visualizzazione dello Stack per la gestione "stars in una revisione (da 1 a 5):

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

<a name="Dynamically-Changing-Content" />

## <a name="dynamically-changing-content"></a>Modifica dinamica del contenuto

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

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato la progettazione e l'utilizzo di visualizzazione in pila all'interno di un'app xamarin. tvos.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [le guide dell'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
