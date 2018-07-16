---
title: Introduzione a monotouch. Dialog per xamarin. IOS
description: Questo documento descrive monotouch. Dialog (MT. D), un framework per lo sviluppo di interfaccia utente rapida e dichiarativo con xamarin. IOS. Descrive come usare le APIs MonoTouch.Dialog per creare un'interfaccia nel codice o JSON e usare funzionalità quali pull per aggiornare, ricerca, caricamento dell'immagine di sfondo e altro ancora.
ms.prod: xamarin
ms.assetid: 52A35B24-C23B-8461-A8FF-5928A2128FB0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: bee4b460552c7273021b16955b52ba3d95d3e07c
ms.sourcegitcommit: cb80df345795989528e9df78eea8a5b45d45f308
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/14/2018
ms.locfileid: "39038404"
---
# <a name="introduction-to-monotouchdialog-for-xamarinios"></a>Introduzione a monotouch. Dialog per xamarin. IOS

Monotouch. Dialog, detto MT. D in breve, è un toolkit di sviluppo dell'interfaccia utente rapida che consente agli sviluppatori di compilare le schermate dell'applicazione e navigazione usando le informazioni, anziché l'attività ripetitive di creazione di controller di visualizzazione, le tabelle e così via. Di conseguenza, fornisce una significativa semplificazione della riduzione del codice e lo sviluppo dell'interfaccia utente. Ad esempio, si consideri lo screenshot seguente:

 [![](images/image1.png "Ad esempio, prendere in considerazione questo screenshot")](images/image1.png#lightbox)

Il codice seguente è stato usato per definire l'intera schermata:

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

Quando si lavora con le tabelle in iOS, è spesso una considerevole quantità di codice ripetitive.
Ad esempio, ogni volta che è necessaria una tabella, un'origine dati è necessario per popolare la tabella. In un'applicazione che ha due schermate basate su tabelle che sono connessi tramite un controller di spostamento, ogni schermata condivide molti dello stesso codice.

MT. 1!d semplifica che incapsulando tutto il codice in un'API generica per la creazione della tabella. Fornisce quindi un'astrazione basata su tale API che consente di un oggetto dichiarativo che rende ancora più semplice sintassi di associazione. Di conseguenza, esistono due API disponibili in MT. UNITÀ D:

-   **API di basso livello gli elementi** : tramite il *API Elements* si basa sulla creazione di un albero gerarchico di elementi che rappresentano le schermate e i relativi componenti. L'API Elements offre agli sviluppatori la massima flessibilità e controllo per la creazione di interfacce utente. Inoltre, l'API Elements con supporto avanzato per definizione dichiarativa tramite JSON, che consente la dichiarazione incredibilmente veloce sia, nonché la generazione dinamica dell'interfaccia utente da un server. 
-   **Ad alto livello API di Reflection** , noto anche come i *associazione**API* , in cui le classi vengono annotate con hint dell'interfaccia utente e quindi MT. D automaticamente consente di creare schermate basate sugli oggetti e fornisce un'associazione tra ciò che viene visualizzata (e facoltativamente modificata) nella schermata e l'oggetto sottostante il backup.   Nell'esempio precedente viene illustrato l'uso dell'API di Reflection. Questa API non fornisce il controllo con granularità fine che esegue gli elementi API, ma riduce ulteriormente la complessità creando automaticamente out la gerarchia degli elementi in base agli attributi di classe. 


MT. 1!d proviene compressa con un numero elevato di compilato negli elementi dell'interfaccia utente per la creazione dello schermo, ma riconosce anche la necessità per gli elementi personalizzati e i layout di schermata avanzata. Di conseguenza, l'estendibilità è che un eccellente in primo piano virtuali create con bake all'API. Gli sviluppatori possono estendere gli elementi esistenti o crearne uno nuovo e quindi si integrano perfettamente.

Inoltre, MT. D dispone di un numero di funzionalità comuni dell'esperienza utente di iOS predefiniti, ad esempio "pull per aggiornare" supporta, immagini asincrono il caricamento e il supporto di ricerca.

Questo articolo avrà un quadro completo di utilizzo di MT. 1!d, tra cui:

-   **MT. I componenti D** – ciò sarà incentrata sugli Cenni preliminari sulle classi che costituiscono MT. 1!d abilitare accelerare rapidamente. 
-   **Riferimento agli elementi** : un elenco completo degli elementi predefiniti di MT. D. 
-   **Uso avanzato** : descrive le funzionalità avanzate, ad esempio pull per aggiornare, ricerca, caricamento dell'immagine di sfondo, utilizzo di LINQ per compilare gerarchie di elementi e creazione di elementi personalizzati, le celle, e i controller per utilizzano con MT. D. 

## <a name="setting-up-mtd"></a>Configurazione di MT. 1!D

MT. 1!d viene distribuito con xamarin. IOS. Per usarlo, fare clic sui **riferimenti** nodo di xamarin. IOS del progetto in Visual Studio 2017 o Visual Studio per Mac e aggiungere un riferimento al **monotouch. Dialog-1** assembly. Aggiungere quindi `using MonoTouch.Dialog` istruzioni presenti nell'origine del codice in base alle esigenze.

## <a name="understanding-the-pieces-of-mtd"></a>Comprendere le parti di MT. 1!D

Anche quando si usa la Reflection, API, MT. 1!d crea una gerarchia di elementi dietro le quinte, come se sono stati creato tramite l'API Elements direttamente. Inoltre, il supporto JSON indicato nella sezione precedente crea anche gli elementi. Per questo motivo, è importante avere una conoscenza di base degli elementi costitutivi di MT. D.

MT. 1!d compilazioni schermate usando quattro parti seguenti:

-  **DialogViewController**
-  **RootElement**
-  **Sezione**
-  **Elemento**


### <a name="dialogviewcontroller"></a>DialogViewController

Oggetto *DialogViewController*, o *DVC* breve, eredita da `UITableViewController` e pertanto rappresenta una schermata con una tabella. Un controller di spostamento come un normale UITableViewController può essere inserito molto diffuso.

### <a name="rootelement"></a>RootElement

Oggetto *RootElement* è il contenitore di primo livello per gli elementi di cui vengono inseriti una DVC. Contiene le sezioni, che possono quindi contenere elementi. Non viene eseguito RootElements; invece si tratta semplicemente di contenitori per ciò che effettivamente viene sottoposto a rendering. Un RootElement viene assegnato a una DVC e quindi il DVC esegue il rendering dei relativi elementi figlio.

### <a name="section"></a>Sezione

Una sezione è un gruppo di celle in una tabella. Come con una sezione di tabella normali, possono facoltativamente avere un'intestazione e piè di pagina che può essere testo o le visualizzazioni personalizzate anche, come nello screenshot seguente:

 [![](images/image2.png "Come con una sezione di tabella normali, possono facoltativamente avere un'intestazione e piè di pagina che può essere testo o le visualizzazioni personalizzate anche, come in questo screenshot")](images/image2.png#lightbox)

### <a name="element"></a>Elemento

Un elemento rappresenta una cella nella tabella effettiva. MT. 1!d viene compresso con un'ampia gamma di elementi che rappresentano diversi tipi di dati o di input diversi. Ad esempio, gli screenshot seguenti illustrano alcuni degli elementi disponibili:

 [![](images/image3.png "Ad esempio, in questo screenshot illustrano alcuni degli elementi disponibili")](images/image3.png#lightbox)

## <a name="more-on-sections-and-rootelements"></a>Altre sezioni di accensione e RootElements

Ora esaminiamo RootElements e delle sezioni in modo più dettagliato.

### <a name="rootelements"></a>RootElements

RootElement almeno uno è necessario per avviare il processo di monotouch. Dialog.

Se un RootElement viene inizializzata con un valore/elemento section questo valore viene utilizzato per individuare un elemento che fornisce un riepilogo della configurazione, che viene eseguito il rendering sul lato destro della visualizzazione figlio. Ad esempio, la schermata seguente mostra una tabella a sinistra con una cella contenente il titolo della schermata dei dettagli a destra, "Dessert", insieme al valore di Deserto selezionato.

 [![](images/image4.png "Questo screenshot Mostra una tabella a sinistra con una cella contenente il titolo della schermata dei dettagli a destra, Dessert, insieme al valore di selezionato Deserto") ](images/image4.png#lightbox) [ ![ ] (images/image5.png "questo nello screenshot seguente mostra una tabella a sinistra con una cella contenente il titolo della schermata dei dettagli a destra, Dessert, insieme al valore di Deserto selezionato")](images/image5.png#lightbox)

Elementi radice inoltre possono essere utilizzati all'interno di sezioni per attivare il caricamento di una nuova pagina di configurazione annidata, come illustrato in precedenza. Quando usato in questa modalità la didascalia fornita viene utilizzata durante il rendering all'interno di una sezione e viene usata anche come il titolo per la pagina secondaria. Ad esempio:

```csharp
var root = new RootElement ("Meals") {
    new Section ("Dinner"){
            new RootElement ("Dessert", new RadioGroup ("dessert", 2)) {
                new Section () {
                    new RadioElement ("Ice Cream", "dessert"),
                    new RadioElement ("Milkshake", "dessert"),
                    new RadioElement ("Chocolate Cake", "dessert")
                }
            }
        }
    }
```

Nell'esempio precedente, quando l'utente tocca "Dessert", monotouch. Dialog verrà creata una nuova pagina e spostarsi su di esso con la radice in corso "Dessert" e disporre di un gruppo di opzione con tre valori.

In questo particolare esempio, il gruppo di opzione selezionerà "Torta al cioccolato" nella sezione "Dessert" poiché è stato passato il valore "2" per il RadioGroup. Ciò significa selezionare l'elemento 3 nell'elenco (indice zero).

Chiamare il metodo Add o usando la sintassi dell'inizializzatore in c# 4 consente di aggiungere sezioni.
Vengono forniti i metodi Insert per inserire le sezioni con un'animazione.

Se si crea il RootElement con un'istanza di gruppo (anziché un RadioGroup) il valore di riepilogo di RootElement quando visualizzata in una sezione sarà il conteggio cumulativo di tutte le BooleanElements e CheckboxElements che hanno la stessa chiave come valore Group.Key.

### <a name="sections"></a>Sezioni

Le sezioni vengono utilizzate per raggruppare gli elementi nella schermata e sono validi solo elementi figlio diretti del RootElement. Le sezioni possono contenere uno degli elementi standard, tra cui RootElements nuovo.

RootElements incorporato in una sezione vengono usati per passare a un nuovo livello più profondo.

Nelle sezioni possono avere intestazioni e piè di pagina come stringhe o come UIViews.
In genere si utilizzerà semplicemente le stringhe, ma per creare le interfacce utente personalizzate è possibile usare qualsiasi UIView come l'intestazione o piè di pagina. È possibile utilizzare una stringa per crearli simile al seguente:

```csharp
var section = new Section ("Header", "Footer")
```

Per usare le visualizzazioni, è sufficiente passare le viste al costruttore:

```csharp
var header = new UIImageView (Image.FromFile ("sample.png"));
var section = new Section (header)
```

### <a name="getting-notified"></a>Ricevere notifiche

#### <a name="handling-nsaction"></a>La gestione NSAction

MT. Le superfici D un `NSAction` come un delegato per gestire i callback.
Ad esempio, che si vuole gestire un evento di tocco per una cella della tabella creata da MT. D. Quando si crea un elemento con MT. Funzione D, è sufficiente specificare un callback, come illustrato di seguito:

```csharp
new Section () {
        new StringElement ("Demo Callback", 
                delegate { Console.WriteLine ("Handled"); })
}
```

#### <a name="retrieving-element-value"></a>Valore dell'elemento durante il recupero

Combinato con il `Element.Value` proprietà, il callback può recuperare il valore impostato in altri elementi. Si consideri il codice di esempio seguente:

```csharp
var element = new EntryElement (task.Name, "Enter task description",
        task.Description);
                
var taskElement = new RootElement (task.Name){
        new Section () { element },
        new Section () { 
                new DateElement ("Due Date", task.DueDate)
        },
        new Section ("Demo Retrieving Element Value") {
                new StringElement ("Output Task Description", 
                        delegate { Console.WriteLine (element.Value); })
        }
};
```

Questo codice crea un'interfaccia utente, come illustrato di seguito. Per una procedura dettagliata completa di questo esempio, vedere la [procedura dettagliata sull'API elementi](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md) esercitazione.

 [![](images/image6.png "In combinazione con la proprietà Element.Value, il callback può recuperare il valore impostato in altri elementi")](images/image6.png#lightbox)

Quando l'utente preme ultima cella della tabella, viene eseguito il codice nella funzione anonima scrivendo il valore dal `element` dell'istanza per il **Output applicazione** riempimento in Visual Studio per Mac.

## <a name="built-in-elements"></a>Elementi predefiniti

MT. 1!d dotata di un numero di elementi di cella di tabella predefinite noto come elementi.
Questi elementi vengono usati per visualizzare un'ampia gamma di tipi diversi nelle celle di tabella, ad esempio stringhe, valori a virgola mobile, le date e persino le immagini, per citarne solo alcuni. Ogni elemento si occupa della visualizzazione del tipo di dati in modo appropriato. Ad esempio, un elemento boolean verrà visualizzata un'opzione per attivare o disattivare il relativo valore. Analogamente, un elemento float visualizzerà un dispositivo di scorrimento per modificare il valore float.

Sono presenti elementi ancora più complessi per supportare i tipi di dati più avanzati, ad esempio immagini e html. Ad esempio, un elemento html, che verrà aperto un UIWebView per caricare una pagina web quando selezionato, visualizza una didascalia nella cella della tabella.

### <a name="working-with-element-values"></a>Utilizzo di valori di elemento

Gli elementi che consentono di acquisire l'input dell'utente espongono pubblico `Value` proprietà che contiene il valore corrente dell'elemento in qualsiasi momento. Viene automaticamente aggiornata perché l'utente usa l'applicazione.

Si tratta del comportamento per tutti gli elementi che fanno parte di monotouch. Dialog, ma non obbligatorio per gli elementi creati dall'utente.

### <a name="string-element"></a>Elemento di stringa

Oggetto `StringElement` Mostra un titolo a sinistra di una cella della tabella e il valore della stringa sul lato destro della cella.

 [![](images/image7.png "Un StringElement Mostra una didascalia sul lato sinistro di una cella della tabella e il valore della stringa sul lato destro della cella")](images/image7.png#lightbox)

Usare un `StringElement` come un pulsante, fornire un delegato.

```csharp
new StringElement (
        "Click me",
        () => { new UIAlertView("Tapped", "String Element Tapped"
, null, "ok", null).Show(); })
```

 [![](images/image8.png "Per usare un StringElement come un pulsante, fornire un delegato")](images/image8.png#lightbox)

### <a name="styled-string-element"></a>Elemento di stringa con stile

Oggetto `StyledStringElement` consente di essere presentati tramite entrambi stili di cella di tabella stringhe o con formattazione personalizzata.

 [![](images/image9.png "Un StyledStringElement consente di essere presentati tramite entrambi stili di cella di tabella stringhe o con formattazione personalizzata")](images/image9.png#lightbox)

Il `StyledStringElement` deriva dalla classe `StringElement`, ma consente agli sviluppatori di personalizzare un numero limitato di proprietà, ad esempio il tipo di carattere, colore del testo, colore di sfondo, modalità di interruzione di riga, numero di righe da visualizzare, e se deve essere visualizzato un accessorio.

### <a name="multiline-element"></a>Elemento su più righe

 [![](images/image10.png "Elemento su più righe")](images/image10.png#lightbox)

### <a name="entry-element"></a>Elemento Entry

Il `EntryElement`, come implica il nome, viene usato per ottenere l'input dell'utente. Supporta stringhe regolari o le password, in cui caratteri sono nascoste.

 [![](images/image11.png "Il EntryElement viene usato per ottenere l'input dell'utente")](images/image11.png#lightbox)

Viene inizializzato con tre valori:

-  La didascalia per la voce che verrà visualizzata all'utente.
-  Testo segnaposto (si tratta del testo disattivato che fornisce un suggerimento all'utente). 
-  Il valore del testo.


Il segnaposto e il valore può essere null. Tuttavia, la didascalia è obbligatoria.

In qualsiasi momento, accedendo alla relativa proprietà valore possibile recuperare il valore della `EntryElement`.

Inoltre il `KeyboardType` proprietà può essere impostata in fase di creazione allo stile della tastiera desiderato per l'immissione di dati. Ciò può essere utilizzato per configurare la tastiera usando i valori di `UIKeyboardType` come indicato di seguito:

-  Numerico
-  Telefono
-  URL
-  Email


### <a name="boolean-element"></a>Elemento Boolean

 [![](images/image12.png "Elemento Boolean")](images/image12.png#lightbox)

### <a name="checkbox-element"></a>Elemento casella di controllo

 [![](images/image13.png "Elemento casella di controllo")](images/image13.png#lightbox)

### <a name="radio-element"></a>Elemento di opzione

Oggetto `RadioElement` richiede un `RadioGroup` dovrà essere specificato nella `RootElement`.

```csharp
mtRoot = new RootElement ("Demos", new RadioGroup("MyGroup", 0))
```

 [![](images/image14.png "Un RadioElement richiede un RadioGroup sia specificato nel RootElement")](images/image14.png#lightbox)

 `RootElements` consentono inoltre di coordinare gli elementi radio. Il `RadioElement` i membri possono estendersi su più sezioni (ad esempio implementare qualcosa di simile al selettore di tono anello e suonerie personalizzato separato dal sistema suonerie). La visualizzazione di riepilogo visualizzerà l'elemento opzione attualmente selezionata. Per usarlo, creare il `RootElement` con il costruttore di gruppo, simile al seguente:

```csharp
var root = new RootElement ("Meals", new RadioGroup ("myGroup", 0))
```

Il nome del gruppo di nel `RadioGroup` consente di visualizzare il valore selezionato nella pagina che lo contiene (se presente) e il valore, ovvero zero in questo caso, è l'indice del primo elemento selezionato.

### <a name="badge-element"></a>Elemento badge

 [![](images/image15.png "Elemento badge")](images/image15.png#lightbox)

### <a name="float-element"></a>Elemento float

 [![](images/image16.png "Elemento float")](images/image16.png#lightbox)

### <a name="activity-element"></a>Elemento di attività

 [![](images/image17.png "Elemento di attività")](images/image17.png#lightbox)

### <a name="date-element"></a>Elemento data

 ![](images/image18.png "Elemento data")

Quando si seleziona la cella corrispondente il DateElement, un controllo selezione data viene presentata come illustrato di seguito:

 [![](images/image19.png "Quando si seleziona la cella corrispondente il DateElement, un controllo selezione data viene presentata come illustrato")](images/image19.png#lightbox)

### <a name="time-element"></a>Elemento Time

 [![](images/image20.png "Elemento Time")](images/image20.png#lightbox)

Quando si seleziona la cella corrispondente il TimeElement, selezione ora viene presentato come illustrato di seguito:

 [![](images/image21.png "Quando si seleziona la cella corrispondente il TimeElement, selezione ora viene presentato come illustrato")](images/image21.png#lightbox)

### <a name="datetime-element"></a>Elemento data/ora

 [![](images/image22.png "Elemento data/ora")](images/image22.png#lightbox)

Quando si seleziona la cella corrispondente il DateTimeElement, un controllo selezione data/ora viene presentato come illustrato di seguito:

 [![](images/image23.png "Quando si seleziona la cella corrispondente il DateTimeElement, un controllo selezione data/ora viene presentato come illustrato")](images/image23.png#lightbox)

### <a name="html-element"></a>Elemento HTML

 [![](images/image24.png "Elemento HTML")](images/image24.png#lightbox)

Il `HTMLElement` Visualizza il valore della relativa `Caption` proprietà nella cella della tabella. Quando è selezionata, il `Url` assegnato all'elemento viene caricato in un `UIWebView` controllano, come illustrato di seguito:

 [![](images/image25.png "Quando selezionata, viene caricato dall'Url assegnato all'elemento in un controllo UIWebView come illustrato di seguito")](images/image25.png#lightbox)

### <a name="message-element"></a>Elemento del messaggio

 [![](images/image26.png "Elemento del messaggio")](images/image26.png#lightbox)

### <a name="load-more-element"></a>Carica altro elemento

Usare questo elemento per consentire agli utenti di caricare più elementi nell'elenco. È possibile personalizzare la normale e caricamento dei sottotitoli in lingua originale, nonché il tipo di carattere e colore del testo.
Il `UIActivity` indicatore viene avviata l'animazione e la didascalia di caricamento viene visualizzata quando un utente tocca la cella e quindi il `NSAction` passato nel costruttore viene eseguito. Una volta il codice nel `NSAction` al termine, il `UIActivity` indicatore interrompe l'animazione e la didascalia normale viene visualizzata nuovamente.

### <a name="uiview-element"></a>Elemento UIView

Inoltre, qualsiasi custom `UIView` possono essere visualizzati utilizzando il `UIViewElement`.

### <a name="owner-drawn-element"></a>Elemento proprietario

Questo elemento deve essere una sottoclasse perché è una classe astratta. È consigliabile eseguire l'override di `Height(RectangleF bounds)` metodo in cui è necessario restituire l'altezza dell'elemento, nonché `Draw(RectangleF bounds, CGContext context, UIView view)` in cui è necessario eseguire il disegno personalizzato entro il limite specificato, utilizzando i parametri di contesto e visualizzazione. Questo elemento svolge il grosso della creazione di una sottoclasse un `UIView`e posizionarla nella cella da restituire, lasciando che solo tu debba implementare due override semplice. È possibile visualizzare un esempio di implementazione migliore nell'app di esempio nel `DemoOwnerDrawnElement.cs` file.

Ecco un esempio molto semplice dell'implementazione della classe:

```csharp
public class SampleOwnerDrawnElement : OwnerDrawnElement
 {
    public SampleOwnerDrawnElement (string text) : base(UITableViewCellStyle.Default, "sampleOwnerDrawnElement")
    {
        this.Text = text;
    }

    public string Text
    {
        get;set;    
    }

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

Il `JsonElement` è una sottoclasse di `RootElement` che estende un `RootElement` sia in grado di caricare il contenuto del figlio annidato da un url locale o remoto.

Il `JsonElement` è un `RootElement` che è possibile creare istanze in due forme. Crea una versione una `RootElement` che verrà caricato il contenuto su richiesta. Questi vengono creati tramite il `JsonElement` costruttori che accettano un argomento aggiuntivo al termine, l'url per il contenuto da caricare:

```csharp
var je = new JsonElement ("Dynamic Data", "http://tirania.org/tmp/demo.json");
```

L'altra forma crea i dati da un file locale o un oggetto esistente `System.Json.JsonObject` che sono già analizzate:

```csharp
var je = JsonElement.FromFile ("json.sample");
using (var reader = File.OpenRead ("json.sample"))
    return JsonElement.FromJson (JsonObject.Load (reader) as JsonObject, arg);
```

Per altre informazioni sull'uso di JSON con MT. 1!d, vedere la [Walkthrough elemento JSON](http://docs.xamarin.com/guides/ios/user_interface/monotouch.dialog/json_element_walkthrough) esercitazione.

## <a name="other-features"></a>Altre funzionalità

### <a name="pull-to-refresh-support"></a>Supporto di pull per aggiornare

 *Eseguire il pull-a-* *aggiornare* un effetto visivo originariamente si trova nel *Tweetie2* app, che è diventato un effetto più diffusi tra diverse applicazioni.

Per aggiungere il supporto di pull per l'aggiornamento automatico per le finestre di dialogo, è sufficiente fare due cose: associare un gestore eventi per ricevere una notifica quando l'utente effettua il pull dei dati e invia una notifica di `DialogViewController` quando i dati sono stati caricati per tornare allo stato predefinito.

Associazione di una notifica è semplice. Connettiti al `RefreshRequested` evento sul `DialogViewController`, simile al seguente:

```csharp
dvc.RefreshRequested += OnUserRequestedRefresh;
```

Quindi il metodo `OnUserRequestedRefresh`, si sarebbe accodare il caricamento di alcuni dati, richiesta alcuni dati dalla rete o avviare un thread per calcolare i dati. Una volta caricati i dati, è necessario notificare il `DialogViewController` i nuovi dati e che per ripristinare la visualizzazione dello stato predefinito, eseguire questa operazione chiamando `ReloadComplete`:

```csharp
dvc.ReloadComplete ();
```

### <a name="search-support"></a>Supporto per la ricerca

Per supportare la ricerca, impostare il `EnableSearch` proprietà di `DialogViewController`. È anche possibile impostare il `SearchPlaceholder` proprietà da utilizzare come testo della filigrana nella barra di ricerca.

La ricerca verrà modificato il contenuto della visualizzazione mentre l'utente digita. Cerca i campi visibili e sono indicati i all'utente. Il `DialogViewController` espone tre metodi per avviare, terminare o attivare una nuova operazione di filtro in base ai risultati a livello di codice. Questi metodi sono elencati di seguito:

-  `StartSearch`
-  `FinishSearch`
-  `PerformFilter`


Il sistema è estensibile, pertanto se si desidera, è possibile modificare questo comportamento.

### <a name="background-image-loading"></a>Caricamento dell'immagine di sfondo

Monotouch. Dialog incorpora il [TweetStation](https://github.com/migueldeicaza/TweetStation) caricatore immagine dell'applicazione. Questo caricatore immagine può essere utilizzato per caricare immagini in background, supporta la memorizzazione nella cache e possa notificare al codice quando l'immagine è stata caricata.

Limiterà inoltre il numero di connessioni di rete in uscita.

Il caricatore di immagini viene implementato nel `ImageLoader` (classe), è sufficiente eseguire viene chiamata la `DefaultRequestImage` metodo, è necessario specificare l'Uri per l'immagine da caricare, nonché un'istanza del `IImageUpdated` interfaccia che verrà richiamato quando l'immagine a disponibilità elevata s è stato caricato.

Ad esempio il codice seguente carica un'immagine da un Url in un `BadgeElement`:

```csharp
string uriString = "http://some-server.com/some image url";

var rootElement = new RootElement("Image Loader") {
        new Section(){
                new BadgeElement( ImageLoader.DefaultRequestImage( new Uri(uriString), this), "Xamarin")
        }
};
```

La classe ImageLoader espone un metodo di eliminazione che è possibile chiamare quando si desidera rilasciare tutte le immagini attualmente memorizzati nella cache in memoria. Il codice corrente ha una cache per le immagini di 50. Se si vuole usare una dimensione della cache diverso (ad esempio, se ci si aspetta le immagini di essere troppo grande che le 50 immagini sarebbe eccessiva), è possibile solo creare istanze di ImageLoader e passare il numero di immagini che si desidera mantenere nella cache.

## <a name="using-linq-to-create-element-hierarchy"></a>Utilizzo di LINQ per creare una gerarchia di elementi

Tramite l'utilizzo di clever della sintassi di inizializzazione di # e LINQ, LINQ può essere utilizzato per creare una gerarchia dell'elemento. Ad esempio, il codice seguente crea una schermata da alcuni matrici di stringhe e gli handle di selezione tramite una funzione anonima che viene passata in ognuna delle celle `StringElement`:

```csharp
var rootElement = new RootElement ("LINQ root element") {
from x in new string [] { "one", "two", "three" }
select new Section (x) {
from y in "Hello:World".Split (':')
select (Element) new StringElement (y,
delegate { Debug.WriteLine("cell tapped"); })
}
};
```

Questo può essere combinato facilmente con un archivio dati XML o dati da un database per creare applicazioni complesse quasi interamente dai dati.

## <a name="extending-mtd"></a>Estensione di MT. 1!D

### <a name="creating-custom-elements"></a>Creare elementi personalizzati

È possibile creare un elemento personalizzato ereditando da una un elemento esistente o mediante la derivazione dalla classe elemento radice.

Per creare un proprio elemento, si dovranno eseguire l'override dei seguenti:

```csharp
// To release any heavy resources that you might have
    void Dispose (bool disposing);

    // To retrieve the UITableViewCell for your element
    // you would need to prepare the cell to be reused, in the
    // same way that UITableView expects reusable cells to work
    UITableViewCell GetCell (UITableView tv)

    // To retrieve a "summary" that can be used with
    // a root element to render a summary one level up.  
    string Summary ()
    // To detect when the user has tapped on the cell
    void Selected (DialogViewController dvc, UITableView tableView, NSIndexPath path)
    // If you support search, to probe if the cell matches the user input
    bool Matches (string text)
```

Se l'elemento può avere una dimensione variabile, è necessario implementare il `IElementSizing` interfaccia, che contiene un metodo:

```csharp
// Returns the height for the cell at indexPath.Section, indexPath.Row
    float GetHeight (UITableView tableView, NSIndexPath indexPath);
```

Se si prevede di implementare il `GetCell` metodo chiamando `base.GetCell(tv)` e personalizzare la cella restituita, è necessario eseguire l'override anche di `CellKey` proprietà per restituire una chiave che sia univoca per l'elemento, come segue:

```csharp
static NSString MyKey = new NSString ("MyKey");
    protected override NSString CellKey {
        get {
            return MyKey;
        }
    }
```

Questa procedura funziona per la maggior parte degli elementi, ma non per il `StringElement` e `StyledStringElement` come quelli usare il proprio set di chiavi per diversi scenari di rendering. È necessario replicare il codice in tali classi.

### <a name="dialogviewcontrollers-dvcs"></a>DialogViewControllers (molto diffuso)

Usare lo stesso sia la Reflection e l'API Elements `DialogViewController`. In alcuni casi è opportuno personalizzare l'aspetto della visualizzazione alternativa è possibile usare alcune funzionalità del `UITableViewController` che vanno oltre la creazione di base delle interfacce utente.

Il `DialogViewController` è semplicemente una sottoclasse del `UITableViewController` ed è possibile personalizzare, nello stesso modo che è necessario personalizzare un `UITableViewController`.

Ad esempio, se si desidera modificare lo stile di elenco per essere rappresentato `Grouped` o `Plain`, è possibile impostare questo valore modificando la proprietà quando si crea il controller, simile al seguente:

```csharp
var myController = new DialogViewController (root, true){
        Style = UITableViewStyle.Grouped;
    }
```

Per altre informazioni avanzate delle personalizzazioni del `DialogViewController`, ad esempio l'impostazione relativo sfondo, si farebbe sottoclasse ed eseguire l'override appropriate metodi, come illustrato nell'esempio seguente:

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

Un altro punto di personalizzazione è dei seguenti metodi virtuali la `DialogViewController`:

```csharp
public override Source CreateSizingSource (bool unevenRows)
```

Questo metodo deve restituire una sottoclasse `DialogViewController.Source` per i casi in cui le celle dimensioni uguali o una sottoclasse di `DialogViewController.SizingSource` se le celle sono sbilanciate.

È possibile utilizzare questo override per acquisire uno qualsiasi del `UITableViewSource` metodi. Ad esempio, [TweetStation](https://github.com/migueldeicaza/TweetStation) questa viene usata per rilevare quando l'utente ha fatto scorrere nella parte superiore e aggiornare di conseguenza il numero di tweet da leggere.

## <a name="validation"></a>Convalida

Gli elementi non forniscono la convalida se stessi come i modelli che sono particolarmente adatti per le pagine web e applicazioni desktop di non eseguire il mapping direttamente al modello di interazione dell'iPhone.

Se si desidera eseguire la convalida dei dati, è necessario eseguire questa operazione quando l'utente attiva un'azione con i dati immessi. Ad esempio un <span class="ui">eseguita</span> oppure <span class="ui">successivo</span> pulsante sulla barra degli strumenti superiore o alcuni `StringElement` usato come pulsante per passare alla fase successiva.

Si tratta in cui si esegue la convalida di input di base, e probabilmente più complesso, ad esempio il controllo della validità di una combinazione di utente/password con un server di convalida.

Modo in cui si notifica all'utente di un errore è specifico dell'applicazione. È possibile visualizzare una un `UIAlertView` o Mostra un suggerimento.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato numerose informazioni sulle monotouch. Dialog. È stato illustrato le nozioni di base di come MT. D funziona e illustrati i vari componenti che costituiscono MT. D. Ha inoltre mostrato l'ampia gamma di elementi e le personalizzazioni di tabella supportate da MT. 1!d e illustrato come MT. 1!d possono essere estese con gli elementi personalizzati. Inoltre è descritto il supporto JSON in MT. 1!d che consente di creare gli elementi in modo dinamico da JSON.


## <a name="related-links"></a>Collegamenti correlati

- [Screencast - Miguel de Icaza consente di creare una schermata di accesso di iOS con monotouch. Dialog](http://youtu.be/3butqB1EG0c)
- [Screencast - facilmente creare interfacce utente iOS con monotouch. Dialog](http://youtu.be/j7OC5r8ZkYg)
- [Procedura dettagliata: Creazione di un'applicazione mediante l'API Elements](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Procedura dettagliata: Creazione di un'applicazione mediante l'API Reflection](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Procedura dettagliata: Uso di un elemento JSON per creare un'interfaccia utente](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Markup JSON per monotouch. Dialog](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Finestra di dialogo di MonoTouch su Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Riferimento alla classe UITableViewController](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Riferimento alla classe UINavigationController](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
