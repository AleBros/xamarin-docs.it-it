---
title: Architettura
ms.prod: xamarin
ms.assetid: 7DC22A08-808A-DC0C-B331-2794DD1F9229
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 815e3ddf44ae94b6b26a325599de1f4c1f6714a8
ms.sourcegitcommit: ae34d048aeb23a99678ae768cdeef0c92ca36b51
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51681540"
---
# <a name="architecture"></a>Architettura

Le applicazioni xamarin. Android vengono eseguite all'interno dell'ambiente di esecuzione in Mono.
Questa esecuzione ambiente viene eseguito side-by-side con la macchina virtuale del Runtime Android (ART). Entrambi gli ambienti di runtime eseguiti sul kernel Linux ed espongono diverse API per il codice utente che consente agli sviluppatori di accedere al sistema sottostante. Il runtime di Mono è scritto in linguaggio C.

È possibile usare la [System](xref:System), [System.IO](xref:System.IO), [System.Net](xref:System.Net) e librerie di classi il resto di .NET per le funzioni di sistema operativo Linux sottostanti di accesso.

In Android, la maggior parte delle strutture del sistema, ad esempio Audio, immagini, OpenGL e telefonia non sono disponibile direttamente alle applicazioni native, sono esposte solo attraverso le API di Java Runtime Android che si trova in uno dei [Java](https://developer.xamarin.com/api/namespace/Java.Lang/). * gli spazi dei nomi o le [Android](https://developer.xamarin.com/api/namespace/Android/). * gli spazi dei nomi. L'architettura è più o meno simile al seguente:

[![Diagramma di Mono ed elementi grafici sopra il kernel e sotto .NET/Java e le associazioni](architecture-images/architecture1.png)](architecture-images/architecture1.png#lightbox)

Gli sviluppatori di xamarin. Android di accedere alle varie funzionalità nel sistema operativo mediante la chiamata alle API .NET che sanno (per l'accesso a basso livello) o tramite le classi esposte negli spazi dei nomi Android che fornisce un collegamento alle API esposte da Java il Runtime di Android.

Per altre informazioni su come le classi Android comunicano con le classi di Runtime di Android, vedere la [progettazione di API](~/android/internals/api-design.md) documento.


## <a name="application-packages"></a>Pacchetti dell'applicazione

Pacchetti di applicazioni Android sono contenitori con estensione ZIP con un *file con estensione apk* estensione di file. I pacchetti dell'applicazione xamarin. Android hanno la stessa struttura e il layout come normali pacchetti Android, con le aggiunte seguenti:

-   Gli assembly dell'applicazione (livello di integrità contenente) sono *archiviate* non compresso all'interno di *assembly* cartella. Durante il processo di avvio nella versione si basa il *file con estensione apk* viene *mmap()* ed nel processo e gli assembly vengono caricati dalla memoria. Ciò consente all'avvio dell'app più veloce, come gli assembly non sono necessario essere estratto prima dell'esecuzione.  
-   *Nota:* le informazioni sul percorso di Assembly, ad esempio [Assembly.Location](xref:System.Reflection.Assembly.Location) e [Assembly.CodeBase](xref:System.Reflection.Assembly.CodeBase)
    *non può essere ritenuto affidabile* nella versione compilazioni. Non sono presenti come voci di file System distinti e non dispongono di alcun percorso utilizzabile.


-   Le librerie native che contiene il runtime di Mono sono presenti all'interno di *file con estensione apk* . Un'applicazione xamarin. Android deve contenere le librerie native per le architetture di Android desired/destinazione, ad esempio *armeabi* , *armeabi-v7a* , *x86* . Le applicazioni xamarin. Android non è possibile eseguire su una piattaforma a meno che non contiene le librerie di runtime appropriato.


Contengono anche le applicazioni xamarin. Android *Android Callable Wrapper* per consentire di Android effettuare chiamate nel codice gestito.



## <a name="android-callable-wrappers"></a>Android Callable Wrapper

- **Android callable wrapper** sono una [JNI](http://en.wikipedia.org/wiki/Java_Native_Interface) bridge usati in qualsiasi momento il runtime di Android deve richiamare il codice gestito. Android callable wrapper sono metodi come virtuali può essere sottoposto a override e possono essere implementate interfacce Java. Vedere le [Java Integration Overview](~/android/platform/java-integration/index.md) doc per altre informazioni.


<a name="Managed_Callable_Wrappers" />

## <a name="managed-callable-wrappers"></a>Callable Wrapper gestito

Callable wrapper gestiti sono un bridge JNI usati in qualsiasi momento il codice gestito deve richiamare il codice Android e forniscono supporto per l'override dei metodi virtuali e implementazione delle interfacce Java. L'intera [Android](https://developer.xamarin.com/api/namespace/Android/). * e relativi spazi dei nomi vengono gestiti callable wrapper generato tramite [binding con estensione jar](~/android/platform/binding-java-library/index.md).
Wrapper chiamabile gestiti sono responsabili per la conversione tra i tipi gestiti e Android e richiamo dei metodi di piattaforma Android sottostante tramite JNI.

Ognuno creato wrapper chiamabile gestito contiene un riferimento di global Java, che è accessibile tramite il [Android.Runtime.IJavaObject.Handle](https://developer.xamarin.com/api/property/Android.Runtime.IJavaObject.Handle/) proprietà. I riferimenti globali vengono utilizzati per fornire il mapping tra le istanze di Java e le istanze gestite. I riferimenti globali sono una risorsa limitata: gli emulatori consentono solo 2000 riferimenti globali a esistere contemporaneamente, mentre la maggior parte dei componenti hardware consente riferimenti globali oltre 52.000 esista in un momento.

Per rilevare quando i riferimenti globali vengono creati ed eliminati, è possibile impostare il [debug.mono.log](~/android/troubleshooting/index.md) proprietà di sistema per contenere [gref](~/android/troubleshooting/index.md).

I riferimenti globali possono essere liberati chiamando esplicitamente [Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/) sul callable wrapper gestito. Questa operazione rimuoverà il mapping tra l'istanza di Java e l'istanza gestita e consentire all'istanza di Java da raccogliere. Se l'istanza di Java viene nuovamente accessibile dal codice gestito, verrà creato un nuovo callable wrapper gestito per tale.

È necessario attentamente quando elimini gestiti Callable Wrapper se l'istanza può essere inavvertitamente condivisa tra thread, come l'istanza di eliminazione influirà riferimenti da qualsiasi altro thread. Per la massima sicurezza, solo `Dispose()` delle istanze che sono state allocate via `new` *oppure* dai metodi che si *sapere* sempre allocare nuove istanze e non memorizzato nella cache le istanze che è possibile causare accidentale condivisione tra i thread di istanza.



## <a name="managed-callable-wrapper-subclasses"></a>Le sottoclassi Callable Wrapper gestito

Le sottoclassi wrapper chiamabile gestito sono in cui può trovarsi tutta la logica specifica dell'applicazione "interessante". Questi includono custom [Android.App.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/) sottoclassi (ad esempio il [Activity1](https://github.com/xamarin/monodroid-samples/blob/master/HelloM4A/Activity1.cs#L13) tipo nel modello di progetto predefinito). (In particolare, sono tutti *Java.Lang.Object* sottoclassi di ripetere l'operazione *non* contengono una [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) attributo personalizzato o [ RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) viene *false*, ovvero l'impostazione predefinita.)

Come gestiti callable wrapper, gestito sottoclassi callable wrapper contengono anche un riferimento globale, accessibile tramite il [Java.Lang.Object.Handle](https://developer.xamarin.com/api/property/Java.Lang.Object.Handle/) proprietà. Proprio come con callable wrapper gestiti, i riferimenti globali possono essere liberati in modo esplicito chiamando [Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/).
A differenza dei wrapper chiamabile gestiti, *molta attenzione* deve essere eseguita prima dell'eliminazione di tali istanze, come *Dispose ()*- ing dell'istanza interromperà il mapping tra l'istanza di Java (un'istanza di un Android Callable Wrapper) e l'istanza gestita.


### <a name="java-activation"></a>Attivazione di Java

Quando un [Android Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) viene creato da Java, il costruttore ACW farà sì che il costruttore c# corrispondente da richiamare. Ad esempio, il ACW per *MainActivity* conterrà un costruttore predefinito che verrà richiamato *MainActivity*del costruttore predefinito. (Questa operazione viene eseguita tramite il *TypeManager.Activate()* chiamare all'interno dei costruttori ACW.)

È un'altra firma del costruttore di conseguenza: il *(IntPtr, JniHandleOwnership)* costruttore. Il *(IntPtr, JniHandleOwnership)* costruttore viene richiamato ogni volta che un oggetto di Java viene esposta al codice gestito e un Callable Wrapper gestito deve essere creata per gestire l'handle JNI. Questa operazione viene in genere eseguita automaticamente.

Esistono due scenari in cui il *(IntPtr, JniHandleOwnership)* costruttore deve essere fornito manualmente in una sottoclasse Callable Wrapper gestiti:

1. [Android](https://developer.xamarin.com/api/type/Android.App.Application/) è una sottoclasse. *Application* è speciale; il valore predefinito *dell'applicazione* costruttore verrà *mai* essere richiamati e il [(IntPtr, JniHandleOwnership) costruttore deve invece essere fornito ](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/SanityTests/Hello.cs#L105).

2. Chiamata del metodo virtuale da un costruttore di classe di base.


Si noti che (2) è un'astrazione debole. In Java, c#, le chiamate ai metodi virtuali da un costruttore richiamano sempre l'implementazione del metodo più derivato. Ad esempio, il [costruttore TextView (contesto, AttributeSet, int)](https://developer.xamarin.com/api/constructor/Android.Widget.TextView.TextView/p/Android.Content.Context/Android.Util.IAttributeSet/System.Int32/) richiama il metodo virtuale [TextView.getDefaultMovementMethod()](http://developer.android.com/reference/android/widget/TextView.html#getDefaultMovementMethod()), che viene associato come il [ Proprietà TextView.DefaultMovementMethod](https://developer.xamarin.com/api/property/Android.Widget.TextView.DefaultMovementMethod/).
Di conseguenza, se un tipo [LogTextBox](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs) dovesse (1) [sottoclasse TextView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L26), (2) [override TextView.DefaultMovementMethod](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L45)e (3) [attivare un'istanza di quel classe tramite il codice XML,](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Resources/layout/log_text_box_1.xml#L29) sottoposto a override *DefaultMovementMethod* proprietà sarebbe possibile richiamare prima che il costruttore ACW soddisfacente per l'esecuzione e che verrebbe eseguito prima il C# soddisfacente costruttore eseguire.

Questo è supportato da un'istanza LogTextBox tramite il [LogTextView (IntPtr, JniHandleOwnership)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L28) costruttore quando l'istanza ACW LogTextBox innanzitutto entra in codice gestito e quindi richiamando il [ (Contesto, IAttributeSet, int) LogTextBox](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L41) costruttore *sulla stessa istanza* quando viene eseguito il costruttore ACW.

Ordine degli eventi:

1.  Layout XML viene caricato in un [ContentView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox1.cs#L41).

2.  Crea un'istanza dell'oggetto grafico di Layout Android e crea un'istanza di un'istanza di *monodroid.apidemo.LogTextBox* , per il ACW *LogTextBox* .

3.  Il *monodroid.apidemo.LogTextBox* costruttore esegue le [android.widget.TextView](http://developer.android.com/reference/android/widget/TextView.html#TextView%28android.content.Context,%20android.util.AttributeSet%29) costruttore.

4.  Il *TextView* costruttore richiama *monodroid.apidemo.LogTextBox.getDefaultMovementMethod()* .

5.  *monodroid.apidemo.LogTextBox.getDefaultMovementMethod()* richiama *LogTextBox.n_getDefaultMovementMethod()* , che richiama *TextView.n_GetDefaultMovementMethod()* , che richiama [Java.Lang.Object.GetObject&lt;TextView&gt; (handle, JniHandleOwnership.DoNotTransfer)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) .

6.  *Java.Lang.Object.GetObject&lt;TextView&gt;()* controlla se è già presente un corrispondente c# dell'istanza *gestire* . Se è presente, viene restituito. In questo scenario, non c'è, pertanto *Object.GetObject&lt;T&gt;()* necessario crearne uno.

7.  *Object.GetObject&lt;T&gt;()* cerca i *LogTextBox (IntPtr, JniHandleOwneship)* costruttore, esso viene richiamato, crea un mapping tra *gestire* e istanza creata e restituisce l'istanza creata.

8.  *TextView.n_GetDefaultMovementMethod()* richiama il *LogTextBox.DefaultMovementMethod* getter della proprietà.

9.  Il controllo torna al *android.widget.TextView* costruttore, che termina l'esecuzione.

10. Il *monodroid.apidemo.LogTextBox* costruttore esegue, richiamare *TypeManager.Activate()* .

11. Il *LogTextBox (contesto, IAttributeSet, int)* costruttore esegue *nella stessa istanza creata in (7)* .

12. Se (IntPtr, JniHandleOwnership) costruttore non è possibile trovare e quindi verrà generata una System.MissingMethodException](xref:System.MissingMethodException).

<a name="Premature_Dispose_Calls" />

### <a name="premature-dispose-calls"></a>Chiama Dispose (prematura)

È presente un mapping tra l'istanza corrispondente di c# e un handle JNI. Java.Lang.Object.Dispose() interrompe questo mapping. Se un handle JNI rientro nel codice gestito dopo che il mapping è stato interrotto, sembra l'attivazione, Java e il *(IntPtr, JniHandleOwnership)* costruttore verrà controllato la presenza e richiamato. Se il costruttore non esiste, verrà generata un'eccezione.

Ad esempio, data la sottoclasse Wraper chiamabile gestita seguente:

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

Se è creare un'istanza di Dispose () e causare il gestiti Callable Wrapper essere ricreate:

```csharp
var list = new JavaList<IJavaObject>();
list.Add (new ManagedValue ("value"));
list [0].Dispose ();
Console.WriteLine (list [0].ToString ());
```

Il programma scompare:

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

Se contengono la sottoclasse di un *(IntPtr, JniHandleOwnership)* costruttore, un *nuove* viene creata l'istanza del tipo. Di conseguenza, l'istanza verrà visualizzato "perdere" tutti i dati di istanza, perché è una nuova istanza. Si noti che il valore è null.

```shell
I/mono-stdout( 2993): [Managed: Value=]
```

Solo *Dispose ()* di gestiti sottoclassi callable wrapper quando si è certi che l'oggetto di Java non verrà utilizzato più o sottoclasse non contiene alcun dato di istanza e un *(IntPtr, JniHandleOwnership)* costruttore è stato specificato.



## <a name="application-startup"></a>Avvio dell'applicazione

Quando un'attività, servizio, e così via viene avviato, Android dapprima il controllo per verificare se è già presente un processo in esecuzione per ospitare le attività/service/e così via. Se tale processo non esiste, verrà creato un nuovo processo, il [androidmanifest. XML](http://developer.android.com/guide/topics/manifest/manifest-intro.html) è di lettura e il tipo specificato nella [ /manifest/application/@android:name ](http://developer.android.com/guide/topics/manifest/application-element.html#nm) attributo viene caricato e creare un'istanza. Successivamente, tutti i tipi specificati dal [ /manifest/application/provider/@android:name ](http://developer.android.com/guide/topics/manifest/provider-element.html#nm) valori di attributo vengono create istanze e hanno loro [ContentProvider.attachInfo%28)](https://developer.xamarin.com/api/member/Android.Content.ContentProvider.AttachInfo/p/Android.Content.Context/Android.Content.PM.ProviderInfo/) metodo richiamato. Hook di xamarin. Android in ciò aggiungendo una *mono. MonoRuntimeProvider* *ContentProvider* al file androidmanifest. XML durante il processo di compilazione. Il *mono. MonoRuntimeProvider.attachInfo()* metodo è responsabile per il caricamento di runtime di Mono nel processo.
Qualsiasi tentativo di usare Mono prima che questo avrà esito negativo. ( *Nota*: è per questo motivo i tipi cui sottoclasse [Android](https://developer.xamarin.com/api/type/Android.App.Application/) necessario fornire un [(IntPtr, JniHandleOwnership) costruttore](https://github.com/xamarin/monodroid-samples/blob/a9e8ef23/SanityTests/Hello.cs#L103), poiché è l'istanza dell'applicazione creata prima può essere inizializzato Mono).

Dopo aver completato l'inizializzazione del processo, `AndroidManifest.xml` viene consultato per trovare il nome della classe di attività/service/ECC da avviare. Ad esempio, il [ /manifest/application/activity/@android:name attributo](http://developer.android.com/guide/topics/manifest/activity-element.html#nm) viene usato per determinare il nome di un'attività da caricare. Per le attività, questo tipo deve ereditare [android.app.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/).
Il tipo specificato viene caricato tramite [forName ()](http://developer.android.com/reference/java/lang/Class.html#forName(java.lang.String)) (che è necessario che il tipo sia un linguaggio, quindi digitare Android Callable Wrapper), quindi creata un'istanza. Creazione di un'istanza di Android Callable Wrapper attiverà la creazione di un'istanza del tipo c# corrispondente. Android quindi richiamerà [Activity.onCreate(Bundle)](http://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle)) , che produrrà il corrispondente [Activity.OnCreate(Bundle)](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) verrà richiamato e si è per le competizioni.
