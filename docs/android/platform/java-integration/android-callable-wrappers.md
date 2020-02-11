---
title: Android Callable Wrapper per Xamarin.Android
ms.prod: xamarin
ms.assetid: C33E15FA-1E2B-819A-C656-CA588D611492
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: 7278fd624bb3147c2e1a1a1a79adde68813a9888
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020159"
---
# <a name="android-callable-wrappers-for-xamarinandroid"></a>Android Callable Wrapper per Xamarin.Android

Android Callable Wrapper (ACWs) è necessario ogni volta che il runtime di Android richiama codice gestito. Questi wrapper sono necessari perché non è possibile registrare le classi con l'arte (il runtime di Android) in fase di esecuzione. In particolare, la [funzione JNI defineClass ()](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp15986) non è supportata dal runtime di Android.} I wrapper richiamabili Android comportano quindi la mancanza di supporto per la registrazione dei tipi runtime. 

*Ogni volta* Il codice Android deve eseguire un `virtual` o un metodo di interfaccia `overridden` o implementato nel codice gestito, Xamarin.Android deve fornire un proxy Java in modo che questo metodo venga inviato al tipo gestito appropriato. Questi tipi di proxy Java sono codice Java che ha la stessa classe di base e l'elenco di interfacce Java come tipo gestito, implementando gli stessi costruttori e dichiarando i metodi di interfaccia e di classe base sottoposti a override. 

Android callable wrapper viene generato dal programma **monodroid. exe** durante il [processo di compilazione](~/android/deploy-test/building-apps/build-process.md): vengono generati per tutti i tipi che (direttamente o indirettamente) ereditano [java. lang. Object](xref:Java.Lang.Object). 

## <a name="android-callable-wrapper-naming"></a>Denominazione di Android Callable Wrapper

I nomi di pacchetto per Android Callable Wrapper si basano sull'MD5SUM del nome qualificato dall'assembly del tipo esportato. Questa tecnica di denominazione rende possibile che lo stesso nome di tipo completo venga reso disponibile da assembly diversi senza introdurre un errore di creazione del pacchetto. 

A causa di questo schema di denominazione MD5SUM, non è possibile accedere direttamente ai tipi in base al nome. Ad esempio, il comando di `adb` seguente non funzionerà perché il nome del tipo `my.ActivityType` non viene generato per impostazione predefinita: 

```shell
adb shell am start -n My.Package.Name/my.ActivityType
```

Se si tenta di fare riferimento a un tipo in base al nome, è inoltre possibile che vengano visualizzati errori simili ai seguenti:

```shell
java.lang.ClassNotFoundException: Didn't find class "com.company.app.MainActivity"
on path: DexPathList[[zip file "/data/app/com.company.App-1.apk"] ...
```

Se è necessario accedere ai tipi in base al nome *, è possibile* dichiarare un nome per quel tipo in una dichiarazione di attributo. Ecco, ad esempio, il codice che dichiara un'attività con il nome completo `My.ActivityType`:

```csharp
namespace My {
    [Activity]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

È possibile impostare la proprietà `ActivityAttribute.Name` per dichiarare in modo esplicito il nome dell'attività: 

```csharp
namespace My {
    [Activity(Name="my.ActivityType")]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

Una volta aggiunta questa impostazione della proprietà, è possibile accedere a `my.ActivityType` in base al nome dal codice esterno e dagli script di `adb`. È possibile impostare l'attributo `Name` per molti tipi diversi, tra cui `Activity`, `Application`, `Service`, `BroadcastReceiver`e `ContentProvider`: 

- [ActivityAttribute.Name](xref:Android.App.ActivityAttribute.Name)
- [ApplicationAttribute.Name](xref:Android.App.ApplicationAttribute.Name)
- [ServiceAttribute.Name](xref:Android.App.ServiceAttribute.Name)
- [BroadcastReceiverAttribute.Name](xref:Android.Content.BroadcastReceiverAttribute.Name)
- [ContentProviderAttribute.Name](xref:Android.Content.ContentProviderAttribute.Name)

La denominazione ACW basata su MD5SUM è stata introdotta in Xamarin.Android 5,0. Per ulteriori informazioni sulla denominazione degli attributi, vedere [RegisterAttribute](xref:Android.Runtime.RegisterAttribute). 

## <a name="implementing-interfaces"></a>Implementazione di interfacce

In alcuni casi potrebbe essere necessario implementare un'interfaccia Android, ad esempio [Android. Content. IComponentCallbacks](xref:Android.Content.IComponentCallbacks). Poiché tutte le classi e l'interfaccia Android estendono l'interfaccia [Android. Runtime. IJavaObject](xref:Android.Runtime.IJavaObject) , si pone la domanda: come si implementano `IJavaObject`? 

La domanda è stata risposta in precedenza: il motivo per cui tutti i tipi Android devono implementare `IJavaObject` è in modo che Xamarin.Android disponga di un Android Callable Wrapper da fornire ad Android, ovvero un proxy Java per il tipo specificato. Poiché **monodroid. exe** cerca solo `Java.Lang.Object` sottoclassi e `Java.Lang.Object` implementa `IJavaObject,` la risposta è ovvia: sottoclasse `Java.Lang.Object`: 

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

## <a name="implementation-details"></a>Dettagli sull'implementazione

*Il resto di questa pagina fornisce i dettagli di implementazione soggetti a modifiche senza preavviso* (e viene presentato qui solo perché gli sviluppatori saranno curiosi di sapere cosa sta accadendo). 

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

Si noti che la classe di base viene mantenuta e vengono fornite `native` dichiarazioni di metodo per ogni metodo sottoposto a override all'interno del codice gestito. 
