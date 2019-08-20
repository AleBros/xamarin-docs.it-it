---
title: Uso degli storyboard in Novell. Mac
description: Questo documento descrive come usare gli storyboard in Novell. Mac, esaminando come caricarli dal codice, il ciclo di vita del controller di visualizzazione, la catena di risponditori, gli elementi segue, i controller di finestra, i riconoscitori dei movimenti e altro ancora.
ms.prod: xamarin
ms.assetid: DF4DF7C2-DDD7-4A32-B375-5C5446301EC5
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 06d3127bbe7766a70efc570bd455a8d570548731
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653778"
---
# <a name="working-with-storyboards-in-xamarinmac"></a>Uso degli storyboard in Novell. Mac

Uno storyboard definisce tutta l'interfaccia utente per una determinata app suddivisa in una panoramica funzionale dei controller di visualizzazione. In Interface Builder di Xcode, ognuno di questi controller si trova in una propria scena.

[![](indepth-images/intro01.png "Uno storyboard nella Interface Builder di Xcode")](indepth-images/intro01.png#lightbox)

Lo storyboard è un file di risorse (con le estensioni `.storyboard`di) che viene incluso nel bundle dell'app Novell. Mac quando viene compilato e spedito. Per definire lo storyboard iniziale per l'app, modificare `Info.plist` il file e selezionare l' **interfaccia principale** nella casella a discesa: 

[![](indepth-images/sb01.png "Editor info. plist")](indepth-images/sb01.png#lightbox)

<a name="Loading-from-Code" />

## <a name="loading-from-code"></a>Caricamento dal codice

In alcuni casi è possibile che sia necessario caricare uno Storyboard specifico dal codice e creare manualmente un controller di visualizzazione. Per eseguire questa azione, è possibile usare il codice seguente:

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);
```

`FromName` Carica il file storyboard con il nome specificato che è stato incluso nel bundle dell'app. `InstantiateControllerWithIdentifier` Crea un'istanza del controller di visualizzazione con l'identità specificata. È possibile impostare l'identità nel Interface Builder di Xcode durante la progettazione dell'interfaccia utente:

[![](indepth-images/sb02.png "Impostazione dell'ID storyboard")](indepth-images/sb02.png#lightbox)

Facoltativamente, è possibile usare il `InstantiateInitialController` metodo per caricare il controller di visualizzazione a cui è stato assegnato il controller iniziale in Interface Builder:

[![](indepth-images/sb03.png "Impostazione del controller iniziale")](indepth-images/sb03.png#lightbox)

È contrassegnato dal punto di **ingresso dello storyboard** e dalla freccia chiusa aperta sopra.

<a name="View-Controllers" />

## <a name="view-controllers"></a>Controller di visualizzazione

I controller di visualizzazione definiscono le relazioni tra una determinata visualizzazione di informazioni all'interno di un'app Mac e il modello di dati che fornisce tali informazioni. Ogni scena di primo livello nello storyboard rappresenta un controller di visualizzazione nel codice dell'app Novell. Mac.

<a name="The-View-Controller-Lifecycle" />

### <a name="the-view-controller-lifecycle"></a>Ciclo di vita del controller di visualizzazione

Sono stati aggiunti diversi nuovi metodi alla classe `NSViewController` per supportare gli storyboard in MacOS. In particolare, i metodi seguenti usano per rispondere al ciclo di vita della visualizzazione controllata dal controller di visualizzazione specificato:

- `ViewDidLoad`-Questo metodo viene chiamato quando la vista viene caricata dal file dello storyboard.
- `ViewWillAppear`-Questo metodo viene chiamato immediatamente prima che la visualizzazione venga visualizzata sullo schermo.
- `ViewDidAppear`-Questo metodo viene chiamato direttamente dopo che la visualizzazione è stata visualizzata sullo schermo.
- `ViewWillDisappear`-Questo metodo viene chiamato immediatamente prima della rimozione della visualizzazione dalla schermata.
- `ViewDidDisappear`-Questo metodo viene chiamato direttamente dopo che la visualizzazione è stata rimossa dalla schermata.
- `UpdateViewConstraints`-Questo metodo viene chiamato quando è necessario aggiornare i vincoli che definiscono la posizione e la dimensione del layout automatico.
- `ViewWillLayout`-Questo metodo viene chiamato immediatamente prima che le sottoviste di questa visualizzazione siano disposte sullo schermo.
- `ViewDidLayout`-Questo metodo viene chiamato direttamente dopo la disposizione delle visualizzazioni visualizzate sullo schermo.

<a name="The-Responder-Chain" />

### <a name="the-responder-chain"></a>Catena di risponditori

Inoltre, `NSViewControllers` fa ora parte della _catena_di risponditori della finestra:

[![](indepth-images/vc01.png "Catena di risponditori")](indepth-images/vc01.png#lightbox)

E, di conseguenza, vengono cablati per ricevere e rispondere a eventi quali le selezioni delle voci di menu taglia, copia e incolla. Questo cablaggio del controller di visualizzazione automatico si verifica solo nelle app in esecuzione in macOS Sierra (10,12) e versioni successive.

<a name="Containment" />

### <a name="containment"></a>Contenuto

Negli storyboard, i controller di visualizzazione, come il controller di visualizzazione suddiviso e il controller di visualizzazione a schede, possono ora implementare il contenimento, in modo che possano "contenere" altri controller di visualizzazione secondari:

[![](indepth-images/vc02.png "Esempio di contenimento del controller di visualizzazione")](indepth-images/vc02.png#lightbox)

I controller di visualizzazione figlio contengono metodi e proprietà per associarli di nuovo al controller di visualizzazione padre e per lavorare con la visualizzazione e la rimozione di visualizzazioni dalla schermata.

Tutti i controller di visualizzazione contenitore incorporati in macOS hanno un layout specifico che Apple suggerisce di seguire se si crea un controller di visualizzazione del contenitore personalizzato:

[![](indepth-images/vc03.png "Layout del controller di visualizzazione")](indepth-images/vc03.png#lightbox)

Il controller di visualizzazione raccolta contiene una matrice di elementi della visualizzazione raccolta, ognuno dei quali contiene uno o più controller di visualizzazione che contengono visualizzazioni personalizzate.

<a name="Segues" />

## <a name="segues"></a>Gli elementi segue

Gli elementi segue forniscono le relazioni tra tutte le scene che definiscono l'interfaccia utente dell'app. Se si ha familiarità con l'uso degli storyboard in iOS, si sa che gli elementi segue per iOS definisce in genere le transizioni tra le visualizzazioni a schermo intero. Questo comportamento è diverso da macOS, quando gli elementi segue in genere definiscono "[contenimento](#Containment)", dove una scena è l'elemento figlio di una scena padre.

In macOS la maggior parte delle app tende a raggruppare le visualizzazioni all'interno della stessa finestra usando gli elementi dell'interfaccia utente, ad esempio le visualizzazioni e le schede suddivise. A differenza di iOS, in cui le visualizzazioni devono essere sottoposte a transizione sullo schermo, a causa dello spazio di visualizzazione fisico limitato.

<a name="Presentation-Segues" />

### <a name="presentation-segues"></a>Gli elementi segue presentazione

Date le tendenze di macOS all'indipendenza, esistono situazioni in cui vengono usate le _gli elementi segue di presentazione_ , ad esempio le finestre modali, le visualizzazioni dei fogli e i Popov. macOS fornisce i tipi di segue predefiniti seguenti:

- **Show** : Visualizza la destinazione del segue come finestra non modale. Usare, ad esempio, questo tipo di segue per presentare un'altra istanza di una finestra del documento nell'app.
- **Modale** : presenta la destinazione di segue come finestra modale. Usare, ad esempio, questo tipo di segue per presentare la finestra delle preferenze per l'app.
- **Sheet** : viene visualizzata la destinazione di segue come foglio collegato alla finestra padre. Usare, ad esempio, questo tipo di segue per presentare un foglio di ricerca e sostituzione.
- **Popover** : presenta la destinazione del segue come in una finestra di popopov. Usare, ad esempio, questo tipo di segue per presentare le opzioni quando si fa clic su un elemento dell'interfaccia utente.
- **Personalizzata** : presenta la destinazione del segue usando un tipo di segue personalizzato definito dallo sviluppatore. Per altri dettagli, vedere la sezione [creazione di gli elementi segue personalizzati](#Creating-Custom-Segues) di seguito.

Quando si usa Presentation gli elementi segue, è possibile eseguire `PrepareForSegue` l'override del metodo del controller di visualizzazione padre per la presentazione per inizializzare e variabili e fornire i dati al controller di visualizzazione visualizzato.

<a name="Triggered-Segues" />

### <a name="triggered-segues"></a>Gli elementi segue attivato

I gli elementi segue attivati consentono di specificare il nome gli elementi segue (tramite la relativa proprietà **Identifier** in Interface Builder) e di attivarli da eventi quali l'utente che fa clic su un pulsante o `PerformSegue` chiamando il metodo nel codice:

```csharp
// Display the Scene defined by the given Segue ID
PerformSegue("MyNamedSegue", this);
``` 

L'ID segue viene definito all'interno del Interface Builder di Xcode quando si definisce l'interfaccia utente dell'app:

[![](indepth-images/sg02.png "Immissione di un nome segue")](indepth-images/sg02.png#lightbox)

Nel controller di visualizzazione che funge da origine del segue, è necessario eseguire l'override del `PrepareForSegue` metodo ed eseguire qualsiasi inizializzazione richiesta prima che venga eseguito il segue e venga visualizzato il controller di visualizzazione specificato:

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

Facoltativamente, è possibile eseguire l' `ShouldPerformSegue` override del metodo e controllare se il segue viene effettivamente eseguito tramite C# codice. Per i controller di visualizzazione presentati manualmente, `DismissController` chiamare il metodo per rimuoverli dalla visualizzazione quando non sono più necessari.

<a name="Creating-Custom-Segues" />

### <a name="creating-custom-segues"></a>Creazione di gli elementi segue personalizzati

In alcuni casi è possibile che l'app richieda un tipo di segue non fornito dalla gli elementi segue di compilazione definita in macOS. In tal caso, è possibile creare un segue personalizzato che può essere assegnato nella Interface Builder di Xcode quando si definisce l'interfaccia utente dell'app.

Ad esempio, per creare un nuovo tipo segue che sostituisce il controller di visualizzazione corrente all'interno di una finestra, anziché aprire la scena di destinazione in una nuova finestra, è possibile usare il codice seguente:

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

Ecco alcuni aspetti da considerare:

- Si sta usando l' `Register` attributo per esporre questa classe a Objective-C/MacOS.
- Viene eseguito l'override del `Perform` metodo per eseguire effettivamente l'azione del segue personalizzato.
- Il `ContentViewController` controller della finestra viene sostituito con quello definito dalla destinazione (destinazione) del segue.
- Il controller di visualizzazione originale verrà rimosso per liberare memoria utilizzando il `RemoveFromParentViewController` metodo.

Per usare questo nuovo tipo di segue nel Interface Builder di Xcode, è necessario compilare prima l'app, quindi passare a Xcode e aggiungere una nuova segue tra due scene. Impostare lo **stile** su **Custom** e la **classe segue** su `ReplaceViewSegue` (il nome della classe segue personalizzata):

[![](indepth-images/sg01.png "Impostazione della classe segue")](indepth-images/sg01.png#lightbox)

<a name="Triggered-Segues" />

## <a name="window-controllers"></a>Controller di finestra

I controller di finestra contengono e controllano i diversi tipi di finestra che l'app macOS può creare. Per gli storyboard sono disponibili le funzionalità seguenti:

1. È necessario che forniscano un controller di visualizzazione del contenuto. Si tratta dello stesso controller di visualizzazione del contenuto della finestra figlio.
2. La `Storyboard` proprietà conterrà lo storyboard dal quale è stato caricato il controller della finestra `null` ; in caso contrario, se non è stato caricato da uno storyboard.
3. È possibile chiamare il `DismissController` metodo per chiudere la finestra specificata e rimuoverla dalla visualizzazione.

Come i controller di visualizzazione, i controller `PerformSegue`di `PrepareForSegue` finestra implementano i `ShouldPerformSegue` metodi e e possono essere usati come origine di un'operazione segue.

Il controller di finestra è responsabile delle funzionalità seguenti di un'app macOS:

- Gestiscono una finestra specifica.
- Gestiscono la barra del titolo della finestra e la barra degli strumenti, se disponibile.
- Gestiscono il controller di visualizzazione del contenuto per visualizzare il contenuto della finestra.

<a name="Gesture-Recognizers" />

## <a name="gesture-recognizers"></a>Riconoscitori di movimento

I riconoscitori dei movimenti per macOS sono quasi identici alle loro controparti in iOS e consentono allo sviluppatore di aggiungere facilmente movimenti, ad esempio facendo clic su un pulsante del mouse, per gli elementi nell'interfaccia utente dell'app.

Tuttavia, quando i movimenti in iOS sono determinati dalla progettazione dell'applicazione (ad esempio, toccando lo schermo con due dita), la maggior parte dei movimenti in macOS sono determinati dall'hardware.

Utilizzando i riconoscitori di movimento, è possibile ridurre notevolmente la quantità di codice necessaria per aggiungere interazioni personalizzate a un elemento nell'interfaccia utente. Poiché possono determinare automaticamente tra doppio e singolo clic, fare clic e trascinare gli eventi e così via.

Anziché eseguire l'override dell' `MouseDown` evento nel controller di visualizzazione, è necessario utilizzare un riconoscimento di movimento per gestire l'evento di input utente quando si utilizzano gli storyboard.

In macOS sono disponibili i riconoscitori dei movimenti seguenti:

- `NSClickGestureRecognizer`-Registrare gli eventi del mouse verso il basso e verso l'alto.
- `NSPanGestureRecognizer`-Registra gli eventi di trascinamento e rilascio del pulsante del mouse.
- `NSPressGestureRecognizer`: Registra tenendo premuto il pulsante del mouse per un determinato periodo di tempo.
- `NSMagnificationGestureRecognizer`: Registra un evento di ingrandimento dall'hardware del trackpad.
- `NSRotationGestureRecognizer`: Registra un evento di rotazione dall'hardware del trackpad.

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>Utilizzo di riferimenti a storyboard

Un riferimento a storyboard consente di eseguire una progettazione storyboard complessa e di grandi dimensioni e suddividerla in storyboard più piccoli a cui viene fatto riferimento dall'originale, eliminando così la complessità e rendendo più semplice la progettazione e la gestione degli storyboard singoli risultanti.

Inoltre, un riferimento a storyboard può fornire un _ancoraggio_ a un'altra scena nello stesso storyboard o in una scena specifica in un'altra.

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>Riferimento a uno storyboard esterno

Per aggiungere un riferimento a uno storyboard esterno, procedere come segue:

1. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Aggiungi** > **nuovo file...** Storyboard Mac.  >  >  Immettere un **nome** per il nuovo storyboard e fare clic sul pulsante **nuovo** : 

    [![](indepth-images/ref01.png "Aggiunta di un nuovo storyboard")](indepth-images/ref01.png#lightbox)
2. Nella **Esplora soluzioni**fare doppio clic sul nuovo nome dello storyboard per aprirlo per la modifica nella Interface Builder di Xcode.
3. Progettare il layout dei nuovi scenari dello storyboard come si farebbe normalmente e salvare le modifiche: 

    [![](indepth-images/ref02.png "Progettazione dell'interfaccia")](indepth-images/ref02.png#lightbox)
4. Passare allo storyboard a cui si intende aggiungere il riferimento nel Interface Builder.
5. Trascinare un **riferimento a Storyboard** dalla **libreria di oggetti** nel area di progettazione: 

    [![](indepth-images/ref03.png "Selezione di un riferimento a Storyboard nella libreria")](indepth-images/ref03.png#lightbox)
6. In **attributo Inspector**selezionare il nome dello **storyboard** creato in precedenza: 

    [![](indepth-images/ref04.png "Configurazione del riferimento")](indepth-images/ref04.png#lightbox)
7. CTRL: fare clic su un widget dell'interfaccia utente (ad esempio un pulsante) in una scena esistente e creare un nuovo segue per il **riferimento allo storyboard** appena creato.  Dal menu popup selezionare **Mostra** per completare il segue: 

    [![](indepth-images/ref06.png "Impostazione del tipo segue")](indepth-images/ref06.png#lightbox) 
8. Salvare le modifiche apportate allo storyboard.
9. Tornare a Visual Studio per Mac per sincronizzare le modifiche.

Quando l'app viene eseguita e l'utente fa clic sull'elemento dell'interfaccia utente da cui è stato creato il segue, viene visualizzato il controller della finestra iniziale dello storyboard esterno specificato nel riferimento dello storyboard.

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>Riferimento a una scena specifica in uno storyboard esterno

Per aggiungere un riferimento a una scena specifica uno storyboard esterno (e non il controller della finestra iniziale), eseguire le operazioni seguenti:

1. Nella **Esplora soluzioni**fare doppio clic sullo storyboard esterno per aprirlo per la modifica nella Interface Builder di Xcode.
2. Aggiungere una nuova scena e progettarne il layout normalmente: 

    [![](indepth-images/ref07.png "Progettazione del layout in Xcode")](indepth-images/ref07.png#lightbox)
3. In **Identity Inspector**immettere un **ID storyboard** per il controller di finestra della nuova scena: 

    [![](indepth-images/ref08.png "Impostazione dell'ID storyboard")](indepth-images/ref08.png#lightbox)
4. Aprire lo storyboard a cui si intende aggiungere il riferimento in Interface Builder.
5. Trascinare un **riferimento a Storyboard** dalla **libreria di oggetti** nel area di progettazione: 

    [![](indepth-images/ref03.png "Selezione di un riferimento a Storyboard dalla libreria")](indepth-images/ref03.png#lightbox)
6. Nel **controllo di identità**selezionare il nome dello **storyboard** e l'ID di **riferimento** (ID Storyboard) della scena creata in precedenza: 

    [![](indepth-images/ref09.png "Impostazione dell'ID di riferimento")](indepth-images/ref09.png#lightbox)
7. CTRL: fare clic su un widget dell'interfaccia utente (ad esempio un pulsante) in una scena esistente e creare un nuovo segue per il **riferimento allo storyboard** appena creato. Dal menu popup selezionare **Mostra** per completare il segue: 

    [![](indepth-images/ref06.png "Impostazione del tipo segue")](indepth-images/ref06.png#lightbox) 
8. Salvare le modifiche apportate allo storyboard.
9. Tornare a Visual Studio per Mac per sincronizzare le modifiche.

Quando l'app viene eseguita e l'utente fa clic sull'elemento dell'interfaccia utente da cui è stato creato il segue, viene visualizzata la scena con l' **ID di storyboard** specificato dallo storyboard esterno specificato nel riferimento dello storyboard.

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>Riferimento a una scena specifica nello stesso storyboard

Per aggiungere un riferimento a una scena specifica dello stesso storyboard, procedere come segue:

1. Nella **Esplora soluzioni**fare doppio clic sullo storyboard per aprirlo per la modifica.
2. Aggiungere una nuova scena e progettarne il layout normalmente: 

    [![](indepth-images/ref11.png "Modifica dello storyboard in Xcode")](indepth-images/ref11.png#lightbox)
3. In **Identity Inspector**immettere un **ID storyboard** per il controller di finestra della nuova scena: 

    [![](indepth-images/ref12.png "Impostazione dell'ID storyboard")](indepth-images/ref12.png#lightbox)
4. Trascinare un **riferimento a Storyboard** dalla **casella degli strumenti** nel area di progettazione: 

    [![](indepth-images/ref03.png "Selezione di un riferimento a Storyboard dalla libreria")](indepth-images/ref03.png#lightbox)
5. In **controllo attributi**selezionare **ID di riferimento** (ID Storyboard) della scena creata in precedenza: 

    [![](indepth-images/ref13.png "Impostazione dell'ID di riferimento")](indepth-images/ref13.png#lightbox)
6. CTRL: fare clic su un widget dell'interfaccia utente (ad esempio un pulsante) in una scena esistente e creare un nuovo segue per il **riferimento allo storyboard** appena creato. Dal menu popup selezionare **Mostra** per completare il segue: 

    [![](indepth-images/ref06.png "Selezione del tipo di segue")](indepth-images/ref06.png#lightbox) 
7. Salvare le modifiche apportate allo storyboard.
8. Tornare a Visual Studio per Mac per sincronizzare le modifiche.

Quando l'app viene eseguita e l'utente fa clic sull'elemento dell'interfaccia utente da cui è stato creato il segue, viene visualizzata la scena con l' **ID di storyboard** specificato nello stesso storyboard specificato nel riferimento dello storyboard.

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>Esempio di storyboard complesso

Per un esempio complesso di utilizzo degli storyboard in un'app Novell. Mac, vedere l'app di [esempio SourceWriter](https://docs.microsoft.com/samples/xamarin/mac-samples/sourcewriter). SourceWriter è un editor di codice sorgente semplice che offre supporto per il completamento del codice e informazioni sulla sintassi.

Il codice SourceWriter include tutti i commenti e, dove possibile, sono stati indicati i collegamenti delle tecnologie e dei metodi principali alle informazioni corrispondenti nelle guide di Xamarin.Mac.

## <a name="related-links"></a>Collegamenti correlati

- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Utilizzo di Windows](~/mac/user-interface/window.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduzione a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
