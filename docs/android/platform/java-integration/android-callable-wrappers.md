---
title: Android Callable Wrapper
ms.prod: xamarin
ms.assetid: C33E15FA-1E2B-819A-C656-CA588D611492
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 7edbdaa5a690a641523cb5baad7909ed01992aa5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121815"
---
# <a name="android-callable-wrappers"></a>Android Callable Wrapper

Android Callable Wrapper (ACWs) sono necessari ogni volta che il runtime di Android Richiama il codice gestito. Questi wrapper sono necessari perché non è possibile registrare le classi con ART (runtime di Android) in fase di esecuzione. (In particolare, il [JNI DefineClass() funzione](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp15986) non è supportato dal runtime di Android.} Android Callable Wrapper costituiscono pertanto per la mancanza di supporto di registrazione tipo di runtime. 

*Ogni volta che* codice Android è necessario eseguire una `virtual` o un metodo di interfaccia `overridden` o implementato in codice gestito, xamarin. Android è necessario fornire un proxy di Java in modo che questo metodo viene inviato al tipo gestito appropriato. Questi tipi di proxy Java sono codice Java che include la classe di base "stesso" e un elenco delle interfacce Java come tipo gestito, che implementa il costruttore stesso e la dichiarazione di qualsiasi classe di base sottoposto a override e i metodi di interfaccia. 

Android callable wrapper vengono generati dal **monodroid.exe** programma durante la [processo di compilazione](~/android/deploy-test/building-apps/build-process.md): vengono generati per tutti i tipi che ereditano (direttamente o indirettamente) [ Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/). 



## <a name="android-callable-wrapper-naming"></a>Android Callable Wrapper di denominazione

Nomi dei pacchetti per Android Callable Wrapper sono basati su MD5SUM del nome qualificato dall'assembly del tipo in fase di esportazione. Questa tecnica denominazione rende possibile per lo stesso nome di tipo completo di essere rese disponibili dagli assembly diverso senza introdurre un errore di creazione dei pacchetti. 

A causa di questo schema di denominazione MD5SUM, è possibile accedere direttamente i tipi in base al nome. Ad esempio, il seguente `adb` comando non funziona perché il nome del tipo `my.ActivityType` non viene generato per impostazione predefinita: 

```shell
adb shell am start -n My.Package.Name/my.ActivityType
```

Inoltre, è possibile riscontrare errori simile al seguente se si tenta di fare riferimento a un tipo in base al nome:

```shell
java.lang.ClassNotFoundException: Didn't find class "com.company.app.MainActivity"
on path: DexPathList[[zip file "/data/app/com.company.App-1.apk"] ...
```

Se si *scopo* richiedono l'accesso ai tipi in base al nome, è possibile dichiarare un nome per tale tipo in una dichiarazione di attributo. Ad esempio, ecco il codice che dichiara un'attività con il nome completo `My.ActivityType`:

```csharp
namespace My {
    [Activity]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

Il `ActivityAttribute.Name` proprietà può essere impostata a dichiarare esplicitamente il nome dell'attività corrente: 

```csharp
namespace My {
    [Activity(Name="my.ActivityType")]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

Dopo l'aggiunta di questa impostazione di proprietà, `my.ActivityType` sono accessibili in base al nome da codice esterno e da `adb` script. Il `Name` attributo può essere impostato per molti tipi diversi tra cui `Activity`, `Application`, `Service`, `BroadcastReceiver`, e `ContentProvider`: 

-   [ActivityAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Name/)
-   [ApplicationAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ApplicationAttribute.Name/)
-   [ServiceAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ServiceAttribute.Name/)
-   [BroadcastReceiverAttribute.Name](https://developer.xamarin.com/api/property/Android.Content.BroadcastReceiverAttribute.Name/)
-   [ContentProviderAttribute.Name](https://developer.xamarin.com/api/property/Android.Content.ContentProviderAttribute.Name/)

Denominazione ACW basata su MD5SUM è stata introdotta in xamarin. Android 5.0. Per altre informazioni sulla denominazione degli attributi, vedere [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/). 



## <a name="implementing-interfaces"></a>Implementazione di interfacce

Quando potrebbe essere necessario implementare un'interfaccia di Android, ad esempio talvolta [Android.Content.IComponentCallbacks](https://developer.xamarin.com/api/type/Android.Content.IComponentCallbacks/). Poiché tutte le classi di Android e interfaccia estende la [Android.Runtime.IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) interfaccia, la domanda sorge: come Implementiamo `IJavaObject`? 

La domanda è stata risposta sopra: il motivo per tutti i tipi di Android devono implementare `IJavaObject` in modo da xamarin. Android è un wrapper chiamabile Android per fornire ad Android, vale a dire un proxy di Java per il tipo specificato. Poiché **monodroid.exe** Cerca solo `Java.Lang.Object` sottoclassi, e `Java.Lang.Object` implementa `IJavaObject,` la risposta è ovvia: sottoclasse `Java.Lang.Object`: 

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

*La parte restante di questa pagina fornisce i dettagli di implementazione soggette a modifiche senza preavviso* (e viene presentato in questo caso solo perché gli sviluppatori saranno sono curiosi riguardo cosa sta succedendo). 

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

Si noti che la classe di base viene conservata, e `native` le dichiarazioni di metodo vengono fornite per ogni metodo sottoposto a override nel codice gestito. 
