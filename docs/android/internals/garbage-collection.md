---
title: Garbage Collection
ms.topic: article
ms.prod: xamarin
ms.assetid: 298139E2-194F-4A58-BC2D-1D22231066C4
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/15/2018
ms.openlocfilehash: e27e9577957229f347b217a8920eac239799da15
ms.sourcegitcommit: 20ca85ff638dbe3a85e601b5eb09b2f95bda2807
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="garbage-collection"></a>Garbage Collection

Xamarin utilizza del Mono [semplice generazionale garbage collector](http://www.mono-project.com/docs/advanced/garbage-collector/sgen/). Si tratta di un garbage collector di contrassegnare lo sweep con due generazioni e *spazio LOB*, con due tipi di raccolte: 

-   Raccolte secondarie (raccoglie Gen0 heap) 
-   Raccolte principali (raccoglie Gen1 e oggetti di grandi dimensioni spazio heap). 

> [!NOTE]
> In assenza di una raccolta esplicita tramite [GC. Collect](xref:System.GC.Collect) le raccolte sono *su richiesta*, in base al momento le allocazioni di heap. *Non si tratta di un sistema di conteggio dei riferimenti*; oggetti *non appena non sono presenti riferimenti in sospeso non verranno raccolti*, o quando è stato terminato un ambito. Il Garbage Collector viene eseguito quando l'heap secondario ha esaurito la memoria per nuove allocazioni. Se sono non presenti allocazioni, non verrà eseguito.


Raccolte secondarie sono semplici e frequenti e vengono utilizzate per raccogliere gli oggetti allocati e messaggi non recapitabili di recente. Raccolte secondarie vengono eseguite dopo ogni MB alcuni degli oggetti allocati. Raccolte secondarie possono essere eseguite manualmente tramite la chiamata [GC. Raccogliere (0)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) 

Insiemi principali sono meno frequenti e costoso e vengono utilizzati per recuperare tutti gli oggetti inutilizzati. Raccolte principali vengono eseguite dopo l'esaurimento di memoria per le dimensioni dell'heap corrente (prima del ridimensionamento dell'heap). Raccolte principale possono essere eseguite manualmente tramite la chiamata [GC. Raccogliere ()](xref:System.GC.Collect) o chiamando [GC. Raccogliere (int)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) con l'argomento [GC. MaxGeneration](xref:System.GC.MaxGeneration). 



## <a name="cross-vm-object-collections"></a>Raccolte di oggetti tra VM

Esistono tre categorie di tipi di oggetto.

-   **Gli oggetti gestiti**: i tipi che si *non* ereditare [lang](https://developer.xamarin.com/api/type/Java.Lang.Object/) , ad esempio [System. String](xref:System.String). 
    Questi vengono raccolte in genere per il Garbage Collector. 

-   **Gli oggetti Java**: i tipi Java che sono presenti all'interno del runtime Android VM ma non esposta alla macchina virtuale Mono. Questi sono un'operazione noiosa e non verrà trattati ulteriormente. Questi vengono raccolti in genere dal runtime Android macchina virtuale. 

-   **Gli oggetti peer**: i tipi che implementano [IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) , ad esempio, tutti [lang](https://developer.xamarin.com/api/type/Java.Lang.Object/) e [Java.Lang.Throwable](https://developer.xamarin.com/api/type/Java.Lang.Throwable/) sottoclassi. Le istanze di questi tipi hanno due "halfs" un *peer gestito* e *peer nativo*. Il peer gestito è un'istanza della classe c#. Il peer nativo è un'istanza di una classe Java all'interno del runtime Android VM e c# [IJavaObject.Handle](https://developer.xamarin.com/api/property/Android.Runtime.IJavaObject.Handle/) proprietà contiene un riferimento globale JNI peer nativo. 


Esistono due tipi di peer nativo:

-   **Framework peer** : i tipi Java "Normal" che non conoscono i xamarin, ad esempio [android.content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/).

-   **Utente peer** : [Android Callable Wrapper](~/android/platform/java-integration/working-with-jni.md) che vengono generati in fase di compilazione per ogni sottoclasse lang presente all'interno dell'applicazione.


Sono presenti due macchine virtuali all'interno di un processo di xamarin, esistono due tipi di operazioni di garbage collection:

-   Raccolte di runtime Android 
-   Raccolte mono 

Runtime Android raccolte operano in genere, ma con uno svantaggio: un riferimento globale JNI viene considerato come una radice di Garbage Collection. Di conseguenza, se è presente un JNI globali fanno riferimento a mantenere un tempo di esecuzione Android VM, l'oggetto *Impossibile* raccolte, anche se è idoneo per la raccolta.

Raccolte mono sono in cui viene eseguita fun. Gli oggetti gestiti vengono raccolti normalmente. Oggetti peer vengono raccolti eseguendo il processo seguente:

1.  Tutti gli oggetti Peer idonei per la raccolta Mono dispongono i relativi riferimenti globali JNI sostituito con un riferimento globale debole JNI. 

2.  Viene richiamato un runtime Android GC di macchina virtuale. Qualsiasi istanza nativa peer può essere raccolti. 

3.  I riferimenti globali deboli JNI creati (1) vengono controllati. Se è stati raccolti il riferimento debole, l'oggetto Peer verrà raccolti. Se il riferimento debole è *non* stati raccolti, quindi il riferimento debole viene sostituito con un riferimento globale JNI e l'oggetto Peer non vengono raccolti. Nota: in API 14+, ciò significa che il valore restituito da `IJavaObject.Handle` possono essere modificati dopo un catalogo globale. 

Il risultato finale di tutto un'istanza di un oggetto Peer risiederanno, purché vi fa riferimento il codice gestito (ad esempio, archiviate in un `static` variabile) o a cui fa riferimento il codice Java. Inoltre, la durata del peer nativo verrà estesa oltre a ciò che accadrebbe in caso contrario in tempo reale, come peer nativo non potrà più essere ritirabile fino a quando non sia peer nativo e gestito peer ritirabile.


## <a name="object-cycles"></a>Cicli di oggetti

Oggetti peer sono logicamente presenti all'interno della macchina virtuale Mono sia il runtime Android. Ad esempio, un [Android.App.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/) istanza gestita peer avrà un corrispondente [android.app.Activity](http://developer.android.com/reference/android/app/Activity.html) istanza Java peer di framework. Tutti gli oggetti che ereditano da [lang](https://developer.xamarin.com/api/type/Java.Lang.Object/) dovrà hanno rappresentazioni in entrambe le macchine virtuali. 

Tutti gli oggetti che dispongono di rappresentazione in entrambe le macchine virtuali verranno hanno durate che vengono estesi rispetto agli oggetti che sono presenti solo all'interno di una singola macchina virtuale (ad esempio un [ `System.Collections.Generic.List<int>` ](xref:System.Collections.Generic.List%601)). La chiamata [GC. Raccogliere](xref:System.GC.Collect) non necessariamente raccogliere tali oggetti, come il Garbage Collector xamarin deve assicurarsi che l'oggetto non viene fatto riferimento da una macchina virtuale prima di raccolta. 

Per abbreviare la durata degli oggetti, [Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/) deve essere richiamato. Questo manuale "interromperà" la connessione per l'oggetto tra le due macchine virtuali per liberare il riferimento globale, consentendo in tal modo gli oggetti da raccogliere più velocemente. 


## <a name="automatic-collections"></a>Raccolte automatico

A partire da [versione 4.1.0](https://developer.xamarin.com/releases/android/mono_for_android_4/mono_for_android_4.1.0), xamarin esegue automaticamente un catalogo globale completo quando viene superata una soglia gref. Questa soglia è 90% del noto grefs massimo per la piattaforma: 1800 grefs nell'emulatore (2000 max) e 46800 grefs su hardware (massimo 52000). *Nota:* xamarin conta solo le grefs creato da [Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)e non riconoscerà su qualsiasi altro grefs creato nel processo. Si tratta di un approccio euristico *solo*. 

Quando viene eseguita una raccolta automatica, nel log di debug verrà stampato un messaggio simile al seguente:

```shell
I/monodroid-gc(PID): 46800 outstanding GREFs. Performing a full GC!
```

L'occorrenza di questo oggetto è non deterministico e può verificarsi in viaggio (ad esempio, durante il rendering della grafica). Se viene visualizzato questo messaggio, si consiglia di eseguire un'esplicita insieme in un' posizione oppure è possibile provare a [ridurre la durata degli oggetti peer](#Helping_the_GC). 

## <a name="gc-bridge-options"></a>Opzioni di Bridge GC

Xamarin offre la gestione della memoria trasparente con Android e il runtime Android. Viene implementato come un'estensione al garbage collector Mono chiamato il *GC Bridge*. 

Il Bridge di Garbage Collection funziona durante un'operazione Mono garbage collection e figure out quali peer oggetti deve loro verificare con l'heap di runtime Android "liveness". Il Bridge di GC rende questo aspetto effettuando i passaggi seguenti (in ordine):

1.  Forzare il grafico di riferimento mono degli oggetti peer non è raggiungibile in oggetti Java che rappresentano. 

2.  Eseguire un catalogo globale di Java.

3.  Verificare quali oggetti sono effettivamente messaggi non recapitabili. 

Questo processo complesso è ciò che consente alle sottoclassi di `Java.Lang.Object` liberamente riferimento gli oggetti, bensì rimuove le eventuali restrizioni su quali Java oggetti possono essere associati a c#. A causa di questa complessità, il processo di bridge può risultare molto costoso e comporta un notevole pause in un'applicazione. Se l'applicazione ha riscontrato pause significative, vale la pena analizzando una delle tre implementazioni di GC Bridge seguenti: 

-   **Tarjan** -una completamente nuova progettazione del Bridge di Garbage Collection in base alle [algoritmo di Robert Tarjan e con le versioni precedenti fare riferimento a propagazione](http://en.wikipedia.org/wiki/Tarjan's_strongly_connected_components_algorithm).
    Ha prestazioni ottimali con i carichi di lavoro, ma include anche la condivisione del codice sperimentale più grande. 

-   **Nuovo** -una revisione principale del codice originale, la correzione di due istanze di comportamento quadratica ma mantenendo l'algoritmo principale (in base a [algoritmo del Kosaraju](http://en.wikipedia.org/wiki/Kosaraju's_algorithm) per ricerca fortemente connesso i componenti). 

-   **Vecchio** -l'implementazione originale (considerato più stabile dei tre). Il bridge che un'applicazione deve usare se si tratta di `GC_BRIDGE` le pause sono accettabili. 


L'unico modo per capire meglio il cui funzionamento del Bridge di catalogo globale viene sperimentato in un'applicazione e l'analisi dell'output. Esistono due modi per raccogliere i dati per quelle di benchmarking: 

-   **Abilitare la registrazione** -abilitare la registrazione (come descritto nella [configurazione](~/android/internals/garbage-collection.md) sezione) per ogni opzione di catalogo globale Bridge, quindi acquisire e confrontare l'output di log da ogni impostazione. Controllare il `GC` messaggi per ogni opzione, in particolare il `GC_BRIDGE` messaggi. Per le applicazioni non interattive sono tollerabile, ma le pause sopra 60 ms per applicazioni estremamente interattive (ad esempio, giochi) sono un problema, viene sospeso fino a 150 ms. 

-   **Attivare l'accounting bridge** -accounting Bridge visualizzerà il costo medio degli oggetti cui fa riferimento a ogni oggetto coinvolto nel processo di bridge. Ordinamento di queste informazioni per dimensioni fornirà gli hint da ciò che contiene la maggiore quantità di oggetti aggiuntivi. 


Per specificare quali `GC_BRIDGE` opzione un'applicazione deve utilizzare, passare `bridge-implementation=old`, `bridge-implementation=new` o `bridge-implementation=tarjan` per il `MONO_GC_PARAMS` variabile di ambiente, ad esempio: 

```shell
MONO_GC_PARAMS=bridge-implementation=tarjan
```

L'impostazione predefinita è **Tarjan**. Se si rileva una regressione, potrebbe essere necessario impostare questa opzione su **precedente**. Inoltre, è possibile scegliere di utilizzare più stabile **precedente** opzione **Tarjan** non produce un miglioramento delle prestazioni. 

<a name="Helping_the_GC" />

## <a name="helping-the-gc"></a>Consentire il catalogo globale

Esistono più modi per consentire il Garbage Collector per ridurre i tempi di utilizzo e raccolta di memoria.



### <a name="disposing-of-peer-instances"></a>Eliminazione di istanze di Peer

Il catalogo globale è una vista incompleta del processo e potrebbe non essere eseguita in memoria è bassa poiché il Garbage Collector non sa che la memoria è insufficiente. 

Ad esempio, un'istanza di un [lang](https://developer.xamarin.com/api/type/Java.Lang.Object/) tipo o un tipo derivato è di almeno 20 byte (soggette a modifiche senza preavviso e così via, ecc.). 
[Callable Wrapper gestiti](~/android/internals/architecture.md) non aggiungere i membri di istanza aggiuntive, pertanto quando si dispone di un [Android.Graphics.Bitmap](https://developer.xamarin.com/api/type/Android.Graphics.Bitmap/) istanza che fa riferimento a un blob di 10MB di memoria, non è in grado GC di xamarin che &ndash; il catalogo globale verrà visualizzato un oggetto a 20 byte e sarà in grado di determinare ciò che è collegato agli oggetti allocati runtime Android che mantiene i 10MB di memoria attiva. 

È spesso necessario per il Garbage Collector. Purtroppo, *GC. AddMemoryPressure()* e *GC. RemoveMemoryPressure()* non sono supportati, pertanto se si *conoscere* liberata un grafico di oggetti allocati Java grandi potrebbe essere necessario chiamare manualmente [GC. Collect](xref:System.GC.Collect) al prompt dei comandi di Garbage Collection per rilasciare il lato Java memoria oppure è possibile in modo esplicito smaltire *lang* sottoclassi, il mapping tra il callable wrapper gestito e l'istanza di Java di rilievo. Ad esempio, vedere [1084 Bug](http://bugzilla.xamarin.com/show_bug.cgi?id=1084#c6). 


> [!NOTE]
> È necessario essere *estremamente* attenzione durante l'eliminazione di `Java.Lang.Object` istanze sottoclasse.

Per ridurre al minimo la possibilità di danneggiamento della memoria, osservare le linee guida seguenti quando si chiama `Dispose()`.


#### <a name="sharing-between-multiple-threads"></a>La condivisione tra più thread

Se il *Java o gestiti* istanza può essere condivise tra più thread, *non deve essere `Dispose()`d*, **mai**. Ad esempio, [ `Typeface.Create()` ](https://developer.xamarin.com/api/member/Android.Graphics.Typeface.Create/(System.String%2cAndroid.Graphics.TypefaceStyle)) può restituire un *istanza memorizzata nella cache*. Se più thread forniscono gli stessi argomenti, ottengono il *stesso* istanza. Di conseguenza, `Dispose()`sta eseguendo un'operazione del `Typeface` istanza da un thread può invalidare altri thread, che può comportare `ArgumentException`s da `JNIEnv.CallVoidMethod()` (tra gli altri) perché l'istanza è stata eliminata da un altro thread. 


#### <a name="disposing-bound-java-types"></a>Eliminazione di tipi Java associato

Se l'istanza è di un tipo di linguaggio associato, è possibile eliminare l'istanza di *purché* l'istanza non può essere riutilizzato da codice gestito *e* l'istanza di Java non può essere condivisa tra thread (vedere la precedente `Typeface.Create()` discussione). (Effettua questa operazione potrebbe risultare difficile.) La volta successiva che l'istanza di Java entra in codice gestito, un *nuova* verrà creato per il wrapper. 

Ciò è spesso utile quando si tratta di Drawables e altre istanze di risorse:

```csharp
using (var d = Drawable.CreateFromPath ("path/to/filename"))
    imageView.SetImageDrawable (d);
```

Sopra è sicuro in quanto il Peer che [Drawable.CreateFromPath()](https://developer.xamarin.com/api/member/Android.Graphics.Drawables.Drawable.CreateFromPath/) restituisce farà riferimento a un peer di Framework, *non* un peer dell'utente. Il `Dispose()` chiamare alla fine del `using` blocco interromperà la relazione tra gestito [Drawable](https://developer.xamarin.com/api/type/Android.Graphics.Drawables.Drawable/) e framework [Drawable](http://developer.android.com/reference/android/graphics/drawable/Drawable.html) istanze, consentendo l'istanza di Java da raccolte come il runtime Android è necessario. Questo sarebbe *non* sicuri se l'istanza di Peer di cui per un peer dell'utente; qui viene utilizzato "external" informazioni *conoscere* che il `Drawable` non può fare riferimento a un peer dell'utente e pertanto il `Dispose()` chiamare è sicuro. 


#### <a name="disposing-other-types"></a>Eliminazione di altri tipi 

Se l'istanza fa riferimento a un tipo che non è un'associazione di un tipo Java (ad esempio un oggetto personalizzato `Activity`), **non** chiamare `Dispose()` a meno che non si *conoscere* che nessun codice Java chiamerà i metodi sottoposti a override su tale istanza. In caso contrario comporta [ `NotSupportedException`s](~/android/internals/architecture.md#Premature_Dispose_Calls). 

Ad esempio, se dispone di un oggetto personalizzato fare clic su listener:

```csharp
partial class MyClickListener : Java.Lang.Object, View.IOnClickListener {
    // ...
}
```

Si *non* eliminare questa istanza, come Java verrà eseguito un tentativo di richiamare metodi su di esso in futuro:

```csharp
// BAD CODE; DO NOT USE
Button b = FindViewById<Button> (Resource.Id.myButton);
using (var listener = new MyClickListener ())
    b.SetOnClickListener (listener);
```


#### <a name="using-explicit-checks-to-avoid-exceptions"></a>Usando i controlli espliciti per evitare eccezioni

Se è stato implementato un [Java.Lang.Object.Dispose](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose(System.Boolean)/) overload di metodo, evitare di toccare oggetti che coinvolgono JNI. In questo modo è possibile creare un *doppia dispose* situazione che rende possibile il codice (se) tenta di accedere a un oggetto Java sottostante che è già stato sottoposto a garbage collection. Questa operazione genera un'eccezione simile al seguente: 

```shell
System.ArgumentException: 'jobject' must not be IntPtr.Zero.
Parameter name: jobject
at Android.Runtime.JNIEnv.CallVoidMethod
```

Spesso questa situazione si verifica quando il metodo dispose prima di un oggetto fa sì che un membro diventano null e quindi un tentativo di accesso successivo su questo membro null genererà un'eccezione generata. In particolare, dell'oggetto `Handle` (un'istanza gestita che collega all'istanza di Java sottostante) viene invalidato per il prima dispose, ma il codice gestito tenta comunque di accedere a questa istanza di Java sottostante anche se non è più disponibile (vedere [ Gestito Callable Wrapper](~/android/internals/architecture.md#Managed_Callable_Wrappers) per ulteriori informazioni sul mapping tra le istanze di Java e le istanze gestite). 

Un buon metodo per evitare questa eccezione consiste nel verificare in modo esplicito nel `Dispose` metodo che è ancora valido; è il mapping tra l'istanza gestita e l'istanza di Java sottostante, verificare se l'oggetto `Handle` è null (`IntPtr.Zero`) prima di accedere ai relativi membri. Ad esempio, `Dispose` metodo accede un `childViews` oggetto: 

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

Se un dispose iniziale passa cause `childViews` disporre di un oggetto non valido `Handle`, `for` accesso ciclo genererà un `ArgumentException`. Tramite l'aggiunta esplicita `Handle` null controllo prima del primo `childViews` accedere, le operazioni seguenti `Dispose` metodo impedisce la generazione dell'eccezione che si verificano: 

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

Ogni volta che un'istanza di un `Java.Lang.Object` tipo o una sottoclasse viene analizzato durante il Garbage Collector, l'intero *oggetto grafico* che l'istanza fa riferimento a deve inoltre essere analizzato. L'oggetto grafico è il set di istanze di un oggetto che fa riferimento l'istanza"radice", *più* tutti gli elementi a cui fa riferimento l'istanza radice di quale si intende, in modo ricorsivo. 

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

Quando `BadActivity` è costruito, l'oggetto grafico conterrà le 10004 istanze (1 x `BadActivity`, 1 x `strings`, 1 x `string[]` contenute `strings`, 10000 x istanze di stringa), *tutti* di cui dovrà essere ogni volta che l'analisi di `BadActivity` istanza viene analizzata. 

Ciò può avere effetti negativi sui periodi di raccolta, determinando un miglioramento GC tempi di pausa. 

Consente di catalogo globale da *riduzione* le dimensioni degli oggetti grafici che contengono una radice da istanze di peer di utente. Nell'esempio precedente, questa operazione può essere eseguita tramite lo spostamento `BadActivity.strings` in una classe separata che non eredita da lang: 

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

Raccolte secondarie possono essere eseguite manualmente tramite la chiamata [GC. Collect(0)](xref:System.GC.Collect). Le raccolte secondarie sono semplici (rispetto alle raccolte principale), ma con un impatto significativo fixed costo, pertanto non si desidera attivare li troppo spesso e deve avere un tempo di pochi millisecondi. 

Se l'applicazione dispone di un "ciclo di servizio" in cui la stessa operazione viene eseguita ripetutamente, potrebbe essere consigliabile eseguire manualmente una raccolta secondaria una volta completato il ciclo di servizio. Ciclo di esempio include: 

-  Il ciclo di rendering di un singolo frame del gioco.
-  L'intera interazione con una finestra di dialogo di applicazione specificata (apertura, la compilazione di chiusura) 
-  Un gruppo di richieste di rete per l'aggiornamento e sincronizzazione dati delle app.



## <a name="major-collections"></a>Raccolte principali

Raccolte principale possono essere eseguite manualmente tramite la chiamata [GC. Collect](xref:System.GC.Collect) o `GC.Collect(GC.MaxGeneration)`. 

Essi devono essere eseguite raramente e potrebbe essere un tempo di secondo in un dispositivo Android stile durante la raccolta di un heap di 512MB. 

Raccolte principali devono solo essere richiamate manualmente, mai: 

-   Alla fine di lunga durata imposta cicli e quando una lunga pausa non presenta un problema all'utente. 

-   All'interno di un override [Android.App.Activity.OnLowMemory()](https://developer.xamarin.com/api/member/Android.App.Activity.OnLowMemory/) metodo. 



## <a name="diagnostics"></a>Diagnostica

Per visualizzare i riferimenti globali vengono creati ed eliminati, è possibile impostare il [debug.mono.log](~/android/troubleshooting/index.md) proprietà di sistema per contenere [ *gref* ](~/android/troubleshooting/index.md) e/o [ *gc*](~/android/troubleshooting/index.md). 



## <a name="configuration"></a>Configurazione

Il garbage collector xamarin può essere configurato impostando la `MONO_GC_PARAMS` variabile di ambiente. Le variabili di ambiente possono essere impostate con un'azione di compilazione di [AndroidEnvironment](~/android/deploy-test/environment.md).

Il `MONO_GC_PARAMS` variabile di ambiente è un elenco delimitato da virgole dei parametri seguenti: 

-   `nursery-size` = *dimensioni* : imposta la dimensione del infermeria. Le dimensioni vengono specificate in byte e devono essere una potenza di due. I suffissi `k` , `m` e `g` può essere utilizzato per specificare kg, mega e gigabyte, rispettivamente. Infermeria è la prima generazione (di due). Una maggiore vivaio consente in genere di velocizzare il programma ma ovviamente verrà utilizzata più memoria. Infermeria predefinito dimensioni pari a 512 kb. 

-   `soft-heap-limit` = *dimensioni* : il numero massimo di destinazione gestiti il consumo di memoria per l'app. Quando l'utilizzo della memoria è inferiore al valore specificato, il Garbage Collector è ottimizzato per il tempo di esecuzione (un numero inferiore di raccolte). 
    Di sopra di questo limite, il Garbage Collector è ottimizzato per l'utilizzo della memoria (altre raccolte). 

-   `evacuation-threshold` = *soglia* : imposta la soglia di evacuazione in percentuale. Il valore deve essere un numero intero compreso tra 0 e 100. Il valore predefinito è 66. Se la fase di apertura della raccolta rileva che l'occupazione del tipo di blocco di heap specifico è minore di questa percentuale, eseguirà una raccolta di copia per il tipo di blocco nella raccolta delle principale Avanti ripristinando in questo modo occupazione al prossimo al 100%. Il valore 0 disattiva lo spostamento. 

-   `bridge-implementation` = *implementazione di Bridge* : questo imposterà l'opzione di Bridge di catalogo globale per i problemi di prestazioni indirizzo GC. Esistono tre possibili valori: *precedente* , *nuova* , *tarjan*.

-   `bridge-require-precise-merge`: Il Tarjan bridge contiene un'ottimizzazione che, in rari casi, potrebbe essere un oggetto di essere raccolti un GC dopo diventa garbage. Tra cui questa opzione Disabilita che l'ottimizzazione, rendendo più prevedibile ma potenzialmente più lenta cataloghi globali.

Ad esempio, per configurare il catalogo globale per un limite di dimensione heap di 128MB, aggiungere un nuovo file al progetto con un **azione di compilazione** di `AndroidEnvironment` con il contenuto: 

```shell
MONO_GC_PARAMS=soft-heap-limit=128m
```
