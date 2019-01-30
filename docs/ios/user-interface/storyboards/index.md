---
title: Introduzione agli storyboard in xamarin. IOS
description: Questo documento fornisce un'introduzione agli storyboard in xamarin. IOS. Viene descritto come utilizzare uno storyboard per definire l'interfaccia utente e così gli elementi segue e come utilizzare la finestra di progettazione di iOS per modificare i file storyboard.
ms.prod: xamarin
ms.assetid: A3339BD2-9F56-7965-25F5-4B7C991EB775
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 6294d696fe309e1952124caaaace89d40ad24bc6
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233562"
---
# <a name="introduction-to-storyboards-in-xamarinios"></a>Introduzione agli storyboard in xamarin. IOS

In questa Guida verrà spiegato quali uno Storyboard è ed esaminare alcuni dei componenti principali, come segue. Verrà esaminato come gli storyboard possono essere creati e usati, e sui vantaggi che dispongono di uno sviluppatore.

Prima che il formato di file Storyboard è stato introdotto da Apple come una rappresentazione visiva dell'interfaccia utente di un'applicazione iOS, gli sviluppatori creati i file XIB per ogni controller di visualizzazione e programmate manualmente la navigazione tra ogni visualizzazione.  Utilizzare uno Storyboard consente allo sviluppatore di definire i controller di visualizzazione e la navigazione tra di essi in un'area di progettazione e offre funzionalità di modifica WYSIWYG dell'interfaccia utente dell'applicazione.

Uno Storyboard può essere creato, aperti e modificato con Xamarin iOS Designer. Questa Guida che è possibile ottenere questa procedura dettagliata come usare la finestra di progettazione per creare storyboard quando si usa c# per la navigazione del programma.


## <a name="requirements"></a>Requisiti

Gli storyboard sono utilizzabile con iOS Designer in Visual Studio per Mac o Visual Studio 2015 e 2017 con i carichi di lavoro Xamarin installati.

## <a name="what-is-a-storyboard"></a>Che cos'è uno Storyboard?

Uno Storyboard è la rappresentazione visiva di tutte le schermate in un'applicazione. Contiene una sequenza delle scene, con ogni scena che rappresenta un *Controller di visualizzazione* e il relativo *viste*. Queste viste possono contenere gli oggetti e [controlli](~/ios/user-interface/controls/index.md) che consentirà all'utente di interagire con l'applicazione. Questa raccolta di controlli e visualizzazioni (o *le visualizzazioni secondarie*) è noto come una *gerarchia di visualizzazione contenuto*. Le scene connessi da elemento segue gli oggetti che rappresentano una transizione tra i controller di visualizzazione. In genere questo avviene tramite la creazione di un elemento segue tra un oggetto nella visualizzazione iniziale e la vista che si connette. Le relazioni nell'area di progettazione sono illustrate nell'immagine seguente:

 [![](images/storyboardsview.png "Le relazioni nell'area di progettazione sono illustrate in questa immagine")](images/storyboardsview.png#lightbox)

Come illustrato, lo storyboard layout ognuna delle scene con contenuto già eseguito il rendering e illustrate le connessioni tra di essi.  Vale la pena notare a questo punto, quando si parla in background in un iPhone, è opportuno presupporre che uno *scena* sullo storyboard è uguale a uno *schermata* del contenuto nel dispositivo. Tuttavia, con un iPad che è possibile disporre le scene più visualizzati in una sola volta, ad esempio, usando un controller di visualizzazione nel Popover.

Esistono molti vantaggi rispetto all'utilizzo degli storyboard per creare l'interfaccia utente dell'applicazione, soprattutto quando si usa Xamarin. In primo luogo, è una rappresentazione visiva dell'interfaccia utente, come tutti gli oggetti, tra cui [controlli personalizzati](~/ios/user-interface/designer/ios-designable-controls-overview.md) – viene eseguito il rendering in fase di progettazione. Ciò significa che prima di compilare o distribuire l'applicazione è possibile visualizzare l'aspetto e il flusso. Si consideri ad esempio l'immagine precedente. È possibile sapere da esaminare rapidamente la progettazione surface presenti scene di quanti sono, il layout di ogni visualizzazione e come tutti gli elementi sono correlati. Questo è ciò che rende gli storyboard così potente.

Gli eventi sono più gestibili con gli storyboard, specialmente quando si usa iOS Designer. La maggior parte dei controlli dell'interfaccia utente avrà un elenco di possibili eventi nel riquadro delle proprietà. Il gestore dell'evento può essere aggiunte qui e completato in un metodo parziale della classe di controller di visualizzazione..

Il contenuto di uno storyboard è archiviato come file XML. In fase di compilazione, qualsiasi `.storyboard` file vengono compilati in file binari, noti come nibs. In fase di esecuzione questi nibs inizializzati e creare un'istanza per creare nuove visualizzazioni.

## <a name="segues"></a>Gli elementi segue

Oggetto *elemento Segue*, o *Segue oggetto*, viene usato nello sviluppo di iOS per rappresentare una transizione tra in background. Per creare un elemento segue, tenere premuto il **Ctrl** chiave e fare clic e trascinare tra una scena a altra. Quando si trascina il mouse, viene visualizzato un collegamento blu, che indica dove l'elemento segue si comporti come illustrato nell'immagine seguente:

 [![](images/createsegue.png "Viene visualizzato un collegamento blu, che indica dove l'elemento segue si comporti come illustrato in questa immagine")](images/createsegue.png#lightbox)

Nel rilascio del mouse, verrà visualizzato un menu permette di scegliere l'azione per l'elemento segue. Potrebbe essere simile alle immagini seguenti: 

**Le classi di pre-iOS 8 e alle dimensioni**:

[![](images/segue1.png "Elenco a discesa azione Segue senza classi di dimensioni")](images/segue1.png#lightbox)

**Quando si usano le classi di dimensioni e gli elementi segue adattivo**:

[![](images/16new.png "Elenco a discesa azione elemento Segue con classi di dimensioni")](images/16new.png#lightbox)

> [!IMPORTANT]
> Se si usa VMWare per la macchina virtuale Windows, Ctrl + clic viene mappata come le _destro_ pulsante del mouse per impostazione predefinita. Per creare un elemento Segue, modificare le preferenze della tastiera tramite **preferenze** > **tastiera e Mouse** > **scelte rapide del Mouse** e mappare di nuovo il **Pulsante secondario** come illustrato di seguito:
> 
> [![](images/image22.png "Impostazioni di preferenza tastiera e Mouse")](images/image22.png#lightbox)
> 
> È ora possibile aggiungere un elemento segue tra i controller di visualizzazione come di consueto.

Esistono diversi tipi di transizioni, ogni controllo che fornisce come nuovo controller di visualizzazione vengono presentati all'utente e come interagisce con altri controller di visualizzazione nello Storyboard. Questi vengono spiegati di seguito. È anche possibile per creare una sottoclasse un oggetto di elemento segue per implementare una transizione personalizzata:

-  **Mostra / Push** – un push segue aggiunge il controller di visualizzazione per lo stack di navigazione. Si presuppone che il controller di visualizzazione che hanno origine i push fa parte dello stesso controller di spostamento come controller di visualizzazione che viene aggiunto allo stack. Si esegue la stessa operazione come `pushViewController` e viene in genere usate quando non c'è una relazione tra i dati nelle schermate. Usando il push segue ti offre il lusso di una barra di navigazione con un pulsante Indietro e titolo aggiunto a ogni vista nello stack, che consente il drill-down navigazione attraverso la gerarchia di visualizzazione.
-  **Modale** : un elemento segue modale creare una relazione tra tutti i controller di due visualizzazione nel progetto, con la possibilità di una transizione animata da visualizzare. Il controller di visualizzazione figlio nasconderà completamente il controller di visualizzazione padre quando inserito nella visualizzazione. A differenza di un'operazione push segue, che aggiunge un pulsante Indietro per noi; Quando tramite una finestra modale segue `DismissViewController` devono essere usate per restituire al controller di visualizzazione precedente.
-  **Personalizzato** – personalizzati qualsiasi elemento segue può essere creato come una sottoclasse di ` UIStoryboardSegue`.
-  **Rimozione** : rimozione segue può essere utilizzato per spostarsi indietro tra un push o modale segue – ad esempio, ignorando il controller di visualizzazione presentati come form modale. Inoltre, è possibile rimuovere tramite non solo uno, ma una serie di push e modale gli elementi segue e tornare più passaggi nella gerarchia di navigazione con una singola azione di rimozione. Per informazioni su come usare un'operazione di rimozione segue in iOS, vedere la [creazione di rimozione elementi segue](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/unwind_segue) recipe.
-  **Sourceless** : un elemento sourceless segue indica la scena che contiene il controller di visualizzazione iniziale e pertanto visualizzazione in cui l'utente vedranno per primo. È rappresentato dall'elemento segue illustrato di seguito:  

    [![](images/sourcelesssegue.png "Un elemento sourceless segue")](images/sourcelesssegue.png#lightbox)

### <a name="adaptive-segue-types"></a>Tipi di elemento Segue adattivo

 iOS 8 introdotte [le classi di dimensioni](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes) per consentire un file storyboard iOS funzionare con tutte le dimensioni dello schermo disponibile, consentendo agli sviluppatori di creare un'interfaccia utente per tutti i dispositivi iOS. Per impostazione predefinita, tutte le nuove applicazioni xamarin. IOS useranno le classi di dimensioni. Per usare le classi di dimensioni da un progetto precedente, vedere la [Introduzione agli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md) Guida. 
 
Qualsiasi applicazione che usa le classi di dimensioni userà anche la nuova [ *gli elementi segue adattivo*](~/ios/user-interface/storyboards/unified-storyboards.md). Quando si utilizzano classi di dimensioni, tenere presente che è non stiamo specificare direttamente se si sta usando un iPhone o iPad. In altre parole stiamo creando un'interfaccia utente che avrà un aspetto sempre lo stesso, indipendentemente dalla quantità immobiliare deve funzionare con. Lavoro segue adattivo a giudicare l'ambiente e determinare il modo migliore presentare il contenuto. Gli elementi segue adattivo sono illustrati di seguito: 

[![](images/adaptivesegue.png "L'elenco a discesa elementi segue adattivo")](images/adaptivesegue.png#lightbox)

|Elemento segue|Descrizione|
|--- |--- |
|Mostra|Ciò è molto simile a un'operazione Push segue, ma tiene in considerazione il contenuto della schermata.|
|Mostra dettagli|Se l'app Visualizza una visualizzazione master e di dettaglio (ad esempio, in un controller doppia visualizzazione su un iPad), il contenuto viene sostituito nella visualizzazione dettagli. Se l'app viene visualizzato solo lo schema o i dettagli, il contenuto viene sostituito l'inizio dello stack di controller di visualizzazione.|
|Presentazione|Ciò è simile all'elemento segue modale e consente la selezione di stili di presentazione e di transizione.|
|Nel popover presentazione|Questo presenta il contenuto come nel popover|

### <a name="transferring-data-with-segues"></a>Il trasferimento dei dati con gli elementi segue

I vantaggi di un elemento segue non terminano con le transizioni. Possono inoltre essere utilizzati per gestire il trasferimento dei dati tra i controller di visualizzazione. Questo risultato viene ottenuto eseguendo l'override di `PrepareForSegue` metodo sul controller di visualizzazione iniziale e la gestione di dati noi stessi. Quando viene attivato l'elemento segue, ad esempio, con la pressione di un pulsante: l'applicazione chiamerà questo metodo, che fornisce un'opportunità per preparare il nuovo controller di visualizzazione *prima di* qualsiasi di una navigazione. Il codice seguente, dal [Phoneword](https://developer.xamarin.com/samples/monotouch/Hello_iOS/) di esempio, questa procedura: 


```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, 
NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    var callHistoryController = segue.DestinationViewController 
                                  as CallHistoryController;

    if (callHistoryController != null) {
        callHistoryController.PhoneNumbers = PhoneNumbers;
    }
}
```

In questo esempio, il `PrepareForSegue` metodo verrà chiamato quando l'elemento segue viene attivato dall'utente. È innanzitutto necessario creare un'istanza del controller di visualizzazione 'ricezione' e impostarla come destinazione dell'elemento segue il Controller di visualizzazione. Questa operazione viene eseguita dalla riga di codice riportato di seguito:

```csharp
var callHistoryController = segue.DestinationViewController as CallHistoryController;
```

Il metodo ora ha la possibilità di impostare le proprietà di `DestinationViewController`. In questo esempio sono state prese vantaggio di questo approccio passando un elenco denominato `PhoneNumbers` per il `CallHistoryController` e assegnarlo a un oggetto con lo stesso nome:

```csharp
if (callHistoryController != null) {
        callHistoryController.PhoneNumbers = PhoneNumbers;
    }
```

Al termine della transizione, l'utente visualizzerà il `CallHistoryController` con l'elenco popolato.

## <a name="adding-a-storyboard-to-a-non-storyboard-project"></a>Aggiunta di uno Storyboard a un progetto Non Storyboard

In alcuni casi si potrebbe essere necessario aggiungere uno Storyboard a un file in precedenza non storyboard. Una volta questa operazione in Visual Studio per Mac può essere semplificata con la procedura seguente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Creare un nuovo file di Storyboard selezionando **File > Nuovo File > iOS > Storyboard**, come illustrato di seguito: 
    
    [![](images/new-storyboard-xs.png "Finestra di dialogo Nuovo file")](images/new-storyboard-xs.png#lightbox)

2. Aggiungere il nome di uno Storyboard per il **interfaccia principale** sezione del **Info. plist**, come illustrato di seguito:
    
    [![](images/infoplist.png "L'editor Info. plist")](images/infoplist.png#lightbox)
    
    Questa operazione l'equivalente di un'istanza di Controller di visualizzazione iniziale nel `FinishedLaunching` metodo all'interno del delegato dell'App. Con questa opzione è impostata, l'applicazione crea un'istanza di una finestra (vedere sotto), carica lo storyboard principale e assegna un'istanza di Controller di visualizzazione iniziale dello storyboard (quello accanto all'elemento sourceless Segue) come il `RootViewController` proprietà della finestra e quindi rende la finestra visibile sullo schermo.

3. Nel `AppDelegate`, sostituire il valore predefinito `Window` (metodo), con il codice seguente per implementare la proprietà della finestra:
        
        public override UIWindow Window {
            get;
            set;
            }
            
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Creare un nuovo file Storyboard facendo clic sul progetto di **Aggiungi > Nuovo File > iOS > Storyboard vuoto**, come illustrato di seguito: 
    
    [![](images/new-storyboard-vs.png "Finestra di dialogo Nuovo elemento")](images/new-storyboard-vs.png#lightbox)

2. Aggiungere il nome di uno Storyboard per il **interfaccia principale** sezione di iOS dell'applicazione, come illustrato di seguito:
    
    [![](images/ios-app.png "L'editor Info. plist")](images/ios-app.png#lightbox)
    
    Questa operazione l'equivalente di un'istanza di Controller di visualizzazione iniziale nel `FinishedLaunching` metodo all'interno del delegato dell'App. Con questa opzione è impostata, l'applicazione crea un'istanza di una finestra (vedere sotto), carica lo storyboard principale e assegna un'istanza di Controller di visualizzazione iniziale dello storyboard (quello accanto all'elemento sourceless Segue) come il `RootViewController` proprietà della finestra e quindi rende la finestra visibile sullo schermo.

3. Nel `AppDelegate`, sostituire il valore predefinito `Window` (metodo), con il codice seguente per implementare la proprietà della finestra:

        public override UIWindow Window {
            get;
            set;
            }
            
-----

## <a name="creating-a-storyboard-with-the-ios-designer"></a>Creazione di uno Storyboard con iOS Designer

È possibile creare uno Storyboard usando la finestra di progettazione di Xamarin per iOS, che è stato integrato senza problemi con Visual Studio per Mac e Visual Studio.

Per iniziare a usare iOS Designer per creare storyboard, seguire le [Hello, iOS Multiscreen](~/ios/get-started/hello-ios-multiscreen/index.md) Guida. In questa procedura dettagliata si esploreranno navigazione tra i controller di visualizzazione usando segue e come gestire gli eventi sui controlli.

## <a name="instantiate-storyboards-manually"></a>Creare manualmente un'istanza di storyboard

Gli storyboard sostituire completamente singoli file XIB nel progetto, tuttavia, i controller di visualizzazione in uno Storyboard possono ancora essere implementati con `Storyboard.InstantiateViewController`.

In alcuni casi le applicazioni hanno requisiti speciali che non possono essere gestiti con le transizioni di storyboard predefiniti fornite dalla finestra di progettazione. Ad esempio, se fossimo per creare un'applicazione che avvia schermate diverse dal pulsante stesso, a seconda dello stato corrente di un'applicazione, si potrebbe essere necessario creare manualmente un'istanza di controller di visualizzazione e la transizione del programma noi stessi.

La schermata seguente mostra due controller di visualizzazione nell'area progettazione senza alcun elemento segue tra di essi. Nella sezione successiva illustra come è possibile configurare un tale transizione nel codice.

 [![](images/viewcontrollerspink.png "Questo screenshot Mostra due controller di visualizzazione nell'area di progettazione senza alcun elemento segue tra di essi")](images/viewcontrollerspink.png#lightbox)

1. Aggiungere un _Storyboard iPhone vuoto_ a un progetto a progetto esistente:
    
    [![](images/add-storyboard1.png "Aggiunta di uno storyboard")](images/add-storyboard1.png#lightbox)

2. Fare doppio clic sullo storyboard appena creato per aprirlo e aggiungere una nuova **Controller di spostamento** all'area di progettazione. Il Controller di spostamento è senza interfaccia utente, per impostazione predefinita verrà fornito con un Controller visualizzazione radice, come illustrato di seguito:

    [![](images/uinavigationcontroller.png "I controller di visualizzazione con gli elementi segue")](images/uinavigationcontroller.png#lightbox)

3. Selezionare il _Controller visualizzazione_ facendo clic sulla barra nera nella parte inferiore. Nella finestra di progettazione **proprietà Pad**, in **identità** possiamo specificare una classe personalizzata, nonché un ID univoco per il Controller di visualizzazione. Impostare il **nome della classe** e **Storyboard ID** a `MainViewController`.

    [![](images/identitypanelnew.png "Specificare una classe personalizzata")](images/identitypanelnew.png#lightbox)

4. In un secondo momento, sarà necessario creare un'istanza ai controller di visualizzazione dallo storyboard e userà l'ID di Storyboard per farvi riferimento nel codice. Impostazione dell'ID di ripristino in modo che corrisponda all'ID di Storyboard garantisce che il controller di visualizzazione ottiene ricreato correttamente se lo stato deve essere ripristinato.

5. Attualmente è sufficiente un controller di visualizzazione. Trascinare un altro controller di visualizzazione nell'area di progettazione. Nel **proprietà Pad**, in identità, impostare la classe e l'ID di Storyboard da `PinkViewController`, come illustrato di seguito:

    [![](images/pinkvcnew.png "Il riquadro proprietà")](images/pinkvcnew.png#lightbox)
    
    L'IDE creerà queste classi personalizzate per i controller di visualizzazione. Questi possono essere visualizzati nei **riquadro della soluzione**, come illustrato nello screenshot seguente:
    
    [![](images/solution-pad.png "Riquadro della soluzione")](images/solution-pad.png#lightbox)

6. Nel `PinkViewController`, selezionare la vista facendo verso il centro del frame del controller. Nel riquadro delle proprietà, nella vista modificare il **sfondo** a Magenta:
    
    [![](images/pinkcontroller.png "Impostare il colore di sfondo")](images/pinkcontroller.png#lightbox)

7. Infine, trascinare un pulsante dal **casella degli strumenti** nel `MainViewController`. Nel riquadro delle proprietà, assegnarvi il nome `PinkButton` e GoToPink il titolo, come illustrato di seguito:

    [![](images/pinkbutton.png "Nome del set di pulsante")](images/pinkbutton.png#lightbox)

Lo storyboard è stato completato, ma se si distribuisce il progetto a questo punto, si otterrà una schermata vuota. Infatti, tuttavia è necessario indicare all'IDE per usare i nostri storyboard e configurare un controller visualizzazione radice da usare come la prima visualizzazione. In genere questa operazione può essere eseguita tramite le opzioni di progetto, come illustrato in precedenza. Tuttavia in questo esempio si sarà ottenuto lo stesso risultato nel codice, aggiungendo il codice seguente per il **AppDelegate**:

```csharp
public partial class AppDelegate : UIApplicationDelegate
    {
        UIWindow window;
        public static UIStoryboard Storyboard = UIStoryboard.FromName ("MainStoryboard", null);
        public static UIViewController initialViewController;

        public override bool FinishedLaunching (UIApplication app, NSDictionary options)
        {
            window = new UIWindow (UIScreen.MainScreen.Bounds);

            initialViewController = Storyboard.InstantiateInitialViewController () as UIViewController;

            window.RootViewController = initialViewController;
            window.MakeKeyAndVisible ();
            return true;
        }

    }
```

Che è una grande quantità di codice, ma solo poche righe si ha dimestichezza. In primo luogo, registriamo nostro storyboard con il **AppDelegate** passando il nome dello storyboard, **MainStoryboard**. Successivamente, è possibile indicare all'applicazione di un'istanza di un controller di visualizzazione iniziale dallo storyboard chiamando `InstantiateInitialViewController` sul nostro storyboard, e impostiamo tale controller di visualizzazione come controller visualizzazione radice dell'applicazione. Questo metodo determina la prima schermata visualizzata all'utente, e crea una nuova istanza di tale Controller di visualizzazione.

Si noti che nel riquadro della soluzione che ha creato l'IDE di una `MainViewcontroller.cs` (classe) e il relativo `corresponding designer.cs` quando viene aggiunto il nome della classe per il riquadro delle proprietà nel passaggio 4. È possibile osservare questa classe creata un costruttore speciale che include una classe di base:

```csharp
public MainViewController (IntPtr handle) : base (handle) 
{
}
```


Quando si crea uno Storyboard usando la finestra di progettazione, l'IDE aggiungerà automaticamente il [[registrare]](xref:Foundation.RegisterAttribute) attributo all'inizio del `designer.cs` classe e passare un identificatore di stringa, che è identico all'ID di Storyboard specificato di passaggio precedente. Il codice c# verranno collegati alla scena rilevante nello Storyboard.

A un certo punto si potrebbe voler aggiungere una classe esistente che è stata **non** creata nella finestra di progettazione. In questo caso, si potrebbe registrare questa classe come di consueto:

```csharp
[Register ("MainViewController")]
public partial class MainViewController : UIViewController
{
public MainViewController (IntPtr handle) : base (handle) 
{
}

...
}
```

Per altre informazioni sulla registrazione di classi e metodi, vedere la [Registrar tipo](http://docs.xamarin.com/guides/ios/advanced_topics/registrar/) documentazione.

L'ultimo passaggio in questa classe consiste nel collegare il pulsante e la transizione al controller di visualizzazione rosa. È possibile creare un'istanza di `PinkViewController` dallo Storyboard; quindi, si verrà programmare un push elemento segue con `PushViewController`, come illustrato nell'esempio di codice riportato di seguito:

```csharp
public partial class MainViewController : UIViewController
{
    UIViewController pinkViewController;

    public MainViewController (IntPtr handle) : base (handle)
    {

    }

    public override void AwakeFromNib ()
    {
    // Called when loaded from xib or storyboard.

    this.Initialize ();
    }

    public void Initialize(){

    //Instantiating View Controller with Storyboard ID 'PinkViewController'
    pinkViewController = Storyboard.InstantiateViewController ("PinkViewController") as PinkViewController;
    }

    public override void ViewDidLoad ()
    {
    base.ViewDidLoad ();

    //When we push the button, we will push the pinkViewController onto our current Navigation Stack
    PinkButton.TouchUpInside += (o, e) =&gt; {
        this.NavigationController.PushViewController (pinkViewController, true);
    };
    }

}
```

Esecuzione dell'applicazione produce un'applicazione 2-Schermata:

![](images/finishedstoryboard.png "App di esempio esegue le schermate")

## <a name="conditional-segues"></a>Gli elementi segue condizionale

Spesso, lo spostamento da un controller di visualizzazione al successivo è dipendente da una determinata condizione. Ad esempio, se una schermata di accesso semplice si sarebbe vuole solo visualizzare la schermata successiva *se* siano stati verificati il nome utente e password.

Nell'esempio successivo si aggiungerà un campo della password per l'esempio precedente. L'utente solo sarà in grado di accedere il *PinkViewController* se si immette la password corretta, in caso contrario, un errore verrà visualizzato.

Prima di iniziare, seguire i passaggi da 1-8. In questi passaggi è creare nostro storyboard, iniziare a creare l'interfaccia utente e indicare il delegato App quali Controller di visualizzazione da utilizzare come succede RootViewController.

1. A questo punto, è possibile creare l'interfaccia utente e aggiungere le visualizzazioni aggiuntive elencate per il `MainViewController` in modo che appaia come quello nello screenshot seguente:

    - UITextField
        - Nome: PasswordTextField
        - Segnaposto: 'Immettere la Password segreta'
    - UILabel
        - Testo: ' Errore: Password errata. Non deve passare!'
        - Colore: Rosso
        - Allineamento: Center
        - Righe: 2
        - Casella di controllo 'Hidden' selezionata 
        
    [![](images/passwordvc.png "Righe di Center")](images/passwordvc.png#lightbox)
    
2. Creare un elemento Segue tra sul pulsante Vai a rosa e il controller di visualizzazione, trascinare Ctrl dal *PinkButton* per il *PinkViewController*e selezionando **Push** nel rilascio del mouse . 

3. Fare clic sull'elemento Segue e assegnargli il *Identifier* `SegueToPink`:

    [![](images/namesegue.png "Fare clic sull'elemento Segue e assegnargli il SegueToPink identificatore")](images/namesegue.png#lightbox)  
    

4. Infine, aggiungere il seguente metodo ShouldPerformSegue al `MainViewController` classe:

    ```csharp
    public override bool ShouldPerformSegue (string segueIdentifier, NSObject sender)
    {
        
        if(segueIdentifier == "SegueToPink"){
            if (PasswordTextField.Text == "password") {
                PasswordTextField.ResignFirstResponder ();
                return true;
            }
            else{
                ErrorLabel.Hidden = false;
                return false;
            }
        }
        return base.ShouldPerformSegue (segueIdentifier, sender);
    }
    ```

In questo codice è stato confrontato il segueIdentifier a nostro `SegueToPink` segue, in modo che sia quindi possibile testare una condizione; una password valida in questo caso. Se la condizione restituisce `true`, l'elemento Segue eseguirà e presenterà il `PinkViewController`. Se `false`, il nuovo Controller di visualizzazione non verranno visualizzato.

È possibile applicare questo approccio per qualsiasi elemento Segue nel controller di visualizzazione controllando l'argomento segueIdentifier al metodo ShouldPerformSegue. In questo caso è necessario solo un identificatore di elemento Segue – `SegueToPink`.

Fare riferimento alla soluzione Storyboards.Conditional nel [esempio di storyboard manuale](https://developer.xamarin.com/samples/monotouch/ManualStoryboard/) per un esempio funzionante.

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>Utilizzo di riferimenti a uno Storyboard

Un riferimento di Storyboard consente di prendere una progettazione dello Storyboard grande e complessa e di suddividerlo in più piccoli storyboard che ottenere cui viene fatto riferimento dall'originale, pertanto la rimozione di complessità e rendendo più semplice progettare e gestire gli storyboard singoli risultanti.

Inoltre, fornisce un riferimento di Storyboard un' _ancoraggio_ alla scena un altro all'interno dello stesso Storyboard o una scena specifica in una diversa.

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>Riferimento a uno Storyboard di esterno

Per aggiungere un riferimento a uno Storyboard esterno, eseguire le operazioni seguenti:

1. Nel **Esplora soluzioni**, fare doppio clic sul nome del progetto e selezionare **Add** > **nuovo File...**   >  **iOS** > **Storyboard**. Immettere un **Name** per il nuovo Storyboard e fare clic sui **New** pulsante:
    
    [![](images/ref01.png "Finestra di dialogo Nuovo File")](images/ref01.png#lightbox)
    
2. Progettare il layout delle scene dello Storyboard di nuovo normalmente verrebbe e salvare le modifiche: 
    
    [![](images/ref02.png "Il layout della nuova scena")](images/ref02.png#lightbox)
    
3. Aprire lo Storyboard che si sta per essere aggiunto il riferimento in iOS Designer.

4. Trascinare un **riferimento di Storyboard** dalle **della casella degli strumenti** nell'area di progettazione: 
    
    [![](images/ref03.png "Un riferimento di Storyboard")](images/ref03.png#lightbox)
    
5. Nel **Widget** scheda della finestra di **riquadro delle proprietà**, selezionare il nome del **Storyboard** creata in precedenza: 

    [![](images/ref04.png "La scheda di Widget")](images/ref04.png#lightbox)
    
6. CTRL + clic su un Widget dell'interfaccia utente (ad esempio un pulsante) in una scena esistente e creare un nuovo elemento Segue per il **Storyboard riferimento** appena creato: 

    [![](images/ref05.png "Creazione di un elemento segue")](images/ref05.png#lightbox) 
    
7. Scegliere dal menu a comparsa **mostrare** per completare l'elemento Segue: 

    [![](images/ref06.png "Selezionare Mostra per completare l'elemento Segue")](images/ref06.png#lightbox) 
    
8. Salvare le modifiche allo Storyboard.

Verrà visualizzato quando l'app viene eseguita e l'utente fa clic sull'elemento dell'interfaccia utente che è stato creato l'elemento Segue da, il Controller di visualizzazione iniziale dallo Storyboard esterno specificati nel riferimento Storyboard.

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>Riferimento a una scena specifica in uno Storyboard di esterno

Per aggiungere un riferimento a una scena specifico uno Storyboard esterno e non il Controller di visualizzazione iniziale, eseguire le operazioni seguenti:

1. Nel **Esplora soluzioni**, fare doppio clic sullo Storyboard esterno per aprirlo e modificarlo.

2. Aggiungere una nuova scena e progettare il layout come di consueto: 

    [![](images/ref07.png "Il nuovo layout scena")](images/ref07.png#lightbox)
    
3. Nel **Widget** scheda della finestra di **riquadro delle proprietà**, immettere un **ID Storyboard** per Controller di visualizzazione della nuova scena: 

    [![](images/ref08.png "Immettere un ID di Storyboard per il nuovo Controller di visualizzazione in background")](images/ref08.png#lightbox)
    
3. Aprire lo Storyboard che si sta per essere aggiunto il riferimento in iOS Designer.

4. Trascinare un **riferimento di Storyboard** dalle **della casella degli strumenti** nell'area di progettazione: 

    [![](images/ref03.png "Un riferimento di Storyboard")](images/ref03.png#lightbox)
    
5. Nel **Widget** scheda della finestra di **Esplora proprietà**, selezionare il nome del **Storyboard** e il **ID riferimento** (ID di Storyboard) del Scena 3D che creato in precedenza: 

    [![](images/ref09.png "La scheda di Widget ")](images/ref09.png#lightbox)
    
6. CTRL + clic su un Widget dell'interfaccia utente (ad esempio un pulsante) in una scena esistente e creare un nuovo elemento Segue per il **Storyboard riferimento** appena creato: 

    [![](images/ref10.png "Creazione di un elemento segue")](images/ref10.png#lightbox) 
    
7. Scegliere dal menu a comparsa **mostrare** per completare l'elemento Segue: 

    [![](images/ref06.png "Selezionare Mostra per completare l'elemento Segue")](images/ref06.png#lightbox) 
    
8. Salvare le modifiche allo Storyboard.

Quando l'app viene eseguita e l'utente fa clic sull'elemento dell'interfaccia utente che è stato creato l'elemento Segue da, scena con la determinata **ID di Storyboard** dallo Storyboard esterno specificati nel riferimento Storyboard verranno visualizzati.

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>Riferimento a una scena specifica nello stesso Storyboard

Per aggiungere un riferimento a una scena specifica lo stesso Storyboard, eseguire le operazioni seguenti:

1. Nel **Esplora soluzioni**, fare doppio clic sullo Storyboard per aprirlo e modificarlo.

2. Aggiungere una nuova scena e progettare il layout come di consueto: 

    [![](images/ref11.png "Il nuovo layout scena")](images/ref11.png#lightbox)

3. Nel **Widget** scheda della finestra di **riquadro delle proprietà**, immettere un **ID Storyboard** per Controller di visualizzazione della nuova scena: 

    [![](images/ref12.png "La scheda di Widget")](images/ref12.png#lightbox)
    
3. Trascinare un **riferimento di Storyboard** dalle **della casella degli strumenti** nell'area di progettazione: 

    [![](images/ref03.png "Un riferimento di Storyboard")](images/ref03.png#lightbox)
    
5. Nel **Widget** scheda della finestra di **riquadro delle proprietà**, selezionare **ID riferimento** (ID di Storyboard) della scena creata in precedenza: 

    [![](images/ref13.png "La scheda di Widget")](images/ref13.png#lightbox)
    
6. CTRL + clic su un Widget dell'interfaccia utente (ad esempio un pulsante) in una scena esistente e creare un nuovo elemento Segue per il **Storyboard riferimento** appena creato: 

    [![](images/ref14.png "Creazione di un elemento segue")](images/ref14.png#lightbox) 
    
7. Scegliere dal menu a comparsa **mostrare** per completare l'elemento Segue: 

    [![](images/ref06.png "Selezionare Mostra per completare l'elemento Segue")](images/ref06.png#lightbox) 
    
8. Salvare le modifiche allo Storyboard.

Quando l'app viene eseguita e l'utente fa clic sull'elemento dell'interfaccia utente che è stato creato l'elemento Segue da, scena con la determinata **ID di Storyboard** nello Storyboard stesso specificati nel riferimento Storyboard verranno visualizzati.

## <a name="summary"></a>Riepilogo

Questo articolo introduce il concetto di storyboard e come può essere utile nello sviluppo di applicazioni iOS. Illustra le scene, i controller di visualizzazione, viste e le gerarchie di visualizzazione e modalità di collegamento tra le scene insieme a diversi tipi di segue.  Vengono inoltre descritti i controller di visualizzazione istanze manualmente da un storyboard e creazione segue condizionale.



## <a name="related-links"></a>Collegamenti correlati

- [Manuale dello Storyboard (esempio)](https://developer.xamarin.com/samples/ManualStoryboard/)
- [Introduzione a iOS Designer](~/ios/user-interface/designer/introduction.md)
- [Conversione agli storyboard](http://developer.apple.com/library/ios/#releasenotes/Miscellaneous/RN-AdoptingStoryboards/)
- [Riferimento alla classe UIStoryboard](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIStoryboard_Class/Reference/Reference.html)
