---
title: file con estensione xib in xamarin. Mac
description: Questo articolo descrive l'uso con i file con estensione xib creati in Interface Builder di Xcode per creare e gestire le interfacce utente per un'applicazione xamarin. Mac.
ms.prod: xamarin
ms.assetid: 6AF3D216-448D-4B2D-9026-74E4FFF5923A
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 45eeee745b133646aef0f775bc879fa6a5d867c7
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61062912"
---
# <a name="xib-files-in-xamarinmac"></a>file con estensione xib in xamarin. Mac

_Questo articolo descrive l'uso con i file con estensione xib creati in Interface Builder di Xcode per creare e gestire le interfacce utente per un'applicazione xamarin. Mac._

> [!NOTE]
> Il modo migliore per creare un'interfaccia utente per un'app xamarin. Mac è con gli storyboard. Questa documentazione è stata lasciata posto per motivi storici e l'utilizzo con progetti xamarin. Mac meno recenti. Per altre informazioni, vedere la [Introduzione agli storyboard](~/mac/platform/storyboards/index.md) documentazione.

## <a name="overview"></a>Panoramica

Quando si lavora con C# e .NET in un'applicazione xamarin. Mac, disporre dell'accesso agli elementi dell'interfaccia utente stessa e degli strumenti che gli sviluppatori che lavorano *Objective-C* e *Xcode* viene. Poiché xamarin. Mac si integra direttamente con Xcode, è possibile usare Xcode _Interface Builder_ per creare e gestire le interfacce utente (oppure crearle direttamente nel codice c#).

Un file con estensione xib viene utilizzato per macOS per definire gli elementi dell'interfaccia utente dell'applicazione (ad esempio menu, Windows, viste, le etichette, campi di testo) che vengono creati e gestiti graficamente in Interface Builder di Xcode.

[![Un esempio di app in esecuzione](xib-images/intro01.png "un esempio di app in esecuzione")](xib-images/intro01-large.png#lightbox)

In questo articolo verranno trattati i concetti fondamentali di uso dei file con estensione xib in un'applicazione xamarin. Mac. È altamente consigliabile usare la [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto quanto descrive i concetti chiave e le tecniche che verrà usato in questo articolo.

È possibile ottenere un quadro il [c# esposizione di classi / metodi di Objective-c](~/mac/internals/how-it-works.md) sezione del [meccanismi interni di xamarin. Mac](~/mac/internals/how-it-works.md) del documento, viene spiegato il `Register` e `Export` attributi utilizzato per impostare backup di classi c# per gli oggetti di Objective-C e interfaccia utente di elementi.


## <a name="introduction-to-xcode-and-interface-builder"></a>Introduzione a Xcode e Interface Builder

Come parte di Xcode, Apple ha creato uno strumento chiamato Interface Builder, che consente di creare visivamente un'interfaccia utente in una finestra di progettazione. Xamarin. Mac si integra facilmente con Interface Builder, consentendo di creare l'interfaccia utente con gli stessi strumenti che gli utenti di Objective-C.


### <a name="components-of-xcode"></a>Componenti di Xcode

Quando si apre un file con estensione xib in Xcode da Visual Studio per Mac, viene aperto con un **Project Navigator** a sinistra, il **gerarchia delle interfacce** e **Editor dell'interfaccia** al centro e un **delle proprietà e utilità** sezione nella parte destra:

[![I componenti dell'UI di Xcode](xib-images/xcode03.png "i componenti dell'UI di Xcode")](xib-images/xcode03-large.png#lightbox)

Esaminiamo ognuna di queste sezioni Xcode non e come si sarà usarli per creare l'interfaccia per l'applicazione xamarin. Mac.


#### <a name="project-navigation"></a>Navigazione progetto

Quando si apre un file con estensione xib per la modifica in Xcode, Visual Studio per Mac crea un file di progetto Xcode in background per comunicare le modifiche tra Xcode e viceversa. In un secondo momento, quando si passa a Visual Studio per Mac da Xcode, tutte le modifiche apportate a questo progetto vengono sincronizzate con il progetto xamarin. Mac da Visual Studio per Mac.

Il **nel progetto** sezione consente di passare tra tutti i file che compongono questa _shim_ progetto Xcode. In genere, sarà essere interessati solo i file con estensione xib in questo elenco, ad esempio **MainMenu.xib** e **MainWindow.xib**.


#### <a name="interface-hierarchy"></a>Gerarchia delle interfacce

Il **gerarchia delle interfacce** sezione consente di accedere facilmente alle diverse proprietà chiave dell'interfaccia utente, ad esempio relativi **segnaposto** e principale **finestra**. È anche possibile utilizzare questa sezione per accedere ai singoli elementi (visualizzazioni) che rendono di un'interfaccia utente e per modificare la modalità di annidamento trascinandoli all'interno della gerarchia.


#### <a name="interface-editor"></a>Editor dell'interfaccia

Il **Editor dell'interfaccia** sezione Visualizza la superficie su cui si graficamente di interfaccia utente di layout. È possibile trascinare elementi dal **libreria** sezione del **delle proprietà e utilità** sezione per creare uno schema personalizzato. Man mano che si aggiungono elementi dell'interfaccia utente (visualizzazioni) all'area di progettazione, vengono aggiunti per il **gerarchia delle interfacce** sezione nell'ordine in cui appaiono nel **Editor dell'interfaccia**.


#### <a name="properties--utilities"></a>Le proprietà e utilità

Il **le proprietà e utilità** sezione è suddivisa in due sezioni principali che verrà utilizzato con **proprietà** (anche denominati controlli) e il **libreria**:

![L'ispezione](xib-images/xcode04.png "del controllo proprietà")

Inizialmente in questa sezione è quasi vuota. tuttavia se si seleziona un elemento nel **Interface Editor** o **gerarchia delle interfacce**, il **proprietà** sezione verrà popolata con le informazioni relative all'elemento e le proprietà che è possibile regolare.

Nella sezione **Properties** (Proprietà) sono incluse 8 *schede di controllo*, come mostra la figura seguente:

[![Una panoramica di tutti i controlli](xib-images/xcode05.png "una panoramica di tutti i controlli")](xib-images/xcode05-large.png#lightbox)

Da sinistra a destra, queste schede sono:

-   **File Inspector** (Controllo file): visualizza le informazioni sui file, ad esempio il nome file e il percorso del file Xib in fase di modifica.
-   **Quick Help** (Guida rapida): questa scheda offre una guida contestuale in base all'elemento selezionato in Xcode.
-   **Identity Inspector** (Controllo identità): offre informazioni sul comando o la visualizzazione selezionata.
-   **Attributes Inspector** – finestra di ispezione degli attributi consente di personalizzare diversi attributi del controllo o visualizzazione selezionata.
-   **Size Inspector** : controllo dimensioni consente di controllare le dimensioni e il ridimensionamento di comportamento di controllo o visualizzazione selezionata.
-   **Connections Inspector** : controllo connessioni illustra le connessioni outlet e azioni dei controlli selezionati. Verrà esaminato Outlet e azioni tra qualche istante.
-   **Bindings Inspector** – The Bindings Inspector consente di configurare i controlli in modo che i relativi valori vengono associati automaticamente ai modelli di dati.
-   **View Effects Inspector** : controllo effetti visualizzazioni consente di specificare gli effetti nei comandi, ad esempio le animazioni.

Nel **libreria** sezione, è possibile trovare controlli e oggetti per l'inserimento graficamente nella finestra di progettazione per compilano un'interfaccia utente:

![Un esempio di Library Inspector](xib-images/xcode06.png "un esempio di Library Inspector")

Ora che si ha familiarità con l'IDE di Xcode e Interface Builder, diamo un'occhiata usandolo per creare un'interfaccia utente.


## <a name="creating-and-maintaining-windows-in-xcode"></a>Creazione e gestione di windows in Xcode

È il metodo preferito per la creazione dell'interfaccia utente di un'app xamarin. Mac con gli storyboard (vedere la [Introduzione agli storyboard](~/mac/platform/storyboards/index.md) documentazione per informazioni dettagliate) e, di conseguenza, qualsiasi nuovo progetto di avvio in xamarin. Mac verrà usare gli storyboard per impostazione predefinita.

Per passare a usando un xib basate dell'interfaccia utente, effettuare le operazioni seguenti:

1. Aprire Visual Studio per Mac e avviare un nuovo progetto xamarin. Mac.
2. Nel **riquadro della soluzione**, fare doppio clic sul progetto e selezionare **Add** > **nuovo File...**
3. Selezionare **Mac** > **Controller Windows**:

    ![Aggiunge un nuovo Controller di finestra](xib-images/setup00.png "aggiunge un nuovo Controller di finestra")
4. Immettere `MainWindow` per il nome, quindi scegliere il **New** pulsante:

    ![Aggiunta di una nuova finestra principale](xib-images/setup01.png "aggiunta una nuova finestra principale")
5. Fare doppio clic sul progetto e selezionare **Add** > **nuovo File...**
6. Selezionare **Mac** > **dal Menu principale**:

    ![Aggiunta di un nuovo Menu principale](xib-images/setup02.png "aggiungendo un nuovo Menu principale")
7. Lasciare il nome `MainMenu` e scegliere il **New** pulsante.
8. Nel **riquadro della soluzione** selezionare la **Main. Storyboard** del file, fare doppio clic e selezionare **Rimuovi**:

    ![Selezionando lo storyboard principale](xib-images/setup03.png "selezionando lo storyboard principale")
9. Nella finestra di dialogo Rimuovi, scegliere il **eliminare** pulsante:

    ![Per confermare l'eliminazione](xib-images/setup04.png "per confermare l'eliminazione")
10. Nel **riquadro della soluzione**, fare doppio clic il **Info. plist** file per aprirlo e modificarlo.
11. Selezionare `MainMenu` dal **interfaccia principale** elenco a discesa:

    [![Impostazione del menu principale](xib-images/setup05.png "impostazione nel menu principale")](xib-images/setup05-large.png#lightbox)
12. Nel **riquadro della soluzione**, fare doppio clic il **MainMenu.xib** file per aprirlo e modificarlo in Interface Builder di Xcode.
13. Nel **Library Inspector**, digitare `object` nel campo di ricerca quindi trascinare una nuova **oggetto** nell'area di progettazione:

    [![Modifica del menu principale](xib-images/setup06.png "nel menu principale di modifica")](xib-images/setup06-large.png#lightbox)
14. Nel **Identity Inspector**, immettere `AppDelegate` per il **classe**:

    [![Delegato dell'App selezionando](xib-images/setup07.png "selezionando delegato dell'App")](xib-images/setup07-large.png#lightbox)
15. Selezionare **il proprietario del File** dal **gerarchia delle interfacce**, passare al **controllo connessione** e trascinare una linea dal delegato per il `AppDelegate` **Oggetto** appena aggiunto al progetto:

    [![La connessione di delegato dell'App](xib-images/setup08.png "connessione delegato dell'App")](xib-images/setup08-large.png#lightbox)
16. Salvare le modifiche e tornare a Visual Studio per Mac.

Con tutte queste modifiche sul posto, modificare il **AppDelegate.cs** file e renderlo simile al seguente:

```csharp
using AppKit;
using Foundation;

namespace MacXib
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        public MainWindowController mainWindowController { get; set; }

        public AppDelegate ()
        {
        }

        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
            mainWindowController = new MainWindowController ();
            mainWindowController.Window.MakeKeyAndOrderFront (this);
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
    }
}
```

A questo punto la finestra principale dell'app definita in un **xib** file automaticamente inclusi nel progetto quando si aggiunge un Controller di finestra. Per modificare la progettazione di windows, nelle **riquadro della soluzione**, fare doppio clic il **MainWindow.xib** file:

![Selezione del file MainWindow.xib](xib-images/edit01.png "selezionando il file MainWindow.xib")

Verrà aperta la progettazione di finestra in Interface Builder di Xcode:

[![Modifica il MainWindow.xib](xib-images/edit02.png "modifica il MainWindow.xib")](xib-images/edit02-large.png#lightbox)


### <a name="standard-window-workflow"></a>Flusso di lavoro di finestra standard

Per qualsiasi finestra che creano e si lavora in un'applicazione xamarin. Mac, il processo è fondamentalmente lo stesso:

1. Per la nuova finestra in cui non è il valore predefinito aggiunto automaticamente al progetto, aggiungere una nuova definizione di finestra per il progetto.
2. Fare doppio clic sul file con estensione xib per aprire la progettazione della finestra per la modifica in Interface Builder di Xcode.
3. Impostare qualsiasi proprietà della finestra richiesto nel **attributo Inspector** e il **Size Inspector**.
4. Trascinare i controlli necessari per compilare l'interfaccia e configurarle nel **Inspector attributo**.
5. Usare la **controllo dimensioni** per gestire il ridimensionamento di elementi dell'interfaccia utente.
6. Esporre gli elementi dell'interfaccia utente della finestra di C# codice tramite Outlet e azioni.
7. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.


### <a name="designing-a-window-layout"></a>Progettare un layout di finestra

Il processo per la disposizione un'interfaccia utente in Interface builder è fondamentalmente lo stesso per tutti gli elementi aggiunti:

1. Trovare il controllo desiderato nel **Library Inspector** e trascinarlo nel **Interface Editor** e posizionarlo.
2. Impostare qualsiasi proprietà della finestra richiesto nel **Inspector attributo**.
3. Usare la **controllo dimensioni** per gestire il ridimensionamento di elementi dell'interfaccia utente.
4. Se si usa una classe personalizzata, impostarlo **Identity Inspector**.
5. Esporre gli elementi dell'interfaccia utente per C# codice tramite Outlet e azioni.
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Di seguito è riportato un esempio:

1. In Xcode trascinare **Push Button** (Pulsante Push) da **Library Section** (Sezione librerie):

    [![Selezione di un pulsante dalla libreria](xib-images/xcode07.png "selezionando un pulsante dalla libreria")](xib-images/xcode07-large.png#lightbox)
2. Trascinare il pulsante nel **finestra** nel **Interface Editor**:

    [![Aggiunta di un pulsante nella finestra](xib-images/xcode08.png "aggiunta di un pulsante alla finestra")](xib-images/xcode08-large.png#lightbox)
3. Fare clic sulla proprietà **Title** (Titolo) in **Attribute Inspector** (Controllo attributi) e modificare il titolo del pulsante in `Click Me`:

    ![Impostazione degli attributi del pulsante](xib-images/xcode09.png "impostazione degli attributi del pulsante")
4. Trascinare **Label** (Etichetta) da **Library Section** (Sezione librerie):

    [![Selezione di un'etichetta nella libreria](xib-images/xcode10.png "selezionando un'etichetta nella libreria")](xib-images/xcode10-large.png#lightbox)
5. Trascinare l'etichetta in **Window** (Finestra) accanto al pulsante in **Interface Editor** (Editor interfaccia):

    [![Aggiunta di un'etichetta alla finestra](xib-images/xcode11.png "aggiunta di un'etichetta alla finestra")](xib-images/xcode11-large.png#lightbox)
6. Selezionare il quadratino a destra sull'etichetta e trascinarlo fino a quando non è vicino al bordo della finestra:

    [![Ridimensionamento dell'etichetta](xib-images/xcode12.png "ridimensionamento dell'etichetta")](xib-images/xcode12-large.png#lightbox)
7. Con l'etichetta ancora selezionata nel **Interface Editor**, passare al **controllo dimensioni**:

    ![Selezionare il controllo delle dimensioni](xib-images/xcode13.png "selezionando il controllo di dimensioni")
8. Nel **casella di ridimensionamento automatico** fare clic sul **Dim tra parentesi quadre Red** a destra e la **Dim freccia orizzontale Red** al centro:

    ![Modifica delle proprietà di ridimensionamento automatico](xib-images/xcode14.png "modificando le proprietà di ridimensionamento automatico")
9. Ciò garantisce che l'etichetta verrà esteso per aumentare e ridurre la finestra viene ridimensionata nell'applicazione in esecuzione. Il **tra parentesi quadre Red** e la parte superiore e sinistra del **casella di ridimensionamento automatico** casella indicano l'etichetta viene bloccata alle relative X e Y posizioni.
10. Salvare le modifiche all'interfaccia utente

Durante il ridimensionamento e lo spostamenti dei comandi, si dovrebbe notare che Interface Builder offre suggerimenti utili snap basate su [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). Queste linee guida consentono di creare applicazioni di qualità elevata che avranno un aspetto famigliare per gli utenti Mac.

Se si osserva il **gerarchia delle interfacce** sezione, si noti come appaiono il layout e la gerarchia degli elementi che costituiscono l'interfaccia utente:

![Selezione di un elemento nella gerarchia dell'interfaccia](xib-images/xcode15.png "selezionando un elemento nella gerarchia dell'interfaccia")

Da qui è possibile selezionare gli elementi da modificare o trascinare per riordinare gli elementi dell'interfaccia utente, se necessario. Ad esempio, se un elemento dell'interfaccia utente è stato coperto da un altro elemento, è possibile trascinarlo nella parte inferiore dell'elenco per impostarlo come elemento superiore della finestra.

Per altre informazioni sull'uso di Windows in un'applicazione xamarin. Mac, vedere la [Windows](~/mac/user-interface/window.md) documentazione.


## <a name="exposing-ui-elements-to-c-code"></a>Esporre gli elementi dell'interfaccia utente per C# codice

Dopo aver completato la definizione del layout l'aspetto dell'interfaccia utente in Interface Builder, è necessario esporre gli elementi dell'interfaccia utente in modo che siano accessibili da C# codice. A tale scopo, si useranno gli Outlet e azioni.


### <a name="setting-a-custom-main-window-controller"></a>L'impostazione di un controller di finestra personalizzati principale

Per poter creare Outlet e azioni per esporre gli elementi dell'interfaccia utente al codice c#, l'app xamarin. Mac dovrà essere utilizzando un Controller di finestra personalizzati.

Seguire questa procedura:

1. Aprire Storyboard dell'app in Interface Builder di Xcode.
2. Selezionare il `NSWindowController` nell'area di progettazione.
3. Passare al **Identity Inspector** consente di visualizzare e immettere `WindowController` come il **nome classe**:

    [![Modifica il nome della classe](xib-images/windowcontroller01.png "modifica il nome della classe")](xib-images/windowcontroller01-large.png#lightbox)
4. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione.
5. Oggetto **WindowController.cs** file verrà aggiunto al progetto nel **riquadro della soluzione** in Visual Studio per Mac:

    ![Il nuovo nome di classe in Visual Studio per Mac](xib-images/windowcontroller02.png "il nuovo nome di classe in Visual Studio per Mac")
6. Riaprire lo Storyboard di Interface Builder di Xcode.
7. Il **WindowController.h** file sarà disponibile per l'uso:

    [![Il file con estensione h corrispondente in Xcode](xib-images/windowcontroller03.png "il file con estensione h corrispondente in Xcode")](xib-images/windowcontroller03-large.png#lightbox)


### <a name="outlets-and-actions"></a>Outlet e azioni

Quali sono gli Outlet e azioni? Nella programmazione tradizione dell'interfaccia utente .NET, i comandi dell'interfaccia utente vengono esposti automaticamente come proprietà quando vengono aggiunti. In Mac ciò non avviene, la semplice aggiunta di un comando in una visualizzazione non lo rende accessibile al codice. Lo sviluppatore deve esporre esplicitamente l'elemento dell'interfaccia utente al codice. A tale scopo in ordine, Apple offre due opzioni:

-  **Outlet**: gli outlet sono analoghi alle proprietà. Se associare un controllo a una presa di corrente, essa viene esposta al codice tramite una proprietà, pertanto è possibile eseguire operazioni come collegare gestori di eventi, chiamare i metodi e così via.
-  **Azioni**: le azioni sono analoghe al modello di comando in WPF. Ad esempio, quando viene eseguita un'azione su un controllo, ad esempio un pulsante, il controllo chiamerà automaticamente un metodo nel codice. Le azioni sono comodo e potente poiché può collegare numerosi comandi alla stessa azione.

In Xcode, vengono aggiunti gli Outlet e azioni direttamente nel codice tramite *trascinamento*. In particolare, ciò significa che per creare un'azione o un outlet, si sceglie quale elemento di controllo si desidera aggiungere un'azione o un outlet, tenere premuto il **controllo** pulsante sulla tastiera e trascina il comando direttamente nel codice.

Per gli sviluppatori di xamarin. Mac, ciò significa che si trascinano i file stub Objective-C che corrispondono ai C# file in cui si desidera creare l'azione o outlet. Visual Studio per Mac ha creato un file denominato **MainWindow.h** come parte del progetto Xcode shim generato per l'uso di Interface Builder:

[![Un esempio di un file con estensione h in Xcode](xib-images/xcode16.png "un esempio di un file con estensione h in Xcode")](xib-images/xcode16-large.png#lightbox)

Questo file con estensione h stub rispecchia il **MainWindow.designer.cs** che viene aggiunto automaticamente a un progetto xamarin. Mac quando un nuovo `NSWindow` viene creato. Questo file verrà usato per sincronizzare le modifiche effettuate da Interface Builder ed è dove verrà creato il Outlet e azioni in modo che gli elementi dell'interfaccia utente vengono esposti a C# codice.


#### <a name="adding-an-outlet"></a>Aggiunta di un outlet

Con una conoscenza di base dei quali sono gli Outlet e azioni, esaminiamo la creazione di un outlet per esporre un elemento dell'interfaccia utente per il C# codice.

Seguire questa procedura:

1. In Xcode nell'angolo superiore destro della schermata fare clic sul pulsante **Double Circle** (Doppio cerchio) per aprire **Assistant Editor** (Editor assistente):

    [![Selezionare Assistente dell'Editor](xib-images/outlet01.png "selezionando Assistente dell'Editor")](xib-images/outlet01-large.png#lightbox)
2. Xcode passa in una modalità di visualizzazione suddivisa con **Interface Editor** (Editor interfacce) da un lato e **Code Editor** (Editor codice) dall'altro.
3. Si noti che Xcode ha selezionato automaticamente il **MainWindowController.m** del file nei **Editor di codice**, non è corretto. Se si ricorda dalla nostra discussione su cosa Outlet e azioni sono di sopra, è necessario che il **MainWindow.h** selezionato.
4. Nella parte superiore del **Editor di codice** fare clic sui **collegamento automatico** e selezionare il **MainWindow.h** file:

    [![Selezione del file con estensione h corretta](xib-images/outlet02.png "selezionando il file corretto. h")](xib-images/outlet02-large.png#lightbox)
5. Xcode dovrebbe ora avere il file selezionato corretto:

    [![Il file selezionato corretto](xib-images/outlet03.png "il file selezionato corretto")](xib-images/outlet03-large.png#lightbox)
6. **L'ultimo passaggio è molto importante.** Se non hai selezionato il file corretto, non sarà in grado di creare Outlet e azioni o sarà esposta a una classe errata in C#.
7. Nel **Editor dell'interfaccia**, tenere premuto il **controllo** tasto sulla tastiera e fare clic e trascinare l'etichetta creata in precedenza nell'editor di codice sotto la `@interface MainWindow : NSWindow { }` codice:

    [![Il trascinamento per creare un nuovo negozio](xib-images/outlet04.png "il trascinamento per creare un nuovo Negozio")](xib-images/outlet04-large.png#lightbox)
8. Viene visualizzata una finestra di dialogo. Lasciare il **Connection** impostato su outlet e immettere `ClickedLabel` per il **nome**:

    [![Impostazione delle proprietà Outlet](xib-images/outlet05.png "impostando le proprietà Outlet")](xib-images/outlet05-large.png#lightbox)
9. Scegliere il **Connect** pulsante per la creazione dell'outlet:

    ![Dell'Outlet completate](xib-images/outlet06.png "dell'Outlet completato")
10. Salvare le modifiche apportate al file.


#### <a name="adding-an-action"></a>Aggiunta di un'azione

Successivamente, verrà ora esaminata la creazione di un'azione per esporre un'interazione utente con l'elemento dell'interfaccia utente di C# codice.

Seguire questa procedura:

1. Assicurarsi che si trovano ancora nel **Assistente dell'Editor** e il **MainWindow.h** è visibile nel file la **Editor di codice**.
2. Nel **Editor dell'interfaccia**, tenere premuto il **controllo** tasto sulla tastiera e fare clic e trascinare il pulsante creato in precedenza nell'editor di codice sotto la `@property (assign) IBOutlet NSTextField *ClickedLabel;` codice:

    [![Il trascinamento per creare un'azione](xib-images/action01.png "il trascinamento per creare un'azione")](xib-images/action01-large.png#lightbox)
3. Modifica il **connessione** tipo di azione:

    [![Selezionare un tipo di azione](xib-images/action02.png "selezionare un tipo di azione")](xib-images/action02-large.png#lightbox)
4. Immettere `ClickedButton` in **Name** (Nome):

    [![Configurazione dell'azione](xib-images/action03.png "configurazione dell'azione")](xib-images/action03-large.png#lightbox)
5. Scegliere il **Connect** pulsante per creare azioni:

    ![L'azione completata](xib-images/action04.png "azione completata")
6. Salvare le modifiche apportate al file.

Con l'interfaccia utente di reti cablate e averla esposta al C# code, tornare a Visual Studio per Mac che sincronizzerà le modifiche da Xcode e Interface Builder.


### <a name="writing-the-code"></a>Scrittura del codice

Con l'interfaccia utente creato e gli elementi dell'interfaccia esposti al codice tramite gli Outlet e azioni, si è pronti a scrivere il codice per attivare il programma. Ad esempio, aprire il **MainWindow.cs** file per la modifica facendo doppio clic su esso nel **riquadro della soluzione**:

[![Il file MainWindow.cs](xib-images/code01.png "MainWindow.cs The file")](xib-images/code01-large.png#lightbox)

E aggiungere il codice seguente per il `MainWindow` classe per funzionare con l'uscita di esempio creata in precedenza:

```csharp
private int numberOfTimesClicked = 0;
...

public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Set the initial value for the label
    ClickedLabel.StringValue = "Button has not been clicked yet.";
}
```

Si noti che il `NSLabel` avviene C# per il nome diretto che è stata assegnata in Xcode durante la creazione relativa outlet in Xcode, in questo caso, si tratta `ClickedLabel`. Si può accedere qualsiasi metodo o proprietà dell'oggetto esposto l'esattamente come qualsiasi normale C# classe.

> [!IMPORTANT]
> È necessario usare `AwakeFromNib`, anziché un altro metodo, ad esempio `Initialize`, in quanto `AwakeFromNib` viene chiamato _dopo_ il sistema operativo ha caricato e creare un'istanza dell'interfaccia utente dal file con estensione xib. Se si è tentato di accedere al controllo etichetta prima il file con estensione xib è stato completamente caricato e creare un'istanza, si otterrebbe un `NullReferenceException` errore perché il controllo etichetta non verrebbe ancora creato.

Successivamente, aggiungere la classe parziale seguente al `MainWindow` classe:

```csharp
partial void ClickedButton (Foundation.NSObject sender) {

    // Update counter and label
    ClickedLabel.StringValue = string.Format("The button has been clicked {0} time{1}.",++numberOfTimesClicked, (numberOfTimesClicked < 2) ? "" : "s");
}
```

Questo codice collega all'azione creato in Xcode e Interface Builder e verrà chiamato ogni volta che l'utente fa clic sul pulsante.

Alcuni elementi dell'interfaccia utente hanno compilato automaticamente nel riquadro azioni, ad esempio, gli elementi nella barra dei Menu predefinito, ad esempio il **aprire...**  voce di menu (`openDocument:`). Nel **riquadro della soluzione**, fare doppio clic sul **AppDelegate.cs** file per aprirlo e modificarlo e aggiungere il codice seguente sotto il `DidFinishLaunching` metodo:

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

La riga di chiave è `[Export ("openDocument:")]`, viene indicato `NSMenu` che il **AppDelegate** dispone di un metodo `void OpenDialog (NSObject sender)` che risponde al `openDocument:` azione.

Per altre informazioni sull'utilizzo dei menu, vedere la [menu](~/mac/user-interface/menu.md) documentazione.


## <a name="synchronizing-changes-with-xcode"></a>Sincronizzazione delle modifiche con Xcode

Quando si passa a Visual Studio per Mac da Xcode, tutte le modifiche apportate in Xcode verranno automaticamente sincronizzate con il progetto xamarin. Mac.

Se si seleziona il **MainWindow.designer.cs** nel **riquadro della soluzione** sarà in grado di vedere come nostro outlet e azioni sono stati collegati nel nostro C# codice:

[![Sincronizzazione delle modifiche con Xcode](xib-images/sync01.png "sincronizzazione delle modifiche con Xcode")](xib-images/sync01-large.png#lightbox)

Si noti come le due definizioni nel **MainWindow.designer.cs** file:

```csharp
[Outlet]
AppKit.NSTextField ClickedLabel { get; set; }

[Action ("ClickedButton:")]
partial void ClickedButton (Foundation.NSObject sender);
```

Sono allineate le definizioni nella **MainWindow.h** file in Xcode:

```objc
@property (assign) IBOutlet NSTextField *ClickedLabel;
- (IBAction)ClickedButton:(id)sender;
```

Come può notare, Visual Studio per Mac è in ascolto per le modifiche al file con estensione h e quindi si sincronizza automaticamente le modifiche nella rispettiva **. designer.cs** file per esporle all'applicazione. È anche possibile notare che **MainWindow.designer.cs** è una classe parziale, in modo che non deve modificare Visual Studio per Mac **MainWindow.cs** sovrascrivendo eventuali modifiche che sono stati apportati alla classe.

In genere non sarà mai necessario aprire la **MainWindow.designer.cs** manualmente, è stata presentata esclusivamente per scopi didattici.

> [!IMPORTANT]
> Nella maggior parte dei casi, Visual Studio per Mac verrà automaticamente le modifiche apportate in Xcode e Sincronizza con il progetto xamarin. Mac. Se la sincronizzazione non viene eseguita automaticamente, tornare a Xcode e quindi di nuovo in Visual Studio per Mac. Questa operazione avvia solitamente un ciclo di sincronizzazione.


## <a name="adding-a-new-window-to-a-project"></a>Aggiunta di una nuova finestra in un progetto

A parte la finestra del documento principale, un'applicazione xamarin. Mac potrebbe essere necessario visualizzare altri tipi di windows per l'utente, ad esempio le preferenze o i pannelli di controllo. Quando si aggiunge una nuova finestra per il progetto è consigliabile usare sempre la **finestra Cocoa con Controller** opzione, poiché in questo modo il processo di caricamento di finestra dal xib file più semplice.

Per aggiungere una nuova finestra, eseguire le operazioni seguenti:

1. Nel **riquadro della soluzione**, fare doppio clic sul progetto e selezionare **Add** > **nuovo File...** .
2. Nella finestra di dialogo Nuovo File, selezionare **xamarin. Mac** > **finestra Cocoa con Controller**:

    ![Aggiunge un nuovo Controller di finestra](xib-images/new01.png "aggiunge un nuovo Controller di finestra")
3. Immettere `PreferencesWindow` in **Nome** e fare clic sul pulsante **Nuovo**.
4. Fare doppio clic il **PreferencesWindow.xib** file per aprirlo e modificarlo in Interface Builder:

    [![Modifica la finestra in Xcode](xib-images/new02.png "modifica la finestra in Xcode")](xib-images/new02-large.png#lightbox)
5. Progettare l'interfaccia:

    [![Progettazione del layout di windows](xib-images/new03.png "progettare il layout di windows")](xib-images/new03-large.png#lightbox)
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Aggiungere il seguente codice al **AppDelegate.cs** per visualizzare la nuova finestra:

```csharp
[Export("applicationPreferences:")]
void ShowPreferences (NSObject sender)
{
    var preferences = new PreferencesWindowController ();
    preferences.Window.MakeKeyAndOrderFront (this);
}
```

Il `var preferences = new PreferencesWindowController ();` riga crea una nuova istanza del Controller di finestra che viene caricata la finestra dal file con estensione xib e ingrandita. Il `preferences.Window.MakeKeyAndOrderFront (this);` riga visualizza la finestra di nuovo all'utente.

Se si esegue il codice e selezionare il **preferenze...**  dal **Menu applicazione**, verrà visualizzata la finestra:

![L'esecuzione dell'app di esempio](xib-images/new04.png "in esecuzione l'app di esempio")

Per altre informazioni sull'uso di Windows in un'applicazione xamarin. Mac, vedere la [Windows](~/mac/user-interface/window.md) documentazione.


## <a name="adding-a-new-view-to-a-project"></a>Aggiunta di una nuova visualizzazione a un progetto

Esistono casi in cui è più semplice suddividere la progettazione della finestra verso il basso in diverse, i file con estensione xib più gestibili. Ad esempio, come passare il contenuto della finestra principale quando si seleziona un elemento della barra degli strumenti in un **finestra Preferenze** o scambiare contenuto in risposta a un **Source List** selezione.

Quando si aggiunge una nuova visualizzazione per il progetto è consigliabile usare sempre la **visualizzazione Cocoa con Controller** opzione, poiché in questo modo il processo di caricamento della visualizzazione dal xib file più semplice.

Per aggiungere una nuova vista, eseguire le operazioni seguenti:

1. Nel **riquadro della soluzione**, fare doppio clic sul progetto e selezionare **Add** > **nuovo File...** .
2. Nella finestra di dialogo Nuovo File, selezionare **xamarin. Mac** > **visualizzazione Cocoa con Controller**:

    ![Aggiunta di una nuova vista](xib-images/view01.png "aggiunta una nuova visualizzazione")
3. Immettere `SubviewTable` in **Nome** e fare clic sul pulsante **Nuovo**.
4. Fare doppio clic il **SubviewTable.xib** file per aprirlo e modificarlo in Interface Builder e progettare l'interfaccia utente:

    [![Progetta la nuova vista in Xcode](xib-images/view02.png "progettazione la nuova vista in Xcode")](xib-images/view02-large.png#lightbox)
5. Collegare tutti gli Outlet e azioni necessari.
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Modificare quindi il **SubviewTable.cs** e aggiungere il codice seguente per il **AwakeFromNib** file per popolare la nuova vista quando viene caricato:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create the Product Table Data Source and populate it
    var DataSource = new ProductTableDataSource ();
    DataSource.Products.Add (new Product ("Xamarin.iOS", "Allows you to develop native iOS Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Android", "Allows you to develop native Android Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Mac", "Allows you to develop Mac native Applications in C#"));
    DataSource.Sort ("Title", true);

    // Populate the Product Table
    ProductTable.DataSource = DataSource;
    ProductTable.Delegate = new ProductTableDelegate (DataSource);

    // Auto select the first row
    ProductTable.SelectRow (0, false);
}
```

Aggiungere un'enumerazione al progetto per tenere traccia di quale visualizzazione viene attualmente visualizzato. Ad esempio, **SubviewType.cs**:

```csharp
public enum SubviewType
{
    None,
    TableView,
    OutlineView,
    ImageView
}
```

Modificare il file con estensione xib della finestra che verrà utilizzato la visualizzazione e visualizzarla. Aggiungere un **vista personalizzata** che verrà utilizzato come contenitore per la visualizzazione di una volta che viene caricato in memoria da C# del codice e lo si espone a un outlet chiamato `ViewContainer`:

[![Creazione dell'Outlet necessaria](xib-images/view03.png "creazione dell'Outlet obbligatorio")](xib-images/view03-large.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Successivamente, modificare il file con estensione cs della finestra che prevede di visualizzare la nuova vista (ad esempio, **MainWindow.cs**) e aggiungere il codice seguente:

```csharp
private SubviewType ViewType = SubviewType.None;
private NSViewController SubviewController = null;
private NSView Subview = null;
...

private void DisplaySubview(NSViewController controller, SubviewType type) {

    // Is this view already displayed?
    if (ViewType == type) return;

    // Is there a view already being displayed?
    if (Subview != null) {
        // Yes, remove it from the view
        Subview.RemoveFromSuperview ();

        // Release memory
        Subview = null;
        SubviewController = null;
    }

    // Save values
    ViewType = type;
    SubviewController = controller;
    Subview = controller.View;

    // Define frame and display
    Subview.Frame = new CGRect (0, 0, ViewContainer.Frame.Width, ViewContainer.Frame.Height);
    ViewContainer.AddSubview (Subview);
}
```

Quando è necessario visualizzare una nuova visualizzazione caricato da un file con estensione xib nel contenitore della finestra (il **vista personalizzata** aggiunte in precedenza), questo codice consente di gestire la rimozione di qualsiasi visualizzazione esistente e scambiarlo out per quella nuova. Verifica, che si dispone già di una visualizzazione, se così lo rimuove dalla schermata. Successivamente richiede la visualizzazione che è stata passata in (come caricato da un Controller di visualizzazione) viene ridimensionato per adattarlo nell'Area di contenuto e lo aggiunge al contenuto per la visualizzazione.

Per visualizzare una nuova visualizzazione, usare il codice seguente:

```csharp
DisplaySubview(new SubviewTableController(), SubviewType.TableView);
```

Ciò crea una nuova istanza del Controller di visualizzazione per la nuova vista da visualizzare, imposta il tipo (come specificato dal tipo enum aggiunto al progetto) e utilizza il `DisplaySubview` metodo aggiunto alla classe della finestra per visualizzare effettivamente la visualizzazione. Ad esempio:

[![L'esecuzione dell'app di esempio](xib-images/view04.png "in esecuzione l'app di esempio")](xib-images/view04-large.png#lightbox)

Per altre informazioni sull'uso di Windows in un'applicazione xamarin. Mac, vedere la [Windows](~/mac/user-interface/window.md) e [finestre di dialogo](~/mac/user-interface/dialog.md) documentazione.


## <a name="summary"></a>Riepilogo

Questo articolo ha fatto un quadro dettagliato di uso dei file con estensione xib in un'applicazione xamarin. Mac. Abbiamo visto tipi diversi e all'utilizzo dei file con estensione xib per creare l'interfaccia utente dell'applicazione, come creare e gestire con estensione xib i file in Xcode interfaccia generatore e su come usare i file con estensione xib in C# codice.


## <a name="related-links"></a>Collegamenti correlati

- [MacImages (sample)](https://developer.xamarin.com/samples/mac/MacImages/) (MacImages - Esempio)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [Menu](~/mac/user-interface/menu.md)
- [Dialogs](~/mac/user-interface/dialog.md) (Finestre di dialogo)
- [Utilizzo di immagini](~/mac/app-fundamentals/image.md)
- [Linee guida dell'interfaccia umana macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
