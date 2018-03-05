---
title: Utilizzo di JNI
description: "Xamarin consente la scrittura di App Android in c#, invece di Java. Con xamarin vengono forniti diversi assembly che forniscono associazioni per le librerie di linguaggio, tra cui Mono.Android.dll e Mono.Android.GoogleMaps.dll. Tuttavia, le associazioni non disponibili per ogni libreria Java possibili e associazioni forniti non è possono associare ogni tipo di Java e il membro. Per utilizzare i tipi Java e i membri non associato, può essere utilizzato il JNI Java Native Interface (). In questo articolo viene illustrato come utilizzare JNI di interagire con i tipi Java e i membri da applicazioni di xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: A417DEE9-7B7B-4E35-A79C-284739E3838E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: d1c441de089a84c93c251588115abecb19816868
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-jni"></a>Utilizzo di JNI

_Xamarin consente la scrittura di App Android in c#, invece di Java. Con xamarin vengono forniti diversi assembly che forniscono associazioni per le librerie di linguaggio, tra cui Mono.Android.dll e Mono.Android.GoogleMaps.dll. Tuttavia, le associazioni non disponibili per ogni libreria Java possibili e associazioni forniti non è possono associare ogni tipo di Java e il membro. Per utilizzare i tipi Java e i membri non associato, può essere utilizzato il JNI Java Native Interface (). In questo articolo viene illustrato come utilizzare JNI di interagire con i tipi Java e i membri da applicazioni di xamarin._

<a name="_Overview" />

## <a name="overview"></a>Panoramica

Non è sempre possibile creare un gestito Callable Wrapper (MCW) per richiamare il codice Java o necessarie. In molti casi, "inline" JNI è perfettamente accettabile e utile per l'uso d'uso occasionale di membri di linguaggio non associati. È spesso più semplice usare JNI per richiamare un singolo metodo in una classe Java di per generare un'associazione JAR intero.

Xamarin offre il `Mono.Android.dll` assembly, che fornisce un'associazione per di Android `android.jar` libreria. Tipi e membri non presentano all'interno di `Mono.Android.dll` e tipi presentano all'interno di `android.jar` può essere utilizzato da associarli manualmente. Per associare tipi Java e i membri, utilizzare il **Java Native Interface** (**JNI**) per i tipi di ricerca, scrivere e leggere i campi e richiamare i metodi.

L'API di JNI di xamarin è concettualmente molto simile al `System.Reflection` API in .NET: rende possibile la ricerca di tipi e membri in base al nome, leggere e scrivere i valori dei campi, richiamare i metodi e altro ancora. È possibile utilizzare JNI e `Android.Runtime.RegisterAttribute` attributo personalizzato per dichiarare i metodi virtuali che possono essere associati per supportare l'override. È possibile associare le interfacce in modo che possano essere implementati in c#.

Questo documento viene illustrato:

-  Come JNI fa riferimento ai tipi.
-  Modalità di ricerca, lettura e scrittura campi.
-  Come cercare e richiamare i metodi.
-  Come esporre metodi virtuali per consentire l'override dal codice gestito.
-  Modalità di esposizione delle interfacce.


<a name="_Requirements" />

## <a name="requirements"></a>Requisiti

JNI, esposto tramite la [Android.Runtime.JNIEnv dello spazio dei nomi](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/), disponibile in ogni versione di xamarin.
Per associare le interfacce e tipi Java, è necessario usare xamarin 4.0 o versione successiva.

<a name="_Managed_Callable_Wrappers" />

## <a name="managed-callable-wrappers"></a>Callable Wrapper gestito

Oggetto **Callable Wrapper gestiti** (**MCW**) è un *associazione* per una classe Java o un'interfaccia che esegue il wrapping di tutte le macchine JNI in modo che il codice client c# non è necessario preoccuparsi la complessità di JNI. La maggior parte delle `Mono.Android.dll` è costituito da callable wrapper gestito.

Callable wrapper gestito per due scopi:

1.  Incapsulare utilizzare JNI in modo che il codice client non è necessario conoscere la complessità sottostante.
1.  Consentono di classe Sub tipi Java e implementare interfacce Java.

Il primo è esclusivamente per motivi di praticità e l'incapsulamento di complessità in modo che i consumer devono un set di classi da utilizzare semplice e gestito. Questo richiede l'utilizzo dei vari [JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/) membri, come descritto più avanti in questo articolo. Tenere presente che callable wrapper gestiti non sono strettamente necessarie &ndash; "inline" JNI utilizzare è perfettamente accettabile ed è utile per l'uso d'uso occasionale di membri di linguaggio non associati. Sottoclasse e implementazione di interfacce richiede l'utilizzo di callable wrapper gestito.


<a name="_Android_Callable_Wrappers" />

## <a name="android-callable-wrappers"></a>Android Callable Wrapper

Android callable wrapper (ACW) sono necessari ogni volta che il runtime Android (ART) deve richiamare codice non gestito. questi wrapper sono necessari poiché non è possibile registrare le classi di grafica in fase di esecuzione.
(In particolare, il [DefineClass](http://docs.oracle.com/javase/6/docs/technotes/guides/jni/spec/functions.html#wp15986) JNI funzione non è supportata dal runtime di Android. Android callable wrapper pertanto costituiscono la mancanza di supporto di registrazione di tipo di runtime.)

Ogni volta codice Android è necessario eseguire virtuale o l'interfaccia di metodo che viene sottoposto a override o implementato nel codice gestito, xamarin deve fornire un proxy di Java in modo che questo metodo ottiene inviato al tipo gestito appropriato. Questi tipi di proxy di Java sono codice Java con la classe di base "stesso" e di un elenco delle interfacce Java come tipo gestito, i costruttori stessi implementazione e la dichiarazione di qualsiasi classe di base sottoposto a override e i metodi di interfaccia.

Android callable wrapper generati dal **monodroid.exe** programma durante la [processo di compilazione](~/android/deploy-test/building-apps/build-process.md)e vengono generati per tutti i tipi che ereditano (direttamente o indirettamente) [ Lang](https://developer.xamarin.com/api/type/Java.Lang.Object/).


<a name="_Implementing_Interfaces" />

### <a name="implementing-interfaces"></a>Implementazione di interfacce

Talvolta, quando è necessario implementare un'interfaccia, Android (ad esempio [Android.Content.IComponentCallbacks](https://developer.xamarin.com/api/type/Android.Content.IComponentCallbacks/)).

Tutte le interfacce e le classi Android estendono il [Android.Runtime.IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) interfaccia; pertanto, è necessario implementare tutti i tipi di Android `IJavaObject`.
Xamarin sfrutta il fatto &ndash; Usa `IJavaObject` per fornire Android con un proxy di Java (Android callable wrapper) per il tipo gestito specificato. Poiché **monodroid.exe** Cerca solo `Java.Lang.Object` sottoclassi (che deve implementare `IJavaObject`), la creazione di sottoclassi `Java.Lang.Object` offre un modo per implementare le interfacce nel codice gestito. Ad esempio:

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

<a name="_Implementation_Details" />

### <a name="implementation-details"></a>Dettagli di implementazione

*Il resto di questo articolo fornisce i dettagli di implementazione soggette a modifiche senza preavviso* (e di seguito vengono forniti solo perché gli sviluppatori possono essere conoscere ciò che accade dietro le quinte).

Ad esempio, dato il seguente codice sorgente c#:

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

Il **mandroid.exe** verranno generati il Wrapper RCW Android seguenti:

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

Si noti che la classe di base viene mantenuta e le dichiarazioni di metodo nativo sono disponibili per ogni metodo che viene eseguito l'override all'interno di codice gestito.


<a name="_ExportAttribute_and_ExportFieldAttribute" />

### <a name="exportattribute-and-exportfieldattribute"></a>ExportAttribute ed ExportFieldAttribute

In genere, xamarin genera automaticamente il codice Java che comprende il ACW; Questa generazione è basata sui nomi di classe e metodo quando una classe deriva da una classe Java ed esegue l'override di metodi Java esistenti. Tuttavia, in alcuni scenari, la generazione di codice non è sufficiente, come descritto di seguito:

-   Supporto Android azione nomi negli attributi xml layout, ad esempio il [android: onClick](https://developer.xamarin.com/api/member/Android.Views.View+IOnClickListener.OnClick/p/Android.Views.View/) attributo XML. Quando è specificato, l'istanza di visualizzazione ingrandita provare a cercare il metodo Java.

-   Il [Java.IO. Serializable](http://developer.android.com/reference/java/io/Serializable.html) interfaccia richiede `readObject` e `writeObject` metodi. Poiché non sono membri di questa interfaccia, l'implementazione gestito corrispondente non espone questi metodi per il codice Java.

-   Il [android.os.Parcelable](https://developer.xamarin.com/api/type/Android.Os.Parcelable/) interfaccia prevede che una classe di implementazione deve avere un campo statico `CREATOR` di tipo `Parcelable.Creator`. Il codice Java generato richiede un campo esplicito. Con questo scenario standard, non è al campo di output nel codice Java da codice gestito.


Poiché la generazione di codice non fornisce una soluzione per generare metodi Java arbitrari con nomi arbitrari, a partire da xamarin 4.2, il [ExportAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute/) e [ExportFieldAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportFieldAttribute/) sono stati introdotto per offrire una soluzione per scenari precedenti. Entrambi gli attributi si trovano nel `Java.Interop` dello spazio dei nomi:

-   `ExportAttribute` &ndash; Specifica un nome di metodo e i relativi tipi di eccezione prevista (per fornire l'esplicita "genera" Java). Quando viene utilizzata su un metodo, il metodo "Esporta" un metodo Java che genera un codice di invio alla chiamata JNI corrispondente al metodo gestito. Può essere usato con `android:onClick` e `java.io.Serializable`.

-   `ExportFieldAttribute` &ndash; Specifica un nome di campo. Si trova su un metodo che funziona come un inizializzatore di campo. Può essere usato con `android.os.Parcelable`.

Il [ExportAttribute](https://developer.xamarin.com/samples/monodroid/ExportAttribute/) progetto di esempio viene illustrato come utilizzare questi attributi.

<a name="_Troubleshooting_ExportAttribute_and_ExportFieldAttribute" />

#### <a name="troubleshooting-exportattribute-and-exportfieldattribute"></a>Risoluzione dei problemi ExportAttribute ed ExportFieldAttribute

-   Creazione del pacchetto si verifica un errore causato dalla mancanza **Mono.Android.Export.dll** &ndash; se è stato utilizzato `ExportAttribute` o `ExportFieldAttribute` alcuni metodi nel codice o le librerie dipendenti, è necessario aggiungere  **Mono.Android.Export.dll**. L'assembly è isolato per supportare il codice di callback da Java. È separato dal **Mono.Android.dll** durante l'aggiunta di altre dimensioni per l'applicazione.

-   Nelle build di rilascio, `MissingMethodException` si verifica per i metodi di esportazione &ndash; build di rilascio In `MissingMethodException` si verifica per i metodi di esportazione. (Questo problema viene risolto nella versione più recente di xamarin).


<a name="_ExportParameterAttribute" />

### <a name="exportparameterattribute"></a>ExportParameterAttribute

`ExportAttribute` e `ExportFieldAttribute` forniscono funzionalità di tale linguaggio di codice in fase di esecuzione è possibile utilizzare. Questo codice in fase di esecuzione accede a codice gestito tramite i metodi JNI generati dovuti a tali attributi. Di conseguenza, non è disponibile alcun metodo Java esistente associa il metodo gestito; di conseguenza, il metodo Java viene generato da una firma del metodo gestito.

In questo caso, tuttavia, non è completamente determinante. In particolare, questo vale in alcuni avanzate mapping tra tipi gestiti e i tipi di linguaggio, ad esempio:

-  InputStream
-  OutputStream
-  XmlPullParser
-  XmlResourceParser

Se per i metodi esportati, sono necessari come i seguenti tipi del `ExportParameterAttribute` deve essere utilizzata per assegnare al parametro corrispondente in modo esplicito o un tipo di valore restituito.

 <a name="_Annotation_Attribute" />


### <a name="annotation-attribute"></a>Attributo di annotazione

Xamarin 4.2, abbiamo convertito `IAnnotation` i tipi di implementazione in attributi (Attribute) e il supporto aggiunto per la generazione di annotazione in wrapper Java.

Ciò significa che le modifiche direzionale seguenti:

-   Il generatore di associazione genera `Java.Lang.DeprecatedAttribute` da `java.Lang.Deprecated` (mentre dovrebbe essere `[Obsolete]` nel codice gestito).

-   Ciò non significa esistenti `Java.Lang.Deprecated` classe problema viene risolto. Questi oggetti basati su Java potrebbero essere ancora utilizzati come normali oggetti Java (se è presente tale utilizzo). Esistere `Deprecated` e `DeprecatedAttribute` classi.

-   Il `Java.Lang.DeprecatedAttribute` classe è contrassegnata come `[Annotation]` . Quando è presente un attributo personalizzato ereditato da questo `[Annotation]` attributo, msbuild (attività) genererà un'annotazione di Java per l'attributo personalizzato (@Deprecated) nei Android Callable Wrapper (ACW).

-   Le annotazioni è stato possibile generare in classi, metodi e campi (che è un metodo nel codice gestito) esportato.

Se la classe contenitore (la classe con annotazioni o la classe che contiene i membri con annotazioni) non è registrata, l'intera origine della classe Java non viene generato, incluse le annotazioni. Per i metodi, è possibile specificare il `ExportAttribute` per ottenere il metodo generato e annotato in modo esplicito. Inoltre, non è una funzionalità per una definizione di classe Java annotazione "generazione". In altre parole, se si definisce un attributo personalizzato gestito per un determinato annotazione, sarà necessario aggiungere un'altra libreria JAR contenente la classe di annotazione Java corrispondente. Aggiunta di un file di origine Java che definisce il tipo di annotazione non è sufficiente. Il compilatore Java non funziona in modo analogo **apt**.

Inoltre, si applicano le limitazioni seguenti:

-   Questo processo di conversione non viene considerato `@Target` finora l'annotazione del tipo di annotazione.

-   Gli attributi in una proprietà non funziona. Utilizzare gli attributi per la proprietà getter o setter.


<a name="_Class_Binding" />

## <a name="class-binding"></a>Associazione di classe

Associazione di una classe significa che la scrittura di un callable wrapper gestito per semplificare la chiamata del tipo sottostante di Java.

Associazione di metodi astratti e virtuali per consentire l'override di c# richiede xamarin 4.0. Tuttavia, qualsiasi versione di xamarin possibile associare metodi non virtuali, i metodi statici o metodi virtuali senza il supporto di sostituzioni.

In genere, un'associazione contiene gli elementi seguenti:

-  Oggetto [JNI handle per il tipo Java associato](#_Looking_up_Java_Types).

-  [JNI campo ID e le proprietà per ogni campo associato](#_Instance_Fields).

-  [JNI metodo ID e i metodi per ogni associazione metodo](#_Instance_Methods).

-  Se l'inserimento è obbligatorio, il tipo deve avere un [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) la dichiarazione di tipo con attributo personalizzato [RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) impostato su `true`.


<a name="_Declaring_Type_Handle" />

### <a name="declaring-type-handle"></a>Handle del tipo dichiarante

I metodi di ricerca di campo e metodo richiedono un riferimento all'oggetto che fa riferimento al tipo dichiarante. Per convenzione, questo viene mantenuto un `class_ref` campo:

```csharp
static IntPtr class_ref = JNIEnv.FindClass(CLASS);
```

Vedere il [riferimenti al tipo JNI](#_JNI_Type_References) sezione per informazioni dettagliate sul `CLASS` token.

<a name="_Binding_Fields" />

### <a name="binding-fields"></a>Associazione di campi

I campi di Java vengono esposti come proprietà di c#, ad esempio il campo Java [java.lang.System.in](http://developer.android.com/reference/java/lang/System.html#in) è associato come proprietà c# [Java.Lang.JavaSystem.In](https://developer.xamarin.com/api/property/Java.Lang.JavaSystem.In/).
Inoltre, poiché JNI distingue tra i campi statici e i campi di istanza, diversi metodi usare quando si implementa la proprietà.

Campo binding comporta tre set di metodi:

1.  Il *ottenere id campo* metodo. Il *ottenere id campo* metodo è responsabile della restituzione di un campo handle il *ottenere il valore campo* e *impostare il valore di campo* metodi utilizzeranno. Ottenere l'id del campo, è necessario conoscere la dichiarazione di tipo, il nome del campo e [la firma del tipo JNI](#_JNI_Type_Signatures) del campo.

1.  Il *ottenere il valore campo* metodi. Questi metodi richiedono l'handle del campo e sono responsabili per leggere il valore del campo da Java.
    Il metodo da utilizzare dipende dal tipo del campo.

1.  Il *impostare il valore di campo* metodi. Questi metodi richiedono l'handle del campo e sono responsabili per scrivere il valore del campo all'interno di Java. Il metodo da utilizzare dipende dal tipo del campo.


 [I campi statici](#_Static_Fields) utilizzare il [JNIEnv.GetStaticFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/), `JNIEnv.GetStatic*Field`, e [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/) metodi.

 [Campi di istanza](#_Instance_Fields) utilizzare il [JNIEnv.GetFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFieldID/), `JNIEnv.Get*Field`, e [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/) metodi.

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

Nota: Utilizziamo [InputStreamInvoker.FromJniHandle](https://developer.xamarin.com/api/member/Android.Runtime.InputStreamInvoker.FromJniHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) per convertire il riferimento JNI in un `System.IO.Stream` istanza ed è usata `JniHandleOwnership.TransferLocalRef` perché [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/) Restituisce un riferimento locale.

Molte del [Android.Runtime](https://developer.xamarin.com/api/namespace/Android.Runtime/) tipi hanno `FromJniHandle` riferimento per i metodi che convertirà un JNI nel tipo desiderato.

 <a name="_Method_Binding" />


### <a name="method-binding"></a>Associazione (metodo)

Metodi di Java vengono esposte come metodi di c# e come proprietà in c#. Ad esempio, il metodo Java [java.lang.Runtime.runFinalizersOnExit](http://developer.android.com/reference/java/lang/Runtime.html#runFinalizersOnExit(boolean)) metodo viene associato come il [Java.Lang.Runtime.RunFinalizersOnExit](https://developer.xamarin.com/api/member/Java.Lang.Runtime.RunFinalizersOnExit/) (metodo) e [java.lang.Object.getClass ](http://developer.android.com/reference/java/lang/Object.html#getClass) metodo viene associato come il [Java.Lang.Object.Class](https://developer.xamarin.com/api/property/Java.Lang.Object.Class/) proprietà.

Chiamata del metodo è un processo in due passaggi:

1.  Il *ottenere l'ID del metodo* per il metodo da richiamare. Il *ottenere l'ID del metodo* metodo è responsabile della restituzione di un handle che utilizzeranno i metodi di chiamata del metodo. Ottenere l'id (metodo), è necessario conoscere la dichiarazione di tipo, il nome del metodo e [la firma del tipo JNI](#_JNI_Type_Signatures) del metodo.

1.  Richiamare il metodo.

Come con i campi, i metodi da utilizzare per ottenere l'id di metodo e richiamare il metodo diversi metodi statici e i metodi di istanza.

[I metodi statici](#_Static_Methods_1) utilizzare [JNIEnv.GetStaticMethodID()](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/) per cercare l'id di metodo e utilizzare il `JNIEnv.CallStatic*Method` famiglia di metodi per la chiamata.

[Metodi di istanza](#_Instance_Methods) utilizzare [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/) per cercare l'id di metodo e utilizzare il `JNIEnv.Call*Method` e `JNIEnv.CallNonvirtual*Method` famiglie di metodi per la chiamata.

Associazione di metodo è potenzialmente più di un semplice chiamata al metodo. Associazione (metodo), inoltre, include un metodo da sottoporre a override (per i metodi astratti e non finale) consentendo o implementato (per i metodi di interfaccia). Il [supportano l'ereditarietà, interfacce](#_Supporting_Inheritance,_Interfaces_1) sezione vengono illustrate le complessità di supportare metodi virtuali e i metodi di interfaccia.

 <a name="_Static_Methods" />


#### <a name="static-methods"></a>Metodi statici

Associazione di un metodo statico prevede l'uso `JNIEnv.GetStaticMethodID` per ottenere un handle (metodo), quindi utilizzando il metodo appropriato `JNIEnv.CallStatic*Method` (metodo), in base al tipo restituito del metodo. Di seguito è riportato un esempio di un'associazione per il [Runtime.getRuntime](http://developer.android.com/reference/java/lang/Runtime.html#getRuntime()) metodo:

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

Si noti che l'handle del metodo, viene memorizzato in un campo statico, `id_getRuntime`. Si tratta di un'ottimizzazione delle prestazioni, in modo che l'handle del metodo non deve necessariamente essere cercati in ogni chiamata. Non è necessario memorizzare nella cache l'handle del metodo in questo modo. Dopo aver ottenuto l'handle del metodo, [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) viene utilizzato per richiamare il metodo. `JNIEnv.CallStaticObjectMethod` Restituisce un `IntPtr` che contiene l'handle dell'istanza di Java restituita.
[Java.Lang.Object.GetObject&lt;T&gt;(IntPtr, JniHandleOwnership)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) viene usata per convertire l'handle di Java in un'istanza di oggetto fortemente tipizzato.

 <a name="_Non-virtual_Instance_Method_Binding" />


#### <a name="non-virtual-instance-method-binding"></a>Associazione di metodo di istanza non virtuale

Associazione di un `final` metodo di istanza o un metodo di istanza che non richiede l'override, comporta l'utilizzo `JNIEnv.GetMethodID` per ottenere un handle (metodo), quindi utilizzando il metodo appropriato `JNIEnv.Call*Method` (metodo), in base al tipo restituito del metodo. Di seguito è riportato un esempio di un'associazione per il `Object.Class` proprietà:

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

Si noti che l'handle del metodo, viene memorizzato in un campo statico, `id_getClass`.
Si tratta di un'ottimizzazione delle prestazioni, in modo che l'handle del metodo non deve necessariamente essere cercati in ogni chiamata. Non è necessario memorizzare nella cache l'handle del metodo in questo modo. Dopo aver ottenuto l'handle del metodo, [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) viene utilizzato per richiamare il metodo. `JNIEnv.CallStaticObjectMethod` Restituisce un `IntPtr` che contiene l'handle dell'istanza di Java restituita.
[Java.Lang.Object.GetObject&lt;T&gt;(IntPtr, JniHandleOwnership)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) viene usata per convertire l'handle di Java in un'istanza di oggetto fortemente tipizzato.

<a name="_Binding_Constructors" />

### <a name="binding-constructors"></a>Costruttori di associazione

I costruttori sono metodi di Java con il nome `"<init>"`. Come con i metodi di istanza Java, `JNIEnv.GetMethodID` viene utilizzato per cercare l'handle del costruttore. A differenza dei metodi di Java, il [JNIEnv.NewObject](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewObject/) metodi usati per richiamare l'handle di metodo del costruttore. Il valore restituito di `JNIEnv.NewObject` è un riferimento locale JNI:


```csharp
int value = 42;
IntPtr class_ref    = JNIEnv.FindClass ("java/lang/Integer");
IntPtr id_ctor_I    = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
IntPtr lrefInstance = JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value));
// Dispose of lrefInstance, class_ref…
```

In genere un'associazione di classe verrà sottoclasse [lang](https://developer.xamarin.com/api/type/Java.Lang.Object/).
Quando si crea una sottoclasse `Java.Lang.Object`, un aggiuntive semantica a entra in gioco: un `Java.Lang.Object` istanza mantiene un riferimento a un'istanza di Java tramite globale di `Java.Lang.Object.Handle` proprietà.

1.  Il `Java.Lang.Object` costruttore predefinito verrà allocata un'istanza di Java.

1.  Se il tipo ha un `RegisterAttribute` , e `RegisterAttribute.DoNotGenerateAcw` è `true` , quindi un'istanza di `RegisterAttribute.Name` tipo viene creato tramite il costruttore predefinito.

1.  In caso contrario, il [Android Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) corrispondente a `this.GetType` viene creata un'istanza tramite il costruttore predefinito. Android Callable Wrapper generati durante la creazione del pacchetto per ogni `Java.Lang.Object` sottoclasse per cui `RegisterAttribute.DoNotGenerateAcw` non è impostata su `true`.

Per i tipi che rappresentano non classe associazioni, ciò è previsto semantica: creazione di un `Mono.Samples.HelloWorld.HelloAndroid` istanza c# è necessario costruire un linguaggio `mono.samples.helloworld.HelloAndroid` istanza che è un Wrapper RCW Android generato.

Per le associazioni di classe, è possibile il comportamento corretto se il tipo Java contiene un costruttore predefinito e/o nessun altro costruttore deve essere richiamato. In caso contrario, è necessario fornire un costruttore che esegue le azioni seguenti:

1.  Richiamare il [lang (IntPtr, JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) anziché il valore predefinito `Java.Lang.Object` costruttore. Questa operazione è necessaria per evitare di creare una nuova istanza di Java.

1.  Controllare il valore di [Java.Lang.Object.Handle](https://developer.xamarin.com/api/property/Java.Lang.Object.Handle/) prima di creare tutte le istanze di Java. Il `Object.Handle` proprietà avrà un valore diverso da `IntPtr.Zero` se è stato costruito un Android Callable Wrapper in codice Java e il binding di classe viene creato per contenere l'istanza creata di Android Callable Wrapper. Ad esempio, quando crea Android un `mono.samples.helloworld.HelloAndroid` istanza, il primo e il linguaggio verrà creato il Wrapper RCW Android `HelloAndroid` costruttore verrà creata un'istanza dell'oggetto corrispondente `Mono.Samples.HelloWorld.HelloAndroid` tipo, con il `Object.Handle` proprietà viene impostata per l'istanza di Java prima dell'esecuzione del costruttore.

1.  Se il tipo di runtime corrente non è lo stesso come la dichiarazione di tipo, quindi un'istanza del Android Callable Wrapper corrispondente è necessario creare e utilizzare [Object.SetHandle](https://developer.xamarin.com/api/member/Java.Lang.Object.SetHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) per archiviare l'handle restituito da [ JNIEnv.CreateInstance](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CreateInstance/).

1.  Se il tipo di runtime corrente è identico al tipo dichiarante, quindi richiamare il costruttore di Java e utilizzare [Object.SetHandle](https://developer.xamarin.com/api/member/Java.Lang.Object.SetHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) per archiviare l'handle restituito da `JNIEnv.NewInstance` .


Si consideri ad esempio il [java.lang.Integer(int)](http://developer.android.com/reference/java/lang/Integer.html#Integer(int)) costruttore. L'oggetto è associato come:

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

 <a name="_Supporting_Inheritance,_Interfaces" />


### <a name="supporting-inheritance-interfaces"></a>Supporto dell'ereditarietà, interfacce

Crea una sottoclasse di un tipo Java o l'implementazione di un'interfaccia Java richiede la generazione di [Android Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md) (ACWs) che vengono generati per ogni `Java.Lang.Object` sottoclasse durante il processo di creazione del pacchetto. Generazione ACW viene controllata tramite il [Android.Runtime.RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) attributo personalizzato.

Per i tipi c#, la `[Register]` costruttore di attributo personalizzato richiede un argomento: il [JNI semplificato riferimento al tipo](#_Simplified_Type_References_1) per il linguaggio corrispondente digitare. Ciò consente di fornire nomi diversi tra Java e c#.

Prima di xamarin 4.0, il `[Register]` attributo personalizzato non è disponibile in "alias" tipi Java esistenti. In questo modo il processo di generazione ACW genererebbero ACWs per ogni `Java.Lang.Object` sottoclasse rilevato.

Xamarin 4.0 ha introdotto la [RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) proprietà. Questa proprietà indica al processo di generazione ACW di *ignorare* il tipo con annotazioni, consentendo la dichiarazione di nuovi gestiti Callable Wrapper che non si tradurrà in ACWs viene generato al momento della creazione del pacchetto. In questo modo i tipi Java associazione esistenti. Ad esempio, considerare la seguente classe Java semplice, `Adder`, che contiene un metodo, `add`, che aggiunge a numeri interi e restituisce il risultato:

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

In questo caso, il `Adder` tipo c# *alias* il `Adder` tipo Java. Il `[Register]` attributo viene utilizzato per specificare il nome JNI del `mono.android.test.Adder` tipo Java e `DoNotGenerateAcw` proprietà viene utilizzata per impedire la generazione di ACW. Ciò comporterà la generazione di un ACW per il `ManagedAdder` tipo, che in modo corretto le sottoclassi di `mono.android.test.Adder` tipo. Se il `RegisterAttribute.DoNotGenerateAcw` proprietà non era stata utilizzata, quindi il processo di compilazione xamarin sarebbe stato generato un nuovo `mono.android.test.Adder` tipo Java. Questa operazione potrebbe causare errori di compilazione, come il `mono.android.test.Adder` tipo può essere incluso due volte, in due file diversi.

 <a name="_Binding_Virtual_Methods" />


### <a name="binding-virtual-methods"></a>Associazione di metodi virtuali

`ManagedAdder` le sottoclassi di Java `Adder` tipo, ma non è particolarmente interessante: c# `Adder` tipo non definisce metodi virtuali, pertanto `ManagedAdder` non è possibile eseguire l'override di qualsiasi elemento.

Associazione `virtual` metodi per consentire l'override dalle sottoclassi richiede alcune operazioni che devono essere eseguite che rientrano in due categorie seguenti:

1.  **Associazione (metodo)**

1.  **Registrazione (metodo)**

<a name="_Method_Binding" />

#### <a name="method-binding"></a>Associazione (metodo)

Un'associazione di metodo richiede l'aggiunta di due membri di supporto per c# `Adder` definizione: `ThresholdType`, e `ThresholdClass`.

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

`ThresholdType` viene utilizzato nell'associazione di metodo per determinare quando è necessario eseguire virtuale e invio di metodi non virtuali. Deve sempre restituire un `System.Type` istanza che corrisponde al tipo dichiarante di c#.

##### <a name="thresholdclass"></a>ThresholdClass

Il `ThresholdClass` proprietà restituisce il riferimento alla classe JNI per il tipo di binding:

```csharp
partial class Adder {
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```

`ThresholdClass` viene utilizzato nel metodo di associazione durante la chiamata di metodi non virtuali.

#### <a name="binding-implementation"></a>Implementazione dell'associazione

L'implementazione del metodo dell'associazione è responsabile per la chiamata del metodo di Java runtime. Contiene inoltre un `[Register]` dichiarazione di attributo personalizzato che fa parte della registrazione (metodo) e verrà descritta nella sezione relativa alla registrazione di metodo:

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

Il `id_add` campo contiene l'ID del metodo per il metodo Java da richiamare. Il `id_add` valore ottenuto da `JNIEnv.GetMethodID`, che richiede la dichiarazione di classe (`class_ref`), il nome del metodo Java (`"add"`) e la firma del metodo di JNI (`"(II)I"`).

Dopo aver ottenuto l'ID del metodo, `GetType` viene confrontato con `ThresholdType` per determinare se è necessario dispatch virtuali o non virtuale. Invio virtuale è obbligatorio quando `GetType` corrisponde `ThresholdType`, come `Handle` possono fare riferimento a una sottoclasse allocata Java che esegue l'override del metodo.

Quando `GetType` non corrisponde a `ThresholdType`, `Adder` è stato sottoclassato (ad esempio, per `ManagedAdder`) e `Adder.Add` implementazione verrà richiamata solo se la sottoclasse richiamato `base.Add`. Questo è il caso di invio non virtuale, ovvero dove `ThresholdClass` è disponibile in. `ThresholdClass` Specifica la classe Java fornirà l'implementazione del metodo da richiamare.

 <a name="_Method_Registration" />


#### <a name="method-registration"></a>Registrazione (metodo)

Si supponga che una versione aggiornata `ManagedAdder` definizione che esegue l'override di `Adder.Add` metodo:

```csharp
partial class ManagedAdder : Adder {
    public override int Add (int a, int b) {
        return (a*2) + (b*2);
    }
}
```

Tenere presente che `Adder.Add` aveva un `[Register]` attributo personalizzato:

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
```

Il `[Register]` costruttore di attributo personalizzato accetta tre valori:

1.  Il nome del metodo Java `"add"` in questo caso.

1.  La firma del metodo, tipo di JNI `"(II)I"` in questo caso.

1.  Il *metodo connettore* , `GetAddHandler` in questo caso.
    Metodi di connettore verranno descritta più avanti.


I primi due parametri consentono al processo di generazione ACW di generare una dichiarazione di metodo per eseguire l'override del metodo. La risultante ACW dovrebbe contenere una parte del codice seguente:

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

Si noti che un `@Override` metodo viene dichiarato che delega a un `n_`-metodo con lo stesso nome di prefisso. Assicurarsi che quando si richiama il codice Java `ManagedAdder.add`, `ManagedAdder.n_add` verrà richiamato, in modo da consentire l'override di c# `ManagedAdder.Add` metodo da eseguire.

Di conseguenza, la domanda più importante: come è `ManagedAdder.n_add` collegato al `ManagedAdder.Add`?

Java `native` metodi registrati con il runtime Java (runtime Android) tramite il [funzione JNI RegisterNatives](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp17734).
`RegisterNatives` accetta una matrice di strutture contenente il nome del metodo Java, la firma di tipo JNI e un puntatore a funzione da richiamare che segue [JNI convenzione di chiamata](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/design.html#wp715).
Puntatore a funzione deve essere una funzione che accetta due argomenti di puntatore, aggiungendo i parametri del metodo. Il linguaggio `ManagedAdder.n_add` metodo deve essere implementato tramite una funzione che ha il seguente prototipo C:

```csharp
int FunctionName(JNIEnv *env, jobject this, int a, int b)
```

Xamarin non espone un `RegisterNatives` metodo. Al contrario, il ACW e il MCW insieme forniscono le informazioni necessarie per richiamare `RegisterNatives`: il ACW contiene il nome del metodo e la firma di tipo JNI, l'unica cosa mancante è un puntatore a funzione collegare.

Questa opzione è quando il *metodo connettore* è disponibile in. Il terzo `[Register]` parametro di attributo personalizzato è il nome di un metodo definito nel tipo registrato o di una classe di base del tipo registrato che non accetta parametri e restituisce un `System.Delegate`. L'oggetto restituito `System.Delegate` a sua volta fa riferimento a un metodo che presenta la firma della funzione JNI corretta. Infine, il delegato restituito dal metodo connettore *deve* essere sempre in modo che il Garbage Collector non raccoglie, come il delegato viene fornito per Java.

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

Il `GetAddHandler` metodo crea un `Func<IntPtr, IntPtr, int, int,
int>` delegato che fa riferimento al `n_Add` (metodo), quindi richiama [JNINativeWrapper.CreateDelegate](https://developer.xamarin.com/api/member/Android.Runtime.JNINativeWrapper.CreateDelegate/).
`JNINativeWrapper.CreateDelegate` include il metodo specificato in un blocco try/catch, in modo che eventuali eccezioni non gestite vengono gestite e comporterà la generazione di [AndroidEvent.UnhandledExceptionRaiser](https://developer.xamarin.com/api/event/Android.Runtime.AndroidEnvironment.UnhandledExceptionRaiser/) evento. Il delegato risultante viene archiviato in statica `cb_add` variabile in modo che il Garbage Collector non libera il delegato.

Infine, il `n_Add` metodo è responsabile per il marshalling dei parametri JNI di tipi gestiti corrispondenti, quindi chiamare il metodo di delega.

Nota: Utilizzare sempre `JniHandleOwnership.DoNotTransfer` durante il recupero di un MCW su un'istanza di Java. Trattandoli come un riferimento locale (e pertanto la chiamata `JNIEnv.DeleteLocalRef`) interromperà gestiti -&gt; Java -&gt; gestiti transizioni dello stack.

 <a name="_Complete_Adder_Binding" />


### <a name="complete-adder-binding"></a>Completare l'associazione di metodi Add

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

 <a name="_Restrictions" />


### <a name="restrictions"></a>Restrizioni

Durante la scrittura di un tipo che corrisponde ai criteri seguenti:

1.  Sottoclassi `Java.Lang.Object`

1.  È un `[Register]` attributo personalizzato

1.  `RegisterAttribute.DoNotGenerateAcw` è `true`


Quindi per il tipo di interazione di GC *non devono* hanno tutti i campi che possono fare riferimento a un `Java.Lang.Object` o `Java.Lang.Object` sottoclasse in fase di esecuzione. Ad esempio, i campi di tipo `System.Object` e qualsiasi tipo di interfaccia non sono consentiti. I tipi che non possono fare riferimento a `Java.Lang.Object` sono consentite le istanze, ad esempio `System.String` e `List<int>`. Questa restrizione consiste nell'impedire la raccolta di oggetti imprevista per il Garbage Collector.

Se il tipo deve contenere un campo di istanza che può fare riferimento a un `Java.Lang.Object` istanza, il tipo di campo deve essere `System.WeakReference` o `GCHandle`.


 <a name="_Binding_Abstract_Methods" />

## <a name="binding-abstract-methods"></a>Associazione di metodi astratti

Associazione `abstract` metodi è in gran parte identico all'associazione di metodi virtuali. Sono disponibili solo due differenze:

1.  Il metodo astratto è astratto. Ancora mantiene il `[Register]` attributo e la registrazione del metodo associato, il metodo di associazione viene semplicemente spostato il `Invoker` tipo.

1.  Non - `abstract` `Invoker` creazione che crea una sottoclasse del tipo tipo astratto. Il `Invoker` tipo deve eseguire l'override di metodi tutti astratti dichiarati nella classe base e l'implementazione sottoposta a override è l'implementazione del metodo di associazione, ma nel caso di invio non virtuale può essere ignorato.


Ad esempio, si supponga che la precedente `mono.android.test.Adder.add` metodo `abstract`. L'associazione c# comporterebbe la modifica in modo che `Adder.Add` erano astratta e un nuovo `AdderInvoker` tipo deve essere definito che implementata `Adder.Add`:

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

 <a name="_Binding_Interfaces" />

## <a name="binding-interfaces"></a>Interfacce di associazione

Interfacce di associazione sono concettualmente simile al binding di classi che contengono metodi virtuali, ma molte delle specifiche presentano differenze minime (e non minima). Tenere presente quanto segue [dichiarazione di interfaccia Java](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/Adder.java#L14):

```csharp
public interface Progress {
    void onAdd(int[] values, int currentIndex, int currentSum);
}
```

Associazioni di interfaccia dispongono di due parti: la definizione dell'interfaccia in c# e una definizione di Invoker per l'interfaccia.


<a name="_Interface_Definition" />

### <a name="interface-definition"></a>Definizione di interfaccia

La definizione dell'interfaccia in c# è necessario soddisfare i requisiti seguenti:

-   La definizione dell'interfaccia deve avere un `[Register]` attributo personalizzato.

-   La definizione dell'interfaccia è necessario estendere il `IJavaObject interface`.
    In caso contrario, non potrà ACWs che eredita dall'interfaccia Java.

-   Ogni metodo di interfaccia deve contenere un `[Register]` attributo che specifica il nome del metodo Java corrispondente, la firma JNI e il metodo del connettore.

-   Il metodo connettore deve inoltre specificare il tipo che è possibile individuare il metodo del connettore.

Quando si associano `abstract` e `virtual` metodi, il metodo di connettore verrebbero eseguita una ricerca all'interno della gerarchia di ereditarietà del tipo da registrare. Le interfacce non possono disporre di alcun metodo che contiene gli organismi, in modo il problema persiste, pertanto il requisito che è possibile specificare un tipo che indica dove si trova il metodo di connettore. Il tipo è specificato all'interno della stringa di metodo connettore, dopo i due punti `':'`, e deve essere il nome di tipo completo dell'assembly del tipo che contiene l'invoker.

Le dichiarazioni di metodo di interfaccia sono una traduzione dell'oggetto corrispondente metodo Java utilizzando *compatibile* tipi. Per i tipi di Java builtin, i tipi compatibili sono corrispondenti tipi in c#, Java, ad esempio `int` c# `int`. Per i tipi di riferimento, il tipo compatibile è un tipo che può fornire un handle JNI del tipo di linguaggio appropriato.

I membri di interfaccia non verranno richiamati direttamente Java &ndash; chiamata verrà avvenga tramite il tipo Invoker &ndash; in modo che è consentito un livello di flessibilità.

L'interfaccia Java lo stato di avanzamento può essere [dichiarato in c# come](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L83):

```csharp
[Register ("mono/android/test/Adder$Progress", DoNotGenerateAcw=true)]
public interface IAdderProgress : IJavaObject {
    [Register ("onAdd", "([III)V",
            "GetOnAddHandler:Mono.Samples.SanityTests.IAdderProgressInvoker, SanityTests, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null")]
    void OnAdd (JavaArray<int> values, int currentIndex, int currentSum);
}
```

Si noti che in precedenti che è il mapping di Java `int[]` parametro a un [JavaArray&lt;int&gt;](https://developer.xamarin.com/api/type/Android.Runtime.JavaArray%601/).
Questo non è necessario: è stato possibile stato associato a c# `int[]`, o un `IList<int>`, o un altro completamente. Indipendentemente dal tipo scelto, il `Invoker` deve essere in grado di convertirla in un linguaggio `int[]` tipo per la chiamata.

<a name="_Invoker_Definition" />

### <a name="invoker-definition"></a>Invoker definizione

Il `Invoker` definizione di tipo deve ereditare `Java.Lang.Object`, implementare l'interfaccia appropriata e fornire tutti i metodi di connessione a cui fa riferimento nella definizione dell'interfaccia. È un suggerimento più che differisce da un'associazione di classe: il `class_ref` ID campo e il metodo deve essere membri di istanza, i membri non statici.

Membri di istanza di preferenza per il motivo è legato al `JNIEnv.GetMethodID` comportamento nel runtime di Android. (È possibile anche il comportamento di Java; non è stato testato). `JNIEnv.GetMethodID` restituisce null per la ricerca di un metodo che proviene da un'interfaccia implementata e non l'interfaccia dichiarata. Si consideri il [java.util.SortedMap&lt;K, V&gt; ](http://developer.android.com/reference/java/util/SortedMap.html) interfaccia Java, che implementa il [java.util.Map&lt;K, V&gt; ](http://developer.android.com/reference/java/util/Map.html) interfaccia. La mappa è un [deselezionare](http://developer.android.com/reference/java/util/Map.html#clear()) metodo, pertanto un apparentemente ragionevole `Invoker` definizione per SortedMap sarebbe:

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

Il precedente avrà esito negativo perché `JNIEnv.GetMethodID` restituirà `null` quando si cercano il `Map.clear` metodo tramite il `SortedMap` istanza della classe.

Sono disponibili due soluzioni: tenere traccia di ogni metodo proviene da interfaccia e avere un `class_ref` per ogni interfaccia, o mantenere tutti gli elementi come membri di istanza e di eseguire la ricerca del metodo al tipo di classe più derivata, non il tipo di interfaccia. Quest'ultima viene eseguita in **Mono.Android.dll**.

La definizione di Invoker ha sei sezioni: il costruttore, il `Dispose` (metodo), il `ThresholdType` e `ThresholdClass` membri, il `GetObject` (metodo), l'implementazione di metodo di interfaccia e l'implementazione del metodo connettore.

 <a name="_Constructor" />


#### <a name="constructor"></a>Costruttore

Il costruttore deve cercare la classe di runtime dell'istanza viene richiamato e archiviare la classe di runtime nell'istanza `class_ref` campo:

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

Nota: Il `Handle` proprietà deve essere utilizzata all'interno del corpo del costruttore e non il `handle` parametro, come in Android v 4.0 il `handle` parametro sia valido al termine dell'esecuzione del costruttore base.

<a name="_Dispose_Method" />

#### <a name="dispose-method"></a>Metodo Dispose

Il `Dispose` metodo necessario liberare il riferimento globale allocato nel costruttore:

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

<a name="_ThresholdType_and_ThresholdClass" />

#### <a name="thresholdtype-and-thresholdclass"></a>ThresholdType e ThresholdClass

Il `ThresholdType` e `ThresholdClass` membri sono identici a che si trova in un'associazione di classe:

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

<a name="_GetObject_Method" />

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

<a name="_Interface_Methods" />

#### <a name="interface-methods"></a>Metodi di interfaccia

Ogni metodo dell'interfaccia deve avere un'implementazione che richiama il metodo Java corrispondente tramite JNI:

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

 <a name="_Connector_Methods" />


#### <a name="connector-methods"></a>Metodi di connettore

I metodi di connettore e l'infrastruttura di supporto sono responsabili del marshalling di parametri JNI ai tipi c# appropriati. Il linguaggio `int[]` parametro viene passato come un JNI `jintArray`, ovvero un `IntPtr` in c#. Il `IntPtr` deve essere sottoposto a marshalling un `JavaArray<int>` per supportare richiama l'interfaccia in c#:

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

Se `int[]` sarebbe preferibile `JavaList<int>`, quindi [JNIEnv.GetArray()](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetArray/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership%2cSystem.Type)) sarebbe possibile usare invece:

```csharp
int[] _values = (int[]) JNIEnv.GetArray(values, JniHandleOwnership.DoNotTransfer, typeof (int));
```

Si noti tuttavia che `JNIEnv.GetArray` copia l'intera matrice tra le macchine virtuali, pertanto per matrici di grandi dimensioni ciò potrebbe causare un numero elevato di aggiunta di stress di Garbage Collection.

<a name="_Complete_Invoker_Definition" />

### <a name="complete-invoker-definition"></a>Completare la definizione Invoker

Il [completare definizione IAdderProgressInvoker](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L88):

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

 <a name="_JNI_Object_References" />


## <a name="jni-object-references"></a>Riferimenti agli oggetti JNI

Molti metodi JNIEnv restituiscono *JNI* *i riferimenti all'oggetto*, che sono simili a `GCHandle`s. JNI fornisce tre diversi tipi di riferimenti a oggetti: riferimenti locali, globali riferimenti e i riferimenti deboli globali. Tutte e tre sono rappresentati come `System.IntPtr`, *ma* (in base alla sezione di tipi di funzione JNI) non tutti i `IntPtr`restituiti da `JNIEnv` metodi sono riferimenti. Ad esempio, [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/) restituisce un `IntPtr`, ma non restituisce un riferimento all'oggetto, restituisce un `jmethodID`. Consultare la [documentazione della funzione JNI](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html) per informazioni dettagliate.

Riferimenti locali creati da *la maggior parte dei* metodi di creazione di riferimento.
Android consente solo un numero limitato di riferimenti locali a esistere in qualsiasi momento, in genere 512. Riferimenti locali possono essere eliminati tramite [JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/).
A differenza di JNI, non tutti i riferimenti JNIEnv utilizzando i metodi che fa riferimento a un oggetto restituito restituiscono riferimenti locali. [JNIEnv.FindClass](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/) restituisce un *globale* riferimento. È consigliabile eliminare riferimenti locali più rapidamente possibile, possibilmente costruendo un [lang](https://developer.xamarin.com/api/type/Java.Lang.Object/) attorno all'oggetto e specificando `JniHandleOwnership.TransferLocalRef` per il [lang (IntPtr gestire, trasferimento JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) costruttore.

Vengono creati i riferimenti globali [JNIEnv.NewGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewGlobalRef/) e [JNIEnv.FindClass](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/).
Possono essere eliminati definitivamente con [JNIEnv.DeleteGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteGlobalRef/).
Emulatori hanno un limite di 2.000 globale i riferimenti in attesa, mentre i dispositivi hardware hanno un limite di circa 52.000 riferimenti globali.

I riferimenti deboli globali sono disponibili solo in Android versione 2.2 (Froyo) e versioni successive. I riferimenti deboli globali possono essere eliminati con [JNIEnv.DeleteWeakGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteWeakGlobalRef/(System.IntPtr)).

<a name="_Dealing_With_JNI_Local_References" />

### <a name="dealing-with-jni-local-references"></a>Gestione dei riferimenti locale JNI in

Il [JNIEnv.GetObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetObjectField/), [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/), [JNIEnv.CallObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallObjectMethod/), [JNIEnv.CallNonvirtualObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualObjectMethod/)e [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) i metodi restituiscono un `IntPtr` che contiene un riferimento locale JNI a un oggetto, Java o `IntPtr.Zero` se Java restituito `null`. A causa di un numero di riferimenti locali che possono essere in sospeso in una volta (512 voci), è opportuno assicurarsi che i riferimenti vengono eliminati in modo tempestivo. Esistono tre modi che possono essere gestiti riferimenti locali: in modo esplicito l'eliminazione, la creazione di un `Java.Lang.Object` istanza per contenere, utilizzando i loro `Java.Lang.Object.GetObject<T>()` per creare un callable wrapper gestito intorno ad essi.

 <a name="_Explicitly_Deleting_Local_References" />


### <a name="explicitly-deleting-local-references"></a>Eliminazione in modo esplicito i riferimenti locali

[JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/) consente di eliminare i riferimenti locali. Una volta eliminato il riferimento locale, non può essere utilizzato più, pertanto prestare attenzione per garantire che `JNIEnv.DeleteLocalRef` è l'ultima operazione eseguita con il riferimento locale.

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
try {
    // Do something with `lref`
}
finally {
    JNIEnv.DeleteLocalRef (lref);
}
```

 <a name="_Wrapping_with_Java.Lang.Object" />


### <a name="wrapping-with-javalangobject"></a>Ritorno a capo con lang

`Java.Lang.Object` fornisce un [lang (handle IntPtr, trasferimento JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) costruttore che può essere usato per eseguire il wrapping di un riferimento a JNI esistente. Il [JniHandleOwnership](https://developer.xamarin.com/api/type/Android.Runtime.JniHandleOwnership/) parametro determina il modo in `IntPtr` parametro deve essere considerato:

-   [JniHandleOwnership.DoNotTransfer](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.DoNotTransfer/) &ndash; creato `Java.Lang.Object` istanza verrà creato un nuovo riferimento globale dal `handle` parametro e `handle` rimane invariato.
    Il chiamante è responsabile per liberare `handle` , se necessario.

-   [JniHandleOwnership.TransferLocalRef](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.TransferLocalRef/) &ndash; creato `Java.Lang.Object` istanza verrà creato un nuovo riferimento globale dal `handle` parametro e `handle` viene eliminata con [JNIEnv.DeleteLocalRef ](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/) . Il chiamante non è necessario liberare `handle` e non devono utilizzare `handle` al termine dell'esecuzione il costruttore.

-   [JniHandleOwnership.TransferGlobalRef](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.TransferLocalRef/) &ndash; creato `Java.Lang.Object` istanza verrà assumere la proprietà del `handle` parametro. Il chiamante non è necessario liberare `handle` .


Poiché i metodi di chiamata di metodo JNI restituiscono locale refs, `JniHandleOwnership.TransferLocalRef` normalmente utilizzati:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef);
```

Il riferimento globale creato non verranno liberato finché il `Java.Lang.Object` istanza è sottoposto a garbage collection. Se sono in grado di eliminazione dell'istanza consente di liberare il riferimento globale, velocizzare le operazioni di garbage collection:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
using (var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef)) {
    // use value ...
}
```

 <a name="_Using_Java.Lang.Object.GetObject&lt;T&gt;()" />


### <a name="using-javalangobjectgetobjectlttgt"></a>Using Java.Lang.Object.GetObject&lt;T&gt;()

`Java.Lang.Object` fornisce un [Java.Lang.Object.GetObject&lt;T&gt;(handle IntPtr, trasferimento JniHandleOwnership)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) metodo che può essere utilizzato per creare un callable wrapper gestito del tipo specificato.

Il tipo `T` deve soddisfare i requisiti seguenti:

1.  `T` deve essere un tipo di riferimento.

1.  `T` deve implementare il `IJavaObject` interfaccia.

1.  Se `T` è una classe astratta o interfaccia, quindi `T` devono fornire un costruttore con i tipi di parametro `(IntPtr,
    JniHandleOwnership)` .

1.  Se `T` è una classe astratta o un'interfaccia, si *deve* essere un *invoker* disponibile per `T` . Un invoker di è un tipo non astratto che eredita `T` o implementa `T` , e ha lo stesso nome `T` con un suffisso Invoker. Ad esempio, se T è l'interfaccia `Java.Lang.IRunnable` , quindi il tipo `Java.Lang.IRunnableInvoker` deve esistere e deve contenere la `(IntPtr,
    JniHandleOwnership)` costruttore.


Poiché i metodi di chiamata di metodo JNI restituiscono locale refs, `JniHandleOwnership.TransferLocalRef` normalmente utilizzati:

```csharp
IntPtr lrefString = JNIEnv.CallObjectMethod(instance, methodID);
Java.Lang.String value = Java.Lang.Object.GetObject<Java.Lang.String>( lrefString, JniHandleOwnership.TransferLocalRef);
```

<a name="_Looking_up_Java_Types" />

## <a name="looking-up-java-types"></a>La ricerca di tipi Java

Per effettuare la ricerca di un campo o metodo in JNI, il tipo dichiarante per il campo o metodo debba essere cercato prima. Il [Android.Runtime.JNIEnv.FindClass(string)](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/(System.String)) metodo viene utilizzato per cercare i tipi Java. Il parametro della stringa è il *semplificato riferimento al tipo* o *riferimento di tipo completo* per il tipo Java. Vedere il [sezione riferimenti di tipo JNI](#_JNI_Type_References) per ulteriori informazioni sui riferimenti di tipo semplificata e completo.

Nota: a differenza di tutti gli altri `JNIEnv` metodo che restituisce istanze di un oggetto, `FindClass` restituisce un riferimento globale, non è un riferimento locale.

<a name="_Instance_Fields" />

## <a name="instance-fields"></a>Campi di istanza

I campi vengono modificati tramite *campo ID*. Campo ID vengono ottenuti tramite [JNIEnv.GetFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFieldID/), che richiede la classe che il campo è definito, il nome del campo e [la firma del tipo JNI](#_JNI_Type_Signatures) del campo.

ID di campo non è necessario essere liberata e sono validi, purché il tipo Java corrispondente viene caricato. (Android attualmente non supporta lo scaricamento di classe.)

Esistono due set di metodi per modificare i campi di istanza: uno per la lettura dei campi di istanza e uno per la scrittura di campi di istanza. Tutti i set di metodi richiedono un ID di campo leggere o scrivere il valore del campo.

<a name="_Reading_Instance_Field_Values" />

### <a name="reading-instance-field-values"></a>Valori dei campi di istanza di lettura

Il set di metodi per la lettura dei valori di campo di istanza segua il modello di denominazione:

```csharp
* JNIEnv.Get*Field(IntPtr instance, IntPtr fieldID);
```
dove `*` è il tipo del campo:

-   [JNIEnv.GetObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetObjectField/) &ndash; leggere il valore di qualsiasi campo di istanza che non è un tipo incorporato, ad esempio `java.lang.Object` , matrici e i tipi di interfaccia. Il valore restituito è un riferimento locale JNI.

-   [JNIEnv.GetBooleanField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetBooleanField/) &ndash; leggere il valore di `bool` campi di istanza.

-   [JNIEnv.GetByteField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetByteField/) &ndash; leggere il valore di `sbyte` campi di istanza.

-   [JNIEnv.GetCharField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetCharField/) &ndash; leggere il valore di `char` campi di istanza.

-   [JNIEnv.GetShortField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetShortField/) &ndash; leggere il valore di `short` campi di istanza.

-   [JNIEnv.GetIntField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetIntField/) &ndash; leggere il valore di `int` campi di istanza.

-   [JNIEnv.GetLongField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetLongField/) &ndash; leggere il valore di `long` campi di istanza.

-   [JNIEnv.GetFloatField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFloatField/) &ndash; leggere il valore di `float` campi di istanza.

-   [JNIEnv.GetDoubleField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetDoubleField/) &ndash; leggere il valore di `double` campi di istanza.



 <a name="_Writing_Instance_Field_Values" />


### <a name="writing-instance-field-values"></a>Valori dei campi di istanza di scrittura

Il set di metodi per la scrittura di valori di campo di istanza segua il modello di denominazione:

```csharp
JNIEnv.SetField(IntPtr instance, IntPtr fieldID, Type value);
```

dove *tipo* è il tipo del campo:

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.IntPtr)) &ndash; scrivere il valore di qualsiasi campo che non è un tipo incorporato, ad esempio `java.lang.Object` , matrici e i tipi di interfaccia. Il `IntPtr` valore può essere un riferimento locale JNI, riferimento globale JNI, riferimento globale JNI debole, o `IntPtr.Zero` (per `null` ).

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

I campi statici vengono modificati tramite *campo ID*. Campo ID vengono ottenuti tramite [JNIEnv.GetStaticFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticFieldID/), che richiede la classe che il campo è definito, il nome del campo e [la firma del tipo JNI](#JNI%20Type%20Signatures) del campo.

ID di campo non è necessario essere liberata e sono validi, purché il tipo Java corrispondente viene caricato. (Android attualmente non supporta lo scaricamento di classe.)

Esistono due set di metodi per modificare i campi statici: uno per la lettura dei campi di istanza e uno per la scrittura di campi di istanza. Tutti i set di metodi richiedono un ID di campo leggere o scrivere il valore del campo.

<a name="_Reading_Static_Field_Values" />

### <a name="reading-static-field-values"></a>Lettura dei valori di campo statico

I metodi per la lettura dei valori di campi statici che segue il modello di denominazione:

```csharp
* JNIEnv.GetStatic*Field(IntPtr class, IntPtr fieldID);
```

dove `*` è il tipo del campo:

-   [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/) &ndash; leggere il valore di qualsiasi campo statico che non è un tipo incorporato, ad esempio `java.lang.Object` , matrici e i tipi di interfaccia. Il valore restituito è un riferimento locale JNI.

-   [JNIEnv.GetStaticBooleanField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticBooleanField/) &ndash; leggere il valore di `bool` i campi statici.

-   [JNIEnv.GetStaticByteField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticByteField/) &ndash; leggere il valore di `sbyte` i campi statici.

-   [JNIEnv.GetStaticCharField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticCharField/) &ndash; leggere il valore di `char` i campi statici.

-   [JNIEnv.GetStaticShortField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticShortField/) &ndash; leggere il valore di `short` i campi statici.

-   [JNIEnv.GetStaticLongField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticLongField/) &ndash; leggere il valore di `long` i campi statici.

-   [JNIEnv.GetStaticFloatField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticFloatField/) &ndash; leggere il valore di `float` i campi statici.

-   [JNIEnv.GetStaticDoubleField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticDoubleField/) &ndash; leggere il valore di `double` i campi statici.


<a name="_Writing_Static_Field_Values" />

### <a name="writing-static-field-values"></a>Scrittura di valori di campo statico

I metodi per la scrittura di valori di campi statici che segue il modello di denominazione:

```csharp
JNIEnv.SetStaticField(IntPtr class, IntPtr fieldID, Type value);
```

dove *tipo* è il tipo del campo:

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.IntPtr)) &ndash; scrivere il valore di qualsiasi campo statico che non è un tipo incorporato, ad esempio `java.lang.Object` , matrici e i tipi di interfaccia. Il `IntPtr` valore può essere un riferimento locale JNI, riferimento globale JNI, riferimento globale JNI debole, o `IntPtr.Zero` (per `null` ).

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Boolean)) &ndash; scrivere il valore di `bool` i campi statici.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.SByte)) &ndash; scrivere il valore di `sbyte` i campi statici.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Char)) &ndash; scrivere il valore di `char` i campi statici.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int16)) &ndash; scrivere il valore di `short` i campi statici.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int32)) &ndash; scrivere il valore di `int` i campi statici.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int64)) &ndash; scrivere il valore di `long` i campi statici.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Single)) &ndash; scrivere il valore di `float` i campi statici.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Double)) &ndash; scrivere il valore di `double` i campi statici.


<a name="_Instance_Methods" />

## <a name="instance-methods"></a>Metodi di istanza

Metodi di istanza vengono richiamati tramite *metodo ID*. Metodo ID vengono ottenuti tramite [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/), che richiede il tipo che il metodo è definito, il nome del metodo e [la firma del tipo JNI](#_JNI_Type_Signatures) del metodo.

Metodo ID non è necessario essere liberata e sono validi, purché il tipo Java corrispondente viene caricato. (Android attualmente non supporta lo scaricamento di classe.)

Esistono due set di metodi per il richiamo di metodi: uno per richiamare metodi virtualmente e uno per richiamare metodi non virtuale. Entrambi i set di metodi richiedono un ID di metodo richiamare il metodo e la chiamata non virtuali richiede inoltre che si specifichi quale implementazione della classe deve essere richiamato.

Metodi di interfaccia possono solo essere cercati all'interno del tipo dichiarante; metodi che provengono da estesi ereditate interfacce non possono essere cercati. Vedere le interfacce di associazione successive / implementazione Invoker sezione per ulteriori dettagli.

Qualsiasi metodo dichiarato nella classe o qualsiasi classe di base o interfaccia implementata può essere cercato.

<a name="_Virtual_Method_Invocation" />

### <a name="virtual-method-invocation"></a>Chiamata di metodo virtuale

Il set di metodi per richiamare metodi virtualmente segue il modello di denominazione:

```csharp
* JNIEnv.Call*Method( IntPtr instance, IntPtr methodID, params JValue[] args );
```

dove `*` è il tipo restituito del metodo.

-   [JNIEnv.CallObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallObjectMethod/) &ndash; richiama un metodo che restituisce un tipo non incorporato, ad esempio `java.lang.Object` , matrici e le interfacce. Il valore restituito è un riferimento locale JNI.

-   [JNIEnv.CallBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallBooleanMethod/) &ndash; richiama un metodo che restituisce un `bool` valore.

-   [JNIEnv.CallByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallByteMethod/) &ndash; richiama un metodo che restituisce un `sbyte` valore.

-   [JNIEnv.CallCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallCharMethod/) &ndash; richiama un metodo che restituisce un `char` valore.

-   [JNIEnv.CallShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallShortMethod/) &ndash; richiama un metodo che restituisce un `short` valore.

-   [JNIEnv.CallLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallLongMethod/) &ndash; richiama un metodo che restituisce un `long` valore.

-   [JNIEnv.CallFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallFloatMethod/) &ndash; richiama un metodo che restituisce un `float` valore.

-   [JNIEnv.CallDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallDoubleMethod/) &ndash; richiama un metodo che restituisce un `double` valore.


<a name="_Non-virtual_Method_Invocation" />

### <a name="non-virtual-method-invocation"></a>Chiamata di metodo non virtuale

Il set di metodi per richiamare metodi non virtualmente segue il modello di denominazione:

```csharp
* JNIEnv.CallNonvirtual*Method( IntPtr instance, IntPtr class, IntPtr methodID, params JValue[] args );
```

dove `*` è il tipo restituito del metodo. Chiamata al metodo non virtuale è in genere utilizzata per richiamare il metodo di base di un metodo virtuale.

-   [JNIEnv.CallNonvirtualObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualObjectMethod/) &ndash; Non virtualmente richiamare un metodo che restituisce un tipo non incorporato, ad esempio `java.lang.Object` , matrici e le interfacce. Il valore restituito è un riferimento locale JNI.

-   [JNIEnv.CallNonvirtualBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualBooleanMethod/) &ndash; Non virtualmente richiamare un metodo che restituisce un `bool` valore.

-   [JNIEnv.CallNonvirtualByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualByteMethod/) &ndash; Non virtualmente richiamare un metodo che restituisce un `sbyte` valore.

-   [JNIEnv.CallNonvirtualCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualCharMethod/) &ndash; Non virtualmente richiamare un metodo che restituisce un `char` valore.

-   [JNIEnv.CallNonvirtualShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualShortMethod/) &ndash; Non virtualmente richiamare un metodo che restituisce un `short` valore.

-   [JNIEnv.CallNonvirtualLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualLongMethod/) &ndash; Non virtualmente richiamare un metodo che restituisce un `long` valore.

-   [JNIEnv.CallNonvirtualFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualFloatMethod/) &ndash; Non virtualmente richiamare un metodo che restituisce un `float` valore.

-   [JNIEnv.CallNonvirtualDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualDoubleMethod/) &ndash; Non virtualmente richiamare un metodo che restituisce un `double` valore.


 <a name="_Static_Methods" />

## <a name="static-methods"></a>Metodi statici

I metodi statici vengono richiamati tramite *metodo ID*. Metodo ID vengono ottenuti tramite [JNIEnv.GetStaticMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/), che richiede il tipo che il metodo è definito, il nome del metodo e [la firma del tipo JNI](#JNI%20Type%20Signatures) del metodo.

Metodo ID non è necessario essere liberata e sono validi, purché il tipo Java corrispondente viene caricato. (Android attualmente non supporta lo scaricamento di classe.)

 <a name="_Static_Method_Invocation" />


### <a name="static-method-invocation"></a>Chiamata al metodo statico

Il set di metodi per richiamare metodi virtualmente segue il modello di denominazione:

```csharp
* JNIEnv.CallStatic*Method( IntPtr class, IntPtr methodID, params JValue[] args );
```

dove `*` è il tipo restituito del metodo.

-   [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) &ndash; richiamare un metodo statico che restituisce un tipo non incorporato, ad esempio `java.lang.Object` , matrici e le interfacce. Il valore restituito è un riferimento locale JNI.

-   [JNIEnv.CallStaticBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticBooleanMethod/) &ndash; richiamare un metodo statico che restituisce un `bool` valore.

-   [JNIEnv.CallStaticByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticByteMethod/) &ndash; richiamare un metodo statico che restituisce un `sbyte` valore.

-   [JNIEnv.CallStaticCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticCharMethod/) &ndash; richiamare un metodo statico che restituisce un `char` valore.

-   [JNIEnv.CallStaticShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticShortMethod/) &ndash; richiamare un metodo statico che restituisce un `short` valore.

-   [JNIEnv.CallStaticLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallLongMethod/) &ndash; richiamare un metodo statico che restituisce un `long` valore.

-   [JNIEnv.CallStaticFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticFloatMethod/) &ndash; richiamare un metodo statico che restituisce un `float` valore.

-   [JNIEnv.CallStaticDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticDoubleMethod/) &ndash; richiamare un metodo statico che restituisce un `double` valore.


 <a name="_JNI_Type_Signatures" />


## <a name="jni-type-signatures"></a>Firme di tipo JNI

[Le firme di tipo JNI](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/types.html#wp16432) sono [riferimenti al tipo JNI](#_JNI_Type_References) (riferimenti di tipo anche se non semplificata), ad eccezione dei metodi. Con i metodi, la firma di tipo JNI è una parentesi aperta `'('`, seguito da riferimenti del tipo per tutti i tipi concatenati (con separazione Nessun separatore delle migliaia o altro), seguito da una parentesi di chiusura parametro `')'`, aggiungendo il riferimento al tipo JNI di tipo restituito del metodo.

Ad esempio, poiché il metodo Java:

```java
long f(int n, String s, int[] array);
```

La firma di tipo JNI sarebbe:

```csharp
(ILjava/lang/String;[I)J
```

In generale, è *fortemente* consiglia di utilizzare il `javap` per determinare le firme JNI. Ad esempio, la firma di tipo JNI del [java.lang.Thread.State.valueOf(String)](http://developer.android.com/reference/java/lang/Thread.State.html#valueOf(java.lang.String)) metodo è "(Ljava/lang/stringa); Ljava/lang/Thread$ stato;", mentre il JNI digitare firma del [ java.lang.Thread.State.values](http://developer.android.com/reference/java/lang/Thread.State.html#values) metodo è "() [Ljava/lang/Thread$ stato;". Prestare attenzione per i punti e virgola finali; tali *sono* fa parte della firma del tipo JNI.

<a name="_JNI_Type_References" />

## <a name="jni-type-references"></a>Riferimenti al tipo JNI

Riferimenti al tipo JNI sono diversi da riferimenti al tipo Java. Non è possibile utilizzare nomi completi di tipo Java, ad esempio `java.lang.String` con JNI, è necessario utilizzare invece le variazioni JNI `"java/lang/String"` o `"Ljava/lang/String;"`, a seconda del contesto; vedere di seguito per informazioni dettagliate.
Sono disponibili quattro tipi di riferimenti al tipo JNI:

-  **built-in**
-  **simplified**
-  **type**
-  **array**


 <a name="_Built-in_Type_References" />


### <a name="built-in-type-references"></a>Riferimenti di tipo incorporato

Riferimenti ai tipi predefiniti sono un solo carattere utilizzato per fare riferimento ai tipi di valore incorporato. Il mapping è come segue:

-  `"B"` per `sbyte` .
-  `"S"` per `short` .
-  `"I"` per `int` .
-  `"J"` per `long` .
-  `"F"` per `float` .
-  `"D"` per `double` .
-  `"C"` per `char` .
-  `"Z"` per `bool` .
-  `"V"` per `void` tipi restituiti di metodo.


 <a name="_Simplified_Type_References" />


### <a name="simplified-type-references"></a>Riferimenti al tipo semplificata

Riferimenti di tipo semplificata possono essere utilizzati solo [JNIEnv.FindClass(string)](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/(System.String)).
Esistono due modi per derivare un riferimento al tipo semplificata:

1.  Da un nome di linguaggio completo, sostituire ogni `'.'` all'interno del nome di pacchetto e prima che il nome del tipo con `'/'` e ogni `'.'` all'interno di un nome di tipo `'$'` .

1.  Leggere l'output di `'unzip -l android.jar | grep JavaName'` .


Uno dei due comporterà il tipo Java [java.lang.Thread.State](http://developer.android.com/reference/java/lang/Thread.State.html) che viene mappato al riferimento al tipo semplificata `java/lang/Thread$State`.

<a name="_Type_References" />

### <a name="type-references"></a>Riferimenti di tipo

Riferimento a un tipo è un riferimento di tipo incorporato o un riferimento al tipo semplificata con un `'L'` prefisso e un `';'` suffisso. Per il tipo Java [lang](http://developer.android.com/reference/java/lang/String.html), il riferimento al tipo semplificato è `"java/lang/String"`, mentre il riferimento al tipo è `"Ljava/lang/String;"`.

Riferimenti al tipo vengono utilizzati con i riferimenti di tipo matrice e JNI firme.

Un ulteriore modo per ottenere un riferimento di tipo è leggendo l'output di `'javap -s -classpath android.jar fully.qualified.Java.Name'`.
A seconda del tipo coinvolti, è possibile utilizzare una dichiarazione di costruttore o un metodo tipo per determinare il nome JNI. Ad esempio:

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

`Thread.State` è un tipo di enumerazione Java, pertanto è possibile utilizzare la firma del `valueOf` metodo per determinare che il riferimento al tipo è stato$ Ljava/lang/Thread;.


<a name="_Array_Type_References" />

### <a name="array-type-references"></a>Riferimenti a tipo di matrice

I riferimenti di tipo matrice sono `'['` preceduti da un riferimento al tipo JNI.
Impossibile utilizzare riferimenti di tipo semplificata quando si specificano le matrici.

Ad esempio, `int[]` è `"[I"`, `int[][]` è `"[[I"`, e `java.lang.Object[]` è `"[Ljava/lang/Object;"`.

<a name="_Java_Generics_and_Type_Erasure" />


## <a name="java-generics-and-type-erasure"></a>Generics Java e la cancellazione di tipo

*La maggior parte delle* del tempo, come avviene per JNI, generics Java *non esistono*.
Esistono alcuni "rughe", ma tali rughe sono in modalità di interazione con i generics, non con la modalità di ricerca e richiama membri generici JNI di Java.

Non vi è alcuna differenza tra un tipo generico o membro e un tipo non generico o un membro quando si interagisce con JNI. Ad esempio, il tipo generico [java.lang.Class&lt;T&gt; ](http://developer.android.com/reference/java/lang/Class.html) è anche il tipo generico "non elaborato" `java.lang.Class`, entrambi hanno lo stesso riferimento di tipo semplificata, `"java/lang/Class"`.

<a name="Java_Native_Interface_Support" />

## <a name="java-native-interface-support"></a>Supporto di Java Native Interface

[Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/) è un wrapper gestito per il Jave JNI (Native Interface). JNI funzioni vengono dichiarate all'interno di [Java Native Interface Specification](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html), anche se i metodi sono stati modificati per rimuovere la proprietà esplicita `JNIEnv*` parametro e `IntPtr` viene usata invece di `jobject`, `jclass`, `jmethodID`e così via. Si consideri ad esempio il [funzione JNI NewObject](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp4517):

```csharp
jobject NewObjectA(JNIEnv *env, jclass clazz, jmethodID methodID, jvalue *args);
```

Questa funzionalità è esposta come il [JNIEnv.NewObject](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewObject/p/System.IntPtr/System.IntPtr/Android.Runtime.JValue[]/) metodo:

```csharp
public static IntPtr NewObject(IntPtr clazz, IntPtr jmethod, params JValue[] parms);
```

La conversione tra le due chiamate è piuttosto semplice. In C sono:

```c
jobject CreateMapActivity(JNIEnv *env)
{
    jclass    Map_Class   = (*env)->FindClass(env, "mono/samples/googlemaps/MyMapActivity");
    jmethodID Map_defCtor = (*env)->GetMethodID (env, Map_Class, "<init>", "()V");
    jobject   instance    = (*env)->NewObject (env, Map_Class, Map_defCtor);

    return instance;
}
```

È l'equivalente di c#:

```csharp
IntPtr CreateMapActivity()
{
    IntPtr Map_Class   = JNIEnv.FindClass ("mono/samples/googlemaps/MyMapActivity");
    IntPtr Map_defCtor = JNIEnv.GetMethodID (Map_Class, "<init>", "()V");
    IntPtr instance    = JNIEnv.NewObject (Map_Class, Map_defCtor);

    return instance;
}
```

Dopo aver creato un'istanza di oggetto Java contenuta in un elemento IntPtr, è opportuno eseguire un'operazione con esso. È possibile utilizzare, ad esempio metodi JNIEnv [ <span class="external">JNIEnv.CallVoidMethod()</span> ](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallVoidMethod/p/System.IntPtr/System.IntPtr/Android.Runtime.JValue[]/) a tale scopo, ma se è già presente un wrapper di analogico c#, è necessario creare un wrapper sul riferimento JNI. È possibile eseguire tramite il [Extensions.JavaCast <t>()</t> ](https://developer.xamarin.com/api/member/Android.Runtime.Extensions.JavaCast%7BTResult%7D/p/Android.Runtime.IJavaObject/) metodo di estensione:

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = new Java.Lang.Object(lrefActivity, JniHandleOwnership.TransferLocalRef)
    .JavaCast<Activity>();
```

È inoltre possibile utilizzare il [Java.Lang.Object.GetObject <t>()</t> ](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) metodo:

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = Java.Lang.Object.GetObject<Activity>(lrefActivity, JniHandleOwnership.TransferLocalRef);
```

Inoltre, tutte le funzioni JNI sono state modificate rimuovendo il `JNIEnv*` parametro presente in ogni funzione JNI.

<a name="_Summary" />

## <a name="summary"></a>Riepilogo

Gestire direttamente JNI è un'esperienza terribile che dovrebbe essere evitata tutti i costi. Purtroppo, non è sempre inevitabile; Questa guida fornirà probabilmente alcune informazioni quando si raggiunge i case di linguaggio non associati con Mono per Android.


## <a name="related-links"></a>Collegamenti correlati

- [SanityTests (esempio)](https://developer.xamarin.com/samples/SanityTests/)
- [Java Native Interface Specification](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/jniTOC.html)
- [Funzioni di Java Native Interface](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)
