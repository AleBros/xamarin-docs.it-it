---
title: Risoluzione dei problemi delle associazioni
description: In questo articolo vengono riepilogati gli errori comuni del server che possono verificarsi durante la generazione di binding, nonché le possibili cause e le modalità consigliate per risolverli.
ms.prod: xamarin
ms.assetid: BB81FCCF-F7BF-4C78-884E-F02C49AA819A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: c752f4acf4bf43c138a7b359b94620dae5e8d46e
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524530"
---
# <a name="troubleshooting-bindings"></a>Risoluzione dei problemi delle associazioni

_In questo articolo vengono riepilogati gli errori comuni del server che possono verificarsi durante la generazione di binding, nonché le possibili cause e le modalità consigliate per risolverli._


## <a name="overview"></a>Panoramica

Il binding di una libreria Android (un file **. AAR** o **. jar**) è raramente un affare semplice; richiede in genere ulteriore sforzo per attenuare i problemi derivanti dalle differenze tra Java e .NET.
Questi problemi impediscono a Novell. Android di associare la libreria Android e presentarsi come messaggi di errore nel log di compilazione. In questa guida vengono forniti alcuni suggerimenti per la risoluzione dei problemi, vengono elencati alcuni dei problemi o scenari più comuni e vengono fornite le possibili soluzioni per associare correttamente la libreria Android.

Quando si associa una libreria Android esistente, è necessario tenere presenti le considerazioni seguenti:

- **Dipendenze esterne per la libreria** Tutte le dipendenze Java richieste dalla libreria Android devono essere incluse nel progetto Novell. Android come **ReferenceJar** o come EmbeddedReferenceJar. &ndash;

- **Il livello API Android che la libreria Android è destinazione** &ndash; Non è possibile effettuare il "downgrade" del livello API Android. Assicurarsi che il progetto di binding Novell. Android sia destinato allo stesso livello API (o superiore) della libreria Android.

- **Versione di Android JDK usata per creare il pacchetto della libreria Android** &ndash; È possibile che si verifichino errori di binding se la libreria Android è stata compilata con una versione di JDK diversa da quella usata da Novell. Android. Se possibile, ricompilare la libreria Android usando la stessa versione del JDK usata dall'installazione di Novell. Android.

Il primo passaggio per la risoluzione dei problemi relativi all'associazione di una libreria Novell. Android consiste nell'abilitare l' [output di MSBuild di diagnostica](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).
Dopo aver abilitato l'output di diagnostica, ricompilare il progetto di binding Novell. Android ed esaminare il log di compilazione per individuare gli indizi sulla causa del problema.

Può inoltre risultare utile decompilare la libreria Android ed esaminare i tipi e i metodi che Novell. Android sta tentando di associare. Questo aspetto viene trattato in modo più dettagliato più avanti in questa guida.


## <a name="decompiling-an-android-library"></a>Decompilazione di una libreria Android

Il controllo delle classi e dei metodi delle classi Java può fornire informazioni importanti che aiuteranno ad associare una libreria.
[JD-GUI](http://jd.benow.ca/) è un'utilità grafica che consente di visualizzare il codice sorgente Java dai file di **classe** contenuti in un file jar. Può essere eseguita come applicazione autonoma o come plug-in per IntelliJ o Eclipse.

Per decompilare una libreria Android aprire il **. File JAR** con il decompilatore Java. Se la libreria è un oggetto **. File AAR** è necessario estrarre il file **classs. jar** dal file di archivio. Di seguito è riportata una schermata di esempio dell'uso di JD-GUI per analizzare il file jar di [Picasso](http://square.github.io/picasso/) :

![Uso del decompilatore Java per analizzare Picasso-2.5.2. jar](troubleshooting-bindings-images/troubleshoot-bindings-01.png)

Dopo aver decompilato la libreria Android, esaminare il codice sorgente. In generale, cercare:

- **Classi con caratteristiche di offuscamento** &ndash; Le caratteristiche delle classi offuscate includono:

    - Il nome della classe include **$** , ad esempio, **una classe $.**
    - Il nome della classe è interamente compromesso da caratteri minuscoli, ad esempio **una classe.**      

- le istruzioni &ndash; **per le librerie senza riferimenti identificano la libreria senza riferimenti e aggiungono tali dipendenze al progetto di binding Novell. Android con un'azione di compilazione ReferenceJar o `import`**   **EmbedddedReferenceJar**.

> [!NOTE]
> La decompilazione di una libreria Java potrebbe essere vietata o soggetta a restrizioni legali basate sulle leggi locali o sulla licenza con cui è stata pubblicata la libreria Java. Se necessario, integrare i servizi di un professionista legale prima di provare a decompilare una libreria Java ed esaminare il codice sorgente.


## <a name="inspect-apixml"></a>Controllare l'API. XML

Come parte della compilazione di un progetto di binding, Novell. Android genererà un nome file XML **obj/debug/API. XML**:

![API. XML generato in obj/debug](troubleshooting-bindings-images/troubleshoot-bindings-02.png)

Questo file fornisce un elenco di tutte le API Java che Novell. Android sta tentando di associare. Il contenuto di questo file può aiutare a identificare i tipi o i metodi mancanti, ovvero l'associazione duplicata. Sebbene l'ispezione di questo file sia noiosa e dispendiosa in termini di tempo, può fornire indizi su cosa potrebbe causare problemi di binding. Ad esempio, il **file API. XML** potrebbe rivelare che una proprietà restituisce un tipo non appropriato o che esistono due tipi che condividono lo stesso nome gestito.


## <a name="known-issues"></a>Problemi noti

In questa sezione sono elencati alcuni dei messaggi di errore comuni che si verificano quando si tenta di associare una libreria Android.


### <a name="problem-java-version-mismatch"></a>Problema: Versione Java non corrispondente

A volte i tipi non vengono generati o possono verificarsi arresti imprevisti perché si utilizza una versione più recente o precedente di Java rispetto a quella con cui la libreria è stata compilata. Ricompilare la libreria Android con la stessa versione del JDK usato dal progetto Novell. Android.


### <a name="problem-at-least-one-java-library-is-required"></a>Problema: Almeno una libreria Java è obbligatoria

Viene visualizzato l'errore "almeno una libreria Java è obbligatoria", anche se a. Il file JAR è stato aggiunto.

#### <a name="possible-causes"></a>Possibili cause:

Verificare che l'azione di compilazione sia impostata `EmbeddedJar`su. Poiché sono presenti più azioni di compilazione per. File jar, ad esempio `InputJar` `ReferenceJar` , `EmbeddedJar`e `EmbeddedReferenceJar`, il generatore di associazioni non può indovinare automaticamente quale usare per impostazione predefinita. Per ulteriori informazioni sulle azioni di compilazione, vedere [azioni di compilazione](~/android/platform/binding-java-library/index.md).


### <a name="problem-binding-tools-cannot-load-the-jar-library"></a>Problema: Gli strumenti di associazione non possono caricare. Libreria JAR

Il generatore di librerie di binding non è in grado di caricare. Libreria JAR.

#### <a name="possible-causes"></a>Possibili cause

Alcuni. Le librerie JAR che usano l'offuscamento del codice (tramite strumenti come Proguard) non possono essere caricate dagli strumenti Java. Poiché lo strumento usa la reflection Java e la libreria di progettazione del codice di byte ASM, questi strumenti dipendenti possono rifiutare le librerie offuscate, mentre gli strumenti di runtime di Android potrebbero essere superati. Per risolvere questo problema, è necessario associare manualmente queste librerie anziché utilizzare il generatore di associazioni.



### <a name="problem-missing-c-types-in-generated-output"></a>Problema: Tipi C# mancanti nell'output generato.

Binding **. dll** viene compilato ma non contiene alcuni tipi Java oppure l'origine C# generata non viene compilata a causa di un errore che informa che mancano tipi.

#### <a name="possible-causes"></a>Possibili cause:

Questo errore può verificarsi a causa di diversi motivi, come indicato di seguito:

- La libreria da associare può fare riferimento a una seconda libreria Java. Se l'API pubblica per la libreria associata usa tipi della seconda libreria, è necessario fare riferimento anche a un'associazione gestita per la seconda libreria.

- È possibile che una libreria sia stata inserita a causa della reflection Java, in modo analogo al motivo dell'errore di caricamento della libreria precedente, causando il caricamento imprevisto dei metadati. Gli strumenti di Novell. Android non possono attualmente risolvere questa situazione. In tal caso, è necessario associare manualmente la libreria.

- Si è verificato un errore nel Runtime .NET 4,0 che non è riuscito a caricare gli assembly quando necessario. Questo problema è stato risolto nel runtime di .NET 4,5.

- Java consente di derivare una classe pubblica da una classe non pubblica, ma non è supportata in .NET. Poiché il generatore di associazioni non genera binding per le classi non pubbliche, le classi derivate, ad esempio, non possono essere generate correttamente. Per risolvere questo problema, rimuovere la voce di metadati per le classi derivate usando il Remove-node in **Metadata. XML**o correggere i metadati che rendono pubblica la classe non pubblica. Anche se quest'ultima soluzione creerà l'associazione in modo C# che l'origine venga compilata, non è consigliabile usare la classe non pubblica.

    Ad esempio:

    ```xml
    <attr path="/api/package[@name='com.some.package']/class[@name='SomeClass']"
        name="visibility">public</attr>
    ```

- Gli strumenti per offuscare le librerie Java potrebbero interferire con il generatore di binding Novell. Android C# e la relativa capacità di generare classi wrapper. Il frammento di codice seguente illustra come aggiornare **Metadata. XML** per deoffuscare il nome di una classe:

    ```xml
    <attr path="/api/package[@name='{package_name}']/class[@name='{name}']"
        name="obfuscated">false</attr>
    ```

### <a name="problem-generated-c-source-does-not-build-due-to-parameter-type-mismatch"></a>Problema: L' C# origine generata non viene compilata a causa della mancata corrispondenza del tipo di parametro

L'origine C# generata non viene compilata. I tipi di parametro del metodo sottoposto a override non corrispondono.

#### <a name="possible-causes"></a>Possibili cause:

Novell. Android include un'ampia gamma di campi Java mappati alle enumerazioni nelle C# associazioni. Questi possono causare incompatibilità dei tipi nelle associazioni generate. Per risolvere questo problema, è necessario modificare le firme del metodo create dal generatore di binding per usare le enumerazioni. Per ulteriori informazioni, vedere [correzione](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md)delle enumerazioni.

### <a name="problem-noclassdeffounderror-in-packaging"></a>Problema: NoClassDefFoundError nel pacchetto

`java.lang.NoClassDefFoundError`viene generata nel passaggio della creazione del pacchetto.

#### <a name="possible-causes"></a>Possibili cause:

Il motivo più probabile di questo errore è che è necessario aggiungere una libreria Java obbligatoria al progetto dell'applicazione (**csproj**). . I file JAR non vengono risolti automaticamente. Un'associazione di libreria Java non viene sempre generata a fronte di un assembly utente che non esiste nel dispositivo o nell'emulatore di destinazione, ad esempio Google Maps **maps. jar**. Questo non è il caso del supporto per il progetto di libreria Android, come la libreria. JAR è incorporato nella dll della libreria. Ad esempio:  [Bug 4288](https://bugzilla.xamarin.com/show_bug.cgi?id=4288)

### <a name="problem-duplicate-custom-eventargs-types"></a>Problema: Tipi EventArgs personalizzati duplicati

Compilazione non riuscita a causa di tipi EventArgs personalizzati duplicati. Si verifica un errore analogo al seguente:

```shell
error CS0102: The type `Com.Google.Ads.Mediation.DismissScreenEventArgs' already contains a definition for `p0'
```

#### <a name="possible-causes"></a>Possibili cause:

Ciò è dovuto al fatto che si verifica un conflitto tra i tipi di evento provenienti da più di un tipo di interfaccia "listener" che condivide i metodi con nomi identici. Se, ad esempio, sono presenti due interfacce Java, come illustrato nell'esempio seguente, il generatore `DismissScreenEventArgs` crea `MediationBannerListener` per e `MediationInterstitialListener`, causando l'errore.

```java
// Java:
public interface MediationBannerListener {
    void onDismissScreen(MediationBannerAdapter p0);
}
public interface MediationInterstitialListener {
    void onDismissScreen(MediationInterstitialAdapter p0);
}
```

Questa operazione è progettata in modo che i nomi lunghi sui tipi di argomento dell'evento vengano evitati. Per evitare questi conflitti, è necessaria una trasformazione dei metadati. Modificare [**Transforms\Metadata.XML**](https://github.com/xamarin/monodroid-samples/blob/master/AdMob/AdMob/Transforms/Metadata.xml) e aggiungere un `argsType` attributo in una delle interfacce (o nel metodo di interfaccia):

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

### <a name="problem-class-does-not-implement-interface-method"></a>Problema: La classe non implementa il metodo di interfaccia

Viene generato un messaggio di errore che indica che una classe generata non implementa un metodo necessario per un'interfaccia implementata dalla classe generata. Tuttavia, osservando il codice generato, è possibile osservare che il metodo è implementato.

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

Si tratta di un problema che si verifica con l'associazione di metodi Java con tipi restituiti covariante. In questo esempio, il metodo `Oauth.Signpost.Http.IHttpRequest.UnWrap()` deve restituire. `Java.Lang.Object` Tuttavia, il metodo `Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.UnWrap()` ha un `HttpURLConnection`tipo restituito. Esistono due modi per risolvere il problema:

- Aggiungere una dichiarazione di classe parziale `HttpURLConnectionRequestAdapter` per e implementare `IHttpRequest.Unwrap()`in modo esplicito:

    ```csharp
    namespace Oauth.Signpost.Basic {
        partial class HttpURLConnectionRequestAdapter {
            Java.Lang.Object OauthSignpost.Http.IHttpRequest.Unwrap() {
                return Unwrap();
            }
        }
    }
    ```

- Rimuovere la covarianza dal codice C# generato. Questa operazione comporta l'aggiunta della trasformazione seguente a `Java.Lang.Object` **Transforms\Metadata.XML** , che farà C# sì che il codice generato abbia un tipo restituito:

    ```xml
    <attr
        path="/api/package[@name='oauth.signpost.basic']/class[@name='HttpURLConnectionRequestAdapter']/method[@name='unwrap']"
        name="managedReturn">Java.Lang.Object
    </attr>
    ```

### <a name="problem-name-collisions-on-inner-classes--properties"></a>Problema: Conflitti di nomi per classi/proprietà interne

Visibilità in conflitto sugli oggetti ereditati.

In Java non è necessario che una classe derivata abbia la stessa visibilità del padre. Java lo correggerà. In C#, questo deve essere esplicito, pertanto è necessario assicurarsi che tutte le classi nella gerarchia abbiano la visibilità appropriata. Nell'esempio seguente viene illustrato come modificare il nome di un pacchetto `com.evernote.android.job` Java `Evernote.AndroidJob`da a:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

### <a name="problem-a-so-library-required-by-the-binding-is-not-loading"></a>Problema: A **.** la libreria richiesta dall'associazione non viene caricata

Alcuni progetti di associazione possono inoltre dipendere dalla funzionalità di una libreria. **so** . È possibile che Novell. Android non caricherà automaticamente la libreria **. so** . Quando viene eseguito il codice Java di cui è stato eseguito il Wrapped, Novell. Android non riuscirà a eseguire _la chiamata JNI e il messaggio di errore Java. lang. UnsatisfiedLinkError: Metodo nativo non trovato:_ verrà visualizzato in logcat out per l'applicazione.

La correzione per questa operazione consiste nel caricare manualmente la libreria **. so** con una chiamata `Java.Lang.JavaSystem.LoadLibrary`a. Si supponga, ad esempio, che un progetto Novell. Android disponga di una libreria condivisa **libpocketsphinx_jni. pertanto** , incluso nel progetto di binding con un'azione di compilazione **EmbeddedNativeLibrary**, il frammento di codice seguente (eseguito prima di utilizzare la libreria condivisa) caricherà la libreria **. so** :

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="summary"></a>Riepilogo

In questo articolo sono stati elencati i problemi comuni di risoluzione dei problemi associati ai binding Java e viene spiegato come risolverli.


## <a name="related-links"></a>Collegamenti correlati

- [Progetti di libreria](https://developer.android.com/tools/projects/index.html#LibraryProjects)
- [Uso di JNI](~/android/platform/java-integration/working-with-jni.md)
- [Abilita output di diagnostica](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)
- [Novell per sviluppatori Android](~/android/get-started/java-developers.md)
- [JD-GUI](http://jd.benow.ca/)
