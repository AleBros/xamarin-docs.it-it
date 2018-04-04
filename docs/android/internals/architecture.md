---
title: Architettura
ms.prod: xamarin
ms.assetid: 7DC22A08-808A-DC0C-B331-2794DD1F9229
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: f4be88a1eabb3fa3cca733690a3f097a03516272
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="architecture"></a>Architettura

Applicazioni di xamarin vengono eseguite all'interno dell'ambiente di esecuzione Mono.
Questa esecuzione ambiente viene eseguito side-by-side con la macchina virtuale di Runtime Android (ART). Entrambi gli ambienti di runtime sono eseguiti sul kernel Linux ed espongono diverse API per il codice utente che consente agli sviluppatori di accedere al sistema sottostante. Il runtime Mono viene scritto nel linguaggio C.

È possibile utilizzare il [sistema](http://msdn.microsoft.com/en-us/library/system.aspx), [System.IO](http://msdn.microsoft.com/en-us/library/system.io.aspx), [System.Net](http://msdn.microsoft.com/en-us/library/system.net.aspx) e librerie di classi il resto di .NET per accedere le funzioni di sistema operativo Linux sottostante.

La maggior parte delle strutture di sistema come Audio, immagini, OpenGL e telefonia in Android, non disponibili direttamente alle applicazioni native, sono visibili solo tramite le API di Java Runtime Android che si trovano in uno del [Java](https://developer.xamarin.com/api/namespace/Java.Lang/). * spazi dei nomi o [Android](https://developer.xamarin.com/api/namespace/Android/). * spazi dei nomi. L'architettura è quasi simile al seguente:

[![Diagramma di Mono ed elementi grafici di sopra del kernel e sotto .NET/Java + associazioni](architecture-images/architecture1.png)](architecture-images/architecture1.png#lightbox)

Gli sviluppatori di xamarin accedere le varie funzionalità nel sistema operativo chiama le API .NET che sappiano (per l'accesso di basso livello) o utilizzando le classi esposte negli spazi dei nomi Android che fornisce un ponte per le API Java che sono esposte da il Runtime di Android.

Per ulteriori informazioni sulla modalità di comunicazione con le classi di Runtime Android le classi per Android, vedere il [progettazione API](~/android/internals/api-design.md) documento.


## <a name="application-packages"></a>Pacchetti dell'applicazione

Pacchetti di applicazioni Android sono contenitori ZIP con un *apk* estensione di file. Pacchetti di applicazioni xamarin avere la stessa struttura e layout come pacchetti Android normale, con le seguenti aggiunte:

-   Gli assembly dell'applicazione (contenente IL) sono *archiviati* non compressa all'interno di *assembly* cartella. Durante il processo di avvio in versione compila il *apk* è *mmap()* ed in processo e gli assembly vengono caricati dalla memoria. In tal modo avvio più rapido delle app, come gli assembly non devono essere estratti prima dell'esecuzione. - *Nota:* le informazioni sul percorso di Assembly, ad esempio [Assembly.Location](https://developer.xamarin.com/api/property/System.Reflection.Assembly.Location/) e [Assembly.CodeBase](https://developer.xamarin.com/api/property/System.Reflection.Assembly.CodeBase/)
    *non può essere ritenuto affidabile* nella versione compilazioni. Non sono disponibili come voci filesystem distinti e non dispongono di alcun percorso utilizzabile.


-   Librerie native contenente il runtime Mono presenti all'interno di *apk* . Un'applicazione di xamarin deve contenere le librerie native per le architetture di Android/destinazione desiderata, ad esempio *armeabi* , *armeabi v7a* , *x86* . Applicazioni xamarin non è possibile eseguire su una piattaforma a meno che non contiene le librerie di runtime appropriato.


Le applicazioni di xamarin contengono anche *Android Callable Wrapper* per consentire di Android per effettuare chiamate nel codice gestito.



## <a name="android-callable-wrappers"></a>Android Callable Wrapper

- **Android callable wrapper** sono un [JNI](http://en.wikipedia.org/wiki/Java_Native_Interface) bridge che viene utilizzato ogni volta che il runtime Android è necessario richiamare il codice gestito. Android callable wrapper sono metodi come virtuali può essere sottoposto a override e interfacce Java possono essere implementate. Vedere il [panoramica dell'integrazione Java](~/android/platform/java-integration/index.md) doc per ulteriori informazioni.


<a name="Managed_Callable_Wrappers" />

## <a name="managed-callable-wrappers"></a>Callable Wrapper gestito

Gestito callable wrapper sono un bridge JNI che viene utilizzato ogni volta che il codice gestito deve richiamare codice Android e forniscono il supporto per eseguire l'override di metodi virtuali e implementazione di interfacce di Java. L'intero [Android](https://developer.xamarin.com/api/namespace/Android/). * e i relativi spazi dei nomi vengono gestiti callable wrapper generato tramite [JAR associazione](~/android/platform/binding-java-library/index.md).
Callable wrapper gestiti sono responsabili per la conversione tra tipi gestiti e Android e richiamo dei metodi di piattaforma Android sottostante tramite JNI.

Ognuno creato wrapper richiamabili gestito contiene un riferimento globale Java, è possibile accedere tramite il [Android.Runtime.IJavaObject.Handle](https://developer.xamarin.com/api/property/Android.Runtime.IJavaObject.Handle/) proprietà. Riferimenti globali vengono utilizzati per fornire il mapping tra le istanze di Java e le istanze gestite. I riferimenti globali sono una risorsa limitata: emulatori consentono solo 2000 riferimenti globali esistente alla volta, mentre la maggior parte dei riferimenti globali 52.000 oltre a esistere in un momento.

Per visualizzare i riferimenti globali vengono creati ed eliminati, è possibile impostare il [debug.mono.log](~/android/troubleshooting/index.md) proprietà di sistema per contenere [gref](~/android/troubleshooting/index.md).

I riferimenti globali possono essere liberati in modo esplicito chiamando [Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/) sul callable wrapper gestito. Per rimuovere il mapping tra l'istanza di Java e l'istanza gestita e l'istanza di Java da raccogliere. Se l'istanza di Java nuovamente dal codice gestito, che verrà creato un nuovo callable wrapper gestito.

Attenzione deve far funzionare durante l'eliminazione di Callable Wrapper gestiti, se l'istanza può essere inavvertitamente condivisa tra thread, come l'istanza di eliminazione avrà un impatto riferimenti da qualsiasi altro thread. Per la massima sicurezza, solo `Dispose()` di istanze che sono state allocate tramite `new` *o* dai metodi che si *conoscere* sempre allocare nuove istanze e le istanze non memorizzati nella cache che è possibile causare istanza accidentale di condivisione tra thread.



## <a name="managed-callable-wrapper-subclasses"></a>Le sottoclassi Callable Wrapper gestiti

Wrapper richiamabili gestito sottoclassi sono tutta la logica specifica dell'applicazione "interessante" può luogo di residenza. Questi includono custom [Android.App.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/) sottoclassi (ad esempio il [Activity1](https://github.com/xamarin/monodroid-samples/blob/master/HelloM4A/Activity1.cs#L13) tipo nel modello di progetto predefinito). (In particolare, sono tutti *lang* sottoclassi che *non* contengono un [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) attributo personalizzato o [ RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) è *false*, ovvero l'impostazione predefinita.)

Come gestiti callable wrapper, gestito sottoclassi callable wrapper inoltre contengano un riferimento globale, accessibile tramite il [Java.Lang.Object.Handle](https://developer.xamarin.com/api/property/Java.Lang.Object.Handle/) proprietà. Come con callable wrapper gestito, i riferimenti globali possono essere liberati in modo esplicito chiamando [Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/).
A differenza di callable wrapper gestito, *molta attenzione* deve essere eseguita prima dell'eliminazione di tali istanze, come *Dispose*sta eseguendo un'operazione dell'istanza interromperà il mapping tra l'istanza di Java (un'istanza di un Android Callable Wrapper) e l'istanza gestita.


### <a name="java-activation"></a>Attivazione di Java

Quando un [Android Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) viene creato da Java, il costruttore ACW causerà il corrispondente c# costruttore da richiamare. Ad esempio, il ACW per *MainActivity* conterrà un costruttore predefinito che verrà richiamato *MainActivity*del costruttore predefinito. (Questa operazione viene eseguita tramite il *TypeManager.Activate()* chiamare all'interno dei costruttori ACW.)

È un altra firma del costruttore di conseguenza: il *(IntPtr, JniHandleOwnership)* costruttore. Il *(IntPtr, JniHandleOwnership)* costruttore viene richiamato ogni volta che un oggetto Java è esposto al codice gestito e deve essere creata per gestire l'handle JNI Callable Wrapper gestito. Questa operazione viene in genere eseguita automaticamente.

Esistono due scenari in cui il *(IntPtr, JniHandleOwnership)* costruttore deve essere fornito manualmente in una sottoclasse Callable Wrapper gestiti:

1. [Android.App.Application](https://developer.xamarin.com/api/type/Android.App.Application/) è una sottoclasse. *Applicazione* è speciale; il valore predefinito *applicazione* costruttore verrà *mai* da richiamare e [(IntPtr, JniHandleOwnership) costruttore debba essere fornito invece ](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/SanityTests/Hello.cs#L105).

2. Chiamata di metodo virtuale da un costruttore di classe di base.


Si noti che (2) è un'astrazione debole. In Java, come c#, le chiamate ai metodi virtuali da un costruttore richiamano sempre l'implementazione del metodo più derivata. Ad esempio, il [costruttore TextView (contesto, AttributeSet, int)](https://developer.xamarin.com/api/constructor/Android.Widget.TextView.TextView/p/Android.Content.Context/Android.Util.IAttributeSet/System.Int32/) richiama il metodo virtuale [TextView.getDefaultMovementMethod()](http://developer.android.com/reference/android/widget/TextView.html#getDefaultMovementMethod()), che è associato come il [ Proprietà TextView.DefaultMovementMethod](https://developer.xamarin.com/api/property/Android.Widget.TextView.DefaultMovementMethod/).
Pertanto, se un tipo [LogTextBox](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs) dovesse (1) [sottoclasse TextView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L26), (2) [override TextView.DefaultMovementMethod](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L45)e (3) [attivare un'istanza di classe tramite il codice XML,](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Resources/layout/log_text_box_1.xml#L29) sottoposto a override *DefaultMovementMethod* proprietà verrebbe richiamata prima che il costruttore ACW aveva la possibilità di eseguire e si verifica prima che il costruttore c# aveva la possibilità di eseguire.

Questa funzionalità è supportata da un'istanza LogTextBox tramite il [LogTextView (IntPtr, JniHandleOwnership)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L28) costruttore quando l'istanza LogTextBox ACW passa prima volta codice gestito e quindi richiamare il [ LogTextBox (contesto, IAttributeSet, int)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L41) costruttore *nella stessa istanza* quando viene eseguito il costruttore ACW.

Ordine degli eventi:

1.  Layout XML viene caricato in un [ContentView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox1.cs#L41).

2.  Crea un'istanza dell'oggetto grafico del Layout, Android e crea un'istanza di *monodroid.apidemo.LogTextBox* , il ACW per *LogTextBox* .

3.  Il *monodroid.apidemo.LogTextBox* costruttore viene eseguito il [android.widget.TextView](http://developer.android.com/reference/android/widget/TextView.html#TextView%28android.content.Context,%20android.util.AttributeSet%29) costruttore.

4.  Il *TextView* costruttore richiama *monodroid.apidemo.LogTextBox.getDefaultMovementMethod()* .

5.  *monodroid.apidemo.LogTextBox.getDefaultMovementMethod()* richiama *LogTextBox.n_getDefaultMovementMethod()* , che richiama *TextView.n_GetDefaultMovementMethod()* , che richiama [Java.Lang.Object.GetObject&lt;TextView&gt; (handle, JniHandleOwnership.DoNotTransfer)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) .

6.  *Java.Lang.Object.GetObject&lt;TextView&gt;()* controlla se è già presente un corrispondente c# istanza per *gestire* . Se è presente, viene restituito. In questo scenario, non è, pertanto *Object.GetObject&lt;T&gt;()* necessario crearne uno.

7.  *Object.GetObject&lt;T&gt;()* Cerca il *LogTextBox (IntPtr, JniHandleOwneship)* costruttore richiama, crea un mapping tra *gestire* e l'istanza creata e restituisce l'istanza creata.

8.  *TextView.n_GetDefaultMovementMethod()* richiama il *LogTextBox.DefaultMovementMethod* getter di proprietà.

9.  Controllo viene restituito il *android.widget.TextView* costruttore, che termina l'esecuzione.

10. Il *monodroid.apidemo.LogTextBox* costruttore viene eseguito, richiamare *TypeManager.Activate()* .

11. Il *LogTextBox (contesto, IAttributeSet, int)* costruttore esegue *nella stessa istanza creata (7)* .

12. ...


Se (IntPtr, JniHandleOwnership) Impossibile trovare il costruttore, un [System. MissingMethodException](https://developer.xamarin.com/api/type/System.MissingMethodException/) verrà generata.


<a name="Premature_Dispose_Calls" />

### <a name="premature-dispose-calls"></a>Chiamate prematura Dispose)

È presente un mapping tra un handle JNI e l'istanza corrispondente di c#. Java.Lang.Object.Dispose() interrompe questo mapping. Se un handle JNI immette il codice gestito, dopo il mapping è stato interrotto, sembra che l'attivazione, Java e *(IntPtr, JniHandleOwnership)* costruttore verrà controllato la presenza e richiamato. Se il costruttore non esiste, verrà generata un'eccezione.

Ad esempio, data la sottoclasse gestiti Callable Wraper seguente:

```csharp
class ManagedValue : Java.Lang.Object {

    public string Value {get; private set;}

    public ManagedValue (string value)
    {
        Value = value;
    }

    public override string ToString ()
    {
        return string.Format ("[Managed: Value={0}]", Value);
    }
}
```

Se si crea un'istanza, Dispose () e causare il gestiti Callable Wrapper essere nuovamente creato:

```csharp
var list = new JavaList<IJavaObject>();
list.Add (new ManagedValue ("value"));
list [0].Dispose ();
Console.WriteLine (list [0].ToString ());
```

Il programma verrà die:

```shell
E/mono    ( 2906): Unhandled Exception: System.NotSupportedException: Unable to activate instance of type Scratch.PrematureDispose.ManagedValue from native handle 4051c8c8 --->
System.MissingMethodException: No constructor found for Scratch.PrematureDispose.ManagedValue::.ctor(System.IntPtr, Android.Runtime.JniHandleOwnership)
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateProxy (System.Type type, IntPtr handle, JniHandleOwnership transfer) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateInstance (IntPtr handle, JniHandleOwnership transfer, System.Type targetType) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   --- End of inner exception stack trace ---
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateInstance (IntPtr handle, JniHandleOwnership transfer, System.Type targetType) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Lang.Object.GetObject (IntPtr handle, JniHandleOwnership transfer, System.Type type) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Lang.Object._GetObject[IJavaObject] (IntPtr handle, JniHandleOwnership transfer) [0x00000
```

Se la sottoclasse contiene un *(IntPtr, JniHandleOwnership)* costruttore, un *nuova* verrà creata l'istanza del tipo. Di conseguenza, l'istanza risulterà "perdere" tutti i dati di istanza, perché è una nuova istanza. Si noti che il valore è null.

```shell
I/mono-stdout( 2993): [Managed: Value=]
```

Solo *Dispose* di gestiti sottoclassi callable wrapper, quando si è certi che l'oggetto Java non verrà più utilizzato, o la sottoclasse non contiene alcun dato di istanza e un *(IntPtr, JniHandleOwnership)* costruttore è stato specificato.



## <a name="application-startup"></a>Avvio dell'applicazione

Quando un'attività, il servizio, e così via viene avviato, Android dapprima il controllo per verificare se è già un processo in esecuzione per ospitare l'attività, servizio e così via. Se tale processo non esiste, verrà creato un nuovo processo, il [AndroidManifest.xml](http://developer.android.com/guide/topics/manifest/manifest-intro.html) viene letto e nel tipo specificato di [ /manifest/application/@android:name ](http://developer.android.com/guide/topics/manifest/application-element.html#nm) attributo viene caricato e creare un'istanza. Successivamente, tutti i tipi specificati dal [ /manifest/application/provider/@android:name ](http://developer.android.com/guide/topics/manifest/provider-element.html#nm) valori di attributo vengono create istanze e di loro [ContentProvider.attachInfo%28)](https://developer.xamarin.com/api/member/Android.Content.ContentProvider.AttachInfo/p/Android.Content.Context/Android.Content.PM.ProviderInfo/) metodo richiamato. Xamarin hook questo aggiungendo un *mono. MonoRuntimeProvider* *provider di contenuti* a AndroidManifest.xml durante il processo di compilazione. Il *mono. MonoRuntimeProvider.attachInfo()* metodo è responsabile di caricamento del runtime Mono nel processo.
Qualsiasi tentativo di utilizzare Mono prima che questo avrà esito negativo. ( *Nota*: perché i tipi che creano una sottoclasse [Android.App.Application](https://developer.xamarin.com/api/type/Android.App.Application/) necessario fornire un [(IntPtr, JniHandleOwnership) costruttore](https://github.com/xamarin/monodroid-samples/blob/a9e8ef23/SanityTests/Hello.cs#L103), come l'istanza di applicazione è prima di creare può essere inizializzato Mono.)

Una volta completata l'inizializzazione del processo, `AndroidManifest.xml` viene consultato per trovare il nome della classe di attività/service/ECC da avviare. Ad esempio, il [ /manifest/application/activity/@android:name attributo](http://developer.android.com/guide/topics/manifest/activity-element.html#nm) viene utilizzato per determinare il nome di un'attività da caricare. Per le attività, questo tipo deve ereditare [android.app.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/).
Il tipo specificato viene caricato tramite [forName ()](http://developer.android.com/reference/java/lang/Class.html#forName(java.lang.String)) (che richiede che il tipo sia un linguaggio, pertanto digitare Android Callable Wrapper), quindi creata un'istanza. Creazione di un'istanza di Android Callable Wrapper verrà attivata la creazione di un'istanza del tipo c# corrispondente. Android richiamerà quindi [Activity.onCreate(Bundle)](http://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle)) , causando il corrispondente [Activity.OnCreate(Bundle)](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) da richiamare e si è disattivata per la race condition che.
