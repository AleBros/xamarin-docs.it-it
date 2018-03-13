---
title: Introduzione a MonoTouch.Dialog
description: "MonoTouch.Dialog (MT D) toolkit è un framework indispensabile per lo sviluppo dell'interfaccia utente in xamarin. IOS rapido di applicazioni. MT D rende rapido e semplice definire l'applicazione complessa dell'interfaccia utente utilizzando un approccio dichiarativo, anziché il numero di controller di navigazione, tabelle e così via. Inoltre, MT D dispone di un set di API che forniscono agli sviluppatori con un controllo completo o trasferisce approccio, nonché a funzionalità aggiuntive, ad esempio l'immagine di sfondo pull per l'aggiornamento, il caricamento, Cerca il supporto e la generazione dell'interfaccia utente dinamica tramite i dati JSON flessibile. Questa guida illustra i diversi modi per utilizzare MT D e quindi approfondisce deep utilizzo avanzato."
ms.topic: article
ms.prod: xamarin
ms.assetid: 52A35B24-C23B-8461-A8FF-5928A2128FB0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: b9bf4c5ee803aa60a2730703e64fcf73d07efdb5
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="introduction-to-monotouchdialog"></a>Introduzione a MonoTouch.Dialog

_MonoTouch.Dialog (MT D) toolkit è un framework indispensabile per lo sviluppo dell'interfaccia utente in xamarin. IOS rapido di applicazioni. MT D rende rapido e semplice definire l'applicazione complessa dell'interfaccia utente utilizzando un approccio dichiarativo, anziché il numero di controller di navigazione, tabelle e così via. Inoltre, MT D dispone di un set di API che forniscono agli sviluppatori con un controllo completo o trasferisce approccio, nonché a funzionalità aggiuntive, ad esempio l'immagine di sfondo pull per l'aggiornamento, il caricamento, Cerca il supporto e la generazione dell'interfaccia utente dinamica tramite i dati JSON flessibile. Questa guida illustra i diversi modi per utilizzare MT D e quindi approfondisce deep utilizzo avanzato._


MonoTouch.Dialog, detto MT D in breve, è un rapido sviluppo toolkit dell'interfaccia che consente agli sviluppatori di creare schermate dell'applicazione e di navigazione utilizzando le informazioni, anziché la necessità di creazione di controller di visualizzazione, le tabelle e così via. Di conseguenza, offre una notevole semplificazione della riduzione di sviluppo e il codice dell'interfaccia utente. Si consideri, ad esempio, nella schermata seguente:

 [![](images/image1.png "Si consideri ad esempio questa schermata")](images/image1.png#lightbox)

Il codice seguente è stato usato per definire questo schermo intero:

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

Quando si utilizzano tabelle in iOS, è spesso molto codice ripetitive.
Ad esempio, ogni volta che è necessaria una tabella, un'origine dati è necessario per popolare la tabella. In un'applicazione che dispone di due schermate basate su tabella connessi tramite un controller di navigazione, ogni schermata condivide molti lo stesso codice.

MT D che semplifica incapsulando tutto il codice in un'API generica per la creazione della tabella. Quindi, fornisce un'astrazione di sopra di tale API che consente di un oggetto dichiarativo sintassi che rende ancora più semplice di associazione. Di conseguenza, sono disponibili due API in MT UNITÀ D:

-   **API di basso livello elementi** : *elementi API* basato sulla creazione di una struttura ad albero gerarchico di elementi che rappresentano le schermate e i relativi componenti. L'API di elementi offre agli sviluppatori la massima flessibilità e controllo per la creazione di interfacce utente. Inoltre, l'API di elementi con supporto avanzato per definizione dichiarativa tramite JSON, che consente di sia dichiarazione molto veloci, nonché la generazione dell'interfaccia utente dinamica da un server. 
-   **API di Reflection di alto livello** : anche noto come il *associazione**API* , in cui le classi che vengono annotate con l'hint dell'interfaccia utente e quindi MT D automaticamente consente di creare schermate basate sugli oggetti e fornisce un'associazione tra ciò che viene visualizzata (e facoltativamente modificare) sullo schermo e l'oggetto sottostante il backup. Nell'esempio precedente viene illustrato l'utilizzo dell'API di Reflection. Questa API non forniscono il controllo con granularità fine che esegue gli elementi API, ma riduce ulteriormente la complessità di costruire automaticamente la gerarchia degli elementi in base agli attributi di classe. 


MT D sono un ampio set di elementi dell'interfaccia utente per la creazione della schermata incorporati, ma anche riconosce la necessità di elementi personalizzati e layout dello schermo avanzate. Di conseguenza, l'estendibilità è caratterizzato da un costrutto sfornato nell'API. Gli sviluppatori possono estendere gli elementi esistenti o crearne di nuovi e quindi si integrano perfettamente.

Inoltre, MT D include molte funzionalità comuni iOS UX incorporato, ad esempio "pull per l'aggiornamento" supporta, asincrona di caricamento, immagini e il supporto di ricerca.

In questo articolo verrà un quadro completo utilizzo MT D, tra cui:

-   **MT Componenti D** – in questo articolo è incentrato su Cenni preliminari sulle classi che costituiscono MT D per consentire di ottenere rapidamente un esperto. 
-   **Riferimento agli elementi** : un elenco completo degli elementi incorporati della MT D. 
-   **Utilizzo avanzato** : questo articolo descrive le funzionalità avanzate, ad esempio pull per l'aggiornamento, ricerca, caricamento dell'immagine di sfondo, l'utilizzo di LINQ per creare gerarchie di elementi e creazione di elementi personalizzati, le celle, e i controller per utilizzano con MT D. 

## <a name="understanding-the-pieces-of-mtd"></a>Comprendere le parti di MT D

Anche quando si utilizza la Reflection API, MT D viene creata una gerarchia di elemento dietro le quinte, come se sono stati creato tramite l'API di elementi direttamente. Inoltre, il supporto JSON riportato nella sezione precedente crea anche gli elementi. Per questo motivo, è importante avere familiarità con gli elementi costitutivi di MT D.

MT D compila schermate mediante quattro parti seguenti:

-  **DialogViewController**
-  **RootElement**
-  **Sezione**
-  **Elemento**


### <a name="dialogviewcontroller"></a>DialogViewController

Oggetto *DialogViewController*, o *DVC* in breve, eredita da `UITableViewController` e pertanto rappresenta una schermata con una tabella. DVCs può essere inserito in un controller di navigazione come un normale UITableViewController.

### <a name="rootelement"></a>RootElement

Oggetto *RootElement* è il contenitore di primo livello per gli elementi che costituiscono una DVC. Contiene sezioni, che quindi possono contenere elementi. Non viene eseguito RootElements; invece, si tratta semplicemente di contenitori per ciò che effettivamente il rendering. Un RootElement viene assegnato a una DVC e quindi il DVC esegue il rendering nei relativi elementi figlio.

### <a name="section"></a>Sezione

Una sezione è un gruppo di celle in una tabella. Come con una sezione normale tabella, può facoltativamente includere un'intestazione e piè di pagina che può essere testo o visualizzazioni anche personalizzate, come illustrato nella schermata seguente:

 [![](images/image2.png "Come con una sezione normale tabella, può facoltativamente includere un'intestazione e piè di pagina che possono essere di testo o visualizzazioni anche personalizzate, come in questa schermata")](images/image2.png#lightbox)

### <a name="element"></a>Elemento

Un elemento rappresenta una cella nella tabella effettiva. MT D viene compresso con un'ampia gamma di elementi che rappresentano diversi tipi di dati o di input diversi. Ad esempio, le schermate seguenti illustrano alcuni degli elementi disponibili:

 [![](images/image3.png "Ad esempio, questo schermate illustrano alcuni degli elementi disponibili")](images/image3.png#lightbox)

## <a name="more-on-sections-and-rootelements"></a>Altre sezioni e RootElements

Ora esaminiamo RootElements e sezioni in modo più dettagliato.

### <a name="rootelements"></a>RootElements

Per avviare il processo MonoTouch.Dialog, è necessario almeno RootElement.

Se un RootElement viene inizializzata con un valore di sezione o elemento questo valore viene utilizzato per individuare un elemento che fornisce un riepilogo della configurazione, che viene eseguito il rendering sul lato destro della visualizzazione figlio. Ad esempio, la schermata seguente mostra una tabella a sinistra con una cella contenente il titolo della schermata Dettagli a destra "Qui", insieme al valore del desert selezionato.

 [![](images/image4.png "Questa schermata mostra una tabella a sinistra con una cella contenente il titolo della schermata Dettagli a destra, qui, insieme al valore del desert selezionato") ](images/image4.png#lightbox) [ ![ ] (images/image5.png "questo schermata riportata di seguito è illustrata una tabella a sinistra con una cella contenente il titolo della schermata Dettagli a destra, qui, insieme al valore del desert selezionato")](images/image5.png#lightbox)

Elementi radice anche possono essere utilizzati all'interno di sezioni per attivare il caricamento di una nuova pagina di configurazione annidati, come illustrato in precedenza. Quando usato in questa modalità la didascalia fornita durante il rendering all'interno di una sezione e viene usata anche come titolo per la pagina secondaria. Ad esempio:

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

Nell'esempio precedente, quando l'utente tocca su "Qui", MonoTouch.Dialog verrà creata una nuova pagina e spostarsi su di esso con la radice da "Qui" e con un gruppo di opzione con tre valori.

In questo particolare esempio, il gruppo di opzione selezionare "Torta al cioccolato" nella sezione "Qui" perché il valore "2" è stato passato al RadioGroup. Ciò significa selezionare l'elemento 3 nell'elenco (indice zero).

Chiamare il metodo Add o utilizzando la sintassi dell'inizializzatore c# 4 Aggiunge sezioni.
Metodi di inserimento vengono forniti per inserire le sezioni con un'animazione.

Se si crea il RootElement con un'istanza del gruppo (anziché un RadioGroup) il valore di riepilogo di RootElement quando visualizzata in una sezione sarà il conteggio cumulativo di tutte le BooleanElements e CheckboxElements che hanno la stessa chiave come valore Group.Key.

### <a name="sections"></a>Sezioni

Le sezioni sono utilizzate per raggruppare gli elementi nella schermata e sono i solo elementi figlio diretti validi di RootElement. Le sezioni possono contenere uno degli elementi standard, tra cui RootElements nuovo.

Incorporato in una sezione RootElements vengono utilizzati per passare a un nuovo livello di dettaglio.

Nelle sezioni presentano intestazioni e piè di pagina sotto forma di stringhe o come UIViews.
In genere si utilizzerà solo le stringhe, ma per creare interfacce utente personalizzate è possibile utilizzare qualsiasi UIView come l'intestazione o piè di pagina. È possibile utilizzare una stringa per crearli simile al seguente:

```csharp
var section = new Section ("Header", "Footer")
```

Per utilizzare le viste, passare solo le viste al costruttore:

```csharp
var header = new UIImageView (Image.FromFile ("sample.png"));
var section = new Section (header)
```

### <a name="getting-notified"></a>Ricevere notifiche

#### <a name="handling-nsaction"></a>Gestione NSAction

MT Superfici D un `NSAction` come un delegato per la gestione di callback.
Ad esempio che si desidera gestire un evento di tocco per una cella della tabella creata da MT D. Quando si crea un elemento con MT Funzione D, è sufficiente fornire un callback, come illustrato di seguito:

```csharp
new Section () {
        new StringElement ("Demo Callback", 
                delegate { Console.WriteLine ("Handled"); })
}
```

#### <a name="retrieving-element-value"></a>Recuperare il valore dell'elemento

Combinata con la `Element.Value` proprietà, il callback può recuperare il valore impostato in altri elementi. Si consideri il codice di esempio seguente:

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

Questo codice crea un'interfaccia utente, come illustrato di seguito. Per una descrizione completa di questo esempio, vedere il [elementi API Walkthrough](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md) esercitazione.

 [![](images/image6.png "In combinazione con la proprietà Element.Value, il callback può recuperare il valore impostato in altri elementi")](images/image6.png#lightbox)

Quando l'utente preme la cella della tabella nella parte inferiore, viene eseguito il codice nella funzione anonima scrivendo il valore dal `element` istanza per il **Output dell'applicazione** riempimento in Visual Studio per Mac.

## <a name="built-in-elements"></a>Elementi predefiniti

MT D viene fornito con un numero di elementi di cella di tabella predefinite noto come elementi.
Questi elementi vengono utilizzati per visualizzare un'ampia gamma di tipi diversi in celle della tabella, ad esempio stringhe, float, date e anche le immagini, per assegnare un nome di un numero limitato. Ogni elemento occupa la visualizzazione del tipo di dati in modo appropriato. Ad esempio, un elemento boolean visualizzerà un'opzione per attivare o disattivare il relativo valore. Analogamente, un elemento float verrà visualizzato un dispositivo di scorrimento per modificare il valore float.

Sono presenti elementi ancora più complessi per supportare più tipi di dati, ad esempio immagini e html. Ad esempio, un elemento html, che aprirà un UIWebView per caricare una pagina web quando selezionato, visualizza una didascalia nella cella della tabella.

### <a name="working-with-element-values"></a>Utilizzo di valori di elemento

Gli elementi che consentono di acquisire l'input dell'utente espongono un pubblico `Value` proprietà che contiene il valore corrente dell'elemento in qualsiasi momento. Come l'utente utilizza l'applicazione viene aggiornata automaticamente.

Si tratta del comportamento per tutti gli elementi che fanno parte di MonoTouch.Dialog, ma non è necessaria per gli elementi creati dall'utente.

### <a name="string-element"></a>Elemento di tipo stringa

Oggetto `StringElement` Mostra una didascalia sul lato sinistro di una cella della tabella e il valore di stringa sul lato destro della cella.

 [![](images/image7.png "Un StringElement Mostra una didascalia sul lato sinistro di una cella della tabella e il valore di stringa sul lato destro della cella")](images/image7.png#lightbox)

Per utilizzare un `StringElement` come un pulsante, viene fornito un delegato.

```csharp
new StringElement (
        "Click me",
        () => { new UIAlertView("Tapped", "String Element Tapped"
, null, "ok", null).Show(); })
```

 [![](images/image8.png "Per utilizzare un StringElement come un pulsante, viene fornito un delegato")](images/image8.png#lightbox)

### <a name="styled-string-element"></a>Elemento di stringa con stile

Oggetto `StyledStringElement` consente di essere presentati utilizzando entrambi stili di cella di tabella stringhe o con formattazione personalizzata.

 [![](images/image9.png "Un StyledStringElement consente di essere presentati utilizzando entrambi stili di cella di tabella stringhe o con formattazione personalizzata")](images/image9.png#lightbox)

Il `StyledStringElement` deriva dalla classe `StringElement`, ma consente agli sviluppatori di personalizzare un numero limitato di proprietà quali il tipo di carattere, colore del testo, colore di sfondo, modalità di interruzione di riga, il numero di righe da visualizzare, e se deve essere visualizzato un accessorio.

### <a name="multiline-element"></a>Elemento su più righe

 [![](images/image10.png "Elemento su più righe")](images/image10.png#lightbox)

### <a name="entry-element"></a>Elemento Entry

Il `EntryElement`, come il nome implica, viene utilizzato per ottenere l'input dell'utente. Supporta stringhe regolari o password, in cui vengono nascosti i caratteri.

 [![](images/image11.png "Il EntryElement viene usata per ottenere l'input dell'utente")](images/image11.png#lightbox)

Viene inizializzato con tre valori:

-  Didascalia della voce che verrà visualizzata all'utente.
-  Testo segnaposto (si tratta di testo che fornisce un suggerimento all'utente disabilitato-out). 
-  Il valore del testo.


Il segnaposto e il valore può essere null. Tuttavia, la didascalia è obbligatoria.

In qualsiasi momento, accedendo alla relativa proprietà valore possibile recuperare il valore della `EntryElement`.

Inoltre il `KeyboardType` proprietà può essere impostata in fase di creazione allo stile del tipo di tastiera desiderato per l'immissione di dati. Questo può essere usato per configurare la tastiera utilizzando i valori di `UIKeyboardType` come indicato di seguito:

-  Numerico
-  Telefono
-  URL
-  Email


### <a name="boolean-element"></a>Elemento Boolean

 [![](images/image12.png "Elemento Boolean")](images/image12.png#lightbox)

### <a name="checkbox-element"></a>Elemento casella di controllo

 [![](images/image13.png "Elemento casella di controllo")](images/image13.png#lightbox)

### <a name="radio-element"></a>Elemento di opzione

A `RadioElement` richiede un `RadioGroup` specifichi il `RootElement`.

```csharp
mtRoot = new RootElement ("Demos", new RadioGroup("MyGroup", 0))
```

 [![](images/image14.png "Un RadioElement richiede un RadioGroup specifichi il RootElement")](images/image14.png#lightbox)

 `RootElements` vengono inoltre utilizzati per coordinare gli elementi di opzione. Il `RadioElement` membri possono estendersi su più sezioni (ad esempio implementare un comportamento simile per il selettore di tono circolare e suonerie personalizzato separato dal sistema suonerie). La visualizzazione di riepilogo visualizzerà l'elemento di opzione attualmente selezionato. Per usare questo, creare il `RootElement` con il costruttore di gruppo, simile al seguente:

```csharp
var root = new RootElement ("Meals", new RadioGroup ("myGroup", 0))
```

Il nome del gruppo in `RadioGroup` viene utilizzato per visualizzare il valore selezionato nella pagina contenitore (se presente) e il valore, ovvero zero in questo caso, è l'indice del primo elemento selezionato.

### <a name="badge-element"></a>Elemento badge

 [![](images/image15.png "Elemento badge")](images/image15.png#lightbox)

### <a name="float-element"></a>Elemento float

 [![](images/image16.png "Elemento float")](images/image16.png#lightbox)

### <a name="activity-element"></a>Elemento di attività

 [![](images/image17.png "Elemento di attività")](images/image17.png#lightbox)

### <a name="date-element"></a>Elemento data

 ![](images/image18.png "Elemento data")

Se la cella corrispondente il DateElement è selezionata, viene visualizzata un controllo selezione data come illustrato di seguito:

 [![](images/image19.png "Se la cella corrispondente il DateElement è selezionata, viene visualizzata un controllo selezione data come mostrato")](images/image19.png#lightbox)

### <a name="time-element"></a>Elemento Time

 [![](images/image20.png "Elemento Time")](images/image20.png#lightbox)

Se la cella corrispondente il TimeElement è selezionata, viene visualizzato un controllo selezione ora come illustrato di seguito:

 [![](images/image21.png "Quando la cella corrispondente il TimeElement è selezionata, una selezione di tempo viene presentata come illustrato")](images/image21.png#lightbox)

### <a name="datetime-element"></a>Elemento DateTime

 [![](images/image22.png "Elemento DateTime")](images/image22.png#lightbox)

Se la cella corrispondente il DateTimeElement è selezionata, viene visualizzato un controllo selezione data/ora come illustrato di seguito:

 [![](images/image23.png "Se la cella corrispondente il DateTimeElement è selezionata, viene visualizzato un controllo selezione data/ora come mostrato")](images/image23.png#lightbox)

### <a name="html-element"></a>Elemento HTML

 [![](images/image24.png "Elemento HTML")](images/image24.png#lightbox)

Il `HTMLElement` Visualizza il valore della relativa `Caption` proprietà nella cella della tabella. Quando è selezionata, il `Url` assegnato all'elemento viene caricato in un `UIWebView` controllare come illustrato di seguito:

 [![](images/image25.png "Quando selezionata, l'Url assegnato all'elemento viene caricato in un controllo UIWebView, come illustrato di seguito")](images/image25.png#lightbox)

### <a name="message-element"></a>Elemento del messaggio

 [![](images/image26.png "Elemento del messaggio")](images/image26.png#lightbox)

### <a name="load-more-element"></a>Caricare più elemento

Utilizzare questo elemento per consentire agli utenti di caricare più elementi nell'elenco. È possibile personalizzare la normale e le didascalie di caricamento, nonché il tipo di carattere e colore del testo.
Il `UIActivity` indicatore viene avviata l'animazione e la didascalia di caricamento viene visualizzata quando un utente tocca la cella, quindi il `NSAction` passato il costruttore viene eseguito. Una volta il codice nel `NSAction` al termine, il `UIActivity` indicatore arresta l'animazione e la didascalia normale viene visualizzata nuovamente.

### <a name="uiview-element"></a>Elemento UIView

Inoltre, qualsiasi personalizzato `UIView` possono essere visualizzati utilizzando il `UIViewElement`.

### <a name="owner-drawn-element"></a>Elemento proprietario

Questo elemento deve essere sottoclassato perché è una classe astratta. È necessario eseguire l'override di `Height(RectangleF bounds)` metodo in cui è necessario restituire l'altezza dell'elemento, così come `Draw(RectangleF bounds, CGContext context, UIView view)` in cui è necessario eseguire il disegno personalizzato entro i limiti specificati, utilizzando i parametri di contesto e visualizzazione. Questo elemento provvederà di creazione di una sottoclasse un `UIView`e inserirlo nella cella da restituire, lasciando è la necessità di implementare due sostituzioni semplice. È possibile visualizzare una migliore implementazione di esempio nell'applicazione di esempio nel `DemoOwnerDrawnElement.cs` file.

Di seguito è riportato un esempio molto semplice dell'implementazione della classe:

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

Il `JsonElement` è una sottoclasse di `RootElement` che estende un `RootElement` per essere in grado di caricare il contenuto dell'elemento figlio nidificata da un url locale o remoto.

Il `JsonElement` è un `RootElement` che può essere creata un'istanza in due formati. Crea una versione di un `RootElement` che caricherà il contenuto su richiesta. Vengono creati utilizzando il `JsonElement` costruttori che accettano un argomento supplementare alla fine, l'url per il contenuto da caricare:

```csharp
var je = new JsonElement ("Dynamic Data", "http://tirania.org/tmp/demo.json");
```

L'altro modulo Crea i dati da un file locale o un oggetto esistente `System.Json.JsonObject` analizzabile già:

```csharp
var je = JsonElement.FromFile ("json.sample");
using (var reader = File.OpenRead ("json.sample"))
    return JsonElement.FromJson (JsonObject.Load (reader) as JsonObject, arg);
```

Per ulteriori informazioni sull'uso di JSON con MT D, vedere il [procedura dettagliata elemento JSON](http://docs.xamarin.com/guides/ios/user_interface/monotouch.dialog/json_element_walkthrough) esercitazione.

## <a name="other-features"></a>Altre funzionalità

### <a name="pull-to-refresh-support"></a>Supporto di pull per l'aggiornamento

 *Pull-a-* *aggiornare* è un effetto visivo originariamente, vedere il *Tweetie2* app, che è diventata un effetto più diffuso tra più applicazioni.

Per aggiungere il supporto di pull per l'aggiornamento automatico per le finestre di dialogo, è necessario solo eseguire due operazioni: associare un gestore eventi per ricevere una notifica quando l'utente effettua il pull dei dati e informare il `DialogViewController` quando i dati sono stati caricati per tornare allo stato predefinito.

Associazione di una notifica è semplice. connettersi solo al `RefreshRequested` evento il `DialogViewController`, come segue:

```csharp
dvc.RefreshRequested += OnUserRequestedRefresh;
```

Scegliere il metodo `OnUserRequestedRefresh`, si sarebbe coda il caricamento di alcuni dati, richiesta di alcuni dati dalla rete o creare un thread per calcolare i dati. Una volta caricati i dati, è necessario notificare il `DialogViewController` i nuovi dati e per ripristinare la visualizzazione dello stato predefinito, tale scopo, chiamare `ReloadComplete`:

```csharp
dvc.ReloadComplete ();
```

### <a name="search-support"></a>Supporto per la ricerca

Per supportare la ricerca, impostare il `EnableSearch` proprietà il `DialogViewController`. È inoltre possibile impostare il `SearchPlaceholder` proprietà da utilizzare come testo della filigrana nella barra di ricerca.

La ricerca verrà modificato il contenuto della visualizzazione dei tipi di utente. Cerca i campi visibili e Mostra quelle all'utente. Il `DialogViewController` espone tre metodi per avviare, terminare o attivare una nuova operazione di filtro con i risultati a livello di codice. Questi metodi sono elencati di seguito:

-  `StartSearch`
-  `FinishSearch`
-  `PerformFilter`


Il sistema è estensibile, pertanto se si desidera, è possibile modificare questo comportamento.

### <a name="background-image-loading"></a>Caricamento dell'immagine di sfondo

MonoTouch.Dialog incorpora il [TweetStation](https://github.com/migueldeicaza/TweetStation) caricatore immagine dell'applicazione. Il caricatore immagine può essere utilizzato per caricare immagini in background, supporta la memorizzazione nella cache e può informare il codice quando è stata caricata l'immagine.

Inoltre limiterà il numero di connessioni di rete in uscita.

Il caricatore immagine viene implementato nel `ImageLoader` (classe), è sufficiente è chiamata la `DefaultRequestImage` (metodo), è necessario specificare l'Uri per l'immagine da caricare, nonché un'istanza del `IImageUpdated` interfaccia che verrà richiamato quando l'immagine a disponibilità elevata s è stato caricato.

Ad esempio il codice seguente carica un'immagine da un Url in un `BadgeElement`:

```csharp
string uriString = "http://some-server.com/some image url";

var rootElement = new RootElement("Image Loader") {
        new Section(){
                new BadgeElement( ImageLoader.DefaultRequestImage( new Uri(uriString), this), "Xamarin")
        }
};
```

La classe ImageLoader espone un metodo di eliminazione che è possibile chiamare quando si desidera rilasciare tutte le immagini memorizzate nella cache in memoria. Il codice corrente dispone di una cache per le immagini di 50. Se si desidera utilizzare una dimensione della cache diverso (ad esempio, se ci si aspetta le immagini troppo grande che le 50 immagini sarebbe troppo), è possibile solo creare istanze di ImageLoader e passare il numero di immagini che si desidera mantenere nella cache.

## <a name="using-linq-to-create-element-hierarchy"></a>Utilizzo di LINQ per creare una gerarchia di elementi

Tramite l'uso intelligente di LINQ e # sintassi di inizializzazione, LINQ può essere utilizzato per creare una gerarchia dell'elemento. Ad esempio, il codice seguente crea una schermata da alcune matrici di stringhe e gli handle di selezione tramite una funzione anonima viene passata in ciascuna delle celle `StringElement`:

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

Questo può facilmente essere combinato con un archivio di dati XML o dati da un database per creare applicazioni complesse quasi interamente dai dati.

## <a name="extending-mtd"></a>Estensione MT D

### <a name="creating-custom-elements"></a>Creazione di elementi personalizzati

È possibile creare un elemento personalizzato ereditando dalla classe di un elemento esistente o derivandolo dalla classe elemento radice.

Per creare un elemento personalizzato, sarà opportuno l'override dei metodi seguenti:

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

Se l'elemento può avere una dimensione variabile, è necessario implementare la `IElementSizing` interfaccia, che contiene un metodo:

```csharp
// Returns the height for the cell at indexPath.Section, indexPath.Row
    float GetHeight (UITableView tableView, NSIndexPath indexPath);
```

Se si prevede di implementare il `GetCell` metodo chiamando `base.GetCell(tv)` e personalizzare la cella restituita, è necessario inoltre eseguire l'override di `CellKey` proprietà per restituire una chiave che sarà univoca per l'elemento, come segue:

```csharp
static NSString MyKey = new NSString ("MyKey");
    protected override NSString CellKey {
        get {
            return MyKey;
        }
    }
```

Questo procedimento funziona per la maggior parte degli elementi, ma non per il `StringElement` e `StyledStringElement` come quelli usano il proprio set di chiavi per vari scenari per il rendering. È necessario replicare il codice in tali classi.

### <a name="dialogviewcontrollers-dvcs"></a>DialogViewControllers (DVCs)

Utilizzare lo stesso sia la Reflection e l'API di elementi `DialogViewController`. Talvolta è necessario personalizzare l'aspetto della visualizzazione o si potrebbe voler usare alcune funzionalità del `UITableViewController` che vanno oltre la creazione di base di interfacce utente.

Il `DialogViewController` è semplicemente una sottoclasse di `UITableViewController` e sarà possibile personalizzarlo nello stesso modo che è necessario personalizzare un `UITableViewController`.

Ad esempio, se si desidera modificare lo stile di elenco per essere `Grouped` o `Plain`, è possibile impostare questo valore modificando la proprietà quando si crea il controller, simile al seguente:

```csharp
var myController = new DialogViewController (root, true){
        Style = UITableViewStyle.Grouped;
    }
```

Per ulteriori informazioni avanzate delle personalizzazioni del `DialogViewController`, ad esempio l'impostazione allo sfondo, si farebbe sottoclasse ed eseguire l'override appropriate metodi, come illustrato nell'esempio riportato di seguito:

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

Un altro punto di personalizzazione è dei seguenti metodi virtuali di `DialogViewController`:

```csharp
public override Source CreateSizingSource (bool unevenRows)
```

Questo metodo deve restituire una sottoclasse di `DialogViewController.Source` per i casi in cui vengono ridimensionate in modo uniforme alle celle o una sottoclasse di `DialogViewController.SizingSource` se le celle sono dispari.

Questa sostituzione consente di acquisire uno del `UITableViewSource` metodi. Ad esempio, [TweetStation](https://github.com/migueldeicaza/TweetStation) lo utilizza per rilevare quando l'utente ha eseguito lo scorrimento verso l'alto e aggiornare di conseguenza il numero di tweet da leggere.

## <a name="validation"></a>Convalida

Gli elementi non forniscono la convalida se stessi come i modelli che sono particolarmente adatti per le pagine web e applicazioni desktop non eseguano il mapping direttamente al modello di interazione iPhone.

Se si desidera eseguire la convalida dei dati, eseguire questa operazione quando l'utente attiva di un'azione con i dati immessi. Ad esempio un <span class="ui">eseguita</span> o <span class="ui">Avanti</span> pulsante sulla barra degli strumenti superiore o alcuni `StringElement` utilizzato come un pulsante per passare alla fase successiva.

Si tratta in cui eseguire la convalida di input di base, e probabilmente più complessa di convalida, ad esempio la verifica della validità di una combinazione di utente/password con un server.

Modalità con cui si notifica all'utente di un errore è specifico dell'applicazione. È possibile visualizzare una un `UIAlertView` o visualizzare un suggerimento.

## <a name="summary"></a>Riepilogo

In questo articolo illustrate numerose informazioni su MonoTouch.Dialog. Le nozioni di base di come è stato illustrato MT D funziona e trattati i vari componenti che costituiscono MT D. È stato inoltre l'ampia gamma di elementi e le personalizzazioni di tabella supportate da MT D e illustrato come MT D può essere esteso con gli elementi personalizzati. Inoltre è illustrato il supporto JSON in MT D che consente la creazione di elementi in modo dinamico da JSON.


## <a name="related-links"></a>Collegamenti correlati

- [Screencast - Miguel de Icaza crea una schermata di accesso di iOS con MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [Screencast - creare con facilità iOS interfacce utente con MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [Procedura dettagliata: Creazione di un'applicazione mediante l'API Elements](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Procedura dettagliata: Creazione di un'applicazione mediante l'API Reflection](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Procedura dettagliata: Uso di un elemento JSON per creare un'interfaccia utente](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Markup MonoTouch.Dialog JSON](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Finestra di dialogo MonoTouch su Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Riferimento alla classe UITableViewController](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Riferimento alla classe UINavigationController](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
