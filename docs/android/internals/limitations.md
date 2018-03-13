---
title: Limitazioni
ms.topic: article
ms.prod: xamarin
ms.assetid: F953F9B4-3596-8B3A-A8E4-8219B5B9F7CA
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: 1b970432d7cd5b6a84b8af72ab616493f3cd36a7
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="limitations"></a>Limitazioni

Poiché le applicazioni in Android richiedono la generazione di tipi di proxy del linguaggio durante il processo di compilazione, non è possibile generare tutto il codice in fase di esecuzione.

Queste sono le limitazioni di xamarin rispetto al desktop Mono:


## <a name="limited-dynamic-language-support"></a>Supporto limitato Dynamic Language

 [Android callable wrapper](~/android/platform/java-integration/android-callable-wrappers.md) sono necessari ogni volta che il runtime Android è necessario richiamare il codice gestito. Android callable wrapper vengono generati in fase di compilazione, in base alle analisi statica del linguaggio intermedio. Il risultato di questo: si *Impossibile* utilizzare linguaggi dinamici (IronPython, IronRuby, e così via) in qualsiasi scenario in cui sottoclassi di tipi Java obbligatorio (incluse le sottoclassi indiretta), poiché non è in alcun modo di estrazione di questi tipi dinamici in fase di compilazione per generare i wrapper richiamabili Android necessari.


## <a name="limited-java-generation-support"></a>Supporto per la generazione di Java limitato

[Android Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md) devono essere generati in ordine per il codice Java chiamare codice gestito. *Per impostazione predefinita*, Android callable wrapper conterrà solo (determinati) dichiarati costruttori e metodi che eseguono l'override di un metodo di Java virtual (vale a dire ha [ `RegisterAttribute` ](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)) o implementare una (metodo di interfaccia Java interfaccia in modo analogo è `Attribute`).
  
Prima del rilascio 4.1, è non stati dichiarati alcun metodi aggiuntivi. Con la versione 4.1, [il `Export` e `ExportField` attributi personalizzati possono essere utilizzati per dichiarare i metodi di Java e i campi all'interno di Android Callable Wrapper](~/android/platform/java-integration/working-with-jni.md).

### <a name="missing-constructors"></a>Costruttori mancanti

Costruttori rimangono complesso, a meno che non [ `ExportAttribute` ](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute) viene utilizzato. L'algoritmo per la generazione di costruttori Android wrapper RCW è che se viene emesso un costruttore di Java:

1. Un mapping di Java per tutti i tipi di parametro
2. La classe di base dichiara il costruttore stesso &ndash; questa operazione è necessaria perché il wrapper RCW Android *deve* richiamare il costruttore di classe di base corrispondente; Nessun argomenti predefiniti possono essere utilizzati (come non è facile per determinare i valori devono essere utilizzati all'interno di linguaggio).

Si consideri ad esempio la classe seguente:

```csharp
[Service]
class MyIntentService : IntentService {
    public MyIntentService (): base ("value")
    {
    }
}
```

Durante questo aspetto logico, il wrapper RCW Android risultante *nelle build di rilascio* non conterrà un costruttore predefinito. Di conseguenza, se si tenta di avviare il servizio (ad esempio [ `Context.StartService` ](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/)), verrà generato un errore:

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

La soluzione consiste nel dichiarare un costruttore predefinito, per decorare con il `ExportAttribute`e impostare il [ `ExportAttribute.SuperStringArgument` ](https://developer.xamarin.com/api/property/Java.Interop.ExportAttribute.SuperArgumentsString/): 

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


### <a name="generic-c-classes"></a>Classi generiche in c#

Classi generiche in c# sono supportate solo parzialmente. Esistono le limitazioni seguenti:


-   Non è possono utilizzare i tipi generici `[Export]` o `[ExportField`]. Il tentativo di eseguire questa operazione genererà un `XA4207` errore.

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

-   Metodi generici non possono utilizzare `[Export]` o `[ExportField]`:

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

-   `[ExportField]` non può essere utilizzata con metodi che restituiscono `void`:

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

-   Le istanze di tipi generici _non devono_ essere creati dal codice Java.
    Possono essere creati solo in modo sicuro da codice gestito:

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


## <a name="partial-java-generics-support"></a>Supporto dei Generics Java parziale

Il supporto dei generics associazione è limitato. In particolare, vengono lasciati i membri in una classe di istanza generica che deriva da un'altra classe generica (non-creare un'istanza) esposti come lang. Ad esempio, [Android.Content.Intent.GetParcelableExtra](https://developer.xamarin.com/api/member/Android.Content.Intent.GetParcelableExtra/p/System.String/) lang restituisce. Ciò è dovuto generics Java cancellato.
Sono disponibili alcune classi che non si applicano questa limitazione, ma vengono modificate manualmente.


## <a name="related-links"></a>Collegamenti correlati

- [Android Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md)
- [Utilizzo di JNI](~/android/platform/java-integration/working-with-jni.md)
- [ExportAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute/)
- [SuperString](https://developer.xamarin.com/api/property/Java.Interop.ExportAttribute.SuperArgumentsString/)
- [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)
