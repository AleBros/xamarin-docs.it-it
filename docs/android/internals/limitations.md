---
title: Confronto tra Xamarin.Android e desktop-differenze nel runtime di mono
ms.prod: xamarin
ms.assetid: F953F9B4-3596-8B3A-A8E4-8219B5B9F7CA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: 8fe0e3a9adedb161c527ccdf6d6c3a7cd06a1d86
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027845"
---
# <a name="limitations"></a>Limitazioni

Poiché le applicazioni in Android richiedono la generazione di tipi proxy Java durante il processo di compilazione, non è possibile generare tutto il codice in fase di esecuzione.

Queste sono le limitazioni di Xamarin.Android rispetto alla mono desktop:

## <a name="limited-dynamic-language-support"></a>Supporto del linguaggio dinamico limitato

 [Android Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md) è necessario ogni volta che il runtime di Android deve richiamare il codice gestito. I wrapper richiamabili Android vengono generati in fase di compilazione, in base all'analisi statica del linguaggio intermedio. Il risultato finale è che non è *possibile* usare linguaggi dinamici (IronPython, IronRuby e così via) in qualsiasi scenario in cui è richiesta la creazione di una sottoclasse di tipi Java (inclusa la creazione di una sottoclasse indiretta), in quanto non esiste alcun modo per estrarre questi tipi dinamici in fase di compilazione generare i necessari Android Callable Wrapper.

## <a name="limited-java-generation-support"></a>Supporto per la generazione Java limitata

È necessario generare [Android Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md) affinché il codice Java chiami codice gestito. *Per impostazione predefinita*, Android Callable Wrapper conterrà solo (determinati) costruttori e metodi dichiarati che eseguono l'override di un metodo Java virtuale (ad esempio, ha [`RegisterAttribute`](xref:Android.Runtime.RegisterAttribute)) o implementano un metodo di interfaccia Java (l'interfaccia ha in modo analogo `Attribute`).
  
Prima della versione 4,1, non venivano dichiarati altri metodi. Con la versione 4,1, [è possibile usare gli attributi personalizzati `Export` e `ExportField` per dichiarare i metodi e i campi Java all'interno di Android Callable Wrapper](~/android/platform/java-integration/working-with-jni.md).

### <a name="missing-constructors"></a>Costruttori mancanti

I costruttori rimangono difficili, a meno che non venga usato [`ExportAttribute`](xref:Java.Interop.ExportAttribute) . L'algoritmo per la generazione di costruttori Android Callable Wrapper è che verrà generato un costruttore Java se:

1. È disponibile un mapping Java per tutti i tipi di parametro
2. La classe base dichiara lo stesso costruttore &ndash; questa operazione è necessaria perché Android callable wrapper *deve* richiamare il costruttore della classe base corrispondente; non è possibile usare argomenti predefiniti (poiché non esiste un modo semplice per determinare i valori da usare in Java).

Si consideri ad esempio la classe seguente:

```csharp
[Service]
class MyIntentService : IntentService {
    public MyIntentService (): base ("value")
    {
    }
}
```

Sebbene l'aspetto sia perfettamente logico, il wrapper richiamabile Android risultante *nelle build di versione* non conterrà un costruttore predefinito. Di conseguenza, se si tenta di avviare il servizio, ad esempio [`Context.StartService`](xref:Android.Content.Context.StartService*), l'operazione avrà esito negativo:

```shell
E/AndroidRuntime(31766): FATAL EXCEPTION: main
E/AndroidRuntime(31766): java.lang.RuntimeException: Unable to instantiate service example.MyIntentService: java.lang.InstantiationException: can't instantiate class example.MyIntentService; no empty constructor
E/AndroidRuntime(31766):        at android.app.ActivityThread.handleCreateService(ActivityThread.java:2347)
E/AndroidRuntime(31766):        at android.app.ActivityThread.access$1600(ActivityThread.java:130)
E/AndroidRuntime(31766):        at android.app.ActivityThread$H.handleMessage(ActivityThread.java:1277)
E/AndroidRuntime(31766):        at android.os.Handler.dispatchMessage(Handler.java:99)
E/AndroidRuntime(31766):        at android.os.Looper.loop(Looper.java:137)
E/AndroidRuntime(31766):        at android.app.ActivityThread.main(ActivityThread.java:4745)
E/AndroidRuntime(31766):        at java.lang.reflect.Method.invokeNative(Native Method)
E/AndroidRuntime(31766):        at java.lang.reflect.Method.invoke(Method.java:511)
E/AndroidRuntime(31766):        at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:786)
E/AndroidRuntime(31766):        at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:553)
E/AndroidRuntime(31766):        at dalvik.system.NativeStart.main(Native Method)
E/AndroidRuntime(31766): Caused by: java.lang.InstantiationException: can't instantiate class example.MyIntentService; no empty constructor
E/AndroidRuntime(31766):        at java.lang.Class.newInstanceImpl(Native Method)
E/AndroidRuntime(31766):        at java.lang.Class.newInstance(Class.java:1319)
E/AndroidRuntime(31766):        at android.app.ActivityThread.handleCreateService(ActivityThread.java:2344)
E/AndroidRuntime(31766):        ... 10 more
```

La soluzione alternativa consiste nel dichiarare un costruttore predefinito, decorarlo con il `ExportAttribute`e impostare l' [`ExportAttribute.SuperStringArgument`](xref:Java.Interop.ExportAttribute.SuperArgumentsString): 

```csharp
[Service]
class MyIntentService : IntentService {
    [Export (SuperArgumentsString = "\"value\"")]
    public MyIntentService (): base("value")
    {
    }

    // ...
}
```

### <a name="generic-c-classes"></a>Classi C# generiche

Le C# classi generiche sono supportate solo parzialmente. Esistono le limitazioni seguenti:

- I tipi generici non possono usare `[Export]` o `[ExportField`]. Il tentativo di eseguire questa operazione genererà un errore di `XA4207`.

    ```csharp
    public abstract class Parcelable<T> : Java.Lang.Object, IParcelable
    {
        // Invalid; generates XA4207
        [ExportField ("CREATOR")]
        public static IParcelableCreator CreateCreator ()
        {
            ...
    }
    ```

- I metodi generici non possono usare `[Export]` o `[ExportField]`:

    ```csharp
    public class Example : Java.Lang.Object
    {
        
        // Invalid; generates XA4207
        [Export]
        public static void Method<T>(T value)
        {
            ...
        }
    }
    ```

- non è possibile usare `[ExportField]` nei metodi che restituiscono `void`:

    ```csharp
    public class Example : Java.Lang.Object
    {
        // Invalid; generates XA4208
        [ExportField ("CREATOR")]
        public static void CreateSomething ()
        {
        }
    }
    ```

- Le istanze dei tipi generici _non devono_ essere create dal codice Java.
    Possono essere creati solo in modo sicuro dal codice gestito:

    ```csharp
    [Activity (Label="Die!", MainLauncher=true)]
    public class BadGenericActivity<T> : Activity
    {
        protected override void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);
        }
    }
    ```

## <a name="partial-java-generics-support"></a>Supporto di generics Java parziali

Il supporto per l'associazione dei generics Java è limitato. In particolare, i membri in una classe di istanza generica derivata da un'altra classe generica (senza istanze) vengono lasciati esposti come Java. lang. Object. Ad esempio, il metodo [Android. Content. Intent. GetParcelableExtra](xref:Android.Content.Intent.GetParcelableExtra*) restituisce Java. lang. Object. Ciò è dovuto a generics Java cancellati.
Sono disponibili alcune classi che non applicano questa limitazione, ma sono regolate manualmente.

## <a name="related-links"></a>Collegamenti correlati

- [Android Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md)
- [Uso di JNI](~/android/platform/java-integration/working-with-jni.md)
- [ExportAttribute](xref:Java.Interop.ExportAttribute)
- [Superstringhe](xref:Java.Interop.ExportAttribute.SuperArgumentsString)
- [RegisterAttribute](xref:Android.Runtime.RegisterAttribute)
