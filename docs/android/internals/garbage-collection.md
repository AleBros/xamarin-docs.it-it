---
title: Garbage Collection
ms.prod: xamarin
ms.assetid: 298139E2-194F-4A58-BC2D-1D22231066C4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: c5a4247b2e10706014c9f92a487803e4a718c1a6
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671977"
---
# <a name="garbage-collection"></a>Garbage Collection

Xamarin. Android Usa Mono [Simple Generational garbage collector](https://www.mono-project.com/docs/advanced/garbage-collector/sgen/). Si tratta di un garbage collector di mark-and-sweep con due generazioni e un *dello spazio di oggetti di grandi dimensioni*, con due tipi di raccolte: 

-   Raccolte secondarie (raccoglie Gen0 heap) 
-   Raccolte principali (raccoglie Gen1 e oggetti di grandi dimensioni spazio heap). 

> [!NOTE]
> In assenza di una raccolta esplicita tramite [GC. Collect ()](xref:System.GC.Collect) le raccolte sono *su richiesta*, in base al momento le allocazioni di heap. *Non è un sistema di conteggio dei riferimenti*; gli oggetti *, non appena non sono presenti riferimenti in sospeso non verranno raccolti*, o quando è stato terminato un ambito. Il Garbage Collector verrà eseguito quando l'heap secondaria ha esaurito la memoria per le nuove allocazioni. Se non sono presenti allocazioni, non verrà eseguito.


Raccolte secondarie e frequenti disponibili e vengono usate per raccogliere gli oggetti inutilizzati e allocati di recente. Raccolte secondarie vengono eseguite dopo ogni pochi MB degli oggetti allocati. Raccolte secondarie possono essere eseguite manualmente tramite la chiamata [GC. Raccogliere (0)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) 

Raccolte di principali sono meno frequenti e dispendiosi e vengono usate per recuperare tutti gli oggetti inutilizzati. Raccolte principali vengono eseguite dopo l'esaurimento della memoria per le dimensioni dell'heap correnti (prima di ridimensionare l'heap). Raccolte principale possono essere eseguite manualmente tramite la chiamata [GC. Raccogliere ()](xref:System.GC.Collect) o chiamando [GC. (Int) di raccogliere](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) con l'argomento [GC. MaxGeneration](xref:System.GC.MaxGeneration). 



## <a name="cross-vm-object-collections"></a>Raccolte di oggetti tra VM

Esistono tre categorie di tipi di oggetto.

-   **Oggetti gestiti**: i tipi che eseguono *non* ereditare [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) , ad esempio [System. String](xref:System.String). 
    Questi vengono raccolti in genere dal GC. 

-   **Gli oggetti Java**: Tipi di Java che sono presenti all'interno del runtime Android VM ma non visibili alla macchina virtuale di Mono. Si tratta di un'operazione noiosa e non verranno illustrate ulteriormente. Questi vengono raccolti in genere dal runtime di Android della macchina virtuale. 

-   **Eseguire il peering di oggetti**: i tipi che implementano [IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) , ad esempio, tutti [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) e [Throwable](https://developer.xamarin.com/api/type/Java.Lang.Throwable/) sottoclassi. Le istanze di questi tipi hanno due "halfs" una *peer gestito* e una *peer native*. Il peer gestito è un'istanza del C# classe. Il peer nativo è un'istanza di una classe Java all'interno del runtime Android macchina virtuale e il C# [IJavaObject.Handle](https://developer.xamarin.com/api/property/Android.Runtime.IJavaObject.Handle/) proprietà contiene un riferimento globale JNI al peer nativa. 


Esistono due tipi di pari livello nativo:

-   **I peer Framework** : "Normale" tipi di Java che conoscono nulla di xamarin. Android, ad esempio [android.content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/).

-   **I peer utente** : [Android Callable Wrapper](~/android/platform/java-integration/working-with-jni.md) che vengono generati in fase di compilazione per ogni sottoclasse Java.Lang.Object presente all'interno dell'applicazione.


Poiché sono presenti due macchine virtuali all'interno di un processo di xamarin. Android, esistono due tipi di operazioni di garbage collection:

-   Raccolte di runtime di Android 
-   Raccolte di mono 

Runtime Android raccolte operano in genere, ma con una sola avvertenza: un riferimento globale JNI viene considerato come una radice di Garbage Collection. Di conseguenza, se è presente un JNI globali di riferimento mantiene un runtime di Android della macchina virtuale, l'oggetto *non è possibile* raccolti, anche se è idoneo per la raccolta.

Le raccolte di mono sono in cui si verifica il divertimento. In genere vengono raccolti gli oggetti gestiti. Gli oggetti peer vengono raccolti eseguendo il processo seguente:

1.  Tutti gli oggetti Peer idonei per la raccolta Mono hanno i riferimenti globali JNI sostituito con un riferimento di global debole JNI. 

2.  Un runtime di Android che viene richiamato Garbage Collector della macchina virtuale. Qualsiasi istanza nativa peer può essere raccolti. 

3.  Vengono controllati i riferimenti globali deboli JNI creati in (1). Se il riferimento debole siano stati raccolti, l'oggetto Peer verrà raccolti. Se il riferimento debole dispone *non* stati raccolti, quindi il riferimento debole viene sostituito con un riferimento globale JNI e l'oggetto Peer non verrà raccolti. Nota: nella API 14 +, ciò significa che il valore restituito da `IJavaObject.Handle` possono essere modificati dopo un catalogo globale. 

Il risultato finale di tutto ciò è che un'istanza di un oggetto Peer risiederanno, purché vi fanno riferimento sia codice gestito (ad esempio, archiviati in un `static` variabile) o a cui viene fatto riferimento dal codice Java. Inoltre, la durata di pari livello nativo verrà estesa oltre ciò che accade in caso contrario, in tempo reale, come il peer Native non sarà ritirabile fino a quando sia il peer nativo e gestito peer sono ritirabile.


## <a name="object-cycles"></a>Cicli di oggetti

Gli oggetti peer sono logicamente presenti all'interno della macchina virtuale di Mono sia il runtime di Android. Ad esempio, un' [Android.App.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/) disporrà di una corrispondente istanza gestita di peer [android.app.Activity](https://developer.android.com/reference/android/app/Activity.html) l'istanza di Java peer framework. Tutti gli oggetti da cui ereditare [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) si può prevedere hanno rappresentazioni in entrambe le macchine virtuali. 

Tutti gli oggetti con rappresentazione in entrambe le macchine virtuali avranno durate cui vengono estese rispetto agli oggetti che sono presenti solo all'interno di una singola macchina virtuale (ad esempio un [ `System.Collections.Generic.List<int>` ](xref:System.Collections.Generic.List%601)). La chiamata a [GC. Raccogliere](xref:System.GC.Collect) non sarà necessariamente raccogliere questi oggetti, quando sono necessari per il Garbage Collector di xamarin. Android per assicurarsi che l'oggetto non viene fatto riferimento da una macchina virtuale prima della raccolta degli stessi dati. 

Per abbreviare la durata degli oggetti, [Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/) deve essere richiamato. Questo verrà manualmente "server" la connessione per l'oggetto tra le due macchine virtuali con la liberazione di riferimento globale, consentendo in tal modo gli oggetti da raccogliere più velocemente. 


## <a name="automatic-collections"></a>Raccolte automatica

A partire [versione 4.1.0](https://developer.xamarin.com/releases/android/mono_for_android_4/mono_for_android_4.1.0), xamarin. Android esegue automaticamente una Garbage Collection completo quando viene superata una soglia di gref. Questa soglia è 90% dei noti grefs massimo per la piattaforma: 1800 grefs nell'emulatore (massimo 2000) e 46800 grefs su hardware (52000 massima). *Nota:* Xamarin. Android vengono conteggiati solo i grefs creato da [Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)e non riconoscerà su qualsiasi altro grefs creato nel processo. Si tratta di un'euristica *solo*. 

Quando viene eseguita una raccolta automatica, nel log di debug verrà stampato un messaggio simile al seguente:

```shell
I/monodroid-gc(PID): 46800 outstanding GREFs. Performing a full GC!
```

L'occorrenza di questo oggetto è non deterministico e può verificarsi in viaggio (ad esempio, al centro per il rendering della grafica). Se viene visualizzato questo messaggio, è possibile eseguire una raccolta esplicita in un' posizione oppure è possibile tentare [ridurre la durata degli oggetti peer](#Helping_the_GC). 

## <a name="gc-bridge-options"></a>Opzioni di Bridge GC

Xamarin. Android offre la gestione della memoria trasparente con il runtime di Android e Android. Viene implementato come un'estensione per il garbage collector Mono denominato il *Bridge GC*. 

Il Bridge di Garbage Collection funziona durante un'operazione Mono garbage collection e figure out il peer che ha gli oggetti deve le "attività" verificato con l'heap del runtime Android. Il Bridge di GC rende questo aspetto eseguendo le operazioni seguenti (nell'ordine indicato):

1.  Indurre il grafico di riferimento di mono di oggetti peer non raggiungibile negli oggetti Java che rappresentano. 

2.  Eseguire un Garbage Collector Java.

3.  Verificare quali oggetti siano davvero inattivi. 

Questo processo complicato è quello che consente alle sottoclassi della `Java.Lang.Object` per riferimento liberamente uno qualsiasi degli oggetti, ma rimuove le eventuali restrizioni su quali Java oggetti possono essere associati a C#. A causa di questa complessità, il processo di bridge possa essere molto costoso e comporta un notevole mette in pausa in un'applicazione. Se l'applicazione ha riscontrato significativi mette in pausa, è utile esaminare una delle implementazioni di Garbage Collection Bridge tre seguenti: 

-   **Tarjan** -base una completamente nuova progettazione del Bridge di Garbage Collection [algoritmo di Robert Tarjan e con le versioni precedenti fare riferimento a propagazione](https://en.wikipedia.org/wiki/Tarjan's_strongly_connected_components_algorithm).
    Ha le migliori prestazioni con i carichi di lavoro simulati, ma include anche la condivisione di dimensioni maggiori di codice sperimentale. 

-   **Nuove** -rivoluzionati del codice originale, correzione di due istanze di comportamento quadratica ma mantenendo l'algoritmo principale (in base [algoritmo del Kosaraju](https://en.wikipedia.org/wiki/Kosaraju's_algorithm) per ricerca fortemente connessi i componenti). 

-   **Vecchio** -implementazione originale (considerata più stabile di tre). Il bridge che un'applicazione deve usare se si tratta di `GC_BRIDGE` pause sono accettabili. 


L'unico modo per capire meglio il cui funzionamento del Bridge di Garbage Collection è la sperimentazione in un'applicazione e analizzando l'output. Esistono due modi per raccogliere i dati di benchmark: 

-   **Abilitare la registrazione** -abilitare la registrazione (come descritto nel [Configuration](~/android/internals/garbage-collection.md) sezione) per ogni opzione di Bridge di catalogo globale, quindi acquisire e confrontare gli output di log da ogni impostazione. Esaminare i `GC` messaggi per ogni opzione, in particolare il `GC_BRIDGE` messaggi. Mette in pausa fino a 150 ms per le applicazioni non interattive sono tollerabile, ma mette in pausa di sopra di 60 ms per le applicazioni molto interattive (ad esempio i giochi) costituiscono un problema. 

-   **Attivare l'accounting di bridge** -accounting Bridge mostrerà il costo medio degli oggetti coinvolti nel processo di bridge ogni oggetto cui punta. Ordinamento di queste informazioni in base a dimensioni fornirà suggerimento in merito a ciò che contiene la quantità maggiore di oggetti aggiuntivi. 


Per specificare quali `GC_BRIDGE` opzione di un'applicazione deve usare, passare `bridge-implementation=old`, `bridge-implementation=new` oppure `bridge-implementation=tarjan` per il `MONO_GC_PARAMS` variabile di ambiente, ad esempio: 

```shell
MONO_GC_PARAMS=bridge-implementation=tarjan
```

L'impostazione predefinita è **Tarjan**. Se si trova una regressione, si potrebbe essere necessario impostare questa opzione su **vecchio**. Inoltre, è possibile scegliere di usare la maggiore stabilità **vecchio** opzione se **Tarjan** non consente di produrre un miglioramento delle prestazioni. 

<a name="Helping_the_GC" />

## <a name="helping-the-gc"></a>Aiutare il Garbage Collector

Esistono diversi modi per consentire il Garbage Collector per ridurre i tempi di raccolta e utilizzo di memoria.



### <a name="disposing-of-peer-instances"></a>Eliminazione di istanze di Peer

Il Garbage Collector ha una visualizzazione completa del processo e potrebbe non essere eseguita in memoria è insufficiente perché il Garbage Collector non sa che la memoria è insufficiente. 

Ad esempio, un'istanza di un [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) tipo o tipo derivato è di almeno 20 byte (soggetto a modifiche senza preavviso e così via, e così via.). 
[Managed Callable Wrapper](~/android/internals/architecture.md) non vengono aggiunti i membri di istanza aggiuntivo, quindi, quando si dispone di un [Android.Graphics.Bitmap](https://developer.xamarin.com/api/type/Android.Graphics.Bitmap/) istanza che fa riferimento a un blob di 10MB di memoria, Garbage Collection di xamarin. Android non sa che &ndash; il Garbage Collector verrà visualizzato un oggetto di 20 byte e sarà in grado di determinare ciò che è collegato agli oggetti allocati al runtime di Android che mantiene i 10MB di memoria attiva. 

Spesso è necessario per il Garbage Collector. Sfortunatamente, *GC. AddMemoryPressure()* e *GC. RemoveMemoryPressure()* non sono supportati, pertanto, se si *conoscere* liberata un grafico di oggetti grandi allocati Java potrebbe essere necessario chiamare manualmente [GC. Collect ()](xref:System.GC.Collect) al prompt dei comandi una Garbage Collection per rilasciare il lato Java memoria oppure è possibile in modo esplicito smaltire *Java.Lang.Object* sottoclassi, interrompere il mapping tra il wrapper chiamabile gestito e l'istanza di Java. Ad esempio, vedere [1084 Bug](http://bugzilla.xamarin.com/show_bug.cgi?id=1084#c6). 


> [!NOTE]
> È necessario essere *estremamente* attenzione durante l'eliminazione di `Java.Lang.Object` istanze sottoclasse.

Per ridurre al minimo la possibilità di danneggiamento della memoria, osservare le linee guida seguenti quando si chiama `Dispose()`.


#### <a name="sharing-between-multiple-threads"></a>La condivisione tra più thread

Se il *Java o gestite* istanza può essere condivisi tra più thread, *non deve essere `Dispose()`1!d*, **mai**. Per esempio [`Typeface.Create()`](https://developer.xamarin.com/api/member/Android.Graphics.Typeface.Create/(System.String%2cAndroid.Graphics.TypefaceStyle)) 
può restituire un *istanza memorizzata nella cache*. Se più thread forniscono gli stessi argomenti, si otterrà il *stesso* istanza. Di conseguenza, `Dispose()`ing del `Typeface` istanza da un thread potrebbe invalidare altri thread, che può comportare `ArgumentException`s dalla `JNIEnv.CallVoidMethod()` (tra gli altri) perché l'istanza è stata eliminata da un altro thread. 


#### <a name="disposing-bound-java-types"></a>Eliminazione di tipi associato Java

Se l'istanza è di un tipo di linguaggio associato, l'istanza può essere eliminato *purché* l'istanza non può essere riutilizzato da codice gestito *e* l'istanza di Java non può essere condivisa tra thread (vedere la precedente `Typeface.Create()` discussione). (Stabilito questo aspetto può essere difficile). La volta successiva che l'istanza di Java entra in codice gestito, un *nuovo* wrapper verrà creato appositamente. 

Ciò è spesso utile se si desidera Drawable e altre istanze elevati in termini di risorse:

```csharp
using (var d = Drawable.CreateFromPath ("path/to/filename"))
    imageView.SetImageDrawable (d);
```

Il codice precedente è sicura perché il Peer che [Drawable.CreateFromPath()](https://developer.xamarin.com/api/member/Android.Graphics.Drawables.Drawable.CreateFromPath/) restituisce farà riferimento a un peer di Framework, *non* un peer dell'utente. Il `Dispose()` chiamare alla fine del `using` blocco interromperà la relazione tra managed [Drawable](https://developer.xamarin.com/api/type/Android.Graphics.Drawables.Drawable/) e framework [Drawable](https://developer.android.com/reference/android/graphics/drawable/Drawable.html) istanze, consentendo l'istanza di Java sia raccolte, non appena il runtime di Android. Questo sarebbe *non* sicuri se l'istanza di Peer fa riferimento a un peer dell'utente; in questo caso usiamo le informazioni di "external" per *sapere* che il `Drawable` non possono fare riferimento a un peer di utente e pertanto il `Dispose()` chiamare è sicuro. 


#### <a name="disposing-other-types"></a>Eliminazione di altri tipi 

Se l'istanza fa riferimento a un tipo che non è un'associazione di un tipo di Java (ad esempio un oggetto personalizzato `Activity`), **non viene** chiamare `Dispose()` a meno che non si *sapere* che nessun codice Java chiamerà i metodi sottoposti a override su esso istanza. In caso contrario, restituisce [ `NotSupportedException`s](~/android/internals/architecture.md#Premature_Dispose_Calls). 

Ad esempio, se si dispone di una classe personalizzata fare clic su listener:

```csharp
partial class MyClickListener : Java.Lang.Object, View.IOnClickListener {
    // ...
}
```

Si *non deve* dispose di questa istanza, come Java proverà a richiamare metodi su di esso in futuro:

```csharp
// BAD CODE; DO NOT USE
Button b = FindViewById<Button> (Resource.Id.myButton);
using (var listener = new MyClickListener ())
    b.SetOnClickListener (listener);
```


#### <a name="using-explicit-checks-to-avoid-exceptions"></a>Usando i controlli espliciti per evitare eccezioni

Se è stato implementato un [Java.Lang.Object.Dispose](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose(System.Boolean)/) overload di metodo, evitare di modificare gli oggetti che coinvolgono JNI. In questo modo è possibile creare un *doppia dispose* situazione che rende possibile il codice (preoccupantemente) tenta di accedere a un oggetto sottostante di Java che è già stato sottoposto a garbage collection. Questa operazione genera un'eccezione simile alla seguente: 

```shell
System.ArgumentException: 'jobject' must not be IntPtr.Zero.
Parameter name: jobject
at Android.Runtime.JNIEnv.CallVoidMethod
```

Spesso questa situazione si verifica quando il primo metodo dispose di un oggetto fa sì che un membro a diventare null e un tentativo di accesso successivi per questo membro null causa un'eccezione generata. In particolare, dell'oggetto `Handle` (un'istanza gestita che collega all'istanza di Java sottostante) viene invalidato per il prima dispose, ma il codice gestito tenta comunque accedere a questa istanza di Java sottostante anche se non è più disponibile (vedere [ Wrapper richiamabili gestiti](~/android/internals/architecture.md#Managed_Callable_Wrappers) per altre informazioni sul mapping tra le istanze di Java e le istanze gestite). 

Un buon metodo per impedire questa eccezione consiste nel verificare in modo esplicito nel `Dispose` metodo che il mapping tra l'istanza gestita e l'istanza di Java sottostante è ancora valido; ovvero, verificare se l'oggetto `Handle` è null (`IntPtr.Zero`) prima di accedere ai relativi membri. Ad esempio, il seguente `Dispose` metodo accessi un `childViews` oggetto: 

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

Se un metodo dispose iniziale passa cause `childViews` per avere un valore non valido `Handle`, il `for` accesso ciclo genererà un `ArgumentException`. Aggiungendo l'impostazione esplicita `Handle` null controllo prima del primo `childViews` accedere, seguenti `Dispose` metodo consente di evitare l'eccezione che si verifichi: 

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


### <a name="reduce-referenced-instances"></a>Ridurre le istanze di cui si fa riferimento

Ogni volta che un'istanza di un `Java.Lang.Object` tipo o una sottoclasse viene analizzato durante il Garbage Collector, l'intera *oggetto grafico* che l'istanza fa riferimento a deve anche essere analizzato. L'oggetto grafico è il set di istanze dell'oggetto che fa riferimento l'istanza"radice", *plus* tutto quello che fa riferimento quali l'istanza radice fa riferimento, in modo ricorsivo. 

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

Quando `BadActivity` viene costruito, l'oggetto grafico conterrà 10004 istanze (1x `BadActivity`, 1x `strings`, 1x `string[]` contenute `strings`, 10000 x istanze di stringa), *tutti i* di cui dovrà essere analisi ogni volta che il `BadActivity` istanza viene analizzata. 

Ciò può avere effetti negativi sui tempi di raccolta, determinando un miglioramento tempi di pausa GC. 

È possibile aiutare il Garbage Collector dal *riducendo* le dimensioni degli oggetti grafici che contengono una radice da istanze utente di peer. Nell'esempio precedente, questa operazione può essere eseguita tramite lo spostamento `BadActivity.strings` in una classe separata che non eredita da Java.Lang.Object: 

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

Raccolte secondarie possono essere eseguite manualmente tramite la chiamata [GC. Collect(0)](xref:System.GC.Collect). Le raccolte di minori sono economiche (quando rispetto alle raccolte principale), ma è necessario un notevole costo, fisso, in modo che non si vuole attivare tali troppo spesso e deve avere una pausa di pochi millisecondi. 

Se l'applicazione presenta un "ciclo di servizio" in cui la stessa operazione viene eseguita ripetutamente, potrebbe essere consigliabile eseguire manualmente una raccolta secondaria dopo il ciclo è terminato. Ciclo di lavoro di esempio include: 

-  Il ciclo di rendering di un singolo frame del gioco.
-  L'intera interazione con una finestra di dialogo app specificata (apertura, la compilazione di chiusura) 
-  Un gruppo di richieste di rete per l'aggiornamento/sincronizzazione dei dati delle app.



## <a name="major-collections"></a>Raccolte principali

Raccolte principale possono essere eseguite manualmente tramite la chiamata [GC. Collect ()](xref:System.GC.Collect) o `GC.Collect(GC.MaxGeneration)`. 

Sono devono essere eseguite raramente e possono avere una pausa di secondo in un dispositivo Android in stile quando la raccolta di un heap 512MB. 

Raccolte principali devono solo essere richiamate manualmente, se dovesse: 

-   Alla fine dei compiti lunghi cicli e quando una lunga pausa non costituire un problema all'utente. 

-   Within an overridden [Android.App.Activity.OnLowMemory()](https://developer.xamarin.com/api/member/Android.App.Activity.OnLowMemory/) method. 



## <a name="diagnostics"></a>Diagnostica

Per rilevare quando i riferimenti globali vengono creati ed eliminati, è possibile impostare il [debug.mono.log](~/android/troubleshooting/index.md) proprietà di sistema per contenere [ *gref* ](~/android/troubleshooting/index.md) e/o [ *gc*](~/android/troubleshooting/index.md). 



## <a name="configuration"></a>Configurazione

Il garbage collector di xamarin. Android può essere configurato impostando la `MONO_GC_PARAMS` variabile di ambiente. Le variabili di ambiente possono essere impostate con un'azione di compilazione [AndroidEnvironment](~/android/deploy-test/environment.md).

Il `MONO_GC_PARAMS` variabile di ambiente è un elenco delimitato da virgole dei parametri seguenti: 

-   `nursery-size` = *dimensioni* : Imposta le dimensioni di nursery. Le dimensioni vengono specificate in byte e devono essere una potenza di due. I suffissi `k` , `m` e `g` può essere utilizzato per specificare chilogrammo, mega e gigabyte, rispettivamente. Nursery è la prima generazione (di due). Un nursery più grandi sarà in genere più veloce del programma, ma ovviamente utilizzerà più memoria. Nursery predefinita dimensioni di 512 kb. 

-   `soft-heap-limit` = *dimensioni* : Il numero massimo di destinazione gestiti il consumo di memoria per l'app. Quando l'utilizzo della memoria è inferiore al valore specificato, il Garbage Collector è ottimizzato per il tempo di esecuzione (raccolte di un numero inferiore). 
    Oltre questo limite, il Garbage Collector è ottimizzato per l'utilizzo della memoria (altre raccolte). 

-   `evacuation-threshold` = *soglia* : Imposta la soglia di evacuazione espresse come percentuale. Il valore deve essere un numero intero compreso nell'intervallo da 0 a 100. Il valore predefinito è 66. Se la fase di apertura della raccolta rileva che l'occupazione di un tipo di blocco dell'heap specifico è minore di questa percentuale, lo farà un insieme di copia per quel tipo di blocco nella raccolta principale successiva, ripristinando in questo modo l'occupazione al prossimo al 100%. Il valore 0 disattiva lo spostamento. 

-   `bridge-implementation` = *implementazione di colmare* : Questo verrà impostato l'opzione di Bridge di Garbage Collection per consentire di risolvere i problemi di prestazioni di Garbage Collection. Esistono tre possibili valori: *vecchio* , *nuove* , *tarjan*.

-   `bridge-require-precise-merge`: Il Tarjan bridge contiene un'ottimizzazione che, in rare occasioni, potrebbe essere un oggetto di essere raccolti una Garbage Collection dopo il primo rilascio garbage. Tra cui questa opzione Disabilita che l'ottimizzazione, rendendo più prevedibili ma potenzialmente più lente cataloghi globali.

Ad esempio, per configurare il catalogo globale per un limite di dimensioni dell'heap di 128MB, aggiungere un nuovo file al progetto con un **azione di compilazione** di `AndroidEnvironment` con il contenuto: 

```shell
MONO_GC_PARAMS=soft-heap-limit=128m
```
