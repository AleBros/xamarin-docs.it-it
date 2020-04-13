---
title: Suggerimenti per la risoluzione dei problemi
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 56137ACA-4811-B312-6860-E16D0FA123F7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/15/2018
ms.openlocfilehash: 6d83afa47c459633506736b2497a82c444352c90
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "79303918"
---
# <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi

## <a name="getting-diagnostic-information"></a>Recupero delle informazioni diagnostiche

Xamarin.Android ha alcuni posti da cercare quando si rintracciano vari bug.
incluse le seguenti:

1. Output MSBuild diagnostico.
2. Log di distribuzione del dispositivo.
3. Output del registro di debug Android.Android Debug Log Output.

<a name="Diagnostic_MSBuild_Output" />

## <a name="diagnostic-msbuild-output"></a>Output MSBuild diagnostico

MSBuild di diagnostica può contenere informazioni aggiuntive relative alla compilazione di pacchetti e può contenere alcune informazioni sulla distribuzione del pacchetto.

Per abilitare l'output di MSBuild di diagnostica in Visual Studio:

1. Fare clic su **Strumenti > Opzioni...**
2. Nella visualizzazione albero a sinistra, selezionare **Progetti e soluzioni > compila ed esegui**
3. Nel riquadro di destra impostare l'elenco a discesa livello di dettaglio dell'output di compilazione MSBuild su Diagnostica
4. Fare clic su **OK**.
5. Pulire e ricompilare il pacchetto.
6. L'output diagnostico è visibile all'interno del pannello Output.

Per abilitare l'output MSBuild diagnostico all'interno di Visual Studio per Mac/OS X:To enable diagnostic MSBuild output within Visual Studio for Mac/OS X:

1. Fare clic su **Preferenze > di Visual Studio per Mac...**
2. Nella visualizzazione albero a sinistra selezionare **Progetti > compilazione**
3. Nel pannello di destra, impostare il menu a discesa Livello di dettaglio Log su Diagnostica
4. Fare clic su **OK**.
5. Riavviare Visual Studio per Mac
6. Pulire e ricompilare il pacchetto.
7. L'output di diagnostica è visibile all'interno del riquadro Errori **(Visualizza > Pad > Errori** ), facendo clic sul pulsante Output di compilazione .

## <a name="device-deployment-logs"></a>Registri di distribuzione dei dispositiviDevice Deployment Logs

Per abilitare la registrazione della distribuzione dei dispositivi in Visual Studio:To enable device deployment logging within Visual Studio:

1. **Strumenti > Opzioni...**>
2. Nella vista ad albero a sinistra, seleziona **Xamarin > Impostazioni Android**
3. Nel pannello di destra, attivare la casella di controllo [X] **di debug dell'estensione (scrive monodroid.log sul desktop).**
4. I messaggi di log vengono scritti nel file monodroid.log sul desktop.

Visual Studio per Mac scrive sempre i log di distribuzione dei dispositivi. Festeggiarli è leggermente più difficile; viene creato un file di log *AndroidUtils* per ogni giorno, ovvero l'ora in cui si verifica una distribuzione, ad esempio: **AndroidTools-2012-10-24_12-35-45.log**.

- In Windows, i file `%LOCALAPPDATA%\XamarinStudio-{VERSION}\Logs`di registro vengono scritti in .
- In OS X, i `$HOME/Library/Logs/XamarinStudio-{VERSION}`file di registro vengono scritti in .

## <a name="android-debug-log-output"></a>Output del registro di debug AndroidAndroid Debug Log Output

Android scriverà molti messaggi nel [registro](~/android/deploy-test/debugging/android-debug-log.md)di debug di Android .
Xamarin.Android uses Android system properties to control the generation of additional messages to the Android Debug Log. Le proprietà di sistema Android possono essere impostate tramite il comando *setprop* all'interno di [Android Debug Bridge (adb)](https://developer.android.com/guide/developing/tools/adb.html):

```shell
adb shell setprop PROPERTY_NAME PROPERTY_VALUE
```

Le proprietà di sistema vengono lette durante l'avvio del processo e pertanto devono essere impostate prima dell'avvio dell'applicazione oppure è necessario riavviare l'applicazione dopo la modifica delle proprietà di sistema.

### <a name="xamarinandroid-system-properties"></a>Proprietà di sistema di Xamarin.Android

Xamarin.Android supporta le seguenti proprietà di sistema:

- *debug.mono.debug*: se una stringa non vuota, equivale a `*mono-debug*`.

- *debug.mono.env*: elenco di variabili*|* di ambiente separate da pipe (' ') da esportare durante l'avvio dell'applicazione, *prima* dell'inizializzazione di mono. Ciò consente di impostare le variabili di ambiente che controllano la registrazione mono.

  > [!NOTE]
  > Poiché il*|* valore è '-separato, il valore deve avere un \`livello aggiuntivo di citazione, poiché il comando *adb shell* \` rimuoverà un set di virgolette.

  > [!NOTE]
  > I valori delle proprietà di sistema Android non possono essere più lunghi di 92 caratteri.

  Esempio:

  ```
  adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"
  ```

- *debug.mono.log*: elenco delimitato da virgole ('*,*') di componenti che devono stampare messaggi aggiuntivi nel registro di debug di Android. Per impostazione predefinita, non è impostato nulla. I componenti includono:

  - *all*: Stampa tutti i messaggi
  - *gc*: Stampa i messaggi relativi a GC.
  - *gref*: Stampa (debole, globale) i messaggi di allocazione e deallocazione dei riferimenti.
  - *lref*: Stampa i messaggi di allocazione e deallocazione dei riferimenti locali.

  > [!NOTE]
  > Questi sono *estremamente* dettagliati. Non abilitare a meno che non sia realmente necessario.

- *debug.mono.trace*: Consente di impostare l'impostazione [mono --trace.](http://docs.go-mono.com/?link=man%3amono(1)) `=PROPERTY_VALUE`

## <a name="deleting-bin-and-obj"></a>Cancellazione `bin` e`obj`

Xamarin.Android ha sofferto in passato di una situazione come:

- Si verifica uno strano errore di compilazione o di runtime.
- È `Clean` `Rebuild`possibile eliminare manualmente `bin` `obj` le directory e .
- Il problema scompare.

Siamo fortemente investiti nella risoluzione di problemi come questi a causa del loro impatto sulla produttività degli sviluppatori.

Se un problema come questo accade all'utente:

1. Fai una nota mentale. Qual è stata l'ultima azione che ha portato il tuo progetto in questo stato?
1. Salvare il log di compilazione corrente. Provare a compilare di nuovo e registrare un [log di compilazione di diagnostica.](#diagnostic-msbuild-output)
1. Inviare una [segnalazione di bug][bug].

Prima di `bin` `obj` eliminare le tue e directory, comprimerle e salvarle per una diagnosi successiva, se necessario. Probabilmente si `Clean` può semplicemente solo il vostro progetto di applicazione Xamarin.Android per ottenere le cose di nuovo di lavoro.

[bug]: https://github.com/xamarin/xamarin-android/wiki/Submitting-Bugs,-Feature-Requests,-and-Pull-Requests

## <a name="xamarinandroid-cannot-resolve-systemvaluetuple"></a>Xamarin.Android non è in grado di risolvere System.ValueTuple

Questo errore si verifica a causa di un'incompatibilità con Visual Studio.

- **Visual Studio 2017 Update 1** (versione 15.1 o precedente) è compatibile solo con **System.ValueTuple NuGet 4.3.0** (o versioni precedenti).

- **Visual Studio 2017 Update 2** (versione 15.2 o successiva) è compatibile solo con **System.ValueTuple NuGet 4.3.1** (o versione successiva).

Scegliere il System.ValueTuple NuGet corretto che corrisponde all'installazione di Visual Studio 2017.

## <a name="gc-messages"></a>Messaggi GC

I messaggi del componente GC possono essere visualizzati impostando la proprietà di sistema debug.mono.log su un valore che contiene gc.

I messaggi GC vengono generati ogni volta che viene eseguito il catalogo globale e forniscono informazioni sulla quantità di lavoro svolto dal catalogo globale:

```shell
I/monodroid-gc(12331): GC cleanup summary: 81 objects tested - resurrecting 21.
```

È possibile generare informazioni GC aggiuntive, `MONO_LOG_LEVEL` ad `debug`esempio informazioni di intervallo, impostando la variabile di ambiente su :

```shell
adb shell setprop debug.mono.env MONO_LOG_LEVEL=debug
```

Questo si tradurrà in (un sacco di) messaggi Mono aggiuntivi, tra cui questi tre di conseguenza:

```shell
D/Mono (15723): GC_BRIDGE num-objects 1 num_hash_entries 81226 sccs size 81223 init 0.00ms df1 285.36ms sort 38.56ms dfs2 50.04ms setup-cb 9.95ms free-data 106.54ms user-cb 20.12ms clenanup 0.05ms links 5523436/5523436/5523096/1 dfs passes 1104 6883/11046605
D/Mono (15723): GC_MINOR: (Nursery full) pause 2.01ms, total 287.45ms, bridge 225.60 promoted 0K major 325184K los 1816K
D/Mono ( 2073): GC_MAJOR: (user request) pause 2.17ms, total 2.47ms, bridge 28.77 major 576K/576K los 0K/16K
```

Nel `GC_BRIDGE` messaggio, `num-objects` è il numero di oggetti bridge `num_hash_entries` che questo passaggio sta prendendo in considerazione ed è il numero di oggetti elaborati durante questa chiamata del codice bridge.

Nei `GC_MINOR` messaggi `GC_MAJOR` e, `total` è la quantità di tempo mentre il mondo è `bridge` in pausa (nessun thread sono in esecuzione), mentre è la quantità di tempo impiegato nel codice di elaborazione bridge (che si occupa di Java VM). Il mondo *non* viene messo in pausa durante l'elaborazione del bridge.

 *In generale,* maggiore è `num_hash_entries`il valore di `bridge` , maggiore sarà il `total` tempo necessario per le raccolte e maggiore sarà il tempo impiegato per la raccolta.

## <a name="global-reference-messages"></a>Messaggi di riferimento globali

Per abilitare la registrazione GROF (Global Reference loggig), la proprietà di sistema *debug.mono.log* deve contenere *gref*, ad esempio:

```shell
adb shell setprop debug.mono.log gref
```

Xamarin.Android utilizza riferimenti globali Android per fornire mapping tra le istanze Java e le istanze gestite associate, come quando si richiama un metodo Java un'istanza Java deve essere fornita a Java.

Sfortunatamente, gli emulatori Android consentono solo 2000 riferimenti globali alla volta. L'hardware ha un limite molto più alto di 52000 riferimenti globali. Il limite inferiore può essere problematico quando si eseguono applicazioni nell'emulatore, quindi sapere *da dove* proviene l'istanza può essere molto utile.

> [!NOTE]
> Il conteggio dei riferimenti globale è interno a Xamarin.Android e non include (e non può) includere riferimenti globali tratti da altre librerie native caricate nel processo. Utilizzare il conteggio dei riferimenti globali come stima.

```shell
I/monodroid-gref(12405): +g+ grefc 108 gwrefc 0 obj-handle 0x40517468/L -> new-handle 0x40517468/L from    at Java.Lang.Object.RegisterInstance(IJavaObject instance, IntPtr value, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Object.SetHandle(IntPtr value, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Object..ctor(IntPtr handle, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor..ctor(System.Action handler, Boolean removable)
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor..ctor(System.Action handler)
I/monodroid-gref(12405):    at Android.App.Activity.RunOnUiThread(System.Action action)
I/monodroid-gref(12405):    at Mono.Samples.Hello.HelloActivity.UseLotsOfMemory(Android.Widget.TextView textview)
I/monodroid-gref(12405):    at Mono.Samples.Hello.HelloActivity.<OnCreate>m__3(System.Object o)
I/monodroid-gref(12405): handle 0x40517468; key_handle 0x40517468: Java Type: `mono/java/lang/RunnableImplementor`; MCW type: `Java.Lang.Thread+RunnableImplementor`
I/monodroid-gref(12405): Disposing handle 0x40517468
I/monodroid-gref(12405): -g- grefc 107 gwrefc 0 handle 0x40517468/L from    at Java.Lang.Object.Dispose(System.Object instance, IntPtr handle, IntPtr key_handle, JObjectRefType handle_type)
I/monodroid-gref(12405):    at Java.Lang.Object.Dispose()
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor.Run()
I/monodroid-gref(12405):    at Java.Lang.IRunnableInvoker.n_Run(IntPtr jnienv, IntPtr native__this)
I/monodroid-gref(12405):    at System.Object.c200fe6f-ac33-441b-a3a0-47659e3f6750(IntPtr , IntPtr )
I/monodroid-gref(27679): +w+ grefc 1916 gwrefc 296 obj-handle 0x406b2b98/G -> new-handle 0xde68f4bf/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1915 gwrefc 294 handle 0xde691aaf/W from take_global_ref_jni
```

Ci sono quattro messaggi di conseguenza:

- Creazione di riferimenti globali: queste sono le righe che iniziano con *l'oggetto g* e forniscono un'analisi dello stack per il percorso del codice di creazione.
- Distruzione dei riferimenti globali: queste sono le righe che iniziano con *-g-* e possono fornire un'analisi dello stack per il percorso del codice che elimina il riferimento globale. Se il GC sta smaltindo il gref, non verrà fornita alcuna traccia dello stack.
- Debole creazione di riferimenti globali: queste sono le linee che iniziano con *.*
- Debole distruzione di riferimento globale: queste sono linee che iniziano con *-w-* .

In tutti i messaggi, il valore *grefc* è il conteggio dei riferimenti globali creati da Xamarin.Android, mentre il valore *grefwc* è il conteggio dei riferimenti globali deboli creati da Xamarin.Android. *L'handle* o il valore *obj-handle* è il valore */* dell'handle JNI e il carattere dopo ' ' è il tipo di valore dell'handle: */L* per riferimento locale, */G* per i riferimenti globali e */W* per i riferimenti globali deboli.

Come parte del processo di Garbage Collector, i riferimenti globali (g) vengono convertiti in riferimenti globali deboli (causando un'eccezione -g-) e -g-), un GC lato Java viene espulso e quindi viene controllato il riferimento globale debole per verificare se è stato raccolto. Se è ancora attivo, viene creato un nuovo gref intorno al rif debole (-g,, -w-), altrimenti il rif debole viene distrutto (-w).

## <a name="java-instance-is-created-and-wrapped-by-a-mcw"></a>L'istanza Java viene creata e di cui viene eseguito il wrapping da un MCW

```shell
I/monodroid-gref(27679): +g+ grefc 2211 gwrefc 0 obj-handle 0x4066df10/L -> new-handle 0x4066df10/L from ...
I/monodroid-gref(27679): handle 0x4066df10; key_handle 0x4066df10: Java Type: `android/graphics/drawable/TransitionDrawable`; MCW type: `Android.Graphics.Drawables.TransitionDrawable`
```

## <a name="a-gc-is-being-performed"></a>È in corso l'esecuzione di un GC...

```shell
I/monodroid-gref(27679): +w+ grefc 1953 gwrefc 259 obj-handle 0x4066df10/G -> new-handle 0xde68f95f/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -g- grefc 1952 gwrefc 259 handle 0x4066df10/G from take_weak_global_ref_jni
```

## <a name="object-is-still-alive-as-handle--null"></a>L'oggetto è ancora attivo, come handle !
## <a name="wref-turned-back-into-a-gref"></a>wref trasformato indietro in un gref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x4066df10
I/monodroid-gref(27679): +g+ grefc 1930 gwrefc 39 obj-handle 0xde68f95f/W -> new-handle 0x4066df10/G from take_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1930 gwrefc 38 handle 0xde68f95f/W from take_global_ref_jni
```

## <a name="object-is-dead-as-handle--null"></a>L'oggetto è morto, come l'handle è null
## <a name="wref-is-freed-no-new-gref-created"></a>wref viene liberato, non viene creato alcun nuovo gref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x0
I/monodroid-gref(27679): -w- grefc 1914 gwrefc 296 handle 0xde68f95f/W from take_global_ref_jni
```

C'è una ruga "interessante" qui: su obiettivi che eseguono Android prima della 4.0, il valore gref è uguale all'indirizzo dell'oggetto Java nella memoria del runtime Android. (Ovvero, il GC è un non-movimento, conservatore, collezionista, e sta dissegnando riferimenti diretti a tali oggetti.) Pertanto, dopo una sequenza di tipo g, w, -g-, g, -w- , il gref risultante avrà lo stesso valore del valore gref originale. Questo rende grepping attraverso i registri abbastanza semplice.

Android 4.0, tuttavia, ha un raccoglitore in movimento e non distribuisce più riferimenti diretti agli oggetti VM di runtime di Android.Android 4.0, however, has a moving collector and no longer hands out direct references to Android runtime VM objects. Di conseguenza, dopo una sequenza di tipo g, w, -g-, g, -w-, il valore gref *sarà diverso.* Se l'oggetto sopravvive a più C, passerà da diversi valori gref, rendendo più difficile determinare da dove è stata effettivamente allocata un'istanza.

### <a name="querying-programmatically"></a>Esecuzione di query a livello di codiceQuerying Programmatically

È possibile eseguire una query sui conteggi GREF e WREF eseguendo una query sull'oggetto. `JniRuntime`

`Java.Interop.JniRuntime.CurrentRuntime.GlobalReferenceCount`- Conteggio riferimenti globale

`Java.Interop.JniRuntime.CurrentRuntime.WeakGlobalReferenceCount`- Conteggio riferimenti debole

## <a name="android-debug-logs"></a>Registri di debug AndroidAndroid Debug Logs

I registri di [debug di Android](~/android/deploy-test/debugging/android-debug-log.md) possono fornire un contesto aggiuntivo per quanto riguarda eventuali errori di runtime visualizzati.

## <a name="floating-point-performance-is-terrible"></a>Le prestazioni a virgola mobile sono terribili!

In alternativa, "L'app viene eseguita 10 volte più velocemente con la build di debug che con la build di rilascio!"

Xamarin.Android supporta asta a più dispositivi: *armeabi*, *armeabi-v7a*e *x86*. Gli ABI dispositivo possono essere specificati all'interno delle **proprietà del progetto > scheda Applicazione > Architetture supportate**.

Le build di debug usano un pacchetto Android che fornisce tutti gli ABI e pertanto utilizzeranno l'ABI più veloce per il dispositivo di destinazione.

Le build di rilascio includeranno solo gli ABI selezionati nella scheda Proprietà progetto. È possibile selezionarne più di uno.

*armeabi* è l'ABI predefinito e ha il supporto più ampio per i dispositivi. *Tuttavia*, armeabi non supporta dispositivi multi-CPU e hardware a virgola mobile, amont altre cose. Di conseguenza, le app che usano il runtime di rilascio armeabi saranno legate a un singolo core e utilizzeranno un'implementazione soft-float. Entrambi questi possono contribuire a un miglioramento significativo delle prestazioni per l'app.

Se la tua app richiede prestazioni a virgola mobile e dignitose (ad esempio i giochi), devi abilitare l'ABI *armeabi-v7a.* Puoi supportare solo il runtime *armeabi-v7a,* anche se ciò significa che i dispositivi meno recenti che supportano solo *armeabi* non saranno in grado di eseguire l'app.

## <a name="could-not-locate-android-sdk"></a>Impossibile individuare Android SDK

Ci sono 2 download disponibili da Google per Android SDK per Windows.
Se si sceglie il programma di installazione .exe, scriverà le chiavi del Registro di sistema che dicono Xamarin.Android dove è stato installato. Se scegli il file .zip e decomprimi da solo, Xamarin.Android non sa dove cercare l'SDK. È possibile indicare a Xamarin.Android dove si trova l'SDK in Visual Studio accedendo a **Strumenti > Opzioni > Impostazioni di Xamarin > Android**:

[![Posizione di Android SDK in Impostazioni Android Xamarin](troubleshooting-images/01.png)](troubleshooting-images/01.png#lightbox)

## <a name="ide-does-not-display-target-device"></a>IDE non visualizza il dispositivo di destinazione

A volte si tenterà di distribuire l'applicazione in un dispositivo, ma il dispositivo che si desidera distribuire non viene visualizzato nella finestra di dialogo Seleziona dispositivo. Questo può accadere quando il ponte di debug Android decide di andare in vacanza.

Per diagnosticare questo problema, individuare il [programma adb](~/android/deploy-test/debugging/android-debug-log.md), quindi eseguire:

```shell
adb devices
```

Se il dispositivo non è presente, è necessario riavviare il server Android Debug Bridge in modo che il dispositivo sia reperibile:

```shell
adb kill-server
adb start-server
```

Il software HTC Sync potrebbe impedire il corretto funzionamento di **adb start-server.** Se il comando **adb start-server** non stampa la porta su cui viene avviato, uscire dal software HTC Sync e provare a riavviare il server adb.

## <a name="the-specified-task-executable-keytool-could-not-be-run"></a>Impossibile eseguire l'eseguibile dell'attività specificata "keytool"

Ciò significa che path non contiene la directory in cui si trova la directory bin di Java SDK. Verificare di aver seguito questi passaggi nella Guida [all'installazione.](~/android/get-started/installation/index.md)

## <a name="monodroidexe-or-aresgenexe-exited-with-code-1"></a>monodroid.exe o aresgen.exe terminato con codice 1

Per facilitare il debug di questo problema, passare a Visual Studio e modificare il livello di dettaglio MSBuild, a tale scopo, selezionare: **Strumenti > Opzioni > e** **soluzioni > Compila** ed Esegui > processo **MSBuild E** impostare questo valore su **Normale**.

Ricompilare e controllare il riquadro Output di Visual Studio, che deve contenere l'errore completo.

## <a name="there-is-not-enough-storage-space-on-the-device-to-deploy-the-package"></a>Lo spazio di archiviazione sul dispositivo non è sufficiente per distribuire il pacchetto

Ciò si verifica quando non si avvia l'emulatore dall'interno di Visual Studio. Quando si avvia l'emulatore all'esterno `-partition-size 512` di Visual Studio, è necessario passare le opzioni, ad esempio

```shell
emulator -partition-size 512 -avd MonoDroid
```

Assicurarsi di utilizzare il nome del simulatore corretto, ovvero [il nome utilizzato durante la configurazione del simulatore.](~/android/get-started/installation/windows.md#device)

## <a name="install_failed_invalid_apk-when-installing-a-package"></a>INSTALL\_\_FAILED\_INVALID INVALID APK durante l'installazione di un pacchetto

I nomi dei pacchetti Android *devono* contenere un punto ('*.*'). Modificare il nome del pacchetto in modo che contenga un punto.

- All'interno di Visual Studio:
  - Fare clic con il pulsante destro del mouse sul progetto > Proprietà
  - Fare clic sulla scheda Manifesto Android a sinistra.
  - Aggiornare il campo Nome pacchetto.
    - Se viene visualizzato &ldquo;il messaggio Nessun AndroidManifest.xml trovato. Fare clic per aggiungerne uno. &rdquo;, fare clic sul collegamento e quindi aggiornare il campo Nome pacchetto.
- All'interno di Visual Studio per Mac:
  - Fare clic con il pulsante destro del mouse sul progetto > Opzioni.
  - Passare alla sezione Compila / Applicazione Android.Navigate to the Build / Android Application section.
  - Modificare il campo Nome pacchetto in modo che contenga un '.'.

## <a name="install_failed_missing_shared_library-when-installing-a-package"></a>INSTALL\_\_FAILED\_\_MISSING SHARED LIBRARY durante l'installazione di un pacchetto

Una "libreria condivisa" in questo contesto *non* è un file di libreria condivisa nativo (*libfoo.so*); è invece una libreria che deve essere installata separatamente sul dispositivo di destinazione, ad esempio Google Maps.

Il pacchetto Android specifica quali librerie `<uses-library/>` condivise sono necessarie con l'elemento. Se una libreria *richiesta* non è presente nel `//uses-library/@android:required` dispositivo di destinazione (ad esempio è *true*, che è l'impostazione predefinita), l'installazione del pacchetto avrà esito negativo con *\_INSTALL\_FAILED MISSING SHARED\_\_LIBRARY*.

Per determinare quali librerie condivise sono necessarie, visualizzare il file**AndroidManifest.xml** *generato*
(ad esempio **obj\\Debug\\androidAndroidManifest.xml\\**) e cercare gli `<uses-library/>` elementi. `<uses-library/>`Gli elementi possono essere aggiunti manualmente nel file **Properties\\AndroidManifest.xml** del progetto e tramite l'attributo [personalizzato UsesLibraryAttribute](xref:Android.App.UsesLibraryAttribute).

Ad esempio, l'aggiunta di un riferimento all'assembly a `<uses-library/>` *Mono.Android.GoogleMaps.dll* aggiungerà in modo implicito un oggetto per la libreria condivisa di Google Maps.

## <a name="install_failed_update_incompatible-when-installing-a-package"></a>INSTALLA\_\_NON\_RIUSCITO AGGIORNAMENTO INCOMPATIBLE durante l'installazione di un pacchetto

I pacchetti Android hanno tre requisiti:

- Devono contenere un '.' (vedere la voce precedente)
- Devono avere un nome di pacchetto di stringhe univoco (da qui la convenzione inversa visualizzata nei nomi delle app Android, ad esempio com.android.chrome per l'app Chrome)
- Quando si aggiornano i pacchetti, il pacchetto deve avere la stessa chiave di firma.

Quindi, immaginate questo scenario:

1. Compilare & distribuire l'app come app di debugYou build & deploy your app as a Debug app
2. Modificare la chiave di firma, ad esempio per utilizzare come app di rilascio (o perché non ti piace la chiave di firma Debug predefinita)
3. L'app viene installata senza prima rimuoverla, ad esempio eseguire il debug > avviare eseguire il debug all'interno di Visual Studio

In questo caso, l'installazione\_del\_\_pacchetto avrà esito negativo con un errore INSTALL FAILED UPDATE INCOMPATIBLE, perché il nome del pacchetto non è stato modificato mentre la chiave di firma non è stata modificata. Il [registro](~/android/deploy-test/debugging/android-debug-log.md) di debug di Android conterrà anche un messaggio simile al:

```shell
E/PackageManager(  146): Package [PackageName] signatures do not match the previously installed version; ignoring!
```

Per correggere questo errore, rimuovere completamente l'applicazione dal dispositivo prima di reinstallarla.

## <a name="install_failed_uid_changed-when-installing-a-package"></a>INSTALLARE\_\_FAILED\_UID CHANGED durante l'installazione di un pacchetto

Quando viene installato un pacchetto Android, viene assegnato un *ID utente* (UID).
*A volte*, per motivi attualmente sconosciuti, quando si installa `INSTALL_FAILED_UID_CHANGED`su un'applicazione già installata, l'installazione avrà esito negativo con:

```shell
ERROR [2015-03-23 11:19:01Z]: ANDROID: Deployment failed
Mono.AndroidTools.InstallFailedException: Failure [INSTALL_FAILED_UID_CHANGED]
   at Mono.AndroidTools.Internal.AdbOutputParsing.CheckInstallSuccess(String output, String packageName)
   at Mono.AndroidTools.AndroidDevice.<>c__DisplayClass2c.<InstallPackage>b__2b(Task`1 t)
   at System.Threading.Tasks.ContinuationTaskFromResultTask`1.InnerInvoke()
   at System.Threading.Tasks.Task.Execute()
```

Per risolvere questo problema, *disinstallare completamente* il pacchetto Android, installando l'app `adb`dalla GUI della destinazione Android o utilizzando:

```shell
$ adb uninstall @PACKAGE_NAME@
```

**DO NOT USE** `adb uninstall -k`, in quanto *conserverà* i dati dell'applicazione e quindi manterrà l'UID in conflitto nel dispositivo di destinazione.

## <a name="release-apps-fail-to-launch-on-device"></a>Release apps fail to launch on device

L'output del registro di debug di Android conterrà un messaggio simile al:

```shell
D/AndroidRuntime( 1710): Shutting down VM
W/dalvikvm( 1710): threadid=1: thread exiting with uncaught exception (group=0xb412f180)
E/AndroidRuntime( 1710): FATAL EXCEPTION: main
E/AndroidRuntime( 1710): java.lang.UnsatisfiedLinkError: Couldn't load monodroid: findLibrary returned null
E/AndroidRuntime( 1710):        at java.lang.Runtime.loadLibrary(Runtime.java:365)
```

In tal caso, ci sono due possibili cause per questo:

1. Il file .apk non fornisce un'interfaccia ABI supportata dal dispositivo di destinazione.
    Ad esempio, il file con estensione apk contiene solo file binari armeabi-v7a e il dispositivo di destinazione supporta solo armeabi.

2. Un [bug Android](https://code.google.com/p/android/issues/detail?id=21670). In questo caso, disinstalla l'app, incrocia le dita e reinstalla l'app.

Per risolvere (1), modificare le opzioni/proprietà del progetto e [aggiungere il supporto per l'ABI richiesto all'elenco degli ACA supportati](~/android/app-fundamentals/cpu-architectures.md).Per determinare quale ABI è necessario aggiungere, eseguire il comando adb seguente sul dispositivo di destinazione:

```shell
adb shell getprop ro.product.cpu.abi
adb shell getprop ro.product.cpu.abi2
```

L'output conterrà gli ARI primari (e secondari facoltativi).

```shell
$ adb shell getprop | grep ro.product.cpu
[ro.product.cpu.abi2]: [armeabi]
[ro.product.cpu.abi]: [armeabi-v7a]
```

## <a name="the-outpath-property-is-not-set-for-project-ldquomyappcsprojrdquo"></a>La proprietà OutPath non &ldquo;è impostata per il progetto MyApp.csproj&rdquo;

Questo in genere significa che si &ldquo;dispone&rdquo; di un computer HP e la variabile di ambiente Piattaforma è stata impostata su qualcosa come MCD o HPD. Ciò è in conflitto con la &ldquo;proprietà&rdquo; &ldquo;Piattaforma MSBuild generalmente impostata su Qualsiasi CPU o x86&rdquo;. È necessario rimuovere questa variabile di ambiente dal computer prima che MSBuild possa funzionare:You will need to remove this environment variable from your machine before MSBuild can function:

- Sistema di > del Pannello di controllo > Variabili di ambiente > avanzate del Pannello di controllo

Riavviare Visual Studio o Visual Studio per Mac e provare a ricompilare. Le cose dovrebbero ora funzionare come previsto.

## <a name="javalangclasscastexception-monoandroidruntimejavaobject-cannot-be-cast-to"></a>impossibile eseguire il cast di java.lang.ClassCastException: mono.android.runtime.JavaObject su...

Xamarin.Android 4.x non esegue correttamente il marshalling dei tipi generici annidati correttamente. Si consideri, ad\# esempio, il seguente codice C utilizzando [SimpleExpandableListAdapter](xref:Android.Widget.SimpleExpandableListAdapter):

```csharp
// BAD CODE; DO NOT USE
var groupData = new List<IDictionary<string, object>> () {
        new Dictionary<string, object> {
                { "NAME", "Group 1" },
                { "IS_EVEN", "This group is odd" },
        },
};
var childData = new List<IList<IDictionary<string, object>>> () {
        new List<IDictionary<string, object>> {
                new Dictionary<string, object> {
                        { "NAME", "Child 1" },
                        { "IS_EVEN", "This group is odd" },
                },
        },
};
mAdapter = new SimpleExpandableListAdapter (
        this,
        groupData,
        Android.Resource.Layout.SimpleExpandableListItem1,
        new string[] { "NAME", "IS_EVEN" },
        new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 },
        childData,
        Android.Resource.Layout.SimpleExpandableListItem2,
        new string[] { "NAME", "IS_EVEN" },
        new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 }
);
```

Il problema è che Xamarin.Android esegue erroneamente il marshalling di tipi generici annidati. Viene `List<IDictionary<string, object>>` eseguito il marshalling di in `Dictionary<string, object>` [java.lang.ArrrayList](xref:Java.Util.ArrayList), ma `ArrayList` contiene `mono.android.runtime.JavaObject` istanze (che fanno riferimento alle istanze) anziché in un elemento che implementa [java.util.Map](xref:Java.Util.IMap), generando l'eccezione seguente:

```shell
E/AndroidRuntime( 2991): FATAL EXCEPTION: main
E/AndroidRuntime( 2991): java.lang.ClassCastException: mono.android.runtime.JavaObject cannot be cast to java.util.Map
E/AndroidRuntime( 2991):        at android.widget.SimpleExpandableListAdapter.getGroupView(SimpleExpandableListAdapter.java:278)
E/AndroidRuntime( 2991):        at android.widget.ExpandableListConnector.getView(ExpandableListConnector.java:446)
E/AndroidRuntime( 2991):        at android.widget.AbsListView.obtainView(AbsListView.java:2271)
E/AndroidRuntime( 2991):        at android.widget.ListView.makeAndAddView(ListView.java:1769)
E/AndroidRuntime( 2991):        at android.widget.ListView.fillDown(ListView.java:672)
E/AndroidRuntime( 2991):        at android.widget.ListView.fillFromTop(ListView.java:733)
E/AndroidRuntime( 2991):        at android.widget.ListView.layoutChildren(ListView.java:1622)
```

La soluzione consiste nell'utilizzare i tipi `System.Collections.Generic` di &ldquo;raccolta&rdquo; [Java](~/android/internals/api-design.md) forniti anziché i tipi per i tipi interni. Ciò comporterà tipi Java appropriati durante il marshalling delle istanze. (Il codice seguente è più complicato del necessario per ridurre la durata del gref. Può essere semplificato per modificare `s/List/JavaList/g` il `s/Dictionary/JavaDictionary/g` codice originale via e se la durata gref non sono una preoccupazione.)

```csharp
// insert good code here
using (var groupData = new JavaList<IDictionary<string, object>> ()) {
    using (var groupEntry = new JavaDictionary<string, object> ()) {
        groupEntry.Add ("NAME", "Group 1");
        groupEntry.Add ("IS_EVEN", "This group is odd");
        groupData.Add (groupEntry);
    }
    using (var childData = new JavaList<IList<IDictionary<string, object>>> ()) {
        using (var childEntry = new JavaList<IDictionary<string, object>> ())
        using (var childEntryDict = new JavaDictionary<string, object> ()) {
            childEntryDict.Add ("NAME", "Child 1");
            childEntryDict.Add ("IS_EVEN", "This child is odd.");
            childEntry.Add (childEntryDict);
            childData.Add (childEntry);
        }
        mAdapter = new SimpleExpandableListAdapter (
            this,
            groupData,
            Android.Resource.Layout.SimpleExpandableListItem1,
            new string[] { "NAME", "IS_EVEN" },
            new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 },
            childData,
            Android.Resource.Layout.SimpleExpandableListItem2,
            new string[] { "NAME", "IS_EVEN" },
            new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 }
        );
    }
}
```

[Questo problema verrà risolto in una versione futura.](https://bugzilla.xamarin.com/show_bug.cgi?id=5401)

## <a name="unexpected-nullreferenceexceptions"></a>NullReferenceException impreviste

Occasionalmente il [registro](~/android/deploy-test/debugging/android-debug-log.md) di debug di &ldquo;Android&rdquo; menzionerà NullReferenceExceptions che non può accadere, o provengono da Mono per il codice di runtime Android poco prima che l'app muore:

```shell
E/mono(15202): Unhandled Exception: System.NullReferenceException: Object reference not set to an instance of an object
E/mono(15202):   at Java.Lang.Object.GetObject (IntPtr handle, System.Type type, Boolean owned)
E/mono(15202):   at Java.Lang.Object._GetObject[IOnTouchListener] (IntPtr handle, Boolean owned)
E/mono(15202):   at Java.Lang.Object.GetObject[IOnTouchListener] (IntPtr handle, Boolean owned)
E/mono(15202):   at Android.Views.View+IOnTouchListenerAdapter.n_OnTouch_Landroid_view_View_Landroid_view_MotionEvent_(IntPtr jnienv, IntPtr native__this, IntPtr native_v, IntPtr native_e)
E/mono(15202):   at (wrapper dynamic-method) object:b039cbb0-15e9-4f47-87ce-442060701362 (intptr,intptr,intptr,intptr)
```

o

```shell
E/mono    ( 4176): Unhandled Exception:
E/mono    ( 4176): System.NullReferenceException: Object reference not set to an instance of an object
E/mono    ( 4176): at Android.Runtime.JNIEnv.NewString (string)
E/mono    ( 4176): at Android.Util.Log.Info (string,string)
```

Questo può accadere quando il runtime Android decide di interrompere il processo, che può accadere per &ldquo;una&rdquo; serie di motivi, tra cui il limite GREF del bersaglio o fare qualcosa di sbagliato con JNI.

Per vedere se questo è il caso, controllare il registro di debug di Android per un messaggio dal processo simile a:

```shell
E/dalvikvm(  123): VM aborting
```

## <a name="abort-due-to-global-reference-exhaustion"></a>Interruzione a causa dell'esaurimento dei riferimenti globali

Il livello JNI del runtime Android supporta solo un numero limitato di riferimenti a oggetti JNI validi in un determinato momento. Quando questo limite viene superato, le cose si interrompono.

Il limite GREF (*global reference*) è 2000 riferimenti nell'emulatore e i riferimenti 52000 su hardware.

Sai che stai iniziando a creare troppi GREF quando vedi messaggi come questo nel Registro di Debug Android:

```shell
D/dalvikvm(  602): GREF has increased to 1801
```

Quando si raggiunge il limite GREF, viene stampato un messaggio simile al seguente:

```shell
D/dalvikvm(  602): GREF has increased to 2001
W/dalvikvm(  602): Last 10 entries in JNI global reference table:
W/dalvikvm(  602):  1991: 0x4057eff8 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1992: 0x4057f010 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1993: 0x40698e70 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1994: 0x40698e88 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1995: 0x40698ea0 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1996: 0x406981f0 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1997: 0x40698208 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1998: 0x40698220 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1999: 0x406956a8 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  2000: 0x406956c0 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602): JNI global reference table summary (2001 entries):
W/dalvikvm(  602):    51 of Ljava/lang/Class; 164B (41 unique)
W/dalvikvm(  602):    46 of Ljava/lang/Class; 188B (17 unique)
W/dalvikvm(  602):     6 of Ljava/lang/Class; 212B (6 unique)
W/dalvikvm(  602):    11 of Ljava/lang/Class; 236B (7 unique)
W/dalvikvm(  602):     3 of Ljava/lang/Class; 260B (3 unique)
W/dalvikvm(  602):     4 of Ljava/lang/Class; 284B (2 unique)
W/dalvikvm(  602):     8 of Ljava/lang/Class; 308B (6 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 316B
W/dalvikvm(  602):     4 of Ljava/lang/Class; 332B (3 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 356B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 380B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 428B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 452B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 476B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 500B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 548B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 572B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 596B (2 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 692B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 956B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1004B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1148B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 1172B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1316B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 3428B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 3452B
W/dalvikvm(  602):     1 of Ljava/lang/String; 28B
W/dalvikvm(  602):     2 of Ldalvik/system/VMRuntime; 12B (1 unique)
W/dalvikvm(  602):    10 of Ljava/lang/ref/WeakReference; 28B (10 unique)
W/dalvikvm(  602):     1 of Ldalvik/system/PathClassLoader; 44B
W/dalvikvm(  602):  1553 of Landroid/graphics/Point; 20B (1553 unique)
W/dalvikvm(  602):   261 of Landroid/graphics/Point; 28B (261 unique)
W/dalvikvm(  602):     1 of Landroid/view/MotionEvent; 100B
W/dalvikvm(  602):     1 of Landroid/app/ActivityThread$ApplicationThread; 28B
W/dalvikvm(  602):     1 of Landroid/content/ContentProvider$Transport; 28B
W/dalvikvm(  602):     1 of Landroid/view/Surface$CompatibleCanvas; 44B
W/dalvikvm(  602):     1 of Landroid/view/inputmethod/InputMethodManager$ControlledInputConnectionWrapper; 36B
W/dalvikvm(  602):     1 of Landroid/view/ViewRoot$1; 12B
W/dalvikvm(  602):     1 of Landroid/view/ViewRoot$W; 28B
W/dalvikvm(  602):     1 of Landroid/view/inputmethod/InputMethodManager$1; 28B
W/dalvikvm(  602):     1 of Landroid/view/accessibility/AccessibilityManager$1; 28B
W/dalvikvm(  602):     1 of Landroid/widget/LinearLayout$LayoutParams; 44B
W/dalvikvm(  602):     1 of Landroid/widget/LinearLayout; 332B
W/dalvikvm(  602):     2 of Lorg/apache/harmony/xnet/provider/jsse/TrustManagerImpl; 28B (1 unique)
W/dalvikvm(  602):     1 of Landroid/view/SurfaceView$MyWindow; 36B
W/dalvikvm(  602):     1 of Ltouchtest/RenderThread; 92B
W/dalvikvm(  602):     1 of Landroid/view/SurfaceView$3; 12B
W/dalvikvm(  602):     1 of Ltouchtest/DrawingView; 412B
W/dalvikvm(  602):     1 of Ltouchtest/Activity1; 180B
W/dalvikvm(  602): Memory held directly by tracked refs is 75624 bytes
E/dalvikvm(  602): Excessive JNI global references (2001)
E/dalvikvm(  602): VM aborting
```

Nell'esempio precedente (che, per inciso, proviene dal [bug 685215](https://bugzilla.novell.com/show_bug.cgi?id=685215)) il problema è che vengono create troppe istanze Android.Graphics.Point; vedere [ \#il commento 2](https://bugzilla.novell.com/show_bug.cgi?id=685215#c2) per un elenco di correzioni per questo particolare bug.

In genere, una soluzione utile consiste nel trovare &ndash; il tipo con troppe &ndash; istanze allocate Android.Graphics.Point nel dump precedente e quindi trovare dove vengono creati nel codice sorgente ed eliminarli in modo appropriato (in modo che la durata dell'oggetto Java viene abbreviata). Questo non è\#sempre appropriato ( 685215 è multithreading, quindi la soluzione semplice evita la chiamata Dispose), ma è la prima cosa da considerare.

È possibile abilitare [la registrazione GREF](~/android/troubleshooting/index.md) per vedere quando vengono creati i GREF e quanti ne esistono.

## <a name="abort-due-to-jni-type-mismatch"></a>Interruzione a causa di mancata corrispondenza del tipo JNIAbort due to JNI type mismatch

Se si esegue il rollforward manuale del codice JNI, è possibile che i tipi `java.lang.Runnable.run` non corrispondano correttamente, ad esempio se si tenta di richiamare su un tipo che non implementa `java.lang.Runnable`. In questo caso, verrà visualizzato un messaggio simile al seguente nel registro di debug di Android:When this occurs, there will be a message similar to this in the Android Debug Log:

```shell
W/dalvikvm( 123): JNI WARNING: can't call Ljava/Type;;.method on instance of Lanother/java/Type;
W/dalvikvm( 123):              in Lmono/java/lang/RunnableImplementor;.n_run:()V (CallVoidMethodA)
...
E/dalvikvm( 123): VM aborting
```

## <a name="dynamic-code-support"></a>Supporto dinamico del codice

### <a name="dynamic-code-does-not-compile"></a>Il codice dinamico non viene compilato

Per utilizzare\# C dynamic nell'applicazione o nella libreria, è necessario aggiungere System.Core.dll, Microsoft.CSharp.dll e Mono.CSharp.dll al progetto.

### <a name="in-release-build-missingmethodexception-occurs-for-dynamic-code-at-run-time"></a>Nella build di rilascio, MissingMethodException si verifica per il codice dinamico in fase di esecuzione.

- È probabile che il progetto di applicazione non disponga di riferimenti a System.Core.dll, Microsoft.CSharp.dll o Mono.CSharp.dll. Assicurarsi che venga fatto riferimento a tali assembly.

  - Tieni presente che il codice dinamico costa sempre. Se è necessario codice efficiente, è consigliabile non utilizzare codice dinamico.

- Nella prima anteprima, tali assembly sono stati esclusi a meno che i tipi in ogni assembly non vengano utilizzati in modo esplicito dal codice dell'applicazione. Per una soluzione alternativa, vedere quanto segue:[http://lists.ximian.com/pipermail/mo...il/009798.html](http://lists.ximian.com/pipermail/monodroid/2012-April/009798.html)

## <a name="projects-built-with-aotllvm-crash-on-x86-devices"></a>Progetti compilati con l'arresto anomalo dell'AOT-LLVM su dispositivi x86

Quando si distribuisce un'app compilata con [AOT-LLVM](~/android/deploy-test/release-prep/index.md) su dispositivi basati su x86, è possibile che venga visualizzato un messaggio di errore di eccezione simile al seguente:

```shell
Assertion: should not be reached at /Users/.../external/mono/mono/mini/tramp-x86.c:124
Fatal signal 6 (SIGABRT), code -6 in tid 4051 (Xamarin.bug56111)
```

Si tratta di un problema noto: la soluzione consiste nel disabilitare LLVM.
