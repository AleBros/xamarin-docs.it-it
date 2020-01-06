---
title: Garbage Collection
ms.prod: xamarin
ms.assetid: 298139E2-194F-4A58-BC2D-1D22231066C4
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/15/2018
ms.openlocfilehash: da00eef7c08f7025239d15e60e6ec42416a36089
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487841"
---
# <a name="garbage-collection"></a>Garbage Collection

Novell. Android usa il [Garbage Collector generazionale semplice](https://www.mono-project.com/docs/advanced/garbage-collector/sgen/)di mono. Si tratta di un Garbage Collector Mark-and-sweep con due generazioni e uno *spazio di oggetti di grandi dimensioni*, con due tipi di raccolte: 

- Raccolte secondarie (raccoglie l'heap Gen0) 
- Raccolte principali (raccoglie Gen1 e heap dello spazio degli oggetti grandi). 

> [!NOTE]
> In assenza di una raccolta esplicita tramite [GC. Le raccolte Collect ()](xref:System.GC.Collect) sono *su richiesta, in base alle*allocazioni di heap. *Non si tratta di un sistema di conteggio dei riferimenti*. gli oggetti *non verranno raccolti nel momento in*cui non sono presenti riferimenti in attesa o quando un ambito è terminato. Il GC viene eseguito quando l'heap secondario esaurisce la memoria per le nuove allocazioni. Se non sono presenti allocazioni, non verrà eseguita.

Le raccolte secondarie sono a basso costo e frequenti e vengono utilizzate per raccogliere gli oggetti allocati e non recapitati di recente. Le raccolte secondarie vengono eseguite dopo ogni MB di oggetti allocati. Le raccolte secondarie possono essere eseguite manualmente chiamando [GC. Raccogli (0)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) 

Le raccolte principali sono costose e meno frequenti e vengono utilizzate per recuperare tutti gli oggetti non recapitabili. Le raccolte principali vengono eseguite una volta esaurita la memoria per le dimensioni dell'heap corrente (prima del ridimensionamento dell'heap). Le raccolte principali possono essere eseguite manualmente chiamando [GC. Collect ()](xref:System.GC.Collect) o chiamando [GC. Collect (int)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) con l'argomento [GC. MaxGeneration](xref:System.GC.MaxGeneration). 

## <a name="cross-vm-object-collections"></a>Raccolte di oggetti tra macchine virtuali

Sono disponibili tre categorie di tipi di oggetti.

- **Oggetti gestiti**: tipi che *non* ereditano da [java. lang. Object](xref:Java.Lang.Object) , ad esempio [System. String](xref:System.String). 
    Questi vengono raccolti normalmente dal GC. 

- **Oggetti Java**: tipi Java presenti all'interno della VM di runtime di Android ma non esposti alla VM mono. Questi sono noiosi e non verranno discussi ulteriormente. Questi vengono raccolti normalmente dalla VM di runtime di Android. 

- **Oggetti peer**: tipi che implementano [IJavaObject](xref:Android.Runtime.IJavaObject) , ad esempio tutte le sottoclassi [java. lang. Object](xref:Java.Lang.Object) e [java. lang. Throwable](xref:Java.Lang.Throwable) . Le istanze di questi tipi hanno due "mezze" un *peer gestito* e un *peer nativo*. Il peer gestito è un'istanza della C# classe. Il peer nativo è un'istanza di una classe Java all'interno della VM del runtime di Android C# e la proprietà [IJavaObject. handle](xref:Android.Runtime.IJavaObject.Handle) contiene un riferimento globale JNI al peer nativo. 

Esistono due tipi di peer nativi:

- **Peer del Framework** : tipi Java "normali" che non conoscono Novell. Android, ad esempio   [Android. Content. Context](xref:Android.Content.Context).

- **Peer utente** : [Android Callable Wrappers](~/android/platform/java-integration/working-with-jni.md) generati in fase di compilazione per ogni sottoclasse Java. lang. Object presente nell'applicazione.

Poiché sono presenti due macchine virtuali all'interno di un processo Novell. Android, esistono due tipi di Garbage Collection:

- Raccolte di Runtime Android 
- Raccolte mono 

Le raccolte di runtime di Android funzionano normalmente, ma con un avvertimento: un riferimento globale JNI viene considerato come una radice GC. Di conseguenza, se è presente un riferimento globale JNI che contiene un oggetto VM di runtime di Android, *non è possibile* raccogliere l'oggetto, neanche se è altrimenti idoneo per la raccolta.

Le raccolte mono sono il luogo in cui si verifica il divertimento. Gli oggetti gestiti vengono raccolti normalmente. Gli oggetti peer vengono raccolti eseguendo il processo seguente:

1. Tutti gli oggetti peer idonei per la raccolta mono hanno il riferimento globale JNI sostituito con un riferimento globale debole JNI. 

2. Viene richiamato un GC della VM di runtime di Android. Qualsiasi istanza peer nativa può essere raccolta. 

3. I riferimenti globali vulnerabili JNI creati in (1) vengono controllati. Se il riferimento debole è stato raccolto, viene raccolto l'oggetto peer. Se il riferimento debole *non* è stato raccolto, il riferimento debole viene sostituito con un riferimento globale JNI e l'oggetto peer non viene raccolto. Nota: nell'API 14 +, ciò significa che il valore restituito da `IJavaObject.Handle` può variare dopo un GC. 

Il risultato finale è che un'istanza di un oggetto peer sarà disponibile fino a quando vi viene fatto riferimento da codice gestito, ad esempio archiviato in una variabile di `static`, o a cui viene fatto riferimento dal codice Java. Inoltre, la durata dei peer nativi verrà estesa oltre a quella che altrimenti vivrebbe, perché il peer nativo non sarà ritirabile fino a quando non saranno ritirati sia il peer nativo che il peer gestito.

## <a name="object-cycles"></a>Cicli oggetto

Gli oggetti peer sono presenti logicamente nel runtime di Android e nelle VM mono. Ad esempio, un'istanza peer gestita di [Android. app. Activity](xref:Android.App.Activity) avrà un'istanza java peer di [Android. app. Activity](https://developer.android.com/reference/android/app/Activity.html) Framework corrispondente. Tutti gli oggetti che ereditano da [java. lang. Object](xref:Java.Lang.Object) possono prevedere rappresentazioni all'interno di entrambe le macchine virtuali. 

Tutti gli oggetti con rappresentazione in entrambe le macchine virtuali avranno durate che vengono estese rispetto agli oggetti presenti solo all'interno di una singola macchina virtuale (ad esempio, un [`System.Collections.Generic.List<int>`](xref:System.Collections.Generic.List%601)). Chiamata a [GC. Collect](xref:System.GC.Collect) non raccoglierà necessariamente questi oggetti, perché il GC Novell. Android deve assicurarsi che l'oggetto non faccia riferimento a una macchina virtuale prima di raccoglierla. 

Per abbreviare la durata degli oggetti, è necessario richiamare [java. lang. Object. Dispose ()](xref:Java.Lang.Object.Dispose) . Questa operazione consentirà di eseguire manualmente il "Sever" della connessione nell'oggetto tra le due VM liberando il riferimento globale, consentendo così la raccolta più veloce degli oggetti. 

## <a name="automatic-collections"></a>Raccolte automatiche

A partire dalla [versione 4.1.0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/mono_for_android_4/mono_for_android_4.1.0/index.md), Novell. Android esegue automaticamente un catalogo globale completo quando viene superata una soglia di Gref. Questa soglia corrisponde al 90% del numero massimo noto di Grefs per la piattaforma: 1800 Grefs nell'emulatore (2000 max) e 46800 Grefs su hardware (massimo 52000). *Nota:* Novell. Android conta solo il Grefs creato da [Android. Runtime. JNIEnv](xref:Android.Runtime.JNIEnv)e non è in grado di conoscere altri Grefs creati nel processo. Si tratta di un *approccio*euristico. 

Quando viene eseguita una raccolta automatica, nel log di debug viene stampato un messaggio simile al seguente:

```shell
I/monodroid-gc(PID): 46800 outstanding GREFs. Performing a full GC!
```

L'occorrenza di questo oggetto non è deterministica e può verificarsi in momenti inopportuni, ad esempio durante il rendering della grafica. Se viene visualizzato questo messaggio, potrebbe essere necessario eseguire una raccolta esplicita altrove oppure provare a [ridurre la durata degli oggetti peer](#Helping_the_GC). 

## <a name="gc-bridge-options"></a>Opzioni del Bridge GC

Novell. Android offre una gestione trasparente della memoria con Android e il runtime di Android. Viene implementato come un'estensione del Garbage Collector mono denominato *GC Bridge*. 

Il Bridge GC funziona durante una Garbage Collection mono e rileva quali oggetti peer necessitano di una "dinamicità" verificata con l'heap di runtime di Android. Il Bridge GC esegue questa determinazione eseguendo i passaggi seguenti (in ordine):

1. Indurre il grafico di riferimento mono degli oggetti peer non raggiungibili negli oggetti Java che rappresentano. 

2. Eseguire un catalogo globale Java.

3. Verificare quali oggetti sono effettivamente inattivi. 

Questo processo complicato consente alle sottoclassi di `Java.Lang.Object` di fare riferimento liberamente a qualsiasi oggetto; Rimuove tutte le restrizioni a cui è possibile associare oggetti Java C#. A causa di questa complessità, il processo Bridge può essere molto costoso e può causare pause evidenti in un'applicazione. Se l'applicazione sta riscontrando pause significative, vale la pena esaminare una delle tre implementazioni di Bridge GC seguenti: 

- **Tarjan** : progettazione completamente nuova del Bridge GC basata sull'algoritmo di [Robert Tarjan e sulla propagazione dei riferimenti alle versioni precedenti](https://en.wikipedia.org/wiki/Tarjan's_strongly_connected_components_algorithm).
    Offre prestazioni ottimali nei carichi di lavoro simulati, ma ha anche la maggior parte del codice sperimentale. 

- **Novità** : revisione principale del codice originale, correzione di due istanze di comportamento quadratico mantenendo l'algoritmo di base (basato sull' [algoritmo di Kosaraju](https://en.wikipedia.org/wiki/Kosaraju's_algorithm) per la ricerca di componenti fortemente connessi). 

- **Precedente** : l'implementazione originale (considerata la più stabile tra le tre). Si tratta del Bridge che un'applicazione deve usare se le `GC_BRIDGE` pause sono accettabili. 

L'unico modo per determinare quale Bridge GC funziona meglio è sperimentare in un'applicazione e analizzare l'output. Esistono due modi per raccogliere i dati per il benchmarking: 

- **Abilitare la registrazione** : abilitare la registrazione (come descritto nella sezione [configurazione](~/android/internals/garbage-collection.md) ) per ogni opzione del Bridge GC, quindi acquisire e confrontare gli output di log di ogni impostazione. Esaminare i messaggi di `GC` per ogni opzione. in particolare, il `GC_BRIDGE` messaggi. Le pause fino a 150ms per le applicazioni non interattive sono tollerabili, ma le pause sopra 60 ms per le applicazioni molto interattive, ad esempio i giochi, rappresentano un problema. 

- **Abilita Bridge** Accounting: l'accounting Bridge Visualizza il costo medio degli oggetti a cui fa riferimento ogni oggetto del processo Bridge. L'ordinamento di queste informazioni in base alle dimensioni fornirà suggerimenti sugli elementi che contengono la maggiore quantità di oggetti aggiuntivi. 

L'impostazione predefinita è **Tarjan**. Se si rileva una regressione, potrebbe essere necessario impostare questa opzione su **obsoleto**. Inoltre, è possibile scegliere di utilizzare l'opzione **precedente** più stabile se **Tarjan** non produce un miglioramento delle prestazioni.

Per specificare l'opzione di `GC_BRIDGE` che deve essere utilizzata da un'applicazione, passare `bridge-implementation=old`, `bridge-implementation=new` o `bridge-implementation=tarjan` alla variabile di ambiente `MONO_GC_PARAMS`. Questa operazione viene eseguita aggiungendo un nuovo file al progetto con un' **azione di compilazione** di `AndroidEnvironment`. Ad esempio: 

```shell
MONO_GC_PARAMS=bridge-implementation=tarjan
```

Per altre informazioni, vedere [Configurazione](#configuration).

<a name="Helping_the_GC" />

## <a name="helping-the-gc"></a>Supporto del GC

Sono disponibili diversi modi per aiutare il Garbage Collector a ridurre l'utilizzo della memoria e i tempi di raccolta.

### <a name="disposing-of-peer-instances"></a>Eliminazione di istanze peer

Il Garbage Collector presenta una visualizzazione incompleta del processo e potrebbe non essere eseguito quando la memoria è insufficiente perché il Garbage Collector non sa che la memoria è insufficiente. 

Ad esempio, un'istanza di un tipo [java. lang. Object](xref:Java.Lang.Object) o un tipo derivato ha una dimensione di almeno 20 byte (soggetto a modifiche senza preavviso, ecc.). 
I [wrapper richiamabili gestiti](~/android/internals/architecture.md) non aggiungono membri di istanza aggiuntivi. Pertanto, quando si dispone di un'istanza di [Android. graphics. bitmap](xref:Android.Graphics.Bitmap) che fa riferimento a un BLOB di 10 MB di memoria, il catalogo globale di Novell. Android non saprà che &ndash; GC visualizzerà un oggetto a 20 byte e non sarà in grado di determinare che è collegato a oggetti allocati da Android Runtime che mantiene 10 MB 

Spesso è necessario aiutare il GC. Sfortunatamente, *GC. AddMemoryPressure ()* e *GC. RemoveMemoryPressure ()* non sono supportati, pertanto se si è *certi* di aver appena liberato un oggetto grafico di grandi dimensioni allocato da Java, potrebbe essere necessario chiamare manualmente [GC. Collect ()](xref:System.GC.Collect) per richiedere a un catalogo globale di rilasciare la memoria sul lato Java oppure è possibile eliminare in modo esplicito le sottoclassi *java. lang. Object* , suddividendo il mapping tra l'istanza gestita Callable Wrapper e l'istanza java. Vedere ad esempio il [Bug 1084](https://bugzilla.xamarin.com/show_bug.cgi?id=1084#c6). 

> [!NOTE]
> Quando si eliminano istanze di sottoclasse `Java.Lang.Object`, è *necessario prestare molta attenzione.*

Per ridurre al minimo la possibilità di danneggiamento della memoria, osservare le linee guida seguenti quando si chiama `Dispose()`.

#### <a name="sharing-between-multiple-threads"></a>Condivisione tra più thread

Se l'istanza di *Java o gestita* può essere condivisa tra più thread, *non dovrebbe essere `Dispose()`d*, **mai**. Ad esempio, [`Typeface.Create()`](xref:Android.Graphics.Typeface.Create*) 
può restituire un' *istanza memorizzata nella cache*. Se più thread forniscono gli stessi argomenti, otterranno la *stessa* istanza. Di conseguenza, `Dispose()`dell'istanza `Typeface` da un thread potrebbe invalidare altri thread, il che può comportare `ArgumentException`s da `JNIEnv.CallVoidMethod()` (tra gli altri) perché l'istanza è stata eliminata da un altro thread. 

#### <a name="disposing-bound-java-types"></a>Eliminazione di tipi Java associati

Se l'istanza è di un tipo Java associato, l'istanza può essere eliminata finché l'istanza non verrà riutilizzata dal codice gestito *e* l'istanza Java non può essere condivisa *tra i thread* (vedere la discussione precedente `Typeface.Create()`). Questa decisione potrebbe essere difficile. Alla successiva immissione del codice gestito da parte dell'istanza di Java verrà creato un *nuovo* wrapper. 

Questa operazione è spesso utile quando si tratta di drawables e altre istanze con utilizzo intensivo di risorse:

```csharp
using (var d = Drawable.CreateFromPath ("path/to/filename"))
    imageView.SetImageDrawable (d);
```

Il valore precedente è sicuro perché il peer che può essere restituito da [CreateFromPath ()](xref:Android.Graphics.Drawables.Drawable.CreateFromPath*) si riferisce a un peer del Framework, *non* a un peer utente. La chiamata `Dispose()` alla fine del blocco di `using` interromperà la relazione tra [le](https://developer.android.com/reference/android/graphics/drawable/Drawable.html) istanze [di disegnatore gestito e](xref:Android.Graphics.Drawables.Drawable) di .NET Framework, consentendo la raccolta dell'istanza di Java non appena il runtime di Android deve essere. Questo *non* è sicuro se l'istanza peer fa riferimento a un peer utente; Qui vengono usate informazioni "esterne" per *capire* che il `Drawable` non può fare riferimento a un peer utente e pertanto la chiamata `Dispose()` è sicura. 

#### <a name="disposing-other-types"></a>Eliminazione di altri tipi 

Se l'istanza fa riferimento a un tipo che non è un'associazione di un tipo Java (ad esempio un `Activity`personalizzato **), non** chiamare `Dispose()` a meno che non si sia *certi* che nessun codice Java chiamerà metodi sottoposti a override su tale istanza. In caso contrario, il risultato [è`NotSupportedException`](~/android/internals/architecture.md#Premature_Dispose_Calls). 

Ad esempio, se si dispone di un listener di clic personalizzato:

```csharp
partial class MyClickListener : Java.Lang.Object, View.IOnClickListener {
    // ...
}
```

*Non è consigliabile* eliminare questa istanza perché Java tenterà di richiamare i metodi in futuro:

```csharp
// BAD CODE; DO NOT USE
Button b = FindViewById<Button> (Resource.Id.myButton);
using (var listener = new MyClickListener ())
    b.SetOnClickListener (listener);
```

#### <a name="using-explicit-checks-to-avoid-exceptions"></a>Uso di controlli espliciti per evitare eccezioni

Se è stato implementato un metodo di overload [java. lang. Object. Dispose](xref:Java.Lang.Object.Dispose*) , evitare di toccare oggetti che coinvolgono JNI. In questo modo è possibile creare una situazione a *doppia eliminazione* che consente al codice di provare (fatalmente) ad accedere a un oggetto Java sottostante che è già stato sottoposta a Garbage Collection. Questa operazione genera un'eccezione simile alla seguente: 

```shell
System.ArgumentException: 'jobject' must not be IntPtr.Zero.
Parameter name: jobject
at Android.Runtime.JNIEnv.CallVoidMethod
```

Questa situazione si verifica spesso quando il primo Dispose di un oggetto fa sì che un membro diventi null e quindi un successivo tentativo di accesso su questo membro NULL causa la generazione di un'eccezione. In particolare, il `Handle` dell'oggetto (che collega un'istanza gestita alla relativa istanza java sottostante) viene invalidato alla prima Dispose, ma il codice gestito tenta comunque di accedere a questa istanza di Java sottostante anche se non è più disponibile. vedere [wrapper richiamabili gestiti](~/android/internals/architecture.md#Managed_Callable_Wrappers) per ulteriori informazioni sul mapping tra istanze Java e istanze gestite. 

Un modo efficace per evitare questa eccezione consiste nel verificare in modo esplicito nel metodo `Dispose` che il mapping tra l'istanza gestita e l'istanza java sottostante sia ancora valido. ovvero, verificare se la `Handle` dell'oggetto è null (`IntPtr.Zero`) prima di accedere ai relativi membri. Ad esempio, il seguente metodo di `Dispose` accede a un oggetto `childViews`: 

```csharp
class MyClass : Java.Lang.Object, ISomeInterface 
{
    protected override void Dispose (bool disposing)
    {
        base.Dispose (disposing);
        for (int i = 0; i < this.childViews.Count; ++i)
        {
            // ...
        }
    }
}
```

Se un passaggio di eliminazione iniziale causa la `Handle`di un `childViews` non valido, l'accesso al ciclo di `for` genererà un'`ArgumentException`. Se si aggiunge un controllo `Handle` null esplicito prima del primo accesso `childViews`, il metodo `Dispose` seguente impedisce che si verifichi l'eccezione: 

```csharp
class MyClass : Java.Lang.Object, ISomeInterface 
{
    protected override void Dispose (bool disposing)
    {
        base.Dispose (disposing);

        // Check for a null handle:
        if (this.childViews.Handle == IntPtr.Zero)
            return;

        for (int i = 0; i < this.childViews.Count; ++i)
        {
            // ...
        }
    }
}
```

### <a name="reduce-referenced-instances"></a>Ridurre le istanze a cui si fa riferimento

Ogni volta che viene eseguita l'analisi di un'istanza di un tipo o di una sottoclasse `Java.Lang.Object` durante il Garbage Collector, deve essere analizzato anche l'intero *oggetto grafico* a cui fa riferimento l'istanza. L'oggetto grafico è il set di istanze dell'oggetto a cui fa riferimento la "istanza radice", *più* tutti gli elementi a cui fa riferimento l'istanza radice, in modo ricorsivo. 

Si consideri la classe seguente:

```csharp
class BadActivity : Activity {

    private List<string> strings;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        strings.Value = new List<string> (
                Enumerable.Range (0, 10000)
                .Select(v => new string ('x', v % 1000)));
    }
}
```

Quando `BadActivity` viene costruito, l'oggetto grafico conterrà 10004 istanze (1x `BadActivity`, 1x `strings`, 1x `string[]` detenute da `strings`, istanze di stringa 10000x *), che* dovranno essere analizzate ogni volta che viene eseguita l'analisi dell'istanza di `BadActivity`. 

Ciò può avere effetti negativi sui tempi di raccolta, con conseguente aumento dei tempi di sospensione GC. 

È possibile semplificare il Garbage Collector *riducendo* le dimensioni degli oggetti grafici che hanno radice da istanze peer utente. Nell'esempio precedente questa operazione può essere eseguita spostando `BadActivity.strings` in una classe separata che non eredita da Java. lang. Object: 

```csharp
class HiddenReference<T> {

    static Dictionary<int, T> table = new Dictionary<int, T> ();
    static int idgen = 0;

    int id;

    public HiddenReference ()
    {
        lock (table) {
            id = idgen ++;
        }
    }

    ~HiddenReference ()
    {
        lock (table) {
            table.Remove (id);
        }
    }

    public T Value {
        get { lock (table) { return table [id]; } }
        set { lock (table) { table [id] = value; } }
    }
}

class BetterActivity : Activity {

    HiddenReference<List<string>> strings = new HiddenReference<List<string>>();

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        strings.Value = new List<string> (
                Enumerable.Range (0, 10000)
                .Select(v => new string ('x', v % 1000)));
    }
}
```

## <a name="minor-collections"></a>Raccolte secondarie

Le raccolte secondarie possono essere eseguite manualmente chiamando [GC. Collect (0)](xref:System.GC.Collect). Le raccolte secondarie sono economiche (rispetto alle raccolte principali), ma hanno un costo fisso significativo, quindi non è necessario attivarle troppo spesso e devono avere un tempo di pausa di pochi millisecondi. 

Se l'applicazione ha un "ciclo di lavoro" in cui la stessa operazione viene eseguita più volte, potrebbe essere consigliabile eseguire manualmente una raccolta secondaria al termine del ciclo di lavoro. I cicli Duty di esempio includono: 

- Ciclo di rendering di un singolo frame di gioco.
- L'intera interazione con una determinata finestra di dialogo dell'app (apertura, riempimento, chiusura) 
- Gruppo di richieste di rete per aggiornare/sincronizzare i dati dell'app.

## <a name="major-collections"></a>Raccolte principali

Le raccolte principali possono essere eseguite manualmente chiamando [GC. Collect ()](xref:System.GC.Collect) o `GC.Collect(GC.MaxGeneration)`. 

Devono essere eseguite raramente e possono avere un tempo di pausa di un secondo in un dispositivo di tipo Android quando si raccoglie un heap 512MB. 

Le raccolte principali devono essere richiamate manualmente, se mai: 

- Alla fine dei cicli di lavoro lunghi e quando una pausa lunga non presenta un problema per l'utente. 

- All'interno di un metodo [Android. app. Activity. OnLowMemory ()](xref:Android.App.Activity.OnLowMemory) sottoposto a override. 

## <a name="diagnostics"></a>Diagnostica

Per tenere traccia del momento in cui i riferimenti globali vengono creati ed eliminati, è possibile impostare la proprietà di sistema [debug. mono. log](~/android/troubleshooting/index.md) in modo che contenga [*Gref*](~/android/troubleshooting/index.md) e/o [*GC*](~/android/troubleshooting/index.md). 

## <a name="configuration"></a>Configurazione di

È possibile configurare il Garbage Collector Novell. Android impostando la variabile di ambiente `MONO_GC_PARAMS`. Le variabili di ambiente possono essere impostate con un'azione di compilazione di [AndroidEnvironment](~/android/deploy-test/environment.md).

La variabile di ambiente `MONO_GC_PARAMS` è un elenco delimitato da virgole dei parametri seguenti: 

- `nursery-size` = *size* : imposta la dimensione della nursery. La dimensione è specificata in byte e deve essere una potenza di due. I suffissi `k`, `m` e `g` possono essere usati per specificare rispettivamente kilo, Mega e Gigabyte. La nursery è la prima generazione (di due). Una nursery più grande in genere accelererà il programma, ma userà ovviamente una maggiore quantità di memoria. Dimensioni predefinite della nursery 512 KB. 

- `soft-heap-limit` = *size* : il consumo massimo managed memory di destinazione per l'app. Quando l'utilizzo della memoria è inferiore al valore specificato, il GC viene ottimizzato per il tempo di esecuzione (un minor numero di raccolte). 
    Al di sopra di questo limite, il GC è ottimizzato per l'utilizzo della memoria (altre raccolte). 

- *soglia* `evacuation-threshold` = : imposta la soglia di evacuazione in percentuale. Il valore deve essere un numero intero compreso tra 0 e 100. Il valore predefinito è 66. Se la fase di sweep della raccolta rileva che l'occupazione di un tipo di blocco heap specifico è inferiore a questa percentuale, effettuerà una raccolta di copia per quel tipo di blocco nella raccolta principale successiva, ripristinando in tal modo l'occupazione fino al 100%. Il valore 0 disattiva l'evacuazione. 

- implementazione di `bridge-implementation` = *Bridge* : consente di impostare l'opzione GC Bridge per risolvere i problemi di prestazioni GC. Esistono tre possibili valori: *Old* , *New* , *Tarjan*.

- `bridge-require-precise-merge`: il Bridge Tarjan contiene un'ottimizzazione che può, in rare occasioni, causare la raccolta di un oggetto in un Garbage Collector dopo che è stato prima sottoposta a Garbage Collection. Questa opzione consente di disabilitare l'ottimizzazione, rendendo i cataloghi globali più prevedibili ma potenzialmente più lenti.

Ad esempio, per configurare il GC in modo da avere un limite di dimensioni dell'heap di 128 MB, aggiungere un nuovo file al progetto con un' **azione di compilazione** di `AndroidEnvironment` con il contenuto: 

```shell
MONO_GC_PARAMS=soft-heap-limit=128m
```
