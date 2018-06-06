---
title: Menu Xamarin.Mac
description: In questo articolo viene descritto l'utilizzo dei menu in un'applicazione Xamarin.Mac. Descrive la creazione e la gestione di menu e voci di menu in Xcode e interfaccia generatore e utilizzarli a livello di codice.
ms.prod: xamarin
ms.assetid: 5D367F8E-3A76-4995-8A89-488530FAD802
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: cb89d1df60bafe14dcc989666f0eeb5d757e4017
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792921"
---
# <a name="menus-in-xamarinmac"></a>Menu Xamarin.Mac

_In questo articolo viene descritto l'utilizzo dei menu in un'applicazione Xamarin.Mac. Descrive la creazione e la gestione di menu e voci di menu in Xcode e interfaccia generatore e utilizzarli a livello di codice._

Quando si utilizza c# e .NET in un'applicazione Xamarin.Mac, è possibile accedere ai menu Cocoa stesso che uno sviluppatore che lavora in Objective-C e Xcode. Poiché Xamarin.Mac si integra direttamente con Xcode, è possibile utilizzare il generatore di interfaccia di Xcode per creare e gestire le barre dei menu, menu e voci di menu (o, facoltativamente, crearli direttamente nel codice c#).

I menu sono parte integrante dell'esperienza utente dell'applicazione un Mac e in genere vengono visualizzati nelle varie parti dell'interfaccia utente:

- **Barra dei menu dell'applicazione** -si tratta del menu principale che viene visualizzato nella parte superiore dello schermo, per ogni applicazione di Mac.
- **Menu di scelta rapida** -vengono visualizzati quando l'utente fa o fa clic un elemento in una finestra del controllo.
- **La barra di stato** -questa è l'area sul lato destro della barra dei menu dell'applicazione che viene visualizzato nella parte superiore dello schermo (a sinistra dell'orologio della barra dei menu) e a sinistra aumenta man mano che vengono aggiunti elementi.
- **Ancorare menu** -per ogni applicazione di ancoraggio che viene visualizzato il menu quando l'utente fa o controllo clic sull'icona dell'applicazione o quando l'utente effettua l'icona e contiene il pulsante del mouse verso il basso.
- **Pulsante popup e gli elenchi a discesa** -un pulsante popup viene visualizzato un elemento selezionato e visualizza un elenco di opzioni per selezionare da quando l'utente fa clic su. Un elenco a discesa è un tipo di pulsante popup in genere utilizzato per i comandi specifici per il contesto dell'attività corrente. Entrambi possono trovarsi in qualsiasi punto in una finestra.

[![Un menu di esempio](menu-images/intro01.png "un menu di esempio")](menu-images/intro01-large.png#lightbox)

In questo articolo verranno descritte le nozioni di base dell'utilizzo di barre dei menu Cocoa, menu e voci di menu in un'applicazione Xamarin.Mac. È altamente consigliabile che il [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare il [Introduzione a Xcode e interfaccia generatore](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [punti vendita e le azioni](~/mac/get-started/hello-mac.md#Outlets_and_Actions) le sezioni, come illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

È possibile dare un'occhiata il [c# esposizione di classi / metodi per Objective-C](~/mac/internals/how-it-works.md) sezione del [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) del documento, nonché viene descritto il `Register` e `Export` gli attributi utilizzato per le classi c# in transito-fino a oggetti Objective-C e gli elementi dell'interfaccia utente.

## <a name="the-applications-menu-bar"></a>Barra dei menu dell'applicazione 

A differenza di applicazioni in esecuzione nel sistema operativo Windows in cui ogni finestra può avere un proprio barra dei menu collegato, ogni applicazione in esecuzione su macOS dispone di una barra dei menu singolo che esegue nella parte superiore della schermata che viene utilizzata per ogni finestra dell'applicazione:

[![Una barra dei menu](menu-images/appmenu01.png "una barra dei menu")](menu-images/appmenu01-large.png#lightbox)

Gli elementi di questa barra dei menu sono attivati o disattivati in base al contesto corrente o lo stato dell'applicazione e la relativa interfaccia utente in qualsiasi momento. Ad esempio: gli elementi se l'utente seleziona un campo di testo, il **modifica** menu verrà abilitato, ad esempio **copia** e **Taglia**.

In base a Apple e per impostazione predefinita, tutte le applicazioni macOS dispongono di un set standard di menu e voci di menu visualizzati sulla barra dei menu dell'applicazione:

- **Menu Apple** -consente di accedere al sistema wide gli elementi disponibili all'utente in qualsiasi momento, indipendentemente dall'applicazione è in esecuzione. Questi elementi non possono essere modificati dallo sviluppatore.
- **Menu app** -questo menu viene visualizzato il nome dell'applicazione in grassetto e consente all'utente di identificare l'applicazione è attualmente in esecuzione. Contiene elementi che si applicano all'applicazione come un intero e non un determinato documento o un processo, ad esempio la chiusura dell'applicazione.
- **Dal menu file** : gli elementi utilizzati per creare, aprire o salvare i documenti che l'applicazione funziona con. Se l'applicazione non è basata sul documento, questo menu può essere rinominato o rimosso.
- **Menu Modifica** -contiene i comandi, ad esempio **Taglia**, **copia**, e **Incolla** che consentono di modificare o modificare gli elementi di interfaccia utente dell'applicazione.
- **Menu Formato** : se l'applicazione funziona con il testo, questo menu contiene i comandi per modificare la formattazione del testo.
- **Menu Visualizza** -contiene i comandi che influiscono sulla modalità di visualizzazione di contenuto (visualizzati) nell'interfaccia utente dell'applicazione.
- **Menu specifici dell'applicazione** -si tratta di tutti i menu sono specifici dell'applicazione (ad esempio, un menu di segnalibri per un browser web). Appaiono tra il **vista** e **finestra** menu della barra.
- **Menu finestra** -contiene i comandi per l'utilizzo di windows dell'applicazione, nonché un elenco di finestre aperte corrente.
- **Dal menu** -se l'applicazione fornisce le informazioni visualizzate sullo schermo, il menu della Guida in linea deve essere il menu di più a destra della barra. 

Per ulteriori informazioni sull'applicazione barra dei menu e menu standard e le voci di menu, vedere Apple [linee guida dell'interfaccia umana](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/).

### <a name="the-default-application-menu-bar"></a>La barra dei menu di applicazione predefinito

Quando si crea un nuovo progetto Xamarin.Mac, si ottiene automaticamente un standard, applicazione barra dei menu predefinita che contiene gli elementi tipici di un'applicazione macOS disporrebbe (come illustrato nella sezione precedente). Barra dei menu dell'applicazione predefinito viene definita nel **Main** file (insieme al resto dell'interfaccia utente dell'applicazione) nel progetto nel **soluzione riempimento**:  

![Selezionare lo storyboard principale](menu-images/appmenu02.png "selezionare lo storyboard principale")

Fare doppio clic su di **Main** file da aprire per la modifica in Generatore di Xcode interfaccia e verrà visualizzata l'interfaccia dell'editor di menu:

[![Modifica l'interfaccia utente in Xcode](menu-images/defaultbar01.png "modifica l'interfaccia utente in Xcode")](menu-images/defaultbar01-large.png#lightbox)

Da qui è possibile fare clic sugli elementi, ad esempio il **aprire** voce di menu il **File** menu e modificare o modificarne le proprietà nel **attributi controllo**:

[![La modifica degli attributi di un menu](menu-images/defaultbar02.png "la modifica degli attributi di un menu")](menu-images/defaultbar02-large.png#lightbox)

Verranno descritte in aggiunta, modifica ed eliminazione di menu e voci più avanti in questo articolo. Per ora si desidera visualizzare solo i menu e voci di menu sono disponibili per impostazione predefinita e come sono stati automaticamente esposti al codice tramite un set di base predefiniti e le azioni (per ulteriori informazioni, vedere il nostro [punti vendita e le azioni](~/mac/get-started/hello-mac.md#Outlets_and_Actions) documentazione).

Ad esempio, se si fa clic su di **controllo connessione** per il **aprire** voce di menu, possiamo vedere automaticamente cablata fino al `openDocument:` azione: 

[![Visualizzazione dell'azione associata](menu-images/defaultbar03.png "visualizzando l'azione associata")](menu-images/defaultbar03-large.png#lightbox)

Se si seleziona il **risponditore prima** nel **interfaccia gerarchia** e scorrere verso il basso il **controllo connessione**, per visualizzare la definizione del `openDocument:` azione che il **aprire** voce di menu è collegata a (insieme a diverse altre azioni predefinite per l'applicazione e non vengono automaticamente collegate fino a controlli):

[![Visualizzazione delle azioni collegate](menu-images/defaultbar04.png "la visualizzazione di tutte le azioni collegate")](menu-images/defaultbar04-large.png#lightbox) 

È importante? Nella prossima sezione verrà visualizzato il funzionano di queste azioni definito automaticamente con altri elementi dell'interfaccia utente Cocoa per automaticamente abilitare e disabilitare le voci di menu, nonché fornire funzionalità incorporate per gli elementi.

In un secondo momento verrà usato queste azioni predefinite per abilitare e disabilitare gli elementi di codice e fornire la funzionalità quando vengono selezionati.

<a name="Built-In_Menu_Functionality" />

### <a name="built-in-menu-functionality"></a>Funzionalità del menu incorporato

Se l'esecuzione di un'applicazione Xamarin.Mac appena creata prima di aggiungere eventuali elementi dell'interfaccia utente o il codice, si noterà che alcuni elementi vengono automaticamente accesso cablato e abilitate per l'utente (con completamente integrate funzionalità automaticamente), ad esempio il **Quit** elemento il **App** menu:

![Una voce di menu abilitati](menu-images/appmenu03.png "una voce di menu abilitato")

Mentre altre voci di menu, ad esempio **Taglia**, **copia**, e **Incolla** non sono:

![Voci di menu disabilitata](menu-images/appmenu04.png "disabilitato voci di menu")

Consente di arrestare l'applicazione e fare doppio clic su di **Main** file nel **soluzione riempimento** per aprirlo e modificarlo in Xcode del generatore di interfaccia. Successivamente, trascinare un **visualizzazione testo** dal **libreria** nel controller di visualizzazione della finestra il **Editor dell'interfaccia**:

[![Selezione di una visualizzazione di testo dalla libreria](menu-images/appmenu05.png "selezionando una visualizzazione di testo dalla libreria")](menu-images/appmenu05-large.png#lightbox)

Nel **Editor vincoli di** si blocca la visualizzazione del testo al bordo della finestra e impostarlo in cui aumentano e si riducono la finestra facendo clic su tutti i quattro rosso I-i raggi nella parte superiore dell'editor e il **aggiungere vincoli 4** pulsante:

[![Modifica di vincoli](menu-images/appmenu06.png "la modifica di vincoli")](menu-images/appmenu06-large.png#lightbox)

Salvare le modifiche alla progettazione dell'interfaccia utente e tornare alla modalità di Visual Studio per Mac sincronizzare le modifiche al progetto Xamarin.Mac. Ora avviare l'applicazione, digitare il testo nella visualizzazione di testo, selezionarlo e aprire il **modifica** menu:

![Le voci di menu vengono automaticamente abilitato/disabilitato](menu-images/appmenu07.png "le voci di menu vengono automaticamente abilitato/disabilitato")

Si noti come **Taglia**, **copia**, e **Incolla** elementi vengono automaticamente abilitati e completamente funzionanti, tutto senza dover scrivere una singola riga di codice. 

Cosa accade in seguito? Ricordare incorporati predefinire le azioni che precedono cablate un massimo di voci di menu predefinito (come sopra), la maggior parte degli elementi dell'interfaccia utente Cocoa che fanno parte di macOS siano compilati in hook per azioni specifiche (ad esempio `copy:`). Pertanto, quando vengono aggiunti a una finestra, attiva e selezionati, la voce di menu corrispondente o elementi associati a tale azione vengono abilitati automaticamente. Se l'utente seleziona la voce di menu, le funzionalità incorporate in elemento dell'interfaccia utente viene chiamata ed eseguita, tutto senza l'intervento di uno sviluppatore.

### <a name="enabling-and-disabling-menus-and-items"></a>Abilitazione e disabilitazione di menu e voci

Per impostazione predefinita, ogni volta che si verifica un evento utente, `NSMenu` automaticamente Abilita e disabilita ogni visibile e menu di elementi in base al contesto dell'applicazione. Esistono tre modi per abilitare o disabilitare un elemento:

- **L'abilitazione automatica dei menu** -una voce di menu è abilitata se `NSMenu` possibile trovare un oggetto appropriato che risponde all'azione che l'elemento è cablata-fino a. Ad esempio, la visualizzazione del testo precedente che aveva un hook predefinito per il `copy:` azione.
- **Azioni personalizzate e validateMenuItem:** : per qualsiasi voce di menu che viene associata a un [finestra o una vista di azione personalizzata controller](#Working-with-Custom-Window-Actions), è possibile aggiungere il `validateMenuItem:` azione manualmente abilitare o disabilitare le voci di menu.
- **L'abilitazione manuale menu** -impostare manualmente il `Enabled` proprietà di ogni `NSMenuItem` per abilitare o disabilitare individualmente ogni elemento in un menu.

Per scegliere un sistema, impostare il `AutoEnablesItems` proprietà di un `NSMenu`. `true` automatico (comportamento predefinito) e `false` è manuale. 

> [!IMPORTANT]
> Se si sceglie di utilizzare l'attivazione di menu manuale, nessuno dei menu di elementi, anche quelli controllato dalle classi AppKit come `NSTextView`, vengono aggiornati automaticamente. Sarà responsabile per l'abilitazione e disabilitazione di tutti gli elementi nel codice manualmente.

#### <a name="using-validatemenuitem"></a>Utilizzando validateMenuItem

Come descritto in precedenza, per qualsiasi voce di menu a cui è associata un [finestra o l'azione personalizzata Controller visualizzazione](#Working-with-Custom-Window-Actions), è possibile aggiungere il `validateMenuItem:` azione manualmente abilitare o disabilitare le voci di menu.

Nell'esempio seguente, il `Tag` proprietà verrà utilizzata per decidere il tipo di voce di menu che verrà essere abilitato o disabilitato per il `validateMenuItem:` azione in base allo stato del testo selezionato in un `NSTextView`. Il `Tag` proprietà è stata impostata in Generatore di interfaccia per ogni voce di menu:

![Impostare la proprietà Tag](menu-images/validate01.png "impostando la proprietà Tag")

E il seguente codice aggiunto al Controller di visualizzazione:

```csharp
[Action("validateMenuItem:")]
public bool ValidateMenuItem (NSMenuItem item) {

    // Take action based on the menu item type
    // (As specified in its Tag)
    switch (item.Tag) {
    case 1:
        // Wrap menu items should only be available if
        // a range of text is selected
        return (TextEditor.SelectedRange.Length > 0);
    case 2:
        // Quote menu items should only be available if
        // a range is NOT selected.
        return (TextEditor.SelectedRange.Length == 0);
    }

    return true;
}
```

Quando questo codice viene eseguito e non viene selezionato testo nel `NSTextView`, le voci di menu due incapsulamento sono disabilitate (anche se essi vengono collegati alle azioni nel controller di visualizzazione):

![Disattivata la visualizzazione di elementi](menu-images/validate02.png "con elementi disattivati")

Se è selezionata una sezione di testo e riaperto il menu, le voci di menu due incapsulamento saranno disponibili:

![Abilitata la visualizzazione di elementi](menu-images/validate03.png "con articoli abilitati")

## <a name="enabling-and-responding-to-menu-items-in-code"></a>Abilitazione e risponde alle voci di menu nel codice

Come abbiamo visto in precedenza, solo tramite l'aggiunta di elementi dell'interfaccia utente Cocoa specifici per la progettazione dell'interfaccia utente (ad esempio un campo di testo), alcune delle voci di menu predefinito sarà abilitati e funzione automaticamente, senza dover scrivere alcun codice. Avanti diamo un'occhiata aggiungendo il codice c# al progetto Xamarin.Mac per consentire una voce di menu e fornire funzionalità quando l'utente la seleziona.

Ad esempio, consentono ad esempio si desidera che l'utente sia in grado di utilizzare il **aprire** elemento il **File** menu per selezionare una cartella. Poiché si desidera creare una funzione a livello di applicazione e non è limitato a una finestra di concedere o di un elemento dell'interfaccia utente, si intende aggiungere il codice per questo handle per il delegato dell'applicazione.

Nel **soluzione riempimento**, fare doppio clic su di `AppDelegate.CS` file per aprirlo e modificarlo:

![Selezionando il delegato app](menu-images/appmenu08.png "selezionando il delegato di app")

Aggiungere il codice seguente sotto il `DidFinishLaunching` metodo:

```csharp
[Export ("openDocument:")]
void OpenDialog (NSObject sender)
{
    var dlg = NSOpenPanel.OpenPanel;
    dlg.CanChooseFiles = false;
    dlg.CanChooseDirectories = true;

    if (dlg.RunModal () == 1) {
        var alert = new NSAlert () {
            AlertStyle = NSAlertStyle.Informational,
            InformativeText = "At this point we should do something with the folder that the user just selected in the Open File Dialog box...",
            MessageText = "Folder Selected"
        };
        alert.RunModal ();
    }
}
```

Consente di eseguire ora l'applicazione e aprire il **File** menu: 

![Dal menu File](menu-images/appmenu09.png "dal menu File")

Si noti che il **aprire** voce di menu è ora abilitata. Se si seleziona, verrà visualizzata la finestra di dialogo aprirlo:

![Una finestra di dialogo aprirlo](menu-images/appmenu10.png "una finestra di dialogo aprirlo")

Se si sceglie il **aprire** pulsante, verrà visualizzato il messaggio di avviso:

![Un messaggio di dialogo di esempio](menu-images/appmenu11.png "un messaggio di dialogo di esempio")

Della riga di chiave `[Export ("openDocument:")]`, indica `NSMenu` che il nostro **AppDelegate** dispone di un metodo `void OpenDialog (NSObject sender)` che risponde al `openDocument:` azione. Ricordare precedente, il **aprire** voce di menu viene automaticamente reti cablate-fino a questa azione per impostazione predefinita in Generatore di interfaccia:

[![Visualizzazione delle azioni associate](menu-images/defaultbar03.png "visualizzando le azioni collegate")](menu-images/defaultbar03-large.png#lightbox)

Avanti verrà ora esaminata la creazione di un menu, le voci di menu e azioni e rispondere a essi nel codice.

### <a name="working-with-the-open-recent-menu"></a>Utilizzo di menu Apri recente

Per impostazione predefinita, il **File** menu contiene un **Apri recente** elemento che tiene traccia degli ultimi diversi file che l'utente ha aperto con l'app. Se si sta creando un `NSDocument` Xamarin.Mac app, basata su questo menu verrà gestito automaticamente per l'utente. Per qualsiasi altro tipo di app Xamarin.Mac sarà responsabile per la gestione e la risposta per questa voce di menu manualmente.

Per gestire manualmente il **Apri recente** menu, è necessario innanzitutto comunica che un nuovo file è stata aperto o salvato con i seguenti:

```csharp
// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

Anche se l'app non usa `NSDocuments`, è comunque possibile utilizzare il `NSDocumentController` per mantenere il **Apri recente** menu inviando un `NSUrl` con il percorso del file dal `NoteNewRecentDocumentURL` metodo il `SharedDocumentController`.

Successivamente, è necessario eseguire l'override di `OpenFile` metodo del delegato di app per aprire qualsiasi file che l'utente seleziona dal **aprire recenti** menu. Ad esempio:

```csharp
public override bool OpenFile (NSApplication sender, string filename)
{
    // Trap all errors
    try {
        filename = filename.Replace (" ", "%20");
        var url = new NSUrl ("file://"+filename);
        return OpenFile(url);
    } catch {
        return false;
    }
}
```

Restituire `true` se il file può essere aperto, in caso contrario restituisce `false` e verrà visualizzato un messaggio di avviso predefinita per l'utente che il file non può essere aperto.

Perché il nome di file e il percorso restituito dal **Apri recente** menu, possono includere uno spazio, è necessario il carattere di escape in modo corretto prima di creare un `NSUrl` o si verificherà un errore. Facciamo con il codice seguente:

```csharp
filename = filename.Replace (" ", "%20");
```

Infine, verrà creato un `NSUrl` che punta al file e utilizzare un metodo di supporto nell'app delegato per aprire una nuova finestra e caricare il file:

```csharp
var url = new NSUrl ("file://"+filename);
return OpenFile(url);
```

Per mettere insieme tutto, è opportuno esaminare in un esempio di implementazione in un **appdelegate. cs** file:

```csharp
using AppKit;
using Foundation;
using System.IO;
using System;

namespace MacHyperlink
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewWindowNumber { get; set;} = -1;
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }

        public override bool OpenFile (NSApplication sender, string filename)
        {
            // Trap all errors
            try {
                filename = filename.Replace (" ", "%20");
                var url = new NSUrl ("file://"+filename);
                return OpenFile(url);
            } catch {
                return false;
            }
        }
        #endregion

        #region Private Methods
        private bool OpenFile(NSUrl url) {
            var good = false;

            // Trap all errors
            try {
                var path = url.Path;

                // Is the file already open?
                for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
                    var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
                    if (content != null && path == content.FilePath) {
                        // Bring window to front
                        NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
                        return true;
                    }
                }

                // Get new window
                var storyboard = NSStoryboard.FromName ("Main", null);
                var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

                // Display
                controller.ShowWindow(this);

                // Load the text into the window
                var viewController = controller.Window.ContentViewController as ViewController;
                viewController.Text = File.ReadAllText(path);
                viewController.SetLanguageFromPath(path);
                viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
                viewController.View.Window.RepresentedUrl = url;

                // Add document to the Open Recent menu
                NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);

                // Make as successful
                good = true;
            } catch {
                // Mark as bad file on error
                good = false;
            }

            // Return results
            return good;
        }
        #endregion

        #region actions
        [Export ("openDocument:")]
        void OpenDialog (NSObject sender)
        {
            var dlg = NSOpenPanel.OpenPanel;
            dlg.CanChooseFiles = true;
            dlg.CanChooseDirectories = false;

            if (dlg.RunModal () == 1) {
                // Nab the first file
                var url = dlg.Urls [0];

                if (url != null) {
                    // Open the document in a new window
                    OpenFile (url);
                }
            }
        }
        #endregion
    }
}
```

In base ai requisiti dell'app, è possibile evitare all'utente di aprire il file stesso in più di una finestra nello stesso momento. Nella nostra app di esempio, se l'utente sceglie un file che è già aperto (tramite il **aprire recenti** o **aprire...** voci di menu), la finestra che contiene il file viene portata in primo piano.

A tale scopo, è stato utilizzato il codice seguente nel metodo di supporto:

```csharp
var path = url.Path;

// Is the file already open?
for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
    var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
    if (content != null && path == content.FilePath) {
        // Bring window to front
        NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
        return true;
    }
}
```

È stato progettato il `ViewController` classe per contenere il percorso del file nel relativo `Path` proprietà. Successivamente, abbiamo scorrere in ciclo tutte le finestre attualmente aperte nell'app. Se il file è già aperto in una delle finestre, viene portato all'inizio di tutte le altre finestre utilizzando:

```csharp
NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
```

Se viene trovata alcuna corrispondenza, viene aperta una nuova finestra con il file caricato e il file è inserito tra il **Apri recente** menu:

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);

// Load the text into the window
var viewController = controller.Window.ContentViewController as ViewController;
viewController.Text = File.ReadAllText(path);
viewController.SetLanguageFromPath(path);
viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
viewController.View.Window.RepresentedUrl = url;

// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

<a name="Working-with-Custom-Window-Actions" />

### <a name="working-with-custom-window-actions"></a>Utilizzo di azioni di finestra personalizzati

Analogamente alla racconta predefinita **risponditore prima** le azioni che precedono già cablate alle voci di menu standard, è possibile creare nuove azioni personalizzate e li alle voci di menu nel generatore di interfaccia di rete.

In primo luogo, definire un'azione personalizzata in uno dei controller di finestra dell'applicazione. Ad esempio:

```csharp
[Action("defineKeyword:")]
public void defineKeyword (NSObject sender) {
    // Preform some action when the menu is selected
    Console.WriteLine ("Request to define keyword");
}
```

Successivamente, fare doppio clic sul file di storyboard dell'app nel **soluzione riempimento** per aprirlo e modificarlo in Xcode del generatore di interfaccia. Selezionare il **risponditore prima** sotto il **scena applicazione**, quindi passare al **attributi controllo**:

![Il controllo degli attributi](menu-images/action01.png "il controllo degli attributi")

Fare clic su di **+** pulsante nella parte inferiore del **attributi controllo** per aggiungere una nuova azione personalizzata:

![Aggiunta di una nuova azione](menu-images/action02.png "aggiunta una nuova azione")

Assegnargli lo stesso nome dell'azione personalizzata creata sul controller di finestra:

![Modifica il nome dell'azione](menu-images/action03.png "modifica il nome dell'azione")

CTRL + clic e trascinare una voce di menu per il **risponditore prima** sotto il **scena applicazione**. Dall'elenco popup, selezionare la nuova azione appena creato (`defineKeyword:` in questo esempio):

![Collegamento di un'azione](menu-images/action04.png "associare un'azione")

Salvare le modifiche allo storyboard e tornare a Visual Studio per Mac sincronizzare le modifiche. Se si esegue l'app, la voce di menu che si è connessi l'azione personalizzata verrà automaticamente essere abilitato o disabilitato (basato su finestra con l'azione di apertura) e selezionando la voce di menu verrà avviata l'azione:

[![Nuova azione di test](menu-images/action05.png "nuova azione di test")](menu-images/action05-large.png#lightbox)

<a name="Adding,_Editing_and_Deleting_Menus" />

### <a name="adding-editing-and-deleting-menus"></a>Aggiunta, modifica ed eliminazione di menu

Come abbiamo visto nelle sezioni precedenti, un'applicazione Xamarin.Mac viene fornito con un numero preimpostato di menu predefiniti e che specifica i controlli dell'interfaccia utente verranno automaticamente attivata e rispondere alle voci di menu. Sono state inoltre come aggiungere codice all'applicazione che consentirà inoltre e rispondere a questi elementi predefiniti.

In questa sezione verranno esaminati la rimozione delle voci di menu che non è più necessario, la riorganizzazione di menu e l'aggiunta di nuovi menu e voci di menu Azioni.

Fare doppio clic su di **Main** file nel **soluzione riempimento** per aprirlo e modificarlo:

[![Modifica l'interfaccia utente in Xcode](menu-images/maint01.png "modifica l'interfaccia utente in Xcode")](menu-images/maint01-large.png#lightbox)

Per l'applicazione Xamarin.Mac specifico non verrà utilizzato il valore predefinito **vista** menu in modo verrà rimosso. Nel **interfaccia gerarchia** selezionare il **vista** voce di menu che fa parte della barra dei menu principale:

![Selezionando la voce di menu visualizzazione](menu-images/maint02.png "selezionando la voce di menu Visualizza")

Premere CANC o backspace per eliminare il menu. Successivamente, si propone di utilizzare tutti gli elementi nel **formato** menu e si desidera spostare gli elementi che verranno utilizza out scegliendo i sottomenu. Nel **interfaccia gerarchia** selezionare le voci di menu seguenti:

![Evidenziazione di più elementi](menu-images/maint03.png "evidenziazione più elementi")

Trascinare gli elementi padre **Menu** dal sottomenu in cui sono attualmente:

[![Il trascinamento degli elementi di menu al menu del padre](menu-images/maint04.png "trascinando le voci di menu al menu del padre")](menu-images/maint04-large.png#lightbox)

Il menu sarà ora simile:

[![Gli elementi nella nuova posizione](menu-images/maint05.png "gli elementi nella nuova posizione")](menu-images/maint05-large.png#lightbox)

Avanti trascinare il **testo** sottomenu out sotto il **formato** menu e inserirlo nella barra dei menu principale tra il **formato** e **finestra** menu:

[![Il menu di testo](menu-images/maint06.png "menu il testo")](menu-images/maint06-large.png#lightbox)

Passiamo sotto il il **formato** menu ed eliminare il **carattere** voce del sottomenu. Selezionare quindi il **formato** menu e rinominarlo in "Tipo di carattere":

[![Menu carattere](menu-images/maint07.png "menu il tipo di carattere")](menu-images/maint07-large.png#lightbox)

Successivamente, creare un menu personalizzato di frasi predefine automaticamente ottenere aggiunto al testo nella visualizzazione di testo quando vengono selezionati. Nella casella di ricerca in basso sul **controllo libreria** tipo "menu". Ciò renderà più facile trovare e utilizzare con tutti gli elementi dell'interfaccia utente di menu:

![Il controllo della libreria](menu-images/maint08.png "il controllo della libreria")

Ora possibile eseguire le operazioni seguenti per creare i menu:

1. Trascinare un **voce di Menu** dal **controllo libreria** nella barra dei menu tra il **testo** e **finestra** menu: 

    ![Selezione di una nuova voce di menu nella libreria](menu-images/maint10.png "selezionando una nuova voce di menu nella libreria")
2. Rinominare l'elemento "Frasi": 

    [![L'impostazione del nome di menu](menu-images/maint09.png "l'impostazione del nome di menu")](menu-images/maint09-large.png#lightbox)
3. Trascinare quindi un **Menu** dal **controllo libreria**: 

    ![Selezione di un menu dalla libreria](menu-images/maint11.png "selezionando un menu dalla libreria")
4. Eliminare quindi **Menu** sul nuovo **voce di Menu** abbiamo appena creato e modificarne il nome con "Frasi": 

    [![Modifica il nome del menu](menu-images/maint12.png "modifica il nome del menu")](menu-images/maint12-large.png#lightbox)
5. Ora rinominare il valore predefinito di tre **voci di Menu** "Address", "Data" e "Saluto": 

    [![Il menu di frasi](menu-images/maint13.png "dal menu di frasi")](menu-images/maint13-large.png#lightbox)
6. Aggiungere un quarto **voce di Menu** trascinando un **voce di Menu** dal **controllo libreria** e chiamata "Firma": 

    [![Modifica il nome dell'elemento menu](menu-images/maint14.png "modifica il nome voce di menu")](menu-images/maint14-large.png#lightbox)
7. Salvare le modifiche apportate alla barra dei menu.

Ora consente di creare un set di azioni personalizzate in modo che il nostro nuove voci di menu vengono esposti al codice c#. In Xcode si passare il **Assistente** Vista:

[![Creazione di azioni necessarie](menu-images/maint15.png "creazione le azioni necessarie")](menu-images/maint15-large.png#lightbox)

Di seguito, eseguire le operazioni seguenti:

1. Trascinamento del controllo dal **indirizzo** voce di menu per il **appdelegate. H** file.
2. Opzione di **connessione** tipo **azione**: 

    [![Selezionare il tipo di azione](menu-images/maint17.png "selezionando il tipo di azione")](menu-images/maint17-large.png#lightbox)
3. Immettere un **nome** di "phraseAddress" e premere il **Connetti** pulsante per creare la nuova azione: 

    [![Configurazione dell'azione](menu-images/maint18.png "configurazione dell'azione")](menu-images/maint18-large.png#lightbox)
4. Ripetere i passaggi precedenti per il **data**, **saluto**, e **firma** voci di menu: 

    [![Le azioni completate](menu-images/maint19.png "azioni completate")](menu-images/maint19-large.png#lightbox)
5. Salvare le modifiche apportate alla barra dei menu.

Successivamente è necessario creare una presa di corrente per la visualizzazione di testo in modo che è possibile modificare il contenuto dal codice. Selezionare il **ViewController.h** file nel **Assistente Editor** e creare nuovi punti vendita chiamato `documentText`:

[![Creazione di una presa](menu-images/maint20.png "la creazione di una presa di corrente")](menu-images/maint20-large.png#lightbox)

Tornare a Visual Studio per Mac sincronizzare le modifiche da Xcode. Modificare successivamente il **ViewController.cs** file e renderlo simile al seguente:

```csharp
using System;

using AppKit;
using Foundation;

namespace MacMenus
{
    public partial class ViewController : NSViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public override NSObject RepresentedObject {
            get {
                return base.RepresentedObject;
            }
            set {
                base.RepresentedObject = value;
                // Update the view, if already loaded.
            }
        }

        public string Text {
            get { return documentText.Value; }
            set { documentText.Value = value; }
        } 
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any additional setup after loading the view.
        }

        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            App.textEditor = this;
        }

        public override void ViewWillDisappear ()
        {
            base.ViewDidDisappear ();

            App.textEditor = null;
        }
        #endregion
    }
}
```

Espone il testo della visualizzazione testo all'esterno del `ViewController` classe e informa il delegato di app quando la finestra acquisisce o perde lo stato attivo. Modificare ora la **appdelegate. cs** file e renderlo simile al seguente:

```csharp
using AppKit;
using Foundation;
using System;

namespace MacMenus
{
    [Register ("AppDelegate")]
    public partial class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public ViewController textEditor { get; set;} = null;
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
        #endregion

        #region Custom actions
        [Export ("openDocument:")]
        void OpenDialog (NSObject sender)
        {
            var dlg = NSOpenPanel.OpenPanel;
            dlg.CanChooseFiles = false;
            dlg.CanChooseDirectories = true;

            if (dlg.RunModal () == 1) {
                var alert = new NSAlert () {
                    AlertStyle = NSAlertStyle.Informational,
                    InformativeText = "At this point we should do something with the folder that the user just selected in the Open File Dialog box...",
                    MessageText = "Folder Selected"
                };
                alert.RunModal ();
            }
        }

        partial void phrasesAddress (Foundation.NSObject sender) {

            textEditor.Text += "Xamarin HQ\n394 Pacific Ave, 4th Floor\nSan Francisco CA 94111\n\n";
        }

        partial void phrasesDate (Foundation.NSObject sender) {

            textEditor.Text += DateTime.Now.ToString("D");
        }

        partial void phrasesGreeting (Foundation.NSObject sender) {

            textEditor.Text += "Dear Sirs,\n\n";
        }

        partial void phrasesSignature (Foundation.NSObject sender) {

            textEditor.Text += "Sincerely,\n\nKevin Mullins\nXamarin,Inc.\n";
        }
        #endregion
    }
}
```

Di seguito sono stati apportati i `AppDelegate` un parziale classe in modo da poter utilizzare le azioni e i punti vendita che è stato definito in Generatore di interfaccia. Inoltre, è necessario esporre un `textEditor` per tenere traccia di quale finestra è attualmente attivo.

I metodi seguenti vengono utilizzati per gestire i menu e voci di menu personalizzati:

```csharp
partial void phrasesAddress (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Xamarin HQ\n394 Pacific Ave, 4th Floor\nSan Francisco CA 94111\n\n";
}

partial void phrasesDate (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += DateTime.Now.ToString("D");
}

partial void phrasesGreeting (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Dear Sirs,\n\n";
}

partial void phrasesSignature (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Sincerely,\n\nKevin Mullins\nXamarin,Inc.\n";
}
```

Se si esegue l'applicazione, tutti gli elementi nel **frase** menu sarà attivo e verrà aggiunta la frase consentono la visualizzazione del testo quando selezionata:

![Un esempio di app in esecuzione](menu-images/maint21.png "un esempio di app in esecuzione")

Ora che abbiamo le nozioni di base dell'utilizzo di una barra dei menu applicazione verso il basso, si esaminerà la creazione di un menu di scelta rapida personalizzato.

### <a name="creating-menus-from-code"></a>Creazione di menu dal codice

Oltre a creare i menu e voci di menu con generatore del Xcode di interfaccia, potrebbe essere volte quando un'app Xamarin.Mac deve creare, modificare o rimuovere un menu, sottomenu o voce di menu dal codice.

Nell'esempio seguente viene creata una classe per contenere le informazioni sulle voci di menu e sottomenu che verrà creato in modo dinamico in tempo reale:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace AppKit.TextKit.Formatter
{
    public class LanguageFormatCommand : NSObject
    {
        #region Computed Properties
        public string Title { get; set; } = "";
        public string Prefix { get; set; } = "";
        public string Postfix { get; set; } = "";
        public List<LanguageFormatCommand> SubCommands { get; set; } = new List<LanguageFormatCommand>();
        #endregion

        #region Constructors
        public LanguageFormatCommand () {

        }

        public LanguageFormatCommand (string title)
        {
            // Initialize
            this.Title = title;
        }

        public LanguageFormatCommand (string title, string prefix)
        {
            // Initialize
            this.Title = title;
            this.Prefix = prefix;
        }

        public LanguageFormatCommand (string title, string prefix, string postfix)
        {
            // Initialize
            this.Title = title;
            this.Prefix = prefix;
            this.Postfix = postfix;
        }
        #endregion
    }
}
```

#### <a name="adding-menus-and-items"></a>Aggiunta di menu ed elementi

Con questa classe è definita, la routine seguente analizza una raccolta di `LanguageFormatCommand`in modo ricorsivo e oggetti compilare nuovi menu e voci di menu aggiungendole alla fine del menu esistente, creato nel generatore di interfaccia, che è stato passato:

```csharp
private void AssembleMenu(NSMenu menu, List<LanguageFormatCommand> commands) {
    NSMenuItem menuItem;

    // Add any formatting commands to the Formatting menu
    foreach (LanguageFormatCommand command in commands) {
        // Add separator or item?
        if (command.Title == "") {
            menuItem = NSMenuItem.SeparatorItem;
        } else {
            menuItem = new NSMenuItem (command.Title);

            // Submenu?
            if (command.SubCommands.Count > 0) {
                // Yes, populate submenu
                menuItem.Submenu = new NSMenu (command.Title);
                AssembleMenu (menuItem.Submenu, command.SubCommands);
            } else {
                // No, add normal menu item
                menuItem.Activated += (sender, e) => {
                    // Apply the command on the selected text
                    TextEditor.PerformFormattingCommand (command);
                };
            }
        }
        menu.AddItem (menuItem);
    }
}
``` 

Per qualsiasi `LanguageFormatCommand` oggetto che ha un valore vuoto `Title` questa routine di proprietà, crea un **separatore di menu** (una linea grigia thin) tra le sezioni di menu:

```csharp
menuItem = NSMenuItem.SeparatorItem;
```

Se viene fornito un titolo, viene creata una nuova voce di menu con tale titolo:

```csharp
menuItem = new NSMenuItem (command.Title);
``` 

Se il `LanguageFormatCommand` oggetto contiene figlio `LanguageFormatCommand` oggetti, viene creato un sottomenu e `AssembleMenu` metodo è chiamato per creare menu di in modo ricorsivo:

```csharp
menuItem.Submenu = new NSMenu (command.Title);
AssembleMenu (menuItem.Submenu, command.SubCommands);
```

Per qualsiasi nuova voce di menu che non dispone di sottomenu, verrà aggiunto codice per gestire la voce di menu viene selezionata dall'utente:

```csharp
menuItem.Activated += (sender, e) => {
    // Do something when the menu item is selected
    ...
};
```

#### <a name="testing-the-menu-creation"></a>La creazione di menu test

Con tutto il codice sopra riportato sul posto, se la raccolta seguente di `LanguageFormatCommand` gli oggetti sono stati creati:

```csharp
// Define formatting commands
FormattingCommands.Add(new LanguageFormatCommand("Stong","**","**"));
FormattingCommands.Add(new LanguageFormatCommand("Emphasize","_","_"));
FormattingCommands.Add(new LanguageFormatCommand("Inline Code","`","`"));
FormattingCommands.Add(new LanguageFormatCommand("Code Block","```\n","\n```"));
FormattingCommands.Add(new LanguageFormatCommand("Comment","<!--","-->"));
FormattingCommands.Add (new LanguageFormatCommand ());
FormattingCommands.Add(new LanguageFormatCommand("Unordered List","* "));
FormattingCommands.Add(new LanguageFormatCommand("Ordered List","1. "));
FormattingCommands.Add(new LanguageFormatCommand("Block Quote","> "));
FormattingCommands.Add (new LanguageFormatCommand ());

var Headings = new LanguageFormatCommand ("Headings");
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 1","# "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 2","## "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 3","### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 4","#### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 5","##### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 6","###### "));
FormattingCommands.Add (Headings);

FormattingCommands.Add(new LanguageFormatCommand ());
FormattingCommands.Add(new LanguageFormatCommand("Link","[","]()"));
FormattingCommands.Add(new LanguageFormatCommand("Image","![]("-")"));
FormattingCommands.Add(new LanguageFormatCommand("Image Link","[ ![]("-")](linkimagehere/index.md)"));
```

E che insieme passato il `AssembleMenu` (funzione) (con il **formato** Menu impostato come base), verrebbero create un menu dinamici seguenti e voci di menu:

![Nuove voci di menu in app in esecuzione](menu-images/dynamic01.png "nuove voci di menu in app in esecuzione")

#### <a name="removing-menus-and-items"></a>Rimozione di menu e voci

Se è necessario rimuovere qualsiasi menu o una voce di menu dall'interfaccia utente dell'applicazione, è possibile utilizzare il `RemoveItemAt` metodo la `NSMenu` classe semplicemente assegnando lo zero basato su indice dell'elemento da rimuovere.

Per rimuovere i menu e voci di menu creati in base alla routine precedente, ad esempio, è possibile utilizzare il codice seguente:

```csharp
public void UnpopulateFormattingMenu(NSMenu menu) {

    // Remove any additional items
    for (int n = (int)menu.Count - 1; n > 4; --n) {
        menu.RemoveItemAt (n);
    }
}
```

Nel caso di codice precedente, le voci di quattro menu prima vengono create in Generatore di interfaccia di Xcode e qualora disponibile in app, in modo non vengono rimossi in modo dinamico.

<a name="Contextual_Menus" />

## <a name="contextual-menus"></a>Menu di scelta rapida

Menu di scelta rapida visualizzato quando l'utente fa o fa clic un elemento in una finestra del controllo. Per impostazione predefinita, molte di elementi dell'interfaccia utente incorporati macOS già presentano i menu contestuali a esse associati (ad esempio la visualizzazione di testo). Tuttavia, potrebbe essere orari in cui si desidera creare i menu contestuali nostro personalizzati per un elemento dell'interfaccia utente che è stato aggiunto a una finestra.

Modifica la **Main** file in Xcode e aggiungere un **finestra** finestra di progettazione, impostare il **classe** per "NSPanel" nel **il controllo di identità**, aggiungere un nuovo **Assistente** elemento il **finestra** menu e associarla a una nuova finestra utilizzando una **Mostra definire**:

[![L'impostazione del tipo segue](menu-images/context01.png "l'impostazione del tipo segue")](menu-images/context01-large.png#lightbox)

Di seguito, eseguire le operazioni seguenti:

1. Trascinare un **etichetta** dal **controllo libreria** sul **pannello** finestra e impostare il testo su "Proprietà": 

    [![La modifica del valore dell'etichetta](menu-images/context03.png "la modifica del valore dell'etichetta")](menu-images/context03-large.png#lightbox)
2. Trascinare quindi un **Menu** dal **controllo libreria** nel controller di visualizzazione nella gerarchia della visualizzazione e rinominare le tre voci di menu predefinito **documento**, **testo**  e **carattere**:

    [![Le voci di menu necessarie](menu-images/context02.png "le voci di menu obbligatorio")](menu-images/context02-large.png#lightbox)
3. Ora trascinamento del controllo dal **proprietà etichetta** sul **Menu**:

    [![Il trascinamento per creare un segue](menu-images/context04.png "trascinamento per creare un segue")](menu-images/context04-large.png#lightbox)
4. Finestra di dialogo popup, selezionare **Menu**: 

    ![L'impostazione del tipo segue](menu-images/context05.png "l'impostazione del tipo segue")
5. Dal **controllo di identità**, impostare la classe del Controller di visualizzazione per "PanelViewController": 

    [![L'impostazione della classe segue](menu-images/context10.png "l'impostazione della classe segue")](menu-images/context10-large.png#lightbox)
6. Tornare a Visual Studio per Mac per la sincronizzazione, quindi tornare al generatore di interfaccia.
7. Passare il **Assistente Editor** e selezionare il **PanelViewController.h** file.
8. Crea un'azione per il **documento** voce di menu chiamato `propertyDocument`: 

    [![Configurazione dell'azione](menu-images/context06.png "configurazione dell'azione")](menu-images/context06-large.png#lightbox)
9. Ripetere la creazione di azioni per le voci di menu rimanenti: 

    [![Le azioni necessarie](menu-images/context07.png "le azioni necessarie")](menu-images/context07-large.png#lightbox)
10. Creare infine una presa di corrente per il **proprietà etichetta** chiamato `propertyLabel`: 

    [![Configurazione di uscita](menu-images/context08.png "presa di configurazione")](menu-images/context08-large.png#lightbox)
11. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Modificare il **PanelViewController.cs** file e aggiungere il codice seguente:

```csharp
partial void propertyDocument (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Document";
}

partial void propertyFont (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Font";
}

partial void propertyText (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Text";
}
```

Se si esegue l'applicazione e fare doppio clic sull'etichetta della proprietà nel pannello, ora vedremo i menu di scelta rapida personalizzato. Se si seleziona e dal menu di elemento, si modificherà il valore dell'etichetta:

![Menu di scelta rapida esecuzione](menu-images/context09.png "menu di scelta rapida in esecuzione")

Avanti verrà ora esaminata la creazione di menu della barra di stato.

## <a name="status-bar-menus"></a>Menu della barra di stato

Menu della barra di stato Visualizza una raccolta di voci di menu di stato che forniscono l'interazione con o commenti e suggerimenti per l'utente, ad esempio un menu o un'immagine di riflettere lo stato di un'applicazione. Dal menu della barra di stato di un'applicazione è abilitato e attivo, anche se l'applicazione è in esecuzione in background. La barra di stato a livello di sistema si trova sul lato destro della barra dei menu dell'applicazione ed è la barra di stato solo attualmente disponibile in macOS.

Modifica il nostro **appdelegate. cs** file e verificare il `DidFinishLaunching` metodo aspetto simile al seguente:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    // Create a status bar menu
    NSStatusBar statusBar = NSStatusBar.SystemStatusBar;

    var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);
    item.Title = "Text";
    item.HighlightMode = true;
    item.Menu = new NSMenu ("Text");

    var address = new NSMenuItem ("Address");
    address.Activated += (sender, e) => {
        PhraseAddress(address);
    };
    item.Menu.AddItem (address);

    var date = new NSMenuItem ("Date");
    date.Activated += (sender, e) => {
        PhraseDate(date);
    };
    item.Menu.AddItem (date);

    var greeting = new NSMenuItem ("Greeting");
    greeting.Activated += (sender, e) => {
        PhraseGreeting(greeting);
    };
    item.Menu.AddItem (greeting);

    var signature = new NSMenuItem ("Signature");
    signature.Activated += (sender, e) => {
        PhraseSignature(signature);
    };
    item.Menu.AddItem (signature);
}
```

`NSStatusBar statusBar = NSStatusBar.SystemStatusBar;` fornisce l'accesso alla barra di stato a livello di sistema. `var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);` Crea un nuovo elemento della barra di stato. Da qui è creare un menu e un numero di voci di menu e associare il menu per l'elemento della barra di stato che appena creato. 

Se si esegue l'applicazione, verrà visualizzato il nuovo elemento della barra di stato. Selezione di un elemento dal menu verrà modificato il testo nella visualizzazione di testo: 

![Menu barra di stato in esecuzione](menu-images/statusbar01.png "menu barra di stato in esecuzione")

Successivamente, verrà ora esaminata la creazione di voci di menu personalizzate dock.

## <a name="custom-dock-menus"></a>Menu ancoraggio personalizzati

Quando l'utente fa o fa clic sull'icona dell'applicazione nell'alloggiamento di espansione controllo, l'applicazione Mac viene visualizzato il menu ancoraggio:

![Un oggetto personalizzato ancorare menu](menu-images/dock01.png "personalizzato ancorare menu")

Creiamo un menu di ancoraggio personalizzato per l'applicazione di eseguire le operazioni seguenti:

1. In Visual Studio per Mac, fare clic sul progetto e selezionare l'applicazione **Aggiungi** > **nuovo File...** Nella finestra di dialogo Nuovo file, selezionare **Xamarin.Mac** > **definizione di interfaccia vuota**, utilizzare "DockMenu" per il **nome** e fare clic su di **nuovo**  pulsante per creare il nuovo **DockMenu.xib** file:

    ![Aggiunta di una definizione di interfaccia vuota](menu-images/dock02.png "aggiunta di una definizione di interfaccia vuota")
2. Nel **soluzione riempimento**, fare doppio clic su di **DockMenu.xib** file per aprirlo e modificarlo in Xcode. Creare un nuovo **Menu** con i seguenti elementi: **indirizzo**, **data**, **saluto**, e **firma** 

    [![Layout dell'interfaccia utente](menu-images/dock03.png "layout dell'interfaccia utente")](menu-images/dock03-large.png#lightbox)
3. Successivamente, consente di connettersi il nostro nuove voci di menu nostre azioni esistenti creati per i menu personalizzato nel [aggiunta, modifica ed eliminazione di menu](#Adding,_Editing_and_Deleting_Menus) sezione precedente. Passare il **controllo connessione** e selezionare il **risponditore prima** nel **gerarchia delle interfacce**. Scorrere verso il basso e trovare il `phraseAddress:` azione. Trascinare una linea da cerchio a tale azione per il **indirizzo** voce di menu:

    [![Il trascinamento di associare un'azione](menu-images/dock04.png "il trascinamento di associare un'azione")](menu-images/dock04-large.png#lightbox)
4. Ripetere per tutte le altre voci di menu associandoli alle azioni corrispondenti: 

    [![Le azioni necessarie](menu-images/dock05.png "le azioni necessarie")](menu-images/dock05-large.png#lightbox)
5. Selezionare quindi il **applicazione** nel **interfaccia gerarchia**. Nel **controllo connessione**, trascinare una riga dal cerchio di `dockMenu` presa al menu appena creato:

    [![Trascinando la trasmissione di uscita](menu-images/dock06.png "trascinando la trasmissione di uscita")](menu-images/dock06-large.png#lightbox)
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.
7. Fare doppio clic su di **Info. plist** file per aprirlo e modificarlo: 

    [![Modifica del file Info.plist](menu-images/dock07.png "Modifica del file Info.plist")](menu-images/dock07-large.png#lightbox)
8. Fare clic su di **origine** scheda nella parte inferiore dello schermo: 

    [![Selezionare la vista origine](menu-images/dock08.png "selezionando la visualizzazione origine")](menu-images/dock08-large.png#lightbox)
9. Fare clic su **Aggiungi nuova voce**, scegliere il colore verde e un pulsante, impostare il nome della proprietà "AppleDockMenu" e il valore su "DockMenu" (il nome del nuovo file senza l'estensione .xib): 

    [![Aggiungere l'elemento DockMenu](menu-images/dock09.png "aggiungere l'elemento DockMenu")](menu-images/dock09-large.png#lightbox)

Ora se si esegue l'applicazione e fare doppio clic sulla relativa icona in ancoraggio, verranno visualizzate il nostro nuove voci di menu:

![Un esempio del menu ancora in esecuzione](menu-images/dock10.png "un esempio del menu ancora in esecuzione")

Se si seleziona uno degli elementi personalizzati dal menu, verrà modificato il testo nella visualizzazione di testo.

<a name="Pop-up_Menus_and_Pull-Down_Lists" />

## <a name="pop-up-button-and-pull-down-lists"></a>Pulsante popup e gli elenchi a discesa

Un pulsante popup viene visualizzato un elemento selezionato e visualizza un elenco di opzioni per selezionare da quando l'utente fa clic su. Un elenco a discesa è un tipo di pulsante popup in genere utilizzato per i comandi specifici per il contesto dell'attività corrente. Entrambi possono trovarsi in qualsiasi punto in una finestra.

Creiamo un pulsante personalizzato popup per l'applicazione di eseguire le operazioni seguenti:

1. Modificare il **Main** file in Xcode, trascinare un **pulsante Popup** dal **controllo libreria** sul **pannello** finestra creata in il [i menu contestuali](#Contextual_Menus) sezione: 

    [![Aggiunta di un pulsante popup](menu-images/popup01.png "aggiungendo un pulsante di popup")](menu-images/popup01-large.png#lightbox)
2. Aggiungere una nuova voce di menu e impostare i titoli degli elementi nella finestra Popup per: **indirizzo**, **data**, **saluto**, e **firma** 

    [![Configurare le voci di menu](menu-images/popup02.png "configurazione le voci di menu")](menu-images/popup02-large.png#lightbox)
3. Successivamente, consente di connettersi il nostro nuove voci di menu azioni esistenti creati per i menu personalizzato nel [aggiunta, modifica ed eliminazione di menu](#Adding,_Editing_and_Deleting_Menus) sezione precedente. Passare il **controllo connessione** e selezionare il **risponditore prima** nel **gerarchia delle interfacce**. Scorrere verso il basso e trovare il `phraseAddress:` azione. Trascinare una linea da cerchio a tale azione per il **indirizzo** voce di menu: 

    [![Il trascinamento di associare un'azione](menu-images/popup03.png "il trascinamento di associare un'azione")](menu-images/popup03-large.png#lightbox)
4. Ripetere per tutte le altre voci di menu associandoli alle azioni corrispondenti: 

    [![Tutti i necessari azioni](menu-images/popup04.png "tutti azioni necessarie")](menu-images/popup04-large.png#lightbox)
5. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Se si esegue l'applicazione e selezionare un elemento dalla finestra popup, il testo nella visualizzazione di testo verrà modificate:

![Un esempio di finestra popup in esecuzione](menu-images/popup05.png "un esempio di finestra popup in esecuzione")

È possibile creare e utilizzare gli elenchi a discesa in esattamente così come pulsanti popup. Anziché associare all'azione esistente, è possibile creare proprie azioni personalizzate, proprio come abbiamo fatto per il menu di scelta rapida nel [i menu contestuali](#Contextual_Menus) sezione.

## <a name="summary"></a>Riepilogo

In questo articolo è stato applicato l'utilizzo di menu e voci di menu in un'applicazione Xamarin.Mac un'analisi approfondita. Prima di tutto esaminata barra dei menu dell'applicazione, quindi è stato esaminato la creazione di menu di scelta rapida, quindi esaminata menu della barra di stato e personalizzato ancorare i menu. Infine, trattati i menu a comparsa e gli elenchi a discesa.


## <a name="related-links"></a>Collegamenti correlati

- [MacMenus (esempio)](https://developer.xamarin.com/samples/mac/MacMenus/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Linee guida dell'interfaccia umana - menu](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)
- [Introduzione al menu dell'applicazione e gli elenchi popup](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MenuList/MenuList.html)
