---
title: Xamarin.Android vs. Desktop - differenze nel Runtime di Mono
ms.prod: xamarin
ms.assetid: F953F9B4-3596-8B3A-A8E4-8219B5B9F7CA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 115d715214d7af3174c41d9d82e894ce429dab42
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60953344"
---
# <a name="limitations"></a>Limitazioni

Poiché le applicazioni su Android richiedono la generazione di tipi proxy Java durante il processo di compilazione, non è possibile generare tutto il codice in fase di esecuzione.

Queste sono le limitazioni di xamarin. Android rispetto al desktop Mono:


## <a name="limited-dynamic-language-support"></a>Supporto per linguaggi dinamici limitato

 [Android callable wrapper](~/android/platform/java-integration/android-callable-wrappers.md) sono necessari ogni volta il runtime di Android è necessario per richiamare il codice gestito. Android callable wrapper vengono generati in fase di compilazione basato su analisi statica del linguaggio intermedio. Il risultato finale di questo oggetto: si *non è possibile* si usano linguaggi dinamici (IronPython, IronRuby e così via) in qualsiasi scenario in cui è richiesto (tra cui sottoclassi indiretto), creazione di una sottoclasse di tipi di Java, a quanto non accade in alcun modo di estrazione di questi tipi dinamici in fase di compilazione per generare l'Android callable wrapper necessari.


## <a name="limited-java-generation-support"></a>Supporto per la generazione limitata Java

[Android Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md) devono essere generati in ordine per il codice Java chiamare codice gestito. *Per impostazione predefinita*, Android callable wrapper conterrà solo (alcuni) costruttori dichiarati e i metodi che eseguono l'override di un metodo virtuale Java (vale a dire dispone [ `RegisterAttribute` ](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)) o implementare una (metodo di interfaccia Java interfaccia allo stesso modo dispone di `Attribute`).
  
Prima della versione 4.1, non potrebbe essere dichiarati metodi aggiuntivi. Con la versione 4.1 [il `Export` e `ExportField` attributi personalizzati possono essere utilizzati per dichiarare metodi Java e i campi all'interno di Android Callable Wrapper](~/android/platform/java-integration/working-with-jni.md).

### <a name="missing-constructors"></a>Costruttori mancanti

I costruttori rimangono complesso, a meno che [ `ExportAttribute` ](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute) viene usato. L'algoritmo per la generazione di costruttori di Android callable wrapper è che verrà generato un costruttore di Java se:

1. Viene applicato un mapping per tutti i tipi di parametro Java
2. La classe di base dichiara il costruttore stesso &ndash; questa operazione è necessaria perché il Android callable wrapper *necessario* richiamare il costruttore di classe di base corrispondente; nessun argomento predefinito può essere utilizzato (perché non esiste un modo semplice per determinare quali valori devono essere utilizzati all'interno di Java).

Si consideri ad esempio la classe seguente:

```csharp
[Service]
class MyIntentService : IntentService {
    public MyIntentService (): base ("value")
    {
    }
}
```

Anche se questo ha un aspetto logico, il risultante Android callable wrapper *nelle build di rilascio* non conterrà un costruttore predefinito. Di conseguenza, se si tenta di avviare il servizio (ad esempio [ `Context.StartService` ](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/)), viene restituito un errore:

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

La soluzione alternativa consiste nel dichiarare un costruttore predefinito, decorare con il `ExportAttribute`e impostare il [ `ExportAttribute.SuperStringArgument` ](https://developer.xamarin.com/api/property/Java.Interop.ExportAttribute.SuperArgumentsString/): 

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


### <a name="generic-c-classes"></a>Generic C# classi

Generic C# le classi sono supportate solo parzialmente. Sono presenti le limitazioni seguenti:


-   Non è possono usare i tipi generici `[Export]` o `[ExportField`]. Tentativo di eseguire questa operazione genererà un `XA4207` errore.

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

-   Non è possono usare i metodi generici `[Export]` o `[ExportField]`:

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

-   `[ExportField]` non può essere utilizzata con i metodi che restituiscono `void`:

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

-   Le istanze di tipi generici _non deve_ essere creata dal codice Java.
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


## <a name="partial-java-generics-support"></a>Supporto dei Generics Java parziale

Il linguaggio supportano i generics di associazione è limitato. In particolare, vengono lasciati i membri in una classe di istanza generica che è derivato da un'altra classe generica (non-creare un'istanza) esposti come Java.Lang.Object. Ad esempio, [Android.Content.Intent.GetParcelableExtra](https://developer.xamarin.com/api/member/Android.Content.Intent.GetParcelableExtra/p/System.String/) Java.Lang.Object termina metodo. Ciò è dovuto cancellato generics di Java.
Sono presenti alcune classi che non si applicano questa limitazione, ma essi vengono modificate manualmente.


## <a name="related-links"></a>Collegamenti correlati

- [Android Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md)
- [Utilizzo di JNI](~/android/platform/java-integration/working-with-jni.md)
- [ExportAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute/)
- [SuperString](https://developer.xamarin.com/api/property/Java.Interop.ExportAttribute.SuperArgumentsString/)
- [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)
