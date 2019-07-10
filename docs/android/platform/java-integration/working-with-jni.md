---
title: Utilizzo di JNI
description: Xamarin. Android consente la scrittura di App Android all'interno di C# invece di Java. Diversi assembly forniti con xamarin. Android che forniscono associazioni per le librerie Java incluse Mono.Android.dll e Mono.Android.GoogleMaps.dll. Tuttavia, le associazioni non disponibili per ogni libreria Java possibili e le associazioni fornite non possono associare ogni tipo di Java e membro. Per utilizzare non associato tipi Java e i membri, Java Native Interface (JNI) possono essere usati. Questo articolo illustra come usare JNI per interagire con i tipi Java e i membri da applicazioni xamarin. Android.
ms.prod: xamarin
ms.assetid: A417DEE9-7B7B-4E35-A79C-284739E3838E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: aa2e2ac96b37bc781f2e4a3778ea0aaf970649ec
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674611"
---
# <a name="working-with-jni"></a>Utilizzo di JNI

_Xamarin. Android consente la scrittura di App Android all'interno di C# invece di Java. Diversi assembly forniti con xamarin. Android che forniscono associazioni per le librerie Java incluse Mono.Android.dll e Mono.Android.GoogleMaps.dll. Tuttavia, le associazioni non disponibili per ogni libreria Java possibili e le associazioni fornite non possono associare ogni tipo di Java e membro. Per utilizzare non associato tipi Java e i membri, Java Native Interface (JNI) possono essere usati. Questo articolo illustra come usare JNI per interagire con i tipi Java e i membri da applicazioni xamarin. Android._


## <a name="overview"></a>Panoramica

Non è sempre possibile creare un gestiti Callable Wrapper (MCW) per richiamare il codice Java o necessarie. In molti casi, "inline" JNI è perfettamente accettabile e utile per l'uso occasionale dei membri di Java non associati. È spesso più semplice l'utilizzo di JNI per richiamare un metodo singolo in una classe Java rispetto per generare un'associazione con estensione jar intero.

Xamarin. Android fornisce il `Mono.Android.dll` assembly, che fornisce un'associazione per Android `android.jar` libreria. Tipi e membri non presentano all'interno `Mono.Android.dll` e i tipi presentano all'interno di `android.jar` possono essere usati da associandole manualmente. Per associare tipi Java e i membri, si utilizza il **Java Native Interface** (**JNI**) per cercare i tipi, leggere e scrivere i campi e richiamare i metodi.

L'API di JNI in xamarin. Android è concettualmente molto simile al `System.Reflection` API in .NET: rende inoltre possibile cercare i tipi e membri in base al nome, leggere e scrivere i valori di campo, richiamare i metodi e altro ancora. È possibile usare JNI e `Android.Runtime.RegisterAttribute` attributo personalizzato da dichiarare metodi virtuali che possono essere associati per il supporto viene sottoposto a override. È possibile associare le interfacce in modo che possano essere implementati C#.

Questo documento illustra:

-  Come JNI fa riferimento a tipi.
-  Come effettuare la ricerca, leggere e scrivere i campi.
-  Come effettuare la ricerca e richiamare i metodi.
-  Viene descritto come esporre metodi virtuali per consentire la sostituzione dal codice gestito.
-  Modalità di esposizione delle interfacce.



## <a name="requirements"></a>Requisiti

JNI, come viene esposta tramite il [dello spazio dei nomi Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/), è disponibile in ogni versione di xamarin. Android.
Per associare le interfacce e tipi di Java, è necessario usare xamarin. Android 4.0 o versione successiva.


## <a name="managed-callable-wrappers"></a>Callable Wrapper gestito

Oggetto **gestito Callable Wrapper** (**MCW**) è un *associazione* per una classe Java o un'interfaccia che esegue il wrapping di tutte le macchine JNI pertanto tale client C# non è necessario codice Devi preoccuparti della complessità sottostante di JNI. La maggior parte delle `Mono.Android.dll` è costituito da callable wrapper gestito.

Wrapper richiamabili gestiti svolgono due funzioni:

1.  Incapsulare l'utilizzo JNI in modo che il codice client non è necessario conoscere le complessità sottostanti.
1.  Rendono possibile la sottoclasse tipi Java e implementare interfacce Java.

Il primo scopo è esclusivamente per comodità e l'incapsulamento di complessità in modo che gli utenti abbiano un set di classi da usare semplice e gestito. Ciò richiede l'uso di vari [JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/) membri come descritto più avanti in questo articolo. Tenere presente che callable wrapper gestiti non sono strettamente necessarie &ndash; "inline" utilizzo di JNI è perfettamente accettabile ed è utile per l'uso occasionale dei membri di Java non associati. Implementazione di una sottoclasse e interfaccia richiede l'uso di callable wrapper gestito.



## <a name="android-callable-wrappers"></a>Android Callable Wrapper

Android callable wrapper (ACW) sono necessari ogni volta che il runtime di Android (ART) deve richiamare codice non gestito. questi wrapper sono necessari perché non è possibile registrare le classi con ART in fase di esecuzione.
(In particolare, il [DefineClass](http://docs.oracle.com/javase/6/docs/technotes/guides/jni/spec/functions.html#wp15986) JNI funzione non è supportata dal runtime di Android. Android callable wrapper pertanto costituiscono la mancanza di supporto di registrazione tipo di runtime.)

Ogni volta che codice Android deve eseguire un virtuale o l'interfaccia di metodo che viene viene sottoposto a override o implementato nel codice gestito, xamarin. Android deve fornire un proxy di Java in modo che questo metodo ottiene inviato al tipo gestito appropriato. Questi tipi di proxy Java sono codice Java che hanno la classe di base "stesso" e un elenco delle interfacce Java come tipo gestito, che implementa il costruttore stesso e la dichiarazione di qualsiasi classe di base sottoposto a override e i metodi di interfaccia.

Android callable wrapper vengono generati per il **monodroid.exe** programma durante il [processo di compilazione](~/android/deploy-test/building-apps/build-process.md)e vengono generati per tutti i tipi che ereditano (direttamente o indirettamente) [ Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/).



### <a name="implementing-interfaces"></a>Implementazione di interfacce

Vi sono casi quando potrebbe essere necessario implementare un'interfaccia di Android, (ad esempio [Android.Content.IComponentCallbacks](https://developer.xamarin.com/api/type/Android.Content.IComponentCallbacks/)).

Tutte le classi di Android e le interfacce estendono la [Android.Runtime.IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) interfaccia; pertanto, devono implementare tutti i tipi di Android `IJavaObject`.
Xamarin. Android consente di sfruttare questo fatto &ndash; Usa `IJavaObject` fornire Android con un proxy di Java (Android callable wrapper) per il tipo gestito specificato. In quanto **monodroid.exe** Cerca solo `Java.Lang.Object` sottoclassi (che deve implementare `IJavaObject`), la creazione di sottoclassi `Java.Lang.Object` ci offre un modo per implementare le interfacce nel codice gestito. Ad esempio:

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

*La parte restante di questo articolo fornisce i dettagli di implementazione soggette a modifiche senza preavviso* (e viene presentato in questo caso solo perché gli sviluppatori potrebbero essere sono curiosi riguardo cosa succede dietro le quinte).

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

Il **mandroid.exe** programma genererà il Wrapper chiamabile Android seguenti:

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

Si noti che la classe di base viene mantenuta e le dichiarazioni di metodo nativo sono disponibili per ogni metodo sottoposto a override nel codice gestito.



### <a name="exportattribute-and-exportfieldattribute"></a>ExportAttribute ed ExportFieldAttribute

In genere, xamarin. Android genera automaticamente il codice Java che comprende il ACW; Questa generazione si basa sui nomi di classe e metodo quando una classe derivata da una classe Java e si esegue l'override di metodi Java esistenti. Tuttavia, in alcuni scenari, la generazione di codice non è adeguata, come indicato di seguito:

-   Android supporta i nomi delle azioni negli attributi XML di layout, ad esempio la [android: onClick](https://developer.xamarin.com/api/member/Android.Views.View+IOnClickListener.OnClick/p/Android.Views.View/) attributo XML. Quando viene specificato, l'istanza di visualizzazione ingrandita prova a cercare il metodo di Java.

-   Il [java.io.Serializable](https://developer.android.com/reference/java/io/Serializable.html) richiede l'interfaccia `readObject` e `writeObject` metodi. Poiché non sono membri di questa interfaccia, la nostra implementazione gestita corrispondente non espone questi metodi per il codice Java.

-   Il [android.os.Parcelable](https://developer.xamarin.com/api/type/Android.Os.Parcelable/) interfaccia prevede che una classe di implementazione deve avere un campo statico `CREATOR` typu `Parcelable.Creator`. Il codice Java generato richiede un campo esplicito. Con questo scenario standard, non è possibile al campo di output nel codice Java da codice gestito.


Poiché la generazione di codice non fornisce una soluzione per generare metodi Java arbitrari con nomi arbitrari, a partire da xamarin. Android 4.2, il [ExportAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute/) e [ExportFieldAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportFieldAttribute/) erano introdotto per offrire una soluzione per gli scenari precedenti. Entrambi gli attributi si trovano nel `Java.Interop` dello spazio dei nomi:

-   `ExportAttribute` &ndash; Specifica un nome di metodo e i relativi tipi di eccezione prevista (per fornire l'esplicita "lancia" in Java). Quando viene usato su un metodo, il metodo "Esporta" un metodo di Java che genera un codice di invio della chiamata di JNI corrispondente al metodo gestito. Può essere usato con `android:onClick` e `java.io.Serializable`.

-   `ExportFieldAttribute` &ndash; Specifica un nome di campo. Si trova su un metodo che funziona come un inizializzatore di campo. Può essere usato con `android.os.Parcelable`.

Il [ExportAttribute](https://developer.xamarin.com/samples/monodroid/ExportAttribute/) progetto di esempio illustra come usare questi attributi.


#### <a name="troubleshooting-exportattribute-and-exportfieldattribute"></a>Risoluzione dei problemi ExportAttribute ed ExportFieldAttribute

-   Si verifica un errore di creazione del pacchetto a causa dell'assenza **Mono.Android.Export.dll** &ndash; se è stato usato `ExportAttribute` oppure `ExportFieldAttribute` in alcuni metodi nel codice o le librerie dipendenti, è necessario aggiungere  **Mono.Android.Export.dll**. Questo assembly è isolato per supportare il codice di callback da Java. Separata dalle **Mono.Android.dll** durante l'aggiunta delle dimensioni aggiuntivi all'applicazione.

-   Nelle build di rilascio `MissingMethodException` si verifica per i metodi di esportazione &ndash; build di rilascio In `MissingMethodException` si verifica per i metodi di esportazione. (Questo problema viene risolto nella versione più recente di xamarin. Android).



### <a name="exportparameterattribute"></a>ExportParameterAttribute

`ExportAttribute` e `ExportFieldAttribute` offrono funzionalità che Java utilizzabili dal codice in fase di esecuzione. Questo codice in fase di esecuzione accede a codice gestito tramite i metodi JNI generati dai quegli attributi. Di conseguenza, non vi è alcun metodo Java esistente che associa il metodo gestito; di conseguenza, il metodo di Java viene generato da una firma di metodo gestito.

In questo caso, tuttavia, non è completamente determinante. In particolare, questo vale in alcuni mapping avanzato tra i tipi gestiti e tipi di Java, ad esempio:

-  InputStream
-  OutputStream
-  XmlPullParser
-  XmlResourceParser

Quando i tipi come i seguenti sono necessari per i metodi esportati, i `ExportParameterAttribute` deve essere utilizzato per assegnare al parametro corrispondente in modo esplicito o un tipo di valore restituito.



### <a name="annotation-attribute"></a>Attributo di annotazione

In xamarin. Android 4.2, abbiamo convertito `IAnnotation` i tipi di implementazione in attributi (Attribute) e il supporto aggiunto per la generazione di annotazione dei wrapper di Java.

Ciò significa che le modifiche direzionale seguenti:

-   Il generatore di binding genera `Java.Lang.DeprecatedAttribute` dal `java.Lang.Deprecated` (anche se deve essere `[Obsolete]` nel codice gestito).

-   Ciò non significa esistenti `Java.Lang.Deprecated` classe svaniranno. Questi oggetti basata su Java potrebbero essere ancora usati come normali oggetti di Java (se è presente tale utilizzo). Esisterà `Deprecated` e `DeprecatedAttribute` classi.

-   Il `Java.Lang.DeprecatedAttribute` classe è contrassegnata come `[Annotation]` . Quando è presente un attributo personalizzato che viene ereditato da questa `[Annotation]` attributo, attività msbuild genererà un'annotazione di Java per l'attributo personalizzato (@Deprecated) nell'Android Callable Wrapper (ACW).

-   Le annotazioni è stato possibile generare in classi, metodi e campi (che è un metodo nel codice gestito) esportato.

Se la classe contenitore (classe annotata stesso o la classe che contiene i membri con annotazioni) non è registrata, l'intera origine della classe Java non viene generato, tra cui le annotazioni. Per i metodi, è possibile specificare il `ExportAttribute` a ottenere il metodo generato in modo esplicito e annotate. Inoltre, non è una funzionalità per la definizione di una classe di annotazione Java "generazione". In altre parole, se si definisce un attributo personalizzato gestito per un determinato annotazione, è possibile aggiungere un'altra libreria con estensione jar che contiene la classe di annotazione Java corrispondente. Aggiunta di un file di origine Java che definisce il tipo di annotazione non è sufficiente. Il compilatore Java non funziona nello stesso modo **apt**.

Inoltre, si applicano le limitazioni seguenti:

-   Questo processo di conversione non viene considerato `@Target` annotazione del tipo di annotazione finora.

-   Gli attributi in una proprietà non funziona. Usare invece gli attributi per la proprietà getter o setter.



## <a name="class-binding"></a>Classe Binding

Una classe di associazione, significa che la scrittura di un oggetto callable wrapper gestito per semplificare la chiamata del tipo Java sottostante.

Associazione di metodi virtuali e astratti per consentire l'override di C# richiede xamarin. Android 4.0. Tuttavia, qualsiasi versione di xamarin. Android può associare metodi non virtuali, i metodi statici o metodi virtuali senza che supportano le sostituzioni.

In genere, un'associazione contiene gli elementi seguenti:

-  Oggetto [JNI handle per il tipo di linguaggio associato](#_Looking_up_Java_Types).

-  [JNI campo ID e le proprietà per ogni campo associato](#_Instance_Fields).

-  [Gli ID metodo JNI e metodi per ogni associati metodo](#_Instance_Methods).

-  Se è necessaria una sottoclasse, il tipo deve avere una [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) attributo personalizzato nella dichiarazione del tipo con [RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) impostato su `true`.



### <a name="declaring-type-handle"></a>Handle del tipo dichiarante

I metodi di ricerca di campo e metodo richiedono un riferimento all'oggetto che fa riferimento al tipo dichiarante. Per convenzione, questa viene mantenuta in un `class_ref` campo:

```csharp
static IntPtr class_ref = JNIEnv.FindClass(CLASS);
```

Vedere le [riferimenti a tipi di JNI](#_JNI_Type_References) sezione per informazioni dettagliate sul `CLASS` token.


### <a name="binding-fields"></a>Campi di associazione

I campi di Java sono esposte come C# proprietà, ad esempio il campo Java [java.lang.System.in](https://developer.android.com/reference/java/lang/System.html#in) viene associato come il C# proprietà [Java.Lang.JavaSystem.In](https://developer.xamarin.com/api/property/Java.Lang.JavaSystem.In/).
Inoltre, poiché JNI distingue tra i campi statici e i campi di istanza, possibile utilizzare metodi diversi quando si implementa la proprietà.

Associazione campo include tre set di metodi:

1.  Il *Ottieni id campo* (metodo). Il *Ottieni id del campo* metodo è responsabile della restituzione di un campo di gestire questa la *ottenere il valore campo* e *impostare il valore di campo* useranno i metodi. Come ottenere l'id campo, è necessario conoscere la dichiarazione di tipo, il nome del campo e il [firma del tipo JNI](#JNI_Type_Signatures) del campo.

1.  Il *ottenere il valore campo* metodi. Questi metodi richiedono l'handle di campo e sono responsabili della lettura il valore del campo da Java.
    Il metodo da usare dipende dal tipo del campo.

1.  Il *impostare il valore di campo* metodi. Questi metodi richiedono l'handle di campo e sono responsabili della scrittura il valore del campo all'interno di Java. Il metodo da usare dipende dal tipo del campo.


 [I campi statici](#_Static_Fields) usare il [JNIEnv.GetStaticFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/), `JNIEnv.GetStatic*Field`, e [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/) metodi.

 [Campi di istanza](#_Instance_Fields) usare il [JNIEnv.GetFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFieldID/), `JNIEnv.Get*Field`, e [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/) metodi.

Ad esempio, la proprietà statica `JavaSystem.In` possono essere implementate come:

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

Nota: Utilizziamo [InputStreamInvoker.FromJniHandle](https://developer.xamarin.com/api/member/Android.Runtime.InputStreamInvoker.FromJniHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) per convertire il riferimento JNI in un `System.IO.Stream` istanza e si sta usando `JniHandleOwnership.TransferLocalRef` perché [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/) restituisce un riferimento locale.

Molte delle [Android.Runtime](https://developer.xamarin.com/api/namespace/Android.Runtime/) tipi hanno `FromJniHandle` riferimento per i metodi che convertirà un JNI nel tipo desiderato.



### <a name="method-binding"></a>Associazione (metodo)

Metodi Java vengono esposte come C# metodi sia come C# delle proprietà. Ad esempio, il metodo di Java [java.lang.Runtime.runFinalizersOnExit](https://developer.android.com/reference/java/lang/Runtime.html#runFinalizersOnExit(boolean)) metodo associato come le [Java.Lang.Runtime.RunFinalizersOnExit](https://developer.xamarin.com/api/member/Java.Lang.Runtime.RunFinalizersOnExit/) metodo e il [java.lang.Object.getClass ](https://developer.android.com/reference/java/lang/Object.html#getClass) metodo viene associato come i [Java.Lang.Object.Class](https://developer.xamarin.com/api/property/Java.Lang.Object.Class/) proprietà.

Chiamata al metodo è un processo in due passaggi:

1.  Il *Ottieni id metodo* per il metodo da richiamare. Il *Ottieni id metodo* metodo è responsabile della restituzione di un handle che useranno i metodi di chiamata al metodo. Come ottenere l'id del metodo, è necessario conoscere la dichiarazione di tipo, il nome del metodo e il [firma del tipo JNI](#JNI_Type_Signatures) del metodo.

1.  Richiamare il metodo.

Proprio come con i campi, i metodi da usare per ottenere l'id del metodo e richiamare il metodo differiscono tra i metodi statici e metodi di istanza.

[I metodi statici](#_Static_Methods_1) usano [JNIEnv.GetStaticMethodID()](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/) per cercare l'id del metodo e usare il `JNIEnv.CallStatic*Method` famiglia di metodi per la chiamata.

[Metodi di istanza](#_Instance_Methods) usano [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/) per cercare l'id del metodo e usare i `JNIEnv.Call*Method` e `JNIEnv.CallNonvirtual*Method` famiglie dei metodi per la chiamata.

Associazione di metodo è potenzialmente più della semplice chiamata al metodo. Associazione metodo anche implica che un metodo da sottoporre a override (per i metodi astratti e non finali) o implementata (per i metodi di interfaccia). Il [che supportano l'ereditarietà, interfacce](#_Supporting_Inheritance,_Interfaces_1) sezione descrive le complessità dei metodi virtuali e i metodi di interfaccia di supporto.

<a name="_Static_Methods_1" />

#### <a name="static-methods"></a>Metodi statici

Associazione di un metodo statico prevede l'utilizzo `JNIEnv.GetStaticMethodID` per ottenere un handle di metodo, quindi utilizzando il metodo appropriato `JNIEnv.CallStatic*Method` (metodo), a seconda del tipo restituito del metodo. Di seguito è riportato un esempio di un'associazione per il [Runtime.getRuntime](https://developer.android.com/reference/java/lang/Runtime.html#getRuntime()) metodo:

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

Si noti che è memorizzare l'handle del metodo in un campo statico, `id_getRuntime`. Si tratta di un'ottimizzazione delle prestazioni, in modo che l'handle del metodo non deve necessariamente essere cercati in ogni chiamata. Non è necessario memorizzare nella cache l'handle del metodo in questo modo. Dopo aver ottenuto l'handle del metodo, [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) viene utilizzato per richiamare il metodo. `JNIEnv.CallStaticObjectMethod` Restituisce un `IntPtr` che contiene l'handle dell'istanza di Java restituita.
[Java.Lang.Object.GetObject&lt;T&gt;(IntPtr, JniHandleOwnership)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) viene usata per convertire l'handle di Java in un'istanza dell'oggetto fortemente tipizzato.



#### <a name="non-virtual-instance-method-binding"></a>Associazione di metodo di istanza non virtuale

Associazione di un `final` metodo di istanza o un metodo di istanza che non richiede l'override, prevede l'uso `JNIEnv.GetMethodID` per ottenere un handle di metodo, quindi utilizzando il metodo appropriato `JNIEnv.Call*Method` (metodo), a seconda del tipo restituito del metodo. Di seguito è riportato un esempio di un'associazione per il `Object.Class` proprietà:

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

Si noti che è memorizzare l'handle del metodo in un campo statico, `id_getClass`.
Si tratta di un'ottimizzazione delle prestazioni, in modo che l'handle del metodo non deve necessariamente essere cercati in ogni chiamata. Non è necessario memorizzare nella cache l'handle del metodo in questo modo. Dopo aver ottenuto l'handle del metodo, [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) viene utilizzato per richiamare il metodo. `JNIEnv.CallStaticObjectMethod` Restituisce un `IntPtr` che contiene l'handle dell'istanza di Java restituita.
[Java.Lang.Object.GetObject&lt;T&gt;(IntPtr, JniHandleOwnership)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) viene usata per convertire l'handle di Java in un'istanza dell'oggetto fortemente tipizzato.


### <a name="binding-constructors"></a>Costruttori di associazione

I costruttori sono metodi Java con il nome `"<init>"`. Come con i metodi di istanza di Java, `JNIEnv.GetMethodID` viene usato per cercare l'handle del costruttore. A differenza dei metodi di Java, il [JNIEnv.NewObject](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewObject/) metodi vengono usati per richiamare l'handle di metodo del costruttore. Il valore restituito di `JNIEnv.NewObject` è un riferimento locale JNI:


```csharp
int value = 42;
IntPtr class_ref    = JNIEnv.FindClass ("java/lang/Integer");
IntPtr id_ctor_I    = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
IntPtr lrefInstance = JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value));
// Dispose of lrefInstance, class_ref…
```

In genere un'associazione di classe verrà sottoclasse [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/).
Quando si crea una sottoclasse `Java.Lang.Object`, un aggiuntive semantica entra il gioco: una `Java.Lang.Object` istanza mantiene un riferimento a un'istanza di Java tramite globale il `Java.Lang.Object.Handle` proprietà.

1.  Il `Java.Lang.Object` costruttore predefinito verrà allocata un'istanza di Java.

1.  Se il tipo ha un `RegisterAttribute` , e `RegisterAttribute.DoNotGenerateAcw` viene `true` , quindi un'istanza del `RegisterAttribute.Name` tipo viene creato tramite il costruttore predefinito.

1.  In caso contrario, il [Android Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) corrispondente a `this.GetType` viene creata un'istanza tramite il costruttore predefinito. Android Callable Wrapper vengono generati durante la creazione del pacchetto per ogni `Java.Lang.Object` sottoclasse per il quale `RegisterAttribute.DoNotGenerateAcw` non è impostata su `true`.

Per i tipi che rappresentano non la classe Binding, ciò è previsto semantic: creare un'istanza di un `Mono.Samples.HelloWorld.HelloAndroid` C# istanza deve costruire un oggetto Java `mono.samples.helloworld.HelloAndroid` istanza che è un Android Callable Wrapper generato.

Per le associazioni di classe, è possibile il comportamento corretto se il tipo Java contiene un costruttore predefinito e/o nessun altro costruttore deve essere richiamato. In caso contrario, è necessario specificare un costruttore che consente di eseguire le azioni seguenti:

1.  Richiama il [Java.Lang.Object (IntPtr, JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) anziché il valore predefinito `Java.Lang.Object` costruttore. Questa operazione è necessaria per evitare di creare una nuova istanza di Java.

1.  Controllare il valore della [Java.Lang.Object.Handle](https://developer.xamarin.com/api/property/Java.Lang.Object.Handle/) prima di creare tutte le istanze Java. Il `Object.Handle` proprietà avrà un valore diverso da `IntPtr.Zero` se è stato costruito un Android Callable Wrapper nel codice Java e l'associazione di classe in fase di costruzione per contenere l'istanza di Android Callable Wrapper creata. Ad esempio, quando Android crea una `mono.samples.helloworld.HelloAndroid` istanza, verrà creato il Android Callable Wrapper primo e il linguaggio `HelloAndroid` costruttore creerà un'istanza dell'oggetto corrispondente `Mono.Samples.HelloWorld.HelloAndroid` tipo, con la `Object.Handle` proprietà in corso impostare per l'istanza di Java prima dell'esecuzione del costruttore.

1.  Se il tipo di runtime corrente non è quello utilizzato per la dichiarazione di tipo, quindi un'istanza di Android Callable Wrapper corrispondente è necessario creare e usare [Object.SetHandle](https://developer.xamarin.com/api/member/Java.Lang.Object.SetHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) per archiviare l'handle restituito da [ JNIEnv.CreateInstance](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CreateInstance/).

1.  Se il tipo di runtime corrente è lo stesso tipo dichiarante, quindi richiamare il costruttore di Java e usano [Object.SetHandle](https://developer.xamarin.com/api/member/Java.Lang.Object.SetHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) per archiviare l'handle restituito da `JNIEnv.NewInstance` .


Si consideri, ad esempio, il [java.lang.Integer(int)](https://developer.android.com/reference/java/lang/Integer.html#Integer(int)) costruttore. Questo viene associato come:

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

Il [JNIEnv.CreateInstance](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CreateInstance/) sono metodi helper per eseguire un `JNIEnv.FindClass`, `JNIEnv.GetMethodID`, `JNIEnv.NewObject`, e `JNIEnv.DeleteGlobalReference` sul valore restituito da `JNIEnv.FindClass`. Per informazioni dettagliate, vedere la sezione successiva.

<a name="_Supporting_Inheritance,_Interfaces_1" />

### <a name="supporting-inheritance-interfaces"></a>Supporto dell'ereditarietà, interfacce

Crea una sottoclasse di un tipo di Java o sull'implementazione delle interfacce Java richiede la generazione di [Android Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md) (ACWs) che vengono generati per ogni `Java.Lang.Object` sottoclasse durante il processo di creazione dei pacchetti. Generazione ACW è controllata tramite il [Android.Runtime.RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) attributo personalizzato.

Per C# tipi, il `[Register]` costruttore di attributo personalizzato richiede un argomento: il [JNI semplificato riferimento di tipo](#_Simplified_Type_References_1) per il linguaggio corrispondente digitare. Ciò consente di fornire nomi diversi tra Java e C#.

Prima di xamarin. Android 4.0, il `[Register]` attributo personalizzato non è disponibile "alias" tipi Java esistente. Infatti, il processo di generazione ACW genererebbero ACWs per ogni `Java.Lang.Object` sottoclasse rilevato.

Xamarin. Android 4.0 ha introdotto il [RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) proprietà. Questa proprietà indica il processo della generazione ACW *ignorare* il tipo con annotazione, che consente la dichiarazione di nuovi gestiti Callable Wrapper che non comporterà ACWs generati al momento della creazione del pacchetto. In questo modo i tipi Java esistenti di associazione. La classe Java semplice seguente, si consideri ad esempio `Adder`, che contiene un solo metodo, `add`, che aggiunge a numeri interi e restituisce il risultato:

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

In questo caso, il `Adder` C# tipo *alias* il `Adder` tipo Java. Il `[Register]` attributo è usato per specificare il nome JNI del `mono.android.test.Adder` tipo Java e il `DoNotGenerateAcw` proprietà viene utilizzata per impedire la generazione di ACW. Ciò comporterà la generazione di un ACW per il `ManagedAdder` tipo, in modo corretto le sottoclassi di `mono.android.test.Adder` tipo. Se il `RegisterAttribute.DoNotGenerateAcw` proprietà non veniva ancora utilizzata, quindi sarebbe stato generato un nuovo processo di compilazione xamarin. Android `mono.android.test.Adder` tipo Java. Ciò potrebbe comportare errori di compilazione, come il `mono.android.test.Adder` tipo può essere incluso due volte, in due file separati.



### <a name="binding-virtual-methods"></a>Associazione di metodi virtuali

`ManagedAdder` le sottoclassi di Java `Adder` tipo, ma non è particolarmente interessante: la C# `Adder` tipo non definisce metodi virtuali, pertanto `ManagedAdder` non è possibile eseguire l'override di qualsiasi elemento.

Associazione `virtual` metodi per consentire l'override dalle sottoclassi richiede diverse operazioni che devono essere eseguite che rientrano in due categorie seguenti:

1.  **Associazione (metodo)**

1.  **Registrazione del metodo.**


#### <a name="method-binding"></a>Associazione (metodo)

Un'associazione al metodo richiede l'aggiunta di due membri del supporto per il C# `Adder` definizione: `ThresholdType`, e `ThresholdClass`.

##### <a name="thresholdtype"></a>ThresholdType

Il `ThresholdType` proprietà restituisce il tipo dell'associazione corrente:

```csharp
partial class Adder {
    protected override System.Type ThresholdType {
        get {
            return typeof (Adder);
        }
    }
}
```

`ThresholdType` viene utilizzato nel metodo di associazione per determinare quando l'esecuzione di invio dei metodi non virtuali e non virtuale. L'app deve sempre restituire un `System.Type` istanza che corrisponde alla dichiarazione C# digitare.

##### <a name="thresholdclass"></a>ThresholdClass

Il `ThresholdClass` proprietà restituisce il riferimento alla classe JNI per il tipo associato:

```csharp
partial class Adder {
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```

`ThresholdClass` viene utilizzato il metodo di associazione quando si chiamano i metodi non virtuali.

#### <a name="binding-implementation"></a>Implementazione dell'associazione

L'implementazione dell'associazione del metodo è responsabile di runtime la chiamata al metodo di Java. Contiene inoltre un `[Register]` dichiarazione di attributo personalizzato che fa parte della registrazione (metodo) e verrà descritta nella sezione di registrazione del metodo:

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

Il `id_add` campo contiene l'ID di metodo per il metodo Java da richiamare. Il `id_add` valore viene ottenuto dal `JNIEnv.GetMethodID`, che richiede la classe dichiarante (`class_ref`), il nome del metodo Java (`"add"`) e la firma del metodo di JNI (`"(II)I"`).

Dopo aver ottenuto l'ID del metodo, `GetType` viene confrontato con `ThresholdType` per determinare se macchina virtuale o non invio è necessaria. Chiamate virtuali è obbligatoria quando `GetType` corrisponde al `ThresholdType`, come `Handle` può fare riferimento a una sottoclasse allocata Java che esegue l'override del metodo.

Quando `GetType` non corrisponde al `ThresholdType`, `Adder` sottoclassato (ad esempio, da `ManagedAdder`) e il `Adder.Add` implementazione verrà richiamata solo se la sottoclasse richiamato `base.Add`. Ciò avviene dispatch non virtuale, ovvero dove `ThresholdClass` è disponibile in. `ThresholdClass` Specifica la classe Java fornirà l'implementazione del metodo da richiamare.



#### <a name="method-registration"></a>Registrazione del metodo.

Si supponga che una versione aggiornata `ManagedAdder` definizione che esegue l'override di `Adder.Add` metodo:

```csharp
partial class ManagedAdder : Adder {
    public override int Add (int a, int b) {
        return (a*2) + (b*2);
    }
}
```

È importante ricordare che `Adder.Add` aveva una `[Register]` attributo personalizzato:

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
```

Il `[Register]` costruttore dell'attributo personalizzato accetta tre valori:

1.  Il nome del metodo Java `"add"` in questo caso.

1.  La firma del metodo, tipo di JNI `"(II)I"` in questo caso.

1.  Il *metodo connettore* , `GetAddHandler` in questo caso.
    I metodi di connettore verranno discusso più avanti.


I primi due parametri consentono al processo di generazione ACW generare una dichiarazione di metodo per eseguire l'override del metodo. La risultante ACW conterrebbe una parte del codice seguente:

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

Si noti che un `@Override` metodo viene dichiarato, che delega a un `n_`-metodo lo stesso nome come prefisso. Assicurarsi che quando il codice Java richiama `ManagedAdder.add`, `ManagedAdder.n_add` verrà richiamato, in modo che l'override di C# `ManagedAdder.Add` metodo da eseguire.

Di conseguenza, la domanda più importante: come viene `ManagedAdder.n_add` collegato alla `ManagedAdder.Add`?

Java `native` i metodi sono registrati con il runtime di Java (runtime di Android) tramite il [JNI RegisterNatives funzione](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp17734).
`RegisterNatives` accetta una matrice di strutture che contiene il nome del metodo Java, la firma di tipo JNI e un puntatore a funzione da richiamare che segue [JNI convenzione di chiamata](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/design.html#wp715).
Puntatore a funzione deve essere una funzione che accetta due argomenti di puntatore seguiti dai parametri di metodo. Java `ManagedAdder.n_add` metodo deve essere implementato tramite una funzione che ha il seguente prototipo C:

```csharp
int FunctionName(JNIEnv *env, jobject this, int a, int b)
```

Xamarin. Android non espongono un `RegisterNatives` (metodo). Al contrario, il ACW e MCW insieme forniscono le informazioni necessarie per richiamare `RegisterNatives`: il ACW contiene il nome del metodo e la firma di tipo JNI, il manca solo un puntatore a funzione collegherà.

Si tratta di dove il *metodo connettore* è disponibile in. La terza `[Register]` parametro di attributo personalizzato è il nome di un metodo definito nel tipo registrato o di una classe di base del tipo registrato che non accetta parametri e restituisce un `System.Delegate`. L'oggetto restituito `System.Delegate` a sua volta fa riferimento a un metodo che ha la firma della funzione JNI corretta. Infine, il delegato restituito dal metodo connector *necessario* deve contenere una radice in modo che il Garbage Collector non raccoglie, come il delegato viene fornito a Java.

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

Il `GetAddHandler` metodo crea un' `Func<IntPtr, IntPtr, int, int,
int>` delegato che fa riferimento al `n_Add` metodo, quindi richiama [JNINativeWrapper.CreateDelegate](https://developer.xamarin.com/api/member/Android.Runtime.JNINativeWrapper.CreateDelegate/).
`JNINativeWrapper.CreateDelegate` include il metodo specificato in un blocco try/catch, in modo che vengono gestiti e comporterà la generazione di eccezioni non gestite di [AndroidEvent.UnhandledExceptionRaiser](https://developer.xamarin.com/api/event/Android.Runtime.AndroidEnvironment.UnhandledExceptionRaiser/) evento. Il delegato risultante viene archiviato in statico `cb_add` variabili in modo che il Garbage Collector non consente di liberare il delegato.

Infine, il `n_Add` metodo è responsabile per il marshalling dei parametri JNI per i tipi gestiti corrispondenti, quindi delega il metodo di chiamata.

Nota: Usare sempre `JniHandleOwnership.DoNotTransfer` durante il recupero di un MCW su un'istanza di Java. Considerarle come un riferimento locale (e pertanto la chiamata `JNIEnv.DeleteLocalRef`) si interromperà gestito -&gt; Java -&gt; gestiti transizioni dello stack.



### <a name="complete-adder-binding"></a>Binding Adder completo

L'intero gestiti associazione per il `mono.android.tests.Adder` è di tipo:

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

Durante la scrittura di un tipo che corrisponde ai criteri seguenti:

1.  Sottoclassi `Java.Lang.Object`

1.  Ha un `[Register]` attributo personalizzato

1.  `RegisterAttribute.DoNotGenerateAcw` è `true`


Quindi per il tipo di interazione di Garbage Collection *non deve* hanno tutti i campi che possono fare riferimento a un `Java.Lang.Object` o `Java.Lang.Object` sottoclasse in fase di esecuzione. Ad esempio, i campi di tipo `System.Object` e qualsiasi tipo di interfaccia non sono consentiti. I tipi che non possono fare riferimento a `Java.Lang.Object` sono consentite istanze, ad esempio `System.String` e `List<int>`. Questa restrizione è per impedire la raccolta di oggetti prematura dal GC.

Se il tipo deve contenere un campo di istanza che faccia riferimento a un `Java.Lang.Object` dell'istanza, il tipo di campo deve essere `System.WeakReference` o `GCHandle`.



## <a name="binding-abstract-methods"></a>I metodi astratti di associazione

Associazione `abstract` metodi è in gran parte identico all'associazione di metodi virtuali. Sono disponibili solo due differenze:

1.  Il metodo astratto è astratto. Mantiene ancora il `[Register]` attributo e la registrazione del metodo associato, il metodo di associazione viene semplicemente spostato il `Invoker` tipo.

1.  Non - `abstract` `Invoker` creazione che crea una sottoclasse del tipo del tipo astratto. Il `Invoker` tipo deve eseguire l'override di metodi astratti tutti dichiarati nella classe di base e l'implementazione sottoposta a override è l'implementazione di metodo di associazione, anche se può essere ignorato il caso di invio non virtuale.


Ad esempio, si supponga che il codice precedente `mono.android.test.Adder.add` metodo fosse `abstract`. Il C# associazione comporterebbe la modifica in modo che `Adder.Add` erano astratta e un nuovo `AdderInvoker` tipo deve essere definito che implementato `Adder.Add`:

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

Il `Invoker` tipo è necessario solo quando si ottengono riferimenti JNI alle istanze create Java.


## <a name="binding-interfaces"></a>Interfacce di binding

Interfacce di binding sono concettualmente simile al binding di classi che contengono metodi virtuali, ma molte delle specifiche differenze meno evidenti (e meno complessi). Tenere presente quanto segue [dichiarazione di interfaccia Java](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/Adder.java#L14):

```csharp
public interface Progress {
    void onAdd(int[] values, int currentIndex, int currentSum);
}
```

Binding dell'interfaccia hanno due parti: il C# definizione di interfaccia e una definizione Invoker dell'interfaccia.



### <a name="interface-definition"></a>Definizione di interfaccia

Il C# definizione di interfaccia deve soddisfare i requisiti seguenti:

-   La definizione dell'interfaccia deve avere un `[Register]` attributo personalizzato.

-   La definizione dell'interfaccia deve estendere la `IJavaObject interface`.
    In caso contrario, non potrà ACWs che eredita dall'interfaccia Java.

-   Ogni metodo di interfaccia deve contenere un `[Register]` attributo che specifica il nome del metodo Java corrispondente, la firma JNI e il metodo connector.

-   Il metodo di connettore è necessario specificare anche il tipo che il metodo connettore può essere posizionato in.

Quando si associano `abstract` e `virtual` metodi, il metodo connettore verrebbero da cercare all'interno della gerarchia di ereditarietà del tipo in fase di registrazione. Le interfacce non possono avere alcun metodo che contiene i corpi, in modo che il problema persiste, pertanto il requisito che è possibile specificare un tipo che indica dove si trova il metodo connettore. Il tipo viene specificato all'interno della stringa di metodo connettore, dopo i due punti `':'`, e deve essere il nome completo del tipo di assembly del tipo contenente l'invoker.

Le dichiarazioni di metodo di interfaccia sono una traduzione dell'oggetto corrispondente metodo Java utilizzando *compatibili* tipi. Per tipi Java builtin, i tipi compatibili sono il corrispondente C# tipi, ad esempio Java `int` è C# `int`. Per i tipi di riferimento, il tipo compatibile è un tipo che può fornire un handle JNI di tipo Java appropriato.

I membri di interfaccia non verranno richiamati direttamente da Java &ndash; chiamata verrà avvenga tramite il tipo Invoker &ndash; in modo che è consentito un livello di flessibilità.

L'interfaccia Java lo stato di avanzamento può essere [dichiarato in C# come](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L83):

```csharp
[Register ("mono/android/test/Adder$Progress", DoNotGenerateAcw=true)]
public interface IAdderProgress : IJavaObject {
    [Register ("onAdd", "([III)V",
            "GetOnAddHandler:Mono.Samples.SanityTests.IAdderProgressInvoker, SanityTests, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null")]
    void OnAdd (JavaArray<int> values, int currentIndex, int currentSum);
}
```

Si noti che in quello precedente è che il mapping di Java `int[]` parametro per un [JavaArray&lt;int&gt;](https://developer.xamarin.com/api/type/Android.Runtime.JavaArray%601/).
Questo non è necessario: è stato possibile sono associati a un C# `int[]`, o un' `IList<int>`, o qualsiasi altro interamente. Indipendentemente dal tipo viene scelto, il `Invoker` deve essere in grado di convertirlo in un linguaggio `int[]` tipo per la chiamata.


### <a name="invoker-definition"></a>Invoker definizione

Il `Invoker` definizione del tipo deve ereditare `Java.Lang.Object`, implementare l'interfaccia appropriata e fornire tutti i metodi di connessione fa riferimento nella definizione dell'interfaccia. È presente un suggerimento altre che differisce da un'associazione di classe: il `class_ref` ID campo e metodo devono essere membri di istanza, i membri non statici.

Il motivo per preferire i membri di istanza ha a che fare con `JNIEnv.GetMethodID` comportamento nel runtime di Android. (Potrebbe trattarsi anche il comportamento di Java, non è stato testato). `JNIEnv.GetMethodID` restituisce null durante la ricerca di un metodo che deriva da un'interfaccia implementata e non l'interfaccia dichiarata. Prendere in considerazione la [java.util.SortedMap&lt;K, V&gt; ](https://developer.android.com/reference/java/util/SortedMap.html) interfaccia Java, che implementa il [java.util.Map&lt;K, V&gt; ](https://developer.android.com/reference/java/util/Map.html) interfaccia. Mappa include un [cancellare](https://developer.android.com/reference/java/util/Map.html#clear()) metodo, pertanto un apparentemente ragionevole `Invoker` definizione per SortedMap sarebbe:

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

Il codice precedente avrà esito negativo perché `JNIEnv.GetMethodID` restituirà `null` quando si cercano le `Map.clear` metodo tramite la `SortedMap` istanza della classe.

Sono disponibili due soluzioni a questo: tenere traccia di quale interfaccia proviene ogni metodo e avere un `class_ref` per ogni interfaccia, o mantenere tutto come membri di istanza ed eseguire la ricerca del metodo nel tipo di classe più derivata, non il tipo di interfaccia. Quest'ultimo viene eseguito in **Mono.Android.dll**.

La definizione dell'Invoker contiene sei sezioni: il costruttore, il `Dispose` metodo, il `ThresholdType` e `ThresholdClass` membri, il `GetObject` metodo di implementazione del metodo dell'interfaccia e l'implementazione del metodo connector.



#### <a name="constructor"></a>Costruttore

Il costruttore deve cercare la classe di runtime dell'istanza di richiamata e archiviare la classe di runtime nell'istanza `class_ref` campo:

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

Nota: Il `Handle` proprietà deve essere usata all'interno del corpo di costruttore e non il `handle` parametro, come in Android v4.0 il `handle` parametro potrebbe non essere valido al termine dell'esecuzione del costruttore base.


#### <a name="dispose-method"></a>Metodo Dispose

Il `Dispose` metodo deve liberare il riferimento globale allocato nel costruttore:

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

Il `ThresholdType` e `ThresholdClass` membri sono identici a ciò che si trova in un'associazione di classe:

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

Un valore statico `GetObject` metodo è necessario per supportare [Extensions.JavaCast&lt;T&gt;()](https://developer.xamarin.com/api/member/Android.Runtime.Extensions.JavaCast%7BTResult%7D/p/Android.Runtime.IJavaObject/):

```csharp
partial class IAdderProgressInvoker {
    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }
}
```


#### <a name="interface-methods"></a>Metodi di interfaccia

Ogni metodo dell'interfaccia deve avere un'implementazione che richiama il metodo corrispondente Java tramite JNI:

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



#### <a name="connector-methods"></a>Metodi di connettore

I metodi di connettore e l'infrastruttura di supporto sono responsabili del marshalling di parametri JNI appropriati C# tipi. Il linguaggio `int[]` parametro viene passato come un JNI `jintArray`, ovvero un' `IntPtr` all'interno di C#. Il `IntPtr` deve essere sottoposto a marshalling una `JavaArray<int>` per supportare il richiamo di C# interfaccia:

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

Se `int[]` sarebbe preferibile `JavaList<int>`, quindi [JNIEnv.GetArray()](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetArray/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership%2cSystem.Type)) è anche possibile usare:

```csharp
int[] _values = (int[]) JNIEnv.GetArray(values, JniHandleOwnership.DoNotTransfer, typeof (int));
```

Si noti tuttavia che `JNIEnv.GetArray` copia l'intera matrice tra le macchine virtuali, in modo che per le matrici di grandi dimensioni ciò può comportare un numero elevato di aggiunta di stress di Garbage Collection.


### <a name="complete-invoker-definition"></a>Definizione di completato Invoker

Il [completare IAdderProgressInvoker definizione](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L88):

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

Molti metodi JNIEnv restituiscono *JNI* *riferimenti a oggetti*, che sono simili a `GCHandle`s. JNI offre tre diversi tipi di riferimenti agli oggetti: riferimenti locali, i riferimenti globali e riferimenti deboli globali. Tutte e tre sono rappresentati come `System.IntPtr`, *ma* (come indicato nella sezione di tipi di funzioni di JNI) non tutti `IntPtr`restituiti da `JNIEnv` metodi sono i riferimenti. Ad esempio, [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/) restituisce un `IntPtr`, ma non restituisce un riferimento all'oggetto, viene restituito un `jmethodID`. Consultare il [documentazione della funzione JNI](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html) per informazioni dettagliate.

Riferimenti locali creati da *la maggior parte delle* metodi di creazione di riferimento.
Android consente solo un numero limitato di riferimenti locali esista in qualsiasi momento, in genere 512. Riferimenti locali possono essere eliminati tramite [JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/).
A differenza di JNI, non tutti i riferimenti JNIEnv utilizzando i metodi che fa riferimento a un oggetto restituito restituiscono riferimenti locali; [JNIEnv.FindClass](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/) restituisce un *globale* riferimento. È consigliabile eliminare riferimenti locali più rapidamente possibile, eventualmente creando un [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) attorno all'oggetto e specificando `JniHandleOwnership.TransferLocalRef` per il [Java.Lang.Object (IntPtr gestire, trasferimento JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) costruttore.

I riferimenti globali creati da [JNIEnv.NewGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewGlobalRef/) e [JNIEnv.FindClass](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/).
Possono essere eliminati definitivamente con [JNIEnv.DeleteGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteGlobalRef/).
Gli emulatori hanno un limite di 2.000 riferimenti globali in sospeso, anche se i dispositivi hardware hanno un limite di circa 52.000 riferimenti globali.

Riferimenti deboli globali sono disponibili solo in Android versione 2.2 (Froyo) e versioni successive. Riferimenti deboli globali possono essere eliminati con [JNIEnv.DeleteWeakGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteWeakGlobalRef/(System.IntPtr)).


### <a name="dealing-with-jni-local-references"></a>Gestione dei riferimenti locale JNI in

Il [JNIEnv.GetObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetObjectField/), [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/), [JNIEnv.CallObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallObjectMethod/), [JNIEnv.CallNonvirtualObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualObjectMethod/)e [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) metodi restituiscono un `IntPtr` che contiene un riferimento locale JNI a un oggetto di Java, o `IntPtr.Zero` se Java restituito `null`. A causa del numero limitato di riferimenti locali che possono essere in sospeso a una volta (512 voci), è opportuno assicurarsi che i riferimenti vengono eliminati in modo tempestivo. Esistono tre modi in cui possono essere gestiti riferimenti locali: in modo esplicito l'eliminazione, la creazione di un `Java.Lang.Object` istanza per contenere stesse e usando `Java.Lang.Object.GetObject<T>()` per creare un gestito callable wrapper attorno a esse.



### <a name="explicitly-deleting-local-references"></a>In modo esplicito l'eliminazione di riferimenti locali

[JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/) consente di eliminare riferimenti locali. Una volta eliminato il riferimento locale, non può essere usato più, pertanto prestare attenzione per assicurarsi che `JNIEnv.DeleteLocalRef` è l'ultima operazione eseguita con il riferimento locale.

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

`Java.Lang.Object` fornisce una [Java.Lang.Object (handle IntPtr, trasferimento JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) costruttore che può essere utilizzato per eseguire il wrapping di un riferimento JNI in fase di chiusura. Il [JniHandleOwnership](https://developer.xamarin.com/api/type/Android.Runtime.JniHandleOwnership/) parametro determina come il `IntPtr` parametro deve essere trattato:

-   [JniHandleOwnership.DoNotTransfer](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.DoNotTransfer/) &ndash; creato `Java.Lang.Object` istanza verrà creato un nuovo riferimento globale dal `handle` parametro e `handle` rimane invariato.
    Il chiamante è responsabile della liberazione `handle` , se necessario.

-   [JniHandleOwnership.TransferLocalRef](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.TransferLocalRef/) &ndash; creato `Java.Lang.Object` istanza verrà creato un nuovo riferimento globale dal `handle` parametro, e `handle` venga eliminata con [JNIEnv.DeleteLocalRef ](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/) . Il chiamante non deve liberare `handle` e non devono utilizzare `handle` al termine dell'esecuzione al costruttore.

-   [JniHandleOwnership.TransferGlobalRef](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.TransferLocalRef/) &ndash; creato `Java.Lang.Object` istanza assumerà la proprietà del `handle` parametro. Non deve essere liberato dal chiamante `handle` .


Poiché i metodi di chiamata al metodo JNI restituiscono locale refs, `JniHandleOwnership.TransferLocalRef` normalmente utilizzati:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef);
```

Il riferimento di global creato non verranno liberato finché il `Java.Lang.Object` istanza è sottoposto a garbage collection. Se si è in grado di, eliminazione dell'istanza per liberare il riferimento di global, velocizzare le operazioni di garbage collection:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
using (var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef)) {
    // use value ...
}
```



### <a name="using-javalangobjectgetobjectlttgt"></a>Using Java.Lang.Object.GetObject&lt;T&gt;()

`Java.Lang.Object` fornisce una [Java.Lang.Object.GetObject&lt;T&gt;(handle IntPtr, trasferimento JniHandleOwnership)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) metodo che può essere utilizzato per creare un wrapper chiamabile gestito del tipo specificato.

Il tipo `T` devono soddisfare i requisiti seguenti:

1.  `T` deve essere un tipo di riferimento.

1.  `T` deve implementare il `IJavaObject` interfaccia.

1.  Se `T` non è una classe astratta o interfaccia, quindi `T` devono fornire un costruttore con i tipi di parametro `(IntPtr,
    JniHandleOwnership)` .

1.  Se `T` è una classe astratta o un'interfaccia, non esiste *necessario* da un *invoker* disponibili per `T` . Un invoker è un tipo non astratta che eredita `T` o implementa `T` , e ha lo stesso nome `T` con un suffisso dell'Invoker. Ad esempio, se T è l'interfaccia `Java.Lang.IRunnable` , quindi il tipo `Java.Lang.IRunnableInvoker` deve essere presente e deve contenere la necessaria `(IntPtr,
    JniHandleOwnership)` costruttore.


Poiché i metodi di chiamata al metodo JNI restituiscono locale refs, `JniHandleOwnership.TransferLocalRef` normalmente utilizzati:

```csharp
IntPtr lrefString = JNIEnv.CallObjectMethod(instance, methodID);
Java.Lang.String value = Java.Lang.Object.GetObject<Java.Lang.String>( lrefString, JniHandleOwnership.TransferLocalRef);
```

<a name="_Looking_up_Java_Types" />

## <a name="looking-up-java-types"></a>Ricerca dei tipi di Java

Per cercare un campo o un metodo in JNI, il tipo dichiarante per il campo o metodo debba essere cercato prima. Il [Android.Runtime.JNIEnv.FindClass(string)](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/(System.String)) metodo viene usato per cercare tipi Java. Il parametro della stringa è il *semplificato di riferimento di tipo* o il *riferimento completo del tipo* per il tipo Java. Vedere le [sezione riferimenti di tipo JNI](#_JNI_Type_References) per informazioni dettagliate sui riferimenti di tipo completo e semplificato.

Nota: A differenza di tutti gli altri `JNIEnv` metodo che restituisce istanze di oggetti, `FindClass` restituisce un riferimento globale, non un riferimento locale.

<a name="_Instance_Fields" />

## <a name="instance-fields"></a>Campi di istanza

I campi vengono modificati tramite *ID campo*. Campo ID vengono ottenuti tramite [JNIEnv.GetFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFieldID/), che richiede la classe che il campo è definito, il nome del campo e il [JNI tipo firma](#JNI_Type_Signatures) del campo.

ID campo non è necessario essere liberata e sono validi purché il tipo Java corrispondente viene caricato. (Android non supporta attualmente lo scaricamento di classe.)

Sono disponibili due set di metodi per la modifica dei campi di istanza: uno per la lettura dei campi di istanza e uno per la scrittura di campi di istanza. Tutti i set di metodi richiedono un ID di campo da leggere o scrivere il valore del campo.


### <a name="reading-instance-field-values"></a>Valori dei campi di istanza di lettura

Il set di metodi per la lettura dei valori di campo di istanza segue il modello di denominazione:

```csharp
* JNIEnv.Get*Field(IntPtr instance, IntPtr fieldID);
```
in cui `*` è il tipo del campo:

-   [JNIEnv.GetObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetObjectField/) &ndash; leggere il valore di qualsiasi campo di istanza che non è un tipo incorporato, ad esempio `java.lang.Object` , matrici e i tipi di interfaccia. Il valore restituito è un riferimento locale JNI.

-   [JNIEnv.GetBooleanField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetBooleanField/) &ndash; leggere il valore di `bool` campi di istanza.

-   [JNIEnv.GetByteField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetByteField/) &ndash; leggere il valore di `sbyte` campi di istanza.

-   [JNIEnv.GetCharField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetCharField/) &ndash; leggere il valore di `char` campi di istanza.

-   [JNIEnv.GetShortField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetShortField/) &ndash; leggere il valore di `short` campi di istanza.

-   [JNIEnv.GetIntField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetIntField/) &ndash; leggere il valore di `int` campi di istanza.

-   [JNIEnv.GetLongField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetLongField/) &ndash; leggere il valore di `long` campi di istanza.

-   [JNIEnv.GetFloatField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFloatField/) &ndash; leggere il valore di `float` campi di istanza.

-   [JNIEnv.GetDoubleField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetDoubleField/) &ndash; leggere il valore di `double` campi di istanza.





### <a name="writing-instance-field-values"></a>Valori dei campi di istanza di scrittura

Il set di metodi per la scrittura di valori di campo di istanza segue il modello di denominazione:

```csharp
JNIEnv.SetField(IntPtr instance, IntPtr fieldID, Type value);
```

in cui *tipo* è il tipo del campo:

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.IntPtr)) &ndash; scrivere il valore di qualsiasi campo che non è un tipo incorporato, ad esempio `java.lang.Object` , matrici e i tipi di interfaccia. Il `IntPtr` valore può essere un riferimento locale JNI, riferimento globale JNI, riferimento globale JNI debole, oppure `IntPtr.Zero` (per `null` ).

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Boolean)) &ndash; scrivere il valore di `bool` campi di istanza.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.SByte)) &ndash; scrivere il valore di `sbyte` campi di istanza.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Char)) &ndash; scrivere il valore di `char` campi di istanza.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int16)) &ndash; scrivere il valore di `short` campi di istanza.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int32)) &ndash; scrivere il valore di `int` campi di istanza.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int64)) &ndash; scrivere il valore di `long` campi di istanza.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Single)) &ndash; scrivere il valore di `float` campi di istanza.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Double)) &ndash; scrivere il valore di `double` campi di istanza.


<a name="_Static_Fields" />

## <a name="static-fields"></a>Campi statici

I campi statici vengono modificati tramite *ID campo*. Campo ID vengono ottenuti tramite [JNIEnv.GetStaticFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticFieldID/), che richiede la classe che il campo è definito, il nome del campo e il [JNI tipo firma](#JNI_Type_Signatures) del campo.

ID campo non è necessario essere liberata e sono validi purché il tipo Java corrispondente viene caricato. (Android non supporta attualmente lo scaricamento di classe.)

Esistono due set di metodi per modificare i campi statici: uno per la lettura dei campi di istanza e uno per la scrittura di campi di istanza. Tutti i set di metodi richiedono un ID di campo da leggere o scrivere il valore del campo.


### <a name="reading-static-field-values"></a>La lettura dei valori di campo statico

Il set di metodi per la lettura dei valori di campi statici segue il modello di denominazione:

```csharp
* JNIEnv.GetStatic*Field(IntPtr class, IntPtr fieldID);
```

in cui `*` è il tipo del campo:

-   [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/) &ndash; leggere il valore di un campo statico che non è un tipo incorporato, ad esempio `java.lang.Object` , matrici e i tipi di interfaccia. Il valore restituito è un riferimento locale JNI.

-   [JNIEnv.GetStaticBooleanField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticBooleanField/) &ndash; leggere il valore di `bool` campi statici.

-   [JNIEnv.GetStaticByteField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticByteField/) &ndash; leggere il valore di `sbyte` campi statici.

-   [JNIEnv.GetStaticCharField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticCharField/) &ndash; leggere il valore di `char` campi statici.

-   [JNIEnv.GetStaticShortField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticShortField/) &ndash; leggere il valore di `short` campi statici.

-   [JNIEnv.GetStaticLongField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticLongField/) &ndash; leggere il valore di `long` campi statici.

-   [JNIEnv.GetStaticFloatField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticFloatField/) &ndash; leggere il valore di `float` campi statici.

-   [JNIEnv.GetStaticDoubleField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticDoubleField/) &ndash; leggere il valore di `double` campi statici.



### <a name="writing-static-field-values"></a>La scrittura dei valori di campo statico

Il set di metodi per la scrittura di valori di campi statici segue il modello di denominazione:

```csharp
JNIEnv.SetStaticField(IntPtr class, IntPtr fieldID, Type value);
```

in cui *tipo* è il tipo del campo:

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.IntPtr)) &ndash; scrivere il valore di un campo statico che non è un tipo incorporato, ad esempio `java.lang.Object` , matrici e i tipi di interfaccia. Il `IntPtr` valore può essere un riferimento locale JNI, riferimento globale JNI, riferimento globale JNI debole, oppure `IntPtr.Zero` (per `null` ).

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Boolean)) &ndash; scrivere il valore di `bool` campi statici.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.SByte)) &ndash; scrivere il valore di `sbyte` campi statici.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Char)) &ndash; scrivere il valore di `char` campi statici.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int16)) &ndash; scrivere il valore di `short` campi statici.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int32)) &ndash; scrivere il valore di `int` campi statici.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int64)) &ndash; scrivere il valore di `long` campi statici.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Single)) &ndash; scrivere il valore di `float` campi statici.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Double)) &ndash; scrivere il valore di `double` campi statici.


<a name="_Instance_Methods" />

## <a name="instance-methods"></a>Metodi di istanza

Metodi di istanza vengono richiamati tramite *metodo ID*. Metodo ID vengono ottenuti tramite [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/), che richiede il tipo che il metodo è definito, il nome del metodo e il [JNI tipo firma](#JNI_Type_Signatures) del metodo.

Gli ID di metodo non è necessario essere liberata e sono validi purché il tipo Java corrispondente viene caricato. (Android non supporta attualmente lo scaricamento di classe.)

Sono disponibili due set di metodi per la chiamata dei metodi: uno per il richiamo di metodi praticamente e uno per richiamare metodi non virtuale. Entrambi i set di metodi richiedono un ID di metodo richiamare il metodo e chiamata non virtuali è anche necessario specificare quale implementazione della classe deve essere richiamato.

I metodi dell'interfaccia possono solo essere cercati all'interno del tipo dichiarante; i metodi che derivano da interfacce estese o ereditata non possono essere ricercati. Vedere le interfacce di Binding successive /Section Invoker dell'implementazione per altri dettagli.

Qualsiasi metodo dichiarato nella classe o qualsiasi classe di base o interfaccia implementata possa essere cercato.


### <a name="virtual-method-invocation"></a>Chiamata del metodo virtuale

Il set di metodi per richiamare metodi praticamente segue il modello di denominazione:

```csharp
* JNIEnv.Call*Method( IntPtr instance, IntPtr methodID, params JValue[] args );
```

in cui `*` è il tipo restituito del metodo.

-   [JNIEnv.CallObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallObjectMethod/) &ndash; richiamare un metodo che restituisce un tipo non incorporato, ad esempio `java.lang.Object` , matrici e le interfacce. Il valore restituito è un riferimento locale JNI.

-   [JNIEnv.CallBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallBooleanMethod/) &ndash; richiamare un metodo che restituisce un `bool` valore.

-   [JNIEnv.CallByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallByteMethod/) &ndash; richiamare un metodo che restituisce un `sbyte` valore.

-   [JNIEnv.CallCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallCharMethod/) &ndash; richiamare un metodo che restituisce un `char` valore.

-   [JNIEnv.CallShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallShortMethod/) &ndash; richiamare un metodo che restituisce un `short` valore.

-   [JNIEnv.CallLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallLongMethod/) &ndash; richiamare un metodo che restituisce un `long` valore.

-   [JNIEnv.CallFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallFloatMethod/) &ndash; richiamare un metodo che restituisce un `float` valore.

-   [JNIEnv.CallDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallDoubleMethod/) &ndash; richiamare un metodo che restituisce un `double` valore.



### <a name="non-virtual-method-invocation"></a>Chiamata al metodo non virtuale

Il set di metodi per richiamare metodi non virtuale segue il modello di denominazione:

```csharp
* JNIEnv.CallNonvirtual*Method( IntPtr instance, IntPtr class, IntPtr methodID, params JValue[] args );
```

in cui `*` è il tipo restituito del metodo. Chiamata al metodo non virtuale viene in genere utilizzato per richiamare il metodo di base di un metodo virtuale.

-   [JNIEnv.CallNonvirtualObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualObjectMethod/) &ndash; Non praticamente richiamare un metodo che restituisce un tipo non incorporato, ad esempio `java.lang.Object` , matrici e le interfacce. Il valore restituito è un riferimento locale JNI.

-   [JNIEnv.CallNonvirtualBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualBooleanMethod/) &ndash; Non praticamente richiamare un metodo che restituisce un `bool` valore.

-   [JNIEnv.CallNonvirtualByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualByteMethod/) &ndash; Non praticamente richiamare un metodo che restituisce un `sbyte` valore.

-   [JNIEnv.CallNonvirtualCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualCharMethod/) &ndash; Non praticamente richiamare un metodo che restituisce un `char` valore.

-   [JNIEnv.CallNonvirtualShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualShortMethod/) &ndash; Non praticamente richiamare un metodo che restituisce un `short` valore.

-   [JNIEnv.CallNonvirtualLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualLongMethod/) &ndash; Non praticamente richiamare un metodo che restituisce un `long` valore.

-   [JNIEnv.CallNonvirtualFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualFloatMethod/) &ndash; Non praticamente richiamare un metodo che restituisce un `float` valore.

-   [JNIEnv.CallNonvirtualDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualDoubleMethod/) &ndash; Non praticamente richiamare un metodo che restituisce un `double` valore.


<a name="_Static_Methods" />

## <a name="static-methods"></a>Metodi statici

Tramite vengono richiamati i metodi statici *metodo ID*. Metodo ID vengono ottenuti tramite [JNIEnv.GetStaticMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/), che richiede il tipo che il metodo è definito, il nome del metodo e il [JNI tipo firma](#JNI_Type_Signatures) del metodo.

Gli ID di metodo non è necessario essere liberata e sono validi purché il tipo Java corrispondente viene caricato. (Android non supporta attualmente lo scaricamento di classe.)



### <a name="static-method-invocation"></a>Chiamata al metodo statico

Il set di metodi per richiamare metodi praticamente segue il modello di denominazione:

```csharp
* JNIEnv.CallStatic*Method( IntPtr class, IntPtr methodID, params JValue[] args );
```

in cui `*` è il tipo restituito del metodo.

-   [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) &ndash; richiamare un metodo statico che restituisce un tipo non incorporato, ad esempio `java.lang.Object` , matrici e le interfacce. Il valore restituito è un riferimento locale JNI.

-   [JNIEnv.CallStaticBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticBooleanMethod/) &ndash; richiamare un metodo statico che restituisce un `bool` valore.

-   [JNIEnv.CallStaticByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticByteMethod/) &ndash; richiamare un metodo statico che restituisce un `sbyte` valore.

-   [JNIEnv.CallStaticCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticCharMethod/) &ndash; richiamare un metodo statico che restituisce un `char` valore.

-   [JNIEnv.CallStaticShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticShortMethod/) &ndash; richiamare un metodo statico che restituisce un `short` valore.

-   [JNIEnv.CallStaticLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallLongMethod/) &ndash; richiamare un metodo statico che restituisce un `long` valore.

-   [JNIEnv.CallStaticFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticFloatMethod/) &ndash; richiamare un metodo statico che restituisce un `float` valore.

-   [JNIEnv.CallStaticDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticDoubleMethod/) &ndash; richiamare un metodo statico che restituisce un `double` valore.


<a name="JNI_Type_Signatures" />

## <a name="jni-type-signatures"></a>Firme di tipo JNI

[Le firme di tipo JNI](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/types.html#wp16432) vengono [i riferimenti ai tipi di JNI](#_JNI_Type_References) (riferimenti di tipo anche se non semplificato), ad eccezione dei metodi. Con i metodi, la firma di tipo JNI è una parentesi aperta `'('`, seguita da riferimenti del tipo per tutti i parametri di tipi concatenati tra loro (con nessun separatore delle migliaia separazione o altro), seguita da una parentesi di chiusura `')'`, aggiungendo il riferimento al tipo JNI del tipo restituito del metodo.

Ad esempio, dato il metodo di Java:

```java
long f(int n, String s, int[] array);
```

La firma di tipo JNI sarebbe:

```csharp
(ILjava/lang/String;[I)J
```

In generale, si tratta *fortemente* consiglia di utilizzare il `javap` comando per determinare le firme JNI. Ad esempio, la firma di tipo JNI del [java.lang.Thread.State.valueOf(String)](https://developer.android.com/reference/java/lang/Thread.State.html#valueOf(java.lang.String)) metodo è "(Ljava/lang/stringa); stato$ Ljava/lang/Thread;", mentre il JNI digitare firma del [ java.lang.Thread.State.values](https://developer.android.com/reference/java/lang/Thread.State.html#values) metodo è "() [stato$ Ljava/lang/Thread;". Prestare attenzione per i punti e virgola finali; tali *sono* fa parte della firma del tipo JNI.

<a name="_JNI_Type_References" />

## <a name="jni-type-references"></a>Riferimenti ai tipi JNI

I riferimenti ai tipi JNI sono diversi dai riferimenti di tipo Java. Non è possibile utilizzare nomi completi di tipo Java, ad esempio `java.lang.String` di JNI, è necessario usare invece le variazioni di JNI `"java/lang/String"` o `"Ljava/lang/String;"`, a seconda del contesto; vedere di seguito per informazioni dettagliate.
Esistono quattro tipi di riferimenti a tipi JNI:

-  **built-in**
-  **simplified**
-  **type**
-  **array**


### <a name="built-in-type-references"></a>Riferimenti ai tipi predefiniti

I riferimenti ai tipi predefiniti sono un singolo carattere, usato per fare riferimento a tipi valore predefiniti. Come indicato di seguito è riportato il mapping:

-  `"B"` per `sbyte` .
-  `"S"` per `short` .
-  `"I"` per `int` .
-  `"J"` per `long` .
-  `"F"` per `float` .
-  `"D"` per `double` .
-  `"C"` per `char` .
-  `"Z"` per `bool` .
-  `"V"` per `void` metodo tipi restituiti.


<a name="_Simplified_Type_References_1" />

### <a name="simplified-type-references"></a>Riferimenti ai tipi semplificata

I riferimenti ai tipi semplificata è utilizzabile solo in [JNIEnv.FindClass(string)](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/(System.String)).
Esistono due modi per ottenere un riferimento di tipo semplificata:

1.  Da un nome di linguaggio completo, sostituire ogni `'.'` all'interno del nome di pacchetto e prima del nome del tipo con `'/'` e ogni `'.'` all'interno di un nome di tipo `'$'` .

1.  Leggere l'output di `'unzip -l android.jar | grep JavaName'` .


Uno dei due comporterà il tipo Java [java.lang.Thread.State](https://developer.android.com/reference/java/lang/Thread.State.html) che viene mappato al riferimento al tipo semplificata `java/lang/Thread$State`.


### <a name="type-references"></a>Riferimenti di tipo

Riferimento a un tipo è un riferimento di tipo incorporato o un riferimento di tipo semplificata con un' `'L'` prefisso e un `';'` suffisso. Per il tipo Java [lang](https://developer.android.com/reference/java/lang/String.html), è il riferimento al tipo semplificata `"java/lang/String"`, mentre il riferimento al tipo è `"Ljava/lang/String;"`.

I riferimenti di tipo vengono usati con i riferimenti ai tipi di matrice e con le firme di JNI.

Un altro modo per ottenere un riferimento di tipo è leggendo l'output di `'javap -s -classpath android.jar fully.qualified.Java.Name'`.
A seconda del tipo complesso, è possibile usare una dichiarazione di costruttore o un metodo tipo per determinare il nome JNI. Ad esempio:

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

`Thread.State` è un tipo di enumerazione di Java, quindi è possibile usare la firma del `valueOf` metodo per determinare che il riferimento al tipo è stato$ Ljava/lang/Thread;.



### <a name="array-type-references"></a>Riferimenti ai tipi di matrice

Riferimenti a tipi matrice sono `'['` preceduti da un riferimento di tipo JNI.
Impossibile utilizzare i riferimenti ai tipi semplificata quando si specificano le matrici.

Ad esempio, `int[]` viene `"[I"`, `int[][]` viene `"[[I"`, e `java.lang.Object[]` è `"[Ljava/lang/Object;"`.



## <a name="java-generics-and-type-erasure"></a>I Generics di Java e la cancellazione di tipo

*La maggior parte degli* dei casi, come avviene per JNI, i generics Java *inesistenti*.
Esistono alcuni "rughe", ma tali rughe sono in modalità di interazione di Java con i generics, non con la modalità JNI Cerca e richiama membri generici.

Non vi è alcuna differenza tra un tipo generico o membro e un tipo non generico o un membro quando si interagisce con JNI. Ad esempio, il tipo generico [java.lang.Class&lt;T&gt; ](https://developer.android.com/reference/java/lang/Class.html) è anche il tipo "raw" generico `java.lang.Class`, entrambi con lo stesso riferimento di tipo semplificata, `"java/lang/Class"`.


## <a name="java-native-interface-support"></a>Supporto Java Native Interface

[Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/) è wrapper gestito per il Jave JNI (Native Interface). JNI funzioni vengono dichiarate all'interno di [Java Native Interface Specification](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html), anche se i metodi sono stati modificati per rimuovere l'impostazione esplicita `JNIEnv*` parametro e `IntPtr` viene usato al posto di `jobject`, `jclass`, `jmethodID`e così via. Si consideri, ad esempio, il [JNI NewObject funzione](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp4517):

```csharp
jobject NewObjectA(JNIEnv *env, jclass clazz, jmethodID methodID, jvalue *args);
```

Questa funzionalità è esposta come le [JNIEnv.NewObject](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewObject/p/System.IntPtr/System.IntPtr/Android.Runtime.JValue[]/) metodo:

```csharp
public static IntPtr NewObject(IntPtr clazz, IntPtr jmethod, params JValue[] parms);
```

Conversione tra le due chiamate è piuttosto semplice. In C sono:

```c
jobject CreateMapActivity(JNIEnv *env)
{
    jclass    Map_Class   = (*env)->FindClass(env, "mono/samples/googlemaps/MyMapActivity");
    jmethodID Map_defCtor = (*env)->GetMethodID (env, Map_Class, "<init>", "()V");
    jobject   instance    = (*env)->NewObject (env, Map_Class, Map_defCtor);

    return instance;
}
```

Il C# equivalente sarebbe:

```csharp
IntPtr CreateMapActivity()
{
    IntPtr Map_Class   = JNIEnv.FindClass ("mono/samples/googlemaps/MyMapActivity");
    IntPtr Map_defCtor = JNIEnv.GetMethodID (Map_Class, "<init>", "()V");
    IntPtr instance    = JNIEnv.NewObject (Map_Class, Map_defCtor);

    return instance;
}
```

Dopo aver creato un'istanza di oggetto Java conservata in un elemento IntPtr, è opportuno eseguire un'operazione con esso. È possibile usare, ad esempio metodi JNIEnv [ <span class="external">JNIEnv.CallVoidMethod()</span> ](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallVoidMethod/p/System.IntPtr/System.IntPtr/Android.Runtime.JValue[]/) a tale scopo, ma se è già presente un'analogia C# wrapper, è opportuno creare un wrapper sul riferimento JNI. È possibile farlo tramite il [Extensions.JavaCast <t>()</t> ](https://developer.xamarin.com/api/member/Android.Runtime.Extensions.JavaCast%7BTResult%7D/p/Android.Runtime.IJavaObject/) metodo di estensione:

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = new Java.Lang.Object(lrefActivity, JniHandleOwnership.TransferLocalRef)
    .JavaCast<Activity>();
```

È anche possibile usare la [Java.Lang.Object.GetObject <t>()</t> ](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) metodo:

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = Java.Lang.Object.GetObject<Activity>(lrefActivity, JniHandleOwnership.TransferLocalRef);
```

Inoltre, tutte le funzioni JNI sono state modificate tramite la rimozione di `JNIEnv*` parametro presente in ogni funzione JNI.


## <a name="summary"></a>Riepilogo

Gestire direttamente JNI è un'esperienza terribile da evitare a tutti i costi. Sfortunatamente, non sempre è inevitabile; Questa guida si spera fornirà alcune assistenza quando si raggiunge i case di Java non associati con Mono for Android.


## <a name="related-links"></a>Collegamenti correlati

- [SanityTests (esempio)](https://developer.xamarin.com/samples/SanityTests/)
- [Java Native Interface Specification](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/jniTOC.html)
- [Funzioni Java Native Interface](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)
