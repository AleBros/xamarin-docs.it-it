---
title: ProGuard
description: ProGuard di Xamarin.Android è uno strumento di classe Java che consente di compattare, ottimizzare ed eseguire una verifica preliminare dei file. ProGuard rileva e rimuove eventuale codice non usato e analizza e ottimizza il bytecode. Questa guida illustra il funzionamento di ProGuard e spiega come abilitare questo strumento nel progetto e come configurarlo, presentando anche alcuni esempi di configurazione.
ms.prod: xamarin
ms.assetid: 29C0E850-3A49-4618-9078-D59BE0284D5A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 28f48766dc62ac31fc357410eac27c33c9cae6d8
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "76940685"
---
# <a name="proguard"></a>ProGuard

_Xamarin.Android ProGuard è un sistema di compattazione di file di classe Java, ottimizzatore e pre-verifier. Rileva e rimuove il codice inutilizzato, analizza e ottimizza il bytecode. Questa guida spiega come funziona ProGuard, come abilitarlo nel progetto e come configurarlo. Fornisce anche diversi esempi di configurazioni ProGuard._

## <a name="overview"></a>Panoramica

ProGuard rileva e rimuove classi, campi, metodi e attributi inutilizzati dal pacchetto dell'applicazione. Può eseguire queste operazioni anche per le librerie di riferimento, consentendo di evitare il limite di 64 KB. Lo strumento ProGuard di Android SDK ottimizza anche il bytecode e rimuove le istruzioni di codice non utilizzato. ProGuard legge **file JAR di input** e quindi compatta, ottimizza e verifica in via preliminare tali file, scrivendo i risultati in uno o più **file JAR di output**. 

Per elaborare i file APK di input, ProGuard esegue i passaggi seguenti: 

1. **Compattazione** &ndash; ProGuard determina in modo ricorsivo quali classi e quali membri di classi vengono usati. Tutte le altre classi e tutti i membri di queste vengono eliminati. 

2. **Ottimizzazione** &ndash; ProGuard ottimizza ulteriormente il codice. 
    Tra l'altro, ProGuard rende privati, statici o finali le classi e i metodi che non rappresentano punti di ingresso, rimuove i parametri inutilizzati e imposta alcuni metodi come inline. 

3. **Offuscamento** &ndash; Nello sviluppo Android nativo ProGuard rinomina classi e membri di classi che non rappresentano punti di ingresso. Il mantenimento di punti di ingresso garantisce che le classi e i membri rinominati siano ancora accessibili con i nomi originari. Tuttavia, questo passaggio non è supportato da Xamarin.Android poiché l'app viene compilata nel linguaggio intermedio (IL).

4. **Verifica preliminare** &ndash; ProGuard esegue controlli sui bytecode Java prima della fase di runtime e annota i file di classe a vantaggio della macchina virtuale Java. Questo è l'unico passaggio per il quale non ha bisogno conoscere i punti di ingresso. 

Ognuno di questi passaggi è *facoltativo*. Come verrà spiegato nella prossima sezione, in Xamarin.Android ProGuard usa solo un subset di questi passaggi. 

## <a name="proguard-in-xamarinandroid"></a>ProGuard in Xamarin.Android

La configurazione di ProGuard per Xamarin.Android non esegue l'offuscamento dei file APK. Non è in effetti possibile abilitare l'offuscamento con ProGuard, neanche usando file di configurazione personalizzati. In Xamarin.Android, quindi, ProGuard esegue solo i passaggi di **compattazione** e **ottimizzazione**: 

[![Passaggi di riduzione e ottimizzazione](proguard-images/01-xa-chain-sml.png)](proguard-images/01-xa-chain.png#lightbox)

Un elemento importante da conoscere in anticipo prima di usare ProGuard è il funzionamento di questo strumento all'interno del processo di compilazione di `Xamarin.Android`. Questo processo si svolge in due passaggi distinti: 

1. Linker di Xamarin.Android

2. ProGuard

Di seguito viene descritto ognuno di questi passaggi.

### <a name="linker-step"></a>Linker

Il linker di Xamarin.Android impiega l'analisi statica dell'applicazione per determinare quanto segue: 

- Gli assembly effettivamente usati.

- I tipi effettivamente usati.

- I membri effettivamente usati. 

Il linker viene sempre eseguito prima del passaggio relativo a ProGuard. Per questo motivo, il linker può rimuovere un assembly, un tipo o un membro per il quale sia prevista l'esecuzione di ProGuard. Per altre informazioni sull'uso del linker in Xamarin.Android, vedere [Linking on Android](~/android/deploy-test/linker.md) (Uso del linker in Android).

### <a name="proguard-step"></a>ProGuard

Dopo il completamento del passaggio relativo al linker, viene eseguito ProGuard per rimuovere bytecode Java inutilizzati. Questo è il passaggio che ottimizza il file APK. 

## <a name="using-proguard"></a>Uso di ProGuard

Per usare ProGuard nel progetto dell'app, è prima necessario abilitare ProGuard. Per il processo di compilazione di Xamarin.Android si potrà quindi usare il file di configurazione di ProGuard predefinito o, in alternativa, crearne uno personalizzato. 

### <a name="enabling-proguard"></a>Abilitazione di ProGuard

Per abilitare ProGuard nel progetto dell'app, usare la procedura seguente:

1. Assicurarsi che il progetto sia impostato sulla configurazione **di rilascio** (questo è importante perché il linker deve essere eseguito per consentire l'esecuzione di ProGuard): 

    [![Selezionare Configurazione versione](proguard-images/02-set-release-sml.png)](proguard-images/02-set-release.png#lightbox)
   
2. Scegliere **ProGuard** dall'elenco a discesa  **Code shrinker** nella finestra **Proprietà > Opzioni Android**: 

    [![Shrinker del codice Proguard selezionato](proguard-images/03-enable-proguard-shrinker-sml.png)](proguard-images/03-enable-proguard-shrinker.png#lightbox)

Per la maggior parte delle app Xamarin.Android, il file di configurazione ProGuard predefinito fornito da Xamarin.Android è sufficiente per rimuovere tutto (e solo) il codice inutilizzato. Per visualizzare la configurazione di ProGuard predefinita, aprire il file **obj\\Release\\proguard\\proguard_xamarin.cfg**.

L'esempio seguente illustra un tipico file di configurazione **proguard_xamarin.cfg**:

```cfg
# This is Xamarin-specific (and enhanced) configuration.

-dontobfuscate

-keep class mono.MonoRuntimeProvider { *; <init>(...); }
-keep class mono.MonoPackageManager { *; <init>(...); }
-keep class mono.MonoPackageManager_Resources { *; <init>(...); }
-keep class mono.android.** { *; <init>(...); }
-keep class mono.java.** { *; <init>(...); }
-keep class mono.javax.** { *; <init>(...); }
-keep class opentk.platform.android.AndroidGameView { *; <init>(...); }
-keep class opentk.GameViewBase { *; <init>(...); }
-keep class opentk_1_0.platform.android.AndroidGameView { *; <init>(...); }
-keep class opentk_1_0.GameViewBase { *; <init>(...); }

-keep class android.runtime.** { <init>(***); }
-keep class assembly_mono_android.android.runtime.** { <init>(***); }
# hash for android.runtime and assembly_mono_android.android.runtime.
-keep class md52ce486a14f4bcd95899665e9d932190b.** { *; <init>(...); }
-keepclassmembers class md52ce486a14f4bcd95899665e9d932190b.** { *; <init>(...); }

# Android's template misses fluent setters...
-keepclassmembers class * extends android.view.View {
   *** set*(***);
}

# also misses those inflated custom layout stuff from xml...
-keepclassmembers class * extends android.view.View {
   <init>(android.content.Context,android.util.AttributeSet);
   <init>(android.content.Context,android.util.AttributeSet,int);
}
```

La prossima sezione descrive come creare un file di configurazione di ProGuard personalizzato. 

### <a name="customizing-proguard"></a>Personalizzazione di ProGuard

Facoltativamente, è possibile aggiungere un file di configurazione di ProGuard personalizzato per esercitare un controllo maggiore sugli strumenti ProGuard, ad esempio se si vogliono indicare in modo esplicito a ProGuard le classi da mantenere. A tale scopo, creare un nuovo file con estensione **cfg** e applicare l'azione di compilazione `ProGuardConfiguration` nel riquadro **Proprietà** di **Esplora soluzioni**: 

[![Azione di compilazione ProguardConfiguration selezionata](proguard-images/04-build-action-sml.png)](proguard-images/04-build-action.png#lightbox)

Tenere presente che questo file di configurazione non sostituisce il file **proguard_xamarin.cfg** di Xamarin.Android, dato che ProGuard li usa entrambi. 

In alcuni casi ProGuard non è in grado di analizzare correttamente l'applicazione e potrebbe rimuovere codice in realtà necessario. Se questo si verifica, è possibile aggiungere una riga `-keep` al file di configurazione ProGuard personalizzato: 

```
-keep public class MyClass
```

In questo esempio, `MyClass` rappresenta il nome effettivo della classe che deve essere ignorata da ProGuard.

È anche possibile registrare nomi personalizzati con annotazioni `[Register]` e usare questi nomi per personalizzare le regole di ProGuard. È possibile registrare nomi per Adapters, Views, BroadcastReceivers, Services, ContentProviders, Activities e Fragments. Per altre informazioni sull'uso dell'attributo personalizzato `[Register]`, vedere [Working with JNI](~/android/platform/java-integration/working-with-jni.md) (Uso di JNI).

### <a name="proguard-options"></a>Opzioni di ProGuard

ProGuard offre una serie di opzioni che è possibile configurare per controllare il suo funzionamento in modo più granulare. Il [manuale di ProGuard](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/index.html#manual/introduction.html) costituisce la documentazione di riferimento completa per l'uso di ProGuard. 

Xamarin supporta le opzioni di ProGuard seguenti: 

- [Opzioni di input/output](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#iooptions)

- [Opzioni di mantenimento](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#keepoptions)

- [Opzioni di compattazione](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#shrinkingoptions)

- [Opzioni generali](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#generaloptions)

- [Percorsi delle classi](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#classpath)

- [Nomi dei file](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#filename)

- [Filtri di file](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#filefilters)

- [Filtri](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#filters)

- [Panoramica delle opzioni `Keep`](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#keepoverview)

- [Modificatori delle opzioni di mantenimento](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#keepoptionmodifiers)

- [Specifiche delle classi](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#classspecification)

Le opzioni seguenti vengono *ignorate* da Xamarin.Android:

- [Opzioni di ottimizzazione](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#optimizationoptions)

- [Opzioni di offuscamento](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#obfuscationoptions) 

- [Opzioni di verifica preliminare](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#preverificationoptions)

## <a name="proguard-and-android-nougat"></a>ProGuard e Android Nougat

Se si vuole usare ProGuard con Android 7.0 o versione successiva, è necessario scaricare una versione più recente di ProGuard, perché Android SDK non ne fornisce una nuova versione compatibile con JDK 1.8.

È possibile usare questo [pacchetto NuGet](https://www.nuget.org/packages/name.atsushieno.proguard.facebook/5.3.0) per installare una versione più recente di `proguard.jar`. Per altre informazioni sull'aggiornamento dell'Android SDK predefinito`proguard.jar`, vedere questa discussione in [Stack Overflow](https://stackoverflow.com/questions/39514518/xamarin-android-proguard-unsupported-class-version-number-52-0/39514706#39514706).

Tutte le versioni di ProGuard sono disponibili in questa [pagina SourceForge](https://sourceforge.net/projects/proguard/files/). 

## <a name="example-proguard-configurations"></a>Configurazioni di ProGuard di esempio

Di seguito sono riportati due file di configurazione di ProGuard di esempio. Si noti che, in questi casi, i file JAR di **input**, **di output** e di **libreria** vengono forniti dal processo di compilazione di Xamarin.Android. È quindi possibile concentrarsi su altre opzioni, ad esempio `-keep`. 

### <a name="a-simple-android-activity"></a>Una semplice attività Android

Nell'esempio seguente viene illustrata la configurazione per una semplice attività Android:

```
-injars  bin/classes
-outjars bin/classes-processed.jar
-libraryjars /usr/local/java/android-sdk/platforms/android-9/android.jar

-dontpreverify
-repackageclasses ''
-allowaccessmodification
-optimizations !code/simplification/arithmetic

-keep public class mypackage.MyActivity
```

### <a name="a-complete-android-application"></a>Un'applicazione Android completa

Nell'esempio seguente viene illustrata la configurazione per un'app Android completa:

```
-injars  bin/classes
-injars  libs
-outjars bin/classes-processed.jar
-libraryjars /usr/local/java/android-sdk/platforms/android-9/android.jar

-dontpreverify
-repackageclasses ''
-allowaccessmodification
-optimizations !code/simplification/arithmetic
-keepattributes *Annotation*

-keep public class * extends android.app.Activity
-keep public class * extends android.app.Application
-keep public class * extends android.app.Service
-keep public class * extends android.content.BroadcastReceiver
-keep public class * extends android.content.ContentProvider

-keep public class * extends android.view.View {
public <init>(android.content.Context);
public <init>(android.content.Context, android.util.AttributeSet);
public <init>(android.content.Context, android.util.AttributeSet, int);
public void set*(...);
}

-keepclasseswithmembers class * {
public <init>(android.content.Context, android.util.AttributeSet);
}

-keepclasseswithmembers class * {
public <init>(android.content.Context, android.util.AttributeSet, int);
}

-keepclassmembers class * implements android.os.Parcelable {
static android.os.Parcelable$Creator CREATOR;
}

-keepclassmembers class **.R$* {
public static <fields>;
}
```

## <a name="proguard-and-the-xamarinandroid-build-process"></a>ProGuard e il processo di compilazione di Xamarin.Android

Le sezioni seguenti spiegano il funzionamento di ProGuard durante una compilazione **Rilascio** di Xamarin.Android.

### <a name="what-command-is-proguard-running"></a>Qual è il comando eseguito da ProGuard?

ProGuard è semplicemente un file `.jar` fornito con Android SDK. Viene quindi richiamato in un comando: 

```shell
java -jar proguard.jar options ...
```

### <a name="the-proguard-task"></a>Attività ProGuard

L'attività ProGuard si trova all'interno dell'assembly **Xamarin.Android.Build.Tasks.dll**. Fa parte della destinazione `_CompileToDalvikWithDx`, che a sua volta fa parte della destinazione `_CompileDex`. 

L'elenco seguente offre un esempio dei parametri predefiniti che vengono generati dopo la creazione di un nuovo progetto tramite **File > Nuovo progetto**: 

```
ProGuardJarPath = C:\Android\android-sdk\tools\proguard\lib\proguard.jar
AndroidSdkDirectory = C:\Android\android-sdk\
JavaToolPath = C:\Program Files (x86)\Java\jdk1.8.0_92\\bin
ProGuardToolPath = C:\Android\android-sdk\tools\proguard\
JavaPlatformJarPath = C:\Android\android-sdk\platforms\android-25\android.jar
ClassesOutputDirectory = obj\Release\android\bin\classes
AcwMapFile = obj\Release\acw-map.txt
ProGuardCommonXamarinConfiguration = obj\Release\proguard\proguard_xamarin.cfg
ProGuardGeneratedReferenceConfiguration = obj\Release\proguard\proguard_project_references.cfg
ProGuardGeneratedApplicationConfiguration = obj\Release\proguard\proguard_project_primary.cfg
ProGuardConfigurationFiles

    {sdk.dir}tools\proguard\proguard-android.txt;
    {intermediate.common.xamarin};
    {intermediate.references};
    {intermediate.application};
    ;

JavaLibrariesToEmbed = C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\MonoAndroid\v7.0\mono.android.jar
ProGuardJarInput = obj\Release\proguard\__proguard_input__.jar
ProGuardJarOutput = obj\Release\proguard\__proguard_output__.jar
DumpOutput = obj\Release\proguard\dump.txt
PrintSeedsOutput = obj\Release\proguard\seeds.txt
PrintUsageOutput = obj\Release\proguard\usage.txt
PrintMappingOutput = obj\Release\proguard\mapping.txt
```

Il prossimo esempio illustra un comando ProGuard tipico eseguito nell'ambiente IDE:

```cmd
C:\Program Files (x86)\Java\jdk1.8.0_92\\bin\java.exe -jar C:\Android\android-sdk\tools\proguard\lib\proguard.jar -include obj\Release\proguard\proguard_xamarin.cfg -include obj\Release\proguard\proguard_project_references.cfg -include obj\Release\proguard\proguard_project_primary.cfg "-injars 'obj\Release\proguard\__proguard_input__.jar';'C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\MonoAndroid\v7.0\mono.android.jar'" "-libraryjars 'C:\Android\android-sdk\platforms\android-25\android.jar'" -outjars "obj\Release\proguard\__proguard_output__.jar" -optimizations !code/allocation/variable
```

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="file-issues"></a>Problemi relativi ai file

Il messaggio di errore seguente può essere visualizzato quando ProGuard legge il proprio file di configurazione: 

```
Unknown option '-keep' in line 1 of file 'proguard.cfg'
```

Questo problema in genere si verifica in Windows a causa della codifica non corretta del file `.cfg`. ProGuard non è in grado di gestire eventuali caratteri _byte order mark_ (BOM) all'interno di file di testo. Se è presente un carattere BOM, ProGuard termina con l'errore precedente. 

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Per evitare questo problema, modificare il file di configurazione personalizzato con un editor di testo che consenta il salvataggio del file senza un carattere BOM. Per risolvere questo problema, assicurarsi che per l'editor di testo la codifica sia impostata su `UTF-8`. Con l'editor di testo [Notepad++](https://notepad-plus-plus.org/), ad esempio, è possibile salvare i file senza carattere BOM selezionando **Encoding (Codifica) &gt; Encode in UTF-8 Without BOM** (Codifica UTF-8 senza BOM) quando si salva il file. 

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Per evitare questo problema, salvare il file di configurazione personalizzato da un editor di testo che consente di omettere il carattere BOM. 

-----

### <a name="other-issues"></a>Altri problemi

La pagina [Troubleshooting](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/index.html#manual/troubleshooting.html) (Risoluzione dei problemi) di ProGuard illustra i problemi più comuni che possono verificarsi durante l'uso di ProGuard e le rispettive soluzioni.

## <a name="summary"></a>Riepilogo

Questa guida ha illustrato il funzionamento di ProGuard in Xamarin.Android e ha spiegato come abilitare questo strumento nel progetto dell'app e come configurarlo, presentando esempi di configurazioni di ProGuard e fornendo le soluzioni dei problemi più comuni. Per altre informazioni sullo strumento ProGuard e su Android, vedere [Compattare il codice e le risorse](https://developer.android.com/tools/help/proguard.html). 

## <a name="related-links"></a>Collegamenti correlati

- [Preparazione di un'applicazione per il rilascio](~/android/deploy-test/release-prep/index.md)
