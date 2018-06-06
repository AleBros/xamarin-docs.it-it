---
title: Utilizzo di storyboard in Xamarin.Mac
description: Questo documento viene descritto come lavorare con gli storyboard in Xamarin.Mac, esaminare le procedure caricare in modo da codice, il ciclo di vita di controller di visualizzazione, la catena di risponditore, segues, controller di finestra, i riconoscitori di movimento e altro ancora.
ms.prod: xamarin
ms.assetid: DF4DF7C2-DDD7-4A32-B375-5C5446301EC5
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 72986ed4247c3b6f66f6f1813d74bf0a95d0de53
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792840"
---
# <a name="working-with-storyboards-in-xamarinmac"></a>Utilizzo di storyboard in Xamarin.Mac

Uno storyboard definisce l'interfaccia utente per un'applicazione specificata suddivisa in una panoramica dei controller di vista funzionale. In Generatore di interfaccia di Xcode, ognuno di questi controller si trova nella propria scena.

[![](indepth-images/intro01.png "Uno storyboard in Generatore del Xcode di interfaccia")](indepth-images/intro01.png#lightbox)

Lo storyboard è un file di risorse (con le estensioni di `.storyboard`) che ottiene incluso nel bundle dell'app Xamarin.Mac quando viene compilato e spedito. Per definire lo Storyboard inizio per l'app, modificarlo del `Info.plist` file e selezionare il **interfaccia principale** dalla casella a discesa: 

[![](indepth-images/sb01.png "L'editor di Info. plist")](indepth-images/sb01.png#lightbox)

<a name="Loading-from-Code" />

## <a name="loading-from-code"></a>Il caricamento dal codice

Talvolta potrebbe essere quando è necessario caricare uno Storyboard specifico dal codice e creare manualmente un Controller di visualizzazione. Per eseguire questa azione, è possibile utilizzare il codice seguente:

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);
```

Il `FromName` carica il file di Storyboard con il nome specificato che è stato incluso nel bundle dell'app. Il `InstantiateControllerWithIdentifier` crea un'istanza del Controller di visualizzazione con l'identità specificata. È consigliabile impostare l'identità in interfaccia generatore del Xcode quando si progetta l'interfaccia utente:

[![](indepth-images/sb02.png "Impostazione dell'ID di Storyboard")](indepth-images/sb02.png#lightbox)

Facoltativamente, è possibile utilizzare il `InstantiateInitialController` per caricare il Controller di visualizzazione che è stato assegnato il Controller iniziale nel generatore di interfaccia:

[![](indepth-images/sb03.png "Impostazione del controller di iniziale")](indepth-images/sb03.png#lightbox)

Perché è contrassegnato come il **punto di ingresso Storyboard** e freccia open è terminata.

<a name="View-Controllers" />

## <a name="view-controllers"></a>Visualizzazione controller

Visualizzazione controller definiscono le relazioni tra una determinata visualizzazione di informazioni all'interno di un'app Mac e il modello di dati che fornisce le informazioni. Ogni scena di primo livello dello storyboard rappresenta un Controller di visualizzazione nel codice dell'app Xamarin.Mac.

<a name="The-View-Controller-Lifecycle" />

### <a name="the-view-controller-lifecycle"></a>Il ciclo di vita di Controller di visualizzazione

Sono stati aggiunti numerosi nuovi metodi per la `NSViewController` classe per supportare gli storyboard in macOS. Sono ancora più importante che utilizzano i metodi seguenti per rispondere al ciclo di vita della vista che viene controllata dal Controller di visualizzazione specificata:

- `ViewDidLoad` -Questo metodo viene chiamato quando la vista viene caricata dal file di Storyboard.
- `ViewWillAppear` -Questo metodo viene chiamato subito prima della visualizzazione viene visualizzata sullo schermo.
- `ViewDidAppear` -Questo metodo viene chiamato direttamente dopo la visualizzazione sullo schermo.
- `ViewWillDisappear` -Questo metodo viene chiamato subito prima della visualizzazione viene rimosso dallo schermo.
- `ViewDidDisappear` -Questo metodo viene chiamato direttamente dopo la visualizzazione è stato rimosso dallo schermo.
- `UpdateViewConstraints` -Questo metodo viene chiamato quando i vincoli che definiscono una vista auto layout posizione e dimensioni è necessario aggiornare.
- `ViewWillLayout` -Questo metodo viene chiamato subito prima sottoviste di questa visualizzazione sono visualizzate sullo schermo.
- `ViewDidLayout` -Questo metodo viene chiamato direttamente dopo sottoviste di visualizzazione sono visualizzate sullo schermo.

<a name="The-Responder-Chain" />

### <a name="the-responder-chain"></a>La catena di risponditore

Inoltre, `NSViewControllers` fanno ora parte della finestra _risponditore catena_:

[![](indepth-images/vc01.png "La catena di risponditore")](indepth-images/vc01.png#lightbox)

E pertanto sono cablate fino a ricevere e rispondere agli eventi, ad esempio Taglia, copia e Incolla voci di menu selezionate. Questo automatico View Controller transito verticale si verifica solo su App in esecuzione su macOS Sierra (10.12) e versioni successive.

<a name="Containment" />

### <a name="containment"></a>Contenuto

In storyboard, visualizzazione controller (ad esempio il Controller di visualizzazione di divisione e la scheda View Controller) ora è possibile implementare _contenimento_, in modo che sia possibile "contengono" altri sub, visualizzazione controller:

[![](indepth-images/vc02.png "Un esempio di contenimento di Controller di visualizzazione")](indepth-images/vc02.png#lightbox)

Controller di visualizzazione figlio contengono i metodi e proprietà collegarli il backup per il Controller di visualizzazione padre e di utilizzare la visualizzazione e rimozione di viste dalla schermata.

Tutti i controller di visualizzazione contenitore incorporata macOS presentano un layout specifico che Apple è consigliabile seguire se creare la propria visualizzazione controller di contenitore personalizzato:

[![](indepth-images/vc03.png "Il layout View Controller")](indepth-images/vc03.png#lightbox)

Il Controller di visualizzazione di raccolta contiene una matrice di visualizzazione di elementi della raccolta, ognuno dei quali contiene uno o più controller di visualizzazione che contengono le visualizzazioni.

<a name="Segues" />

## <a name="segues"></a>Segues

Segues forniscono le relazioni tra tutte le scene di cui definiscono l'interfaccia utente dell'applicazione. Se si ha familiarità con l'utilizzo degli storyboard in iOS, si è certi che Segues per iOS in genere definiscono le transizioni tra le viste schermo intero. Questo comportamento è diverso da macOS, quando si definiscono in genere Segues "[contenimento](#Containment)", dove una scena è figlio di un elemento padre di scena.

In Mac OS, la maggior parte delle applicazioni tendono a raggruppare le visualizzazioni all'interno della stessa finestra usando gli elementi dell'interfaccia utente, ad esempio le visualizzazioni suddivise e schede. A differenza di iOS, è necessario passata e disattivazione dello schermo in cui viste, visualizzare lo spazio a causa del fisico limitato.

<a name="Presentation-Segues" />

### <a name="presentation-segues"></a>Presentazione Segues

Dato tendenze del macOS verso il contenimento, esistono situazioni in cui _presentazione Segues_ vengono utilizzate, ad esempio le finestre modali, visualizzazioni di finestra e questi elementi costituiscono. i tipi macOS consente di definire i predefiniti seguenti:

- **Mostra** -Visualizza la destinazione di Segue come una finestra non modale. Ad esempio, è possibile utilizzare questo tipo di Segue per presentare un'altra istanza di una finestra del documento nell'applicazione.
- **Modale** -Visualizza la destinazione di Segue come una finestra modale. Ad esempio, è possibile utilizzare questo tipo di Segue per visualizzare la finestra Preferenze per l'app.
- **Foglio** -Visualizza la destinazione di Segue come un foglio è collegato alla finestra padre. Ad esempio, usare questo tipo di definire per presentare un Trova e Sostituisci foglio.
- **Popover** -Visualizza la destinazione di Segue come in una finestra popover. Ad esempio, è possibile utilizzare questo tipo Segue per presentare opzioni quando l'utente fa clic su un elemento dell'interfaccia utente.
- **Personalizzato** -Visualizza la destinazione di Segue utilizzando un tipo personalizzato definire definite dallo sviluppatore. Vedere il [creazione personalizzata Segues](#Creating-Custom-Segues) sezione riportata di seguito per ulteriori dettagli.

Quando si utilizza Segues presentazione, è possibile eseguire l'override di `PrepareForSegue` metodo del Controller di visualizzazione padre per la presentazione per inizializzare e variabili e fornire i dati per il Controller di visualizzazione viene presentato.

<a name="Triggered-Segues" />

### <a name="triggered-segues"></a>Attivato Segues

Segues trigger consentono di specificare denominata Segues (tramite i relativi **identificatore** proprietà nell'interfaccia generatore) e attivata dagli eventi, ad esempio l'utente facendo clic su un pulsante o chiamando il `PerformSegue` metodo nel codice:

```csharp
// Display the Scene defined by the given Segue ID
PerformSegue("MyNamedSegue", this);
``` 

L'ID definire è definito all'interno interfaccia generatore di Xcode layout dell'interfaccia utente dell'applicazione quando si:

[![](indepth-images/sg02.png "Immettere un nome di definire")](indepth-images/sg02.png#lightbox)

Nel Controller di visualizzazione che funge da origine della Segue, è necessario eseguire l'override di `PrepareForSegue` (metodo) e di effettuare qualsiasi inizializzazione necessaria prima che venga eseguita la Segue e il Controller di visualizzazione specificato viene visualizzato:

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on Segue ID
    switch (segue.Identifier) {
    case "MyNamedSegue":
        // Prepare for the segue to happen
        ...
        break;
    }
}
```

Facoltativamente, è possibile eseguire l'override di `ShouldPerfromSegue` (metodo) e il controllo o meno la Segue viene effettivamente eseguita tramite codice c#. Per i controller di visualizzazione presentato manualmente, è possibile chiamare i relativi `DismissController` metodo per rimuovere dalla visualizzazione quando non sono più necessari.

<a name="Creating-Custom-Segues" />

### <a name="creating-custom-segues"></a>Creazione personalizzata Segues

Talvolta potrebbe essere quando l'app richiede un tipo di Segue non fornito dai Segues compilazione aggiuntivo definito in macOS. In questo caso, è possibile creare un definire personalizzato che può essere assegnato in Generatore di interfaccia di Xcode durante il layout dell'interfaccia utente dell'applicazione.

Per creare un nuovo tipo Segue che sostituisce il Controller di visualizzazione corrente all'interno di una finestra (anziché la destinazione di scena in una nuova finestra), ad esempio, è possibile utilizzare il codice seguente:

```csharp
using System;
using AppKit;
using Foundation;

namespace OnCardMac
{
    [Register("ReplaceViewSeque")]
    public class ReplaceViewSeque : NSStoryboardSegue
    {
        #region Constructors
        public ReplaceViewSeque() {

        }

        public ReplaceViewSeque (string identifier, NSObject sourceController, NSObject destinationController) : base(identifier,sourceController,destinationController) {

        }

        public ReplaceViewSeque (IntPtr handle) : base(handle) {
        }

        public ReplaceViewSeque (NSObjectFlag x) : base(x) {
        }
        #endregion

        #region Override Methods
        public override void Perform ()
        {
            // Cast the source and destination controllers
            var source = SourceController as NSViewController;
            var destination = DestinationController as NSViewController;

            // Swap the controllers
            source.View.Window.ContentViewController = destination;

            // Release memory
            source.RemoveFromParentViewController ();
        }
        #endregion

    }
        
}
```

Un paio di aspetti da notare:

- Si sta usando il `Register` attributo per esporre questa classe per Objective-C/macOS.
- È stato eseguito l'override di `Perform` metodo per eseguire effettivamente l'azione del nostro Segue personalizzato.
- È in corso la sostituzione della finestra `ContentViewController` controller con quello definito per la destinazione (destinazione) la Segue.
- Il Controller di visualizzazione per liberare memoria tramite originale viene rimossa la `RemoveFromParentViewController` metodo.

Per usare questo nuovo tipo Segue in Generatore del Xcode di interfaccia, è necessario compilare l'app in primo luogo, quindi passare a Xcode e aggiungere un nuovo Segue tra i due scene. Impostare il **stile** a **personalizzato** e **classe definire** a `ReplaceViewSegue` (il nome di questa classe Segue personalizzata):

[![](indepth-images/sg01.png "L'impostazione della classe Segue")](indepth-images/sg01.png#lightbox)

<a name="Triggered-Segues" />

## <a name="window-controllers"></a>Controller di finestra

Finestra controller contenere e controllare i diversi tipi di finestra che consente di creare app macOS. Per gli storyboard presentano le caratteristiche seguenti:

1. È necessario specificare un Controller di visualizzazione del contenuto. Questo sarà lo stesso Controller di visualizzazione del contenuto con il finestra figlio.
2. Il `Storyboard` proprietà conterrà lo Storyboard che il Controller di finestra è stato caricato, in caso contrario `null` se non è stato caricato da uno Storyboard.
3. È possibile chiamare il `DismissController` metodo per chiudere la finestra specificata e rimuoverlo dalla visualizzazione.

Come controller di visualizzazione, finestra controller implementano la `PerformSegue`, `PrepareForSegue` e `ShouldPerfromSegue` metodi e può essere utilizzato come origine di un'operazione Segue.

Finestra Controller sono responsabili per le caratteristiche seguenti di un'app macOS:

- Gestiscono una finestra specifica.
- Barra del titolo della finestra e barra degli strumenti gestiti (se disponibile).
- E gestire il Controller di visualizzazione del contenuto per visualizzare il contenuto della finestra.

<a name="Gesture-Recognizers" />

## <a name="gesture-recognizers"></a>Riconoscitori di movimento

I riconoscitori di movimento per macOS sono quasi identici alle relative controparti in iOS e consentono allo sviluppatore di aggiungere facilmente i movimenti (ad esempio, facendo clic su un pulsante del mouse) agli elementi di interfaccia utente dell'applicazione.

Tuttavia, in cui i movimenti di iOS sono determinati in base alla progettazione dell'applicazione (ad esempio, toccare lo schermo con due dita), più i movimenti di macOS sono determinati dall'hardware.

Tramite i riconoscitori di movimento, è possibile ridurre notevolmente la quantità di codice necessario per aggiungere le interazioni personalizzate a un elemento nell'interfaccia utente. Come è possibile determinare automaticamente tra i clic doppie e singole, fare clic e trascinare gli eventi e così via.

Anziché eseguire l'override di `MouseDown` evento nel Controller di visualizzazione, è consigliabile usare un riconoscimento di movimento di gestire l'evento di input utente quando si lavora con gli storyboard.

I riconoscitori di movimento seguenti sono disponibili in macOS:

- `NSClickGestureRecognizer` -Registrare il mouse verso il basso e gli eventi.
- `NSPanGestureRecognizer` -Registri pulsante del mouse verso il basso, trascinare e rilasciare gli eventi.
- `NSPressGestureRecognizer` -Registri premuto un pulsante del mouse per una determinata quantità di eventi.
- `NSMagnificationGestureRecognizer` -Registra un evento di ingrandimento da trackpad hardware.
- `NSRotationGestureRecognizer` -Registra un evento di rotazione da trackpad hardware.

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>Utilizzo di riferimenti a uno Storyboard

Un riferimento di Storyboard consente di prendere una progettazione di uno Storyboard grande e complessa e suddividerla in più piccoli storyboard che ottenere cui viene fatto riferimento dall'originale, pertanto di rimozione di complessità e rendere risultante singoli rimuovere storyboard più semplice alla progettazione e Gestisci.

Inoltre, è possibile fornire un riferimento di Storyboard un _ancoraggio_ alla scena un'altra all'interno dello stesso Storyboard o una scena specifica in un altro.

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>Riferimento a uno Storyboard esterno

Per aggiungere un riferimento a uno Storyboard esterno, eseguire le operazioni seguenti:

1. Nel **Esplora**del mouse sul nome del progetto e scegliere **Aggiungi** > **nuovo File...**   >  **Mac** > **Storyboard**. Immettere un **nome** per fare clic su Nuovo Storyboard la **New** pulsante: 

    [![](indepth-images/ref01.png "Aggiunta di un nuovo Storyboard")](indepth-images/ref01.png#lightbox)
2. Nel **Esplora**, fare doppio clic sul nuovo nome di uno Storyboard per aprirlo e modificarlo in Generatore del Xcode di interfaccia.
2. Progettare il layout delle scene del nuovo Storyboard normalmente verrebbe e salvare le modifiche: 

    [![](indepth-images/ref02.png "Progettazione dell'interfaccia")](indepth-images/ref02.png#lightbox)
3. Passare allo Storyboard che si sta per essere aggiunto il riferimento nel generatore di interfaccia.
4. Trascinare un **Storyboard riferimento** dal **oggetto libreria** nell'area di progettazione: 

    [![](indepth-images/ref03.png "Selezione di un riferimento di Storyboard nella libreria")](indepth-images/ref03.png#lightbox)
5. Nel **controllo attributo**, selezionare il nome del **Storyboard** appena creato: 

    [![](indepth-images/ref04.png "Configurazione di riferimento")](indepth-images/ref04.png#lightbox)
6. Controllo fare clic su un Widget dell'interfaccia utente (ad esempio un pulsante) in una scena esistente e creare un nuovo Segue per il **riferimento Storyboard** appena creato.  Dal menu di scelta rapida selezionare **Mostra** per completare la Segue: 

    [![](indepth-images/ref06.png "L'impostazione del tipo Segue")](indepth-images/ref06.png#lightbox) 
8. Salvare le modifiche allo Storyboard.
9. Tornare a Visual Studio per Mac sincronizzare le modifiche.

Verrà visualizzato quando si esegue l'app e l'utente fa clic sull'elemento dell'interfaccia utente che la Segue è stato creato da, il Controller finestra iniziale dallo Storyboard esterno specificato nel riferimento Storyboard.

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>Riferimento a una scena specifica in uno Storyboard esterno

Per aggiungere un riferimento a una scena specifica uno Storyboard esterno (e non il Controller finestra iniziale), eseguire le operazioni seguenti:

1. Nel **Esplora**, fare doppio clic sullo Storyboard esterno per aprirlo e modificarlo in Generatore del Xcode di interfaccia.
2. Aggiungere una nuova scena e progettare il layout in modo analogo: 

    [![](indepth-images/ref07.png "Progettare il layout in Xcode")](indepth-images/ref07.png#lightbox)
3. Nel **controllo di identità**, immettere un **ID Storyboard** per finestra Controller della scena nuova: 

    [![](indepth-images/ref08.png "Impostazione dell'ID di Storyboard")](indepth-images/ref08.png#lightbox)
3. Aprire lo Storyboard che si sta per essere aggiunta del riferimento a nel generatore di interfaccia.
4. Trascinare un **Storyboard riferimento** dal **oggetto libreria** nell'area di progettazione: 

    [![](indepth-images/ref03.png "Selezione di un riferimento di Storyboard dalla libreria")](indepth-images/ref03.png#lightbox)
5. Nel **controllo di identità**, selezionare il nome del **Storyboard** e **ID riferimento** (Storyboard ID) della scena appena creato: 

    [![](indepth-images/ref09.png "Impostazione dell'ID di riferimento")](indepth-images/ref09.png#lightbox)
6. Controllo fare clic su un Widget dell'interfaccia utente (ad esempio un pulsante) in una scena esistente e creare un nuovo Segue per il **riferimento Storyboard** appena creato. Dal menu di scelta rapida selezionare **Mostra** per completare la Segue: 

    [![](indepth-images/ref06.png "L'impostazione del tipo Segue")](indepth-images/ref06.png#lightbox) 
8. Salvare le modifiche allo Storyboard.
9. Tornare a Visual Studio per Mac sincronizzare le modifiche.

Quando l'app viene eseguita e l'utente fa clic sull'elemento dell'interfaccia utente che la Segue è stato creato da, la scena con il determinato **ID Storyboard** dallo Storyboard esterno specificato nel riferimento Storyboard verrà visualizzato.

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>Riferimento a una scena specifica nello stesso Storyboard

Per aggiungere un riferimento a una scena specifica lo stesso Storyboard, eseguire le operazioni seguenti:

1. Nel **Esplora**, fare doppio clic sullo Storyboard per aprirlo e modificarlo.
2. Aggiungere una nuova scena e progettare il layout in modo analogo: 

    [![](indepth-images/ref11.png "Modifica lo storyboard in Xcode")](indepth-images/ref11.png#lightbox)
3. Nel **controllo di identità**, immettere un **ID Storyboard** per finestra Controller della scena nuova: 

    [![](indepth-images/ref12.png "Impostazione dell'ID di Storyboard")](indepth-images/ref12.png#lightbox)
3. Trascinare un **Storyboard riferimento** dal **della casella degli strumenti** nell'area di progettazione: 

    [![](indepth-images/ref03.png "Selezione di un riferimento di Storyboard dalla libreria")](indepth-images/ref03.png#lightbox)
5. In **controllo attributo**selezionare **ID riferimento** (Storyboard ID) della scena appena creato: 

    [![](indepth-images/ref13.png "Impostazione dell'ID di riferimento")](indepth-images/ref13.png#lightbox)
6. Controllo fare clic su un Widget dell'interfaccia utente (ad esempio un pulsante) in una scena esistente e creare un nuovo Segue per il **riferimento Storyboard** appena creato. Dal menu di scelta rapida selezionare **Mostra** per completare la Segue: 

    [![](indepth-images/ref06.png "Selezione del tipo Segue")](indepth-images/ref06.png#lightbox) 
8. Salvare le modifiche allo Storyboard.
9. Tornare a Visual Studio per Mac sincronizzare le modifiche.

Quando l'app viene eseguita e l'utente fa clic sull'elemento dell'interfaccia utente che la Segue è stato creato da, la scena con il determinato **ID Storyboard** nello stesso Storyboard specificato nel riferimento Storyboard verrà visualizzato.

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>Esempio di Storyboard complessi

Per un esempio complesso di utilizzo in un'app Xamarin.Mac storyboard, vedere il [App di esempio SourceWriter](https://developer.xamarin.com/samples/mac/SourceWriter/). SourceWriter è un editor di codice sorgente semplice che offre supporto per il completamento del codice e informazioni sulla sintassi.

Il codice SourceWriter include tutti i commenti e, dove possibile, sono stati indicati i collegamenti delle tecnologie e dei metodi principali alle informazioni corrispondenti nelle guide di Xamarin.Mac.

## <a name="related-links"></a>Collegamenti correlati

- [MacStoryboard (esempio)](https://developer.xamarin.com/samples/mac/MacStoryboard/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Utilizzo di Windows](~/mac/user-interface/window.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduzione a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
