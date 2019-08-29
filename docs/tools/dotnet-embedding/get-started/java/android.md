---
title: Introduzione ad Android
description: Questo documento descrive come iniziare a usare l'incorporamento di .NET con Android. Viene illustrata l'installazione dell'incorporamento di .NET, la creazione di un progetto di libreria Android, l'uso di output generato in un progetto Android Studio e altre considerazioni.
ms.prod: xamarin
ms.assetid: 870F0C18-A794-4C5D-881B-64CC78759E30
author: lobrien
ms.author: laobri
ms.date: 03/28/2018
ms.openlocfilehash: d1d05c75b8026112e8b81c91144361b65ad3a8e0
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120029"
---
# <a name="getting-started-with-android"></a>Introduzione ad Android

Oltre ai requisiti della Guida introduttiva a [Java](~/tools/dotnet-embedding/get-started/java/index.md) , è necessario anche:

- [Novell. Android 7,5](https://visualstudio.microsoft.com/xamarin/) o versione successiva
- [Android Studio 3. x](https://developer.android.com/studio/index.html) con Java 1,8

Come panoramica, viene:

- Creare un C# progetto di libreria Android
- Installare l'incorporamento .NET tramite NuGet
- Eseguire l'incorporamento .NET nell'assembly della libreria Android
- Usare il file AAR generato in un progetto Java in Android Studio

## <a name="create-an-android-library-project"></a>Creare un progetto di libreria Android

Aprire Visual Studio per Windows o Mac, creare un nuovo progetto di libreria di classi Android, denominarlo **Hello-from-CSharp**e salvarlo in **~/Projects/Hello-from-CSharp** o **%USERPROFILE%\Projects\hello-from-CSharp**.

Aggiungere una nuova attività Android denominata **HelloActivity.cs**, seguita da un layout Android in **Resource/layout/Hello. aXML**.

Aggiungere un nuovo `TextView` elemento al layout e modificare il testo in modo che sia piacevole.

L'origine del layout avrà un aspetto simile al seguente:

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

Nell'attività assicurarsi di chiamare `SetContentView` con il nuovo layout:

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
> Non dimenticare l' `[Register]` attributo. Per informazioni dettagliate, vedere [limitazioni](#current-limitations-on-android).

Compilare il progetto. L'assembly risultante verrà salvato in `bin/Debug/hello-from-csharp.dll`.

## <a name="installing-net-embedding-from-nuget"></a>Installazione dell'incorporamento di .NET da NuGet

Seguire queste [istruzioni](~/tools/dotnet-embedding/get-started/install/install.md) per installare e configurare l'incorporamento .NET per il progetto.

La chiamata del comando da configurare avrà un aspetto simile al seguente:

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

1. Aprire Android Studio e creare un nuovo progetto con un' **attività vuota**.
2. Fare clic con il pulsante destro del mouse sul modulo dell' **app** e scegliere **nuovo modulo >** .
3. Selezionare **Importa. JAR/. Pacchetto AAR**.
4. Usare il Visualizzatore directory per individuare **~/Projects/Hello-from-CSharp/output/hello_from_csharp.AAR** e fare clic su **fine**.

![Importare AAR in Android Studio](android-images/androidstudioimport.png)

Il file AAR verrà copiato in un nuovo modulo denominato **hello_from_csharp**.

![Progetto Android Studio](android-images/androidstudioproject.png)

Per usare il nuovo modulo dall' **app**, fare clic con il pulsante destro del mouse e scegliere **Apri Impostazioni modulo**. Nella scheda **dipendenze** aggiungere una nuova **dipendenza del modulo** e scegliere **: hello_from_csharp**.

![Dipendenze Android Studio](android-images/androidstudiodependencies.png)

Nell'attività aggiungere un nuovo `onResume` metodo e usare il codice seguente per avviare l' C# attività:

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

### <a name="assembly-compression-important"></a>Compressione degli assembly (*importante*)

È necessaria un'ulteriore modifica per l'incorporamento di .NET nel progetto Android Studio.

Aprire il file **Build. Gradle** dell'app e aggiungere la modifica seguente:

```groovy
android {
    // ...
    aaptOptions {
        noCompress 'dll'
    }
}
```

Novell. Android carica attualmente assembly .NET direttamente dall'APK, ma richiede che gli assembly non vengano compressi.

Se questa configurazione non è presente, l'app si arresterà in modo anomalo all'avvio e stampa qualcosa di simile alla console:

```shell
com.xamarin.hellocsharp A/monodroid: No assemblies found in '(null)' or '<unavailable>'. Assuming this is part of Fast Deployment. Exiting...
```

## <a name="run-the-app"></a>Esecuzione dell'app

All'avvio dell'app:

![Hello C# da esempio in esecuzione nell'emulatore](android-images/hello-from-csharp-android.png)

Si noti che cosa è successo qui:

- È presente una C# classe, `HelloActivity`, che sottoclassi Java
- Sono presenti file di risorse Android
- Sono stati usati da Java in Android Studio

Per il corretto funzionamento di questo esempio, nell'APK finale sono configurati tutti gli elementi seguenti:

- Novell. Android è configurato all'avvio dell'applicazione
- Assembly .NET inclusi in **Asset/assembly**
- Modifiche a **file AndroidManifest. XML** per C# le attività e così via.
- Risorse e asset Android dalle librerie .NET
- [Android Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md) per qualsiasi `Java.Lang.Object` sottoclasse

Se si sta cercando una procedura dettagliata aggiuntiva, vedere il video seguente, che illustra l'incorporamento della [demo FingerPaint](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) di Charles Petzold in un progetto Android Studio:

[![Embeddinator-4000 per Android](https://img.youtube.com/vi/ZVcrXUpCNpI/0.jpg)](https://www.youtube.com/watch?v=ZVcrXUpCNpI)

## <a name="using-java-18"></a>Uso di Java 1,8

Al momento della stesura di questo articolo, l'opzione migliore consiste nell'usare Android Studio 3,0 ([scaricare qui](https://developer.android.com/studio/index.html)).

Per abilitare Java 1,8 nel file **Build. Gradle** del modulo dell'app:

```groovy
android {
    // ...
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
```

Per ulteriori informazioni, è anche possibile esaminare un [progetto di test Android Studio](https://github.com/mono/Embeddinator-4000/blob/master/tests/android/app/build.gradle) . 

Se si vuole usare Android Studio 2.3. x stabile, è necessario abilitare la cassetta di stato di Jack deprecata:

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

A questo punto, se si `Java.Lang.Object`esegue la sottoclasse, Novell. Android genererà lo stub Java (Android Callable Wrapper) invece dell'incorporamento di .NET. Per questo motivo, è necessario seguire le stesse regole per l' C# esportazione in Java come Novell. Android. Ad esempio:

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

- `[Register]`è necessario per eseguire il mapping a un nome di pacchetto Java desiderato
- `[Export]`è necessario per rendere un metodo visibile a Java

È possibile usare `ViewSubclass` in Java come segue:

```java
import mono.embeddinator.android.ViewSubclass;
//...
ViewSubclass v = new ViewSubclass(this);
v.apply("Hello");
```

Scopri di più sull' [integrazione Java con Novell. Android](~/android/platform/java-integration/index.md).

## <a name="multiple-assemblies"></a>Più assembly

L'incorporamento di un singolo assembly è semplice; Tuttavia, è molto più probabile che sia presente più di un C# assembly. Molte volte si avranno dipendenze dai pacchetti NuGet, ad esempio le librerie di supporto Android o Google Play Services che complicano ulteriormente le operazioni.

Questo causa un dilemma, poiché l'incorporamento di .NET deve includere molti tipi di file nell'AAR finale, ad esempio:

- Asset Android
- Risorse Android
- Librerie native Android
- Origine Java Android

È probabile che non si voglia includere questi file dalla libreria del supporto Android o Google Play Services nell'AAR, ma usare invece la versione ufficiale di Google in Android Studio.

Ecco l'approccio consigliato:

- Passare .NET incorporando qualsiasi assembly di cui si è proprietari (avere origine) e si vuole chiamare da Java
- Passare .NET incorporando qualsiasi assembly necessario per asset Android, librerie native o risorse da
- Aggiungere dipendenze Java come la libreria di supporto Android o la Google Play Services in Android Studio

Quindi, il comando potrebbe essere:

```shell
mono Embeddinator-4000.exe --gen=Java --platform=Android -c -o output YourMainAssembly.dll YourDependencyA.dll YourDependencyB.dll
```

È necessario escludere qualsiasi elemento da NuGet, a meno che non sia presente un asset Android, risorse e così via, che sarà necessario nel progetto Android Studio. È anche possibile omettere le dipendenze che non è necessario chiamare da Java e il linker _deve_ includere le parti della libreria necessarie.

Per aggiungere le dipendenze Java necessarie in Android Studio, il file **Build. Gradle** potrebbe essere simile al seguente:

```groovy
dependencies {
    // ...
    compile 'com.android.support:appcompat-v7:25.3.1'
    compile 'com.google.android.gms:play-services-games:11.0.4'
    // ...
}
```

## <a name="further-reading"></a>Ulteriori informazioni

- [Callback in Android](~/tools/dotnet-embedding/android/callbacks.md)
- [Ricerca preliminare per Android](~/tools/dotnet-embedding/android/index.md)
- [Limitazioni dell'incorporamento di .NET](~/tools/dotnet-embedding/limitations.md)
- [Aggiunta come contributo al progetto open source](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
- [Codici di errore e descrizioni](~/tools/dotnet-embedding/errors.md)

## <a name="related-links"></a>Collegamenti correlati

- [Esempio meteorologico (Android)](https://github.com/jamesmontemagno/embeddinator-weather)
