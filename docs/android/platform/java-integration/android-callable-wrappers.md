---
title: Android Callable Wrapper
ms.prod: xamarin
ms.assetid: C33E15FA-1E2B-819A-C656-CA588D611492
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: bb15c7f3a36cc7f1ed235d92e343bbae67a718b8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="android-callable-wrappers"></a>Android Callable Wrapper

Android Callable Wrapper (ACWs) sono necessari ogni volta che il runtime Android Richiama il codice gestito. Questi wrapper sono necessari poiché non è possibile registrare le classi di ART (runtime Android) in fase di esecuzione. (In particolare, il [DefineClass() JNI funzione](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp15986) non è supportato dal runtime di Android.} Android Callable Wrapper costituiscono pertanto la mancanza di supporto di registrazione di tipo di runtime. 

*Ogni volta che* codice Android è necessario eseguire un `virtual` o metodo di interfaccia `overridden` o implementato in codice gestito, xamarin deve fornire un proxy Java in modo che questo metodo viene inviato al tipo gestito appropriato. Questi tipi di proxy di Java sono codice Java che è la classe di base "stesso" e di un elenco delle interfacce Java come tipo gestito, i costruttori stessi implementazione e la dichiarazione di qualsiasi classe di base sottoposto a override e i metodi di interfaccia. 

Android callable wrapper generati dal **monodroid.exe** programma durante la [processo di compilazione](~/android/deploy-test/building-apps/build-process.md): vengono generati per tutti i tipi che ereditano (direttamente o indirettamente) [ Lang](https://developer.xamarin.com/api/type/Java.Lang.Object/). 



## <a name="android-callable-wrapper-naming"></a>Denominazione Android Callable Wrapper

Nomi dei pacchetti per Android Callable Wrapper sono basati su MD5SUM del nome completo dell'assembly del tipo da esportare. Questa tecnica denominazione rende possibile lo stesso nome di tipo completo per essere rese disponibili dagli assembly diversi senza introdurre un errore del pacchetto. 

A causa di questo schema di denominazione MD5SUM, è possibile accedere direttamente ai tipi in base al nome. Ad esempio, `adb` comando non funziona perché il nome del tipo `my.ActivityType` non viene generato per impostazione predefinita: 

```shell
adb shell am start -n My.Package.Name/my.ActivityType
```

Inoltre, è possibile riscontrare errori simile al seguente se si tenta di fare riferimento a un tipo in base al nome:

```shell
java.lang.ClassNotFoundException: Didn't find class "com.company.app.MainActivity"
on path: DexPathList[[zip file "/data/app/com.company.App-1.apk"] ...
```

Se si *si* richiedono l'accesso a tipi in base al nome, è possibile dichiarare un nome per tale tipo in una dichiarazione di attributo. Ad esempio, ecco il codice che dichiara un'attività con il nome completo `My.ActivityType`:

```csharp
namespace My {
    [Activity]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

Il `ActivityAttribute.Name` può essere impostata su dichiarare in modo esplicito il nome di questa attività: 

```csharp
namespace My {
    [Activity(Name="my.ActivityType")]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

Dopo l'aggiunta di questa impostazione di proprietà, `my.ActivityType` accessibili in base al nome del codice esterno e da `adb` script. Il `Name` attributo può essere impostato per molti tipi diversi tra `Activity`, `Application`, `Service`, `BroadcastReceiver`, e `ContentProvider`: 

-   [ActivityAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Name/)
-   [ApplicationAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ApplicationAttribute.Name/)
-   [ServiceAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ServiceAttribute.Name/)
-   [BroadcastReceiverAttribute.Name](https://developer.xamarin.com/api/property/Android.Content.BroadcastReceiverAttribute.Name/)
-   [ContentProviderAttribute.Name](https://developer.xamarin.com/api/property/Android.Content.ContentProviderAttribute.Name/)

Basato su MD5SUM ACW denominazione è stata introdotta in xamarin 5.0. Per ulteriori informazioni sulla denominazione degli attributi, vedere [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/). 



## <a name="implementing-interfaces"></a>Implementazione di interfacce

Talvolta, quando è necessario implementare un'interfaccia Android, ad esempio [Android.Content.IComponentCallbacks](https://developer.xamarin.com/api/type/Android.Content.IComponentCallbacks/). Poiché tutte le classi di Android e interfaccia estendere il [Android.Runtime.IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) si presenta di interfaccia, la domanda: come è implementare `IJavaObject`? 

È stata risposta alla domanda precedente: il motivo tutti i tipi di Android devono implementare `IJavaObject` in modo da xamarin è un wrapper richiamabile Android per fornire a Android, vale a dire un proxy di linguaggio per il tipo specificato. Poiché **monodroid.exe** Cerca solo `Java.Lang.Object` sottoclassi, e `Java.Lang.Object` implementa `IJavaObject,` la risposta è ovvia: sottoclasse `Java.Lang.Object`: 

```csharp
class MyComponentCallbacks : Java.Lang.Object, Android.Content.IComponentCallbacks {

    public void OnConfigurationChanged (Android.Content.Res.Configuration newConfig)
    {
        // implementation goes here...
    } 

    public void OnLowMemory ()
    {
        // implementation goes here...
    }
}
```


## <a name="implementation-details"></a>Dettagli di implementazione

*Nella parte restante di questa pagina fornisce i dettagli di implementazione soggette a modifiche senza preavviso* (e di seguito vengono forniti solo perché gli sviluppatori sarà conoscere cosa accade in). 

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

public class HelloAndroid
    extends android.app.Activity
{
    static final String __md_methods;
    static {
        __md_methods = "n_onCreate:(Landroid/os/Bundle;)V:GetOnCreate_Landroid_os_Bundle_Handler\n" + "";
        mono.android.Runtime.register (
            "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, 
            Culture=neutral, PublicKeyToken=null", HelloAndroid.class, __md_methods);
    }

    public HelloAndroid ()
    {
        super ();
        if (getClass () == HelloAndroid.class)
            mono.android.TypeManager.Activate (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, 
                Culture=neutral, PublicKeyToken=null", "", this, new java.lang.Object[] {  });
    }

    @Override
    public void onCreate (android.os.Bundle p0)
    {
        n_onCreate (p0);
    }

    private native void n_onCreate (android.os.Bundle p0);
}
```

Si noti che la classe di base viene mantenuta, e `native` le dichiarazioni di metodo vengono fornite per ogni metodo che viene eseguito l'override all'interno di codice gestito. 
