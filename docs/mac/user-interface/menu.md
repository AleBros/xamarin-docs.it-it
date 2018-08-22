---
title: Menu di scelta in xamarin. Mac
description: Questo articolo descrive l'uso dei menu in un'applicazione xamarin. Mac. Descrive la creazione e la gestione di menu e voci di menu in Xcode e Interface Builder e utilizzarli a livello di codice.
ms.prod: xamarin
ms.assetid: 5D367F8E-3A76-4995-8A89-488530FAD802
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: d84910cd5c2bc72a563fb04457532d544aedf571
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2018
ms.locfileid: "40250975"
---
# <a name="menus-in-xamarinmac"></a>Menu di scelta in xamarin. Mac

_Questo articolo descrive l'uso dei menu in un'applicazione xamarin. Mac. Descrive la creazione e la gestione di menu e voci di menu in Xcode e Interface Builder e utilizzarli a livello di codice._

Quando si usa c# e .NET in un'applicazione xamarin. Mac, è possibile utilizzare i menu Cocoa stesso che gli sviluppatori che lavorano in Objective-C e Xcode. Poiché xamarin. Mac si integra direttamente con Xcode, è possibile usare Interface Builder di Xcode per creare e gestire le barre dei menu, menu e voci di menu (oppure crearle direttamente nel codice c#).

I menu sono parte integrante dell'esperienza utente di un'applicazione di Mac e in genere vengono visualizzati in diverse parti dell'interfaccia utente:

- **Barra dei menu dell'applicazione** -si tratta del menu principale che viene visualizzato nella parte superiore della schermata per ogni applicazione Mac.
- **I menu contestuali** -questi elementi vengono visualizzati quando l'utente fa clic o clic controllo un elemento in una finestra.
- **La barra di stato** -questa è l'area sul lato destro della barra dei menu dell'applicazione che viene visualizzato nella parte superiore della schermata (a sinistra dell'orologio della barra dei menu) e a sinistra aumenta man mano che gli elementi vengono aggiunti a esso.
- **Ancorare menu** -per ogni applicazione nel dock che viene visualizzato il menu quando l'utente fa clic o controllo clic sull'icona dell'applicazione o quando l'utente effettua l'icona e premuto il pulsante del mouse.
- **Pulsante popup ed elenchi a discesa** -un pulsante popup viene visualizzato un elemento selezionato e visualizza un elenco di opzioni per selezionare da quando l'utente fa clic su. Un elenco a discesa è un tipo di pulsante popup in genere utilizzato per la selezione di comandi specifici per il contesto dell'attività corrente. Entrambi possono trovarsi in qualsiasi punto in una finestra.

[![Un menu di esempio](menu-images/intro01.png "un menu di esempio")](menu-images/intro01-large.png#lightbox)

In questo articolo, si affronterà le nozioni di base dell'utilizzo di barre dei menu Cocoa, menu e voci di menu in un'applicazione xamarin. Mac. È altamente consigliabile usare la [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare le [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [Outlet e azioni](~/mac/get-started/hello-mac.md#outlets-and-actions) le sezioni, perché illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

È possibile ottenere un quadro il [c# esposizione di classi / metodi di Objective-c](~/mac/internals/how-it-works.md) sezione del [meccanismi interni di xamarin. Mac](~/mac/internals/how-it-works.md) del documento, viene spiegato il `Register` e `Export` attributi utilizzato per wireup classi c# per gli oggetti di Objective-C e gli elementi dell'interfaccia utente.

## <a name="the-applications-menu-bar"></a>Barra dei menu dell'applicazione 

A differenza delle applicazioni in esecuzione nel sistema operativo Windows in cui tutte le finestre possono avere un proprio barra dei menu collegato a esso, ogni applicazione in esecuzione in macOS è una barra dei menu singolo che viene eseguito nella parte superiore della schermata che viene usata per ogni finestra dell'applicazione:

[![Una barra dei menu](menu-images/appmenu01.png "una barra dei menu")](menu-images/appmenu01-large.png#lightbox)

Gli elementi sulla barra dei menu vengono attivati o disattivati in base al contesto corrente o lo stato dell'applicazione e la relativa interfaccia utente in qualsiasi momento. Ad esempio: se l'utente seleziona un campo di testo, gli elementi del **Edit** menu verrà abilitato, ad esempio **copia** e **Taglia**.

In base a Apple e per impostazione predefinita, tutte le applicazioni di macOS hanno un set standard di menu e voci di menu visualizzato nella barra dei menu dell'applicazione:

- **Menu Apple** -consente di accedere al sistema wide gli elementi disponibili all'utente in qualsiasi momento, indipendentemente da quale applicazione è in esecuzione. Questi elementi non possono essere modificati dallo sviluppatore.
- **Menu dell'app** -questo menu consente di visualizzare il nome dell'applicazione in grassetto e consente all'utente di identificare quale applicazione è attualmente in esecuzione. Contiene elementi che si applicano all'applicazione come un intero e non un documento specifico o un processo, ad esempio l'applicazione in fase di chiusura.
- **Menu file** : elementi usati per creare, aprire o salvare i documenti che l'applicazione funziona con. Se l'applicazione non basata su documenti, questo menu può essere rinominato o rimosso.
- **Menu Modifica** -contiene i comandi quali **tagliare**, **copia**, e **Incolla** che vengono usati per modificare o modificare gli elementi nell'interfaccia utente dell'applicazione.
- **Menu Formato** : se l'applicazione funziona con il testo, questo menu contiene i comandi per configurare la formattazione del testo.
- **Menu Visualizza** -contiene i comandi che influiscono sulla modalità di visualizzazione di contenuto (visualizzazione) nell'interfaccia utente dell'applicazione.
- **Menu dell'applicazione specifici** -questi sono tutti i menu sono specifici dell'applicazione (ad esempio un menu di segnalibri per un web browser). Dovrebbero essere visualizzate tra i **vista** e **finestra** sulla barra dei menu.
- **Menu finestra** -contiene i comandi per l'uso di finestre nell'applicazione, nonché un elenco delle finestre aperte corrente.
- **Menu Guida** -se l'applicazione vengono fornite informazioni sullo schermo, il menu della Guida in linea deve essere il menu a destra sulla barra. 

Per altre informazioni sull'applicazione barra dei menu e menu standard e voci di menu, vedere di Apple [Human Interface Guidelines](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/).

### <a name="the-default-application-menu-bar"></a>Barra dei menu dell'applicazione predefinita

Quando si crea un nuovo progetto xamarin. Mac, si ottiene automaticamente una standard, impostazione predefinita dell'applicazione barra dei menu con gli elementi tipici che un'applicazione macOS disporrebbe (come indicato nella sezione precedente). Barra di menu dell'applicazione predefinito è definita nel **Main. Storyboard** file (insieme al resto dell'interfaccia utente dell'app) nel progetto nel **riquadro della soluzione**:  

![Selezionare lo storyboard principale](menu-images/appmenu02.png "selezionare lo storyboard principale")

Fare doppio clic il **Main. Storyboard** file per aprirlo per la modifica in Interface Builder di Xcode e verrà visualizzata l'interfaccia dell'editor menu:

[![Modifica l'interfaccia utente in Xcode](menu-images/defaultbar01.png "modifica l'interfaccia utente in Xcode")](menu-images/defaultbar01-large.png#lightbox)

A questo punto possiamo fare clic su elementi, ad esempio il **aperto** voce di menu nel **File** dal menu e modificare o modificarne le proprietà nel **Attributes Inspector**:

[![La modifica degli attributi del menu](menu-images/defaultbar02.png "la modifica degli attributi del menu")](menu-images/defaultbar02-large.png#lightbox)

Si esamineranno l'aggiunta, modifica ed eliminazione di menu e voci più avanti in questo articolo. Per ora si vuole solo vedere quali i menu e voci di menu sono disponibili per impostazione predefinita e come sono stati automaticamente esposti al codice tramite un set di predefinite Outlet e azioni (per altre informazioni vedere la [Outlet e azioni](~/mac/get-started/hello-mac.md#outlets-and-actions) documentazione).

Ad esempio, se si fa clic sul **connessione Inspector** per il **Open** voce di menu è possibile osservare automaticamente associati fino al `openDocument:` azione: 

[![Visualizzazione dell'azione associata](menu-images/defaultbar03.png "visualizzando l'azione associata")](menu-images/defaultbar03-large.png#lightbox)

Se si seleziona il **risponditore prima** nel **gerarchia delle interfacce** e scorrere verso il basso il **controllo connessione**, e verrà visualizzata la definizione del `openDocument:` azione che il **aperto** voce di menu è collegata a (insieme a diverse altre azioni predefinite per l'applicazione e non vengono collegate automaticamente fino a controlli):

[![Visualizzazione azioni collegate tutti i](menu-images/defaultbar04.png "visualizzando tutte le azioni associate")](menu-images/defaultbar04-large.png#lightbox) 

Perché è importante? Nella prossima sezione verrà visualizzato come queste azioni definite automaticamente funzionano con gli altri elementi dell'interfaccia utente Cocoa automaticamente abilitare e disabilitare le voci di menu, nonché, fornire una funzionalità incorporata per gli elementi.

In un secondo momento si userà queste azioni predefinite per abilitare e disabilitare gli elementi dal codice e fornire la propria funzionalità quando vengono selezionati.

<a name="Built-In_Menu_Functionality" />

### <a name="built-in-menu-functionality"></a>Funzionalità di menu incorporato

Se si eseguisse l'esecuzione di un'applicazione xamarin. Mac appena creata prima di aggiungere eventuali elementi dell'interfaccia utente o il codice, si noterà che alcuni elementi vengono automaticamente dopo aver e abilitate per l'utente (con completamente integrate funzionalità automaticamente), ad esempio il **Quit** degli elementi nella **App** menu:

![Una voce di menu abilitata](menu-images/appmenu03.png "una voce di menu abilitata")

Mentre altre voci di menu, ad esempio **tagliare**, **copia**, e **Incolla** non sono:

![Voci di menu disabilitate](menu-images/appmenu04.png "disabilitato le voci di menu")

È possibile arrestare l'applicazione e fare doppio clic il **Main. Storyboard** del file nei **riquadro della soluzione** aprirlo e modificarlo in Xcode Interface Builder di. Trascinare quindi una **visualizzazione di testo** dal **libreria** nel controller di visualizzazione della finestra di **Editor dell'interfaccia**:

[![Selezione di una visualizzazione di testo dalla libreria](menu-images/appmenu05.png "selezionando una visualizzazione di testo dalla libreria")](menu-images/appmenu05-large.png#lightbox)

Nel **Editor vincoli** è possibile aggiungere la visualizzazione di testo ai bordi della finestra e impostarlo in cui ingrandisce e si riduce con la finestra facendo clic su tutti i quattro ai rossi nella parte superiore dell'editor e la **Aggiungi 4 vincoli** pulsante:

[![Modifica di vincoli](menu-images/appmenu06.png "la modifica di vincoli")](menu-images/appmenu06-large.png#lightbox)

Salvare le modifiche alla progettazione dell'interfaccia utente e tornare alla modalità precedente di Visual Studio per Mac sincronizzare le modifiche con il progetto xamarin. Mac. Ora avviare l'applicazione, digitare un testo nella visualizzazione di testo, selezionarlo e aprire il **modifica** menu:

![Le voci di menu vengono automaticamente abilitata o disabilitata](menu-images/appmenu07.png "le voci di menu vengono automaticamente abilitata o disabilitata")

Si noti che il **tagliare**, **copia**, e **Incolla** gli elementi vengono automaticamente abilitata e completamente funzionante, tutto senza scrivere una sola riga di codice. 

Che cosa succede? Tenere presente l'elemento predefinito predefinire le azioni che precedono cablate un massimo di voci di menu predefinito (come presentato in precedenza), la maggior parte degli elementi dell'interfaccia utente Cocoa che fanno parte di macOS siano compilati in hook ad azioni specifiche (ad esempio `copy:`). Pertanto, quando vengono aggiunti a una finestra, attiva e selezionate, la voce di menu corrispondente o elementi associati a tale azione vengono abilitati automaticamente. Se l'utente seleziona la voce di menu, le funzionalità integrate di elemento dell'interfaccia utente verrà richiamata ed eseguita, tutto senza l'intervento per gli sviluppatori.

### <a name="enabling-and-disabling-menus-and-items"></a>Abilitazione e disabilitazione di menu e voci

Per impostazione predefinita, ogni volta che si verifica un evento utente, `NSMenu` automaticamente Abilita e disabilita ogni menu visibile ed elementi in base al contesto dell'applicazione. Esistono tre modi per abilitare o disabilitare un elemento:

- **L'abilitazione automatica dei menu** -una voce di menu è abilitata se `NSMenu` può trovare un oggetto appropriato che risponde all'azione che l'elemento è reti cablate-fino a. Ad esempio, la visualizzazione di testo precedente che aveva un hook predefinito per il `copy:` azione.
- **Azioni personalizzate e validateMenuItem:** : per qualsiasi voce di menu che viene associato a un [azione del controller personalizzato finestra o visualizzazione](#Working-with-Custom-Window-Actions), è possibile aggiungere il `validateMenuItem:` azione e manualmente abilitare o disabilitare le voci di menu.
- **Abilitazione di menu manuale** -si imposta manualmente il `Enabled` proprietà di ogni `NSMenuItem` per abilitare o disabilitare individualmente ogni elemento in un menu.

Per scegliere un sistema, impostare il `AutoEnablesItems` proprietà di un `NSMenu`. `true` automatico (comportamento predefinito) e `false` è manuale. 

> [!IMPORTANT]
> Se si sceglie di usare in modo manuale menu, del menu elementi none, anche quelle controllate da classi di AppKit come `NSTextView`, vengono aggiornate automaticamente. Sarai ritenuto responsabile per l'abilitazione e disabilitazione di tutti gli elementi manualmente nel codice.

#### <a name="using-validatemenuitem"></a>Usando validateMenuItem

Come indicato in precedenza, per qualsiasi voce di menu che viene associato a un [finestra o azione personalizzata di Controller di visualizzazione](#Working-with-Custom-Window-Actions), è possibile aggiungere il `validateMenuItem:` azione e manualmente abilitare o disabilitare le voci di menu.

Nell'esempio seguente, il `Tag` verrà utilizzata per decidere il tipo di voce di menu che verrà essere abilitato o disabilitato da proprietà la `validateMenuItem:` azione basato sullo stato del testo selezionato in un `NSTextView`. Il `Tag` proprietà è stata impostata in Interface Builder per ogni voce di menu:

![Impostazione della proprietà Tag](menu-images/validate01.png "impostando la proprietà Tag")

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

Quando questo codice viene eseguito e viene selezionato alcun testo nel `NSTextView`, le voci di menu due incapsulamento sono disabilitate (anche se essi vengono collegati alle azioni del controller di visualizzazione):

![Gli elementi che mostra disabilitata](menu-images/validate02.png "che mostra gli elementi disabilitati")

Se viene selezionata una sezione di testo e riaprire il menu di scelta, le voci di menu due incapsulamento saranno disponibili:

![Gli elementi che mostra abilitata](menu-images/validate03.png "visualizzazione elemento abilitato")

## <a name="enabling-and-responding-to-menu-items-in-code"></a>Abilitazione e risponde alle voci di menu nel codice

Come abbiamo visto in precedenza, semplicemente aggiungendo elementi dell'interfaccia utente Cocoa specifici per la progettazione dell'interfaccia utente (ad esempio, un campo di testo), alcune delle voci di menu predefinito sarà abilitati e funzionano automaticamente, senza dover scrivere alcun codice. Successivo verrà ora esaminato aggiungendo il codice c# per il progetto xamarin. Mac per consentire una voce di menu e fornire funzionalità quando viene selezionata dall'utente.

Ad esempio, presupponiamo si desidera che l'utente sia in grado di usare il **aperto** degli elementi nella **File** menu per selezionare una cartella. Poiché deve essere una funzione a livello di applicazione e non è limitato a una finestra di assegnare o elemento dell'interfaccia utente, dobbiamo aggiungere il codice per gestire questa situazione per il delegato dell'applicazione.

Nel **riquadro della soluzione**, fare doppio clic il `AppDelegate.CS` file per aprirlo e modificarlo:

![Delegato dell'app selezionando](menu-images/appmenu08.png "selezionando delegato dell'app")

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

È possibile eseguire ora l'applicazione e aprire il **File** menu: 

![Nel menu File](menu-images/appmenu09.png "dal menu File")

Si noti che il **aperto** voce di menu è ora abilitata. Se si seleziona, verrà visualizzata la finestra di dialogo aprire:

![Una finestra di dialogo aperta](menu-images/appmenu10.png "una finestra di dialogo aprire")

Se si sceglie il **Open** pulsante, verrà visualizzato il messaggio di avviso:

![Un esempio di messaggio di dialogo](menu-images/appmenu11.png "un messaggio di dialogo di esempio")

La riga di chiave è stata `[Export ("openDocument:")]`, viene indicato `NSMenu` che nostro **AppDelegate** dispone di un metodo `void OpenDialog (NSObject sender)` che risponde al `openDocument:` azione. Se verrà memorizzata in precedenza, il **aperto** voce di menu viene automaticamente reti cablate-fino a questa azione per impostazione predefinita in Interface Builder:

[![Visualizzare le azioni associate](menu-images/defaultbar03.png "visualizzando le azioni associate")](menu-images/defaultbar03-large.png#lightbox)

Successivo verrà ora esaminato la creazione di un menu, le voci di menu e le azioni e rispondere ad essi nel codice.

### <a name="working-with-the-open-recent-menu"></a>Utilizzo di menu Apri recenti

Per impostazione predefinita, il **File** menu contiene una **Apri recenti** elemento che tiene traccia degli ultimi diversi file che l'utente ha aperto l'app. Se si sta creando un `NSDocument` basato su app xamarin. Mac, questo menu verrà gestito automaticamente. Per qualsiasi altro tipo di app xamarin. Mac, sarai ritenuto responsabile per la gestione e risposta per questa voce di menu manualmente.

Per gestire manualmente il **Apri recenti** menu, è necessario innanzitutto informa che un nuovo file è stato aperto o salvato con i seguenti:

```csharp
// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

Anche se l'app non usa `NSDocuments`, è comunque possibile usare il `NSDocumentController` per mantenere il **Apri recenti** menu inviando un `NSUrl` con il percorso del file dal `NoteNewRecentDocumentURL` metodo il `SharedDocumentController`.

Successivamente, è necessario eseguire l'override di `OpenFile` metodo del delegato dell'app per aprire qualsiasi file selezionato dall'utente il **aprire recenti** menu. Ad esempio:

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

Restituire `true` se il file può essere aperto, altrimenti restituire `false` e verrà visualizzato un messaggio di avviso predefinita per l'utente che non è stato possibile aprire il file.

Poiché il nome file e il percorso restituito dal **Apri recenti** menu, possono includere uno spazio, dobbiamo correttamente questo carattere di escape prima di creare un `NSUrl` o si verificherà un errore. Tale scopo, eseguire il codice seguente:

```csharp
filename = filename.Replace (" ", "%20");
```

Infine, creiamo un `NSUrl` che punta al file e usare un metodo di supporto nell'app delegato per aprire una nuova finestra e caricare il file al suo interno:

```csharp
var url = new NSUrl ("file://"+filename);
return OpenFile(url);
```

Per tutto ciò che aggregano, diamo un'occhiata a un esempio di implementazione in una **AppDelegate.cs** file:

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

In base ai requisiti dell'app, è possibile evitare all'utente di aprire il file stesso in più di una finestra nello stesso momento. Nell'app di esempio, se l'utente sceglie un file che è già aperto (dal **Apri recenti** o **aprire..** voci di menu), la finestra che contiene il file viene portata in primo piano.

A tale scopo, è stato usato il codice seguente nel metodo di supporto:

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

È stato progettato nostri `ViewController` classe per contenere il percorso del file nella relativa `Path` proprietà. Successivamente, esaminati in ciclo tutte le finestre attualmente aperte nell'app. Se il file è già aperto in una delle finestre, viene inserito all'inizio di tutte le altre finestre utilizzando:

```csharp
NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
```

Se viene trovata alcuna corrispondenza, viene aperta una nuova finestra con il file caricato e il file è indicato nel **Apri recenti** menu:

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

### <a name="working-with-custom-window-actions"></a>Uso di azioni di finestra personalizzati

Esattamente come l'oggetto incorporato **risponditore prima** le azioni che vengono pre-cablate in voci di menu standard, è possibile crearne uno nuovo, azioni personalizzate e associare a voci di menu in Interface Builder.

In primo luogo, definire un'azione personalizzata in uno dei controller di finestra dell'app. Ad esempio:

```csharp
[Action("defineKeyword:")]
public void defineKeyword (NSObject sender) {
    // Preform some action when the menu is selected
    Console.WriteLine ("Request to define keyword");
}
```

Successivamente, fare doppio clic sul file di storyboard dell'app nel **riquadro della soluzione** aprirlo e modificarlo in Xcode Interface Builder di. Selezionare il **risponditore prima** sotto il **applicazione scena**, quindi passare al **Attributes Inspector**:

![Attributes Inspector](menu-images/action01.png "Attributes Inspector")

Fare clic sui **+** pulsante in fondo il **Attributes Inspector** per aggiungere una nuova azione personalizzata:

![Aggiunta di una nuova azione](menu-images/action02.png "aggiunta una nuova azione")

Assegnargli lo stesso nome di azione personalizzata che è stato creato nel controller di finestra:

![Modifica il nome dell'azione](menu-images/action03.png "modifica il nome dell'azione")

CTRL + clic e trascinare da una voce di menu per il **risponditore prima** sotto il **scena applicazione**. Nell'elenco popup, selezionare il nuovo elemento action appena creato (`defineKeyword:` in questo esempio):

![Collegamento di un'azione](menu-images/action04.png "collegare un'azione")

Salvare le modifiche apportate allo storyboard e tornare a Visual Studio per Mac sincronizzare le modifiche. Se si esegue l'app, la voce di menu che connesso l'azione personalizzata verrà automaticamente essere abilitato o disabilitato (basato su finestra con l'azione è aperto) e selezionando la voce di menu verrà avviata l'azione:

[![Prova il nuovo elemento action](menu-images/action05.png "nuova azione di test")](menu-images/action05-large.png#lightbox)

<a name="Adding,_Editing_and_Deleting_Menus" />

### <a name="adding-editing-and-deleting-menus"></a>Aggiunta, modifica ed eliminazione di menu

Come abbiamo visto nelle sezioni precedenti, un'applicazione xamarin. Mac include una serie di set di impostazioni predefinito menu e voci di menu che specifici controlli dell'interfaccia utente verranno automaticamente attivata e rispondere a. È stato anche descritto come aggiungere codice all'applicazione che ti permetterà di rispondere a questi elementi predefiniti anche.

In questa sezione si esaminerà la rimozione di voci di menu che non è necessaria, la riorganizzazione di menu e aggiunta di nuovi menu, le voci di menu e le azioni.

Fare doppio clic il **Main. Storyboard** del file nei **riquadro della soluzione** per aprirlo e modificarlo:

[![Modifica l'interfaccia utente in Xcode](menu-images/maint01.png "modifica l'interfaccia utente in Xcode")](menu-images/maint01-large.png#lightbox)

Per la nostra applicazione xamarin. Mac specifico non verrà usato il valore predefinito **vista** menu, quindi verrà usato per rimuoverlo. Nel **gerarchia delle interfacce** selezionare la **visualizzazione** voce di menu che fa parte della barra dei menu principale:

![Selezionando la voce di menu Visualizza](menu-images/maint02.png "selezionando la voce di menu Visualizza")

Premere CANC o backspace per eliminare il menu di scelta. Successivamente, non ci occuperemo di usare tutti gli elementi nel **formato** menu e si desidera spostare gli elementi si intende usare out che si trova sotto i sottomenu. Nel **gerarchia delle interfacce** selezionare le voci di menu seguenti:

![L'evidenziazione di più elementi](menu-images/maint03.png "evidenziazione più elementi")

Trascinare gli elementi sotto l'elemento padre **Menu** dal sottomenu in cui sono attualmente:

[![Il trascinamento di voci di menu al menu del padre](menu-images/maint04.png "trascinando le voci di menu al menu del padre")](menu-images/maint04-large.png#lightbox)

Il menu sarà ora simile:

[![Gli elementi nella nuova posizione](menu-images/maint05.png "gli elementi nella nuova posizione")](menu-images/maint05-large.png#lightbox)

Successiva trascinare i **testo** sottomenu out dal **formato** menu e inserirlo nella barra dei menu principale tra il **formato** e **finestra** menu di scelta:

[![Il menu di testo](menu-images/maint06.png "testo di menu")](menu-images/maint06-large.png#lightbox)

Torniamo indietro sotto il **formato** menu ed eliminare il **Font** la voce del sottomenu. Selezionare quindi il **formato** menu e rinominarlo "Tipo di carattere":

[![Il menu di scelta del tipo di carattere](menu-images/maint07.png "menu il tipo di carattere")](menu-images/maint07-large.png#lightbox)

Successivamente, passiamo alla creazione di un menu personalizzato di frasi predefine che verrà automaticamente ottenere aggiunto al testo nella visualizzazione di testo quando vengono selezionati. Nella casella di ricerca nella parte inferiore di **Library Inspector** tipo in "menu". Ciò renderà più facile trovare e utilizzare con tutti gli elementi dell'interfaccia utente dal menu:

![Library Inspector](menu-images/maint08.png "Library Inspector")

A questo punto è possibile eseguire le operazioni seguenti per creare i menu di scelta:

1. Trascinare un **voce di Menu** dal **Library Inspector** sulla barra dei menu di scelta tra il **testo** e **finestra** menu: 

    ![Selezione di una nuova voce di menu nella libreria](menu-images/maint10.png "selezionando una nuova voce di menu nella libreria")
2. Rinominare l'elemento "Frasi": 

    [![Impostazione del nome di menu](menu-images/maint09.png "impostando il nome del menu")](menu-images/maint09-large.png#lightbox)
3. Trascinare un **dal Menu** dalle **Library Inspector**: 

    ![Selezione di un menu dalla libreria](menu-images/maint11.png "selezionando un menu dalla libreria")
4. Eliminare quindi **dal Menu** sul nuovo **voce di Menu** abbiamo appena creato e modificarne il nome in "Frasi": 

    [![Modifica il nome del menu](menu-images/maint12.png "modifica il nome del menu")](menu-images/maint12-large.png#lightbox)
5. A questo punto è possibile rinominare il valore predefinito di tre **voci di Menu** "Address", "Date" e "Greeting": 

    [![Il menu di frasi](menu-images/maint13.png "dal menu di frasi")](menu-images/maint13-large.png#lightbox)
6. È possibile aggiungere un quarto **voce di Menu** trascinando un **voce di Menu** dal **Library Inspector** e chiamarlo "Firma": 

    [![Modifica il nome di voce di menu](menu-images/maint14.png "modifica il nome dell'elemento menu")](menu-images/maint14-large.png#lightbox)
7. Salvare le modifiche apportate alla barra dei menu.

A questo punto verrà creato un set di azioni personalizzate in modo che i nuovi elementi di menu vengono esposti al codice c#. In Xcode si passa al **Assistant** Vista:

[![Creare le azioni necessarie indicate](menu-images/maint15.png "creando le azioni necessarie")](menu-images/maint15-large.png#lightbox)

È possibile eseguire le operazioni seguenti:

1. Controllo e trascinare dal **indirizzi** voce di menu per il **appdelegate. H** file.
2. Switch il **Connection** tipo **azione**: 

    [![Selezione del tipo di azione](menu-images/maint17.png "selezionando il tipo di azione")](menu-images/maint17-large.png#lightbox)
3. Immettere un **Name** del "phraseAddress" e premere il **Connect** pulsante per creare il nuovo elemento action: 

    [![Configurazione dell'azione](menu-images/maint18.png "configurazione dell'azione")](menu-images/maint18-large.png#lightbox)
4. Ripetere i passaggi precedenti per il **data**, **il messaggio di saluto**, e **firma** voci di menu: 

    [![Le azioni completate](menu-images/maint19.png "le azioni completate")](menu-images/maint19-large.png#lightbox)
5. Salvare le modifiche apportate alla barra dei menu.

Successivamente è necessario creare una presa per la visualizzazione di testo in modo che è possibile modificare il contenuto dal codice. Selezionare il **viewcontroller. H** del file nei **Assistente dell'Editor** e creare nuovi punti vendita chiamato `documentText`:

[![Creazione di un outlet](menu-images/maint20.png "la creazione di un outlet")](menu-images/maint20-large.png#lightbox)

Tornare a Visual Studio per Mac sincronizzare le modifiche da Xcode. Modificare quindi il **ViewController.cs** file e renderlo simile al seguente:

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

Ciò espone il testo della visualizzazione testo di fuori del `ViewController` classe e informa il delegato dell'app quando la finestra acquisisce o perde lo stato attivo. A questo punto modificare il **AppDelegate.cs** file e renderlo simile al seguente:

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

Qui abbiamo esaminato la `AppDelegate` parziale una classe in modo che è possibile usare le azioni e gli Outlet definito in Interface Builder. È anche possibile esporre un `textEditor` per tenere traccia di quale finestra è attualmente in stato attivo.

Per gestire i menu e voci di menu personalizzati vengono utilizzati i metodi seguenti:

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

Ora, se eseguiamo l'applicazione, tutti gli elementi nel **frase** menu sarà attivo e aggiungerà la frase di assegnare alla visualizzazione di testo, quando selezionato:

![Un esempio di app in esecuzione](menu-images/maint21.png "un esempio di app in esecuzione")

Ora che abbiamo le nozioni di base dell'utilizzo con la barra dei menu di applicazione verso il basso, esaminiamo la creazione di un menu contestuale personalizzato.

### <a name="creating-menus-from-code"></a>Creazione di menu dal codice

Oltre a creare i menu e voci di menu con Interface Builder di Xcode, potrebbero esserci volte quando è necessario che un'app xamarin. Mac per creare, modificare o rimuovere un menu, sottomenu o voce di menu dal codice.

Nell'esempio seguente viene creata una classe per contenere le informazioni sulle voci di menu e menu di scelta secondario che verrà creato in modo dinamico in tempo reale:

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

Questa classe è definita, la routine seguente analizza una raccolta di `LanguageFormatCommand`in modo ricorsivo e oggetti di compilare nuovi menu e voci di menu aggiungendole alla parte inferiore del menu esistente (creato in Interface Builder) che è stata passata:

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

Per qualsiasi `LanguageFormatCommand` oggetto che ha un valore vuoto `Title` crea questa routine di proprietà, un **separatore di menu** (una linea grigia thin) tra sezioni del menu di scelta:

```csharp
menuItem = NSMenuItem.SeparatorItem;
```

Se viene fornito un titolo, viene creata una nuova voce di menu con tale titolo:

```csharp
menuItem = new NSMenuItem (command.Title);
``` 

Se il `LanguageFormatCommand` oggetto contiene l'elemento figlio `LanguageFormatCommand` oggetti, viene creato un sottomenu e `AssembleMenu` metodo viene chiamato per creare tale menu in modo ricorsivo:

```csharp
menuItem.Submenu = new NSMenu (command.Title);
AssembleMenu (menuItem.Submenu, command.SubCommands);
```

Per qualsiasi nuova voce di menu che non dispongono di menu secondarie, verrà aggiunto codice per gestire la voce di menu viene selezionata dall'utente:

```csharp
menuItem.Activated += (sender, e) => {
    // Do something when the menu item is selected
    ...
};
```

#### <a name="testing-the-menu-creation"></a>La creazione di menu test

Con tutto il codice sopra riportato sul posto, se la seguente raccolta di `LanguageFormatCommand` gli oggetti sono stati creati:

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

E tale raccolta passato al `AssembleMenu` funzione (con il **formato** Menu Imposta come base), verrebbe create le voci di menu seguenti menu dinamici:

![Le nuove voci di menu nell'app in esecuzione](menu-images/dynamic01.png "nuove voci di menu nell'app in esecuzione")

#### <a name="removing-menus-and-items"></a>Rimozione di menu e voci

Se è necessario rimuovere eventuali menu o una voce di menu dall'interfaccia utente dell'app, è possibile usare la `RemoveItemAt` metodo di `NSMenu` classe assegnandogli lo zero basata semplicemente indice dell'elemento da rimuovere.

Ad esempio, per rimuovere i menu e voci di menu creati dalla routine precedente, si potrebbe usare il codice seguente:

```csharp
public void UnpopulateFormattingMenu(NSMenu menu) {

    // Remove any additional items
    for (int n = (int)menu.Count - 1; n > 4; --n) {
        menu.RemoveItemAt (n);
    }
}
```

Nel caso di codice precedente, le voci di quattro menu prima vengono create in Interface Builder di Xcode e qualora disponibile nell'app, in modo che non vengono rimossi in modo dinamico.

<a name="Contextual_Menus" />

## <a name="contextual-menus"></a>Menu di scelta rapida

Menu di scelta rapida visualizzato quando l'utente fa clic o clic controllo un elemento in una finestra. Per impostazione predefinita, molte di elementi dell'interfaccia utente già compilati in macOS presentano i menu contestuali associati (ad esempio la visualizzazione di testo). Tuttavia, potrebbe essere tempi di cui si vogliono creare nostro menu contestuali personalizzati per un elemento dell'interfaccia utente che è stato aggiunto a una finestra.

Modifica della nostro **Main. Storyboard** file in Xcode e aggiungere un **finestra** finestra per la progettazione, impostare relativo **classe** a "NSPanel" nel **Identity Inspector**, aggiungere un nuovo **Assistant** elemento per il **finestra** dal menu e collegarlo alla finestra nuova usando un **Segue illustrato**:

[![Impostare il tipo di elemento segue](menu-images/context01.png "impostando il tipo di elemento segue")](menu-images/context01-large.png#lightbox)

È possibile eseguire le operazioni seguenti:

1. Trascinare un **etichetta** dalle **Library Inspector** nel **pannello** finestra e impostare il testo su "Proprietà": 

    [![La modifica del valore dell'etichetta](menu-images/context03.png "la modifica del valore dell'etichetta")](menu-images/context03-large.png#lightbox)
2. Trascinare un **dal Menu** dal **Library Inspector** al Controller di visualizzazione nella gerarchia di visualizzazione e rinominare le tre voci di menu predefinito **documento**, **testo**  e **Font**:

    [![Le voci di menu necessaria](menu-images/context02.png "le voci di menu obbligatorio")](menu-images/context02-large.png#lightbox)
3. Ora controllo e trascinare dal **un'etichetta della proprietà** nel **Menu**:

    [![Il trascinamento per creare un elemento segue](menu-images/context04.png "il trascinamento per creare un elemento segue")](menu-images/context04-large.png#lightbox)
4. Nella finestra di dialogo popup selezionare **Menu**: 

    ![Impostare il tipo di elemento segue](menu-images/context05.png "impostando il tipo di elemento segue")
5. Dal **Identity Inspector**, impostare la classe del Controller di visualizzazione a "PanelViewController": 

    [![L'impostazione della classe di elemento segue](menu-images/context10.png "l'impostazione della classe di elemento segue")](menu-images/context10-large.png#lightbox)
6. Tornare a Visual Studio per Mac per la sincronizzazione, quindi tornare a Interface Builder.
7. Passare al **Assistente dell'Editor** e selezionare il **PanelViewController.h** file.
8. Creare un'azione per il **documento** voce di menu denominato `propertyDocument`: 

    [![Configurazione dell'azione](menu-images/context06.png "configurazione dell'azione")](menu-images/context06-large.png#lightbox)
9. Ripetere le azioni di creazione per le rimanenti voci di menu: 

    [![Le azioni necessarie indicate](menu-images/context07.png "le azioni necessarie")](menu-images/context07-large.png#lightbox)
10. Creare infine una presa per la **proprietà Label** chiamato `propertyLabel`: 

    [![Configurazione dell'outlet](menu-images/context08.png "la configurazione dell'outlet")](menu-images/context08-large.png#lightbox)
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

Ora se si esegue l'applicazione e fare doppio clic sull'etichetta della proprietà nel pannello, si vedrà il menu di scelta rapida personalizzato. Se si seleziona e di elemento dal menu di scelta, modifica il valore dell'etichetta:

![Il menu di scelta rapida che eseguono](menu-images/context09.png "il menu di scelta rapida in esecuzione")

Successivo verrà ora esaminata la creazione di menu della barra di stato.

## <a name="status-bar-menus"></a>Menu della barra di stato

Menu della barra di stato Visualizza una raccolta di voci di menu dello stato che forniscono l'interazione con o commenti e suggerimenti all'utente, ad esempio un menu o un'immagine di riflettere lo stato di un'applicazione. Menu sulla barra di stato di un'applicazione è abilitato e attivo anche se l'applicazione è in esecuzione in background. La barra di stato a livello di sistema si trova sul lato destro della barra dei menu dell'applicazione ed è l'unica barra di stato attualmente disponibili in macOS.

Modifica della nostro **AppDelegate.cs** del file e verificare il `DidFinishLaunching` metodo aspetto simile al seguente:

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

`NSStatusBar statusBar = NSStatusBar.SystemStatusBar;` ci offre l'accesso alla barra di stato a livello di sistema. `var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);` Crea un nuovo elemento di barra di stato. Da qui è creare un menu di scelta e un numero di voci di menu e allegare il menu di scelta per l'elemento della barra di stato che appena creato. 

Se si esegue l'applicazione, verrà visualizzato il nuovo elemento di barra di stato. Selezione di un elemento dal menu di scelta verrà modificato il testo nella visualizzazione di testo: 

![Il menu sulla barra di stato in esecuzione](menu-images/statusbar01.png "menu barra di stato in esecuzione")

Successivamente, verrà ora esaminata la creazione di voci di menu ancoraggio personalizzato.

## <a name="custom-dock-menus"></a>Menu ancoraggio personalizzati

Quando l'utente fa clic o controllo clic sull'icona dell'applicazione nel dock, viene visualizzato il menu ancoraggio per l'applicazione Mac:

![Menu di ancoraggio di una classe personalizzata](menu-images/dock01.png "personalizzato dal menu di ancoraggio")

È possibile creare un menu ancoraggio personalizzati per la nostra applicazione eseguendo le operazioni seguenti:

1. In Visual Studio per Mac, fare doppio clic sul progetto dell'applicazione e selezionare **Add** > **nuovo File...** Nella finestra di dialogo Nuovo file, selezionare **xamarin. Mac** > **definizione di interfaccia vuota**, usare "DockMenu" per il **nome** e fare clic su di **New**  per creare il nuovo **DockMenu.xib** file:

    ![Aggiunta di una definizione di interfaccia vuota](menu-images/dock02.png "aggiungendo una definizione di interfaccia vuota")
2. Nel **riquadro della soluzione**, fare doppio clic il **DockMenu.xib** file per aprirlo e modificarlo in Xcode. Creare una nuova **dal Menu** con gli elementi seguenti: **indirizzo**, **data**, **Greeting**, e **firma** 

    [![Definizione del layout dell'interfaccia utente](menu-images/dock03.png "definizione del layout dell'interfaccia utente")](menu-images/dock03-large.png#lightbox)
3. Successivamente, ci si connetterà la nuove voci di menu per le azioni esistenti creati per i menu personalizzati nel [aggiunta, modifica e l'eliminazione di menu](#Adding,_Editing_and_Deleting_Menus) sezione precedente. Passare al **connessione Inspector** e selezionare il **risponditore prima** nel **gerarchia delle interfacce**. Scorrere verso il basso e trovare il `phraseAddress:` azione. Trascinare una linea dal controllo circle a tale azione per il **indirizzo** voce di menu:

    [![Consente di associare un'azione di trascinamento](menu-images/dock04.png "trascinando consente di associare un'azione")](menu-images/dock04-large.png#lightbox)
4. Ripetere per tutte le altre voci di menu associandoli alle azioni corrispondenti: 

    [![Le azioni necessarie indicate](menu-images/dock05.png "le azioni necessarie")](menu-images/dock05-large.png#lightbox)
5. Selezionare quindi il **Application** nel **gerarchia delle interfacce**. Nel **connessione Inspector**, trascinare una linea dal controllo circle sul `dockMenu` outlet al menu appena creato:

    [![Trascinando la trasmissione dell'outlet](menu-images/dock06.png "trascinando la trasmissione dell'outlet")](menu-images/dock06-large.png#lightbox)
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.
7. Fare doppio clic il **Info. plist** file per aprirlo e modificarlo: 

    [![Modifica del file Info.plist](menu-images/dock07.png "Modifica del file Info.plist")](menu-images/dock07-large.png#lightbox)
8. Scegliere il **origine** scheda nella parte inferiore della schermata: 

    [![Selezione della vista di origine](menu-images/dock08.png "selezionando la visualizzazione origine")](menu-images/dock08-large.png#lightbox)
9. Fare clic su **Aggiungi una nuova voce**, scegliere il pulsante con segno più verde, impostare il nome della proprietà "AppleDockMenu" e il valore per "DockMenu" (il nome del nostro nuovo file con estensione xib senza l'estensione): 

    [![Aggiungere l'elemento DockMenu](menu-images/dock09.png "aggiungendo l'elemento DockMenu")](menu-images/dock09-large.png#lightbox)

Ora se si esegue la nostra applicazione e fare doppio clic sulla relativa icona nel Dock, verranno visualizzate la nuove voci di menu:

![Un esempio del dock menu che esegue](menu-images/dock10.png "un esempio del menu ancoraggio in esecuzione")

Se si seleziona uno degli elementi personalizzati dal menu di scelta, verrà modificato il testo nella visualizzazione testo.

<a name="Pop-up_Menus_and_Pull-Down_Lists" />

## <a name="pop-up-button-and-pull-down-lists"></a>Pulsante popup ed elenchi a discesa

Un pulsante popup viene visualizzato un elemento selezionato e visualizza un elenco di opzioni per selezionare da quando l'utente fa clic su. Un elenco a discesa è un tipo di pulsante popup in genere utilizzato per la selezione di comandi specifici per il contesto dell'attività corrente. Entrambi possono trovarsi in qualsiasi punto in una finestra.

Creiamo un pulsante personalizzato a comparsa per la nostra applicazione eseguendo le operazioni seguenti:

1. Modificare il **Main. Storyboard** file in Xcode e trascinare un **pulsante Popup** dal **Library Inspector** nel **pannello** finestra creata in il [menu di scelta rapida](#Contextual_Menus) sezione: 

    [![Aggiunta di un pulsante di finestra popup](menu-images/popup01.png "aggiunta di un pulsante di popup")](menu-images/popup01-large.png#lightbox)
2. Aggiungere una nuova voce di menu e impostare i titoli degli elementi nella finestra Popup per: **indirizzi**, **data**, **Greeting**, e **firma** 

    [![Configurare le voci di menu](menu-images/popup02.png "configurazione le voci di menu")](menu-images/popup02-large.png#lightbox)
3. Successivamente, ci si connetterà la nuove voci di menu per le azioni esistenti creati per i menu personalizzati nel [aggiunta, modifica e l'eliminazione di menu](#Adding,_Editing_and_Deleting_Menus) sezione precedente. Passare al **connessione Inspector** e selezionare il **risponditore prima** nel **gerarchia delle interfacce**. Scorrere verso il basso e trovare il `phraseAddress:` azione. Trascinare una linea dal controllo circle a tale azione per il **indirizzo** voce di menu: 

    [![Consente di associare un'azione di trascinamento](menu-images/popup03.png "trascinando consente di associare un'azione")](menu-images/popup03-large.png#lightbox)
4. Ripetere per tutte le altre voci di menu associandoli alle azioni corrispondenti: 

    [![Tutte le azioni necessarie](menu-images/popup04.png "tutte le azioni necessarie")](menu-images/popup04-large.png#lightbox)
5. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Ora se si esegue l'applicazione e selezionare un elemento dal popup della, il testo nella nostra vista testo cambierà:

![Un esempio della finestra popup che eseguono](menu-images/popup05.png "un esempio della finestra popup in esecuzione")

È possibile creare e usare gli elenchi a discesa nel esattamente così come i pulsanti a comparsa. Invece di collegare all'azione esistente, è possibile creare proprie azioni personalizzate, come abbiamo fatto per il menu di scelta rapida nel [menu di scelta rapida](#Contextual_Menus) sezione.

## <a name="summary"></a>Riepilogo

Questo articolo ha fatto un quadro dettagliato di lavorare con i menu e voci di menu in un'applicazione xamarin. Mac. Prima di tutto esaminata barra dei menu dell'applicazione, quindi è stata esaminata la creazione di menu di scelta rapida, quindi sono stati esaminati i menu della barra di stato e custom ancorare i menu. Infine, abbiamo illustrato nei menu a comparsa e gli elenchi a discesa.


## <a name="related-links"></a>Collegamenti correlati

- [MacMenus (esempio)](https://developer.xamarin.com/samples/mac/MacMenus/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Linee guida dell'interfaccia umana - menu](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)
- [Introduzione al menu dell'applicazione e gli elenchi popup](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MenuList/MenuList.html)
