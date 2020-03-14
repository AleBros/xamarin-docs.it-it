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
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79303918"
---
# <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi

## <a name="getting-diagnostic-information"></a>Recupero delle informazioni di diagnostica

Novell. Android presenta alcune posizioni da considerare quando si verificano diversi bug.
incluse le seguenti:

1. Output di MSBuild di diagnostica.
2. Log di distribuzione del dispositivo.
3. Output del log di debug Android.

<a name="Diagnostic_MSBuild_Output" />

## <a name="diagnostic-msbuild-output"></a>Output di MSBuild di diagnostica

MSBuild di diagnostica può contenere informazioni aggiuntive relative alla compilazione del pacchetto e può contenere alcune informazioni sulla distribuzione del pacchetto.

Per abilitare l'output di MSBuild di diagnostica in Visual Studio:

1. Fare clic su **strumenti > opzioni...**
2. Nella visualizzazione albero a sinistra selezionare **progetti e soluzioni > compila ed Esegui**
3. Nel pannello a destra impostare l'elenco a discesa del livello di dettaglio dell'output di compilazione di MSBuild su diagnostica
4. Fare clic su **OK**.
5. Pulire e ricompilare il pacchetto.
6. L'output di diagnostica è visibile all'interno del pannello di output.

Per abilitare l'output di MSBuild di diagnostica entro Visual Studio per Mac/OS X:

1. Fare clic su **Visual Studio per Mac > preferenze...**
2. Nella visualizzazione albero a sinistra selezionare **progetti > compila**
3. Nel pannello a destra impostare l'elenco a discesa del livello di dettaglio dei log su diagnostica
4. Fare clic su **OK**.
5. Riavviare Visual Studio per Mac
6. Pulire e ricompilare il pacchetto.
7. L'output di diagnostica è visibile all'interno del riquadro errori (**visualizzare > rilievi > errori** ) facendo clic sul pulsante output compilazione.

## <a name="device-deployment-logs"></a>Log di distribuzione del dispositivo

Per abilitare la registrazione della distribuzione dei dispositivi in Visual Studio:

1. **Strumenti > opzioni...** >
2. Nella visualizzazione albero a sinistra selezionare **novell > Android Settings**
3. Nel pannello a destra abilitare la casella di controllo [X] **Extension Debug Logging (scrive monodroid. log nel desktop)** .
4. I messaggi di log vengono scritti nel file monodroid. log sul desktop.

Visual Studio per Mac scrive sempre i log di distribuzione del dispositivo. Trovarli è leggermente più difficile; viene creato un file di log *AndroidUtils* per ogni giorno + tempo in cui si verifica una distribuzione, ad esempio: **AndroidTools-2012-10 -24 _12-35 -45. log**.

- In Windows, i file di log vengono scritti in `%LOCALAPPDATA%\XamarinStudio-{VERSION}\Logs`.
- In OS X, i file di log vengono scritti in `$HOME/Library/Logs/XamarinStudio-{VERSION}`.

## <a name="android-debug-log-output"></a>Output del log di debug Android

Android scriverà molti messaggi nel [log di debug Android](~/android/deploy-test/debugging/android-debug-log.md).
Novell. Android usa le proprietà di sistema di Android per controllare la generazione di altri messaggi nel log di debug Android. Le proprietà di sistema di Android possono essere impostate tramite il comando *seprop* all'interno del [Android Debug Bridge (ADB)](https://developer.android.com/guide/developing/tools/adb.html):

```shell
adb shell setprop PROPERTY_NAME PROPERTY_VALUE
```

Le proprietà di sistema vengono lette durante l'avvio del processo e devono pertanto essere impostate prima che l'applicazione venga avviata o riavviata dopo la modifica delle proprietà di sistema.

### <a name="xamarinandroid-system-properties"></a>Proprietà di sistema di Xamarin.Android

Novell. Android supporta le proprietà di sistema seguenti:

- *debug. mono. debug*: se una stringa non vuota, equivale a `*mono-debug*`.

- *debug. mono. env*: elenco di variabili di ambiente separate da pipe (' *|* ') da esportare durante l'avvio dell'applicazione, *prima* dell'inizializzazione di mono. Ciò consente di impostare le variabili di ambiente che controllano la registrazione mono.

  > [!NOTE]
  > Poiché il valore è " *|* " separato, il valore deve avere un ulteriore livello di quota, perché il comando \`*ADB shell*\` rimuoverà un set di virgolette.

  > [!NOTE]
  > I valori delle proprietà di sistema Android possono avere una lunghezza non superiore a 92 caratteri.

  Esempio:

  ```
  adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"
  ```

- *debug. mono. log*: elenco di componenti separati da virgole (' *,* ') che devono stampare messaggi aggiuntivi nel log di debug Android. Per impostazione predefinita, non viene impostato alcun elemento. I componenti includono:

  - *All*: stampa tutti i messaggi
  - *GC*: stampa messaggi correlati a GC.
  - *Gref*: stampa (vulnerabile, globale) messaggi di allocazione e deallocazione di riferimento.
  - *Lref*: stampa i messaggi di allocazione e deallocazione dei riferimenti locali.

  > [!NOTE]
  > Si tratta di un livello *estremamente* dettagliato. Non abilitare, a meno che non sia effettivamente necessario.

- *debug. mono. Trace*: consente di impostare l'impostazione [mono--Trace](http://docs.go-mono.com/?link=man%3amono(1))`=PROPERTY_VALUE`.

## <a name="deleting-bin-and-obj"></a>Eliminazione di `bin` e `obj`

Novell. Android ha sofferto in passato da una situazione simile alla seguente:

- Si verifica un errore di compilazione o di runtime sconosciuto.
- È `Clean`, `Rebuild`o eliminare manualmente le directory di `bin` e `obj`.
- Il problema scompare.

La risoluzione dei problemi, ad esempio, è molto investito a causa del loro effetto sulla produttività degli sviluppatori.

Se si verifica un problema di questo tipo:

1. Prendere nota mentali. Qual è stata l'ultima azione che ha ottenuto il progetto in questo stato?
1. Salvare il log di compilazione corrente. Provare di nuovo a compilare e registrare un [log di compilazione di diagnostica](#diagnostic-msbuild-output).
1. Inviare un [report sui bug][bug].

Prima di eliminare le directory di `bin` e `obj`, eseguirne la compressione e salvarle per una diagnosi successiva, se necessario. Probabilmente è possibile semplicemente `Clean` il progetto di applicazione Novell. Android per ricominciare a funzionare.

[bug]: https://github.com/xamarin/xamarin-android/wiki/Submitting-Bugs,-Feature-Requests,-and-Pull-Requests

## <a name="xamarinandroid-cannot-resolve-systemvaluetuple"></a>Novell. Android non è in grado di risolvere System. ValueTuple

Questo errore si verifica a causa di un'incompatibilità con Visual Studio.

- **Visual Studio 2017 Update 1** (versione 15,1 o precedente) è compatibile solo con il **4.3.0 di NuGet System. ValueTuple** (o versione precedente).

- **Visual Studio 2017 Update 2** (versione 15,2 o successiva) è compatibile solo con il **sistema. ValueTuple NuGet 4.3.1** (o versioni successive).

Scegliere il pacchetto NuGet System. ValueTuple corretto che corrisponde all'installazione di Visual Studio 2017.

## <a name="gc-messages"></a>Messaggi GC

È possibile visualizzare i messaggi del componente GC impostando la proprietà di sistema debug. mono. log su un valore che contiene GC.

I messaggi GC vengono generati ogni volta che viene eseguito il GC e forniscono informazioni sulla quantità di lavoro eseguita dal Garbage Collector:

```shell
I/monodroid-gc(12331): GC cleanup summary: 81 objects tested - resurrecting 21.
```

È possibile generare informazioni aggiuntive su GC, ad esempio le informazioni di temporizzazione, impostando la variabile di ambiente `MONO_LOG_LEVEL` su `debug`:

```shell
adb shell setprop debug.mono.env MONO_LOG_LEVEL=debug
```

Questo comporterà un numero di messaggi mono aggiuntivi, tra cui tre di conseguenza:

```shell
D/Mono (15723): GC_BRIDGE num-objects 1 num_hash_entries 81226 sccs size 81223 init 0.00ms df1 285.36ms sort 38.56ms dfs2 50.04ms setup-cb 9.95ms free-data 106.54ms user-cb 20.12ms clenanup 0.05ms links 5523436/5523436/5523096/1 dfs passes 1104 6883/11046605
D/Mono (15723): GC_MINOR: (Nursery full) pause 2.01ms, total 287.45ms, bridge 225.60 promoted 0K major 325184K los 1816K
D/Mono ( 2073): GC_MAJOR: (user request) pause 2.17ms, total 2.47ms, bridge 28.77 major 576K/576K los 0K/16K
```

Nel messaggio `GC_BRIDGE` `num-objects` è il numero di oggetti Bridge che questo passaggio sta considerando e `num_hash_entries` è il numero di oggetti elaborati durante questa chiamata del codice del Bridge.

Nei messaggi `GC_MINOR` e `GC_MAJOR` `total` è la quantità di tempo durante la quale il mondo è sospeso (nessun thread è in esecuzione), mentre `bridge` è la quantità di tempo impiegato nel codice di elaborazione del Bridge (che gestisce la VM Java). Il mondo *non* viene sospeso mentre viene eseguita l'elaborazione del Bridge.

 *In generale*, maggiore è il valore di `num_hash_entries`, maggiore sarà il tempo necessario per le raccolte di `bridge` e maggiore sarà il tempo di `total` di raccolta.

## <a name="global-reference-messages"></a>Messaggi di riferimento globali

Per abilitare la registrazione loggig di riferimento globale (GREF), la proprietà di sistema *debug. mono. log* deve contenere *Gref*, ad esempio:

```shell
adb shell setprop debug.mono.log gref
```

Novell. Android usa i riferimenti globali Android per fornire i mapping tra le istanze Java e le istanze gestite associate, come quando si richiama un metodo Java, è necessario fornire a Java un'istanza java.

Sfortunatamente, gli emulatori Android consentono di esistere solo riferimenti globali 2000 alla volta. L'hardware ha un limite di 52000 riferimenti globali molto più elevato. Il limite inferiore può essere problematico durante l'esecuzione di applicazioni nell'emulatore, quindi sapere da *dove* proviene l'istanza può essere molto utile.

> [!NOTE]
> Il conteggio dei riferimenti globali è interno a Novell. Android e non (e non può) includere riferimenti globali estratte da altre librerie native caricate nel processo. Utilizzare il conteggio dei riferimenti globale come una stima.

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

- Creazione di riferimenti globali: si tratta delle righe che iniziano con *+ g +* e forniscono una traccia dello stack per il percorso del codice di creazione.
- Distruzione globale dei riferimenti: queste sono le righe che iniziano con *-g-* e possono fornire un'analisi dello stack per il percorso del codice che elimina il riferimento globale. Se il GC Elimina Gref, non verrà fornita alcuna traccia dello stack.
- Creazione di riferimento globale debole: queste sono le righe che iniziano con *+ w +* .
- Distruzione di riferimento globale debole: si tratta di righe che iniziano con *-w-* .

In tutti i messaggi, il valore *grefc* è il numero di riferimenti globali creati da Novell. Android, mentre il valore *grefwc* è il numero di riferimenti globali vulnerabili creati da Novell. Android. Il valore handle *o obj-handle* *è il valore* dell'handle JNI e il carattere dopo ' */* ' è il tipo di valore handle: */l* per riferimento locale, */g* per i riferimenti globali e */W* per i riferimenti globali vulnerabili.

Come parte del processo GC, i riferimenti globali (+ g +) vengono convertiti in riferimenti globali vulnerabili (causando + w + e-g-), viene attivato un GC sul lato Java, quindi viene eseguito il controllo del riferimento globale debole per verificare se è stato raccolto. Se è ancora attivo, viene creato un nuovo Gref intorno al Ref debole (+ g +,-w-); in caso contrario, il riferimento debole viene eliminato (-w).

## <a name="java-instance-is-created-and-wrapped-by-a-mcw"></a>L'istanza Java viene creata e racchiusa tra MCW

```shell
I/monodroid-gref(27679): +g+ grefc 2211 gwrefc 0 obj-handle 0x4066df10/L -> new-handle 0x4066df10/L from ...
I/monodroid-gref(27679): handle 0x4066df10; key_handle 0x4066df10: Java Type: `android/graphics/drawable/TransitionDrawable`; MCW type: `Android.Graphics.Drawables.TransitionDrawable`
```

## <a name="a-gc-is-being-performed"></a>È in corso l'esecuzione di un GC...

```shell
I/monodroid-gref(27679): +w+ grefc 1953 gwrefc 259 obj-handle 0x4066df10/G -> new-handle 0xde68f95f/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -g- grefc 1952 gwrefc 259 handle 0x4066df10/G from take_weak_global_ref_jni
```

## <a name="object-is-still-alive-as-handle--null"></a>L'oggetto è ancora attivo, come handle! = null
## <a name="wref-turned-back-into-a-gref"></a>Wref riattivato in un Gref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x4066df10
I/monodroid-gref(27679): +g+ grefc 1930 gwrefc 39 obj-handle 0xde68f95f/W -> new-handle 0x4066df10/G from take_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1930 gwrefc 38 handle 0xde68f95f/W from take_global_ref_jni
```

## <a name="object-is-dead-as-handle--null"></a>L'oggetto è inattivo, come handle = = null
## <a name="wref-is-freed-no-new-gref-created"></a>Wref è stato liberato, non è stato creato alcun nuovo Gref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x0
I/monodroid-gref(27679): -w- grefc 1914 gwrefc 296 handle 0xde68f95f/W from take_global_ref_jni
```

Ecco una "interessante": nelle destinazioni che eseguono Android prima del 4,0, il valore Gref è uguale all'indirizzo dell'oggetto Java nella memoria del runtime di Android. Ovvero, il Garbage Collector è un agente di raccolta non in uno stato di trasferimento, un agente di raccolta dati e la consegna dei riferimenti diretti a tali oggetti. Quindi, dopo un + g +, + w +,-g-, + g +,-w-Sequence, il Gref risultante avrà lo stesso valore del valore Gref originale. In questo modo, grepping è abbastanza semplice.

Android 4,0, tuttavia, ha un agente di raccolta in fase di esecuzione e non invia più riferimenti diretti agli oggetti macchina virtuale di runtime di Android. Di conseguenza, dopo un + g +, + w +,-g-, + g +,-w-Sequence, il valore Gref *sarà diverso*. Se l'oggetto sopravvive a più cataloghi globali, verranno rilevati diversi valori Gref, rendendo più difficile determinare la posizione da cui un'istanza è stata effettivamente allocata.

### <a name="querying-programmatically"></a>Esecuzione di query a livello di codice

È possibile eseguire una query sui conteggi GREF e WREF eseguendo una query sull'oggetto `JniRuntime`.

`Java.Interop.JniRuntime.CurrentRuntime.GlobalReferenceCount`-conteggio riferimenti globali

Conteggio riferimenti `Java.Interop.JniRuntime.CurrentRuntime.WeakGlobalReferenceCount`-debole

## <a name="android-debug-logs"></a>Log di debug Android

I [log di debug Android](~/android/deploy-test/debugging/android-debug-log.md) possono fornire un contesto aggiuntivo relativo a eventuali errori di runtime che vengono visualizzati.

## <a name="floating-point-performance-is-terrible"></a>Le prestazioni a virgola mobile sono terribili.

In alternativa, "My app esegue 10x più velocemente con la build di debug rispetto alla build di versione!"

Novell. Android supporta più dispositivi ABI: *ARMEABI*, *ARMEABI-v7a*e *x86*. Il dispositivo ABIs può essere specificato all'interno delle **proprietà del progetto > scheda applicazione > architetture supportate**.

Le compilazioni di debug usano un pacchetto Android che fornisce tutte le ABI, quindi utilizzerà l'ABI più veloce per il dispositivo di destinazione.

Le compilazioni di versione includeranno solo l'oggetto ABIs selezionato nella scheda Proprietà progetto. È possibile selezionare più di uno.

*ARMEABI* è l'Abi predefinito e dispone del supporto più ampio per i dispositivi. *Tuttavia*, ARMEABI non supporta i dispositivi a più CPU e l'hardware a virgola mobile, Amont other things. Di conseguenza, le app che usano il runtime di rilascio di ARMEABI saranno associate a un singolo core e useranno un'implementazione Soft-float. Entrambi possono contribuire a prestazioni significativamente più lente per l'app.

Se l'app richiede prestazioni a virgola mobile decenti, ad esempio giochi, è necessario abilitare l'Abi *ARMEABI-v7a* . È possibile che si voglia supportare solo il runtime *ARMEABI-v7a* , anche se ciò significa che i dispositivi meno recenti che supportano solo *ARMEABI* non saranno in grado di eseguire l'app.

## <a name="could-not-locate-android-sdk"></a>Non è stato possibile individuare Android SDK

Sono disponibili 2 download da Google per la Android SDK per Windows.
Se si sceglie il programma di installazione con estensione exe, vengono scritte le chiavi del registro di sistema che indicano a Novell. Android dove è stato installato. Se si sceglie il file con estensione zip e lo si decomprime autonomamente, Novell. Android non sa dove cercare l'SDK. È possibile indicare a Novell. Android il percorso dell'SDK in Visual Studio passando a **strumenti > opzioni > novell > Android Settings**:

[![Android SDK percorso nelle impostazioni di Novell Android](troubleshooting-images/01.png)](troubleshooting-images/01.png#lightbox)

## <a name="ide-does-not-display-target-device"></a>IDE non Visualizza il dispositivo di destinazione

In alcuni casi si tenterà di distribuire l'applicazione in un dispositivo, ma il dispositivo in cui si vuole eseguire la distribuzione non viene visualizzato nella finestra di dialogo Seleziona dispositivo. Questo problema può verificarsi quando il Android Debug Bridge decide di andare in vacanza.

Per diagnosticare questo problema, trovare il [programma ADB](~/android/deploy-test/debugging/android-debug-log.md), quindi eseguire:

```shell
adb devices
```

Se il dispositivo non è presente, è necessario riavviare il server Android Debug Bridge in modo che sia possibile trovare il dispositivo:

```shell
adb kill-server
adb start-server
```

Il software HTC Sync potrebbe impedire il corretto funzionamento di **ADB Start-Server** . Se il comando **ADB Start-Server** non stampa la porta da cui sta iniziando, uscire dal software HTC Sync e provare a riavviare il server ADB.

## <a name="the-specified-task-executable-keytool-could-not-be-run"></a>Non è stato possibile eseguire l'eseguibile dell'attività specificato "strumento".

Questo significa che il percorso non contiene la directory in cui si trova la directory bin di Java SDK. Verificare di aver seguito i passaggi della Guida all' [installazione](~/android/get-started/installation/index.md) .

## <a name="monodroidexe-or-aresgenexe-exited-with-code-1"></a>monodroid. exe o aresgen. exe terminato con il codice 1

Per semplificare il debug di questo problema, passare a Visual Studio e modificare il livello di dettaglio di MSBuild. a tale scopo, selezionare: **strumenti > opzioni > progetto** e **soluzioni > compila** ed **Esegui > livello di dettaglio dell'output di compilazione del progetto MSBuild** e impostare questo valore su **normale**.

Ricompilare e controllare il riquadro di output di Visual Studio, che dovrebbe contenere l'errore completo.

## <a name="there-is-not-enough-storage-space-on-the-device-to-deploy-the-package"></a>Lo spazio di archiviazione disponibile nel dispositivo non è sufficiente per distribuire il pacchetto

Questo errore si verifica quando l'emulatore non viene avviato dall'interno di Visual Studio. Quando si avvia l'emulatore al di fuori di Visual Studio, è necessario passare le opzioni di `-partition-size 512`, ad esempio

```shell
emulator -partition-size 512 -avd MonoDroid
```

Assicurarsi di usare il nome corretto del simulatore, ovvero [il nome usato durante la configurazione del simulatore](~/android/get-started/installation/windows.md#device).

## <a name="install_failed_invalid_apk-when-installing-a-package"></a>INSTALLAZIONE\_non riuscita\_APK\_non valido durante l'installazione di un pacchetto

I nomi dei pacchetti Android *devono* contenere un punto (' *.* '). Modificare il nome del pacchetto in modo che contenga un punto.

- All'interno di Visual Studio:
  - Fare clic con il pulsante destro del mouse sul progetto > Proprietà
  - Fare clic sulla scheda Manifesto Android sulla sinistra.
  - Aggiornare il campo del nome del pacchetto.
    - Se viene visualizzato il messaggio &ldquo;non sono stati trovati file AndroidManifest. XML. Fare clic per aggiungerne una.&rdquo;fare clic sul collegamento e quindi aggiornare il campo del nome del pacchetto.
- All'interno Visual Studio per Mac:
  - Fare clic con il pulsante destro del mouse sul progetto > Opzioni
  - Passare alla sezione relativa all'applicazione Build/Android.
  - Modificare il campo del nome del pacchetto in modo che contenga ".".

## <a name="install_failed_missing_shared_library-when-installing-a-package"></a>INSTALLAZIONE\_non riuscita\_manca\_libreria\_condivisa durante l'installazione di un pacchetto

Una "libreria condivisa" in questo contesto *non* è un file di libreria condivisa nativa (*libfoo.so*). si tratta invece di una libreria che deve essere installata separatamente sul dispositivo di destinazione, ad esempio Google Maps.

Il pacchetto Android specifica quali librerie condivise sono necessarie con l'elemento `<uses-library/>`. Se una libreria *necessaria* non è presente nel dispositivo di destinazione (ad esempio `//uses-library/@android:required` è *true*, ovvero l'impostazione predefinita), l'installazione del pacchetto avrà esito negativo con l' *installazione\_non riuscita\_manca\_libreria\_condivisa*.

Per determinare quali librerie condivise sono necessarie, visualizzare il file *generato*
**file AndroidManifest. XML** (ad esempio, **obj\\debug\\Android\\file AndroidManifest. XML**) e cercare gli elementi `<uses-library/>`. è possibile aggiungere manualmente elementi di `<uses-library/>` nelle proprietà del progetto **\\file file AndroidManifest. XML** e tramite l' [attributo personalizzato UsesLibraryAttribute](xref:Android.App.UsesLibraryAttribute).

Ad esempio, se si aggiunge un riferimento a un assembly *mono. Android. GoogleMaps. dll* , si aggiunge in modo implicito un `<uses-library/>` per la libreria condivisa di Google Maps.

## <a name="install_failed_update_incompatible-when-installing-a-package"></a>Impossibile installare\_\_aggiornamento\_incompatibile durante l'installazione di un pacchetto

I pacchetti Android hanno tre requisiti:

- Devono contenere ' .' (vedere la voce precedente)
- Devono avere un nome univoco per il pacchetto di stringhe, di conseguenza la convenzione di TLD inverso visualizzata nei nomi delle app Android, ad esempio com. Android. Chrome per l'app Chrome.
- Quando si aggiornano i pacchetti, il pacchetto deve avere la stessa chiave di firma.

Si supponga quindi che questo scenario:

1. Si compila & distribuire l'app come app di debug
2. Si modifica la chiave di firma, ad esempio per usarla come app per la versione (o perché la chiave di firma del debug specificata dal valore predefinito)
3. Si installa l'app senza rimuoverla prima, ad esempio debug > Avvia senza eseguire debug in Visual Studio

Quando si verifica questa situazione, l'installazione del pacchetto avrà esito negativo con un errore di installazione\_\_aggiornamento\_incompatibile, perché il nome del pacchetto non è stato modificato durante la chiave di firma. Il [log di debug Android](~/android/deploy-test/debugging/android-debug-log.md) conterrà anche un messaggio simile a:

```shell
E/PackageManager(  146): Package [PackageName] signatures do not match the previously installed version; ignoring!
```

Per correggere l'errore, rimuovere completamente l'applicazione dal dispositivo prima di reinstallare.

## <a name="install_failed_uid_changed-when-installing-a-package"></a>Impossibile installare\_\_UID\_modificato durante l'installazione di un pacchetto

Quando viene installato un pacchetto Android, viene assegnato un *ID utente* (UID).
In *alcuni casi*, per motivi sconosciuti, quando si esegue l'installazione su un'app già installata, l'installazione avrà esito negativo con `INSTALL_FAILED_UID_CHANGED`:

```shell
ERROR [2015-03-23 11:19:01Z]: ANDROID: Deployment failed
Mono.AndroidTools.InstallFailedException: Failure [INSTALL_FAILED_UID_CHANGED]
   at Mono.AndroidTools.Internal.AdbOutputParsing.CheckInstallSuccess(String output, String packageName)
   at Mono.AndroidTools.AndroidDevice.<>c__DisplayClass2c.<InstallPackage>b__2b(Task`1 t)
   at System.Threading.Tasks.ContinuationTaskFromResultTask`1.InnerInvoke()
   at System.Threading.Tasks.Task.Execute()
```

Per risolvere questo problema, *disinstallare completamente* il pacchetto Android installando l'app dalla GUI della destinazione Android o usando `adb`:

```shell
$ adb uninstall @PACKAGE_NAME@
```

**Non usare** `adb uninstall -k`, perché questo conserverà i dati *dell'applicazione e* quindi manterrà l'UID in conflitto sul dispositivo di destinazione.

## <a name="release-apps-fail-to-launch-on-device"></a>Non è possibile avviare le app della versione nel dispositivo

L'output del log di debug Android conterrà un messaggio simile a:

```shell
D/AndroidRuntime( 1710): Shutting down VM
W/dalvikvm( 1710): threadid=1: thread exiting with uncaught exception (group=0xb412f180)
E/AndroidRuntime( 1710): FATAL EXCEPTION: main
E/AndroidRuntime( 1710): java.lang.UnsatisfiedLinkError: Couldn't load monodroid: findLibrary returned null
E/AndroidRuntime( 1710):        at java.lang.Runtime.loadLibrary(Runtime.java:365)
```

In tal caso, esistono due possibili cause:

1. Il file con estensione APK non fornisce un'ABI supportata dal dispositivo di destinazione.
    Ad esempio, il file con estensione APK contiene solo file binari ARMEABI-v7a e il dispositivo di destinazione supporta solo ARMEABI.

2. Un [bug Android](https://code.google.com/p/android/issues/detail?id=21670). In tal caso, disinstallare l'app, incrociare le dita e reinstallare l'app.

Per correggere (1), modificare le proprietà e le opzioni del progetto e [aggiungere il supporto per l'interfaccia ABI richiesta all'elenco di Abi supportate](~/android/app-fundamentals/cpu-architectures.md). Per determinare quale ABI è necessario aggiungere, eseguire il comando ADB seguente sul dispositivo di destinazione:

```shell
adb shell getprop ro.product.cpu.abi
adb shell getprop ro.product.cpu.abi2
```

L'output conterrà l'Abi primario (e il database secondario facoltativo).

```shell
$ adb shell getprop | grep ro.product.cpu
[ro.product.cpu.abi2]: [armeabi]
[ro.product.cpu.abi]: [armeabi-v7a]
```

## <a name="the-outpath-property-is-not-set-for-project-ldquomyappcsprojrdquo"></a>La proprietà outPath non è impostata per Project &ldquo;MyApp. csproj&rdquo;

Ciò significa in genere che sono presenti un computer HP e la variabile di ambiente &ldquo;piattaforma&rdquo; è stata impostata su un valore simile a MCD o Honolulu. Questa operazione è in conflitto con la proprietà della piattaforma MSBuild che in genere è impostata su &ldquo;qualsiasi&rdquo; CPU o &ldquo;&rdquo;x86. È necessario rimuovere questa variabile di ambiente dal computer prima che MSBuild possa funzionare:

- Pannello di controllo > sistema > variabili di ambiente > avanzate

Riavviare Visual Studio o Visual Studio per Mac e provare a ricompilare. Gli elementi dovrebbero ora funzionare come previsto.

## <a name="javalangclasscastexception-monoandroidruntimejavaobject-cannot-be-cast-to"></a>Impossibile eseguire il cast di Java. lang. ClassCastException: mono. Android. Runtime. JavaObject a...

Novell. Android 4. x non esegue correttamente il marshalling di tipi generici annidati. Si consideri, ad esempio, il codice C\# seguente usando [SimpleExpandableListAdapter](xref:Android.Widget.SimpleExpandableListAdapter):

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

Il problema è che Novell. Android esegue erroneamente il marshalling dei tipi generici annidati. È in corso il marshalling del `List<IDictionary<string, object>>` a un oggetto [java. lang. ArrrayList](xref:Java.Util.ArrayList), ma il `ArrayList` contiene `mono.android.runtime.JavaObject` istanze (che fanno riferimento alle istanze di `Dictionary<string, object>`) invece di un elemento che implementa [java. util. map](xref:Java.Util.IMap), causando la seguente eccezione:

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

La soluzione alternativa consiste nell'usare i [tipi di raccolta Java](~/android/internals/api-design.md) forniti invece dei tipi di `System.Collections.Generic` per i tipi di&rdquo; interni &ldquo;. Questa operazione determinerà tipi Java appropriati per il marshalling delle istanze. Il codice seguente è più complicato del necessario per ridurre le durate Gref. Può essere semplificato per modificare il codice originale tramite `s/List/JavaList/g` e `s/Dictionary/JavaDictionary/g` se le durate di Gref non sono un problema.

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

## <a name="unexpected-nullreferenceexceptions"></a>NullReferenceException impreviste

Occasionalmente, il [log di debug Android](~/android/deploy-test/debugging/android-debug-log.md) menzionerà le eccezioni NullReferenceException che non &ldquo;possono verificarsi,&rdquo; o provengono dal codice di runtime di mono per Android poco prima che l'app muoia:

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

Questo problema può verificarsi quando il runtime di Android decide di interrompere il processo, operazione che può verificarsi per diversi motivi, tra cui il raggiungimento del limite GREF della destinazione o l'esecuzione di qualcosa &ldquo;errato&rdquo; con JNI.

Per verificare se questo è il caso, controllare il log di debug Android per un messaggio del processo simile al seguente:

```shell
E/dalvikvm(  123): VM aborting
```

## <a name="abort-due-to-global-reference-exhaustion"></a>Interrompi a causa dell'esaurimento del riferimento globale

Il livello JNI del runtime di Android supporta solo un numero limitato di riferimenti a oggetti JNI che devono essere validi in un determinato momento. Quando questo limite viene superato, vengono eseguite interruzioni.

Il limite GREF (*riferimento globale*) è 2000 riferimenti nell'emulatore e ~ 52000 fa riferimento all'hardware.

Si sa che si sta iniziando a creare troppi GREFs quando vengono visualizzati messaggi simili al seguente nel log di debug Android:

```shell
D/dalvikvm(  602): GREF has increased to 1801
```

Quando si raggiunge il limite di GREF, viene stampato un messaggio simile al seguente:

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

Nell'esempio precedente, che, incidentalmente, deriva dal [bug 685215](https://bugzilla.novell.com/show_bug.cgi?id=685215), il problema è dovuto al fatto che sono state create troppe istanze di Android. graphics. Point. per un elenco di correzioni per questo particolare bug, vedere il [commento \#2](https://bugzilla.novell.com/show_bug.cgi?id=685215#c2) .

In genere, una soluzione utile è trovare il tipo con un numero eccessivo di istanze allocate &ndash; Android. graphics. puntare nel dump precedente &ndash; quindi trovare la posizione in cui vengono creati nel codice sorgente ed eliminarli in modo appropriato, in modo che la durata dell'oggetto Java venga abbreviata. Questa operazione non è sempre appropriata (\#685215 è multithreading, quindi la soluzione Trivial evita la chiamata a Dispose), ma è la prima cosa da considerare.

È possibile abilitare la [registrazione Gref](~/android/troubleshooting/index.md) per vedere quando vengono creati GREFs e il numero di exist.

## <a name="abort-due-to-jni-type-mismatch"></a>Interruzione a causa di mancata corrispondenza del tipo JNI

Se si esegue il rollback del codice JNI, è possibile che i tipi non corrispondano correttamente, ad esempio se si tenta di richiamare `java.lang.Runnable.run` su un tipo che non implementa `java.lang.Runnable`. Quando si verifica questo problema, nel log di debug Android verrà visualizzato un messaggio simile al seguente:

```shell
W/dalvikvm( 123): JNI WARNING: can't call Ljava/Type;;.method on instance of Lanother/java/Type;
W/dalvikvm( 123):              in Lmono/java/lang/RunnableImplementor;.n_run:()V (CallVoidMethodA)
...
E/dalvikvm( 123): VM aborting
```

## <a name="dynamic-code-support"></a>Supporto del codice dinamico

### <a name="dynamic-code-does-not-compile"></a>Il codice dinamico non viene compilato

Per usare C\# dinamica nell'applicazione o nella libreria, è necessario aggiungere System. Core. dll, Microsoft. CSharp. dll e mono. CSharp. dll al progetto.

### <a name="in-release-build-missingmethodexception-occurs-for-dynamic-code-at-run-time"></a>Nella build di rilascio MissingMethodException si verifica per il codice dinamico in fase di esecuzione.

- È probabile che il progetto dell'applicazione non includa riferimenti a System. Core. dll, Microsoft. CSharp. dll o mono. CSharp. dll. Assicurarsi che venga fatto riferimento a tali assembly.

  - Tenere presente che il codice dinamico costi sempre. Se è necessario codice efficiente, provare a non usare il codice dinamico.

- Nella prima anteprima questi assembly sono stati esclusi a meno che i tipi in ogni assembly non vengano usati in modo esplicito dal codice dell'applicazione. Per una soluzione alternativa, vedere quanto segue: [http://lists.ximian.com/pipermail/mo...il/009798.html](http://lists.ximian.com/pipermail/monodroid/2012-April/009798.html)

## <a name="projects-built-with-aotllvm-crash-on-x86-devices"></a>Progetti compilati con AOT + LLVM arresti anomali sui dispositivi x86

Quando si distribuisce un'app compilata con [AOT + LLVM](~/android/deploy-test/release-prep/index.md) su dispositivi basati su x86, è possibile che venga visualizzato un messaggio di errore di eccezione simile al seguente:

```shell
Assertion: should not be reached at /Users/.../external/mono/mono/mini/tramp-x86.c:124
Fatal signal 6 (SIGABRT), code -6 in tid 4051 (Xamarin.bug56111)
```

Si tratta di un problema noto: la soluzione alternativa consiste nel disabilitare LLVM.
