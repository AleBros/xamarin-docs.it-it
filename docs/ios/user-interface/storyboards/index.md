---
title: Introduzione agli storyboard in Novell. iOS
description: Questo documento fornisce un'introduzione agli storyboard in Novell. iOS. Viene descritto come viene utilizzato uno storyboard per definire un'interfaccia utente, gli elementi segue, e come utilizzare iOS designer per modificare i file dello storyboard.
ms.prod: xamarin
ms.assetid: A3339BD2-9F56-7965-25F5-4B7C991EB775
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: cf181cf6c27476b7073073467ef186c352645e39
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768884"
---
# <a name="introduction-to-storyboards-in-xamarinios"></a>Introduzione agli storyboard in Novell. iOS

In questa guida verrà illustrato lo storyboard ed esaminando alcuni componenti chiave, ad esempio gli elementi segue. Si esaminerà il modo in cui gli storyboard possono essere creati e usati e i vantaggi che hanno per uno sviluppatore.

Prima che il formato del file di storyboard venisse introdotto da Apple come rappresentazione visiva dell'interfaccia utente di un'applicazione iOS, gli sviluppatori creavano i file XIB per ogni controller di visualizzazione e programmavano manualmente la navigazione tra ciascuna visualizzazione.  L'uso di uno storyboard consente allo sviluppatore di definire sia i controller di visualizzazione che la navigazione tra di essi in un'area di progettazione e offre la modifica WYSIWYG dell'interfaccia utente dell'applicazione.

È possibile creare, aprire e modificare uno storyboard con Novell iOS designer. In questa guida verrà inoltre illustrato come utilizzare la finestra di progettazione per creare gli storyboard durante C# l'utilizzo di per programmare la navigazione.

## <a name="requirements"></a>Requisiti

Gli storyboard possono essere usati con la finestra di progettazione iOS in Visual Studio per Mac o con Visual Studio 2017 con i carichi di lavoro Novell installati.

## <a name="what-is-a-storyboard"></a>Che cos'è uno storyboard?

Uno storyboard è la rappresentazione visiva di tutte le schermate in un'applicazione. Contiene una sequenza di scene, con ogni scena che rappresenta un *controller di visualizzazione* e le relative *visualizzazioni*. Queste visualizzazioni possono contenere oggetti e [controlli](~/ios/user-interface/controls/index.md) che consentiranno all'utente di interagire con l'applicazione. Questa raccolta di viste e controlli *(o sottoviste)* è nota come gerarchia di *visualizzazione del contenuto*. Le scene sono connesse da oggetti segue, che rappresentano una transizione tra i controller di visualizzazione. Questa operazione viene in genere eseguita creando un segue tra un oggetto nella visualizzazione iniziale e la visualizzazione connessione. Le relazioni nell'area di progettazione sono illustrate nell'immagine seguente:

 [![](images/storyboardsview.png "Le relazioni nell'area di progettazione sono illustrate in questa immagine")](images/storyboardsview.png#lightbox)

Come illustrato, lo storyboard disporrà di ogni scena con contenuto già sottoposto a rendering e illustra le connessioni tra di essi.  Vale la pena notare che, quando parliamo di scene su un iPhone, è preferibile presupporre che una *scena* sullo storyboard sia uguale a una *schermata* di contenuto del dispositivo. Tuttavia, con un iPad è possibile che vengano visualizzate più scene in una sola volta, ad esempio utilizzando un controller di visualizzazione di popover.

Per creare l'interfaccia utente dell'applicazione, in particolare quando si usa Novell, è possibile usare gli storyboard per molti vantaggi. In primo luogo, si tratta di una rappresentazione visiva dell'interfaccia utente, poiché tutti gli oggetti, inclusi i [controlli personalizzati](~/ios/user-interface/designer/ios-designable-controls-overview.md) , vengono sottoposti a rendering in fase di progettazione. Questo significa che prima di compilare o distribuire l'applicazione, è possibile visualizzarne l'aspetto e il flusso. Prendere l'immagine precedente, ad esempio. È possibile esaminare rapidamente l'area di progettazione del numero di scene, il layout di ogni visualizzazione e il modo in cui tutto è correlato. Questo è ciò che rende gli storyboard così potenti.

Gli eventi sono più gestibili con gli storyboard, in particolare quando si usa iOS designer. La maggior parte dei controlli dell'interfaccia utente avrà un elenco di possibili eventi nel riquadro delle proprietà. Il gestore eventi può essere aggiunto qui e completato in un metodo parziale nella classe controller di visualizzazione.

Il contenuto di uno storyboard viene archiviato come file XML. In fase di compilazione, `.storyboard` tutti i file vengono compilati in file binari noti come pennini. In fase di esecuzione, questi pennini vengono inizializzati e ne viene creata un'istanza per creare nuove visualizzazioni.

## <a name="segues"></a>Gli elementi segue

Un oggetto *segue*, o *segue*, viene usato nello sviluppo di iOS per rappresentare una transizione tra le scene. Per creare un segue, tenere premuto il tasto **CTRL** e fare clic su trascinare da una scena a un'altra. Quando si trascina il mouse, viene visualizzato un connettore blu, che indica il punto in cui il segue comporterà come illustrato nell'immagine seguente:

 [![](images/createsegue.png "Viene visualizzato un connettore blu, che indica il punto in cui il segue dimostrerà come illustrato in questa immagine")](images/createsegue.png#lightbox)

Al passaggio del mouse, viene visualizzato un menu che consente di scegliere l'azione per la segue. Potrebbe avere un aspetto simile a quello delle immagini seguenti: 

**Classi precedenti a iOS 8 e dimensioni**:

[![](images/segue1.png "Elenco a discesa segue azione senza classi di dimensioni")](images/segue1.png#lightbox)

**Quando si usano le classi di dimensioni e gli elementi segue adattivo**:

[![](images/16new.png "Elenco a discesa segue azione con classi di dimensioni")](images/16new.png#lightbox)

> [!IMPORTANT]
> Se si usa VMWare per la macchina virtuale Windows, per impostazione predefinita viene eseguito il mapping di CTRL + clic con il pulsante _destro_ del mouse. Per creare un segue, modificare le preferenze della tastiera tramite la tastiera delle **Preferenze** >  **&**  > le**scelte rapide** del mouse e modificare il mapping del **pulsante secondario** come illustrato di seguito:
> 
> [![](images/image22.png "Impostazioni delle preferenze di tastiera e mouse")](images/image22.png#lightbox)
> 
> A questo punto dovrebbe essere possibile aggiungere un segue tra i controller di visualizzazione come di consueto.

Sono disponibili diversi tipi di transizioni, ognuna delle quali fornisce il controllo sulla modalità di presentazione di un nuovo controller di visualizzazione all'utente e sul modo in cui interagisce con altri controller di visualizzazione nello storyboard. Queste sono illustrate di seguito. È anche possibile eseguire la sottoclasse di un oggetto segue per implementare una transizione personalizzata:

- **Mostra/push** : un segue push aggiunge il controller di visualizzazione allo stack di navigazione. Si presuppone che il controller di visualizzazione che ha originato il push faccia parte dello stesso controller di spostamento del controller di visualizzazione aggiunto allo stack. Questa operazione equivale a `pushViewController` e viene in genere utilizzata quando esiste una relazione tra i dati sulle schermate. L'uso di push segue offre il lusso di avere una barra di navigazione con un pulsante indietro e un titolo aggiunti a ogni visualizzazione nello stack, consentendo di eseguire il drill-down nella gerarchia di visualizzazione.
- **Modale** : un segue modale crea una relazione tra due controller di visualizzazione nel progetto, con l'opzione di una transizione animata visualizzata. Il controller di visualizzazione figlio nasconde completamente il controller di visualizzazione padre quando viene visualizzato. A differenza di un segue push, che aggiunge un pulsante indietro per noi; Quando si usa un segue `DismissViewController` modale, è necessario usare per tornare al controller di visualizzazione precedente.
- **Personalizzata** : qualsiasi segue personalizzato può essere creato come sottoclasse di `UIStoryboardSegue`.
- **Rimozione** : è possibile usare un segue di rimozione per tornare indietro tramite un segue push o modale, ad esempio, eliminando il controller di visualizzazione presentato in modo modale. Oltre a questo, è possibile rimuovere non solo uno, ma una serie di gli elementi segue push e modali e tornare a più passaggi nella gerarchia di navigazione con una singola azione di rimozione. Per informazioni su come usare un segue di rimozione in iOS, vedere la pagina relativa alla creazione della ricetta [gli elementi segue di rimozione](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/unwind_segue) .
- Senza **origine** : un segue di origine indica la scena contenente il controller di visualizzazione iniziale e quindi la visualizzazione che l'utente visualizzerà per primo. È rappresentato dal segue mostrato di seguito:  

    [![](images/sourcelesssegue.png "Segue di origine")](images/sourcelesssegue.png#lightbox)

### <a name="adaptive-segue-types"></a>Tipi di segue adattivi

 iOS 8 ha introdotto [le classi di dimensioni](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes) per consentire a un file storyboard iOS di funzionare con tutte le dimensioni dello schermo disponibili, consentendo agli sviluppatori di creare un'interfaccia utente per tutti i dispositivi iOS. Per impostazione predefinita, tutte le nuove applicazioni Novell. iOS utilizzeranno le classi di dimensioni. Per utilizzare le classi di dimensioni di un progetto precedente, fare riferimento alla guida [introduttiva agli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md) . 

Qualsiasi applicazione che utilizza le classi di dimensioni utilizzerà anche il nuovo [*gli elementi segue adattivo*](~/ios/user-interface/storyboards/unified-storyboards.md). Quando si usano le classi di dimensioni, tenere presente che non viene specificato direttamente il modo in cui si usa un iPhone o iPad. In altre parole, si crea un'interfaccia utente che avrà sempre lo stesso aspetto, indipendentemente dalla quantità di spazio reale con cui lavorare. Il gli elementi segue adattivo funziona a giudizio dell'ambiente e determina il modo migliore per presentare il contenuto. I gli elementi segue adattivi sono illustrati di seguito: 

[![](images/adaptivesegue.png "Elenco a discesa gli elementi segue adattivo")](images/adaptivesegue.png#lightbox)

|Segue|Descrizione|
|--- |--- |
|Mostra|Questo è molto simile a un segue push, ma prende in considerazione il contenuto dello schermo.|
|Mostra dettagli|Se l'app visualizza una visualizzazione master e dettagli (ad esempio, in un controller di visualizzazione divisa su un iPad), il contenuto sostituirà la visualizzazione dettagli. Se l'app Visualizza solo il master o i dettagli, il contenuto sostituirà la parte superiore dello stack del controller di visualizzazione.|
|Presentazione|Questa operazione è simile a segue modale e consente di selezionare stili di presentazione e di transizione.|
|Presentazione di popover|Questa operazione presenta il contenuto come un popopov|

### <a name="transferring-data-with-segues"></a>Trasferimento dei dati con gli elementi segue

I vantaggi di un segue non terminano con le transizioni. Possono anche essere usati per gestire il trasferimento dei dati tra i controller di visualizzazione. Questa operazione viene eseguita eseguendo l'override `PrepareForSegue` del metodo sul controller di visualizzazione iniziale e gestendo i dati stessi. Quando viene attivato il segue, ad esempio con un pulsante premere, l'applicazione chiamerà questo metodo, offrendo la possibilità di preparare il nuovo controller di visualizzazione *prima* che si verifichi la navigazione. Nel codice riportato di seguito, dall'esempio [Phoneword](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios) , viene illustrato quanto segue: 

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

In questo esempio, il `PrepareForSegue` metodo verrà chiamato quando segue viene attivato dall'utente. Prima di tutto è necessario creare un'istanza del controller di visualizzazione ' ricezione ' e impostarla come controller di visualizzazione di destinazione di segue. Questa operazione viene eseguita dalla riga di codice seguente:

```csharp
var callHistoryController = segue.DestinationViewController as CallHistoryController;
```

Il metodo ha ora la possibilità di impostare le proprietà su `DestinationViewController`. In questo esempio è stato sfruttato il passaggio di un elenco chiamato `PhoneNumbers` `CallHistoryController` a e l'assegnazione a un oggetto con lo stesso nome:

```csharp
if (callHistoryController != null) {
        callHistoryController.PhoneNumbers = PhoneNumbers;
    }
```

Al termine della transizione, l'utente visualizzerà l'oggetto `CallHistoryController` con l'elenco popolato.

## <a name="adding-a-storyboard-to-a-non-storyboard-project"></a>Aggiunta di uno storyboard a un progetto non Storyboard

In alcuni casi potrebbe essere necessario aggiungere uno storyboard a un file non Storyboard precedentemente. Una volta eseguita questa operazione in Visual Studio per Mac possibile semplificarla attenendosi alla procedura seguente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Creare un nuovo file storyboard passando a **file > nuovo file > iOS > storyboard**, come illustrato di seguito: 
    
    [![](images/new-storyboard-xs.png "Finestra di dialogo nuovo file")](images/new-storyboard-xs.png#lightbox)

2. Aggiungere il nome dello storyboard alla sezione dell' **interfaccia principale** di **info. plist**, come illustrato di seguito:
    
    [![](images/infoplist.png "Editor info. plist")](images/infoplist.png#lightbox)
    
    Questa operazione equivale a creare un'istanza del controller di visualizzazione iniziale nel `FinishedLaunching` metodo all'interno del delegato dell'applicazione. Se questa opzione è impostata, l'applicazione crea un'istanza di una finestra (vedere di seguito), carica lo storyboard principale e assegna un'istanza del controller di visualizzazione iniziale dello storyboard (quello accanto al segue di origine) come `RootViewController` proprietà della finestra e quindi crea finestra visibile sullo schermo.

3. In eseguire l'override del metodo `Window` predefinito, con il codice seguente per implementare la proprietà della finestra: `AppDelegate`

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Per creare un nuovo file storyboard, fare clic con il pulsante destro del mouse sul progetto per **aggiungere > nuovo file > iOS > lo storyboard vuoto**, come illustrato di seguito: 
    
    [![](images/new-storyboard-vs.png "Finestra di dialogo nuovo elemento")](images/new-storyboard-vs.png#lightbox)

2. Aggiungere il nome dello storyboard alla sezione dell' **interfaccia principale** dell'applicazione iOS, come illustrato di seguito:
    
    [![](images/ios-app.png "Editor info. plist")](images/ios-app.png#lightbox)
    
    Questa operazione equivale a creare un'istanza del controller di visualizzazione iniziale nel `FinishedLaunching` metodo all'interno del delegato dell'applicazione. Se questa opzione è impostata, l'applicazione crea un'istanza di una finestra (vedere di seguito), carica lo storyboard principale e assegna un'istanza del controller di visualizzazione iniziale dello storyboard (quello accanto al segue di origine) come `RootViewController` proprietà della finestra e quindi crea finestra visibile sullo schermo.

3. In eseguire l'override del metodo `Window` predefinito, con il codice seguente per implementare la proprietà della finestra: `AppDelegate`

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

-----

## <a name="creating-a-storyboard-with-the-ios-designer"></a>Creazione di uno storyboard con iOS designer

È possibile creare uno storyboard usando il Xamarin Designer per iOS, che è stato integrato senza interruzioni con Visual Studio per Mac e Visual Studio.

Per iniziare a usare iOS designer per creare gli storyboard, seguire la Guida [Hello, iOS Multiscreen](~/ios/get-started/hello-ios-multiscreen/index.md) . In questa procedura dettagliata si esaminerà la navigazione tra i controller di visualizzazione con gli elementi segue e come gestire gli eventi nei controlli.

## <a name="instantiate-storyboards-manually"></a>Creare istanze di storyboard manualmente

Gli storyboard sostituiscono completamente singoli file XIB nel progetto, tuttavia è comunque possibile creare istanze dei singoli controller di visualizzazione in uno `Storyboard.InstantiateViewController`Storyboard usando.

A volte le applicazioni hanno requisiti speciali che non possono essere gestiti con le transizioni dello storyboard predefinite fornite dalla finestra di progettazione. Se, ad esempio, si crea un'applicazione che avvia schermate diverse dallo stesso pulsante, a seconda dello stato corrente di un'applicazione, potrebbe essere necessario creare manualmente un'istanza dei controller di visualizzazione e programmare la transizione.

La schermata seguente mostra due controller di visualizzazione nell'area di progettazione senza segue tra di essi. Nella sezione successiva verrà illustrata la configurazione della transizione nel codice.

 [![](images/viewcontrollerspink.png "Questa schermata mostra due controller di visualizzazione nell'area di progettazione senza segue tra di essi")](images/viewcontrollerspink.png#lightbox)

1. Aggiungere uno _storyboard iPhone vuoto_ a un progetto di progetto esistente:
    
    [![](images/add-storyboard1.png "Aggiunta dello storyboard")](images/add-storyboard1.png#lightbox)

2. Fare doppio clic sullo storyboard appena creato per aprirlo e aggiungere un nuovo **controller di spostamento** all'area di progettazione. Poiché il controller di spostamento è senza interfaccia utente, per impostazione predefinita viene visualizzato con un controller di visualizzazione radice, come illustrato di seguito:

    [![](images/uinavigationcontroller.png "Controller di visualizzazione con gli elementi segue")](images/uinavigationcontroller.png#lightbox)

3. Selezionare il _controller di visualizzazione_ facendo clic sulla barra nera nella parte inferiore. Nel **riquadro delle proprietà**della finestra di progettazione, in **Identity** è possibile specificare una classe personalizzata, oltre a un ID univoco per il controller di visualizzazione. Impostare il **nome della classe** e l'ID `MainViewController`dello **storyboard** su.

    [![](images/identitypanelnew.png "Specifica classe personalizzata")](images/identitypanelnew.png#lightbox)

4. Successivamente, è necessario creare un'istanza dei controller di visualizzazione dallo storyboard e usare l'ID storyboard per farvi riferimento nel codice. L'impostazione dell'ID di ripristino in modo che corrisponda all'ID dello storyboard garantisce che il controller di visualizzazione venga ricreato correttamente se lo stato deve essere ripristinato.

5. Attualmente è disponibile un solo controller di visualizzazione. Trascinare un altro controller di visualizzazione nell'area di progettazione. Nel **riquadro delle proprietà**, in Identity, impostare la classe e l'ID dello `PinkViewController`storyboard su, come illustrato di seguito:

    [![](images/pinkvcnew.png "Riquadro delle proprietà")](images/pinkvcnew.png#lightbox)
    
    L'IDE creerà queste classi personalizzate per i controller di visualizzazione. Questi possono essere visualizzati nel **riquadro della soluzione**, come illustrato nella schermata seguente:
    
    [![](images/solution-pad.png "riquadro della soluzione")](images/solution-pad.png#lightbox)

6. `PinkViewController`Nella selezionare la visualizzazione facendo clic verso il centro del frame del controller. Nel riquadro delle proprietà, in Visualizza modificare lo **sfondo** in Magenta:
    
    [![](images/pinkcontroller.png "Imposta colore di sfondo")](images/pinkcontroller.png#lightbox)

7. Infine, trascinare un pulsante dalla **casella degli strumenti** `MainViewController`in. Nella riquadro delle proprietà assegnare il nome `PinkButton` e il titolo GoToPink, come illustrato di seguito:

    [![](images/pinkbutton.png "Imposta nome pulsante")](images/pinkbutton.png#lightbox)

Lo storyboard è completo, ma se si distribuisce il progetto ora verrà visualizzata una schermata vuota. Ciò è dovuto al fatto che è ancora necessario indicare all'IDE di usare lo storyboard e configurare un controller di visualizzazione radice per fungere da prima visualizzazione. Normalmente questa operazione può essere eseguita tramite le opzioni del progetto, come illustrato in precedenza. Tuttavia, in questo esempio si otterrà lo stesso risultato nel codice aggiungendo quanto segue a **AppDelegate**:

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

Si tratta di una grande quantità di codice, ma solo poche righe sono poco note. In primo luogo, si registra lo storyboard con **AppDelegate** passando il nome dello storyboard, **file mainstoryboard**. A questo punto si indica all'applicazione di creare un'istanza di un controller di visualizzazione iniziale dallo `InstantiateInitialViewController` storyboard chiamando sullo storyboard e si imposta il controller di visualizzazione come controller visualizzazione radice dell'applicazione. Questo metodo determina la prima schermata visualizzata dall'utente e crea una nuova istanza del controller di visualizzazione.

Si noti che nel riquadro della soluzione l'IDE ha creato `MainViewcontroller.cs` una classe e il `corresponding designer.cs` relativo valore quando è stato aggiunto il nome della classe al riquadro delle proprietà nel passaggio 4. È possibile osservare che questa classe ha creato un costruttore speciale che include una classe base:

```csharp
public MainViewController (IntPtr handle) : base (handle) 
{
}
```

Quando si crea uno storyboard usando la finestra di progettazione, l'IDE aggiunge automaticamente l'attributo [[Register]](xref:Foundation.RegisterAttribute) all'inizio della `designer.cs` classe e passa un identificatore di stringa, che è identico all'ID dello storyboard specificato nel passaggio precedente. In questo modo verrà C# collegato alla scena pertinente nello storyboard.

A un certo punto potrebbe essere necessario aggiungere una classe esistente che **non** è stata creata nella finestra di progettazione. In questo caso, è necessario registrare questa classe come di consueto:

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

Per ulteriori informazioni sulla registrazione di classi e metodi, vedere la documentazione relativa al [registrar](http://docs.xamarin.com/guides/ios/advanced_topics/registrar/).

L'ultimo passaggio di questa classe consiste nel collegare il pulsante e la transizione al controller di visualizzazione rosa. Verrà creata un'istanza `PinkViewController` di dallo storyboard. verrà quindi programmato un segue push con `PushViewController`, come illustrato nel codice di esempio seguente:

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

L'esecuzione dell'applicazione produce un'applicazione a 2 schermate:

![](images/finishedstoryboard.png "Schermate di esecuzione dell'app di esempio")

## <a name="conditional-segues"></a>Gli elementi segue condizionale

Spesso, il passaggio da un controller di visualizzazione a quello successivo dipende da una determinata condizione. Se ad esempio si sta creando una semplice schermata di accesso, si desidera passare alla schermata successiva solo *se* il nome utente e la password sono stati verificati.

Nell'esempio successivo verrà aggiunto un campo password all'esempio precedente. L'utente sarà in grado di accedere al *PinkViewController* solo se immette la password corretta; in caso contrario, verrà visualizzato un errore.

Prima di iniziare, seguire i passaggi da 1 a 8 precedenti. In questi passaggi viene creato lo storyboard, si inizia a creare l'interfaccia utente e si indica al delegato dell'app quale controller di visualizzazione usare come RootViewController.

1. A questo punto, è `MainViewController` possibile creare l'interfaccia utente e aggiungere le visualizzazioni aggiuntive elencate in per renderlo simile allo screenshot seguente:

    - UITextField
        - Nome: PasswordTextField
        - Segnaposto ' Immettere la password del segreto '
    - UILabel
        - Testo Errore Password errata. Non è necessario passare!'
        - Colore: Rosso
        - Allineamento Center
        - Linee 2
        - Casella di controllo ' nascosto ' selezionata    
        
    [![](images/passwordvc.png "Allinea al centro")](images/passwordvc.png#lightbox)
    
2. Creare una segue tra il pulsante Vai a rosa e il controller di visualizzazione premendo CTRL-trascinando da *PinkButton* a *PinkViewController*e selezionando **push** al passaggio del mouse. 

3. Fare clic su segue e assegnargli l' *identificatore* `SegueToPink`:

    [![](images/namesegue.png "Fare clic su segue e assegnargli l'identificatore SegueToPink")](images/namesegue.png#lightbox)  

4. Aggiungere infine il metodo ShouldPerformSegue seguente alla `MainViewController` classe:

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

In questo codice è stata abbinata la segueIdentifier al `SegueToPink` segue, quindi è possibile testare una condizione, in questo caso una password valida. Se la condizione restituisce `true`, il segue eseguirà e `PinkViewController`presenterà. Se `false`, il nuovo controller di visualizzazione non verrà visualizzato.

È possibile applicare questo approccio a qualsiasi segue in questo controller di visualizzazione controllando l'argomento segueIdentifier per il metodo ShouldPerformSegue. In questo caso è presente un solo identificatore segue, `SegueToPink`ovvero.

Per un esempio funzionante, vedere la soluzione storyboards. Conditional nell' [esempio storyboards manuale](https://docs.microsoft.com/samples/xamarin/ios-samples/manualstoryboard) .

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>Utilizzo di riferimenti a storyboard

Un riferimento a storyboard consente di eseguire una progettazione storyboard complessa e di grandi dimensioni e suddividerla in storyboard più piccoli a cui viene fatto riferimento dall'originale, eliminando così la complessità e rendendo più semplice la progettazione e la gestione degli storyboard singoli risultanti.

Inoltre, un riferimento a storyboard può fornire un _ancoraggio_ a un'altra scena nello stesso storyboard o in una scena specifica in un'altra.

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>Riferimento a uno storyboard esterno

Per aggiungere un riferimento a uno storyboard esterno, procedere come segue:

1. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Aggiungi** > **nuovo file...** Storyboard iOS.  >  >  Immettere un **nome** per il nuovo storyboard e fare clic sul pulsante **nuovo** :
    
    [![](images/ref01.png "Finestra di dialogo nuovo file")](images/ref01.png#lightbox)
    
2. Progettare il layout dei nuovi scenari dello storyboard come si farebbe normalmente e salvare le modifiche: 
    
    [![](images/ref02.png "Layout della nuova scena")](images/ref02.png#lightbox)
    
3. Aprire lo storyboard a cui si intende aggiungere il riferimento in iOS designer.

4. Trascinare un **riferimento a Storyboard** dalla **casella degli strumenti** nel area di progettazione: 
    
    [![](images/ref03.png "Riferimento A storyboard")](images/ref03.png#lightbox)
    
5. Nella scheda **widget** di **Esplora proprietà**Selezionare il nome dello **storyboard** creato in precedenza: 

    [![](images/ref04.png "Scheda widget")](images/ref04.png#lightbox)
    
6. CTRL: fare clic su un widget dell'interfaccia utente (ad esempio un pulsante) in una scena esistente e creare un nuovo segue per il **riferimento allo storyboard** appena creato: 

    [![](images/ref05.png "Creazione di un segue")](images/ref05.png#lightbox) 
    
7. Dal menu popup selezionare **Mostra** per completare il segue: 

    [![](images/ref06.png "Selezione di Mostra per completare il segue")](images/ref06.png#lightbox) 
    
8. Salvare le modifiche apportate allo storyboard.

Quando l'app viene eseguita e l'utente fa clic sull'elemento dell'interfaccia utente da cui è stato creato il segue, verrà visualizzato il controller di visualizzazione iniziale dello storyboard esterno specificato nel riferimento dello storyboard.

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>Riferimento a una scena specifica in uno storyboard esterno

Per aggiungere un riferimento a una scena specifica uno storyboard esterno (e non il controller di visualizzazione iniziale), eseguire le operazioni seguenti:

1. Nella **Esplora soluzioni**fare doppio clic sullo storyboard esterno per aprirlo per la modifica.

2. Aggiungere una nuova scena e progettarne il layout normalmente: 

    [![](images/ref07.png "Nuovo layout della scena")](images/ref07.png#lightbox)
    
3. Nella scheda **widget** di **Esplora proprietà**immettere un **ID storyboard** per il controller di visualizzazione della nuova scena: 

    [![](images/ref08.png "Immettere un ID storyboard per il controller di visualizzazione delle nuove scene")](images/ref08.png#lightbox)
    
4. Aprire lo storyboard a cui si intende aggiungere il riferimento in iOS designer.

5. Trascinare un **riferimento a Storyboard** dalla **casella degli strumenti** nel area di progettazione: 

    [![](images/ref03.png "Riferimento A storyboard")](images/ref03.png#lightbox)
    
6. Nella scheda **widget** di **Esplora proprietà**Selezionare il nome dello **storyboard** e l' **ID di riferimento** (ID Storyboard) della scena creata in precedenza: 

    [![](images/ref09.png "Scheda widget")](images/ref09.png#lightbox)
    
7. CTRL: fare clic su un widget dell'interfaccia utente (ad esempio un pulsante) in una scena esistente e creare un nuovo segue per il **riferimento allo storyboard** appena creato: 

    [![](images/ref10.png "Creazione di un segue")](images/ref10.png#lightbox) 
    
8. Dal menu popup selezionare **Mostra** per completare il segue: 

    [![](images/ref06.png "Selezione di Mostra per completare il segue")](images/ref06.png#lightbox) 
    
9. Salvare le modifiche apportate allo storyboard.

Quando l'app viene eseguita e l'utente fa clic sull'elemento dell'interfaccia utente da cui è stato creato il segue, viene visualizzata la scena con l' **ID di storyboard** specificato dallo storyboard esterno specificato nel riferimento dello storyboard.

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>Riferimento a una scena specifica nello stesso storyboard

Per aggiungere un riferimento a una scena specifica dello stesso storyboard, procedere come segue:

1. Nella **Esplora soluzioni**fare doppio clic sullo storyboard per aprirlo per la modifica.

2. Aggiungere una nuova scena e progettarne il layout normalmente: 

    [![](images/ref11.png "Nuovo layout della scena")](images/ref11.png#lightbox)

3. Nella scheda **widget** di **Esplora proprietà**immettere un **ID storyboard** per il controller di visualizzazione della nuova scena: 

    [![](images/ref12.png "Scheda widget")](images/ref12.png#lightbox)
    
4. Trascinare un **riferimento a Storyboard** dalla **casella degli strumenti** nel area di progettazione: 

   [![](images/ref03.png "Riferimento A storyboard")](images/ref03.png#lightbox)
    
5. Nella scheda **widget** di **Esplora proprietà**Selezionare **ID riferimento** (ID Storyboard) della scena creata in precedenza: 

    [![](images/ref13.png "Scheda widget")](images/ref13.png#lightbox)
    
6. CTRL: fare clic su un widget dell'interfaccia utente (ad esempio un pulsante) in una scena esistente e creare un nuovo segue per il **riferimento allo storyboard** appena creato: 

    [![](images/ref14.png "Creazione di un segue")](images/ref14.png#lightbox) 
    
7. Dal menu popup selezionare **Mostra** per completare il segue: 

    [![](images/ref06.png "Selezione di Mostra per completare il segue")](images/ref06.png#lightbox) 
    
8. Salvare le modifiche apportate allo storyboard.

Quando l'app viene eseguita e l'utente fa clic sull'elemento dell'interfaccia utente da cui è stato creato il segue, viene visualizzata la scena con l' **ID di storyboard** specificato nello stesso storyboard specificato nel riferimento dello storyboard.

## <a name="summary"></a>Riepilogo

In questo articolo viene introdotto il concetto di storyboard e il modo in cui possono essere utili per lo sviluppo di applicazioni iOS. Vengono illustrate le scene, i controller di visualizzazione, le visualizzazioni e le gerarchie di visualizzazione e il modo in cui le scene sono collegate con tipi diversi di gli elementi segue.  Esplora inoltre la creazione manuale di un'istanza dei controller di visualizzazione da uno storyboard e la creazione di gli elementi segue condizionali.

## <a name="related-links"></a>Collegamenti correlati

- [Storyboard manuale (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/manualstoryboard/)
- [Introduzione a iOS designer](~/ios/user-interface/designer/introduction.md)
- [Conversione in Storyboard](https://developer.apple.com/library/ios/#releasenotes/Miscellaneous/RN-AdoptingStoryboards/)
- [Riferimento alla classe UIStoryboard](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIStoryboard_Class/Reference/Reference.html)
