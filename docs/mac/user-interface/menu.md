---
title: Menu in Novell. Mac
description: Questo articolo illustra l'uso dei menu in un'applicazione Novell. Mac. Descrive la creazione e la gestione di menu e voci di menu in Xcode e Interface Builder e l'uso a livello di codice.
ms.prod: xamarin
ms.assetid: 5D367F8E-3A76-4995-8A89-488530FAD802
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 7cca5be2ea13deb17b27e5452df389a998c6eb09
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026176"
---
# <a name="menus-in-xamarinmac"></a>Menu in Novell. Mac

_Questo articolo illustra l'uso dei menu in un'applicazione Novell. Mac. Descrive la creazione e la gestione di menu e voci di menu in Xcode e Interface Builder e l'uso a livello di codice._

Quando si lavora C# con e .NET in un'applicazione Novell. Mac, è possibile accedere agli stessi menu Cocoa che uno sviluppatore lavora in Objective-C e Xcode. Poiché Novell. Mac si integra direttamente con Xcode, è possibile usare Interface Builder di Xcode per creare e gestire le barre dei menu, i menu e le voci di menu oppure, facoltativamente, C# crearli direttamente nel codice.

I menu sono parte integrante dell'esperienza utente di un'applicazione Mac e vengono in genere visualizzati in diverse parti dell'interfaccia utente:

- **La barra dei menu dell'applicazione** , ovvero il menu principale visualizzato nella parte superiore della schermata per ogni applicazione Mac.
- **Menu contestuali** : vengono visualizzati quando l'utente fa clic con il pulsante destro del mouse o controlla un elemento in una finestra.
- **Barra di stato** : si tratta dell'area all'estrema destra della barra dei menu dell'applicazione visualizzata nella parte superiore della schermata, a sinistra del clock della barra dei menu, e cresce a sinistra man mano che gli elementi vengono aggiunti.
- **Menu ancora** : menu per ogni applicazione nel Dock visualizzato quando l'utente fa clic con il pulsante destro del mouse o controlla l'icona dell'applicazione oppure quando l'utente fa clic sull'icona e il pulsante del mouse è premuto.
- **Pulsante a comparsa ed elenchi** a discesa: un pulsante popup Visualizza un elemento selezionato e visualizza un elenco di opzioni da selezionare quando si fa clic sull'utente. Un elenco a discesa è un tipo di pulsante popup usato in genere per selezionare i comandi specifici del contesto dell'attività corrente. Entrambi possono essere presenti in qualsiasi punto di una finestra.

[![Menu di esempio](menu-images/intro01.png "Menu di esempio")](menu-images/intro01-large.png#lightbox)

In questo articolo verranno illustrate le nozioni di base sull'uso delle barre dei menu, dei menu e delle voci di menu di Cocoa in un'applicazione Novell. Mac. Si consiglia di usare prima di tutto l'articolo [Hello, Mac](~/mac/get-started/hello-mac.md) , in particolare l' [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e le sezioni [Outlets and actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , in cui vengono illustrati i concetti chiave e le tecniche che verranno usati in Questo articolo.

Si consiglia di esaminare la sezione [esporre C# classi/metodi in Objective-c](~/mac/internals/how-it-works.md) del documento [interno di Novell. Mac](~/mac/internals/how-it-works.md) , spiegando gli attributi `Register` e `Export` usati per collegare le C# classi a Objective-c. oggetti ed elementi dell'interfaccia utente.

## <a name="the-applications-menu-bar"></a>Barra dei menu dell'applicazione 

Diversamente dalle applicazioni in esecuzione nel sistema operativo Windows in cui a ogni finestra è associata una barra dei menu, ogni applicazione in esecuzione su macOS dispone di una singola barra dei menu che viene eseguita nella parte superiore dello schermo utilizzata per ogni finestra dell'applicazione:

[![Barra dei menu](menu-images/appmenu01.png "Barra dei menu")](menu-images/appmenu01-large.png#lightbox)

Gli elementi in questa barra dei menu vengono attivati o disattivati in base al contesto corrente o allo stato dell'applicazione e alla relativa interfaccia utente in un determinato momento. Ad esempio: se l'utente seleziona un campo di testo, verranno abilitati gli elementi del menu **modifica** , ad esempio **copia** e **taglia**.

In base a Apple e per impostazione predefinita, tutte le applicazioni macOS hanno un insieme standard di menu e voci di menu che vengono visualizzati nella barra dei menu dell'applicazione:

- **Menu Apple** -questo menu consente di accedere agli elementi a livello di sistema disponibili per l'utente in qualsiasi momento, indipendentemente dall'applicazione in esecuzione. Questi elementi non possono essere modificati dallo sviluppatore.
- **Menu app** : questo menu Visualizza il nome dell'applicazione in grassetto e consente all'utente di identificare l'applicazione attualmente in esecuzione. Contiene gli elementi che si applicano all'applicazione nel suo insieme, non un documento o un processo specifico, ad esempio la chiusura dell'applicazione.
- **Menu file** -elementi utilizzati per creare, aprire o salvare documenti con cui l'applicazione funziona. Se l'applicazione non è basata su documenti, questo menu può essere rinominato o rimosso.
- **Menu modifica** : include i comandi, ad esempio **taglia**, **copia**e **Incolla** , usati per modificare o modificare gli elementi nell'interfaccia utente dell'applicazione.
- **Menu formato** : se l'applicazione funziona con testo, questo menu include i comandi per regolare la formattazione del testo.
- **Menu Visualizza** : include i comandi che influiscono sul modo in cui il contenuto viene visualizzato (visualizzato) nell'interfaccia utente dell'applicazione.
- **Menu specifici dell'applicazione** : si tratta di tutti i menu specifici dell'applicazione, ad esempio un menu di segnalibri per un Web browser. Devono essere visualizzati tra i menu **Visualizza** e **finestra** sulla barra.
- **Menu finestra** : contiene i comandi per l'utilizzo di Windows nell'applicazione, nonché un elenco delle finestre aperte correnti.
- **Menu** ?: se l'applicazione fornisce la guida sullo schermo, il menu? dovrebbe essere il menu della barra di destra. 

Per ulteriori informazioni sulla barra dei menu dell'applicazione e sui menu e le voci di menu standard, vedere la pagina relativa alle [linee guida per l'interfaccia umana](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)di Apple.

### <a name="the-default-application-menu-bar"></a>Barra dei menu dell'applicazione predefinita

Ogni volta che si crea un nuovo progetto Novell. Mac, si ottiene automaticamente una barra dei menu dell'applicazione standard e predefinita che contiene gli elementi tipici di un'applicazione macOS (come descritto nella sezione precedente). La barra dei menu predefinita dell'applicazione viene definita nel file **Main. Storyboard** (insieme al resto dell'interfaccia utente dell'app) sotto il progetto nella **riquadro della soluzione**:  

![Selezionare lo storyboard principale](menu-images/appmenu02.png "Selezionare lo storyboard principale")

Fare doppio clic sul file **Main. Storyboard** per aprirlo per la modifica nel Interface Builder di Xcode. verrà visualizzata l'interfaccia dell'editor dei menu:

[![Modifica dell'interfaccia utente in Xcode](menu-images/defaultbar01.png "Modifica dell'interfaccia utente in Xcode")](menu-images/defaultbar01-large.png#lightbox)

Da qui è possibile fare clic su elementi come la voce di menu **Apri** nel menu **file** e modificare o modificare le proprietà nel **controllo attributi**:

[![Modifica degli attributi di un menu](menu-images/defaultbar02.png "Modifica degli attributi di un menu")](menu-images/defaultbar02-large.png#lightbox)

Verranno aggiunti, modificati ed eliminati menu e elementi più avanti in questo articolo. Per ora è sufficiente vedere quali menu e voci di menu sono disponibili per impostazione predefinita e come sono stati esposti automaticamente al codice tramite un set di Outlet e azioni predefiniti (per altre informazioni, vedere la documentazione degli [Outlet e delle azioni](~/mac/get-started/hello-mac.md#outlets-and-actions) ).

Ad esempio, se si fa clic sul **controllo di connessione** per la voce di menu **Apri** , è possibile vedere che viene cablato automaticamente all'azione `openDocument:`: 

[![Visualizzazione dell'azione collegata](menu-images/defaultbar03.png "Visualizzazione dell'azione collegata")](menu-images/defaultbar03-large.png#lightbox)

Se si seleziona il **primo risponditore** nella **gerarchia dell'interfaccia** e si scorre verso il basso nel **controllo della connessione**, verrà visualizzata la definizione dell'azione di `openDocument:` a cui è collegata la voce di menu **Apri** (insieme a diverse altre le azioni predefinite per l'applicazione e non vengono cablate automaticamente ai controlli:

[![Visualizzazione di tutte le azioni associate](menu-images/defaultbar04.png "Visualizzazione di tutte le azioni associate")](menu-images/defaultbar04-large.png#lightbox) 

Perché è importante? Nella sezione successiva verrà illustrato il funzionamento di queste azioni definite automaticamente con altri elementi dell'interfaccia utente Cocoa per abilitare e disabilitare automaticamente le voci di menu, nonché per fornire funzionalità predefinite per gli elementi.

In seguito verranno usate le azioni predefinite per abilitare e disabilitare gli elementi dal codice e fornire funzionalità personalizzate quando vengono selezionate.

<a name="Built-In_Menu_Functionality" />

### <a name="built-in-menu-functionality"></a>Funzionalità del menu predefinito

Se è stata eseguita un'applicazione Novell. Mac appena creata prima di aggiungere elementi o codice dell'interfaccia utente, si noterà che alcuni elementi vengono automaticamente cablati e abilitati per l'utente (con funzionalità completamente integrate automaticamente), ad esempio l'elemento **Quit** nelMenu dell'app:

![Una voce di menu abilitata](menu-images/appmenu03.png "Una voce di menu abilitata")

Mentre altre voci di menu, ad esempio **taglia**, **copia**e **Incolla** , non sono:

![Voci di menu disabilitate](menu-images/appmenu04.png "Voci di menu disabilitate")

Arrestare l'applicazione e fare doppio clic sul file **Main. Storyboard** nel **riquadro della soluzione** per aprirlo per la modifica nella Interface Builder di Xcode. Trascinare quindi una **visualizzazione di testo** dalla **libreria** sul controller di visualizzazione della finestra nell'editor di **interfaccia**:

[![Selezione di una visualizzazione di testo dalla libreria](menu-images/appmenu05.png "Selezione di una visualizzazione di testo dalla libreria")](menu-images/appmenu05-large.png#lightbox)

Nell' **Editor dei vincoli** aggiungere la visualizzazione di testo ai bordi della finestra e impostarla nel punto in cui si espande e si riduce con la finestra facendo clic su tutti e quattro i-Beam rossi nella parte superiore dell'editor e facendo clic sul pulsante **Aggiungi 4 vincoli** :

[![Modifica dei conformi](menu-images/appmenu06.png "Modifica dei conformi")](menu-images/appmenu06-large.png#lightbox)

Salvare le modifiche apportate alla progettazione dell'interfaccia utente e tornare alla Visual Studio per Mac per sincronizzare le modifiche con il progetto Novell. Mac. A questo punto, avviare l'applicazione, digitare del testo nella visualizzazione di testo, selezionarla e aprire il menu **modifica** :

![Le voci di menu vengono abilitate/disabilitate automaticamente](menu-images/appmenu07.png "Le voci di menu vengono abilitate/disabilitate automaticamente")

Si noti che gli elementi **taglia**, **copia**e **Incolla** vengono automaticamente abilitati e completamente funzionanti, senza dover scrivere una sola riga di codice. 

Che cosa succede qui? Tenere presente che le azioni predefinite predefinite che vengono cablate fino alle voci di menu predefinite, come illustrato in precedenza, la maggior parte degli elementi dell'interfaccia utente di Cocoa che fanno parte di macOS hanno incorporato hook per azioni specifiche, ad esempio `copy:`. Quindi, quando vengono aggiunti a una finestra, attiva e selezionata, la voce di menu corrispondente o gli elementi collegati a tale azione vengono abilitati automaticamente. Se l'utente seleziona tale voce di menu, la funzionalità incorporata nell'elemento dell'interfaccia utente viene chiamata ed eseguita senza alcun intervento da parte dello sviluppatore.

### <a name="enabling-and-disabling-menus-and-items"></a>Abilitazione e disabilitazione di menu ed elementi

Per impostazione predefinita, ogni volta che si verifica un evento utente, `NSMenu` Abilita e Disabilita automaticamente ogni menu visibile e voce di menu in base al contesto dell'applicazione. Sono disponibili tre modi per abilitare o disabilitare un elemento:

- **Abilitazione automatica dei menu** -una voce di menu è abilitata se `NSMenu` possibile trovare un oggetto appropriato che risponde all'azione a cui l'elemento è connesso. Ad esempio, la visualizzazione di testo precedente con un hook incorporato all'azione `copy:`.
- **Azioni personalizzate e validateMenuItem:** per qualsiasi voce di menu associata a un' [azione personalizzata del controller di finestra o di visualizzazione](#Working-with-Custom-Window-Actions), è possibile aggiungere l'azione `validateMenuItem:` e abilitare o disabilitare manualmente le voci di menu.
- **Abilitazione manuale del menu** : è possibile impostare manualmente la proprietà `Enabled` di ogni `NSMenuItem` per abilitare o disabilitare individualmente ogni elemento in un menu.

Per scegliere un sistema, impostare la proprietà `AutoEnablesItems` di un `NSMenu`. `true` è automatico (comportamento predefinito) e `false` è manuale. 

> [!IMPORTANT]
> Se si sceglie di usare l'abilitazione manuale dei menu, nessuna delle voci di menu, anche quelle controllate dalle classi AppKit come `NSTextView`, vengono aggiornate automaticamente. L'utente sarà responsabile dell'abilitazione e disabilitazione manuale di tutti gli elementi nel codice.

#### <a name="using-validatemenuitem"></a>Uso di validateMenuItem

Come indicato in precedenza, per qualsiasi voce di menu associata a un' [azione personalizzata della finestra o del controller di visualizzazione](#Working-with-Custom-Window-Actions), è possibile aggiungere l'azione `validateMenuItem:` e attivare o disattivare manualmente le voci di menu.

Nell'esempio seguente, la proprietà `Tag` verrà usata per stabilire il tipo di voce di menu che verrà abilitata o disabilitata dall'azione `validateMenuItem:` in base allo stato del testo selezionato in un `NSTextView`. La proprietà `Tag` è stata impostata in Interface Builder per ogni voce di menu:

![Impostazione della proprietà Tag](menu-images/validate01.png "Impostazione della proprietà Tag")

E il codice seguente è stato aggiunto al controller di visualizzazione:

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

Quando questo codice viene eseguito e non è selezionato alcun testo nella `NSTextView`, le due voci di menu a capo sono disabilitate (anche se sono collegate a azioni sul controller di visualizzazione):

![Visualizzazione degli elementi disabilitati](menu-images/validate02.png "Visualizzazione degli elementi disabilitati")

Se si seleziona una sezione di testo e il menu viene riaperto, le due voci del menu a capo saranno disponibili:

![Visualizzazione di elementi abilitati](menu-images/validate03.png "Visualizzazione di elementi abilitati")

## <a name="enabling-and-responding-to-menu-items-in-code"></a>Abilitazione e risposta alle voci di menu nel codice

Come è stato illustrato in precedenza, con l'aggiunta di elementi specifici dell'interfaccia utente Cocoa alla progettazione dell'interfaccia utente, ad esempio un campo di testo, diverse voci di menu predefinite verranno abilitate e funzioneranno automaticamente, senza dover scrivere codice. Verrà ora esaminato come aggiungere il C# codice al progetto Novell. Mac per abilitare una voce di menu e fornire funzionalità quando l'utente la seleziona.

Ad esempio, supponiamo di volere che l'utente sia in grado di usare l'elemento **aperto** nel menu **file** per selezionare una cartella. Poiché si vuole che questa sia una funzione a livello di applicazione e non limitata a una finestra di give o a un elemento dell'interfaccia utente, aggiungeremo il codice per gestirlo nel delegato dell'applicazione.

Nella **riquadro della soluzione**fare doppio clic sul file `AppDelegate.CS` per aprirlo per la modifica:

![Selezione del delegato dell'app](menu-images/appmenu08.png "Selezione del delegato dell'app")

Aggiungere il codice seguente sotto il metodo `DidFinishLaunching`:

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

A questo punto, eseguire l'applicazione e aprire il menu **file** : 

![Menu file](menu-images/appmenu09.png "Menu file")

Si noti che la voce di menu **Apri** è ora abilitata. Se la si seleziona, verrà visualizzata la finestra di dialogo Apri:

![Finestra di dialogo Apri](menu-images/appmenu10.png "Finestra di dialogo Apri")

Se si fa clic sul pulsante **Apri** , verrà visualizzato il messaggio di avviso:

![Un messaggio di dialogo di esempio](menu-images/appmenu11.png "Un messaggio di dialogo di esempio")

La riga chiave è `[Export ("openDocument:")]`, indica `NSMenu` che la **AppDelegate** ha un metodo `void OpenDialog (NSObject sender)` che risponde all'azione `openDocument:`. Se si ricorda quanto sopra indicato, la voce di menu **Apri** viene automaticamente collegata a questa azione per impostazione predefinita in Interface Builder:

[![Visualizzazione delle azioni associate](menu-images/defaultbar03.png "Visualizzazione delle azioni associate")](menu-images/defaultbar03-large.png#lightbox)

A questo punto si osserverà la creazione di menu, voci di menu e azioni e la relativa risposta nel codice.

### <a name="working-with-the-open-recent-menu"></a>Uso del menu Apri recenti

Per impostazione predefinita, il menu **file** contiene un elemento **recente aperto** che tiene traccia degli ultimi file aperti dall'utente con l'app. Se si sta creando un'app Novell. Mac basata su `NSDocument`, questo menu verrà gestito automaticamente. Per qualsiasi altro tipo di app Novell. Mac, l'utente sarà responsabile della gestione e della risposta manualmente a questa voce di menu.

Per gestire manualmente il menu **Apri recenti** , prima di tutto è necessario informare l'utente che è stato aperto o salvato un nuovo file usando quanto segue:

```csharp
// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

Anche se l'app non usa `NSDocuments`, è ancora possibile usare il `NSDocumentController` per mantenere il menu **Apri recenti** inviando una `NSUrl` con il percorso del file al metodo `NoteNewRecentDocumentURL` del `SharedDocumentController`.

Successivamente, è necessario eseguire l'override del metodo `OpenFile` del delegato dell'app per aprire tutti i file selezionati dall'utente dal menu **Apri recenti** . Esempio:

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

Restituisce `true` se il file può essere aperto. in caso contrario, restituisce `false` e viene visualizzato un avviso predefinito per l'utente che non è stato possibile aprire il file.

Poiché il nome file e il percorso restituiti dal menu **Apri recenti** possono includere uno spazio, è necessario eseguire il escape di questo carattere prima di creare un `NSUrl` o verrà visualizzato un errore. Questa operazione viene eseguita con il codice seguente:

```csharp
filename = filename.Replace (" ", "%20");
```

Infine, viene creato un `NSUrl` che punta al file e si usa un metodo helper nel delegato dell'app per aprire una nuova finestra e caricarvi il file:

```csharp
var url = new NSUrl ("file://"+filename);
return OpenFile(url);
```

Per eseguire il pull di tutti gli elementi, viene ora esaminata un'implementazione di esempio in un file **AppDelegate.cs** :

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

In base ai requisiti dell'app, potrebbe non essere necessario che l'utente apra lo stesso file in più di una finestra nello stesso momento. Nell'app di esempio, se l'utente sceglie un file già aperto (dall'apertura **recente** o **Apri...** voci di menu), la finestra che contiene il file viene portata in primo piano.

A tale scopo, nel metodo helper è stato usato il codice seguente:

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

La classe `ViewController` è stata progettata in modo da conservare il percorso del file nella relativa proprietà `Path`. A questo punto si scorre in ciclo tutte le finestre attualmente aperte nell'app. Se il file è già aperto in una delle finestre, viene portato all'inizio di tutte le altre finestre utilizzando:

```csharp
NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
```

Se non viene trovata alcuna corrispondenza, viene aperta una nuova finestra con il file caricato e il file viene indicato nel menu **Apri recenti** :

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

### <a name="working-with-custom-window-actions"></a>Utilizzo delle azioni finestra personalizzate

Analogamente alle azioni di **primo risponditore** predefinite che vengono precablate a voci di menu standard, è possibile creare nuove azioni personalizzate e collegarle alle voci di menu in Interface Builder.

Per prima cosa, definire un'azione personalizzata su uno dei controller della finestra dell'app. Esempio:

```csharp
[Action("defineKeyword:")]
public void defineKeyword (NSObject sender) {
    // Preform some action when the menu is selected
    Console.WriteLine ("Request to define keyword");
}
```

Fare quindi doppio clic sul file dello storyboard dell'app nella **riquadro della soluzione** per aprirlo per la modifica nella Interface Builder di Xcode. Selezionare il **primo risponditore** nella **scena dell'applicazione**, quindi passare al **controllo attributi**:

![Controllo attributi](menu-images/action01.png "Controllo attributi")

Fare clic sul pulsante **+** nella parte inferiore del **controllo attributi** per aggiungere una nuova azione personalizzata:

![Aggiunta di una nuova azione](menu-images/action02.png "Aggiunta di una nuova azione")

Assegnargli lo stesso nome dell'azione personalizzata creata nel controller della finestra:

![Modifica del nome dell'azione](menu-images/action03.png "Modifica del nome dell'azione")

Controllo: fare clic e trascinare da una voce di menu al **primo risponditore** nella **scena dell'applicazione**. Dall'elenco popup selezionare la nuova azione appena creata (`defineKeyword:` in questo esempio):

![Associazione di un'azione](menu-images/action04.png "Associazione di un'azione")

Salvare le modifiche apportate allo storyboard e tornare a Visual Studio per Mac per sincronizzare le modifiche. Se si esegue l'app, la voce di menu a cui è stata connessa l'azione personalizzata verrà abilitata o disabilitata automaticamente (in base alla finestra con l'azione aperta) e la selezione della voce di menu comporterà l'interruzione dell'azione:

[![Test della nuova azione](menu-images/action05.png "Test della nuova azione")](menu-images/action05-large.png#lightbox)

<a name="Adding,_Editing_and_Deleting_Menus" />

### <a name="adding-editing-and-deleting-menus"></a>Aggiunta, modifica ed eliminazione di menu

Come è stato illustrato nelle sezioni precedenti, un'applicazione Novell. Mac include un numero preimpostato di menu e voci di menu predefiniti a cui i controlli dell'interfaccia utente specifici verranno attivati e risponderanno automaticamente. È stato anche illustrato come aggiungere codice all'applicazione che consentirà e risponderà anche a questi elementi predefiniti.

In questa sezione verrà esaminata la rimozione delle voci di menu non necessarie, la riorganizzazione dei menu e l'aggiunta di nuovi menu, voci di menu e azioni.

Fare doppio clic sul file **Main. Storyboard** nel **riquadro della soluzione** per aprirlo per la modifica:

[![Modifica dell'interfaccia utente in Xcode](menu-images/maint01.png "Modifica dell'interfaccia utente in Xcode")](menu-images/maint01-large.png#lightbox)

Per questa specifica applicazione Novell. Mac non verrà usato il menu **visualizzazione** predefinito, quindi verrà rimosso. Nella **gerarchia di interfaccia** selezionare la voce di menu **Visualizza** che fa parte della barra dei menu principale:

![Selezione della voce di menu Visualizza](menu-images/maint02.png "Selezione della voce di menu Visualizza")

Premere CANC o BACKSPACE per eliminare il menu. Quindi, non utilizzeremo tutti gli elementi del menu **formato** e vogliamo spostare gli elementi da usare in base ai menu sub (sottomenu). Nella **gerarchia dell'interfaccia** selezionare le voci di menu seguenti:

![Evidenziazione di più elementi](menu-images/maint03.png "Evidenziazione di più elementi")

Trascinare gli elementi nel **menu** padre dal sottomenu dove sono attualmente:

[![Il trascinamento delle voci di menu nel menu padre](menu-images/maint04.png "Il trascinamento delle voci di menu nel menu padre")](menu-images/maint04-large.png#lightbox)

Il menu sarà ora simile al seguente:

[![Gli elementi nella nuova posizione](menu-images/maint05.png "Gli elementi nella nuova posizione")](menu-images/maint05-large.png#lightbox)

Trascinare quindi il sottomenu **testo** dal menu **formato** e posizionarlo sulla barra dei menu principale tra i menu **formato** e **finestra** :

[![Menu di testo](menu-images/maint06.png "Menu di testo")](menu-images/maint06-large.png#lightbox)

Tornare al menu **formato** ed eliminare la voce del sottomenu del **tipo di carattere** . Selezionare quindi il menu **formato** e rinominarlo "tipo di carattere":

[![Menu dei tipi di carattere](menu-images/maint07.png "Menu dei tipi di carattere")](menu-images/maint07-large.png#lightbox)

A questo punto, creare un menu personalizzato di frasi predefine che verrà automaticamente aggiunto al testo nella visualizzazione di testo quando vengono selezionate. Nella casella di ricerca nella parte inferiore di **Inspector Library** Digitare "menu". In questo modo sarà più semplice trovare e utilizzare tutti gli elementi dell'interfaccia utente di menu:

![Controllo libreria](menu-images/maint08.png "Controllo libreria")

A questo punto, è possibile eseguire le operazioni seguenti per creare il menu:

1. Trascinare una **voce di menu** dal **controllo libreria** sulla barra dei menu tra i menu **testo** e **finestra** : 

    ![Selezione di una nuova voce di menu nella libreria](menu-images/maint10.png "Selezione di una nuova voce di menu nella libreria")
2. Rinominare l'elemento "phrases": 

    [![Impostazione del nome del menu](menu-images/maint09.png "Impostazione del nome del menu")](menu-images/maint09-large.png#lightbox)
3. Trascinare quindi un **menu** da **controllo libreria**: 

    ![Selezione di un menu dalla libreria](menu-images/maint11.png "Selezione di un menu dalla libreria")
4. Rilasciare quindi **menu** nella nuova **voce di menu** appena creata e modificare il nome in "frasi": 

    [![Modifica del nome del menu](menu-images/maint12.png "Modifica del nome del menu")](menu-images/maint12-large.png#lightbox)
5. Rinominare ora le tre **voci di menu** predefinite "Address", "date" e "Greeting": 

    [![Menu frasi](menu-images/maint13.png "Menu frasi")](menu-images/maint13-large.png#lightbox)
6. Verrà ora aggiunta una quarta **voce di menu** trascinando una **voce di menu** dal **controllo libreria** e chiamandola "Signature": 

    [![Modifica del nome della voce di menu](menu-images/maint14.png "Modifica del nome della voce di menu")](menu-images/maint14-large.png#lightbox)
7. Salvare le modifiche apportate alla barra dei menu.

A questo punto è possibile creare un set di azioni personalizzate in modo che le nuove voci di C# menu siano esposte al codice. In Xcode passare alla visualizzazione **Assistente** :

[![Creazione delle azioni necessarie](menu-images/maint15.png "Creazione delle azioni necessarie")](menu-images/maint15-large.png#lightbox)

Eseguire le operazioni seguenti:

1. Controllo: trascinare dalla voce di menu **Indirizzo** al file **AppDelegate. h** .
2. Modificare il tipo di **connessione** in **azione**: 

    [![Selezione del tipo di azione](menu-images/maint17.png "Selezione del tipo di azione")](menu-images/maint17-large.png#lightbox)
3. Immettere il **nome** "phraseAddress" e premere il pulsante **Connetti** per creare la nuova azione: 

    [![Configurazione dell'azione](menu-images/maint18.png "Configurazione dell'azione")](menu-images/maint18-large.png#lightbox)
4. Ripetere i passaggi precedenti per le voci di menu **Data**, **saluto**e **firma** : 

    [![Azioni completate](menu-images/maint19.png "Azioni completate")](menu-images/maint19-large.png#lightbox)
5. Salvare le modifiche apportate alla barra dei menu.

Successivamente, è necessario creare un outlet per la visualizzazione di testo in modo che sia possibile modificare il contenuto dal codice. Selezionare il file **ViewController. h** nell' **editor di assistente** e creare un nuovo outlet denominato `documentText`:

[![Creazione di un Outlet](menu-images/maint20.png "Creazione di un Outlet")](menu-images/maint20-large.png#lightbox)

Tornare a Visual Studio per Mac per sincronizzare le modifiche da Xcode. Successivamente, modificare il file **ViewController.cs** e renderlo simile al seguente:

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

In questo modo viene esposto il testo della visualizzazione testo all'esterno della classe `ViewController` e il delegato dell'app viene informata quando la finestra acquisisce o perde lo stato attivo. A questo punto, modificare il file **AppDelegate.cs** e renderlo simile al seguente:

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

Qui abbiamo creato la `AppDelegate` una classe parziale in modo che sia possibile usare le azioni e gli outlet definiti nel Interface Builder. Viene inoltre esposto un `textEditor` per tenere traccia della finestra attualmente attiva.

Per gestire le voci di menu e menu personalizzati, vengono usati i metodi seguenti:

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

A questo punto, se si esegue l'applicazione, tutti gli elementi nel menu **frase** saranno attivi e aggiungere la frase Give alla visualizzazione di testo quando viene selezionata:

![Esempio di app in esecuzione](menu-images/maint21.png "Esempio di app in esecuzione")

Ora che sono state apportate le nozioni di base sull'uso della barra dei menu dell'applicazione, si osservi la creazione di un menu contestuale personalizzato.

### <a name="creating-menus-from-code"></a>Creazione di menu dal codice

Oltre a creare menu e voci di menu con Interface Builder di Xcode, è possibile che in alcuni casi un'app Novell. Mac debba creare, modificare o rimuovere un menu, un sottomenu o una voce di menu dal codice.

Nell'esempio seguente viene creata una classe per contenere le informazioni sulle voci di menu e i sottomenu che verranno creati in modo dinamico in tempo reale:

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

Con questa classe definita, la routine seguente analizzerà una raccolta di `LanguageFormatCommand`objects e compilerà in modo ricorsivo nuovi menu e voci di menu, aggiungendoli alla fine del menu esistente (creato in Interface Builder) passato:

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

Per qualsiasi `LanguageFormatCommand` oggetto con una proprietà `Title` vuota, questa routine crea una **voce di menu separatore** (una linea grigia sottile) tra le sezioni di menu:

```csharp
menuItem = NSMenuItem.SeparatorItem;
```

Se viene specificato un titolo, viene creata una nuova voce di menu con tale titolo:

```csharp
menuItem = new NSMenuItem (command.Title);
``` 

Se l'oggetto `LanguageFormatCommand` contiene oggetti `LanguageFormatCommand` figlio, viene creato un sottomenu e il metodo `AssembleMenu` viene chiamato in modo ricorsivo per compilare tale menu:

```csharp
menuItem.Submenu = new NSMenu (command.Title);
AssembleMenu (menuItem.Submenu, command.SubCommands);
```

Per qualsiasi nuova voce di menu che non include sottomenu, viene aggiunto codice per gestire la voce di menu selezionata dall'utente:

```csharp
menuItem.Activated += (sender, e) => {
    // Do something when the menu item is selected
    ...
};
```

#### <a name="testing-the-menu-creation"></a>Test della creazione di menu

Con tutto il codice riportato sopra, se è stata creata la seguente raccolta di oggetti `LanguageFormatCommand`:

```csharp
// Define formatting commands
FormattingCommands.Add(new LanguageFormatCommand("Strong","**","**"));
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
FormattingCommands.Add(new LanguageFormatCommand("Image","![](",")"));
FormattingCommands.Add(new LanguageFormatCommand("Image Link","[![](",")](LinkImageHere)"));
```

E la raccolta passata alla funzione `AssembleMenu` (con il menu **formato** impostato come base), verranno creati i menu dinamici e le voci di menu seguenti:

![Nuove voci di menu nell'app in esecuzione](menu-images/dynamic01.png "Nuove voci di menu nell'app in esecuzione")

#### <a name="removing-menus-and-items"></a>Rimozione di menu ed elementi

Se è necessario rimuovere qualsiasi menu o voce di menu dall'interfaccia utente dell'app, è possibile usare il metodo `RemoveItemAt` della classe `NSMenu` semplicemente assegnando a esso l'indice in base zero dell'elemento da rimuovere.

Ad esempio, per rimuovere i menu e le voci di menu creati dalla routine precedente, è possibile usare il codice seguente:

```csharp
public void UnpopulateFormattingMenu(NSMenu menu) {

    // Remove any additional items
    for (int n = (int)menu.Count - 1; n > 4; --n) {
        menu.RemoveItemAt (n);
    }
}
```

Nel caso del codice precedente, le prime quattro voci di menu vengono create nel Interface Builder di Xcode e sono disponibili nell'app, in modo che non vengano rimosse dinamicamente.

<a name="Contextual_Menus" />

## <a name="contextual-menus"></a>Menu contestuali

I menu contestuali vengono visualizzati quando l'utente fa clic con il pulsante destro del mouse o controlla un elemento in una finestra. Per impostazione predefinita, molti degli elementi dell'interfaccia utente incorporati in macOS dispongono già di menu contestuali collegati, ad esempio la visualizzazione di testo. Tuttavia, in alcuni casi potrebbe essere necessario creare menu contestuali personalizzati per un elemento dell'interfaccia utente che è stato aggiunto a una finestra.

Modificare il file **Main. Storyboard** in Xcode e aggiungere una finestra della **finestra** alla progettazione, impostare la relativa **classe** su "NSPanel" in **Identity Inspector**, aggiungere un nuovo elemento **Assistant** al menu **finestra** e collegarlo alla nuova finestra con un **segue di visualizzazione**:

[![Impostazione del tipo segue](menu-images/context01.png "Impostazione del tipo segue")](menu-images/context01-large.png#lightbox)

Eseguire le operazioni seguenti:

1. Trascinare un' **etichetta** da **controllo libreria** nella finestra del **Pannello** e impostarne il testo su "Property": 

    [![Modifica del valore dell'etichetta](menu-images/context03.png "Modifica del valore dell'etichetta")](menu-images/context03-large.png#lightbox)
2. Trascinare quindi un **menu** dal controllo **libreria** sul controller di visualizzazione nella gerarchia di visualizzazione e rinominare le tre voci di menu predefinite **documento**, **testo** e **carattere**:

    [![Voci di menu obbligatorie](menu-images/context02.png "Voci di menu obbligatorie")](menu-images/context02-large.png#lightbox)
3. A questo punto, controllare-trascinare dall' **etichetta della proprietà** nel **menu**:

    [![Trascinamento per creare un segue](menu-images/context04.png "Trascinamento per creare un segue")](menu-images/context04-large.png#lightbox)
4. Nella finestra di dialogo popup selezionare **menu**: 

    ![Impostazione del tipo segue](menu-images/context05.png "Impostazione del tipo segue")
5. Dal **controllo di identità**impostare la classe del controller di visualizzazione su "PanelViewController": 

    [![Impostazione della classe segue](menu-images/context10.png "Impostazione della classe segue")](menu-images/context10-large.png#lightbox)
6. Tornare a Visual Studio per Mac per sincronizzarlo, quindi tornare a Interface Builder.
7. Passare all' **editor di assistente** e selezionare il file **PanelViewController. h** .
8. Creare un'azione per la voce di menu del **documento** denominata `propertyDocument`: 

    [![Configurazione dell'azione](menu-images/context06.png "Configurazione dell'azione")](menu-images/context06-large.png#lightbox)
9. Ripetere la creazione di azioni per le voci di menu rimanenti: 

    [![Azioni necessarie](menu-images/context07.png "Azioni necessarie")](menu-images/context07-large.png#lightbox)
10. Infine, creare un outlet per l' **etichetta della proprietà** denominata `propertyLabel`: 

    [![Configurazione dell'Outlet](menu-images/context08.png "Configurazione dell'Outlet")](menu-images/context08-large.png#lightbox)
11. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Modificare il file **PanelViewController.cs** e aggiungere il codice seguente:

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

A questo punto, se si esegue l'applicazione e si fa clic con il pulsante destro del mouse sull'etichetta della proprietà nel pannello, verrà visualizzato il menu contestuale personalizzato. Se si seleziona l'elemento e dal menu, il valore dell'etichetta cambierà:

![Menu contestuale che esegue](menu-images/context09.png "Menu contestuale che esegue")

Verrà ora esaminato come creare i menu della barra di stato.

## <a name="status-bar-menus"></a>Menu della barra di stato

I menu della barra di stato visualizzano una raccolta di voci di menu di stato che forniscono all'utente interazioni o commenti, ad esempio un menu o un'immagine che riflette lo stato di un'applicazione. Il menu della barra di stato di un'applicazione è abilitato e attivo anche se l'applicazione è in esecuzione in background. La barra di stato a livello di sistema risiede sul lato destro della barra dei menu dell'applicazione ed è l'unica barra di stato attualmente disponibile in macOS.

Modificare il file **AppDelegate.cs** e rendere il metodo `DidFinishLaunching` simile al seguente:

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

`NSStatusBar statusBar = NSStatusBar.SystemStatusBar;` consente di accedere alla barra di stato a livello di sistema. `var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);` crea un nuovo elemento della barra di stato. Da qui viene creato un menu e un numero di voci di menu e il menu viene collegato all'elemento della barra di stato appena creato. 

Se si esegue l'applicazione, verrà visualizzato il nuovo elemento della barra di stato. Se si seleziona un elemento dal menu, il testo viene modificato nella visualizzazione di testo: 

![Il menu della barra di stato che esegue](menu-images/statusbar01.png "Il menu della barra di stato che esegue")

Successivamente, verrà ora esaminata la creazione di voci di menu dock personalizzato.

## <a name="custom-dock-menus"></a>Menu di ancoraggio personalizzati

Il menu Dock viene visualizzato per l'applicazione Mac quando l'utente fa clic con il pulsante destro del mouse o controlla l'icona dell'applicazione nel Dock:

![Menu dock personalizzato](menu-images/dock01.png "Menu dock personalizzato")

Per creare un menu dock personalizzato per l'applicazione, eseguire le operazioni seguenti:

1. In Visual Studio per Mac fare clic con il pulsante destro del mouse sul progetto dell'applicazione e scegliere **aggiungi**  > **nuovo file...** Nella finestra di dialogo nuovo file selezionare **Novell. Mac**  > **definizione di interfaccia vuota**, usare "dockmenu" come **nome** e fare clic sul pulsante **nuovo** per creare il nuovo file **dockmenu. xib** :

    ![Aggiunta di una definizione di interfaccia vuota](menu-images/dock02.png "Aggiunta di una definizione di interfaccia vuota")
2. Nel **riquadro della soluzione**fare doppio clic sul file **dockmenu. xib** per aprirlo per la modifica in Xcode. Crea un nuovo **menu** con gli elementi seguenti: **Indirizzo**, **Data**, **saluto**e **firma** 

    [![Layout dell'interfaccia utente](menu-images/dock03.png "Layout dell'interfaccia utente")](menu-images/dock03-large.png#lightbox)
3. Successivamente, connettere le nuove voci di menu alle azioni esistenti create per il menu personalizzato nella sezione [aggiunta, modifica ed eliminazione](#Adding,_Editing_and_Deleting_Menus) dei menu precedente. Passare a **controllo connessione** e selezionare il **primo risponditore** nella gerarchia dell' **interfaccia**. Scorrere verso il basso e individuare l'azione `phraseAddress:`. Trascinare una linea dal cerchio sull'azione alla voce di menu **Indirizzo** :

    [![Trascinamento per collegare un'azione](menu-images/dock04.png "Trascinamento per collegare un'azione")](menu-images/dock04-large.png#lightbox)
4. Ripetere l'operazione per tutte le altre voci di menu che li collegano alle azioni corrispondenti: 

    [![Azioni necessarie](menu-images/dock05.png "Azioni necessarie")](menu-images/dock05-large.png#lightbox)
5. Successivamente, selezionare l' **applicazione** nella **gerarchia dell'interfaccia**. Nel **controllo della connessione**trascinare una linea dal cerchio nella `dockMenu` Outlet al menu appena creato:

    [![Trascinando la rete in uscita](menu-images/dock06.png "Trascinando la rete in uscita")](menu-images/dock06-large.png#lightbox)
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.
7. Fare doppio clic sul file **info. plist** per aprirlo per la modifica: 

    [![Modifica del file INFO. plist](menu-images/dock07.png "Modifica del file INFO. plist")](menu-images/dock07-large.png#lightbox)
8. Fare clic sulla scheda **origine** nella parte inferiore della schermata: 

    [![Selezione della visualizzazione origine](menu-images/dock08.png "Selezione della visualizzazione origine")](menu-images/dock08-large.png#lightbox)
9. Fare clic su **Aggiungi nuova voce**, fare clic sul pulsante con il segno più verde, impostare il nome della proprietà su "AppleDockMenu" e il valore su "dockmenu" (il nome del nuovo file con estensione XIB senza estensione): 

    [![Aggiunta dell'elemento DockMenu](menu-images/dock09.png "Aggiunta dell'elemento DockMenu")](menu-images/dock09-large.png#lightbox)

A questo punto, se si esegue l'applicazione e si fa clic con il pulsante destro del mouse sull'icona nel Dock, verranno visualizzate le nuove voci di menu:

![Un esempio del menu dock che esegue](menu-images/dock10.png "Un esempio del menu dock che esegue")

Se si seleziona uno degli elementi personalizzati dal menu, il testo nella visualizzazione di testo verrà modificato.

<a name="Pop-up_Menus_and_Pull-Down_Lists" />

## <a name="pop-up-button-and-pull-down-lists"></a>Pulsante popup ed elenchi a discesa

Un pulsante popup Visualizza un elemento selezionato e visualizza un elenco di opzioni tra cui selezionare quando viene fatto clic dall'utente. Un elenco a discesa è un tipo di pulsante popup usato in genere per selezionare i comandi specifici del contesto dell'attività corrente. Entrambi possono essere presenti in qualsiasi punto di una finestra.

Viene ora creato un pulsante popup personalizzato per l'applicazione eseguendo le operazioni seguenti:

1. Modificare il file **Main. Storyboard** in Xcode e trascinare un **pulsante popup** dal **controllo libreria** nella finestra del **Pannello** creata nella sezione dei [menu contestuali](#Contextual_Menus) : 

    [![Aggiunta di un pulsante popup](menu-images/popup01.png "Aggiunta di un pulsante popup")](menu-images/popup01-large.png#lightbox)
2. Aggiungere una nuova voce di menu e impostare i titoli degli elementi nel popup su: **Indirizzo**, **Data**, **saluto**e **firma** 

    [![Configurazione delle voci di menu](menu-images/popup02.png "Configurazione delle voci di menu")](menu-images/popup02-large.png#lightbox)
3. Successivamente, connettere le nuove voci di menu alle azioni esistenti create per il menu personalizzato nella sezione [aggiunta, modifica ed eliminazione](#Adding,_Editing_and_Deleting_Menus) dei menu precedente. Passare a **controllo connessione** e selezionare il **primo risponditore** nella gerarchia dell' **interfaccia**. Scorrere verso il basso e individuare l'azione `phraseAddress:`. Trascinare una linea dal cerchio sull'azione alla voce di menu **Indirizzo** : 

    [![Trascinamento per collegare un'azione](menu-images/popup03.png "Trascinamento per collegare un'azione")](menu-images/popup03-large.png#lightbox)
4. Ripetere l'operazione per tutte le altre voci di menu che li collegano alle azioni corrispondenti: 

    [![Tutte le azioni necessarie](menu-images/popup04.png "Tutte le azioni necessarie")](menu-images/popup04-large.png#lightbox)
5. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

A questo punto, se si esegue l'applicazione e si seleziona un elemento dal popup, il testo nella visualizzazione di testo verrà modificato:

![Esempio di popup che esegue](menu-images/popup05.png "Esempio di popup che esegue")

È possibile creare e utilizzare elenchi a discesa esattamente come i pulsanti popup. Anziché connettersi a un'azione esistente, è possibile creare azioni personalizzate come per il menu contestuale nella sezione dei [menu contestuali](#Contextual_Menus) .

## <a name="summary"></a>Riepilogo

Questo articolo ha esaminato in dettaglio l'uso di menu e voci di menu in un'applicazione Novell. Mac. Per prima cosa è stata esaminata la barra dei menu dell'applicazione, quindi è stato esaminato come creare menu contestuali, quindi sono stati esaminati i menu della barra di stato e i menu dock personalizzato. Infine, abbiamo analizzato i menu a comparsa e gli elenchi a discesa.

## <a name="related-links"></a>Collegamenti correlati

- [MacMenus (esempio)](https://docs.microsoft.com/samples/xamarin/mac-samples/macmenus)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Linee guida per l'interfaccia umana-menu](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)
- [Introduzione ai menu delle applicazioni e agli elenchi popup](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MenuList/MenuList.html)
