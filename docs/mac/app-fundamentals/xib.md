---
title: file .xib Xamarin.Mac
description: In questo articolo viene descritto l'utilizzo con i file .xib creati in Generatore di Xcode interfaccia per creare e gestire le interfacce utente per un'applicazione Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 6AF3D216-448D-4B2D-9026-74E4FFF5923A
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 3ef536ddb19ed60975368bd022e57c34c6f473dc
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792274"
---
# <a name="xib-files-in-xamarinmac"></a>file .xib Xamarin.Mac

_In questo articolo viene descritto l'utilizzo con i file .xib creati in Generatore di Xcode interfaccia per creare e gestire le interfacce utente per un'applicazione Xamarin.Mac._

> [!NOTE]
> Il modo preferito per creare un'interfaccia utente per un'app Xamarin.Mac è con storyboard. Questa documentazione è stata lasciata sul posto per motivi cronologici e per l'utilizzo di progetti Xamarin.Mac precedenti. Per ulteriori informazioni, vedere il nostro [Introduzione agli storyboard](~/mac/platform/storyboards/index.md) documentazione.

## <a name="overview"></a>Panoramica

Quando si lavora con c# e .NET in un'applicazione Xamarin.Mac, disporre dell'accesso agli elementi dell'interfaccia utente stesso e strumenti che uno sviluppatore che lavora *Objective-C* e *Xcode* does. Poiché Xamarin.Mac si integra direttamente con Xcode, è possibile utilizzare del Xcode _generatore interfaccia_ per creare e gestire le interfacce utente (o, facoltativamente, crearli direttamente nel codice c#).

Viene utilizzato un file di .xib da macOS per definire gli elementi dell'interfaccia utente dell'applicazione (ad esempio menu, Windows, viste, le etichette, campi di testo) che vengono creati e gestiti graficamente in Generatore del Xcode di interfaccia.

[![Un esempio di app in esecuzione](xib-images/intro01.png "un esempio di app in esecuzione")](xib-images/intro01-large.png#lightbox)

In questo articolo verranno descritte le nozioni fondamentali sull'utilizzo dei file in un'applicazione Xamarin.Mac .xib. È altamente consigliabile che il [Hello, Mac](~/mac/get-started/hello-mac.md) articolo in primo luogo, come illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

È possibile dare un'occhiata il [c# esposizione di classi / metodi per Objective-C](~/mac/internals/how-it-works.md) sezione del [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) del documento, nonché viene descritto il `Register` e `Export` gli attributi utilizzato per associare le classi c# Objective-C e agli oggetti dell'interfaccia utente gli elementi.


## <a name="introduction-to-xcode-and-interface-builder"></a>Introduzione a Xcode e Interface Builder

Come parte di Xcode, Apple ha creato uno strumento denominato Generatore di interfaccia, che consente di creare visivamente l'interfaccia utente in una finestra di progettazione. Xamarin.Mac correntemente una cinquantina integra generatore di interfaccia, che consente di creare l'interfaccia utente con gli stessi strumenti che gli utenti Objective-C.


### <a name="components-of-xcode"></a>Componenti di Xcode

Quando si apre un file .xib in Xcode da Visual Studio per Mac, viene aperto con un **Project Navigator** a sinistra di **interfaccia gerarchia** e **Editor dell'interfaccia** al centro e un **proprietà & utilità** sezione a destra:

[![I componenti di UI di Xcode](xib-images/xcode03.png "i componenti di UI di Xcode")](xib-images/xcode03-large.png#lightbox)

Diamo un'occhiata al contenuto di ciascuno di questi Xcode sezioni non e come si verranno usate per creare l'interfaccia per l'applicazione Xamarin.Mac.


#### <a name="project-navigation"></a>Nel progetto

Quando si apre un file .xib per la modifica in Xcode, Visual Studio per Mac crea un file di progetto Xcode in background per comunicare le modifiche tra l'elemento e Xcode. In un secondo momento, quando si passa a Visual Studio per Mac da Xcode, eventuali modifiche apportate al progetto vengono sincronizzati con il progetto Xamarin.Mac da Visual Studio per Mac.

Il **nel progetto** sezione consente di spostarsi tra tutti i file che compongono questo _shim_ progetto Xcode. In genere, si solo essere interessati in questo elenco, ad esempio i file di .xib **MainMenu.xib** e **MainWindow.xib**.


#### <a name="interface-hierarchy"></a>Gerarchia delle interfacce

Il **interfaccia gerarchia** sezione consente di accedere facilmente diverse proprietà di chiave dell'interfaccia utente, ad esempio il **segnaposto** e main **finestra**. È anche possibile utilizzare questa sezione per accedere ai singoli elementi (visualizzazioni) che costituiscono l'interfaccia utente e la regola il modo in cui sono nidificati trascinandoli intorno all'interno della gerarchia.


#### <a name="interface-editor"></a>Editor dell'interfaccia

Il **Editor dell'interfaccia** sezione fornisce l'area in cui si graficamente di interfaccia utente di layout. È possibile trascinare gli elementi dal **libreria** sezione la **proprietà & utilità** sezione per creare il progetto. Quando si aggiungono elementi dell'interfaccia utente (visualizzazioni) nell'area di progettazione, verrà aggiunto al **interfaccia gerarchia** nell'ordine in cui compaiono nella sezione di **Editor dell'interfaccia**.


#### <a name="properties--utilities"></a>Proprietà & utilità

Il **proprietà & utilità** sezione è suddivisa in due sezioni principali che verrà utilizzato con, **proprietà** (detto anche controlli) e **libreria**:

![Il controllo proprietà](xib-images/xcode04.png "di ispezione")

Inizialmente in questa sezione è quasi vuota, tuttavia se si seleziona un elemento nel **Editor dell'interfaccia** o **gerarchia delle interfacce**, **proprietà** sezione verrà popolata con le informazioni con l'elemento specificato e le proprietà che è possibile modificare.

Nella sezione **Properties** (Proprietà) sono incluse 8 *schede di controllo*, come mostra la figura seguente:

[![Una panoramica di tutti i controlli](xib-images/xcode05.png "una panoramica di tutti i controlli")](xib-images/xcode05-large.png#lightbox)

Da sinistra a destra, queste schede sono:

-   **File Inspector** (Controllo file): visualizza le informazioni sui file, ad esempio il nome file e il percorso del file Xib in fase di modifica.
-   **Quick Help** (Guida rapida): questa scheda offre una guida contestuale in base all'elemento selezionato in Xcode.
-   **Identity Inspector** (Controllo identità): offre informazioni sul comando o la visualizzazione selezionata.
-   **Gli attributi di controllo** : il controllo degli attributi consente di personalizzare i vari attributi della vista del controllo selezionato.
-   **Ridimensiona controllo** : il controllo di dimensioni consente di controllare le dimensioni e il ridimensionamento di comportamento della vista del controllo selezionato.
-   **Le connessioni controllo** : il controllo connessioni Mostra le connessioni in uscita e l'azione dei controlli selezionati. Si esamineranno le prese e le azioni nell'istante.
-   **Controllo delle associazioni** : il controllo di binding consente di configurare i controlli in modo che i relativi valori vengono associati automaticamente ai modelli di dati.
-   **Visualizzare gli effetti controllo** : il controllo di effetti di visualizzazione consente di specificare gli effetti sui controlli, ad esempio animazioni.

Nel **libreria** sezione, è possibile trovare i controlli e gli oggetti per l'inserimento graficamente nella finestra di progettazione per compilare un'interfaccia utente:

![Un esempio di controllo di libreria](xib-images/xcode06.png "un esempio di controllo di libreria")

Ora che si ha familiarità con Xcode IDE e l'interfaccia generatore, diamo un'occhiata utilizzarla per creare un'interfaccia utente.


## <a name="creating-and-maintaining-windows-in-xcode"></a>Creazione e gestione di windows in Xcode

È il metodo preferito per la creazione dell'interfaccia utente dell'applicazione un Xamarin.Mac con storyboard (vedere il nostro [Introduzione agli storyboard](~/mac/platform/storyboards/index.md) documentazione per ulteriori informazioni) e, di conseguenza, qualsiasi nuovo progetto è stato avviato in causerà Xamarin.Mac Per impostazione predefinita, utilizzare gli storyboard.

Per passare a usando un .xib basata su interfaccia utente, effettuare le operazioni seguenti:

1. Aprire Visual Studio per Mac e avviare un nuovo progetto Xamarin.Mac.
2. Nel **soluzione riempimento**del mouse sul progetto e scegliere **Aggiungi** > **nuovo File...**
3. Selezionare **Mac** > **Windows Controller**:

    ![Aggiunge un nuovo Controller di finestra](xib-images/setup00.png "aggiunge un nuovo Controller di finestra")
4. Immettere `MainWindow` per il nome e fare clic su di **New** pulsante:

    ![Aggiunta di una nuova finestra principale](xib-images/setup01.png "aggiunta una nuova finestra principale")
5. Fare doppio clic sul progetto e selezionare **Aggiungi** > **nuovo File...**
6. Selezionare **Mac** > **Menu principale**:

    ![Aggiunta di un nuovo Menu principale](xib-images/setup02.png "aggiunta di un nuovo Menu principale")
7. Lasciare il nome come `MainMenu` e fare clic su di **New** pulsante.
8. Nel **soluzione riempimento** selezionare il **Main** file, fare clic e selezionare **rimuovere**:

    ![Selezione storyboard principale](xib-images/setup03.png "selezionando lo storyboard principale")
9. Nella finestra di dialogo Rimuovi fare clic su di **eliminare** pulsante:

    ![Per confermare l'eliminazione](xib-images/setup04.png "per confermare l'eliminazione")
10. Nel **soluzione riempimento**, fare doppio clic su di **Info. plist** file per aprirlo e modificarlo.
11. Selezionare `MainMenu` dal **interfaccia principale** elenco a discesa:

    [![Impostazione del menu principale](xib-images/setup05.png "l'impostazione del menu principale")](xib-images/setup05-large.png#lightbox)
12. Nel **soluzione riempimento**, fare doppio clic su di **MainMenu.xib** file per aprirlo e modificarlo in Generatore del Xcode di interfaccia.
13. Nel **controllo libreria**, tipo `object` nel campo Cerca quindi trascinare una nuova **oggetto** nell'area di progettazione:

    [![Modifica del menu principale](xib-images/setup06.png "modifica del menu principale")](xib-images/setup06-large.png#lightbox)
14. Nel **controllo di identità**, immettere `AppDelegate` per il **classe**:

    [![Selezionando il delegato App](xib-images/setup07.png "selezionando il delegato di App")](xib-images/setup07-large.png#lightbox)
15. Selezionare **proprietario del File** dal **gerarchia delle interfacce**, passare il **connessione controllo** e trascinare una linea dal delegato per il `AppDelegate` **Oggetto** appena aggiunto al progetto:

    [![Connette il delegato App](xib-images/setup08.png "connessione delegato App")](xib-images/setup08-large.png#lightbox)
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

Ora la finestra principale dell'applicazione è definita in un **.xib** file incluso nel progetto automaticamente quando si aggiunge un Controller di finestra. Per modificare la progettazione di windows, nel **soluzione riempimento**, fare doppio clic il **MainWindow.xib** file:

![Selezionare il file MainWindow.xib](xib-images/edit01.png "selezionando il file MainWindow.xib")

La progettazione della finestra verrà aperta in Generatore del Xcode di interfaccia:

[![Modifica il MainWindow.xib](xib-images/edit02.png "modifica il MainWindow.xib")](xib-images/edit02-large.png#lightbox)


### <a name="standard-window-workflow"></a>Flusso di lavoro di finestra standard

Per qualsiasi finestra che si crea e si utilizzano Xamarin.Mac nell'applicazione in uso, il processo è fondamentalmente lo stesso:

1. Per nuove finestre che non sono predefiniti aggiunti automaticamente al progetto, aggiungere una nuova definizione di finestra per il progetto.
2. Fare doppio clic sul file .xib per aprire la progettazione della finestra per la modifica in Generatore del Xcode di interfaccia.
3. Impostare le proprietà necessarie finestra **controllo attributo** e **dimensioni controllo**.
4. Trascinare i controlli necessari per compilare un'interfaccia e configurarli nel **controllo attributo**.
5. Utilizzare il **dimensioni controllo** per gestire il ridimensionamento di elementi dell'interfaccia utente.
6. Esporre gli elementi dell'interfaccia utente della finestra per il codice c# tramite punti vendita e le azioni.
7. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.


### <a name="designing-a-window-layout"></a>Progettazione di un layout di finestra

Il processo per la disposizione su un'interfaccia utente in Generatore di interfaccia è sostanzialmente lo stesso per ogni elemento aggiunto:

1. Trovare il controllo desiderato nel **controllo libreria** e trascinarla nella **Editor dell'interfaccia** e posizionarlo in.
2. Impostare le proprietà necessarie finestra **controllo attributo**.
3. Utilizzare il **dimensioni controllo** per gestire il ridimensionamento di elementi dell'interfaccia utente.
4. Se si utilizza una classe personalizzata, impostarla **controllo di identità**.
5. Esporre gli elementi dell'interfaccia utente al codice c# tramite punti vendita e le azioni.
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Di seguito è riportato un esempio:

1. In Xcode trascinare **Push Button** (Pulsante Push) da **Library Section** (Sezione librerie):

    [![Selezione di un pulsante dalla libreria](xib-images/xcode07.png "selezionando un pulsante dalla libreria")](xib-images/xcode07-large.png#lightbox)
2. Rilasciare il pulsante sul **finestra** nel **Editor dell'interfaccia**:

    [![Aggiunta di un pulsante nella finestra](xib-images/xcode08.png "aggiunta di un pulsante nella finestra")](xib-images/xcode08-large.png#lightbox)
3. Fare clic sulla proprietà **Title** (Titolo) in **Attribute Inspector** (Controllo attributi) e modificare il titolo del pulsante in `Click Me`:

    ![Impostazione degli attributi del pulsante](xib-images/xcode09.png "impostazione degli attributi del pulsante")
4. Trascinare **Label** (Etichetta) da **Library Section** (Sezione librerie):

    [![Selezione di un'etichetta nella libreria](xib-images/xcode10.png "selezionando un'etichetta nella libreria")](xib-images/xcode10-large.png#lightbox)
5. Trascinare l'etichetta in **Window** (Finestra) accanto al pulsante in **Interface Editor** (Editor interfaccia):

    [![Aggiunta di un'etichetta nella finestra](xib-images/xcode11.png "aggiunta di un'etichetta nella finestra")](xib-images/xcode11-large.png#lightbox)
6. Selezionare il quadratino a destra sull'etichetta e trascinarlo fino a quando non è vicino al bordo della finestra:

    [![L'etichetta di ridimensionamento](xib-images/xcode12.png "l'etichetta di ridimensionamento")](xib-images/xcode12-large.png#lightbox)
7. Con l'etichetta ancora selezionato nel **Editor dell'interfaccia**, passare il **dimensioni controllo**:

    ![Selezionare il controllo di dimensioni](xib-images/xcode13.png "selezionando il controllo delle dimensioni")
8. Nel **casella di ridimensionamento automatico** fare clic sul **Dim tra parentesi quadre rosso** a destra e **Dim freccia orizzontale a rosso** nel centro:

    ![Modifica delle proprietà di ridimensionamento automatico](xib-images/xcode14.png "modificando le proprietà di ridimensionamento automatico")
9. In questo modo si garantisce che l'etichetta verrà esteso per aumentare e ridurre come la finestra viene ridimensionata nell'applicazione in esecuzione. Il **tra parentesi quadre rosso** e la parte superiore e sinistra del **casella di ridimensionamento automatico** casella indicare l'etichetta da bloccarsi ai relativi dati X e Y percorsi.
10. Salvare le modifiche all'interfaccia utente

Durante il ridimensionamento e spostamento dei controlli in, si dovrebbero notare che il generatore di interfaccia suggerisce è utile snap basati su [OS X umano linee guida sull'interfaccia](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). Queste linee guida consentono di creare applicazioni di alta qualità che avranno un aspetto familiare per gli utenti Mac.

Se si osserva il **interfaccia gerarchia** sezione, si noti come vengono visualizzati i layout e la gerarchia degli elementi che costituiscono l'interfaccia utente:

![Selezione di un elemento nella gerarchia delle interfacce](xib-images/xcode15.png "la selezione di un elemento nella gerarchia delle interfacce")

Da qui è possibile selezionare gli elementi da modificare o trascinare per riordinare gli elementi dell'interfaccia utente, se necessario. Ad esempio, se un elemento dell'interfaccia utente è stato viene analizzato da un altro elemento, è possibile trascinarlo nella parte inferiore dell'elenco per selezionare un elemento in primo piano nella finestra di.

Per ulteriori informazioni sull'uso di Windows in un'applicazione Xamarin.Mac, consultare il nostro [Windows](~/mac/user-interface/window.md) documentazione.


## <a name="exposing-ui-elements-to-c-code"></a>Esposizione di elementi dell'interfaccia utente al codice c#

Dopo avere a disposizione l'aspetto dell'interfaccia utente in Generatore di interfaccia, è necessario esporre gli elementi dell'interfaccia utente in modo che siano accessibili dal codice c#. A tale scopo, sarà possibile usare le azioni e i punti vendita.


### <a name="setting-a-custom-main-window-controller"></a>L'impostazione di un controller personalizzato finestra principale

Per essere in grado di creare i punti vendita e le azioni per esporre elementi dell'interfaccia utente in codice c#, l'app Xamarin.Mac dovrà essere utilizzando un Controller di finestra personalizzati.

Seguire questa procedura:

1. Aprire Storyboard dell'app in Generatore di interfaccia del Xcode.
2. Selezionare il `NSWindowController` nell'area di progettazione.
3. Passare il **controllo di identità** consente di visualizzare e `WindowController` come il **nome classe**:

    [![Modifica il nome della classe](xib-images/windowcontroller01.png "modifica il nome della classe")](xib-images/windowcontroller01-large.png#lightbox)
4. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione.
5. Oggetto **WindowController.cs** file verrà aggiunto al progetto nel **soluzione riempimento** in Visual Studio per Mac:

    ![Il nuovo nome di classe in Visual Studio per Mac](xib-images/windowcontroller02.png "il nuovo nome di classe in Visual Studio per Mac")
6. Riaprire lo Storyboard in Generatore di interfaccia del Xcode.
7. Il **WindowController.h** file sarà disponibile per l'utilizzo:

    [![Il file con estensione h corrispondente in Xcode](xib-images/windowcontroller03.png "il file con estensione h corrispondente in Xcode")](xib-images/windowcontroller03-large.png#lightbox)


### <a name="outlets-and-actions"></a>Punti vendita e le azioni

Quali sono prese e le azioni? Nella programmazione tradizione dell'interfaccia utente .NET, i comandi dell'interfaccia utente vengono esposti automaticamente come proprietà quando vengono aggiunti. In Mac ciò non avviene, la semplice aggiunta di un comando in una visualizzazione non lo rende accessibile al codice. Lo sviluppatore deve esporre esplicitamente l'elemento dell'interfaccia utente al codice. A tale scopo in ordine, Apple offre due opzioni:

-  **Outlet**: gli outlet sono analoghi alle proprietà. Se associare un controllo a una presa elettrica, essa viene esposta al codice tramite una proprietà, in modo da poter eseguire le operazioni come collegare gestori eventi, chiamare i metodi, e così via.
-  **Azioni**: le azioni sono analoghe al modello di comando in WPF. Ad esempio, quando viene eseguita un'azione su un controllo, ad esempio un pulsante, il controllo verrà automaticamente chiamare un metodo nel codice. Azioni sono pratico e potente, perché è possibile associare molti controlli per la stessa azione.

In Xcode, punti vendita e le azioni vengono aggiunte direttamente nel codice tramite *trascinando controllo*. In particolare, ciò significa che per creare un'azione o uscita, scegliere quale elemento di controllo si desidera aggiungere un'azione o uscita, tenere premuto il **controllo** pulsante sulla tastiera e trascinare il controllo direttamente nel codice.

Per gli sviluppatori Xamarin.Mac, ciò significa che si trascinano i file stub Objective-C che corrispondono al file c# in cui si desidera creare il punto o l'azione. Visual Studio per Mac ha creato un file denominato **MainWindow.h** come parte del progetto Xcode lo shim viene generato per utilizzare il generatore di interfaccia:

[![Un esempio di un file con estensione h in Xcode](xib-images/xcode16.png "un esempio di un file con estensione h in Xcode")](xib-images/xcode16-large.png#lightbox)

Questo file con estensione h stub rispecchia il **MainWindow.designer.cs** che viene aggiunto automaticamente a un progetto Xamarin.Mac quando un nuovo `NSWindow` viene creato. Il file verrà usato per sincronizzare le modifiche apportate dal generatore di interfaccia e in cui verrà creato il prese e azioni in modo che gli elementi dell'interfaccia utente vengono esposti al codice c#.


#### <a name="adding-an-outlet"></a>Aggiunta di una presa di corrente

Con una conoscenza di base e le azioni sono base, si esaminerà la creazione di un Negozio o per esporre un elemento dell'interfaccia utente al codice c#.

Seguire questa procedura:

1. In Xcode nell'angolo superiore destro della schermata fare clic sul pulsante **Double Circle** (Doppio cerchio) per aprire **Assistant Editor** (Editor assistente):

    [![Selezionare l'Editor Assistente](xib-images/outlet01.png "selezionando l'Editor Assistente")](xib-images/outlet01-large.png#lightbox)
2. Xcode passa in una modalità di visualizzazione suddivisa con **Interface Editor** (Editor interfacce) da un lato e **Code Editor** (Editor codice) dall'altro.
3. Si noti che Xcode è selezionato automaticamente il **MainWindowController.m** file nel **Editor di codice**, non è corretto. Se si ricorda dalla discussione su quali punti vendita e le azioni sono di sopra, è necessario disporre di **MainWindow.h** selezionato.
4. Nella parte superiore del **Editor di codice** fare clic su di **collegamento automatico** e selezionare il **MainWindow.h** file:

    [![Selezionando il file con estensione h corretto](xib-images/outlet02.png "selezionando il file corretto. h")](xib-images/outlet02-large.png#lightbox)
5. Xcode dovrebbe ora avere il file selezionato corretto:

    [![Il file corretto selezionato](xib-images/outlet03.png "il file corretto selezionato")](xib-images/outlet03-large.png#lightbox)
6. **L'ultimo passaggio è molto importante.** Se non si dispone del file corretto selezionato, non sarà in grado di creare i punti vendita e le azioni o saranno esposti alla classe non corretta in c#!
7. Nel **Editor dell'interfaccia**, tenere premuto il **controllo** tasto della tastiera e fare clic e trascinare l'etichetta creato in precedenza nell'editor di codice sotto il `@interface MainWindow : NSWindow { }` codice:

    [![Il trascinamento per creare nuovi punti vendita](xib-images/outlet04.png "trascinamento per creare nuovi punti vendita")](xib-images/outlet04-large.png#lightbox)
8. Viene visualizzata una finestra di dialogo. Lasciare il **connessione** impostato su presa e immettere `ClickedLabel` per il **nome**:

    [![Impostazione delle proprietà presa](xib-images/outlet05.png "impostando le proprietà di uscita")](xib-images/outlet05-large.png#lightbox)
9. Fare clic su di **Connetti** pulsante per la creazione di uscita:

    ![La presa completata](xib-images/outlet06.png "presa completata")
10. Salvare le modifiche apportate al file.


#### <a name="adding-an-action"></a>Aggiunta di un'azione

Successivamente, verrà ora esaminata la creazione di un'azione per esporre l'interazione utente con l'elemento dell'interfaccia utente al codice c#.

Seguire questa procedura:

1. Assicurarsi che si trovano ancora nel **Assistente Editor** e **MainWindow.h** è visibile nel file di **Editor di codice**.
2. Nel **Editor dell'interfaccia**, tenere premuto il **controllo** tasto della tastiera e trascina fare clic sul pulsante creato in precedenza nell'editor di codice sotto il `@property (assign) IBOutlet NSTextField *ClickedLabel;` codice:

    [![Il trascinamento per creare un'azione](xib-images/action01.png "trascinamento per creare un'azione")](xib-images/action01-large.png#lightbox)
3. Modifica il **connessione** tipo di azione:

    [![Selezionare un tipo di azione](xib-images/action02.png "selezionare un tipo di azione")](xib-images/action02-large.png#lightbox)
4. Immettere `ClickedButton` in **Name** (Nome):

    [![Configurazione dell'azione](xib-images/action03.png "configurazione dell'azione")](xib-images/action03-large.png#lightbox)
5. Fare clic su di **Connetti** pulsante per la creazione di azioni:

    ![Azione completata](xib-images/action04.png "azione completata")
6. Salvare le modifiche apportate al file.

Con l'interfaccia utente remota cablate ed esposta al codice c#, tornare a Visual Studio per Mac e consente di sincronizzare le modifiche da Xcode e il generatore di interfaccia.


### <a name="writing-the-code"></a>Scrittura del codice

Con l'interfaccia utente creato e i relativi elementi dell'interfaccia utente esposte al codice tramite punti vendita e le azioni, si è pronti a scrivere il codice affinché questo programma. Ad esempio, aprire il **MainWindow.cs** file per la modifica facendo doppio clic su esso nel **soluzione riempimento**:

[![Il file MainWindow.cs](xib-images/code01.png "MainWindow.cs il file")](xib-images/code01-large.png#lightbox)

E aggiungere il codice seguente per la `MainWindow` classe per funzionare con l'uscita dell'esempio creato in precedenza:

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

Si noti che il `NSLabel` avviene in c# mediante il nome diretto che è assegnato in Xcode durante la creazione di uscita in Xcode, in questo caso, viene chiamato `ClickedLabel`. È possibile accedere qualsiasi metodo o proprietà dell'oggetto esposto allo stesso modo qualsiasi classe c# normale.

> [!IMPORTANT]
> È necessario utilizzare `AwakeFromNib`, invece di un altro metodo, ad esempio `Initialize`perché `AwakeFromNib` viene chiamato _dopo_ il sistema operativo è stato caricato e creare un'istanza di interfaccia utente dal file .xib. Se si è tentato di accedere al controllo etichetta prima del file .xib è stato completamente caricata e creare un'istanza, si otterrebbe un `NullReferenceException` errore perché il controllo etichetta non verrebbe ancora creato.

Successivamente, aggiungere la seguente classe parziale per la `MainWindow` classe:

```csharp
partial void ClickedButton (Foundation.NSObject sender) {

    // Update counter and label
    ClickedLabel.StringValue = string.Format("The button has been clicked {0} time{1}.",++numberOfTimesClicked, (numberOfTimesClicked < 2) ? "" : "s");
}
```

Questo codice collega all'azione creata in Xcode e il generatore di interfaccia e verrà chiamata ogni volta che l'utente fa clic sul pulsante.

Alcuni elementi dell'interfaccia utente sono create automaticamente nel riquadro azioni, ad esempio, gli elementi della barra dei Menu predefinito, ad esempio il **Apri...**  voce di menu (`openDocument:`). Nel **riempimento soluzione**, fare doppio clic sul **appdelegate. cs** file per aprirlo e modificarlo e aggiungere il seguente codice di `DidFinishLaunching` metodo:

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

La riga di chiave è `[Export ("openDocument:")]`, indica `NSMenu` che il **AppDelegate** dispone di un metodo `void OpenDialog (NSObject sender)` che risponde al `openDocument:` azione.

Per ulteriori informazioni sull'utilizzo dei menu, vedere il nostro [menu](~/mac/user-interface/menu.md) documentazione.


## <a name="synchronizing-changes-with-xcode"></a>La sincronizzazione delle modifiche con Xcode

Quando si passa a Visual Studio per Mac da Xcode, le modifiche effettuate in Xcode verranno sincronizzate automaticamente con il progetto Xamarin.Mac.

Se si seleziona il **MainWindow.designer.cs** nel **riempimento soluzione** sarà in grado di vedere come l'uscita e l'azione sono stati collegati backup nel codice c#:

[![La sincronizzazione delle modifiche con Xcode](xib-images/sync01.png "la sincronizzazione delle modifiche con Xcode")](xib-images/sync01-large.png#lightbox)

Si noti come le due definizioni nel **MainWindow.designer.cs** file:

```csharp
[Outlet]
AppKit.NSTextField ClickedLabel { get; set; }

[Action ("ClickedButton:")]
partial void ClickedButton (Foundation.NSObject sender);
```

Allineate con le definizioni di **MainWindow.h** file in Xcode:

```objc
@property (assign) IBOutlet NSTextField *ClickedLabel;
- (IBAction)ClickedButton:(id)sender;
```

Come si può notare, Visual Studio per Mac è in attesa di modifiche al file con estensione h e quindi si sincronizza automaticamente le modifiche nel rispettivo **. designer.cs** file esporli all'applicazione. È inoltre possibile notare che **MainWindow.designer.cs** è una classe parziale, in modo che Visual Studio per Mac senza dover modificare **MainWindow.cs** che sovrascriverà le modifiche che sono stati apportati alla classe.

È in genere, non è necessario aprire il **MainWindow.designer.cs** manualmente, è stata presentata esclusivamente per scopi didattici.

> [!IMPORTANT]
> Nella maggior parte dei casi, Visual Studio per Mac verrà automaticamente vedere tutte le modifiche apportate in Xcode e sincronizzarli al progetto Xamarin.Mac. Se la sincronizzazione non viene eseguita automaticamente, tornare a Xcode e quindi di nuovo in Visual Studio per Mac. Questa operazione avvia solitamente un ciclo di sincronizzazione.


## <a name="adding-a-new-window-to-a-project"></a>Aggiunta di una nuova finestra in un progetto

A parte la finestra principale del documento, un'applicazione Xamarin.Mac potrebbe essere necessario visualizzare altri tipi di windows per l'utente, ad esempio le preferenze o pannelli di controllo. Quando si aggiunge una nuova finestra per il progetto è necessario utilizzare sempre il **Cocoa finestra con Controller** opzione, poiché in questo modo il processo di caricamento di finestra dal .xib file semplice.

Per aggiungere una nuova finestra, eseguire le operazioni seguenti:

1. Nel **soluzione riempimento**del mouse sul progetto e scegliere **Aggiungi** > **nuovo File...** .
2. Nella finestra di dialogo Nuovo File, selezionare **Xamarin.Mac** > **Cocoa finestra con Controller**:

    ![Aggiunta di un nuovo Controller di finestra](xib-images/new01.png "aggiunge un nuovo Controller di finestra")
3. Immettere `PreferencesWindow` in **Nome** e fare clic sul pulsante **Nuovo**.
4. Fare doppio clic su di **PreferencesWindow.xib** file per aprirlo e modificarlo in Generatore di interfaccia:

    [![Modifica la finestra in Xcode](xib-images/new02.png "in Xcode la finestra di modifica")](xib-images/new02-large.png#lightbox)
5. Progettare l'interfaccia:

    [![Progettare il layout di windows](xib-images/new03.png "progettare il layout di windows")](xib-images/new03-large.png#lightbox)
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

Il `var preferences = new PreferencesWindowController ();` riga crea una nuova istanza del Controller di finestra che consente di caricare la finestra dal file .xib e ingrandita. Il `preferences.Window.MakeKeyAndOrderFront (this);` riga consente di visualizzare la nuova finestra per l'utente.

Se si esegue il codice e selezionare il **preferenze...**  dal **Menu applicazione**, verrà visualizzata la finestra:

![Esecuzione dell'applicazione di esempio](xib-images/new04.png "esecuzione dell'applicazione di esempio")

Per ulteriori informazioni sull'uso di Windows in un'applicazione Xamarin.Mac, consultare il nostro [Windows](~/mac/user-interface/window.md) documentazione.


## <a name="adding-a-new-view-to-a-project"></a>Aggiunta di una nuova visualizzazione a un progetto

Esistono casi in cui è più semplice suddividere la progettazione della finestra in più, i file .xib più gestibili. Ad esempio, come il contenuto della finestra principale di commutazione quando si seleziona un elemento della barra degli strumenti in un **finestra Preferenze** o scambiare contenuto in risposta a un **elenco origine** selezione.

Quando si aggiunge una nuova visualizzazione per il progetto è necessario utilizzare sempre il **Cocoa vista con Controller** opzione, poiché in questo modo il processo di caricamento della visualizzazione dal .xib file semplice.

Per aggiungere una nuova vista, eseguire le operazioni seguenti:

1. Nel **soluzione riempimento**del mouse sul progetto e scegliere **Aggiungi** > **nuovo File...** .
2. Nella finestra di dialogo Nuovo File, selezionare **Xamarin.Mac** > **Cocoa vista con Controller**:

    ![Aggiunta di una nuova vista](xib-images/view01.png "aggiunta una nuova vista")
3. Immettere `SubviewTable` in **Nome** e fare clic sul pulsante **Nuovo**.
4. Fare doppio clic su di **SubviewTable.xib** file per aprirlo e modificarlo in Generatore di interfaccia e la progettazione dell'interfaccia utente:

    [![Progettare la nuova vista in Xcode](xib-images/view02.png "progettazione la nuova vista in Xcode")](xib-images/view02-large.png#lightbox)
5. Associare le prese e le azioni necessarie.
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Modificare successivamente il **SubviewTable.cs** e aggiungere il codice seguente per il **AwakeFromNib** file per popolare la nuova vista quando viene caricato:

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

Aggiungere un enum al progetto per rilevare la visualizzazione viene attualmente visualizzato. Ad esempio, **SubviewType.cs**:

```csharp
public enum SubviewType
{
    None,
    TableView,
    OutlineView,
    ImageView
}
```

Modificare il file .xib della finestra che verrà utilizzato la visualizzazione e visualizzarla. Aggiungere un **vista personalizzata** che fungerà da contenitore per la visualizzazione dopo il caricamento in memoria da codice c# ed esporre una presa denominato `ViewContainer`:

[![Creazione di uscita obbligatorio](xib-images/view03.png "la creazione di uscita obbligatorio")](xib-images/view03-large.png#lightbox)

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

Quando è necessario visualizzare una nuova vista caricata da un file .xib nel contenitore della finestra (la **vista personalizzata** aggiunte in precedenza), questo codice consente di gestire la rimozione di qualsiasi visualizzazione esistente e lo scambio out per quella nuova. Cerca per visualizzarlo che si dispone già di una visualizzazione, se così rimuove dalla schermata. Successivamente accetta la vista che è stata passata in (durante il caricamento da un Controller di visualizzazione) Ridimensiona desiderato nell'Area di contenuto e lo aggiunge al contenuto per la visualizzazione.

Per visualizzare una nuova vista, utilizzare il codice seguente:

```csharp
DisplaySubview(new SubviewTableController(), SubviewType.TableView);
```

Questo crea una nuova istanza del Controller di visualizzazione per la nuova vista da visualizzare, imposta il tipo (come specificato dal tipo enum aggiunto al progetto) e utilizza il `DisplaySubview` metodo aggiunto alla classe della finestra per visualizzare la vista. Ad esempio:

[![Esecuzione dell'applicazione di esempio](xib-images/view04.png "esecuzione dell'applicazione di esempio")](xib-images/view04-large.png#lightbox)

Per ulteriori informazioni sull'uso di Windows in un'applicazione Xamarin.Mac, consultare il nostro [Windows](~/mac/user-interface/window.md) e [finestre di dialogo](~/mac/user-interface/dialog.md) documentazione.


## <a name="summary"></a>Riepilogo

In questo articolo è stato applicato l'utilizzo dei file in un'applicazione Xamarin.Mac .xib un'analisi approfondita. È stato illustrato i diversi tipi e dei file .xib viene utilizzato per creare l'interfaccia utente dell'applicazione, su come creare e gestire .xib file in Xcode interfaccia generatore e come utilizzare i file .xib nel codice c#.


## <a name="related-links"></a>Collegamenti correlati

- [MacImages (sample)](https://developer.xamarin.com/samples/mac/MacImages/) (MacImages - Esempio)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [Menu](~/mac/user-interface/menu.md)
- [Dialogs](~/mac/user-interface/dialog.md) (Finestre di dialogo)
- [Utilizzo di immagini](~/mac/app-fundamentals/image.md)
- [linee guida dell'interfaccia umana macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
