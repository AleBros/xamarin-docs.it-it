---
title: Uso di JNI e Novell. Android
description: Novell. Android consente la scrittura di app C# Android con anziché Java. Con Novell. Android sono disponibili diversi assembly che forniscono binding per le librerie Java, inclusi mono. Android. dll e mono. Android. GoogleMaps. dll. Tuttavia, le associazioni non vengono fornite per ogni possibile libreria Java e i binding forniti potrebbero non associare tutti i tipi e i membri Java. Per usare i tipi e i membri Java non associati, è possibile usare l'interfaccia Java Native (JNI). Questo articolo illustra come usare JNI per interagire con i tipi e i membri Java dalle applicazioni Novell. Android.
ms.prod: xamarin
ms.assetid: A417DEE9-7B7B-4E35-A79C-284739E3838E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 4d4274770263b120e856cf8db01a71f7ed124a63
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027174"
---
# <a name="working-with-jni-and-xamarinandroid"></a>Uso di JNI e Novell. Android

_Novell. Android consente la scrittura di app C# Android con anziché Java. Con Novell. Android sono disponibili diversi assembly che forniscono binding per le librerie Java, inclusi mono. Android. dll e mono. Android. GoogleMaps. dll. Tuttavia, le associazioni non vengono fornite per ogni possibile libreria Java e i binding forniti potrebbero non associare tutti i tipi e i membri Java. Per usare i tipi e i membri Java non associati, è possibile usare l'interfaccia Java Native (JNI). Questo articolo illustra come usare JNI per interagire con i tipi e i membri Java dalle applicazioni Novell. Android._

## <a name="overview"></a>Panoramica

Non è sempre necessario o possibile creare un wrapper gestito chiamabile (MCW) per richiamare il codice Java. In molti casi, il JNI "inline" è perfettamente accettabile e utile per l'uso monouso di membri Java non associati. Spesso è più semplice usare JNI per richiamare un solo metodo su una classe Java anziché generare un'intera associazione. jar.

Novell. Android fornisce l'assembly `Mono.Android.dll`, che fornisce un binding per la libreria `android.jar` di Android. I tipi e i membri non presenti all'interno `Mono.Android.dll` e i tipi non presenti all'interno di `android.jar` possono essere utilizzati dall'associazione manuale. Per associare tipi e membri Java, è possibile usare l' **interfaccia nativa Java** (**JNI**) per cercare i tipi, leggere e scrivere i campi e richiamare i metodi.

L'API JNI in Novell. Android è concettualmente molto simile all'API `System.Reflection` in .NET: consente di cercare i tipi e i membri in base al nome, a leggere e scrivere i valori dei campi, richiamare metodi e altro ancora. È possibile usare JNI e l'attributo personalizzato `Android.Runtime.RegisterAttribute` per dichiarare metodi virtuali che possono essere associati per supportare l'override di. È possibile associare le interfacce in modo che possano essere implementate in C#.

In questo documento viene illustrato quanto segue:

- In che modo JNI fa riferimento ai tipi.
- Come cercare, leggere e scrivere campi.
- Modalità di ricerca e richiamo di metodi.
- Come esporre i metodi virtuali per consentire l'override dal codice gestito.
- Come esporre le interfacce.

## <a name="requirements"></a>Requisiti

JNI, esposto tramite lo [spazio dei nomi Android. Runtime. JNIEnv](xref:Android.Runtime.JNIEnv), è disponibile in tutte le versioni di Novell. Android.
Per associare tipi e interfacce Java, è necessario usare Novell. Android 4,0 o versione successiva.

## <a name="managed-callable-wrappers"></a>Wrapper richiamabili gestiti

Un **wrapper gestito callable wrapper** (**MCW**) è un *Binding* per una classe o un'interfaccia Java che esegue il wrapping di tutti i macchinari JNI C# , in modo che il codice client non debba preoccuparsi della complessità sottostante di JNI. La maggior parte delle `Mono.Android.dll` è costituita da wrapper richiamabili gestiti.

I wrapper richiamabili gestiti servono due scopi:

1. Incapsulare l'uso di JNI in modo che il codice client non debba conoscere la complessità sottostante.
1. Consente di eseguire tipi di sottoclassi Java e implementare interfacce Java.

Il primo scopo è esclusivamente per praticità e incapsulamento della complessità, in modo che i consumer dispongano di un set semplice e gestito di classi da usare. Questa operazione richiede l'uso dei diversi membri [JNIEnv](xref:Android.Runtime.JNIEnv) , come descritto più avanti in questo articolo. Tenere presente che i wrapper richiamabili gestiti non sono strettamente necessari &ndash; l'uso di JNI "inline" è perfettamente accettabile ed è utile per l'uso monouso di membri Java non associati. L'implementazione di sottoclassi e interfacce richiede l'uso di wrapper richiamabili gestiti.

## <a name="android-callable-wrappers"></a>Android Callable Wrapper

Android Callable Wrapper (ACW) è necessario ogni volta che il runtime di Android (ART) deve richiamare il codice gestito; questi wrapper sono necessari perché non è possibile registrare le classi con l'arte in fase di esecuzione.
In particolare, la funzione [defineClass](https://docs.oracle.com/javase/6/docs/technotes/guides/jni/spec/functions.html#wp15986) JNI non è supportata dal runtime di Android. I wrapper richiamabili Android comportano quindi la mancanza di supporto per la registrazione dei tipi runtime.

Ogni volta che il codice Android deve eseguire un metodo virtuale o di interfaccia sottoposto a override o implementato nel codice gestito, Novell. Android deve fornire un proxy Java in modo che questo metodo venga inviato al tipo gestito appropriato. Questi tipi di proxy Java sono codice Java che hanno la stessa classe di base e l'elenco di interfacce Java come tipo gestito, implementando gli stessi costruttori e dichiarando i metodi di interfaccia e di classe base sottoposti a override.

Android Callable Wrapper vengono generati dal programma **monodroid. exe** durante il [processo di compilazione](~/android/deploy-test/building-apps/build-process.md)e vengono generati per tutti i tipi che (direttamente o indirettamente) ereditano [java. lang. Object](xref:Java.Lang.Object).

### <a name="implementing-interfaces"></a>Implementazione di interfacce

In alcuni casi potrebbe essere necessario implementare un'interfaccia Android, ad esempio [Android. Content. IComponentCallbacks](xref:Android.Content.IComponentCallbacks).

Tutte le classi e le interfacce Android estendono l'interfaccia [Android. Runtime. IJavaObject](xref:Android.Runtime.IJavaObject) ; Pertanto, tutti i tipi Android devono implementare `IJavaObject`.
Novell. Android sfrutta questo fatto &ndash; USA `IJavaObject` per fornire a Android un proxy Java (un wrapper richiamabile Android) per il tipo gestito specificato. Poiché **monodroid. exe** cerca solo `Java.Lang.Object` sottoclassi (che devono implementare `IJavaObject`), la sottoclasse `Java.Lang.Object` fornisce un modo per implementare le interfacce nel codice gestito. Esempio:

```csharp
class MyComponentCallbacks : Java.Lang.Object, Android.Content.IComponentCallbacks {
    public void OnConfigurationChanged (Android.Content.Res.Configuration newConfig) {
        // implementation goes here...
    }
    public void OnLowMemory () {
        // implementation goes here...
    }
}
```

### <a name="implementation-details"></a>Dettagli sull'implementazione

*La parte restante di questo articolo fornisce i dettagli di implementazione soggetti a modifiche senza preavviso* (e viene presentato qui solo perché gli sviluppatori possono essere curiosi di sapere cosa sta accadendo dietro le quinte).

Ad esempio, data la seguente C# origine:

```csharp
using System;
using Android.App;
using Android.OS;

namespace Mono.Samples.HelloWorld
{
    public class HelloAndroid : Activity
    {
        protected override void OnCreate (Bundle savedInstanceState)
        {
            base.OnCreate (savedInstanceState);
            SetContentView (R.layout.main);
        }
    }
}
```

Il programma **dei Mandroidi. exe** genererà il wrapper chiamabile Android seguente:

```java
package mono.samples.helloWorld;

public class HelloAndroid extends android.app.Activity {
    static final String __md_methods;
    static {
        __md_methods =
            "n_onCreate:(Landroid/os/Bundle;)V:GetOnCreate_Landroid_os_Bundle_Handler\n" +
            "";
        mono.android.Runtime.register (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null",
                HelloAndroid.class,
                __md_methods);
    }

    public HelloAndroid ()
    {
        super ();
        if (getClass () == HelloAndroid.class)
            mono.android.TypeManager.Activate (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null",
                "", this, new java.lang.Object[] { });
    }

    @Override
    public void onCreate (android.os.Bundle p0)
    {
        n_onCreate (p0);
    }

    private native void n_onCreate (android.os.Bundle p0);
}
```

Si noti che la classe di base viene mantenuta e vengono fornite dichiarazioni di metodi nativi per ogni metodo sottoposto a override all'interno del codice gestito.

### <a name="exportattribute-and-exportfieldattribute"></a>ExportAttribute e ExportFieldAttribute

In genere, Novell. Android genera automaticamente il codice Java che comprende ACW; Questa generazione è basata sui nomi di classe e metodo quando una classe deriva da una classe Java ed esegue l'override dei metodi Java esistenti. Tuttavia, in alcuni scenari, la generazione del codice non è adeguata, come descritto di seguito:

- Android supporta i nomi di azione negli attributi XML del layout, ad esempio l'attributo XML di [Android: OnClick](xref:Android.Views.View.IOnClickListener.OnClick*) . Quando viene specificato, l'istanza di visualizzazione inflat tenta di cercare il metodo Java.

- L'interfaccia [java. io. Serializable](https://developer.android.com/reference/java/io/Serializable.html) richiede `readObject` e `writeObject` metodi. Poiché non sono membri di questa interfaccia, l'implementazione gestita corrispondente non espone questi metodi al codice Java.

- L'interfaccia [Android. OS. Parcelable](xref:Android.OS.Parcelable) prevede che una classe di implementazione deve avere un campo statico `CREATOR` di tipo `Parcelable.Creator`. Il codice Java generato richiede un campo esplicito. Con lo scenario standard, non è possibile eseguire l'output del campo nel codice Java dal codice gestito.

Poiché la generazione del codice non fornisce una soluzione per generare metodi Java arbitrari con nomi arbitrari, a partire da Novell. Android 4,2, sono stati introdotti [ExportAttribute](xref:Java.Interop.ExportAttribute) e [ExportFieldAttribute](xref:Java.Interop.ExportFieldAttribute) per offrire una soluzione ai precedenti scenari. Entrambi gli attributi si trovano nello spazio dei nomi `Java.Interop`:

- `ExportAttribute` &ndash; specifica il nome di un metodo e i tipi di eccezione previsti (per assegnare "genera" espliciti in Java). Quando viene usato in un metodo, il metodo "Esporta" un metodo Java che genera un codice di invio alla chiamata JNI corrispondente al metodo gestito. Questa operazione può essere utilizzata con `android:onClick` e `java.io.Serializable`.

- `ExportFieldAttribute` &ndash; specifica il nome di un campo. Si trova su un metodo che funziona come inizializzatore di campo. Questa operazione può essere utilizzata con `android.os.Parcelable`.

Il progetto di esempio [ExportAttribute](https://docs.microsoft.com/samples/xamarin/monodroid-samples/exportattribute) illustra come usare questi attributi.

#### <a name="troubleshooting-exportattribute-and-exportfieldattribute"></a>Risoluzione dei problemi relativi a ExportAttribute e ExportFieldAttribute

- Il pacchetto non riesce a causa della mancata creazione di **mono. Android. Export. dll** &ndash; se è stato usato `ExportAttribute` o `ExportFieldAttribute` su alcuni metodi del codice o delle librerie dipendenti, è necessario aggiungere **mono. Android. Export. dll**. Questo assembly è isolato per supportare il codice di callback da Java. È separato da **mono. Android. dll** perché aggiunge dimensioni aggiuntive all'applicazione.

- Nella build di rilascio `MissingMethodException` si verifica per i metodi di esportazione &ndash; nella build di rilascio `MissingMethodException` si verifica per i metodi di esportazione. Questo problema è stato risolto nella versione più recente di Novell. Android.

### <a name="exportparameterattribute"></a>ExportParameterAttribute

`ExportAttribute` e `ExportFieldAttribute` forniscono funzionalità che possono essere utilizzate dal codice di runtime Java. Questo codice di run-time accede al codice gestito tramite i metodi JNI generati da tali attributi. Di conseguenza, non esiste alcun metodo Java esistente associato dal metodo gestito; di conseguenza, il metodo Java viene generato da una firma del metodo gestito.

Tuttavia, questo caso non è completamente determinante. In particolare, questo vale in alcuni mapping avanzati tra i tipi gestiti e i tipi Java, ad esempio:

- InputStream
- OutputStream
- XmlPullParser
- XmlResourceParser

Quando sono necessari tipi come questi per i metodi esportati, è necessario utilizzare il `ExportParameterAttribute` per assegnare in modo esplicito al parametro o al valore restituito corrispondente un tipo.

### <a name="annotation-attribute"></a>Attributo annotation

In Novell. Android 4,2 sono stati convertiti `IAnnotation` tipi di implementazione in attributi (System. Attribute) e è stato aggiunto il supporto per la generazione di annotazioni nei wrapper Java.

Ciò significa che le seguenti modifiche direzionali:

- Il generatore di binding genera `Java.Lang.DeprecatedAttribute` da `java.Lang.Deprecated` (mentre deve essere `[Obsolete]` nel codice gestito).

- Ciò non significa che la classe `Java.Lang.Deprecated` esistente scomparirà. Questi oggetti basati su Java possono essere ancora usati come normali oggetti Java (se tale uso esiste). Verranno `Deprecated` e `DeprecatedAttribute` classi.

- La classe `Java.Lang.DeprecatedAttribute` è contrassegnata come `[Annotation]`. Quando è presente un attributo personalizzato ereditato da questo attributo `[Annotation]`, l'attività MSBuild genererà un'annotazione Java per l'attributo personalizzato (@Deprecated) in Android Callable Wrapper (ACW).

- Le annotazioni possono essere generate su classi, metodi e campi esportati (un metodo nel codice gestito).

Se la classe che lo contiene (la classe con annotazioni o la classe che contiene i membri annotati) non è registrata, l'intera origine della classe Java non viene generata, incluse le annotazioni. Per i metodi, è possibile specificare il `ExportAttribute` per ottenere il metodo generato e annotato in modo esplicito. Inoltre, non è una funzionalità per "generare" una definizione di classe di annotazione Java. In altre parole, se si definisce un attributo gestito personalizzato per una determinata annotazione, sarà necessario aggiungere un'altra libreria. jar che contiene la classe di annotazione Java corrispondente. L'aggiunta di un file di origine Java che definisce il tipo di annotazione non è sufficiente. Il compilatore Java non funziona allo stesso modo di **apt**.

Si applicano inoltre le seguenti limitazioni:

- Questo processo di conversione non considera finora `@Target` annotazione per il tipo di annotazione.

- Gli attributi su una proprietà non funzionano. Usare invece gli attributi per il getter o il setter della proprietà.

## <a name="class-binding"></a>Associazione di classi

Il binding di una classe significa scrivere un wrapper gestito chiamabile per semplificare la chiamata del tipo Java sottostante.

L'associazione di metodi virtuali e astratti per consentire C# l'override da richiede Novell. Android 4,0. Tuttavia, qualsiasi versione di Novell. Android può associare metodi non virtuali, metodi statici o metodi virtuali senza supportare le sostituzioni.

Un'associazione contiene in genere gli elementi seguenti:

- [Handle JNI al tipo Java associato](#_Looking_up_Java_Types).

- [ID di campo e proprietà di JNI per ogni campo associato](#_Instance_Fields).

- [Metodi e ID del metodo JNI per ogni metodo associato](#_Instance_Methods).

- Se è richiesta la sottoclasse, il tipo deve avere un attributo personalizzato [RegisterAttribute](xref:Android.Runtime.RegisterAttribute) nella dichiarazione di tipo con [RegisterAttribute. DoNotGenerateAcw](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw) impostato su `true`.

### <a name="declaring-type-handle"></a>Dichiarazione dell'handle di tipo

I metodi di ricerca del metodo e del campo richiedono un riferimento a un oggetto che fa riferimento al tipo dichiarante. Per convenzione, questa impostazione viene mantenuta in un campo `class_ref`:

```csharp
static IntPtr class_ref = JNIEnv.FindClass(CLASS);
```

Per informazioni dettagliate sul token `CLASS`, vedere la sezione [riferimenti ai tipi di JNI](#_JNI_Type_References) .

### <a name="binding-fields"></a>Campi di binding

I campi Java sono esposti C# come proprietà, ad esempio il campo Java [java.lang.System.in](https://developer.android.com/reference/java/lang/System.html#in) è associato come C# proprietà [java.lang.JavaSystem.in](xref:Java.Lang.JavaSystem.In).
Inoltre, poiché JNI distingue tra campi statici e campi di istanza, quando si implementano le proprietà vengono usati metodi diversi.

L'associazione di campi prevede tre set di metodi:

1. Metodo *Get Field ID* . Il metodo *Get Field ID* è responsabile della restituzione di un handle di campo che verrà usato dai metodi *Get Field* value e *set field value* . Per ottenere l'ID del campo è necessario conoscere il tipo dichiarante, il nome del campo e la [firma del tipo JNI](#JNI_Type_Signatures) del campo.

1. Metodi *Get field value* . Questi metodi richiedono l'handle di campo e sono responsabili della lettura del valore del campo da Java.
    Il metodo da utilizzare dipende dal tipo del campo.

1. Metodi di *impostazione del valore del campo* . Questi metodi richiedono l'handle di campo e sono responsabili della scrittura del valore del campo in Java. Il metodo da utilizzare dipende dal tipo del campo.

I [campi statici](#_Static_Fields) utilizzano i metodi [JNIEnv. GetStaticFieldID](xref:Android.Runtime.JNIEnv.GetStaticMethodID*), `JNIEnv.GetStatic*Field`e [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) .

 I [campi di istanza](#_Instance_Fields) utilizzano i metodi [JNIEnv. GetFieldID](xref:Android.Runtime.JNIEnv.GetFieldID*), `JNIEnv.Get*Field`e [JNIEnv. sefield](xref:Android.Runtime.JNIEnv.SetField*) .

Ad esempio, la proprietà statica `JavaSystem.In` può essere implementata come:

```csharp
static IntPtr in_jfieldID;
public static System.IO.Stream In
{
    get {
        if (in_jfieldId == IntPtr.Zero)
            in_jfieldId = JNIEnv.GetStaticFieldID (class_ref, "in", "Ljava/io/InputStream;");
        IntPtr __ret = JNIEnv.GetStaticObjectField (class_ref, in_jfieldId);
        return InputStreamInvoker.FromJniHandle (__ret, JniHandleOwnership.TransferLocalRef);
    }
}
```

Nota: si sta usando [InputStreamInvoker. FromJniHandle](xref:Android.Runtime.InputStreamInvoker.FromJniHandle*) per convertire il riferimento JNI in un'istanza di `System.IO.Stream` e si sta usando `JniHandleOwnership.TransferLocalRef` perché [JNIEnv. GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*) restituisce un riferimento locale.

Molti dei tipi [Android. Runtime](xref:Android.Runtime) hanno `FromJniHandle` metodi che convertiranno un riferimento JNI nel tipo desiderato.

### <a name="method-binding"></a>Associazione al metodo

I metodi Java vengono esposti C# come metodi e C# come proprietà. Ad esempio, il metodo Java [. lang. Runtime. runFinalizersOnExit](https://developer.android.com/reference/java/lang/Runtime.html#runFinalizersOnExit(boolean)) del metodo Java viene associato come metodo [java. lang. Runtime. runFinalizersOnExit](xref:Java.Lang.Runtime.RunFinalizersOnExit*) e il metodo [java. lang. Object. GetClass](https://developer.android.com/reference/java/lang/Object.html#getClass) viene associato come oggetto [java. lang. Object. Class ](xref:Java.Lang.Object.Class)proprietà.

La chiamata al metodo è un processo in due passaggi:

1. *ID del metodo Get* per il metodo da richiamare. Il metodo *Get Method ID* è responsabile della restituzione di un handle di metodo che verrà utilizzato dai metodi di chiamata del metodo. Per ottenere l'ID del metodo è necessario conoscere il tipo dichiarante, il nome del metodo e la [firma del tipo JNI](#JNI_Type_Signatures) del metodo.

1. Richiamare il metodo.

Analogamente ai campi, i metodi da usare per ottenere l'ID del metodo e richiamare il metodo differiscono tra i metodi statici e i metodi di istanza.

I [metodi statici](#_Static_Methods_1) utilizzano [JNIEnv. GetStaticMethodID ()](xref:Android.Runtime.JNIEnv.GetStaticMethodID*) per cercare l'ID del metodo e utilizzano la famiglia di metodi `JNIEnv.CallStatic*Method` per la chiamata.

I [metodi di istanza](#_Instance_Methods) utilizzano [JNIEnv. GetMethodID](xref:Android.Runtime.JNIEnv.GetMethodID*) per cercare l'ID del metodo e utilizzano le famiglie `JNIEnv.Call*Method` e `JNIEnv.CallNonvirtual*Method` di metodi per la chiamata.

L'associazione al metodo è potenzialmente più della semplice chiamata al metodo. L'associazione al metodo include anche la possibilità di eseguire l'override di un metodo (per i metodi astratti e non finali) o di implementarlo (per i metodi di interfaccia). La sezione [interfacce e ereditarietà di supporto](#_Supporting_Inheritance,_Interfaces_1) descrive le complessità del supporto di metodi virtuali e metodi di interfaccia.

<a name="_Static_Methods_1" />

#### <a name="static-methods"></a>Metodi statici

Il binding di un metodo statico prevede l'uso di `JNIEnv.GetStaticMethodID` per ottenere un handle di metodo, quindi l'utilizzo del metodo `JNIEnv.CallStatic*Method` appropriato, a seconda del tipo restituito del metodo. Di seguito è riportato un esempio di associazione per il metodo [Runtime. GetRuntime](https://developer.android.com/reference/java/lang/Runtime.html#getRuntime()) :

```csharp
static IntPtr id_getRuntime;

[Register ("getRuntime", "()Ljava/lang/Runtime;", "")]
public static Java.Lang.Runtime GetRuntime ()
{
    if (id_getRuntime == IntPtr.Zero)
        id_getRuntime = JNIEnv.GetStaticMethodID (class_ref,
                "getRuntime", "()Ljava/lang/Runtime;");

    return Java.Lang.Object.GetObject<Java.Lang.Runtime> (
            JNIEnv.CallStaticObjectMethod  (class_ref, id_getRuntime),
            JniHandleOwnership.TransferLocalRef);
}
```

Si noti che l'handle del metodo viene archiviato in un campo statico, `id_getRuntime`. Si tratta di un'ottimizzazione delle prestazioni, in modo che l'handle del metodo non debba essere cercato a ogni chiamata. Non è necessario memorizzare nella cache l'handle del metodo in questo modo. Una volta ottenuto l'handle del metodo, viene usato [JNIEnv. CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) per richiamare il metodo. `JNIEnv.CallStaticObjectMethod` restituisce un `IntPtr` contenente l'handle dell'istanza di Java restituita.
[Java. lang. Object. GetObject&lt;t&gt;(IntPtr, JniHandleOwnership)](xref:Java.Lang.Object.GetObject*) viene usato per convertire l'handle Java in un'istanza di oggetto fortemente tipizzata.

#### <a name="non-virtual-instance-method-binding"></a>Associazione al metodo di istanza non virtuale

L'associazione di un metodo di istanza `final` o di un metodo di istanza che non richiede l'override di comporta l'utilizzo di `JNIEnv.GetMethodID` per ottenere un handle di metodo, quindi l'utilizzo del metodo `JNIEnv.Call*Method` appropriato, a seconda del tipo restituito del metodo. Di seguito è riportato un esempio di associazione per la proprietà `Object.Class`:

```csharp
static IntPtr id_getClass;
public Java.Lang.Class Class {
    get {
        if (id_getClass == IntPtr.Zero)
            id_getClass = JNIEnv.GetMethodID (class_ref, "getClass", "()Ljava/lang/Class;");
        return Java.Lang.Object.GetObject<Java.Lang.Class> (
                JNIEnv.CallObjectMethod (Handle, id_getClass),
                JniHandleOwnership.TransferLocalRef);
    }
}
```

Si noti che l'handle del metodo viene archiviato in un campo statico, `id_getClass`.
Si tratta di un'ottimizzazione delle prestazioni, in modo che l'handle del metodo non debba essere cercato a ogni chiamata. Non è necessario memorizzare nella cache l'handle del metodo in questo modo. Una volta ottenuto l'handle del metodo, viene usato [JNIEnv. CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) per richiamare il metodo. `JNIEnv.CallStaticObjectMethod` restituisce un `IntPtr` contenente l'handle dell'istanza di Java restituita.
[Java. lang. Object. GetObject&lt;t&gt;(IntPtr, JniHandleOwnership)](xref:Java.Lang.Object.GetObject*) viene usato per convertire l'handle Java in un'istanza di oggetto fortemente tipizzata.

### <a name="binding-constructors"></a>Costruttori di binding

I costruttori sono metodi Java con il nome `"<init>"`. Analogamente a quanto avviene con i metodi di istanza Java, `JNIEnv.GetMethodID` viene usato per cercare l'handle del costruttore. A differenza dei metodi Java, i metodi [JNIEnv. NewObject](xref:Android.Runtime.JNIEnv.NewObject*) vengono usati per richiamare l'handle del metodo del costruttore. Il valore restituito di `JNIEnv.NewObject` è un riferimento locale JNI:

```csharp
int value = 42;
IntPtr class_ref    = JNIEnv.FindClass ("java/lang/Integer");
IntPtr id_ctor_I    = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
IntPtr lrefInstance = JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value));
// Dispose of lrefInstance, class_ref…
```

In genere, un'associazione di classi creerà la sottoclasse [java. lang. Object](xref:Java.Lang.Object).
Quando si crea una sottoclasse `Java.Lang.Object`, entra in gioco una semantica aggiuntiva: un'istanza di `Java.Lang.Object` gestisce un riferimento globale a un'istanza di Java tramite la proprietà `Java.Lang.Object.Handle`.

1. Il `Java.Lang.Object` costruttore predefinito alloca un'istanza java.

1. Se il tipo dispone di un `RegisterAttribute` e `RegisterAttribute.DoNotGenerateAcw` è `true`, viene creata un'istanza del tipo `RegisterAttribute.Name` tramite il costruttore predefinito.

1. In caso contrario, viene creata un'istanza di [Android callable wrapper](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) corrispondente a `this.GetType` tramite il costruttore predefinito. I wrapper richiamabili Android vengono generati durante la creazione del pacchetto per ogni sottoclasse `Java.Lang.Object` per cui `RegisterAttribute.DoNotGenerateAcw` non è impostato su `true`.

Per i tipi che non sono associazioni di classi, questa è la semantica prevista: la creazione C# di un'istanza di `Mono.Samples.HelloWorld.HelloAndroid` deve creare un'istanza di `mono.samples.helloworld.HelloAndroid`Java che è un wrapper richiamabile Android generato.

Per le associazioni di classi, questo può essere il comportamento corretto se il tipo Java contiene un costruttore predefinito e/o nessun altro costruttore deve essere richiamato. In caso contrario, è necessario fornire un costruttore che esegua le azioni seguenti:

1. Richiamando [java. lang. Object (IntPtr, JniHandleOwnership)](xref:Java.Lang.Object#ctor*) anziché il costruttore predefinito `Java.Lang.Object`. Questa operazione è necessaria per evitare la creazione di una nuova istanza di Java.

1. Prima di creare istanze Java, controllare il valore di [java. lang. Object. handle](xref:Java.Lang.Object.Handle) . La proprietà `Object.Handle` avrà un valore diverso da `IntPtr.Zero` se è stato creato un wrapper richiamabile Android nel codice Java e l'associazione di classi viene costruita per contenere l'istanza di Android Callable Wrapper creata. Ad esempio, quando Android crea un'istanza di `mono.samples.helloworld.HelloAndroid`, il wrapper richiamabile Android verrà creato per primo e il costruttore Java `HelloAndroid` creerà un'istanza del tipo di `Mono.Samples.HelloWorld.HelloAndroid` corrispondente, con la proprietà `Object.Handle` impostata sull'istanza java prima dell'esecuzione del costruttore.

1. Se il tipo di runtime corrente non è uguale al tipo dichiarante, è necessario creare un'istanza del wrapper richiamabile di Android corrispondente e usare [Object. tohandle](xref:Java.Lang.Object.SetHandle*) per archiviare l'handle restituito da [JNIEnv. CreateInstance](xref:Android.Runtime.JNIEnv.CreateInstance*).

1. Se il tipo di runtime corrente corrisponde al tipo dichiarante, richiamare il costruttore Java e utilizzare [Object. sehandle](xref:Java.Lang.Object.SetHandle*) per archiviare l'handle restituito da `JNIEnv.NewInstance`.

Si consideri, ad esempio, il costruttore [java. lang. Integer (int)](https://developer.android.com/reference/java/lang/Integer.html#Integer(int)) . Questo è associato come:

```csharp
// Cache the constructor's method handle for later use
static IntPtr id_ctor_I;

// Need [Register] for subclassing
// RegisterAttribute.Name is always ".ctor"
// RegisterAttribute.Signature is tye JNI type signature of constructor
// RegisterAttribute.Connector is ignored; use ""
[Register (".ctor", "(I)V", "")]
public Integer (int value)
    // 1. Prevent Object default constructor execution
    : base (IntPtr.Zero, JniHandleOwnership.DoNotTransfer)
{
    // 2. Don't allocate Java instance if already allocated
    if (Handle != IntPtr.Zero)
        return;

    // 3. Derived type? Create Android Callable Wrapper
    if (GetType () != typeof (Integer)) {
        SetHandle (
                Android.Runtime.JNIEnv.CreateInstance (GetType (), "(I)V", new JValue (value)),
                JniHandleOwnership.TransferLocalRef);
        return;
    }

    // 4. Declaring type: lookup &amp; cache method id...
    if (id_ctor_I == IntPtr.Zero)
        id_ctor_I = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
    // ...then create the Java instance and store
    SetHandle (
            JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value)),
            JniHandleOwnership.TransferLocalRef);
}
```

I metodi [JNIEnv. CreateInstance](xref:Android.Runtime.JNIEnv.CreateInstance*) sono helper per l'esecuzione di un `JNIEnv.FindClass`, `JNIEnv.GetMethodID`, `JNIEnv.NewObject`e `JNIEnv.DeleteGlobalReference` sul valore restituito da `JNIEnv.FindClass`. Per informazioni dettagliate, vedere la sezione successiva.

<a name="_Supporting_Inheritance,_Interfaces_1" />

### <a name="supporting-inheritance-interfaces"></a>Supporto dell'ereditarietà, delle interfacce

La creazione di una sottoclasse di un tipo Java o l'implementazione di un'interfaccia Java richiede la generazione di [Android callable wrapper](~/android/platform/java-integration/android-callable-wrappers.md) (ACWs) generati per ogni sottoclasse `Java.Lang.Object` durante il processo di creazione del pacchetto. La generazione di ACW viene controllata tramite l'attributo personalizzato [Android. Runtime. RegisterAttribute](xref:Android.Runtime.RegisterAttribute) .

Per C# i tipi, il costruttore dell'attributo personalizzato`[Register]`richiede un argomento: il [riferimento di tipo semplificato JNI](#_Simplified_Type_References_1) per il tipo Java corrispondente. In questo modo è possibile fornire nomi diversi C#tra Java e.

Prima di Novell. Android 4,0, l'attributo personalizzato `[Register]` non era disponibile per i tipi Java esistenti "alias". Ciò è dovuto al fatto che il processo di generazione di ACW genera ACWs per ogni `Java.Lang.Object` sottoclasse rilevata.

In Novell. Android 4,0 è stata introdotta la proprietà [RegisterAttribute. DoNotGenerateAcw](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw) . Questa proprietà indica al processo di generazione ACW di *ignorare* il tipo annotato, consentendo la dichiarazione di nuovi wrapper gestiti chiamabili che non comporteranno la generazione di ACWs al momento della creazione del pacchetto. In questo modo è possibile associare i tipi Java esistenti. Si consideri, ad esempio, la semplice classe Java seguente, `Adder`, che contiene un metodo, `add`, che aggiunge ad Integer e restituisce il risultato:

```java
package mono.android.test;
public class Adder {
    public int add (int a, int b) {
        return a + b;
    }
}
```

Il tipo di `Adder` può essere associato come:

```csharp
[Register ("mono/android/test/Adder", DoNotGenerateAcw=true)]
public partial class Adder : Java.Lang.Object {
    static IntPtr class_ref = JNIEnv.FindClass ( "mono/android/test/Adder");

    public Adder ()
    {
    }

    public Adder (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }
}
partial class ManagedAdder : Adder {
}
```

In questo caso, C# il tipo di `Adder` *alias* il tipo di`Adder`Java. L'attributo `[Register]` viene usato per specificare il nome JNI del tipo di `mono.android.test.Adder` Java e la proprietà `DoNotGenerateAcw` viene usata per impedire la generazione di ACW. Questo comporterà la generazione di un ACW per il tipo di `ManagedAdder`, che consente di sottoclassare correttamente il tipo di `mono.android.test.Adder`. Se la proprietà `RegisterAttribute.DoNotGenerateAcw` non è stata usata, il processo di compilazione di Novell. Android avrebbe generato un nuovo tipo di `mono.android.test.Adder` Java. Ciò comporterebbe errori di compilazione, in quanto il tipo di `mono.android.test.Adder` sarebbe presente due volte, in due file distinti.

### <a name="binding-virtual-methods"></a>Associazione di metodi virtuali

`ManagedAdder` sottoclassi del tipo di `Adder` Java, ma non è particolarmente interessante: il C# tipo di`Adder`non definisce metodi virtuali, quindi`ManagedAdder`non può eseguire l'override di alcun elemento.

Il binding `virtual` metodi per consentire l'override dalle sottoclassi richiede diverse operazioni che devono essere eseguite nelle due categorie seguenti:

1. **Associazione al metodo**

1. **Registrazione del metodo**

#### <a name="method-binding"></a>Associazione al metodo

Un'associazione al metodo richiede l'aggiunta di due membri del supporto C# alla definizione`Adder`:`ThresholdType`e`ThresholdClass`.

##### <a name="thresholdtype"></a>ThresholdType

La proprietà `ThresholdType` restituisce il tipo corrente dell'associazione:

```csharp
partial class Adder {
    protected override System.Type ThresholdType {
        get {
            return typeof (Adder);
        }
    }
}
```

`ThresholdType` viene usato nell'associazione al metodo per determinare quando eseguire l'invio di metodi virtuali e non virtuali. Deve sempre restituire un'istanza di `System.Type` che corrisponde al C# tipo dichiarante.

##### <a name="thresholdclass"></a>ThresholdClass

La proprietà `ThresholdClass` restituisce il riferimento alla classe JNI per il tipo associato:

```csharp
partial class Adder {
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```

`ThresholdClass` viene usato nell'associazione al metodo quando si richiamano metodi non virtuali.

#### <a name="binding-implementation"></a>Implementazione dell'associazione

L'implementazione dell'associazione di metodi è responsabile della chiamata di runtime del metodo Java. Contiene anche un `[Register]` dichiarazione di attributo personalizzato che fa parte della registrazione del metodo e verrà discussa nella sezione relativa alla registrazione del metodo:

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
    public virtual int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        if (GetType () == ThresholdType)
            return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
        return JNIEnv.CallNonvirtualIntMethod (Handle, ThresholdClass, id_add, new JValue (a), new JValue (b));
    }
}
```

Il campo `id_add` contiene l'ID del metodo Java da richiamare. Il valore `id_add` viene ottenuto da `JNIEnv.GetMethodID`, che richiede la classe dichiarante (`class_ref`), il nome del metodo Java (`"add"`) e la firma JNI del metodo (`"(II)I"`).

Una volta ottenuto l'ID del metodo, `GetType` viene confrontato con `ThresholdType` per determinare se è necessario un dispatch virtuale o non virtuale. Il recapito virtuale è necessario quando `GetType` corrisponde a `ThresholdType`, in quanto `Handle` può fare riferimento a una sottoclasse allocata da Java che esegue l'override del metodo.

Quando `GetType` non corrisponde `ThresholdType`, `Adder` è stata sottoclassata, ad esempio per `ManagedAdder`, e l'implementazione di `Adder.Add` verrà richiamata solo se la sottoclasse richiamata `base.Add`. Questo è il caso di invio non virtuale, che è il punto in cui viene `ThresholdClass`. `ThresholdClass` specifica quale classe Java fornirà l'implementazione del metodo da richiamare.

#### <a name="method-registration"></a>Registrazione del metodo

Si supponga di disporre di una definizione `ManagedAdder` aggiornata che esegue l'override del metodo `Adder.Add`:

```csharp
partial class ManagedAdder : Adder {
    public override int Add (int a, int b) {
        return (a*2) + (b*2);
    }
}
```

Ricordare che `Adder.Add` dispone di un attributo `[Register]` personalizzato:

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
```

Il costruttore dell'attributo personalizzato `[Register]` accetta tre valori:

1. Il nome del metodo Java, `"add"` in questo caso.

1. La firma del tipo JNI del metodo, `"(II)I"` in questo caso.

1. Il *metodo del connettore* , `GetAddHandler` in questo caso.
    I metodi del connettore verranno descritti in seguito.

I primi due parametri consentono al processo di generazione di ACW di generare una dichiarazione di metodo per eseguire l'override del metodo. L'oggetto ACW risultante conterrà parte del codice seguente:

```csharp
public class ManagedAdder extends mono.android.test.Adder {
    static final String __md_methods;
    static {
        __md_methods = "n_add:(II)I:GetAddHandler\n" +
            "";
        mono.android.Runtime.register (...);
    }
    @Override
    public int add (int p0, int p1) {
        return n_add (p0, p1);
    }
    private native int n_add (int p0, int p1);
    // ...
}
```

Si noti che viene dichiarato un metodo di `@Override`, che delega a un metodo con prefisso `n_`con lo stesso nome. In questo modo, quando il codice Java richiama `ManagedAdder.add`, viene richiamato `ManagedAdder.n_add`, che consente l'esecuzione dell' C# override del`ManagedAdder.Add`metodo.

Quindi, la domanda più importante: in che modo `ManagedAdder.n_add` viene collegato al `ManagedAdder.Add`?

I metodi Java `native` vengono registrati con il runtime Java (il runtime di Android) tramite la [funzione JNI RegisterNatives](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp17734).
`RegisterNatives` accetta una matrice di strutture che contengono il nome del metodo Java, la firma del tipo JNI e un puntatore a funzione per richiamare che segue la [convenzione di chiamata JNI](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/design.html#wp715).
Il puntatore a funzione deve essere una funzione che accetta due argomenti puntatore seguiti dai parametri del metodo. Il metodo Java `ManagedAdder.n_add` deve essere implementato tramite una funzione con il prototipo C seguente:

```csharp
int FunctionName(JNIEnv *env, jobject this, int a, int b)
```

Novell. Android non espone un metodo `RegisterNatives`. Al contrario, ACW e MCW forniscono insieme le informazioni necessarie per richiamare `RegisterNatives`: l'ACW contiene il nome del metodo e la firma del tipo JNI, l'unico elemento mancante è un puntatore a funzione per l'associazione.

Questo è il punto in cui viene inserito il *metodo del connettore* . Il terzo `[Register]` parametro dell'attributo personalizzato è il nome di un metodo definito nel tipo registrato o una classe di base del tipo registrato che non accetta parametri e restituisce un `System.Delegate`. Il `System.Delegate` restituito a sua volta fa riferimento a un metodo con la firma della funzione JNI corretta. Infine, il delegato restituito dal metodo del connettore *deve* avere una radice, in modo che il Garbage Collector non la raccolga, perché il delegato viene fornito a Java.

```csharp
#pragma warning disable 0169
static Delegate cb_add;
// This method must match the third parameter of the [Register]
// custom attribute, must be static, must return System.Delegate,
// and must accept no parameters.
static Delegate GetAddHandler ()
{
    if (cb_add == null)
        cb_add = JNINativeWrapper.CreateDelegate ((Func<IntPtr, IntPtr, int, int, int>) n_Add);
    return cb_add;
}
// This method is registered with JNI.
static int n_Add (IntPtr jnienv, IntPtr lrefThis, int a, int b)
{
    Adder __this = Java.Lang.Object.GetObject<Adder>(lrefThis, JniHandleOwnership.DoNotTransfer);
    return __this.Add (a, b);
}
#pragma warning restore 0169
```

Il metodo `GetAddHandler` crea un delegato di `Func<IntPtr, IntPtr, int, int,
int>` che fa riferimento al metodo `n_Add`, quindi richiama [JNINativeWrapper. CreateDelegate](xref:Android.Runtime.JNINativeWrapper.CreateDelegate*).
`JNINativeWrapper.CreateDelegate` esegue il wrapping del metodo fornito in un blocco try/catch, in modo che tutte le eccezioni non gestite siano gestite e provochino l'innalzamento dell'evento [AndroidEvent. UnhandledExceptionRaiser](xref:Android.Runtime.AndroidEnvironment.UnhandledExceptionRaiser) . Il delegato risultante viene archiviato nella variabile di `cb_add` statica, in modo che il Garbage Collector non libererà il delegato.

Infine, il metodo `n_Add` è responsabile del marshalling dei parametri JNI ai tipi gestiti corrispondenti, quindi della delega della chiamata al metodo.

Nota: usare sempre `JniHandleOwnership.DoNotTransfer` quando si ottiene un MCW su un'istanza java. Considerandoli come un riferimento locale (e in tal modo la chiamata a `JNIEnv.DeleteLocalRef`) interrompe le transizioni dello stack gestite con&gt; Java&gt; gestite.

### <a name="complete-adder-binding"></a>Completa associazione Adder

Il binding gestito completo per il tipo di `mono.android.tests.Adder` è:

```csharp
[Register ("mono/android/test/Adder", DoNotGenerateAcw=true)]
public class Adder : Java.Lang.Object {

    static IntPtr class_ref = JNIEnv.FindClass ("mono/android/test/Adder");

    public Adder ()
    {
    }

    public Adder (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }

    protected override Type ThresholdType {
        get {return typeof (Adder);}
    }

    protected override IntPtr ThresholdClass {
        get {return class_ref;}
    }

#region Add
    static IntPtr id_add;

    [Register ("add", "(II)I", "GetAddHandler")]
    public virtual int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        if (GetType () == ThresholdType)
            return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
        return JNIEnv.CallNonvirtualIntMethod (Handle, ThresholdClass, id_add, new JValue (a), new JValue (b));
    }

#pragma warning disable 0169
    static Delegate cb_add;
    static Delegate GetAddHandler ()
    {
        if (cb_add == null)
            cb_add = JNINativeWrapper.CreateDelegate ((Func<IntPtr, IntPtr, int, int, int>) n_Add);
        return cb_add;
    }

    static int n_Add (IntPtr jnienv, IntPtr lrefThis, int a, int b)
    {
        Adder __this = Java.Lang.Object.GetObject<Adder>(lrefThis, JniHandleOwnership.DoNotTransfer);
        return __this.Add (a, b);
    }
#pragma warning restore 0169
#endregion
}
```

### <a name="restrictions"></a>Restrizioni

Quando si scrive un tipo che corrisponde ai criteri seguenti:

1. Sottoclassi `Java.Lang.Object`

1. Ha un `[Register]` attributo personalizzato

1. `RegisterAttribute.DoNotGenerateAcw` è `true`

Per l'interazione GC, quindi, il tipo *non deve* contenere campi che possono fare riferimento a una sottoclasse `Java.Lang.Object` o `Java.Lang.Object` in fase di esecuzione. Ad esempio, i campi di tipo `System.Object` e qualsiasi tipo di interfaccia non sono consentiti. I tipi che non possono fare riferimento alle istanze di `Java.Lang.Object` sono consentiti, ad esempio `System.String` e `List<int>`. Questa restrizione impedisce la raccolta prematura degli oggetti da parte del Garbage Collector.

Se il tipo deve contenere un campo di istanza che può fare riferimento a un'istanza di `Java.Lang.Object`, il tipo di campo deve essere `System.WeakReference` o `GCHandle`.

## <a name="binding-abstract-methods"></a>Associazione di metodi astratti

Il binding `abstract` metodi è in gran parte identico all'associazione di metodi virtuali. Esistono solo due differenze:

1. Il metodo astratto è abstract. Mantiene comunque l'attributo `[Register]` e la registrazione del metodo associato, l'associazione al metodo viene appena spostata nel tipo di `Invoker`.

1. Viene creato un tipo di `Invoker` non `abstract` che consente di sottoclassere il tipo astratto. Il tipo di `Invoker` deve eseguire l'override di tutti i metodi astratti dichiarati nella classe di base e l'implementazione sottoposta a override è l'implementazione dell'associazione al metodo, sebbene il caso di invio non virtuale possa essere ignorato.

Si supponga, ad esempio, che il metodo `mono.android.test.Adder.add` sia stato `abstract`. L' C# associazione cambierebbe in modo che `Adder.Add`fossero astratte e venisse definito un nuovo tipo di`AdderInvoker`che implementava`Adder.Add`:

```csharp
partial class Adder {
    [Register ("add", "(II)I", "GetAddHandler")]
    public abstract int Add (int a, int b);

    // The Method Registration machinery is identical to the
    // virtual method case...
}

partial class AdderInvoker : Adder {
    public AdderInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }

    static IntPtr id_add;
    public override int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
    }
}
```

Il tipo di `Invoker` è necessario solo quando si ottengono riferimenti JNI a istanze create da Java.

## <a name="binding-interfaces"></a>Interfacce di binding

Le interfacce di associazione sono concettualmente simili alle classi di associazione che contengono metodi virtuali, ma molte delle specifiche differiscono in modo sottile (e non così sottile). Si consideri la seguente [dichiarazione di interfaccia java](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/Adder.java#L14):

```csharp
public interface Progress {
    void onAdd(int[] values, int currentIndex, int currentSum);
}
```

Le associazioni di interfaccia includono due parti: C# la definizione dell'interfaccia e una definizione invoker per l'interfaccia.

### <a name="interface-definition"></a>Definizione di interfaccia

La C# definizione dell'interfaccia deve soddisfare i requisiti seguenti:

- La definizione dell'interfaccia deve avere un `[Register]` attributo personalizzato.

- La definizione dell'interfaccia deve estendere il `IJavaObject interface`.
    In caso contrario, ACWs non potrà ereditare dall'interfaccia java.

- Ogni metodo di interfaccia deve contenere un attributo `[Register]` che specifica il nome del metodo Java corrispondente, la firma JNI e il metodo del connettore.

- Il metodo del connettore deve inoltre specificare il tipo in cui può trovarsi il metodo del connettore.

Quando si associano `abstract` e `virtual` metodi, viene eseguita la ricerca del metodo del connettore all'interno della gerarchia di ereditarietà del tipo in fase di registrazione. Le interfacce non possono avere metodi contenenti corpi, quindi questa operazione non funziona, quindi è necessario specificare un tipo che indichi dove si trova il metodo del connettore. Il tipo viene specificato all'interno della stringa del metodo del connettore, dopo i due punti `':'`e deve essere il nome del tipo completo di assembly del tipo che contiene l'invoker.

Le dichiarazioni del metodo di interfaccia sono una traduzione del metodo Java corrispondente utilizzando tipi *compatibili* . Per i tipi Java Builtin, i tipi compatibili sono i C# tipi corrispondenti, ad esempio `int`java C# è`int`. Per i tipi di riferimento, il tipo compatibile è un tipo che può fornire un handle JNI del tipo Java appropriato.

I membri di interfaccia non verranno richiamati direttamente da Java &ndash; la chiamata verrà mediata tramite il tipo invoker &ndash; quindi è consentita una certa flessibilità.

L'interfaccia di stato di Java può essere [dichiarata C# in come](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L83):

```csharp
[Register ("mono/android/test/Adder$Progress", DoNotGenerateAcw=true)]
public interface IAdderProgress : IJavaObject {
    [Register ("onAdd", "([III)V",
            "GetOnAddHandler:Mono.Samples.SanityTests.IAdderProgressInvoker, SanityTests, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null")]
    void OnAdd (JavaArray<int> values, int currentIndex, int currentSum);
}
```

Si noti che in precedenza viene mappato il parametro Java `int[]` a un [&gt;JavaArray&lt;int ](xref:Android.Runtime.JavaArray`1).
Questa operazione non è necessaria: è possibile che sia stata C# associata a un`int[]`o a un`IList<int>`oppure a un altro elemento. Indipendentemente dal tipo scelto, il `Invoker` deve essere in grado di tradurlo in un tipo di `int[]` Java per la chiamata.

### <a name="invoker-definition"></a>Definizione invoker

La definizione del tipo di `Invoker` deve ereditare `Java.Lang.Object`, implementare l'interfaccia appropriata e fornire tutti i metodi di connessione a cui viene fatto riferimento nella definizione dell'interfaccia. È disponibile un ulteriore suggerimento che differisce da un'associazione di classi: gli ID del campo e del metodo `class_ref` devono essere membri di istanza, non membri statici.

Il motivo per cui si fa riferimento ai membri dell'istanza è il comportamento `JNIEnv.GetMethodID` nel runtime di Android. (Potrebbe trattarsi anche di un comportamento Java, non è stato testato). `JNIEnv.GetMethodID` restituisce null quando si cerca un metodo che deriva da un'interfaccia implementata e non dall'interfaccia dichiarata. Si consideri l'interfaccia java [. util. SortedMap&lt;K, V&gt;](https://developer.android.com/reference/java/util/SortedMap.html) , che implementa l'interfaccia [java. util. map&lt;K, v&gt;](https://developer.android.com/reference/java/util/Map.html) . Map fornisce un metodo [chiaro](https://developer.android.com/reference/java/util/Map.html#clear()) , pertanto una definizione `Invoker` apparentemente ragionevole per SortedMap sarà:

```csharp
// Fails at runtime. DO NOT FOLLOW
partial class ISortedMapInvoker : Java.Lang.Object, ISortedMap {
    static IntPtr class_ref = JNIEnv.FindClass ("java/util/SortedMap");
    static IntPtr id_clear;
    public void Clear()
    {
        if (id_clear == IntPtr.Zero)
            id_clear = JNIEnv.GetMethodID(class_ref, "clear", "()V");
        JNIEnv.CallVoidMethod(Handle, id_clear);
    }
     // ...
}
```

L'operazione precedente avrà esito negativo perché `JNIEnv.GetMethodID` restituirà `null` quando si cerca il metodo `Map.clear` tramite l'istanza della classe `SortedMap`.

Esistono due soluzioni: è possibile tenere traccia dell'interfaccia da cui provengono ogni metodo e avere un `class_ref` per ogni interfaccia oppure mantenerli come membri di istanza ed eseguire la ricerca del metodo sul tipo di classe più derivato, non sul tipo di interfaccia. Quest'ultimo viene eseguito in **mono. Android. dll**.

La definizione invoker include sei sezioni: il costruttore, il metodo `Dispose`, i membri `ThresholdType` e `ThresholdClass`, il metodo `GetObject`, l'implementazione del metodo di interfaccia e l'implementazione del metodo del connettore.

#### <a name="constructor"></a>Costruttore

Il costruttore deve eseguire la ricerca della classe di runtime dell'istanza richiamata e archiviare la classe runtime nel campo `class_ref` dell'istanza:

```csharp
partial class IAdderProgressInvoker {
    IntPtr class_ref;
    public IAdderProgressInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass (Handle);
        class_ref   = JNIEnv.NewGlobalRef (lref);
        JNIEnv.DeleteLocalRef (lref);
    }
}
```

Nota: è necessario usare la proprietà `Handle` all'interno del corpo del costruttore e non il parametro `handle`, come in Android v 4.0 il parametro `handle` potrebbe non essere valido al termine dell'esecuzione del costruttore di base.

#### <a name="dispose-method"></a>Metodo Dispose

Il metodo `Dispose` deve liberare il riferimento globale allocato nel costruttore:

```csharp
partial class IAdderProgressInvoker {
    protected override void Dispose (bool disposing)
    {
        if (this.class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef (this.class_ref);
        this.class_ref = IntPtr.Zero;
        base.Dispose (disposing);
    }
}
```

#### <a name="thresholdtype-and-thresholdclass"></a>ThresholdType e ThresholdClass

I membri `ThresholdType` e `ThresholdClass` sono identici a quelli disponibili in un'associazione di classe:

```csharp
partial class IAdderProgressInvoker {
    protected override Type ThresholdType {
        get {
            return typeof (IAdderProgressInvoker);
        }
    }
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```

#### <a name="getobject-method"></a>Metodo GetObject

Per supportare Extensions, è necessario un metodo di `GetObject` statico [. JavaCast&lt;t&gt;()](xref:Android.Runtime.Extensions.JavaCast*):

```csharp
partial class IAdderProgressInvoker {
    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }
}
```

#### <a name="interface-methods"></a>Metodi di interfaccia

Ogni metodo dell'interfaccia deve avere un'implementazione, che richiama il metodo Java corrispondente tramite JNI:

```csharp
partial class IAdderProgressInvoker {
    IntPtr id_onAdd;
    public void OnAdd (JavaArray<int> values, int currentIndex, int currentSum)
    {
        if (id_onAdd == IntPtr.Zero)
            id_onAdd = JNIEnv.GetMethodID (class_ref, "onAdd", "([III)V");
        JNIEnv.CallVoidMethod (Handle, id_onAdd, new JValue (JNIEnv.ToJniHandle (values)), new JValue (currentIndex), new JValue (currentSum));
    }
}
```

#### <a name="connector-methods"></a>Metodi del connettore

I metodi del connettore e l'infrastruttura di supporto sono responsabili del marshalling dei parametri C# JNI ai tipi appropriati. Il parametro Java `int[]` viene passato come `jintArray`JNI, che è un `IntPtr` all'interno C#di. È necessario effettuare il marshalling del `IntPtr` a un `JavaArray<int>` per supportare la chiamata dell' C# interfaccia:

```csharp
partial class IAdderProgressInvoker {
    static Delegate cb_onAdd;
    static Delegate GetOnAddHandler ()
    {
        if (cb_onAdd == null)
            cb_onAdd = JNINativeWrapper.CreateDelegate ((Action<IntPtr, IntPtr, IntPtr, int, int>) n_OnAdd);
        return cb_onAdd;
    }

    static void n_OnAdd (IntPtr jnienv, IntPtr lrefThis, IntPtr values, int currentIndex, int currentSum)
    {
        IAdderProgress __this = Java.Lang.Object.GetObject<IAdderProgress>(lrefThis, JniHandleOwnership.DoNotTransfer);
        using (var _values = new JavaArray<int>(values, JniHandleOwnership.DoNotTransfer)) {
            __this.OnAdd (_values, currentIndex, currentSum);
        }
    }
}
```

Se si preferisce `int[]` `JavaList<int>`, è possibile usare [JNIEnv. GetArray ()](xref:Android.Runtime.JNIEnv.GetArray*) :

```csharp
int[] _values = (int[]) JNIEnv.GetArray(values, JniHandleOwnership.DoNotTransfer, typeof (int));
```

Si noti, tuttavia, che `JNIEnv.GetArray` copia l'intera matrice tra le macchine virtuali, pertanto per le matrici di grandi dimensioni questo potrebbe comportare un numero elevato di richieste GC aggiuntive.

### <a name="complete-invoker-definition"></a>Definizione completa invoker

[Definizione IAdderProgressInvoker completa](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L88):

```csharp
class IAdderProgressInvoker : Java.Lang.Object, IAdderProgress {

    IntPtr class_ref;

    public IAdderProgressInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass (Handle);
        class_ref = JNIEnv.NewGlobalRef (lref);
        JNIEnv.DeleteLocalRef (lref);
    }

    protected override void Dispose (bool disposing)
    {
        if (this.class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef (this.class_ref);
        this.class_ref = IntPtr.Zero;
        base.Dispose (disposing);
    }

    protected override Type ThresholdType {
        get {return typeof (IAdderProgressInvoker);}
    }

    protected override IntPtr ThresholdClass {
        get {return class_ref;}
    }

    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }

#region OnAdd
    IntPtr id_onAdd;
    public void OnAdd (JavaArray<int> values, int currentIndex, int currentSum)
    {
        if (id_onAdd == IntPtr.Zero)
            id_onAdd = JNIEnv.GetMethodID (class_ref, "onAdd",
                    "([III)V");
        JNIEnv.CallVoidMethod (Handle, id_onAdd,
                new JValue (JNIEnv.ToJniHandle (values)),
                new JValue (currentIndex),
new JValue (currentSum));
    }

#pragma warning disable 0169
    static Delegate cb_onAdd;
    static Delegate GetOnAddHandler ()
    {
        if (cb_onAdd == null)
            cb_onAdd = JNINativeWrapper.CreateDelegate ((Action<IntPtr, IntPtr, IntPtr, int, int>) n_OnAdd);
        return cb_onAdd;
    }

    static void n_OnAdd (IntPtr jnienv, IntPtr lrefThis, IntPtr values, int currentIndex, int currentSum)
    {
        IAdderProgress __this = Java.Lang.Object.GetObject<IAdderProgress>(lrefThis, JniHandleOwnership.DoNotTransfer);
        using (var _values = new JavaArray<int>(values, JniHandleOwnership.DoNotTransfer)) {
            __this.OnAdd (_values, currentIndex, currentSum);
        }
    }
#pragma warning restore 0169
#endregion
}
```

## <a name="jni-object-references"></a>Riferimenti a oggetti JNI

Molti metodi JNIEnv restituiscono *riferimenti a oggetti* *JNI* , che sono simili a `GCHandle`s. JNI fornisce tre tipi diversi di riferimenti a oggetti: riferimenti locali, riferimenti globali e riferimenti globali vulnerabili. Tutte e tre sono rappresentate come `System.IntPtr`, *ma* (in base alla sezione tipi di funzione JNI) non tutti i `IntPtr`restituiti dai metodi `JNIEnv` sono riferimenti. Ad esempio, [JNIEnv. GetMethodID](xref:Android.Runtime.JNIEnv.GetMethodID*) restituisce un `IntPtr`, ma non restituisce un riferimento a un oggetto, restituisce una `jmethodID`. Per informazioni dettagliate, vedere la [documentazione della funzione JNI](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html) .

I riferimenti locali vengono creati con *la maggior parte dei* metodi di creazione di riferimenti.
Android consente di esistere solo un numero limitato di riferimenti locali in un determinato momento, in genere pari a 512. I riferimenti locali possono essere eliminati tramite [JNIEnv. DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*).
A differenza di JNI, non tutti i metodi JNIEnv di riferimento che restituiscono riferimenti a oggetti restituiscono riferimenti locali. [JNIEnv. FindClass](xref:Android.Runtime.JNIEnv.FindClass*) restituisce un riferimento *globale* . Si consiglia vivamente di eliminare i riferimenti locali il più rapidamente possibile, possibilmente costruendo un [oggetto Java. lang. Object](xref:Java.Lang.Object) intorno all'oggetto e specificando `JniHandleOwnership.TransferLocalRef` all'oggetto [java. lang. Object (handle IntPtr, JniHandleOwnership Transfer)](xref:Java.Lang.Object#ctor*) Costruttore.

I riferimenti globali vengono creati da [JNIEnv. NewGlobalRef](xref:Android.Runtime.JNIEnv.NewGlobalRef*) e [JNIEnv. FindClass](xref:Android.Runtime.JNIEnv.FindClass*).
Possono essere eliminati con [JNIEnv. DeleteGlobalRef](xref:Android.Runtime.JNIEnv.DeleteGlobalRef*).
Gli emulatori hanno un limite di 2.000 riferimenti globali in attesa, mentre i dispositivi hardware hanno un limite di circa 52.000 riferimenti globali.

I riferimenti globali vulnerabili sono disponibili solo in Android v 2.2 (Froyo) e versioni successive. È possibile eliminare i riferimenti globali vulnerabili con [JNIEnv. DeleteWeakGlobalRef](xref:Android.Runtime.JNIEnv.DeleteWeakGlobalRef*).

### <a name="dealing-with-jni-local-references"></a>Gestione dei riferimenti locali di JNI

I metodi [JNIEnv. GetObjectField](xref:Android.Runtime.JNIEnv.GetObjectField*), [JNIEnv. GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*), [JNIEnv. CallObjectMethod](xref:Android.Runtime.JNIEnv.CallObjectMethod*), [JNIEnv. CallNonvirtualObjectMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualObjectMethod*) e [JNIEnv. CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) restituiscono un `IntPtr` contenente un oggetto JNI riferimento locale a un oggetto Java o `IntPtr.Zero` se Java ha restituito `null`. A causa del numero limitato di riferimenti locali che possono essere in attesa contemporaneamente (512 voci), è consigliabile assicurarsi che i riferimenti vengano eliminati in modo tempestivo. È possibile gestire i riferimenti locali in tre modi: eliminarli in modo esplicito, creare un'istanza di `Java.Lang.Object` per mantenerli e usare `Java.Lang.Object.GetObject<T>()` per creare un wrapper richiamabile gestito.

### <a name="explicitly-deleting-local-references"></a>Eliminazione esplicita di riferimenti locali

[JNIEnv. DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*) viene usato per eliminare i riferimenti locali. Una volta che il riferimento locale è stato eliminato, non è più possibile usarlo, quindi è necessario prestare attenzione per assicurarsi che `JNIEnv.DeleteLocalRef` sia l'ultima operazione eseguita con il riferimento locale.

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
try {
    // Do something with `lref`
}
finally {
    JNIEnv.DeleteLocalRef (lref);
}
```

### <a name="wrapping-with-javalangobject"></a>Wrapping con Java. lang. Object

`Java.Lang.Object` fornisce un costruttore [java. lang. Object (handle IntPtr, JniHandleOwnership Transfer)](xref:Java.Lang.Object#ctor*) che può essere usato per eseguire il wrapping di un riferimento JNI di uscita. Il parametro [JniHandleOwnership](xref:Android.Runtime.JniHandleOwnership) determina il modo in cui deve essere trattato il `IntPtr` parametro:

- [JniHandleOwnership. DoNotTransfer](xref:Android.Runtime.JniHandleOwnership.DoNotTransfer) &ndash; l'istanza di `Java.Lang.Object` creata creerà un nuovo riferimento globale dal parametro `handle` e la `handle` resterà invariata.
    Il chiamante è responsabile di liberare `handle`, se necessario.

- [JniHandleOwnership. TransferLocalRef](xref:Android.Runtime.JniHandleOwnership.TransferLocalRef) &ndash; l'istanza di `Java.Lang.Object` creata creerà un nuovo riferimento globale dal parametro `handle` e `handle` verrà eliminato con [JNIEnv. DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*) . Il chiamante non deve liberare `handle` e non deve usare `handle` al termine dell'esecuzione del costruttore.

- [JniHandleOwnership. TransferGlobalRef](xref:Android.Runtime.JniHandleOwnership.TransferLocalRef) &ndash; l'istanza di `Java.Lang.Object` creata assumerà la proprietà del parametro `handle`. Il chiamante non deve liberare `handle`.

Poiché i metodi di chiamata del metodo JNI restituiscono refs locali, viene normalmente usato `JniHandleOwnership.TransferLocalRef`:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef);
```

Il riferimento globale creato non verrà liberato fino a quando l'istanza di `Java.Lang.Object` non viene sottoposta a Garbage Collection. Se si è in grado di eseguire l'eliminazione dell'istanza, il riferimento globale verrà liberato, velocizzando le operazioni di Garbage Collection:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
using (var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef)) {
    // use value ...
}
```

### <a name="using-javalangobjectgetobjectlttgt"></a>Uso di Java. lang. Object. GetObject&lt;T&gt;()

`Java.Lang.Object` fornisce un metodo [java. lang. Object. GetObject&lt;t&gt;(handle IntPtr, JniHandleOwnership Transfer)](xref:Java.Lang.Object.GetObject*) che può essere usato per creare un wrapper richiamabile gestito del tipo specificato.

Il tipo `T` deve soddisfare i requisiti seguenti:

1. `T` deve essere un tipo riferimento.

1. `T` necessario implementare l'interfaccia `IJavaObject`.

1. Se `T` non è una classe o un'interfaccia astratta, `T` deve fornire un costruttore con i tipi di parametro `(IntPtr,
    JniHandleOwnership)`.

1. Se `T` è una classe astratta o un'interfaccia, è *necessario* che sia disponibile un *invoker* per `T`. Un invoker è un tipo non astratto che eredita `T` o implementa `T` e ha lo stesso nome di `T` con un suffisso invoker. Se, ad esempio, T è l'interfaccia `Java.Lang.IRunnable`, il tipo `Java.Lang.IRunnableInvoker` deve esistere e deve contenere il costruttore di `(IntPtr,
    JniHandleOwnership)` obbligatorio.

Poiché i metodi di chiamata del metodo JNI restituiscono refs locali, viene normalmente usato `JniHandleOwnership.TransferLocalRef`:

```csharp
IntPtr lrefString = JNIEnv.CallObjectMethod(instance, methodID);
Java.Lang.String value = Java.Lang.Object.GetObject<Java.Lang.String>( lrefString, JniHandleOwnership.TransferLocalRef);
```

<a name="_Looking_up_Java_Types" />

## <a name="looking-up-java-types"></a>Ricerca di tipi Java

Per cercare un campo o un metodo in JNI, è necessario cercare prima il tipo dichiarante per il campo o il metodo. Il metodo [Android. Runtime. JNIEnv. FindClass (String)](xref:Android.Runtime.JNIEnv.FindClass*)) viene usato per cercare i tipi Java. Il parametro di stringa è il *riferimento al tipo semplificato* o il *riferimento al tipo completo* per il tipo Java. Per informazioni dettagliate sui riferimenti di tipo semplificato e completo, vedere la [sezione riferimenti ai tipi di JNI](#_JNI_Type_References) .

Nota: a differenza di tutti gli altri `JNIEnv` metodo che restituisce le istanze dell'oggetto, `FindClass` restituisce un riferimento globale, non un riferimento locale.

<a name="_Instance_Fields" />

## <a name="instance-fields"></a>Campi dell'istanza

I campi vengono modificati tramite gli *ID campo*. Gli ID campo vengono ottenuti tramite [JNIEnv. GetFieldID](xref:Android.Runtime.JNIEnv.GetFieldID*), che richiede la classe in cui è definito il campo, il nome del campo e la [firma del tipo JNI](#JNI_Type_Signatures) del campo.

Gli ID campo non devono essere liberati e sono validi fino a quando viene caricato il tipo Java corrispondente. Android attualmente non supporta lo scaricamento delle classi.

Sono disponibili due set di metodi per la modifica dei campi di istanza: uno per la lettura dei campi di istanza e uno per la scrittura dei campi di istanza. Tutti i set di metodi richiedono un ID campo per la lettura o la scrittura del valore del campo.

### <a name="reading-instance-field-values"></a>Lettura dei valori dei campi di istanza

Il set di metodi per la lettura dei valori dei campi di istanza segue il modello di denominazione:

```csharp
* JNIEnv.Get*Field(IntPtr instance, IntPtr fieldID);
```

dove `*` è il tipo del campo:

- [JNIEnv. GetObjectField](xref:Android.Runtime.JNIEnv.GetObjectField*) &ndash; leggere il valore di un campo di istanza che non è un tipo incorporato, ad esempio `java.lang.Object`, matrici e tipi di interfaccia. Il valore restituito è un riferimento locale JNI.

- [JNIEnv. GetBooleanField](xref:Android.Runtime.JNIEnv.GetBooleanField*) &ndash; leggere il valore dei campi di istanza `bool`.

- [JNIEnv. GetByteField](xref:Android.Runtime.JNIEnv.GetByteField*) &ndash; leggere il valore dei campi di istanza `sbyte`.

- [JNIEnv. GetCharField](xref:Android.Runtime.JNIEnv.GetCharField*) &ndash; leggere il valore dei campi di istanza `char`.

- [JNIEnv. GetShortField](xref:Android.Runtime.JNIEnv.GetShortField*) &ndash; leggere il valore dei campi di istanza `short`.

- [JNIEnv. GetIntField](xref:Android.Runtime.JNIEnv.GetIntField*) &ndash; leggere il valore dei campi di istanza `int`.

- [JNIEnv. GetLongField](xref:Android.Runtime.JNIEnv.GetLongField*) &ndash; leggere il valore dei campi di istanza `long`.

- [JNIEnv. GetFloatField](xref:Android.Runtime.JNIEnv.GetFloatField*) &ndash; leggere il valore dei campi di istanza `float`.

- [JNIEnv. GetDoubleField](xref:Android.Runtime.JNIEnv.GetDoubleField*) &ndash; leggere il valore dei campi di istanza `double`.

### <a name="writing-instance-field-values"></a>Scrittura dei valori dei campi di istanza

Il set di metodi per la scrittura dei valori dei campi di istanza segue il modello di denominazione:

```csharp
JNIEnv.SetField(IntPtr instance, IntPtr fieldID, Type value);
```

dove *Type* è il tipo del campo:

- [JNIEnv. sefield](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; scrivere il valore di qualsiasi campo che non sia un tipo incorporato, ad esempio `java.lang.Object`, matrici e tipi di interfaccia. Il valore `IntPtr` può essere un riferimento locale JNI, un riferimento globale JNI, un riferimento globale debole JNI o `IntPtr.Zero` (per `null`).

- [JNIEnv. sefield](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; scrivere il valore dei campi `bool` istanza.

- [JNIEnv. sefield](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; scrivere il valore dei campi `sbyte` istanza.

- [JNIEnv. sefield](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; scrivere il valore dei campi `char` istanza.

- [JNIEnv. sefield](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; scrivere il valore dei campi `short` istanza.

- [JNIEnv. sefield](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; scrivere il valore dei campi `int` istanza.

- [JNIEnv. sefield](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; scrivere il valore dei campi `long` istanza.

- [JNIEnv. sefield](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; scrivere il valore dei campi `float` istanza.

- [JNIEnv. sefield](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; scrivere il valore dei campi `double` istanza.

<a name="_Static_Fields" />

## <a name="static-fields"></a>Campi statici

I campi statici vengono modificati tramite gli *ID dei campi*. Gli ID campo vengono ottenuti tramite [JNIEnv. GetStaticFieldID](xref:Android.Runtime.JNIEnv.GetStaticFieldID*), che richiede la classe in cui è definito il campo, il nome del campo e la [firma del tipo JNI](#JNI_Type_Signatures) del campo.

Gli ID campo non devono essere liberati e sono validi fino a quando viene caricato il tipo Java corrispondente. Android attualmente non supporta lo scaricamento delle classi.

Sono disponibili due set di metodi per la modifica di campi statici: uno per la lettura dei campi di istanza e uno per la scrittura dei campi di istanza. Tutti i set di metodi richiedono un ID campo per la lettura o la scrittura del valore del campo.

### <a name="reading-static-field-values"></a>Lettura di valori di campi statici

Il set di metodi per la lettura dei valori dei campi statici segue il modello di denominazione:

```csharp
* JNIEnv.GetStatic*Field(IntPtr class, IntPtr fieldID);
```

dove `*` è il tipo del campo:

- [JNIEnv. GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*) &ndash; leggere il valore di un campo statico che non è un tipo incorporato, ad esempio `java.lang.Object`, matrici e tipi di interfaccia. Il valore restituito è un riferimento locale JNI.

- [JNIEnv. GetStaticBooleanField](xref:Android.Runtime.JNIEnv.GetStaticBooleanField*) &ndash; leggere il valore di `bool` campi statici.

- [JNIEnv. GetStaticByteField](xref:Android.Runtime.JNIEnv.GetStaticByteField*) &ndash; leggere il valore di `sbyte` campi statici.

- [JNIEnv. GetStaticCharField](xref:Android.Runtime.JNIEnv.GetStaticCharField*) &ndash; leggere il valore di `char` campi statici.

- [JNIEnv. GetStaticShortField](xref:Android.Runtime.JNIEnv.GetStaticShortField*) &ndash; leggere il valore di `short` campi statici.

- [JNIEnv. GetStaticLongField](xref:Android.Runtime.JNIEnv.GetStaticLongField*) &ndash; leggere il valore di `long` campi statici.

- [JNIEnv. GetStaticFloatField](xref:Android.Runtime.JNIEnv.GetStaticFloatField*) &ndash; leggere il valore di `float` campi statici.

- [JNIEnv. GetStaticDoubleField](xref:Android.Runtime.JNIEnv.GetStaticDoubleField*) &ndash; leggere il valore di `double` campi statici.

### <a name="writing-static-field-values"></a>Scrittura di valori di campi statici

Il set di metodi per la scrittura di valori di campo statici segue il modello di denominazione:

```csharp
JNIEnv.SetStaticField(IntPtr class, IntPtr fieldID, Type value);
```

dove *Type* è il tipo del campo:

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; scrivere il valore di un campo statico che non è un tipo incorporato, ad esempio `java.lang.Object`, matrici e tipi di interfaccia. Il valore `IntPtr` può essere un riferimento locale JNI, un riferimento globale JNI, un riferimento globale debole JNI o `IntPtr.Zero` (per `null`).

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; scrivere il valore di `bool` campi statici.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; scrivere il valore di `sbyte` campi statici.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; scrivere il valore di `char` campi statici.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; scrivere il valore di `short` campi statici.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; scrivere il valore di `int` campi statici.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; scrivere il valore di `long` campi statici.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; scrivere il valore di `float` campi statici.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; scrivere il valore di `double` campi statici.

<a name="_Instance_Methods" />

## <a name="instance-methods"></a>Metodi di istanza

I metodi di istanza vengono richiamati tramite gli *ID del metodo*. Gli ID metodo vengono ottenuti tramite [JNIEnv. GetMethodID](xref:Android.Runtime.JNIEnv.GetMethodID*), che richiede il tipo in cui è definito il metodo, il nome del metodo e la [firma del tipo JNI](#JNI_Type_Signatures) del metodo.

Gli ID metodo non devono essere liberati e sono validi purché venga caricato il tipo Java corrispondente. Android attualmente non supporta lo scaricamento delle classi.

Esistono due set di metodi per richiamare i metodi: uno per richiamare i metodi virtualmente e uno per richiamare i metodi in maniera non virtuale. Entrambi i set di metodi richiedono un ID metodo per richiamare il metodo e la chiamata non virtuale richiede anche di specificare l'implementazione della classe da richiamare.

I metodi di interfaccia possono essere cercati solo all'interno del tipo dichiarante. non è possibile cercare metodi che derivano da interfacce estese/ereditate. Per ulteriori informazioni, vedere la sezione relativa all'implementazione di interfacce/invoker in seguito.

È possibile cercare tutti i metodi dichiarati nella classe o in qualsiasi classe di base o interfaccia implementata.

### <a name="virtual-method-invocation"></a>Chiamata al metodo virtuale

Il set di metodi per richiamare i metodi virtualmente segue il modello di denominazione:

```csharp
* JNIEnv.Call*Method( IntPtr instance, IntPtr methodID, params JValue[] args );
```

dove `*` è il tipo restituito del metodo.

- [JNIEnv. CallObjectMethod](xref:Android.Runtime.JNIEnv.CallObjectMethod*) &ndash; richiamare un metodo che restituisce un tipo non incorporato, ad esempio `java.lang.Object`, matrici e interfacce. Il valore restituito è un riferimento locale JNI.

- [JNIEnv. CallBooleanMethod](xref:Android.Runtime.JNIEnv.CallBooleanMethod*) &ndash; richiamare un metodo che restituisce un valore `bool`.

- [JNIEnv. CallByteMethod](xref:Android.Runtime.JNIEnv.CallByteMethod*) &ndash; richiamare un metodo che restituisce un valore `sbyte`.

- [JNIEnv. CallCharMethod](xref:Android.Runtime.JNIEnv.CallCharMethod*) &ndash; richiamare un metodo che restituisce un valore `char`.

- [JNIEnv. CallShortMethod](xref:Android.Runtime.JNIEnv.CallShortMethod*) &ndash; richiamare un metodo che restituisce un valore `short`.

- [JNIEnv. CallLongMethod](xref:Android.Runtime.JNIEnv.CallLongMethod*) &ndash; richiamare un metodo che restituisce un valore `long`.

- [JNIEnv. CallFloatMethod](xref:Android.Runtime.JNIEnv.CallFloatMethod*) &ndash; richiamare un metodo che restituisce un valore `float`.

- [JNIEnv. CallDoubleMethod](xref:Android.Runtime.JNIEnv.CallDoubleMethod*) &ndash; richiamare un metodo che restituisce un valore `double`.

### <a name="non-virtual-method-invocation"></a>Chiamata al metodo non virtuale

Il set di metodi per richiamare i metodi non praticamente segue il modello di denominazione:

```csharp
* JNIEnv.CallNonvirtual*Method( IntPtr instance, IntPtr class, IntPtr methodID, params JValue[] args );
```

dove `*` è il tipo restituito del metodo. La chiamata al metodo non virtuale viene in genere utilizzata per richiamare il metodo di base di un metodo virtuale.

- [JNIEnv. CallNonvirtualObjectMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualObjectMethod*) &ndash; non richiamano virtualmente un metodo che restituisce un tipo non incorporato, ad esempio `java.lang.Object`, matrici e interfacce. Il valore restituito è un riferimento locale JNI.

- [JNIEnv. CallNonvirtualBooleanMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualBooleanMethod*) &ndash; non richiamano virtualmente un metodo che restituisce un valore `bool`.

- [JNIEnv. CallNonvirtualByteMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualByteMethod*) &ndash; non richiamano virtualmente un metodo che restituisce un valore `sbyte`.

- [JNIEnv. CallNonvirtualCharMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualCharMethod*) &ndash; non richiamano virtualmente un metodo che restituisce un valore `char`.

- [JNIEnv. CallNonvirtualShortMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualShortMethod*) &ndash; non richiamano virtualmente un metodo che restituisce un valore `short`.

- [JNIEnv. CallNonvirtualLongMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualLongMethod*) &ndash; non richiamano virtualmente un metodo che restituisce un valore `long`.

- [JNIEnv. CallNonvirtualFloatMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualFloatMethod*) &ndash; non richiamano virtualmente un metodo che restituisce un valore `float`.

- [JNIEnv. CallNonvirtualDoubleMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualDoubleMethod*) &ndash; non richiamano virtualmente un metodo che restituisce un valore `double`.

<a name="_Static_Methods" />

## <a name="static-methods"></a>Metodi statici

I metodi statici vengono richiamati tramite gli *ID del metodo*. Gli ID metodo vengono ottenuti tramite [JNIEnv. GetStaticMethodID](xref:Android.Runtime.JNIEnv.GetStaticMethodID*), che richiede il tipo in cui è definito il metodo, il nome del metodo e la [firma del tipo JNI](#JNI_Type_Signatures) del metodo.

Gli ID metodo non devono essere liberati e sono validi purché venga caricato il tipo Java corrispondente. Android attualmente non supporta lo scaricamento delle classi.

### <a name="static-method-invocation"></a>Chiamata al metodo statico

Il set di metodi per richiamare i metodi virtualmente segue il modello di denominazione:

```csharp
* JNIEnv.CallStatic*Method( IntPtr class, IntPtr methodID, params JValue[] args );
```

dove `*` è il tipo restituito del metodo.

- [JNIEnv. CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) &ndash; richiamare un metodo statico che restituisce un tipo non incorporato, ad esempio `java.lang.Object`, matrici e interfacce. Il valore restituito è un riferimento locale JNI.

- [JNIEnv. CallStaticBooleanMethod](xref:Android.Runtime.JNIEnv.CallStaticBooleanMethod*) &ndash; richiamare un metodo statico che restituisce un valore `bool`.

- [JNIEnv. CallStaticByteMethod](xref:Android.Runtime.JNIEnv.CallStaticByteMethod*) &ndash; richiamare un metodo statico che restituisce un valore `sbyte`.

- [JNIEnv. CallStaticCharMethod](xref:Android.Runtime.JNIEnv.CallStaticCharMethod*) &ndash; richiamare un metodo statico che restituisce un valore `char`.

- [JNIEnv. CallStaticShortMethod](xref:Android.Runtime.JNIEnv.CallStaticShortMethod*) &ndash; richiamare un metodo statico che restituisce un valore `short`.

- [JNIEnv. CallStaticLongMethod](xref:Android.Runtime.JNIEnv.CallLongMethod*) &ndash; richiamare un metodo statico che restituisce un valore `long`.

- [JNIEnv. CallStaticFloatMethod](xref:Android.Runtime.JNIEnv.CallStaticFloatMethod*) &ndash; richiamare un metodo statico che restituisce un valore `float`.

- [JNIEnv. CallStaticDoubleMethod](xref:Android.Runtime.JNIEnv.CallStaticDoubleMethod*) &ndash; richiamare un metodo statico che restituisce un valore `double`.

<a name="JNI_Type_Signatures" />

## <a name="jni-type-signatures"></a>Firme di tipo JNI

Le [firme di tipo JNI](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/types.html#wp16432) sono [riferimenti di tipo JNI](#_JNI_Type_References) (anche se non riferimenti ai tipi semplificati), ad eccezione dei metodi. Con i metodi, la firma del tipo JNI è una parentesi aperta `'('`, seguita dai riferimenti al tipo per tutti i tipi di parametro concatenati (senza virgole separate o nient'altro), seguiti da una parentesi di chiusura `')'`, seguita da Riferimento al tipo JNI del tipo restituito dal metodo.

Ad esempio, dato il metodo Java:

```java
long f(int n, String s, int[] array);
```

La firma del tipo JNI sarà:

```csharp
(ILjava/lang/String;[I)J
```

In generale, *è consigliabile usare* il comando `javap` per determinare le firme JNI. Ad esempio, la firma del tipo JNI del metodo [java. lang. thread. state. valueOf (String)](https://developer.android.com/reference/java/lang/Thread.State.html#valueOf(java.lang.String)) è "(Ljava/lang/String;) Ljava/lang/thread $ state;", mentre la firma del tipo JNI del metodo [java. lang. thread. state. Values](https://developer.android.com/reference/java/lang/Thread.State.html#values) è "() [Ljava/ lang/thread $ state; ". Osservare i punti e virgola finali; che *fanno* parte della firma del tipo JNI.

<a name="_JNI_Type_References" />

## <a name="jni-type-references"></a>Riferimenti al tipo JNI

I riferimenti ai tipi JNI sono diversi dai riferimenti ai tipi Java. Non è possibile usare nomi di tipi Java completi, ad esempio `java.lang.String` con JNI, è necessario usare invece le varianti JNI `"java/lang/String"` o `"Ljava/lang/String;"`, a seconda del contesto; per informazioni dettagliate, vedere di seguito.
Sono disponibili quattro tipi di riferimenti al tipo JNI:

- **predefinito**
- **semplificato**
- **type**
- **array**

### <a name="built-in-type-references"></a>Riferimenti ai tipi incorporati

I riferimenti ai tipi incorporati sono costituiti da un singolo carattere, usati per fare riferimento a tipi di valore predefiniti. Il mapping è il seguente:

- `"B"` per `sbyte`.
- `"S"` per `short`.
- `"I"` per `int`.
- `"J"` per `long`.
- `"F"` per `float`.
- `"D"` per `double`.
- `"C"` per `char`.
- `"Z"` per `bool`.
- `"V"` per `void` tipi restituiti del metodo.

<a name="_Simplified_Type_References_1" />

### <a name="simplified-type-references"></a>Riferimenti di tipo semplificato

I riferimenti ai tipi semplificati possono essere usati solo in [JNIEnv. FindClass (String)](xref:Android.Runtime.JNIEnv.FindClass*).
Esistono due modi per derivare un riferimento di tipo semplificato:

1. Da un nome Java completo, sostituire ogni `'.'` nel nome del pacchetto e prima del nome del tipo con `'/'` e ogni `'.'` all'interno di un nome di tipo con `'$'`.

1. Leggere l'output del `'unzip -l android.jar | grep JavaName'`.

Uno dei due comporterà il mapping del tipo Java [java. lang. thread. state](https://developer.android.com/reference/java/lang/Thread.State.html) al riferimento di tipo semplificato `java/lang/Thread$State`.

### <a name="type-references"></a>Riferimenti ai tipi

Un riferimento a un tipo è un riferimento a un tipo incorporato o un riferimento di tipo semplificato con un prefisso `'L'` e un suffisso di `';'`. Per il tipo Java [java. lang. String](https://developer.android.com/reference/java/lang/String.html), il riferimento al tipo semplificato è `"java/lang/String"`, mentre il riferimento al tipo è `"Ljava/lang/String;"`.

I riferimenti ai tipi vengono usati con i riferimenti ai tipi di matrice e con le firme JNI.

Un ulteriore modo per ottenere un riferimento al tipo consiste nel leggere l'output di `'javap -s -classpath android.jar fully.qualified.Java.Name'`.
A seconda del tipo richiesto, è possibile utilizzare una dichiarazione del costruttore o un tipo restituito del metodo per determinare il nome JNI. Esempio:

```shell
$ javap -classpath android.jar -s java.lang.Thread.State
Compiled from "Thread.java"
```

```java
public final class java.lang.Thread$State extends java.lang.Enum{
public static final java.lang.Thread$State NEW;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State RUNNABLE;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State BLOCKED;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State WAITING;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State TIMED_WAITING;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State TERMINATED;
  Signature: Ljava/lang/Thread$State;
public static java.lang.Thread$State[] values();
  Signature: ()[Ljava/lang/Thread$State;
public static java.lang.Thread$State valueOf(java.lang.String);
  Signature: (Ljava/lang/String;)Ljava/lang/Thread$State;
static {};
  Signature: ()V
}
```

`Thread.State` è un tipo di enumerazione Java, quindi è possibile usare la firma del metodo `valueOf` per determinare che il riferimento al tipo è Ljava/lang/thread $ state;.

### <a name="array-type-references"></a>Riferimenti ai tipi di matrice

I riferimenti ai tipi di matrice sono `'['` preceduti da un riferimento al tipo JNI.
Non è possibile usare i riferimenti di tipo semplificato quando si specificano matrici.

Ad esempio, `int[]` è `"[I"`, `int[][]` è `"[[I"`e `java.lang.Object[]` è `"[Ljava/lang/Object;"`.

## <a name="java-generics-and-type-erasure"></a>Generics Java e cancellazione di tipi

Nella *maggior parte* dei casi, come visto da JNI, i generics Java *non esistono*.
Ci sono alcune "rughe", ma le rughe si trovano nel modo in cui Java interagisce con i generics, non con il modo in cui JNI Cerca e richiama i membri generici.

Non esiste alcuna differenza tra un tipo o un membro generico e un membro o un tipo non generico quando si interagisce con JNI. Ad esempio, il tipo generico [java. lang. Class&lt;t&gt;](https://developer.android.com/reference/java/lang/Class.html) è anche il tipo generico "raw" `java.lang.Class`, entrambi con lo stesso riferimento di tipo semplificato, `"java/lang/Class"`.

## <a name="java-native-interface-support"></a>Supporto Java Native Interface

[Android. Runtime. JNIEnv](xref:Android.Runtime.JNIEnv) è un wrapper gestito per l'interfaccia Jave nativa (JNI). Le funzioni JNI sono dichiarate all'interno della [specifica Java Native Interface](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html), sebbene i metodi siano stati modificati per rimuovere il parametro esplicito `JNIEnv*` e `IntPtr` venga usato al posto di `jobject`, `jclass`, `jmethodID`e così via. Si consideri, ad esempio, la [funzione JNI NewObject](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp4517):

```csharp
jobject NewObjectA(JNIEnv *env, jclass clazz, jmethodID methodID, jvalue *args);
```

Questo metodo viene esposto come metodo [JNIEnv. NewObject](xref:Android.Runtime.JNIEnv.NewObject*) :

```csharp
public static IntPtr NewObject(IntPtr clazz, IntPtr jmethod, params JValue[] parms);
```

La conversione tra le due chiamate è ragionevolmente semplice. In C si avrebbe:

```c
jobject CreateMapActivity(JNIEnv *env)
{
    jclass    Map_Class   = (*env)->FindClass(env, "mono/samples/googlemaps/MyMapActivity");
    jmethodID Map_defCtor = (*env)->GetMethodID (env, Map_Class, "<init>", "()V");
    jobject   instance    = (*env)->NewObject (env, Map_Class, Map_defCtor);

    return instance;
}
```

L' C# equivalente sarebbe:

```csharp
IntPtr CreateMapActivity()
{
    IntPtr Map_Class   = JNIEnv.FindClass ("mono/samples/googlemaps/MyMapActivity");
    IntPtr Map_defCtor = JNIEnv.GetMethodID (Map_Class, "<init>", "()V");
    IntPtr instance    = JNIEnv.NewObject (Map_Class, Map_defCtor);

    return instance;
}
```

Una volta che un'istanza dell'oggetto Java è stata mantenuta in un elemento IntPtr, è probabile che si desideri eseguire un'operazione. A tale scopo, è possibile usare i metodi JNIEnv, ad esempio [JNIEnv. CallVoidMethod ()](xref:Android.Runtime.JNIEnv.CallVoidMethod*) , ma se è già C# presente un wrapper analogico, è opportuno costruire un wrapper sul riferimento JNI. Questa operazione può essere eseguita tramite il metodo di estensione [JavaCast\<t >](xref:Android.Runtime.Extensions.JavaCast*) :

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = new Java.Lang.Object(lrefActivity, JniHandleOwnership.TransferLocalRef)
    .JavaCast<Activity>();
```

È anche possibile usare il metodo [java. lang. Object. GetObject\<t >](xref:Java.Lang.Object.GetObject*) :

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = Java.Lang.Object.GetObject<Activity>(lrefActivity, JniHandleOwnership.TransferLocalRef);
```

Inoltre, tutte le funzioni JNI sono state modificate rimuovendo il parametro `JNIEnv*` presente in ogni funzione JNI.

## <a name="summary"></a>Riepilogo

La gestione diretta di JNI è un'esperienza terribile da evitare a tutti i costi. Sfortunatamente, non è sempre evitabile. si spera che questa guida fornisca assistenza quando si raggiungeranno i casi Java non associati con mono per Android.

## <a name="related-links"></a>Collegamenti correlati

- [Specifica di Java Native Interface](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/jniTOC.html)
- [Funzioni di interfaccia Native Java](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)
