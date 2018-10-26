---
title: Utilizzo degli storyboard in xamarin. Mac
description: Questo documento viene descritto come lavorare con gli storyboard in xamarin. Mac, esaminare le procedure per caricarle dal codice, il ciclo di vita di controller di visualizzazione, la catena di risponditore, gli elementi segue, controller di finestra, i riconoscitori di movimento e altro ancora.
ms.prod: xamarin
ms.assetid: DF4DF7C2-DDD7-4A32-B375-5C5446301EC5
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 469ae19021ae524c302b8a9920aaa64bb4a681f4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117291"
---
# <a name="working-with-storyboards-in-xamarinmac"></a>Utilizzo degli storyboard in xamarin. Mac

Uno storyboard definisce tutti l'interfaccia utente per una determinata app, suddivise in una panoramica delle funzionalità dei controller di visualizzazione. In Interface Builder di Xcode, ognuno di questi controller si trova in un proprio scena.

[![](indepth-images/intro01.png "Uno storyboard di Interface Builder di Xcode")](indepth-images/intro01.png#lightbox)

Lo storyboard è un file di risorse (con le estensioni di `.storyboard`) che vengono inclusi nel bundle dell'app xamarin. Mac quando viene compilato e spedito. Per definire lo Storyboard inizio per l'app, modificarla del `Info.plist` file e selezionare il **interfaccia principale** dalla casella a discesa: 

[![](indepth-images/sb01.png "L'editor Info. plist")](indepth-images/sb01.png#lightbox)

<a name="Loading-from-Code" />

## <a name="loading-from-code"></a>Il caricamento dal codice

Potrebbero esserci casi è necessario per caricare uno Storyboard specifico dal codice e creare manualmente un Controller di visualizzazione. È possibile usare il codice seguente per eseguire questa azione:

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);
```

Il `FromName` carica il file di Storyboard con il nome specificato che è stato incluso nel bundle dell'app. Il `InstantiateControllerWithIdentifier` crea un'istanza del Controller di visualizzazione con l'identità specificata. È consigliabile impostare l'identità in Interface Builder di Xcode durante la progettazione dell'interfaccia utente:

[![](indepth-images/sb02.png "Impostazione dell'ID di Storyboard")](indepth-images/sb02.png#lightbox)

Facoltativamente, è possibile usare il `InstantiateInitialController` per caricare il Controller di visualizzazione che è stato assegnato il Controller iniziale in Interface Builder:

[![](indepth-images/sb03.png "Impostazione del controller di iniziale")](indepth-images/sb03.png#lightbox)

È contrassegnato per la **punto di ingresso dello Storyboard** e freccia open è terminata.

<a name="View-Controllers" />

## <a name="view-controllers"></a>Controller di visualizzazione

I controller di visualizzazione definiscono le relazioni tra una determinata visualizzazione di informazioni all'interno di un'app Mac e il modello di dati che fornisce tali informazioni. Ogni scena di primo livello nello Storyboard rappresenta un Controller di visualizzazione nel codice dell'app xamarin. Mac.

<a name="The-View-Controller-Lifecycle" />

### <a name="the-view-controller-lifecycle"></a>Il ciclo di vita di Controller di visualizzazione

Sono stati aggiunti diversi nuovi metodi per il `NSViewController` classe per supportare gli storyboard in macOS. Sono ancora più importante che usano i metodi seguenti per rispondere al ciclo di vita della visualizzazione che viene controllata dal Controller di visualizzazione specificata:

- `ViewDidLoad` -Questo metodo viene chiamato quando la visualizzazione viene caricata dal file di Storyboard.
- `ViewWillAppear` -Questo metodo viene chiamato subito prima della visualizzazione viene visualizzata sullo schermo.
- `ViewDidAppear` -Questo metodo viene chiamato direttamente dopo la visualizzazione è stata visualizzata sullo schermo.
- `ViewWillDisappear` -Questo metodo viene chiamato subito prima della visualizzazione viene rimossa dallo schermo.
- `ViewDidDisappear` -Questo metodo viene chiamato direttamente dopo la visualizzazione è stato rimosso dallo schermo.
- `UpdateViewConstraints` -Questo metodo viene chiamato quando i vincoli che definiscono una vista auto layout posizione e le dimensioni devono essere aggiornati.
- `ViewWillLayout` -Questo metodo viene chiamato poco prima che le visualizzazioni secondarie di questa visualizzazione sono visualizzate sullo schermo.
- `ViewDidLayout` -Questo metodo viene chiamato direttamente dopo le visualizzazioni secondarie della visualizzazione di visualizzazione sono visualizzate sullo schermo.

<a name="The-Responder-Chain" />

### <a name="the-responder-chain"></a>La catena del risponditore

È inoltre `NSViewControllers` fanno ora parte della finestra _catena risponditore_:

[![](indepth-images/vc01.png "La catena del risponditore")](indepth-images/vc01.png#lightbox)

E di conseguenza sono reti cablate-fino a ricevere e rispondere agli eventi, ad esempio le selezioni delle voci di menu Taglia, copia e Incolla. Questo wire-up del Controller di visualizzazione automatica si verifica solo nelle App in esecuzione in macOS Sierra (10.12) e versioni successive.

<a name="Containment" />

### <a name="containment"></a>Contenuto

Negli storyboard, i controller di visualizzazione (ad esempio il Controller di visualizzazione di divisione e il Controller di visualizzazione della scheda) ora è possibile implementare _contenimento_, in modo che sia possibile "contengono" altri sub i controller di visualizzazione:

[![](indepth-images/vc02.png "Un esempio di contenimento di Controller di visualizzazione")](indepth-images/vc02.png#lightbox)

I controller di visualizzazione figlio contiene i metodi e proprietà per collegarle nuovamente al rispettivo Controller di visualizzazione padre e a lavorare con la visualizzazione e rimozione di viste dalla schermata.

Tutti i controller di visualizzazione contenitore incorporato in macOS hanno un layout specifico che Apple consigliabile seguire se creare i propri controller di visualizzazione contenitore personalizzato:

[![](indepth-images/vc03.png "Il layout del Controller di visualizzazione")](indepth-images/vc03.png#lightbox)

Il Controller di visualizzazione raccolta contiene una matrice di elementi di visualizzazione di raccolta, ognuno dei quali contiene uno o più controller di visualizzazione che contengono le proprie visualizzazioni.

<a name="Segues" />

## <a name="segues"></a>Gli elementi segue

Gli elementi segue forniscono le relazioni tra tutte le scene di cui definiscono l'interfaccia utente dell'app. Se si ha familiarità con l'utilizzo degli storyboard in iOS, si è certi che gli elementi segue per iOS in genere definiscono transizioni tra le viste schermo intero. Questo comportamento è diverso da macOS, quando si definiscono in genere segue "[contenimento](#Containment)", dove una scena rappresenta l'elemento figlio di un elemento padre di scena.

In macOS, la maggior parte delle App tendono a raggruppare le visualizzazioni insieme all'interno della stessa finestra usando gli elementi dell'interfaccia utente, ad esempio schede e viste di divisione. A differenza di iOS, in cui le visualizzazioni dovranno eseguire la transizione accensione e spegnimento dello schermo, visualizzare lo spazio a causa di fisica limitata.

<a name="Presentation-Segues" />

### <a name="presentation-segues"></a>Gli elementi segue presentazione

Dato utenti di macOS verso il contenimento, esistono situazioni in cui _presentazione gli elementi segue_ vengono usati, ad esempio Windows modale, foglio le visualizzazioni e Popovers. i tipi di macOS fornisce segue predefiniti seguenti:

- **Mostra** -Visualizza la destinazione dell'elemento Segue come una finestra non modale. Ad esempio, usare questo tipo di elemento Segue per presentare un'altra istanza di una finestra del documento nell'app.
- **Modale** -presenta la destinazione dell'elemento Segue come una finestra modale. Ad esempio, usare questo tipo di elemento Segue per presentare la finestra Preferenze per l'app.
- **Foglio** -presenta la destinazione dell'elemento Segue come un foglio associata alla finestra padre. Ad esempio, usare questo tipo di elemento segue per presentare un Trova e Sostituisci foglio.
- **Nel popover** -presenta la destinazione dell'elemento Segue come in una finestra nel popover. Ad esempio, usare questo tipo di elemento Segue per presentare opzioni quando viene fatto clic su un elemento dell'interfaccia utente dell'utente.
- **Personalizzato** -presenta la destinazione dell'elemento Segue usando un tipo di elemento Segue personalizzate definite dallo sviluppatore. Vedere le [creazione personalizzata gli elementi segue](#Creating-Custom-Segues) sezione di seguito per altri dettagli.

Quando si usano gli elementi segue presentazione, è possibile eseguire l'override di `PrepareForSegue` metodo del Controller di visualizzazione padre per la presentazione per inizializzare e le variabili e fornire i dati per il Controller di visualizzazione viene presentato.

<a name="Triggered-Segues" />

### <a name="triggered-segues"></a>Attivato gli elementi segue

Segue attivate consentono di specificare segue denominata (tramite loro **Identifier** proprietà in Interface Builder) e che siano attivati dagli eventi, ad esempio l'utente facendo clic su un pulsante o chiamando il `PerformSegue` metodo nel codice:

```csharp
// Display the Scene defined by the given Segue ID
PerformSegue("MyNamedSegue", this);
``` 

L'ID elemento Segue è definito all'interno di Interface Builder di Xcode durante sono il layout dell'interfaccia utente dell'app:

[![](indepth-images/sg02.png "Immettere un nome di elemento Segue")](indepth-images/sg02.png#lightbox)

Nel Controller di visualizzazione che rappresenta l'origine dell'elemento Segue, è necessario eseguire l'override di `PrepareForSegue` metodo ed effettuare qualsiasi inizializzazione necessaria prima che venga eseguito il Segue e il Controller di visualizzazione specificato viene visualizzato:

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

Facoltativamente, è possibile eseguire l'override di `ShouldPerfromSegue` metodo e il controllo o meno l'elemento Segue viene effettivamente eseguita tramite C# codice. Per i controller di visualizzazione manualmente presentata, chiamare il metodo loro `DismissController` metodo rimuoverle da visualizzare quando non sono più necessari.

<a name="Creating-Custom-Segues" />

### <a name="creating-custom-segues"></a>Creazione personalizzata gli elementi segue

Potrebbero esserci volte quando l'app richiede un tipo di elemento Segue non fornito da segue compilazione aggiuntivo definito in macOS. In questo caso, è possibile creare un elemento Segue personalizzati che possono essere assegnati in Interface Builder di Xcode durante il layout dell'interfaccia utente dell'app.

Per creare un nuovo tipo di elemento Segue che sostituisce il Controller di visualizzazione corrente all'interno di una finestra (invece di aprire la destinazione di scena in una nuova finestra), ad esempio, è possibile usare il codice seguente:

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

- Usiamo il `Register` attributo per esporre questa classe per Objective-C/macOS.
- Viene eseguito l'override di `Perform` metodo per eseguire effettivamente l'azione del nostro elemento Segue personalizzato.
- Si sta sostituendo la finestra `ContentViewController` controller con quello definito dalla destinazione (destinazione) di elemento Segue.
- Verranno rimosse il Controller di visualizzazione originale per liberare memoria tramite la `RemoveFromParentViewController` (metodo).

Per usare questo nuovo tipo di elemento Segue in Interface Builder di Xcode, è necessario compilare l'app in primo luogo, quindi passare a Xcode e aggiungere un nuovo elemento Segue tra le due scene. Impostare il **stile** a **personalizzato** e il **classe Segue** a `ReplaceViewSegue` (il nome della nostra classe di elemento Segue personalizzato):

[![](indepth-images/sg01.png "L'impostazione della classe di elemento Segue")](indepth-images/sg01.png#lightbox)

<a name="Triggered-Segues" />

## <a name="window-controllers"></a>Controller di finestra

I controller di finestra contengono e controllano i diversi tipi di finestra in grado di creare l'app macOS. Per gli storyboard, presentano le caratteristiche seguenti:

1. Devono specificare un Controller di visualizzazione del contenuto. Questo sarà il Controller di visualizzazione del contenuto stesso con la finestra figlio.
2. Il `Storyboard` proprietà conterrà lo Storyboard il Controller di finestra caricato da, altrimenti `null` se non è stato caricato da uno Storyboard.
3. È possibile chiamare il `DismissController` metodo per chiudere la finestra specificata e lo rimuove dalla visualizzazione.

Come i controller di visualizzazione, i controller di finestra implementano la `PerformSegue`, `PrepareForSegue` e il `ShouldPerfromSegue` metodi e può essere usato come origine di un'operazione di elemento Segue.

Controller di finestra sono responsabili per le caratteristiche seguenti di un'app macOS:

- Che gestiscono una specifica finestra.
- Barra del titolo della finestra e sulla barra degli strumenti che gestiscono (se disponibile).
- Che gestiscono il Controller di visualizzazione del contenuto per visualizzare il contenuto della finestra.

<a name="Gesture-Recognizers" />

## <a name="gesture-recognizers"></a>Riconoscitori di movimento

I riconoscitori di movimento per macOS sono quasi identici alle rispettive controparti in iOS e consentono agli sviluppatori di aggiungere facilmente i movimenti (ad esempio, facendo clic su un pulsante del mouse) agli elementi nell'interfaccia utente dell'app.

Tuttavia, in cui i movimenti in iOS sono determinati in base alla progettazione dell'app (ad esempio, se si tocca la schermata con due dita), più i movimenti in macOS sono determinati dall'hardware.

Usando i riconoscitori di movimento, è possibile ridurre notevolmente la quantità di codice necessario per aggiungere questo tipo di interazione a un elemento nell'interfaccia utente. Come si può determinare automaticamente tra i clic doppie e singole, fare clic e trascinare gli eventi e così via.

Invece di sostituire il `MouseDown` evento nel Controller di visualizzazione, è consigliabile usare un riconoscitore di movimento per gestire l'evento di input utente quando si lavora con gli storyboard.

I riconoscitori di movimento seguenti sono disponibili in macOS:

- `NSClickGestureRecognizer` -Registrare mouse verso il basso e gli eventi.
- `NSPanGestureRecognizer` -Registri pulsante del mouse premuto, trascinare e rilasciare gli eventi.
- `NSPressGestureRecognizer` -Registri premuto un pulsante del mouse per una determinata quantità di eventi di tempo.
- `NSMagnificationGestureRecognizer` : Registra un evento di ingrandimento dall'hardware trackpad.
- `NSRotationGestureRecognizer` : Registra un evento di rotazione da hardware trackpad.

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>Utilizzo di riferimenti a uno Storyboard

Un riferimento di Storyboard consente di prendere una progettazione dello Storyboard grande e complessa e suddividerla in più piccoli storyboard che ottenere cui viene fatto riferimento dall'originale, pertanto di rimozione rimuovendo la complessità e rendendo risultante singoli storyboard più semplice alla progettazione e Consente di gestire.

Inoltre, fornisce un riferimento di Storyboard un' _ancoraggio_ alla scena un altro all'interno dello stesso Storyboard o una scena specifica in una diversa.

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>Riferimento a uno Storyboard di esterno

Per aggiungere un riferimento a uno Storyboard esterno, eseguire le operazioni seguenti:

1. Nel **Esplora soluzioni**, fare doppio clic sul nome del progetto e selezionare **Add** > **nuovo File...**   >  **Mac** > **Storyboard**. Immettere un **Name** per il nuovo Storyboard e fare clic sui **New** pulsante: 

    [![](indepth-images/ref01.png "Aggiunta di un nuovo Storyboard")](indepth-images/ref01.png#lightbox)
2. Nel **Esplora soluzioni**, fare doppio clic il nuovo nome di uno Storyboard per aprirlo e modificarlo in Interface Builder di Xcode.
2. Progettare il layout delle scene dello Storyboard di nuovo normalmente verrebbe e salvare le modifiche: 

    [![](indepth-images/ref02.png "Progettazione dell'interfaccia")](indepth-images/ref02.png#lightbox)
3. Passare allo Storyboard che si sta per essere aggiunto il riferimento a in Interface Builder.
4. Trascinare un **riferimento di Storyboard** dalle **oggetto libreria** nell'area di progettazione: 

    [![](indepth-images/ref03.png "Selezione di un riferimento di Storyboard nella libreria")](indepth-images/ref03.png#lightbox)
5. Nel **attributo Inspector**, selezionare il nome del **Storyboard** creata in precedenza: 

    [![](indepth-images/ref04.png "Configurazione di riferimento")](indepth-images/ref04.png#lightbox)
6. CTRL + clic su un Widget dell'interfaccia utente (ad esempio un pulsante) in una scena esistente e creare un nuovo elemento Segue per il **Storyboard riferimento** appena creato.  Scegliere dal menu a comparsa **mostrare** per completare l'elemento Segue: 

    [![](indepth-images/ref06.png "Impostare il tipo di elemento Segue")](indepth-images/ref06.png#lightbox) 
8. Salvare le modifiche allo Storyboard.
9. Tornare a Visual Studio per Mac sincronizzare le modifiche.

Verrà visualizzato quando l'app viene eseguita e l'utente fa clic sull'elemento dell'interfaccia utente che è stato creato l'elemento Segue da, il Controller di finestra iniziale dallo Storyboard esterno specificati nel riferimento Storyboard.

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>Riferimento a una scena specifica in uno Storyboard di esterno

Per aggiungere un riferimento a una scena specifico uno Storyboard esterno e non i Controller di finestra iniziale, eseguire le operazioni seguenti:

1. Nel **Esplora soluzioni**, fare doppio clic sullo Storyboard esterno per aprirlo e modificarlo in Interface Builder di Xcode.
2. Aggiungere una nuova scena e progettare il layout come di consueto: 

    [![](indepth-images/ref07.png "Progettare il layout in Xcode")](indepth-images/ref07.png#lightbox)
3. Nel **Identity Inspector**, immettere una **ID Storyboard** per finestra del Controller della scena nuova: 

    [![](indepth-images/ref08.png "Impostazione dell'ID di Storyboard")](indepth-images/ref08.png#lightbox)
3. Aprire lo Storyboard che si sta per essere aggiunto il riferimento a in Interface Builder.
4. Trascinare un **riferimento di Storyboard** dalle **oggetto libreria** nell'area di progettazione: 

    [![](indepth-images/ref03.png "Selezione di un riferimento di Storyboard dalla libreria")](indepth-images/ref03.png#lightbox)
5. Nel **Identity Inspector**, selezionare il nome del **Storyboard** e il **ID riferimento** (ID di Storyboard) della scena creata in precedenza: 

    [![](indepth-images/ref09.png "Impostazione dell'ID di riferimento")](indepth-images/ref09.png#lightbox)
6. CTRL + clic su un Widget dell'interfaccia utente (ad esempio un pulsante) in una scena esistente e creare un nuovo elemento Segue per il **Storyboard riferimento** appena creato. Scegliere dal menu a comparsa **mostrare** per completare l'elemento Segue: 

    [![](indepth-images/ref06.png "Impostare il tipo di elemento Segue")](indepth-images/ref06.png#lightbox) 
8. Salvare le modifiche allo Storyboard.
9. Tornare a Visual Studio per Mac sincronizzare le modifiche.

Quando l'app viene eseguita e l'utente fa clic sull'elemento dell'interfaccia utente che è stato creato l'elemento Segue da, scena con la determinata **ID di Storyboard** dallo Storyboard esterno specificati nel riferimento Storyboard verranno visualizzati.

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>Riferimento a una scena specifica nello stesso Storyboard

Per aggiungere un riferimento a una scena specifica lo stesso Storyboard, eseguire le operazioni seguenti:

1. Nel **Esplora soluzioni**, fare doppio clic sullo Storyboard per aprirlo e modificarlo.
2. Aggiungere una nuova scena e progettare il layout come di consueto: 

    [![](indepth-images/ref11.png "Modifica lo storyboard in Xcode")](indepth-images/ref11.png#lightbox)
3. Nel **Identity Inspector**, immettere una **ID Storyboard** per finestra del Controller della scena nuova: 

    [![](indepth-images/ref12.png "Impostazione dell'ID di Storyboard")](indepth-images/ref12.png#lightbox)
3. Trascinare un **riferimento di Storyboard** dalle **della casella degli strumenti** nell'area di progettazione: 

    [![](indepth-images/ref03.png "Selezione di un riferimento di Storyboard dalla libreria")](indepth-images/ref03.png#lightbox)
5. Nelle **attributo Inspector**, selezionare **ID riferimento** (ID di Storyboard) della scena creata in precedenza: 

    [![](indepth-images/ref13.png "Impostazione dell'ID di riferimento")](indepth-images/ref13.png#lightbox)
6. CTRL + clic su un Widget dell'interfaccia utente (ad esempio un pulsante) in una scena esistente e creare un nuovo elemento Segue per il **Storyboard riferimento** appena creato. Scegliere dal menu a comparsa **mostrare** per completare l'elemento Segue: 

    [![](indepth-images/ref06.png "Selezione del tipo di elemento Segue")](indepth-images/ref06.png#lightbox) 
8. Salvare le modifiche allo Storyboard.
9. Tornare a Visual Studio per Mac sincronizzare le modifiche.

Quando l'app viene eseguita e l'utente fa clic sull'elemento dell'interfaccia utente che è stato creato l'elemento Segue da, scena con la determinata **ID di Storyboard** nello Storyboard stesso specificati nel riferimento Storyboard verranno visualizzati.

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>Esempio di Storyboard complessi

Per un esempio complesso di lavorare con gli storyboard in un'app xamarin. Mac, vedere la [App di esempio SourceWriter](https://developer.xamarin.com/samples/mac/SourceWriter/). SourceWriter è un editor di codice sorgente semplice che offre supporto per il completamento del codice e informazioni sulla sintassi.

Il codice SourceWriter include tutti i commenti e, dove possibile, sono stati indicati i collegamenti delle tecnologie e dei metodi principali alle informazioni corrispondenti nelle guide di Xamarin.Mac.

## <a name="related-links"></a>Collegamenti correlati

- [MacStoryboard (esempio)](https://developer.xamarin.com/samples/mac/MacStoryboard/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Utilizzo di Windows](~/mac/user-interface/window.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduzione a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
