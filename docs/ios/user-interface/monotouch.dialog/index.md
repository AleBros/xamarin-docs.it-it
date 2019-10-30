---
title: Introduzione a MonoTouch. dialog per Novell. iOS
description: In questo documento viene descritta la finestra di dialogo MonoTouch. Dialog (MT. D), un Framework per lo sviluppo rapido e dichiarativo dell'interfaccia utente con Novell. iOS. Viene illustrato come utilizzare le API MonoTouch. dialog per creare un'interfaccia nel codice o JSON e utilizzare funzionalità come pull-to-refresh, ricerca, caricamento di immagini di sfondo e altro ancora.
ms.prod: xamarin
ms.assetid: 52A35B24-C23B-8461-A8FF-5928A2128FB0
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: 68f8349fd6c8f90b36fb5edb2838dfec352a5800
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73002577"
---
# <a name="introduction-to-monotouchdialog-for-xamarinios"></a>Introduzione a MonoTouch. dialog per Novell. iOS

MonoTouch. Dialog, definito MT. D per brevità, è un toolkit di sviluppo rapido per l'interfaccia utente che consente agli sviluppatori di creare schermate delle applicazioni e spostamento usando le informazioni, anziché la noia della creazione di controller di visualizzazione, tabelle e così via. Di conseguenza, fornisce una semplificazione significativa dello sviluppo dell'interfaccia utente e della riduzione del codice. Si consideri, ad esempio, la schermata seguente:

 [![](images/image1.png "For example, consider this screenshot")](images/image1.png#lightbox)

Per definire l'intera schermata è stato usato il codice seguente:

```csharp
public enum Category
{
    Travel,
    Lodging,
    Books
}
        
public class Expense
{
    [Section("Expense Entry")]

    [Entry("Enter expense name")]
    public string Name;
    [Section("Expense Details")]
  
    [Caption("Description")]
    [Entry]
    public string Details;
        
    [Checkbox]
    public bool IsApproved = true;
    [Caption("Category")]
    public Category ExpenseCategory;
}
```

Quando si lavora con le tabelle in iOS, spesso è presente una tonnellata di codice ripetitivo.
Ad esempio, ogni volta che è necessaria una tabella, per popolare tale tabella è necessaria un'origine dati. In un'applicazione che dispone di due schermate basate su tabelle connesse tramite un controller di spostamento, ogni schermata condivide una grande quantità di codice.

Mt. D semplifica l'incapsulamento di tutto il codice in un'API generica per la creazione di tabelle. Fornisce quindi un'astrazione su tale API che consente la sintassi di associazione di un oggetto dichiarativa che lo rende ancora più semplice. Di conseguenza, in MT sono disponibili due API. D

- **API elementi di basso livello** : l' *API elementi* è basata sulla creazione di un albero gerarchico di elementi che rappresentano le schermate e i relativi componenti. L'API degli elementi offre agli sviluppatori la massima flessibilità e il controllo per la creazione di interfacce utente. Inoltre, l'API degli elementi ha un supporto avanzato per la definizione dichiarativa tramite JSON, che consente la dichiarazione incredibilmente veloce, nonché la generazione dinamica dell'interfaccia utente da un server. 
- **API di reflection di alto livello** , nota anche come*API* di binding, in cui le classi sono annotate con hint dell'interfaccia utente e quindi mt. D crea automaticamente schermate basate sugli oggetti e fornisce un'associazione tra gli elementi visualizzati (e facoltativamente modificati) sullo schermo e il supporto dell'oggetto sottostante. Nell'esempio precedente è stato illustrato l'uso dell'API di Reflection. Questa API non fornisce il controllo granulare svolto dall'API degli elementi, ma riduce ulteriormente la complessità grazie alla creazione automatica della gerarchia di elementi in base agli attributi della classe. 

Mt. D è dotato di un ampio set di elementi dell'interfaccia utente incorporati per la creazione dello schermo, ma riconosce anche la necessità di elementi personalizzati e layout avanzati dello schermo. Di conseguenza, l'estendibilità è un primo livello in primo piano nell'API. Gli sviluppatori possono estendere gli elementi esistenti o crearne di nuovi e quindi integrarsi facilmente.

Inoltre, MT. D include una serie di funzionalità comuni di app UX per iOS, come il supporto per il pull all'aggiornamento, il caricamento asincrono delle immagini e il supporto per la ricerca.

Questo articolo illustra in maniera completa l'uso di MT. D, tra cui:

- **Mt. Componenti D** : questa operazione si concentra sulla comprensione delle classi che costituiscono mt. D per consentire un rapido aumento della velocità. 
- **Riferimento agli elementi** : un elenco completo degli elementi predefiniti di mt. D. 
- **Utilizzo avanzato** : vengono illustrate le funzionalità avanzate, ad esempio pull-to-refresh, ricerca, caricamento di immagini in background, utilizzo di LINQ per compilare gerarchie di elementi e creazione di elementi, celle e controller personalizzati per l'utilizzo con Mt. D. 

## <a name="setting-up-mtd"></a>Configurazione di MT. D

Mt. D viene distribuito con Novell. iOS. Per usarlo, fare clic con il pulsante destro del mouse sul nodo **riferimenti** di un progetto Novell. iOS in Visual Studio 2017 o Visual Studio per Mac e aggiungere un riferimento all'assembly **MonoTouch. Dialog-1** . Aggiungere quindi `using MonoTouch.Dialog` istruzioni nel codice sorgente in base alle esigenze.

## <a name="understanding-the-pieces-of-mtd"></a>Informazioni sulle parti di MT. D

Anche quando si usa l'API Reflection, MT. D crea una gerarchia di elementi sotto la cappa, esattamente come se fosse stata creata tramite l'API Elements direttamente. Inoltre, il supporto JSON indicato nella sezione precedente crea anche elementi. Per questo motivo, è importante avere una conoscenza di base delle parti costitutive di MT. D.

Mt. D compila le schermate usando le quattro parti seguenti:

- **DialogViewController**
- **RootElement**
- **Section**
- **Elemento**

### <a name="dialogviewcontroller"></a>DialogViewController

Un *DialogViewController*o *DVC* in breve eredita da `UITableViewController` e pertanto rappresenta una schermata con una tabella. DVCs può essere inserito in un controller di spostamento proprio come un normale UITableViewController.

### <a name="rootelement"></a>RootElement

Un *elemento RootElement* è il contenitore di livello superiore per gli elementi che vengono inseriti in un DVC. Contiene sezioni, che possono quindi contenere elementi. Non viene eseguito il rendering di RootElements. ma sono semplicemente contenitori per gli elementi di cui viene effettivamente eseguito il rendering. Un elemento rootElement viene assegnato a un DVC e quindi il DVC esegue il rendering dei relativi elementi figlio.

### <a name="section"></a>Sezione

Una sezione è un gruppo di celle in una tabella. Come per una normale sezione della tabella, può facoltativamente avere un'intestazione e un piè di pagina che possono essere testo o persino visualizzazioni personalizzate, come nello screenshot seguente:

 [![](images/image2.png "As with a normal table section, it can optionally have a header and footer that can either be text, or even custom views, as in this screenshot")](images/image2.png#lightbox)

### <a name="element"></a>Elemento

Un elemento rappresenta una cella effettiva nella tabella. Mt. D viene compresso con una vasta gamma di elementi che rappresentano tipi di dati o input diversi. Ad esempio, gli screenshot seguenti illustrano alcuni degli elementi disponibili:

 [![](images/image3.png "For example, this screenshots illustrate a few of the available elements")](images/image3.png#lightbox)

## <a name="more-on-sections-and-rootelements"></a>Altre informazioni sulle sezioni e RootElements

Si esamineranno ora RootElements e le sezioni in modo più dettagliato.

### <a name="rootelements"></a>RootElements

Per avviare il processo MonoTouch. Dialog è necessario almeno un elemento RootElement.

Se un elemento rootElement viene inizializzato con un valore di sezione/elemento, questo valore viene utilizzato per individuare un elemento figlio che fornirà un riepilogo della configurazione, di cui viene eseguito il rendering sul lato destro dello schermo. Ad esempio, la schermata seguente mostra una tabella a sinistra con una cella contenente il titolo della schermata dei dettagli a destra, "dessert", insieme al valore del deserto selezionato.

 [![](images/image4.png "Questa schermata mostra una tabella a sinistra con una cella che contiene il titolo della schermata dei dettagli a destra, dessert, insieme al valore del deserto selezionato")](images/image4.png#lightbox)[![](images/image5.png "Questa schermata seguente mostra una tabella a sinistra con una cella che contiene il titolo della schermata dei dettagli a destra, dessert, insieme al valore del deserto selezionato")](images/image5.png#lightbox)

Gli elementi radice possono anche essere usati all'interno delle sezioni per attivare il caricamento di una nuova pagina di configurazione nidificata, come illustrato in precedenza. Se utilizzata in questa modalità, la didascalia fornita viene utilizzata durante il rendering all'interno di una sezione e viene utilizzata anche come titolo per la pagina della pagina. Esempio:

```csharp
var root = new RootElement ("Meals") {
    new Section ("Dinner") {
        new RootElement ("Dessert", new RadioGroup ("dessert", 2)) {
            new Section () {
                new RadioElement ("Ice Cream", "dessert"),
                new RadioElement ("Milkshake", "dessert"),
                new RadioElement ("Chocolate Cake", "dessert")
            }
        }
    }
};
```

Nell'esempio precedente, quando l'utente tocca "dessert", MonoTouch. la finestra di dialogo creerà una nuova pagina e vi passerà con la radice "dessert" e un gruppo di radio con tre valori.

In questo particolare esempio, il gruppo di radio selezionerà "Chocolate Cake" nella sezione "dessert" perché è stato passato il valore "2" al radiogruppo. Questo significa selezionare il terzo elemento nell'elenco (indice zero).

La chiamata al metodo Add o l' C# utilizzo della sintassi a 4 inizializzatori aggiunge sezioni.
I metodi Insert vengono forniti per inserire sezioni con un'animazione.

Se si crea l'elemento Radiceelement con un'istanza di gruppo (anziché un radiogruppo), il valore di riepilogo dell'elemento RootElement quando viene visualizzato in una sezione sarà il conteggio cumulativo di tutti i BooleanElements e CheckboxElements che hanno la stessa chiave del valore Group. Key.

### <a name="sections"></a>Sezioni

Le sezioni vengono utilizzate per raggruppare gli elementi dello schermo e sono gli unici elementi figlio diretti validi dell'elemento RootElement. Le sezioni possono contenere qualsiasi elemento standard, incluso il nuovo RootElements.

RootElements incorporato in una sezione vengono usati per passare a un nuovo livello più profondo.

Le sezioni possono avere intestazioni e piè di pagina come stringhe o come UIViews.
In genere si useranno solo le stringhe, ma per creare interfacce utente personalizzate è possibile usare qualsiasi UIView come intestazione o piè di pagina. È possibile usare una stringa per crearli come segue:

```csharp
var section = new Section ("Header", "Footer");
```

Per usare le visualizzazioni, è sufficiente passare le visualizzazioni al costruttore:

```csharp
var header = new UIImageView (Image.FromFile ("sample.png"));
var section = new Section (header);
```

### <a name="getting-notified"></a>Ricezione di notifiche

#### <a name="handling-nsaction"></a>Gestione di NSAction

Mt. D superficie di un `NSAction` come delegato per la gestione dei callback.
Si immagini, ad esempio, di voler gestire un evento Touch per una cella della tabella creata da MT. D. Quando si crea un elemento con MT. D, è sufficiente fornire una funzione di callback, come illustrato di seguito:

```csharp
new Section () {
    new StringElement ("Demo Callback", delegate { Console.WriteLine ("Handled"); })
}
```

#### <a name="retrieving-element-value"></a>Recupero del valore dell'elemento

In combinazione con la proprietà `Element.Value`, il callback può recuperare il valore impostato in altri elementi. Si consideri il codice di esempio seguente:

```csharp
var element = new EntryElement (task.Name, "Enter task description", task.Description);
                
var taskElement = new RootElement (task.Name) {
    new Section () { element },
    new Section () { new DateElement ("Due Date", task.DueDate) },
    new Section ("Demo Retrieving Element Value") {
        new StringElement ("Output Task Description", delegate { Console.WriteLine (element.Value); })
    }
};
```

Questo codice crea un'interfaccia utente come illustrato di seguito. Per una procedura dettagliata completa di questo esempio, vedere l'esercitazione sulla [procedura dettagliata per l'API elementi](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md) .

 [![](images/image6.png "Combined with the Element.Value property, the callback can retrieve the value set in other elements")](images/image6.png#lightbox)

Quando l'utente preme la cella della tabella inferiore, il codice nella funzione anonima viene eseguito, scrivendo il valore dall'istanza di `element` al riquadro di **output dell'applicazione** nella Visual Studio per Mac.

## <a name="built-in-elements"></a>Elementi predefiniti

Mt. D è costituito da una serie di elementi di celle di tabella predefiniti noti come elementi.
Questi elementi vengono usati per visualizzare una varietà di tipi diversi nelle celle della tabella, ad esempio stringhe, float, date e persino immagini, per citarne solo alcuni. Ogni elemento si occupa della visualizzazione del tipo di dati in modo appropriato. Ad esempio, un elemento booleano visualizzerà un'opzione per attivare o disattivare il relativo valore. Analogamente, un elemento float visualizzerà un dispositivo di scorrimento per modificare il valore float.

Sono disponibili anche elementi più complessi per supportare tipi di dati più ricchi, ad esempio immagini e HTML. Ad esempio, un elemento HTML, che apre un UIWebView per caricare una pagina Web quando viene selezionata, Visualizza una didascalia nella cella della tabella.

### <a name="working-with-element-values"></a>Utilizzo dei valori degli elementi

Gli elementi utilizzati per acquisire l'input dell'utente espongono una proprietà `Value` pubblica che include il valore corrente dell'elemento in qualsiasi momento. Viene aggiornata automaticamente quando l'utente usa l'applicazione.

Questo è il comportamento per tutti gli elementi che fanno parte di MonoTouch. Dialog, ma non è necessario per gli elementi creati dall'utente.

### <a name="string-element"></a>Elemento String

Un `StringElement` Mostra una didascalia sul lato sinistro di una cella della tabella e il valore di stringa sul lato destro della cella.

 [![](images/image7.png "A StringElement shows a caption on the left side of a table cell and the string value on the right side of the cell")](images/image7.png#lightbox)

Per usare un `StringElement` come pulsante, fornire un delegato.

```csharp
new StringElement ("Click me", () => { 
    new UIAlertView("Tapped", "String Element Tapped", null, "ok", null).Show();
});
```

 [![](images/image8.png "To use a StringElement as a button, provide a delegate")](images/image8.png#lightbox)

### <a name="styled-string-element"></a>Elemento String con stile

Un `StyledStringElement` consente di presentare le stringhe utilizzando gli stili di cella della tabella predefiniti o la formattazione personalizzata.

 [![](images/image9.png "A StyledStringElement allows strings to be presented using either built-in table cell styles or with custom formatting")](images/image9.png#lightbox)

La classe `StyledStringElement` deriva da `StringElement`, ma consente agli sviluppatori di personalizzare alcune proprietà, ad esempio il tipo di carattere, il colore del testo, il colore della cella di sfondo, la modalità di interruzioni di riga, il numero di righe da visualizzare e se deve essere visualizzato un accessorio.

### <a name="multiline-element"></a>Elemento su più righe

 [![](images/image10.png "Multiline Element")](images/image10.png#lightbox)

### <a name="entry-element"></a>Elemento entry

Il `EntryElement`, come implica il nome, viene usato per ottenere l'input dell'utente. Supporta stringhe o password regolari, dove i caratteri sono nascosti.

 [![](images/image11.png "The EntryElement is used to get user input")](images/image11.png#lightbox)

Viene inizializzata con tre valori:

- Didascalia per la voce che verrà visualizzata all'utente.
- Testo segnaposto (questo è il testo in grigio che fornisce un suggerimento all'utente). 
- Valore del testo.

Il segnaposto e il valore possono essere null. Tuttavia, la didascalia è obbligatoria.

In qualsiasi momento, l'accesso alla proprietà Value può recuperare il valore della `EntryElement`.

Inoltre, la proprietà `KeyboardType` può essere impostata in fase di creazione sullo stile del tipo di tastiera desiderato per l'immissione di dati. Questa operazione può essere usata per configurare la tastiera usando i valori di `UIKeyboardType` come indicato di seguito:

- Numerico
- Phone
- URL
- Email

### <a name="boolean-element"></a>Elemento Boolean

 [![](images/image12.png "Boolean Element")](images/image12.png#lightbox)

### <a name="checkbox-element"></a>CheckBox (elemento)

 [![](images/image13.png "Checkbox Element")](images/image13.png#lightbox)

### <a name="radio-element"></a>Elemento radio

Un `RadioElement` richiede la specifica di un `RadioGroup` nel `RootElement`.

```csharp
mtRoot = new RootElement ("Demos", new RadioGroup("MyGroup", 0));
```

 [![](images/image14.png "A RadioElement requires a RadioGroup to be specified in the RootElement")](images/image14.png#lightbox)

 `RootElements` vengono usati anche per coordinare gli elementi radio. I membri del `RadioElement` possono estendersi su più sezioni, ad esempio per implementare un elemento simile al selettore del tono della suoneria e i toni dell'anello personalizzato separati dalle suonerie del sistema. Nella visualizzazione riepilogo verrà visualizzato l'elemento radio attualmente selezionato. Per usarlo, creare il `RootElement` con il costruttore del gruppo, come indicato di seguito:

```csharp
var root = new RootElement ("Meals", new RadioGroup ("myGroup", 0));
```

Il nome del gruppo in `RadioGroup` viene usato per visualizzare il valore selezionato nella pagina contenitore (se presente) e il valore, che è zero in questo caso, è l'indice del primo elemento selezionato.

### <a name="badge-element"></a>Badge (elemento)

 [![](images/image15.png "Badge Element")](images/image15.png#lightbox)

### <a name="float-element"></a>Float (elemento)

 [![](images/image16.png "Float Element")](images/image16.png#lightbox)

### <a name="activity-element"></a>Activity-elemento

 [![](images/image17.png "Activity Element")](images/image17.png#lightbox)

### <a name="date-element"></a>Elemento date

 ![](images/image18.png "Date Element")

Quando la cella corrispondente a Dateelement è selezionata, viene visualizzato un selettore di data, come illustrato di seguito:

 [![](images/image19.png "When the cell corresponding to the DateElement is selected, a date picker is presented as shown")](images/image19.png#lightbox)

### <a name="time-element"></a>Time-elemento

 [![](images/image20.png "Time Element")](images/image20.png#lightbox)

Quando la cella corrispondente a Timeelement è selezionata, viene visualizzato un selettore di ora, come illustrato di seguito:

 [![](images/image21.png "When the cell corresponding to the TimeElement is selected, a time picker is presented as shown")](images/image21.png#lightbox)

### <a name="datetime-element"></a>DateTime-elemento

 [![](images/image22.png "DateTime Element")](images/image22.png#lightbox)

Quando la cella corrispondente a DateTimeelement è selezionata, viene visualizzata una selezione DateTime come illustrato di seguito:

 [![](images/image23.png "When the cell corresponding to the DateTimeElement is selected, a datetime picker is presented as shown")](images/image23.png#lightbox)

### <a name="html-element"></a>Elemento HTML

 [![](images/image24.png "HTML Element")](images/image24.png#lightbox)

Nella `HTMLElement` viene visualizzato il valore della relativa proprietà `Caption` nella cella della tabella. Se selezionato, il `Url` assegnato all'elemento viene caricato in un controllo `UIWebView`, come illustrato di seguito:

 [![](images/image25.png "Whe selected, the Url assigned to the element is loaded in a UIWebView control as shown below")](images/image25.png#lightbox)

### <a name="message-element"></a>Message-elemento

 [![](images/image26.png "Message Element")](images/image26.png#lightbox)

### <a name="load-more-element"></a>Carica altro elemento

Usare questo elemento per consentire agli utenti di caricare più elementi nell'elenco. È possibile personalizzare le didascalie normali e di caricamento, nonché il colore del tipo di carattere e del testo.
L'indicatore `UIActivity` avvia l'animazione e la didascalia di caricamento viene visualizzata quando un utente tocca la cella e quindi viene eseguita la `NSAction` passata al costruttore. Una volta terminato il codice nel `NSAction`, l'indicatore `UIActivity` interrompe l'animazione e la didascalia normale viene nuovamente visualizzata.

### <a name="uiview-element"></a>Elemento UIView

Inoltre, è possibile visualizzare qualsiasi `UIView` personalizzata utilizzando l'`UIViewElement`.

### <a name="owner-drawn-element"></a>Elemento creato dal proprietario

Questo elemento deve essere sottoclassato perché è una classe astratta. È necessario eseguire l'override del metodo `Height(RectangleF bounds)` in cui è necessario restituire l'altezza dell'elemento, nonché `Draw(RectangleF bounds, CGContext context, UIView view)` in cui è necessario eseguire tutto il disegno personalizzato all'interno dei limiti specificati, utilizzando il contesto e i parametri di visualizzazione. Questo elemento esegue un elevato numero di sottoclassi di un `UIView`e lo inserisce nella cella da restituire, lasciando che sia necessario implementare solo due override semplici. È possibile visualizzare un'implementazione di esempio migliore nell'app di esempio nel file di `DemoOwnerDrawnElement.cs`.

Di seguito è riportato un esempio molto semplice di implementazione della classe:

```csharp
public class SampleOwnerDrawnElement : OwnerDrawnElement
{
    public SampleOwnerDrawnElement (string text) : base(UITableViewCellStyle.Default, "sampleOwnerDrawnElement")
    {
        this.Text = text;
    }

    public string Text { get; set; }

    public override void Draw (RectangleF bounds, CGContext context, UIView view)
    {
        UIColor.White.SetFill();
        context.FillRect(bounds);

        UIColor.Black.SetColor();   
        view.DrawString(this.Text, new RectangleF(10, 15, bounds.Width - 20, bounds.Height - 30), UIFont.BoldSystemFontOfSize(14.0f), UILineBreakMode.TailTruncation);
    }

    public override float Height (RectangleF bounds)
    {
        return 44.0f;
    }
}
```

### <a name="json-element"></a>Elemento JSON

Il `JsonElement` è una sottoclasse di `RootElement` che estende un `RootElement` per poter caricare il contenuto di un elemento figlio annidato da un URL locale o remoto.

Il `JsonElement` è un `RootElement` di cui è possibile creare un'istanza in due formati. Una versione crea una `RootElement` che caricherà il contenuto su richiesta. Questi vengono creati usando i costruttori di `JsonElement` che accettano un argomento aggiuntivo alla fine, l'URL da cui caricare il contenuto:

```csharp
var je = new JsonElement ("Dynamic Data", "https://tirania.org/tmp/demo.json");
```

L'altro modulo crea i dati da un file locale o da un `System.Json.JsonObject` esistente che è già stato analizzato:

```csharp
var je = JsonElement.FromFile ("json.sample");
using (var reader = File.OpenRead ("json.sample"))
    return JsonElement.FromJson (JsonObject.Load (reader) as JsonObject, arg);
```

Per altre informazioni sull'uso di JSON con MT. D, vedere l'esercitazione [dettagliata sull'elemento JSON](https://docs.microsoft.com/xamarin/ios/user-interface/monotouch.dialog/json-element-walkthrough) .

## <a name="other-features"></a>Altre funzionalità

### <a name="pull-to-refresh-support"></a>Supporto pull-to-refresh

 Il *pull a* *Refresh* è un effetto visivo originariamente trovato nell'app *Tweetie2* , che è diventato un effetto comune tra molte applicazioni.

Per aggiungere il supporto automatico di pull a aggiornamento alle finestre di dialogo, è sufficiente eseguire due operazioni: collegare un gestore eventi per ricevere una notifica quando l'utente effettua il pull dei dati e invia una notifica al `DialogViewController` quando i dati sono stati caricati per tornare allo stato predefinito.

L'associazione di una notifica è semplice; è sufficiente connettersi all'evento `RefreshRequested` nel `DialogViewController`, come indicato di seguito:

```csharp
dvc.RefreshRequested += OnUserRequestedRefresh;
```

Quindi, nel metodo `OnUserRequestedRefresh`, è necessario accodare il caricamento dei dati, richiedere alcuni dati dalla rete o ruotare un thread per calcolare i dati. Una volta caricati i dati, è necessario notificare all'`DialogViewController` che i nuovi dati si trovino e ripristinare lo stato predefinito della visualizzazione, chiamando `ReloadComplete`:

```csharp
dvc.ReloadComplete ();
```

### <a name="search-support"></a>Supporto per la ricerca

Per supportare la ricerca, impostare la proprietà `EnableSearch` nel `DialogViewController`. È anche possibile impostare la proprietà `SearchPlaceholder` da usare come testo della filigrana nella barra di ricerca.

La ricerca modificherà il contenuto della visualizzazione come tipi di utente. Esegue la ricerca nei campi visibili e li visualizza all'utente. Il `DialogViewController` espone tre metodi per avviare, terminare o attivare una nuova operazione di filtro a livello di codice sui risultati. Questi metodi sono elencati di seguito:

- `StartSearch`
- `FinishSearch`
- `PerformFilter`

Il sistema è estensibile, pertanto è possibile modificare questo comportamento se lo si desidera.

### <a name="background-image-loading"></a>Caricamento dell'immagine di sfondo

MonoTouch. Dialog incorpora il caricatore di immagini dell'applicazione [TweetStation](https://github.com/migueldeicaza/TweetStation) . Questo caricatore di immagini può essere usato per caricare immagini in background, supporta la memorizzazione nella cache e può inviare una notifica al codice quando l'immagine è stata caricata.

Verrà inoltre limitato il numero di connessioni di rete in uscita.

Il caricatore di immagini viene implementato nella classe `ImageLoader`, è sufficiente chiamare il metodo `DefaultRequestImage`, è necessario specificare l'URI per l'immagine da caricare, oltre a un'istanza dell'interfaccia `IImageUpdated` che verrà richiamata quando l'immagine è stato caricato.

Il codice seguente, ad esempio, carica un'immagine da un URL in un `BadgeElement`:

```csharp
string uriString = "http://some-server.com/some image url";

var rootElement = new RootElement("Image Loader") {
    new Section() {
        new BadgeElement( ImageLoader.DefaultRequestImage( new Uri(uriString), this), "Xamarin")
    }
};
```

La classe ImageLoader espone un metodo Purge che è possibile chiamare quando si desidera rilasciare tutte le immagini attualmente memorizzate nella cache. Il codice corrente include una cache per le immagini 50. Se si vuole usare una dimensione della cache diversa (ad esempio, se si prevede che le immagini siano troppo grandi, le immagini di 50 sarebbero troppo elevate), è possibile creare solo istanze di ImageLoader e passare il numero di immagini che si desidera memorizzare nella cache.

## <a name="using-linq-to-create-element-hierarchy"></a>Utilizzo di LINQ per creare la gerarchia degli elementi

Tramite l'utilizzo intelligente della sintassi di C#inizializzazione di LINQ e, è possibile utilizzare LINQ per creare una gerarchia di elementi. Ad esempio, il codice seguente crea una schermata da alcune matrici di stringhe e gestisce la selezione delle celle tramite una funzione anonima passata in ogni `StringElement`:

```csharp
var rootElement = new RootElement ("LINQ root element") {
    from x in new string [] { "one", "two", "three" }
    select new Section (x) {
        from y in "Hello:World".Split (':')
        select (Element) new StringElement (y, delegate { Debug.WriteLine("cell tapped"); })
    }
};
```

Questo può essere facilmente combinato con un archivio dati o dati XML da un database per creare applicazioni complesse quasi interamente dai dati.

## <a name="extending-mtd"></a>Estensione di MT. D

### <a name="creating-custom-elements"></a>Creazione di elementi personalizzati

È possibile creare un elemento personalizzato ereditando da un elemento esistente o derivando dall'elemento della classe radice.

Per creare un elemento personalizzato, è necessario eseguire l'override dei metodi seguenti:

```csharp
// To release any heavy resources that you might have
void Dispose (bool disposing);

// To retrieve the UITableViewCell for your element
// you would need to prepare the cell to be reused, in the
// same way that UITableView expects reusable cells to work
UITableViewCell GetCell (UITableView tv);

// To retrieve a "summary" that can be used with
// a root element to render a summary one level up.  
string Summary ();

// To detect when the user has tapped on the cell
void Selected (DialogViewController dvc, UITableView tableView, NSIndexPath path);

// If you support search, to probe if the cell matches the user input
bool Matches (string text);
```

Se l'elemento può avere una dimensione variabile, è necessario implementare l'interfaccia `IElementSizing`, che contiene un metodo:

```csharp
// Returns the height for the cell at indexPath.Section, indexPath.Row
float GetHeight (UITableView tableView, NSIndexPath indexPath);
```

Se si intende implementare il metodo di `GetCell` chiamando `base.GetCell(tv)` e personalizzando la cella restituita, è necessario eseguire l'override anche della proprietà `CellKey` per restituire una chiave che sarà univoca per l'elemento, come indicato di seguito:

```csharp
static NSString MyKey = new NSString ("MyKey");
protected override NSString CellKey {
    get {
        return MyKey;
    }
}
```

Questa operazione è valida per la maggior parte degli elementi, ma non per la `StringElement` e `StyledStringElement` perché usano il proprio set di chiavi per diversi scenari di rendering. Sarebbe necessario replicare il codice in tali classi.

### <a name="dialogviewcontrollers-dvcs"></a>DialogViewControllers (DVCs)

Sia la reflection che l'API degli elementi utilizzano lo stesso `DialogViewController`. In alcuni casi è possibile personalizzare l'aspetto della visualizzazione o usare alcune funzionalità del `UITableViewController` che vanno oltre la creazione di base delle interfacce utente.

Il `DialogViewController` è semplicemente una sottoclasse della `UITableViewController` ed è possibile personalizzarla nello stesso modo in cui si personalizza un `UITableViewController`.

Se ad esempio si desidera modificare lo stile di elenco in modo che sia `Grouped` o `Plain`, è possibile impostare questo valore modificando la proprietà quando si crea il controller, come segue:

```csharp
var myController = new DialogViewController (root, true) {
    Style = UITableViewStyle.Grouped;
}
```

Per le personalizzazioni più avanzate del `DialogViewController`, ad esempio l'impostazione dello sfondo, è necessario sottoclassare la classe ed eseguire l'override dei metodi appropriati, come illustrato nell'esempio seguente:

```csharp
class SpiffyDialogViewController : DialogViewController {
    UIImage image;

    public SpiffyDialogViewController (RootElement root, bool pushing, UIImage image) 
        : base (root, pushing) 
    {
        this.image = image;
    }

    public override LoadView ()
    {
        base.LoadView ();
        var color = UIColor.FromPatternImage(image);
        TableView.BackgroundColor = UIColor.Clear;
        ParentViewController.View.BackgroundColor = color;
    }
}
```

Un altro punto di personalizzazione è il seguente metodo virtuale nella `DialogViewController`:

```csharp
public override Source CreateSizingSource (bool unevenRows)
```

Questo metodo deve restituire una sottoclasse di `DialogViewController.Source` per i casi in cui le celle sono uniformemente dimensionate o una sottoclasse di `DialogViewController.SizingSource` se le celle non sono uniformi.

È possibile usare questa sostituzione per acquisire uno dei metodi `UITableViewSource`. Ad esempio, [TweetStation](https://github.com/migueldeicaza/TweetStation) usa questo per tenere traccia del momento in cui l'utente ha eseguito lo scorrimento alla parte superiore e aggiorna di conseguenza il numero di Tweet non letti.

## <a name="validation"></a>Convalida

Gli elementi non forniscono la convalida in quanto i modelli particolarmente adatti per le pagine Web e le applicazioni desktop non eseguono il mapping direttamente al modello di interazione iPhone.

Se si desidera eseguire la convalida dei dati, è necessario eseguire questa operazione quando l'utente attiva un'azione con i dati immessi. Ad esempio un pulsante **fatto** o **Avanti** sulla barra degli strumenti superiore o alcuni `StringElement` utilizzati come pulsante per passare alla fase successiva.

Questo è il punto in cui si esegue la convalida dell'input di base e, forse, una convalida più complessa, ad esempio la verifica della validità di una combinazione di utente/password con un server.

La modalità di notifica all'utente di un errore è specifica dell'applicazione. È possibile visualizzare un `UIAlertView` o un suggerimento.

## <a name="summary"></a>Riepilogo

Questo articolo ha trattato numerose informazioni su MonoTouch. dialog. Sono state illustrate le nozioni di base della MT. D funziona e copre i vari componenti che comprendono MT. D. È stata inoltre mostrata la vasta gamma di elementi e le personalizzazioni di tabella supportate da MT. D e illustrato come MT. D può essere esteso con gli elementi personalizzati. È stato inoltre descritto il supporto JSON in MT. D che consente la creazione dinamica di elementi da JSON.

## <a name="related-links"></a>Collegamenti correlati

- [Procedura dettagliata: Creazione di un'applicazione mediante l'API Elements](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Procedura dettagliata: Creazione di un'applicazione mediante l'API Reflection](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Procedura dettagliata: Uso di un elemento JSON per creare un'interfaccia utente](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [MonoTouch. finestra di dialogo JSON markup](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Finestra di dialogo MonoTouch su GitHub](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Riferimento alla classe UITableViewController](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Riferimento alla classe UINavigationController](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
