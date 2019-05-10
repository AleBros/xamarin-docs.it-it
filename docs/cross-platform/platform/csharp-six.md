---
title: C#Panoramica sulle nuove funzionalità di 6
description: La versione più recente del C# language – versione 6 – continua ad evolvere il linguaggio in modo da avere meno boilerplate, maggiore chiarezza e coerenza delle altre. La sintassi più chiara di inizializzazione, la possibilità di usare await nei blocchi catch/finally e il condizionale null? operatore sono particolarmente utili.
ms.prod: xamarin
ms.assetid: 4B4E41A8-68BA-4E2B-9539-881AC19971B
ms.custom: xamu-video
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 1a261c009f105994ac0b9c84f3d0ac037c4a3517
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2019
ms.locfileid: "64978495"
---
# <a name="c-6-new-features-overview"></a>C#Panoramica sulle nuove funzionalità di 6

_La versione più recente del C# language – versione 6 – continua ad evolvere il linguaggio in modo da avere meno boilerplate, maggiore chiarezza e coerenza delle altre. La sintassi più chiara di inizializzazione, la possibilità di usare await nei blocchi catch/finally e il condizionale null? operatore sono particolarmente utili._

Questo documento introduce le nuove funzionalità di C# 6. È completamente supportato dal compilatore mono e gli sviluppatori possono iniziare a usare le nuove funzionalità tra tutte le piattaforme di destinazione di Xamarin.

> [!VIDEO https://youtube.com/embed/7UdV7zGPfMU]

**Novità in C# video di 6**

## <a name="using-c-6"></a>Usando C# 6

Il C# compilatore 6 viene utilizzato in tutte le versioni recenti di Visual Studio per Mac.
Coloro che utilizzano i compilatori della riga di comando verificare che `mcs --version` restituisce 4.0 o versione successive.
Visual Studio per gli utenti Mac è possibile controllare se hanno Mono 4 (o versione successiva) installato facendo riferimento alla **informazioni su Visual Studio per Mac > Visual Studio per Mac > Mostra dettagli**.

## <a name="less-boilerplate"></a>Meno Boilerplate
### <a name="using-static"></a>using static
Enumerazioni e alcune classi, ad esempio `System.Math`, sono principalmente i titolari di valori statici e funzioni. In C# 6, è possibile importare tutti i membri statici di un tipo con una singola `using static` istruzione. Confrontare una tipica funzione trigonometriche in C# 5 e C# 6:

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

`using static` non rendere pubblici `const` campi, ad esempio `Math.PI` e `Math.E`direttamente accessibile:

```csharp
for (var angle = 0.0; angle <= Math.PI * 2.0; angle += Math.PI / 8) ... 
//PI is const, not static, so requires Math.PI
```

### <a name="using-static-with-extension-methods"></a>uso di statico con i metodi di estensione

Il `using static` funzionalità varia leggermente con metodi di estensione. Sebbene i metodi di estensione vengono scritti utilizzando `static`, non ha senso senza un'istanza su cui operare. Quando `using static` viene usato con un tipo che definisce i metodi di estensione, i metodi di estensione diventi disponibili nel relativo tipo di destinazione (il metodo `this` tipo). Ad esempio, `using static System.Linq.Enumerable` possono essere utilizzate per estendere l'API di `IEnumerable<T>` oggetti senza mai in tutti i tipi LINQ:

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

L'esempio precedente dimostra la differenza nel comportamento: il metodo di estensione `Enumerable.Where` associata con la matrice, mentre il metodo statico `String.Join` può essere chiamato senza riferimento al `String` tipo.

### <a name="nameof-expressions"></a>Espressioni nameof
In alcuni casi, si vuole fare riferimento al nome è stato concesso una variabile o campo. In C# 6 `nameof(someVariableOrFieldOrType)` restituirà la stringa `"someVariableOrFieldOrType"`. Ad esempio, quando si genera un `ArgumentException` si finirà per assegnare un nome quale argomento non è valido:

```csharp
throw new ArgumentException ("Problem with " + nameof(myInvalidArgument))
```

Il vantaggio principale di `nameof` espressioni è che sono controllo del tipo e sono compatibili con lo strumento consumo di refactoring. Il controllo del tipo di `nameof` espressioni è particolarmente importante per noi in situazioni in cui un `string` viene usato per associare in modo dinamico tipi. Ad esempio, in iOS un `string` viene usato per specificare il tipo utilizzato per prototipi `UITableViewCell` gli oggetti in un `UITableView`. `nameof` può garantire che tale associazione non presenti errori a causa di un errore di ortografia o refactoring scrivevano:

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (nameof(CellTypeA), indexPath);
    cell.TextLabel.Text = objects [indexPath.Row].ToString ();
    return cell;
}
```

Sebbene sia possibile passare un nome completo `nameof`, solo l'ultimo elemento (dopo l'ultimo `.`) viene restituito. Ad esempio, è possibile aggiungere un data binding in xamarin. Forms:

```csharp
var myReactiveInstance = new ReactiveType ();
var myLabelOld.BindingContext = myReactiveInstance;
var myLabelNew.BindingContext = myReactiveInstance;
var myLabelOld.SetBinding (Label.TextProperty, "StringField");
var myLabelNew.SetBinding (Label.TextProperty, nameof(ReactiveType.StringField));
```

Le due chiamate a `SetBinding` stanno passando valori identici: `nameof(ReactiveType.StringField)` viene `"StringField"`, non `"ReactiveType.StringField"` come inizialmente è prevedibile.

## <a name="null-conditional-operator"></a>Operatore condizionale null
Aggiornamenti precedenti a C# sono stati introdotti i concetti di tipi nullable e l'operatore null-coalescing `??` per ridurre la quantità di codice boilerplate durante la gestione di valori nullable. C#6 continua questo tema con i "operatore condizionale null" `?.`. Quando usata in un oggetto sul lato destro di un'espressione, l'operatore condizionale null restituisce il valore del membro se l'oggetto non è `null` e `null` in caso contrario:

```csharp
var ss = new string[] { "Foo", null };
var length0 = ss [0]?.Length; // 3
var length1 = ss [1]?.Length; // null
var lengths = ss.Select (s => s?.Length ?? 0); //[3, 0]
```

(Entrambe `length0` e `length1` inferiti sia di tipo `int?`)

L'ultima riga nell'esempio precedente viene illustrato il `?` operatore condizionale null in combinazione con il `??` operatore null-coalescing. Il nuovo C# 6 operatore condizionale null restituisce `null` sull'elemento 2 nella matrice, a quel punto l'operatore null-coalescing interviene e fornisce un 0 e il `lengths` matrici (indipendentemente che sia appropriato non è, ovviamente, problema specifico).

L'operatore condizionale null estremamente deve ridurre la quantità di necessario Check null boilerplate in molte applicazioni.

Esistono alcune limitazioni per l'operatore condizionale null a causa dell'ambiguità. Non è possibile seguire immediatamente una `?` con un elenco di argomenti tra parentesi, come si potrebbe auguriamo a che fare con un delegato:

```csharp
SomeDelegate?("Some Argument") // Not allowed
```

Tuttavia `Invoke` può essere utilizzato per separare il `?` dall'elenco di argomenti e che sia ancora un miglioramento contrassegnato tramite un `null`-controllo blocco di testo standard:

```csharp
public event EventHandler HandoffOccurred;
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    HandoffOccurred?.Invoke (this, userActivity.UserInfo);
    return true;
}
```

## <a name="string-interpolation"></a>Interpolazione di stringhe
Il `String.Format` funzione è tradizionalmente usato indici come segnaposto nella stringa di formato, ad esempio, `String.Format("Expected: {0} Received: {1}.", expected, received`). Naturalmente, aggiunta di un nuovo valore sempre ha comportato un'attività di poco fastidiosa del conteggio di argomenti rinumerazione segnaposto e inserire il nuovo argomento nella sequenza corretta nell'elenco di argomenti.

C#6 della nuova funzionalità di interpolazione di stringhe migliora notevolmente `String.Format`. A questo punto, è possibile assegnare direttamente le variabili in una stringa con preceduta con un `$`. Ad esempio:

```csharp
$"Expected: {expected} Received: {received}."
```

Le variabili vengono, naturalmente, archiviate e una variabile contenente errori di ortografia o non a disponibilità causerà un errore del compilatore.

I segnaposto non deve necessariamente essere le variabili semplici, possono essere qualsiasi espressione. All'interno di questi segnaposto, è possibile utilizzare le virgolette *senza* escape tali offerte. Ad esempio, si noti il `"s"` nell'esempio seguente:

```csharp
var s = $"Timestamp: {DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}"
```

L'interpolazione delle stringhe supporta l'allineamento e la sintassi di formattazione di `String.Format`. Esattamente come è scritto in precedenza `{index, alignment:format}`, in C# 6 è scrivere `{placeholder, alignment:format}`:

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
risultati in:

    The value is       1.00.
    The value is       2.00.
    The value is       3.00.
    The value is       4.00.
    The value is      12.00.
    The value is 123,456.00.
    Minimum is 1.00.

Interpolazione di stringhe è zucchero sintattico `String.Format`: non può essere utilizzata con `@""` valori letterali stringa e non è compatibile con `const`, anche se nessun segnaposto vengono usati:

```csharp
const string s = $"Foo"; //Error : const requires value
```

Nel caso di utilizzo comune della creazione di argomenti della funzione con l'interpolazione di stringhe, è necessario prestare particolare attenzione escape, la codifica e i problemi delle impostazioni cultura. Le query SQL e l'URL sono ovviamente fondamentali per purificare. Come per gli `String.Format`, Usa l'interpolazione di stringhe di `CultureInfo.CurrentCulture`. Usando `CultureInfo.InvariantCulture` è leggermente più prolisso:

```csharp
Thread.CurrentThread.CurrentCulture  = new CultureInfo ("de");
Console.WriteLine ($"Today is: {DateTime.Now}"); //"21.05.2015 13:52:51"
Console.WriteLine ($"Today is: {DateTime.Now.ToString(CultureInfo.InvariantCulture)}"); //"05/21/2015 13:52:51"
```

## <a name="initialization"></a>Inizializzazione

C#6 fornisce una serie di modi concisi per specificare le proprietà, campi e i membri.

### <a name="auto-property-initialization"></a>Inizializzazione di proprietà automatiche

Proprietà automatiche possono ora essere inizializzate nello stesso modo conciso come campi. Proprietà automatiche non modificabile possono essere scritti con solo un metodo di richiamo:

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
```

Nel costruttore, è possibile impostare il valore di una proprietà solo Get automatica:

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

Questa inizializzazione di proprietà automatiche è una funzionalità di risparmio di spazio generale sia un enorme vantaggio per sviluppatori che vogliono evidenziare la non modificabilità nei relativi oggetti.

### <a name="index-initializers"></a>Inizializzatori di indice.

C#6 introduce gli inizializzatori di indice, che consentono di impostare il valore sia la chiave in tipi che dispongono di un indicizzatore. Si tratta in genere per `Dictionary`-applicare uno stile a strutture di dati:

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

### <a name="expression-bodied-function-members"></a>Membri con corpo di espressione (funzione)

Le funzioni lambda offrono alcuni vantaggi, è sufficiente una delle quali è risparmiare spazio. Analogamente, membri con corpo di espressione della classe consentono le funzioni piccole esprimere un po' più breve rispetto a quanto accadeva nelle versioni precedenti di C# 6.

I membri con corpo di espressione di funzione utilizzano la sintassi freccia lambda anziché la sintassi tradizionale blocco:

```csharp
public override string ToString () => $"{FirstName} {LastName}";
```

Si noti che la sintassi lambda freccia non viene utilizzata l'impostazione esplicita `return`. Per le funzioni che restituiscono `void`, l'espressione deve anche essere un'istruzione:

```csharp
public void Log(string message) => System.Console.WriteLine($"{DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}: {message}");
```

I membri con corpo di espressione sono comunque soggetti alla regola che `async` è supportata per i metodi, ma non le proprietà:

```csharp
//A method, so async is valid
public async Task DelayInSeconds(int seconds) => await Task.Delay(seconds * 1000);
//The following property will not compile
public async Task<int> LeisureHours => await Task.FromResult<char> (DateTime.Now.DayOfWeek.ToString().First()) == 'S' ? 16 : 5;
```

## <a name="exceptions"></a>Eccezioni

Non ci sono modi Nessuna due informazioni: gestione delle eccezioni è complessa. Nuove funzionalità di C# 6 rendere la gestione delle eccezioni più flessibile e coerente.

### <a name="exception-filters"></a>Filtri eccezioni

Per definizione, le eccezioni si verificano in circostanze non comuni e può essere molto difficile motivo e il codice sui *tutti* i modi in cui potrebbe verificarsi un'eccezione di un determinato tipo. C#6 introduce la possibilità di evitare un gestore di esecuzione con un filtro valutata runtime. Questa operazione viene eseguita mediante l'aggiunta di un `when (bool)` pattern dopo la normale `catch(ExceptionType)` dichiarazione. Nell'esempio seguente, un filtro consente di distinguere un errore di analisi relativi al `date` parametro anziché altri errori di analisi.

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

### <a name="await-in-catchfinally"></a>Await nei catch... finally...

Il `async` le funzionalità introdotte in C# 5 sono stati un cambiamento radicale per il linguaggio. In C# 5, `await` non era prevista nelle `catch` e `finally` si blocca, una seccatura assegnata il valore di `async/await` funzionalità. C#6 rimuove questa limitazione, che consente i risultati asincroni attesa in modo coerente attraverso il programma come illustrato nel frammento di codice seguente:

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

Il C# linguaggio continua ad evolvere per rendere più produttivi gli sviluppatori, anche innalzamento di livello di procedure consigliate e gli strumenti di supporto. Questo documento ha presentato una panoramica delle nuove funzionalità del linguaggio in C# 6 e ha illustrato brevemente come vengono usati.

## <a name="related-links"></a>Collegamenti correlati

- [Le funzioni del linguaggio nuovi C# 6](https://github.com/dotnet/roslyn/wiki/New-Language-Features-in-C%23-6)

