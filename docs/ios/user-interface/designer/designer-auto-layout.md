---
title: Layout automatico con la finestra di progettazione Xamarin iOS
description: Questa guida vengono presentate iOS Layout automatico e il nuovo flusso di lavoro vincoli disponibile nella finestra di progettazione di Xamarin per iOS.
ms.prod: xamarin
ms.assetid: CAC7A715-55BB-45E2-BB6D-2168D36D428F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 2023483f817c365d2cfad6945b281d630317693b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="auto-layout-with-the-xamarin-designer-for-ios"></a>Layout automatico con la finestra di progettazione Xamarin iOS

_Questa guida vengono presentate iOS Layout automatico e il nuovo flusso di lavoro vincoli disponibile nella finestra di progettazione di Xamarin per iOS._

Layout automatico (detto anche "adattivo layout") è un approccio di progettazione reattiva. A differenza di sistema di layout di transizione, in cui ogni elemento si trova a livello di codice per un punto dello schermo, Layout automatico sta *relazioni* -le posizioni degli elementi relazione ad altri elementi nell'area di progettazione. Il fulcro di Layout automatico è l'idea di vincoli o le regole che definiscono la posizione di un elemento o un set di elementi nel contesto di altri elementi sullo schermo. Poiché gli elementi non sono collegati a una particolare posizione sullo schermo, i vincoli consentono di creare un layout adattivo può sembrare adeguato su orientamenti di dispositivo e dimensioni dello schermo diverse.

In questa Guida, si introduce i vincoli e utilizzo nella finestra di progettazione iOS Xamarin. Questa Guida non illustra l'utilizzo dei vincoli a livello di codice. Per informazioni sull'utilizzo di Layout automatico a livello di codice, vedere l'il [documentazione Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html).

## <a name="requirements"></a>Requisiti

La finestra di progettazione di Xamarin per iOS è disponibile in Visual Studio per Mac in Visual Studio 2015 e 2017 in Windows.

Questa guida presuppone una conoscenza dei componenti della finestra di progettazione dal [Introduzione a progettazione iOS](~/ios/user-interface/designer/introduction.md) Guida.

## <a name="introduction-to-constraints"></a>Introduzione ai vincoli

Un vincolo è una rappresentazione matematica della relazione tra due elementi sullo schermo. Che rappresenta un'interfaccia utente la posizione dell'elemento come una relazione matematica consente di risolvere numerosi problemi associati al percorso di un elemento interfaccia utente a livello di codice. Ad esempio, se si dovesse effettuare 20px un pulsante dalla parte inferiore dello schermo in modalità verticale, la posizione del pulsante sarà fuori dello schermo in modalità orizzontale. Per evitare questo problema, è possibile impostare un vincolo che inserisce il bordo inferiore di 20px il pulsante dalla parte inferiore della visualizzazione. La posizione del bordo del pulsante viene quindi calcolata come *button.bottom = view.bottom - 20px*, che comporterebbe la 20px pulsante dalla parte inferiore della visualizzazione in modalità sia verticale e orizzontale. La possibilità di calcolare la selezione in base a una relazione matematica risulta vincoli così utile nella progettazione dell'interfaccia utente.

Quando si imposta un vincolo, creare un `NSLayoutConstraint` oggetto che accetta come argomenti, gli oggetti di essere vincolato e le proprietà, o *attributi*, che verrà applicato il vincolo. Nella finestra di progettazione iOS, gli attributi includono bordi, ad esempio il *sinistro*, *destra*, *top*, e *inferiore* di un elemento. È inoltre incluso gli attributi di dimensione, ad esempio *altezza* e *larghezza*e il centro di percorso, del punto di *centerX* e *centerY*. Ad esempio, quando si aggiunge un vincolo sulla posizione del bordo sinistro del due pulsanti, la finestra di progettazione genera il codice dietro le quinte seguente:

```csharp
View.AddConstraint (NSLayoutConstraint.Create (Button1, NSLayoutAttribute.Left, NSLayoutRelation.Equal, Button2, NSLayoutAttribute.Left, 1, 10));
```

La sezione successiva viene descritto l'utilizzo con vincoli mediante la finestra di progettazione, inclusi l'abilitazione e disabilitazione Layout automatico e la barra degli strumenti vincoli iOS.

## <a name="enable-auto-layout"></a>Abilitare il Layout automatico

La configurazione predefinita della finestra di progettazione iOS è abilitata la modalità di vincolo. Tuttavia, è necessario abilitarla o disabilitarla manualmente, è possibile farlo in due passaggi:

1.  Fare clic su uno spazio vuoto nell'area di progettazione. Questo consente di deselezionare tutti gli elementi e consente di visualizzare le proprietà per il documento di Storyboard.
1.  Selezionare o deselezionare il **utilizzare Autolayout** casella di controllo nel Pannello proprietà:

    ![](designer-auto-layout-images/image01.png "La casella di controllo utilizzare Autolayout nel Pannello proprietà")


Per impostazione predefinita, vincoli non vengono creati o visibile nell'area di. In alternativa, automaticamente vengono dedotti dalle informazioni sui frame in fase di compilazione. Per aggiungere i vincoli, è necessario selezionare un elemento nell'area di progettazione e aggiungere i vincoli. È possibile ottenere questo risultato con il **vincolo sulla barra degli strumenti**.

## <a name="constraints-toolbar"></a>I vincoli sulla barra degli strumenti

 [![](designer-auto-layout-images/toolbarnew.png "I comandi di Menu di scelta")](designer-auto-layout-images/toolbarnew.png#lightbox)

La barra degli strumenti vincoli è stata aggiornata e ora è costituito da due parti principali:

- **Un elemento toggle pulsante della modalità vincoli**: si immesse in precedenza, la modalità di vincoli facendo nuovamente clic su una vista selezionata nell'area di progettazione. È ora necessario utilizzare questo interruttore nella barra di vincoli:

  ![Attiva/Disattiva modalità di vincoli](designer-auto-layout-images/constraints.png)

- **Un pulsante "Aggiorna vincoli":** è importante notare che le modifiche a seconda che siano in modalità di modifica di vincoli.
  - In modalità di modifica vincolo questo pulsante consente di modificare i vincoli corrispondano il frame di elemento.
  - In modalità di modifica Frame questo pulsante consente di regolare il frame di elemento per corrispondere alla posizione che definiscono i vincoli.


## <a name="surface-based-constraint-editing"></a>Vincolo basate sull'area di modifica

Nella sezione precedente, è stato descritto come aggiungere vincoli predefiniti e rimuovere i vincoli utilizzando la barra degli strumenti di vincoli. Per la modifica di più vincoli ottimizzare, è possibile interagire con vincoli direttamente nell'area di progettazione. In questa sezione illustrano i concetti di base di vincolo basate sull'area di modifica, inclusi i controlli pin spaziatura, aree di rilascio e l'utilizzo con diversi tipi di vincoli.

### <a name="creating-constraints"></a>Creazione di vincoli

Lo strumento di progettazione iOS offre due tipi di controlli per la modifica di elementi nell'area di progettazione. *Trascinamento dei controlli* e *controlli pin spaziatura*, come illustrato nella figura seguente:

![controlli di visualizzazione](designer-auto-layout-images/controls.png)

Questi vengono commutati selezionando il pulsante di modalità vincoli nella barra dei vincoli.

Definiscono gli handle a forma di T 4 su ciascun lato dell'elemento di *top*, *destra*, *inferiore*, e *sinistro* bordi dell'elemento per un vincolo. Definire a forma di I quadratini di ridimensionamento a destra e inferiore dell'elemento *altezza* e *larghezza* vincolo rispettivamente. Il quadrato centrale gestisce entrambe *centerX* e *centerY* vincoli.

Per creare un vincolo, scegliere un handle e trascinarlo in un punto qualsiasi nell'area di progettazione. Quando si avvia l'operazione di trascinamento, una serie di linee o caselle verdi verrà visualizzato nell'area di indica ciò che è possibile vincolare. Nella schermata seguente, ad esempio, si sta vincolare il lato superiore del pulsante centrale:

 [![](designer-auto-layout-images/image07.png "Vincolare il lato superiore del pulsante centrale")](designer-auto-layout-images/image07.png#lightbox)

Si noti tre linee tratteggiate verde tra gli altri due pulsanti. Linee verdi indicano *aree di rilascio*, o gli attributi di altri elementi a cui è possibile vincolare. Nella schermata precedente, gli altri due pulsanti offrono 3 aree di rilascio verticale ( *inferiore*, *centerY*, *top*) per vincolare il pulsante. Visualizzazione controller offre un vincolo nella parte superiore della visualizzazione e la casella di colore verde a tinta unita significa che il controller Vista offre un vincolo sulla Guida superiore di sotto, significa che la linea tratteggiata verde nella parte superiore della visualizzazione.

> [!IMPORTANT]
> Le guide sono tipi speciali di destinazioni di vincoli che consentono di creare top e vincoli inferiore che prendono in considerazione la presenza di barre di sistema, ad esempio le barre di stato o le barre degli strumenti. Uno degli usi principali è dispongono di un'app compatibile tra iOS 6 e iOS 7 poiché la versione più recente è la visualizzazione del contenitore estende di sotto la barra di stato. Per ulteriori informazioni sulla Guida di layout superiore, consultare il [documentazione Apple](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/transitionguide/AppearanceCustomization.html#//apple_ref/doc/uid/TP40013174-CH15-SW2).



Tre sezioni introducono utilizzo dei diversi tipi di vincoli.

### <a name="size-constraints"></a>Vincoli relativi alle dimensioni

Con i vincoli di dimensione - *altezza* e *larghezza* -sono disponibili due opzioni. La prima consiste nel trascinare il quadratino per limitare a dimensioni di un elemento adiacente, come illustrato nell'esempio precedente. L'altra opzione è necessario fare doppio clic sull'handle per creare un vincolo di self. Ciò consente di specificare un valore di dimensione costante, come illustrato nella schermata riportata di seguito:

 [![](designer-auto-layout-images/sizec.png "Trascinare il quadratino per limitare a dimensioni di un elemento adiacente, come illustrato di seguito")](designer-auto-layout-images/sizec.png#lightbox)

### <a name="center-constraints"></a>Vincoli Center

L'handle quadrato creerà un *centerX* o *centerY* vincolo, a seconda del contesto. Trascinare il quadratino si illuminerà gli altri elementi di offrire entrambe le aree di rilascio verticale e orizzontale, come illustrato nella schermata riportata di seguito:

 [![](designer-auto-layout-images/centerc.png "Vincoli Center")](designer-auto-layout-images/centerc.png#lightbox)

Se si sceglie un'area di rilascio verticale, un *centerY* vincolo verrà creato. Se si sceglie un'area di rilascio orizzontale, il vincolo verrà basato su *centerX*.

### <a name="combinational-constraints"></a>Vincoli combinational

Per creare sia l'allineamento e vincoli relativi alle dimensioni l'uguaglianza tra due elementi, è possibile selezionare gli elementi da una barra degli strumenti superiore per specificare - nell'ordine: l'allineamento orizzontale, l'allineamento verticale e predicati di uguaglianza di dimensioni, come illustrato nella schermata riportata di seguito:

 [![](designer-auto-layout-images/image06.png "Vincoli combinational")](designer-auto-layout-images/image06.png#lightbox)

### <a name="visualizing-and-editing-constraints"></a>Visualizzazione e la modifica di vincoli

Quando si aggiunge un vincolo, che verrà visualizzato nella finestra di progettazione come una linea blu quando si seleziona un elemento:

 [![](designer-auto-layout-images/image09.png "Visualizzazione dei vincoli")](designer-auto-layout-images/image09.png#lightbox)

È possibile selezionare un vincolo facendo clic su una riga blu e la modifica dei valori di vincolo direttamente nel Pannello proprietà. In alternativa, fare doppio clic su una linea blu verrà visualizzata una popover che consente di modificare i valori direttamente nella finestra di progettazione:

 [![](designer-auto-layout-images/image08.png "La modifica di vincoli")](designer-auto-layout-images/image08.png#lightbox)

## <a name="constraint-issues"></a>Problemi di vincolo

Diversi tipi di problemi possono verificarsi quando si usano vincoli:

-  **I vincoli in conflitto** , ciò si verifica quando più vincoli forzare valori in conflitto per un attributo all'elemento e il motore di vincolo è in grado di risolvere le differenze tra di essi.
-  **Definendo alcuni vincoli elementi** , proprietà di un elemento (posizione + dimensioni) devono essere completamente coperte da un set di vincoli e le dimensioni intrinseche per i vincoli essere valido. Se questi valori sono ambigui, l'elemento è detto essere definendo alcuni vincoli.
-  **Frame misplacement** , questo errore si verifica quando il frame di un elemento e un set di vincoli definiscono due rettangoli risultanti diversi.


In questa sezione vengono illustrati i tre problemi elencati in precedenza e fornisce dettagli su come gestirli.

### <a name="conflicting-constraints"></a>Vincoli in conflitto

I vincoli in conflitto vengono contrassegnati in rosso e un sono di avviso. Passaggio del mouse sui simboli di avviso viene visualizzata una popover con informazioni sul conflitto:

 [![](designer-auto-layout-images/image11.png "I vincoli in conflitto avviso")](designer-auto-layout-images/image11.png#lightbox)

### <a name="underconstrained-items"></a>Elementi definendo alcuni vincoli

Definendo alcuni vincoli, gli elementi vengono visualizzati in arancione e attivano l'aspetto di un'icona marcatore arancione nella barra di oggetto del controller di visualizzazione:

 [![](designer-auto-layout-images/image02.png "Definendo alcuni vincoli, gli elementi vengono visualizzati in arancione")](designer-auto-layout-images/image02.png#lightbox)

Se si fa clic sull'icona tale marcatore, è possibile ottenere informazioni sugli elementi definendo alcuni vincoli nella scena e risolvere i problemi mediante uno completamente vincoli li o rimuovendo i vincoli, come illustrato nella schermata riportata di seguito:

 [![](designer-auto-layout-images/image10.png "Correzione elementi definendo alcuni vincoli")](designer-auto-layout-images/image10.png#lightbox)

### <a name="frame-misplacement"></a>Frame Misplacement

Misplacement frame Usa lo stesso codice colore come elementi definendo alcuni vincoli. L'elemento verrà sempre rappresentato nell'area dell'utilizzando il relativo frame nativo, ma nel caso di un misplacement frame un rettangolo rosso contrassegnerà in cui l'elemento terminerà quando viene eseguita l'applicazione, come illustrato nella schermata riportata di seguito:

 [![](designer-auto-layout-images/image05.png "Visualizzazione di Frame Misplacement di esempio")](designer-auto-layout-images/image05.png#lightbox)

Per risolvere errori di frame misplacement, selezionare il **aggiornamento frame in base ai vincoli** pulsante nella barra degli strumenti vincoli (pulsante a destra):

 [![](designer-auto-layout-images/image03.png "Aggiornare i fotogrammi basati sul pulsante della barra degli strumenti di vincoli")](designer-auto-layout-images/image03.png#lightbox)

Questo regolerà automaticamente il frame di elemento per corrispondenza delle posizioni definite dai controlli.

<a name="modifying-in-code" />

## <a name="modifying-constraints-in-code"></a>Modifica di vincoli nel codice

In base ai requisiti dell'app, potrebbe esserci casi è necessario modificare un vincolo nel codice. Per ridimensionare o modificare la posizione di visualizzazione, ad esempio, un vincolo è collegato a, per modificare la priorità del vincolo o disattivare completamente un vincolo.

Per accedere a un vincolo nel codice, è innanzitutto necessario esporre quest'ultimo nella finestra di progettazione iOS effettuando le seguenti operazioni:

1. Creare il vincolo normalmente (utilizzando uno dei metodi elencati in precedenza).
2. Nel **struttura Document Explorer**, trovare il vincolo desiderato e selezionarla:

    [![](designer-auto-layout-images/modify01.png "Lo struttura Document Explorer")](designer-auto-layout-images/modify01.png#lightbox)
3. Successivamente, assegnare un **nome** per il vincolo nel **Widget** scheda della finestra il **Esplora proprietà**:

    [![](designer-auto-layout-images/modify02.png "La scheda Widget")](designer-auto-layout-images/modify02.png#lightbox)
4. Salvare le modifiche.

Con le suddette modifiche sul posto, è possibile accedere al vincolo nel codice e modificarne le proprietà. Ad esempio, è possibile utilizzare le operazioni seguenti per impostare l'altezza della vista associata a zero:

```csharp
ViewInfoHeight.Constant = 0;
```

Data la seguente impostazione per il vincolo nella finestra di progettazione iOS:

[![](designer-auto-layout-images/modify03.png "Modifica di un vincolo in Esplora risorse di proprietà")](designer-auto-layout-images/modify03.png#lightbox)

### <a name="the-deferred-layout-pass"></a>Il passaggio di Layout posticipata

Anziché aggiornare immediatamente la visualizzazione associata in risposta alle modifiche di vincolo, il motore di Layout automatico pianifica un _passaggio di Layout posticipata_ nel prossimo futuro. Durante questo passaggio posticipato, non solo è il vincolo della vista specificata aggiornato, i vincoli per tutte le viste nella gerarchia vengono ricalcolate e aggiornate per regolare per il nuovo layout.

In qualsiasi momento, è possibile pianificare un passaggio di Layout personalizzati posticipata chiamando il `SetNeedsLayout` o `SetNeedsUpdateConstraints` metodi di visualizzazione padre. 

Il passaggio di Layout posticipata è costituito da due passaggi univoci per la gerarchia della visualizzazione:

- **Il passaggio di aggiornamento** -In questo passaggio, il motore di Layout automatico consente di scorrere la gerarchia della visualizzazione e richiama il `UpdateViewConstraints` metodo su tutti i controller di visualizzazione e `UpdateConstraints` metodo su tutte le visualizzazioni.
- **Il passaggio di Layout** : nuovamente, il motore di Layout automatico consente di scorrere la gerarchia della visualizzazione, ma questa volta richiama il `ViewWillLayoutSubviews` metodo su tutti i controller di visualizzazione e `LayoutSubviews` metodo su tutte le visualizzazioni. Il `LayoutSubviews` metodo aggiorna il `Frame` proprietà di ogni visualizzazione secondaria con il rettangolo calcolato dal motore di Layout automatico.

### <a name="animating-constraint-changes"></a>Animazione di modifica dei vincoli

Oltre a modificare le proprietà di vincolo, è possibile utilizzare l'animazione di Core per animare le modifiche apportate ai vincoli di una vista. Ad esempio:

```csharp
UIView.BeginAnimations("OpenInfo");
UIView.SetAnimationDuration(1.0f);
ViewInfoHeight.Constant = 237;
View.LayoutIfNeeded();

//Execute Animation
UIView.CommitAnimations();
```

La chiave qui consiste nel chiamare il `LayoutIfNeeded` della visualizzazione padre all'interno del blocco di animazione. In questo modo la visualizzazione per disegnare ogni "frame" del percorso animata o modifica delle dimensioni. Senza questa riga, la vista rimarrebbe semplicemente bloccata per la versione finale senza animazione.

## <a name="summary"></a>Riepilogo

Questa Guida introdotto iOS automatica (o "adaptive") il Layout e il concetto di vincoli come rappresentazioni matematiche delle relazioni tra gli elementi nell'area di progettazione. Descritto come abilitare il Layout automatico nella finestra di progettazione iOS, utilizzano il **barra degli strumenti vincoli**e la modifica di vincoli singolarmente nella finestra di progettazione. Successivamente, spiegato come risolvere i tre problemi comuni di vincoli. Infine, è stato spiegato come modificare i vincoli nel codice.

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione agli storyboard](~/ios/user-interface/storyboards/index.md)
- [procedura dettagliata controlli possa iOS](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Panoramica della finestra di progettazione Android](~/android/user-interface/android-designer/index.md)
- [Vincoli a livello di codice](~/ios/user-interface/programmatic-layout-constraints.md)
- [Apple - Guida di Layout automatico](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/AutolayoutPG/Introduction/Introduction.html#/apple_ref/doc/uid/TP40010853-CH13-SW1)
