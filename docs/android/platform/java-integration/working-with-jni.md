---
title: Lavorare con JNI e Xamarin.Android
description: Xamarin.Android consente di scrivere applicazioni Android con C , invece di Java. Diversi assembly sono forniti con Xamarin.Android che forniscono associazioni per le librerie Java, tra cui Mono.Android.dll e Mono.Android.GoogleMaps.dll. Tuttavia, le associazioni non vengono fornite per ogni possibile libreria Java e le associazioni fornite potrebbero non associare ogni tipo e membro Java.However, bindings are not provided for every possible Java library, and the bindings that are provided may not bind every Java type and member. Per utilizzare tipi e membri Java non associati, è possibile utilizzare Java Native Interface (JNI). In questo articolo viene illustrato come utilizzare JNI per interagire con tipi Java e membri da applicazioni Xamarin.Android.This article illustrates how to use JNI to interact with Java types and members from Xamarin.Android applications.
ms.prod: xamarin
ms.assetid: A417DEE9-7B7B-4E35-A79C-284739E3838E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 0fa717a775ff2f1ace9e248a8afde8d373e8a1f8
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292988"
---
# <a name="working-with-jni-and-xamarinandroid"></a>Lavorare con JNI e Xamarin.Android

_Xamarin.Android consente di scrivere applicazioni Android con C , invece di Java. Diversi assembly sono forniti con Xamarin.Android che forniscono associazioni per le librerie Java, tra cui Mono.Android.dll e Mono.Android.GoogleMaps.dll. Tuttavia, le associazioni non vengono fornite per ogni possibile libreria Java e le associazioni fornite potrebbero non associare ogni tipo e membro Java.However, bindings are not provided for every possible Java library, and the bindings that are provided may not bind every Java type and member. Per utilizzare tipi e membri Java non associati, è possibile utilizzare Java Native Interface (JNI). In questo articolo viene illustrato come utilizzare JNI per interagire con tipi Java e membri da applicazioni Xamarin.Android.This article illustrates how to use JNI to interact with Java types and members from Xamarin.Android applications._

## <a name="overview"></a>Panoramica

Non è sempre necessario o possibile creare un Managed Callable Wrapper (MCW) per richiamare il codice Java. In molti casi, JNI "inline" è perfettamente accettabile e utile per l'uso una tantum di membri Java non associati. È spesso più semplice utilizzare JNI per richiamare un singolo metodo su una classe Java piuttosto che generare un'intera associazione .jar.

Xamarin.Android fornisce `Mono.Android.dll` l'assembly, che fornisce `android.jar` un'associazione per la libreria di Android.Xamarin.Android provides the assembly, which provides a binding for Android's library. I tipi e `Mono.Android.dll` i membri non `android.jar` presenti all'interno e i tipi non presenti all'interno possono essere utilizzati associandoli manualmente. Per associare tipi e membri Java, utilizzare **Java Native Interface** (**JNI**) per cercare tipi, leggere e scrivere campi e richiamare metodi.

L'API JNI in Xamarin.Android è `System.Reflection` concettualmente molto simile all'API in .NET: consente di cercare tipi e membri in base al nome, leggere e scrivere valori di campo, richiamare metodi e altro ancora. È possibile utilizzare JNI e l'attributo `Android.Runtime.RegisterAttribute` personalizzato per dichiarare metodi virtuali che possono essere associati per supportare l'override. È possibile associare le interfacce in modo che possano essere implementate in C .

Questo documento spiega:

- Come JNI si riferisce ai tipi.
- Come cercare, leggere e scrivere campi.
- Come cercare e richiamare i metodi.
- Come esporre metodi virtuali per consentire l'override dal codice gestito.
- Come esporre le interfacce.

## <a name="requirements"></a>Requisiti

JNI, come esposto tramite lo spazio dei [nomi Android.Runtime.JNIEnv](xref:Android.Runtime.JNIEnv), è disponibile in ogni versione di Xamarin.Android.
Per associare tipi e interfacce Java, è necessario utilizzare Xamarin.Android 4.0 o versione successiva.

## <a name="managed-callable-wrappers"></a>Wrapper callable gestiti

Un **Managed Callable Wrapper** (**MCW**) è *un'associazione* per una classe Java o un'interfaccia che esegue il wrapping di tutti i macchinari JNI in modo che il codice del client in linguaggio C, non debba preoccuparsi della complessità sottostante di JNI. La `Mono.Android.dll` maggior parte dei quali è costituita da wrapper chiamabili gestiti.

I wrapper richiamabili gestiti hanno due scopi:

1. Incapsulare JNI utilizzare in modo che il codice client non ha bisogno di conoscere la complessità sottostante.
1. Rendere possibile la sottoclasse dei tipi Java e implementare le interfacce Java.

Il primo scopo è puramente per comodità e l'incapsulamento della complessità in modo che i consumer dispongano di un set di classi semplice e gestito da utilizzare. Ciò richiede l'utilizzo dei vari membri [JNIEnv](xref:Android.Runtime.JNIEnv) come descritto più avanti in questo articolo. Tenere presente che i wrapper richiamabili gestiti &ndash; non sono strettamente necessari l'uso di JNI "inline" è perfettamente accettabile ed è utile per l'utilizzo una tantum di membri Java non associati. La creazione di sottoclassi e l'implementazione dell'interfaccia richiedono l'utilizzo di wrapper chiamabili gestiti.

## <a name="android-callable-wrappers"></a>Android Callable Wrapper

Android callable wrapper (ACW) sono necessari ogni volta che il runtime Android (ART) deve richiamare codice gestito; questi wrapper sono necessari perché non è possibile registrare le classi con ART in fase di esecuzione.
(In particolare, la funzione JNI [DefineClass](https://docs.oracle.com/javase/6/docs/technotes/guides/jni/spec/functions.html#wp15986) non è supportata dal runtime Android. Android callable wrapper compensano quindi la mancanza di supporto per la registrazione del tipo di runtime.)

Ogni volta che il codice Android deve eseguire un metodo virtuale o di interfaccia che viene sottoposto a override o implementato nel codice gestito, Xamarin.Android deve fornire un proxy Java in modo che questo metodo venga inviato al tipo gestito appropriato. Questi tipi proxy Java sono codice Java che hanno la "stessa" classe di base e l'elenco di interfacce Java come tipo gestito, implementando gli stessi costruttori e dichiarando eventuali metodi di classe di base e di interfaccia sottoposti a override.

I wrapper richiamabili Android vengono generati dal programma **monodroid.exe** durante il processo di [compilazione](~/android/deploy-test/building-apps/build-process.md)e vengono generati per tutti i tipi che (direttamente o indirettamente) ereditano [Java.Lang.Object](xref:Java.Lang.Object).

### <a name="implementing-interfaces"></a>Implementazione di interfacce

In alcuni casi potrebbe essere necessario implementare un'interfaccia Android, ad esempio [Android.Content.IComponentCallbacks](xref:Android.Content.IComponentCallbacks).

Tutte le classi e le interfacce Android estendono l'interfaccia [Android.Runtime.IJavaObject;](xref:Android.Runtime.IJavaObject) pertanto, tutti i `IJavaObject`tipi Android devono implementare .
Xamarin.Android sfrutta questo &ndash; fatto `IJavaObject` che utilizza per fornire Android con un proxy Java (un wrapper richiamabile Android) per il tipo gestito specificato. Poiché **monodroid.exe** `Java.Lang.Object` cerca solo le `IJavaObject`sottoclassi (che devono implementare ), la sottoclasse `Java.Lang.Object` fornisce un modo per implementare interfacce nel codice gestito. Ad esempio:

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

### <a name="implementation-details"></a>Dettagli dell'implementazione

*Il resto di questo articolo fornisce i dettagli di implementazione soggetti a modifiche senza preavviso* (e viene presentato qui solo perché gli sviluppatori possono essere curiosi di sapere cosa sta succedendo sotto il cofano).

Ad esempio, data l'origine seguente di C:

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

Il programma **mandroid.exe** genererà il seguente Android Callable Wrapper:

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

Si noti che la classe base viene mantenuta e vengono fornite dichiarazioni di metodi nativi per ogni metodo sottoposto a override all'interno del codice gestito.

### <a name="exportattribute-and-exportfieldattribute"></a>ExportAttribute ed ExportFieldAttribute

In genere, Xamarin.Android genera automaticamente il codice Java che comprende l'ACW; questa generazione si basa sui nomi di classe e metodo quando una classe deriva da una classe Java ed esegue l'override dei metodi Java esistenti. Tuttavia, in alcuni scenari, la generazione di codice non è adeguata, come descritto di seguito:However, in some scenarios, the code generation is not adequate, as outlined below:

- Android supporta i nomi delle azioni negli attributi XML di layout, ad esempio l'attributo XML [android:onClick.Android](xref:Android.Views.View.IOnClickListener.OnClick*) supports action names in layout XML attributes, for example the android:onClick XML attribute. Quando viene specificato, l'istanza di View gonfiata tenta di cercare il metodo Java.

- L'interfaccia [java.io.Serializable](https://developer.android.com/reference/java/io/Serializable.html) richiede `readObject` e `writeObject` metodi. Poiché non sono membri di questa interfaccia, l'implementazione gestita corrispondente non espone questi metodi al codice Java.As they are not members of this interface, our corresponding managed implementation does not expose these methods to Java code.

- L'interfaccia [android.os.Parcelable](xref:Android.OS.Parcelable) prevede che una classe `CREATOR` di `Parcelable.Creator`implementazione deve avere un campo statico di tipo . Il codice Java generato richiede un campo esplicito. Con il nostro scenario standard, non è possibile eseguire l'output del campo nel codice Java dal codice gestito.

Poiché la generazione di codice non fornisce una soluzione per generare metodi Java arbitrari con nomi arbitrari, a partire da Xamarin.Android 4.2, [ExportAttribute](xref:Java.Interop.ExportAttribute) ed [ExportFieldAttribute](xref:Java.Interop.ExportFieldAttribute) sono stati introdotti per offrire una soluzione agli scenari precedenti. Entrambi gli attributi `Java.Interop` risiedono nello spazio dei nomi:Both attributes reside in the namespace:

- `ExportAttribute`&ndash; specifica un nome di metodo e i relativi tipi di eccezione previsti (per fornire "throw" espliciti in Java). Quando viene utilizzato su un metodo, il metodo "esporta" un metodo Java che genera un codice dispatch alla chiamata JNI corrispondente al metodo gestito. Questo può essere `android:onClick` `java.io.Serializable`utilizzato con e .

- `ExportFieldAttribute`&ndash; specifica un nome di campo. Si trova su un metodo che funziona come un inizializzatore di campo. Questo può essere `android.os.Parcelable`utilizzato con .

#### <a name="troubleshooting-exportattribute-and-exportfieldattribute"></a>Risoluzione dei problemi relativi a ExportAttribute ed ExportFieldAttribute

- La creazione di pacchetti non riesce a `ExportAttribute` causa `ExportFieldAttribute` della mancanza **di Mono.Android.Export.dll** &ndash; se è stato utilizzato o su alcuni metodi nel codice o nelle librerie dipendenti, è necessario aggiungere **Mono.Android.Export.dll**. Questo assembly è isolato per supportare il codice di callback da Java.This assembly is isolated to support callback code from Java. È separato da **Mono.Android.dll** in quanto aggiunge dimensioni aggiuntive all'applicazione.

- Nella build `MissingMethodException` di rilascio, si verifica per i metodi di &ndash; esportazione nella build di rilascio, `MissingMethodException` si verifica per i metodi di esportazione. (Questo problema è stato risolto nella versione più recente di Xamarin.Android.)

### <a name="exportparameterattribute"></a>ExportParameterAttribute

`ExportAttribute`e `ExportFieldAttribute` fornire funzionalità che può essere utilizzato dal codice di runtime Java. Questo codice di runtime accede al codice gestito tramite i metodi JNI generati guidati da tali attributi. Di conseguenza, non esiste alcun metodo Java esistente associato al metodo gestito; Di conseguenza, il metodo Java viene generato da una firma del metodo gestito.

Tuttavia, questo caso non è completamente determinante. In particolare, ciò è vero in alcuni mapping avanzati tra tipi gestiti e tipi Java, ad esempio:

- InputStream
- Outputstream
- XmlPullParser (informazioni in questo con tag)
- XmlResourceParser (informazioni in base al gruppo dei documenti

Quando i tipi come questi sono necessari `ExportParameterAttribute` per i metodi esportati, è necessario utilizzare l'oggetto per assegnare un tipo al parametro o al valore restituito corrispondente.

### <a name="annotation-attribute"></a>Attributo annotazione

In Xamarin.Android 4.2, `IAnnotation` abbiamo convertito i tipi di implementazione in attributi (System.Attribute) e aggiunto il supporto per la generazione di annotazioni nei wrapper Java.In amarin.Android 4.2, we converted implementation types into attributes (System.Attribute), and added support for annotation generation in Java wrappers.

Ciò significa che le seguenti modifiche direzionali:

- Il generatore `Java.Lang.DeprecatedAttribute` di `java.Lang.Deprecated` associazione genera `[Obsolete]` da (mentre deve essere in codice gestito).

- Ciò non significa `Java.Lang.Deprecated` che la classe esistente svanirà. Questi oggetti basati su Java potrebbero ancora essere utilizzati come normali oggetti Java (se tale utilizzo esiste). Ci saranno `Deprecated` `DeprecatedAttribute` e lezioni.

- La `Java.Lang.DeprecatedAttribute` classe è `[Annotation]` contrassegnata come . Quando è presente un attributo personalizzato `[Annotation]` ereditato da questo attributo, l'attività@Deprecatedmsbuild genererà un'annotazione Java per tale attributo personalizzato ( ) in Android Callable Wrapper (ACW).

- Le annotazioni possono essere generate su classi, metodi e campi esportati (che è un metodo nel codice gestito).

Se la classe contenitore (la classe annotata stessa o la classe che contiene i membri con annotazioni) non è registrata, l'intera origine della classe Java non viene generata affatto, incluse le annotazioni. Per i metodi, `ExportAttribute` è possibile specificare l'oggetto per ottenere il metodo generato e annotato in modo esplicito. Inoltre, non è una funzionalità per "generare" una definizione di classe di annotazione Java. In altre parole, se si definisce un attributo gestito personalizzato per una determinata annotazione, sarà necessario aggiungere un'altra libreria JAR che contiene la classe di annotazione Java corrispondente. L'aggiunta di un file di origine Java che definisce il tipo di annotazione non è sufficiente. Il compilatore Java non funziona allo stesso modo di **apt**.

Inoltre si applicano le seguenti limitazioni:

- Questo processo di `@Target` conversione non considera l'annotazione sul tipo di annotazione finora.

- Gli attributi su una proprietà non funzionano. Utilizzare invece gli attributi per il getter o il setter della proprietà.

## <a name="class-binding"></a>Associazione di classi

Associazione di una classe significa scrivere un wrapper richiamabile gestito per semplificare la chiamata del tipo Java sottostante.

L'associazione di metodi virtuali e astratti per consentire l'override da C , richiede Xamarin.Android 4.0. Tuttavia, qualsiasi versione di Xamarin.Android può associare metodi non virtuali, metodi statici o metodi virtuali senza supportare gli override.

Un'associazione contiene in genere gli elementi seguenti:A binding typically contains the following items:

- Handle [JNI per il tipo Java da associare.](#_Looking_up_Java_Types)

- ID e proprietà dei campi [JNI per ogni campo associato.](#_Instance_Fields)

- ID e metodi di [metodo JNI per ogni metodo associato.](#_Instance_Methods)

- Se è necessaria la sottoclasse, il tipo deve disporre di un attributo personalizzato [RegisterAttribute](xref:Android.Runtime.RegisterAttribute) nella `true`dichiarazione del tipo con [RegisterAttribute.DoNotGenerateAcw](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw) impostato su .

### <a name="declaring-type-handle"></a>Dichiarazione dell'handle del tipoDeclaring Type Handle

I metodi di ricerca di campi e metodi richiedono un riferimento a un oggetto che fa riferimento al relativo tipo dichiarante. Per convenzione, questo `class_ref` si svolge in un campo:

```csharp
static IntPtr class_ref = JNIEnv.FindClass(CLASS);
```

Vedere la sezione [Riferimenti ai](#_JNI_Type_References) `CLASS` tipi JNI per informazioni dettagliate sul token.

### <a name="binding-fields"></a>Campi di associazione

I campi Java vengono esposti come proprietà di C, ad esempio il campo Java [java.lang.System.in](https://developer.android.com/reference/java/lang/System.html#in) è associato come [Java.Lang.JavaSystem.In](xref:Java.Lang.JavaSystem.In)della proprietà di C.
Inoltre, poiché JNI distingue tra campi statici e campi di istanza, durante l'implementazione delle proprietà vengono utilizzati metodi diversi.

L'associazione di campi prevede tre set di metodi:Field binding involves three sets of methods:

1. Il metodo *get field id.* Il metodo *get field id* è responsabile della restituzione di un handle di campo che verrà utilizzato dai metodi get *value* e set *field value.* Per ottenere l'ID del campo è necessario conoscere il tipo dichiarante, il nome del campo e la firma del [tipo JNI](#JNI_Type_Signatures) del campo.

1. Metodi *del valore del campo get.* Questi metodi richiedono l'handle di campo e sono responsabili della lettura del valore del campo da Java.
    Il metodo da utilizzare dipende dal tipo di campo.

1. Metodi del *valore del campo set.* Questi metodi richiedono l'handle di campo e sono responsabili della scrittura del valore del campo all'interno di Java. Il metodo da utilizzare dipende dal tipo di campo.

[I campi statici](#_Static_Fields) utilizzano i metodi `JNIEnv.GetStatic*Field` [JNIEnv.GetStaticFieldID](xref:Android.Runtime.JNIEnv.GetStaticMethodID*), e [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) .

 [I campi di istanza utilizzano](#_Instance_Fields) i `JNIEnv.Get*Field`metodi [JNIEnv.GetFieldID](xref:Android.Runtime.JNIEnv.GetFieldID*), , e [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) .

Ad esempio, la `JavaSystem.In` proprietà statica può essere implementata come:For example, the static property can be implemented as:

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

Nota: Stiamo usando [InputStreamInvoker.FromJniHandle](xref:Android.Runtime.InputStreamInvoker.FromJniHandle*) per convertire il `System.IO.Stream` riferimento JNI in `JniHandleOwnership.TransferLocalRef` un'istanza e stiamo usando perché [JNIEnv.GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*) restituisce un riferimento locale.

Molti dei tipi [Android.Runtime](xref:Android.Runtime) dispongono `FromJniHandle` di metodi che convertono un riferimento JNI nel tipo desiderato.

### <a name="method-binding"></a>Associazione del metodoMethod Binding

I metodi Java vengono esposti come metodi di C e come proprietà di C. Ad esempio, il metodo Java [java.lang.Runtime.runFinalizersOnExit](https://developer.android.com/reference/java/lang/Runtime.html#runFinalizersOnExit(boolean)) è associato come metodo [Java.Lang.Runtime.RunFinalizersOnExit](xref:Java.Lang.Runtime.RunFinalizersOnExit*) e il metodo [java.lang.Object.getClass](https://developer.android.com/reference/java/lang/Object.html#getClass) è associato come proprietà [Java.Lang.Object.Class](xref:Java.Lang.Object.Class) .

La chiamata al metodo è un processo in due passaggi:Method invocation is a two-step process:

1. *ID del metodo get* per il metodo da richiamare. Il metodo *get id* è responsabile della restituzione di un handle di metodo che verrà utilizzato dai metodi di chiamata del metodo. Per ottenere l'ID del metodo è necessario conoscere il tipo dichiarante, il nome del metodo e la firma del [tipo JNI](#JNI_Type_Signatures) del metodo.

1. Richiamare il metodo.

Come per i campi, i metodi da utilizzare per ottenere l'ID del metodo e richiamare il metodo differiscono tra metodi statici e metodi di istanza.

[I metodi statici](#_Static_Methods_1) utilizzano [JNIEnv.GetStaticMethodID()](xref:Android.Runtime.JNIEnv.GetStaticMethodID*) per `JNIEnv.CallStatic*Method` cercare l'ID del metodo e la famiglia di metodi per la chiamata.

[I metodi](#_Instance_Methods) di istanza utilizzano [JNIEnv.GetMethodID](xref:Android.Runtime.JNIEnv.GetMethodID*) `JNIEnv.Call*Method` per `JNIEnv.CallNonvirtual*Method` cercare l'ID del metodo e le famiglie di metodi e per la chiamata.

L'associazione di metodi è potenzialmente più di una semplice chiamata di metodo. L'associazione di metodi include anche la possibilità di eseguire l'override di un metodo (per i metodi astratti e non finali) o l'implementazione (per i metodi di interfaccia). La sezione [Supporto dell'ereditarietà, interfacce](#_Supporting_Inheritance,_Interfaces_1) illustra le complessità del supporto dei metodi virtuali e dei metodi di interfaccia.

<a name="_Static_Methods_1" />

#### <a name="static-methods"></a>Metodi statici

L'associazione di `JNIEnv.GetStaticMethodID` un metodo statico comporta l'utilizzo per ottenere un handle del metodo, quindi l'utilizzo del metodo appropriato, `JNIEnv.CallStatic*Method` a seconda del tipo restituito del metodo. Di seguito è riportato un esempio di associazione per il [metodo Runtime.getRuntime:The](https://developer.android.com/reference/java/lang/Runtime.html#getRuntime()) following is an example of a binding for the Runtime.getRuntime method:

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

Si noti che l'handle del `id_getRuntime`metodo viene archiviato in un campo statico, . Si tratta di un'ottimizzazione delle prestazioni, in modo che l'handle del metodo non debba essere cercato a ogni chiamata. Non è necessario memorizzare nella cache l'handle del metodo in questo modo. Una volta ottenuto l'handle del metodo, [JNIEnv.CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) viene utilizzato per richiamare il metodo. `JNIEnv.CallStaticObjectMethod`restituisce `IntPtr` un che contiene l'handle dell'istanza Java restituita.
[Java.Lang.Object.GetObject&lt;&gt;T (IntPtr, JniHandleOwnership)](xref:Java.Lang.Object.GetObject*) viene utilizzato per convertire l'handle Java in un'istanza di oggetto fortemente tipizzata.

#### <a name="non-virtual-instance-method-binding"></a>Associazione di metodi di istanza non virtuali

L'associazione di un `final` metodo di istanza o di `JNIEnv.GetMethodID` un metodo di istanza che `JNIEnv.Call*Method` non richiede l'override comporta l'utilizzo per ottenere un handle del metodo, quindi l'utilizzo del metodo appropriato, a seconda del tipo restituito del metodo. Di seguito è riportato un `Object.Class` esempio di associazione per la proprietà:The following is an example of a binding for the property:

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

Si noti che l'handle del `id_getClass`metodo viene archiviato in un campo statico, .
Si tratta di un'ottimizzazione delle prestazioni, in modo che l'handle del metodo non debba essere cercato a ogni chiamata. Non è necessario memorizzare nella cache l'handle del metodo in questo modo. Una volta ottenuto l'handle del metodo, [JNIEnv.CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) viene utilizzato per richiamare il metodo. `JNIEnv.CallStaticObjectMethod`restituisce `IntPtr` un che contiene l'handle dell'istanza Java restituita.
[Java.Lang.Object.GetObject&lt;&gt;T (IntPtr, JniHandleOwnership)](xref:Java.Lang.Object.GetObject*) viene utilizzato per convertire l'handle Java in un'istanza di oggetto fortemente tipizzata.

### <a name="binding-constructors"></a>Costruttori di associazioneBinding Constructors

I costruttori sono metodi `"<init>"`Java con il nome . Come con i metodi `JNIEnv.GetMethodID` di istanza Java, viene utilizzato per cercare l'handle del costruttore. A differenza dei metodi Java, i metodi [JNIEnv.NewObject](xref:Android.Runtime.JNIEnv.NewObject*) vengono utilizzati per richiamare l'handle del metodo del costruttore. Il valore `JNIEnv.NewObject` restituito di è un riferimento locale JNI:The return value of is a JNI local reference:

```csharp
int value = 42;
IntPtr class_ref    = JNIEnv.FindClass ("java/lang/Integer");
IntPtr id_ctor_I    = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
IntPtr lrefInstance = JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value));
// Dispose of lrefInstance, class_ref…
```

In genere un'associazione di classe farà creare una sottoclasse [Java.Lang.Object](xref:Java.Lang.Object).
Quando si `Java.Lang.Object`crea una sottoclasse , `Java.Lang.Object` entra in gioco una semantica aggiuntiva: un'istanza mantiene un riferimento globale a un'istanza Java tramite la `Java.Lang.Object.Handle` proprietà .

1. Il `Java.Lang.Object` costruttore predefinito allocherà un'istanza Java.The default constructor will allocate a Java instance.

1. Se il tipo `RegisterAttribute` dispone `RegisterAttribute.DoNotGenerateAcw` `true` di un e `RegisterAttribute.Name` è , un'istanza del tipo viene creata tramite il costruttore predefinito.

1. In caso contrario, il [Android Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) corrispondente a `this.GetType` viene creata un'istanza tramite il costruttore predefinito. I wrapper chiamabili Android vengono generati `Java.Lang.Object` durante la `RegisterAttribute.DoNotGenerateAcw` creazione del `true`pacchetto per ogni sottoclasse per la quale non è impostata su .

Per i tipi che non sono associazioni di classe, questa `Mono.Samples.HelloWorld.HelloAndroid` è la semantica `mono.samples.helloworld.HelloAndroid` prevista: la creazione di un'istanza di C , è necessario costruire un'istanza Java che è un wrapper richiamabile Android generato.

Per le associazioni di classe, questo può essere il comportamento corretto se il tipo Java contiene un costruttore predefinito e/o nessun altro costruttore deve essere richiamato. In caso contrario, è necessario fornire un costruttore che esegue le azioni seguenti:Otherwise, a constructor must be provided which performs the following actions:

1. Richiamare [Java.Lang.Object(IntPtr, JniHandleOwnership)](xref:Java.Lang.Object#ctor*) anziché `Java.Lang.Object` il costruttore predefinito. Ciò è necessario per evitare la creazione di una nuova istanza Java.This is needed to avoid creating a new Java instance.

1. Controllare il valore di [Java.Lang.Object.Handle](xref:Java.Lang.Object.Handle) prima di creare qualsiasi istanza Java. La `Object.Handle` proprietà avrà un `IntPtr.Zero` valore diverso da se un Android Callable Wrapper è stato costruito nel codice Java e l'associazione di classe viene costruita per contenere l'istanza di Android Callable Wrapper creata. Ad esempio, quando `mono.samples.helloworld.HelloAndroid` Android crea un'istanza, il wrapper richiamabile `HelloAndroid` Android verrà creato per `Mono.Samples.HelloWorld.HelloAndroid` primo e `Object.Handle` il costruttore Java creerà un'istanza del tipo corrispondente, con la proprietà impostata sull'istanza Java prima dell'esecuzione del costruttore.

1. Se il tipo di runtime corrente non corrisponde al tipo dichiarante, è necessario creare un'istanza del corrispondente Android Callable Wrapper e utilizzare [Object.SetHandle](xref:Java.Lang.Object.SetHandle*) per archiviare l'handle restituito da [JNIEnv.CreateInstance](xref:Android.Runtime.JNIEnv.CreateInstance*).

1. Se il tipo di runtime corrente è uguale al tipo dichiarante, richiamare il costruttore `JNIEnv.NewInstance` Java e utilizzare [Object.SetHandle](xref:Java.Lang.Object.SetHandle*) per archiviare l'handle restituito da .

Si consideri, ad esempio, il costruttore [java.lang.Integer(int).](https://developer.android.com/reference/java/lang/Integer.html#Integer(int)) Questo è legato come:

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

I metodi [JNIEnv.CreateInstance](xref:Android.Runtime.JNIEnv.CreateInstance*) sono helper `JNIEnv.FindClass` `JNIEnv.GetMethodID`per `JNIEnv.NewObject`eseguire `JNIEnv.DeleteGlobalReference` un' esecuzione `JNIEnv.FindClass`di , , e sul valore restituito da . Per informazioni dettagliate, vedere la sezione che segue.

<a name="_Supporting_Inheritance,_Interfaces_1" />

### <a name="supporting-inheritance-interfaces"></a>Supporto dell'ereditarietà, interfacceSupporting Inheritance, Interfaces

La creazione di una sottoclasse di un tipo Java o l'implementazione di un'interfaccia Java richiede la generazione di [Android Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) generati per ogni `Java.Lang.Object` sottoclasse durante il processo di creazione del pacchetto. La generazione ACW viene controllata tramite l'attributo personalizzato Android.Runtime.RegisterAttribute.ACW generation is controlled through the [Android.Runtime.RegisterAttribute](xref:Android.Runtime.RegisterAttribute) custom attribute.

Per i tipi `[Register]` di C, il costruttore dell'attributo personalizzato richiede un argomento: il riferimento al [tipo semplificato JNI](#_Simplified_Type_References_1) per il tipo Java corrispondente. In questo modo è possibile fornire nomi diversi tra Java e C .

Prima di Xamarin.Android 4.0, l'attributo `[Register]` personalizzato non era disponibile per i tipi Java esistenti "alias". Ciò è dovuto al fatto che il processo `Java.Lang.Object` di generazione ACW genererebbe ACW per ogni sottoclasse rilevata.

In Xamarin.Android 4.0 è stata introdotta la proprietà [RegisterAttribute.DoNotGenerateAcw.](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw) Questa proprietà indica al processo di generazione ACW di *ignorare* il tipo annotato, consentendo la dichiarazione di nuovi Managed Callable Wrapper che non commetteranno la generazione di ACW al momento della creazione del pacchetto. Ciò consente l'associazione di tipi Java esistenti. Si consideri, ad esempio, `Adder`la seguente classe `add`Java semplice, , che contiene un metodo, , che aggiunge ai numeri interi e restituisce il risultato:

```java
package mono.android.test;
public class Adder {
    public int add (int a, int b) {
        return a + b;
    }
}
```

Il `Adder` tipo può essere associato come:

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

In questo `Adder` caso, il tipo `Adder` di c'è *aliasing* il tipo Java. L'attributo `[Register]` viene utilizzato per specificare il nome JNI del `mono.android.test.Adder` tipo Java e la `DoNotGenerateAcw` proprietà viene utilizzata per inibire la generazione ACW. Ciò comporterà la generazione di un `ManagedAdder` ACW per `mono.android.test.Adder` il tipo, che sottoclassi correttamente il tipo. Se `RegisterAttribute.DoNotGenerateAcw` la proprietà non fosse stata utilizzata, il processo di compilazione `mono.android.test.Adder` Xamarin.Android avrebbe generato un nuovo tipo Java. Ciò comporterebbe errori `mono.android.test.Adder` di compilazione, poiché il tipo sarebbe presente due volte, in due file separati.

### <a name="binding-virtual-methods"></a>Associazione di metodi virtualiBinding Virtual Methods

`ManagedAdder`sottoclassi di `Adder` Java tipo, ma non è particolarmente `Adder` interessante: il tipo di `ManagedAdder` c'è non definisce alcun metodo virtuale, quindi non può eseguire l'override di nulla.

I `virtual` metodi di associazione per consentire l'override da parte delle sottoclassi richiede diversi elementi che devono essere eseguiti che rientrano nelle due categorie seguenti:Binding methods to permit overriding by subclasses requires several things that need to be done which fall into the following two categories:

1. **Associazione del metodoMethod Binding**

1. **Metodo di registrazione**

#### <a name="method-binding"></a>Associazione del metodoMethod Binding

L'associazione di un metodo richiede l'aggiunta `ThresholdType`di `ThresholdClass`due membri di supporto alla definizione di C: `Adder` , e .

##### <a name="thresholdtype"></a>ThresholdType

La `ThresholdType` proprietà restituisce il tipo corrente dell'associazione:

```csharp
partial class Adder {
    protected override System.Type ThresholdType {
        get {
            return typeof (Adder);
        }
    }
}
```

`ThresholdType`viene utilizzato nell'associazione di metodo per determinare quando deve eseguire l'invio di metodi virtuali e non virtuali. Deve sempre restituire `System.Type` un'istanza che corrisponde al tipo di dichiarazione di C .

##### <a name="thresholdclass"></a>Classe Threshold

La `ThresholdClass` proprietà restituisce il riferimento alla classe JNI per il tipo associato:

```csharp
partial class Adder {
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```

`ThresholdClass`viene utilizzato nell'associazione di metodo quando si richiamano metodi non virtuali.

#### <a name="binding-implementation"></a>Implementazione di associazioneBinding Implementation

L'implementazione dell'associazione del metodo è responsabile della chiamata di runtime del metodo Java. Contiene inoltre `[Register]` una dichiarazione di attributo personalizzato che fa parte della registrazione del metodo e verrà descritta nella sezione Registrazione del metodo:It also contains a custom attribute declaration that is part of the method registration, and will be discussed in the Method Registration section:

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

Il `id_add` campo contiene l'ID del metodo Java da richiamare. Il `id_add` valore viene `JNIEnv.GetMethodID`ottenuto da ,`class_ref`che richiede la`"add"`classe dichiarante ( ), il`"(II)I"`nome del metodo Java ( ) e la firma JNI del metodo ( ).

Una volta ottenuto `GetType` l'ID `ThresholdType` metodo, viene confrontato con determinare se è necessario un invio virtuale o non virtuale. L'invio virtuale `GetType` `ThresholdType`è `Handle` necessario quando corrisponde, come può fare riferimento a una sottoclasse allocata da Java che esegue l'override del metodo.

Quando `GetType` non corrisponde `ThresholdType` `Adder` a , è stata sottoclassata (ad esempio da `ManagedAdder`) e l'implementazione `Adder.Add` verrà richiamata solo se la sottoclasse è stata richiamata. `base.Add` Questo è il caso di spedizione `ThresholdClass` non virtuale, che è dove entra in gioco. `ThresholdClass`specifica quale classe Java fornirà l'implementazione del metodo da richiamare.

#### <a name="method-registration"></a>Metodo di registrazione

Si supponga di `ManagedAdder` disporre di `Adder.Add` una definizione aggiornata che esegue l'override del metodo:

```csharp
partial class ManagedAdder : Adder {
    public override int Add (int a, int b) {
        return (a*2) + (b*2);
    }
}
```

Tenere `Adder.Add` presente `[Register]` che aveva un attributo personalizzato:Re that had a custom attribute:

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
```

Il `[Register]` costruttore dell'attributo personalizzato accetta tre valori:The custom attribute constructor accepts three values:

1. Il nome del metodo `"add"` Java, in questo caso.

1. La firma di tipo JNI del metodo, `"(II)I"` in questo caso.

1. In questo `GetAddHandler` caso, il metodo del *connettore.*
    I metodi del connettore verranno illustrati più avanti.

I primi due parametri consentono al processo di generazione ACW di generare una dichiarazione di metodo per eseguire l'override del metodo. L'ACW risultante conterrà parte del codice seguente:

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

Si noti che viene dichiarato `@Override` `n_`un metodo, che delega a un metodo con prefisso con lo stesso nome. In questo modo, quando `ManagedAdder.add` `ManagedAdder.n_add` il codice Java richiama , verrà richiamato, che consentirà l'esecuzione del metodo di override di C. `ManagedAdder.Add`

Così, la domanda più `ManagedAdder.n_add` importante: come `ManagedAdder.Add`è collegato a ?

I `native` metodi Java vengono registrati con il runtime Java (runtime Android) tramite la [funzione JNI RegisterNatives](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp17734).
`RegisterNatives`accetta una matrice di strutture contenente il nome del metodo Java, la firma di tipo JNI e un puntatore a funzione da richiamare che segue la convenzione di [chiamata JNI](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/design.html#wp715).
Il puntatore a funzione deve essere una funzione che accetta due argomenti del puntatore seguiti dai parametri del metodo. Il `ManagedAdder.n_add` metodo Java deve essere implementato tramite una funzione con il seguente prototipo C:

```csharp
int FunctionName(JNIEnv *env, jobject this, int a, int b)
```

Xamarin.Android non espone `RegisterNatives` un metodo. Al contrario, ACW e MCW forniscono `RegisterNatives`insieme le informazioni necessarie per richiamare : l'ACW contiene il nome del metodo e la firma del tipo JNI, l'unica cosa che manca è un puntatore a funzione da collegare.

È qui che entra in gioco il metodo del *connettore.* Il `[Register]` terzo parametro di attributo personalizzato è il nome di un metodo definito nel tipo `System.Delegate`registrato o una classe base del tipo registrato che non accetta parametri e restituisce un oggetto . L'oggetto `System.Delegate` restituito a sua volta fa riferimento a un metodo con la firma della funzione JNI corretta. Infine, il delegato restituito dal metodo del connettore *deve* essere radicato in modo che il Garbage Collector non lo raccolga, poiché il delegato viene fornito a Java.

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

Il `GetAddHandler` metodo `Func<IntPtr, IntPtr, int, int,
int>` crea un delegato `n_Add` che fa riferimento al metodo , quindi richiama [JNINativeWrapper.CreateDelegate](xref:Android.Runtime.JNINativeWrapper.CreateDelegate*).
`JNINativeWrapper.CreateDelegate`esegue il wrapping del metodo fornito in un blocco try/catch, in modo che tutte le eccezioni non gestite vengano gestite e genererà l'evento [AndroidEvent.UnhandledExceptionRaiser.](xref:Android.Runtime.AndroidEnvironment.UnhandledExceptionRaiser) Il delegato risultante viene `cb_add` archiviato nella variabile statica in modo che il garbage collector non liberi il delegato.

Infine, `n_Add` il metodo è responsabile del marshalling dei parametri JNI ai tipi gestiti corrispondenti, quindi della delega della chiamata al metodo.

Nota: `JniHandleOwnership.DoNotTransfer` utilizzare sempre quando si ottiene un MCW su un'istanza Java. Trattandoli come un riferimento locale `JNIEnv.DeleteLocalRef`(e quindi&gt; chiamando&gt; ) interromperà le transizioni dello stack gestito, Java.

### <a name="complete-adder-binding"></a>Completa associazione Adder

L'associazione gestita completa per il tipo è:The complete managed binding for the `mono.android.tests.Adder` type is:

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

Quando si scrive un tipo che corrisponda ai criteri seguenti:

1. Sottoclassi`Java.Lang.Object`

1. Ha `[Register]` un attributo personalizzato

1. `RegisterAttribute.DoNotGenerateAcw` è `true`

Quindi per l'interazione GC il tipo non `Java.Lang.Object` `Java.Lang.Object` *deve* avere campi che possono fare riferimento a una o sottoclasse in fase di esecuzione. Ad esempio, i `System.Object` campi di tipo e qualsiasi tipo di interfaccia non sono consentiti. Sono consentiti `Java.Lang.Object` tipi che non `System.String` possono `List<int>`fare riferimento a istanze, ad esempio e . Questa restrizione consente di impedire la raccolta di oggetti non maturi da parte del catalogo globale.

Se il tipo deve contenere un `Java.Lang.Object` campo di istanza che `System.WeakReference` può `GCHandle`fare riferimento a un'istanza, il tipo di campo deve essere o .

## <a name="binding-abstract-methods"></a>Associazione di metodi astrattiBinding Abstract Methods

I `abstract` metodi di associazione sono in gran parte identici all'associazione di metodi virtuali. Ci sono solo due differenze:

1. Il metodo astratto è astratto. Mantiene comunque `[Register]` l'attributo e la registrazione del metodo `Invoker` associato, l'associazione al metodo viene appena spostata nel tipo.

1. Viene creato `abstract` `Invoker` un tipo non di tipo che sottoclassi il tipo astratto. Il `Invoker` tipo deve eseguire l'override di tutti i metodi astratti dichiarati nella classe base e l'implementazione sottoposta a override è l'implementazione dell'associazione di metodi, anche se il caso di invio non virtuale può essere ignorato.

Si supponga, ad `mono.android.test.Adder.add` esempio, `abstract`che il metodo precedente sia . L'associazione di C `Adder.Add` , cambierebbe `AdderInvoker` in modo che `Adder.Add`fossero astratti e sarebbe stato definito un nuovo tipo che implementava :

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

Il `Invoker` tipo è necessario solo quando si ottengono riferimenti JNI a istanze create da Java.

## <a name="binding-interfaces"></a>Interfacce di associazioneBinding Interfaces

Le interfacce di associazione sono concettualmente simili all'associazione di classi contenenti metodi virtuali, ma molte delle specifiche differiscono in modi sottili (e non così sottili). Si consideri la seguente [dichiarazione](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/Adder.java#L14)di interfaccia Java :

```csharp
public interface Progress {
    void onAdd(int[] values, int currentIndex, int currentSum);
}
```

Le associazioni di interfaccia sono due parti: la definizione dell'interfaccia di C , e una definizione di Invoker per l'interfaccia.

### <a name="interface-definition"></a>Definizione dell'interfaccia

È necessario che la definizione dell'interfaccia di C'è soddisfare i requisiti seguenti:The C's interface definition must fulfill the following requirements:

- La definizione dell'interfaccia deve avere un `[Register]` attributo personalizzato.

- La definizione dell'interfaccia deve estendere l'oggetto `IJavaObject interface`.
    In caso contrario, gli ACW non potranno più ereditare dall'interfaccia Java.

- Ogni metodo di `[Register]` interfaccia deve contenere un attributo che specifica il nome del metodo Java corrispondente, la firma JNI e il metodo del connettore.

- Il metodo del connettore deve inoltre specificare il tipo in cui è possibile posizionare il metodo del connettore.

Quando `abstract` si `virtual` associano e metodi, il metodo del connettore viene cercato all'interno della gerarchia di ereditarietà del tipo registrato. Le interfacce non possono avere metodi contenenti corpi, pertanto questo non funziona, pertanto il requisito che venga specificato un tipo che indica dove si trova il metodo del connettore. Il tipo viene specificato all'interno della `':'`stringa del metodo del connettore, dopo i due punti , e deve essere il nome del tipo completo dell'assembly del tipo contenente l'invoker.

Le dichiarazioni dei metodi di interfaccia sono una conversione del metodo Java corrispondente utilizzando tipi *compatibili.* Per i tipi incorporati Java, i tipi compatibili sono i corrispondenti tipi di C, ad esempio Java `int` è in `int`C. Per i tipi di riferimento, il tipo compatibile è un tipo che può fornire un handle JNI del tipo Java appropriato.

I membri di interfaccia non verranno richiamati direttamente dalla chiamata &ndash; &ndash; Java verranno mediati tramite il tipo Invoker in modo che sia consentita una certa flessibilità.

L'interfaccia di Java Progress può essere [dichiarata in C , come](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L83):

```csharp
[Register ("mono/android/test/Adder$Progress", DoNotGenerateAcw=true)]
public interface IAdderProgress : IJavaObject {
    [Register ("onAdd", "([III)V",
            "GetOnAddHandler:Mono.Samples.SanityTests.IAdderProgressInvoker, SanityTests, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null")]
    void OnAdd (JavaArray<int> values, int currentIndex, int currentSum);
}
```

Si noti che nell'esempio precedente viene eseguito il mapping del parametro Java `int[]` a un oggetto [JavaArray&lt;int&gt;](xref:Android.Runtime.JavaArray`1).
Questo non è necessario: avremmo potuto associarlo a un c, `int[]`o un `IList<int>`, o qualcos'altro del tutto. Qualunque sia il `Invoker` tipo scelto, deve essere `int[]` in grado di tradurlo in un tipo Java per la chiamata.

### <a name="invoker-definition"></a>Definizione Invoker

La `Invoker` definizione `Java.Lang.Object`del tipo deve ereditare , implementare l'interfaccia appropriata e fornire tutti i metodi di connessione a cui si fa riferimento nella definizione dell'interfaccia. Esiste un altro suggerimento diverso da un'associazione di classe: gli `class_ref` ID campo e metodo devono essere membri di istanza, non membri statici.

Il motivo per preferire i `JNIEnv.GetMethodID` membri di istanza ha a che fare con il comportamento nel runtime Android.The reason for preferreferring instance members has to do with behavior in the Android runtime. (Questo può essere anche il comportamento Java; non è stato testato.) `JNIEnv.GetMethodID` restituisce null quando si cerca un metodo che proviene da un'interfaccia implementata e non l'interfaccia dichiarata. Si consideri l'interfaccia [java.util.SortedMap&lt;K, V&gt; ](https://developer.android.com/reference/java/util/SortedMap.html) Java, che implementa l'interfaccia [java.util.Map&lt;K, V.&gt; ](https://developer.android.com/reference/java/util/Map.html) Map fornisce un metodo [chiaro,](https://developer.android.com/reference/java/util/Map.html#clear()) `Invoker` quindi una definizione apparentemente ragionevole per SortedMap sarebbe:

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

Quanto sopra avrà esito negativo `JNIEnv.GetMethodID` perché restituirà `null` durante la ricerca del `Map.clear` metodo tramite l'istanza della `SortedMap` classe.

Esistono due soluzioni: tenere traccia dell'interfaccia da `class_ref` cui proviene ogni metodo e avere un per ogni interfaccia oppure mantenere tutto come membri di istanza ed eseguire la ricerca del metodo sul tipo di classe più derivato, non sul tipo di interfaccia. Quest'ultimo è fatto in **Mono.Android.dll**.

La definizione di Invoker ha `Dispose` sei sezioni: il costruttore, il metodo, i `ThresholdType` membri e `ThresholdClass` , il metodo, l'implementazione del `GetObject` metodo di interfaccia e l'implementazione del metodo del connettore.

#### <a name="constructor"></a>Costruttore

Il costruttore deve cercare la classe di runtime dell'istanza richiamata e archiviare la classe di runtime nel campo dell'istanza: `class_ref`

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

Nota: `Handle` la proprietà deve essere utilizzata all'interno del corpo del costruttore e non nel `handle` parametro, come in Android v4.0 il `handle` parametro potrebbe non essere valido al termine dell'esecuzione del costruttore di base.

#### <a name="dispose-method"></a>Metodo Dispose

Il `Dispose` metodo deve liberare il riferimento globale allocato nel costruttore:The method needs to free the global reference allocated in the constructor:

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

I `ThresholdType` `ThresholdClass` membri e sono identici a quelli presenti in un'associazione di classe:The and members are identical to what is found in a class binding:

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

È `GetObject` necessario un metodo statico per supportare [Extensions.JavaCast&lt;&gt;T ()](xref:Android.Runtime.Extensions.JavaCast*):

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

#### <a name="connector-methods"></a>Metodi Connector

I metodi del connettore e l'infrastruttura di supporto sono responsabili del marshalling dei parametri JNI per i tipi C. Il `int[]` parametro Java verrà passato `jintArray`come JNI , che è un'area `IntPtr` di c'è. È `IntPtr` necessario eseguire il `JavaArray<int>` marshalling in un in modo da supportare la chiamata dell'interfaccia di C :

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

Se `int[]` si preferisce rispetto `JavaList<int>`a , è possibile utilizzare [JNIEnv.GetArray():](xref:Android.Runtime.JNIEnv.GetArray*)

```csharp
int[] _values = (int[]) JNIEnv.GetArray(values, JniHandleOwnership.DoNotTransfer, typeof (int));
```

Si noti, `JNIEnv.GetArray` tuttavia, che copia l'intero array tra macchine virtuali, pertanto per gli array di grandi dimensioni ciò potrebbe comportare una notevole pressione GC aggiuntiva.

### <a name="complete-invoker-definition"></a>Completare la definizione di InvokerComplete Invoker Definition

La [definizione completa di IAdderProgressInvoker](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L88):

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

## <a name="jni-object-references"></a>Riferimenti agli oggetti JNI

Molti metodi JNIEnv restituiscono *riferimenti*a `GCHandle`oggetti *JNI* , che sono simili a s. JNI fornisce tre diversi tipi di riferimenti a oggetti: riferimenti locali, riferimenti globali e riferimenti globali deboli. Tutti e tre `System.IntPtr`sono rappresentati come , *ma* (come `IntPtr`per la `JNIEnv` sezione Tipi di funzione JNI) non tutti i metodi restituiti dai metodi sono riferimenti . Ad esempio, [JNIEnv.GetMethodID](xref:Android.Runtime.JNIEnv.GetMethodID*) restituisce un `IntPtr`valore , ma non `jmethodID`restituisce un riferimento a un oggetto, ma restituisce un oggetto . Consultare la documentazione della [funzione JNI](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html) per i dettagli.

I riferimenti locali vengono creati dalla *maggior parte* dei metodi di creazione dei riferimenti.
Android consente solo un numero limitato di riferimenti locali di esistere in un determinato momento, in genere 512. I riferimenti locali possono essere eliminati tramite [JNIEnv.DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*).
A differenza di JNI, non tutti i metodi JNIEnv di riferimento che restituiscono riferimenti a oggetti restituiscono riferimenti locali; [JNIEnv.FindClass](xref:Android.Runtime.JNIEnv.FindClass*) restituisce un riferimento *globale.* È consigliabile eliminare i riferimenti locali il più rapidamente possibile, possibilmente costruendo un oggetto `JniHandleOwnership.TransferLocalRef` [Java.Lang.Object](xref:Java.Lang.Object) intorno all'oggetto e specificando il costruttore [Java.Lang.Object(IntPtr handle, JniHandleOwnership transfer).](xref:Java.Lang.Object#ctor*)

I riferimenti globali vengono creati da [JNIEnv.NewGlobalRef](xref:Android.Runtime.JNIEnv.NewGlobalRef*) e [JNIEnv.FindClass](xref:Android.Runtime.JNIEnv.FindClass*).
Possono essere distrutti con [JNIEnv.DeleteGlobalRef](xref:Android.Runtime.JNIEnv.DeleteGlobalRef*).
Gli emulatori hanno un limite di 2.000 riferimenti globali in sospeso, mentre i dispositivi hardware hanno un limite di circa 52.000 riferimenti globali.

I riferimenti globali deboli sono disponibili solo su Android v2.2 (Froyo) e versioni successive. I riferimenti globali deboli possono essere eliminati con [JNIEnv.DeleteWeakGlobalRef](xref:Android.Runtime.JNIEnv.DeleteWeakGlobalRef*).

### <a name="dealing-with-jni-local-references"></a>Gestione dei riferimenti locali JNI

I metodi [JNIEnv.GetObjectField](xref:Android.Runtime.JNIEnv.GetObjectField*), [JNIEnv.GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*), [JNIEnv.CallObjectMethod](xref:Android.Runtime.JNIEnv.CallObjectMethod*), [JNIEnv.CallNonvirtualObjectMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualObjectMethod*) e `IntPtr` [JNIEnv.CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) restituiscono un `IntPtr.Zero` oggetto che `null`contiene un riferimento locale JNI a un oggetto Java o se Java ha restituito . A causa del numero limitato di riferimenti locali che possono essere in sospeso contemporaneamente (512 voci), è opportuno assicurarsi che i riferimenti vengano eliminati in modo tempestivo. Esistono tre modi in cui è possibile gestire i `Java.Lang.Object` riferimenti locali: eliminarli `Java.Lang.Object.GetObject<T>()` in modo esplicito, creare un'istanza per conservarli e utilizzare per creare un wrapper richiamabile gestito intorno a essi.

### <a name="explicitly-deleting-local-references"></a>Eliminazione esplicita di riferimenti localiExplicitly Deleting Local References

[JNIEnv.DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*) viene utilizzato per eliminare i riferimenti locali. Una volta che il riferimento locale è stato eliminato, non può `JNIEnv.DeleteLocalRef` più essere utilizzato, quindi è necessario prestare attenzione per garantire che sia l'ultima cosa fatta con il riferimento locale.

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
try {
    // Do something with `lref`
}
finally {
    JNIEnv.DeleteLocalRef (lref);
}
```

### <a name="wrapping-with-javalangobject"></a>Ritorno a capo con Java.Lang.Object

`Java.Lang.Object`fornisce un costruttore [Java.Lang.Object(IntPtr handle, JniHandleOwnership transfer)](xref:Java.Lang.Object#ctor*) che può essere utilizzato per eseguire il wrapping di un riferimento JNI in uscita. Il parametro [JniHandleOwnership](xref:Android.Runtime.JniHandleOwnership) determina la modalità di trattamento del `IntPtr` parametro:

- [JniHandleOwnership.DoNotTransfer](xref:Android.Runtime.JniHandleOwnership.DoNotTransfer) &ndash; L'istanza creata `Java.Lang.Object` creerà un `handle` nuovo `handle` riferimento globale dal parametro e rimarrà invariata.
    Il chiamante è `handle` responsabile della liberazione di , se necessario.

- [JniHandleOwnership.TransferLocalRef](xref:Android.Runtime.JniHandleOwnership.TransferLocalRef) &ndash; `Java.Lang.Object` L'istanza creata creerà un `handle` nuovo `handle` riferimento globale dal parametro e verrà eliminata con [JNIEnv.DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*) . Il chiamante `handle` non deve liberare e non deve utilizzare `handle` al termine dell'esecuzione del costruttore.

- [JniHandleOwnership.TransferGlobalRef](xref:Android.Runtime.JniHandleOwnership.TransferLocalRef) &ndash; L'istanza creata `Java.Lang.Object` assumerà `handle` la proprietà del parametro. Il chiamante `handle` non deve liberare .

Poiché i metodi di chiamata del `JniHandleOwnership.TransferLocalRef` metodo JNI restituiscono riferimenti locali, in genere vengono utilizzati:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef);
```

Il riferimento globale creato non verrà `Java.Lang.Object` liberato fino a quando l'istanza non viene sottoposta a Garbage Collection. Se è possibile, l'eliminazione dell'istanza libererà il riferimento globale, velocizzando le operazioni di Garbage Collection:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
using (var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef)) {
    // use value ...
}
```

### <a name="using-javalangobjectgetobjectlttgt"></a>Utilizzo di Java.Lang.Object.GetObject&lt;T&gt;()

`Java.Lang.Object`fornisce un metodo [Java.Lang.Object.GetObject&lt;&gt;T (handle IntPtr, trasferimento JniHandleOwnership)](xref:Java.Lang.Object.GetObject*) che può essere utilizzato per creare un wrapper richiamabile gestito del tipo specificato.

Il `T` tipo deve soddisfare i seguenti requisiti:

1. `T`deve essere un tipo di riferimento.

1. `T` deve implementare l'interfaccia `IJavaObject`.

1. Se `T` non è una classe `T` o un'interfaccia astratta, è necessario fornire un costruttore con i tipi di `(IntPtr,
    JniHandleOwnership)` parametro .

1. Se `T` è una classe astratta o un'interfaccia, `T` *deve* essere disponibile un *invoker* per . Un invoker è un tipo non `T` astratto che eredita o implementa `T` e ha lo stesso nome di `T` un suffisso Invoker. Ad esempio, se T `Java.Lang.IRunnable` è l'interfaccia , il `Java.Lang.IRunnableInvoker` `(IntPtr,
    JniHandleOwnership)` tipo deve esistere e deve contenere il costruttore richiesto.

Poiché i metodi di chiamata del `JniHandleOwnership.TransferLocalRef` metodo JNI restituiscono riferimenti locali, in genere vengono utilizzati:

```csharp
IntPtr lrefString = JNIEnv.CallObjectMethod(instance, methodID);
Java.Lang.String value = Java.Lang.Object.GetObject<Java.Lang.String>( lrefString, JniHandleOwnership.TransferLocalRef);
```

<a name="_Looking_up_Java_Types" />

## <a name="looking-up-java-types"></a>Ricerca di tipi Java

Per cercare un campo o un metodo in JNI, il tipo dichiarante per il campo o il metodo deve essere cercato prima. Il metodo [Android.Runtime.JNIEnv.FindClass(string)](xref:Android.Runtime.JNIEnv.FindClass*)) viene utilizzato per cercare tipi Java. Il parametro string è il *riferimento al tipo semplificato* o il *riferimento al tipo completo* per il tipo Java. Vedere la [sezione Riferimenti ai](#_JNI_Type_References) tipi JNI per informazioni dettagliate sui riferimenti ai tipi semplificati e completi.

Nota: A `JNIEnv` differenza di ogni `FindClass` altro metodo che restituisce istanze dell'oggetto, restituisce un riferimento globale, non un riferimento locale.

<a name="_Instance_Fields" />

## <a name="instance-fields"></a>Campi di istanza

I campi vengono manipolati tramite *gli ID di campo.* Gli ID campo vengono ottenuti tramite [JNIEnv.GetFieldID](xref:Android.Runtime.JNIEnv.GetFieldID*), che richiede la classe in cui è definito il campo, il nome del campo e la firma del campo di [tipo JNI.](#JNI_Type_Signatures)

Gli ID di campo non devono essere liberati e sono validi finché viene caricato il tipo Java corrispondente. (Android non supporta attualmente lo scaricamento della classe.)

Esistono due set di metodi per la modifica dei campi di istanza: uno per la lettura dei campi di istanza e uno per la scrittura di campi di istanza. Tutti i set di metodi richiedono un ID di campo per leggere o scrivere il valore del campo.

### <a name="reading-instance-field-values"></a>Lettura dei valori dei campi dell'istanzaReading I Field Values

Il set di metodi per la lettura dei valori dei campi di istanza segue il modello di denominazione:The set of methods for reading instance field values follows the naming pattern:

```csharp
* JNIEnv.Get*Field(IntPtr instance, IntPtr fieldID);
```

dove `*` è il tipo di campo:

- [JNIEnv.GetObjectField](xref:Android.Runtime.JNIEnv.GetObjectField*) &ndash; Legge il valore di qualsiasi campo di istanza che `java.lang.Object` non sia un tipo incorporato, ad esempio , matrici e tipi di interfaccia. Il valore restituito è un riferimento locale JNI.

- [JNIEnv.GetBooleanField](xref:Android.Runtime.JNIEnv.GetBooleanField*) &ndash; Legge il `bool` valore dei campi di istanza.

- [JNIEnv.GetByteField](xref:Android.Runtime.JNIEnv.GetByteField*) &ndash; Legge il `sbyte` valore dei campi di istanza.

- [JNIEnv.GetCharField](xref:Android.Runtime.JNIEnv.GetCharField*) &ndash; Legge il `char` valore dei campi di istanza.

- [JNIEnv.GetShortField](xref:Android.Runtime.JNIEnv.GetShortField*) &ndash; Legge il `short` valore dei campi di istanza.

- [JNIEnv.GetIntField](xref:Android.Runtime.JNIEnv.GetIntField*) &ndash; Legge il `int` valore dei campi di istanza.

- [JNIEnv.GetLongField](xref:Android.Runtime.JNIEnv.GetLongField*) &ndash; Legge il `long` valore dei campi di istanza.

- [JNIEnv.GetFloatField](xref:Android.Runtime.JNIEnv.GetFloatField*) &ndash; Legge il `float` valore dei campi di istanza.

- [JNIEnv.GetDoubleField](xref:Android.Runtime.JNIEnv.GetDoubleField*) &ndash; Legge il `double` valore dei campi di istanza.

### <a name="writing-instance-field-values"></a>Scrittura di valori di campo di istanzaWriting Instance Field Values

Il set di metodi per la scrittura dei valori dei campi di istanza segue il modello di denominazione:The set of methods for writing instance field values follows the naming pattern:

```csharp
JNIEnv.SetField(IntPtr instance, IntPtr fieldID, Type value);
```

dove *Tipo* è il tipo del campo:

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Scrivere il valore di qualsiasi campo che non `java.lang.Object` sia un tipo incorporato, ad esempio , matrici e tipi di interfaccia. Il `IntPtr` valore può essere un riferimento locale JNI, un riferimento `IntPtr.Zero` globale `null` JNI, un riferimento globale debole JNI o (per ).

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Scrivere `bool` il valore dei campi di istanza.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Scrivere `sbyte` il valore dei campi di istanza.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Scrivere `char` il valore dei campi di istanza.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Scrivere `short` il valore dei campi di istanza.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Scrivere `int` il valore dei campi di istanza.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Scrivere `long` il valore dei campi di istanza.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Scrivere `float` il valore dei campi di istanza.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Scrivere `double` il valore dei campi di istanza.

<a name="_Static_Fields" />

## <a name="static-fields"></a>Campi statici

I campi statici vengono manipolati tramite *gli ID di campo.* Gli ID campo vengono ottenuti tramite [JNIEnv.GetStaticFieldID](xref:Android.Runtime.JNIEnv.GetStaticFieldID*), che richiede la classe in cui è definito il campo, il nome del campo e la firma del tipo [JNI](#JNI_Type_Signatures) del campo.

Gli ID di campo non devono essere liberati e sono validi finché viene caricato il tipo Java corrispondente. (Android non supporta attualmente lo scaricamento della classe.)

Esistono due set di metodi per la modifica dei campi statici: uno per la lettura dei campi di istanza e uno per la scrittura di campi di istanza. Tutti i set di metodi richiedono un ID di campo per leggere o scrivere il valore del campo.

### <a name="reading-static-field-values"></a>Lettura di valori di campi staticiReading Static Field Values

Il set di metodi per la lettura dei valori dei campi statici segue il modello di denominazione:The set of methods for reading static field values follows the naming pattern:

```csharp
* JNIEnv.GetStatic*Field(IntPtr class, IntPtr fieldID);
```

dove `*` è il tipo di campo:

- [JNIEnv.GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*) &ndash; Legge il valore di qualsiasi campo statico che non `java.lang.Object` sia un tipo incorporato, ad esempio , matrici e tipi di interfaccia. Il valore restituito è un riferimento locale JNI.

- [JNIEnv.GetStaticBooleanField](xref:Android.Runtime.JNIEnv.GetStaticBooleanField*) &ndash; Legge il `bool` valore dei campi statici.

- [JNIEnv.GetStaticByteField](xref:Android.Runtime.JNIEnv.GetStaticByteField*) &ndash; Legge il `sbyte` valore dei campi statici.

- [JNIEnv.GetStaticCharField](xref:Android.Runtime.JNIEnv.GetStaticCharField*) &ndash; Legge il `char` valore dei campi statici.

- [JNIEnv.GetStaticShortField](xref:Android.Runtime.JNIEnv.GetStaticShortField*) &ndash; Legge il `short` valore dei campi statici.

- [JNIEnv.GetStaticLongField](xref:Android.Runtime.JNIEnv.GetStaticLongField*) &ndash; Legge il `long` valore dei campi statici.

- [JNIEnv.GetStaticFloatField](xref:Android.Runtime.JNIEnv.GetStaticFloatField*) &ndash; Legge il `float` valore dei campi statici.

- [JNIEnv.GetStaticDoubleField](xref:Android.Runtime.JNIEnv.GetStaticDoubleField*) &ndash; Legge il `double` valore dei campi statici.

### <a name="writing-static-field-values"></a>Scrittura di valori di campo staticiWriting Static Field Values

Il set di metodi per la scrittura di valori di campo statici segue il modello di denominazione:The set of methods for writing static field values follows the naming pattern:

```csharp
JNIEnv.SetStaticField(IntPtr class, IntPtr fieldID, Type value);
```

dove *Tipo* è il tipo del campo:

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Scrivere il valore di qualsiasi campo statico che `java.lang.Object` non sia un tipo incorporato, ad esempio , matrici e tipi di interfaccia. Il `IntPtr` valore può essere un riferimento locale JNI, un riferimento `IntPtr.Zero` globale `null` JNI, un riferimento globale debole JNI o (per ).

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Scrivere `bool` il valore dei campi statici.

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Scrivere `sbyte` il valore dei campi statici.

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Scrivere `char` il valore dei campi statici.

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Scrivere `short` il valore dei campi statici.

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Scrivere `int` il valore dei campi statici.

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Scrivere `long` il valore dei campi statici.

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Scrivere `float` il valore dei campi statici.

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Scrivere `double` il valore dei campi statici.

<a name="_Instance_Methods" />

## <a name="instance-methods"></a>Metodi di istanza

I metodi di istanza vengono richiamati tramite *gli ID di metodo*. Gli ID di metodo vengono ottenuti tramite [JNIEnv.GetMethodID](xref:Android.Runtime.JNIEnv.GetMethodID*), che richiede il tipo in cui è definito il metodo, il nome del metodo e la firma del [tipo JNI](#JNI_Type_Signatures) del metodo.

Gli ID di metodo non devono essere liberati e sono validi finché viene caricato il tipo Java corrispondente. (Android non supporta attualmente lo scaricamento della classe.)

Esistono due set di metodi per richiamare i metodi: uno per richiamare i metodi virtualmente e uno per richiamare i metodi in modo non virtuale. Entrambi i set di metodi richiedono un ID metodo per richiamare il metodo e la chiamata non virtuale richiede anche di specificare quale implementazione della classe deve essere richiamata.

I metodi di interfaccia possono essere cercati solo all'interno del tipo dichiarante; i metodi che provengono da interfacce estese/ereditate non possono essere cercati. Vedere la sezione relativa alle interfacce di associazione / implementazione di Invoker più avanti per ulteriori dettagli.

Qualsiasi metodo dichiarato nella classe o in qualsiasi classe base o interfaccia implementata può essere cercato.

### <a name="virtual-method-invocation"></a>Chiamata di metodo virtualeVirtual Method Invocation

Il set di metodi per richiamare i metodi segue virtualmente il modello di denominazione:The set of methods for invoking methods virtually follows the naming pattern:

```csharp
* JNIEnv.Call*Method( IntPtr instance, IntPtr methodID, params JValue[] args );
```

dove `*` è il tipo restituito del metodo.

- [JNIEnv.CallObjectMethod](xref:Android.Runtime.JNIEnv.CallObjectMethod*) &ndash; Richiama un metodo che restituisce un `java.lang.Object` tipo non incorporato, ad esempio , matrici e interfacce. Il valore restituito è un riferimento locale JNI.

- [JNIEnv.CallBooleanMethod](xref:Android.Runtime.JNIEnv.CallBooleanMethod*) &ndash; Richiama un metodo `bool` che restituisce un valore.

- [JNIEnv.CallByteMethod](xref:Android.Runtime.JNIEnv.CallByteMethod*) &ndash; Richiama un metodo `sbyte` che restituisce un valore.

- [JNIEnv.CallCharMethod](xref:Android.Runtime.JNIEnv.CallCharMethod*) &ndash; Richiama un metodo `char` che restituisce un valore.

- [JNIEnv.CallShortMethod](xref:Android.Runtime.JNIEnv.CallShortMethod*) &ndash; Richiama un metodo `short` che restituisce un valore.

- [JNIEnv.CallLongMethod](xref:Android.Runtime.JNIEnv.CallLongMethod*) &ndash; Richiama un metodo `long` che restituisce un valore.

- [JNIEnv.CallFloatMethod](xref:Android.Runtime.JNIEnv.CallFloatMethod*) &ndash; Richiama un metodo `float` che restituisce un valore.

- [JNIEnv.CallDoubleMethod](xref:Android.Runtime.JNIEnv.CallDoubleMethod*) &ndash; Richiama un metodo `double` che restituisce un valore.

### <a name="non-virtual-method-invocation"></a>Chiamata di metodo non virtuale

Il set di metodi per richiamare metodi in modo non virtuale segue il modello di denominazione:The set of methods for invoking methods non-virtually follows the naming pattern:

```csharp
* JNIEnv.CallNonvirtual*Method( IntPtr instance, IntPtr class, IntPtr methodID, params JValue[] args );
```

dove `*` è il tipo restituito del metodo. La chiamata di metodo non virtuale viene in genere utilizzata per richiamare il metodo di base di un metodo virtuale.

- [JNIEnv.CallNonvirtualObjectMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualObjectMethod*) &ndash; Non richiama virtualmente un metodo che restituisce un `java.lang.Object` tipo non incorporato, ad esempio , matrici e interfacce. Il valore restituito è un riferimento locale JNI.

- [JNIEnv.CallNonvirtualBooleanMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualBooleanMethod*) &ndash; Non richiama virtualmente un `bool` metodo che restituisce un valore.

- [JNIEnv.CallNonvirtualByteMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualByteMethod*) &ndash; Non richiama virtualmente un `sbyte` metodo che restituisce un valore.

- [JNIEnv.CallNonvirtualCharMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualCharMethod*) &ndash; Non richiama virtualmente un `char` metodo che restituisce un valore.

- [JNIEnv.CallNonvirtualShortMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualShortMethod*) &ndash; Non richiama virtualmente un `short` metodo che restituisce un valore.

- [JNIEnv.CallNonvirtualLongMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualLongMethod*) &ndash; Non richiama virtualmente un `long` metodo che restituisce un valore.

- [JNIEnv.CallNonvirtualFloatMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualFloatMethod*) &ndash; Non richiama virtualmente un `float` metodo che restituisce un valore.

- [JNIEnv.CallNonvirtualDoubleMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualDoubleMethod*) &ndash; Non richiama virtualmente un `double` metodo che restituisce un valore.

<a name="_Static_Methods" />

## <a name="static-methods"></a>Metodi statici

I metodi statici vengono richiamati tramite *gli ID metodo*. Gli ID di metodo vengono ottenuti tramite [JNIEnv.GetStaticMethodID](xref:Android.Runtime.JNIEnv.GetStaticMethodID*), che richiede il tipo in cui è definito il metodo, il nome del metodo e la firma del [tipo JNI](#JNI_Type_Signatures) del metodo.

Gli ID di metodo non devono essere liberati e sono validi finché viene caricato il tipo Java corrispondente. (Android non supporta attualmente lo scaricamento della classe.)

### <a name="static-method-invocation"></a>Chiamata di metodo staticoStatic Method Invocation

Il set di metodi per richiamare i metodi segue virtualmente il modello di denominazione:The set of methods for invoking methods virtually follows the naming pattern:

```csharp
* JNIEnv.CallStatic*Method( IntPtr class, IntPtr methodID, params JValue[] args );
```

dove `*` è il tipo restituito del metodo.

- [JNIEnv.CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) &ndash; Richiama un metodo statico che restituisce un `java.lang.Object` tipo non incorporato, ad esempio , matrici e interfacce. Il valore restituito è un riferimento locale JNI.

- [JNIEnv.CallStaticBooleanMethod](xref:Android.Runtime.JNIEnv.CallStaticBooleanMethod*) &ndash; Richiama un metodo `bool` statico che restituisce un valore.

- [JNIEnv.CallStaticByteMethod](xref:Android.Runtime.JNIEnv.CallStaticByteMethod*) &ndash; Richiama un metodo `sbyte` statico che restituisce un valore.

- [JNIEnv.CallStaticCharMethod](xref:Android.Runtime.JNIEnv.CallStaticCharMethod*) &ndash; Richiama un metodo `char` statico che restituisce un valore.

- [JNIEnv.CallStaticShortMethod](xref:Android.Runtime.JNIEnv.CallStaticShortMethod*) &ndash; Richiama un metodo `short` statico che restituisce un valore.

- [JNIEnv.CallStaticLongMethod](xref:Android.Runtime.JNIEnv.CallLongMethod*) &ndash; Richiama un metodo `long` statico che restituisce un valore.

- [JNIEnv.CallStaticFloatMethod](xref:Android.Runtime.JNIEnv.CallStaticFloatMethod*) &ndash; Richiama un metodo `float` statico che restituisce un valore.

- [JNIEnv.CallStaticDoubleMethod](xref:Android.Runtime.JNIEnv.CallStaticDoubleMethod*) &ndash; Richiama un metodo `double` statico che restituisce un valore.

<a name="JNI_Type_Signatures" />

## <a name="jni-type-signatures"></a>Firme di tipo JNI

[Le firme di tipo JNI](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/types.html#wp16432) sono [riferimenti ai](#_JNI_Type_References) tipi JNI (anche se non i riferimenti ai tipi semplificati), ad eccezione dei metodi. Con i metodi, la firma del `'('`tipo JNI è una parentesi aperta , seguita dai riferimenti ai tipi per tutti i `')'`tipi di parametro concatenati tra loro (senza virgole di separazione o altro), seguiti da una parentesi di chiusura , seguita dal riferimento al tipo JNI del tipo restituito del metodo.

Ad esempio, dato il metodo Java:

```java
long f(int n, String s, int[] array);
```

La firma del tipo JNI sarebbe:

```csharp
(ILjava/lang/String;[I)J
```

In generale, *strongly* si consiglia vivamente `javap` di utilizzare il comando per determinare le firme JNI. Ad esempio, la firma di tipo JNI del metodo [java.lang.Thread.State.valueOf(String)](https://developer.android.com/reference/java/lang/Thread.State.html#valueOf(java.lang.String)) è "(Ljava/lang/String;)Ljava/lang/Thread-State;", mentre la firma del tipo JNI del metodo [java.lang.Thread.State.values](https://developer.android.com/reference/java/lang/Thread.State.html#values) è "()[Ljava/lang/Thread-State;". Attenzione per i punti e virgola finali; questi *fanno* parte della firma di tipo JNI.

<a name="_JNI_Type_References" />

## <a name="jni-type-references"></a>Riferimenti al tipo JNI

I riferimenti ai tipi JNI sono diversi dai riferimenti ai tipi Java.JNI type references are different from Java type references. Non è possibile utilizzare nomi `java.lang.String` di tipo Java completi, ad esempio `"java/lang/String"` `"Ljava/lang/String;"`con JNI, è invece necessario utilizzare le varianti JNI o , a seconda del contesto; vedere di seguito per i dettagli.
Esistono quattro tipi di riferimenti di tipo JNI:

- **Incorporato**
- **Semplificato**
- **type**
- **Matrice**

### <a name="built-in-type-references"></a>Riferimenti ai tipi incorporati

I riferimenti ai tipi incorporati sono un carattere singolo, utilizzato per fare riferimento ai tipi di valore incorporati. Il mapping è il seguente:

- `"B"`per `sbyte` .
- `"S"`per `short` .
- `"I"`per `int` .
- `"J"`per `long` .
- `"F"`per `float` .
- `"D"`per `double` .
- `"C"`per `char` .
- `"Z"`per `bool` .
- `"V"`per `void` i tipi restituiti del metodo.

<a name="_Simplified_Type_References_1" />

### <a name="simplified-type-references"></a>Riferimenti ai tipi semplificati

I riferimenti ai tipi semplificati possono essere utilizzati solo in [JNIEnv.FindClass(string)](xref:Android.Runtime.JNIEnv.FindClass*)).
Esistono due modi per derivare un riferimento al tipo semplificato:There are two ways to derive a simplified type reference:

1. Da un nome Java completo `'.'` sostituire every all'interno del `'/'` nome del `'.'` pacchetto e `'$'` prima del nome del tipo con , e ogni all'interno di un nome di tipo con .

1. Leggere l'output di `'unzip -l android.jar | grep JavaName'` .

Uno dei due comporterà il mapping del tipo Java [java.lang.Thread.State](https://developer.android.com/reference/java/lang/Thread.State.html) al riferimento `java/lang/Thread$State`al tipo semplificato.

### <a name="type-references"></a>Riferimenti al tipo

Un riferimento al tipo è un riferimento al tipo `'L'` incorporato `';'` o un riferimento al tipo semplificato con un prefisso e un suffisso. Per il tipo Java [java.lang.String](https://developer.android.com/reference/java/lang/String.html), `"java/lang/String"`il riferimento al `"Ljava/lang/String;"`tipo semplificato è , mentre il riferimento al tipo è .

I riferimenti ai tipi vengono utilizzati con i riferimenti ai tipi Array e con le firme JNI.

Un altro modo per ottenere un riferimento `'javap -s -classpath android.jar fully.qualified.Java.Name'`al tipo consiste nel leggere l'output di .
A seconda del tipo coinvolto, è possibile utilizzare una dichiarazione del costruttore o un tipo restituito del metodo per determinare il nome JNI. Ad esempio:

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

`Thread.State`è un tipo enum Java, pertanto `valueOf` è possibile utilizzare la firma del metodo per determinare che il riferimento al tipo è Ljava/lang/Thread-State;.

### <a name="array-type-references"></a>Riferimenti ai tipi di array

I riferimenti `'['` ai tipi di matrice sono preceduti da un riferimento al tipo JNI.
I riferimenti ai tipi semplificati non possono essere utilizzati quando si specificano matrici.

`int[]` Ad esempio, `"[I"` `int[][]` è `"[[I"`, `java.lang.Object[]` `"[Ljava/lang/Object;"`è e è .

## <a name="java-generics-and-type-erasure"></a>Generics Java e cancellazione dei tipi

*La maggior parte* delle volte, come visto attraverso JNI, i generici Java *non esistono*.
Ci sono alcune "rughe", ma quelle rughe sono nel modo in cui Java interagisce con i generics, non con il modo in cui JNI guarda e richiama i membri generici.

Non esiste alcuna differenza tra un tipo o un membro generico e un tipo o membro non generico durante l'interazione tramite JNI. Ad esempio, il tipo generico [&lt;java.lang.Class T&gt; ](https://developer.android.com/reference/java/lang/Class.html) è anche il tipo `java.lang.Class`generico `"java/lang/Class"`"raw", entrambi con lo stesso riferimento al tipo semplificato, .

## <a name="java-native-interface-support"></a>Supporto dell'interfaccia nativa Java

[Android.Runtime.JNIEnv](xref:Android.Runtime.JNIEnv) è un wrapper gestito per l'interfaccia Jave Native (JNI). Le funzioni JNI vengono dichiarate all'interno della [specifica dell'interfaccia nativa Java](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html), anche se i metodi sono stati modificati per rimuovere il parametro esplicito `JNIEnv*` e `IntPtr` vengono utilizzati al posto di `jobject`, `jclass`, `jmethodID`, e così via. Si consideri, ad esempio, la [funzione JNI NewObject](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp4517):

```csharp
jobject NewObjectA(JNIEnv *env, jclass clazz, jmethodID methodID, jvalue *args);
```

Questo viene esposto come il metodo [JNIEnv.NewObject:](xref:Android.Runtime.JNIEnv.NewObject*)

```csharp
public static IntPtr NewObject(IntPtr clazz, IntPtr jmethod, params JValue[] parms);
```

Tradurre tra le due chiamate è ragionevolmente semplice. In C si avrebbe:

```c
jobject CreateMapActivity(JNIEnv *env)
{
    jclass    Map_Class   = (*env)->FindClass(env, "mono/samples/googlemaps/MyMapActivity");
    jmethodID Map_defCtor = (*env)->GetMethodID (env, Map_Class, "<init>", "()V");
    jobject   instance    = (*env)->NewObject (env, Map_Class, Map_defCtor);

    return instance;
}
```

L'equivalente di C'è:

```csharp
IntPtr CreateMapActivity()
{
    IntPtr Map_Class   = JNIEnv.FindClass ("mono/samples/googlemaps/MyMapActivity");
    IntPtr Map_defCtor = JNIEnv.GetMethodID (Map_Class, "<init>", "()V");
    IntPtr instance    = JNIEnv.NewObject (Map_Class, Map_defCtor);

    return instance;
}
```

Una volta che si dispone di un oggetto Java istanza contenuta in un IntPtr, è probabile che si desidera eseguire un'operazione con esso. È possibile utilizzare i metodi JNIEnv, ad esempio [JNIEnv.CallVoidMethod()](xref:Android.Runtime.JNIEnv.CallVoidMethod*) per eseguire questa operazione, ma se è già presente un wrapper analogico di C, è consigliabile costruire un wrapper sopra il riferimento JNI. È possibile farlo tramite il [Extensions.JavaCast\<T>](xref:Android.Runtime.Extensions.JavaCast*) metodo di estensione:

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = new Java.Lang.Object(lrefActivity, JniHandleOwnership.TransferLocalRef)
    .JavaCast<Activity>();
```

È inoltre possibile utilizzare il metodo [\<Java.Lang.Object.GetObject T>:](xref:Java.Lang.Object.GetObject*)

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = Java.Lang.Object.GetObject<Activity>(lrefActivity, JniHandleOwnership.TransferLocalRef);
```

Inoltre, tutte le funzioni JNI sono state `JNIEnv*` modificate rimuovendo il parametro presente in ogni funzione JNI.

## <a name="summary"></a>Riepilogo

Trattare direttamente con JNI è un'esperienza terribile che dovrebbe essere evitata a tutti i costi. Purtroppo, non è sempre evitabile; si spera che questa guida fornirà un po 'di assistenza quando si colpisce i casi Java non legati con Mono per Android.

## <a name="related-links"></a>Collegamenti correlati

- [Specifiche dell'interfaccia nativa JavaJava Native Interface Specification](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/jniTOC.html)
- [Java Native Interface Functions](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)
