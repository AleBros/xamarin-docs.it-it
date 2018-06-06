---
title: Introduzione agli storyboard in xamarin. IOS
description: Questo documento viene fornita un'introduzione agli storyboard in xamarin. IOS. Questa procedura viene descritto l'utilizzo di uno storyboard per definire un'interfaccia utente, segues e come utilizzare la finestra di progettazione iOS per modificare i file storyboard.
ms.prod: xamarin
ms.assetid: A3339BD2-9F56-7965-25F5-4B7C991EB775
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 3366da2f5dc869c7b075bf32be238a1ebd3145d7
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790968"
---
# <a name="introduction-to-storyboards-in-xamarinios"></a>Introduzione agli storyboard in xamarin. IOS

In questa Guida verrà spiegato quale uno Storyboard è ed esaminare alcuni dei componenti principali: ad esempio Segues. Verrà esaminato come gli storyboard possono essere creati e usati, e quali vantaggi hanno per uno sviluppatore.

Prima che il formato del file dello Storyboard è stato introdotto da Apple, come una rappresentazione visiva dell'interfaccia utente di un'applicazione iOS, gli sviluppatori creati file XI per ogni controller di visualizzazione e programmate manualmente la navigazione tra ogni visualizzazione.  Utilizzando uno Storyboard consente allo sviluppatore di definire i controller di visualizzazione e la navigazione tra di essi nell'area di progettazione e offre funzionalità di modifica WYSIWYG dell'interfaccia utente dell'applicazione.

Uno Storyboard può essere creato, aperti e modificato con Xamarin iOS finestra di progettazione. Questa Guida verrà anche questa procedura dettagliata come utilizzare la finestra di progettazione per creare gli storyboard mentre si utilizza c# per lo spostamento del programma.


## <a name="requirements"></a>Requisiti

Gli storyboard sono utilizzabile con la progettazione di Visual Studio per Mac iOS o con Visual Studio 2015 e 2017 con i carichi di lavoro Xamarin installati.

## <a name="what-is-a-storyboard"></a>Che cos'è uno Storyboard?

Uno Storyboard è la rappresentazione visiva di tutte le schermate in un'applicazione. Contiene una sequenza delle scene, con ogni scena che rappresenta un *View Controller* e il relativo *viste*. Queste viste possono contenere gli oggetti e [controlli](~/ios/user-interface/controls/index.md) che consentono all'utente di interagire con l'applicazione. Questa raccolta di controlli e visualizzazioni (o *sottoviste*) è noto come un *contenuto Visualizza gerarchia*. Scene connessi da definire oggetti che rappresentano una transizione tra controller di visualizzazione. Ciò in genere è possibile creare un segue tra un oggetto nella visualizzazione iniziale e la visualizzazione di connessione. Le relazioni nella finestra di progettazione sono illustrate nella figura seguente:

 [![](images/storyboardsview.png "Le relazioni nella finestra di progettazione sono illustrate in questa immagine")](images/storyboardsview.png#lightbox)

Come illustrato, lo storyboard si troveranno ogni le scene con contenuto già eseguito il rendering e vengono illustrate le connessioni tra di essi.  Vale la pena notare a questo punto, quando si parlerà automaticamente in un iPhone, è opportuno presupporre che un *scena* nello storyboard è uguale a uno *schermata* del contenuto nel dispositivo. Tuttavia, con un iPad, che è possibile avere più scene vengono visualizzati in una sola volta, ad esempio, utilizzando un controller di visualizzazione Popover.

Esistono numerosi vantaggi nell'utilizzo di storyboard per creare l'interfaccia utente dell'applicazione, soprattutto quando si utilizza Xamarin. In primo luogo, è una rappresentazione visiva dell'interfaccia utente, come tutti gli oggetti, tra cui [controlli personalizzati](~/ios/user-interface/designer/ios-designable-controls-overview.md) – viene eseguito il rendering in fase di progettazione. Ciò significa che prima di compilare o distribuire l'applicazione è possibile visualizzare l'aspetto e il flusso. L'immagine precedente, si consideri ad esempio. È possibile sapere da una sintesi la superficie di attacco sono scene di quanti sono di progettazione, il layout di ogni visualizzazione e tutto il modo in cui è correlato. Questo risulta storyboard avanzati.

Gli eventi sono più gestibili con storyboard, specialmente quando si usa la finestra di progettazione iOS. La maggior parte dei controlli dell'interfaccia utente avrà un elenco di possibili eventi nel riquadro proprietà. Il gestore dell'evento può essere aggiunti qui e completato in un metodo parziale della classe di controller di visualizzazione...

Il contenuto di uno storyboard è archiviato come file XML. In fase di compilazione, qualsiasi `.storyboard` file vengono compilati in file binari, noti come nibs. In fase di esecuzione, queste nibs inizializzati e creare un'istanza per creare nuove visualizzazioni.

## <a name="segues"></a>Segues

Oggetto *Segue*, o *oggetto definire*, viene utilizzato in fase di sviluppo di iOS per rappresentare una transizione tra automaticamente. Per creare un segue, tenere premuto il **Ctrl** chiave e i clic e trascinamento tra una scena a un altro. Quando si trascina il mouse, viene visualizzato un collegamento blu, che indica dove la segue si comporti come illustrato nell'immagine riportata di seguito:

 [![](images/createsegue.png "Viene visualizzato un collegamento blu, che indica dove la segue si comporti come illustrato nell'immagine")](images/createsegue.png#lightbox)

Nel rilascio del mouse, verrà visualizzato un menu consentendo di scegliere l'azione per la segue. Risultato potrebbe essere simile a immagini riportate di seguito: 

**Classi di pre-iOS 8 e le dimensioni**:

[![](images/segue1.png "Elenco a discesa azione definire senza classi di dimensione")](images/segue1.png#lightbox)

**Quando si utilizzano le classi di dimensioni e Segues adattivo**:

[![](images/16new.png "Elenco a discesa azione definire con classi di dimensione")](images/16new.png#lightbox)

> [!IMPORTANT]
> Se si utilizza VMWare per la macchina virtuale di Windows, Ctrl + clic è mappato come le _destro_ pulsante del mouse per impostazione predefinita. Per creare un Segue, modificare le preferenze di tastiera tramite **preferenze** > **tastiera e Mouse** > **eseguibili con il Mouse** e riassociare il **Pulsante secondario** come illustrato di seguito:
> 
> [![](images/image22.png "Impostazioni di preferenza tastiera e Mouse")](images/image22.png#lightbox)
> 
> È ora possibile aggiungere un segue tra i controller di visualizzazione come di consueto.

Esistono diversi tipi di transizioni di ogni controllo che fornisce come nuovo controller di visualizzazione vengono presentati all'utente e le relative modalità di interazione con altri controller di visualizzazione dello storyboard. Questi vengono spiegati di seguito. È anche possibile sottoclasse oggetto segue per implementare una transizione personalizzata:

-  **Mostra / Push** : definire un push aggiunge il controller di visualizzazione per lo stack di navigazione. Si presuppone che il controller di visualizzazione origine il push è parte dello stesso controller come controller di visualizzazione che viene aggiunto allo stack di navigazione. Questa operazione equivale al `pushViewController` e viene in genere utilizzata quando è presente una relazione tra i dati nelle schermate. Usando il push definire offre permettersi di disporre di una barra di spostamento con un pulsante Indietro e titolo aggiunto a ogni vista nello stack, consentendo di drill-down navigazione tramite la gerarchia della visualizzazione.
-  **Modale** : un segue modale creare una relazione tra i controller di qualsiasi due vista nel progetto, con l'opzione di una transizione animata attualmente visualizzata. Il controller della visualizzazione figlio nasconderà completamente il controller di visualizzazione padre quando introdotti nella vista. A differenza di push definire, che aggiunge un pulsante Indietro per noi; Quando tramite un oggetto modale definire `DismissViewController` deve essere utilizzata per restituire al controller di visualizzazione precedente.
-  **Personalizzato** : qualsiasi personalizzati segue può essere creato come una sottoclasse di ` UIStoryboardSegue`.
-  **Rimozione** : rimozione definire può essere usata per passare attraverso un push o modale definire, ad esempio, ignorando il controller di visualizzazione presentati come form modale. Oltre a ciò, è possibile rimuovere tramite non solo uno, ma una serie di push e modale segues e tornare più passaggi nella propria gerarchia di navigazione con una singola azione di rimozione. Per comprendere come utilizzare un'operazione di rimozione definire in iOS, leggere la [Creazione rimozione Segues](https://developer.xamarin.com/recipes/ios/general/storyboard/unwind_segue/) recipe.
-  **Sourceless** : segue un sourceless indica la scena che contiene il controller di visualizzazione iniziale e visualizzazione in cui l'utente verrà visualizzato innanzitutto. È rappresentato dal segue illustrato di seguito:  

    [![](images/sourcelesssegue.png "Segue un sourceless")](images/sourcelesssegue.png#lightbox)

### <a name="adaptive-segue-types"></a>Adattata definire i tipi

 iOS 8 introdotto [classi](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes) per consentire a un file storyboard iOS funzionare con tutte le dimensioni dello schermo disponibile, consentendo agli sviluppatori di creare un'interfaccia utente per tutti i dispositivi iOS. Per impostazione predefinita, tutte le nuove applicazioni xamarin utilizzerà le classi di dimensioni. Per utilizzare le classi di dimensioni di un progetto precedente, consultare il [Introduzione agli storyboard unificata](~/ios/user-interface/storyboards/unified-storyboards.md) Guida. 
 
Qualsiasi applicazione che utilizza le classi di dimensioni useranno anche il nuovo [ *Segues adattivo*](~/ios/user-interface/storyboards/unified-storyboards.md). Quando si utilizzano le classi di dimensioni, tenere presente che è non specificare direttamente se si sta usando un iPhone o iPad. In altre parole viene creata un'interfaccia utente che sarà sempre lo stesso, indipendentemente dalla quantità immobiliare deve funzionare con. Lavoro Segues adattivo giudicare l'ambiente e determinare il modo migliore presentare il contenuto. Il Segues adattivo vengono mostrate di seguito: 

[![](images/adaptivesegue.png "L'elenco a discesa Segues adattivo")](images/adaptivesegue.png#lightbox)

|Definire i|Descrizione|
|--- |--- |
|Mostra|È molto simile a definire un Push, ma tiene in considerazione il contenuto della schermata.|
|Visualizzare i dettagli|Se l'app è presente una visualizzazione master e di dettaglio (ad esempio, in un controller di visualizzazione di divisione su un iPad), il contenuto sostituirà la vista Dettagli. Se l'app viene visualizzato solo il database master o dettaglio, il contenuto sostituirà l'inizio dello stack di controller di visualizzazione.|
|Presentazione|Ciò è simile al segue modale e consente la selezione di stili di presentazione e di transizione.|
|Popover presentazione|Questo presenta il contenuto come un popover|

### <a name="transferring-data-with-segues"></a>Il trasferimento dei dati con Segues

I vantaggi di un segue non terminano con le transizioni. Possono inoltre essere utilizzati per gestire il trasferimento dei dati tra i controller di visualizzazione. A questo scopo, si esegue l'override di `PrepareForSegue` metodo sul controller di visualizzazione iniziale e la gestione dei dati effettuata. Quando viene attivata la segue, ad esempio, con un pulsante: l'applicazione verrà chiamare questo metodo, fornisce la possibilità di preparare il nuovo controller di visualizzazione *prima* si verifica di navigazione. Il codice seguente, dal [Phoneword](https://developer.xamarin.com/samples/monotouch/Hello_iOS/) di esempio, viene illustrata questa: 


```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, 
NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    var callHistoryContoller = segue.DestinationViewController 
                                  as CallHistoryController;

    if (callHistoryContoller != null) {
        callHistoryContoller.PhoneNumbers = PhoneNumbers;
    }
}
```

In questo esempio, il `PrepareForSegue` metodo verrà chiamato quando viene attivato il segue dall'utente. È innanzitutto necessario creare un'istanza del controller di visualizzazione 'ricevente' e impostare come destinazione del segue View Controller. Questa operazione viene eseguita la riga di codice riportato di seguito:

```csharp
var callHistoryContoller = segue.DestinationViewController as CallHistoryController;
```

Il metodo ha ora la possibilità di impostare le proprietà di `DestinationViewController`. In questo esempio è stato reindirizzato vantaggio di questo passando un elenco denominato `PhoneNumbers` per il `CallHistoryController` e assegnarlo a un oggetto con lo stesso nome:

```csharp
if (callHistoryContoller != null) {
        callHistoryContoller.PhoneNumbers = PhoneNumbers;
    }
```

Al termine della transizione, l'utente visualizzerà il `CallHistoryController` con l'elenco popolato.

## <a name="adding-a-storyboard-to-a-non-storyboard-project"></a>Aggiunta di uno Storyboard a un progetto Non Storyboard

In alcuni casi si potrebbe essere necessario aggiungere uno Storyboard in un file in precedenza non storyboard. Una volta questa operazione in Visual Studio per Mac può essere semplificata con la procedura seguente:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Creare un nuovo file di Storyboard selezionando **File > Nuovo > iOS > Storyboard**, come illustrato di seguito: 
    
    [![](images/new-storyboard-xs.png "Finestra di dialogo Nuovo file")](images/new-storyboard-xs.png#lightbox)

2. Aggiungere il nome di uno Storyboard per il **interfaccia principale** sezione il **Info. plist**, come illustrato di seguito:
    
    [![](images/infoplist.png "L'editor di Info. plist")](images/infoplist.png#lightbox)
    
    Questa operazione l'equivalente di un'istanza il Controller di visualizzazione iniziale di `FinishedLaunching` metodo all'interno del delegato di App. Con questa opzione è impostata, l'applicazione crea un'istanza di una finestra (vedere sotto), carica lo storyboard principale e lo assegna un'istanza del Controller di visualizzazione iniziale dello storyboard (quello accanto il Segue sourceless) come il `RootViewController` proprietà della finestra e quindi imposta la finestra è visibile sullo schermo.

3. Nel `AppDelegate`, sostituire il valore predefinito `Window` (metodo), con il codice seguente per implementare la proprietà della finestra:
        
        public override UIWindow Window {
            get;
            set;
            }
            
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Facendo clic sul progetto per creare un nuovo file di Storyboard **Aggiungi > Nuovo > iOS > Storyboard vuoto**, come illustrato di seguito: 
    
    [![](images/new-storyboard-vs.png "Finestra di dialogo Nuovo elemento")](images/new-storyboard-vs.png#lightbox)

2. Aggiungere il nome di uno Storyboard per il **interfaccia principale** sezione di iOS dell'applicazione, come illustrato di seguito:
    
    [![](images/ios-app.png "L'editor di Info. plist")](images/ios-app.png#lightbox)
    
    Questa operazione l'equivalente di un'istanza il Controller di visualizzazione iniziale di `FinishedLaunching` metodo all'interno del delegato di App. Con questa opzione è impostata, l'applicazione crea un'istanza di una finestra (vedere sotto), carica lo storyboard principale e lo assegna un'istanza del Controller di visualizzazione iniziale dello storyboard (quello accanto il Segue sourceless) come il `RootViewController` proprietà della finestra e quindi imposta la finestra è visibile sullo schermo.

3. Nel `AppDelegate`, sostituire il valore predefinito `Window` (metodo), con il codice seguente per implementare la proprietà della finestra:

        public override UIWindow Window {
            get;
            set;
            }
            
-----

## <a name="creating-a-storyboard-with-the-ios-designer"></a>Creazione di uno Storyboard con la finestra di progettazione iOS

È possibile creare uno Storyboard utilizzando la finestra di progettazione di Xamarin per iOS, è stato integrato senza problemi con Visual Studio per Mac e Visual Studio.

Per iniziare a utilizzare la finestra di progettazione iOS per creare gli storyboard, seguire la [Hello, iOS Multiscreen](~/ios/get-started/hello-ios-multiscreen/index.md) Guida. In questa procedura dettagliata si esploreranno navigazione tra i controller di visualizzazione utilizzando Segues e come gestire gli eventi sui controlli.

## <a name="instantiate-storyboards-manually"></a>Creare manualmente un'istanza di storyboard

Storyboard sostituiscono completamente i singoli file XI nel progetto, tuttavia i controller di visualizzazione in uno Storyboard possono ancora essere implementati con `Storyboard.InstantiateViewController`.

Talvolta le applicazioni sono necessari requisiti speciali che non possono essere gestiti con le transizioni di storyboard predefiniti fornite dalla finestra di progettazione. Ad esempio, se si crea un'applicazione che avvia schermate diverse dal pulsante stesso, a seconda dello stato corrente di un'applicazione, potrebbe voler creare manualmente un'istanza di controller di visualizzazione e la transizione del programma effettuata.

La schermata riportata di seguito viene illustrato come definire i due controller Visualizza l'area di progettazione senza tra di essi. La sezione successiva verrà illustrata la modalità di transizione da impostare nel codice.

 [![](images/viewcontrollerspink.png "Questa schermata mostra due controller di visualizzazione nell'area di progettazione senza definire tra di essi")](images/viewcontrollerspink.png#lightbox)

1. Aggiungere un _vuoto iPhone Storyboard_ a un progetto a progetto esistente:
    
    [![](images/add-storyboard1.png "Aggiunta di uno storyboard")](images/add-storyboard1.png#lightbox)

2. Fare doppio clic sullo storyboard appena creato per aprirlo e aggiungere un nuovo **navigazione Controller** all'area di progettazione. Il Controller di navigazione è minore dell'interfaccia utente, per impostazione predefinita sarà quello con un Controller di visualizzazione principale, come illustrato di seguito:

    [![](images/uinavigationcontroller.png "Visualizzazione controller con Segues")](images/uinavigationcontroller.png#lightbox)

3. Selezionare il _View Controller_ facendo clic sulla barra di colore nero nella parte inferiore. Nella finestra di progettazione **proprietà riempimento**in **identità** è possibile specificare una classe personalizzata, nonché un ID univoco per il Controller di visualizzazione. Impostare il **nome classe** e **ID Storyboard** a `MainViewController`.

    [![](images/identitypanelnew.png "Specificare una classe personalizzata")](images/identitypanelnew.png#lightbox)

4. In un secondo momento, sarà necessario creare un'istanza di questo controller di visualizzazione dallo storyboard e verrà utilizzato l'ID di uno Storyboard per farvi riferimento nel codice. Impostazione dell'ID di ripristino in modo che corrisponda all'ID di uno Storyboard assicura che il controller di visualizzazione ottiene ricreato correttamente se lo stato deve essere ripristinato.

5. Attualmente solo abbiamo un controller di visualizzazione. Trascinare un altro controller di visualizzazione nella finestra di progettazione. Nel **proprietà riempimento**, con identità, impostare la classe e l'ID di uno Storyboard da `PinkViewController`, come illustrato di seguito:

    [![](images/pinkvcnew.png "Il riquadro proprietà")](images/pinkvcnew.png#lightbox)
    
    L'IDE creerà le classi personalizzate per i controller di visualizzazione. Questi possono essere visualizzati nel **soluzione riempimento**, come illustrato nella schermata seguente:
    
    [![](images/solution-pad.png "Riempimento di soluzione")](images/solution-pad.png#lightbox)

6. Nel `PinkViewController`, selezionare la vista facendo verso il centro del frame del controller. Nel riquadro proprietà, in vista modificare il **Background** in Magenta:
    
    [![](images/pinkcontroller.png "Impostare il colore di sfondo")](images/pinkcontroller.png#lightbox)

7. Infine, trascinare un pulsante dal **della casella degli strumenti** sul `MainViewController`. Nel riquadro proprietà, assegnarvi il nome `PinkButton` e GoToPink il titolo, come illustrato di seguito:

    [![](images/pinkbutton.png "Impostare il nome pulsante")](images/pinkbutton.png#lightbox)

Lo storyboard è stato completato, ma se si distribuisce il progetto a questo punto, si otterrà una schermata vuota. Ciò avviene perché è necessario indicare all'IDE per utilizzare questo storyboard e per configurare un controller di visualizzazione principale come la prima vista. In genere questa operazione può essere eseguita tramite le opzioni di progetto, come illustrato in precedenza. Tuttavia in questo esempio è possibile ottenere lo stesso risultato nel codice, aggiungendo il comando seguente per il **AppDelegate**:

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

Che è la quantità di codice, ma solo alcune righe si ha dimestichezza. Innanzitutto si registra il nostro storyboard con il **AppDelegate** passando il nome dello storyboard, **MainStoryboard**. Successivamente, è possibile indicare l'applicazione per creare un'istanza di un controller di visualizzazione iniziale dallo storyboard chiamando `InstantiateInitialViewController` nel nostro storyboard, e tale controller di visualizzazione viene impostata come controller di visualizzazione principale dell'applicazione. Questo metodo determina la prima schermata visualizzata all'utente e crea una nuova istanza di tale Controller di visualizzazione.

Si noti che in riquadro soluzione che ha creato l'IDE un `MainViewcontroller.cs` (classe) e il relativo `corresponding designer.cs` quando viene aggiunto il nome della classe nel riquadro proprietà del passaggio 4. È possibile osservare questa classe di creazione di un costruttore speciale che include una classe di base:

```csharp
public MainViewController (IntPtr handle) : base (handle) 
{
}
```


Durante la creazione di uno Storyboard utilizzando la finestra di progettazione, l'IDE aggiungerà automaticamente il [[registrare]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/) attributo all'inizio della `designer.cs` classe e passare un identificatore di stringa, corrisponde all'ID di uno Storyboard specificato nella passaggio precedente. Ciò consente di collegare c# scena pertinente dello storyboard.

A un certo punto è necessario aggiungere una classe esistente che è stata **non** creato nella finestra di progettazione. In questo caso, registrare questa classe come di consueto:

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

Per ulteriori informazioni sulla registrazione di classi e metodi, vedere il [tipo Registrar](http://docs.xamarin.com/guides/ios/advanced_topics/registrar/) documentazione.

L'ultimo passaggio di questa classe consiste nell'associare il pulsante e la transizione al controller di visualizzazione rosa. È possibile creare un'istanza di `PinkViewController` dallo Storyboard; quindi, si verrà programmare un push definire con `PushViewController`, come illustrato nell'esempio di codice riportato di seguito:

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

Esecuzione dell'applicazione genera un'applicazione schermata 2:

![](images/finishedstoryboard.png "Eseguire schermate delle app di esempio")

## <a name="conditional-segues"></a>Segues condizionale

Spesso, lo spostamento da un controller di visualizzazione al successivo è dipendente da una determinata condizione. Ad esempio, se si stava eseguendo una semplice schermata di accesso è solo necessario passare alla schermata successiva *se* il nome utente e la password siano stati verificati.

Nell'esempio seguente si aggiungerà un campo di password per l'esempio precedente. L'utente sarà solo in grado di accedere il *PinkViewController* se si immette la password corretta, in caso contrario un errore verrà visualizzato.

Prima di iniziare, seguire i passaggi 1-8. In questa procedura viene creata il storyboard, iniziare a creare l'interfaccia utente e indicare il delegato App il Controller di visualizzazione da utilizzare come RootViewController.

1. A questo punto, si accumulano l'interfaccia utente e aggiungere le visualizzazioni aggiuntive elencate per il `MainViewController` in modo che appaia simili a quelli presenti nella schermata seguente:

    - UITextField
        - Name: PasswordTextField
        - Segnaposto: 'Immettere la Password segreta'
    - UILabel
        - Testo: "errore: Password errata. Non deve passare!'
        - Colori: rosso
        - Allineamento: Center
        - Righe: 2
        - Casella di controllo 'Hidden' selezionata 
        
    [![](images/passwordvc.png "Righe Center")](images/passwordvc.png#lightbox)
    
2. Creare un Segue tra pulsante Go rosa e il controller di visualizzazione mediante il trascinamento Ctrl dal *PinkButton* per il *PinkViewController*e selezionando **Push** nel rilascio del mouse . 

3. Fare clic su di Segue e assegnargli il *identificatore* `SegueToPink`:

    [![](images/namesegue.png "Fare clic su di Segue e assegnargli il SegueToPink identificatore")](images/namesegue.png#lightbox)  
    

4. Infine, aggiungere il seguente metodo ShouldPerformSegue per la `MainViewController` classe:

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

In questo codice è stato corrispondente il segueIdentifier per il nostro `SegueToPink` definire, pertanto è quindi possibile testare una condizione; una password valida in questo caso. Se la condizione restituisce `true`, eseguirà il Segue e offrirà la `PinkViewController`. Se `false`, non verrà presentato nel nuovo Controller di visualizzazione.

È possibile applicare questo approccio a qualsiasi Segue nel controller di visualizzazione selezionando l'argomento segueIdentifier al metodo ShouldPerformSegue. In questo caso è necessario solo un identificatore Segue – `SegueToPink`.

Fare riferimento alla soluzione Storyboards.Conditional nel [esempio manuale storyboard](https://developer.xamarin.com/samples/monotouch/ManualStoryboard/) per un esempio funzionante.

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>Utilizzo di riferimenti a uno Storyboard

Un riferimento di Storyboard consente di prendere una progettazione di uno Storyboard grande e complessa e suddividerla in più piccoli storyboard che ottenere cui viene fatto riferimento dall'originale, pertanto di rimozione di complessità e rendere risultante singoli rimuovere storyboard più semplice alla progettazione e Gestisci.

Inoltre, è possibile fornire un riferimento di Storyboard un _ancoraggio_ alla scena un'altra all'interno dello stesso Storyboard o una scena specifica in un altro.

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>Riferimento a uno Storyboard esterno

Per aggiungere un riferimento a uno Storyboard esterno, eseguire le operazioni seguenti:

1. Nel **Esplora**del mouse sul nome del progetto e scegliere **Aggiungi** > **nuovo File...**   >  **iOS** > **Storyboard**. Immettere un **nome** per fare clic su Nuovo Storyboard la **New** pulsante:
    
    [![](images/ref01.png "Finestra di dialogo Nuovo File")](images/ref01.png#lightbox)
    
2. Progettare il layout delle scene del nuovo Storyboard normalmente verrebbe e salvare le modifiche: 
    
    [![](images/ref02.png "Il layout della nuova scena")](images/ref02.png#lightbox)
    
3. Aprire lo Storyboard che si sta per essere aggiunto il riferimento nella finestra di progettazione iOS.

4. Trascinare un **Storyboard riferimento** dal **della casella degli strumenti** nell'area di progettazione: 
    
    [![](images/ref03.png "Un riferimento di Storyboard")](images/ref03.png#lightbox)
    
5. Nel **Widget** scheda della finestra il **Esplora proprietà**, selezionare il nome del **Storyboard** appena creato: 

    [![](images/ref04.png "La scheda Widget")](images/ref04.png#lightbox)
    
6. Controllo fare clic su un Widget dell'interfaccia utente (ad esempio un pulsante) in una scena esistente e creare un nuovo Segue per il **riferimento Storyboard** appena creato: 

    [![](images/ref05.png "Creazione di un segue")](images/ref05.png#lightbox) 
    
7. Dal menu di scelta rapida selezionare **Mostra** per completare la Segue: 

    [![](images/ref06.png "Selezionare Mostra per completare la Segue.")](images/ref06.png#lightbox) 
    
8. Salvare le modifiche allo Storyboard.

Verrà visualizzato quando si esegue l'app e l'utente fa clic sull'elemento dell'interfaccia utente che la Segue è stato creato da, il Controller di visualizzazione iniziale dallo Storyboard esterno specificato nel riferimento Storyboard.

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>Riferimento a una scena specifica in uno Storyboard esterno

Per aggiungere un riferimento a una scena specifica uno Storyboard esterno e non il Controller di visualizzazione iniziale, eseguire le operazioni seguenti:

1. Nel **Esplora**, fare doppio clic sullo Storyboard esterno per aprirlo e modificarlo.

2. Aggiungere una nuova scena e progettare il layout in modo analogo: 

    [![](images/ref07.png "Il nuovo layout di scena")](images/ref07.png#lightbox)
    
3. Nel **Widget** scheda della finestra il **Esplora proprietà**, immettere un **ID Storyboard** per Controller di visualizzazione della scena nuovo: 

    [![](images/ref08.png "Immettere un ID di uno Storyboard per il nuovo Controller di visualizzazione delle scene")](images/ref08.png#lightbox)
    
3. Aprire lo Storyboard che si sta per essere aggiunto il riferimento nella finestra di progettazione iOS.

4. Trascinare un **Storyboard riferimento** dal **della casella degli strumenti** nell'area di progettazione: 

    [![](images/ref03.png "Un riferimento di Storyboard")](images/ref03.png#lightbox)
    
5. Nel **Widget** scheda della finestra il **Esplora proprietà**, selezionare il nome del **Storyboard** e **ID riferimento** (Storyboard ID) del Scena appena creato: 

    [![](images/ref09.png "La scheda Widget ")](images/ref09.png#lightbox)
    
6. Controllo fare clic su un Widget dell'interfaccia utente (ad esempio un pulsante) in una scena esistente e creare un nuovo Segue per il **riferimento Storyboard** appena creato: 

    [![](images/ref10.png "Creazione di un segue")](images/ref10.png#lightbox) 
    
7. Dal menu di scelta rapida selezionare **Mostra** per completare la Segue: 

    [![](images/ref06.png "Selezionare Mostra per completare la Segue.")](images/ref06.png#lightbox) 
    
8. Salvare le modifiche allo Storyboard.

Quando l'app viene eseguita e l'utente fa clic sull'elemento dell'interfaccia utente che la Segue è stato creato da, la scena con il determinato **ID Storyboard** dallo Storyboard esterno specificato nel riferimento Storyboard verrà visualizzato.

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>Riferimento a una scena specifica nello stesso Storyboard

Per aggiungere un riferimento a una scena specifica lo stesso Storyboard, eseguire le operazioni seguenti:

1. Nel **Esplora**, fare doppio clic sullo Storyboard per aprirlo e modificarlo.

2. Aggiungere una nuova scena e progettare il layout in modo analogo: 

    [![](images/ref11.png "Il nuovo layout di scena")](images/ref11.png#lightbox)

3. Nel **Widget** scheda della finestra il **Esplora proprietà**, immettere un **ID Storyboard** per Controller di visualizzazione della scena nuovo: 

    [![](images/ref12.png "La scheda Widget")](images/ref12.png#lightbox)
    
3. Trascinare un **Storyboard riferimento** dal **della casella degli strumenti** nell'area di progettazione: 

    [![](images/ref03.png "Un riferimento di Storyboard")](images/ref03.png#lightbox)
    
5. Nel **Widget** scheda della finestra il **Esplora proprietà**, selezionare **ID riferimento** (Storyboard ID) della scena appena creato: 

    [![](images/ref13.png "La scheda Widget")](images/ref13.png#lightbox)
    
6. Controllo fare clic su un Widget dell'interfaccia utente (ad esempio un pulsante) in una scena esistente e creare un nuovo Segue per il **riferimento Storyboard** appena creato: 

    [![](images/ref14.png "Creazione di un segue")](images/ref14.png#lightbox) 
    
7. Dal menu di scelta rapida selezionare **Mostra** per completare la Segue: 

    [![](images/ref06.png "Selezionare Mostra per completare la Segue.")](images/ref06.png#lightbox) 
    
8. Salvare le modifiche allo Storyboard.

Quando l'app viene eseguita e l'utente fa clic sull'elemento dell'interfaccia utente che la Segue è stato creato da, la scena con il determinato **ID Storyboard** nello stesso Storyboard specificato nel riferimento Storyboard verrà visualizzato.

## <a name="summary"></a>Riepilogo

In questo articolo viene introdotto il concetto di storyboard e come può essere utile per lo sviluppo di applicazioni iOS. Viene descritto scene, visualizzazione controller, visualizzazioni e visualizzare gerarchie e le modalità di collegamento automaticamente insieme a diversi tipi di Segues.  Vengono inoltre descritti i controller di visualizzazione istanze manualmente da un storyboard e creazione Segues condizionale.



## <a name="related-links"></a>Collegamenti correlati

- [Manuale dello Storyboard (esempio)](https://developer.xamarin.com/samples/ManualStoryboard/)
- [Introduzione a progettazione iOS](~/ios/user-interface/designer/introduction.md)
- [Conversione di storyboard](http://developer.apple.com/library/ios/#releasenotes/Miscellaneous/RN-AdoptingStoryboards/)
- [Riferimento alla classe UIStoryboard](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIStoryboard_Class/Reference/Reference.html)
