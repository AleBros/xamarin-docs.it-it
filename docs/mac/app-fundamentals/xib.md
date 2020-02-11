---
title: file con estensione XIB in Xamarin.Mac
description: Questo articolo illustra l'uso dei file con estensione XIB creati nella Interface Builder di Xcode per creare e gestire interfacce utente per un'applicazione Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 6AF3D216-448D-4B2D-9026-74E4FFF5923A
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 6d40dd3cc994ef8ab21ffb9658f226d36cd97913
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021776"
---
# <a name="xib-files-in-xamarinmac"></a>file con estensione XIB in Xamarin.Mac

_Questo articolo illustra l'uso dei file con estensione XIB creati nella Interface Builder di Xcode per creare e gestire interfacce utente per un'applicazione Xamarin.Mac._

> [!NOTE]
> Il modo migliore per creare un'interfaccia utente per un'app Xamarin.Mac è con gli storyboard. Questa documentazione è stata lasciata per motivi cronologici e per l'utilizzo di progetti Xamarin.Mac precedenti. Per ulteriori informazioni, vedere la documentazione [introduttiva per gli storyboard](~/mac/platform/storyboards/index.md) .

## <a name="overview"></a>Panoramica

Quando si lavora C# con e .NET in un'applicazione Xamarin.Mac, è possibile accedere agli stessi elementi e strumenti dell'interfaccia utente che uno sviluppatore lavora in *Objective-C* e *Xcode* . Poiché Xamarin.Mac si integra direttamente con Xcode, è possibile usare _Interface Builder_ di Xcode per creare e gestire le interfacce utente oppure, facoltativamente, crearle direttamente C# nel codice.

Un file con estensione XIB viene usato da macOS per definire gli elementi dell'interfaccia utente dell'applicazione (ad esempio menu, finestre, visualizzazioni, etichette, campi di testo) che vengono creati e gestiti graficamente nel Interface Builder di Xcode.

[![Esempio di app in esecuzione](xib-images/intro01.png "Esempio di app in esecuzione")](xib-images/intro01-large.png#lightbox)

In questo articolo verranno illustrate le nozioni di base sull'uso dei file con estensione XIB in un'applicazione Xamarin.Mac. Si consiglia di usare prima di tutto l'articolo [Hello, Mac](~/mac/get-started/hello-mac.md) , che illustra i concetti chiave e le tecniche che verranno usati in questo articolo.

Si consiglia di esaminare la sezione [esporre C# classi/metodi in Objective-c](~/mac/internals/how-it-works.md) del documento [interno di Xamarin.Mac](~/mac/internals/how-it-works.md) , spiegando gli attributi `Register` e `Export` usati per collegare le C# classi a Objective-c. oggetti ed elementi dell'interfaccia utente.

## <a name="introduction-to-xcode-and-interface-builder"></a>Introduzione a Xcode e Interface Builder

Come parte di Xcode, Apple ha creato uno strumento denominato Interface Builder, che consente di creare visivamente l'interfaccia utente in una finestra di progettazione. Xamarin.Mac si integra perfettamente con Interface Builder, consentendo di creare l'interfaccia utente con gli stessi strumenti che gli utenti di Objective-C.

### <a name="components-of-xcode"></a>Componenti di Xcode

Quando si apre un file con estensione XIB in Xcode da Visual Studio per Mac, questo viene aperto con uno **strumento di navigazione del progetto** a sinistra, la gerarchia dell' **interfaccia** e l' **Editor dell'interfaccia** al centro e una sezione **Proprietà & utilità** a destra:

[![Componenti dell'interfaccia utente di Xcode](xib-images/xcode03.png "Componenti dell'interfaccia utente di Xcode")](xib-images/xcode03-large.png#lightbox)

Di seguito viene illustrato il funzionamento di ognuna di queste sezioni di Xcode e il modo in cui usarle per creare l'interfaccia per l'applicazione Xamarin.Mac.

#### <a name="project-navigation"></a>Esplorazione del progetto

Quando si apre un file con estensione XIB per la modifica in Xcode, Visual Studio per Mac crea un file di progetto Xcode in background per comunicare le modifiche tra e Xcode. Successivamente, quando si torna a Visual Studio per Mac da Xcode, tutte le modifiche apportate a questo progetto vengono sincronizzate con il progetto Xamarin.Mac per Visual Studio per Mac.

La sezione **esplorazione del progetto** consente di spostarsi tra tutti i file che compongono questo progetto _shim_ Xcode. In genere, saranno interessati solo i file con estensione XIB in questo elenco, ad esempio **MainMenu. xib** e **MainWindow. xib**.

#### <a name="interface-hierarchy"></a>Gerarchia di interfaccia

La sezione **gerarchia di interfaccia** consente di accedere facilmente a diverse proprietà chiave dell'interfaccia utente, ad esempio i **segnaposto** e la **finestra**principale. È anche possibile usare questa sezione per accedere ai singoli elementi (visualizzazioni) che costituiscono l'interfaccia utente e a regolare il modo in cui sono nidificati trascinandoli all'interno della gerarchia.

#### <a name="interface-editor"></a>Editor interfacce

La sezione **Editor interfacce** fornisce la superficie su cui si esegue il layout grafico dell'interfaccia utente. Gli elementi vengono trascinati dalla sezione **Library** della sezione **Properties & Utilities** per creare la progettazione. Quando si aggiungono elementi dell'interfaccia utente (viste) all'area di progettazione, questi verranno aggiunti alla sezione **gerarchia dell'interfaccia** nell'ordine in cui sono visualizzati nell' **editor di interfaccia**.

#### <a name="properties--utilities"></a>Proprietà & utilità

La sezione **properties & Utilities** è divisa in due sezioni principali che verranno elaborate, **Properties** (detti anche ispettori) e la **libreria**:

![Controllo proprietà](xib-images/xcode04.png "Controllo proprietà")

Inizialmente questa sezione è quasi vuota. Tuttavia, se si seleziona un elemento nell' **editor di interfaccia** o nella **gerarchia dell'interfaccia**, la sezione **Properties** verrà popolata con informazioni sull'elemento e sulle proprietà specificati che è possibile modificare.

Nella sezione **Properties** (Proprietà) sono incluse 8 *schede di controllo*, come mostra la figura seguente:

[![Panoramica di tutti i controlli](xib-images/xcode05.png "Panoramica di tutti i controlli")](xib-images/xcode05-large.png#lightbox)

Da sinistra a destra, queste schede sono:

- **File Inspector** (Controllo file): visualizza le informazioni sui file, ad esempio il nome file e il percorso del file Xib in fase di modifica.
- **Quick Help** (Guida rapida): questa scheda offre una guida contestuale in base all'elemento selezionato in Xcode.
- **Identity Inspector** (Controllo identità): offre informazioni sul comando o la visualizzazione selezionata.
- **Attributi Inspector** : il controllo attributi consente di personalizzare diversi attributi della visualizzazione o del controllo selezionato.
- **Size Inspector** : il controllo delle dimensioni consente di controllare le dimensioni e il comportamento di ridimensionamento del controllo/visualizzazione selezionato.
- **Connections Inspector** : il controllo Connections Mostra le connessioni Outlet e Action dei controlli selezionati. Gli Outlet e le azioni verranno esaminati in pochi istanti.
- **Bindings Inspector** : il controllo Binding consente di configurare i controlli in modo che i relativi valori vengano associati automaticamente ai modelli di dati.
- **View Effects Inspector** : il controllo degli effetti di visualizzazione consente di specificare gli effetti sui controlli, ad esempio le animazioni.

Nella sezione **libreria** è possibile trovare controlli e oggetti da inserire nella finestra di progettazione per creare graficamente l'interfaccia utente:

![Esempio di controllo libreria](xib-images/xcode06.png "Esempio di controllo libreria")

Ora che si ha familiarità con l'IDE di Xcode e Interface Builder, è possibile esaminarne l'uso per creare un'interfaccia utente.

## <a name="creating-and-maintaining-windows-in-xcode"></a>Creazione e gestione di Windows in Xcode

Il metodo preferito per la creazione dell'interfaccia utente di un'app Xamarin.Mac è costituito dagli storyboard. per ulteriori informazioni, vedere la documentazione [introduttiva per gli storyboard](~/mac/platform/storyboards/index.md) . di conseguenza, tutti i nuovi progetti avviati in Xamarin.Mac utilizzeranno gli storyboard predefinita.

Per passare all'utilizzo di un'interfaccia utente basata su XIB, eseguire le operazioni seguenti:

1. Aprire Visual Studio per Mac e avviare un nuovo progetto Xamarin.Mac.
2. Nella **riquadro della soluzione**fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi**  > **nuovo file...**
3. Selezionare **Mac**  > **controller Windows**:

    ![Aggiunta di un nuovo controller di finestra](xib-images/setup00.png "Aggiunta di un nuovo controller di finestra")
4. Immettere `MainWindow` per il nome e fare clic sul pulsante **nuovo** :

    ![Aggiunta di una nuova finestra principale](xib-images/setup01.png "Aggiunta di una nuova finestra principale")
5. Fare di nuovo clic con il pulsante destro del mouse sul progetto e scegliere **aggiungi**  > **nuovo file...**
6. Selezionare **Mac**  > **menu principale**:

    ![Aggiunta di un nuovo menu principale](xib-images/setup02.png "Aggiunta di un nuovo menu principale")
7. Lasciare il nome `MainMenu` e fare clic sul pulsante **nuovo** .
8. Nella **riquadro della soluzione** selezionare il file **Main. Storyboard** , fare clic con il pulsante destro del mouse e scegliere **Rimuovi**:

    ![Selezione dello storyboard principale](xib-images/setup03.png "Selezione dello storyboard principale")
9. Nella finestra di dialogo Rimuovi fare clic sul pulsante **Elimina** :

    ![Conferma dell'eliminazione](xib-images/setup04.png "Conferma dell'eliminazione")
10. Nel **riquadro della soluzione**fare doppio clic sul file **info. plist** per aprirlo per la modifica.
11. Selezionare `MainMenu` dall'elenco a discesa dell' **interfaccia principale** :

    [![Impostazione del menu principale](xib-images/setup05.png "Impostazione del menu principale")](xib-images/setup05-large.png#lightbox)
12. Nella **riquadro della soluzione**fare doppio clic sul file **MainMenu. xib** per aprirlo per la modifica nella Interface Builder di Xcode.
13. In **controllo libreria**Digitare `object` nel campo di ricerca, quindi trascinare un nuovo **oggetto** nell'area di progettazione:

    [![Modifica del menu principale](xib-images/setup06.png "Modifica del menu principale")](xib-images/setup06-large.png#lightbox)
14. In **Identity Inspector**immettere `AppDelegate` per la **classe**:

    [![Selezione del delegato dell'app](xib-images/setup07.png "Selezione del delegato dell'app")](xib-images/setup07-large.png#lightbox)
15. Selezionare **il proprietario del file** dalla **gerarchia dell'interfaccia**, passare al **controllo della connessione** e trascinare una riga dal delegato all' **oggetto** `AppDelegate` appena aggiunto al progetto:

    [![Connessione del delegato dell'app](xib-images/setup08.png "Connessione del delegato dell'app")](xib-images/setup08-large.png#lightbox)
16. Salvare le modifiche e tornare a Visual Studio per Mac.

Con tutte queste modifiche apportate, modificare il file **AppDelegate.cs** e renderlo simile al seguente:

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

A questo punto, la finestra principale dell'app è definita in un file con **estensione XIB** automaticamente incluso nel progetto quando si aggiunge un controller di finestra. Per modificare la progettazione di Windows, nella **riquadro della soluzione**fare doppio clic sul file **MainWindow. xib** :

![Selezione del file MainWindow. xib](xib-images/edit01.png "Selezione del file MainWindow. xib")

Verrà aperta la progettazione della finestra nella Interface Builder di Xcode:

[![Modifica di MainWindow. xib](xib-images/edit02.png "Modifica di MainWindow. xib")](xib-images/edit02-large.png#lightbox)

### <a name="standard-window-workflow"></a>Flusso di lavoro finestra standard

Per qualsiasi finestra creata e utilizzata nell'applicazione Xamarin.Mac, il processo è fondamentalmente lo stesso:

1. Per le nuove finestre che non sono aggiunte automaticamente al progetto, aggiungere una nuova definizione di finestra al progetto.
2. Fare doppio clic sul file. xib per aprire la finestra progettazione per la modifica nella Interface Builder di Xcode.
3. Impostare le proprietà della finestra obbligatorie in **controllo attributi** e il **controllo dimensioni**.
4. Trascinare i controlli necessari per compilare l'interfaccia e configurarli nel **controllo attributo**.
5. Usare il **controllo delle dimensioni** per gestire il ridimensionamento per gli elementi dell'interfaccia utente.
6. Esporre gli elementi dell'interfaccia utente della C# finestra al codice tramite Outlet e azioni.
7. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

### <a name="designing-a-window-layout"></a>Progettazione di un layout di finestra

Il processo di definizione di un'interfaccia utente in Interface Builder è fondamentalmente lo stesso per ogni elemento aggiunto:

1. Trovare il controllo desiderato in **Library Inspector** e trascinarlo nell' **Editor dell'interfaccia** e posizionarlo.
2. Impostare le proprietà della finestra obbligatorie nel **controllo attributo**.
3. Usare il **controllo delle dimensioni** per gestire il ridimensionamento per gli elementi dell'interfaccia utente.
4. Se si usa una classe personalizzata, impostarla nel controllo di **identità**.
5. Esporre gli elementi dell'interfaccia C# utente al codice tramite Outlet e azioni.
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Di seguito è riportato un esempio:

1. In Xcode trascinare **Push Button** (Pulsante Push) da **Library Section** (Sezione librerie):

    [![Selezione di un pulsante dalla libreria](xib-images/xcode07.png "Selezione di un pulsante dalla libreria")](xib-images/xcode07-large.png#lightbox)
2. Rilasciare il pulsante sulla **finestra** nell'editor di **interfacce**:

    [![Aggiunta di un pulsante alla finestra](xib-images/xcode08.png "Aggiunta di un pulsante alla finestra")](xib-images/xcode08-large.png#lightbox)
3. Fare clic sulla proprietà **Title** (Titolo) in **Attribute Inspector** (Controllo attributi) e modificare il titolo del pulsante in `Click Me`:

    ![Impostazione degli attributi dei pulsanti](xib-images/xcode09.png "Impostazione degli attributi dei pulsanti")
4. Trascinare **Label** (Etichetta) da **Library Section** (Sezione librerie):

    [![Selezione di un'etichetta nella libreria](xib-images/xcode10.png "Selezione di un'etichetta nella libreria")](xib-images/xcode10-large.png#lightbox)
5. Trascinare l'etichetta in **Window** (Finestra) accanto al pulsante in **Interface Editor** (Editor interfaccia):

    [![Aggiunta di un'etichetta alla finestra](xib-images/xcode11.png "Aggiunta di un'etichetta alla finestra")](xib-images/xcode11-large.png#lightbox)
6. Selezionare il quadratino a destra sull'etichetta e trascinarlo fino a quando non è vicino al bordo della finestra:

    [![Ridimensionamento dell'etichetta](xib-images/xcode12.png "Ridimensionamento dell'etichetta")](xib-images/xcode12-large.png#lightbox)
7. Con l'etichetta ancora selezionata nell' **editor di interfaccia**, passare al **controllo dimensioni**:

    ![Selezione del controllo dimensioni](xib-images/xcode13.png "Selezione del controllo dimensioni")
8. Nella **casella ridimensionamento** automatico fare clic sulla **parentesi quadra di colore rossa** a destra e sulla **freccia orizzontale in rosso** al centro:

    ![Modifica delle proprietà di ridimensionamento automatico](xib-images/xcode14.png "Modifica delle proprietà di ridimensionamento automatico")
9. In questo modo si garantisce che l'etichetta si estenderà per espandersi e compattarla quando la finestra viene ridimensionata nell'applicazione in esecuzione. Le **parentesi quadre rosse** e la parte superiore e sinistra della casella della **casella di ridimensionamento** automatico indicano che l'etichetta deve essere bloccata nelle posizioni X e Y specificate.
10. Salvare le modifiche apportate all'interfaccia utente

Quando si ridimensionano e si spostano i controlli, è necessario notare che Interface Builder offre suggerimenti di snap utili basati sulle [linee guida dell'interfaccia umana di OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). Queste linee guida consentono di creare applicazioni di alta qualità che avranno un aspetto familiare per gli utenti Mac.

Se si osserva la sezione **gerarchia dell'interfaccia** , si noti come vengono visualizzati il layout e la gerarchia degli elementi che costituiscono l'interfaccia utente:

![Selezione di un elemento nella gerarchia dell'interfaccia](xib-images/xcode15.png "Selezione di un elemento nella gerarchia dell'interfaccia")

Da qui è possibile selezionare gli elementi da modificare o trascinare per riordinare gli elementi dell'interfaccia utente, se necessario. Se, ad esempio, un elemento dell'interfaccia utente è stato coperto da un altro elemento, è possibile trascinarlo nella parte inferiore dell'elenco per impostarlo come elemento in primo piano nella finestra.

Per ulteriori informazioni sull'utilizzo di Windows in un'applicazione Xamarin.Mac, consultare la documentazione di [Windows](~/mac/user-interface/window.md) .

## <a name="exposing-ui-elements-to-c-code"></a>Esposizione di elementi dell'interfaccia C# utente al codice

Una volta terminato di definire l'aspetto dell'interfaccia utente in Interface Builder, è necessario esporre gli elementi dell'interfaccia utente in modo che possano accedervi dal C# codice. A tale scopo, si utilizzeranno le azioni e gli outlet.

### <a name="setting-a-custom-main-window-controller"></a>Impostazione di un controller della finestra principale personalizzato

Per poter creare Outlet e azioni per esporre gli elementi dell'interfaccia utente C# al codice, l'app Xamarin.Mac dovrà usare un controller di finestra personalizzato.

Procedere come descritto di seguito:

1. Aprire lo storyboard dell'app nella Interface Builder di Xcode.
2. Selezionare il `NSWindowController` nel Area di progettazione.
3. Passare alla visualizzazione **Identity Inspector** e immettere `WindowController` come nome della **classe**:

    [![Modifica del nome della classe](xib-images/windowcontroller01.png "Modifica del nome della classe")](xib-images/windowcontroller01-large.png#lightbox)
4. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione.
5. Un file **WindowController.cs** verrà aggiunto al progetto nella **riquadro della soluzione** Visual Studio per Mac:

    ![Nome della nuova classe in Visual Studio per Mac](xib-images/windowcontroller02.png "Nome della nuova classe in Visual Studio per Mac")
6. Riaprire lo storyboard nella Interface Builder di Xcode.
7. Il file **WindowController. h** sarà disponibile per l'uso:

    [![Il file con estensione h corrispondente in Xcode](xib-images/windowcontroller03.png "Il file con estensione h corrispondente in Xcode")](xib-images/windowcontroller03-large.png#lightbox)

### <a name="outlets-and-actions"></a>Outlet e azioni

Che cosa sono gli Outlet e le azioni? Nella programmazione tradizione dell'interfaccia utente .NET, i comandi dell'interfaccia utente vengono esposti automaticamente come proprietà quando vengono aggiunti. In Mac ciò non avviene, la semplice aggiunta di un comando in una visualizzazione non lo rende accessibile al codice. Lo sviluppatore deve esporre esplicitamente l'elemento dell'interfaccia utente al codice. A tale scopo, Apple fornisce due opzioni:

- **Outlet**: gli outlet sono analoghi alle proprietà. Se si collega un controllo a un Outlet, questo viene esposto al codice tramite una proprietà, in modo che sia possibile eseguire operazioni come collegare gestori eventi, chiamare metodi su di esso e così via.
- **Azioni**: le azioni sono analoghe al modello di comando in WPF. Ad esempio, quando un'azione viene eseguita su un controllo, ad esempio un clic del pulsante, il controllo chiamerà automaticamente un metodo nel codice. Le azioni sono potenti e convenienti perché è possibile collegare molti controlli alla stessa azione.

In Xcode gli Outlet e le azioni vengono aggiunti direttamente nel codice tramite il *trascinamento del controllo*. In particolare, ciò significa che per creare un outlet o un'azione, è possibile scegliere l'elemento di controllo che si vuole aggiungere a un punto di uscita o un'azione, tenendo premuto il pulsante di **controllo** sulla tastiera e trascinando il controllo direttamente nel codice.

Per gli sviluppatori Xamarin.Mac, ciò significa che si trascinano i file stub di Objective-C che C# corrispondono al file in cui si vuole creare il punto di uscita o l'azione. Visual Studio per Mac creato un file denominato **MainWindow. h** come parte del progetto shim Xcode generato per usare il Interface Builder:

[![Esempio di un file con estensione h in Xcode](xib-images/xcode16.png "Esempio di un file con estensione h in Xcode")](xib-images/xcode16-large.png#lightbox)

Questo file stub. h rispecchia il **MainWindow.designer.cs** che viene aggiunto automaticamente a un progetto Xamarin.Mac quando viene creata una nuova `NSWindow`. Questo file verrà usato per sincronizzare le modifiche apportate da Interface Builder ed è il punto in cui verranno creati gli Outlet e le azioni in modo che C# gli elementi dell'interfaccia utente vengano esposti al codice.

#### <a name="adding-an-outlet"></a>Aggiunta di un Outlet

Con una conoscenza di base degli Outlet e delle azioni, verrà ora esaminata la creazione di un outlet per esporre un elemento dell' C# interfaccia utente al codice.

Procedere come descritto di seguito:

1. In Xcode nell'angolo superiore destro della schermata fare clic sul pulsante **Double Circle** (Doppio cerchio) per aprire **Assistant Editor** (Editor assistente):

    [![Selezione dell'editor di assistente](xib-images/outlet01.png "Selezione dell'editor di assistente")](xib-images/outlet01-large.png#lightbox)
2. Xcode passa in una modalità di visualizzazione suddivisa con **Interface Editor** (Editor interfacce) da un lato e **Code Editor** (Editor codice) dall'altro.
3. Si noti che Xcode ha selezionato automaticamente il file **MainWindowController. m** nell' **editor di codice**, che non è corretto. Se si ricordano i punti vendita e le azioni precedenti, è necessario aver selezionato **MainWindow. h** .
4. Nella parte superiore dell' **editor di codice** fare clic sul **collegamento automatico** e selezionare il file **MainWindow. h** :

    [![Selezione del file con estensione h corretto](xib-images/outlet02.png "Selezione del file con estensione h corretto")](xib-images/outlet02-large.png#lightbox)
5. Xcode dovrebbe ora avere il file selezionato corretto:

    [![Il file selezionato è corretto](xib-images/outlet03.png "Il file selezionato è corretto")](xib-images/outlet03-large.png#lightbox)
6. **L'ultimo passaggio è molto importante.** Se non è stato selezionato il file corretto, non sarà possibile creare Outlet e azioni oppure verranno esposti alla classe errata in C#.
7. Nell' **editor di interfacce**mantenere premuto il tasto **CTRL** sulla tastiera e fare clic su trascinare l'etichetta creata sopra nell'editor di codice sotto il codice `@interface MainWindow : NSWindow { }`:

    [![Trascinamento per creare una nuova uscita](xib-images/outlet04.png "Trascinamento per creare una nuova uscita")](xib-images/outlet04-large.png#lightbox)
8. Viene visualizzata una finestra di dialogo. Lasciare la **connessione** impostata su Outlet e immettere `ClickedLabel` per il **nome**:

    [![Impostazione delle proprietà di uscita](xib-images/outlet05.png "Impostazione delle proprietà di uscita")](xib-images/outlet05-large.png#lightbox)
9. Fare clic sul pulsante **Connetti** per creare l'Outlet:

    ![Outlet completato](xib-images/outlet06.png "Outlet completato")
10. Salvare le modifiche apportate al file.

#### <a name="adding-an-action"></a>Aggiunta di un'azione

Verrà ora esaminata la creazione di un'azione per esporre un'interazione dell'utente con l' C# elemento dell'interfaccia utente al codice.

Procedere come descritto di seguito:

1. Assicurarsi di essere ancora nell'editor di **Assistente** e che il file **MainWindow. h** sia visibile nell' **editor di codice**.
2. Nell' **editor di interfacce**mantenere premuto il tasto **CTRL** sulla tastiera e fare clic su trascinare il pulsante creato in precedenza nell'editor di codice sotto il codice `@property (assign) IBOutlet NSTextField *ClickedLabel;`:

    [![Trascinamento per creare un'azione](xib-images/action01.png "Trascinamento per creare un'azione")](xib-images/action01-large.png#lightbox)
3. Modificare il tipo di **connessione** in azione:

    [![Selezionare un tipo di azione](xib-images/action02.png "Selezionare un tipo di azione")](xib-images/action02-large.png#lightbox)
4. Immettere `ClickedButton` in **Name** (Nome):

    [![Configurazione dell'azione](xib-images/action03.png "Configurazione dell'azione")](xib-images/action03-large.png#lightbox)
5. Fare clic sul pulsante **Connetti** per creare un'azione:

    ![Azione completata](xib-images/action04.png "Azione completata")
6. Salvare le modifiche apportate al file.

Con l'interfaccia utente collegata ed esposta al C# codice, tornare a Visual Studio per Mac e consentire la sincronizzazione delle modifiche da Xcode e Interface Builder.

### <a name="writing-the-code"></a>Scrittura del codice

Dopo aver creato l'interfaccia utente e i relativi elementi dell'interfaccia utente esposti al codice tramite Outlet e azioni, è possibile scrivere il codice per rendere disponibile il programma. Ad esempio, aprire il file **MainWindow.cs** per la modifica facendo doppio clic su di esso nel **riquadro della soluzione**:

[![File MainWindow.cs](xib-images/code01.png "File MainWindow.cs")](xib-images/code01-large.png#lightbox)

Aggiungere quindi il codice seguente alla classe `MainWindow` per lavorare con l'Outlet di esempio creato in precedenza:

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

Si noti che il `NSLabel` è accessibile C# dal nome diretto assegnato in Xcode quando è stato creato il relativo Outlet in Xcode, in questo caso, viene chiamato `ClickedLabel`. È possibile accedere a qualsiasi metodo o proprietà dell'oggetto esposto nello stesso modo in cui si esegue C# una classe normale.

> [!IMPORTANT]
> È necessario usare `AwakeFromNib`, anziché un altro metodo, ad esempio `Initialize`, perché `AwakeFromNib` viene chiamato _dopo che_ il sistema operativo ha caricato e creato un'istanza dell'interfaccia utente dal file. xib. Se si è tentato di accedere al controllo Label prima che il file con estensione XIB sia stato completamente caricato e ne venga creata un'istanza, verrà generato un errore `NullReferenceException` perché il controllo Label non è ancora stato creato.

Successivamente, aggiungere la classe parziale seguente alla classe `MainWindow`:

```csharp
partial void ClickedButton (Foundation.NSObject sender) {

    // Update counter and label
    ClickedLabel.StringValue = string.Format("The button has been clicked {0} time{1}.",++numberOfTimesClicked, (numberOfTimesClicked < 2) ? "" : "s");
}
```

Questo codice si connette all'azione creata in Xcode e Interface Builder e verrà chiamata ogni volta che l'utente fa clic sul pulsante.

Alcuni elementi dell'interfaccia utente hanno automaticamente le azioni predefinite, ad esempio gli elementi nella barra dei menu predefinita, ad esempio la voce di menu **Apri..** . (`openDocument:`). Nel **riquadro della soluzione**fare doppio clic sul file **AppDelegate.cs** per aprirlo per la modifica e aggiungere il codice seguente sotto il metodo `DidFinishLaunching`:

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

La riga chiave è `[Export ("openDocument:")]`, indica `NSMenu` che la **AppDelegate** dispone di un metodo `void OpenDialog (NSObject sender)` che risponde all'azione `openDocument:`.

Per ulteriori informazioni sull'utilizzo dei menu, consultare la documentazione relativa ai [menu](~/mac/user-interface/menu.md) .

## <a name="synchronizing-changes-with-xcode"></a>Sincronizzazione delle modifiche con Xcode

Quando si torna al Visual Studio per Mac da Xcode, tutte le modifiche apportate in Xcode verranno sincronizzate automaticamente con il progetto Xamarin.Mac.

Se si seleziona **MainWindow.designer.cs** nel **riquadro della soluzione** sarà possibile vedere come l'Outlet e l'azione sono stati collegati nel C# codice:

[![Sincronizzazione delle modifiche con Xcode](xib-images/sync01.png "Sincronizzazione delle modifiche con Xcode")](xib-images/sync01-large.png#lightbox)

Si noti come le due definizioni nel file **MainWindow.designer.cs** :

```csharp
[Outlet]
AppKit.NSTextField ClickedLabel { get; set; }

[Action ("ClickedButton:")]
partial void ClickedButton (Foundation.NSObject sender);
```

Allineare con le definizioni nel file **MainWindow. h** in Xcode:

```objc
@property (assign) IBOutlet NSTextField *ClickedLabel;
- (IBAction)ClickedButton:(id)sender;
```

Come si può notare, Visual Studio per Mac resta in attesa delle modifiche apportate al file con estensione h e quindi Sincronizza automaticamente tali modifiche nel rispettivo file con **estensione designer.cs** per esporle all'applicazione. È anche possibile notare che **MainWindow.designer.cs** è una classe parziale, in modo che Visual Studio per Mac non debba modificare **MainWindow.cs** che sovrascriverà le modifiche apportate alla classe.

Normalmente non sarà mai necessario aprire il **MainWindow.designer.cs** , ma è stato presentato solo a scopo didattico.

> [!IMPORTANT]
> Nella maggior parte dei casi, Visual Studio per Mac visualizzerà automaticamente tutte le modifiche apportate in Xcode e le sincronizza con il progetto Xamarin.Mac. Se la sincronizzazione non viene eseguita automaticamente, tornare a Xcode e quindi di nuovo in Visual Studio per Mac. Questa operazione avvia solitamente un ciclo di sincronizzazione.

## <a name="adding-a-new-window-to-a-project"></a>Aggiunta di una nuova finestra a un progetto

A parte la finestra del documento principale, un'applicazione Xamarin.Mac potrebbe dover visualizzare altri tipi di finestre per l'utente, ad esempio le preferenze o i pannelli di controllo. Quando si aggiunge una nuova finestra al progetto, è consigliabile usare sempre la **finestra Cocoa con** l'opzione Controller, in quanto ciò rende più semplice il processo di caricamento della finestra dal file con estensione XIB.

Per aggiungere una nuova finestra, procedere come segue:

1. Nella **riquadro della soluzione**fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi**  > **nuovo file..** .
2. Nella finestra di dialogo nuovo file selezionare **Xamarin.Mac**  > **finestra Cocoa con controller**:

    ![Aggiunta di un nuovo controller di finestra](xib-images/new01.png "Aggiunta di un nuovo controller di finestra")
3. Immettere `PreferencesWindow` in **Nome** e fare clic sul pulsante **Nuovo**.
4. Fare doppio clic sul file **PreferencesWindow. xib** per aprirlo per la modifica in Interface Builder:

    [![Modifica della finestra in Xcode](xib-images/new02.png "Modifica della finestra in Xcode")](xib-images/new02-large.png#lightbox)
5. Progettare l'interfaccia:

    [![Progettazione del layout di Windows](xib-images/new03.png "Progettazione del layout di Windows")](xib-images/new03-large.png#lightbox)
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Aggiungere il codice seguente a **AppDelegate.cs** per visualizzare la nuova finestra:

```csharp
[Export("applicationPreferences:")]
void ShowPreferences (NSObject sender)
{
    var preferences = new PreferencesWindowController ();
    preferences.Window.MakeKeyAndOrderFront (this);
}
```

La riga di `var preferences = new PreferencesWindowController ();` crea una nuova istanza del controller della finestra che carica la finestra dal file con estensione XIB e la gonfia. La riga `preferences.Window.MakeKeyAndOrderFront (this);` Visualizza la nuova finestra per l'utente.

Se si esegue il codice e si selezionano le **Preferenze...** dal **menu applicazione**, verrà visualizzata la finestra:

![Esecuzione dell'app di esempio](xib-images/new04.png "Esecuzione dell'app di esempio")

Per ulteriori informazioni sull'utilizzo di Windows in un'applicazione Xamarin.Mac, consultare la documentazione di [Windows](~/mac/user-interface/window.md) .

## <a name="adding-a-new-view-to-a-project"></a>Aggiunta di una nuova visualizzazione a un progetto

In alcuni casi è più semplice suddividere la progettazione della finestra in più file con estensione XIB gestibili. Ad esempio, come disattivare il contenuto della finestra principale quando si seleziona un elemento della barra degli strumenti in una **finestra delle preferenze** o si scambia il contenuto in risposta a una selezione dell' **elenco di origine** .

Quando si aggiunge una nuova visualizzazione al progetto, è consigliabile usare sempre la **visualizzazione Cocoa con** l'opzione Controller, perché ciò rende più semplice il processo di caricamento della visualizzazione dal file con estensione XIB.

Per aggiungere una nuova vista, procedere come segue:

1. Nella **riquadro della soluzione**fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi**  > **nuovo file..** .
2. Nella finestra di dialogo nuovo file selezionare **Xamarin.Mac**  > **visualizzazione Cocoa con controller**:

    ![Aggiunta di una nuova visualizzazione](xib-images/view01.png "Aggiunta di una nuova visualizzazione")
3. Immettere `SubviewTable` in **Nome** e fare clic sul pulsante **Nuovo**.
4. Fare doppio clic sul file **SubviewTable. xib** per aprirlo per la modifica in Interface Builder e progettare l'interfaccia utente:

    [![Progettazione della nuova visualizzazione in Xcode](xib-images/view02.png "Progettazione della nuova visualizzazione in Xcode")](xib-images/view02-large.png#lightbox)
5. Collegare tutte le azioni e gli outlet richiesti.
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Modificare quindi il **SubviewTable.cs** e aggiungere il codice seguente al file **AwakeFromNib** per popolare la nuova visualizzazione quando viene caricata:

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

Aggiungere un'enumerazione al progetto per tenere traccia della visualizzazione attualmente in fase di visualizzazione. Ad esempio, **SubviewType.cs**:

```csharp
public enum SubviewType
{
    None,
    TableView,
    OutlineView,
    ImageView
}
```

Modificare il file con estensione XIB della finestra che utilizzerà la visualizzazione e la visualizzerà. Aggiungere una **visualizzazione personalizzata** che fungerà da contenitore per la vista dopo che è stata caricata in memoria C# dal codice ed esporla a un Outlet chiamato `ViewContainer`:

[![Creazione dell'Outlet necessario](xib-images/view03.png "Creazione dell'Outlet necessario")](xib-images/view03-large.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Modificare quindi il file con estensione CS della finestra in cui verrà visualizzata la nuova visualizzazione (ad esempio, **MainWindow.cs**) e aggiungere il codice seguente:

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

Quando è necessario visualizzare una nuova vista caricata da un file con estensione XIB nel contenitore della finestra (la **visualizzazione personalizzata** aggiunta in precedenza), questo codice gestisce la rimozione di qualsiasi visualizzazione esistente e lo swapping per il nuovo. Sembra che sia già visualizzata una visualizzazione, in caso affermativo, la rimuove dalla schermata. Successivamente, accetta la visualizzazione passata (caricata da un controller di visualizzazione), la ridimensiona per adattarsi all'area del contenuto e la aggiunge al contenuto per la visualizzazione.

Per visualizzare una nuova visualizzazione, usare il codice seguente:

```csharp
DisplaySubview(new SubviewTableController(), SubviewType.TableView);
```

In questo modo viene creata una nuova istanza del controller di visualizzazione per la nuova vista da visualizzare, viene impostato il tipo (come specificato dall'enum aggiunto al progetto) e viene utilizzato il metodo `DisplaySubview` aggiunto alla classe della finestra per visualizzare effettivamente la visualizzazione. Esempio:

[![Esecuzione dell'app di esempio](xib-images/view04.png "Esecuzione dell'app di esempio")](xib-images/view04-large.png#lightbox)

Per altre informazioni sull'uso di Windows in un'applicazione Xamarin.Mac, vedere la documentazione di [Windows](~/mac/user-interface/window.md) e dei [dialoghi](~/mac/user-interface/dialog.md) .

## <a name="summary"></a>Riepilogo

Questo articolo ha esaminato in dettaglio l'uso dei file con estensione XIB in un'applicazione Xamarin.Mac. Sono stati osservati i diversi tipi e usi dei file con estensione XIB per creare l'interfaccia utente dell'applicazione, come creare e gestire i file XIB nel Interface Builder di Xcode e come usare i file con estensione C# XIB nel codice.

## <a name="related-links"></a>Collegamenti correlati

- [MacImages (sample)](https://docs.microsoft.com/samples/xamarin/mac-samples/macimages) (MacImages - Esempio)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [Menu](~/mac/user-interface/menu.md)
- [Dialogs](~/mac/user-interface/dialog.md) (Finestre di dialogo)
- [Uso delle immagini](~/mac/app-fundamentals/image.md)
- [Linee guida dell'interfaccia umana macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
