---
title: Suggerimenti per la risoluzione dei problemi
ms.topic: article
ms.prod: xamarin
ms.assetid: 56137ACA-4811-B312-6860-E16D0FA123F7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 145c8507ca5ebea6197fa8827b93f58fbc9bb078
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi


## <a name="getting-diagnostic-information"></a>Recupero di informazioni di diagnostica

Xamarin è alcuni punti in cui individuare bug varie cercare.
Sono inclusi:

1.  Output di diagnostica MSBuild.
2.  Registri di distribuzione del dispositivo.
3.  Output di Log di Debug di Android.


<a name="Diagnostic_MSBuild_Output" />

## <a name="diagnostic-msbuild-output"></a>Diagnostica Output di MSBuild

MSBuild diagnostica può contenere informazioni aggiuntive relative alla creazione del pacchetto e può contenere alcune informazioni sulla distribuzione del pacchetto.

Per abilitare l'output di MSBuild di diagnostica in Visual Studio:

1.  Fare clic su **strumenti > Opzioni...**
2.  Nella visualizzazione albero a sinistra, selezionare **progetti e soluzioni > compilare ed eseguire**
3.  Nel Pannello di destra, impostare l'elenco a discesa del livello di dettaglio di MSBuild compilazione output della diagnostica
4.  Fare clic su **OK**
5.  Pulire e ricompilare il pacchetto.
6.  Output di diagnostica è visibile all'interno del Pannello di Output.


Per abilitare la diagnostica output di MSBuild in Visual Studio per Mac/OS x:

1.  Fare clic su **Visual Studio per Mac > Preferenze...**
2.  Nella visualizzazione albero a sinistra, selezionare **progetti > compilare**
3.  Nel Pannello di destra, impostare il livello di dettaglio del Log elenco a discesa della diagnostica
4.  Fare clic su **OK**
5.  Riavviare Visual Studio per Mac
6.  Pulire e ricompilare il pacchetto.
7.  Output di diagnostica è visibile all'interno del riquadro di errori (**Vista > Pad > errori** ), fare clic sul pulsante di Output di compilazione.




## <a name="device-deployment-logs"></a>Registri di distribuzione del dispositivo

Per abilitare la registrazione di distribuzione dispositivo all'interno di Visual Studio:

1.  **Strumenti > Opzioni...**>
2.  Nella visualizzazione albero a sinistra, selezionare **Xamarin > Impostazioni Android**
3.  Nel riquadro a destra, abilitare [X] **la registrazione di debug di estensione (scritture monodroid.log sul desktop)** casella di controllo.
4.  I messaggi di log vengono scritti nel file monodroid.log sul desktop.


Visual Studio per Mac scrive sempre i registri di distribuzione del dispositivo. Ricerca è leggermente più difficile; un *AndroidUtils* viene creato il file di log per ogni giorno + una volta che si verifica una distribuzione, ad esempio: **AndroidTools-2012-10-24_12-35-45.log**.

-  In Windows, i file di log vengono scritti `%LOCALAPPDATA%\XamarinStudio-{VERSION}\Logs`.
-  In OS X, i file di log vengono scritti `$HOME/Library/Logs/XamarinStudio-{VERSION}`.




## <a name="android-debug-log-output"></a>Output del Log di Debug di Android

Android scriverà numero di messaggi per il [Log di Debug Android](~/android/deploy-test/debugging/android-debug-log.md).
Xamarin utilizza le proprietà di sistema Android per controllare la generazione di messaggi aggiuntivi nel log di Debug Android. È possibile impostare proprietà di sistema Android tramite il *setprop* comando all'interno di [Bridge Debug Android (adb)](http://developer.android.com/guide/developing/tools/adb.html):

```shell
adb shell setprop PROPERTY_NAME PROPERTY_VALUE
```

Le proprietà di sistema vengono lette durante l'avvio del processo e pertanto devono essere uno dei due set prima che l'applicazione viene avviata o l'applicazione debba essere riavviato dopo la modifica delle proprietà del sistema.



### <a name="xamarinandroid-system-properties"></a>Proprietà di sistema di Xamarin.Android

Xamarin supporta le proprietà di sistema seguenti:

-   *debug.mono.Debug*: se una stringa non vuota, equivale a `*mono-debug*`.

-   *debug.mono.env*: separate da pipe ('*|*') dell'elenco di variabili di ambiente per esportare durante l'avvio dell'applicazione, *prima* mono è stata inizializzata. Ciò consente di impostare le variabili di ambiente registrazione controllo mono.

    - *Nota*: poiché il valore è '*|*'-separate, il valore deve avere un livello aggiuntivo di racchiudere tra virgolette, come il \` *adb shell* \` comando rimuoverà un set di virgolette.

    - *Nota*: i valori delle proprietà di sistema Android non possono contenere più caratteri 92.

    - Esempio:

            adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"

-   *debug.mono.log*: separati da virgola ('*,*') dell'elenco di componenti che deve visualizzare altri messaggi nel Registro di Debug Android. Per impostazione predefinita, viene effettuata alcuna impostazione. I componenti includono:

    -   *tutti*: tutti i messaggi di stampa
    -   *GC*: i messaggi correlati alla stampa GC.
    -   *gref*: visualizza i messaggi di allocazione e deallocazione di riferimento (debole, globale).
    -   *lref*: visualizza i messaggi di allocazione e deallocazione di riferimento locale.

    *Nota*: si tratta di *estremamente* dettagliato. Non abilitare a meno che non è effettivamente necessario.

-   *debug.mono.Trace*: consente di impostare il [mono - traccia](http://docs.go-mono.com/?link=man%3amono(1)) `=PROPERTY_VALUE` impostazione.



## <a name="xamarinandroid-cannot-resolve-systemvaluetuple"></a>Xamarin non è possibile risolvere System.ValueTuple

Questo errore si verifica a causa di un problema di incompatibilità con Visual Studio.

- **2017 di Visual Studio Update 1** (versione 15.1 o precedente) è compatibile solo con il **System.ValueTuple NuGet 4.3.0** (o versioni precedenti).

- **Aggiornamento 2 di Visual Studio 2017** (versione più recente o 15.2) è compatibile solo con il **System.ValueTuple NuGet 4.3.1** (o versione successiva).

Scegliere il corretto System.ValueTuple NuGet corrispondente con l'installazione di Visual Studio 2017.


## <a name="gc-messages"></a>Messaggi di Garbage Collection

Messaggi componente Garbage Collection possono essere visualizzati impostando la proprietà di sistema debug.mono.log su un valore che contiene i cataloghi globali.

Ogni volta che il Garbage Collector esegue e fornisce informazioni sulla quantità di lavoro GC ha, vengono generati messaggi di Garbage Collection:

```shell
I/monodroid-gc(12331): GC cleanup summary: 81 objects tested - resurrecting 21.
```

Informazioni aggiuntive di catalogo globale, ad esempio informazioni di intervallo possono essere generate mediante l'impostazione di `MONO_LOG_LEVEL` variabile di ambiente `debug`:

```shell
adb shell setprop debug.mono.env MONO_LOG_LEVEL=debug
```

Questo comporterà (moltissimi) Mono messaggi aggiuntivi, inclusi questi tre delle conseguenze:

```shell
D/Mono (15723): GC_BRIDGE num-objects 1 num_hash_entries 81226 sccs size 81223 init 0.00ms df1 285.36ms sort 38.56ms dfs2 50.04ms setup-cb 9.95ms free-data 106.54ms user-cb 20.12ms clenanup 0.05ms links 5523436/5523436/5523096/1 dfs passes 1104 6883/11046605
D/Mono (15723): GC_MINOR: (Nursery full) pause 2.01ms, total 287.45ms, bridge 225.60 promoted 0K major 325184K los 1816K
D/Mono ( 2073): GC_MAJOR: (user request) pause 2.17ms, total 2.47ms, bridge 28.77 major 576K/576K los 0K/16K
```

Nel `GC_BRIDGE` messaggio, `num-objects` è il numero di oggetti ponte di questo passaggio si sta occupando, e `num_hash_entries` è il numero di oggetti elaborati durante la chiamata di codice il bridge.

Nel `GC_MINOR` e `GC_MAJOR` messaggi, `total` è la quantità di tempo durante la sospensione di tutto il mondo (non l'esecuzione di thread), mentre `bridge` è la quantità di tempo impiegato nel bridge di elaborazione di codice (che si occupa la macchina virtuale Java). Il mondo *non* sospesa durante l'elaborazione di bridge.

 *In generale*, maggiore è il valore di `num_hash_entries`, il più tempo, che il `bridge` avranno raccolte e il più elevato di `total` tempo impiegato per la raccolta sarà.



## <a name="global-reference-messages"></a>Messaggi di riferimento globale

Per abilitare riferimento globale loggig (GREF), registrazione di *debug.mono.log* deve contenere proprietà di sistema *gref*, ad esempio:

```shell
adb shell setprop debug.mono.log gref
```

Xamarin Usa Android riferimenti globali per fornire i mapping tra le istanze di Java e le istanze gestite associate, come quando si richiama un metodo Java che un'istanza di Java deve essere fornito per Java.

Sfortunatamente, gli emulatori Android consentono solo 2000 riferimenti globale esistente in una fase. Hardware ha un limite di dimensioni molto maggiore di 52000 riferimenti globali. Il limite inferiore può essere problematico quando l'esecuzione di applicazioni nell'emulatore, quindi sapere *dove* istanza provenienza può rivelarsi molto utile.

 *Nota*: il conteggio dei riferimenti globale è interno al xamarin e non (e non può essere) includono riferimenti globali adottati altre librerie native caricati nel processo. Utilizzare il conteggio dei riferimenti globale come una stima.

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

Esistono quattro messaggi di conseguenza:

-  Creazione di riferimento globale: queste sono le righe che iniziano con *+ g +* e fornirà una traccia dello stack per il percorso del codice di creazione.
-  Distruzione di riferimento globale: queste sono le righe che iniziano con *- g-* , può fornire una traccia dello stack per il percorso del codice eliminazione del riferimento globale. Se il Garbage Collector in fase di eliminazione del gref, non verrà fornito alcun dello stack.
-  Weak globali fanno riferimento a creazione: queste sono le righe che iniziano con *+ w +* .
-  Distruzione fanno riferimento a "Weak" globali: si tratta di righe che iniziano con *-w-* .


In tutti i messaggi, il *grefc* valore è il conteggio dei riferimenti globali creato xamarin, mentre il *grefwc* valore è il conteggio dei riferimenti deboli di globali creati xamarin. Il *gestire* o *obj handle* valore è il valore dell'handle JNI e il carattere dopo la '  */* ' è il tipo del valore dell'handle: */L* per riferimento locale, */G* per i riferimenti, globali e */W* per i riferimenti deboli globali.

Come parte del processo di Garbage Collection, i riferimenti globali (+ + g) vengono convertite in riferimenti globali deboli (causando + w + e - g-), un catalogo globale lato Java viene avviato e quindi il riferimento debole globale viene controllato per verificare se sono state raccolte. Se è ancora attivo, un nuovo gref viene creato per il riferimento debole (c +, -w + -), in caso contrario viene eliminato il riferimento debole (-w).

## <a name="java-instance-is-created-and-wrapped-by-a-mcw"></a>Istanza di Java viene creato e sottoposto a wrapping da un MCW

```shell
I/monodroid-gref(27679): +g+ grefc 2211 gwrefc 0 obj-handle 0x4066df10/L -> new-handle 0x4066df10/L from ...
I/monodroid-gref(27679): handle 0x4066df10; key_handle 0x4066df10: Java Type: `android/graphics/drawable/TransitionDrawable`; MCW type: `Android.Graphics.Drawables.TransitionDrawable`
```

## <a name="a-gc-is-being-performed"></a>Un catalogo globale è in corso...

```shell
I/monodroid-gref(27679): +w+ grefc 1953 gwrefc 259 obj-handle 0x4066df10/G -> new-handle 0xde68f95f/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -g- grefc 1952 gwrefc 259 handle 0x4066df10/G from take_weak_global_ref_jni
```

## <a name="object-is-still-alive-as-handle--null"></a>Oggetto è ancora attivo, come handle! = null
## <a name="wref-turned-back-into-a-gref"></a>diventato un gref wref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x4066df10
I/monodroid-gref(27679): +g+ grefc 1930 gwrefc 39 obj-handle 0xde68f95f/W -> new-handle 0x4066df10/G from take_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1930 gwrefc 38 handle 0xde68f95f/W from take_global_ref_jni
```

## <a name="object-is-dead-as-handle--null"></a>Oggetto è inattivo, come handle = = null
## <a name="wref-is-freed-no-new-gref-created"></a>wref viene liberato, non nuovo gref creato

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x0
I/monodroid-gref(27679): -w- grefc 1914 gwrefc 296 handle 0xde68f95f/W from take_global_ref_jni
```

È presente una ruga "interessante" qui: sulle destinazioni che eseguono Android precedenti alla 4.0, il valore di gref è uguale all'indirizzo dell'oggetto Java nella memoria del runtime Android. (, Ovvero il catalogo globale è un in movimento, moderata, agente di raccolta e è passando i riferimenti diretti a tali oggetti.) Dopo un + + g, + w +, - g-, + g +, - w-sequenza, il gref risultante avrà lo stesso valore come valore gref originale. In questo modo piuttosto semplice grepping tramite registri.

Android 4.0, tuttavia, ha un agente di raccolta mobile e non assegna gli riferimenti diretti al runtime Android gli oggetti macchina virtuale. Di conseguenza, dopo un + + g, + w +, - g-, + g +, - w sequenza, il valore gref *saranno diversi*. Se l'oggetto viene conservata più cataloghi globali, quindi passerà da diversi valori gref, rendendone più difficile determinare in un'istanza è stata effettivamente allocata da.

### <a name="querying-programmatically"></a>L'esecuzione di query a livello di codice

È possibile eseguire query conteggi GREF sia WREF eseguendo una query di `JniRuntime` oggetto.

`Java.Interop.JniRuntime.CurrentRuntime.GlobalReferenceCount` -Conteggio dei riferimenti globale

`Java.Interop.JniRuntime.CurrentRuntime.WeakGlobalReferenceCount` -Conteggio dei riferimenti debole



## <a name="offline-activation"></a>Attivazione non in linea

Se si è grado di attivare xamarin in Windows, o non è possibile installare la versione completa di xamarin su Mac OS X, consultare il [Offline attivazione](~/android/get-started/installation/index.md) pagina.



## <a name="cant-upgrade-to-indiebusiness-from-trial-account"></a>Non è possibile eseguire l'aggiornamento a immaginare/Business dall'Account di valutazione

Se si recentemente acquistato xamarin e avviato in precedenza una versione di valutazione di xamarin, si potrebbe essere necessario completare i passaggi seguenti per ottenere questa modifica licenza prelevata da Visual Studio per Mac o Visual Studio.

-  Chiudere Visual Studio per Mac e Visual Studio
-  Rimuovere tutti i file da ~/Library/MonoAndroid su Mac o %PROGRAMDATA%\Mono Android\License\ per Windows
-  Riaprire Visual Studio per Mac e Visual Studio e compila un progetto xamarin


Questo dovrebbe iniziare attivo e in esecuzione. Se si continua ad avere problemi, si desidera provare un [Offline attivazione](~/android/get-started/installation/index.md) per completare l'attivazione della workstation.



## <a name="receiving-activation-incomplete-error-message"></a>Ricezione di ' messaggio di errore incompleto di attivazione

Questo problema può verificarsi quando si usa xamarin per Visual Studio. Per risolvere questo problema, inviare i log dal seguente percorso per  *contact@xamarin.com* .

-  Percorso file di registro: **% LocalAppData %\\Xamarin\\log**




## <a name="receiving-error-retrieving-update-information-error-message"></a>Ricezione di messaggi di errore 'Errore di recupero delle informazioni di aggiornamento'

A volte, un aggiornamento avrà esito negativo con l'errore seguente, che spesso si verificano durante il controllo degli aggiornamenti:

La maggior parte dei casi, questo errore può essere risolto semplicemente effettuando l'accesso senza accesso all'account Xamarin e quindi nuovamente la registrazione.

A tale scopo, individuare la piattaforma di seguito e seguire i passaggi:

**In Mac:**
1. Aprire Visual Studio per Mac
2. Selezionare Visual Studio per Mac > Account...
3. Fare clic su disconnessione
4. Fare clic su Accedi
5. Immettere le credenziali
6. Controlla aggiornamenti

**Nel PC usando Visual Studio:**
1. Aprire Visual Studio
2. Selezionare Strumenti > Account Xamarin
3. Fare clic su disconnessione
4. Fare clic su Accedi
5. Immettere le credenziali
6. Controlla aggiornamenti

Questo messaggio di errore viene nuovamente visualizzato, il messaggio di posta elettronica  **contact@xamarin.com** .




## <a name="android-debug-logs"></a>Registri di Debug di Android

Il [i log di Debug Android](~/android/deploy-test/debugging/android-debug-log.md) può fornire contesto aggiuntivo riguardanti gli errori di runtime viene visualizzato.



## <a name="floating-point-performance-is-terrible"></a>Le prestazioni a virgola mobile sono terribili!

In alternativa, "My app esegue 10 volte più veloce con la build di Debug più con la build di rilascio!"

Xamarin supporta più dispositivi ABIs: *armeabi*, *armeabi v7a*, e *x86*. È possibile specificare ABIs dispositivo all'interno di **le proprietà del progetto > scheda applicazione > architetture supportate**.

Le compilazioni di debug usano un pacchetto Android che fornisce tutte ABIs e pertanto verrà utilizzato l'ABI più veloce per il dispositivo di destinazione.

Build di rilascio includerà solo i ABIs selezionata nella scheda delle proprietà del progetto. È possibile selezionare più di uno.

*armeabi* è il valore predefinito ABI ed è il più ampio supporto dei dispositivi. *Tuttavia*, armeabi non supportano i dispositivi con più CPU e l'hardware a virgola mobile, amont altre operazioni. Di conseguenza, le app tramite il runtime versione armeabi verranno associate a un singolo core e utilizzerà un'implementazione di soft-float. Entrambi questi può contribuire a in modo significativo calo delle prestazioni per l'app.

Se l'app richiede ragionevole prestazioni a virgola mobile (ad esempio, giochi), è necessario abilitare il *armeabi v7a* ABI. È possibile supportare solo il *armeabi v7a* runtime, anche se ciò significa che dispositivi meno recenti che supportano solo *armeabi* sarà in grado di eseguire l'app.



## <a name="could-not-locate-android-sdk"></a>Non è stato possibile individuare Android SDK

Sono disponibili 2 Scarica da Google Android SDK per Windows.
Se si sceglie l'installazione di .exe, verranno scritte le chiavi del Registro di sistema che indicano xamarin in cui è stato installato. Se si sceglie il file zip e decomprimerlo manualmente, xamarin non conosce la posizione in cui cercare il SDK. È possibile indicare in cui il SDK è in Visual Studio, passare a xamarin **strumenti > Opzioni > Xamarin > Impostazioni Android**:

[![Percorso di Android SDK nelle impostazioni Android di Xamarin](troubleshooting-images/01a.png)](troubleshooting-images/01a.png#lightbox)



## <a name="ide-does-not-display-target-device"></a>Dispositivo di destinazione non viene visualizza

In alcuni casi, si tenterà di distribuire l'applicazione in un dispositivo, ma il dispositivo a cui che si desidera distribuire in non viene visualizzata nella finestra di dialogo Seleziona dispositivo. Questa situazione può verificarsi quando il Bridge Debug Android decide di andare in vacanza.

Per diagnosticare questo problema, trovare il [programma adb](~/android/deploy-test/debugging/android-debug-log.md), quindi eseguire:

```shell
adb devices
```

Se il dispositivo non è presente, è necessario riavviare il server di Bridge Debug Android in modo tale da poter trovare il dispositivo:

```shell
adb kill-server
adb start-server
```

Software HTC sincronizzazione potrebbe non essere possibile **adb. start-server** non funziona correttamente. Se il **adb. start-server** non stampa la porta viene avviato nel comando, chiudere il software di sincronizzazione HTC e provare a riavviare il server adb.


## <a name="the-specified-task-executable-keytool-could-not-be-run"></a>Non è stato eseguito l'eseguibile di attività specificata "keytool"

Ciò significa che il percorso non contiene la directory in cui si trova nella directory bin del SDK di Java. Verificare che siano seguite questi passaggi dal [installazione](~/android/get-started/installation/index.md) Guida.


## <a name="monodroidexe-or-aresgenexe-exited-with-code-1"></a>monodroid.exe o aresgen.exe terminato con codice 1

Per eseguire il debug di questo problema, passare a Visual Studio e modificare il livello di dettaglio di MSBuild, a tale scopo, selezionare: **strumenti > Opzioni > progetto** e **soluzioni > compilare** e **eseguire > Dettaglio di Output di compilazione progetto MSBuild** e impostare questo valore su **normale**.

Ricompilare e controllare il riquadro Output di Visual Studio, che deve contenere l'errore completo.

## <a name="there-is-not-enough-storage-space-on-the-device-to-deploy-the-package"></a>Non c'è spazio sufficiente sul dispositivo per distribuire il pacchetto

Questo errore si verifica quando non si avvia l'emulatore da Visual Studio. Quando si avvia l'emulatore all'esterno di Visual Studio, è necessario passare il `-partition-size 512` opzioni, ad esempio,

```shell
emulator -partition-size 512 -avd MonoDroid
```

Accertarsi di utilizzare il nome corretto simulatore, ad esempio [il nome usato quando si configura il simulatore](~/android/get-started/installation/windows.md#device).


## <a name="installfailedinvalidapk-when-installing-a-package"></a>INSTALLARE\_FAILED\_valido\_APK quando si installa un pacchetto

I nomi di pacchetto Android *deve* contiene un punto ('*.*'). Modificare il nome del pacchetto in modo che contenga un periodo.

-   All'interno di Visual Studio:
    -   Fare clic con il pulsante destro del progetto > proprietà
    -   Fare clic sulla scheda manifesto Android a sinistra.
    -   Aggiornare il campo del nome del pacchetto.
        -   Se viene visualizzato il messaggio &ldquo;AndroidManifest.xml non trovato. Fare clic per aggiungerne uno. &rdquo;, fare clic sul collegamento e quindi aggiornare il campo del nome del pacchetto.
-   All'interno di Visual Studio per Mac:
    -   Fare clic con il pulsante destro del progetto > Opzioni.
    -   Passare alla compilazione / sezione applicazione Android.
    -   Modificare il campo Nome pacchetto includa un '.'.




## <a name="installfailedmissingsharedlibrary-when-installing-a-package"></a>INSTALLARE\_FAILED\_mancante\_SHARED\_libreria quando si installa un pacchetto

Una libreria"condivisa" in questo contesto è *non* una libreria condivisa nativa (*libfoo.so*) del file; è invece una libreria che deve essere installata separatamente nel dispositivo di destinazione, ad esempio Google Map.

Il pacchetto Android specifica quali librerie condivise sono necessarie con il `<uses-library/>` elemento. Se un *obbligatorio* libreria non è presente nel dispositivo di destinazione (ad esempio `//uses-library/@android:required` è *true*, ovvero l'impostazione predefinita), l'installazione del pacchetto avrà esito negativo con *installare\_ Non è stato possibile\_mancante\_SHARED\_libreria*.

Per determinare quali librerie condivise sono necessarie, è possibile visualizzare il *generato*
**AndroidManifest.xml** file (ad esempio **obj\\Debug\\android \\AndroidManifest.xml**) e cercare il `<uses-library/>` elementi. `<uses-library/>` è possibile aggiungere manualmente elementi del progetto **proprietà\\AndroidManifest.xml** file e tramite il [attributo personalizzato UsesLibraryAttribute](https://developer.xamarin.com/api/type/Android.App.UsesLibraryAttribute/).

Ad esempio, aggiungendo un riferimento all'assembly *Mono.Android.GoogleMaps.dll* verrà aggiunto in modo implicito un `<uses-library/>` per la libreria condivisa di mappe di Google.



## <a name="installfailedupdateincompatible-when-installing-a-package"></a>INSTALLARE\_FAILED\_aggiornamento\_incompatibile durante l'installazione di un pacchetto

Pacchetti Android dispongono di tre requisiti:

-   È necessario che contengano un '.' (vedere voce precedente)
-   Devono avere un nome di pacchetto stringa univoca (pertanto la convenzione inversa tld visualizzata nei nomi di app Android, ad esempio com.android.chrome per l'app Chrome)
-   Durante l'aggiornamento di pacchetti, il pacchetto deve avere la stessa chiave di firma.

Di conseguenza, scenario di esempio:

1.  È compilare e distribuire l'app come un'applicazione di Debug
2.  Si modifica la chiave di firma, ad esempio, per utilizzare come un'app di versione (o perché non si desidera predefinito fornito dal Debug di chiave di firma)
3.  Installare l'app senza prima rimuovere, ad esempio, eseguire il Debug > Avvia senza eseguire debug in Visual Studio


In questo caso, installazione del pacchetto avrà esito negativo con un'installazione\_FAILED\_aggiornamento\_errore incompatibili, perché il nome del pacchetto non è stato modificato durante la firma con chiave ha. Il [Log di Debug Android](~/android/deploy-test/debugging/android-debug-log.md) conterrà anche un messaggio simile a:

```shell
E/PackageManager(  146): Package [PackageName] signatures do not match the previously installed version; ignoring!
```

Per correggere l'errore, rimuovere completamente l'applicazione dal dispositivo prima di installare nuovamente.


## <a name="installfaileduidchanged-when-installing-a-package"></a>INSTALLARE\_FAILED\_UID\_modificato quando si installa un pacchetto

Quando si installa un pacchetto Android, viene assegnato un *id utente* (UID).
*A volte*, per motivi sconosciuti attualmente, durante l'installazione di un'app già installata, l'installazione avrà esito negativo con `INSTALL_FAILED_UID_CHANGED`:

```shell
ERROR [2015-03-23 11:19:01Z]: ANDROID: Deployment failed
Mono.AndroidTools.InstallFailedException: Failure [INSTALL_FAILED_UID_CHANGED]
   at Mono.AndroidTools.Internal.AdbOutputParsing.CheckInstallSuccess(String output, String packageName)
   at Mono.AndroidTools.AndroidDevice.<>c__DisplayClass2c.<InstallPackage>b__2b(Task`1 t)
   at System.Threading.Tasks.ContinuationTaskFromResultTask`1.InnerInvoke()
   at System.Threading.Tasks.Task.Execute()
```

Per risolvere questo problema, *disinstallare completamente* pacchetto Android sia installato l'app dall'interfaccia utente del database di destinazione Android o utilizzando `adb`:

```shell
$ adb uninstall @PACKAGE_NAME@
```

**NON utilizzare** `adb uninstall -k`, come in questo caso *mantenere* dati delle applicazioni, mantenendo pertanto UID in conflitto nel dispositivo di destinazione.



## <a name="release-apps-fail-to-launch-on-device"></a>Impossibile avviare il dispositivo versione App

L'output del Log di Debug Android conterrà un messaggio simile a:

```shell
D/AndroidRuntime( 1710): Shutting down VM
W/dalvikvm( 1710): threadid=1: thread exiting with uncaught exception (group=0xb412f180)
E/AndroidRuntime( 1710): FATAL EXCEPTION: main
E/AndroidRuntime( 1710): java.lang.UnsatisfiedLinkError: Couldn't load monodroid: findLibrary returned null
E/AndroidRuntime( 1710):        at java.lang.Runtime.loadLibrary(Runtime.java:365)
```

In questo caso, esistono due motivi possibili:

1.  Il file con estensione apk non fornisce un'interfaccia ABI che supporta il dispositivo di destinazione.
    Ad esempio, il file con estensione apk contiene solo i file binari armeabi v7a, e il dispositivo di destinazione supporta solo armeabi.

2.  Un [bug Android](http://code.google.com/p/android/issues/detail?id=21670). In questo caso, disinstallare l'applicazione, tra le dita e reinstallare l'applicazione.

Per correggere (1), modificare le opzioni o proprietà del progetto e [aggiungere il supporto per l'ABI necessaria all'elenco di supportato ABIs](~/android/app-fundamentals/cpu-architectures.md). Per determinare quali ABI è necessario aggiungere, eseguire il seguente comando di adb per dispositivo di destinazione:

```shell
adb shell getprop ro.product.cpu.abi
adb shell getprop ro.product.cpu.abi2
```

L'output conterrà il database primario (e secondari facoltativi) ABIs.

```shell
$ adb shell getprop | grep ro.product.cpu
[ro.product.cpu.abi2]: [armeabi]
[ro.product.cpu.abi]: [armeabi-v7a]
```

## <a name="the-outpath-property-is-not-set-for-project-ldquomyappcsprojrdquo"></a>Per il progetto non è impostata la proprietà OutPath &ldquo;MyApp.csproj&rdquo;

Ciò significa in genere che la variabile di ambiente e un computer di HP &ldquo;piattaforma&rdquo; è stata impostata su un elemento come MCD o HPD. È in conflitto con la proprietà piattaforma MSBuild che in genere è impostata su &ldquo;qualsiasi CPU&rdquo; o &ldquo;x86&rdquo;. È necessario rimuovere questa variabile di ambiente dal computer prima di MSBuild in grado di funzionare:

-   Pannello di controllo > sistema > Avanzate > variabili di ambiente

Riavviare Visual Studio o Visual Studio per Mac e provare a ricompilare. Operazioni dovrebbero ora funzionano come previsto.

## <a name="javalangclasscastexception-monoandroidruntimejavaobject-cannot-be-cast-to"></a>eccezione java.lang.ClassCastException: Impossibile eseguire il cast di mono.android.runtime.JavaObject per...

Xamarin 4. x non correttamente il marshalling di tipi generici annidati correttamente. Si consideri ad esempio C seguenti\# codice che usa [SimpleExpandableListAdapter](https://developer.xamarin.com/api/type/Android.Widget.SimpleExpandableListAdapter/):


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


Il problema è che xamarin erroneamente effettua il marshalling di tipi generici annidati. Il `List<IDictionary<string, object>>` cui effettuare il marshalling di un [java.lang.ArrrayList](https://developer.xamarin.com/api/type/Java.Util.ArrayList/), ma la `ArrayList` è contenente `mono.android.runtime.JavaObject` istanze (quale riferimento di `Dictionary<string, object>` istanze) anziché un elemento che implementa [java.util.Map](https://developer.xamarin.com/api/type/Java.Util.IMap/), con conseguente l'eccezione seguente:

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

La soluzione alternativa consiste nell'usare l'oggetto fornito [tipi di raccolta Java](~/android/internals/api-design.md) anziché il `System.Collections.Generic` tipi per il &ldquo;interna&rdquo; tipi. Questo comporterà tipi Java appropriati quando le istanze di marshalling. (Il codice seguente è più complicato necessarie per ridurre la durata gref. Può essere semplificata per modificare il codice originale tramite `s/List/JavaList/g` e `s/Dictionary/JavaDictionary/g` se durate gref non sono un problema.)

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

[Questo problema verrà risolto in una versione futura](https://bugzilla.xamarin.com/show_bug.cgi?id=5401).


## <a name="unexpected-nullreferenceexceptions"></a>Eccezioni NullReferenceException imprevisto

In alcuni casi il [Log di Debug Android](~/android/deploy-test/debugging/android-debug-log.md) verrà menzionato NullReferenceException che &ldquo;non può verificarsi,&rdquo; o provenire da Mono per il codice di runtime Android poco prima l'app sia deceduto senza:

```shell
E/mono(15202): Unhandled Exception: System.NullReferenceException: Object reference not set to an instance of an object
E/mono(15202):   at Java.Lang.Object.GetObject (IntPtr handle, System.Type type, Boolean owned)
E/mono(15202):   at Java.Lang.Object._GetObject[IOnTouchListener] (IntPtr handle, Boolean owned)
E/mono(15202):   at Java.Lang.Object.GetObject[IOnTouchListener] (IntPtr handle, Boolean owned)
E/mono(15202):   at Android.Views.View+IOnTouchListenerAdapter.n_OnTouch_Landroid_view_View_Landroid_view_MotionEvent_(IntPtr jnienv, IntPtr native__this, IntPtr native_v, IntPtr native_e)
E/mono(15202):   at (wrapper dynamic-method) object:b039cbb0-15e9-4f47-87ce-442060701362 (intptr,intptr,intptr,intptr)
```

oppure

```shell
E/mono    ( 4176): Unhandled Exception:
E/mono    ( 4176): System.NullReferenceException: Object reference not set to an instance of an object
E/mono    ( 4176): at Android.Runtime.JNIEnv.NewString (string)
E/mono    ( 4176): at Android.Util.Log.Info (string,string)
```

Questa situazione può verificarsi quando il runtime Android decide di interrompere il processo, che può verificarsi per diversi motivi, tra cui raggiungere il limite GREF del database di destinazione o eseguendo un'operazione &ldquo;errato&rdquo; con JNI.

Per determinare se questo è il caso, controllare il Log di Debug Android un messaggio dal processo di simile a:

```shell
E/dalvikvm(  123): VM aborting
```


## <a name="abort-due-to-global-reference-exhaustion"></a>Interrompere dovuti all'esaurimento di riferimento globale

Livello JNI di Android runtime supporta solo un numero limitato di riferimenti a oggetti JNI sia valido in qualsiasi punto nel tempo. Se questo limite viene superato, interruzione di operazioni.

Il GREF (*riferimento globale*) limite è di 2000 riferimenti nell'emulatore e fa riferimento a ~ 52000 su hardware.

Si conosce che si inizia a creare un numero eccessivo di GREFs quando vengono visualizzati messaggi simile al seguente nel Registro di Debug Android:

```shell
D/dalvikvm(  602): GREF has increased to 1801
```

Quando si raggiunge il limite GREF, viene visualizzato un messaggio, ad esempio le operazioni seguenti:

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


Nell'esempio precedente (che, tra l'altro, proviene da [bug 685215](https://bugzilla.novell.com/show_bug.cgi?id=685215)) il problema è che un numero eccessivo di Android.Graphics.Point vengono create istanze, vedere [commento \#2](https://bugzilla.novell.com/show_bug.cgi?id=685215#c2) per un elenco di correzioni per Questo errore specifico.

In genere, è di una soluzione utile per trovare il tipo ha troppe istanze allocata &ndash; Android.Graphics.Point nel dump precedente &ndash; quindi trovare in cui vengono creati nel codice sorgente ed eliminarli in modo appropriato (in modo che i relativi Durata Java-Object viene accorciata). Ciò non è sempre appropriato (\#685215 è multithreading, in modo semplice soluzione evita la chiamata Dispose), ma è la prima cosa da prendere in considerazione.

È possibile abilitare [GREF registrazione](~/android/troubleshooting/index.md) per vedere quando vengono creati i GREFs e quanti esiste.


## <a name="abort-due-to-jni-type-mismatch"></a>Interruzione derivante da una mancata corrispondenza tra tipi JNI

Se si mano-roll codice JNI, è possibile che i tipi non corrispondono correttamente, al ad esempio, se si prova a richiamare `java.lang.Runnable.run` su un tipo che non implementa `java.lang.Runnable`. In questo caso, vi sarà un messaggio simile al seguente nel Registro di Debug Android:

```shell
W/dalvikvm( 123): JNI WARNING: can't call Ljava/Type;;.method on instance of Lanother/java/Type;
W/dalvikvm( 123):              in Lmono/java/lang/RunnableImplementor;.n_run:()V (CallVoidMethodA)
...
E/dalvikvm( 123): VM aborting
```

## <a name="dynamic-code-support"></a>Supporto di codice dinamico

### <a name="dynamic-code-does-not-compile"></a>Codice dinamico non viene compilato.

Per utilizzare C\# dinamica nell'applicazione o libreria, è necessario aggiungere System.Core.dll, Microsoft.CSharp.dll e Mono.CSharp.dll al progetto.

### <a name="in-release-build-missingmethodexception-occurs-for-dynamic-code-at-run-time"></a>Nelle build di rilascio, MissingMethodException si verifica in fase di esecuzione di codice dinamico.

-   È probabile che il progetto di applicazione non ha riferimenti a System.Core.dll, Microsoft.CSharp.dll o Mono.CSharp.dll. Verificare che tali assembly vengono fatto riferimento.

    -   Tenere presente che il codice dinamico sempre i costi. Se è necessario un codice efficiente, considerare l'utilizzo non dinamica del codice.

-   Nella prima anteprima, tali assembly sono stati esclusi tipi in ogni assembly vengono utilizzati in modo esplicito dal codice dell'applicazione. Vedere gli argomenti seguenti per una soluzione alternativa: [http://lists.ximian.com/pipermail/mo...il/009798.html](http://lists.ximian.com/pipermail/monodroid/2012-April/009798.html)


## <a name="projects-built-with-aotllvm-crash-on-x86-devices"></a>I progetti compilati con l'arresto anomalo AOT + LLVM x86 dispositivi

Quando si distribuisce un'app compilata con [AOT + LLVM](~/android/deploy-test/release-prep/index.md) nei dispositivi basati su x86, si può vedere un messaggio di errore di eccezione simile al seguente:

```shell
Assertion: should not be reached at /Users/.../external/mono/mono/mini/tramp-x86.c:124
Fatal signal 6 (SIGABRT), code -6 in tid 4051 (amarin.bug56111)
```

Si tratta di un problema noto come riportato [56111](https://bugzilla.xamarin.com/show_bug.cgi?id=56111). La soluzione alternativa consiste nel disattivare LLVM.
