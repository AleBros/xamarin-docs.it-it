---
title: Layout automatico con il Xamarin Designer per iOS
description: Questa guida introduce il layout automatico di iOS e descrive come usare la Xamarin Designer per iOS per creare e modificare i layout usando i vincoli. Viene inoltre illustrata la modifica di vincoli nel codice, l'animazione delle modifiche ai vincoli e altro ancora.
ms.prod: xamarin
ms.assetid: CAC7A715-55BB-45E2-BB6D-2168D36D428F
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: f931397f50b6b7aece099efb775a6dda560bf0eb
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279996"
---
# <a name="auto-layout-with-the-xamarin-designer-for-ios"></a>Layout automatico con il Xamarin Designer per iOS

Il layout automatico (detto anche "layout adattivo") è un approccio di progettazione reattivo. A differenza del sistema di layout di transizione, in cui il percorso di ogni elemento è hardcoded a un punto sullo schermo, il layout automatico riguarda le *relazioni* , ovvero le posizioni degli elementi rispetto ad altri elementi nell'area di progettazione. Il fulcro del layout automatico è l'idea di vincoli o regole che definiscono la posizione di un elemento o di un set di elementi nel contesto di altri elementi sullo schermo. Poiché gli elementi non sono collegati a una particolare posizione sullo schermo, i vincoli consentono di creare un layout adattivo che risulti valido in dimensioni dello schermo e orientamenti dei dispositivi diversi.

In questa guida vengono introdotti i vincoli e le modalità di utilizzo in Novell iOS designer. Questa guida non illustra l'uso dei vincoli a livello di codice. Per informazioni sull'uso del layout automatico a livello di codice, vedere la [documentazione di Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html).

## <a name="requirements"></a>Requisiti

Il Xamarin Designer per iOS è disponibile in Visual Studio per Mac in Visual Studio 2017 e versioni successive in Windows.

Questa guida presuppone la conoscenza dei componenti della finestra di progettazione illustrati nella Guida [introduttiva a iOS designer](~/ios/user-interface/designer/introduction.md) .

## <a name="introduction-to-constraints"></a>Introduzione ai vincoli

Un vincolo è una rappresentazione matematica della relazione tra due elementi sullo schermo. La rappresentazione della posizione di un elemento dell'interfaccia utente come relazione matematica risolve diversi problemi associati alla posizione di un elemento dell'interfaccia utente a livello di codice. Ad esempio, se si dovesse inserire un pulsante 20px dalla parte inferiore dello schermo in modalità verticale, la posizione del pulsante sarebbe fuori dallo schermo in modalità orizzontale. Per evitare questo problema, è possibile impostare un vincolo che posiziona il bordo inferiore del pulsante 20px dalla parte inferiore della visualizzazione. La posizione del bordo del pulsante verrebbe quindi calcolata come *Button. Bottom = View. Bottom-20px*, che consente di posizionare il pulsante 20px dalla parte inferiore della visualizzazione in modalità verticale e orizzontale. La possibilità di calcolare la selezione host in base a una relazione matematica è ciò che rende tali vincoli utili nella progettazione dell'interfaccia utente.

Quando si imposta un vincolo, viene creato un `NSLayoutConstraint` oggetto che accetta come argomenti gli oggetti da vincolare e le proprietà, o *gli attributi*, sui quali agirà il vincolo. Nella finestra di progettazione di iOS gli attributi includono bordi quali *Left*, *right*, *Top*e *Bottom* di un elemento. Includono anche attributi di dimensione, ad esempio *altezza* e *larghezza*, e posizione del punto centrale, *CenterX* e *centrato*. Ad esempio, quando si aggiunge un vincolo sulla posizione del limite sinistro di due pulsanti, la finestra di progettazione genera il codice seguente dietro le quinte:

```csharp
View.AddConstraint (NSLayoutConstraint.Create (Button1, NSLayoutAttribute.Left, NSLayoutRelation.Equal, Button2, NSLayoutAttribute.Left, 1, 10));
```

La sezione successiva illustra come usare i vincoli con iOS designer, tra cui l'abilitazione e la disabilitazione del layout automatico e l'uso della barra degli strumenti vincoli.

## <a name="enable-auto-layout"></a>Abilita layout automatico

Per la configurazione predefinita di iOS designer è abilitata la modalità vincolo. Tuttavia, se è necessario abilitarla o disabilitarla manualmente, è possibile eseguire questa operazione in due passaggi:

1. Fare clic su uno spazio vuoto nell'area di progettazione. Questa operazione deseleziona tutti gli elementi e visualizza le proprietà del documento storyboard.
1. Selezionare o deselezionare la casella di controllo **Usa layout** automatico nel pannello Proprietà:

    ![](designer-auto-layout-images/image01.png "Casella di controllo Usa layout automatico nel pannello Proprietà")


Per impostazione predefinita, non viene creato alcun vincolo né visibile sulla superficie. Vengono invece dedotti automaticamente dalle informazioni sul frame in fase di compilazione. Per aggiungere vincoli, è necessario selezionare un elemento nell'area di progettazione e aggiungervi vincoli. Questa operazione può essere eseguita tramite la **barra degli strumenti del vincolo**.

## <a name="constraints-toolbar"></a>Barra degli strumenti vincoli

 [![](designer-auto-layout-images/toolbarnew.png "Comandi del menu di scelta rapida")](designer-auto-layout-images/toolbarnew.png#lightbox)

La barra degli strumenti vincoli è stata aggiornata e ora è costituita da due parti principali:

- **Interruttore del pulsante modalità vincoli**: In precedenza è stata attivata la modalità dei vincoli facendo nuovamente clic su una visualizzazione selezionata nell'area di progettazione. A questo punto è necessario usare questo interruttore nella barra dei vincoli:

  ![Mostra/Nascondi modalità di conformazioni](designer-auto-layout-images/constraints.png)

- **Pulsante "Aggiorna vincoli":** È importante notare che le modifiche variano in base alla modalità di modifica dei vincoli.
  - In modalità di modifica vincoli questo pulsante regola i vincoli in modo che corrispondano al frame dell'elemento.
  - In modalità di modifica frame questo pulsante regola il frame dell'elemento in modo che corrisponda alla posizione di definizione dei vincoli.


## <a name="surface-based-constraint-editing"></a>Modifica di vincoli basati sulla superficie

Nella sezione precedente è stato illustrato come aggiungere vincoli predefiniti e rimuovere i vincoli usando la barra degli strumenti vincoli. Per una modifica più accurata dei vincoli, è possibile interagire con i vincoli direttamente nell'area di progettazione. Questa sezione presenta le nozioni di base sulla modifica di vincoli basati sulla superficie, inclusi i controlli di spaziatura dei pin, le aree di rilascio e l'utilizzo di diversi tipi di vincoli.

### <a name="creating-constraints"></a>Creazione di vincoli

Lo strumento iOS designer offre due tipi di controlli per la modifica degli elementi nell'area di progettazione. *Trascinando* i controlli e i *controlli con spaziatura dei pin*, come illustrato nell'immagine seguente:

![Visualizza controlli](designer-auto-layout-images/controls.png)

Questi elementi vengono disabilitati selezionando il pulsante modalità vincoli nella barra dei vincoli.

I 4 handle a T a ogni lato dell'elemento definiscono i bordi *superiore*, *destro*, *inferiore*e *sinistro* dell'elemento per un vincolo. I due handle a forma di I a destra e alla fine dell'elemento definiscono rispettivamente il vincolo *Height* e *Width* . Il quadrato centrale gestisce sia i vincoli *CenterX* che quelli di *centratura* .

Per creare un vincolo, selezionare un handle e trascinarlo in un punto qualsiasi dell'area di progettazione. Quando si inizia il trascinamento, viene visualizzata una serie di linee/caselle verdi sulla superficie che indica gli elementi che è possibile vincolare. Nella schermata seguente, ad esempio, si limita il lato superiore del pulsante centrale:

 [![](designer-auto-layout-images/image07.png "Limitazione del lato superiore del pulsante centrale")](designer-auto-layout-images/image07.png#lightbox)

Prendere nota delle tre linee verdi tratteggiate tra gli altri due pulsanti. Le linee verdi indicano le *aree di rilascio*o gli attributi di altri elementi a cui è possibile applicare il vincolo. Nello screenshot precedente, gli altri due pulsanti offrono 3 aree di rilascio verticali (in *basso*, *centrato*, *superiore*) per vincolare il pulsante. La linea verde tratteggiata nella parte superiore della visualizzazione indica che il controller di visualizzazione offre un vincolo nella parte superiore della visualizzazione e la casella verde a tinta unita indica che il controller di visualizzazione offre un vincolo sotto la Guida di layout superiore.

> [!IMPORTANT]
> Le guide di layout sono tipi speciali di destinazioni di vincolo che consentono di creare vincoli Top e Bottom che prendono in considerazione la presenza di barre di sistema, ad esempio barre di stato o barre degli strumenti. Uno degli usi principali consiste nel disporre di un'app compatibile tra iOS 6 e iOS 7 Poiché la versione più recente ha la visualizzazione del contenitore che si estende sotto la barra di stato. Per ulteriori informazioni sulla guida di layout superiore, consultare la [documentazione di Apple](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/transitionguide/AppearanceCustomization.html#//apple_ref/doc/uid/TP40013174-CH15-SW2).



Nelle tre sezioni successive viene descritto l'utilizzo di diversi tipi di vincoli.

### <a name="size-constraints"></a>Vincoli di dimensione

Con vincoli di dimensione: *altezza* e *larghezza* : sono disponibili due opzioni. La prima opzione consiste nel trascinare l'handle per vincolare le dimensioni degli elementi adiacenti, come illustrato nell'esempio precedente. L'altra opzione consiste nel fare doppio clic sull'handle per creare un vincolo self-service. In questo modo è possibile specificare un valore di dimensione costante, come illustrato nello screenshot seguente:

 [![](designer-auto-layout-images/sizec.png "Trascinare il quadratino per vincolare le dimensioni degli elementi adiacenti, come illustrato di seguito.")](designer-auto-layout-images/sizec.png#lightbox)

### <a name="center-constraints"></a>Vincoli Center

L'handle quadrato creerà un vincolo *CenterX* o *CenterY* , a seconda del contesto. Trascinando il quadratino di controllo si illumineranno gli altri elementi per offrire aree di rilascio verticali e orizzontali, come illustrato nello screenshot seguente:

 [![](designer-auto-layout-images/centerc.png "Vincoli Center")](designer-auto-layout-images/centerc.png#lightbox)

Se si sceglie un'area di rilascio verticale, verrà creato un vincolo di *centratura* . Se si sceglie un'area di rilascio orizzontale, il vincolo sarà basato su *CenterX*.

### <a name="combinational-constraints"></a>Vincoli combinativi

Per creare vincoli di uguaglianza di allineamento e dimensione tra due elementi, è possibile selezionare gli elementi da una barra degli strumenti superiore per specificare l'allineamento orizzontale, l'allineamento verticale e le dimensioni uguali, come illustrato nello screenshot seguente:

 [![](designer-auto-layout-images/image06.png "Vincoli combinativi")](designer-auto-layout-images/image06.png#lightbox)

### <a name="visualizing-and-editing-constraints"></a>Visualizzazione e modifica di vincoli

Quando si aggiunge un vincolo, questo verrà visualizzato nell'area di progettazione come linea blu quando si seleziona un elemento:

 [![](designer-auto-layout-images/image09.png "Visualizzazione di vincoli")](designer-auto-layout-images/image09.png#lightbox)

È possibile selezionare un vincolo facendo clic su una linea blu e modificando i valori dei vincoli direttamente nel pannello Proprietà. In alternativa, facendo doppio clic su una linea blu, sarà possibile visualizzare un oggetto popopov che consente di modificare i valori direttamente nell'area di progettazione:

 [![](designer-auto-layout-images/image08.png "Modifica di vincoli")](designer-auto-layout-images/image08.png#lightbox)

## <a name="constraint-issues"></a>Problemi relativi ai vincoli

Quando si usano i vincoli possono verificarsi diversi tipi di problemi:

- **Vincoli in conflitto** : si verifica quando più vincoli forzano l'elemento a avere valori in conflitto per un attributo e il motore di vincoli non è in grado di riconciliarli.
- **Elementi sottovincolati** : le proprietà di un elemento (posizione + dimensioni) devono essere interamente coperte dal set di vincoli e dalle dimensioni intrinseche per i vincoli validi. Se questi valori sono ambigui, l'elemento viene definito Sottovincolato.
- **Spostamento del frame** : questo errore si verifica quando il frame di un elemento e il relativo set di vincoli definiscono due rettangoli risultanti diversi.


In questa sezione vengono illustrati i tre problemi elencati sopra e vengono fornite informazioni dettagliate su come gestirli.

### <a name="conflicting-constraints"></a>Vincoli in conflitto

I vincoli in conflitto sono contrassegnati in rosso e presentano un simbolo di avviso. Se si passa il puntatore del mouse sui simboli di avviso, viene visualizzato un messaggio con informazioni sul conflitto:

 [![](designer-auto-layout-images/image11.png "Avviso di vincoli in conflitto")](designer-auto-layout-images/image11.png#lightbox)

### <a name="underconstrained-items"></a>Elementi sottovincolati

Gli elementi sottovincolati vengono visualizzati in arancione e attivano l'aspetto di un'icona del marcatore arancione nella barra degli oggetti del controller di visualizzazione:

 [![](designer-auto-layout-images/image02.png "Gli elementi sottovincolati vengono visualizzati in arancione")](designer-auto-layout-images/image02.png#lightbox)

Se si fa clic sull'icona del marcatore, è possibile ottenere informazioni sugli elementi sottovincolati nella scena e risolvere i problemi limitando il problema o rimuovendo i vincoli, come illustrato nello screenshot seguente:

 [![](designer-auto-layout-images/image10.png "Correzione di elementi sottovincolati")](designer-auto-layout-images/image10.png#lightbox)

### <a name="frame-misplacement"></a>Posizionamento del frame

Il posizionamento del frame usa lo stesso codice di colore degli elementi sottoposti a vincoli. L'elemento verrà sempre sottoposto a rendering sull'area utilizzando il relativo frame nativo, ma nel caso di un blocco di frame un rettangolo rosso contrassegna il punto in cui l'elemento viene eseguito quando viene eseguita l'applicazione, come illustrato nello screenshot seguente:

 [![](designer-auto-layout-images/image05.png "Visualizzazione del posizionamento del frame di esempio")](designer-auto-layout-images/image05.png#lightbox)

Per risolvere gli errori di errore di posizionamento dei frame, selezionare il pulsante **Aggiorna frame in base ai vincoli** dalla barra degli strumenti vincoli (pulsante a destra):

 [![](designer-auto-layout-images/image03.png "Pulsante della barra degli strumenti Aggiorna frame basati su vincoli")](designer-auto-layout-images/image03.png#lightbox)

Questo regola automaticamente il frame dell'elemento in modo che corrisponda alle posizioni definite dai controlli.

<a name="modifying-in-code" />

## <a name="modifying-constraints-in-code"></a>Modifica di vincoli nel codice

In base ai requisiti dell'app, in alcuni casi è possibile che sia necessario modificare un vincolo nel codice. Ad esempio, per ridimensionare o riposizionare la visualizzazione a cui è associato un vincolo, per modificare la priorità di un vincolo o disattivare completamente un vincolo.

Per accedere a un vincolo nel codice, è necessario prima esporlo in iOS designer eseguendo le operazioni seguenti:

1. Creare il vincolo normalmente (usando uno dei metodi elencati in precedenza).
2. In **Esplora struttura documento**individuare il vincolo desiderato e selezionarlo:

    [![](designer-auto-layout-images/modify01.png "Esplora struttura documento")](designer-auto-layout-images/modify01.png#lightbox)
3. Assegnare quindi un **nome** al vincolo nella scheda **widget** di **Esplora proprietà**:

    [![](designer-auto-layout-images/modify02.png "Scheda widget")](designer-auto-layout-images/modify02.png#lightbox)
4. Salvare le modifiche.

Con le modifiche sopra riportate, è possibile accedere al vincolo nel codice e modificarne le proprietà. Ad esempio, è possibile usare il comando seguente per impostare l'altezza della visualizzazione collegata su zero:

```csharp
ViewInfoHeight.Constant = 0;
```

Data la seguente impostazione per il vincolo in iOS designer:

[![](designer-auto-layout-images/modify03.png "Modifica di un vincolo in Esplora proprietà")](designer-auto-layout-images/modify03.png#lightbox)

### <a name="the-deferred-layout-pass"></a>Il passaggio di layout posticipato

Anziché aggiornare immediatamente la visualizzazione collegata in risposta alle modifiche dei vincoli, il motore di layout automatico pianifica un _passaggio di layout posticipato_ per il prossimo futuro. Durante questo passaggio posticipato, non solo il vincolo della visualizzazione specificata viene aggiornato, i vincoli per ogni visualizzazione nella gerarchia vengono ricalcolati e aggiornati per adattarsi al nuovo layout.

In qualsiasi momento, è possibile pianificare il passaggio di layout posticipato chiamando il `SetNeedsLayout` metodo o `SetNeedsUpdateConstraints` della visualizzazione padre. 

Il passaggio di layout posticipato è costituito da due passaggi univoci attraverso la gerarchia di visualizzazione:

- **Il passaggio di aggiornamento** in questo passaggio, il motore di layout automatico attraversa la gerarchia di visualizzazione e richiama il `UpdateViewConstraints` metodo su tutti i controller di visualizzazione `UpdateConstraints` e il metodo su tutte le visualizzazioni.
- **Il layout viene passato** nuovamente, il motore di layout automatico attraversa la gerarchia di visualizzazione, ma questa volta richiama il `ViewWillLayoutSubviews` metodo su tutti i controller di visualizzazione `LayoutSubviews` e il metodo su tutte le visualizzazioni. Il `LayoutSubviews` metodo aggiorna la `Frame` proprietà di ogni Sottovisualizzazione con il rettangolo calcolato dal motore di layout automatico.

### <a name="animating-constraint-changes"></a>Animazione delle modifiche ai vincoli

Oltre a modificare le proprietà dei vincoli, è possibile usare l'animazione di base per aggiungere un'animazione alle modifiche ai vincoli di una vista. Ad esempio:

```csharp
UIView.BeginAnimations("OpenInfo");
UIView.SetAnimationDuration(1.0f);
ViewInfoHeight.Constant = 237;
View.LayoutIfNeeded();

//Execute Animation
UIView.CommitAnimations();
```

La chiave qui chiama il `LayoutIfNeeded` metodo della visualizzazione padre all'interno del blocco Animation. Indica alla visualizzazione di creare ogni "frame" della posizione animata o della modifica delle dimensioni. Senza questa riga, la vista potrebbe semplicemente bloccarsi alla versione finale senza l'animazione.

## <a name="summary"></a>Riepilogo

In questa guida è stato introdotto il layout di iOS auto (o "Adaptive") e il concetto di vincoli come rappresentazioni matematiche delle relazioni tra gli elementi nell'area di progettazione. Viene descritto come abilitare il layout automatico in iOS designer, utilizzare la **barra degli strumenti vincoli**e modificare i vincoli singolarmente nell'area di progettazione. Successivamente, è stato illustrato come risolvere tre problemi comuni relativi ai vincoli. Infine, ha illustrato come modificare i vincoli nel codice.

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione agli storyboard](~/ios/user-interface/storyboards/index.md)
- [Procedura dettagliata per i controlli iOS designable](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Panoramica di Android Designer](~/android/user-interface/android-designer/index.md)
- [Vincoli a livello di codice](~/ios/user-interface/programmatic-layout-constraints.md)
- [Apple-Guida al layout automatico](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/AutolayoutPG/Introduction/Introduction.html#/apple_ref/doc/uid/TP40010853-CH13-SW1)
