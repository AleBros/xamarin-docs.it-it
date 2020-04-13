---
title: Risoluzione dei problemi delle associazioni
description: In questo articolo vengono riepilogati gli errori comuni serverali che possono verificarsi durante la generazione di associazioni, insieme alle possibili cause e ai modi suggeriti per risolverli.
ms.prod: xamarin
ms.assetid: BB81FCCF-F7BF-4C78-884E-F02C49AA819A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 0c273797d7512f062260e49e0f71fdd1132f037b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292218"
---
# <a name="troubleshooting-bindings"></a>Risoluzione dei problemi delle associazioni

_In questo articolo vengono riepilogati gli errori comuni serverali che possono verificarsi durante la generazione di associazioni, insieme alle possibili cause e ai modi suggeriti per risolverli._

## <a name="overview"></a>Panoramica

Associazione di un file di libreria Android (un **file .aar** o **.jar)** è raramente un affare semplice; richiede in genere uno sforzo aggiuntivo per attenuare i problemi che derivano dalle differenze tra Java e .NET.
Questi problemi impediranno a Xamarin.Android di associare la libreria Android e presentarsi come messaggi di errore nel log di compilazione. Questa guida fornirà alcuni suggerimenti per la risoluzione dei problemi, elencare alcuni dei problemi/scenari più comuni e fornire le possibili soluzioni per associare correttamente la libreria Android.This guide will provide some tips for troubleshooting the issues, list some of the more common problems/scenarios, and provide possible solutions to successfully binding the Android library.

Quando si associa una libreria Android esistente, è necessario tenere a mente i seguenti punti:

- **Le dipendenze esterne per la libreria** &ndash; Qualsiasi dipendenza Java richiesta dalla libreria Android devono essere incluse nel progetto Xamarin.Android come **ReferenceJar** o come **EmbeddedReferenceJar**.

- **Il livello di ANDROID API che la libreria Android è di** &ndash; destinazione Non è possibile "declassare" il livello di API Android; assicurarsi che il progetto di binding Xamarin.Android è destinato allo stesso livello DI API (o superiore) come la libreria Android.

- **La versione del JDK Android che è stato utilizzato per il pacchetto della libreria** &ndash; Android errori di binding può verificarsi se la libreria Android è stata compilata con una versione diversa di JDK rispetto a quella in uso da Xamarin.Android. Se possibile, ricompilare la libreria Android utilizzando la stessa versione di JDK utilizzata dall'installazione di Xamarin.Android.

Il primo passaggio per la risoluzione dei problemi relativi all'associazione di una libreria Xamarin.Android consiste nell'abilitare [l'output MSBuild diagnostico.](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)
Dopo aver abilitato l'output di diagnostica, ricompilare il progetto di associazione Xamarin.Android ed esaminare il log di compilazione per individuare indizi sulla causa del problema.

Può anche rivelarsi utile per decompilare la libreria Android ed esaminare i tipi e metodi che Xamarin.Android sta tentando di associare. Questo è trattato in modo più dettagliato più avanti in questa guida.

## <a name="decompiling-an-android-library"></a>Decompilazione di una libreria AndroidDecompiling an Android Library

L'ispezione delle classi e dei metodi delle classi Java può fornire informazioni utili che consentono di associare una libreria.
[JD-GUI](http://jd.benow.ca/) è un'utilità grafica che può visualizzare il codice sorgente Java dai file **CLASS** contenuti in un JAR. Può essere eseguito come applicazione autonoma o come plug-in per IntelliJ o Eclipse.

Per decompilare una libreria Android, aprire il file **. JAR** con il decompilatore Java. Se la libreria è un **file . AAR,** è necessario estrarre il file **classes.jar** dal file di archivio. Di seguito è riportato uno screenshot di esempio dell'utilizzo di JD-GUI per analizzare il file JAR di [Picasso:](https://square.github.io/picasso/)

![Utilizzo del decompilatore Java per analizzare picasso-2.5.2.jar](troubleshooting-bindings-images/troubleshoot-bindings-01.png)

Dopo aver decompilato la libreria Android, esaminare il codice sorgente. In generale, cercare :

- Le classi che hanno caratteristiche di caratteristiche di offuscamento Caratteristiche di classi offuscate includono:Classes that **have characteristics of offuscauscation** &ndash; Characteristics of bfuscated classes include:

  - Il nome della **$** classe include un, ad esempio **a.class**
  - Il nome della classe è interamente compromesso da caratteri minuscoli, ad esempio **a.class**      

- &ndash; **ReferenceJar** ** `import` istruzioni per le librerie** senza riferimenti Identificare la libreria senza riferimenti e aggiungere tali dipendenze al progetto di associazione Xamarin.Android con **un'azione** di compilazione di ReferenceJar o **EmbedddedReferenceJar**.

> [!NOTE]
> La decompilazione di una libreria Java può essere vietata o soggetta a restrizioni legali basate sulle leggi locali o sulla licenza in base alla quale è stata pubblicata la libreria Java. Se necessario, arruolare i servizi di un professionista legale prima di tentare di decompilare una libreria Java e controllare il codice sorgente.

## <a name="inspect-apixml"></a>Ispezionare l'API. Xml

Come parte della compilazione di un progetto di associazione, Xamarin.Android genererà un nome di file XML **obj/Debug/api.xml**:

![Generato api.xml in obj/Debug](troubleshooting-bindings-images/troubleshoot-bindings-02.png)

Questo file fornisce un elenco di tutte le API Java che Xamarin.Android sta tentando di associare. Il contenuto di questo file consente di identificare eventuali tipi o metodi mancanti, associazione duplicata. Anche se l'ispezione di questo file è noiosa e richiede molto tempo, può fornire indizi su ciò che potrebbe causare problemi di legame. Ad esempio, **api.xml** potrebbe rivelare che una proprietà restituisce un tipo non appropriato o che esistono due tipi che condividono lo stesso nome gestito.

## <a name="known-issues"></a>Problemi noti

Questa sezione elencherà alcuni dei messaggi di errore comuni o sintomi che il mio si verificano quando si tenta di associare una libreria Android.

### <a name="problem-java-version-mismatch"></a>Problema: versione Java non corrispondente

A volte i tipi non vengono generati o potrebbero verificarsi arresti anomali imprevisti perché si utilizza una versione più recente o precedente di Java rispetto a ciò con cui è stata compilata la libreria. Ricompilare la libreria Android con la stessa versione di JDK usata dal progetto Xamarin.Android.

### <a name="problem-at-least-one-java-library-is-required"></a>Problema: è necessaria almeno una libreria Java

Viene visualizzato l'errore "è necessaria almeno una libreria Java", anche se un file . JAR è stato aggiunto.

#### <a name="possible-causes"></a>Possibili cause:

Assicurarsi che l'azione `EmbeddedJar`di compilazione sia impostata su . Poiché sono disponibili più azioni di compilazione per . JAR `InputJar`(ad esempio `EmbeddedJar` `ReferenceJar` , `EmbeddedReferenceJar`e ), il generatore di associazione non è in grado di indovinare automaticamente quale utilizzare per impostazione predefinita. Per ulteriori informazioni sulle azioni di compilazione, vedere [Azioni di compilazione](~/android/platform/binding-java-library/index.md).

### <a name="problem-binding-tools-cannot-load-the-jar-library"></a>Problema: gli strumenti di associazione non possono caricare il file . Libreria JAR

Il generatore della libreria di associazione non riesce a caricare l'oggetto . Libreria JAR.

#### <a name="possible-causes"></a>Possibili cause

Alcuni. Le librerie JAR che utilizzano l'offuscamento del codice (tramite strumenti come Proguard) non possono essere caricate dagli strumenti Java. Poiché il nostro strumento utilizza la reflection Java e la libreria di progettazione del codice byte ASM, tali strumenti dipendenti possono rifiutare le librerie offuscate mentre gli strumenti di runtime Android possono passare. La soluzione a questo scopo consiste nell'associare a mano queste librerie anziché utilizzare il generatore di associazione.

### <a name="problem-missing-c-types-in-generated-output"></a>Problema: i tipi C ' e ' mancanti nell'output generato.

Il **file DLL** di associazione viene compilato, ma manca alcuni tipi Java, oppure l'origine generata di C, non viene compilata a causa di un errore che indica che sono presenti tipi mancanti.

#### <a name="possible-causes"></a>Possibili cause:

Questo errore può verificarsi a causa di diversi motivi, come elencato di seguito:

- La libreria da associare può fare riferimento a una seconda libreria Java. Se l'API pubblica per la libreria associata utilizza i tipi della seconda libreria, è necessario fare riferimento a un'associazione gestita anche per la seconda libreria.

- È possibile che una libreria sia stata inserita a causa della reflection Java, simile al motivo dell'errore di caricamento della libreria precedente, causando il caricamento imprevisto dei metadati. Gli strumenti di Xamarin.Android non sono attualmente in grado di risolvere questa situazione. In tal caso, la libreria deve essere associata manualmente.

- Si è verificato un bug in .NET 4.0 runtime che non è riuscito a caricare gli assembly quando avrebbe dovuto. Questo problema è stato risolto nel runtime di .NET 4.5.

- Java consente di derivare una classe pubblica da una classe non pubblica, ma questa operazione non è supportata in .NET. Poiché il generatore di associazioni non genera associazioni per le classi non pubbliche, le classi derivate di queste non possono essere generate correttamente. Per risolvere questo problema, rimuovere la voce di metadati per le classi derivate utilizzando il nodo remove in **Metadata.xml**oppure correggere i metadati che rendono pubblica la classe non pubblica. Anche se quest'ultima soluzione creerà l'associazione in modo che l'origine di C , verrà compilata, la classe non pubblica non deve essere utilizzata.

  Ad esempio:

  ```xml
  <attr path="/api/package[@name='com.some.package']/class[@name='SomeClass']"
      name="visibility">public</attr>
  ```

- Gli strumenti che offuscano le librerie Java possono interferire con il generatore di associazioni Xamarin.Android e la sua capacità di generare classi wrapper C. Nel frammento di codice seguente viene illustrato come aggiornare Metadata.xml per debfuscate un nome di classe:The following snippet shows how to update **Metadata.xml** to unobfuscate a class name:

  ```xml
  <attr path="/api/package[@name='{package_name}']/class[@name='{name}']"
      name="obfuscated">false</attr>
  ```

### <a name="problem-generated-c-source-does-not-build-due-to-parameter-type-mismatch"></a>Problema: l'origine c'è generata non viene compilata a causa di una mancata corrispondenza del tipo di parametro

Non viene compilata l'origine generata in C. I tipi di parametro del metodo Overrideden non corrispondono.

#### <a name="possible-causes"></a>Possibili cause:

Xamarin.Android include una varietà di campi Java che sono mappati alle enumerazioni nelle associazioni di C . Questi possono causare incompatibilità di tipo nelle associazioni generate. Per risolvere questo problema, le firme del metodo create dal generatore di associazione devono essere modificate per utilizzare le enumerazioni. Per ulteriori informazioni, vedere [Correzione delle enumerazioni](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md).

### <a name="problem-noclassdeffounderror-in-packaging"></a>Problema: NoClassDefFoundError nel pacchettoProblem: NoClassDefFoundError in packaging

`java.lang.NoClassDefFoundError`viene gettato nella fase di confezionamento.

#### <a name="possible-causes"></a>Possibili cause:

La ragione più probabile di questo errore è che una libreria Java obbligatoria deve essere aggiunta al progetto dell'applicazione (**.csproj**). . I file JAR non vengono risolti automaticamente. Un'associazione di libreria Java non viene sempre generata su un assembly utente che non esiste nel dispositivo o nell'emulatore di destinazione (ad esempio Google Maps **maps.jar**). Questo non è il caso per il supporto del progetto libreria Android, come la libreria . JAR è incorporato nella dll della libreria. Ad esempio: [Bug 4288](https://bugzilla.xamarin.com/show_bug.cgi?id=4288)

### <a name="problem-duplicate-custom-eventargs-types"></a>Problema: tipi Di Args personalizzati duplicati

La compilazione non riesce a causa di tipi EventArgs personalizzati duplicati. Si verifica un errore di questo tipo:

```shell
error CS0102: The type `Com.Google.Ads.Mediation.DismissScreenEventArgs' already contains a definition for `p0'
```

#### <a name="possible-causes"></a>Possibili cause:

Ciò è dovuto al fatto che esiste un conflitto tra i tipi di evento che provengono da più di un tipo "listener" di interfaccia che condivide metodi con nomi identici. Ad esempio, se sono presenti due interfacce Java come `DismissScreenEventArgs` illustrato `MediationBannerListener` nell'esempio seguente, il generatore crea per entrambi e `MediationInterstitialListener`, generando l'errore.

```java
// Java:
public interface MediationBannerListener {
    void onDismissScreen(MediationBannerAdapter p0);
}
public interface MediationInterstitialListener {
    void onDismissScreen(MediationInterstitialAdapter p0);
}
```

Si tratta di progettazione in modo che i nomi lunghi sui tipi di argomento dell'evento vengono evitati. Per evitare questi conflitti, è necessaria la trasformazione dei metadati. Modificare il file **Transforms-Metadata.xml** e aggiungere un `argsType` attributo su una delle interfacce (o sul metodo di interfaccia):

```xml
<attr path="/api/package[@name='com.google.ads.mediation']/
        interface[@name='MediationBannerListener']/method[@name='onDismissScreen']"
        name="argsType">BannerDismissScreenEventArgs</attr>

<attr path="/api/package[@name='com.google.ads.mediation']/
        interface[@name='MediationInterstitialListener']/method[@name='onDismissScreen']"
        name="argsType">IntersitionalDismissScreenEventArgs</attr>

<attr path="/api/package[@name='android.content']/
        interface[@name='DialogInterface.OnClickListener']"
        name="argsType">DialogClickEventArgs</attr>
```

### <a name="problem-class-does-not-implement-interface-method"></a>Problema: la classe non implementa il metodo di interfacciaProblem: Class does not implement interface method

Viene generato un messaggio di errore che indica che una classe generata non implementa un metodo necessario per un'interfaccia implementata dalla classe generata. Tuttavia, esaminando il codice generato, è possibile vedere che il metodo è implementato.

Di seguito è riportato un esempio dell'errore:

```shell
obj\Debug\generated\src\Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.cs(8,23):
error CS0738: 'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter' does not
implement interface member 'Oauth.Signpost.Http.IHttpRequest.Unwrap()'.
'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.Unwrap()' cannot implement
'Oauth.Signpost.Http.IHttpRequest.Unwrap()' because it does not have the matching
return type of 'Java.Lang.Object'
```

#### <a name="possible-causes"></a>Possibili cause:

Si tratta di un problema che si verifica con l'associazione di metodi Java con tipi restituiti covarianti. In questo esempio, `Oauth.Signpost.Http.IHttpRequest.UnWrap()` il `Java.Lang.Object`metodo deve restituire . Tuttavia, `Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.UnWrap()` il metodo ha `HttpURLConnection`un tipo restituito di . Esistono due modi per risolvere questo problema:

- Aggiungere una dichiarazione `HttpURLConnectionRequestAdapter` di `IHttpRequest.Unwrap()`classe parziale per e implementare in modo esplicito :

  ```csharp
  namespace Oauth.Signpost.Basic {
      partial class HttpURLConnectionRequestAdapter {
          Java.Lang.Object OauthSignpost.Http.IHttpRequest.Unwrap() {
              return Unwrap();
          }
      }
  }
  ```

- Rimuovere la covarianza dal codice generato in C. Ciò comporta l'aggiunta della trasformazione seguente a **Transforms.Metadata.xml,** che `Java.Lang.Object`farà sì che il codice generato in C' abbia un tipo restituito di:

  ```xml
  <attr
      path="/api/package[@name='oauth.signpost.basic']/class[@name='HttpURLConnectionRequestAdapter']/method[@name='unwrap']"
      name="managedReturn">Java.Lang.Object
  </attr>
  ```

### <a name="problem-name-collisions-on-inner-classes--properties"></a>Problema: conflitti di nomi nelle classi interne / proprietàProblem: Name Collisions on Inner Classes / Properties

Visibilità in conflitto sugli oggetti ereditati.

Nel linguaggio Java non è necessario che una classe derivata abbia la stessa visibilità dell'elemento padre. Java risolverà questo problema per te. Nel linguaggio C, che deve essere esplicito, pertanto è necessario assicurarsi che tutte le classi nella gerarchia hanno la visibilità appropriata. Nell'esempio seguente viene illustrato come `com.evernote.android.job` modificare `Evernote.AndroidJob`il nome di un pacchetto Java da a :

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

### <a name="problem-a-so-library-required-by-the-binding-is-not-loading"></a>Problema: una libreria **.so** richiesta dall'associazione non viene caricata

Alcuni progetti di associazione possono anche dipendere dalla funzionalità in una libreria **.so.** È possibile che Xamarin.Android non carichi automaticamente la libreria **.so.** Quando viene eseguito il codice Java di cui è stato eseguito il wrapping, Xamarin.Android non riuscirà a effettuare la chiamata JNI e il messaggio di errore _java.lang.UnsatisfiedLinkError: metodo nativo non trovato:_ apparirà nel logcat out per l'applicazione.

La correzione per questo consiste nel caricare manualmente `Java.Lang.JavaSystem.LoadLibrary`la libreria **.così** con una chiamata a . Ad esempio, supponendo che un progetto Xamarin.Android abbia una libreria condivisa **libpocketsphinx_jni.così** inclusa nel progetto di associazione con un'azione di compilazione **Di EmbeddedNativeLibrary**, il frammento seguente (eseguito prima di utilizzare la libreria condivisa) caricherà la libreria **.so:**

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="summary"></a>Riepilogo

In questo articolo sono elencati i problemi comuni di risoluzione dei problemi associati alle associazioni Java e viene illustrato come risolverli.

## <a name="related-links"></a>Collegamenti correlati

- [Progetti libreria](https://developer.android.com/tools/projects/index.html#LibraryProjects)
- [Lavorare con JNI](~/android/platform/java-integration/working-with-jni.md)
- [Abilita output diagnostico](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)
- [Xamarin per sviluppatori Android](~/android/get-started/java-developers.md)
- [JD-GUI](http://jd.benow.ca/)
