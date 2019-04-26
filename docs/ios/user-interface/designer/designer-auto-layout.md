---
title: Layout automatico con la finestra di progettazione di Xamarin per iOS
description: Questa guida presenta iOS Layout automatico e viene descritto come utilizzare la finestra di progettazione di Xamarin per iOS per creare e modificare i layout usando i vincoli. Illustra anche i vincoli di modificando nel codice, l'animazione di modifica dei vincoli e altro ancora.
ms.prod: xamarin
ms.assetid: CAC7A715-55BB-45E2-BB6D-2168D36D428F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 41a9ec90b4b734dde7a982ac3d4b2e7b2082321c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61413074"
---
# <a name="auto-layout-with-the-xamarin-designer-for-ios"></a>Layout automatico con la finestra di progettazione di Xamarin per iOS

Layout automatico (detto anche "layout adattivo") è un approccio di progettazione reattiva. A differenza di sistema di layout transitorio, posizione di ogni elemento in cui è hardcoded su un punto dello schermo, Layout automatico riguarda *relazioni* -le posizioni degli elementi relazione ad altri elementi nell'area di progettazione. Il cuore del Layout automatico è l'idea di vincoli o le regole che definiscono la posizione di un elemento o un set di elementi nel contesto di altri elementi sullo schermo. Poiché gli elementi non sono associati a una determinata posizione sullo schermo, i vincoli consentono di creare un layout adattivo in diverse dimensioni dello schermo e orientamenti del dispositivo.

In questa guida vengono presentati i vincoli e come utilizzarle in Xamarin iOS Designer. Questa Guida non illustra l'utilizzo dei vincoli a livello di codice. Per informazioni sull'uso di Layout automatico a livello di codice, vedere la [documentazione di Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html).

## <a name="requirements"></a>Requisiti

La finestra di progettazione di Xamarin per iOS è disponibile in Visual Studio per Mac in Visual Studio 2017 e versioni successive su Windows.

Questa guida presuppone una conoscenza dei componenti della finestra di progettazione dal [Introduzione a iOS Designer](~/ios/user-interface/designer/introduction.md) Guida.

## <a name="introduction-to-constraints"></a>Introduzione ai vincoli

Un vincolo è una rappresentazione matematica della relazione tra due elementi sullo schermo. Posizione dell'elemento come una relazione matematica che rappresenta un'interfaccia utente risolve diversi problemi associati come hardcoded la posizione dell'elemento di interfaccia utente. Ad esempio, se fossimo posizionare 20px un pulsante nella parte inferiore dello schermo in modalità verticale, posizione del pulsante sarà fuori dallo schermo in modalità orizzontale. Per evitare questo problema, possiamo impostare un vincolo che inserisce il bordo inferiore di 20px il pulsante nella parte inferiore della visualizzazione. La posizione del bordo del pulsante viene quindi calcolata come *button.bottom = view.bottom - 20px*, che comporterebbe la 20px pulsante nella parte inferiore della visualizzazione in modalità sia verticale e orizzontale. La possibilità di calcolare selezione basata su una relazione matematica è ciò che rende i vincoli così utili nella progettazione dell'interfaccia utente.

Quando è impostato un vincolo, creiamo un' `NSLayoutConstraint` oggetto che accetta come argomenti gli oggetti sia vincolato e le proprietà, oppure *attributi*, che verrà applicato il vincolo. Nella finestra di progettazione iOS, gli attributi includono i bordi, ad esempio il *a sinistra*, *a destra*, *top*, e *inferiore* di un elemento. Includono anche gli attributi di dimensione, ad esempio *altezza* e *larghezza*e il percorso, al punto centrale *centerX* e *centerY*. Ad esempio, quando si aggiunge un vincolo sulla posizione del limite sinistro dei due pulsanti, la finestra di progettazione sta generando il codice seguente dietro le quinte:

```csharp
View.AddConstraint (NSLayoutConstraint.Create (Button1, NSLayoutAttribute.Left, NSLayoutRelation.Equal, Button2, NSLayoutAttribute.Left, 1, 10));
```

Nella sezione successiva descrive l'uso con i vincoli di utilizzo di iOS Designer, tra cui l'abilitazione e disabilitazione di Layout automatico e usando la barra degli strumenti di vincoli.

## <a name="enable-auto-layout"></a>Abilitare il Layout automatico

La configurazione predefinita della finestra di progettazione di iOS è abilitata la modalità vincoli. Tuttavia, è necessario attivarla o disattivarla manualmente, è possibile farlo in due passaggi:

1.  Fare clic su uno spazio vuoto nell'area di progettazione. Ciò consente di deselezionare qualsiasi elemento e consente di visualizzare le proprietà per il documento Storyboard.
1.  Selezionare o deselezionare i **Autolayout usare** casella di controllo nel pannello delle proprietà:

    ![](designer-auto-layout-images/image01.png "La casella di controllo del layout automatico Usa nel pannello delle proprietà")


Per impostazione predefinita, vincoli non vengono creati o visibile sulla superficie. Al contrario, automaticamente vengono dedotti dalle informazioni frame in fase di compilazione. Per aggiungere i vincoli, è necessario selezionare un elemento nell'area di progettazione e aggiungere vincoli a esso. È possibile farlo tramite il **sulla barra degli strumenti vincolo**.

## <a name="constraints-toolbar"></a>I vincoli sulla barra degli strumenti

 [![](designer-auto-layout-images/toolbarnew.png "I comandi di menu di scelta rapida")](designer-auto-layout-images/toolbarnew.png#lightbox)

La barra degli strumenti di vincoli è stata aggiornata e ora è costituito da due parti principali:

- **Un elemento toggle pulsante modalità vincoli**: Immesso in precedenza, la modalità dei vincoli facendo nuovamente clic su una visualizzazione selezionata nell'area di progettazione. È ora consigliabile usare questo interruttore nella barra di vincoli:

  ![Attiva/Disattiva modalità di vincoli](designer-auto-layout-images/constraints.png)

- **Un pulsante di "Aggiornamento dei vincoli":** È importante notare che le modifiche a seconda se si è in modalità di modifica di vincoli.
  - In modalità di modifica vincolo questo pulsante consente di modificare i vincoli in modo che corrisponda il frame di elemento.
  - In modalità di modifica Frame questo pulsante si regola il frame di elemento in modo che corrisponda alla posizione che definiscono i vincoli.


## <a name="surface-based-constraint-editing"></a>Modifica area in base al vincolo

Nella sezione precedente, è stato illustrato come aggiungere vincoli predefiniti e rimuovere i vincoli utilizzando la barra degli strumenti di vincoli. Per la modifica di più vincoli ed efficienti, sia possibile interagire con i vincoli direttamente nell'area di progettazione. Questa sezione vengono presentate le nozioni di base della modifica di surface in base al vincolo, inclusi i controlli di spaziatura pin, aree di rilascio e l'uso di diversi tipi di vincoli.

### <a name="creating-constraints"></a>Creazione di vincoli

Lo strumento di progettazione iOS offre due tipi di controlli per la modifica di elementi nell'area di progettazione. *Trascinando i controlli* e *pin spaziatura controlli*, come illustrato nell'immagine seguente:

![controlli di visualizzazione](designer-auto-layout-images/controls.png)

Questi sono attivato o disattivati selezionando il pulsante modalità vincoli nella barra dei vincoli.

Definiscono gli handle a forma di T 4 su ciascun lato dell'elemento il *superiore*, *a destra*, *inferiore*, e *sinistro* bordi dell'elemento per un vincolo. Definiscono gli handle si a forma di due a destra e inferiore dell'elemento *altezza* e *larghezza* vincolo rispettivamente. Il quadrato centrale gestisce entrambe *centerX* e *centerY* vincoli.

Per creare un vincolo, selezionare un punto di manipolazione e trascinarlo in un punto qualsiasi nell'area di progettazione. Quando si avvia l'operazione di trascinamento, una serie di linee o caselle verdi verranno visualizzati nell'area di indicante che ciò che è possibile vincolare. Nella schermata seguente, ad esempio, si sta vincolare il lato superiore del pulsante centrale:

 [![](designer-auto-layout-images/image07.png "Vincolare il lato superiore del pulsante centrale")](designer-auto-layout-images/image07.png#lightbox)

Tenere presente le linee verdi tratteggiate tre tra gli altri due pulsanti. Le linee verdi indicano *aree di rilascio*, o gli attributi di altri elementi a cui è possibile applicare un vincolo. Nella schermata precedente, gli altri due pulsanti offrono 3 aree di rilascio verticale ( *basso*, *centerY*, *top*) per vincolare il nostro pulsante. Il controller di visualizzazione offre un vincolo nella parte superiore della visualizzazione e la casella di colore verde a tinta unita significa che il controller di visualizzazione offre un vincolo sotto la Guida layout in alto, significa che la linea tratteggiata verde nella parte superiore della visualizzazione.

> [!IMPORTANT]
> Le guide sono tipi speciali di destinazioni di vincoli che consentono di creare parte superiore e vincoli nella parte inferiore che prendono in considerazione la presenza di barre di sistema, ad esempio le barre di stato o le barre degli strumenti. Uno degli usi principali è da un'applicazione compatibile tra iOS 6 e iOS 7 perché la versione più recente è la visualizzazione del contenitore estende di sotto la barra di stato. Per altre informazioni sulle linee guida di layout verticale, vedere la [documentazione di Apple](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/transitionguide/AppearanceCustomization.html#//apple_ref/doc/uid/TP40013174-CH15-SW2).



Le tre sezioni seguenti introducono funzionante con diversi tipi di vincoli.

### <a name="size-constraints"></a>Vincoli relativi alle dimensioni

Con i vincoli di dimensioni - *altezza* e *larghezza* -sono disponibili due opzioni. La prima opzione consiste nel trascinare il punto di controllo per applicare un vincolo per una dimensione dell'elemento adiacente, come illustrato nell'esempio precedente. L'altra possibilità consiste nel fare doppio clic il punto di controllo per creare un vincolo di self. Ciò consente di specificare un valore di dimensione costante, come illustrato nello screenshot seguente:

 [![](designer-auto-layout-images/sizec.png "Trascinare il quadratino per limitare a una dimensione dell'elemento adiacente, come illustrato di seguito")](designer-auto-layout-images/sizec.png#lightbox)

### <a name="center-constraints"></a>Vincoli di Center

L'handle quadrato verrà creato un *centerX* oppure *centerY* vincolo, a seconda del contesto. Trascinare il quadratino compatibili con gli altri elementi di offrire entrambe le aree di rilascio verticale e orizzontale, come illustrato nello screenshot seguente:

 [![](designer-auto-layout-images/centerc.png "Vincoli di Center")](designer-auto-layout-images/centerc.png#lightbox)

Se si sceglie un'area di rilascio verticale, un *centerY* vincolo verrà creato. Se si sceglie un'area di rilascio orizzontale, il vincolo baserà *centerX*.

### <a name="combinational-constraints"></a>Vincoli combinational

Per creare sia l'allineamento e i vincoli di uguaglianza dimensioni tra due elementi, è possibile selezionare gli elementi da una barra degli strumenti superiore per specificare - nell'ordine: l'allineamento orizzontale, l'allineamento verticale e predicati di uguaglianza dimensioni, come illustrato nello screenshot seguente:

 [![](designer-auto-layout-images/image06.png "Vincoli combinational")](designer-auto-layout-images/image06.png#lightbox)

### <a name="visualizing-and-editing-constraints"></a>Visualizzazione e modifica di vincoli

Quando si aggiunge un vincolo, che verrà visualizzato nella finestra di progettazione come una linea blu quando si seleziona un elemento:

 [![](designer-auto-layout-images/image09.png "Visualizzazione di vincoli")](designer-auto-layout-images/image09.png#lightbox)

È possibile selezionare un vincolo facendo clic su una linea blu e modificando i valori del vincolo direttamente nel pannello delle proprietà. In alternativa, fare doppio clic su una linea blu verrà visualizzata nel popover che consente di modificare i valori direttamente nell'area di progettazione:

 [![](designer-auto-layout-images/image08.png "Modifica di vincoli")](designer-auto-layout-images/image08.png#lightbox)

## <a name="constraint-issues"></a>Problemi di vincolo

Diversi tipi di problemi possono verificarsi quando si usano i vincoli:

-  **I vincoli in conflitto** , ciò si verifica quando più vincoli di forzare l'elemento per avere i valori in conflitto per un attributo e il motore di vincolo non è in grado di risolvere le differenze tra di essi.
-  **Definendo alcuni vincoli elementi** , ovvero le proprietà di un elemento (percorso + dimensioni) devono essere completamente coperte da un set di vincoli e le dimensioni intrinseche per i vincoli di validità. Se questi valori sono ambigui, l'elemento è detto essere definendo alcuni vincoli.
-  **Frame misplacement** , ciò si verifica quando il frame di un elemento e la relativa serie di vincoli definiscono due rettangoli risultanti diversi.


In questa sezione vengono illustrate le tre problemi elencati in precedenza e fornisce dettagli su come gestirli.

### <a name="conflicting-constraints"></a>Vincoli in conflitto

I vincoli in conflitto vengono contrassegnati in rosso e dispone di un simbolo di avviso. Passare il mouse sui simboli di avviso viene visualizzata nel popover con informazioni sul conflitto:

 [![](designer-auto-layout-images/image11.png "I vincoli in conflitto avviso")](designer-auto-layout-images/image11.png#lightbox)

### <a name="underconstrained-items"></a>Elementi definendo alcuni vincoli

Definendo alcuni vincoli, gli elementi presenti in arancione e attivano l'aspetto di un'icona di colore arancione marcatore nella barra di oggetti controller di visualizzazione:

 [![](designer-auto-layout-images/image02.png "Definendo alcuni vincoli, gli elementi vengono visualizzati in arancione")](designer-auto-layout-images/image02.png#lightbox)

Se fa clic su tale icona marcatore, è possibile ottenere informazioni sugli elementi definendo alcuni vincoli nella scena e risolvere i problemi vincolando entrambi completamente li o rimuovendo i vincoli, come illustrato nello screenshot seguente:

 [![](designer-auto-layout-images/image10.png "Corretto elementi definendo alcuni vincoli")](designer-auto-layout-images/image10.png#lightbox)

### <a name="frame-misplacement"></a>Misplacement frame

Misplacement frame Usa lo stesso codice colore come elementi definendo alcuni vincoli. L'elemento verrà sempre visualizzato nella superficie utilizzando il relativo frame nativo, ma nel caso misplacement un frame un rettangolo rosso contrassegna dove l'elemento finirà quando viene eseguita l'applicazione, come illustrato nello screenshot seguente:

 [![](designer-auto-layout-images/image05.png "Visualizzazione di esempio Misplacement Frame")](designer-auto-layout-images/image05.png#lightbox)

Per risolvere gli errori di frame misplacement, selezionare la **aggiornamento frame in base ai vincoli** pulsante nella barra degli strumenti di vincoli (pulsante a destra):

 [![](designer-auto-layout-images/image03.png "Aggiornamento dei frame basato su pulsante della barra degli strumenti di vincoli")](designer-auto-layout-images/image03.png#lightbox)

Ciò regolerà automaticamente il frame di elemento in modo da corrispondere nelle posizioni definite dai controlli.

<a name="modifying-in-code" />

## <a name="modifying-constraints-in-code"></a>Modifica di vincoli nel codice

In base ai requisiti dell'app, potrebbero esserci casi è necessario per modificare un vincolo nel codice. Per ridimensionare o riposizionare la visualizzazione, ad esempio, un vincolo è collegato, per modificare la priorità del vincolo o disattivare completamente un vincolo.

Per accedere a un vincolo nel codice, è innanzitutto necessario esporla in iOS Designer eseguendo queste operazioni:

1. Creare il vincolo come di consueto (usando uno dei metodi elencati in precedenza).
2. Nel **contorno Document Explorer**, trovare il vincolo desiderato e selezionarlo:

    [![](designer-auto-layout-images/modify01.png "Esplora la struttura documento")](designer-auto-layout-images/modify01.png#lightbox)
3. Quindi assegnare un **nome** per il vincolo nel **Widget** scheda della finestra di **Esplora proprietà**:

    [![](designer-auto-layout-images/modify02.png "La scheda di Widget")](designer-auto-layout-images/modify02.png#lightbox)
4. Salvare le modifiche.

Con le modifiche precedenti posto, è possibile accedere al vincolo nel codice e modificare le relative proprietà. Ad esempio, è possibile usare quanto segue per impostare l'altezza della vista associata a zero:

```csharp
ViewInfoHeight.Constant = 0;
```

Dato l'impostazione seguente per il vincolo in iOS Designer:

[![](designer-auto-layout-images/modify03.png "Modifica di un vincolo in Esplora proprietà")](designer-auto-layout-images/modify03.png#lightbox)

### <a name="the-deferred-layout-pass"></a>Il passaggio di Layout posticipata

Anziché aggiornare immediatamente la visualizzazione associata in risposta alle modifiche di vincolo, il motore di Layout automatico consente di pianificare una _passaggio di Layout Deferred_ nel prossimo futuro. Durante questo passaggio posticipato, non solo è il vincolo della vista specificata aggiornato, i vincoli per ogni visualizzazione della gerarchia vengono ricalcolate e aggiornato in modo da regolare per il nuovo layout.

In qualsiasi momento, è possibile pianificare il proprio passaggio di Layout Deferred chiamando il `SetNeedsLayout` o `SetNeedsUpdateConstraints` metodi della visualizzazione padre. 

Il passaggio di Layout posticipato è costituito da due passaggi univoci per la gerarchia di visualizzazione:

- **Il passaggio di aggiornamento** -In questo passaggio, il motore di Layout automatico attraversa la gerarchia di visualizzazione e richiama il `UpdateViewConstraints` metodo su tutti i controller di visualizzazione e la `UpdateConstraints` metodo su tutte le visualizzazioni.
- **Il passaggio di Layout** : anche in questo caso, il motore di Layout automatico attraversa la gerarchia di visualizzazione, ma questa volta richiama il `ViewWillLayoutSubviews` metodo su tutti i controller di visualizzazione e la `LayoutSubviews` metodo su tutte le visualizzazioni. Il `LayoutSubviews` metodo aggiorna il `Frame` proprietà di ogni visualizzazione secondaria con il rettangolo calcolato dal motore del Layout automatico.

### <a name="animating-constraint-changes"></a>Animazione delle modifiche di vincolo

Oltre a modificare le proprietà di vincolo, è possibile utilizzare Core Animation animare le modifiche apportate ai vincoli di una vista. Ad esempio:

```csharp
UIView.BeginAnimations("OpenInfo");
UIView.SetAnimationDuration(1.0f);
ViewInfoHeight.Constant = 237;
View.LayoutIfNeeded();

//Execute Animation
UIView.CommitAnimations();
```

La chiave qui consiste nel chiamare il `LayoutIfNeeded` metodo della visualizzazione padre all'interno del blocco di animazione. Ciò indica alla visualizzazione per disegnare ogni "frame" del percorso animata o modifica delle dimensioni. Senza questa riga, la vista semplicemente allineava alla versione finale senza animazione.

## <a name="summary"></a>Riepilogo

Questa guida ha presentato iOS automatico (o "adaptive") il concetto di vincoli come matematiche rappresentazioni delle relazioni tra gli elementi nell'area di progettazione e Layout. Descritto come abilitare il Layout automatico nella finestra di progettazione di iOS, lavorare con i **sulla barra degli strumenti di vincoli**e la modifica di vincoli singolarmente nella finestra di progettazione. Successivamente, spiega come risolvere i tre problemi comuni di vincoli. Infine, è stato illustrato come modificare i vincoli nel codice.

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione agli storyboard](~/ios/user-interface/storyboards/index.md)
- [procedura dettagliata di controlli manipolati iOS](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Panoramica della finestra di progettazione Android](~/android/user-interface/android-designer/index.md)
- [Vincoli a livello di codice](~/ios/user-interface/programmatic-layout-constraints.md)
- [Apple - Guida per il Layout automatico](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/AutolayoutPG/Introduction/Introduction.html#/apple_ref/doc/uid/TP40010853-CH13-SW1)
