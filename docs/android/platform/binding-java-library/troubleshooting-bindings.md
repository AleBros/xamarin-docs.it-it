---
title: Risoluzione dei problemi delle associazioni
description: Questo articolo vengono riepilogati i vari errori comuni che possono verificarsi durante la generazione di associazioni, insieme a possibili cause e metodi consigliati per risolverli.
ms.prod: xamarin
ms.assetid: BB81FCCF-F7BF-4C78-884E-F02C49AA819A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: b0bb7cbb6160865af5b1e40d40c7b999a8bd5ebc
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60956533"
---
# <a name="troubleshooting-bindings"></a>Risoluzione dei problemi delle associazioni

_Questo articolo vengono riepilogati i vari errori comuni che possono verificarsi durante la generazione di associazioni, insieme a possibili cause e metodi consigliati per risolverli._


## <a name="overview"></a>Panoramica

Associazione di una libreria Android (un **con estensione AAR** o una **JAR**) file raramente è una semplice-or-miss; in genere richiede un impegno maggiore per mitigare i problemi derivati dalle differenze tra Java e .NET.
Questi problemi verranno impediscono l'associazione la libreria Android di xamarin. Android e vengono visualizzati come i messaggi di errore nel log di compilazione. Questa Guida verrà offrono alcuni suggerimenti per la risoluzione dei problemi, elenca alcuni degli scenari di problemi più comuni e forniscono possibili soluzioni a corretta associazione la libreria Android.

Quando si associa una libreria Android esistente, è necessario tenere presente quanto riportato di seguito:

- **Le dipendenze esterne per la libreria** &ndash; le dipendenze Java di qualsiasi richieste dalla libreria Android devono essere incluso nel progetto xamarin. Android come una **ReferenceJar** o come un  **EmbeddedReferenceJar**.

- **Il livello API Android che la libreria Android è destinata** &ndash; non è possibile "il downgrade di" il livello API Android, assicurarsi che il progetto di associazione di xamarin. Android è destinato alla stessa API livello (o versione successiva) come la libreria Android.

- **La versione del JDK Android usato per creare un pacchetto di libreria Android** &ndash; errori di associazione possono verificarsi se la libreria Android è stata creata con una versione diversa di JDK rispetto a quello usato da xamarin. Android. Se possibile, ricompilare la libreria Android usando la stessa versione di JDK che viene usato per l'installazione di xamarin. Android.

Il primo passaggio per risolvere i problemi con una libreria xamarin. Android di binding è consentire [output di MSBuild di diagnostica](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).
Dopo aver abilitato l'output della diagnostica, ricompilare il progetto di associazione di xamarin. Android ed esaminare il log di compilazione per individuare gli indizi su che cos'è la causa del problema.

Può inoltre rivelarsi utile decompilare la libreria Android ed esaminare i tipi e metodi che xamarin. Android sta tentando di eseguire l'associazione. Questo argomento viene trattato in modo più dettagliato più avanti in questa Guida.


## <a name="decompiling-an-android-library"></a>Decompilazione una libreria Android

Esaminare le classi e metodi delle classi Java può fornire utili informazioni che consentiranno di associazione di una libreria.
[Interfaccia utente grafica di JD](http://jd.benow.ca/) è un'utilità con interfaccia grafica che può visualizzare codice Java source dal **classe** file contenuti in un file JAR. Si può essere eseguito come un'applicazione autonoma o come un plug-in per Eclipse o IntelliJ.

Per decompilare una libreria Android aperta il **. JAR** file con il decompilatore Java. Se la raccolta è un **. AAR** file, è necessario estrarre il file **classes.jar** dal file di archivio. Di seguito è riportato uno screenshot di esempio dell'uso di JD GUI per analizzare le [Picasso](http://square.github.io/picasso/) JAR:

![Uso di Java decompilatore per analizzare picasso 2.5.2.jar](troubleshooting-bindings-images/troubleshoot-bindings-01.png)

Una volta che si have origini decompilate la libreria Android, esaminare il codice sorgente. In generale, cercare:

- **Le classi che dispongono di caratteristiche di offuscamento** &ndash; caratteristiche delle classi offuscate includono:

    - Il nome della classe include una **$**, vale a dire **una class $**
    - Il nome della classe è compromesso interamente di caratteri minuscoli, ad esempio **a.class**      

- **`import` le istruzioni per le raccolte senza riferimenti** &ndash; identificare la libreria privo di riferimento e aggiungere tali dipendenze al progetto di associazione di xamarin. Android con un **azione di compilazione** di **ReferenceJar**  oppure **EmbedddedReferenceJar**.

> [!NOTE]
> Decompilazione una libreria Java potrebbe essere vietato o soggette a restrizioni legali base leggi locali o la licenza in base alle quali è stata pubblicata la libreria Java. Se necessario, è possibile integrare i servizi di un professionista legale prima di provare a decompilare una libreria Java e controllare il codice sorgente.


## <a name="inspect-apixml"></a>Esaminare l'API. XML

Come parte della creazione di un progetto di associazione, xamarin. Android genera un nome file XML **obj/Debug/api.xml**:

![Api.xml generato in obj/Debug](troubleshooting-bindings-images/troubleshoot-bindings-02.png)

Questo file fornisce un elenco di tutte le API di Java che sta tentando di binding di xamarin. Android. Il contenuto di questo file consente di identificare i tipi o metodi mancanti, associazione duplicata. Sebbene l'ispezione di questo file è noioso e richiedere molto tempo, può offrire per indicazioni su cosa potrebbe causare problemi dell'associazione. Ad esempio, **api.xml** potrebbe rivelare che una proprietà restituisce un tipo non appropriato o che sono presenti due tipi che condividono lo stesso nome gestito.


## <a name="known-issues"></a>Problemi noti

Questa sezione vengono elencati alcuni messaggi di errore comuni o i sintomi che mio si verificano durante il tentativo di eseguire l'associazione di una libreria Android.


### <a name="problem-java-version-mismatch"></a>Problema: Mancata corrispondenza tra versione Java

In alcuni casi i tipi non verranno generati o arresti anomali del sistema imprevisto potrebbe verificarsi se si usa una versione più recente o meno recente di Java rispetto a ciò che è stata compilata con la libreria. Ricompilare la libreria Android con la stessa versione di JDK che utilizza il progetto xamarin. Android.


### <a name="problem-at-least-one-java-library-is-required"></a>Problema: È necessaria almeno una libreria Java

Viene visualizzato l'errore "è obbligatorio, almeno una libreria Java" anche se una. File JAR è stato aggiunto.

#### <a name="possible-causes"></a>Possibili cause:

Verificare che l'azione di compilazione è impostata su `EmbeddedJar`. Poiché sono presenti più azioni di compilazione per. File con estensione JAR (ad esempio `InputJar`, `EmbeddedJar`, `ReferenceJar` e `EmbeddedReferenceJar`), il generatore di binding non può dedurre automaticamente quello da usare per impostazione predefinita. Per altre informazioni sulle azioni di compilazione, vedere [azioni di compilazione](~/android/platform/binding-java-library/index.md).


### <a name="problem-binding-tools-cannot-load-the-jar-library"></a>Problema: Strumenti di associazione non è possibile caricare il. Libreria JAR

Il generatore di libreria di binding non è possibile caricare il. Libreria JAR.

#### <a name="possible-causes"></a>Possibili cause

Alcuni. Impossibile caricare le librerie JAR che usano offuscamento codice (tramite strumenti quali Proguard) dagli strumenti di linguaggio. Poiché lo strumento consente di utilizzare la reflection Java e il codice byte ASM libreria di progettazione, questi strumenti dipendenti potrebbero pertanto rifiutare le librerie offuscate anche se gli strumenti di Android runtime possono passare. La soluzione alternativa per questo oggetto è per eseguire l'associazione manualmente queste librerie invece di usare il generatore di associazione.



### <a name="problem-missing-c-types-in-generated-output"></a>Problema: Mancanti C# tipi di output generato.

L'associazione **. dll** compilazioni ma mancati riscontri alcuni tipi di Java o generate C# origine non viene compilato a causa di un errore che indica che esistono tipi mancanti.

#### <a name="possible-causes"></a>Possibili cause:

Questo errore può verificarsi a causa di motivi, come indicato di seguito:

-   La libreria a cui è associata può fare riferimento a una libreria Java secondo. Se l'API pubblica per la libreria con Binding Usa tipi dalla libreria di secondo, deve fare riferimento a un'associazione per la seconda raccolta anche gestita.

-   È possibile che una libreria è stata inserita a causa di reflection di Java, simile al motivo dell'errore di caricamento di libreria di sopra, che causa il caricamento imprevisto di metadati. Strumenti di xamarin. Android non sono attualmente in grado di risolvere questa situazione. In tal caso, è necessario associare manualmente la libreria.

-   Si è verificato un bug nel runtime di .NET 4.0 che non è stato possibile caricare gli assembly devono disporre di. Questo problema è stato risolto in runtime di .NET 4.5.

-   Java consente la derivazione di una classe pubblica dalla classe non pubblica, ma ciò non è supportato in .NET. Poiché il generatore di binding non genera associazioni per le classi non pubblico, le classi derivate, ad esempio, questi non possono essere generati in modo corretto. Per risolvere questo problema, rimuovere la voce di metadati per tali classi derivate tramite il nodo remove nel **Metadata. XML**, o correggere i metadati che rende pubblica la classe non pubblica. Anche se la soluzione quest'ultima verrà creato il binding in modo che il C# origine verrà compilata, non usare la classe non pubblica.

    Ad esempio:

    ```xml
    <attr path="/api/package[@name='com.some.package']/class[@name='SomeClass']"
        name="visibility">public</attr>
    ```

-   Gli strumenti che coprono le librerie Java possono interferire con il generatore di Binding xamarin. Android e la possibilità di generare C# classi wrapper. Il frammento di codice seguente illustra come aggiornare **Metadata. XML** a unobfuscate un nome di classe:

    ```xml
    <attr path="/api/package[@name='{package_name}']/class[@name='{name}']"
        name="obfuscated">false</attr>
    ```

### <a name="problem-generated-c-source-does-not-build-due-to-parameter-type-mismatch"></a>Problema: Generato C# origine non viene compilato a causa di mancata corrispondenza tra tipi di parametro

Generato C# origine non viene compilato. L'override tipi di parametro del metodo non corrispondono.

#### <a name="possible-causes"></a>Possibili cause:

Xamarin. Android include una vasta gamma di campi di Java che vengono mappate alle enumerazioni in di C# associazioni. Tali può causare incompatibilità di tipo nelle associazioni generate. Per risolvere questo problema, le firme di metodo create dal generatore di associazione devono essere modificati per usare le enumerazioni. Per altre imformation, vedi [correzione enumerazioni](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md).

### <a name="problem-noclassdeffounderror-in-packaging"></a>Problema: NoClassDefFoundError nella creazione di pacchetti

`java.lang.NoClassDefFoundError` viene generata nel passaggio di creazione dei pacchetti.

#### <a name="possible-causes"></a>Possibili cause:

Il motivo più probabile di questo errore è che una libreria Java obbligatoria deve essere aggiunto al progetto di applicazione (**file con estensione csproj**). . File con estensione JAR non vengono risolti automaticamente. Un'associazione di libreria Java non viene sempre generata in base a un assembly utente che non esiste nel dispositivo di destinazione o un emulatore (ad esempio Google Maps **maps.jar**). Ciò non avviene per il supporto di progetto di libreria Android, come la libreria. Con estensione JAR sono incorporati nella dll della libreria. Ad esempio: [Bug 4288](https://bugzilla.xamarin.com/show_bug.cgi?id=4288)

### <a name="problem-duplicate-custom-eventargs-types"></a>Problema: Tipi di EventArgs personalizzati duplicati

Compilazione non riesce a causa di tipi di EventArgs personalizzati duplicati. Si verifica un errore simile al seguente:

```shell
error CS0102: The type `Com.Google.Ads.Mediation.DismissScreenEventArgs' already contains a definition for `p0'
```

#### <a name="possible-causes"></a>Possibili cause:

Questo avviene perché si verifica un conflitto tra i tipi di eventi provenienti da più di un tipo di "listener" interfaccia che condivide i metodi con nomi identici. Ad esempio, se sono presenti due interfacce Java come illustrato nell'esempio seguente, il generatore creerà `DismissScreenEventArgs` per entrambi `MediationBannerListener` e `MediationInterstitialListener`, con conseguente nel messaggio di errore.

```java
// Java:
public interface MediationBannerListener {
    void onDismissScreen(MediationBannerAdapter p0);
}
public interface MediationInterstitialListener {
    void onDismissScreen(MediationInterstitialAdapter p0);
}
```

Ciò è per impostazione predefinita, in modo che i nomi di lunga durati su tipi di argomento di evento vengono evitati. Per evitare questi conflitti, è necessaria una trasformazione di metadati. Modificare [ **Transforms\Metadata.xml** ](https://github.com/xamarin/monodroid-samples/blob/master/AdMob/AdMob/Transforms/Metadata.xml) e aggiungere un `argsType` attributo in una delle interfacce (o il metodo di interfaccia):

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

Viene generato un messaggio di errore che indica che una classe generata non implementa un metodo che è necessario per un'interfaccia che implementa la classe generata. Tuttavia, osservando il codice generato, si noterà che il metodo è implementato.

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

Si tratta di un problema che si verifica con binding metodi Java con tipi restituiti covarianti. In questo esempio, il metodo `Oauth.Signpost.Http.IHttpRequest.UnWrap()` deve restituire `Java.Lang.Object`. Tuttavia, il metodo `Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.UnWrap()` ha un tipo restituito di `HttpURLConnection`. Esistono due modi per risolvere questo problema:

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

-   Rimuovere la covarianza dal generato C# codice. È necessario aggiungere la seguente trasformazione **Transforms\Metadata.xml** quale verrà generata C# codice di avere un tipo restituito `Java.Lang.Object`:

    ```xml
    <attr
        path="/api/package[@name='oauth.signpost.basic']/class[@name='HttpURLConnectionRequestAdapter']/method[@name='unwrap']"
        name="managedReturn">Java.Lang.Object
    </attr>
    ```

### <a name="problem-name-collisions-on-inner-classes--properties"></a>Problema: Conflitti nelle classi interne di nome / proprietà

Visibilità in conflitto negli oggetti ereditati.

In Java, non è necessario che una classe derivata hanno la stessa visibilità del relativo sito padre. Linguaggio risolverà appena che per l'utente. In C#, che deve essere esplicita, pertanto è necessario assicurarsi che tutte le classi nella gerarchia di avere la visibilità appropriata. Nell'esempio seguente viene illustrato come modificare un nome del pacchetto da Java `com.evernote.android.job` a `Evernote.AndroidJob`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

### <a name="problem-a-so-library-required-by-the-binding-is-not-loading"></a>Problema: Oggetto **SO** libreria richiesta per l'associazione è il caricamento non

Alcuni progetti di binding possono anche dipendere da funzionalità in un **SO** libreria. È possibile che xamarin. Android non verrà automaticamente caricata la **SO** libreria. Quando viene eseguito il codice Java sottoposto a wrapping, xamarin. Android riuscirà a effettuare la chiamata JNI e il messaggio di errore _java.lang.UnsatisfiedLinkError: Metodo nativo non trovato:_ verranno visualizzati nella finestra di logcat out per l'applicazione.

La soluzione per questo problema consiste nel caricare manualmente il **SO** libreria con una chiamata a `Java.Lang.JavaSystem.LoadLibrary`. Ad esempio presupponendo che un progetto xamarin. Android ha condiviso library **libpocketsphinx_jni.so** incluso nel progetto di associazione con un'azione di compilazione **EmbeddedNativeLibrary**, il seguente frammento di codice (eseguito prima di usare la libreria condivisa) verranno caricati i **SO** libreria:

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="summary"></a>Riepilogo

In questo articolo sono elencati i problemi di risoluzione dei problemi comuni associati associazioni Java ed è stato spiegato come risolvere i problemi.


## <a name="related-links"></a>Collegamenti correlati

- [Progetti di libreria](https://developer.android.com/tools/projects/index.html#LibraryProjects)
- [Utilizzo di JNI](~/android/platform/java-integration/working-with-jni.md)
- [Abilitare l'Output di diagnostica](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)
- [Xamarin per sviluppatori Android](~/android/get-started/java-developers.md)
- [JD-GUI](http://jd.benow.ca/)
