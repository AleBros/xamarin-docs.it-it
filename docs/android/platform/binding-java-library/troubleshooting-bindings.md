---
title: Risoluzione dei problemi di associazioni
description: In questo articolo riepiloga vari errori comuni che possono verificarsi durante la generazione di associazioni, insieme alle possibili cause e suggeriti i metodi per risolverli.
ms.prod: xamarin
ms.assetid: BB81FCCF-F7BF-4C78-884E-F02C49AA819A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: da6286eed091114c117c723f462bbb8cac77034b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="troubleshooting-bindings"></a>Risoluzione dei problemi di associazioni

_In questo articolo riepiloga vari errori comuni che possono verificarsi durante la generazione di associazioni, insieme alle possibili cause e suggeriti i metodi per risolverli._


## <a name="overview"></a>Panoramica

Associazione di una libreria Android (un **AAR** o **JAR**) file raramente è una questione semplice; in genere richiede un lavoro aggiuntivo per limitare i problemi derivanti dalle differenze tra .NET e Java.
Questi problemi verranno impediscono l'associazione la libreria Android di xamarin e vengono visualizzati come i messaggi di errore nel log di compilazione. Questa Guida verrà sono riportati alcuni suggerimenti per la risoluzione dei problemi, elencano alcuni degli scenari di problemi più comuni e fornire le possibili soluzioni all'associazione correttamente la libreria Android.

Quando si associa una libreria Android esistente, è necessario tenere presente quanto segue:

- **Le dipendenze esterne per la libreria** &ndash; dipendenze Any Java richieste dalla libreria Android devono essere incluso nel progetto di xamarin come un **ReferenceJar** o come un  **EmbeddedReferenceJar**.

- **Il livello API Android che la libreria Android è destinata alla** &ndash; non è possibile effettuare il "downgrade" livello dell'API Android, verificare che il progetto di associazione di xamarin è destinato la stessa API livello (o versione successiva) della libreria Android.

- **La versione di JDK Android utilizzato per creare il pacchetto libreria Android** &ndash; errori di associazione possono verificarsi se la libreria Android è stata compilata con una versione diversa di JDK rispetto a quello usato da xamarin. Se possibile, ricompilare la libreria Android utilizzando la stessa versione di JDK che viene utilizzato per l'installazione di xamarin.

Il primo passaggio per la risoluzione dei problemi con l'associazione di una libreria di xamarin è consentire [diagnostica output MSBuild](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).
Dopo aver abilitato l'output della diagnostica, ricompilare il progetto di associazione di xamarin ed esaminare il log di compilazione per individuare le indicazioni sui qual è la causa del problema.

Può inoltre risultare utile decompilare la libreria Android ed esaminare i tipi e metodi che xamarin sta tentando di associare. Questo aspetto è illustrato in dettaglio più avanti in questa Guida.


## <a name="decompiling-an-android-library"></a>Decompilare una libreria Android

Controllando le classi e metodi delle classi Java, è possibile fornire informazioni importanti che fornirà assistenza nell'associazione di una libreria.
[Interfaccia utente grafica di JD](http://jd.benow.ca/) è un'utilità grafica che è possibile visualizzare il codice sorgente Java dal **classe** file contenuti in un file JAR. Può essere eseguita come applicazione autonoma o come un plug-in per IntelliJ o Eclipse.

Per decompilare open libreria Android di **. File JAR** file con il decompilatore Java. Se la raccolta è un **. AAR** file, è necessario estrarre il file **classes.jar** dal file di archivio. Di seguito è riportato una schermata di esempio dell'utilizzo JD GUI per analizzare il [Picasso](http://square.github.io/picasso/) JAR:

![Utilizzo di decompilatore Java per l'analisi picasso 2.5.2.jar](troubleshooting-bindings-images/troubleshoot-bindings-01.png)

Una volta che si è decompilato libreria Android, esaminare il codice sorgente. In generale, cercare:

- **Le classi che dispongono di caratteristiche di offuscamento** &ndash; caratteristiche delle classi offuscate includono:

    - Il nome di classe include un **$**, vale a dire **una class $**
    - Il nome della classe è interamente compromesso di lettere minuscole, ad esempio **a.class**      

- **`import` istruzioni per le raccolte senza riferimenti** &ndash; identificare la libreria senza riferimento e aggiungere tali dipendenze al progetto xamarin associazione con un **azione di compilazione** di **ReferenceJar**  o **EmbedddedReferenceJar**.

> [!NOTE]
> Decompilare una libreria di Java sia consentito o soggetti a limitazioni legali in base alle leggi locali o la licenza in cui è stata pubblicata la libreria di Java. Se necessario, è possibile integrare i servizi di un legale prima di tentare di decompilare una libreria di Java e controllare il codice sorgente.


## <a name="inspect-apixml"></a>Controllare l'API. XML

Come parte della creazione di un progetto di associazione, xamarin genererà un nome di file XML **obj/Debug/api.xml**:

![Api.xml generato in obj/Debug](troubleshooting-bindings-images/troubleshoot-bindings-02.png)

Questo file fornisce un elenco di tutte le API Java che xamarin sta tentando di associazione. Il contenuto di questo file consente di identificare i tipi o i metodi mancante, duplicato dell'associazione. Sebbene l'ispezione di questo file è noiosa e richiedere tempo, può fornire per indicazioni su ciò che potrebbero causare problemi di associazione. Ad esempio, **api.xml** potrebbe rivelare che una proprietà restituisce un tipo appropriato, o che sono disponibili due tipi che condividono lo stesso nome gestito.


## <a name="known-issues"></a>Problemi noti

In questa sezione elenca alcuni messaggi di errore comuni o i sintomi che la si verificano durante il tentativo di associare una libreria Android.


### <a name="problem-java-version-mismatch"></a>Problema: Mancata corrispondenza tra versione di Java

In alcuni casi i tipi non verranno generati o arresti anomali del sistema imprevisto possono verificarsi se si utilizza una versione più recente o versioni precedente di Java rispetto a ciò che è stata compilata con la libreria. Ricompilare la libreria Android con la stessa versione di JDK che utilizza il progetto xamarin.


### <a name="problem-at-least-one-java-library-is-required"></a>Problema: è necessaria almeno una libreria di Java

Viene visualizzato l'errore "è obbligatoria, almeno una libreria di Java" anche se una. File JAR è stato aggiunto.

#### <a name="possible-causes"></a>Cause possibili:

Verificare che l'azione di compilazione è impostata su `EmbeddedJar`. Poiché sono presenti più azioni di compilazione per. File JAR (ad esempio `InputJar`, `EmbeddedJar`, `ReferenceJar` e `EmbeddedReferenceJar`), il generatore di associazione non può dedurre automaticamente quello da utilizzare per impostazione predefinita. Per ulteriori informazioni sulle azioni di compilazione, vedere [azioni di compilazione](~/android/platform/binding-java-library/index.md).


### <a name="problem-binding-tools-cannot-load-the-jar-library"></a>Problema: Gli strumenti di associazione non è possibile caricare il. Libreria JAR

Il generatore di librerie di associazione non riesce a caricare il. Libreria JAR.

#### <a name="possible-causes"></a>Possibili cause

Alcuni. Impossibile caricare il file JAR librerie che utilizzano l'offuscamento codice (tramite strumenti quali Proguard) dagli strumenti di linguaggio. Poiché lo strumento di uso della reflection Java e il codice byte ASM libreria di progettazione, questi strumenti dipendenti potrebbero rifiutare le librerie offuscate anche se potrebbero passare runtime Android degli strumenti. La soluzione alternativa per questo oggetto è per eseguire l'associazione manualmente queste librerie anziché utilizzare il generatore di associazione.



### <a name="problem-missing-c-types-in-generated-output"></a>Problema: Contiene i tipi c# nell'output generato.

L'associazione **DLL** compilazioni mancati riscontri ma alcuni tipi Java o di origine c# generato non viene compilato a causa di un errore per segnalare che esistono tipi mancanti.

#### <a name="possible-causes"></a>Cause possibili:

Questo errore può verificarsi per vari motivi, come indicato di seguito:

-   La libreria da associare può fare riferimento a una raccolta di Java secondo. Se l'API pubblica per la raccolta associata utilizza tipi dalla seconda libreria, è necessario fare riferimento un'associazione per la seconda raccolta nonché gestita.

-   È possibile che una raccolta è stata inserita a causa di reflection di Java, simile al motivo dell'errore di caricamento di libreria di sopra, che causa il caricamento dei metadati imprevisto. Gli strumenti di xamarin attualmente non è possibile risolvere questa situazione. In tal caso, è necessario associare manualmente la libreria.

-   Si è verificato un bug nel runtime di .NET 4.0 che non è riuscito a caricare l'assembly deve disporre di. Questo problema è stato risolto in runtime .NET 4.5.

-   Consente di Java derivando una classe pubblica dalla classe pubblici, ma questo non è supportato in .NET. Poiché il generatore di associazione non generare le associazioni per le classi non pubblici, le classi derivate, ad esempio, questi non possono essere generati correttamente. Per risolvere questo problema, rimuovere la voce di metadati per le classi derivate utilizzando il nodo remove in **Metadata.xml**, o correggere i metadati che effettua la classe non pubblica pubblico. Anche se la soluzione di quest'ultima verrà creato l'associazione in modo che verrà compilato il codice sorgente c#, la classe non pubblica non deve essere utilizzata.

    Ad esempio:

    ```xml
    <attr path="/api/package[@name='com.some.package']/class[@name='SomeClass']"
        name="visibility">public</attr>
    ```

-   Gli strumenti che coprono le librerie di Java possono interferire con il generatore di associazione di xamarin e la possibilità di generare classi wrapper c#. Il frammento di codice seguente viene illustrato come aggiornare **Metadata.xml** per unobfuscate un nome di classe:

    ```xml
    <attr path="/api/package[@name='{package_name}']/class[@name='{name}']"
        name="obfuscated">false</attr>
    ```

### <a name="problem-generated-c-source-does-not-build-due-to-parameter-type-mismatch"></a>Problema: Generato codice sorgente c# non viene compilato a causa di mancata corrispondenza tra tipi di parametro

Non viene compilato l'origine c# generato. L'override tipi di parametro del metodo non corrispondono.

#### <a name="possible-causes"></a>Cause possibili:

Xamarin è inclusa una varietà di campi di Java che sono mappati alle enumerazioni nelle associazioni di c#. Nelle associazioni generate potrebbero causare problemi di incompatibilità di tipo. Per risolvere questo problema, è necessario modificare per utilizzare le enumerazioni le firme del metodo create dal generatore di associazione. Per ulteriori imformation, vedere [correzione enumerazioni](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md).

### <a name="problem-noclassdeffounderror-in-packaging"></a>Problema: NoClassDefFoundError nel pacchetto

`java.lang.NoClassDefFoundError` viene generata nel passaggio di creazione del pacchetto.

#### <a name="possible-causes"></a>Cause possibili:

Il motivo più probabile di questo errore è che una libreria di Java obbligatoria deve essere aggiunto al progetto di applicazione (**csproj**). . File JAR non vengono risolti automaticamente. Un'associazione di libreria di linguaggio non viene generata sempre rispetto a un assembly utente che non esiste un emulatore o il dispositivo di destinazione (ad esempio Google Maps **maps.jar**). Questo non avviene per il supporto di progetto di libreria Android, come la libreria. File JAR è incorporato nella libreria dll. Ad esempio: [4288 Bug](https://bugzilla.xamarin.com/show_bug.cgi?id=4288)

### <a name="problem-duplicate-custom-eventargs-types"></a>Problema: Duplicare tipi EventArgs personalizzati

Compilazione non riesce a causa di tipi EventArgs personalizzati duplicati. Si verifica un errore simile al seguente:

```shell
error CS0102: The type `Com.Google.Ads.Mediation.DismissScreenEventArgs' already contains a definition for `p0'
```

#### <a name="possible-causes"></a>Cause possibili:

In questo modo si verifica un conflitto tra i tipi di eventi provenienti da più di un tipo di "listener" interfaccia che condivide i metodi con nomi identici. Ad esempio, se sono presenti due interfacce Java, come illustrato nell'esempio seguente, il generatore di crea `DismissScreenEventArgs` per entrambi `MediationBannerListener` e `MediationInterstitialListener`, con conseguente errore.

```java
// Java:
public interface MediationBannerListener {
    void onDismissScreen(MediationBannerAdapter p0);
}
public interface MediationInterstitialListener {
    void onDismissScreen(MediationInterstitialAdapter p0);
}
```

È in base alla progettazione in modo che i nomi lunghi nei tipi di argomento evento vengono evitati. Per evitare questi conflitti, è necessaria una trasformazione dei metadati. Modifica [ **Transforms\Metadata.xml** ](https://github.com/xamarin/monodroid-samples/blob/master/AdMob/AdMob/Transforms/Metadata.xml) e aggiungere un `argsType` attributo su una delle interfacce (o il metodo di interfaccia):

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

Viene generato un messaggio di errore che indica che una classe generata non implementa un metodo che è necessario per un'interfaccia che implementa la classe generata. Tuttavia, analizzando il codice generato, si noterà che il metodo è implementato.

Di seguito è riportato un esempio dell'errore:

```shell
obj\Debug\generated\src\Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.cs(8,23):
error CS0738: 'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter' does not
implement interface member 'Oauth.Signpost.Http.IHttpRequest.Unwrap()'.
'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.Unwrap()' cannot implement
'Oauth.Signpost.Http.IHttpRequest.Unwrap()' because it does not have the matching
return type of 'Java.Lang.Object'
```

#### <a name="possible-causes"></a>Cause possibili:

Si tratta di un problema che si verifica con l'associazione di metodi di Java con i tipi restituiti di covariante. In questo esempio, il metodo `Oauth.Signpost.Http.IHttpRequest.UnWrap()` deve restituire `Java.Lang.Object`. Tuttavia, il metodo `Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.UnWrap()` presenta un tipo restituito di `HttpURLConnection`. Esistono due modi per risolvere il problema:

-   Aggiungere una dichiarazione di classe parziale per `HttpURLConnectionRequestAdapter` e implementare in modo esplicito `IHttpRequest.Unwrap()`:

    ```csharp
    namespace Oauth.Signpost.Basic {
        partial class HttpURLConnectionRequestAdapter {
            Java.Lang.Object OauthSignpost.Http.IHttpRequest.Unwrap() {
                return Unwrap();
            }
        }
    }
    ```

-   Rimuovere la covarianza dal codice c# generato. È necessario aggiungere la seguente trasformazione **Transforms\Metadata.xml** che fa sì che il codice c# generato per un tipo restituito di `Java.Lang.Object`:

    ```xml
    <attr
        path="/api/package[@name='oauth.signpost.basic']/class[@name='HttpURLConnectionRequestAdapter']/method[@name='unwrap']"
        name="managedReturn">Java.Lang.Object
    </attr>
    ```

### <a name="problem-name-collisions-on-inner-classes--properties"></a>Problema: Nome conflitti per le classi interne / proprietà

Visibilità in conflitto per gli oggetti ereditati.

In Java, non è necessario che una classe derivata hanno la stessa visibilità del relativo oggetto padre. Java correggerà solo che per l'utente. In c#, che deve essere esplicita, pertanto è necessario assicurarsi che tutte le classi della gerarchia hanno visibilità appropriato. Nell'esempio seguente viene illustrato come modificare un nome di pacchetto Java da `com.evernote.android.job` a `Evernote.AndroidJob`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

### <a name="problem-a-so-library-required-by-the-binding-is-not-loading"></a>Problema: Un **. so** libreria necessaria per l'associazione non è caricato

Alcuni progetti associazione potrebbero inoltre dipendere da funzionalità in un **. so** libreria. È possibile che xamarin non caricherà automaticamente la **. so** libreria. Quando viene eseguito il wrapping codice Java, xamarin avrà esito negativo rendere la chiamata JNI e il messaggio di errore _java.lang.UnsatisfiedLinkError: metodo nativo non trovato:_ verranno visualizzati nella finestra di logcat out per l'applicazione.

La correzione per questo consiste nel caricare manualmente il **. so** libreria con una chiamata a `Java.Lang.JavaSystem.LoadLibrary`. Ad esempio presupponendo che lo stato di un progetto xamarin è libreria condivisa **libpocketsphinx_jni.so** incluso nel progetto di associazione con un'azione di compilazione di **EmbeddedNativeLibrary**, le operazioni seguenti frammento di codice (eseguito prima di utilizzare la libreria condivisa) caricherà il **. so** libreria:

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="summary"></a>Riepilogo

In questo articolo sono elencati i problemi di risoluzione dei problemi comuni associati associazioni Java e spiegato come risolverli.


## <a name="related-links"></a>Collegamenti correlati

- [Progetti di libreria](http://developer.android.com/tools/projects/index.html#LibraryProjects)
- [Utilizzo di JNI](~/android/platform/java-integration/working-with-jni.md)
- [Abilita l'Output di diagnostica](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)
- [Xamarin per sviluppatori Android](~/android/get-started/java-developers.md)
- [JD-GUI](http://jd.benow.ca/)
