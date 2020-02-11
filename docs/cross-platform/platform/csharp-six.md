---
title: C#6 panoramica sulle nuove funzionalità
description: La versione 6 del C# linguaggio continua a evolvere il linguaggio in modo da avere un codice meno standard, una maggiore chiarezza e una maggiore coerenza. Sintassi di inizializzazione più pulita, la possibilità di usare await nei blocchi catch/finally e la condizionale null? gli operatori sono particolarmente utili.
ms.prod: xamarin
ms.assetid: 4B4E41A8-68BA-4E2B-9539-881AC19971B
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 1db7ee95ec261739463fa2584f4acf493ac71217
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014824"
---
# <a name="c-6-new-features-overview"></a>C#6 panoramica sulle nuove funzionalità

_La versione 6 del C# linguaggio continua a evolvere il linguaggio in modo da avere un codice meno standard, una maggiore chiarezza e una maggiore coerenza. Sintassi di inizializzazione più pulita, la possibilità di usare await nei blocchi catch/finally e la condizionale null? gli operatori sono particolarmente utili._

> [!NOTE]
> Per informazioni sulla versione più recente del C# linguaggio-versione 7, vedere l'articolo relativo alle [novità di 7,0 C# ](/dotnet/csharp/whats-new/csharp-7)

In questo documento vengono presentate le nuove C# funzionalità di 6. È completamente supportato dal compilatore mono e gli sviluppatori possono iniziare a usare le nuove funzionalità in tutte le piattaforme di destinazione Xamarin.

> [!VIDEO https://youtube.com/embed/7UdV7zGPfMU]

**Novità di C# 6 video**

## <a name="using-c-6"></a>Uso C# di 6

Il C# compilatore 6 viene usato in tutte le versioni recenti di Visual Studio per Mac.
Quelli che usano i compilatori della riga di comando devono confermare che `mcs --version` restituisce 4,0 o versione successiva.
Visual Studio per Mac utenti possono verificare se è installato mono 4 (o versione successiva) facendo riferimento a **circa Visual Studio per Mac > Visual Studio per Mac > visualizzare i dettagli**.

## <a name="less-boilerplate"></a>Meno standard
### <a name="using-static"></a>using static
Le enumerazioni e alcune classi, ad esempio `System.Math`, sono principalmente titolari di valori e funzioni statici. In C# 6 è possibile importare tutti i membri statici di un tipo con una singola istruzione`using static`. Confrontare una funzione trigonometrica tipica in C# 5 e C# 6:

```csharp
// Classic C#
class MyClass
{
    public static Tuple<double,double> SolarAngleOld(double latitude, double declination, double hourAngle)
    {
        var tmp = Math.Sin (latitude) * Math.Sin (declination) + Math.Cos (latitude) * Math.Cos (declination) * Math.Cos (hourAngle);
        return Tuple.Create (Math.Asin (tmp), Math.Acos (tmp));
    }
}

// C# 6
using static System.Math;

class MyClass
{
    public static Tuple<double, double> SolarAngleNew(double latitude, double declination, double hourAngle)
    {
        var tmp = Asin (latitude) * Sin (declination) + Cos (latitude) * Cos (declination) * Cos (hourAngle);
        return Tuple.Create (Asin (tmp), Acos (tmp));
    }
}
```

`using static` non rende i campi `const` pubblici, ad esempio `Math.PI` e `Math.E`, direttamente accessibili:

```csharp
for (var angle = 0.0; angle <= Math.PI * 2.0; angle += Math.PI / 8) ... 
//PI is const, not static, so requires Math.PI
```

### <a name="using-static-with-extension-methods"></a>utilizzo di static con i metodi di estensione

La funzione `using static` funziona in modo leggermente diverso con i metodi di estensione. Sebbene i metodi di estensione vengano scritti usando `static`, non hanno senso senza un'istanza in cui operare. Quindi, quando `using static` viene usato con un tipo che definisce i metodi di estensione, i metodi di estensione diventano disponibili nel tipo di destinazione (il tipo di `this` del metodo). Ad esempio, è possibile usare `using static System.Linq.Enumerable` per estendere l'API di `IEnumerable<T>` oggetti senza includere tutti i tipi LINQ:

```csharp
using static System.Linq.Enumerable;
using static System.String;

class Program
{
    static void Main()
    {
        var values = new int[] { 1, 2, 3, 4 };
        var evenValues = values.Where (i => i % 2 == 0);
        System.Console.WriteLine (Join(",", evenValues));
    }
}
```

Nell'esempio precedente viene illustrata la differenza nel comportamento: il metodo di estensione `Enumerable.Where` è associato alla matrice, mentre il metodo statico `String.Join` può essere chiamato senza riferimento al tipo di `String`.

### <a name="nameof-expressions"></a>Espressioni NameOf
A volte si vuole fare riferimento al nome a cui è stata assegnata una variabile o un campo. In C# 6`nameof(someVariableOrFieldOrType)`restituirà la stringa`"someVariableOrFieldOrType"`. Ad esempio, quando si genera un `ArgumentException` è molto probabile che si desideri assegnare un nome a un argomento non valido:

```csharp
throw new ArgumentException ("Problem with " + nameof(myInvalidArgument))
```

Il vantaggio principale delle espressioni `nameof` è che sono controllati dal tipo e sono compatibili con il refactoring basato su strumenti. Il controllo dei tipi delle espressioni `nameof` è particolarmente utile nelle situazioni in cui viene usata una `string` per associare dinamicamente i tipi. Ad esempio, in iOS viene usato un `string` per specificare il tipo usato per il prototipo di oggetti `UITableViewCell` in un `UITableView`. `nameof` possibile garantire che questa associazione non abbia esito negativo a causa di un refactoring ortografico o sciatto:

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (nameof(CellTypeA), indexPath);
    cell.TextLabel.Text = objects [indexPath.Row].ToString ();
    return cell;
}
```

Sebbene sia possibile passare un nome completo a `nameof`, viene restituito solo l'elemento finale (dopo l'ultimo `.`). Ad esempio, è possibile aggiungere un data binding in Xamarin.Forms:

```csharp
var myReactiveInstance = new ReactiveType ();
var myLabelOld.BindingContext = myReactiveInstance;
var myLabelNew.BindingContext = myReactiveInstance;
var myLabelOld.SetBinding (Label.TextProperty, "StringField");
var myLabelNew.SetBinding (Label.TextProperty, nameof(ReactiveType.StringField));
```

Le due chiamate a `SetBinding` passano valori identici: `nameof(ReactiveType.StringField)` è `"StringField"`, non `"ReactiveType.StringField"` come previsto inizialmente.

## <a name="null-conditional-operator"></a>Operatore condizionale null
Gli aggiornamenti precedenti C# a hanno introdotto i concetti di tipi nullable e l'operatore di unione null`??`per ridurre la quantità di codice standard quando si gestiscono i valori nullable. C#6 continua questo tema con l'`?.`"operatore condizionale null". Quando viene utilizzato su un oggetto sul lato destro di un'espressione, l'operatore condizionale null restituisce il valore del membro se l'oggetto non è `null` e `null` in caso contrario:

```csharp
var ss = new string[] { "Foo", null };
var length0 = ss [0]?.Length; // 3
var length1 = ss [1]?.Length; // null
var lengths = ss.Select (s => s?.Length ?? 0); //[3, 0]
```

(Sia `length0` che `length1` vengono dedotti come di tipo `int?`)

Nell'ultima riga dell'esempio precedente viene illustrato l'operatore condizionale null `?` in combinazione con l'operatore di Unione null di `??`. Il nuovo C# operatore condizionale null di 6 restituisce`null`sul secondo elemento della matrice. a quel punto, l'operatore di Unione null viene riavviato e fornisce un valore 0 alla matrice di`lengths`(se è appropriato o meno, ovviamente, specifico del problema).

L'operatore condizionale null dovrebbe ridurre notevolmente la quantità di controllo null standard necessario in molte applicazioni.

Esistono alcune limitazioni per l'operatore condizionale null a causa di ambiguità. Non è possibile seguire immediatamente una `?` con un elenco di argomenti tra parentesi, come si può sperare con un delegato:

```csharp
SomeDelegate?("Some Argument") // Not allowed
```

Tuttavia, `Invoke` può essere usato per separare l'`?` dall'elenco di argomenti ed è ancora un miglioramento contrassegnato rispetto a un blocco di controllo del `null`di standard:

```csharp
public event EventHandler HandoffOccurred;
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    HandoffOccurred?.Invoke (this, userActivity.UserInfo);
    return true;
}
```

## <a name="string-interpolation"></a>Interpolazione di stringhe
La funzione `String.Format` usa tradizionalmente gli indici come segnaposto nella stringa di formato, ad esempio `String.Format("Expected: {0} Received: {1}.", expected, received`). Naturalmente, l'aggiunta di un nuovo valore ha sempre compreso una piccola attività fastidiosa per contare gli argomenti, rinumerare i segnaposto e inserire il nuovo argomento nella sequenza corretta nell'elenco degli argomenti.

C#la nuova funzionalità di interpolazione delle stringhe di 6 è notevolmente migliorata in `String.Format`. A questo punto, è possibile denominare direttamente le variabili in una stringa preceduta da un `$`. Ad esempio:

```csharp
$"Expected: {expected} Received: {received}."
```

Le variabili sono, ovviamente, controllate e una variabile non digitata correttamente o non disponibile provocherà un errore del compilatore.

I segnaposto non devono essere semplici variabili, ma possono essere qualsiasi espressione. All'interno di questi segnaposto, è possibile utilizzare le virgolette *senza* eseguire l'escape di tali quote. Si noti, ad esempio, il `"s"` nei seguenti elementi:

```csharp
var s = $"Timestamp: {DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}"
```

L'interpolazione di stringhe supporta la sintassi di allineamento e formattazione dei `String.Format`. Così come è stato scritto in precedenza `{index, alignment:format}`C# , in 6 scrivere`{placeholder, alignment:format}`:

```csharp
using static System.Linq.Enumerable;
using System;

class Program
{
    static void Main ()
    {
        var values = new int[] { 1, 2, 3, 4, 12, 123456 };
        foreach (var s in values.Select (i => $"The value is { i,10:N2}.")) {
            Console.WriteLine (s);
        }
    Console.WriteLine ($"Minimum is { values.Min(i => i):N2}.");
    }
}
```

Risultati in:

```
The value is       1.00.
The value is       2.00.
The value is       3.00.
The value is       4.00.
The value is      12.00.
The value is 123,456.00.
Minimum is 1.00.
```

L'interpolazione di stringhe è un Sugar sintattico per `String.Format`: non può essere usata con `@""` valori letterali stringa e non è compatibile con `const`, anche se non viene usato alcun segnaposto:

```csharp
const string s = $"Foo"; //Error : const requires value
```

Nel caso di utilizzo comune della creazione di argomenti della funzione con l'interpolazione di stringhe, è comunque necessario prestare attenzione all'Escape, alla codifica e ai problemi relativi alle impostazioni cultura. Le query SQL e URL sono, ovviamente, cruciali per la purificazione. Come con `String.Format`, l'interpolazione di stringhe usa il `CultureInfo.CurrentCulture`. L'uso di `CultureInfo.InvariantCulture` è un po' più Wordy:

```csharp
Thread.CurrentThread.CurrentCulture  = new CultureInfo ("de");
Console.WriteLine ($"Today is: {DateTime.Now}"); //"21.05.2015 13:52:51"
Console.WriteLine ($"Today is: {DateTime.Now.ToString(CultureInfo.InvariantCulture)}"); //"05/21/2015 13:52:51"
```

## <a name="initialization"></a>Inizializzazione

C#6 offre una serie di metodi concisi per specificare proprietà, campi e membri.

### <a name="auto-property-initialization"></a>Inizializzazione proprietà automatica

Le proprietà automatiche possono ora essere inizializzate nello stesso modo conciso dei campi. Le proprietà automatiche non modificabili possono essere scritte solo con un getter:

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
```

Nel costruttore è possibile impostare il valore di una proprietà automatica solo Get:

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
    public string Description { get; }

    public ToDo (string description)
    {
        this.Description = description; //Can assign (only in constructor!)
    }
```

Questa inizializzazione delle proprietà automatiche è una funzionalità di risparmio di spazio generale e un vantaggio per gli sviluppatori che desiderano evidenziare l'immutabilità negli oggetti.

### <a name="index-initializers"></a>Inizializzatori di indice.

C#6 introduce gli inizializzatori di indice che consentono di impostare la chiave e il valore nei tipi che hanno un indicizzatore. Questa operazione è in genere destinata alle strutture di dati di tipo `Dictionary`:

```csharp
partial void ActivateHandoffClicked (WatchKit.WKInterfaceButton sender)
{
    var userInfo = new NSMutableDictionary {
        ["Created"] = NSDate.Now,
        ["Due"] = NSDate.Now.AddSeconds(60 * 60 * 24),
        ["Task"] = Description
    };
    UpdateUserActivity ("com.xamarin.ToDo.edit", userInfo, null);
    statusLabel.SetText ("Check phone");
}
```

### <a name="expression-bodied-function-members"></a>Membri di funzione con corpo di espressione

Le funzioni lambda presentano diversi vantaggi, una delle quali è semplicemente il salvataggio dello spazio. Analogamente, i membri della classe con corpo di espressione consentono di esprimere alcune piccole funzioni in modo più conciso rispetto a quanto fosse C# possibile nelle versioni precedenti di 6.

I membri di funzione con corpo di espressione usano la sintassi delle frecce lambda invece della sintassi di blocco tradizionale:

```csharp
public override string ToString () => $"{FirstName} {LastName}";
```

Si noti che la sintassi lambda-Arrow non usa un `return`esplicito. Per le funzioni che restituiscono `void`, l'espressione deve essere anche un'istruzione:

```csharp
public void Log(string message) => System.Console.WriteLine($"{DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}: {message}");
```

I membri con corpo di espressione sono ancora soggetti alla regola che `async` è supportata per i metodi ma non per le proprietà:

```csharp
//A method, so async is valid
public async Task DelayInSeconds(int seconds) => await Task.Delay(seconds * 1000);
//The following property will not compile
public async Task<int> LeisureHours => await Task.FromResult<char> (DateTime.Now.DayOfWeek.ToString().First()) == 'S' ? 16 : 5;
```

## <a name="exceptions"></a>Eccezioni

Non esistono due modi: la gestione delle eccezioni è difficile da ottenere. Le nuove funzionalità C# di 6 rendono la gestione delle eccezioni più flessibile e coerente.

### <a name="exception-filters"></a>Filtri eccezioni

Per definizione, le eccezioni si verificano in circostanze insolite e possono essere molto difficili da motivare e scrivere codice su *tutti* i modi in cui può verificarsi un'eccezione di un determinato tipo. C#6 introduce la possibilità di proteggere un gestore di esecuzione con un filtro valutato in fase di esecuzione. Questa operazione viene eseguita aggiungendo un modello di `when (bool)` dopo la normale dichiarazione di `catch(ExceptionType)`. Nell'esempio seguente un filtro distingue un errore di analisi correlato al parametro `date`, anziché ad altri errori di analisi.

```csharp
public void ExceptionFilters(string aFloat, string date, string anInt)
{
    try
    {
        var f = Double.Parse(aFloat);
        var d = DateTime.Parse(date);
        var n = Int32.Parse(anInt);
    } catch (FormatException e) when (e.Message.IndexOf("DateTime") > -1) {
        Console.WriteLine ($"Problem parsing \"{nameof(date)}\" argument");
    } catch (FormatException x) {
        Console.WriteLine ("Problem parsing some other argument");
    }
}
```

### <a name="await-in-catchfinally"></a>await nel catch... Infine...

Le funzionalità di `async` introdotte C# in 5 sono state un cambio di gioco per il linguaggio. In C# 5`await`non era consentito nei blocchi`catch`e`finally`, un fastidio dato il valore della funzionalità`async/await`. C#6 rimuove questa limitazione, consentendo l'attesa di risultati asincroni in modo coerente tramite il programma, come illustrato nel frammento di codice seguente:

```csharp
async void SomeMethod()
{
    try {
        //...etc...
    } catch (Exception x) {
        var diagnosticData = await GenerateDiagnosticsAsync (x);
        Logger.log (diagnosticData);
    } finally {
        await someObject.FinalizeAsync ();
    }
}
```

## <a name="summary"></a>Riepilogo

Il C# linguaggio continua ad evolversi per rendere gli sviluppatori più produttivi, migliorando allo stesso tempo le procedure consigliate e il supporto degli strumenti. In questo documento è stata fornita una panoramica delle nuove funzionalità del C# linguaggio in 6 ed è stato illustrato brevemente il modo in cui vengono usate.

## <a name="related-links"></a>Collegamenti correlati

- [Nuove funzionalità del linguaggio C# in 6](https://github.com/dotnet/roslyn/wiki/New-Language-Features-in-C%23-6)
