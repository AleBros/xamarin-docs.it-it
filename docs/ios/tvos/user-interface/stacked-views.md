---
title: Utilizzo di visualizzazione in pila
description: Questo articolo descrive la progettazione e l'utilizzo di visualizzazione in pila all'interno di un'app Xamarin.tvOS.
ms.topic: article
ms.prod: xamarin
ms.assetid: 00B07F85-F30B-4DD4-8664-A61D0A1CDB0E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: efc0da0b0f0a800069efb00a402361a8b0cd7f1d
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2018
---
# <a name="working-with-stacked-view"></a>Utilizzo di visualizzazione in pila

_Questo articolo descrive la progettazione e l'utilizzo di visualizzazione in pila all'interno di un'app Xamarin.tvOS._


Controllo di visualizzazione dello Stack (`UIStackView`) si avvale di Layout automatico e le classi di dimensioni per gestire una pila di sottoviste, orizzontale o verticale, che risponde dinamicamente alle modifiche del contenuto e dimensioni dello schermo del dispositivo Apple TV.

In base alle proprietà di sviluppatore definiti, ad esempio asse, distribuzione, l'allineamento e spaziatura gestire il layout di tutti i sottoviste associata a una visualizzazione dello Stack:

[![](stacked-views-images/stacked01.png "Diagramma del layout visualizzazione secondaria")](stacked-views-images/stacked01.png#lightbox)

Quando si utilizza un `UIStackView` in un'app Xamarin.tvOS, lo sviluppatore può definire sottoviste sia all'interno di uno Storyboard nella finestra di progettazione iOS o aggiungendo e rimuovendo sottoviste nel codice c#.

## <a name="about-stacked-view-controls"></a>Informazioni sui controlli di visualizzazione in pila 

Il `UIStackView` è progettato come una visualizzazione del contenitore non per il rendering e di conseguenza, non viene disegnato nell'area di lavoro come le altre sottoclassi di `UIView`. Impostazione delle proprietà, ad esempio `BackgroundColor` o si esegue l'override `DrawRect` non avrà alcun effetto visivo.

Esistono diverse proprietà che controllano come una visualizzazione Stack organizzerà la raccolta di sottoviste:

- **Asse** – determina se la visualizzazione Stack dispone sia di sottoviste **orizzontalmente** o **verticalmente**.
- **Allineamento** : controlla la modalità di allineamento di sottoviste all'interno della visualizzazione dello Stack.
- **Distribuzione** : controlla la modalità di sottoviste vengono ridimensionate all'interno della visualizzazione dello Stack.
- **Spaziatura** – controlla lo spazio minimo tra ogni visualizzazione secondaria nella visualizzazione dello Stack.
- **Linea di base relativo** : se `true`, la spaziatura verticale di ogni visualizzazione secondaria verrà derivata dalla linea di base.
- **I margini di layout relativo** – inserisce le sottoviste rispetto ai margini layout standard.

In genere si utilizzerà una visualizzazione dello Stack per disporre di un numero ridotto di sottoviste. È possibile creare interfacce utente più complesse mediante l'inserimento di una o più visualizzazioni dello Stack uno in altro.

È possibile ottimizzare ulteriormente l'aspetto delle interfacce utente mediante l'aggiunta di vincoli aggiuntivi per le sottoviste (ad esempio al controllo altezza o larghezza). Tuttavia, deve prestare attenzione a non includere i vincoli in conflitto a quelle introdotte dalla visualizzazione Stack stesso.

<a name="Auto-Layout-and-Size-Classes" />

## <a name="auto-layout-and-size-classes"></a>Layout automatico e le classi di dimensioni

Quando una visualizzazione secondaria viene aggiunto a una visualizzazione Stack il layout viene controllato completamente dalla visualizzazione Stack con Layout automatico e le classi di dimensioni per posizionare e ridimensionare le visualizzazioni disposte in modo.

La visualizzazione Stack verrà _pin_ la visualizzazione secondaria e il cognome nella propria raccolta per il **superiore** e **inferiore** bordi per le visualizzazioni dello Stack verticale o **lasciato**e **destra** bordi per le visualizzazioni dello Stack orizzontale. Se si imposta la `LayoutMarginsRelativeArrangement` proprietà `true`, quindi la vista consente di bloccare le sottoviste ai margini pertinente anziché il bordo.

La visualizzazione Stack utilizza la visualizzazione secondaria `IntrinsicContentSize` proprietà durante il calcolo delle dimensioni sottoviste lungo definito `Axis` (tranne che per il `FillEqually Distribution`). Il `FillEqually Distribution` Ridimensiona sottoviste tutti in modo che siano le stesse dimensioni, pertanto la compilazione la visualizzazione Stack lungo il `Axis`.

Ad eccezione del `Fill Alignment`, la visualizzazione Stack utilizza la visualizzazione secondaria `IntrinsicContentSize` proprietà per il calcolo delle dimensioni della vista perpendicolare il determinato `Axis`. Per il `Fill Alignment`, sottoviste tutti vengono ridimensionati in modo da occupare la visualizzazione Stack perpendicolare il determinato `Axis`.

<a name="Positioning-and-Sizing-the-Stack-View" />

## <a name="positioning-and-sizing-the-stack-view"></a>Il posizionamento e la visualizzazione dello Stack di ridimensionamento

Mentre la visualizzazione dello Stack ha il controllo totale il layout di qualsiasi visualizzazione secondaria (in base alle proprietà, ad esempio `Axis` e `Distribution`), è necessario posizionare la vista dello Stack (`UIStackView`) all'interno della visualizzazione padre utilizzando le classi di dimensioni e Layout automatico.

In genere, ciò significa almeno due bordi della visualizzazione dello Stack per espandere e comprimere, definendo pertanto la posizione di blocco. Senza eventuali altri vincoli, la visualizzazione Stack verrà ridimensionata automaticamente per adattare tutti relativo sottoviste come indicato di seguito:

* Le dimensioni lungo il relativo `Axis` sarà la somma di tutte le dimensioni di visualizzazione secondaria oltre lo spazio che è stato definito tra ogni visualizzazione secondaria.
* Se il `LayoutMarginsRelativeArrangement` proprietà `true`, la dimensione dello Stack viste includerà inoltre spazio per i margini.
* Le dimensioni perpendicolare il `Axis` verrà impostata per la visualizzazione più grande secondaria nella raccolta.

Inoltre, è possibile specificare vincoli per la visualizzazione di Stack **altezza** e **larghezza**. In questo caso, le sottoviste verranno disposti (dimensioni) per riempire lo spazio specificato per la visualizzazione dello Stack, come determinato dal `Distribution` e `Alignment` proprietà.

Se il `BaselineRelativeArrangement` proprietà `true`, le sottoviste verranno disposti in base a una linea di base del secondaria primo o ultimo, anziché utilizzare il **Top**, **inferiore** o **Center* -  **Y** posizione. Questi vengono calcolati nel contenuto della visualizzazione dello Stack come segue:

* Una visualizzazione Stack verticale restituirà la prima visualizzazione secondaria per la prima linea di base e l'ultimo per l'ultimo. Se uno di questi sottoviste autonomamente visualizzazioni dello Stack, verrà utilizzato prevista primo o ultimo.
* Una visualizzazione Stack orizzontale utilizzerà la più alta secondaria per la base e il cognome. Se la vista più alta è anche una visualizzazione dello Stack, utilizzerà la più alta secondaria come linea di base.

> [!IMPORTANT]
> Allineamento della linea di base non funziona sulle dimensioni di visualizzazione secondaria allungato o compresso come verrà calcolata la linea di base in posizione errata. Per l'allineamento della linea di base, verificare che la visualizzazione secondaria **altezza** corrisponde la visualizzazione contenuto intrinseco **altezza**.




<a name="Common-Stack-View-Uses" />

## <a name="common-stack-view-uses"></a>Utilizzi comuni di vista dello Stack

Esistono diversi tipi di layout che funzionano bene con controlli di visualizzazione dello Stack. In base a Apple, ecco alcuni degli utilizzi più comuni:

- **Definire dimensioni lungo l'asse** : aggiungendo entrambi lati lungo la visualizzazione di Stack `Axis` e uno dei bordi per impostare la posizione, lo Stack verrà aumento delle dimensioni di visualizzazione lungo l'asse per adattarsi allo spazio definito dal relativo sottoviste adiacenti.
*  **Definire la posizione di visualizzazione secondaria** – aggiungendo al bordo adiacenti della visualizzazione dello Stack per la visualizzazione padre, la visualizzazione Stack aumenterà in entrambe le dimensioni per il contenitore sottoviste.
- **Definire le dimensioni e la posizione dello Stack** – aggiungendo tutti e quattro i bordi della visualizzazione Stack alla visualizzazione padre, la visualizzazione Stack organizza le sottoviste in base allo spazio definito all'interno della visualizzazione dello Stack.
*  **Definire dimensioni perpendicolari all'asse** : aggiungendo entrambi perpendicolarmente bordi della visualizzazione Stack `Axis` e uno dei lati lungo l'asse per impostare la posizione, lo Stack di visualizzazione aumenteranno perpendicolari all'asse per adattarsi allo spazio definito dal relativo sottoviste.

<a name="Stack-Views-and-Storyboards" />

## <a name="stack-views-and-storyboards"></a>Le visualizzazioni dello stack e gli storyboard

Il modo più semplice per utilizzare le visualizzazioni dello Stack in un'app Xamarin.tvOS è per aggiungerli all'interfaccia utente dell'applicazione utilizzando la finestra di progettazione iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Nel **soluzione riempimento**, facendo doppio clic su di `Main.storyboard` e aprirlo per la modifica.
1. Progettare il layout dei singoli elementi che si desidera aggiungere alla visualizzazione dello Stack: 

    [![](stacked-views-images/layout01.png "Esempio di layout di elemento")](stacked-views-images/layout01.png#lightbox)
1. Aggiungere gli elementi per garantire che scalabilità correttamente tutti i vincoli necessari. Questo passaggio è importante dopo l'elemento viene aggiunto alla visualizzazione dello Stack.
1. Verificare il numero necessario di copie (quattro in questo caso): 

    [![](stacked-views-images/layout02.png "Il numero necessario di copie")](stacked-views-images/layout02.png#lightbox)
1. Trascinare un **visualizzazione Stack** dal **della casella degli strumenti** e rilasciarla sulla vista: 

    [![](stacked-views-images/layout03.png "Una visualizzazione dello Stack")](stacked-views-images/layout03.png#lightbox)
1. Selezionare la visualizzazione dello Stack, nel **scheda Widget** del **proprietà riempimento** selezionare **riempimento** per il **allineamento**, **riempimento Ugualmente** per il **distribuzione** e immettere `25` per il **spaziatura**: 

    [![](stacked-views-images/layout04.png "La scheda Widget")](stacked-views-images/layout04.png#lightbox)
1. Posizionare la vista Stack sullo schermo in cui si desidera e aggiungere vincoli per mantenerla nella posizione desiderata.
1. Selezionare i singoli elementi e trascinarli nella vista dello Stack: 

    [![](stacked-views-images/layout05.png "I singoli elementi nella visualizzazione dello Stack")](stacked-views-images/layout05.png#lightbox)
1. Il layout verrà regolato e disposti gli elementi nella visualizzazione dello Stack in base agli attributi che è impostato in precedenza.
1. Assegnare **nomi** nel **scheda Widget** del **Esplora proprietà** per lavorare con i controlli dell'interfaccia utente in codice c#.
1. Salvare le modifiche.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Nel **Esplora**, facendo doppio clic su di `Main.storyboard` e aprirlo per la modifica.
1. Progettare il layout dei singoli elementi che si desidera aggiungere alla visualizzazione dello Stack: 

    [![](stacked-views-images/layout01.png "Layout di elemento di esempio")](stacked-views-images/layout01.png#lightbox)
1. Aggiungere gli elementi per garantire che scalabilità correttamente tutti i vincoli necessari. Questo passaggio è importante dopo l'elemento viene aggiunto alla visualizzazione dello Stack.
1. Verificare il numero necessario di copie (quattro in questo caso): 

    [![](stacked-views-images/layout02.png "Il numero necessario di copie")](stacked-views-images/layout02.png#lightbox)
1. Trascinare un **visualizzazione Stack** dal **della casella degli strumenti** e rilasciarla sulla vista: 

    [![](stacked-views-images/layout03-vs.png "Una visualizzazione dello Stack")](stacked-views-images/layout03-vs.png#lightbox)
1. Selezionare la visualizzazione dello Stack, nel **scheda Widget** del **Esplora proprietà** selezionare **riempimento** per il **allineamento**, **riempimento Ugualmente** per il **distribuzione** e immettere `25` per il **spaziatura**: 

    [![](stacked-views-images/layout04-vs.png "La scheda Widget")](stacked-views-images/layout04-vs.png#lightbox)
1. Posizionare la vista Stack sullo schermo in cui si desidera e aggiungere vincoli per mantenerla nella posizione desiderata.
1. Selezionare i singoli elementi e trascinarli nella vista dello Stack: 

    [![](stacked-views-images/layout05-vs.png "I singoli elementi nella visualizzazione dello Stack")](stacked-views-images/layout05-vs.png#lightbox)
1. Il layout verrà regolato e disposti gli elementi nella visualizzazione dello Stack in base agli attributi che è impostato in precedenza.
1. Assegnare **nomi** nel **scheda Widget** del **Esplora proprietà** per lavorare con i controlli dell'interfaccia utente in codice c#.
1. Salvare le modifiche.

-----

> [!IMPORTANT]
> Sebbene sia possibile assegnare le azioni, ad esempio `TouchUpInside` a un elemento dell'interfaccia utente (ad esempio un `UIButton`) nella finestra di progettazione durante la creazione di un gestore eventi, iOS non verrà mai chiamato perché Apple TV non ha un tocco supportano eventi tocco o sullo schermo. È consigliabile utilizzare sempre il valore predefinito `Action Type` durante la creazione di azioni per tvOS elementi dell'interfaccia utente.

Per ulteriori informazioni sull'uso di storyboard, consultare il nostro [Hello, tvOS Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md).

Nel caso di questo esempio, è stato esposto un uscita e l'azione per il controllo di segmento e una presa di corrente per ogni scheda di Windows Media player"". Nel codice è nascondere e mostrare player dipende il segmento corrente. Ad esempio:

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

Quando si esegue l'app, i quattro elementi verranno distribuiti equamente la visualizzazione dello Stack:

[![](stacked-views-images/layout06.png "Quando si esegue l'app, i quattro elementi verranno distribuiti equamente la visualizzazione dello Stack")](stacked-views-images/layout06.png#lightbox)

Se è diminuito il numero di lettori, le viste non utilizzate sono nascosti e la visualizzazione Stack di adattare il layout in base a:

[![](stacked-views-images/layout07.png "Se è diminuito il numero di lettori, vengono nascoste le viste non utilizzate e la visualizzazione Stack di adattare il layout in base a")](stacked-views-images/layout07.png#lightbox)

<a name="Populate-a-Stack-View-from-Code" />

### <a name="populate-a-stack-view-from-code"></a>Popolare una visualizzazione Stack dal codice

Inoltre, per definire completamente il contenuto e il layout di una visualizzazione dello Stack nella finestra di progettazione iOS, è possibile creare e rimuovere dinamicamente dal codice c#.

Nell'esempio seguente che usa una visualizzazione dello Stack per gestire "stelle" in una revisione (da 1 a 5):

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

<a name="Dynamically-Changing-Content" />

## <a name="dynamically-changing-content"></a>Modificare dinamicamente il contenuto

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

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è descritta la progettazione e l'utilizzo di visualizzazione in pila all'interno di un'app Xamarin.tvOS.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Guide interfaccia umana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
