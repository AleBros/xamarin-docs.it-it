---
title: Introduzione ad Android
description: Questo documento descrive come iniziare a usare .NET incorporamento con Android. Vengono illustrati l'installazione di .NET di incorporamento, creazione di un progetto di libreria Android, in base all'output generato in un progetto Android Studio e altre considerazioni.
ms.prod: xamarin
ms.assetid: 870F0C18-A794-4C5D-881B-64CC78759E30
author: lobrien
ms.author: laobri
ms.date: 03/28/2018
ms.openlocfilehash: e60892edfcf73f3e7cada8923e16bcc1be2c203e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121386"
---
# <a name="getting-started-with-android"></a>Introduzione ad Android

Oltre ai requisiti dei [Introduzione a Java](~/tools/dotnet-embedding/get-started/java/index.md) Guida è inoltre necessario:

* [Xamarin. Android 7.5](https://visualstudio.microsoft.com/xamarin/) o versione successiva
* [Android Studio 3.x](https://developer.android.com/studio/index.html) con Java 1.8

Come una panoramica, si apprenderà come:

* Creare un C# progetto di libreria Android
* Installare .NET incorporamento tramite NuGet
* Eseguire l'incorporamento di .NET dall'assembly di libreria Android
* Usare il file AAR generato in un progetto Java in Android Studio

## <a name="create-an-android-library-project"></a>Creare un progetto di libreria Android

Aprire Visual Studio per Windows o Mac, creare un nuovo progetto di libreria di classi Android, denominarlo **hello-da-csharp**e salvarlo **~/Projects/hello-from-csharp** o **%USERPROFILE%\ Projects\hello-da-csharp**.

Aggiungere una nuova attività Android denominato **HelloActivity.cs**, seguito da un Layout di Android in **Resource/layout/hello.axml**.

Aggiungere un nuovo `TextView` al layout e modificare il testo in modo piacevole.

L'origine di layout dovrebbe essere simile al seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <TextView
        android:text="Hello from C#!"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center" />
</LinearLayout>
```

Nell'attività, assicurarsi che si sta chiamando `SetContentView` con il nuovo layout:

```csharp
[Activity(Label = "HelloActivity"),
    Register("hello_from_csharp.HelloActivity")]
public class HelloActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        SetContentView(Resource.Layout.hello);
    }
}
```

> [!NOTE]
> Non dimenticare di `[Register]` attributo. Per informazioni dettagliate, vedere [limitazioni](#current-limitations-on-android).

Compilare il progetto. L'assembly risultante verrà salvato `bin/Debug/hello-from-csharp.dll`.

## <a name="installing-net-embedding-from-nuget"></a>Installazione di incorporamento di .NET da NuGet

Seguire queste [istruzioni](~/tools/dotnet-embedding/get-started/install/install.md) per installare e configurare .NET l'incorporamento per il progetto.

La chiamata del comando che è necessario configurare avrà un aspetto simile al seguente:

### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

```shell
mono '${SolutionDir}/packages/Embeddinator-4000.0.4.0.0/tools/Embeddinator-4000.exe' '${TargetPath}' --gen=Java --platform=Android --outdir='${SolutionDir}/output' -c
```

#### <a name="visual-studio-2017"></a>Visual Studio 2017

```shell
set E4K_OUTPUT="$(SolutionDir)output"
if exist %E4K_OUTPUT% rmdir /S /Q %E4K_OUTPUT%
"$(SolutionDir)packages\Embeddinator-4000.0.2.0.80\tools\Embeddinator-4000.exe" "$(TargetPath)" --gen=Java --platform=Android --outdir=%E4K_OUTPUT% -c
```

## <a name="use-the-generated-output-in-an-android-studio-project"></a>Usare l'output generato in un progetto Android Studio

1. Aprire Android Studio e creare un nuovo progetto con un **attività vuota**.
2. Fare clic sui **app** modulo e scegliere **New > modulo**.
3. Selezionare **importazione. JAR /. Oltre al pacchetto AAR**.
4. Usare il browser di directory per individuare **~/Projects/hello-from-csharp/output/hello_from_csharp.aar** e fare clic su **fine**.

![Importazione AAR in Android Studio](android-images/androidstudioimport.png)

In un nuovo modulo denominato verranno copiati i file AAR **hello_from_csharp**.

![Progetto Android Studio](android-images/androidstudioproject.png)

Usare il nuovo modulo dalle **app**destro del mouse e scegliere **Apri impostazioni modulo**. Nel **dipendenze** scheda, aggiungere un nuovo **modulo dipendenza** e scegliere **: hello_from_csharp**.

![Dipendenze di Android Studio](android-images/androidstudiodependencies.png)

Nell'attività, aggiungere una nuova `onResume` metodo e utilizzare il codice seguente per avviare la C# attività:

```java
import hello_from_csharp.*;

public class MainActivity extends AppCompatActivity {
    //... Other stuff here ...
    @Override
    protected void onResume() {
        super.onResume();

        Intent intent = new Intent(this, HelloActivity.class);
        startActivity(intent);
    }
}
```

### <a name="assembly-compression-important"></a>La compressione di assembly (*importante*)

Un'ulteriore modifica è necessaria per l'incorporamento di .NET nel progetto Android Studio.

Aprire l'app **Build. gradle** file e aggiungere le seguenti modifiche:

```groovy
android {
    // ...
    aaptOptions {
        noCompress 'dll'
    }
}
```

Xamarin. Android attualmente vengono caricati gli assembly .NET direttamente dall'APK, ma richiede gli assembly non verrà compresso.

Se non hai il programma di installazione, l'app verrà arrestarsi in modo anomalo all'avvio e stampare sulla console simile al seguente:

```shell
com.xamarin.hellocsharp A/monodroid: No assemblies found in '(null)' or '<unavailable>'. Assuming this is part of Fast Deployment. Exiting...
```

## <a name="run-the-app"></a>Eseguire l'app

Una volta avviata l'app:

![Hello di C# esempio in esecuzione nell'emulatore](android-images/hello-from-csharp-android.png)

Si noti che cosa è successo qui:

* È disponibile un C# (classe), `HelloActivity`, tale Java sottoclassi
* Sono disponibili i file di risorse Android
* È stato usato da Java in Android Studio

Per questo esempio funzioni, le condizioni seguenti vengono impostate nell'APK finale:

* Xamarin. Android è configurato all'avvio dell'applicazione
* Assembly .NET inclusi in **asset/assembly**
* **Androidmanifest. XML** modifiche per il C# attività e così via.
* Risorse Android e gli asset da librerie di .NET
* [Android Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md) per qualsiasi `Java.Lang.Object` sottoclasse

Se si sta cercando un'altra procedura dettagliata, vedere il video seguente, che illustra l'incorporamento Petzolds [Pittura con le dita demo](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint/) in un progetto Android Studio:

[![Embeddinator-4000 per Android](https://img.youtube.com/vi/ZVcrXUpCNpI/0.jpg)](https://www.youtube.com/watch?v=ZVcrXUpCNpI)

## <a name="using-java-18"></a>Uso di Java 1.8

A partire dalla redazione, l'opzione migliore consiste nell'usare Android Studio 3.0 ([scaricare qui](https://developer.android.com/studio/index.html)).

Per abilitare Java 1.8 del modulo di app **Build. gradle** file:

```groovy
android {
    // ...
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
```

È anche possibile dare un'occhiata un' [progetto di test di Android Studio](https://github.com/mono/Embeddinator-4000/blob/master/tests/android/app/build.gradle) per altri dettagli. 

Se si vogliono usare stabile 2.3.x Android Studio, è necessario abilitare la toolchain di Jack deprecata:

```groovy
android {
    // ..
    defaultConfig {
        // ...
        jackOptions.enabled true
    }
}
```

## <a name="current-limitations-on-android"></a>Limitazioni correnti in Android

Pulsante destro del mouse a questo punto, se si crea una sottoclasse `Java.Lang.Object`, xamarin. Android genera lo stub di Java (Android Callable Wrapper) invece di incorporare .NET. Per questo motivo, è necessario seguire le stesse regole per l'esportazione di C# a Java come xamarin. Android. Ad esempio:

```csharp
[Register("mono.embeddinator.android.ViewSubclass")]
public class ViewSubclass : TextView
{
    public ViewSubclass(Context context) : base(context) { }

    [Export("apply")]
    public void Apply(string text)
    {
        Text = text;
    }
}
```

* `[Register]` è necessario eseguire il mapping a un nome di pacchetto Java desiderato
* `[Export]` è necessario per rendere un metodo visibile a Java

È possibile usare `ViewSubclass` in Java come segue:

```java
import mono.embeddinator.android.ViewSubclass;
//...
ViewSubclass v = new ViewSubclass(this);
v.apply("Hello");
```

Altre informazioni, vedere [integrazione di Java con xamarin. Android](~/android/platform/java-integration/index.md).

## <a name="multiple-assemblies"></a>Più assembly

Incorporamento di un singolo assembly è semplice. Tuttavia, è molto più probabile che sarà necessario più di un C# assembly. Molte volte si avrà le dipendenze per i pacchetti NuGet, ad esempio le librerie di supporto per Android o Google Play Services che complicare ulteriormente le cose.

In questo modo un dilemma, poiché .NET incorporamento deve includere molti tipi di file nei AAR finale, ad esempio:

* Asset Android
* Risorse Android
* Librerie native di Android
* Codice sorgente java Android

Molto probabilmente non si desidera includere questi file dalla libreria di supporto per Android o Google Play Services nel AAR, ma preferisce usare la versione ufficiale di Google in Android Studio.

Ecco l'approccio consigliato:

* Passare a .NET incorporamento a qualsiasi assembly cui si è proprietari (disponibile il codice sorgente) e si vuole chiamare da Java
* Passare a .NET incorporamento a tutti gli assembly necessari asset Android, le librerie native o le risorse da
* Aggiungere le dipendenze Java, ad esempio Android supportano libreria o Google Play Services in Android Studio

Pertanto, il comando potrebbe essere:

```shell
mono Embeddinator-4000.exe --gen=Java --platform=Android -c -o output YourMainAssembly.dll YourDependencyA.dll YourDependencyB.dll
```

È consigliabile escludere nulla da NuGet, a meno che non si scopre contiene gli asset Android, risorse e così via che sarà necessario nel progetto Android Studio. È anche possibile omettere le dipendenze che non è necessario chiamare da Java e il linker _dovrebbero_ figurano le parti della libreria che sono necessari.

Per aggiungere tutte le dipendenze Java necessari in Android Studio, il **Build. gradle** file potrebbe essere simile:

```groovy
dependencies {
    // ...
    compile 'com.android.support:appcompat-v7:25.3.1'
    compile 'com.google.android.gms:play-services-games:11.0.4'
    // ...
}
```

## <a name="further-reading"></a>Ulteriori informazioni

* [Callback in Android](~/tools/dotnet-embedding/android/callbacks.md)
* [Ricerca preliminare Android](~/tools/dotnet-embedding/android/index.md)
* [Limitazioni di incorporamento di .NET](~/tools/dotnet-embedding/limitations.md)
* [Aggiunta come contributo al progetto open source](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Le descrizioni e i codici di errore](~/tools/dotnet-embedding/errors.md)

## <a name="related-links"></a>Collegamenti correlati

- [Esempio meteo (Android)](https://github.com/jamesmontemagno/embeddinator-weather)
