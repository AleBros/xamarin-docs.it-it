---
title: 6 nuove funzionalità di c# Panoramica
description: La versione più recente del linguaggio c# – versione 6 – continua evoluzione del linguaggio che sono meno boilerplate, maggiore chiarezza e maggiore consistenza. Pulitura memoria sintassi di inizializzazione, la possibilità di utilizzare await nei blocchi catch/finally e condizionale null? operatore risultano particolarmente utili.
ms.prod: xamarin
ms.assetid: 4B4E41A8-68BA-4E2B-9539-881AC19971B
ms.custom: xamu-video
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: fffb78d6f2599c231617ed025d41282beffd319b
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066455"
---
# <a name="c-6-new-features-overview"></a>6 nuove funzionalità di c# Panoramica

_La versione più recente del linguaggio c# – versione 6 – continua evoluzione del linguaggio che sono meno boilerplate, maggiore chiarezza e maggiore consistenza. Pulitura memoria sintassi di inizializzazione, la possibilità di utilizzare await nei blocchi catch/finally e condizionale null? operatore risultano particolarmente utili._

Questo documento vengono presentate le nuove funzionalità di c# 6. È completamente supportato dal compilatore mono e gli sviluppatori possono iniziare a usare le nuove funzionalità in tutte le piattaforme di destinazione di Xamarin.

In questo articolo include frammenti brevi del codice c# 6 che illustrano l'uso di base.
L'applicazione di esempio è un programma della riga di comando che viene eseguito in tutte le piattaforme di destinazione di Xamarin ed esercitazioni le varie funzionalità.


> [!VIDEO https://youtube.com/embed/7UdV7zGPfMU]

**Novità in c# 6, da [University Xamarin](https://university.xamarin.com/)**


## <a name="development-environment"></a>Ambiente di sviluppo

### <a name="mac"></a>Mac

* **Visual Studio per Mac** dispone del supporto per c# 6: è possibile generare e compilare le app Xamarin mediante le funzionalità di c# 6.
  Ulteriori informazioni, vedere [Visual Studio per Mac](https://docs.microsoft.com/visualstudio/mac/).

### <a name="windows"></a>WINDOWS

* **Visual Studio 2015 e 2017** e versioni successive è disponibile supporto completo per c# 6. Le versioni precedenti di Visual Studio non supporterà c# 6.

* **Xamarin Studio per Windows** non supporta le funzionalità di c# 6 nell'editor.



## <a name="compiler"></a>Compilatore

Il compilatore Mono c# 6 è incluso in Mono 4.0 e versioni successiva, ovvero [disponibile gratuitamente per il download](http://www.mono-project.com/download/).
Visual Studio per Mac Aggiorna automaticamente l'installazione Mono nel sistema.

Gli utenti di Windows è necessario [Visual Studio 2015 o 2017 ^](https://visualstudio.microsoft.com/) installato per compilare codice c# 6 (anche se si sceglie Xamarin Studio per Windows come l'IDE).

^ o *[Microsoft compilare strumenti 2015](http://www.microsoft.com/download/details.aspx?id=48159)* per comando riga compilazione o i server di compilazione, ad esempio.

## <a name="using-c-6"></a>Utilizzo di C# 6

Il compilatore c# 6 viene utilizzato in tutte le versioni recenti di Visual Studio per Mac.
Quelli che utilizzano i compilatori della riga di comando deve confermare che `mcs --version` restituisce 4.0 o versione successive.
Visual Studio per gli utenti Mac è possibile controllare se hanno Mono 4 (o versione successiva) installato tramite un riferimento a **informazioni su Visual Studio per Mac > Visual Studio per Mac > Mostra dettagli**.

## <a name="less-boilerplate"></a>Meno Boilerplate
### <a name="using-static"></a>using static
Le enumerazioni e alcune classi, ad esempio `System.Math`, sono principalmente i titolari di valori statici e funzioni. In c# 6, è possibile importare tutti i membri statici di un tipo con un singolo `using static` istruzione. Confrontare una funzione trigonometrica tipica in c# 5 e 6 di c#:

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

`using static` non rendere pubblici `const` campi, ad esempio `Math.PI` e `Math.E`, direttamente accessibile:

```csharp
for (var angle = 0.0; angle <= Math.PI * 2.0; angle += Math.PI / 8) ... 
//PI is const, not static, so requires Math.PI
```

### <a name="using-static-with-extension-methods"></a>utilizzo di statico con i metodi di estensione

Il `using static` funzionalità varia leggermente in con i metodi di estensione. Sebbene i metodi di estensione vengono scritti utilizzando `static`, non si applicano senza un'istanza su cui operare. Quando in questo caso `using static` viene usato con un tipo che definisce i metodi di estensione, i metodi di estensione sono disponibili sul relativo tipo di destinazione (il metodo `this` tipo). Ad esempio `using static System.Linq.Enumerable` possono essere usati per estendere l'API di `IEnumerable<T>` oggetti senza attivare la modalità in tutti i tipi di LINQ:

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

Nell'esempio precedente viene illustrata la differenza nel comportamento: il metodo di estensione `Enumerable.Where` associata con la matrice, mentre il metodo statico `String.Join` può essere chiamato senza riferimento al `String` tipo.

### <a name="nameof-expressions"></a>nameof espressioni
In alcuni casi, si desidera fare riferimento al nome è stato concesso una variabile o campo. Nel linguaggio c# 6 `nameof(someVariableOrFieldOrType)` restituirà la stringa `"someVariableOrFieldOrType"`. Ad esempio, quando si genera un `ArgumentException` si è molto probabile che assegnare un nome quale argomento non è valido:

```csharp
throw new ArgumentException ("Problem with " + nameof(myInvalidArgument))
```

Il vantaggio principale di `nameof` espressioni è che vengono controllati e sono compatibili con profilato strumento refactoring. Il controllo dei tipi di `nameof` espressioni risulta particolarmente importante in situazioni in cui un `string` viene utilizzato per associare in modo dinamico tipi. Ad esempio, in iOS un `string` viene utilizzata per specificare il tipo utilizzato prototipo `UITableViewCell` gli oggetti in un `UITableView`. `nameof` può garantire che l'associazione non avrà esito negativo a causa di un errore di ortografia o refactoring approssimativo:

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (nameof(CellTypeA), indexPath);
    cell.TextLabel.Text = objects [indexPath.Row].ToString ();
    return cell;
}
```

Sebbene sia possibile passare un nome completo `nameof`, solo l'elemento finale (dopo l'ultimo `.`) viene restituito. Ad esempio, è possibile aggiungere un data binding in xamarin. Forms:

```csharp
var myReactiveInstance = new ReactiveType ();
var myLabelOld.BindingContext = myReactiveInstance;
var myLabelNew.BindingContext = myReactiveInstance;
var myLabelOld.SetBinding (Label.TextProperty, "StringField");
var myLabelNew.SetBinding (Label.TextProperty, nameof(ReactiveType.StringField));
```

Le due chiamate a `SetBinding` sta passando valori identici: `nameof(ReactiveType.StringField)` viene `"StringField"`, non `"ReactiveType.StringField"` come inizialmente prevedibile.

## <a name="null-conditional-operator"></a>Operatore condizionale null
Gli aggiornamenti precedenti a c# introdotti i concetti di tipi nullable e l'operatore null-coalescing `??` per ridurre la quantità di codice boilerplate quando la gestione dei valori che ammettono valori null. C# 6 continua questo tema con "operatore condizionali null" `?.`. Quando utilizzato in un oggetto sul lato destro di un'espressione, l'operatore condizionale null restituisce il valore del membro se l'oggetto non è `null` e `null` in caso contrario:

```csharp
var ss = new string[] { "Foo", null };
var length0 = ss [0]?.Length; // 3
var length1 = ss [1]?.Length; // null
var lengths = ss.Select (s => s?.Length ?? 0); //[3, 0]
```

(Entrambi `length0` e `length1` inferiti sia di tipo `int?`)

L'ultima riga illustrato nell'esempio precedente il `?` operatore condizionali null in combinazione con il `??` operatore null-coalescing. Restituisce il nuovo operatore condizionali null c# 6 `null` sull'elemento nella matrice, a quel punto l'operatore null-coalescing interviene e fornisce un 0 e 2 ° il `lengths` matrice (che è appropriato se o se non è, naturalmente, specifici del problema).

L'operatore condizionale null enormemente deve ridurre la quantità di necessario il controllo null boilerplate in molte applicazioni.

Esistono alcune limitazioni sull'operatore condizionali null a causa di ambiguità. Non è possibile seguire immediatamente una `?` con un elenco di argomenti tra parentesi, come si potrebbe auguriamo che riguardano un delegato:

```csharp
SomeDelegate?("Some Argument") // Not allowed
```

Tuttavia, `Invoke` può essere usato per separare il `?` dall'elenco di argomenti e che sia ancora un miglioramento contrassegnato tramite un `null`-controllo blocco di boilerplate:

```csharp
public event EventHandler HandoffOccurred;
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    HandoffOccurred?.Invoke (this, userActivity.UserInfo);
    return true;
}
```

## <a name="string-interpolation"></a>Interpolazione di stringhe
Il `String.Format` funzione è in genere utilizzato indici come segnaposto nella stringa di formato, ad esempio, `String.Format("Expected: {0} Received: {1}.", expected, received`). Naturalmente, aggiunta di un nuovo valore è sempre coinvolta un'attività di poco fastidiosa del conteggio di argomenti, rinumerazione segnaposto e inserire il nuovo argomento in sequenza a destra nell'elenco di argomenti.

Nuova funzione di interpolazione stringa c# 6 migliora notevolmente `String.Format`. A questo punto, è possibile assegnare direttamente le variabili in una stringa con preceduta una `$`. Ad esempio:

```csharp
$"Expected: {expected} Received: {received}."
```

Le variabili, naturalmente, controllate e una variabile contenente errori di ortografia o non disponibile potrebbe causare un errore del compilatore.

I segnaposto non deve necessariamente essere le variabili semplici, possono essere qualsiasi espressione. All'interno di questi segnaposto, è possibile utilizzare le virgolette *senza* escape tali offerte. Ad esempio, si noti il `"s"` nei casi seguenti:

```csharp
var s = $"Timestamp: {DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}"
```

Interpolazione di stringhe supporta l'allineamento e la sintassi di formattazione di `String.Format`. Come scritto in precedenza `{index, alignment:format}`, in c# 6 scriverai `{placeholder, alignment:format}`:

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

Interpolazione di stringhe è sugar sintattici per `String.Format`: non può essere utilizzato con `@""` valori letterali stringa e non è compatibile con `const`, anche se nessun segnaposto vengono utilizzati:

```csharp
const string s = $"Foo"; //Error : const requires value
```

In caso d'uso comune della creazione gli argomenti della funzione con interpolazione di stringhe, è necessario prestare attenzione caratteri di escape, la codifica e i problemi delle impostazioni cultura. Le query SQL e URL sono, naturalmente, fondamentali per purificare. Come con `String.Format`, Usa l'interpolazione di stringhe di `CultureInfo.CurrentCulture`. Utilizzando `CultureInfo.InvariantCulture` è leggermente più prolisso:

```csharp
Thread.CurrentThread.CurrentCulture  = new CultureInfo ("de");
Console.WriteLine ($"Today is: {DateTime.Now}"); //"21.05.2015 13:52:51"
Console.WriteLine ($"Today is: {DateTime.Now.ToString(CultureInfo.InvariantCulture)}"); //"05/21/2015 13:52:51"
```

## <a name="initialization"></a>Inizializzazione

C# 6 fornisce una serie di modi concisi per specificare le proprietà, campi e i membri.

### <a name="auto-property-initialization"></a>Inizializzazione di auto-proprietà

Le proprietà automatiche ora possono essere inizializzate in modo conciso come campi. Auto-proprietà non modificabili possono essere scritti con solo un metodo Get:

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
```

Nel costruttore, è possibile impostare il valore di una proprietà solo Get automatico:

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

L'inizializzazione della proprietà automatica è una funzionalità di risparmio di spazio generale sia un indubbio vantaggio per gli sviluppatori che desiderano evidenziare immutabilità nei rispettivi oggetti.

### <a name="index-initializers"></a>Inizializzatori di indice.

C# 6 introduce gli inizializzatori di indice, che consentono di impostare la chiave e valore nei tipi che dispone di un indicizzatore. In genere, si tratta per `Dictionary`-stile strutture di dati:

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

### <a name="expression-bodied-function-members"></a>Membri con corpo espressione (funzione)

Funzioni lambda presentano vantaggi diversi, uno dei quali è semplicemente risparmiando spazio. Analogamente, i membri della classe densi espressione consentono le piccole funzioni esprimere un po' più conciso rispetto a quanto accadeva nelle versioni precedenti di c# 6.

I membri del corpo di espressione di funzione usare la sintassi freccia lambda anziché la sintassi tradizionale blocco:

```csharp
public override string ToString () => $"{FirstName} {LastName}";
```

Si noti che la sintassi freccia lambda non usi esplicita `return`. Per le funzioni che restituiscono `void`, l'espressione deve essere anche un'istruzione:

```csharp
public void Log(string message) => System.Console.WriteLine($"{DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}: {message}");
```

I membri con corpo espressione sono comunque soggetti alla regola che `async` è supportata per i metodi, ma non le proprietà:

```csharp
//A method, so async is valid
public async Task DelayInSeconds(int seconds) => await Task.Delay(seconds * 1000);
//The following property will not compile
public async Task<int> LeisureHours => await Task.FromResult<char> (DateTime.Now.DayOfWeek.ToString().First()) == 'S' ? 16 : 5;
```

## <a name="exceptions"></a>Eccezioni

Non è nessuna due modi su di esso: è difficile predisporre la gestione delle eccezioni. Nuove funzionalità di c# 6 rendere la gestione delle eccezioni più flessibile e coerente.

### <a name="exception-filters"></a>Filtri eccezioni

Per definizione, le eccezioni si verificano in circostanze non comuni e può essere molto difficile motivo e il codice sul *tutti* modi potrebbe verificarsi un'eccezione di un determinato tipo. C# 6 introduce la possibilità di proteggere un gestore di esecuzione con un filtro a valutato runtime. Questa operazione viene eseguita mediante l'aggiunta di un `when (bool)` pattern dopo il normale `catch(ExceptionType)` dichiarazione. Nell'esempio seguente, un filtro distingue un errore di analisi riguardanti il `date` parametro a differenza di altri errori di analisi.

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

### <a name="await-in-catchfinally"></a>Await in blocco catch... finally...

Il `async` funzionalità introdotte in c# 5 sono state un radicale cambiamento della lingua. In c# 5 `await` non è consentito `catch` e `finally` si blocca, un fastidio assegnato il valore il `async/await` funzionalità. C# 6 rimuove questa limitazione, i risultati asincroni attesa in modo coerente attraverso il programma come illustrato nel frammento riportato di seguito:

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

Linguaggio c# continua evoluzione per rendere gli sviluppatori più produttivi durante anche innalzamento di livello delle procedure consigliate e gli strumenti di supporto. Questo documento ha assegnato una panoramica delle nuove funzionalità del linguaggio c# 6 e ha illustrato brevemente come vengono usati.

## <a name="related-links"></a>Collegamenti correlati

- [Nuove funzionalità del linguaggio nel linguaggio c# 6](https://github.com/dotnet/roslyn/wiki/New-Language-Features-in-C%23-6)
- [Utilizzo di C# 6 cartella di lavoro](https://developer.xamarin.com/workbooks/csharp/csharp6/csharp6.workbook)
