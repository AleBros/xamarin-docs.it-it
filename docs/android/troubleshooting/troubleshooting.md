---
title: Suggerimenti per la risoluzione dei problemi
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 56137ACA-4811-B312-6860-E16D0FA123F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: b2f11bd09e1b1b3fd7af29a026229494a081ad11
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668556"
---
# <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi


## <a name="getting-diagnostic-information"></a>Recupero di informazioni di diagnostica

Xamarin. Android ha diverse posizioni in cui tenere traccia dei bug varie cercare.
Sono inclusi:

1.  Output di MSBuild di diagnostica.
2.  Log distribuzione del dispositivo.
3.  Output del Log di Debug Android.


<a name="Diagnostic_MSBuild_Output" />

## <a name="diagnostic-msbuild-output"></a>Output di MSBuild di diagnostica

MSBuild di diagnostica può contenere informazioni aggiuntive relative alla creazione di pacchetti e può contenere alcune informazioni sulla distribuzione del pacchetto.

Per abilitare l'output di MSBuild di diagnostica in Visual Studio:

1.  Fare clic su **strumenti > Opzioni...**
2.  Nella visualizzazione albero a sinistra, selezionare **progetti e soluzioni > Compila ed Esegui**
3.  Nel pannello a destra, impostare l'elenco a discesa del livello di dettaglio di MSBuild compilazione output della diagnostica
4.  Fare clic su **OK**
5.  Pulire e ricompilare il pacchetto.
6.  Output di diagnostica è visibile all'interno del Pannello di Output.


Per abilitare la diagnostica output di MSBuild in Visual Studio per Mac/OS x:

1.  Fare clic su **Visual Studio per Mac > Preferenze...**
2.  Nella visualizzazione albero a sinistra, selezionare **progetti > compilare**
3.  Nel pannello a destra, impostare il livello di dettaglio del Log elenco a discesa della diagnostica
4.  Fare clic su **OK**
5.  Riavviare Visual Studio per Mac
6.  Pulire e ricompilare il pacchetto.
7.  Output di diagnostica è visibile nel riquadro degli errori (**Vista > riquadri > errori** ), facendo clic sul pulsante Output di compilazione.




## <a name="device-deployment-logs"></a>Registri di distribuzione del dispositivo

Per abilitare la registrazione del dispositivo distribuzione all'interno di Visual Studio:

1.  **Strumenti > Opzioni...**>
2.  Nella visualizzazione albero a sinistra, selezionare **Xamarin > Impostazioni Android**
3.  Nel pannello a destra, abilitare il [X] **la registrazione debug estensione (scrive monodroid. log sul desktop)** casella di controllo.
4.  I messaggi di log vengono scritti nel file di monodroid. log sul desktop.


Visual Studio per Mac scrive sempre i registri di distribuzione del dispositivo. Ricerca di essi è leggermente più complessa; una *AndroidUtils* file di log viene creato per ogni giorno e ora in cui si verifica una distribuzione, ad esempio: **AndroidTools-2012-10-24_12-35-45.log**.

-  In Windows, i file di log vengono scritti `%LOCALAPPDATA%\XamarinStudio-{VERSION}\Logs`.
-  In OS X, i file di log vengono scritti `$HOME/Library/Logs/XamarinStudio-{VERSION}`.




## <a name="android-debug-log-output"></a>Output del Log di Debug Android

Android scriverà numero di messaggi per il [Log di Debug Android](~/android/deploy-test/debugging/android-debug-log.md).
Xamarin. Android Usa le proprietà di sistema di Android per controllare la generazione di messaggi aggiuntivi nel log di Debug Android. Proprietà di sistema Android può essere impostata tramite il *setprop* comando all'interno di [Android Debug Bridge (adb)](https://developer.android.com/guide/developing/tools/adb.html):

```shell
adb shell setprop PROPERTY_NAME PROPERTY_VALUE
```

Le proprietà di sistema vengono letti durante l'avvio del processo e pertanto devono essere uno dei due set prima che l'applicazione viene avviata o l'applicazione deve essere riavviata se vengono modificate le proprietà di sistema.



### <a name="xamarinandroid-system-properties"></a>Proprietà di sistema di Xamarin.Android

Xamarin. Android supporta le proprietà di sistema seguenti:

-   *debug.mono.debug*: Se una stringa non vuota, ciò equivale a `*mono-debug*`.

-   *debug.mono.env*: Delimitato da barra verticale ('*|*') elenco delle variabili di ambiente durante l'avvio dell'applicazione, esportare *prima* mono è stato inizializzato. Ciò consente di impostare le variabili di ambiente che la registrazione mono di controllo.

    - *Nota*: Poiché il valore è '*|*'-separate, il valore deve avere un livello aggiuntivo di evitarlo, come il \` *shell adb* \` comando rimuoverà un set di virgolette.

    - *Nota*: I valori delle proprietà di sistema di Android possono essere composto 92 caratteri di lunghezza.

    - Esempio:

            adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"

-   *debug.mono.log*: Separati da virgola ('*,*') elenco dei componenti che dovrebbe visualizzare messaggi aggiuntivi nel log di Debug Android. Per impostazione predefinita, viene effettuata alcuna impostazione. I componenti includono:

    -   *tutti i*: Stampare tutti i messaggi
    -   *gc*: Stampare i messaggi relativi a GC.
    -   *gref*: Stampare i messaggi di allocazione e deallocazione di riferimento (vulnerabili e globali).
    -   *lref*: Stampare i messaggi di allocazione e deallocazione di riferimento locale.

    *Nota*: si tratta *estremamente* verbose. Non abilitare a meno che non è strettamente necessario.

-   *debug.mono.trace*: Consente di impostare il [mono - trace](http://docs.go-mono.com/?link=man%3amono(1)) `=PROPERTY_VALUE` impostazione.



## <a name="xamarinandroid-cannot-resolve-systemvaluetuple"></a>Xamarin. Android non riesce a risolvere valuetuple

Questo errore si verifica a causa di un problema di incompatibilità con Visual Studio.

- **Visual Studio 2017 Update 1** (versione 15.1 o versioni precedenti) è compatibile solo con il **System. valuetuple NuGet 4.3.0** (o versioni precedenti).

- **Visual Studio 2017 Update 2** (versione 15.2 o versione successiva) è compatibile solo con il **System. valuetuple NuGet 4.3.1** (o versione successiva).

Scegliere di NuGet valuetuple corretto che corrisponde all'installazione di Visual Studio 2017.


## <a name="gc-messages"></a>Messaggi di Garbage Collection

Messaggi componente Garbage Collection possono essere visualizzati impostando la proprietà di sistema debug.mono.log su un valore che contiene gc.

Vengono generati messaggi di Garbage Collection ogni volta che il Garbage Collector esegue e fornisce informazioni relative alla quantità di lavoro GC ha:

```shell
I/monodroid-gc(12331): GC cleanup summary: 81 objects tested - resurrecting 21.
```

È possibile generare informazioni di catalogo globale aggiuntive, ad esempio le informazioni di temporizzazione impostando il `MONO_LOG_LEVEL` variabile di ambiente `debug`:

```shell
adb shell setprop debug.mono.env MONO_LOG_LEVEL=debug
```

Ciò comporterà (molti) Mono messaggi aggiuntivi, tra cui questi tre delle conseguenze:

```shell
D/Mono (15723): GC_BRIDGE num-objects 1 num_hash_entries 81226 sccs size 81223 init 0.00ms df1 285.36ms sort 38.56ms dfs2 50.04ms setup-cb 9.95ms free-data 106.54ms user-cb 20.12ms clenanup 0.05ms links 5523436/5523436/5523096/1 dfs passes 1104 6883/11046605
D/Mono (15723): GC_MINOR: (Nursery full) pause 2.01ms, total 287.45ms, bridge 225.60 promoted 0K major 325184K los 1816K
D/Mono ( 2073): GC_MAJOR: (user request) pause 2.17ms, total 2.47ms, bridge 28.77 major 576K/576K los 0K/16K
```

Nel `GC_BRIDGE` messaggio `num-objects` è il numero di oggetti ponte sta considerando questo passaggio, e `num_hash_entries` è il numero di oggetti elaborati durante la chiamata del codice di bridge.

Nel `GC_MINOR` e `GC_MAJOR` messaggi, `total` è la quantità di tempo durante tutto il mondo è in pausa (nessun thread di esecuzione), mentre `bridge` è la quantità di tempo impiegato nel bridge di elaborazione di codice (che si occupa della macchina virtuale Java). È di mondo *non* sospesa quando viene eseguita l'elaborazione di bridge.

 *In genere*, maggiore è il valore di `num_hash_entries`, il più tempo, che il `bridge` richiederà le raccolte e il più grande il `total` tempo impiegato per la raccolta sarà.



## <a name="global-reference-messages"></a>Messaggi di riferimento globale

Per abilitare riferimento globale loggig (GREF), la registrazione, il *debug.mono.log* proprietà di sistema deve contenere *gref*, ad esempio:

```shell
adb shell setprop debug.mono.log gref
```

Xamarin. Android Usa i riferimenti globali Android per specificare i mapping tra le istanze di Java e le istanze gestite associate, come quando si richiama un metodo di Java che un'istanza di Java è necessario fornire a Java.

Sfortunatamente, gli emulatori Android consentono solo i riferimenti global 2000 esista in un momento. Hardware ha un limite di dimensioni molto maggiore di 52000 riferimenti globali. Il limite inferiore può risultare problematico quando si eseguono applicazioni nell'emulatore, quindi sapere *in cui* la provenienza del istanza può essere molto utile.

 *Nota*: il conteggio dei riferimenti globali interni a xamarin. Android e non esiste (e possibile) includono riferimenti globali sottoscritti dagli altri librerie native caricati nel processo. Utilizzare il conteggio dei riferimenti globale come una stima.

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

-  Creazione del riferimento di Global: queste sono le righe che iniziano con *+ g +* e offre un'analisi dello stack per il percorso del codice di creazione.
-  Distruzione di riferimento globale: queste sono le righe che iniziano con *- g-* e può fornire un'analisi dello stack per il percorso del codice elimini il riferimento globale. Se il Garbage Collector in fase di eliminazione di gref, Nessuna analisi dello stack viene fornita.
-  La creazione di riferimento "Weak" global: queste sono le righe che iniziano con *+ w +* .
-  Distruzione di riferimento "Weak" global: si tratta di righe che iniziano con *-w-* .


In tutti i messaggi, il *grefc* valore corrisponde al conteggio dei riferimenti globali che xamarin. Android è stato creato, mentre le *grefwc* valore è il conteggio dei riferimenti deboli di globali che ha creato xamarin. Android. Il *gestire* oppure *obj-handle* valore è il valore dell'handle JNI, mentre il carattere successivo il ' */*' è il tipo del valore dell'handle: */L* come riferimento locale, */G* per i riferimenti globali, e */W* per riferimenti deboli globali.

Come parte del processo di Garbage Collection, i riferimenti globali (+ g +) vengono convertite in riferimenti deboli globali (causando un + w + e - g-), viene avviato in un catalogo globale sul lato Java e quindi il riferimento debole globale viene controllato per verificare se sono stati raccolti. Se è ancora attivo, viene creato un nuovo gref tutto il riferimento debole (+ g +, -w-), in caso contrario, viene eliminato il riferimento debole (-w).

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
## <a name="wref-turned-back-into-a-gref"></a>wref trasformato in un gref

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

È presente una ruga "interessante" di seguito: sugli obiettivi che eseguono Android precedenti alla 4.0, il valore di gref è uguale all'indirizzo dell'oggetto Java nella memoria del runtime Android. (Vale a dire, il Garbage Collector è una non cambiano, moderata, agente di raccolta e è ingenuamente riferimenti diretti a tali oggetti.) In questo modo dopo un g + + w +, - g-, + g +, - w-sequenza, di gref risultante avrà lo stesso valore come valore gref originale. In questo modo piuttosto semplice grepping tramite i log.

Android 4.0, tuttavia, ha un agente di raccolta dati in movimento e non incaricata riferimenti diretti al runtime di Android non è più gli oggetti macchina virtuale. Di conseguenza, dopo un g + + w +, - g-, + g +, - w-sequence, il valore di gref *saranno diversi*. Se l'oggetto viene conservata più cataloghi globali, viene visualizzata per diversi valori di gref, rendendo più difficile determinare dove un'istanza è stata effettivamente allocata da.

### <a name="querying-programmatically"></a>L'esecuzione di query a livello di codice

È possibile eseguire query di conteggio GREF sia WREF eseguendo una query di `JniRuntime` oggetto.

`Java.Interop.JniRuntime.CurrentRuntime.GlobalReferenceCount` -Conteggio dei riferimenti globale

`Java.Interop.JniRuntime.CurrentRuntime.WeakGlobalReferenceCount` -Conteggio dei riferimenti debole



## <a name="android-debug-logs"></a>Log di Debug Android

Il [log di Debug Android](~/android/deploy-test/debugging/android-debug-log.md) possono fornire contesto aggiuntive riguardanti eventuali errori di runtime si verificano.



## <a name="floating-point-performance-is-terrible"></a>Le prestazioni a virgola mobile sono terribili!

In alternativa, "l'app viene eseguito 10 volte più veloce con la build di Debug rispetto alla compilazione di rilascio!"

Xamarin. Android supporta dispositivi più interfacce ABI: *armeabi*, *armeabi-v7a*, e *x86*. Dispositivo interfacce ABI possono essere specificati all'interno **proprietà progetto > scheda applicazione > architetture supportate**.

Le compilazioni di debug usano un pacchetto Android fornisce tutte le interfacce ABI e pertanto userà l'interfaccia ABI più veloce per il dispositivo di destinazione.

Le build di versione includerà solo le interfacce ABI selezionati nella scheda delle proprietà di progetto. È possibile selezionare più di uno.

*armeabi* è il valore predefinito ABI e ha il supporto più ampio di dispositivo. *Tuttavia*, armeabi non supportano i dispositivi con più CPU e l'hardware a virgola mobile, amont altre cose. Di conseguenza, le app usando il runtime di rilascio armeabi saranno associate a un singolo core e si intende utilizzare un'implementazione di soft-float. Entrambi questi può contribuire a rallentare in modo significativo sulle prestazioni per l'app.

Se l'app richiede una prestazione soddisfacente a virgola mobile (ad esempio, giochi), è necessario abilitare la *armeabi-v7a* ABI. È possibile solo per supportare le *armeabi-v7a* runtime, anche se ciò significa che i dispositivi meno recenti che supportano solo *armeabi* sarà in grado di eseguire l'app.



## <a name="could-not-locate-android-sdk"></a>Non è possibile individuare Android SDK

Esistono 2 download disponibile da Google per Android SDK per Windows.
Se si sceglie il programma di installazione .exe, scriverà le chiavi del Registro di sistema che indicano a xamarin. Android in cui è stato installato. Se si sceglie il file con estensione zip e decomprimere il file manualmente, xamarin. Android non conosce la posizione in cui cercare il SDK. È possibile indicare dove è il SDK in Visual Studio, passare a xamarin. Android **strumenti > Opzioni > Xamarin > Impostazioni Android**:

[![Percorso di Android SDK nelle impostazioni di Xamarin. Android](troubleshooting-images/01.png)](troubleshooting-images/01.png#lightbox)



## <a name="ide-does-not-display-target-device"></a>IDE non viene visualizzato il dispositivo di destinazione

In alcuni casi si tenterà di distribuire l'applicazione in un dispositivo, ma il dispositivo da distribuire a non viene visualizzata nella finestra di dialogo Seleziona dispositivo. Questa situazione può verificarsi quando si decide di andare in vacanza Android Debug Bridge.

Per diagnosticare questo problema, trovare il [adb programma](~/android/deploy-test/debugging/android-debug-log.md), quindi eseguire:

```shell
adb devices
```

Se il dispositivo non è presente, quindi è necessario riavviare il server di Android Debug Bridge in modo tale da poter trovare il dispositivo:

```shell
adb kill-server
adb start-server
```

Software HTC sincronizzazione potrebbe impedire **start-server adb** non funziona correttamente. Se il **start-server adb** comando non viene stampato porta a cui viene avviato in. Chiudere il software di sincronizzazione HTC e provare a riavviare il server adb.


## <a name="the-specified-task-executable-keytool-could-not-be-run"></a>Non è stato possibile eseguire l'eseguibile dell'attività specificato "keytool"

Ciò significa che il percorso non contiene la directory in cui si trova nella directory bin di Java SDK. Verificare di aver seguito le procedure dal [installazione](~/android/get-started/installation/index.md) Guida.


## <a name="monodroidexe-or-aresgenexe-exited-with-code-1"></a>monodroid.exe o aresgen.exe terminato con codice 1

Che consentono di eseguire il debug di questo problema, passare a Visual Studio e cambiare il livello di dettaglio di MSBuild, a tale scopo, selezionare: **Strumenti > Opzioni > progetto** e **soluzioni > compilare** e **eseguire > dettaglio di Output di compilazione progetto MSBuild** e impostare questo valore su **normale**.

Ricompilare e controllo riquadro di Output di Visual Studio, che deve contenere l'errore completo.

## <a name="there-is-not-enough-storage-space-on-the-device-to-deploy-the-package"></a>Non c'è spazio sufficiente sul dispositivo per distribuire il pacchetto

Ciò si verifica quando non si avvia l'emulatore da Visual Studio. Quando si avvia l'emulatore esternamente a Visual Studio, è necessario passare il `-partition-size 512` opzioni, ad esempio,

```shell
emulator -partition-size 512 -avd MonoDroid
```

Assicurarsi di usare il nome del simulatore corretta, vale a dire [il nome usato durante la configurazione del simulatore](~/android/get-started/installation/windows.md#device).


## <a name="installfailedinvalidapk-when-installing-a-package"></a>INSTALLARE\_FAILED\_valido\_APK quando si installa un pacchetto

I nomi di pacchetto Android *devono* contengono un punto ('*.*'). Modificare il nome del pacchetto in modo che contenga un periodo.

-   Within Visual Studio:
    -   Fare clic con il pulsante destro del progetto > proprietà
    -   Fare clic sulla scheda manifesto Android a sinistra.
    -   Aggiornare il campo del nome del pacchetto.
        -   Se viene visualizzato il messaggio &ldquo;androidmanifest. XML non trovato. Fare clic per aggiungerne uno. &rdquo;, fare clic sul collegamento e quindi aggiornare il campo nome del pacchetto.
-   All'interno di Visual Studio per Mac:
    -   Fare clic con il pulsante destro del progetto > Opzioni.
    -   Passare alla compilazione / sezione applicazione Android.
    -   Modificare il campo Nome pacchetto includa un '.'.




## <a name="installfailedmissingsharedlibrary-when-installing-a-package"></a>INSTALLARE\_FAILED\_MISSING\_condiviso\_libreria quando si installa un pacchetto

Una "libreria condivisa" in questo contesto viene *non* una libreria condivisa nativa (*libfoo.so*) del file; è invece una libreria che deve essere installata separatamente sul dispositivo di destinazione, ad esempio Google Maps.

Il pacchetto Android specifica quali librerie condivise sono necessarie con i `<uses-library/>` elemento. Se un *obbligatorio* libreria non è presente nel dispositivo di destinazione (ad esempio `//uses-library/@android:required` è *true*, ovvero l'impostazione predefinita), l'installazione del pacchetto avrà esito negativo con *installare\_ Non è riuscita\_MISSING\_SHARED\_libreria*.

Per determinare quali librerie condivise sono necessarie, visualizzare il *generati*
**androidmanifest. XML** file (ad esempio **obj\\Debug\\android \\Androidmanifest. XML**) e cercare il `<uses-library/>` elementi. `<uses-library/>` è possibile aggiungere manualmente elementi di progetto **delle proprietà\\androidmanifest. XML** file e tramite la [attributo personalizzato UsesLibraryAttribute](https://developer.xamarin.com/api/type/Android.App.UsesLibraryAttribute/).

Ad esempio, aggiungendo un riferimento all'assembly *Mono.Android.GoogleMaps.dll* verrà aggiunto in modo implicito un `<uses-library/>` per la libreria condivisa di Google Maps.



## <a name="installfailedupdateincompatible-when-installing-a-package"></a>INSTALLARE\_FAILED\_UPDATE\_incompatibili quando si installa un pacchetto

I pacchetti Android presentano tre requisiti:

-   È necessario che contengano un '.' (vedere voce precedente)
-   Devono avere un nome di pacchetto stringa univoca (di conseguenza la convenzione inversa tld visualizzata nei nomi di app per Android, ad esempio com.android.chrome per l'app Chrome)
-   Quando si aggiornano i pacchetti, il pacchetto deve avere la stessa chiave di firma.

In questo modo, si immagini questo scenario:

1.  Si compila e Distribuisci l'app come app di Debug
2.  È modificare la chiave di firma, ad esempio, da usare come un'app di rilascio (o perché non sono quelli predefinito fornito dal Debug chiave di firma)
3.  Si installa l'app senza rimuoverlo prima di tutto, ad esempio, eseguire il Debug > Avvia senza eseguire debug in Visual Studio


In questo caso, installazione del pacchetto avrà esito negativo con un'installazione\_FAILED\_UPDATE\_errore incompatibili, perché il nome del pacchetto non è stato modificato durante la firma con chiave ha. Il [Log di Debug Android](~/android/deploy-test/debugging/android-debug-log.md) conterrà anche un messaggio simile a:

```shell
E/PackageManager(  146): Package [PackageName] signatures do not match the previously installed version; ignoring!
```

Per correggere questo errore, rimuovere completamente l'applicazione dal dispositivo prima di installare nuovamente.


## <a name="installfaileduidchanged-when-installing-a-package"></a>INSTALLARE\_FAILED\_UID\_modificato quando si installa un pacchetto

Quando viene installato un pacchetto Android, viene assegnato un *id utente* (UID).
*In alcuni casi*, per motivi sconosciuti attualmente, quando si installa tramite un'app già installata, l'installazione avrà esito negativo con `INSTALL_FAILED_UID_CHANGED`:

```shell
ERROR [2015-03-23 11:19:01Z]: ANDROID: Deployment failed
Mono.AndroidTools.InstallFailedException: Failure [INSTALL_FAILED_UID_CHANGED]
   at Mono.AndroidTools.Internal.AdbOutputParsing.CheckInstallSuccess(String output, String packageName)
   at Mono.AndroidTools.AndroidDevice.<>c__DisplayClass2c.<InstallPackage>b__2b(Task`1 t)
   at System.Threading.Tasks.ContinuationTaskFromResultTask`1.InnerInvoke()
   at System.Threading.Tasks.Task.Execute()
```

Per risolvere questo problema *disinstallare completamente* il pacchetto Android, installando l'app dall'interfaccia utente grafica di destinazione Android, o tramite `adb`:

```shell
$ adb uninstall @PACKAGE_NAME@
```

**NON utilizzare** `adb uninstall -k`, come in questo caso *mantenere* dati dell'applicazione e quindi conservare l'UID in conflitto nel dispositivo di destinazione.



## <a name="release-apps-fail-to-launch-on-device"></a>Le app di rilascio non avviata nel dispositivo

L'output di Log di Debug Android conterrà un messaggio simile a:

```shell
D/AndroidRuntime( 1710): Shutting down VM
W/dalvikvm( 1710): threadid=1: thread exiting with uncaught exception (group=0xb412f180)
E/AndroidRuntime( 1710): FATAL EXCEPTION: main
E/AndroidRuntime( 1710): java.lang.UnsatisfiedLinkError: Couldn't load monodroid: findLibrary returned null
E/AndroidRuntime( 1710):        at java.lang.Runtime.loadLibrary(Runtime.java:365)
```

In questo caso, esistono due motivi possibili:

1.  Il file con estensione apk non fornisce un'interfaccia ABI che supporta il dispositivo di destinazione.
    Ad esempio, il file con estensione apk contiene solo i file binari armeabi-v7a e il dispositivo di destinazione supporta solo armeabi.

2.  Un' [bug Android](http://code.google.com/p/android/issues/detail?id=21670). In questo caso, disinstallare l'app, tra le dita e reinstallare l'app.

Per risolvere (1), modificare le opzioni/proprietà del progetto e [aggiungere il supporto per l'interfaccia ABI necessaria all'elenco delle interfacce ABI supportate](~/android/app-fundamentals/cpu-architectures.md). Per determinare quale ABI è necessario aggiungere, eseguire il comando adb sul dispositivo di destinazione:

```shell
adb shell getprop ro.product.cpu.abi
adb shell getprop ro.product.cpu.abi2
```

L'output conterrà il database primario e facoltativo secondaria, interfacce ABI.

```shell
$ adb shell getprop | grep ro.product.cpu
[ro.product.cpu.abi2]: [armeabi]
[ro.product.cpu.abi]: [armeabi-v7a]
```

## <a name="the-outpath-property-is-not-set-for-project-ldquomyappcsprojrdquo"></a>Per il progetto non è impostata la proprietà OutPath &ldquo;MyApp. csproj&rdquo;

Ciò significa in genere si dispone di un computer di HP e la variabile di ambiente &ldquo;piattaforma&rdquo; è stata impostata su un valore, ad esempio MCD o HPD. Ciò è in conflitto con la proprietà MSBuild Platform che in genere è impostata su &ldquo;qualsiasi CPU&rdquo; oppure &ldquo;x86&rdquo;. È necessario rimuovere questa variabile di ambiente dal computer prima che MSBuild può funzionare:

-   Pannello di controllo > sistema > Avanzate > variabili di ambiente

Riavviare Visual Studio o Visual Studio per Mac e provare a ricompilare. Le cose dovrebbero ora funzionano come previsto.

## <a name="javalangclasscastexception-monoandroidruntimejavaobject-cannot-be-cast-to"></a>eccezione java.lang.ClassCastException: mono.android.runtime.JavaObject non è possibile eseguire il cast...

Xamarin. Android 4.x non correttamente il marshalling di tipi generici annidati correttamente. Si consideri ad esempio C seguenti\# codice che usa [SimpleExpandableListAdapter](https://developer.xamarin.com/api/type/Android.Widget.SimpleExpandableListAdapter/):


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


Il problema è che xamarin. Android in modo non corretto effettua il marshalling di tipi generici annidati. Il `List<IDictionary<string, object>>` è in corso il marshalling in una [java.lang.ArrrayList](https://developer.xamarin.com/api/type/Java.Util.ArrayList/), ma la `ArrayList` è contenente `mono.android.runtime.JavaObject` istanze (cui riferimento il `Dictionary<string, object>` istanze) anziché un elemento che implementa [java.util.Map](https://developer.xamarin.com/api/type/Java.Util.IMap/), ottenendo l'eccezione seguente:

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

La soluzione alternativa consiste nell'usare l'oggetto fornito [tipi di raccolte di Java](~/android/internals/api-design.md) anziché le `System.Collections.Generic` tipi per il &ldquo;interna&rdquo; tipi. Ciò comporterà i tipi Java appropriati durante il marshalling di varianti. (Il codice seguente è più complicato rispetto al necessario per ridurre la durata di gref. Può essere semplificato alla modifica del codice originale tramite `s/List/JavaList/g` e `s/Dictionary/JavaDictionary/g` se gref durate non è un problema.)

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


## <a name="unexpected-nullreferenceexceptions"></a>Eccezioni NullReferenceException imprevista

In alcuni casi il [Log di Debug Android](~/android/deploy-test/debugging/android-debug-log.md) che indicano che le eccezioni NullReferenceException che &ldquo;non possono essere osservate,&rdquo; o provenire da Mono per il codice di runtime Android poco prima l'app smette di funzionare:

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

Questa situazione può verificarsi quando il runtime di Android decide di interrompere il processo, che può verificarsi per diversi motivi, tra cui raggiunge il limite GREF della destinazione o eseguendo un'operazione simile &ldquo;errata&rdquo; di JNI.

Per determinare se questo è il caso, controllare i Log di Debug Android un messaggio dal processo di simile a:

```shell
E/dalvikvm(  123): VM aborting
```


## <a name="abort-due-to-global-reference-exhaustion"></a>Interrompi dovuti all'esaurimento di riferimento globale

Livello JNI del runtime Android supporta solo un numero limitato di riferimenti a oggetti JNI sia valido in qualsiasi punto nel tempo. Quando questo limite viene superato, separa le cose.

Di GREF (*riferimento globale*) limite è 2000 riferimenti nell'emulatore e fa riferimento a ~ 52000 su hardware.

Sai che si inizia a creare troppi GREFs quando vengono visualizzati messaggi simile al seguente nel Log di Debug Android:

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


Nell'esempio precedente (che, per inciso, proviene [bug 685215](https://bugzilla.novell.com/show_bug.cgi?id=685215)) il problema è che troppi Android.Graphics.Point vengono create istanze, vedere [commento \#2](https://bugzilla.novell.com/show_bug.cgi?id=685215#c2) per un elenco di correzioni per Questo bug particolare.

In genere, una soluzione utile consiste nell'individuare il tipo ha troppe istanze allocata &ndash; Android.Graphics.Point nel dump precedente &ndash; quindi trovare in cui vengono create nel codice sorgente e dispose di essi in modo appropriato (in modo che i Durata Java-Object viene accorciata). Non sempre è appropriato (\#685215 è a thread multipli, in modo semplice soluzione consente di evitare la chiamata a Dispose), ma è la prima cosa da considerare.

È possibile abilitare [GREF registrazione](~/android/troubleshooting/index.md) per vedere quando vengono creati GREFs e quanti esiste.


## <a name="abort-due-to-jni-type-mismatch"></a>Interruzione a causa di mancata corrispondenza tra tipi JNI

Se si parte-esegue il rollup codice JNI, è possibile che i tipi non corrispondono ai correttamente, ad esempio, se si prova a richiamare `java.lang.Runnable.run` su un tipo che non implementa `java.lang.Runnable`. In questo caso, esisterà un messaggio simile al seguente nel Log di Debug Android:

```shell
W/dalvikvm( 123): JNI WARNING: can't call Ljava/Type;;.method on instance of Lanother/java/Type;
W/dalvikvm( 123):              in Lmono/java/lang/RunnableImplementor;.n_run:()V (CallVoidMethodA)
...
E/dalvikvm( 123): VM aborting
```

## <a name="dynamic-code-support"></a>Supporto di codice dinamico

### <a name="dynamic-code-does-not-compile"></a>Non compilare codice dinamico

Usare C\# dinamica nell'applicazione o libreria, è necessario aggiungere al progetto di DLL, Microsoft.CSharp.dll e Mono.CSharp.dll.

### <a name="in-release-build-missingmethodexception-occurs-for-dynamic-code-at-run-time"></a>Nelle build di rilascio MissingMethodException viene generato per codice dinamico in fase di esecuzione.

-   È probabile che il progetto di applicazione non dispone di riferimenti alla DLL, Microsoft.CSharp.dll o Mono.CSharp.dll. Assicurarsi che tali assembly vengono fatto riferimento.

    -   Tenere presente tale codice dinamico sempre i costi. Se è necessario un codice efficiente, è consigliabile non usare codice dinamico.

-   Nella prima versione di anteprima, tali assembly sono stati esclusi i tipi in ogni assembly usati in modo esplicito dal codice dell'applicazione. Vedere gli argomenti seguenti per risolvere il problema: [http://lists.ximian.com/pipermail/mo...il/009798.html](http://lists.ximian.com/pipermail/monodroid/2012-April/009798.html)


## <a name="projects-built-with-aotllvm-crash-on-x86-devices"></a>I progetti compilati con l'arresto anomalo del sistema AOT + LLVM x86 dispositivi

Quando si distribuisce un'app compilata con [AOT + LLVM](~/android/deploy-test/release-prep/index.md) nei dispositivi basati su x86, è possibile vedere un messaggio di errore di eccezione simile alla seguente:

```shell
Assertion: should not be reached at /Users/.../external/mono/mono/mini/tramp-x86.c:124
Fatal signal 6 (SIGABRT), code -6 in tid 4051 (amarin.bug56111)
```

Si tratta di un problema noto come indicato nella [56111](https://bugzilla.xamarin.com/show_bug.cgi?id=56111). La soluzione alternativa consiste nel disabilitare LLVM.
