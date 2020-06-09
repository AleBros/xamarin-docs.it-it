---
title: Architettura
ms.prod: xamarin
ms.assetid: 7DC22A08-808A-DC0C-B331-2794DD1F9229
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: 8cbcc0cf99dd82dc9a943c63d9e9544482581896
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571415"
---
# <a name="architecture"></a>Architettura

Le applicazioni Novell. Android vengono eseguite nell'ambiente di esecuzione mono.
Questo ambiente di esecuzione viene eseguito side-by-side con la macchina virtuale di Android Runtime (ART). Entrambi gli ambienti di runtime vengono eseguiti sul kernel Linux ed espongono diverse API al codice utente che consente agli sviluppatori di accedere al sistema sottostante. Il runtime di mono è scritto nel linguaggio C.

Per accedere alle funzionalità di sistema operativo Linux sottostanti, è possibile usare il [sistema](xref:System), [System.io](xref:System.IO), [System.NET](xref:System.Net) e le altre librerie di classi .NET.

In Android la maggior parte delle funzionalità di sistema, ad esempio audio, grafica, OpenGL e telefonia, non sono direttamente disponibili per le applicazioni native, ma vengono esposte solo tramite le API Java del runtime di Android che risiedono in uno degli spazi dei nomi [Java](xref:Java.Lang). * o [Android](xref:Android). *. L'architettura è approssimativamente simile alla seguente:

[![Diagramma di mono e arte sopra il kernel e sotto le associazioni .NET/Java +](architecture-images/architecture1.png)](architecture-images/architecture1.png#lightbox)

Gli sviluppatori Novell. Android accedono alle varie funzionalità del sistema operativo chiamando le API .NET che conoscono (per l'accesso di basso livello) o usando le classi esposte negli spazi dei nomi Android che forniscono un Bridge alle API Java esposte dal runtime di Android.

Per altre informazioni sul modo in cui le classi Android comunicano con le classi di runtime di Android, vedere il documento di [progettazione dell'API](~/android/internals/api-design.md) .

## <a name="application-packages"></a>Pacchetti dell'applicazione

I pacchetti di applicazioni Android sono contenitori ZIP con estensione *apk* . I pacchetti dell'applicazione Novell. Android hanno la stessa struttura e il layout dei normali pacchetti Android, con le aggiunte seguenti:

- Gli assembly dell'applicazione (contenenti IL) vengono *archiviati* non compressi nella cartella degli *assembly* . Durante l'avvio del processo nelle build di rilascio, il file con *estensione APK* è *mmap ()* ed è nel processo e gli assembly vengono caricati dalla memoria. Questo consente un avvio più veloce delle app, perché non è necessario estrarre gli assembly prima dell'esecuzione.  
- *Nota:* Informazioni sul percorso degli assembly, ad esempio [assembly. location](xref:System.Reflection.Assembly.Location) e [assembly.](xref:System.Reflection.Assembly.CodeBase) *non è possibile fare affidamento su* codebase nelle build di rilascio. Non esistono come voci di file System distinte e non hanno un percorso utilizzabile.

- Le librerie native che contengono il runtime di mono sono presenti nel file con *estensione APK* . Un'applicazione Novell. Android deve contenere librerie native per le architetture Android desiderate/di destinazione, ad esempio *ARMEABI* , *ARMEABI-v7a* , *x86* . Le applicazioni Novell. Android non possono essere eseguite su una piattaforma a meno che non contenga le librerie di runtime appropriate.

Le applicazioni Novell. Android contengono anche i *wrapper richiamabili Android* per consentire a Android di effettuare chiamate nel codice gestito.

## <a name="android-callable-wrappers"></a>Android Callable Wrapper

- **Android Callable Wrapper** è un Bridge [JNI](https://en.wikipedia.org/wiki/Java_Native_Interface) che viene usato ogni volta che il runtime di Android deve richiamare il codice gestito. Android Callable Wrapper è il modo in cui è possibile eseguire l'override di metodi virtuali e le interfacce Java possono essere implementate. Per ulteriori informazioni, vedere il documento [Panoramica dell'integrazione Java](~/android/platform/java-integration/index.md) .

<a name="Managed_Callable_Wrappers"></a>

## <a name="managed-callable-wrappers"></a>Wrapper richiamabili gestiti

I wrapper richiamabili gestiti sono un Bridge JNI che viene usato ogni volta che il codice gestito deve richiamare codice Android e fornire supporto per l'override di metodi virtuali e l'implementazione di interfacce Java. L'intero [Android](xref:Android). * e gli spazi dei nomi correlati sono wrapper richiamabili gestiti generati tramite [associazione. jar](~/android/platform/binding-java-library/index.md).
I wrapper richiamabili gestiti sono responsabili della conversione tra i tipi gestiti e Android e il richiamo dei metodi di piattaforma Android sottostanti tramite JNI.

Ogni Managed Callable Wrapper creato include un riferimento globale Java, accessibile tramite la proprietà [Android. Runtime. IJavaObject. handle](xref:Android.Runtime.IJavaObject.Handle) . I riferimenti globali vengono usati per fornire il mapping tra istanze Java e istanze gestite. I riferimenti globali sono una risorsa limitata: gli emulatori consentono l'esistenza solo di 2000 riferimenti globali alla volta, mentre la maggior parte dell'hardware consente la presenza di oltre 52.000 riferimenti globali alla volta.

Per tenere traccia del momento in cui i riferimenti globali vengono creati ed eliminati, è possibile impostare la proprietà di sistema [debug. mono. log](~/android/troubleshooting/index.md) in modo che contenga [Gref](~/android/troubleshooting/index.md).

I riferimenti globali possono essere liberati in modo esplicito chiamando [java. lang. Object. Dispose ()](xref:Java.Lang.Object.Dispose) sul wrapper gestito chiamabile. In questo modo verrà rimosso il mapping tra l'istanza di Java e l'istanza gestita e verrà consentita la raccolta dell'istanza java. Se l'istanza di Java viene nuovamente accessibile dal codice gestito, verrà creato un nuovo wrapper gestito chiamabile.

È necessario prestare attenzione quando si eliminano i wrapper richiamabili gestiti se l'istanza può essere condivisa inavvertitamente tra i thread, poiché l'eliminazione dell'istanza influirà sui riferimenti da altri thread. Per garantire la massima sicurezza, solo `Dispose()` le istanze che sono state allocate tramite `new` *o* da metodi che si *conoscono* sempre allocano nuove istanze e non istanze memorizzate nella cache che possono causare una condivisione accidentale delle istanze tra i thread.

## <a name="managed-callable-wrapper-subclasses"></a>Sottoclassi gestite Callable Wrapper

Le sottoclassi di wrapper richiamabili gestite sono in cui è possibile che sia presente tutta la logica specifica dell'applicazione "interessante". Sono incluse le sottoclassi [Android. app. Activity](xref:Android.App.Activity) personalizzate, ad esempio il tipo [Activity1](https://github.com/xamarin/monodroid-samples/blob/master/HelloM4A/Activity1.cs#L13) nel modello di progetto predefinito. In particolare, si tratta di sottoclassi *java. lang. Object* che *non* contengono un attributo personalizzato [RegisterAttribute](xref:Android.Runtime.RegisterAttribute) o [RegisterAttribute. DoNotGenerateAcw](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw) è *false*, che corrisponde all'impostazione predefinita.

Analogamente ai wrapper richiamabili gestiti, le sottoclassi gestite Callable Wrapper contengono anche un riferimento globale, accessibile tramite la proprietà [java. lang. Object. handle](xref:Java.Lang.Object.Handle) . Analogamente ai wrapper richiamabili gestiti, i riferimenti globali possono essere liberati in modo esplicito chiamando [java. lang. Object. Dispose ()](xref:Java.Lang.Object.Dispose).
A differenza dei wrapper richiamabili gestiti, è necessario prestare particolare *attenzione* prima di eliminare tali istanze, in quanto *Dispose ()*-ing dell'istanza interrompe il mapping tra l'istanza Java (un'istanza di un wrapper Android Callable Wrapper) e l'istanza gestita.

### <a name="java-activation"></a>Attivazione Java

Quando un [Android callable wrapper](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) viene creato da Java, il costruttore ACW provocherà la chiamata del costruttore C# corrispondente. Ad esempio, ACW per *MainActivity* conterrà un costruttore predefinito che richiama il costruttore predefinito di *MainActivity*. Questa operazione viene eseguita tramite la chiamata a *TypeManager. Activate ()* all'interno dei costruttori ACW.)

È presente un'altra firma del costruttore di conseguenza: il costruttore *(IntPtr, JniHandleOwnership)* . Il costruttore *(IntPtr, JniHandleOwnership)* viene richiamato ogni volta che un oggetto Java viene esposto al codice gestito ed è necessario costruire un wrapper gestito chiamabile per gestire l'handle JNI. Questa operazione viene in genere eseguita automaticamente.

Esistono due scenari in cui il costruttore *(IntPtr, JniHandleOwnership)* deve essere fornito manualmente in una sottoclasse wrapper gestita chiamabile:

1. [Android. app. Application](xref:Android.App.Application) è sottoclassata. L' *applicazione* è speciale. il costruttore *Applicaton* predefinito *non verrà mai* richiamato e il [costruttore (IntPtr, JniHandleOwnership) deve essere fornito](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/SanityTests/Hello.cs#L105).

2. Chiamata del metodo virtuale da un costruttore della classe base.

Si noti che (2) è un'astrazione di perdite. In Java, come in C#, le chiamate ai metodi virtuali da un costruttore richiamano sempre l'implementazione del metodo più derivato. Ad esempio, il [Costruttore TextView (context, attribut, int)](xref:Android.Widget.TextView#ctor*) richiama il metodo virtuale [TextView. getDefaultMovementMethod ()](https://developer.android.com/reference/android/widget/TextView.html#getDefaultMovementMethod()), che è associato come [Proprietà TextView. DefaultMovementMethod](xref:Android.Widget.TextView.DefaultMovementMethod).
Pertanto, se un tipo [LogTextBox](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs) era (1) [sottoclasse TextView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L26), (2) [override TextView. DefaultMovementMethod](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L45)e (3) [attiva un'istanza della classe tramite XML,](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Resources/layout/log_text_box_1.xml#L29) la proprietà *DefaultMovementMethod* sottoposta a override verrebbe richiamata prima che il costruttore ACW avesse la possibilità di essere eseguito e si verificherebbe prima che il costruttore C# avesse la possibilità di eseguire.

Questa operazione è supportata creando un'istanza di LogTextBox tramite il costruttore [LogTextView (IntPtr, JniHandleOwnership)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L28) quando l'istanza di ACW LogTextBox immette per la prima volta il codice gestito e quindi richiama il costruttore [LogTextBox (context, IAttributeSet, int)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L41) *nella stessa istanza* quando viene eseguito il costruttore ACW.

Ordine degli eventi:

1. Il layout XML viene caricato in un [contentView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox1.cs#L41).

2. Android crea un'istanza dell'oggetto layout Graph e crea un'istanza di *monodroid. apidemo. LogTextBox* , ACW per *LogTextBox* .

3. Il costruttore *monodroid. apidemo. LogTextBox* esegue il costruttore [Android. widget. TextView](https://developer.android.com/reference/android/widget/TextView.html#TextView%28android.content.Context,%20android.util.AttributeSet%29) .

4. Il costruttore *TextView* richiama *monodroid. apidemo. LogTextBox. getDefaultMovementMethod ()* .

5. *monodroid. apidemo. LogTextBox. getDefaultMovementMethod ()* richiama *LogTextBox. n_getDefaultMovementMethod ()* , che richiama *TextView. n_GetDefaultMovementMethod ()* , che richiama il [linguaggio Java. lang. Object. GetObject &lt; TextView &gt; (handle, JniHandleOwnership. DoNotTransfer)](xref:Java.Lang.Object.GetObject*) .

6. *Java. lang. Object. GetObject &lt; TextView &gt; ()* verifica se esiste già un'istanza C# corrispondente per *handle* . In caso contrario, viene restituito. In questo scenario non è presente, quindi *Object. GetObject &lt; t &gt; ()* deve crearne uno.

7. *Oggetto. GetObject &lt; T &gt; ()* Cerca il costruttore *LogTextBox (IntPtr, JniHandleOwneship)* , lo richiama, crea un mapping tra *handle* e l'istanza creata e restituisce l'istanza creata.

8. *TextView. n_GetDefaultMovementMethod ()* richiama il getter della proprietà *LogTextBox. DefaultMovementMethod* .

9. Il controllo viene restituito al costruttore *Android. widget. TextView* , che termina l'esecuzione.

10. Il costruttore *monodroid. apidemo. LogTextBox* viene eseguito, richiamando *TypeManager. Activate ()* .

11. Il costruttore *LogTextBox (context, IAttributeSet, int)* viene eseguito nella *stessa istanza creata in (7)* .

12. Se il costruttore (IntPtr, JniHandleOwnership) non viene trovato, viene generata un'eccezione System. MissingMethodException] (xrif: System. MissingMethodException).

<a name="Premature_Dispose_Calls"></a>

### <a name="premature-dispose-calls"></a>Chiamate Dispose () premature

Esiste un mapping tra un handle JNI e l'istanza C# corrispondente. Java. lang. Object. Dispose () interrompe questo mapping. Se un handle JNI immette codice gestito dopo che il mapping è stato danneggiato, avrà un aspetto simile all'attivazione Java e il costruttore *(IntPtr, JniHandleOwnership)* verrà controllato e richiamato. Se il costruttore non esiste, verrà generata un'eccezione.

Ad esempio, data la seguente sottoclasse gestita Callable Wraper:

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

Se si crea un'istanza, Dispose () e si ricreerà il wrapper gestito chiamabile:

```csharp
var list = new JavaList<IJavaObject>();
list.Add (new ManagedValue ("value"));
list [0].Dispose ();
Console.WriteLine (list [0].ToString ());
```

Il programma andrà a morire:

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

Se la sottoclasse contiene un costruttore *(IntPtr, JniHandleOwnership)* , verrà creata una *nuova* istanza del tipo. Di conseguenza, l'istanza apparirà "perde" tutti i dati dell'istanza, perché si tratta di una nuova istanza. Si noti che il valore è null.

```shell
I/mono-stdout( 2993): [Managed: Value=]
```

Solo *Dispose ()* di sottoclassi di wrapper gestite chiamabili quando si è certi che l'oggetto Java non verrà più usato o che la sottoclasse non contiene dati dell'istanza e è stato fornito un costruttore *(IntPtr, JniHandleOwnership)* .

## <a name="application-startup"></a>Avvio dell'applicazione

Quando viene avviata un'attività, un servizio e così via, Android verificherà prima di tutto se è già in esecuzione un processo per ospitare l'attività/il servizio e così via. Se non esiste alcun processo di questo tipo, verrà creato un nuovo processo, [file AndroidManifest. XML](https://developer.android.com/guide/topics/manifest/manifest-intro.html) verrà letto e il tipo specificato nell' [/manifest/application/@android:name](https://developer.android.com/guide/topics/manifest/application-element.html#nm) attributo viene caricato e ne viene creata un'istanza. Viene quindi creata un'istanza di tutti i tipi specificati dai [/manifest/application/provider/@android:name](https://developer.android.com/guide/topics/manifest/provider-element.html#nm) valori di attributo e viene richiamato il metodo [ContentProvider. attachInfo %28)](xref:Android.Content.ContentProvider.AttachInfo*) . Novell. Android si associa a questa operazione aggiungendo un *mono. MonoRuntimeProvider* *ContentProvider* in file AndroidManifest. XML durante il processo di compilazione. *Mono. Il metodo MonoRuntimeProvider. attachInfo ()* è responsabile del caricamento del runtime di mono nel processo.
Eventuali tentativi di usare mono prima di questo punto avranno esito negativo. *Nota*: questo è il motivo per cui i tipi con la sottoclasse [Android. app. Application](xref:Android.App.Application) devono fornire un [costruttore (IntPtr, JniHandleOwnership)](https://github.com/xamarin/monodroid-samples/blob/a9e8ef23/SanityTests/Hello.cs#L103), perché l'istanza dell'applicazione viene creata prima che mono possa essere inizializzato.

Al termine dell'inizializzazione del processo, `AndroidManifest.xml` viene consultato per trovare il nome della classe dell'attività/servizio e così via per l'avvio. Ad esempio, l' [ /manifest/application/activity/@android:name attributo](https://developer.android.com/guide/topics/manifest/activity-element.html#nm) viene utilizzato per determinare il nome di un'attività da caricare. Per le attività, questo tipo deve ereditare [Android. app. Activity](xref:Android.App.Activity).
Il tipo specificato viene caricato tramite [Class. forName ()](https://developer.android.com/reference/java/lang/Class.html#forName(java.lang.String)) (che richiede che il tipo sia un tipo Java, quindi i wrapper chiamabili Android) e quindi ne venga creata un'istanza. La creazione di un'istanza di Android Callable Wrapper attiverà la creazione di un'istanza del tipo C# corrispondente. Android richiamerà quindi [Activity. OnCreate (bundle)](https://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle)) , che causerà la richiamata dell' [attività corrispondente. OnCreate (bundle)](xref:Android.App.Activity.OnCreate*) e delle corse.
