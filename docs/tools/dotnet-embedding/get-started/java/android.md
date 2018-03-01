---
title: Introduzione a Android
ms.topic: article
ms.prod: xamarin
ms.assetid: 870F0C18-A794-4C5D-881B-64CC78759E30
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: cb74a814db8d2ca8549ae292bbff880ca9383aa4
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="getting-started-with-android"></a>Introduzione a Android


Oltre ai requisiti dal nostro [Introduzione al linguaggio](~/tools/dotnet-embedding/get-started/java/index.md) Guida sarà inoltre necessario:

* Xamarin 7.4.99 o versione successiva (compilare da [Jenkins](https://jenkins.mono-project.com/view/Xamarin.Android/job/xamarin-android/lastSuccessfulBuild/Azure/))
* [Android Studio 3. x](https://developer.android.com/studio/index.html) con Java 1.8

Come una panoramica, Microsoft:

* Creare un progetto di libreria Android in c#
* Installare .NET incorporamento tramite NuGet
* Eseguire Embeddinator dall'assembly di libreria Android
* Utilizzare il file AAR generato in un progetto Java in Android Studio

## <a name="create-an-android-library-project"></a>Creare un progetto di libreria Android

Aprire Visual Studio per Windows o Mac, creare un nuovo progetto di libreria Android, denominarla `hello-from-csharp`e salvarlo in `~/Projects/hello-from-csharp` o `%USERPROFILE%\Projects\hello-from-csharp`.

Aggiungere una nuova attività Android denominato `HelloActivity.cs`, seguito da un Layout in Android `Resource/layout/hello.axml`.

Aggiungere un nuovo `TextView` al layout e modifica il testo su qualcosa di semplice.

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
*Nota: non dimenticare di `[Register]` attributo, vedere i dettagli in limitazioni*

Compilare il progetto, l'assembly risultante verrà salvato `bin/Debug/hello-from-csharp.dll`.

## <a name="installing-net-embedding-from-nuget"></a>L'installazione di .NET incorporamento da NuGet

Scegliere **aggiungere > aggiungere pacchetti NuGet...**  e installare `Embeddinator-4000` da Gestione pacchetti NuGet: ![Gestione pacchetti NuGet](android-images/visualstudionuget.png)

Verrà installato `Embeddinator-4000.exe` nel `packages/Embeddinator-4000/tools` directory.

## <a name="run-embeddinator-4000"></a>Run Embeddinator-4000

Verrà aggiunto un passaggio di post-compilazione per eseguire Embeddinator e creare un file AAR nativo per l'assembly del progetto di libreria Android.

In Visual Studio per Mac, passare a _opzioni progetto > compilare > comandi personalizzati_ e aggiungere un _dopo la compilazione_ passaggio.

Installazione di commnd seguenti:
```
mono '${SolutionDir}/packages/Embeddinator-4000.0.2.0.80/tools/Embeddinator-4000.exe' '${TargetPath}' --gen=Java --platform=Android --outdir='${SolutionDir}/output' -c
```

> [!NOTE]
> Nota: assicurarsi di utilizzare il numero di versione che è stato installato da NuGet

Se si intende eseguire continuo sviluppo del progetto c#, è possibile anche aggiungere un comando personalizzato per eseguire la pulizia di `output` directory prima dell'esecuzione Embeddinator:
```
rm -Rf '${SolutionDir}/output/'
```

![Azione di compilazione personalizzata](android-images/visualstudiocustombuild.png)

Il file AAR Android verrà inserito nelle `~/Projects/hello-from-csharp/output/hello_from_csharp.aar`. _Nota: poiché Java non supporta tale nei nomi di pacchetto, trattini vengono sostituiti._

### <a name="running-net-embedding-on-windows"></a>In esecuzione .NET incorporamento in Windows

Si installerà essenzialmente la stessa operazione, ma sono leggermente diversi menu in Visual Studio in Windows. Inoltre, i comandi della shell sono leggermente diversi.

Passare a **opzioni progetto > eventi di compilazione** e immettere quanto segue nel **riga di comando eventi post-compilazione** casella:
```
set E4K_OUTPUT="$(SolutionDir)output"
if exist %E4K_OUTPUT% rmdir /S /Q %E4K_OUTPUT%
"$(SolutionDir)packages\Embeddinator-4000.0.2.0.80\tools\Embeddinator-4000.exe" "$(TargetPath)" --gen=Java --platform=Android --outdir=%E4K_OUTPUT% -c
```

Ad esempio nella schermata seguente:

![Embeddinator in Windows](android-images/visualstudiowindows.png)

## <a name="use-the-generated-output-in-an-android-studio-project"></a>Utilizzare l'output generato in un progetto Android Studio

1. Aprire Android Studio e creare un nuovo progetto con un **attività vuota**.
2. Fare clic sui `app` modulo e scegliere **nuovo > modulo**.
3. Selezionare **importazione. FILE JAR /. Pacchetto AAR**.
4. Utilizzare la visualizzazione directory per individuare `~/Projects/hello-from-csharp/output/hello_from_csharp.aar` e fare clic su **fine**.

![Importazione AAR in Android Studio](android-images/androidstudioimport.png)

Il file AAR verranno copiati nel nuovo modulo denominato `hello_from_csharp`.

![Progetto Android Studio](android-images/androidstudioproject.png)

Usare il nuovo modulo dal `app`destro del mouse e scegliere **aprire le impostazioni del modulo**. Nel **dipendenze** scheda, aggiungere un nuovo **modulo dipendenza** e scegliere `:hello_from_csharp`.

![Dipendenze di Android Studio](android-images/androidstudiodependencies.png)

Nell'attività, aggiungere un nuovo `onResume` metodo e utilizzare il codice seguente per avviare l'attività di c#:

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

### <a name="assembly-compression-important"></a>La compressione di assembly *importante*

Un'ulteriore modifica è necessaria per l'incorporamento .NET nel progetto Android Studio.

Apri l'app `build.gradle` file e aggiungere le seguenti modifiche:
```groovy
android {
    // ...
    aaptOptions {
        noCompress 'dll'
    }
}
```
Xamarin attualmente carica gli assembly .NET direttamente il file APK, ma sono richiesti gli assembly non verrà compresso.

Se non si dispone di questo programma di installazione, l'app verrà un arresto anomalo all'avvio e stampare sulla console simile al seguente:

```csharp
com.xamarin.hellocsharp A/monodroid: No assemblies found in '(null)' or '<unavailable>'. Assuming this is part of Fast Deployment. Exiting...
```

## <a name="run-the-app"></a>Eseguire l'app

All'avvio dell'app:

![Hello c# di esempio in esecuzione nell'emulatore](android-images/hello-from-csharp-android.png)

Si noti che cosa è successo qui:

* È disponibile una classe c# `HelloActivity`, tale linguaggio di sottoclassi
* Sono disponibili file di risorse Android
* È stato usato dal linguaggio in Android Studio

Il funzionamento di questo esempio, tutte le seguenti opzioni sono l'installazione nell'APK finale:

* Xamarin è configurato all'avvio dell'applicazione
* Assembly .NET, inclusi in `assets/assemblies`
* `AndroidManifest.xml` modifiche per l'attività in c# e così via.
* Android asset da librerie .NET e risorse
* [Android Callable Wrapper](https://developer.xamarin.com/guides/android/advanced_topics/java_integration_overview/android_callable_wrappers/) per qualsiasi `Java.Lang.Object` sottoclasse

Se si sta cercando una procedura dettagliata di aggiuntiva, consultare questo video incorporamento Petzolds [Pittura con le dita demo](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint/) in un Android Studio progetto qui:

[![Embeddinator-4000 per Android](https://img.youtube.com/vi/ZVcrXUpCNpI/0.jpg)](https://www.youtube.com/watch?v=ZVcrXUpCNpI)

## <a name="using-java-18"></a>Utilizzo di Java 1.8

A partire dalla scrittura, la soluzione migliore consiste nell'utilizzare Android Studio 3.0 ([scaricare qui](https://developer.android.com/studio/index.html)).

Per abilitare Java 1.8 del modulo di app `build.gradle` file:
```groovy
android {
    // ...
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
```
È anche possibile esaminare il progetto di test di Android Studio [qui](https://github.com/mono/Embeddinator-4000/blob/master/tests/android/app/build.gradle) per altri dettagli.

Se si sono desiderano utilizzare stabile 2.3.x Android Studio, è necessario abilitare la toolchain presa deprecata:
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

Se ora si crea una sottoclasse `Java.Lang.Object`, xamarin verrà generato lo stub di Java (Android Callable Wrapper) anziché Embeddinator.

Pertanto, è necessario seguire le stesse regole per l'esportazione in c# e Java come xamarin.

Ad esempio in c#:
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
* `[Export]` è necessario per rendere un metodo visibile per Java

È possibile utilizzare `ViewSubclass` in Java come illustrato di seguito:
```java
import mono.embeddinator.android.ViewSubclass;
//...
ViewSubclass v = new ViewSubclass(this);
v.apply("Hello");
```

Altre informazioni sui [integrazione Java con xamarin](~/android/platform/java-integration/index.md).

## <a name="multiple-assemblies"></a>Più assembly

Incorporamento di un singolo assembly è semplice. Tuttavia, è molto più probabile che sarà necessario più di uno c# assembly. Molte volte si avrà le dipendenze per i pacchetti NuGet, ad esempio il supporto Android librerie o Google Play Services che complicare ulteriormente le cose.

In questo modo un dilemma, poiché Embeddinator deve includere molti tipi di file nel AAR finale, ad esempio:
* Asset Android
* Risorse Android
* Librerie native di Android
* Origine java Android

È molto probabile che non si desidera includere questi file dalla libreria di supporto Android o Google Play Services nel AAR, ma invece utilizzata la versione ufficiale da Google Android Studio.

Di seguito è l'approccio consigliato:
* Passare Embeddinator qualsiasi assembly cui si è proprietari (hanno origine) e si desidera chiamare da Java
* Passare Embeddinator qualsiasi assembly che è necessario Android, librerie native o risorse da
* Aggiungere in Android Studio dipendenze di linguaggio come la Android supportano libreria o Google Play Services

Pertanto, il comando potrebbe essere:
```
mono Embeddinator-4000.exe --gen=Java --platform=Android -c -o output YourMainAssembly.dll YourDependencyA.dll YourDependencyB.dll
```
È necessario escludere nulla da NuGet, a meno che non si scopre contiene risorse Android, risorse e così via che è necessario nel progetto Android Studio. È inoltre possibile omettere le dipendenze che non è necessario chiamare da Java e il linker _deve_ includere le parti della libreria che sono necessari.

Per aggiungere eventuali dipendenze Java necessarie in Android Studio il `build.gradle` file potrebbe essere simile:
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
* [Ricerca di Android preliminare](~/tools/dotnet-embedding/android/index.md)
* [Limitazioni di Embeddinator](~/tools/dotnet-embedding/limitations.md)
* [Che contribuiscono al progetto open source](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [Codici di errore e descrizioni](~/tools/dotnet-embedding/errors.md)


## <a name="related-links"></a>Collegamenti correlati

- [Esempio di meteo (Android)](https://github.com/jamesmontemagno/embeddinator-weather)
